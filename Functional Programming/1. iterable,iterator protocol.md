# 이터러블/이터레이터 프로토콜

## 기존 for문

```jsx
for (let i = 0; i < arr.length; i++) {
	console.log(arr[i]);
}
```

기존의 for문은 숫자로 된 키로 직접 접근해서 해당 키에 매핑되어있는 값들을 순회하는 방식으로 사용을 했는데,

ES6에서 발표된 for...of문은 다른 방식으로 동작한다.

다르게 동작한다는 것은 Set과 Map은 기존 for문으로는 순회할 수 없지만

for...of문으로는 순회할 수 있다는 점에서 알 수 있다.

```jsx
const set = new Set([1,2,3]);
set[0]; // undefined

const map = new Map([['a', 1], ['b', 2]]);
map[0]; // undefined
```

## 이터러블/이터레이터 프로토콜

- 이터러블 : 이터레이터를 리턴하는 `[Symbol.iterator]()`를 가진 값
- 이터레이터 : { value, done } 객체를 리턴하는 next()를 가진 값
- 이터러블/이터레이터 프로토콜 : 이터러블을 for...of, 전개 연산자 등과 함께 동작하도록 한 규약

Array로 살펴보면,

Array는 Symbol.iterator라는 키를 가지고 있다.


Array의 Symbol.iterator라는 키로 접근해서 실행을 시키면 이터레이터를 리턴한다.

```jsx
const arr = [1, 2, 3];
arr[Symbol.iterator]();  // iterator 리턴됨
```

따라서 위의 정의에 따라 Array는 이터러블하다고 말할 수 있다.

Array Iterator는 next()라는 함수를 가지고 있고 이를 실행시키면 value와 done이라는 키를 가지고 있는 객체를 리턴해준다.


Array뿐만 아니라 Set, Map 모두 자바스크립트의 내장 객체로서 이터러블/이터레이터 프로토콜을 따르고 있다.

## for...of의 동작원리

맨 처음에 말한 for...of문이 이 이터러블과 이터레이터의 개념으로 동작한다.

```jsx
const arr = [1, 2, 3];
arr[Symbol.iterator] = null;

for (const a of arr) console.log(a)  // Error!!
```

위의 코드를 실행하면 `arr is not iterable` 라고 에러가 발생한다.

이것으로 for...of문은 Array의 Symbol.iterator와 관련이 있다고 유추해볼 수 있는데,

for...of문은 Symbol.iterator를 실행시키면서 value를 전달하고, done을 통해 모두 실행시켰는지를 판단한다고 생각해볼 수 있다.

이터레이터를 따로 변수에 선언하고 이터레이터가 가지고 있는 next()함수를 실행시켜보면

value와 done을 리턴하는데, 요소의 개수만큼 실행시킨 후 다시 next()를 실행시키면

value에는 undefined, done에는 true가 있는 것을 확인할 수 있다.


그리고 next()를 실행시키고 for...of문을 실행시켜보면 실행시킨 횟수 이후의 요소부터 시작하는 것을 볼 수 있다.


next()를 한번만 실행시키고 for...of문을 실행시키면 2, 3이 출력됨


next()를 두번 실행시키면 3만 출력됨

## Set 과 Map에서도!

위에서 말한 것처럼 Set과 Map도 Array와 마찬가지로 이터러블/이터레이터 프로토콜을 따르고 있다.

```jsx
const set = new Set([1, 2, 3]);
set[Symbol.iterator]();  // iterator 리턴

const map = new Map([['a', 1], ['b', 2],['c', 3]]);
map[Symbol.iterator]();  // iterator 리턴
```

Set은 Array와 큰 차이가 없고, Map은 iterator를 실행시켜보면 value에 Array가 들어있는 것을 볼 수 있다.


### map의 keys, values, entries

map은 keys, values, entries라는 메서드를 가지고 있다.

이것들은 모두 이터레이터를 리턴하기 때문에 for...of문에 가져와 사용할 수 있다.

```jsx
for (const a of map.keys()) console.log(a)  // 이렇게 하면 key들만 출력 가능
```

## 이터레이터도 이터러블함

위에서 map.keys()는 이터레이터를 리턴한다고 했었는데 이 이터레이터를 확인해보면 내부에

Symbol.iterator를 가지고 있다.

이 말은 즉, map.keys()가 리턴한 이터레이터 자체도 이터러블하다고 할 수 있다는 것이다.

(이터러블의 정의가 Symbol.iterator를 가진 값이기 때문)

이터레이터의 Symbol.iterator를 실행하면 자기 자신을 리턴한다.

위에서 map.keys()를 for...of문에서 실행시킬 수 있던 이유가 이터레이터를 리턴하기 때문이라고 했는데,

조금 더 자세하게 말하면 반환된 이터레이터도 `[Symbol.iterator]` 를 가지므로 이터러블하기 때문에 가능하다고 말할 수 있다.