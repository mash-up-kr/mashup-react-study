---
title: 2019-02-12-decorator
author: rohi
---

# Decorator

데코레이터는 선언된 클래스와 그 프로퍼티들을 디자인 시간에 변경할 수 있는 편리한 문법이다.

## 1. Descriptor

#### 1.1 Descriptor란?

- 객체를 만든 뒤 프로퍼티를 추가하면 각 프로퍼티는 기본 프로퍼티 디스크립터(설명자)를 가진다.
- 프로퍼티의 디스크립터를 알기 위해서는 `Object.getOwnPropertyDescriptor(obj, propName)` 을 사용해야 한다. 첫 번째 인자는 객체이고 두 번째 인자는 객체의 속성이다.

```javascript
const obj = {
  num: 42,
  str: 'e',
};

console.log(Object.getOwnPropertyDescriptor(obj, 'num'));
// { value: 42, writable: true, enumerable: true, configurable: true }
```

#### 1.2 Descriptor에서 설정할 수 있는 값

- `writable` : 객체의 프로퍼티가 쓰기 가능한 지의 여부, false일 경우 쓰기 불가능
- `enuerable` : 객체의 프로퍼티가 열거 가능한 지의 여부, false일 경우 `Object.key`에서 열거 불가능
- `configurable` : 객체의 프로퍼티가 `defineProperty` 을 통해 설정 될 수 있는 지의 여부, false일 경우 `object.defineProperty` 로 해당 프로퍼티 수정 불가능

```javascript
const obj = {
  num: 10,
};

Object.defineProperty(obj, 'num', { enumerable: false });
Object.keys(obj); // []

Object.defineProperty(obj, 'num', { writable: false });
obj.num = 0;
console.log(obj); // { num: 100 }

Object.defineProperty(obj, 'num', { writable: false, configurable: false });
Object.defineProperty(obj, 'num', { writable: true }); // Uncaught TypeError
```



## 2. 데코레이터의 선언

- 데코레이터는 함수이다. 함수를 선언한 뒤 `@` 키워드를 이용해 선언된 함수를 데코레이터로 사용할 수 있다.
- 클래스의 프로퍼티, 메소드, 클래스 자신의 바로 윗줄에 `@(함수이름)` 형태로 추가해준다.

```javascript
const readonly = (target, property, descriptor) => { 
  descriptor.writable = false;
  return descriptor;
};

class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

// 선언된 readOnly 함수를 이용하여 decorator로 사용한다.
  @readonly
  getFullName() {
    return `${this.firstName} ${this.lastName}`;
  }
}
```

- 데코레이터는 클래스를 꾸밀지, 클래스의 프로퍼티를 꾸밀지에 따라 선언 방법이 달라진다.



#### 2.1 클래스 프로퍼티의 데코레이터

- 클래스 프로퍼티의 Decorator를 정의하는 경우에는 프로퍼티의 `descriptor` 를 인자로 받아 새로운 `discriptor` 를 반환하는 형태를 가진다.
  - `target` : 프로퍼티를 정의하고자 하는 객체
  - `property` : 프로퍼티 이름
  - `descriptor` : 새로 정의하고자 하는 프로퍼티에 대한 디스크립터

```javascript
/* readonly Decorator */
// 읽기만 가능하고 쓰기는 불가능하게 하는 데코레이터
function readonly(target, property, descriptor) {  
  descriptor.writable = false
  return descriptor
}

class Car {  
  @readonly
  manufacturer = 'RO'
}

const myCar = new Car()  
myCar.manufacturer = 'HI' // 새로운 값을 할당하려고 하면 에러가 난다.  
```

```javascript
/* nonenumerable Decorator */
// 클래스의 프로퍼티를 열거할 때 열거 대상에서 제외하는 Decorator
function nonenumerable(target, property, descriptor) {  
  descriptor.enumerable = false
  return descriptor
}

class Car {  
  @nonenumerable
  acceleration = 10

  manufacturer = 'ROHI'
}

const myCar = new Car()  
for (let key in myCar) {  
  console.log(key)
  // manufacturer 만 출력이 된다. acceleration는 열거 대상에서 제외된다.
}
```



#### 2.2 클래스의 데코레이터

- 클래스의 Decorator는 타겟 클래스의 생성자를 인자로 받아 목적에 맞게 생성자를 바꾼 뒤 반환해준다.
- 클래스 내부에서 속성을 정의하지 않고 Decorator를 통해 정의할 수 있다.

```javascript
// 생성자의 prototype.sound를 지정해준다.
function setAnimalSound(sound) {  
  return (target) => {
    target.prototype.sound = sound
    return target
  }
}

@setAnimalSound('oink')
class Pig {  
  say() {
    return this.sound
  }
}

@setAnimalSound('quack')
class Duck {  
  say() {
    return this.sound
  }
}

const pig = new Pig()  
console.log(pig.say()) // ‘oink’ 출력

const duck = new Duck()  
console.log(duck.say()) // ‘quack’ 출력 
```

- 클래스 데코레이터는 완전히 다른 클래스의 생성자로 바꿔치기 할 수도 있다.

```javascript
function withBus(target) {  
  return class Bus {
    say() {
      return 'I am bus'
    }
  }
}

@withBus
class Car {  
  say() {
    return 'I am car'
  }
}

const car = new Car()  
console.log(car.say()) // ‘I am bus’ 출력
```



## 3. 데코레이터를 위한 환경

- Decorator는 최종적으로 채택된 스펙이 아니기 때문에 `babel`과 함께 사용해야 한다. 
- [bable 설정](https://babeljs.io/docs/en/babel-plugin-proposal-decorators)



### References

- [우아한 설계의 첫걸음, ES7의 decorator](https://blog-kr.zoyi.co/channel-frontend-decorator/)
- [JavaScript Decorator 이해하기](https://wonism.github.io/what-is-decorator/)
- [proposal-decorators](https://github.com/tc39/proposal-decorators)