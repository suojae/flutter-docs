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

- Cubit클래스에서는 state를 제공해준다. 이때 오직 `emit()` 메서드를 통해서만 state를 변화시킬 수 있다.
- emit은 오직 Cubit클래스 내부에서만 이용할 수 있다.

<br/>

```dart
void main() {
  final cubit = CounterCubit();
  print(cubit.state); // 0
  cubit.increment();
  print(cubit.state); // 1
  cubit.close();
}
```
- 즉 이와 같이 외부에서는 상태를 직접 접근하여 쓰지 못하고 cubit 클래스에서 선언해둔 메서드를 통해 간접적으로 상태변환을 바꾸고 직접적인 상태접근은 읽기작업만 허용된다.
- `Cubit`이 방출해주는 값을 스트림의 반응형으로 받고 싶다면 `listen` 메서드를 이용한다.
- `Cubit`의 `emit`메서드는 기본적으로 스트림을 방출하기 때문에 안쓴다면 `close()`를 통해 메모리 해제가 필수다. 안그러면 메모리 누수 발생한다.
- `cancel`은 구독 취소기능을 수행한다. 이때 스트림은 여전히 살려둔채 구독자입장에서 더이상 값을 안받는다고 취소할 뿐이다.

<br/>

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);

  void increment() => emit(state + 1);

  @override
  void onChange(Change<int> change) {
    super.onChange(change);
    print(change);
  }
}
```
- 외부 호출을 통한 상태변화를 큐빗 내부에서도 옵저빙해줄 수 있다. 큐빗의 `onChange()` 메서드 재정의를 이용하자

<br/>

```dart
class SimpleBlocObserver extends BlocObserver {
  @override
  void onChange(BlocBase bloc, Change change) {
    super.onChange(bloc, change);
    print('${bloc.runtimeType} $change');
  }
}

void main() {
  Bloc.observer = SimpleBlocObserver();
  CounterCubit()
    ..increment()
    ..close();
}

// CounterCubit Change { currentState: 0, nextState: 1 }
// Change { currentState: 0, nextState: 1 }
```
- `BlocObserver`를 이용하면 모든 상태 변화를 한 곳에서 관찰하거나 에러처리를 해줄 수 있다.

<br/>

#### Cubit Error handling

```dart
class MyBloc extends Bloc<MyEvent, MyState> {
  MyBloc() : super(MyInitialState());

  @override
  Stream<MyState> mapEventToState(MyEvent event) async* {
    if (event is MyErrorEvent) {
      try {
        // 여기서 에러 발생시킴
        throw Exception('This is a test error');
      } catch (error) {
        // 에러 발생 시 addError 호출
        addError(error, StackTrace.current);
      }
    }
  }

  @override
  void onError(Object error, StackTrace stackTrace) {
    super.onError(error, stackTrace);
    // 에러 처리 로직: 로그 출력
    print('Error: $error'); // 로그: Error: Exception: This is a test error
    print('Stack Trace: $stackTrace'); // 로그: Stack Trace: <stack trace details>

    // 에러 상태 전파 가능
    // emit(MyErrorState(error.toString()));
  }
}
```

- 모든 큐빗 객체는 `addError` 메서드를 가지고 있고 에러가 발생할 때 이 메서드를 실행시킬 수 있다.
- `addError()` 로 던진 에러이벤트는 `onError()` 함수를 재정의해서 받을 수 있다. 

<br/>
<br/>

#

### Bloc

<img width="400" alt="image" src="https://github.com/suojae3/flutter_dart_docs/assets/126137760/fa560e98-68c1-4076-b43f-73ce2d3d5085">

<br/>
<br/>

- `Bloc`은 큐빗보다 좀 더 복잡한 상태관리를 위한 객체이다.








