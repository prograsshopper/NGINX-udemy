# Configuration

## Terms
- Directive
    - 지시어. name - value의 짝으로 이루어져있음
    - 특정 구성 옵션으로 config 파일 내에 존재
- Context
    - 일종의 scope 같은 것
    - 내장될 수 있음 (nested)

## Virtual Host
- 현지의 기본 호스팅에서 가상 호스트를 띄워서 대체해보는 파트
- 기본 /etc/nginx/nginx.conf 파일상의 모든 내용을 지우고 새로 세팅해보자

### event
- event 스코프의 경우엔 지금 당장 추가할 것이 없어도 향후 필요한 configuration 세팅을 위해서 남겨둔다

### 가상 호스트
- 가상호스트를 정의해보자
- 각 가상호스트는 새 server context나 블록이 있고 주어지는 ip나 도메인에 통상적으로 http 상에선 80, https상에선 443으로 정해지는 포트를 가지고 있다.
- 서버명은 와일드카드 *를 사용할 수 있다 
- root: nginx가 요청을 서빙하거나 static 요청을 해석하는 위치

> systemctl [reload/restart] nginx 

-> restart의 경우엔 엔진엑스를 끄고 재시작하는 것이고, reload의 경우엔 에러가 발생시 현재 실행중인 엔진엑스를 끄진 않는 상태로 로드한다.
- 처음에 type에 대한걸 지정하지 않으면 스타일이 꺄져서 나오는데 일일이 타입을 지정해줄수도 있지만 엔진엑스에선 이런 파일들을 서빙하기 간편하게 해주는 파일(/etc/nginx/mime.types)가 있으니 여기서 확인해보면 된다.

### Location Blocks
- URI 등의 요청을 처리할때 사용한다.
- location /<url> 식으로 사용하는데 여기서 정규식등을 이용해서 처리할 수도 있다.

### Variables
- key - value 식으로 설정이 가능
- Configuration variable과 module variable 이렇게 두가지가 있는데 전자는 사용자가 직접 정의한느 것이고 후자의 경우엔 nginx에서 기본적으로 제공한다.
- [엔진엑스 기본 모듈 변수](http://nginx.org/en/docs/varindex.html)
- 사용자가 직접 정의할때는 set ${variable_name} {content}식으로 정의

### Rewrite & Redirectives
#### Two directives for rewriting requests
- return status URI
    - return status_code strings 
    - ex} return 307 /some/path;
- rewrite pattern URI
    - return의 경우에는 uri 자체를 바꿔주지는 않음
    - rewrite를 사용하면 uri를 다른 값으로 설정할 수 있다.
    - 두번 rewrite하는 것도 가능한데, 만약 여러번 rewrite하는걸 방지하고 싶으면 last를 맨 뒤에 붙여주면 된다.


### Try files & Named Locations
#### try_files
- server contect 냐에서 rewrite, return 등과 함께 사용되거나 혹은 location context내에서 사용되기도 한다.
- 어떤 location에 들어가기에 앞서 동작을 실행시킬 수 있다. 보통 존재하지 않는 uri 처리할 때 많이 사용하는 것으로 보인다.

### Logging
- Access Log
    - 서버에 들어온 모든 요청
    - 404의 경우에도 유효한 요청이므로 여기에 저장된다.
- Error Log
    - fail나거나 이상한 동작을 하는 경우
    - 대신 404의 not found되는 경위가 여기에 저장된다. 에러로그는 단순히 제대로된 응답이 돌아오느냐 보다는 완전한 장애발생시에 기록되는 곳이라고 볼 수 있다.
- 로그파일 위치(default): /var/log/nginx/
- 특정 url은 다른 로그를 저장하도록 지정해줄 수도 있다.
```
location /secure {
    access_log /var/log/nginx/secure.access.log;
```

### Inheritance & Directive Types
3 main directives
- Standard Directive: 컨텍스트 내에서 한번만 선언된다.
- Array Directive: 여러번 작성이 가능하다.
- Action Directive: 특정 동작을 발생시키는 지시어. return이 대표 예시

- Standard & Array 에서 상속이 일어난다. 

### Worker Processes
명령어 sudo systemctl status nginx를 치면 다음의 두 가지 프로세스가 뜬다.

```
/system.slice/nginx.service
           ├─ 4666 nginx: worker process
           └─10041 nginx: master process /usr/sbin/nginx -g daemon on; master_process
```

- master: nginx 자신
- worker: 클라이언트의 요청에 응답함

nginx의 기본 프로세스 수는 하나지만, worker_processes라는 지시어를 통해서 더 늘릴 수도 있다. 이 지시어를 auto로 설정하면 서버의 cpu 수 등을 고려하여 자동으로 정해줌.

- nginx는 비동기적으로 동작하므로, 프로세스를 늘리는 것이 더 좋은 퍼포먼스를 약속하는 것은 아니다.
- 지시어 worker_connections를 통해 커넥션 수 조절 가능
- max connection 수: worker_process * worker_connections
- 지시어 pid를 통해 config 파일로 pid 설정 가능...이라는데 실습해보니 에러가 뜨고, 다른 사람들도 이런 경우가 잦았다는데 강의자의 피드백은 없다..

*우분투 커맨드
- ulimit -n: 서버상에서 한번에 열 수 있는 파일 수. 디폴트는 1024인듯
- nproc: 프로세스 수
- lscpu: 현재 서버의 cpu 상태 확인

### Buffers & Timeout
- 둘은 반대되는 개념이다
1. Buffer
    - buffering은 프로세스나 nginx의 워커가 다음 도착지에 쓰기 전에 메모리나 ram에서 데이터를 읽어오는 것을 말한다
    - ex) nginx request가 tcp 포트 80에서 요청을 받은 후, 메모리에 요청된 데이터를 쓰는 것, 혹은 버퍼가 데이터량에 비해 너무 적으면 디스크에 쓰는 것을 말한다.
2. Timeout
    - 주어진 이벤트에 걸리는 시간을 제한하는 것

3. 지시어
    - client_body_buffer_size: 클라이언트로 부터 받은 데이터에 할당되는 메모리 세팅. 기본 단위는 바이트이고 킬로바이트의 경우엔 {숫자}K, 메가바이트의 경우엔 {숫자}M 식으로 작성한다.
    - client_max_body_size: 여기서 작성해준 것 이상의 요청은 더이상 받지않는다는 의미다. 여기보다 더 큰 요청을 받을땐 에러 413을 리턴한다
    - client_body_timeout: incoming request를 핸들링하는 지시어
    = client_header_timeout: incoming request를 핸들링하는 지시어
    - keepalive_timeout: nginx가 커넥션을 유지하는 시간설정.
    - send_file: disk로부터 파일을 보낼 때 버퍼를 사용하지 않고 바로 응답으로 보내는 것