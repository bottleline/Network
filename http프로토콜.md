### HTTP
```Hyper-text Transfer Protocl 의 약자로 www 에서 문서를 
주고받을때 사용하는 프로토콜
TCP, UDP 를 사용하며 80 포트를 사용함
```
  
### HTTP 패킷
```
HTTP 패킷의 구조는 크게

- 헤더 : HTTP method, Client Info, Browser Info, URL 등이 포함
- 바디 : 특정 데이터를 담아서 서버에 요청

로 나뉜다.

- Request Header : 요청하는 페이지의 주소와 현재 컴퓨터의 정보가 전송되는 부분
- Request Body : POST 전송시 데이터가 들어가는 부분
- Response Header : 응답페이지의 상태와 서버에 관한 정보가 전송되는 부분
- Response Body : 페이지의 HTML 소스가 전송되는 부분 

```
  
### HTTP 메소드
```
- Get   : 서버에 Data 요청 (DB 쿼리의 select 역할)
- POST  : 서버에 Data 전송 및 업데이트 (DB 쿼리의 Create 역할)
- PUT   : 서버에 Data 업데이트 (DB 쿼리의 update 역할)
- PATCH : 서버의 Data 를 일부 수정 
- DELETE : 서버의 Data Delete (DB의 Delete 역할)
```
  
### Get 방식
```
전송할 데이터를 url 에 붙여서 전송하는 방식
예) 127.0.0.1:8000/result/?text=hi+hi

특정페이지를 링크하거나 북마크할수있으며 
불필요한 요청을 제한하기 위해 요청이 캐쉬될수있음
```

### Post 방식
```
리소스를 생성/변경하기위해 사용하는 메소드
데이터를 Body에 첨부하여 전송

get과 비교하여 대용량의 데이터를 전송할수 있음
get보다 보안적으로 안정적임

post 요청을 보낼때에는 content-type 에 요청데이터의 타입을 표시해야함
```
