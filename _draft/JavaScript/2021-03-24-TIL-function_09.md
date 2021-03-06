---
title: "JavaScript 함수"
categories: 
  - javascript
tags: 
    - 자바스크립트
    - 자바스크립트기본
    - 기본으로돌아가서
    - 함수
    - 클로저
    - 디자인패턴
    - 생성자함수
    - 실행컨텍스트
toc: true
toc_sticky: true
comments:  true
---
함수는 자바스크립트에서 필수적으로 짚고 넘어가야하는 개념중에 하나이다. 자바스크립트의 함수는 객체이기도하고 함수자체로 값으로 쓰이기도한다. 
- 스코프
- 실행컨텍스트
- 클로저
- 생성자함수에의한 객체생성
- 메서드
- this
- 프로토타입
- 모듈화
- 순수함수
- 함수형프로그래밍
- 일급객체
- 즉시실행함수
- 콜백함수
- 고차함수

이 모든 용어들이 함수와 관련이 있고 또 자바스크립트를 이해하는데 있어 매우 중요한 개념들을 담고있는 내용이기도하다. 

## 함수란
```javascript
function add(x, y) {
	return x + y
}
console.log(add(2, 3)) // 5
```
함수는 입력을 받아 출력을 내보내는과정을 코드블록으로 감싸서 하나의 실행단위로 정리한것으로 재사용이가능하고 코드의 가독성을 높이기 좋다.

```javascript
function add(x, y) {
	return x + y
}
```
해당 부분을 함수를 정의했다 라고 하며 `add`는 매개변수, `(x, y)`는 매개변수 `return x + y` 는 반환값 또는 리턴값이라고 부른다. 

```javascript
add(2, 3)
```
함수를 호출했다 라고 하며 `()` 내부에 있는 값은 인수라고 부른다.

> 함수를 호출하며 넘겨주는 값은 인수 또는 인자값, 함수를 정의하면서 받아오는 값을 매개변수라고 칭한다. 



## 함수의 정의
**함수 선언문**
```javascript
function add(x, y) {
	return x + y;
}
```
> ❌ 함수 선언문은 이름을 생략하여 사용할 수 없다.
> ```javascript
function (x, y) {
	return x + y;
}
>```

**함수 표현식**
```javascript
const add = function (x, y) {
	return x + y;
}
```
> ❌ 함수 선언문은 변수에 할당 할 수 없지만 동작은한다.
```javascript
const add = function add(x, y) {
	return x + y;
}
```

**화살표 함수**
```javascript
const add = (x, y) => x + y;
```

**Function 생성자 함수**
```javascript
const add = new Function('x', 'y', 'return x + y');
```

## 암묵적 생성
```javascript
function add() { return console.log('add') }
(function add2() { return console.log('add2') })
add() // add
add2() // ReferenceError: add2 is not defined
```

첫번째함수는 정상적으로 정의된 함수로 함수의 이름은 함수 몸체내에서만 참조할 수 있는 식별자로 add 라는 이름을 기본적으로는 외부에서 호출하여 사용하는것이 불가능하다. 그러나 함수 선언문으로 작성된 함수는 자바스크립트엔진이 `암묵적으로 생성한 식별자` 가 되기때문에 호출하여 사용이 가능하다.

두번째 함수는 함수리터럴 표현식으로 해석되기때문에 자바스크립트엔진에서 별도로 식별자를 생성하지않기때문에 접근할 이름이 없어 정상적인 호출이 불가능하다.

> 즉 함수는 함수 이름으로 호출되는것이 아닌 함수 객체를 가르키는 식별자로 호출한다. 이때 이 식별자는 함수가 저장된 메모리 주소를 참조한다.

위에서 함수 선언문은 변수에 할당할 수 없지만 동작하는 이유도 같은 이유이다. 

```javascript
const test = function add(x, y) {
	return x + y;
}
// test = 식별자
// add = 함수이름
console.log(test(1, 2)) // 3
console.log(add(1, 2)) // ReferenceError: add is not defined
```

## 함수의 호이스팅
변수와 마찬가지로 함수또한 런타임 이전에 호이스팅이 발생한다. 다만 다른점은 함수는 선언 생성 실행 세가지가 동시에 발생하게되는데 함수표현식을 변수에 할당하면 호이스팅의 동작은 어떻게 될까?
```javascript
console.log(add(1, 2)) // 3
console.log(sub(1, 2)) // TypeError: sub is not a function

function add(x, y) {
	return x + y
}

var sub = function(x, y) {
	return x - y
}
```
여기선 sub 변수가 먼저 호이스팅이 발생하게되고 여기에 할당한 함수는 런타임시점에서 실행되게된다. 때문에 sub 는 undefined 가 할당되어있는 상태인데 함수 호출을 실행하니 타입에러가 발생한다.

> 자바스크립트의 함수는 값으로서 사용가능한 일급객체이다.

> 함수호이스팅은 당연히 함수를 선언하고서 호출해야하는 순서를 무시하는 동작으로 발생하기때문에 함수 선언문보다는 함수표현식을 사용하는것이 권장되어진다.


## 스코프
유효범위라고도하며 모든 식별자는 자신이 선언된 위치에 의해 다른 코드가 식별자 자신을 참조할 수 있는 유효범위가 결정된다.
```javascript
var a = 1;
if (true) { // 블록스코프
	var b = 2;
  	if (true) { // 블록 스코프
    	var c = 3;
    }
 }

console.log(a) // 1
console.log(b) // 2
console.log(c) // 3

function outerFunc() { // 함수 스코프
	var d = 4;
  
  function innerFunc() { // 함수 스코프
  	var e = 5;
  }
}

console.log(d) // ReferenceError: d is not defined
console.log(e) // ReferenceError: e is not defined
```
위 코드에서 주목할점은 함수스코프와 블록레벨 스코프에서 var 로 선언한 변수가 출력되는 기준이 다른것을 볼 수 있다. 위 코드를 해석하자면 블록레벨 스코프에서 var 는 `전역변수`로 선언되고 함수스코프에서는 `지역변수`로 선언된다.

```javascript
var a = '전역';

function func() {
	var a = '지역';
  	console.log(a); // 지역
}
func()
console.log(a); // 전역
```
var 는 위와같이 중복선언이 가능한데 동일한 변수에서 스코프영역에 따라서 다른값을 출력하는걸 확인할 수 있다. 자바스크립트는 코드의 문맥을 고려하여 식별자를 결정하는데 이를 `렉시컬환경` 이라고 부른다. 이를 구현한것이 `실행컨텍스트` 이다.

`func()` 내부에 선언된 a 는 전역에 선언된 a 를 참조가능하지만 외부에서 함수 내부에 선언된 변수를 참조하는것은 불가능하다. (클로저를 활용하면 가능)

```javascript
function varFunc() {
	var a = 1;
  
  	var b = 2;
	console.log(b) // 2
}
varFunc();
```
var 는 위와같이 스코프내에서도 중복선언이 가능한데 이때 의도하지않게 값이 변경될 수 있다. 이를 방지하기 위해서는 let, const 같은 변수를 사용하는것이 좋다.

### let, const
var 는 블록레벨이 아닌 함수스코프에 의해서 통제된다. 블록레벨스코프에서 var 는 내부에 선언된것처럼 보이지만 전역변수가된다. let 과 const 는 블록스코프에 의해서 통제된다.
```javascript
let a = 1;
if (true) { // 블록레벨스코프
	let a = 2;
	console.log(a) // 2
}
console.log(a) // 1
```

for 문같은 반복문에 var 를 사용할경우 전역의 값을 오염시킬 가능성이 높다.
```javascript
var i = 10;
for (var i = 0; i < 5; i++) {
	console.log(i) // 0, 1, 2, 3, 4
}
console.log(i); // 5
```
위의 코드에서는 오염되는걸 확인할 수 있으면서도 재미있는 부분이 하나있다. 반복문 내에서 마지막 결과는 4였지만 오염된 변수를 확인했을때에는 5 가 들어가있다.

마지막 동작만 확인해보자면 i 에는 4를 출력하고나서 i++ 에 의해서 5로 증가한다. 그리고 `i < 5` 5보다 작지않기때문에 반복문 내에서는 출력하지않고 종료되지만 i에는 5라는 값이 할당되어있는것이다.

### 스코프 체인
변수를 참조할때 자바스크립트 엔진은 스코프체인을 통해서 상위스코프로 이동하면서 선언된 변수를 검색하게된다. 하위 스코프에서 상위스코프의 변수내용은 참조 가능하지만 상위스코프에서 하위스포크의 변수를 참조하는것은 불가능한것이 이러한 동작때문이다.

### 렉시컬 스코프
```javascript
var x = 1;
function foo() {
    var x = 10;
    bar(); // 함수 호출시점
}

function bar() { // 함수 정의시점
    console.log(x)
}

foo() // 1
bar() // 1 함수 호출 시점
```
foo 는 10이 출력되어야 할것같고 bar 는 1이 나와야 할것같지만 둘다 1이 출력 되었다. 위 결과로 함수를 보자면 함수는 호출시점이 아닌 `함수를 정의한 시점` 에서 상위스코프를 결정한다. 

위 예제에서 bar 가 정의된 시점에 상위스코프는 전역이기때문에 foo 내에서 호출해도 전역값 x 를 참조하여 1이 출력되게 된다.

### 스코프 정리
- var 변수는 함수스코프내에서는 지역변수 블록스코프내에서는 전역변수가 된다.
- let 과 const 는 블록스코프내라면 지역변수로 사용된다.
- 전역에 생성된 변수의 생명주기는 웹 브라우저가 닫힐때까지 유효하다.
- 함수 내에서 사용된 지역변수는 함수의 생명주기를 따라간다.
- var 보다는 let 과 const 를 사용하고 let을 사용할 경우 스코프의 범위를 좁게 만든다.

## 실행컨텍스트
## 클로저
## 생성자함수에의한 객체생성
## this
## 모듈화
## 순수함수
순수함수의 조건
- 동일한 인자가 들어갈 경우 항상 같은 값이 나와야 한다.
- 부수적인 효과가 일어나면 안된다.
- return 값으로만 소통한다.
- 순수함수는 같은 상황이라면 항상 같은 값이 나오기 때문에 평가시점이 중요하지 않다.

### 순수함수
```javascript
function add(x, y) {
	return x + y;
}
console.log(add(1, 2)) // 3
console.log(add(1, 2)) // 3
console.log(add(1, 2)) // 3
```

### 비순수함수
```javascript
let z = 3;
function add(x, y) {
	return x + y + z;
}
console.log(add(1, 2)) // 6
z = 5;
console.log(add(1, 2)) // 8
```

## 일급객체
자바스크립트의 함수는 일급객체 또는 일급함수 라고 부른다. 일급객체라고 불리우기 위해서 필요한 조건들이있는데  
- 런타임에 생성 가능하다.
- 변수나 자료구조에 저장할 수 있다.
- 함수의 매개변수에 전달할 수 있다.
- 함수의 반환값으로 사용가능하다.

## 즉시실행함수

## 고차 + 콜백 + 커리함수
고차함수는 함수를 인자로 받거나 함수를 리턴하는 함수를 말한다. 이때 다른 함수의 인자로 전달되는 함수를 콜백함수라고 한다.
```javascript
function func (callback) {
	let x = 1;
  	let y = 2;
  	return callback(x, y)
}
function add (x, y) {
	return x + y;
}
console.log(func(add)) // 3
```
이와같은 코드에서 add 는 func의 콜백함수가 된다. 이때 함수를 리턴하는 함수를 칭하는 용어를 따로 부를수도있는데 그걸 커리함수라고 한다.

```javascript
function add(x) {
	return function (y) {
    	return x + y;
    }
}
console.log(add(1)(2)) // 3
```
위와같은 패턴의 사용은 유인동저자의 `함수형 자바스크립트 프로그래밍` 이란 책에서 가장 처음에 나오는 패턴의 코드이기도 하다. 우리가 무의식적으로 사용하던 내장 메소드중에서도 이런 고차함수로 구현되어있는 메소드들이 존재한다.
```javascript
[1, 2, 3, 4].filter(function (el) {
	return el % 2 === 0; // [2, 4]
})

[1, 2, 3, 4].map(function (el) {
	return el * 3 // [3, 6, 9, 12]
})
```
자주 사용하게되는 배열의 내장메소드들이 고차함수이자 콜백함수를 갖는 메소드들이다.

## 함수형프로그래밍
함수형프로그래밍이란.... 일단 시작에 앞서 위에 나온 이론적인 내용들에 대한 충분한 이해가 필요로한다. 함수형프로그래밍이란 부수효과를 최소화하고 조합성을 강조하는 프로그래밍 기법이라고 볼 수 있다. 따라서 부수효과를 줄이기위해서 각각의 함수는 순수함수에 가깝거나 순수함수여야하고 순수함수라는 말은 하나의 함수는 하나의 역할만 맡아야 사이트이펙트를 최소화 할 수 있다. 그리고 함수를 잘 조합해서 쓰기위해서는 콜백, 고차함수, 일급객체, 클로저 등에 대한 이해가 바탕이 되어야 제대로된 함수의 조합을 만들어 낼 수 있다.

### 함수형 자바스크립트의 10가지 기법
> 
1. 함수를 되도록 작게 만들기 - (순수함수 + 한가지 역할만 하는 함수)
2. 다형성 높은 함수 만들기 - ?? 잘 모르겠음
3. 상태를 변경하지 않거나 정확히 다루어 부수 효과를 최소화하기
4. 동일한 인자를 받으면 항상 동일한 결과를 리턴하는 순수 함수 만들기
5. 복잡한 객체 하나를 인자로 사용하기보다느 되도록 일반적인 값 여러개를 인자로 사용하기
6. 큰 로직을 고차 함수로 만들고 세부 로직을 보조함수로 완성하기
7. 어느 곳에서든 바로 실행하거나 혹은 미뤄서 실행할 수 있도록 일반 함수이자 순수함수로 선언하기
8. 모델이나 컬렉션 등의 커스텀 객체보다는 기본 객체를 이용하기
9. 로직의 흐름을 최대한 단방향으로 흐르게 하기
10. 작은 함수를 모아 큰 함수 만들기
>-함수형 자바스크립트 프로그래밍 중에서-



## 모듈화 디자인 패턴
자바스크립트에는 `private` 라는 키워드를 갖고있지않다. (타입스크립트를 사용하거나 최근에 추가된 `#` 을 사용하면 되긴됨 ) 그래서 다양한 은닉법 또는 모듈화 디자인 패턴을 만들어서 `private` 키워드를 가진것처럼 동작하게 만들 수 있다.

### 객체 리터럴 사용
```javascript
const module = {
	privateKey: 'key',
  	ketMethod: function() {
    	return this.privateKey;
    }
}
console.log(module.privateKey) // key
console.log(module.ketMethod()) // key

module.privateKey = 'changeKey';

console.log(module.privateKey) // changeKey
console.log(module.ketMethod()) // changeKey
```
싱글톤패턴이기도 하며 단하나의 객체만 존재하게 만들 수 있는 가장 기본적인 패턴이지만 모든 속성이 공개되어있고 외부에 의해서 속성의 값이 변경 가능하다.

비공개변수를 갖도록 만들어주려면 즉시실행함수의 특성을 활용하여 만들 수 있다. 즉시실행함수는 함수와 변수의 생명주기를 활용하여 실행후 함수와 변수가 소멸되는 점을 이용하여 비공개변수를 만들어 은닉시킬 수 있다.


### 즉시실행함수 + 클로저 사용
```javascript
// 싱글톤 패턴
const module = (function() {
  let privateKey = 0; // 은닉시킬 변수
  let publicValue = 'public' // 공개할 변수
  
  function increaseMethod() { // 클로저로 사용될 메소드
  	return privateKey++;
  }
  
  return { // 공개할 프로퍼티
    publicValue: publicValue,
  	publicMethod: function() {
    	return increaseMethod();
    }
  }
})()
console.log(module.privateKey) // undefined
console.log(module.publicValue) // public
console.log(module.publicMethod()) // 0
console.log(module.publicMethod()) // 1
```

위와같이 즉시실행함수와 클로저를 활용하면 공개할 프로퍼티와 은닉시킬 프로퍼티를 지정하여 모듈화 시켜 사용할 수 있다.

### 생성자함수 사용
생성자함수를 사용하여 모듈화를 구현하면 여러개의 인스턴스를 생성하여 활용할 수 있다.
```javascript
const Module = function() {
  let privateKey = 0; // 은닉시킬 변수
  let publicValue = 'public' // 공개할 변수

  function increaseMethod() { // 클로저로 사용될 메소드
    return privateKey++;
  }

  return { // 공개할 프로퍼티
    publicValue: publicValue,
    publicMethod: function() {
      return increaseMethod();
    }
  }
}
const module1 = Module()
const module2 = Module()
console.log(module1.publicMethod()) // 0
console.log(module1.publicMethod()) // 1

console.log(module2.publicMethod()) // 0
console.log(module2.publicMethod()) // 1
```
클로저를 활용한 싱글톤패턴과 비슷해 보일 수 있는데 차이점은 클로저를 활용한 패턴은 객체를 리턴하고 생성자함수를 사용한 방법은 함수를 리턴한다. 즉시실행함수를 사용한것과 안한것의 차이이고 자바스크립트에서는 생성자함수를 만들때는 네이밍을 첫글자를 대문자로 생성한다.

>생성자 함수라고 부르고있지만 정확히 하자면 위의 예제는 일반 함수에 더 가깝다. this 를 사용했을때 그 차이를 알 수 있는데 일반함수의 this 는 전역객체를 가르키고 진짜 생성자 함수의 this 는 생성할 인스턴스를 가르킨다.

```javascript
const normalFunc = function(a) {
  	this.a = a;
	return this
}
const CreateFunc = function(a) {
	this.a = a;
	return this
}
const normal = normalFunc(1);
const create = new CreateFunc(2);

console.log(normal); // window 객체
console.log(create); // CreateFunc {a: 2}
```
