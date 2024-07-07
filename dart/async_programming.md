
## Asynchronous porgramming: futures, async, await

### 비동기 코드가 중요한 이유
- 다트에서 비동기 계산 결과는 `Future`/`Stream` 타입으로 나온다.

```dart
String createOrderMessage() {
  var order = fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // Imagine that this function is more complex and slow.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() {
  print(createOrderMessage());
}

// Your order is: Instance of '_Future<String>'
```
- 예제코드는 예상했던 라지 라떼가 안나오고 에러가 나는 것을 확인할 수 있다. 
- 이유는 `createOrderMessage()` 가 실행될 때 `fetchUserOrder()`의 딜레이를 기다리지 않고 바로 결과를 리턴하기 때문이다.
- 비동기 함수에 `async`/`await`을 붙이지 않아서이다. 

```dart
// This example shows how *not* to write asynchronous Dart code.

Future createOrderMessage() async {
  var order = await fetchUserOrder();
  return 'Your order is: $order';
}

Future<String> fetchUserOrder() =>
    // Imagine that this function is more complex and slow.
    Future.delayed(
      const Duration(seconds: 2),
      () => 'Large Latte',
    );

void main() async {
  print(await createOrderMessage());
}
```
- 위와 같이 async/await 키워드를 통해 결과값을 기다려주어 원하는 라지 라떼를 출력할 수 있다.

<br/>

#### 💡 용어정리
- **synchronous operation**: 동기식 작업은 자기작업이 끝나기 전까지 다른작업을 block시킨다.
- **synchronous function**: 동기식 함수는 동기식 작업방식으로만 작동한다. (즉 다른 함수를 블락시킨다) 
- **synchronous operation**: 한 번 작업이 생성되면 끝나기 전까지 다른 작업도 진행시킨다 (non-block)
- **asynchronous function**:  비동기 함수는 적어도 하나의 비동기 작업을 수행하며, 동시에 동기 작업도 수행할 수 있다.

<br/>

#

### `Future` 란?
- `future`(소문자)는 `Future` 클래스의 인스턴스로 비동기 작업의 결과물이다.
- `future`는 두가지 상태를 가지고 있다. - uncompletd, completed.

#### Uncompleted

- 비동기함수를 호출하면 먼저 Uncompleted future를 리턴한다.
- 이 상태는 비동기 작업의 결과를 기다리는 상태이다.

#### Completed
```dart
Future<void> fetchUserOrder() {
  // Imagine that this function is fetching user info from another service or database.
  return Future.delayed(const Duration(seconds: 2), () => print('Large Latte'));
}

void main() {
  fetchUserOrder();
  print('Fetching user order...');
}

// Fetching user order...
// Large Latte
```

```dart
Future<void> fetchUserOrder() {
  // Imagine that this function is fetching user info but encounters a bug.
  return Future.delayed(
    const Duration(seconds: 2),
    () => throw Exception('Logout failed: user ID is invalid'),
  );
}

void main() {
  fetchUserOrder();
  print('Fetching user order...');
}

// Fetching user order...
// 에러처리
```

- 비동기 함수작업이 완료시 성공하면 지정해두었던 타입, 실패시 에러가 발생한다.

<br/>

#

### Working with futures: async and await

- 비동기 함수를 구현할 때는 반드시 함수 바디 앞에 `async` 키워드가 있어야한다.
- `await`키워드는 `async` 함수 안에서만 작동한다.

<br/>

#### 💡 용어정리













