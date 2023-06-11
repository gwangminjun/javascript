## 26장 ES6 함수의 추가기능

### 함수의 구분
ES6 이전의 모든 함수는 일반 함수로서 호출할 수 있는것은 물론 생성자 함수로서 호출할수 있었다

이러한 이유로 인하여 함수에 전달되어 보조 함수의 역할을 수행하는 콜백함수또한 불필요한 프로토타입 객체를 생성했엇다.

그리하여 ES6 이후 부터는 사용목적에 따라 종류로 구분햇다


| 함수 구분           | constructor                   | prototype | super | arguments | 
| -------------- | ------------------------ | ------------- | ------------- | ---------------- | 
| 일반 함수   |O  |O              | X            | O                | 
| 메서드      | X| X             |O             | O                | 
| 화살표 함수 |X| X             | X             | X                |


### 메서드
```javascript
const base = {
    name : 'minjun',
    sathi () {
        return `hi! ${this.name}`;
    }
}

const derived = {
    __proto__:base,
    //super은 sayHi의 [[HomeObject]]의 프로토 타입인 base를 가리킨다.
    sayHi() {
        return `${super.sayHi()}`;
    }
}
```
ES6 사양에서 메서드는 메서드 축약 표현으로 정의된 함수만을 의미한다.

또한 정의한 메서드는 인스턴스를 생성할수 없는 non-constructor 이다. 고로 생성자 함수로서 호출할수 없다.

ES6 메서드는 자신을 바인딩한 객체를 가리키는 내부 슬롯 [[HomeObject]]를 갖는다.
super의 참조는 내부 슬롯 [[HomeObject]]를 사용하여 수퍼클래스의 메서드를 참조하므로
ES6 메서드가 아닌 함수는 super를 사용할수 없다 

### 화살표 함수
기존의 함수정의 방식보다 간략하게 함수를 정의한다.

- 객체 리터럴 반환시
    - 객체 리터럴을 반환시에 소괄호로 감싸 주어야 한다.
    ```javascript
    const arrow = (id, content) => ({ id , content});
    arrow(1, 'javascript'); // -> {id:1 , content:'javascript'}

    const arrow2 = (id, content) => { id , content};
    arrow1(1, 'javascript'); // -> undefined
    ```

### 화살표 함수와 일반함수 차이
1. 화살표함수는 인스턴스를 생성할수 없다
    - 인스턴스를 생성할수 없으므로 prototype 프로퍼티가 없고 프로토타입도 생성치 않는다.

2. 중복된 매개변수 이름을 선언할수 없다.

3. 함수자체의 this,arguments,super,new.target 바인딩을 갖지 않는다.
    - 때문에 화살표 함수 내부에서 this,arguments,super,new.target 참조시 스코프 체인을 통해 상위 스코프의 this,arguments,super,new.target를 참조한다.

### 화살표 함수의 this

- 콜백함수 내부의 this 문제
    - this 바인딩은 정적으로 결정되어 있지 않고 함수가 호출할때 어떻게 호출되었는지에 따라 결정된다
    - 이때 문제는 일반함수로서 호출되는 콜백함수이다.
     ```javascript
    class Prefixer {
        constructor(prefix){
            this.prefix = prefix;
        }

        add (arr) {
            return arr.map(function(item) {
                return this.prefix + item;
            });
        }
    }

    const prefixer = new Prefixer('-webkit-');
    console.log(prefixer.add(['transition' , 'user-select']));

    // result : TypeError
    ```
    - 해당 코드에서 우리가 원하는결과는 ['-webkit-transition', '-webkit-user-select']이다 하지만 결과는 TypeError 가 발생한다.
        1. 일단 add 함수 내부의 this는 메서드를 호출한 객체 prefixer를 가리킨다.
        2. 그런데 map의 인수로 전달한 콜백함수의 내부에서 this는 undefiend를 가리킨다. 이유는 map 메서드가 콜백함수를 일반함수로서 호출하기 때문이다.
            - 일반함수로서 호출되는 모든 함수 내부의 this는 전역객체를 가리킨다.
            - 그러나 클래스 내부의 모든 코드는 strict mode가 암묵적으로 적용된다.
            - 따라서 map메서드의 콜백함수에도 strict mode가 적용된다.
            - strict mode에서 일반함수로서 호출된 모든 함수내부의 this는 전역객체가 아닌 undefined가 바인딩되어 map 메서드의 콜백함수 내부의 this에는 undefiend가 바인딩된다.
        3. 이러한 이유로 콜백함수 의 this와 외부함수의 this는 서로 다른값을 가리키게 되어 TypeError가 발생한다.
    
    - 해결
        1. add 메서드를 호출한 prefixer 객체를 가리키는 this를 일단 회피시킨 후에 콜백 함수 내부에서 사용한다.
        ```javascript
        ...
            add (arr) {
                const that = this;
                return arr.map(function(item) {
                    return that.prefix + item;
                });
            }
        ...
        ```
       
        2. map의 두번째 인수로 add 메서드를 호출한 prefixer 객체를 가리키는 this를 전달한다.
        ```javascript
        ...
            add (arr) {
                return arr.map(function(item) {
                    return that.prefix + item;
                },this); //this에 바인딩 된 값이 콜백함수 내부의 this에 바인딩된다.
            }
        ...
        ```

        3. bind 메서드를 사용하여 add 메서드를 호출한 prefixer 객체를 가리키는 this를 바인딩한다.
         ```javascript
        ...
            add (arr) {
                return arr.map(function(item) {
                    return that.prefix + item;
                }.bind(this)); //this에 바인딩 된 값이 콜백함수 내부의 this에 바인딩된다.
            }
        ...
        ```

        4. 화살표함수
        ```javascript
        ...
            add (arr) {
                return arr.mapfunction(item => return this.prefix + item);
            }
        ...
        ```
        - 화살표함수는 함수 자체의 this 바인딩을 갖지않는다.
        - 따라서 화살표 함수 내부에서 this를 참조하면 상위 스코프의 this를 그대로 참조한다. 
        - 해당 내용을 lexical this라 한다.
        - 화살표 함수 내부에서 this를 참조하면 일반적인 식별자 처럼 스코프 체인을 통해 상위 스코프에서 this를 탐색한다.

        - 활용
            - 만약 화살표함수가 중첨되있다면 스코프 체인 상에서 가장 가까운 상위 함수중에서 화살표 함수가 아닌 함수의 this를 참조한다.

            - 만약 화살표함수가 전역함수라면 this는 전역객체를 가리킨다.

            - 화살표함수는 this 바인딩을 가지지 않기때문에 내부의 this를 교체할수 없다.

        - 주의
            - (일반) 메서드를 화살표 함수로 정의하는것은 피해야한다.
                ```javascript
                    const person = {
                        name: 'minjun',
                        sayHi: () => console.log(`Hi ${this.name}`)
                    };

                    person.sayHi();//Hi
                ```
                -위의 경우 화살표 함수 내부의 this는 person을 가리키지않고 상위 스코프인 전역 this를 가리키는 전역객체를 가리킨다.
                - 고로 (일반) 메서드를 정의할때는 메서드 축약표현으로 사용하는것이 좋다
                ```javascript
                    const person = {
                        name: 'minjun',
                        sayHi() {
                            console.log(`Hi ${this.name}`);
                        } 
                    };
                    person.sayHi();//Hi minjun
                ```
                - 프로토타입 객체의 프로퍼티에 할당하는 경우도 마찬가지이다
            
            - 클래스 필드 정의 제안을 사용하여 클래스 필드에 화살표 함수 할당시
                ```javascript
                    class Person {
                        name: 'minjun',
                        sayHi = () => {
                            console.log(`Hi ${this.name}`);
                        } 
                    };

                    const person = new Person(); 
                    person.sayHi();//Hi minjun
                ```
                - 해당 코드의 화살표 함수의 상위 스코프는 사실 클래스 외부이다.
                - 하지만 this는 클래스 외부의 this를 참조하지않고 클래스가 생성한 인스턴스를 참조한다 .
                - 고로 참조한 this는 constructor 내부의 this 바인딩과 같다.
                - constructor 내부의 this 바인딩은 클래스가 생성한 인스턴스를 가리키므로 this또한 클래스가 생성한 인스턴스를 가리킨다.

                - 클래스 필드에 할당한 화살표 함수는 프로토타입 메서드가 아닌 인스턴스 메서드가 된다
                - 따라서 메서드 정의시 메서드 축약 표현으로 정의하는것이 좋다
                ```javascript
                    class Person {
                        name: 'minjun',
                        sayHi () {
                            console.log(`Hi ${this.name}`);
                        } 
                    };

                    const person = new Person(); 
                    person.sayHi();//Hi minjun
                ```
        - super
            - 화살표 함수는 자체 super 바인딩을 갖지않는다.
            - 고로 super 사용시 상위 스코프의 super를 참조한다.
        
        - arguments
            - 화살표 함수는 자체 arguments 바인딩을 갖지않는다.
            - 고로 super 사용시 상위 스코프의 arguments 참조한다.


### REST 파라미터
REST 파라미터는 매개변수 이름 앞에 세게의 점 ...을 붙여서 사용한다.

REST 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받는다.
```javascript
    function foo(...rest) {
        console.log(rest); // [ 1, 2, 3, 4, 5 ]
    }

    foo(1,2,3,4,5);
```

매개변수와 함께 사용할 수 있으며 , 순차적으로 할당된다.
```javascript
    function foo(param, ...rest) {
        console.log(param); // 1
        console.log(rest); // [ 2, 3, 4, 5 ]
    }

    foo(1,2,3,4,5);
```

- 주의
    - REST 파라미터는 단 하나만 선언할수 있다
    - REST 파라미터는 선언된 매개변수에 할당된 인수를 제외한 나머지 인수들로 구성된 배열이 할당되므로 반드시 마지막 파라미터이어야 한다.
    - REST 파라미터는 함수 정의시 선언한 매개변수 개수를 나타내는 length에 영향을 주지 않는다.
    ```javascript
        function foo(param, ...rest) {}
        console.log(foo.length); //0

        function foo1(param,param1, ...rest) {}
        console.log(foo1.length); //1

        function foo2(param,param1,param2, ...rest) {}
        console.log(foo2.length); //2
    ```

### 매개변수 기본값
```javascript
    function sum(x = 0, y = 0) {
        return x + y;
    }

    console.log(sum(1, 2)); //3 
    console.log(sum(1)); //1
```