공식문서 링크 <br/>
[https://bloclibrary.dev/bloc-concepts/](https://bloclibrary.dev/bloc-concepts/)

<br/>
<br/>

## Bloc Concepts



### Streams

```dart
Stream<int> countStream(int max) async* {
    for (int i = 0; i < max; i++) {
        yield i;
    }
}
```

- bloc 라이브러리를 이용하기 위해서 `Stream`에 대한 이해는 필수이다.
- 함수 바디 앞에 `async*`를 붙이고 `yield` 키워드를 통해 `stream`을 방출할 수 있다.
- 매번 yield를 할 때마다 만들어진 스트림에 이벤트가 들어가고 이에따라 소비자쪽에서 반응하게 된다.

 <br/>

```dart
Future<int> sumStream(Stream<int> stream) async {
    int sum = 0;
    await for (int value in stream) {
        sum += value;
    }
    return sum;
}
```
- Future에서는 `async`와 `awiat`을 통해 비동기 값을 받을 수 있다.

<br/>

```dart
void main() async {
    /// Initialize a stream of integers 0-9
    Stream<int> stream = countStream(10);
    /// Compute the sum of the stream of integers
    int sum = await sumStream(stream);
    /// Print the sum
    print(sum); // 45
}
```
- 메인함수에서 Future와 Stream을 위와같이 소비할 수 있다.


<br/>

#

### Cubit

<img width="400" alt="image" src="https://github.com/suojae3/flutter_dart_docs/assets/126137760/d0d13c9a-e8c7-4268-bdbf-259747ea4c7d">

<br/>
<br/>

- **Cubit**은 Bloc 라이브러리의 일부로, 상태 관리를 더욱 간단하게 하기 위해 설계된 클래스이다.
- UI컴포넌트는 **Cubit**에게 현재상태를 전달하고 **Cubit**은 상태변화를 UI컴포넌트에게 방출한다.

<br/>

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);
}
```
- `Cubit`을 만들때는 이 큐빗이 어떤 타입을 관리할지 제너릭으로 비워둔 칸에 써주어야한다.
- 만약 좀더 복잡한 타입을 다룰 때는 커스텀 클래스를 넣어주자
- 아래 `super`에는 초기값을 넣어주는 방식이다. int값을 다루기로 선언했기 때문에 초기값으로 0을 넣어줄 수 있다.

<br/>

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit(int initialState) : super(initialState);
}

final cubitA = CounterCubit(0); // state starts at 0
final cubitB = CounterCubit(10); // state starts at 10
```
- 위와 같이 지금당장 초기값을 넣어주기 싫다면 주입을 통해 넣어줄 수도 있다.

<br/>

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);

  void increment() => emit(state + 1);
}
```








