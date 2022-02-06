## 사용자 정의 iterable

직접 iterable한 값을 만들어볼 수 있다.

```jsx
const iterable = {
    [Symbol.iterator]() {
        let i = 3;

        return {
            next() {
                return i == 0 ? {done : true} : { value: i--, done: false }
            },
        }       
    }
}
```

iterable은 [Symbol.iterator]를 가지고 있어야 하고,

이 [Symbol.iterator]는 next()라는 메서드를 리턴하는데,

next()는 { value, done } 객체를 리턴해야한다.

위에서 만든 iterable은 iterator를 실행시켰을 때 3, 2, 1 순서로 value를 리턴하도록 만들었다.

## well-formed iterable, iterator

> iterable 한 iterator
> 

위에서 만든 iterable한 값은 완벽하지 않은데,

iterator로 순회를 하려고 하면 에러가 발생하기도 하고, 

iterator로 어느 정도 진행을 한 후(next()호출) 순회를 해도 3부터 출력이 된다. 


이러한 상황이 생기는 이유는 iterator가 iterable하지 않기 때문에 발생한다.

iterator가 iterable하다고 `이터러블/이터레이터 프로토콜` 글에서 적었는데, 그걸 말하는 용어가 `well-formed iterator` 였다..

위에서 만든 iterable의 iterator가 iterable하려면 자기 자신을 리턴하는 iterator가 필요하다.

```jsx
const iterable = {
    [Symbol.iterator]() {
        let i = 3;

        return {
            next() {
                return i == 0 ? {done : true} : { value: i--, done: false }
            },
						[Symbol.iterator]() { return this; }  // 이 때는 자기 자신을 리턴함
        }       
    }
}
```

`[Symbol.iterator]() { return this; }` 이것이 iterable[Symbol.iterator]()로 리턴된 iterator가 iterable하게 해주고,

next()로 진행을 하다가 for...of로 순회를 하더라도 진행한 부분 이후부터 순회할 수 있게 해준다.

결론적으로 well-formed iterable은

1. iterable 자체를 넣어도 순회가 되고
2. iterator를 넣어도 순회가 되고
3. 어느 정도 진행한 후에 순회할 경우 그 이후부터 순회가 된다

```jsx
const iterable = {
    [Symbol.iterator]() {
        let i = 3;

        return {
            next() {
                return i == 0 ? {done : true} : { value: i--, done: false }
            },
						[Symbol.iterator]() { return this; }
        }       
    }
}
const iterator = iterable[Symbol.iterator]();

for (const a of iterable) console.log(a)  // 1

// 2. iterable[Symbol.iterator]()을 실행시킨 결과가 iterator여야 가능
for (const a of iterator) console.log(a)

iterator.next();
iterator.next();
for (const a of iterator) console.log(a)  // 3.  1만 출력됨
```

## 기타 iterator/iterable 프로토콜을 따르는 것들 예시

### Web API

web api 중 `document.querySelectorAll()` 이 있는데 이것 또한 iterator/iterable 프로토콜을 따르고 있다.

```jsx
const all = document.querySelectorAll('*');
for(const a of all) console.log(a);
```

위처럼 all을 순회할 수 있는 이유는 `document.querySelectorAll('*');` 의 결과가 배열이라서가 아니라 `[Symbol.iterator]`가 구현되어 있기 때문이다.

```jsx
const all = document.querySelectorAll('*');
let iter = all[Symbol.iterator]();
iter.next();
iter.next();
```

### 전개 연산자

전개 연산자도 마찬가지이다.

```jsx
const a = [1, 2];
a[Symbol.iterator] = null;
console.log([...a]);
```

중간에 있는 `a[Symbol.iterator] = null` 코드를 실행한 후에 `a`를 출력하려고 하면

a가 iterable하지 않다는 에러를 볼 수 있다.

이를 통해 전개 연산자는 iterable한 값들에만 사용할 수 있다는 것을 알 수 있다.