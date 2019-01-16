---
title: 2019-01-08-destructuring
author: rohi
---

# Destructuring

디스트럭쳐링(Destructuring)은 구조화된 배열 또는 객체를 비구조화, 파괴하여 개별적인 변수에 할당하는 것이다.

## 1. 배열 디스트럭처링(Array destructuring)

- ES5에서 배열의 각 요소를 배열로부터 디스트럭처링하여 변수에 할당하기 위한 방법은 다음과 같다.

```javascript
// ES5
var arr = [1, 2, 3];

var one   = arr[0];
var two   = arr[1];
var three = arr[2];

console.log(one, two, three); // 1 2 3
```

- ES6의 배열 디스트럭처링은 배열의 각 요소를 배열로부터 추출하여 변수 리스트에 할당한다.
- 이때 추출/ 할당 기준은 배열의 인덱스이다.

```javascript
// ES6
const arr = [1, 2, 3];

// 배열의 인덱스를 기준으로 배열로부터 요소를 추출하여 변수에 할당
const [one, two, three] = arr;

console.log(one, two, three); // 1 2 3
```

- 배열 디스트럭처링을 위해서는 할당 연산자 왼쪽에 배열 형태의 변수 리스트가 필요하다.
- 왼쪽의 변수 리스트와 오른쪽의 배열은 배열의 인덱스를 기준으로 할당된다.

```javascript
let x, y, z;

[x, y] = [1, 2];
console.log(x, y); // 1 2

[x, y] = [1];
console.log(x, y); // 1 undefined

[x, y] = [1, 2, 3];
console.log(x, y); // 1 2

[x, , z] = [1, 2, 3];
console.log(x, z); // 1 3

// 기본값
[x, y, z = 3] = [1, 2];
console.log(x, y, z); // 1 2 3

[x, y = 10, z = 3] = [1, 2];
console.log(x, y, z); // 1 2 3

// spread 연산자
[x, ...y] = [1, 2, 3];
console.log(x, y); // 1 [ 2, 3 ]
```

- ES6의 배열 디스트럭처링은 배열에서 필요한 요소만 추출하여 변수에 할당하고 싶은 경우에 유용하다.

```javascript
//Date 객체에서 년도, 월, 일을 추출하는 예제
const today = new Date();
const formattedDate = today.toISOString().substring(0, 10);
const [year, month, day] = formattedDate.split('-');
console.log([year, month, day]); // [ '2018', '05', '05' ]
```

## 2. 객체 디스트럭처링(Object destructuring)

- ES5에서 객체의 각 프로퍼티를 객체로부터 디스트럭처링하여 변수에 할당하기 위해서는 프로퍼티 이름(key)을 사용해야한다.

```javascript
// ES5
var obj = { firstName: 'Ungmo', lastName: 'Lee' };

var firstName = obj.firstName;
var lastName  = obj.lastName;

console.log(firstName, lastName); // Ungmo Lee
```

- ES6의 객체 디스트럭처링은 객체의 각 프로퍼티를 객체로부터 추출하여 변수 리스트에 할당한다.
- 이때 할당 기준 프로퍼티 이름(key)이다.

```javascript
// ES6
const obj = { firstName: 'Ungmo', lastName: 'Lee' };

const { firstName, lastName } = obj;

console.log(firstName, lastName); // Ungmo Lee
```

- 객체 디스트럭처링을 위해서는 할당 연산자 왼쪽에 객체 형태의 변수 리스트가 필요하다.

```javascript
const { prop1: p1, prop2: p2 } = { prop1: 'a', prop2: 'b' };
console.log(p1, p2); // 'a' 'b'
console.log({ prop1: p1, prop2: p2 }); // { prop1: 'a', prop2: 'b' }

// 아래는 위의 축약형이다
const { prop1, prop2 } = { prop1: 'a', prop2: 'b' };
console.log({ prop1, prop2 }); // { prop1: 'a', prop2: 'b' }

// default value
const { prop1, prop2, prop3 = 'c' } = { prop1: 'a', prop2: 'b' };
console.log({ prop1, prop2, prop3 }); // { prop1: 'a', prop2: 'b', prop3: 'c' }
```

- 객체 디스트럭처링은 객체에서 프로퍼티 이름(키)으로 필요한 프로퍼티 값만을 추출할 수 있다.
- Array.prototype.filter 메소드의 콜백 함수는 대상 배열(todos)을 순회하며 첫 번째 인자로 대상 배열의 요소를 받는다. 이때 필요한 프로퍼티(completed 프로퍼티)만을 추출할 수 있다.

```javascript
const todos = [
  { id: 1, content: 'HTML', completed: true },
  { id: 2, content: 'CSS', completed: false },
  { id: 3, content: 'JS', completed: false }
];

// todos 배열의 요소인 객체로부터 completed 프로퍼티만을 추출한다.
const completedTodos = todos.filter(({ completed }) => completed);
console.log(completedTodos); // [ { id: 1, content: 'HTML', completed: true } ]
```

- 중첩 객체의 경우는 아래와 같이 사용한다.

```javascript
const person = {
  name: 'Lee',
  address: {
    zipCode: '03068',
    city: 'Seoul'
  }
};

const { address: { city } } = person;
console.log(city); // 'Seoul'
```

### References

- [Poiema Web - Destructuring](https://poiemaweb.com/es6-destructuring)