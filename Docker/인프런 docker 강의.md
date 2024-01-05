- exec : 실행중인 컨테이너에 명령어 전달
- -it : i 옵션과 t 옵션을 합쳐서 실행. 명령어를 실행한 후 계속 명령어를 적을 수 있게 함.
	- -i : interactive. 상호적인
	- -t : terminal

컨테이너에 명령어 하나 입력할 때마다 `docker exec -it 컨테이너아이디 명령어` 를 계속 입력해줬어야 했는데
마지막 명령어로 `sh` 를 입력하면 컨테이너 안의 쉘이나 터미널 환경으로 접속할 수 있음
`docker exec -it 컨테이너아이디 sh/bash/zsh/powershell`
- sh 뿐만 아니라 다른 쉘 타입을 넣을 수 있음
- sh 가 가장 범용적으로 사용할 수 있음
- 터미널을 빠져나올 때 ctrl+c 가 아닌 ctrl+d 로 빠져나올 수 있음

----

도커 이미지로 컨테이너 생성할 때는 `docker create 이미지이름`

도커 이미지 생성하는 순서
1. Dockerfile 작성 
2. 도커 클라이언트로 전달
	 - 도커 파일에 입력된 것들이 도커 클라이언트에 전달되어야 함
3. 도커 서버로 전달
	- 도커 클라이언트에 전달된 모든 중요한 작업들을 하는 곳
4. 이미지 생성


Dockerfile
: Docker Image를 만들기 위한 설정 파일. 컨테이너가 어떻게 행동해야 하는지에 대한 설정들을 정의함

도커 파일 만드는 순서 (도커 이미지가 필요한 것이 무엇인지를 생각하기)
1. 베이스 이미지 명시 (파일 스냅샷에 해당)
2. 추가적으로 필요한 파이를 다운 받기 위한 몇가지 명령어를 명시(파일 스냅샷에 해당)
3. 컨테이너 시작 시 실행될 명령어를 명시 (시작 시 실행될 명령어에 해당)

베이스 이미지
- 도커 이미지는 여러개의 레이어로 되어있는데, 그 중 베이스 이미지는 이미지의 기반이 되는 부분.
- OS라고 생각하면 됨


```dockerfile
# 베이스 이미지 명시
FROM baseImage

# 추가적으로 필요한 파일들을 다운로드 받는다.
RUN command  

# 컨테이너 시작시 실행 될 명령어를 명시
CMD ["executable"]
```

FROM, RUN, CMD 등은 도커 서버에게 무엇을 하라고 알려주는 것과 같음
- FROM
	- 이미지 생성 시 기반이 되는 이미지 레이어
- RUN
	- 도커이미지가 생성되기 전에 수행할 쉘 명령어
- CMD
	- 컨테이너가 시작되었을 때 실행할 실행 파일 또는 쉘 스크립트
	- 해당 명령어는 Dockerfile 내 1회만 사용 가능


-----

도커 파일에 입력된 것들이 도커 클라이언트에 전달되어서 도커 서버가 인식하게 해야함
그렇게 하기 위해서는 `docker build ./` 또는 `docker build .` 명령어 실행하면 됨
- ./ 처럼 / 까지 쓰는게 권장됨

build 명령어는 해당 디렉토리 내에서 dockerfile이라는 파일을 찾아 도커 클라이언트에 전달함


베이스 이미지에서 다른 종속성이나 새로운 커맨드를 추가할 때는 임시 컨테이너를 만든 후
그 컨테이너를 토대로 새로운 이미지를 만듦
그리고 그 임시 컨테이너는 지워준다.

----------

도커 2.4.0+ 버전 업데이트로 인해서 Buildkit을 기본적으로 사용할 수 있게 됐습니다.
이 Buildkit을 사용하므로 인해 나타나는 현상은 도커 파일을 빌드할 때 나타납니다. 

빌드하는 과정에서 다른 출력 문구를 내보냅니다.
그중에서 가장 주요한 차이점은 빌드 프로세스 마지막 부분에 나오는 이미지 ID입니다. 

기존 빌드 프로세스에서 나오는 출력 문구
--->   bd70880ecc90 
Successfully built bd70880ecc90 

Buildkit이 기본으로 사용될 때 빌드 프로세스에서 나오는 출력 문구   
=> => exporting layers  0.0s
=> => writing image sha256:aa19c32ksj94839dj2-2039ccbd9cddd 0.0 s 

결론 적으로는 두 개의 ID 모두 컨테이너를 실행할 수 있는 아이디입니다.   
docker run bd70880ecc90 
docker run aa19c32ksj94839dj2-2039ccbd9cddd   

이 둘 모두 같은 컨테이너를 실행하게 됩니다.
그러기에 이 강의를 더 쉽게 따라가기 위해서는 Buildkit을 비활성화하시면 됩니다. 

 Buildkit 비활성화하는 방법 
1. 도커 아이콘을 클릭합니다. 
2. 아래 그림과 같이 Preferences 버튼을 클릭합니다.   
3. Docker Engine 버튼을 클릭합니다. 
4. 마지막으로 buildkit 키의 값을 false로 바꿔줍니다.

------------

도커 이미지에 이름 주는 방법
- -t 옵션 사용을 사용해 이름을 지정할 수 있음
- `-t ` `도커아이디`  `/`  `저장소/프로젝트이름`  `:`  `버전`

----

workdir을 설정하지 않고 그냥 COPY 하면 안좋은 이유
- 원래 파일 시스템의 파일/폴더와 이름이 같으면 덮어씌워짐
- 모든 파일/폴더가 하나의 디렉토리에 모두 저장되어 지저분해짐

그래서 work directory를 설정해준다

-----

소스 변경으로 다시 빌드하는 것에 대한 문제점
- 소스 코드가 변경되면 다시 이미지 생성하고 다시 컨테이너를 생성한 뒤 앱을 실행시켜 주고 있었음
-> 굉장히 비효율적
-> Volume 사용하기!

** 어플리케이션 소스 변경으로 재빌드 시 효율적으로 하는 방법
> volume 사용이 아닌 그냥 이미지 재빌드 시 효율적으로 하는 방법

기존
```dockerfile
FROM node:10

WORKDIR /usr/src/app

COPY ./ ./

RUN npm install

CMD ["node", "server.js"]
```

변경
```dockerfile
FROM node:10

WORKDIR /usr/src/app

COPY package.json ./

RUN npm install

COPY ./ ./

CMD ["node", "server.js"]
```


코드만 수정하게 되는 경우 npm 종속성에는 변화가 없기 때문에 또 install을 할 필요가 없음
package.json만 먼저 copy한 다음 install을 하면 변화가 없어서 cache를 사용하게 되는데, 
전체 코드를 복사하고 npm install을 하면 또 다시 종속성을 다운받는 과정을 거치기 때문에 비효율적이고 빌드 속도 차이가 남.


Docker Volume
- 로컬에 있는 파일을 도커 컨테이너에서 계속 참조하게 함 (Mapping)

volume 사용해서 어플리케이션 실행하는 법
`docker run -p 5001:8080 -v /usr/src/app/node_modules -v $(pwd):/usr/src/app 이미지아이디`
- `-v /usr/src/app/node_modules` : 호스트 디렉토리에 node_modules가 없으므로 참조하지 말라는 의미
- `-v $(pwd):/usr/src/app` : pwd 경로에 있는 디렉토리 혹은 파일을 /usr/src/app 경로에서 참조
	- : 가 구분자가 되어 앞에는 로컬의 디렉토리, 뒤에는 컨테이너 내부의 경로를 적어준다

이렇게 하면 코드를 바꿨을 때 이미지를 다시 빌드하지 않아도 변경된 사항을 적용할 수 있음.
그대신 container를 껐다가 다시 키긴 해야함

----

docker compose
: 다중 컨테이너 도커 어플리케이션을 정의하고 실행하기 위한 도구

컨테이너를 여러개 사용해서 어플리케이션을 구성할 때, 컨테이너 간 네트워크를 연결시켜주는 것이 docker-compose 이다.

docker compose up vs docker-compose up --build
- docker-compose up : 이미지가 없을 때 이미지를 빌드하고 컨테이너 시작
- docker-compose up --build : 이미지가 있든 없든 이미지를 빌드하고 컨테이너 시작

-d옵션 사용하기
: -d 는 detached 모드로서, 앱을 백그라운드에서 실행시킴. 앱에서 나오는 output을 출력하지 않음


docker compose 종료 : `docker-compose down` 명령어 사용











