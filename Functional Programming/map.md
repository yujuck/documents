### map 함수 만들기

```jsx
const products = [
    {name: '축구공', price: 10000},
    {name: '야구공', price: 13000},
    {name: '농구공', price: 16000},
    {name: '볼링공', price: 10500},
]

const map = (f, iter) => {  // map 함수가 전달받는 값이 이터러블 프로토콜을 따르는 것을 보여주려고 iter라고 작성
    // let names = [];
    let res = [];
    for (const a of iter) {
        // names.push(p.name);  // name처럼 직접적으로 어떤 값을 수집할건지를 정해두는 것이 아니라 밑의 코드 처럼 추상화함
        res.push(f(a));  // 함수를 통해 어떤 값을 수집할건지 전달받음. 전달받은 함수에게 위임함
    }
    // console.log(names);  // 함수 외부의 영역에 직접적인 영향을 끼치는 코드를 작성하기 보다 결과를 리턴하는 것이 함수형 프로그래밍
    return res;  // 함수형 프로그래밍에서는 결과를 리턴해서 이 결과로 변화를 일으키도록 하는 것을 지향
}

// 함수형 프로그래밍에서는 함수가 인자와 리턴값으로 소통하는 것을 권장함

// 위의 map 함수를 이용해 name과 price를 수집하는 코드 작성하기
console.log(map(p => p.name, products));
console.log(map(p => p.price, products));
```

### 이터러블 프로토콜을 따른 map의 다형성
위에서 만든 map 함수는 이터러블 프로토콜을 따르고 있기 때문에 다형성이 높다.



```jsx
console.log(document.querySelectorAll('*').map(el => el.nodeName));
```
document.querySelectorAll('*')은 map 함수를 사용할 수 없는데, 
그 이유는 querySelectorAll는 Array를 상속받은 함수가 아니여서 프로토타입에 map 함수가 구현이 안되어있기 때문이다.

근데 위에서 만든 map함수를 사용하면 동작한다.
```jsx
const map = (f, iter) => {
    const res = [];
    for(const a of iter) {
        res.push(f(a));
    }
    return res;
}

console.log(map(el => el.nodeName, document.querySelectorAll('*')));

const iterator = document.querySelectorAll('*')[Symbol.iterator]();

```
동작하는 이유는 document.querySelectorAll()가 이터러블 프로토콜을 따르고 있기 때문이다.
map 함수 내부에서 이터러블 프로토콜을 따르고 있는 for...of문을 사용하고 있기 때문에 이터러블 프로토콜을 따르고 있는 값을 넘기면 동작하게 되는 것이다.

```jsx
function* gen() {
    yield 2;
    if (false) yield 3;
    yield 4;
}

console.log(map(a => a * a, gen()));
```
제너레이터 함수도 이터러블한 값을 리턴하기 때문에 위에서 만든 map을 사용할 수 있다.
제너레이터 함수의 결과들에 대해서도 map이 가능하다는건
사실상 모든 것에 대해 map을 할 수 있다는 것과 같다.


```jsx
let m = new Map();
m.set('a', 10);
m.set('b', 20);
const iter = m[Symbol.iterator]();

console.log(map(([key, value]) => [key, value * 2], m));  // [['a', 20],['b', 40]]
console.log(new Map(map(([key, value]) => [key, value * 2], m)));  // {'a' => 20, 'b' => 40}
```
자바스크립트의 자료형 중 하나인 Map도 이터러블 프로토콜을 따르고 있기 때문에
활용할 수 있다.