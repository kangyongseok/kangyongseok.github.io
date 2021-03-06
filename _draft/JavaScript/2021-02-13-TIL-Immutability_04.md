---
title: "JavaScript Immutability"
categories: 
  - javascript
tags: 
    - 자바스크립트
    - 자바스크립트기본
    - 기본으로돌아가서
    - Immutability
    - 자바스크립트 불변성
    - 자바스크립트 객체
toc: true
toc_sticky: true
comments:  true
---

## Immutability (불변성)
불변성은 함수형 프로그래밍의 핵심 원리이며 객체가 생성된 이후 그 상태를 변경할 수 없는 상태를 의미한다.  
자바스크립트의 원시타입들은 변경불가능한 값이다.
- boolean
- null
- undefined
- number
- string

원시타입 이외의 모든 값은 객체타입이며 변경가능한 값으로 볼 수 있다.

## 예제1
```javascript
let a = "Hello";
console.log(a) // Hello

a = "world"
console.log(a) // world
```

위의 예제는 얼핏보면 a 에 값이 바뀐것같지만 내부 동작은 다르다. 처음 변수 선언에서 메모리 어딘가에 "Hello" 라는 문자열이 생성되고 a 는 "Hello" 가 생성된 메모리주소를 가르키게된다.  
이후 `a = "world"` 로 새로운 문자열을 넣어주면 새로운 메모리영역에 world 가 생성되고 a 는 world 가 있는 메모리주소를 가르키게된다.
  
즉 메모리 어딘가에 `"Hello"` 와 `"world"` 둘다 존재하고 있다고 볼 수 있다.

## 예제2
```javascript
let b = "I am developer";

let sliceText = b.slice(5, 14);
console.log(sliceText) // developer
console.log(b) // I am developer
```
여기서의 slice() 함수는 새로운 문자열을 생성하여 반환하기때문에 b 를 함수 실행 후 출력해도 저장된 문자열 그대로 출력하게된다.  
문자열으 자바스크립트의 원시타입으로 변경 불가능한 값이다. 변경되는것처럼 보이는건 단지 가르키는 메모리주소가 변경되거나 새로운 영역을 사용할 뿐이다.

## 예제3
```javascript
let arr = [];
console.log(arr.length) // 0

let arr2 = arr.push(1);
console.log(arr.length) // 1
console.log(arr2) // 1
console.log(arr) // [1]
```
예제3에서 arr 변수를 빈 배열을 갖고있는데 배열은 자바스크립트에서 객체이다. 
따라서 arr.push() 함수를 사용하면 객체 자체에 값이 변화가 발생하고 arr 을 출력해보면 1을 갖고있는 배열이 출력되는걸 볼 수 있다.

## 예제4
```javascript
let user = {
  name: 'kang',
  age: 24,
  job: {
    developer: 'frontend',
    lang: 'javascript',
  }
}

let userName = user.name;

user.name = 'Kim';
console.log(userName) // kang
console.log(user) 
/*
name: 'Kim',
  age: 24,
  job: {
    developer: 'frontend',
    lang: 'javascript',
  }
*/

userName = user.name;
console.log(userName) // Kim
```

예제4 에서 user 는 객체이기때문에 내부의 값들이 변경이 가능하다. 그러나 `userName` 이라는 변수에 `user.name` 을 할당하고 user에 name 값을 변경하게되었을때 `userName` 에는 아무런 변화가 없다.  
그렇다는건 userName 변수는 user 객체를 참조하고있는것이 아닌 user.name 이 갖고있는 kang 를 메모리에 생성하고 userName 은 이 메모리를 참조하고있기 때문이다. userName 이 변경된 user.name 값을 참조하기위해서는 재할당을 해주어야하는데 이때도 역시 기존의 kang 는 메모리상에 어딘가에 존재하고있고 Kim 이라는 새로운 문자열을 메모리에 생성하고 userName 은 그 메모리를 참조하게된다. 

## 예제5
```javascript
let obj1 = {} // 빈 객체
let obj2 = obj1 // obj1 을 참조하는 객체

obj1.value = 100;
console.log(obj1) // {value: 100}
console.log(obj2) // {value: 100}
```

예제5는 빈 객체를 갖는 변수 obj1 를 선언하고 obj2 에는 obj1 을 넣어주었는데 이때 obj1 과 obj2 는 동일한 메모리영역의 주소를 참조하고있다. 그래서 `obj1.value = 100;` 이렇게 넣었을때 obj2 를 출력해도 동일한 값이 출력된다.

## 예제6
```javascript
let user = {
  name: 'kang',
  age: 24,
  job: {
    developer: 'frontend',
    lang: 'javascript',
  }
}

let user2 = user;
user2.name = 'Kim';

console.log(user.name) // Kim
console.log(user2.name) // Kim
```

위와같은경우는 객체는 불변의값이 아니므로 user2 에는 name 이 추가되었다. 근데 이때 user 의 값도 변경되었다. 두 객체는 동일한 메모리영역을 참조하고있기때문에 user2 를 변경해도 user 의값이 함께 변동된다. 만약 이러한 동작이 의도하지않은 동작이라면 이를 막을 방법이 필요하다.



## 불변 데이터 패턴
위와같이 객체들은 불변성이 아니기때문에 어디서든지 누구나 변경이 가능하고 이러한 자율성이 매우 편할때도있지만 반대로 생각하자면 쉽게 원본데이터가 오염되어 원하지않는 버그로 이어질가능성이 매우 높다. 따라서 이러한 부분을 방어하기위해 자바스크립트에서는 두가지방법을 사용할 수 있다.
- 객체의 방어적 복사
- 불변객체화를 통한 객체 변경 방지

## 객체의 방어적 복사
### Object.assign() - ES6 에서 추가되었고 IE 에서는 지원하지않음

```javascript
let obj1 = {} // 빈 객체
let obj2 = obj1 // obj1 을 참조하는 객체

obj1.value = 100;
console.log(obj1) // {value: 100}
console.log(obj2) // {value: 100}
```

예제 5번을 다시갖고와서 추가 테스트를하고 객체의 방어적 복사를 진행해보려고한다.

```javascript
let obj1 = {} 
let obj2 = obj1

obj1.value = 100;

let obj3 = Object.assign({}, obj1)

console.log(obj1 === obj2) // true

console.log(obj1) // {value: 100}
console.log(obj2) // {value: 100}
console.log(obj3) // {value: 100}

console.log(obj1 === obj3) // false
```

`Object.assign()` 으로 객체의 복사를 진행하였더니 `obj1 === obj2` 의결과와는 다르게 false 가 나오는걸 볼 수 있다. 즉 같은 메모리영역의 주소를 바라보고있는것이 아닌 객체의 복사를 통해서 새로운 영역에 값을 넣고 그 주소를 참조하고있는걸 알 수 있다.
  
대신 이 방법은 객체의 완전한 복사를 지원하지않기에 객체내의 객체는 얕은복사만 진행하게된다.

```javascript
const user = {
  name: 'kang', // deep copy
  age: 24, // deep copy
  job: {
    developer: 'frontend', // shallow copy
    lang: 'javascript', // shallow copy
  }
}

const user2 = Object.assign({}, user);
console.log(user === user2) // false

user2.name = "Kim";
console.log(user.name) // kang
console.log(user2.name) // Kim

console.log(user.job === user2.job) // true

user.job.lang = 'JAVA';

console.log(user.job.lang) // JAVA
console.log(user2.job.lang) // JAVA
```

user.name 은 불변성이 지켜졌지만 user2.job.lang 은 불변성이 되지않았다. 이유는 객체내의 객체또한 변경가능한 객체이기때문에 user2.job 이 가르키는 또다른 메모리 영역이 생성되고 user2.job 은 그 메모리영역의 주소를 가르키고 있기때문에 사실상 별도로 저장된 값이라고 볼 수 있다. 따라서 얕은복사는 가르키고있는 주소의 값을 복사하는것뿐 객체내의 객체는 동일한 메모리영역의 주소를 가르키고있기때문에 불변성이 지켜지지않았다.
  
## 불변객체화를 통한 객체 변경 방지
### Object.freeze() - 객체를 동결 IE9 부터도 지원
```javascript
const user = {
  name: 'Lee',
  address: {
    city: 'Seoul'
  }
};

Object.freeze(user); // user 라는 객체를 얼려버림

user.address.city = 'Busan'; 
console.log(user); // { name: 'Lee', address: { city: 'Busan' } }
```

IE 까지 지원이 되는건 좋지만 여전히 중복객체에는 불변성이 적용되지않습니다. Nested Object 까지 변경을 막으려면 위의 메소드들을 활용하여 재귀방법으로 객체의 객체를 계속 파고들어 모두 불변객체로 만드는 방법이 있지만 매번 이런식으로 관리하고 작업하기에는 꽤 번거로운 부분이 있다.
  
여기서 우리가 선택할 수 있는건 이미 검증되고 널리 사용되고있는 이미 다른 사용자가 만든 라이브러리를 사용하는것이다. `immutable.js` 와 `lodash.js` 가 있는데 lodash는 이미 배열이나 데이터를 다루는데 많이 사용되고있어 다방면으로 활용가능한 lodash.js 를 기준으로 알아보려고 한다.

## lodash.js 활용한 불변객체 생성
```javascript
const user = {
  name: 'Lee',
  address: {
    city: 'Seoul'
  }
};
const user2 = _.clone(user); // 먼저 소개된 두가지 메소드와 동일하게 깊은복사는 안됨
const user3 = _.cloneDeep(user); // 깊은 복사까지 가능
```

lodash 라이브러리를 사용하면 얕은복사와 깊은복사 모두 수행가능하여 활용도가 높고 또 다양한 데이터를 처리하는 함수들도 있어서 실제 업무를 진행할때는 라이브러리의 도움을 많이 받아서 활용하고 있다.