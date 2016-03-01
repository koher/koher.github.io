
# Three Stories about __Error Handling__ in Swift

## Yuta Koshizawa @koher


---

## The First Story

### Meeting the Optionals




---

### Error Handling in C




```c
// [ C ]
int *numbers = (int *)malloc(sizeof(int) * 42);
if (numbers == NULL) {
    // Error handling here
}
```


---

### Error Handling in C


```c
// [ C ]
int *numbers = (int *)malloc(sizeof(int) * 42);
// Easily forgets to handle errors
```


---

### Error Handling in Java



```java
// [ Java ]
static int toInt(String string)
  throws FormatException {
    ...
}
```

```java
// [ Java ]
toInt("42");    // Success
toInt("Swift"); // Failure
```

---

### Error Handling in Java


```java
// [ Java ]
String string = ...;
int number = toInt(string); // Compilation error
```

---

### Error Handling in Java


```java
// [ Java ]
String string = ...;
try {
  int number = toInt(string);
  ...
} catch (FormatException e) {
  // Error handling
  ...
}
```


---

### Error Handling in Java


```java
// [ Java ]
String string = ...;
try {
  int number = toInt(string);
  ...
} catch (FormatException e) {
  // Error handling
  throw new Error("Never reaches here.");
}
```


---

### Error Handling in Java


```java
// [ Java ]
String string = ...;
int number = toInt(string)!; // Ignores exceptions
  // This `!` was what we wanted.
```

---

### Optionals for Error Handling




```swift
// [ Swift ]
func toInt(string: String) -> Int? {
  ...
}
```

---

### Optionals for Error Handling


```swift
// [ Swift ]
let string: String = ...
let number: Int = toInt(string) // Compilation error
```

---

### Optionals for Error Handling


```swift
// [ Swift ]
let string: String = ...
if let number = toInt(string) {
  ...
} else {
  // Error handling
  ...
}
```

---

### Optionals for Error Handling



```swift
// [ Swift ]
let string: String = ...
let number: Int = toInt(string)! // Ignores an error
```

---

### Optionals for Error Handling


```swift
// [ Swift ]
@warn_unused_result
func updateBar(bar: Bar) -> ()? {
  ...
}
```

```swift
// [ Swift ]
foo.updateBar(bar) // Warning
```

---

### Optionals for Error Handling


```swift
// [ Swift ]
if let _ = foo.updateBar(bar) {
  ...
} else {
  // Error handling
  ...
}
```

```swift
// [ Swift ]
_ = foo.updateBar(bar) // Ignores the error
```


---

### Optionals for Error Handling



```swift
// [ Swift ]
let string: String = ...
let number: Int? = toInt(string)

...

// Errors can be handled lazily
if let number = number {
  ...
} else {
  // Error handling
  ...
}
```

---

### Difficulty of Using Optionals



```swift
// [ Swift ]
let a: Int? = ...
let square = a * a // Compilation error
```


```swift
// [ Swift ]
let a: Int? = ...
let b: Int? = ...
let sum = a + b // Compilation error
```

---

### Difficulty of Using Optionals


```swift
// [ Swift ]
let a: Int? = ...
let square: Int?
if let a = a {
  square = a * a
} else {
  square = nil
}
```

```swift
// [ Swift ]
let a: Int? = ...
let b: Int? = ...
let sum: Int?
if let a = a, b = b {
  sum = a + b
} else {
  sum - nil
}
```

---

### Functional Operations for Optionals



```swift
// [ Swift ]
let a: Int? = ...
let square: Int? = a.map { $0 * $0 }
```


```swift
// [ Swift ]
let a: Int? = ...
let b: Int? = ...
let sum: Int? = a.flatMap { a in b.map { b in a + b } }
```

---

### Functional Operations for Optionals



```swift
// [ Swift ]
let id: String? = json["id"].string
```


```js
// [ JSON ]
// The `json` might not be an `Object`
[ "abc" ]
// It might not have a key named `"id"`
{ "foo": "abc" }
// The value might not be a `String`
{ "id": 42 }
```

---

### Functional Operations for Optionals


```swift
// [ Swift ]
struct Person {
  let id: String
  let firstName: String
  let lastName: String
  let age: Int
  let isAdmin: Bool
}

let id: String? = json["id"].string
let firstName: String? = json["firstName"].string
let lastName: String? = json["lastName"].string
let age: Int? = json["age"].int
let isAdmin: Bool? = json["isAdmin"].bool
```

---

### Functional Operations for Optionals


```swift
// [ Swift ]
let person: Person? = id.flatMap { id in
  firstName.flatMap { firstName in
    lastName.flatMap { lastName in
      age.flatMap { age in
        isAdmin.flatMap { isAdmin in
          Person(id: id, firstName: firstName,
            lastName: lastName, age: age, isAdmin: isAdmin)
        }
      }
    }
  }
}
```

---

### Functional Operations for Optionals


```swift
// [ Swift ]
let person: Person?
if let id = id, firstName = firstName,
  lastName = lastName, age = age, isAdmin = isAdmin {
  person = Person(id: id, firstName: firstName,
    lastName: lastName, age: age, isAdmin: isAdmin)
} else {
  person = nil
}
```


---

### Functional Operations for Optionals


```swift
// [ Swift ]
let person: Person? = curry(Person.init) <^> id
  <*> firstName <*> lastName <*> age <*> isAdmin
```


---

### Syntactic Sugars and Operators for Optionals


```swift
// [ Swift ]
//let foo: Optional<Foo> = ...
let foo: Foo? = ...

// let baz: Baz? = foo.flatMap { $0.bar }.flatMap { $0.baz }
let baz: Baz? = foo?.bar?.baz

// let quxOrNil: Qux? = ...
// let qux: Qux
// if let q = quxOrNil {
//   qux = q
// } else {
//   qux = Qux()
// }
let quxOrNil: Qux? = ...
let qux: Qux = quxOrNil ?? Qux()
```

---

### Optionals in Swift

- `Foo? == Optional<Foo>`
- Forced Unwrapping: `!`
- `map`, `flatMap`
- Applicative styles: `<^>`, `<*>` ([^2])
- Optional chaining: `foo?.bar?.baz`
- Nil coalescing operator: `??`



---


> I couldn't resist the temptation to put in a null reference, simply because it was so easy to implement.
-- Tony Hoare




---

## The Second Story

### Success or Failure






---

### Problems of Optionals


```swift
// [ Swift ]
let a: Int? = toInt(aString)
let b: Int? = toInt(bString)
let sum: Int? = a.flatMap { a in b.map { b in a + b } }

guard let sum = sum else {
  // Which `a` or `b` failed to be parsed?
  // What string was the input?
  ...
}
```


---

### Problems of Optionals



```swift
// [ Swift ]
let isAdmin: Bool
if let admin = json["isAdmin"].bool {
  // { "isAdmin": true }
  isAdmin = admin
} else {
  // 1. [ true ]
  // 2. {}
  // 3. { "isAdmin": 42 }
  isAdmin = ...
}
```



---

### Problems of Optionals

```swift
// [ Swift ]
let isAdmin: Bool
if let admin = json["isAdmin"].bool {
  // { "isAdmin": true }
  isAdmin = admin
} else {
  // 1. [ true ]
  // 2. {}                => false
  // 3. { "isAdmin": 42 }
  isAdmin = ...
}
```


---

### Problems of Optionals

```swift
// [ Swift ]
let isAdmin: Bool
if let admin = json["isAdmin"].bool {
  // { "isAdmin": true }
  isAdmin = admin
} else {
  // 1. [ true ]          => error
  // 2. {}                => false
  // 3. { "isAdmin": 42 } => error
  isAdmin = ...
}
```


---

### Alternatives of Optionals


1. _Tuples_
2. _Union types_
3. _Results_


---

### Tuples


```swift
// [ Swift ]
func toInt(string: String) -> (Int?, FormatError?) {
  ...
}
```


---

### Tuples


- `(value, nil  ) // Success`
- `(nil  , error) // Failure`
- `(value, error) // ???`
- `(nil  , nil  ) // ???`


---

### Union types



```swift
// [ Swift ]
func toInt(string: String) -> Int|FormatError {
  ...
}
```

```swift
// [ Swift ]
switch toInt(...) {
  case let value as Int:
    ...
  case let error as FormatError:
    // Error handling
    ...
}
```

---

### Union types


```java
// [ Ceylon ]
Integer? a = 42;
Integer|Null a = 42;
```

```bash
# [ Python ]
def foo() -> Optional[Foo]: ...
def foo() -> Union[Foo, None]: ...
```


---

### Union types


```swift
// [ Swift ]
enum Optional<T> {
  case Some(T)
  case None
}
```


---

### Results



```swift
// [ Swift ]
enum Result<T, E> {
  case Success(T)
  case Failure(E)
}
```


```swift
// [ Swift ]
enum Optional<T> {
  case Some(T)
  case None
}
```

---

### Results


```swift
// [ Swift ]
let a: Result<Int, FormatError> = toInt(aString)
let b: Result<Int, FormatError> = toInt(bString)
let sum: Result<Int, FormatError> = a.flatMap { a in b.map { b in a + b } }

switch sum {
  case let .Success(sum):
    ...
  case let .Failure(error):
    // Get the detailed error information from `error`
    ...
}
```

---

### Results


```swift
// [ Swift ]
let isAdmin: Bool
switch json["isAdmin"].bool {
  case let .Success(admin):
    isAdmin = admin
  case .Failure(.MissingKey):
    // {}                => false
    isAdmin = false
  case .Failure(.TypeMismatch, .NotObject):
    // [ true ]          => error
    // { "isAdmin": 42 } => error
    ...
}
```



---

### Results



```swift
// [ Swift ]
let foo: Result<Foo, Error> = ...
let baz: Result<Foo, Error>
  = foo.flatMap { $0.bar }.flatMap { $0.baz }
```

```swift
// [ Swift ]
let foo: Foo|Error = ...
let baz: Baz|Error = foo?.bar?.baz
```


---

### Difficulty of Using Results



```swift
// [ Swift ]
let a: Result<Int, ErrorA> = ...
let b: Result<Int, ErrorB> = ...
let sum: Result<Int, ???>
  = a.flatMap { a in b.map { b in a + b } }
```


---

### Difficulty of Using Results


```swift
// [ Swift ]
let a: Result<Int, ErrorA> = ...
let b: Result<Int, ErrorB> = ...
let sum: Result<Int, ErrorA|ErrorB>
  = a.flatMap { a in b.map { b in a + b } }
```

---

### Difficulty of Using Results


```swift
// [ Swift ]
let a: Result<Int, ErrorA> = ...
let b: Result<Int, ErrorB> = ...
let sum: Result<Int, Result<ErrorA, ErrorB>>
  = a.flatMap { a in b.map { b in a + b } }
```


---

### Difficulty of Using Results


```swift
// [ Swift ]
let a: Int|ErrorA = ...
let b: Int|ErrorB = ...
let sum: Int|ErrorA|ErrorB
  = a.flatMap { a in b.map { b in a + b } }
```

---

### Difficulty of Using Results


```swift
// [ Swift ]
let id: String|ErrorA = ...
let firstName: String|ErrorB = ...
let lastName: String|ErrorC = ...
let age: Int|ErrorD = ...
let isAdmin: Bool| ErrorE = ...

let person: Person|(((ErrorA|ErrorB)|ErrorC)|ErrorD)|ErrorE
  = curry(Person.init) <^> id <*> firstName
    <*> lastName <*> age <*> isAdmin
```

---

### Difficulty of Using Results


```swift
// [ Swift ]
switch person {
  case let .Success(person):
    ...
  case let .Failure(.Success(.Success(.Success(.Success(.Failure(errorA)))))):
    ...
  case let .Failure(.Success(.Success(.Success(.Failure(errorB))))):
    ...
  case let .Failure(.Success(.Success(.Failure(errorC)))):
    ...
  case let .Failure(.Success(.Failure(errorD))):
    ...
  case let .Failure(.Failure(errorD)):
    ...
}
```



---

### Results without an Error Type


```swift
// [ Swift ]
enum Result<T, E> {
  case Success(T)
  case Failure(E)
}
```

---

### Results without an Error Type


```swift
// [ Swift ]
enum Result<T> {
  case Success(T)
  case Failure(ErrorType)
}
```


---

### Results without an Error Type



```swift
// [ Swift ]
downloadJson(url) { json: Result<Json> in
  switch json {
    case let .Success(json): // success
      ...
    case let .Failure(.Timeout): // timeout
      // retry
      ...
    case let .Failure(error): // others
      // error
      ...
  }
}
```

---

### Results without an Error Type


```swift
// [ Swift ]
let isAdmin: Bool
switch json["isAdmin"].bool {
  case let .Success(admin): // success
    isAdmin = admin
  case .Failure(.MissingKey): // missing key
    // {}                => false
    isAdmin = false
  case let .Failure(error): // others
    // [ true ]          => error
    // { "isAdmin": 42 } => error
    ...
}
```


---

### Results without an Error Type


```swift
// [ Swift ]
enum Foo {
  case Bar(A)
  case Baz
  case Qux(B)
}

func foo() -> Foo { ... }

switch foo() {
  case let Bar(a):
    ...
  case let Baz:
    ...
  case let Qux(b):
    ...
}
```

---

### Results without an Error Type


```swift
// [ Swift ]
let a: Result<Int> = ... // ErrorA
let b: Result<Int> = ... // ErrorB
let sum: Result<Int> // ErrorA or ErrorB
  = a.flatMap { a in b.map { b in a + b } }
```

---

### Results without an Error Type


```swift
// [ Swift ]
let a: Int| = ...
let b: Int| = ...
let sum: Int|
  = a.flatMap { a in b.map { b in a + b } }
```


---

## The Third Story

### try



---

### Automatic Propagation



```swift
// [ Swift ]
func toInt(string: String) throws -> Int {
  ...
}

do {
  let number = try toInt(string)
  ...
} catch let error {
  // Error handling here
  ...
}
```

---

### Automatic Propagation


```swift
// [ Swift ]
// Manual propagation
switch(toInt(string)) {
  case let .Success(number):
    ...
  case let .Failure(error): // Handles an error manually
    ...
}

// Automatic propagation
do {
  let number = try toInt(string) // Jumps to `catch` automatically
  ...
} catch let error {
  ...
}
```

---

### Automatic Propagation


```swift
// [ Swift ]
// Manual propagation
let a: Result<Int> = toInt(aString)
let b: Result<Int> = toInt(bString)
switch a.flatMap { a in b.map { b in a + b } } {
  case let .Success(sum):
    ...
  case let .Failure(error):
    ...
}

// Automatic propagation
do {
  let a: Int = try toInt(aString)
  let b: Int = try toInt(bString)
  let sum: Int = a + b
  ...
} catch let error {
  ...
}
```

---

### Automatic Propagation



```swift
// [ Swift ]
let sum = toInt(aString).flatMap { a in
   toInt(bString).flatMap {
     .Some(a + b)
   }
}
```

```haskell
-- [ Haskell ]
sum = do
  a <- toInt aString
  b <- toInt bString
  Just (a + b)
```



---

### Marked Propagation



```swift
// [ Swift ]
func toInt(string: String) throws FormatError -> Int { // Compilation error
  ...
}
```

---

### Marked Propagation


```java
// [ Java ]
class FormatException extends RuntimeException {
  ...
}
```

```java
// [ Java ]
static int toInt(String string) throws FormatException {
    ...
}
```

```java
// [ Java ]
String string = ...;
int number = toInt(string); // No compilation error
```

---

### Marked Propagation


```java
// [ Java ]
void foo() { // What can `foo` throw?
  a(); // May throw an unchecked exception
  b(); // May throw an unchecked exception
  c(); // May throw an unchecked exception
  d(); // May throw an unchecked exception
  e(); // May throw an unchecked exception
  f(); // May throw an unchecked exception
  g(); // May throw an unchecked exception
}
```


---

### Marked Propagation



```swift
// [ Swift ]
func foo() throws { // What can `foo` throw?
  a() // Can throw an error?
  b() // Can throw an error?
  c() // Can throw an error?
  d() // Can throw an error?
  e() // Can throw an error?
  f() // Can throw an error?
  g() // Can throw an error?
}
```

---

### Marked Propagation


```swift
// [ Swift ]
func foo() throws {
  a()
  try b() // May throw an error
  c()
  d()
  try e() // May throw an error
  f()
  g()
}
```



---

### Marked Propagation


```java
// [ Java ]
try {
  foo();
  bar();
  baz();
} catch (QuxException e) {
  // Where did it come from?
}
```

```swift
// [ Swift ]
do {
  foo()
  try bar()
  baz()
} catch let error {
  // Came from `bar()`
}
```

---

### Marked Propagation

- Careless about error types
- Implicit control flow


---

### Marked Automatic Propagation for Optionals



```swift
// [ Swift ]
// Optionals for a simple domain error
func toInt(string: String) -> Int? {
  ...
}

// Manual propagation
guard let number = toInt(string) {
  // Error handling here
  ...
}
```

---

### Marked Automatic Propagation for Optionals



```swift
// [ Swift ]
// Manual propagation
let a: Int? = toInt(aString)
let b: Int? = toInt(bString)
if let sum = (a.flatMap { a in b.map { b in a + b } }) {
    ...
} else {
    ...
}

// Automatic propagation
do {
  let a: Int = try toInt(aString)
  let b: Int = try toInt(bString)
  let sum: Int = a + b
  ...
} catch {
  ...
}
```



---

### Results and try



```swift
// [ Swift ]
func toInt(string: String) throws -> Int {
  ...
}
```

```swift
// [ Swift ]
func toInt(string: String) -> Result<Int> {
  ...
}
```

---

### Results and try


```swift
// [ Swift ]
do {
  let a: Int = try toInt(aString)
  let b: Int = try toInt(bString)
  let sum: Int = a + b
  ...
} catch {
  ...
}
```

```swift
// [ Swift ]
let a: Result<Int> = toInt(aString)
let b: Result<Int> = toInt(bString)
switch a.flatMap { a in b.map { b in a + b } } {
  case let .Success(sum):
    ...
  case let .Failure(error):
    ...
}
```



---

### Results and try



```swift
// [ Swift ]
let infinite: List<Int> = List { $0 } // [0, 1, 2, 3, 4, ...]
let square: List<Int> = infinite.map { $0 * $0 } // [0, 1, 4, 9, 16, ...]
```

---

### Results and try


```swift
// [ Swift ]
func toInt(string: String) throws -> Int {
  ...
}

let strings: List<String> = ... // ["0", "1", "2", ...]
do {
  // Never finishes
  let numbers: List<Int> = try strings.map(transform: toInt)
} catch let error {
  ...
}
```


---

### Results and try


```swift
// [ Swift ]
// By throws
func map<U>(transform: T throws -> U) throws -> List<U>


// By `Result`
func map<U>(transform: T -> Result<U>) -> Result<List<U>>
```



---

### Results and try


```swift
// [ Swift ]
// By throws
func map<U>(transform: T throws -> U) throws -> List<U>
func map<U>(transform: T throws -> U) -> List<Result<U>>

// By `Result`
func map<U>(transform: T -> Result<U>) -> Result<List<U>>
func map<U>(transform: T -> Result<U>) -> List<Result<U>>
```

---

### Results and try


```swift
// [ Swift ]
func toInt(string: String) throws -> Int {
  ...
}

let a: List<String> = ... // ["0", "1", "2", ...]
let b: List<Result<Int>> = strings.map(transform: toInt)
  // [Result(0), Result(1), Result(2), ...]
let c: List<Result<Int>> = numbers.take(10)
  // [Result(0), Result(1), Result(2), ..., Result(9)]
let d: Result<List<Int>> = sequence(first10)
  // Result([0, 1, 2, ..., 9])
do {
  let e: List<Int> = try d // [0, 1, 2, ..., 9]
  ...
} catch let error {
  // Handling `FormatError`
  ...
}
```


---

### Results and try



```swift
// Swift 2.x
let a = toInt(aString) // Compilation error here
let b = toInt(bString)
let sum = a + b
```


```swift
// Swift with my proposal
let a = toInt(aString)
let b = toInt(bString)
let sum = a + b // Compilation error here
```


---

### Asynchronous Operations and try



```swift
// [ Swift ]
let a: Promise<Int> = asyncGetInt(...)
let b: Promise<Int> = asyncGetInt(...)
let sum: Promise<Int> = a.flatMap { a in b.map { b in a + b } }
```


```swift
// [ Swift ]
let a: Result<Int> = failableGetInt(...)
let b: Result<Int> = failableGetInt(...)
let sum: Result<Int> = a.flatMap { a in b.map { b in a + b } }
```


---

### Asynchronous Operations and try



```csharp
// [ C# ]
async Task<int> AsyncGetInt(...) {
  ...
}

async void PrintSum() {
  int a = await AsyncGetInt(...);
  int b = await AsyncGetInt(...);
  Console.WriteLine(a + b);
}
```

```swift
// [ Swift ]
func asyncGetInt(...) async -> Promise<Int> {
  ...
}

func printSum() async {
  let a: Int = await asyncGetInt(...)
  let b: Int = await asyncGetInt(...)
  print(a + b)
}
```

---

### Asynchronous Operations and try


```csharp
// [ C# ]
async Task<int> AsyncGetInt(...) {
  ...
}

async void PrintSum() {
  int a = await AsyncGetInt(...);
  int b = await AsyncGetInt(...);
  Console.WriteLine(a + b);
}
```


```swift
// [ Swift ]
func asyncGetInt(...) async -> Promise<Int> {
  ...
}

func printSum() async {
  let a: Int = await asyncGetInt(...)
  let b: Int = await asyncGetInt(...)
  print(a + b)
}
```


---

### Asynchronous Operations and try

```swift
// [ Swift ]
func asyncGetInt(...) async -> Int { // <- Changed Here
  ...
}

func printSum() async {
  let a: Int = await asyncGetInt(...)
  let b: Int = await asyncGetInt(...)
  print(a + b)
}
```

```swift
// [ Swift ]
func asyncGetInt(...) async -> Promise<Int> {
  ...
}

func printSum() async {
  let a: Int = await asyncGetInt(...)
  let b: Int = await asyncGetInt(...)
  print(a + b)
}
```


---

### Asynchronous Operations and try

```swift
// [ Swift ]
func asyncGetInt(...) async -> Int {     // async
  ...
}

func printSum() async {                  // async
  let a: Int = await asyncGetInt(...)    // await
  let b: Int = await asyncGetInt(...)    // await
  print(a + b)
}
```

```swift
// [ Swift ]
func failableGetInt(...) throws -> Int { // throws
  ...
}

func printSum() throws {                 // throws
  let a: Int = try failableGetInt(...)   // try
  let b: Int = try failableGetInt(...)   // try
  print(a + b)
}
```


---

### Asynchronous Operations and try


```swift
// [ Swift ]
func asyncGetInt(...) async -> Int {     // async
  ...
}

do {
  let a: Int = await asyncGetInt(...)    // await
  let b: Int = await asyncGetInt(...)    // await
  print(a + b)
}
```

```swift
// [ Swift ]
func asyncGetInt(...) -> Promise<Int> {  // Promise
  ...
}

do {
  let a: Int = try asyncGetInt(...)      // try
  let b: Int = try asyncGetInt(...)      // try
  print(a + b)
}
```



---

### Let's Discuss Error Handling


- `Result<T>` instead of `Result<T, E>`
- Automatic propagation for `Optional`s
- Interoperation between `throws` and `Result`
- `try` for asynchronous operations

... and let me know your opinions


---




---

# Stay Typed. Stay Practical.





---

## All my codes in the slides are available at

# koherent.org/tryswift/

## I will also post some further topics related to this talk there.


[^1]: "SwiftyJSON", [https://github.com/SwiftyJSON/SwiftyJSON](https://github.com/SwiftyJSON/SwiftyJSON)

[^2]: "thoughtbot/Runes", [https://github.com/thoughtbot/Runes](https://github.com/thoughtbot/Runes)

[^3]: "antitypical/Result", [https://github.com/antitypical/Result](https://github.com/antitypical/Result)

[^4]: "ResultK", [https://github.com/koher/ResultK](https://github.com/koher/ResultK)

[^5]: "Error Handling Rationale and Proposal", [https://github.com/apple/swift/blob/master/docs/ErrorHandlingRationale.rst](https://github.com/apple/swift/blob/master/docs/ErrorHandlingRationale.rst)

[^6]: "ListK" [https://github.com/koher/ListK](https://github.com/koher/ListK)

[^7]: "PromiseK" [https://github.com/koher/PromiseK](https://github.com/koher/PromiseK)

[^8]: "Steve Jobs' Commencement address (2005)" [http://news.stanford.edu/news/2005/june15/jobs-061505.html](http://news.stanford.edu/news/2005/june15/jobs-061505.html)