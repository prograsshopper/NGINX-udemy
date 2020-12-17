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