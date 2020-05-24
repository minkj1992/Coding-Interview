# TCP 소켓 옵션
> https://asfirstalways.tistory.com/356


> [CLOSE_WAIT, TIME_WAIT에 대한 설명](https://tech.kakao.com/2016/04/21/closewait-timewait/)

```
SO_REUSEADDR: 서버 소켓에 적용되는 옵션
tcp_tw_reuse: 클라이언트 소켓에 적용되는 옵션
```

## 용어
- `CLOSE_WAIT` 
  - PASSIVE CLOSE 측에서 ACTIVE CLOSE측의 FIN 요청을 받은 경우, 이에 대해 우선 ACK 확인 메시지를 보내고 자신(Passive)의 통신이 끝날때까지 CLOSE_WAIT 상태로 대기한다. 통신이 끝났으면 연결이 종료되었다고 FIN을 전달한다.
  - **커널 옵션으로 타임아웃 조절이 가능한 FIN_WAIT이나 재사용이 가능한 TIME_WAIT과 달리, CLOSE_WAIT는 포트를 잡고 있는 프로세스의 종료 또는 네트워크 재시작 외에는 제거할 방법이 없습니다. 즉, 로컬 어플리케이션이 정상적으로 close()를 요청하는 것이 가장 좋은 방법입니다.**
  - `CLOSE_WAIT`으로 잉여패킷을 기다리는 PASSIVE CLOSE측 소켓은 커널에 의해서 일정시간 종료되지 않고 대기하게 되는데, 이때 해당 서비스를 다시 시작하더라도 일정시간동안(커널이 소켓포트를 놓아줄때까지) 다시 구동할 수 없지만, 해당 옵션을 사용하면 가능하다.
- `TIME_WAIT` 
  - ACTIVE CLOSE 측에서 PASSIVE CLOSE 측의 FIN 요청을 받을 경우 TIME_WAIT 상태로 변경된다. 이때 커널에게 ACTIVE CLOSE측의 SOCKET 접근 권한이 넘어가게 되고, 일정 시간동안 PASSIVE CLOSE측의 잉여 패킷을 기다리게 된다.
- `SO_REUSEADDR`
  - [참고자료](https://m.blog.naver.com/PostView.nhn?blogId=bringmelove1&logNo=119146643&proxyReferer=https:%2F%2Fwww.google.com%2F)
  - 커널이 소켓을 사용하는 중에도 계속해서 사용할 수 있다.
  - 커널이 소켓의 포트를 아직 점유 중인 경우에 서버 프로그램을 다시 구동해야 할 때 매우 유용하다. 또는 두 개 이상의 ip 주소를 가지는 호스트에서 IP 주소별로 서버를 운용할 경우에도 이 옵션을 사용하면 사용 중인 포트에 대해서 소켓을 성공적으로 연결(bind)할 수 있다.

- `tcp_tw_reuse`
  - TCP Time Wait Reuse (TIME WAIT으로 포트 번호 고갈 방지 -> 재사용)
  - 왜냐하면 TIME_WAIT 시간은 하드코딩되어있기 때문에 시간을 변경할 수 없다.



**SO_REUSEADDR 은  bind와 listen 하는 과정에서 커널이 선점하고 있는 포트를 다시 사용할 수 있게 해 주는 것이고  tw_reuse는 outgoing 트래픽에 대해 TW 상태의 로컬 포트를 재사용할 수 있게 해 주는  것입니다.**

