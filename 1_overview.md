# Overview

## Nginx vs Apache
### Apache
- prefork mode - 프로세스의 수를 세팅해놓고 각각 프로세스가 리퀘스트 타입이 어떤 것인지에 상관없이 하나의 프로세스를 서빙하는 방식
### Nginx
- Asynchronous - 하나의 엔진엑스 프로세스 여러 개의 means single nginx process can serve multiple concurrently depending on the system resource available to nginx process
- 아파치와 다르게 요청의 모든 dynamic content는 php등의 완전히 분리된 프로세스에서 동작하고, 다시 reverse proxy가 nginx를 통해서 클라이언트로 돌아온다
- 아파치는 모든 리퀘스트에서 오버헤드가 있지만, 엔진엑스의 경우에는 스태틱 리소스를 전송할때 아파치보다 훨씬 빠른 성능을 자랑한다

> 엔진엑스가 아파치보다 빠르다!

> Fast static Resources & Higher Concurrency

- incoming request를 다루는 방식
    - 아파치: file systme location
    - 엔진엑스: uri location