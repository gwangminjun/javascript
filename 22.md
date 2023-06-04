### this

객체는 상태를 나타내는 프로퍼티와 동작을 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조 이다.
<br> 동작을 나타내는 메서드는 자신이 속한 객체의 상태를 참조 및 변경할 수 있어야하는데 이때 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.

### 필요성

생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수가 존재하여한다.<br> 생성자 함수를 정의하는 시점에는 아직 인스턴스를 생성하기 이전이므로 생성자 함수가 생성할 인스턴스를 가리키는 식별자를 알 수 없다. 따라서 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 특수한 식별자가 필요한데 그것이 this 이다.

### this 바인딩

바인딩이랑 식별자와 값을 연결 하는 과정이다. this 바인딩은 this 와 this 가 가리킬 객체를 바인딩 하는것 이다.
자바 나 c ++ 같은 클래스 기반 객체는 언제나 this는 클래스가 생성하는 인스턴스를 가리킨다 <br> 하지만 자바스크립트의 this는 함수가 호출되는 방식에 따라 this에 바인딩 될 값 , 즉 this 바인딩이 동적으로 결정된다.

### 함수 호출방식에 따른 this 바인딩

1. 일반 함수 호출

- this는 전역 객체에 바인딩된다.

```javascript
function test() {
  console.log(this); // window
  function ttest() {
    console.log(this); // window
  }
}
```

만약 'use strict'를 적용할경우 undefined가 바인딩된다
일반함수로 호출된 모든함수 내부의 this에는 전역 객체가 바인딩 된다.

2. 메서드 호출
   매서드 내부의 this에는 메스드를 호출한 객체가 바인됭 된다.
   <br> 매서드 내부의 this는 메서드를 소유한 객체가 아닌 매서드를 호출한 객체에 바인딩 된다.

3. 생성자 함수 호출
   생성자 함수 내부의 this에는 생성자 함수가 생성할 인스턴스가 바인딩된다.

```javascript
function test(name) {
  this.name = name;
  this.getName = function () {
    return this.name;
  };
}

const test1 = test("test1");
const test2 = test("test2");

console.log(test1.getName()); //test1
console.log(test1.getName()); //test2
```

4. apply/call/bind 메서드에 의한 간접 호출
   function.prototype.apply, function.prototype.call 메서드는 this로 사용할 객체와 인수리스트를 인수로 전달받아 함수로 호출한다.

- function.prototype.apply(thisArg[ , argsArrary])

  - thisArg : this로 사용할 객체
  - argsArrary : 함수로 전달할 인수 리스트의 배열또는 유사배열

- function.prototype.call (thisArg[, arg1[, arg2[, arg3[, ...]]]])
  - thisArg : this로 사용할 객체
  - arg1 ...: 함수에게 전달할 인수 리스트

apply와 call 메서드의 본질적인 기능은 함수를 호출하는것이다.
함수를 포훌하면서 첫번째 인수로 전달한 특정객체를 호출한 함수의 this에 바인딩한다.

```javascript
function thisBind(name) {
  console.log(arguments);
  return this;
}

const thisArg = { a: 1 };

//호출할 함수의 인수를 배열로 묶어 전달한다.
console.log(thisBind.apply(thisArg, [1, 2, 3]));

//호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(thisBind.call(thisArg, 1, 2, 3));
```

### bind

```javascript
const person = {
  name: "Lee",
  foo(callback) {
    setTimeout(callback.bind(this), 100);
  },
};

person.foo(function () {
  console.log(`${this.name}`); //lee
});
```

해당 예시는 원래는 bind를 하지않으면 콜백함수가 선언된시점에서는 일반함수로 선언되어 this는 전역객체의 name이 바인딩 되있지만
bind를 통하여 바인딩 해줌으로써 person의 name이 호출된다.
