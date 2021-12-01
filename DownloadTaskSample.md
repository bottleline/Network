``` swift
import UIKit
import AVKit

class DownloadTaskViewController: UIViewController {
   
   @IBOutlet weak var sizeLabel: UILabel!
   
   @IBOutlet weak var downloadProgressView: UIProgressView!
   
   lazy var formatter: ByteCountFormatter = {
      let f = ByteCountFormatter()
      f.countStyle = .file
      return f
   }()
   
   var targetUrl: URL {
      guard let targetUrl = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first?.appendingPathComponent("downloadedFile.mp4") else {
         fatalError("Invalid File URL")
      }// 다운로드한 임시파일을 최종 저장할 경로
      
      return targetUrl
   }
   
    var task:URLSessionDownloadTask?
    
    var session: URLSession{
        let c = URLSessionConfiguration.default
        let session = URLSession(configuration: c, delegate: self, delegateQueue: OperationQueue.main)
        
        return session
    }
   
   @IBAction func startDownload(_ sender: Any) {
      do {
         let hasFile = try targetUrl.checkResourceIsReachable()
         if hasFile {
            try FileManager.default.removeItem(at: targetUrl)
         }
      } catch {
         print(error)
      }
      
      guard let url = URL(string: smallFileUrlStr) else { // 나중에서 수정
         fatalError("Invalid URL")
      }
      
      downloadProgressView.progress = 0.0
       
       task = session.downloadTask(with: url)
       task?.resume()
   }
   
   @IBAction func stopDownload(_ sender: Any) {
       task?.cancel()
   }
    
    var resumeData:Data?
   
   @IBAction func pauseDownload(_ sender: Any) {
       task?.cancel(byProducingResumeData: { data in
           self.resumeData = data
       })
   }
   
   @IBAction func resumeDownload(_ sender: Any) {
       guard let data = resumeData else {return}
       task = session.downloadTask(withResumeData: data)
       task?.resume()
   }
   
   override func shouldPerformSegue(withIdentifier identifier: String, sender: Any?) -> Bool {
      return (try? targetUrl.checkResourceIsReachable()) ?? false
   }
   
   override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
      if let playerVC = segue.destination as? AVPlayerViewController {
         let player = AVPlayer(url: targetUrl)
         playerVC.player = player
         playerVC.navigationItem.title = "Play"
      }
   }
   
   override func viewWillDisappear(_ animated: Bool) {
      super.viewWillDisappear(animated)
      
      // Code Input Point #8
       session.invalidateAndCancel()
      // Code Input Point #8
   }
}

// Code Input Point #3
extension DownloadTaskViewController:URLSessionDownloadDelegate{
    func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask, didFinishDownloadingTo location: URL) {
        print(#function)
        
        guard (try? location.checkResourceIsReachable()) ?? false else{return}
        
        do{
            _ = try FileManager.default.replaceItemAt(targetUrl, withItemAt: location)
        }catch{
            fatalError(error.localizedDescription)
        }
    }
    
    func urlSession(_ session: URLSession, task: URLSessionTask, didSendBodyData bytesSent: Int64, totalBytesSent: Int64, totalBytesExpectedToSend: Int64) {

        
    }

    func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask, didWriteData bytesWritten: Int64, totalBytesWritten: Int64, totalBytesExpectedToWrite: Int64) {
        downloadProgressView.progress = Float(totalBytesWritten)/Float(totalBytesExpectedToWrite)
        sizeLabel.text = formatter.string(fromByteCount: totalBytesWritten) + "/" + formatter.string(fromByteCount: totalBytesExpectedToWrite)
    }
    
    func urlSession(_ session: URLSession, task: URLSessionTask, didCompleteWithError error: Error?) {
        print(error ?? "Done")
    }
    
    func urlSession(_ session: URLSession, downloadTask: URLSessionDownloadTask, didResumeAtOffset fileOffset: Int64, expectedTotalBytes: Int64) {
        downloadProgressView.setProgress(Float(fileOffset)/Float(expectedTotalBytes), animated: true)
        sizeLabel.text = formatter.string(fromByteCount: fileOffset) + "/" + formatter.string(fromByteCount: expectedTotalBytes)
    }
    
    
    
}
```
