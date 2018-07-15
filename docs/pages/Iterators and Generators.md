# Iterables

An object is deemed iterable if it has an implementation for the [`Symbol.iterator`](Symbols.md#symboliterator) property.
Some built-in types like `Array`, `Map`, `Set`, `String`, `Int32Array`, `Uint32Array`, etc. have their `Symbol.iterator` property already implemented.
`Symbol.iterator` function on an object is responsible for returning the list of values to iterate on.

[`Symbol.iterator`](Symbols.md#symboliterator) 속성에 대한 구현을 가진 객체라면 iterable 한것으로 간주한다.
몇몇 `Array`, `Map`, `Set`, `String`, `Int32Array`, `Uint32Array`, 등과 같이 것은 이미 `Symbol.iterator` 속성이 구현되어 가지고 있다.  
객체의 `Symbol.iterator` 힘수는 iterabte 되어지는 값의 리스트를 돌려주는 역할을 맡고 있다.

## `for..of` statements(구문)

`for..of` loops over an iterable object, invoking the `Symbol.iterator` property on the object.
Here is a simple `for..of` loop on an array:

iterable 객체에 대한 `for..of` loops 는 , 객체의 `Symbol.iterator` 속성에 의해서 일어난다.
여기 배열에 관한 `for..of` loop 예시이다:

```ts
let someArray = [1, 'string', false];

for (let entry of someArray) {
  console.log(entry); // 1, "string", false
}
```

### `for..of` vs. `for..in` statements (구문)

Both `for..of` and `for..in` statements iterate over lists; the values iterated on are different though, `for..in` returns a list of _keys_ on the object being iterated, whereas `for..of` returns a list of _values_ of the numeric properties of the object being iterated.

Here is an example that demonstrates this distinction:

`for..of` 그리고 `for..in` 구문은 리스트들에 대해서 iterate(순회)한다. `for..in` 은 객체의 *keys*을 list 를 순회하여 리턴하지만 , `for..of` 은 객체에서 순회되는 산술적인 속성인 *values*에 대한 리스트를 리턴한다.

이것에 대한 뚜렷한 차이를 나타내는 예시이다.

```ts
let list = [4, 5, 6];

for (let i in list) {
  console.log(i); // "0", "1", "2",
}

for (let i of list) {
  console.log(i); // "4", "5", "6"
}
```

Another distinction is that `for..in` operates on any object; it serves as a way to inspect properties on this object.
`for..of` on the other hand, is mainly interested in values of iterable objects. Built-in objects like `Map` and `Set` implement `Symbol.iterator` property allowing access to stored values.

`for..in`의 뚜렷한 차이는 객체에서 동작한다: 이것은 객체에 대한 속성들을 보는 탐색하는 방법을 제공한다.
다른 한편으로는 `for..of`은 주로 iterable objects 의 값들에 대한 것에 관심을 둔다. `Symbol.iterator` 속성이 구현된 `Map` 그리고 `Set` 과 같은 내장 객체들은 저장된 값을 접근하는데 쓰인다.

```ts
let pets = new Set(['Cat', 'Dog', 'Hamster']);
pets['species'] = 'mammals';

for (let pet in pets) {
  console.log(pet); // "species"
}

for (let pet of pets) {
  console.log(pet); // "Cat", "Dog", "Hamster"
}
```

### Code generation

#### Targeting ES5 and ES3

When targeting an ES5 or ES3, iterators are only allowed on values of `Array` type.
It is an error to use `for..of` loops on non-Array values, even if these non-Array values implement the `Symbol.iterator` property.

The compiler will generate a simple `for` loop for a `for..of` loop, for instance:

ES5 또는 ES3 를 목표로 할때, iterators 는 오로지 `Array` 타입의 값만 허용된다.
배열링 아닌 값을 `for..of` loop 를 사용하는것이 에러이고, `Symbol.iterator`속성 을 구현한 배열이 아닌 것들의 값도 에러이다.

예를들면, 컴파일러는 `for..of`를 `for` loop로 생성한다.

```ts
let numbers = [1, 2, 3];
for (let num of numbers) {
  console.log(num);
}
```

will be generated as:

```js
var numbers = [1, 2, 3];
for (var _i = 0; _i < numbers.length; _i++) {
  var num = numbers[_i];
  console.log(num);
}
```

#### Targeting ECMAScript 2015 and higher

When targeting an ECMAScipt 2015-compliant engine, the compiler will generate `for..of` loops to target the built-in iterator implementation in the engine.
ECMAScipt 2015-compliant 엔진을 목표로 한다면, 엔진에서 컴파일러는 내장 iterator 구현 `for..of`loop 에서 발생할것이다.
