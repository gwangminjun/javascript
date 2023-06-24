## strict mode

```javascript
function test(name) {
  x = 10;
}

test();

console.log(x); // ??
```

해당 코드는 오류가 발생할거같지만 발생하지 않는다
<br> x 는 선언되지 않앗지만 자바스크립트 엔진은 전역 객체에 x 프로퍼티를 동적 생성한다. 이때 x 프로퍼티는 전역변수처럼 사용되는데 이를 암묵적 전역 이라고 하는데 이는 치명적인 오류의 원인이 되기 쉽다.

이러한 에러를 잡기위해 es5 부터 strict mode가 추가되었다.

### strict mode 의 적용

전역의 선두 혹은 함수 몸체에 'use strict'를 추가한다

### 전역에 선언은 피하자

전역에 선언하는 경우 전체에 선언되는데
외부 서드 파티 라이브러리를 사용하는경우 라이브러리가 'none-strict mode'인 경우가 있기때문에 전역에 선언하는건 피해야한다.

### 함수 단위도 피하자

모든 함수에 일일이 적용하는것도 번거러운 일이며 나중에 적용한 함수를 참조하는 외부 컨텍스트가 'none-strict mode'인 경우가 있기때문에 피해야 한다
<br> 따라서 즉시 실행 함수로 감싼 스크립트 단위로 적용하는것이 바람직하다

### strict mode가 찾는 에러

1. 암묵적 전역

2. 변수, 함수, 매개변수
   변수, 함수, 매개변수 삭제시 에러

```javascript
(function test() {
  "use strict";
  var x = 1;
  delete x; // error
})();
```

3. 매개변수 이름의 중복

```javascript
(function test() {
  "use strict";
  function ttest(x, x) {
    //error
  }
})();
```

4. with 문의 사용
   with 문은 전달된 객체를 스코프 체인에 추가한다.
   동일한 객체의 프로퍼티를 반복하여 사용시 객체이름 생략위험이 있어서 사용하지 않는것이 좋다

```javascript
(function test(name) {
  "use strict";
  with ({ x: 1 }) {
    console.log(x); //error
  }
})();
```

### strict mode 적용에 의한 변화

1. 일반 함수의 this

- 함수를 일반 함수로 호출시 this에 undefined가 바인딩된다
- 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다.

2. arguments 객체

- 매개변수에 전달된 인수를 재할당하여 변경해도 argumnets 객체에 반영되지 않는다
