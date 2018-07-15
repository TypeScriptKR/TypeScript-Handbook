# 변수 선언

`let` and `const` are two relatively new types of variable declarations in JavaScript.
As we mentioned earlier, `let` is similar to `var` in some respects, but allows users to avoid some of the common "gotchas" that users run into in JavaScript.
`const` is an augmentation of `let` in that it prevents re-assignment to a variable.

`let` 과 `const`는 JavaScript에서 두 가지 비교적 새로운 유형의 변수 선언 방식입니다.
이전에 언급했듯이, `let`은 `var`와 비교적 유사하지만, JavaScript에서 사용자들이 주로 하는 실수를 피할 수 있습니다. 
`const`는 변수에 재할당을 막았다는 점에서 `let`을 확장한 것 입니다.

With TypeScript being a superset of JavaScript, the language naturally supports `let` and `const`.
Here we'll elaborate more on these new declarations and why they're preferable to `var`.

JavaScript에서 시작된 TypeScript 슈퍼 셋은 자연스럽게 언어에서 `let`과 `const`을 지원합니다. 
여기서 새로운 선언 방식에 설명할 것이고 왜 `var`보다 나은지 설명할 것입니다.

If you've used JavaScript offhandedly, the next section might be a good way to refresh your memory.
If you're intimately familiar with all the quirks of `var` declarations in JavaScript, you might find it easier to skip ahead.

JavaScript를 아무렇게나 사용했다면 다음 부문은 메모리를 새롭게 고치는 좋은 방법이 될 것입니다. 
JavaScript에서 `var`의 단점을 잘 알고 있으면 더 쉽게 넘어갈 수 있습니다.

# `var` 선언

Declaring a variable in JavaScript has always traditionally been done with the `var` keyword.

JavaScript에서 전통적으로 변수를 `var` 키워드로 선언하였습니다.

```ts
var a = 10;
```

As you might've figured out, we just declared a variable named `a` with the value `10`.

바로 아시겠지만 그냥 `10`이라는 값을 가진 `a`라는 변수를 선언하였습니다. 

We can also declare a variable inside of a function:

또한, 변수를 함수 안에서 선언 할 수 있습니다.

```ts
function f() {
    var message = "Hello, world!";

    return message;
}
```

and we can also access those same variables within other functions:

그리고 같은 변수를 다른 내부 함수에서 또한 접근 할 수 있습니다.

```ts
function f() {
    var a = 10;
    return function g() {
        var b = a + 1;
        return b;
    }
}

var g = f();
g(); // returns '11'
```

In this above example, `g` captured the variable `a` declared in `f`.
At any point that `g` gets called, the value of `a` will be tied to the value of `a` in `f`.
Even if `g` is called once `f` is done running, it will be able to access and modify `a`.

위 예제에서는 `f` 안에서 선언된 `a`라는 변수를 `g`가 사용했습니다.
`g`가 호출 될 때 마다 `a`는 `f`의 `a` 값에 연결됩니다.
`f`가 호출되면 `g`가 호출되더라도 `a`에 접근하여 수정할 수 있습니다.

```ts
function f() {
    var a = 1;

    a = 2;
    var b = g();
    a = 3;

    return b;

    function g() {
        return a;
    }
}

f(); // returns '2'
```

## Scoping rules

`var` declarations have some odd scoping rules for those used to other languages.
Take the following example:

`var` 선언은 다른 언어에서 사용되는 것보다 오래된 범위 규칙을 가지고 있습니다.
예제를 따라 해 봅시다.

```ts
function f(shouldInitialize: boolean) {
    if (shouldInitialize) {
        var x = 10;
    }

    return x;
}

f(true);  // returns '10'
f(false); // returns 'undefined'
```

Some readers might do a double-take at this example.
The variable `x` was declared *within the `if` block*, and yet we were able to access it from outside that block.
That's because `var` declarations are accessible anywhere within their containing function, module, namespace, or global scope - all which we'll go over later on - regardless of the containing block.
Some people call this *`var`-scoping* or *function-scoping*.
Parameters are also function scoped.

몇 독자는 이 예제에서 재확인할 것입니다.
변수 `x`는 *`if`문 안*에서 선언되었습니다. 그리고 if 문밖에서 접근할 수 있습니다.
`var`는 함수, 모듈, namespace, 전역(선언된 블록 위치 관계없이)에서 접근 가능한 선언이기 때문입니다.
이것을 *`var`-scoping* 또는 *function-scoping*이라고 부릅니다.
매개 변수 또한 함수 범위입니다.

These scoping rules can cause several types of mistakes.
One problem they exacerbate is the fact that it is not an error to declare the same variable multiple times:

이 범위 지정 규칙은 여러 실수를 일으킬 수 있습니다.
이 문제가 더 힘들게 하는 것은 같은 변수를 여러 번 선언 하는 것이 오류가 아니라는 것입니다.

```ts
function sumMatrix(matrix: number[][]) {
    var sum = 0;
    for (var i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (var i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }

    return sum;
}
```

Maybe it was easy to spot out for some, but the inner `for`-loop will accidentally overwrite the variable `i` because `i` refers to the same function-scoped variable.
As experienced developers know by now, similar sorts of bugs slip through code reviews and can be an endless source of frustration.

찾아내기 쉬울지도 모르지만, `for`반복문 안의 `i`변수는 같은 함수 범위 안의 `i`를 참조하여 덮어 씁니다.
경험 많은 개발자는 바로 알지만, 비슷한 종류의 버그가 코드리뷰를 통해 계속 나오고 있습니다.

## Variable capturing quirks

Take a quick second to guess what the output of the following snippet is:

잠시 다음 코드의 결과가 어떻게 나올지 추측해보세요.

```ts
for (var i = 0; i < 10; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}
```

For those unfamiliar, `setTimeout` will try to execute a function after a certain number of milliseconds (though waiting for anything else to stop running).

익숙하지 않은 사람들을 위해, `setTimeout`은 함수를 몇 밀리세컨드 뒤에 실행시킵니다. (프로그램이 멈추지 않고 바로 넘어갑니다.)

Ready? Take a look:

준비되었나요? 한번 봅시다.

```text
10
10
10
10
10
10
10
10
10
10
```

Many JavaScript developers are intimately familiar with this behavior, but if you're surprised, you're certainly not alone.
Most people expect the output to be

많은 JavaScript 개발자는 어떻게 결과가 나올지 알고 있지만, 확실히 혼자서만 놀란 것은 아닐 겁니다.
대부분 사람은 다음과 같이 결과가 나오길 기대합니다.

```text
0
1
2
3
4
5
6
7
8
9
```

Remember what we mentioned earlier about variable capturing?
Every function expression we pass to `setTimeout` actually refers to the same `i` from the same scope.

우리가 이전에 변수가 어떻게 묶인다고 언급했는지 기억하시나요?
모든 함수 표현은 `setTimeout`이 사실 같은 `i`를 참조하고 있었습니다.

Let's take a minute to consider what that means.
`setTimeout` will run a function after some number of milliseconds, *but only* after the `for` loop has stopped executing;
By the time the `for` loop has stopped executing, the value of `i` is `10`.
So each time the given function gets called, it will print out `10`!

잠시 그게 어떤 의미인지 들여다봅시다.
`setTimeout`은 함수를 몇 밀리세컨드 뒤에 실행합니다. *그러나 오직* `for`문이 끝난 뒤 실행됩니다.
`for`문이 다 실행되면 `i`의 값은 `10`입니다.
그리고 시간이 지나 함수가 실행되면 `10`이 출력될 것입니다.

A common work around is to use an IIFE - an Immediately Invoked Function Expression - to capture `i` at each iteration:

IIFE(즉시 실행 함수 표현식)을 사용하여 `i`가 각각 반복문에서 일반적으로 사용 할 수 있습니다.

```ts
for (var i = 0; i < 10; i++) {
    // capture the current state of 'i'
    // by invoking a function with its current value
    (function(i) {
        setTimeout(function() { console.log(i); }, 100 * i);
    })(i);
}
```

This odd-looking pattern is actually pretty common.
The `i` in the parameter list actually shadows the `i` declared in the `for` loop, but since we named them the same, we didn't have to modify the loop body too much.

이 이상한 패턴은 사실 꾀흔합니다.
`i` 매개변수는 `for` 반복문 안에서 `i`가 선언되어 그러나 지금까지 같은 이름을 사용 했습니다. 그래서 우리는 반복문을 수정하지 않아도 됩니다.

# `let` 선언

By now you've figured out that `var` has some problems, which is precisely why `let` statements were introduced.
Apart from the keyword used, `let` statements are written the same way `var` statements are.

이제 `var`가 가지고 있는 문제를 알았습니다. 이것은 왜 `let`이 도입되었는지 정확히 설명합니다.
사용된 키워드와 별도로 `let`은 `var`문과 같은 방식으로 작성합니다.

```ts
let hello = "Hello!";
```

The key difference is not in the syntax, but in the semantics, which we'll now dive into.

구문에 차이점이 있는것이 아니라 의미에 있습니다. 이제 내용을 살펴보겠습니다.

## Block-scoping

When a variable is declared using `let`, it uses what some call *lexical-scoping* or *block-scoping*.
Unlike variables declared with `var` whose scopes leak out to their containing function, block-scoped variables are not visible outside of their nearest containing block or `for`-loop.

변수를 `let`으로 선언 하면 *letxical-scoping* 혹은 *block-scoping*이라고 부름니다.
범위 밖에서 사용될수 있는 `var` 선언과 달리 가장 가까운 범위 블록 밖 또눈 `for`문 밖에서 볼수 없습니다.

```ts
function f(input: boolean) {
    let a = 100;

    if (input) {
        // Still okay to reference 'a'
        // 계속 `a`를 참조할 수 있습니다.
        let b = a + 1;
        return b;
    }

    // Error: 'b' doesn't exist here
    // 오류: `b`는 더 이상 존제하지 않습니다.
    return b;
}
```

Here, we have two local variables `a` and `b`.
`a`'s scope is limited to the body of `f` while `b`'s scope is limited to the containing `if` statement's block.

여기 두 로컬변수 `a`와 `b`가 있습니다.
`a`의 범위는 `f`의 안으로 제한 되는 반면에 `b`의 범위는 `if`구문안으로 제한됨니다.

Variables declared in a `catch` clause also have similar scoping rules.

`catch` 에서도 같은 변수 범위 규직을 같이고 있습니다.

```ts
try {
    throw "oh no!";
}
catch (e) {
    console.log("Oh well.");
}

// Error: 'e' doesn't exist here
// 오류 : 'e`는 더 이상 존제하지 않습니다.
console.log(e);
```

Another property of block-scoped variables is that they can't be read or written to before they're actually declared.
While these variables are "present" throughout their scope, all points up until their declaration are part of their *temporal dead zone*.
This is just a sophisticated way of saying you can't access them before the `let` statement, and luckily TypeScript will let you know that.

블록 범위 변수의 또 다른 속성은 실제로 선언되기 전에 읽거나 쓸 수 없다는 점입니다.
이러한 변수가 범위 내에서 "존재"하는 동안 선언은 모두 *temporal dead zone*의 일부가 될 때까지 가리 킵니다.
단지 `let`문 앞에서 접근 할 수 없다는것을 말하는 것이며, 다행이도 TypeScript를 통해 알 수 있습니다.

```ts
a++; // illegal to use 'a' before it's declared; // `a`가 선언되기 전에 사용하느것은 불가능합니다.
let a;
```

Something to note is that you can still *capture* a block-scoped variable before it's declared.
The only catch is that it's illegal to call that function before the declaration.
If targeting ES2015, a modern runtime will throw an error; however, right now TypeScript is permissive and won't report this as an error.

주의해야 할점은 블록 범위 변수가 선언되기전에 여전히 *캡쳐* 할 수 있다는 것입니다.
함수를 호출할 때 변수가 선언되기 전이라면 함수를 호출 할 수 없습니다.
ES2015를 target으로 한다면, 최신 런타임은 오류를 발생시킵니다. 그러나 지금 TypeScript에서는 허용이 되며 오류를 발생시키지 않습니다.

```ts
function foo() {
    // okay to capture 'a'
    return a;
}

// illegal call 'foo' before 'a' is declared
// runtimes should throw an error here
foo();

let a;
```

For more information on temporal dead zones, see relevant content on the [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_dead_zone_and_errors_with_let).

temporal dead zones에 대한 더 자세한 내용은 [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_dead_zone_and_errors_with_let)을 참조하세요.

## 재선언과 Shadowing

With `var` declarations, we mentioned that it didn't matter how many times you declared your variables; you just got one.

`var` 선언으로 변수를 선언한 횟수는 중요하지 않다고 언급했습니다.

```ts
function f(x) {
    var x;
    var x;

    if (true) {
        var x;
    }
}
```

In the above example, all declarations of `x` actually refer to the *same* `x`, and this is perfectly valid.
This often ends up being a source of bugs.
Thankfully, `let` declarations are not as forgiving.

위 예제에서는, `x`의 모든 선언이 사실 *같은* `x`이고 확실히 유효합니다.
이건 종종 버그의 원인이 됩니다.
고맙게도 `let` 선언은 복수 선언을 허용하지 않습니다.

```ts
let x = 10;
let x = 20; // error: can't re-declare 'x' in the same scope
```

The variables don't necessarily need to both be block-scoped for TypeScript to tell us that there's a problem.

TypeScript에 변수가 문제가 있음을 알리기 위해 반드시 블록 범위가 지정 될 필요는 없습니다.

```ts
function f(x) {
    let x = 100; // error: interferes with parameter declaration
}

function g() {
    let x = 100;
    var x = 100; // error: can't have both declarations of 'x'
}
```

That's not to say that block-scoped variable can never be declared with a function-scoped variable.
The block-scoped variable just needs to be declared within a distinctly different block.

블록 범위 변수가 함수 범위의 변수로 절대 선언 될 수 없다는 것은 아닙니다.
블록 범위 변수는 뚜렷하게 다른 블록에서 선언되어야 합니다.

```ts
function f(condition, x) {
    if (condition) {
        let x = 100;
        return x;
    }

    return x;
}

f(false, 0); // returns '0'
f(true, 0);  // returns '100'
```

The act of introducing a new name in a more nested scope is called *shadowing*.
It is a bit of a double-edged sword in that it can introduce certain bugs on its own in the event of accidental shadowing, while also preventing certain bugs.
For instance, imagine we had written our earlier `sumMatrix` function using `let` variables.

좀 더 중첩된 블록 범위 안에서 새 이름을 도입하는것을 *shadowing*이라고 부릅니다.
우발적인 shadowing은 특정 버그를 유발할 수 있는 양날의 검이고 또한 특정 버그를 예방할 수 있습니다.
예를 들어 이전의 `sumMatrix` 함수를 `let` 변수를 사용하여 작성햇다고 생각해보세요. 

```ts
function sumMatrix(matrix: number[][]) {
    let sum = 0;
    for (let i = 0; i < matrix.length; i++) {
        var currentRow = matrix[i];
        for (let i = 0; i < currentRow.length; i++) {
            sum += currentRow[i];
        }
    }

    return sum;
}
```

This version of the loop will actually perform the summation correctly because the inner loop's `i` shadows `i` from the outer loop.

이 버전의 반목분은 실제로 내부 반복문의 `i`과 외부 반목문의 `i`을 보호해서 합계를 올바르게 계산합니다.

Shadowing should *usually* be avoided in the interest of writing clearer code.
While there are some scenarios where it may be fitting to take advantage of it, you should use your best judgement.

shadowing은 더 명확한 코드를 작성하기 위해서 피해야 합니다.
상황에 따라 이점을 활용하는 것이 좀 더 효율적일 수도 있지만, 최선의 판단을 내려야 합니다.

## 변수 블록 범위 캡쳐

When we first touched on the idea of variable capturing with `var` declaration, we briefly went into how variables act once captured.
To give a better intuition of this, each time a scope is run, it creates an "environment" of variables.
That environment and its captured variables can exist even after everything within its scope has finished executing.

`var`선언으로 변수 캡처를 처음 접했을때, 캡쳐된 변수가 어떻게 작동하는지 간략하게 살펴보았습니다.
더 직관적으로 scope가 실행될 때 마다 변수의 "environment"를 생성합니다.
이 environment와 캡처된 변수는 안의 범위에 모든 항목이 완료된 뒤에도 존재 할 수 있습니다.

```ts
function theCityThatAlwaysSleeps() {
    let getCity;

    if (true) {
        let city = "Seattle";
        getCity = function() {
            return city;
        }
    }

    return getCity();
}
```

Because we've captured `city` from within its environment, we're still able to access it despite the fact that the `if` block finished executing.

왜냐하면 `city`를 environment 안에서 캡처했으므로, `if`문이 실행이 끝난 뒤임에도 불구하고 계속 접근할 수 있습니다. 

Recall that with our earlier `setTimeout` example, we ended up needing to use an IIFE to capture the state of a variable for every iteration of the `for` loop.
In effect, what we were doing was creating a new variable environment for our captured variables.
That was a bit of a pain, but luckily, you'll never have to do that again in TypeScript.

이전의 `setTimeout` 예제에서는 `for`문이 반복될때마다 IIFE(즉시 실행 함수 표현)을 변수를 캡쳐 한후에 사용해야 했습니다.
실제로 우리가 한 작업은 캡처된 변수에 대한 새로운 environment를 만드는 것 이었습니다.
조금 귀찮았지만, 다행스럽게도 TypeScript에서는 다신 그럴 필요가 없습니다. 

`let` declarations have drastically different behavior when declared as part of a loop.
Rather than just introducing a new environment to the loop itself, these declarations sort of create a new scope *per iteration*.
Since this is what we were doing anyway with our IIFE, we can change our old `setTimeout` example to just use a `let` declaration.

`let`선언은 반복문에서 선언할떄 다른 행동을 합니다.
반복문 자체에서 새로운 environment를 도입하는 대신 *반복 할때마다* 하나의 새로운 범위를 만듬니다.
어쨌든 IIFE(즉시 실행 함수 표현)을 사용하여 작업을 해왔기 때문에, 이전의 `setTimeout` 예제를 `let` 선언을 이용하여 수정할 수 있습니다. 

```ts
for (let i = 0; i < 10 ; i++) {
    setTimeout(function() { console.log(i); }, 100 * i);
}
```

and as expected, this will print out

그리고 예상대로 출력됩니다.

```text
0
1
2
3
4
5
6
7
8
9
```

# `const` 선언하기

`const` declarations are another way of declaring variables.

`const` 선언은 변수를 선언하는 또 다른 방법입니다.

```ts
const numLivesForCat = 9;
```

They are like `let` declarations but, as their name implies, their value cannot be changed once they are bound.
In other words, they have the same scoping rules as `let`, but you can't re-assign to them.

`let`을 선언하는 것과 비슷하지만 이름에서부터 의미하듯이 값을 한번 선언된 변경할 수 없습니다.
다른 말로 `let`과 같은 범위 지정 규칙을 가졌지만, 재선언을 할 수 없습니다.

This should not be confused with the idea that the values they refer to are *immutable*.

*불변*을 참조하는 값이라고 생각하면 혼란스럽지 않습니다.

```ts
const numLivesForCat = 9;
const kitty = {
    name: "Aurora",
    numLives: numLivesForCat,
}

// Error
kitty = {
    name: "Danielle",
    numLives: numLivesForCat
};

// all "okay"
kitty.name = "Rory";
kitty.name = "Kitty";
kitty.name = "Cat";
kitty.numLives--;
```

Unless you take specific measures to avoid it, the internal state of a `const` variable is still modifiable.
Fortunately, TypeScript allows you to specify that members of an object are `readonly`.
The [chapter on Interfaces](./Interfaces.md) has the details.

이를 피하기 위한 특별히 조치를 취하지 않는다면 `const` 내부 변수들은 여젼히 수정 가능합니다.
다행이도 TypeScript를 사용하면 내부 변수를  `readonly` 으로 지정할 수 있습니다.
[chapter on Interfaces](./Interfaces.md) 에서 자세히 알 수 있습니다.

# `let` vs. `const`

Given that we have two types of declarations with similar scoping semantics, it's natural to find ourselves asking which one to use.
Like most broad questions, the answer is: it depends.

유사한 범위 지정 의미를 가진 두 가지 형태의 선언이 있으면, 어떤 것을 사용할지 스스로 찾는 건 당연한 일입니다.
대부분 일반적인 질문과 마찬가지로, 답은 : 방법 나름대로입니다.

Applying the [principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege), all declarations other than those you plan to modify should use `const`.
The rationale is that if a variable didn't need to get written to, others working on the same codebase shouldn't automatically be able to write to the object, and will need to consider whether they really need to reassign to the variable.
Using `const` also makes code more predictable when reasoning about flow of data.

[principle of least privilege](https://en.wikipedia.org/wiki/Principle_of_least_privilege)을 적용하려면, 수정이 필요하지 않으면 모두 `const`로 선언해야 합니다.
이론적으로 변수에 작성할 필요가 없는 경우, 동일한 코드 베이스에서 작업하는 다른 사람들이 자동으로 객체에 쓸수가 없어서 변수에 실제로 재 할당이 필요한지 생각할 필요가 없습니다.
데이터 흐름을 추측할 때, `const`를 사용하면 코드를 더욱 예측할 수 있습니다.

Use your best judgement, and if applicable, consult the matter with the rest of your team.

최선의 판단을 하고 문제가 있는 경우 팀과 상의하세요.

The majority of this handbook uses `let` declarations.

핸드북에서는 주로 `let`을 사용하여 선언합니다.

# 비구조화

Another ECMAScript 2015 feature that TypeScript has is destructuring.
For a complete reference, see [the article on the Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment).
In this section, we'll give a short overview.

TypeScript에 있는 또 다른 ECMAScript 2015의 기능은 비구조화입니다.
전체 참고서는 [the article on the Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)를 확인해 주세요.
이 부문에서는 간단한 개요를 제공합니다.

## 배열 비구조화

The simplest form of destructuring is array destructuring assignment:

가장 간단한 비구조화 형태는 배열 비구조화 할당입니다.

```ts
let input = [1, 2];
let [first, second] = input;
console.log(first); // outputs 1
console.log(second); // outputs 2
```

This creates two new variables named `first` and `second`.
This is equivalent to using indexing, but is much more convenient:

`first` 와 `second`라는 두 새 변수를 생성했습니다.
이는 인덱싱을 사용하는 것과 같지만 훨씬 편리합니다.

```ts
first = input[0];
second = input[1];
```

Destructuring works with already-declared variables as well:

비구조화는 이미 선언된 변수에서도 작동합니다.

```ts
// swap variables
[first, second] = [second, first];
```

And with parameters to a function:

그리고 함수의 매개변수에서도 작동합니다.

```ts
function f([first, second]: [number, number]) {
    console.log(first);
    console.log(second);
}
f([1, 2]);
```

You can create a variable for the remaining items in a list using the syntax `...`:

`...` 구문을 이용하여 리스트의 나머지 항목에 대한 변수를 만들 수 있습니다.

```ts
let [first, ...rest] = [1, 2, 3, 4];
console.log(first); // outputs 1
console.log(rest); // outputs [ 2, 3, 4 ]
```

Of course, since this is JavaScript, you can just ignore trailing elements you don't care about:

또한, 이것은 JavaScript이므로 불필요한 후행 요소는 무시할 수 있습니다.

```ts
let [first] = [1, 2, 3, 4];
console.log(first); // outputs 1
```

Or other elements:

또는 다른 요소를

```ts
let [, second, , fourth] = [1, 2, 3, 4];
```

## 객체 비구조화

You can also destructure objects:

객체 또한 비구조화 할 수 있습니다.

```ts
let o = {
    a: "foo",
    b: 12,
    c: "bar"
};
let { a, b } = o;
```

This creates new variables `a` and `b` from `o.a` and `o.b`.
Notice that you can skip `c` if you don't need it.

`a`와 `b`를 `o.a`와 `o.b`에서 새 변수를 생성했습니다.
`c`가 필요하지 않다면 건너뛸 수 있습니다.

Like array destructuring, you can have assignment without declaration:

배열 비구조화처럼 선언 없이 할당 할 수 있습니다.

```ts
({ a, b } = { a: "baz", b: 101 });
```

Notice that we had to surround this statement with parentheses.
JavaScript normally parses a `{` as the start of block.

이 문장을 괄호로 묶어야 한다는 것에 주목하세요.
JavaScript는 보통 `{`를 블록의 시작으로 구문분석합니다.

You can create a variable for the remaining items in an object using the syntax `...`:

`...`구문으로 객체의 나머지 항목에 대한 변수를 만들 수 있습니다.

```ts
let { a, ...passthrough } = o;
let total = passthrough.b + passthrough.c.length;

```

### 속성 이름 바꾸기

You can also give different names to properties:

속성에 다른 이름으로 지정할 수 있습니다.

```ts
let { a: newName1, b: newName2 } = o;
```

Here the syntax starts to get confusing.
You can read `a: newName1` as "`a` as `newName1`".
The direction is left-to-right, as if you had written:

이제 구문이 어려워지기 시작합니다.
`a: newName1`을 `a`의 `newName1`으로 읽을 수 있습니다.
글을 쓰는 것처럼, 방향은 왼쪽에서 오른쪽입니다.

```ts
let newName1 = o.a;
let newName2 = o.b;
```

Confusingly, the colon here does *not* indicate the type.
The type, if you specify it, still needs to be written after the entire destructuring:

혼란스럽게 콜론은 여기서 타입을 나타내지 *않습니다*.
타입은 지정한 후, 완전히 비구조화된 뒤에 작성하는 것이 계속 필요합니다

```ts
let { a, b }: { a: string, b: number } = o;
//역자 추가 
let { a: newName1, b: newName2 }: {a: string, b: number } = o;
```

### 기본 값

Default values let you specify a default value in case a property is undefined:

기본값을 사용하여 속성이 정의되지 않았을 때 기본값을 사용할 수 있습니다.

```ts
function keepWholeObject(wholeObject: { a: string, b?: number }) {
    let { a, b = 1001 } = wholeObject;
}
```

`keepWholeObject` now has a variable for `wholeObject` as well as the properties `a` and `b`, even if `b` is undefined.

`keepWholeObject`는 이제 `b`가 정의되지 않아도 `a`와 `b` 속성뿐만 아니라 `wholeObject`에 대한 변수를 같습니다.

## 함수 선언

Destructuring also works in function declarations.
For simple cases this is straightforward:

또한, 비구조화는 함수 선언에도 적용됩니다.
단순한 경우 이것이 간단합니다.

```ts
type C = { a: string, b?: number }
function f({ a, b }: C): void {
    // ...
}
```

But specifying defaults is more common for parameters, and getting defaults right with destructuring can be tricky.
First of all, you need to remember to put the pattern before the default value.

그러나 일반적으로 매개변수에 기본값을 지정하는 것이 일반적이며, 비구조화할 때 기본값을 가져오는 것이 까다로울 수 있습니다.
우선, 기본적으로 패턴 앞에 기본값을 두는 것을 기억합니다.

```ts
function f({ a="", b=0 } = {}): void {
    // ...
}
f();
```

> The snippet above is an example of type inference, explained later in the handbook.

> 위 스니펫은 타입 추론의 예시이며 나중에 핸드북 뒤에서 설명합니다.

Then, you need to remember to give a default for optional properties on the destructured property instead of the main initializer.
Remember that `C` was defined with `b` optional:

그런 다음 기본 이니셜 라이저가 아닌 선택적 비구조화 속성에 대한 기본값을 지정하는 것을 기억해야합니다.
`C`는`b` 옵션으로 정의되었다는 것을 기억하세요.

```ts
function f({ a, b = 0 } = { a: "" }): void {
    // ...
}
f({ a: "yes" }); // ok, default b = 0
f(); // ok, default to { a: "" }, which then defaults b = 0
f({}); // error, 'a' is required if you supply an argument
```

Use destructuring with care.
As the previous example demonstrates, anything but the simplest destructuring expression is confusing.
This is especially true with deeply nested destructuring, which gets *really* hard to understand even without piling on renaming, default values, and type annotations.
Try to keep destructuring expressions small and simple.
You can always write the assignments that destructuring would generate yourself.

조심해서 비구조화를 사용하세요.
앞 예제에서 보여 주듯이, 가장 간단한 비구조화 표현을 제외하고는 혼란스럽습니다.
특히 깊게 중첩된 비구조화는 이름 바꾸기, 기본값 및 타입 주석을 적어도 이해하기 어렵습니다.
비구조화 표현을 작고 간단하게 하려고 계속 유지하세요.
비구조화는 언제나 스스로 과제를 만들 수 있습니다.

## Spread

The spread operator is the opposite of destructuring.
It allows you to spread an array into another array, or an object into another object.
For example:

spread 연산자는 비구조화의 반대입니다.
배열을 다른 배열에 전개하거나 객체를 다른 객체에 전개할수 있습니다.
예:

```ts
let first = [1, 2];
let second = [3, 4];
let bothPlus = [0, ...first, ...second, 5];
```

This gives bothPlus the value `[0, 1, 2, 3, 4, 5]`.
Spreading creates a shallow copy of `first` and `second`.
They are not changed by the spread.

bothPlus값은 `[0, 1, 2, 3, 4, 5]` 입니다.
`first`와 `second`가 복사되어 앝은 복사본을 만듬니다.
spread로 인해 값이 변경되지는 않습니다.

You can also spread objects:

또한, 객체를 전개 할 수 있습니다.

```ts
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { ...defaults, food: "rich" };
```

Now `search` is `{ food: "rich", price: "$$", ambiance: "noisy" }`.
Object spreading is more complex than array spreading.
Like array spreading, it proceeds from left-to-right, but the result is still an object.
This means that properties that come later in the spread object overwrite properties that come earlier.
So if we modify the previous example to spread at the end:

이제 `search`는 `{ food: "rich", price: "$$", ambiance: "noisy" }` 입니다.
객체 전개는 배열 전개보다 좀 더 복잡합니다.
배열전개에처럼 왼쪽에서 오른쪽으로 전개되지만, 결과는 여전히 계속 객체입니다.
즉, 나중에 전개되는 객체 속정은 이전 객체 속성을 덮어씁니다.
앞의 예제를 수정하여 끝에 전개하면

```ts
let defaults = { food: "spicy", price: "$$", ambiance: "noisy" };
let search = { food: "rich", ...defaults };
```

Then the `food` property in `defaults` overwrites `food: "rich"`, which is not what we want in this case.

`default` 안의 `food` 속성은 `food: "rich"`을 덮여 쓰였습니다. 이 경우는 원하던 것이 아닙니다.

Object spread also has a couple of other surprising limits.
First, it only includes an objects'
[own, enumerable properties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties).
Basically, that means you lose methods when you spread instances of an object:

객체 전개는 또한 두 가지 놀라운 한계가 있습니다.
첫 번째는 한 객체만 포함할 수 있습니다.
[own, enumerable properties](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties).
기본적으로 객체를 전개할 때 메소드가 손실된다는 것을 의미합니다.

```ts
class C {
  p = 12;
  m() {
  }
}
let c = new C();
let clone = { ...c };
clone.p; // ok
clone.m(); // error!
```

Second, the Typescript compiler doesn't allow spreads of type parameters from generic functions.
That feature is expected in future versions of the language.

두 번째는 TypeScript 컴파일러는 일반적인 함수 매개변수 행태의 전개를 허용하지 않습니다.
이 기능은 추후 버전의 언어에서 지원됩니다.
