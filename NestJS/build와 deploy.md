## NestJS CLI
Nest 프로젝트를 시작할 때 제일 먼저 설치하는 것이 @nestjs/cli 패키지이다.
```
npm i -g @nestjs/cli
```

@nestjs/cli는 프로젝트의 기본 폴더 구성을 쉽게 할 수 있도록 해주고 빌드 및 번들링 등을 포함해 프로젝트 개발 및 유지 관리를 위한 여러가지 기능을 지원해주고 있다.

이번에 배포 CLI 패키지 개발을 위해 백엔드 팀은 NestJS에서 build는 어떻게 되고 결과물은 어떻게 나오는지,<br />
NestJS 애플리케이션을 실행시키기 위해서는 어떤 것들이 필요한지 찾아보았고<br />
관련 내용을 정리해보려고 한다!

<br />

### Standard vs Monorepo
build를 보기 전에 NestJS는 두가지 모드를 제공하는데, 각 모드에 따라 빌드 동작이 조금 다르다.

`nest new`로 프로젝트 생성 시 기본적으로는 standard 모드로 프로젝트가 생성된다.<br />
NestJS는 monorepo 모드도 지원을 하는데, 몇가지 차이점이 있지만 내장 라이브러리 지원, Nest의 기능 제공 등은 동일하게 적용된다.

```
💡 Monorepo

MonoRepo는 Monolithic Repositories 의 약자로, 하나의 레포지토리에서 여러개의 프로젝트가 구성된 것을 의미한다.

하나의 레포지토리에서 관리하기 때문에 관리가 쉽고 중복되는 코드는 공통화하여 재사용하기 편하다.
반면에 각 프로젝트마다 특정 버전의 모듈이 필요한 경우 다른 프로젝트에서는 충돌이 발생할 수도 있는, dependency 충돌의 문제가 생길 수도 있고 레포지토리의 크기가 커져서 속도가 느려질 수 있다는 단점도 있다.
```

Standard 모드와 Monorepo 모드는 빌드 시 명령어가 조금 다르고 사용되는 컴파일러도 다르다.<br />
Standard 모드는 `tsc`를, Monorepo는 `webpack`을 기본 컴파일러로 사용한다.<br />
그리고 build와 start 명령어 실행 시 Monorepo의 경우는 default project가 명령어의 타겟이 된다.


## Build
NestJS는 기본적으로 tsc에 의해 빌드가 이뤄지기 때문에 tsconfig.json에 설정된 옵션에 의해 빌드 결과물이 달라진다.

빌드 결과물은 default로는 `dist` 폴더 내에 생성이 되는데 `tsconfig.json`에 `outDir` 필드를 통해 다른 이름의 폴더로도 생성시킬 수 있다.

폴더 구조에 따라서도 결과물 내의 구조가 달라진다.
src 폴더와 동일하게 root 경로에 다른 폴더를 만들고 함께 빌드시킬 경우, dist 내 폴더 구조는 src 폴더와 해당 폴더로 만들어진다.

`@types`라는 폴더를 src와 동일한 위치에 생성한다면 dist 내부 구조는 다음과 같다.
```
dist
|- @types
|- src
|  |- main.js 
|  |- auth
|  |- products
.
.
```

위와 같이 생성되면 실행 명령어는 `node dist/src/main.js`가 되어야 한다.

<br />
root 경로가 아닌 src 폴더 내에 만든다면 다른 구조로 생성이 된다.

```
dist
|- @types
|- main.js
|- auth
.
.
```
이 때는 `node dist/main.js`로 실행이 되어야 한다.

어떤 식으로 폴더 구조를 잡아야하는지 정답이 있을까..?<br />
권장되는 구조가 있으려나..?<br />
그건 잘 모르겠는데 우리의 경우 `@types` 폴더를 프로젝트 진행하고 뒤늦게 추가했었는데<br />
추가할 때 root 경로에 생성해서 이전의 빌드 결과물과 달라지게 되면서 배포 시 명령어가 변경되었어야 했다.

처음에는 빌드 결과물이 달라진 줄 모르고 배포했을 때 에러가 나서 확인해보니 결과물이 달라져있는걸 발견했는데
그 때의 경험으로 폴더 생성 위치에 따라 결과물 폴더 내 구조가 달라지는 것을 알게 되었다.

## Deploy

현재 플랫폼 RestAPI 프로젝트를 배포할 때 `빌드 결과물 폴더`, `yarn.lock`, `package.json`, `ecosystem.config.json` 을 압축해서 배포하고 있다.

- package.json : 프로젝트의 정보 및 의존하고 있는 패키지의 정보 알 수 있다.
- yarn.lock : 설치된 패키지 간의 의존성에 대한 정보를 저장하고 있고, 최초 설치 시의 버전 정보를 저장하고 있다.
- ecosystem.config.json : pm2로 프로젝트 실행 프로세스를 관리하는데, pm2 인스턴스의 생성을 위한 정보를 미리 설정해놓는 파일

