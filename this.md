# this

### 전역공간에서의 this

- 브라우저  : window
- node.js : global

> 전역 객체를 나타낸다
 
 
 
### 함수내부에서의 this

- 브라우저  : window
- node.js : global

> 전역 객체를 나타낸다 (바뀔 수 있음)
 
 
 
### 메소드 호출시의 this

```javascript
var a = {
	b: function() {
		console.log(this);
	}
}
a.b();	// Object {b: function} (a 객체)
```

```javascript
var a = {
	b: {
		c: function() {
			console.log(this);
		}
	}
}
a.b.c();	// Object {c: function} (b 객체)
```


##### 우회방법

```javascript
var a = 10;
var obj = {
	a: 20,
	b: function() {
		console.log(this.a);		// 20 (메서드)

		function c() {
			console.log(this.a);	// 10 (함수)
		}
		c();
	}
}
obj.b();
```

```javascript
var a = 10;
var obj = {
	a: 20,
	b: function() {
		var self = this;
		console.log(this.a);		// 20 

		function c() {
			console.log(self.a);	// 20
		}
		c();
	}
}
obj.b();
```
 
> 메서드 호출 주체 (메소드명 앞)

(함수는 전역객체의 메소드라고 생각하면 편함 (실제로는 아님))

 
 
### callback에서의 this

기본적으로는 함수내부에서와 동일 

##### call, apply, bind 메서드

- `func.call(thisArg[, arg1[, arg2[, ...]]])` (즉시 호출)
- `func.apply(thisArg, [argsArray])` (call과 배열인것만 다름, 즉시 호출)
- `func.bind(thisArg[, arg1[, arg2[, ...]]])` (새로운 함수 생성, currying)

```javascript
function a(x, y, z) {
	console.log(this, x, y, z);
}
var b = {
	c: 'eee'
};

a.call(b, 1, 2, 3);			// 즉시 호출

a.apply(b, [1, 2, 3]);		// 즉시 호출

var c = a.bind(b);			// 새로운 함수 생성 
c(1, 2, 3);

var d = a.bind(b, 1, 2);		// 새로운 함수 생성 
d(3);

// 실행 결과
// Object {c: "eee"} 1 2 3
// Object {c: "eee"} 1 2 3
// Object {c: "eee"} 1 2 3
// Object {c: "eee"} 1 2 3
```
 
##### callback, this

```javascript
var callback = function() {
	console.dir(this);
};
var obj = {
	a: 1,
	b: function(cb) {
		cb();				// window
		cb.call(this);		// obj 
	}
};
obj.b(callback);
```

```javascript
var callback = function() {
	console.dir(this);		
};
var obj = {
	a: 1
};
setTimeout(callback.bind(obj), 100);

// Object {a: 1}
```

> 기본적으로는 함수의 this와 같지만 제어권을 가진 함수가 callback의 this를 명시한 경우에는 명시한 것을 따른다 (개발자가 this를 바인딩한 채로 callback을 넘기면 그에 따름)
 
 
### 생성자 함수에서의 this

```javascript
function Person(n, a) {
	this.name = n;
	this.age = a;
}
var vv = new Person('vv', 22);
console.log(vv);
```

> 인스턴스를 가리킴
 
 
 
---
인프런 Javascript 핵심 개념 알아보기 (JS Flow)


