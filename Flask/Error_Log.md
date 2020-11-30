## Error Log

### SIGPIPE: writing to a closed pipe/socket/fd (probably the client disconnected) on request

```bash
Sat Nov 28 15:10:01 2020 - SIGPIPE: writing to a closed pipe/socket/fd (probably the client disconnected) on request 
Sat Nov 28 15:10:01 2020 - SIGPIPE: writing to a closed pipe/socket/fd (probably the client disconnected) on request 
Sat Nov 28 15:10:01 2020 - SIGPIPE: writing to a closed pipe/socket/fd (probably the client disconnected) on request 
Sat Nov 28 15:10:01 2020 - SIGPIPE: writing to a closed pipe/socket/fd (probably the client disconnected) on request
```

에러 발생 원인은 API에 너무 많은 요청이 들어왔고, 미처 처리하지 못한 요청 결과를 Client에게 반환하는 과정에서 Client와의 연결이 끊어져 생긴 현상으로 대체로 PIPE와 관련된 에러 대부분은 클라이언트와의 연결 상의 문제로 자주 발생한다.

예를 들어, 클라이언트의 요청 이후에 처리 시간이 너무 오래 걸려 방치된 경우도 SIGPIPE 에러가 동일하게 발생한다. 그러나 모두 동일한 에러는 아니고 메세지마다 다른 원인이 나타난다.

해결 방법 : nginx 설정에 uwsgi_pass 전 설정 추가

```nginx
{
  uwsgi_connect_timeout 180;
	uwsgi_read_timeout 180;
	uwsgi_send_timeout 180;
}
```



