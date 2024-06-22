공식문서 링크<br/>
[https://docs.flutter.dev/cookbook/testing/unit/introduction](https://docs.flutter.dev/cookbook/testing/unit/introduction)

<br/>


## An introduction to unit testing
- 유닛테스트는 클래스의 메서드 하나하나를 테스트하기에 유용하다
- 테스트 관련 기능들을 `flutter_test`, `test` 패키지를 통해 제공해주고 있다.
- 유닛테스트 코드 작성 순서는 다음과 같다.
1. `test`, `flutter_test` 임포트하기
2. 테스트 파일 만들기
3. 테스트할 클래스 만들기
4. 클래스에 해당하는 테스트 코드 작성
5. 그룹으로 테스트들 묶기
6. 테스트 돌리기

<br/>


#### 1. `test`, `flutter_test` 임포트하기

```
flutter pub add dev:test
```


<br/>

#### 2. 테스트 파일 만들기

```
counter_app/
  lib/
    counter.dart
  test/
    counter_test.dart
```

<br/>

#### 3. 테스트할 클래스 만들기

```dart
class Counter {
  int value = 0;

  void increment() => value++;

  void decrement() => value--;
}
```

<br/>

#### 4. 클래스에 해당하는 테스트 코드 작성

```dart
// Import the test package and Counter class
import 'package:counter_app/counter.dart';
import 'package:test/test.dart';

void main() {
  test('Counter value should be incremented', () {
    final counter = Counter();

    counter.increment();

    expect(counter.value, 1);
  });
}
```

<br/>

#### 5. 그룹으로 테스트들 묶기

```dart
import 'package:counter_app/counter.dart';
import 'package:test/test.dart';

void main() {
  group('Test start, increment, decrement', () {
    test('value should start at 0', () {
      expect(Counter().value, 0);
    });

    test('value should be incremented', () {
      final counter = Counter();

      counter.increment();

      expect(counter.value, 1);
    });

    test('value should be decremented', () {
      final counter = Counter();

      counter.decrement();

      expect(counter.value, -1);
    });
  });
}
```

<br>

#### 6. 테스트 돌리기

테스트 파일을 돌리기

```
flutter test test/counter_test.dart
```

테스트 그룹을 돌리기

```
flutter test --plain-name "Test start, increment, decrement"
```





