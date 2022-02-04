# let, const 키워드와 블록 레벨 스코프

* var 키워드로 선언한 변수의 문제점
### 1. 변수의 중복 선언 허용
```javascript
var x = 1;
var y = 1;

var x = 1000;
var y;
console.log(x); // output : 1000
console.log(y); // output : 1
```

### 2. 함수 레벨 스코프
> 
```javascript
var x = 1;
if(true) {
  // x(전역) 변수와 중복 선언을 인정합니다.
  // 예상하지 못하는 부작용 발생할 변수가 생성됩니다.
  var x = 100;
}

console.log(x) // output : 100
```

### 3. 변수 호스팅
```javascript
// 1 변수 호스팅에 의해 이미 foo 변수가 선언
// 2 foo는 undefined로 초기화됩니다.
console.log(foo); // output : undefined

// 3 변수에 값 할당
foo = 10000;

console.log(foo); // output : 10000

// 변수 선언은 **런타임** 이전에 자바스크립트 엔진에 의해 암목적으로 실행합니다.
var foo;
```

## let 키워드
> var 키워드 단점을 보완하기 위해 ES6에 새로운 변수 선언 키워드 let, const 도입했습니다.
### 1. 변수 중복 선언 금지
```javascript
let a = 1;
let a = 100; // Error
```
### 2. 블록 레벨 스코프
```javascript
let foo = 1;
{
  let foo = 100;
  let doo = 50;
}

console.log(foo); // output : 1
console.log(doo); // Error : doo is not defiend
```

### 3. 변수 호이스팅
* var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행합니다 -> **실행 컨텍스트**
```javascript
console.log(foo); // Error : doo is not defiend

// 1. 변수 선언 후 초기화 단계
let foo;
console.log(foo); // output : undefined

// 2. 할당 단계
foo = 100;
console.log(foo) // output : 100
```

## const 키워드
> 상수를 선언하기 위해 사용합니다
```javascript
{
  console.log(foo); // Error
  const foo = 1;
  console.log(foo); // output : 1
}

// foo(변수)는 블록 레벨 스코프를 갖는다.
console.log(foo) // Error : foo is not defiend
```

### 1. 재할당 금지
> var, let 재할당이 자유롭다, 하지만 const 키워드는 **변수 재할당을 금지합니다.**
```javascript
const doo = 1;
doo = 1000; // Erro
```
### 2. 상수
> 변수의 상대 개념인 상수는 재할당이 금지된 변수로 말합니다.
> 상수도 값을 저장하기 위한 **메모리 공간이 필요하므로 변수라고 할 수 있습니다.**
> 상수의 장점으로 **상태 유지와 가독성으 유지보수의 편의성를 위해 적극적으로 사용해야 합니다.**
```javascript
const RATE = 0.2
let price = 10000;

let totalPrice = price + (price * RATE);
console.log(totalPrice); // output : 12000
```

### 3. const 키워드와 객체
> const 키워드로 선언된 변수 원시값을 재할당 변경할 수 없습니다. 하지만 객체로 할당한 경우 값은 변경할 수 있습니다.
```javascript
const info = {
  name : 'yang',
}

info.name = 'jetty';
console.log(info.name); // output : {name: "jetty"}
```