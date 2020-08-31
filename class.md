# Class

### prototype static 메소드 및 static 프로퍼티

```javascript
function Person(name, age) {
	this._name = name;
	this._age = age;
}

// static method
Person.getInformations = function(instance) {
	return {
		name: instance._name,
		age: instance._age
	};
}

// (prototype) method
Person.prototype.getName = function() {	
	return this._name;
}

// (prototype) method
Person.prototype.getAge = function() {
	return this._age;
}

var iiaii = new Person('iiaii', 27);

console.log(iiaii.getName());			// iiaii
console.log(iiaii.getAge());			// 27

console.log(iiaii.getInformations(iiaii));	// error

console.log(Person.getnformations(iiaii));	// ... // 성공 
```

- static method : 인스턴스에서는 접근할 수 없는 생성자 함수의 메서드
- (prototype) method : 인스턴스에서 접근할 수 있는 메서드 



---
### class 상속 구현


```javascript
function Person(name, age) {
	this.name = name || '이름 없음';
	this.age = age || '나이 모름';
}

Person.prototype.getName = function() {
	return this.name;
}

Person.prototype.getAge = function() {
	return this.age;
}

function Employee(name, age, position) {
	this.name = name || '이름 없음';
	this.age = age || '나이 모름';
	this.position = position || '직책 모름';
}

Employee.prototype = new Person();
Employee.prototype.constructor = Employee;
Employee.prototype.getPosition = function() {
	return this.position;
}

var iiaii = new Employee('iiaii', 27, 'pro');
console.dir(iiaii);

// 실행 결과
// Employee
// 	age: 27
// 	name: 'iiaii'
//		position: 'pro'
//		> __proto__: Person
// 		age: '나이 모름'				// age 중복됨
//			constructor: f Employee...


// 위의 age, name이 중복되는 것 해결
function Bridge() {
	Bridge.prototype = Person.prototype;
	Employee.prototype = new Bridge();
	Employee.prototype.constructor = Employee;

Employee.prototype.getPosition = function() {	
	return this.position;
}

// 중복없이 잘 출력됨	
```



단순히 프로토타입을 연결하면 프로토타입 값 내에서 중복이 발생하게 된다. 따라서 클래스 상속을 구현할 때 실제로 클로저를 활용한 Bridge 생성자를 사용할 것을 권한다.

```javascript
// es5
// 이 함수를 활용하면 자식 부모 관계를 만들어준다 
var extendClass = (function() {
	function Bridge(){}
	return function(Parent, Child) {
		Bridge.prototype = Parent.prototype;
		Child.prototype = new Bridge();
		Child.prototype.constructor = Child;
		Child.prototype.superClass = Parent;
	}
})();

function Person(name, age) {
	this.name = name || '이름 없음';
	this.age = age || '나이 모름';
}

Person.prototype.getName = function() {
	return this.name;
}

Person.prototype.getAge = function() {
	return this.age;
}

function Employee(name, age, position) {
	this.superClass(name, age);
	this.position = position || '직책 모름';
}

// 상속관계 만들어주기 
extendClass(Person, Employee);
Employee.prototype.getPosition = function() {
	return this.position;
}
```

> 위 예제는 자바스크립트의 클로저, 프로토타입, 실행컨텍스트, 함수 등의 모든 개념이 녹아 있기 때문에 진행된 예제로 ES6부터는 아래와 같은 방식으로 상속을 구현한다. 

```javascript
// es6
class Person {
	constructor(name, age) {
		this.name = name || '이름 없음';
		this.age = age || '나이 모름';
	}
	getName() {
		return this.name;
	}
	getAge() {
		return this.age;
	}
}

class Employee extends Person {
	constructor(name, age, position) {
		super(name, age);
		this.position = position || '직책 모름';
	}
	getPosition() {
		return this.position;
	}
}
```




