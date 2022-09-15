# Nestjs Build Command

@nestjs/nest-cli에서는 다양한 커맨드를 제공하고 있는데 이번에 deploy 패키지를 만들면서 제일 많이 찾아본 것이 build 커맨드이다.<br />
NestJS에서 빌드할 때 어떤 폴더가 만들어지고 어떤 파일들이 생성되는지를 파악했어야 했는데,<br />
빌드할 때 프로젝트의 구조에 따라 dist(빌드 시 만들어지는 폴더 - default) 폴더의 구조 또한 달라지고<br />
그에 따라 main.js의 위치도 마찬가지로 달라지는 것을 확인했다.

main.js는 Entry File이기 때문에 위치가 중요한데<br />
프로젝트의 폴더 구조에 따라 달라지면 start 커맨드 실행 시에는 어떻게 main의 위치를 알고 실행시키는지,<br />
개발자가 지정해줘야만 알 수 있는지에 대해서 찾아보려고 하다보니 먼저 build 때 왜 폴더 구조가 달라지는지를 알아야 할 것 같아서<br />
거의 하루동안 @nestjs/nest-cli 코드만 본 것 같다..

<br />

## build.command.ts
build 커맨드 동작에 대해 정의한 파일이다.<br />
커맨드 입력되면 정의한 옵션의 값을 가지고 build.action.ts의 handle 함수를 호출한다.

<br />

## build.action.ts
handle 함수에서는 runBuild 함수 호출하는 부분이 중요하다.<br />
위에서는 watch 옵션에 대한 컨트롤을 하고 최종적으로는 runBuild 함수를 호출하고 마무리가 된다.

<br />

### runBuild
build 명령어 실행 시 config 옵션으로 들어온 값을 찾아서 configFileName으로 설정한다.<br />
(--config : nest-cli의 configuration file 경로를 지정할 수 있다.)<br />
해당 경로의 파일을 읽어서 configuration으로 설정한다.<br />

config file을 읽는 과정은 `nest-configuration.loader.ts` 의 load를 사용한다.<br/>
config file의 값이 있으면 해당 파일을 읽고,<br />
없는 경우(옵션 사용 안하면 undefined)에는 `.nestcli.json, .nest-cli.json, nest-cli.json, nest.json` 중에 하나를 읽어온다.<br />
저 파일들 마저도 없는 경우 defaultConfiguration를 리턴한다.

defaultConfiguration은 다음과 같이 설정되어있다.
```js
{
	"language": "ts",
	"sourceRoot": "src",
  	"collection": "@nestjs/schematics",
  	"entryFile": "main",
  	"projects": {},
  	"monorepo": false,
  	"compilerOptions": {
    		"tsConfigPath": getDefaultTsconfigPath(),  // tsconfig.build.json이 있으면 tsconfig.build.json, 없으면 tsconfig.json
    		"webpack": false,
    		"webpackConfigPath": 'webpack.config.js',
    		"plugins": [],
    		"assets": [],
  	},
  	"generateOptions": {},
}
```

읽어온 내용이 있는 경우에는 `defaultConfiguration과` 읽어온 내용을 합쳐서 리턴한다.<br />

참조할 tsConfig 파일의 경로는 configuration의 `compilerOptions.tsConfigPath`로 설정된다.<br />
해당 경로의 파일을 읽어서 tsOptions로 선언한다.(`tsOptions = tsConfig 파일 내용`)<br />

```
{
  module: 1,
  declaration: true,
  removeComments: true,
  emitDecoratorMetadata: true,
  experimentalDecorators: true,
  allowSyntheticDefaultImports: true,
  target: 4,
  sourceMap: true,
  outDir: '/Users/yujin/Desktop/RedPlatformForLegacy/Red-Platform-RestApi/dist',
  baseUrl: '/Users/yujin/Desktop/RedPlatformForLegacy/Red-Platform-RestApi',
  incremental: true,
  configFilePath: '/Users/yujin/Desktop/RedPlatformForLegacy/Red-Platform-RestApi/tsconfig.build.json'
}
```
tsOptions는 위와 같은 내용으로 설정된다.

<br />

`outDir`는 tsOptions.outDir 사용하고 (없으면 defaultOutDir 사용 - `dist`)<br />
webpack 사용 여부는 `compilerOption.webpack` 참조한다.<br />

outDir를 삭제하는지 확인 후 (compilerOptions.deleteOutDir가 true이면 삭제)<br />
assets이 있으면 복사한다.<br />

<br />

assets도 직접 outDir을 정할 수 있기 때문에 dist의 구조에 영향을 준다.<br />
근데 이거는.. main.js의 위치에는 영향이 없고 그냥 지정한 위치에 만들 수 있게 하는 옵션이기 때문에.. 괜찮을듯..?<br />

복사할 때, 현재 작업 디렉토리 경로에 configuration에 있는 sourceRoot를 join해서 sourceRoot로 정의하고<br />
assets에 string으로만 되어있으면 outDir은 위에서 설정한걸로 되어 복사되고<br />
string이 아니면 (assets에 대해 좀더 상세하게 적은 경우) 해당 정보대로 outDir, exclude, flat 등의 설정을 유지한채로 복사한다.<br />

이후에는 webpackEnabled와 watchMode 여부에 따라 다른 처리가 되고<br />
(webpack -> webpackCompiler 실행, watchMode -> watchCompiler 실행)<br />
둘다 false인 경우에는 compile 하고 끝난다..<br />

<br />

## complie.ts
compile하는 부분에 분명히 폴더 구조를 바꾸는 무언가가 있을거라고 생각을 했는데쉽게 못찾았다..ㅠㅠ<br />

그러다가 `emitResult.sourceMaps`을 보면서 내부에 있는 sourceMap이 Object?로만 나오길래 접근해서 확인해보니 폴더 구조에 따라 달라지는 것을 확인했다..!<br />
찾기가 어려웠던 이유 중에 하나는 `emitResult.sourceMaps`가 dist 폴더가 없을 때만 값이 존재하고<br />
dist 폴더가 이미 존재하는 경우에는 빈배열로 나와서 몇번은 그냥 넘어간 것 같다ㅠㅠ<br />

```
const emitResult = program.emit(undefined, undefined, undefined, undefined, {
        before: tsconfigPathsPlugin
          ? before.concat(tsconfigPathsPlugin)
          : before,
        after,
        afterDeclarations,
});
```
emitResult는 `program.emit()`의 실행 결과인데, 이 program.emit() 실행되면서 바뀌는 것 같다..아마도..<br />

폴더 구조에 따라 달라지는 부분은<br />
emitResult.sourceMaps에 inputSourceFileNames와 sourceMap 필드가 있는데<br /> 
sourceMap.sources를 보면 달라진다<br />
src 폴더 외에 다른 폴더가 있으면 `'../../src/main.ts'`<br />
src 폴더만 있으면 `'../src/main.ts'` 로 나온다.<br />

program.emit()이 어떤 함수길래 다른 결과가 나오는지는...<br />
확인해보고 있는 중에 다른 일도 생기고 퇴근 시간이 되어서..<br />
내일 다시 확인해보는걸로..!





