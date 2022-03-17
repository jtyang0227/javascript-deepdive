# this
> JavaScript에서 함수의 this 키워드는 다른 언어와 조금 다르게 동작합니다. 또한 엄격 모드와 비엄격 모드에서도 일부 차이가 있습니다.
대부분의 경우 this의 값은 함수를 호출한 방법에 의해 결정됩니다. 실행중에는 할당으로 설정할 수 없고 함수를 호출할 때 마다 다를 수 있습니다. ES5는 함수를 어떻게 호출했는지 상관하지 않고 this 값을 설정할 수 있는 bind 메서드를 도입했고, ES2015는 스스로의 this 바인딩을 제공하지 않는 화살표 함수를 추가했습니다(이는 렉시컬 컨텍스트안의 this값을 유지합니다).

```javascript
// 객체 리터럴
const circle = {
  radius: 5,
  getDiameter() {
    // this는 메서드를 호출한 객체를 가리킨다.
    return 2 * this.radius;
  }
};

console.log(circle.getDiameter()); // 10
```
* 객체 리터럴의 메서드 내부에서는 `this`는 메서드를 호출한 객체, circle 가리킨다.

```javascript
// 22-04
// 생성자 함수
function Circle(radius) {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  return 2 * this.radius;
};

// 인스턴스 생성
const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```
* 생성자 함수 내부의 this는 생성자 함수가 생성한 인스턴스를 가리킨다. 이처럼 this는 상황에 따라 가르키는 대상이 다릅니다.

### 자바스크립트의 `this`는 함수가 호출되는 방식에 따라 `this`에 바인딩될 값. 즉 `this binding`이 동적으로 결정됩니다. 또한 `strct mode`(엄격모드) 역시 this 바인딩에 영향을 줍니다.

```javascript
// this는 어디서든지 참조 가능하다.
// 전역에서 this는 전역 객체 window를 가리킨다.
console.log(this); // window

function square(number) {
  // 일반 함수 내부에서 this는 전역 객체 window를 가리킨다.
  console.log(this); // window
  return number * number;
}
square(2);

const person = {
  name: 'yang',
  getName() {
    // 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다.
    console.log(this); // {name: "yang", getName: ƒ}
    return this.name;
  }
};
console.log(person.getName()); // yang

function Person(name) {
  this.name = name;
  // 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  console.log(this); // Person {name: "yang"}
}

const me = new Person('yang');
```

## 함수 호출 방식과 this 바인딩
### this 바인딩은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정됩니다.
* 바인딩(binding) : 식별자와 값을 연결하는 과정입니다.

#### 함수 호출하는 방식
1. 일반 함수 호츌
2. 메서드 호출
3. 생성자(new) 함수 호출
4. Function.prototype.apply/call/bind 메서드의 의한 간접 호출

```javascript
// this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.
const foo = function () {
  console.dir(this);
};

// 동일한 함수도 다양한 방식으로 호출할 수 있다.

// 1. 일반 함수 호출
// foo 함수를 일반적인 방식으로 호출
// foo 함수 내부의 this는 전역 객체 window를 가리킨다.
foo(); // window

// 2. 메서드 호출
// foo 함수를 프로퍼티 값으로 할당하여 호출
// foo 함수 내부의 this는 메서드를 호출한 객체 obj를 가리킨다.
const obj = { foo };
obj.foo(); // obj

// 3. 생성자 함수 호출
// foo 함수를 new 연산자와 함께 생성자 함수로 호출
// foo 함수 내부의 this는 생성자 함수가 생성한 인스턴스를 가리킨다.
new foo(); // foo {}

// 4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출
// foo 함수 내부의 this는 인수에 의해 결정된다.
const bar = { name: 'bar' };

foo.call(bar);   // bar
// call() : 메소드는 주어진 this 값 및 각각 전달된 인수와 함께 함수를 호출합니다.

foo.apply(bar);  // bar
// apply() : 메서드는 주어진 this 값과 배열 (또는 유사 배열 객체) 로 제공되는 arguments 로 함수를 호출합니다.

foo.bind(bar)(); // bar
// bind() : 메소드가 호출되면 새로운 함수를 생성합니다. 받게되는 첫 인자의 value로는 this 키워드를 설정하고, 이어지는 인자들은 바인드된 함수의 인수에 제공됩니다.
```

## 일반 함수 호출
```javascript
// 22-07
function foo() {
  console.log("foo's this: ", this);  // window
  function bar() {
    console.log("bar's this: ", this); // window
  }
  bar();
}
foo();
```
* 위 예제(22-07)는 전역 함수, 중첨 함수를 일반 함수로 호출하면 함수 내부의 `this`에 전역 객체가 바인딩됩니다.

> `this`는 객체의 프로퍼티, 메서드를 참조하기 위한 자기 참조 변수이므로 객체 생성하지 않는 일반 함수에서 `this`는 의마가 없습니다. 따라서 다음 예제(22-08)처럼 strict mode가 적용된 일반 함수 내부의 `this`에 `undefined`가 바인딩 됩니다.

```javascript
// 22-08
function foo() {
  'use strict';

  console.log("foo's this: ", this);  // undefined
  function bar() {
    console.log("bar's this: ", this); // undefined
  }
  bar();
}
foo();
```
---
```javascript
// 22-09
// var 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티다.
var value = 1;
// const 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티가 아니다.
// const value = 1;

const obj = {
  value: 100,
  
  foo() {
    console.log("foo's this: ", this);  // {value: 100, foo: ƒ}
    console.log("foo's this.value: ", this.value); // 100

    // 메서드 내에서 정의한 중첩 함수
    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1
    }

    // 1. 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출.
    // 2. 중첩 함수 내부의 this에는 전역 객체가 바인딩된다.
    bar();
  }
};

obj.foo();
```
* 콜백 함수가 일반 함수로 호출된다면 콜백 함수 내부의 `this`에도 전역 객체가 바인딩됩니다. 어떠한 함수라도 일반 함수로 호출되면 `this`에 전역 객체가 바인딩 됩니다.

```javascript
//22-10
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // {value: 100, foo: ƒ}
    
    // 콜백 함수 내부의 this에는 전역 객체가 바인딩된다.
    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  }
};

obj.foo();
```

## setTimeout
* 메서드는 만료된 후 함수나 지정한 코드 조각을 실행하는 타이머를 설정합니다.

```javascript
// 22-11
var value = 1;

const obj = {
  value: 100,
  foo() {
    // this 바인딩(obj)을 변수 that에 할당한다.
    const that = this;

    // 콜백 함수 내부에서 this 대신 that을 참조한다.
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  }
};

obj.foo();

// 22-12
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 콜백 함수에 명시적으로 this를 바인딩한다.
    setTimeout(function () {
      console.log(this.value); // 100
    }.bind(this), 100);
  }
};
obj.foo();

// 22-13
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 : 내부this는 상위 스코프의 this를 가리킨다.
    setTimeout(() => console.log(this.value), 100); // 100
  }
};
obj.foo();
```

## 메서드 호출
```javascript
// 22-14
const person = {
  name: 'yang',
  getName() {
    // 메서드 : 내부 this는 메서드를 호출한 객체에 바인딩 됩니다.
    return this.name;
  }
};

// 메서드 getName을 호출한 객체는 person이다.
console.log(person.getName()); // yang

// 22-15
const anotherPerson = {
  name: 'jetty'
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // jetty

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName()); // ''
```
* 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
  브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
  Node.js 환경에서 this.name은 undefined다.


```javascript
// 22-16
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

// this.name 초기값 할당
const me = new Person('yang');

// getName 메서드를 호출한 객체는 me다.
console.log(me.getName()); // yang

// this.name 재할당
Person.prototype.name = 'jetty';

// getName 메서드를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // jetty
```

## 생성자 함수 호출
```javascript
// 22-17
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 반지름이 5인 Circle 객체를 생성
const circle1 = new Circle(5);

// 반지름이 10인 Circle 객체를 생성
const circle2 = new Circle(10);

// this.getDiameter === 2 * Circle(5)
console.log(circle1.getDiameter()); // 10

// this.getDiameter === 2 * Circle(10)
console.log(circle2.getDiameter()); // 20
```

## Function.prototype.apply/call/bind 메서드의 의한 간접 호출
* apply, call, bind 메서드는 Function.prototype 메서드입니다. 즉 메서드는 모든 함수가 상속받아 사용할 수 있습니다.
```javascript
// 22-18
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다. 즉, 일반적인 함수의 호출이다.
const circle3 = Circle(15);

// 일반 함수로 호출된 Circle에는 반환문이 없으므로 암묵적으로 undefined를 반환한다.
console.log(circle3); // undefined

// 일반 함수로 호출된 Circle 내부의 this는 전역 객체를 가리킨다.
console.log(radius); // 15

// 22-19
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

console.log(getThisBinding()); // window

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
console.log(getThisBinding.apply(thisArg)); // {a: 1}
console.log(getThisBinding.call(thisArg)); // {a: 1}

// 22-20
function getThisBinding() {
  console.log(arguments);
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
// apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}

// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
```
* `apply` : 호출할 함수의 인수(`Array`)로 전달합니다.
* `call` : 호출하는 함수의 인수(`List`) 전달합니다.


```javascript
// 22-21
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수없이 호출하면 배열의 복사본을 생성한다.
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]

// 22-22
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// bind 메서드는 첫 번째 인수로 전달한 thisArg로 this 바인딩이 교체된
// getThisBinding 함수를 새롭게 생성해 반환한다.
console.log(getThisBinding.bind(thisArg)); // getThisBinding
// bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다.
console.log(getThisBinding.bind(thisArg)()); // {a: 1}

// 22-23
const person = {
  name: 'yang',
  foo(callback) {
    // ①
    setTimeout(callback, 100);
  }
};

person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // ② Hi! my name is .
  // 일반 함수로 호출된 콜백 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
  // 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
  // Node.js 환경에서 this.name은 undefined다.
});

// 22-24
const person = {
  name: 'yang',
  foo(callback) {
    // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
    setTimeout(callback.bind(this), 100);
  }
};

person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // Hi! my name is yang.
});
```

## 함수 호출 방식 : this 바인딩
* 일반 함수 호출 : 전역 객체 
* 메소드 호출 : 메서드를 호출한 객체 
* 생성자 함수 호출 :생성자 함수가 생성할 인스턴스 
* Function.prototype.apply/call/bind 메서드의 의한 간접 호출 : Function.prototype.apply/call/bind 메서드에 첫번째 인수로 전달한 객체