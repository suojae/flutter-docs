공식문서 링크<br/>
[https://docs.flutter.dev/cookbook/testing/unit/introduction](https://docs.flutter.dev/cookbook/testing/unit/introduction) <br/>
[https://docs.flutter.dev/cookbook/testing/unit/mocking](https://docs.flutter.dev/cookbook/testing/unit/mocking)

<br/>


## An introduction to unit testing

<br/>

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

<br/>
<br/>

## Mock dependencies using Mockito

<br/>

- 데이터베이스나 네트워크를 통해 가져온 데이터를 테스트하는 것은 너무 느리다
- I/O는 외부요소이기 때문에 프론트인 내가 테스트 로직을 알맞게 짰더라도 실패할 수 있다. 이를 flaky test(테스트 결과가 일관적x)라고 부른다.
- 따라서 DB나 네트워크 작업으로 가져온 데이터를 이용하는 함수를 테스트할 때는 "mock" 데이터를 쓰자
- Mock을 만들때는 대신 사용할 클래스를 만든다. 이걸 수작업으로도 할 수 있지만 `Mockito` 패키지를 통해 쉽게 할 수 있다.
- Mock만드는 순서는 아래와 같다.
1. 패키지를 임포트한다.
2. 테스트할 함수를 만든다.
3. mock과 함께 테스트파일을 만든다.
4. 테스트 코드를 작성한다.
5. 테스트를 돌린다.

<br/>

#### 1. 패키지를 임포트한다.

```
flutter pub add http dev:mockito dev:build_runner
```

<br/>

#### 2. 테스트할 함수를 만든다.

```dart
Future<Album> fetchAlbum(http.Client client) async {
  final response = await client
      .get(Uri.parse('https://jsonplaceholder.typicode.com/albums/1'));

  if (response.statusCode == 200) {
    // If the server did return a 200 OK response,
    // then parse the JSON.
    return Album.fromJson(jsonDecode(response.body) as Map<String, dynamic>);
  } else {
    // If the server did not return a 200 OK response,
    // then throw an exception.
    throw Exception('Failed to load album');
  }
}
```


<br/>

#### 3. 








