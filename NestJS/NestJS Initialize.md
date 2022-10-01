NestJS를 다음 프로젝트에서도 사용하기로 해서 처음 프로젝트 생성부터 해볼 수 있는 기회가 생겼다

## CLI 설치
NestJS는 cli로 쉽게 프로젝트를 생성할 수 있어서 우선 @nestjs/cli를 설치해야한다. <br />

```bash
npm i -g @nestjs/cli
```

<br />

## Project 생성
공식문서에 나와있는 것처럼 new를 사용해서 아주 쉽게 생성이 가능하다.<br />

```bash
nest new <project 이름>

// 현재 폴더에서 바로 프로젝트를 생성하고 싶을 경우 . 만 쓰면 된다
nest new .
```

<br />

## project 구조

기본적으로 생성되는 폴더 구조는 다음과 같다. <br />

src
|- app.controller.spec.ts
|- app.controller.ts
|- app.module.ts
|- app.service.ts
|- main.ts

<br />

- app.controller.ts : 단일 라우터로 되어있는 기본 컨트롤러
- app.controller.spec.ts : 컨트롤러를 위한 테스트 파일
- app.module.ts : 애플리케이션의 루트 모듈
- app.service.ts : 단일 함수로 되어있는 기본 서비스
- main.ts : 애플리케이션의 entry file, NestFactory를 사용해 Nest 애플리케이션 인스턴스를 생성한다

<br />

## Platform
Nest는 플랫폼에 구애받지 않는 프레임워크를 목표로 하고 있기 때문에 기술적으로는 모든 노드 HTTP 프레임워크와 함께 작동할 수 있다.<br />
express와 fastify는 바로 사용할 수 있도록 지원이 되고 있다.
Nest 내부적으로는 express를 사용하고 있다.

