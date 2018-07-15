# Symbol 소개

Starting with ECMAScript 2015, `symbol` is a primitive data type, just like `number` and `string`.

ECMASript 2015에서 시작된 `symbol`은 `number`와 `string` 같은 원시 데이터 타입입니다.

`symbol` values are created by calling the `Symbol` constructor.

`symbol` 값은 `Symbol` 생성자로 생성합니다.

```ts
let sym1 = Symbol();

let sym2 = Symbol("key"); // optional string key
```

Symbols are immutable, and unique.

Symbols는 불변이고 유일합니다.

```ts
let sym2 = Symbol("key");
let sym3 = Symbol("key");

sym2 === sym3; // false, symbols are unique
```

Just like strings, symbols can be used as keys for object properties.

symbols에서 string을 객체 속성값으로 사용할 수도 있습니다.

```ts
let sym = Symbol();

let obj = {
    [sym]: "value"
};

console.log(obj[sym]); // "value"
```

Symbols can also be combined with computed property declarations to declare object properties and class members.

Symbols는 또한 객체 특성 및 class 구성을 계산된 특성선언과 결합하여 선언 할 수 있습니다.

```ts
const getClassNameSymbol = Symbol();

class C {
    [getClassNameSymbol](){
       return "C";
    }
}

let c = new C();
let className = c[getClassNameSymbol](); // "C"
```

# 잘알려진 Symbols

In addition to user-defined symbols, there are well-known built-in symbols.
Built-in symbols are used to represent internal language behaviors.

사용자 정의 Symbols 이외에도 잘 알려진 내장 symbols가 있습니다.
내장된 Symbols는 내부 언어 동작을 나타내는데 사용됩니다.

Here is a list of well-known symbols:

여기 잘 알려진 symbols 목록입니다.

## `Symbol.hasInstance`

A method that determines if a constructor object recognizes an object as one of the constructor’s instances. Called by the semantics of the instanceof operator.

생성자 객체가 생성자의 instance 중 하나로써 객체를 인식하는 여부를 결정하는 메소드입니다. instanceof 연산자에 의해 호출됩니다.

## `Symbol.isConcatSpreadable`

A Boolean value indicating that an object should be flattened to its array elements by `Array.prototype.concat`.

`Array.prototype.concat`에 의해 객체를 배열 요소로 병합할 때, 객체를 펼침 유무를 나타내는 boolean 값입니다.
>번역자 노트: ['a', 'b']와 ['c', 'd']를 합칠 때 false이면 ['a', 'b', ['c', 'd']]가 되고 true면 ['a, 'b', 'c', 'd']가 됩니다. 기본값은 true입니다.

## `Symbol.iterator`

A method that returns the default iterator for an object. Called by the semantics of the for-of statement.

객체의 기본 iterator를 변환하는 메소드입니다. for-of 문의 의미에 의해 실행됩니다.

## `Symbol.match`

A regular expression method that matches the regular expression against a string. Called by the `String.prototype.match` method.

정규식을 문자열과 비교하는 정규 표현식 메소드입니다. `String.protoype.match`에 의해 호출됩니다.

## `Symbol.replace`

A regular expression method that replaces matched substrings of a string. Called by the `String.prototype.replace` method.

문자열 일부분을 일치하는 문자열로 대처하는 정규 표현식 메소드입니다. `String.prototype.replace`에 의해 호출됩니다.

## `Symbol.search`

A regular expression method that returns the index within a string that matches the regular expression. Called by the `String.prototype.search` method.

문자열안에 정규식에 일치하는 위치를 반환하는 정규 표현식 메소드 입니다. `String.prototype.search`에 의해 호출됨니다.

## `Symbol.species`

A function valued property that is the constructor function that is used to create derived objects.

생성자 함수의 속성값으로 파생된 객체를 생성할때 사용됨니다. 

## `Symbol.split`

A regular expression method that splits a string at the indices that match the regular expression.
Called by the `String.prototype.split` method.

문자열 안에 정규식에 일치하는 위치를 반환하는 정규 표현식 메소드입니다. `String.prototype.search`에 의해 호출됩니다.

## `Symbol.toPrimitive`

A method that converts an object to a corresponding primitive value.
Called by the `ToPrimitive` abstract operation.

원시값에 대응하는 객체로 변환하는 메소드입니다. `ToPrimitive` 추상 동작에 의해 호출됩니다.

## `Symbol.toStringTag`

A String value that is used in the creation of the default string description of an object.
Called by the built-in method `Object.prototype.toString`.

객체의 기본 설명문을 만드는데 이용되는 String 값입니다. `Object.prototype.toString` 내장 메소드에 의해 호출됩니다.

## `Symbol.unscopables`

An Object whose own property names are property names that are excluded from the 'with' environment bindings of the associated objects.

속성 이름으로 객체의 속성값을 연합된 객체의 'with' 환경 바인딩에서부터 제외합니다.
