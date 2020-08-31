# 함수

### 호이스팅

변수 선언과 함수 선언을 코드의 최상단으로 끌어올린다. (할당은 제외)
호이스팅은 실재하는 현상은 아니고 lexicalEnvironment의 environmentRecord(현재 문맥의 식별자 정보)가 하는 정보 수집 과정을 쉽게 이해하기 위해 만들어낸 허구의 개념이다.



```javascript
// 호이스팅 전

console.log(a());
console.log(a());
console.log(a());

function a() {
	return 'a';
}

const b = function bb() {
	return 'bb';
}

const c = function() {
	return 'c';
}
```


```javascript
// 호이스팅 후
function a() {	return 'a'; }
const b;
const c;

console.log(a());
console.log(a());
console.log(a());

b = function bb() {
	return 'bb';
}

c = function() {
	return 'c';
}
```

- environmentRecord 

현재 전역 컨텍스트의 lexicalEnvironment의 environmentRecord가 선언되어 있는 식별자를 수집하는 것으로 실재하는 현상이다. 
environmentRecord가 수집하는 것은 실재하는 현상이며 이를 호이스팅이라고 부르고 이해하는 것이다. 

```
// environmentRecord가 수집한 식별자들
{
   function a() { ... },
   b,
   c
}
```
 
 
 
---
### 함수선언문과 함수표현식

- 함수선언문 (function declaration) (잘 안쓰임)

```javascript
function a() {
	return 'a';
}
```

- 기명 함수 표현식 (named function expression) (잘 안쓰임)

```javascript
const b = function bb() {
	return 'bb';
}
```

- (익명) 함수 표현식 (unnamed/annonymous function expression) (많이 쓰임)

```javascript
const c = function() {
	return 'c';
}
```

> 호이스팅에서도 안전하고 예측 가능하도록 (익명) 함수 표현식을 사용해야 한다



---
### 함수스코프, 실행컨텍스트

##### 스코프 

- 유효 범위 (변수)
- 정의될 때 결정된다

##### 실행컨텍스트

실행컨텍스트는 함수를 실행할 때 필요한 환경정보를 담은 객체이다. 실행컨텍스트에는 3가지가 존재한다.

- VariableEnvironment 

초기 정보의 스냅샷으로 lexicalEnvironment와 같은 것의 스냅샷을 가진다.

- LexicalEnvironment 

> environmentRecord = 현재 문맥의 식별자 정보 = 호이스팅

environmentRecord는 선언된 식별자(변수, 함수)를 수집한다. 호이스팅은 environmentRecord의 수집을 끌어올린다는 용어로 이해를 돕기위한 개념이지 실제로는 일어나지는 않는다.


> outerEnvironmentReference = 외부 문맥의 식별자 정보 = 스코프. 

외부에서 내부를 그냥 참조할 수 없고, 내부 컨텍스트에서는 식별자가 없으면 외부컨텍스트는 자동으로 찾는다. (이것이 스코프 체인)
최종 외부 컨텍스트는 전역 컨텍스트이다.

- ThisBinding 


```
'전역 컨텍스트': {
  변수객체: {
    arguments: null,
    variable: ['name', 'wow', 'say'],
  },
  scopeChain: ['전역 변수객체'],
  this: window,
}

'wow 컨텍스트': {
  변수객체: {
    arguments: [{ word : 'hello' }],
    variable: null,
  },
  scopeChain: ['wow 변수객체', '전역 변수객체'],
  this: window,
}
```

실행 컨텍스트의 특징은 

- 실행되는 코드덩어리 (추상적 개념)
- 실행될때 생성된다
- 호이스팅, this 바인딩 등의 정보가 담긴다 (변수객체(arguments, variable), scope chain, this …)

```javascript
var a = 1;
function outer() {
	console.log(a);			// ㄱ

	function inner() {
		console.log(a);		// ㄴ
		var a = 3;
	}

	inner();

	console.log(a);			// ㄷ
}
outer();
console.log(a);			// ㄹ

// 실행 결과 (순서 - 결과)
// ㄱ - 1
// ㄴ - undefined
// ㄷ - 1
// ㄹ - 1
```
 
1. 전역 실행컨텍스트 생성 [Global]
	1. 변수 a 선언
	2. 함수 outer 선언 [Global > outer]
	3. 변수 a에 1 할당
	4. outer 함수 호출 -> outer 실행컨텍스트 생성 [Global > outer]
		1. 함수 inner 선언 [Global > outer > inner]
		2. outer scope에서 a 탐색 -> global scope에서 a 탐색 -> 1 출력
		3. inner 함수 호출 -> inner 실행컨텍스트 생성 [Global > outer > inner]
			1. 변수 a 선언
			2. inner scope에서 a 탐색 -> undefined 출력
			3. 변수 a에 3할당
		4. inner 실행컨텍스트 종료
		5. outer scope에서 a 탐색 -> global scope에서 a 탐색 -> 1 출력
	5. outer 실행컨텍스트 종료
	6. global scope에서 a 탐색 -> 1 출력
2. 전역 실행컨텍스트 종료 

 
 
---
### 메서드

```javascript
var obj = {
	a: 1,
	b: function bb() {
		console.log(this);
	},
	c: function() {
		console.log(this.a);
	}
};

obj.b();	// 메서드, this에 obj 바인딩
obj.c();	// 메서드, this에 obj 바인딩

console.dir(obj.b);
console.dir(obj.c);
```

1. 전역 실행컨텍스트 생성 [Global]
	1. 변수 obj 선언
	2. 객체 생성 / 변수 obj에 객체 주소값 할당
	3. obj.b 메서드 호출 -> obj.b 실행컨텍스트 생성
		1. this에 obj 바인딩
		2. this(obj) 출력
	4. obj.b 실행컨텍스트 종료
	5. …
2. 전역 실행 컨텍스트 종료 
 
 
 
---
### 콜백함수


함수를 호출해서 그 결과를 돌려주는 것. 즉, 콜백함수는 제어권을 대상에게 넘겨준다. (개념적인 것이지 실제로 제어권을 맡기지는 않는다. 자바스크립트 런타임은 싱글스레드임!)

> `setInterval(callback, milliseconds);`

```javascript
setInterval(function() {
	console.log('1초마다 실행된다');
}, 1000);
```

> `arr.forEach(callback[, thisArg]);` (thisArg : this로 인식할 대상)

```javascript
// forEach의 콜백은 (value, index) 순서
arr.forEach(function(v, i) {
	entries.push([i, v, this[i]]);
}, [10, 20, 30, 40, 50]);
```

> target.addEventListener(type, callback[, options]);

```java
document.getElementById('a)
	.addEventListener('click', cbFunc);

// <div id="a">abc</div>
// > MouseEvent {isTrusted: true, screenX: 11,
```

- 다른 함수 (A)의 매개변수로 콜백함수 (B)를 전달하면, A가 B의 제어권을 갖게 된다
- 특별한 요청(bind)이 없는한 A에 미리 정해진 방식에 따라 B를 호출한다
(미리 정해진 방식이란, this에 무엇을 바인딩할지, 매개변수에는 어떤 값들을 지정할지, 어떤 타이밍에 콜백을 호출할지 등이다)

##### 주의점

콜백함수는 함수다

```javascript
var arr = [1, 2, 3, 4, 5];
var obj = {
	vals: [1, 2, 3],
	logValues: function(v, i) {
		if(this.vals) {
			console.log(this.vals, v, i);
		} else {
			console.log(this, v, i);
		}
	}
};
obj.logValues(1, 2);			// 메서드 호출 : if문 실행
arr.forEach(obj.logValues);	// 콜백 호출 : else문 실행
```
 
 
 
---
- 인프런 Javascript 핵심 개념 알아보기 (JS Flow)
- [실행 컨텍스트](https://www.zerocho.com/category/Javascript/post/5741d96d094da4986bc950a0)


