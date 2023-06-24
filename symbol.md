# symbol
심벌값은 다른값과 중복되지않은 유일무이 한값이다. 
유일한 프로퍼티 키를 만들기 위해서 사용한다.

## symbol 값의 생성
symbol 함수를 호출하여 생성한다.
생성된 symbol 값은 다른값과 절대 중복되지않은 유일한 값이다.

```javascript
const minSymbol = Symbol();

```
symbol 함수는 new 연산자와 호출하지않는다.

## symbol값
생성된 symbol값은 문자열이나 숫자 타입으로 변한되지않는다.
하지만 불리언 값으로는 변경가능하다.
