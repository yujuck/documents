## svelte 폴더 구조
sveltejs/template로 프로젝트를 생성했을 때,<br />
기본적인 svelte의 폴더 구성을 다음과 같다.

```
svelte
│   └── public
│        ├── favicon.png
│        ├── global.css
│        └── index.html
├── scripts
│        ├── setupTypeScript.js
├── src
│   ├── App.svelte
│   └── main.js
├── package.json
└── rollup.config.js0
```
<br />

### src 폴더
프로젝트를 처음 생성하면 기본적으로 App.svelte와 main.js가 src 폴더에 들어있다.<br />

App.svelte는 사용하는 컴포넌트들을 담는, 모든 컴포넌트의 부모 컴포넌트라고 보면 되고,<br />
main.js는 가장 먼저 실행되는 js파일로, js 및 서비스의 시작점이라고 생각하면 된다.(rollup.config.js에서 엔트리 포인트로 설정함)<br />
기본 구성을 App.svelte 컴포넌트에서 가져오고 App 인스턴스를 생성한다.<br />


src에는 모든 사용자 정의 svelte 코드를 저장하면 된다.<br />

<br />

### public/build
Svelte가 수행한 컴파일 결과가 들어간다.

<br />

### rollup.config.js
Rollup이라는 모듈 번들러의 설정 파일이다.<br />

<br />

### scripts
setupTypeScript.js를 실행하면 ts를 사용하는 프로젝트로 변환시켜준다.<br />
```
node /scripts/setupTypeScript.js
```

방탈출 프로젝트에서는 그냥 js를 사용하기로...