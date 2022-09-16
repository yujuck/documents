16일에 본 내용인데 12시가 지나버려서 과거 기록이 되어버린..

## NestJS Build Command
15일에 이어서 build 커맨드 입력 시 진행되는 코드들을 쭉 봤다.<br />
어느 시점부터 fileNames라고 어떤 콜백 함수에서 받은 파라미터의 값이<br />
/dist/src/main.js 또는 /dist/main.js 처럼 폴더 구조에 따라 달라지는 것까지는 발견을 했는데<br />
도저히 왜 그렇게 바뀌게 되는건지는 진짜 엄청 여기저기 읽었는데도 못찾았다..ㅠㅠㅠ<br />

그리고 보다보니 이게 NestJS의 동작이라기보다 typescript 패키지의 파일을 보게 되어서,,<br />
아무래도 컴파일러로 tsc를 사용하다보니 이렇게 된거긴 한데<br />
너무 돌고 도는 중인 것 같아서 포기하고 start 커맨드 동작을 확인해보았다.

## NestJS Start Command
결론부터 말하자면 원래 찾으려던 정보,<br />
즉 "NestJS에서 start 커맨드를 입력했을 때 폴더 구조에 따라 달라지는 entry file의 위치를 어떻게 알고 실행시키냐!" 에 대한 정보를<br />
아 주 쉽 게 찾 았 다 !!!!!ㅠㅠ흑흑...<br />
정말 머리 아프게 찾아봤는데...ㅠ<br />
그래도 빌드 커맨드 동작도 알게 된거니까 헛된 시간을 보낸건 아닌걸로..ㅠ<br />

start command에 대한 내용은 `start.action.ts`를 보면 된다.<br />
전체적인 흐름은 build command와 동일하게 start 커맨드 시 입력받은 옵션에 따라 먼저 값들을 설정하고<br />
`runBuild`를 호출하는데 이때 build할 때와는 다르게 맨 마지막 파라미터로 빌드가 성공적으로 다 된 후에 실행할 함수를 넣어준다.<br />
start를 하면 빌드도 같이 되는 것이 이것 때문이다.<br />
어떻게 보면 빌드 동작에 실행이 추가된 느낌..?

이 부분에서 우리가 봐야하는건 `outDir, sourceRoot, entryFile`이다.<br />
outDir 제외하고 sourcrRoot, entryFile 모두 커맨드 입력 시 옵션으로 받을 수도 있고<br />
세가지 모두 config file에서 설정 가능한 값이다.<br />

`outDir`은 `tsConfig의 outDir`을 보거나 없으면 default값을 사용한다. (`default : dist`)<br />
`sourceRoot`와 `entryFile`은 `configuration(nest-cli.json)`에 있는 값을 읽어오거나 없으면 default값을 사용한다.<br />
(default는 `sourceRoot: src, entryFile : main`이다)<br />

위의 값들을 모두 변수로 선언하고 아까 말한 것처럼 그 후에 성공적으로 빌드가 되었을 때 실행할 hook을 생성하는데,<br /> 
이때 `outputFilePath`를 만든다.<br />
spawnChildProcess() 함수를 보면 outputFilePath를 선언하고 있고 전달받은 outDirName, sourceRoot, entryFile을 사용해 만든다.<br />

그러면 보통의 경우, 아무런 옵션 전달 없이 start command를 사용한다 하면<br />
outDirName, sourceRoot, entryFile에는 `<프로젝트 경로>/dist, src, main`가 들어온다.<br />
outputFilePath는 다음과 같이 만들어진다.
```js
let outputFilePath = join(outDirName, sourceRoot, entryFile);
if (!fs.existsSync(outputFilePath + '.js')) {
  outputFilePath = join(outDirName, entryFile);
}
```

기본적으로는 `outDir, sourceRoot, entryFile`을 `join`해서 만든다.<br />
그 후 .js 를 붙여서 실제 그 경로에 main이 있는지 확인하고, 없다면 `outDir와 entryFile`만 `join`해서 다시 만든다.<br />
폴더 구조에 따라 main.js의 위치가 달라진다고 했지만 어떻게 보면 두가지 경우로만 만들어진다.<br />
outDir의 최상위에 있거나 sourceRoot 밑에 있거나 둘 중 하나이기 때문에 이렇게 설정되는 것 같다.<br />

어쨌든...<br />
원래 찾으려던 정보는 찾은거라 좋긴 한데..<br />
이렇게까지 찾아본거 언제 뭐 때문에 빌드 결과물이 달라지는지도 알면 좋을텐데<br />
이리저리 확인하느라 머리도 너무 아팠고ㅠㅠ 지쳤다...흑흑<br />
누가 좀 찾아서 알려줬으면..

