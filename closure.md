# 클로저

`클로저는 함수와 함수가 선언된 어휘적 환경의 조합이다. (MDN)`

lexical environment (선언된 어휘적 환경) : 선언 당시의 환경에 대한 정보를 담는 객체 (구성 환경, 스코프와 밀접)

> 다시 정리하자면, 함수 내부에서 생성한 데이터와 그 유효범위로 인해 발생하는 특수한 현상 / 상태

예를 들어 함수를 선언했을때 scope와 lexical environment는 변하지 않는다.
즉, 최초 선언시의 정보를 유지한다. 
결국 클로저란 컨텍스트 A에서 선언한 변수 a를 참조하는 내부함수 B를 A의 외부로 전달할 경우, A가 종료된 이후에도 a가 사라지지 않는 현상을 말한다.

 
##### 클로저를 사용하는 이유
 
**클로저를 활용하면 지역변수가 함수 종료후에도 사라지지 않게 할 수 있다.** 또한 다음을 가능하게 한다.
 
- 접근 권한 제어
- 지역변수 보호
- 데이터 보존 및 활용

```javascript
// 외부에서 함수 내부 프로퍼티를 출력
function a() {
	var x = 1;
	return function b() {
		console.log(x);
	}
}
var c = a();
c();
```

```javascript
// 외부에서 함수 내부 프로퍼티 접근 및 수정 가능
function a() {
	var _x = 1;
	var _y = 2;
	return {
		get x() { return _x; },
		set x(v) { _x = v; }
		get y() { return y; },
		set y(v) { throw Error('read only'); }
	}
}
var c = a();
console.log(c.x, c.y);	// 1, 2
c.x = 10;		// 변경 가능
c.y = 5;		// Error: read only
```


##### 클로저 흐름

```javascript
function setName(name) {
	return funciton() {
		return name;
	}
}
var sayMyName = setName('iiaii');
sayMyName();
```

1. 전역 실행컨텍스트 생성 [Global]
	1. 함수 setName 선언 [Global > setName]
	2. 변수 sayMyName 선언
	3. setName(‘iiaii’) 호출 - > setName 실행컨텍스트 생성
		1. 지역변수 name 선언 및 ‘iiaii’ 할당
		2. 익명함수 선언 [Global > setName > unnamed]
		3. 익명함수 반환
	4. setName 실행컨텍스트 종료
	5. 변수 sayMyName에 반환된 함수를 할당
	6. sayMyName 호출 -> sayMyName 실행컨텍스트 생성
		1. unnamed scope 에서 name 탐색 -> setName에서 name 탐색 -> ‘iiaii’ 반환
	7. sayMyName 실행컨텍스트 종료
2. 전역컨텍스트 종료

> 스코프는 정의될 때 결정된다
 

##### 클로저를 활용한 카운터

```javascript
var counter = function() {
  var count = 0;
  function changeCount(number) {
    count += number;
  }
  return {
    increase: function() {
      changeCount(1);
    },
    decrease: function() {
      changeCount(-1);
    },
    show: function() {
      alert(count);
    }
  }
};
var counterClosure = counter();
counterClosure.increase();
counterClosure.show(); // 1
counterClosure.decrease();
counterClosure.show(); // 0
```


> 이처럼 사용자가 제어할 수 없는 비공개 변수를 만들어 활용할 수 있다. 즉, 클로저를 사용해 사용자가 할 수 있는 행동을 통제한다. (자바의 private 변수처럼)


##### 주의점

```javascript
// alert 값이 모두 5 (에러!)
for (var i = 0; i < 5; i++) {
  $('#target' + i).on('click', function() {
    alert(i);
  });
}
```

> 위 결과는 alert 값이 모두 5가 나오게 된다.

컨텍스트에 대한 이해가 부족해서 생기는 문제인데, lexical scoping에 따라 함수는 선언할 때 스코프가 생성된다. 따라서 이벤트 리스너 안의 i는 외부의 i를 계속 참조하게 되는데, i는 반복문 종료후 5가 되기 때문에 모두 5가 된다.

```javascript
// 클로저를 활용하여 해결
for (var i = 0; i < 5; i++) {
  (function(j) {
    $('#target' + j).on('click', function() {
      alert(j);
    });
  })(i);
}
```


---
### 클로저로 지역변수 만들기

```javascript
// 자바의 private 처럼 접근이 불가하도록 구성
var createCar = function(f, p) {
	var fuel = f;
	var power = p;
	var total = 0;
	return {
		run: function(km) {
			var wasteFuel = km / power;
			if(fuel < wasteFuel) {
				console.log('이동 불가');
				return;
			}
			fuel -= wasteFuel;
			total += km;
		}
	}
};
```

1. 함수에서 지역변수 및 내부함수 등을 생성
2. 외부에 노출시키고자 하는 멤버들로 구성된 객체를 return 한다

- return한 객체에 포함되지 않은 멤버들은 private하다
- return한 객체에 포함된 멤버들은 public하다

