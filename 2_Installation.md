# Create a host server
- 강의상에선 디지털오션의 가장 저렴한 티어 사용, 나의 경우엔 AWS t2.micro로 구축
- 환경은 우분투

## 패키지 매니저 업데이트
- 우분투의 패키지매니저는 예전엔 apt-get으로 썻지만 지금은 그냥 apt라고 써도 된다.
- 다음의 커맨드를 사용해서 업데아트를 하자.
> apt-get upgrade

- update는 업데이트할 리스트를 확인하는 명령어에 불과함

## Nginx 설치
> apt install nginx
- nginx 프로세스 확인: ps aux|grep nginx
- 제대로 작동하는지 확인하기 위해서 아래의 명령어를 쳐보자
> nginx -t
- 잘 될경우엔 ifconfig를 통해서 ip를 확인해본 후 해당 ip를 브라우저에 쳐서 nginx 화면이 뜨는지 확인한다
    * AWS를 사용한 경우엔 퍼블릭 ip로 확인해볼 수 있다.

## Nginx 소스로 직접 빌딩하기
엔진엑스 관련해선 아래의 두 사이트가 있다.
- [Nginx Org](https://nginx.org/): docs
- [Nginx Com](https://www.nginx.com/): product side

여기서 org 사이트로 가서 mainline의 소스코드를 직접 받아본다. 우분투의 경우엔 wget [sourcecode link] 를 통해서 받을 수 있다.
받은 후에는 다음의 명령어로 필요한 패키지도 설치해준다
> sudo apt install build-essential

이 명령어를 실행한 후에 소스코드 디렉토리 내에서 ./configure을 실행하면 잘 실행되는 것을 볼 수 있다. 이제 다른 필수 패키지도 설치해보자
> sudo apt install libpcre3 libpcre3-dev zlib1g zlib1g-dev libssl-dev

엔진엑스를 소스로부터 빌딩할 때 관련 옵션은 [다음 링크](https://nginx.org/en/docs/configure.html)에서 확인할 수 있다.
- ssl을 추가한 예제
> ./configure --sbin-path=/usr/bin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --with-pcre --pid-path=/var/run/nginx.pid --with-http_ssl_module

다소 번거롭긴 하지만 소스로부터 빌딩할 때는 다양한 커스터마이징과 모듈추가가 가능하다는 장점이 있다.

## Nginx Service
- 다음의 명령어로 엔진엑스를 멈출 수 있다.
> nginx -s stop

- init 스크립트의 [systemd항목](https://www.nginx.com/resources/wiki/start/topics/examples/systemd/)으로 가자. 여기에서 보이는 위치인 /lib/systemd/system/에 nginx.service 파일을 만들고, 해당 파일이 비어있다면 위 링크에 있는 예시를 그대로 복붙해보자.
- 그 다음 systemctl daemon-reload && systemctl start nginx 로 nginx를 재시작해보자
- 현재까지의 형태론 시스템을 끄면 엔진엑스 역시 구동을 멈추게 되어있으므로 웹서버로서 적합하지 못하다. 이 문제를 해결하기 위해서 아래의 커맨드를 입력한다.
> systemctl enable nginx
- 이 명령어를 통해 이 서비스에 대한 startup symlink를 생성했다. 재부팅을 통해서 확인해볼 수 있다.



