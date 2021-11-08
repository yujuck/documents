Docker를 사용해서 처리해야할 일이 생겼는데 한번도 사용해본 적이 없어서 
일단 기본기라도 익혀보자! 해서 보게 된 유튜브 생활코딩의 Docker 입문 수업 영상..!
뭔가 진짜.. 맛보기만 한 느낌이지만.. 그 이후에 공부하는건 내 몫이기 때문에ㅠㅠ
알게 된 것만이라도 남겨보자..!


# Docker란?

도커에 대한 정의를 알아두는 것이 기본일 것 같아서 가지고 있던 책을 한번 참고해봤는데..

> 컨테이너형 가상화 기술을 구현하기 위한 상주 애플리케이션과 이 애플리케이션을 조작하기 위한 명령행 도구로 구성되는 프로덕트

라고 설명이 되어있다.. 
검색해서 찾아보면 `컨테이너 기반의 오픈소스 가상화 플랫폼`이라는 설명이 많이 나온다.
영상만 보고 난 후 이해한 docker는 원하는 가상환경을 컨테이너 기반으로 쉽게 구축할 수 있게 해주는 도구..? 플랫폼..? 정도인 것 같다.

docker로 배포도 할 수 있는건 알고 있으니까 `애플리케이션을 빠르게 구축및 배포할 수 있는 플랫폼이`라고 하면 좋을 것 같다.

### container 기반

container는 단어가 계속 나오고 있는데, 기본적으로 우리 컴퓨터의 OS가 설치되어있는 곳을 `host`라 하고 docker로 실행시킨 `각각의 실행환경`을 `container`라고 한다.

container에는 라이브러리와 실행파일만 설치되어있다.

# docker hub, image, container

## docker hub

docker hub를 app store에 비유해주셨는데, docker hub에는 우리가 필요한 소프트웨어들이 올려져있다. 여기서 설치하고 싶은 image를 찾아서 설치하면 되는데, docker hub에서 image를 가져와서 설치하는 것을 pull 받는다고 한다.

```
docker pull imageName
```

## image

위에서 말한 것처럼 docker hub에서 pull 받아온 것이 image인데, 컨테이너 실행에 필요한 파일과 설정 값 등을 포함하고 있는 것이다.
docker hub이 app store라면 image는 프로그램으로 비유가 된다.
image를 pull 받아오면 한 개의 image로 여러 개의 container를 만들 수 있다.
image를 실행시켜 container로 만드는 것을 run한다고 말한다.

```
docker run image
```

## container

container는 프로그램을 실행시키면 생성되는 프로세스로 비유할 수 있다.
image를 run 해서 container를 생성하는데, 하나의 프로그램이 여러개의 프로세스를 가질 수 있는 것처럼
하나의 image로 여러 개의 container를 생성할 수 있다.
때문에 container를 만들 때에는 이름을 지정해서 관리하는 것이 좋다.


# 기본적인 docker 명령어

### docker run --name containerName image 

--name 뒤에 생성할 container의 이름을 적고 container를 만들 이미지 이름을 적으면 된다.

### docker ps

실행중인 container의 목록을 볼 수 있고, 뒤에 -a를 붙이면 실행중이 아닌 container도 확인할 수 있다.

### docker stop containerName

컨테이너의 실행을 중단시키는 명령어

### docker rm containerName

컨테이너를 삭제하는 명령어. 실행중인 컨테이너는 삭제하지 못하기 때문에 stop시킨 후 삭제해야한다.

하지만 rm 뒤에 --force 옵션을 붙이면 stop시키지 않고 바로 삭제할 수 있음

### docker logs containerName

실행중인 컨테이너의 로그를 확인할 수 있는 명령어.

명령어를 입력하면 입력하는 순간까지의 로그를 볼 수 있는데 실시간으로 보고 싶으면 컨테이너 이름 앞에 -f 옵션을 붙이면 된다.

### docker rmi images

이미지 삭제 명령어

### docker exec -it containerName /bin/sh

실행중인 컨테이너를 대상으로 쉘 연결. 해당 컨테이너에 지속적으로 쉘 명령어를 입력할 수 있게 해준다.

연결 끊을 때는 exit.

bash쉘을 쓰고 싶을 때는 /bin/bash로 실행하면 된다. (sh가 기본이고 bash가 없을 수도 있음)

# 컨테이너 내부 index.html 수정해보기

```
docker run -p 8080:80 --name ws2 httpd
```

위와 같이 ws2라는 컨테이너를 생성했다.
localhost 8080포트를 연결시켜두었기 때문에 주소창에 `localhost:8080`을 입력하면 컨테이너 내부에 있는 index.html파일의 내용을 볼 수 있다.

기본 index.html의 위치는 usr/local/apache2/htdocs인데, 내용을 바꾸고 싶으면 해당 위치의 index.html을 변경하면 된다.

컨테이너는 기본적으로 용량이 작은 것이 덕목(!)이라고 해서 내부에 vim이나 nano 같이 편집툴이 설치되어있지 않아 컨테이너 내부에서 편집하려면 직접 설치를 해줘야 한다.

설치 후 htdocs 안에 있는 index.html을 수정하면 수정한 내용이 localhost의 8080으로 접속했을 때 보이는 것을 확인할 수 있다.

# host 파일과 연결하기

위처럼 실행 중인 컨테이너 안에서 직접적으로 파일을 수정하는 것은 불편하기도 하고,
실수로 컨테이너를 삭제하면 작업 내용이 사라지기 때문에 좋은 방법은 아니다.

컨테이너를 사용하는 이유는 필요할 때 언제든지 생성했다가 필요가 없어지면 지울 수 있는 것인데, 컨테이너 내부에서의 작업은 이런 장점을 살리지 못한다.

그러면 작업 파일을 host에 두고 변경하는 내용이 container에 반영이 되어 실행이 되면 제일 좋은데, 이때 사용하는 옵션이 -v 옵션이다.

```
docker run -p 8888:80 -v ~/Desktop/htdocs:/usr/local/apache2/htdocs/ httpd
```

이렇게 하면 localhost의 8888은 apache 서버의 80포트와 연결이 되고, desktop/htdocs는 httpd로 실행한 컨테이너의 htdocs와 연결이 된다.

따라서 desktop/htdocs/index.html을 수정하면 컨테이너 내의 htdocs/index.html에 적용되어 localhost:8888에 접속했을 때 desktop에서 수정한 내용을 볼 수 있다.

이렇게 해서 좋은 점은 작업을 vscode같은 에디터를 사용해서 편하게 작업할 수 있다는 점과
코드의 버전관리도 가능하다는 점이다.


# 영상 끝!

위의 내용이 입문 수업 영상에서 알려준 내용 

내가 해야하는 작업은 아마존 람다에 아마존 리눅스 OS 위에서 실행시켜서 올려야하는 작업인데, 이 작업을 위해 docker를 직접 사용해본 적은 없지만 찾아본 적은 있어서..
`dockerfile`을 작성해서 사용하는 방법이라던가..
서버리스를 사용해야 한다는.. 
그런 다른 복잡함(?)이 있다는 것을 알기 때문에 굉장히 기초적인 영상이구나! 를 느꼈지만
기초도 모르고 찾아보는 것과 알고 보는 것은 다를테니..! 조금이나마 기대중이다..ㅠㅠ
얼른 완성시키고 싶은 작업이라.. 열심히 더 찾아볼 예정!!!!!!!!!!!