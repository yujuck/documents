### 이터러블 프로토콜을 따르는 filter

```jsx
const products = [
    {name: '축구공', price: 15000},
    {name: '야구공', price: 20000},
    {name: '농구공', price: 15000},
    {name: '볼링공', price: 30000},
    {name: '당구공', price: 25000},
]

let under20000 = [];

const filter = (f, iter) => {
    let res = [];
    for (const a of iter) {
        if(f(a)) res.push(a);
    }
    return res;
}

const filterResult = filter(p => p.price < 20000, products);
console.log(...filterResult);

filter(n => n % 2, [1,2,3,4])

filter(n => n % 2, function* () {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
})
```
한번 만들어보니 쉽군..