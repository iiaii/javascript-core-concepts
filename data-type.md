# 데이터타입

### Primitive Type

값을 그대로 할당

- Number
- String
- Boolean
- null
- undefined
- symbol (es6)

```javascript
let a = 'abc';
const b = a;
```

```
변수명	|	주소
a	|	@314
b	|	@315


주소	|	데이터
314	|	'abc'
315	|	'abc'
```


### Reference Type

값이 저장된 주소 값을 할당 (참조)

- Object
	- Array
	- Function
	- RegExp
	- Map
	- Set
	- …


```javascript
const obj = {
	a: [4, 5, 6]
};
```

```
변수명	|	주소
obj	|	@511


주소	|	데이터
511	|	@1110
...
1110	|	{ a: @1111 }
1111	|	[ @1234, @1235, @1236 ]
...
1234	|	4
1235	|	5
1236	|	6
```

```javascript
obj.a = 'new';
```

```
변수명	|	주소
obj	|	@511


주소	|	데이터
511	|	@1110
...
1110	|	{ a: @1111 }
1111	|	'new'
...
1234	|	4	(GC 대상)
1235	|	5	(GC 대상)
1236	|	6	(GC 대상)
```

