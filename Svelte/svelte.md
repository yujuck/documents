방탈출 후기 사이트 프로젝트에 svelte를 사용하기 위해서는 일단 svelte에 대해 더 공부해야함

## Svelte
스벨트의 가장 큰 특징이라고 할 수 있는건 virtual DOM이 없다는 것, Runtime에 로드할 프레임워크가 없다는 것이다.

svelte 공식 홈페이지의 메인 화면을 보면 3가지 특징을 말하고 있다. <br />

### write less code
다른 프레임워크들에 비해 코드가 간결하다.<br />
같은 기능을 작업하더라도 svelte의 코드가 React나 vue에 비해 간결하다.<br />
svelte를 사용하면 작업 속도는 빠를 것 같긴 하다..ㅎ

<br />

### No virtual DOM
svelte를 말할 때 가장 먼저 나오는 특징이 virtual DOM을 사용하지 않는다는 점일 것 같다.<br />

virtual DOM을 사용하지 않고도 유사한 프로그래밍 모델을 달성할 수 있다고 svelte의 공식에서 말하고 있다.<br />
(참고: [virtual is pure overhead](https://svelte.dev/blog/virtual-dom-is-pure-overhead))


> Svelte는 런타임에 작업을 기다리지 않고 빌드 시간에 앱에서 변경 사항이 어떻게 발생하는지 알고 있는 컴파일러

<br />

### Truly reactive
반응성은 변경된 값이 DOM에 자동으로 반영되는걸 말한다.<br />
Svelte는 별도의 Setter 없이 값의 할당(assignments)만으로 업데이트를 트리거(Trigger)할 수 있다.<br />
