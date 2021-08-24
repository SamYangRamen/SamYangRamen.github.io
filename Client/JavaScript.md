# Table of contents

- [JavaScript 기본 문법](#JavaScript-기본-문법)
  - [변수](#변수)
  - [연산자](#연산자)
  - [자료형](#자료형)
  - [조건문](#조건문)
  - [반복문](#반복문)
  - [예외 처리문](#예외-처리문)
  - [Promises](#Promises-★★★★★)
  - [문자열 처리](#문자열-처리)
  - [함수](#함수)

- [DOM(Document Object Model)](#DOM(Document-Object-Model))
  - [DOM이란?](#DOM이란?)
  - [관련 함수들](#관련-함수들)

<br/>

---



# JavaScript 기본 문법

- ### 변수

  - 변수는 var, let, const 등의 키워드로 선언 가능

    ```javascript
    var a = 2;
    var a = "aaa";
    var a = 'aaa';
    var a = true;
    var a = [];
    var a = {};
    var a = undefined;
    ```

  <br/>

- ### 연산자

  - #### 삼항연산자

    - 간단한 비교와 값 할당은 삼항연산자를 사용

      ```javascript
      const data = 11;
      const result = (data > 10) ? "ok" : "fail";
      console.log(result);
      ```

      <br/>

  - #### 비교연산자

    - `==`와 `===`이 있음

    - `==`로 인한 다양한 오류 상황이 있기 때문에, 비교할 때는 `==`보다는 `===`을 사용

    - `==`sms 

      ex) 오류 상황 예시

      ```javascript
      0 == false;			// true
      "" == false;		// true
      null == false;		// false
      0 == "0";			// true
      0 === "0";			// false
      null == undefined;	// true
      ```

      <br/>

- ### 자료형

  - 자바스크립트의 자료형에는 다양한 것이 존재함

    ```javascript
    undefined, null, boolean, number, string, object, function, array, Date, RegExp
    ```

  - 자료형은 선언할 때가 아닌, 실행 타임에 따라 결정됨
  - 자료형을 체크하는 또렷한 방법은 없음. 정확히는 **toString.call** 을 이용해서 그 결과를 매칭하곤 하는데, 문자 및 숫자와 같은 기본 자료형은 **typeof** 키워드를 사용하여 체크함
  - 배열의 경우 자료형을 체크하는 **isArray** 함수가 표준으로 존재함. 단, 브라우저 지원 범위를 살펴보고 사용해야 함

  <br/>

- ### 조건문

  - #### if....else문

    ```javascript
    if (condition_1) {
      // statement_1;
    } else if (condition_2) {
      // statement_2;
    } else if (condition_n) {
      // statement_n;
    } else {
      // statement_last;
    } 
    ```

  - #### 거짓으로 취급하는 값

    ```javascript
    false, undefined, null, 0, NaN, ""
    ```

  - #### switch문

    ```javascript
    switch (expression) {
      case label_1:
        // statements_1
        break;
      case label_2:
        // statements_2
        break;
        ...
      default:
        // statements_def
        break;
    }
    ```

  <br/>

- ### 반복문

  - #### for문

    ```javascript
    for([초기문]; [조건문]; [증감문]) {
    	// statements;
    }
    
    for(variable in object)	// 인덱스를 다룸
        // statements;
        
    for(variable of object)	// 값을 다룸
        // statements;
    ```

  - do...while문

  - while문

    <br/>

- ### 예외 처리문

  - #### throw, try...catch, finally문

    ```javascript
    try {
      throw "myException" // generates an exception
    }
    catch (e) {
      // statements to handle any exceptions
      logMyErrors(e) // pass exception object to error handler
    } finally {
        console.log("finish");
    }
    ```

  <br/>

- ### Promises ★★★★★

  - ECMAScript2015를 시작하면서, 자바스크립트는 지연된 흐름과 비동기식의 연산을 제어할 수 있게 하는 [`Promise`](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Promise) 객체에 대한 지원을 얻게 되었음

  <br/>

  - #### Promise 상태의 종류

    - *pending* : 초기상태, fulfilled 되거나 rejected 되지 않음
    - *fulfilled* : 연산 수행 성공
    - *rejected* : 연산 수행 실패
    - *settled* : Promise 가 fulfilled 이거나 rejected 이지만 pending 은 아님

    <br/>

    ![img](https://mdn.mozillademos.org/files/8633/promises.png)
  
  - 강의 링크
    
    - https://joshua1988.github.io/web-development/javascript/promise-for-beginners/

<br/>

- ### 문자열 처리

  - 문자와 문자열은 같은 자료형으로, 모두 문자열임

    ```javascript
    typeof "abc";
    typeof "a";
    typeof 'a';
    
    "ab:cd".split(":");
    "ab:cd".replace(":", "$");
    "  abcde  ".trim();
    ```

<br/>

- ### 함수

  - JavaScript에서 별도의 return문을 작성하지 않는다면 함수의 기본 반환값은 **undefined**임

    ```javascript
    function foo() {
    	// statements;
    }
    ```

  - #### 함수표현식

    - 어떤 코드 안에서 함수 기본선언식 또는 함수표현식 둘 중 하나만 정해서 사용하는 것이 좋음

    ```javascript
    var foo = function() {
    	// statements;
    }
    ```

  - #### arguments 객체

    - 함수가 실행되었을 때 그 안에 생성되는 특별한 지역변수

    ```javascript
    function foo() {
    	console.log(arguments);
    }
    
    foo(1, 2, 3);
    ```

  - #### arrow 함수

    ```javascript
    function getName(name) {
       return "Kim " + name ;
    }
    
    //위 함수는 아래 arrow함수와 같다.
    var getName = (name) => "Kim " + name;
    ```

  - #### 콜백 함수

    - 함수 내에서 어떤 특정한 시점에 호출되는 함수

    - 어떤 이벤트에 의해 호출되는 함수

    - 다른 함수의 인자로서 이용되는 함수

    - 비동기 처리 : 특정 코드의 연산이 끝날 때까지 멈춰있지 않고 다음 코드를 먼저 실행하는 JavaScript의 특성

    - ##### 일반적인 함수

      ```javascript
      function plus(a, b) {
      	var sum = a + b;
      		return sum;
      }
      
      function print(result) {
      	console.log(result);
      }
      
      print(plus(1,2));
      ```

    - ##### 콜백 함수

      ```javascript
      function plus(a, b, callback) {
      	var sum = a + b;
      	callback(sum);
      }
      
      plus(1, 2, function(result) {
      	console.log(result);
      })
      ```

      ex)

      ```javascript
      function run() {
          console.log("start");
          setTimeout(function() {
              var msg = "hello codesquad";
              console.log(msg);  //이 메시지는 즉시 실행되지 않습니다.
          }, 1000);
          console.log("end");
      }
      
      run();
      ```

      ```
      start
      end
      hello codesquad
      ```

      

    - #### 콜백 함수를 쓰는 이유

      - 함수의 과정이 끝나기 전에 다음 프로세스를 진행해야 하는 경우가 있을 때, 해당 함수의 과정이 끝날 때까지 모든 작업이 멈춰버리는 현상이 발생할 수 있기 때문
      
        <br/>
    
    - #### 기본적인 콜백 함수들
    
      - setInterval : 특정 동작을 반복적으로 수행하기 위해 쓰임
      - setTimeout : 특정 동작을 다른 동작이 끝난 후 처리하기 위해 쓰임

<br/>

---



# DOM(Document Object Model)

<br/>

![img](https://cphinf.pstatic.net/mooc/20180126_280/1516956194218XFPk5_PNG/2-2-2_Dom_tree.png)

<br/>

- ### DOM이란?

  - HTML 코드를 트리 형태로 구성한 것
  - 웹브라우저에서는 HTML 코드를 DOM이라는 객체 형태의 모델로 저장함
  - 텍스트 파일로 만들어져 있는 웹 문서를 브라우저에 렌더링하려면 웹 문서를 브라우저가 이해할 수 있는 구조로 메모리에 올려야 한다. 브라우저의 렌더링 엔진은 웹 문서를 로드한 후, 파싱하여 웹 문서를 브라우저가 이해할 수 있는 구조로 구성하여 메모리에 적재하는데 이를 DOM이라 한다.
  - 웹브라우저에서는 다양한 DOM API를 제공함

<br/>

- ### 관련 함수들

  - **getElementById()**

    - id를 통해 값을 찾는 함수. 문서 내에 주어진 ID가 없으면 null을 반환
    - 찾으려는 id는 대소문자를 구분하며, 문서 내에서 유일해야 함

    ```html
    <html>
    <head>
      <title>getElementById 예제</title>
    </head>
    <body>
      <p id="para">어떤 글</p>
      <button onclick="changeColor('blue');">blue</button>
      <button onclick="changeColor('red');">red</button>
    </body>
    </html>
    ```

    ```javascript
    function changeColor(newColor) {
      var elem = document.getElementById('para');
      elem.style.color = newColor;
    }
    ```

  - **getElementsByName()**

  - **getElementsByTagName()**

  - **getElementsByClassName()**

    ※ 위 세 함수들은 값을 배열로 반환함

  - **element.querySelector()**

    - CSS 스타일의 선택자를 통해 값을 찾아내는 함수

      ex1)

      ```html
      <div id="app"></div>
      ```

      ```javascript
      document.getElementById("app")
      document.querySelector("#app")
      ```

      ex2)

      ```html
      <div class="container"></div>
      ```

      ```javascript
      document.getElementsByClassName("container")
      document.querySelector(".container")
      ```

  - 기타 함수들
    - `document.createElement(name)`
    - `parentNode.appendChild(node)`
    - `element.innerHTML`
    - `element.style.left`
    - `element.setAttribute`
    - `element.getAttribute`
    - `element.addEventListener`
    - `window.content`
    - `window.onload`
    - `window.dump`
    - `window.scrollTo`

<br/>

# Events

- ### Event란?

  - 키보드 또는 마우스 등을 통하는 특정 행위
  - 화면 크기 조절, 버튼 클릭, 마우스 스크롤 등

<br/>

- ### 자주 쓰이는 Event 목록

  | 이벤트    | 설명                                     |
  | --------- | ---------------------------------------- |
  | change    | 변동이 있을 때 발생                      |
  | click     | 클릭 시 발생                             |
  | focus     | 포커스를 얻었을 때 발생                  |
  | keydown   | 키를 눌렀을 때 발생                      |
  | keyup     | 키에서 손을 뗐을 때 발생                 |
  | load      | 로드가 완료되었을 때 발생                |
  | mousedown | 마우스를 클릭했을 때 발생                |
  | mouseout  | 마우스가 특정 객체 밖으로 나갔을 때 발생 |
  | mouseover | 마우스가 특정 객체 위로 올려졌을 때 발생 |
  | mousemove | 마우스가 움직였을 때 발생                |
  | mouseup   | 마우스에서 손을 뗐을 때 발생             |
  | select    | option 태그 등에서 선택을 했을 때 발생   |

  <br/>

- ### Event 등록 방법

  ```javascript
  // 방법 1
  var el = document.getElementById("myId");
  el.addEventListener("click", btnAlert);
  
  function btnAlert() {
  	alert("메세지");
  }
  ```

  ```javascript
  // 방법 2
  var el = document.getElementById("myId");
  el.addEventListener(function(){
  	alert("메세지");
  })
  
  ```
