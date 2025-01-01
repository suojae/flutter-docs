#### 공식문서 링크 

[https://dart.dev/language/variables](https://dart.dev/language/variables)

<br/>

#

### Variables

<br/>

```dart
var name = 'Bob';
```

- 변수는 "참조"를 저장한다. name을 부름으로써 `Bob`이라는 `String Object`의 참조를 호출한다.


<br/>

```dart
Object name = 'Bob';
```

- 만약 특정 타입으로 선언하고 싶지 않담녀 `Object`, `dynamic` 타입으로 선언하자.

<br/>

#### Null Safety

```dart
String? name  // Nullable type. Can be `null` or string.

String name   // Non-nullable type. Cannot be `null` but can be string.
```
- 다트의 널세이프티는 참조값이 null일 경우 발생하는 런타임에러를 컴파일 타임에서 잡아준다는 것에 의의를 가진다. 


<br/>

```dart
int? lineCount;
assert(lineCount == null);
```

- 널세이프티로 선언된 타입의 기본값은 `null`이다.

<br/>

#### Late variables

```dart
// This is the program's only call to readThermometer().
late String temperature = readThermometer(); // Lazily initialized.
```

- late modifier는 non-nullable한 변수를 지연(나중에) 생성할 때 사용된다.
- 그렇다고 late로 선언해두고 값안넣고 써버리면 런타임 에러가 발생한다.
- 주의할 점은 late modifier는 **한 번만** 실행된다. 이후부터는 초기화된 값을 계속 사용한다.

<br/>

#### Final and const





