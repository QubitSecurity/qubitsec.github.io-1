## Install Agents for datos

Datos는 웹 트래픽을 모니터링하기 위한 도구입니다.
모니터링을 위하여 네트워크 인터페이스의 포트 정보만을 사용하므로 다른 프로세스와 충돌은 발생하지 않습니다.

다음과 같은 환경에서 사용할 수 있습니다.

1) 웹 서버로 Apache HTTPD, Nginx 가 아닌 환경으로 예를 들면, Apache Tomcat 단독 사용인 경우

2) 컨테이너 환경으로, 호스트에 여러 웹 파드(POD)를 사용할 경우

### Linux Web Server – Datos 설치 영상

https://youtu.be/TqDUR002tt0


### 멀티 포트 로깅

Datos는 한 개의 에이전트에서 하나의 IP:PORT 데이터만 로깅할 수 있습니다.
여러개의 IP주소 또는 PORT 데이터를 로깅하려면, 필요할 개수만큼 Datos 에이전트를 생성해야 합니다.

Datos 에이전트를 추가 생성하는 방법은 아래와 같습니다.


#### 1. Datos 설정 파일 추가

```markdown
vi /etc/datos/conf/httplogger2.conf

interface=eth1
logfile=/var/log/plura/weblog2.log

host=172.16.10.52

port=8080
```

#### 2. Datos 서비스 파일 추가
```markdown
vi /etc/datos/conf/datos2.service

[Unit]
Description=Datos httplogger service

After=network.target

[Service]

Type=simple

User=root

WorkingDirectory=/etc/datos/

ExecStart=/etc/datos/httplogger -name datos2 /etc/datos/conf/httplogger2.conf

ExecStop=/etc/datos/httplogger -name datos2 -stop

Restart=always

[Install]

WantedBy=multi-user.target
```


#### 3. Datos 서비스 추가 등록
```markdown
cp /etc/datos/conf/datos2.service /usr/lib/systemd/system/datos2.service

systemctl enable datos2.service
```


#### 4. Datos 서비스 추가 실행
```markdown
service datos2 start
```

#### 5. 웹UI 서버관리에 파일 등록