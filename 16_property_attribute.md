# property(프로퍼티)와 attribute(어트리뷰트)
> 자바스크립트 엔진의 내부 로직에는 원칙적으로는 접근할 수 없지만, 내부 슬룻(__proto__)를 통해 간접적으로 접근할 수 있습니다.
### property 접근
```javascript
const test = {};

// 접근 방법
test.__proto__ // Object.prototype
```

### 접근자 프로퍼티
> 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.
* 접근자 함수는 getter/setter 함수 부른다. 함수를 정의합니다.
```javascript
const person = {
  firstName: 'jt',
  lastName: 'yang',
  
  // getter function
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  
  // setter function
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ');
  }
}

// 데이터 프로퍼티 참조값
console.log(person.firstName +'_'+ person.lastName); // output : 'jt_yang'

// setter function
person.fullName('jongtae yang');
console.log(person); // output : {firstName: "jongtae", lasName: "yang"}

// getter function
console.log(person.fullName); // output : "jt yang"
```

### 프로퍼티 정의
> 새로운 프로퍼티를 추가하면서 프로퍼티 어트티뷰티를 명시적으로 정의
* defineProperty : 객체 정의
```javascript
const propertyAdd = {};
// 데이터 정의: data1`
Object.defineProperty(propertyAdd, 'info', {
  value: 'jt',
  name: 'yang'
});
Object.defineProperty(propertyAdd, 'address', {
  info: 'Seoul'
});

let getDescriptor = Object.getOwnPropertyDescriptors(propertyAdd, 'info');
console.log('info', getDescriptor); // output : data1 추출됩니다.
```
![img.png](img.png)

### 객체 변경 방지(preventExtensions)
> 변경 가능한 값이므로 재할당 없이 변경할 수 있다. **확장된 객체는 추가 금지됩니다.**
* isExtensible : 확장 여부 확인

```javascript
const person = { name: 'jt'};

// 1. 확장 금지된 객체가 아니다.
console.log(Object.isExtensible(person)); // true

// 2. 확장 금지 적용
Object.preventExtensions(person);
console.log(Object.isExtensible(person)); // false

// 3. 추가 금지
person.age = 100; // ERROR

// 4. 삭제 가능
delete person.name;
console.log(person); // output : {};
```

### 객체 밀봉(seal)
> 프로퍼티 추가, 삭제, 재정의 금지합니다. **밀봉된 객체는 읽기와 쓰기만 가능합니다.**
* isSealed : 밀봉 상태 여부 확인
```javascript
const person = { name: 'jt'};

// 1. 밀봉(seal) 상태가 아닙니다.
console.log(Object.isSealed(person)); // false

// 2. 밀봉 상태 변경 
Object.seal(person);
console.log(Object.isSealed(person)); // true

// 3. 프로퍼티 정의된 값만 갱신 가능합니다.
person.name = 'yang';
console.log(person); // output : {name : "yang"}
```

### 객체 동결(freeze)
> 프로퍼티 추가, 삭제, 재정의, 쓰기 금지합니다. **동결된 객체는 읽기만 가능합니다.**
* isFrozen : 객체 동결 여부 확인
```javascript
const person = { name: 'jt'};

// 1. 동결(freeze) 상태가 아닙니다.
console.log(Object.isFrozen(person)); // false

// 2. 동결 적용
Object.freeze(person);
console.log(Object.isFrozen(person)); // true

console.log(Object.getOwnPropertyDescriptors(person)) // true

// 추가 금지
person.age = 11; // ERROR

// 삭제 금지
delete person.name; // ERROR

// 갱신 금지
person.name = 'jongtae'; // ERROR

// 재정의 금지 
Object.defineProperty(person, "name", {
  address: { city: "Seoul" },
})
```

### 불변 객체
> 지금까지 메소드(preventExtensions, seal, freeze)로 직속 프로퍼티만 변경이 방지되는 얕은 변경 방지이다.
> 그래서 Object.freeze 메소드로 객체를 동결하여도 중첩 객체 까지 동열할 수 없다.
* **읽기 전용 불변 객체에서 갖는 모든 프로퍼티에 대해 Object.freeze 메소드 적용합니다.**
```javascript
const person = {
  name: "jetty",
  address : { city: "Seoul" }
};

// 불변 함수
function freeze(obj) {
  Object.keys(obj).forEach((key) =>
    typeof obj[key] === "object" ? freeze(obj[key]) : Object.freeze(obj)
  );
}

// 불변 함수 적용
freeze(person);

// isFrozen : 동결 여부 확인
console.log(Object.isFrozen(person.name)); // true
console.log(Object.isFrozen(person.address.city)); // true

person.name = "yangjongtae";
person.address.city = "foo";
console.log(person);
```

