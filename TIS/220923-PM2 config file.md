# Configuration File
PM2로 여러 애플리케이션을 관리할 때, 자바스크립트 설정 파일을 사용한다.

## Generate configuration
설정 파일을 생성하기 위해서는 이 커맨드로 할 수 있다.
```bash
$ pm2 init simple
```

이러면 ecosystem.config.js 을 생성하고 다음과 같은 내용으로 설정되어있다.
```js
module.exports = {
    apps : [{
        name : "app1",
        script : "./app.js"
    }]
}
```

pm2가 인식할 수 있는 설정 파일을 만드려면 `.config.js`로 끝나도록 만들어주면 된다.

<br />

## Acting on Configuration File
설정 파일에 포함되어있는 모든 앱을 시작/중지/재시작/삭제할 수 있다.

```bash
# Start all applications
$ pm2 start ecosystem.config.js

# Stop all
$ pm2 stop ecosystem.config.js

# Restart all
$ pm2 restart ecosystem.config.js

# Reload all
$ pm2 reload ecosystem.config.js

# Delete all
$ pm2 delete ecosystem.config.js
```

### Act on a specific process
`--only` 옵션을 사용하면 특정 애플리케이션에서만 동작할 수 있도록 할 수도 있다.

```bash
$ pm2 start ecosystem.config.js --only api-app
```

start뿐만 아니라 start, restart, stop, delete 모두 --only 옵션을 사용할 수 있다.<br />
또한 애플리케이션 이름을 쉼표로 구분해 지정하면 여러 앱에 적용될 수 있게 할 수 있다.

```bash
$ pm2 start ecosystem.config.js --only "api-app,worker-app"
```

<br />

## Switching environments
`env_*` 을 통해 다른 환경 변수를 지정할 수 있다.

```js
module.exports = {
  apps : [{
    name   : "app1",
    script : "./app.js",
    env_production: {
       NODE_ENV: "production"
    },
    env_development: {
       NODE_ENV: "development"
    }
  }]
}
```

production 환경과 development 환경에 따라 다른 환경 변수를 사용할 수 있다.<br />
실행할 때 --env 옵션으로 실행 환경을 변경시킬 수 있다.

```bash
$ pm2 start process.json --env production
$ pm2 restart process.json --env development
```

<br />

## Attributes available
[공식 홈페이지](https://pm2.keymetrics.io/docs/usage/application-declaration/)에서
사용 가능한 옵션들을 볼 수 있다.

<br />

## Considerations
json 애플리케이션을 사용할 때 커맨드 라인에 전달된 모든 옵션은 삭제된다.

<br />

### CWD
설정 파일은 스크립트와 함께 있을 필요는 없다.<br />
다른 위치에 (ex. /etc/pm2/conf.d/node-app.json) 두려면 cwd 기능을 사용해야 한다.<br />
(심볼릭 링크를 사용하는 capistrano 스타일 디렉토리 구조에 유용하다.)<br />
파일은 cwd 디렉토리에 상대적이거나 절대적일 수 있다.<br />

흐잉 이거 무슨 말인지 모르겠엉..<br />

** Capistrano
여러 서버에서 스크립트를 실행하기 위한 오픈 소스 도구.<br />
주요 용도는 웹 응용 프로그램 배포이다.<br />
데이터베이스 변경과 같은 지원 작업을 포함하여 하나 이상의 웹 서버에서 사용 가능한 새 버전의 응용 프로그램을 만드는 프로세스를 자동화한다.

capistrano 검색하면 이거만 나오는데 이거 말하는거 맞나..?
cwd 기능은 뭐여,,