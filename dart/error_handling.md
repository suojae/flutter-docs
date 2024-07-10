공식문서 링크<br/>
[https://dart-ko.dev/language/error-handling](https://dart-ko.dev/language/error-handling)


<br/>


## Error handling

<br/>

### Exception

- 만약 에러를 던졌음에도 캐치되지 않고 있다면 `isolate`된 메모리 스택이 지연된 상태인지 확인해보아야한다.



<br/>

### Throw

```dart
throw FormatException('Expected at least 1 section');

throw 'Out of llamas!';
```

- 기본 `Exception` 타입 뿐만아니라 커스터마이징한 `Exception`타입, `String`등 다양한 타입을 `throw` 할 수 있다.


<br/>

### Catch

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```
- `throw` 했던 에러를 받을 때는 `on` 을 통해서 특정 예외타입만 받아 처리할 수 있다.

<br/>


```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 특정한 예외
  buyMoreLlamas();
} on Exception catch (e) {
  // Exception 타입인 예외
  print('Unknown exception: $e');
} catch (e) {
  // 타입을 정하지 않은 catch 절로 모든 예외를 처리
  print('Something really unknown: $e');
}
```

- `throw` 한 특정 타입을 받을 때 `Exception` 타입이 아닌 경우 `on`만, `Exception`타입이 맞을 경우 `on`, `catch`를 써준다. 
- 그냥 `catch`만 쓸경우 `on`으로 특정 예외처리한 타입을 제외한 모든 예외를 받게 된다.

<br/>

```dart
try {
  // ···
} on Exception catch (e) {
  print('Exception details:\n $e');
} catch (e, s) {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
```

- `catch` 에서는 error 뿐만 아니라 s를 통해 `stack trace`도 가능하다.
- 함수가 연속적으로 호출되면 어디 함수에서 에러가 났는지 알기 어려운데 이러한 연속적인 함수 중 어느곳에서 에러가 났는지 확인할 때 유용한 것이 `stack trace`이다.
- `stack trace`는 에러가 던져진 함수가 어떤 함수들이 줄줄이 엮여있는지 확인할 수 있는 역할을 한다.

<br/>

```dart
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // 런타임 에러
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // 호출자가 예외를 확인 할 수 있도록 허락
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
```
- `rethrow` 기능을 통해 에러가난 함수를 호출한 함수가 에러를 받아 처리할 수 있다. 

<br/>


### Finally

```dart
try {
  breedMoreLlamas();
} finally {
  // 예외가 발생되더라도, 항상 실행됩니다.
  cleanLlamaStalls();
}


try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // 예외를 먼저 처리합니다.
} finally {
  cleanLlamaStalls(); // 다음 실행
}
```

- `finally`는 무조건 실행되는 부분이다. 에러가 던져지든, 정상적으로 처리되든 상관없이 무조건 실행된다.


<br/>

### Assert

```dart
// Make sure the variable has a non-null value.
assert(text != null);

// Make sure the value is less than 100.
assert(number < 100);

// Make sure this is an https URL.
assert(urlString.startsWith('https'));
```

- `assert` 는 기본적으로 `debug` 모드에서만 실행된다.
- `debug` 모드에서 조건을 만족시키지 못하면 런타임 에러를 발생시킨다.