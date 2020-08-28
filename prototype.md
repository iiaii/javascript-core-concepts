# #자바스크립트/Prototype
### prototype, constructor, __proto__

![prototype1](https://github.com/iiaii/javascript-core-concepts/blob/master/images/prototype1.png?raw=true)

`__proto__`는 생략이 가능해서 마치 자신의 메서드, 프로퍼티 처럼 활용 가능하다

![prototype2](https://github.com/iiaii/javascript-core-concepts/blob/master/images/prototype2.png?raw=true)



```javascript
function Person(n, a) {
	this.name = n;
	this.age = a;
}

var iiaii = new Person('iiaii', 27);
var clone1 = new iiaii.__proto__.constructor('clone1',1);
var clone2 = new iiaii.constructor('clone2',2);
var proto = Object.getPrototypeOf(iiaii);
var clone3 = new proto.constructor('clone3', 3);
var clone4 = new Person.prototype.constructor('clone4', 4);
```

##### 프로토타입에 접근하는 4가지 방법

- `[CONSTRUCTOR].prototype`
- `[instance].__proto__`
- `[instance]`
- `Object.getPrototypeOf([instance])`

##### 생성자(constructor)에 접근하는 5가지 방법

- `[CONSTRUCTOR]`
- `[CONSTRUCTOR].prototype.constructor`
- `(Object.getPrototypeOf([instance])).constructor`
- `[instance].__proto__.constructor`
- `[instance].constructor`



---
### 메소드 상속 및 동작 원리

```javascript
// 프로토타입에 중복되는 메서드 주입
function Person(n, a) {
	this.name = n;
	this.age = a;
}

Person.prototype.setOlder = function() {
	this.age += 1;
}

Person.prototype.getAge = function() {
	return this.age;
}

var iiaii = new Person('iiaii', 27);
var vvavv = new Person('vvavv', 27);
```

```
// 실행 결과
iiaii.__proto__.setOlder();
iiaii.__proto__.getAge();	// NaN 
// prototype은 iiaii.__proto__를 가리키기 때문
// 만약 Person.prototype.age = 100 이 있었다면 결과는 101

iiaii.setOlder();
iiaii.getAge();	// 28
```



---
### prototype chaining


![prototype chaining](https://github.com/iiaii/javascript-core-concepts/blob/master/images/prototype-chaining.png?raw=true)

Array는 Object의 `__proto__`를 마치 자신의 것처럼 사용 가능한데, 상속을 흉내낸것 같은 이것을 프로토타입 체이닝(prototype chaining)이라고 한다.


```javascript
Object.assign();
Object.freeze();
Object.create();
Object.values();
Object.keys();
```

> Object는 원시타입을 제외한 모든 타입에서 사용하기 때문에 Object만을 위한 메서드를 생성자 함수 안에 정의해 놓았다. 



```javascript
var arr = [1, 2, 3];
console.log(arr.toString());	// 1, 2, 3

arr.toString = function() {	// 덮어쓰기
	return this.join('_');
}

console.log(arr.toString());	// 1_2_3

console.log(arr.__proto__.toString.call(arr));	// 1, 2, 3

console.log(arr.__proto__.__proto__.toString.call(arr));	// [object Array]

Array.prototype.toString = function() {	// 덮어쓰기
	return '['+this.join(', ')+']';
}

console.log(arr.toString());	// 1_2_3

console.log(arr.__proto__.toString.call(arr));	// 1, 2, 3

console.log(arr.__proto__.__proto__.toString.call(arr));	// [object Array]
```





