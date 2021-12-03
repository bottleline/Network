## MQTT 의 Qos 의 종류
![image](https://user-images.githubusercontent.com/42457589/144591395-6a723434-9a8f-42f9-a7d0-00645df636af.png)

```
mqtt 에는 3가지 qos 가있다
qos0
qos1
qos2
```

### qos0
```
QoS 레벨 0은 보내고 잊는다. 잊는다는 것은 저장하지 않는다는 뜻이다. 
한번에 전송이 성공하지 않으면 전송은 실패한 상태로 끝이 난다.
QoS를 보장하지 않는 상태라고 보면 된다.

```

### qos1
```
레벨 1은 최소 한번은 가게 된다.
정상적으로 통신할 경우 1번을 보내게 된다.
그러나 다음의 시나리오를 생각해보자.

1. Publisher가 Broker에게 정상적으로 보낸다.

2. Broker는 받은 메시지를 Subscriber에게 정상적으로 보낸다. (Subscriber는 메시지를 1회 받음)

3. Broker는 메시지를 보냈으므로 PUBACK을 Publisher에게 보낸다.

4. 그러나 PUBACK이 전달 중에 사라졌다. (Loss 발생)

5. Publisher는 PUBACK을 기다리다가, 시간이 지나자 전송이 실패한 줄 알고 다시 Broker에게 보낸다.(Duplicate Publish)

6. Broker는 메시지에 관한 정보를 지웠기 때문에, 처음 받는 메시지인줄 알고 다시 Subscriber에게 보낸다.(Subscriber는 똑같은 메시지를 2번째 받음)

7. 다시 PUBACK을 보내고, 정상 종료된다.

8. Publisher가 PUBACK을 받고, 통신은 종료된다.
```

### qos2
```
레벨 2는 PUBACK의 과정을 3 Way handshaking으로 바꿈으로 정확히 1번만 가도록 구현하였다.


1. Publsiher가 메시지를 보낸다.

2. Broker가 메시지를 전달한다. (Subscirber 1회 전달받음)

3. Broker가 Publisher에게 PUB Recevied를 보낸다.

4. PUBREC이 분실되어 Publisher가 다시 메시지를 보내도, Broker는 메시지를 이미 갖고 있기 때문에 Subscriber에게 다시 메시지를 보내지 않고 PUBREC를 다시 보낸다.

5. Publisher는 PUBREC를 받으면, 이제서야 Publish Release 메시지를 보낸다.

6. PUBREL이 loss되는 것은 문제가 없다. 브로커는 이미 보냈다는 사실을 알고 있기 때문에 새로 보내지 않는다.

7. PUB REL을 받으면, 이제서야 메시지를 삭제한다.

8. 메시지를 정상 종료하였으므로 PUBCOMPLETE를 보낸다.


위와 같은 시나리오대로 메시지를 정확하게 한번만 보낼 수 있게 된다.
이 외에 QoS -1 레벨이 MQTT-SN을 위하여 존재하는데, 이는 클라이언트-브로커 연결이 되지 않아도 메시지를 보내는 것이다.
일반적으로 QOS 0,1,2 레벨은 클라이언트-브로커 연결이 된 이후에 Publish를 하는 것인데
유일하게 -1 레벨은 연결을 확인하지도 않고 메시지를 그냥 던지는 것이다
```
