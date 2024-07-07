공식문서 링크 <br/>
[https://dart.dev/language/isolates](https://dart.dev/language/isolates)

<br/>

## Isolates

- `isolate` 를 반드시 사용해야한다는 룰은 없지만 공식문서에서 아래 상황의 경우 `isolate` 사용 권고
1. Json 파싱
2. 사진, 오디오, 비디오 파일 압축
3. 오디오 -> 비디오 파일로 전환
4. 대용량 파일 검색
5. 데이터 베이스 같은 I/O 작업
6. 대용량 네트워크 요청

<br/>

#

### Implementing a simple worker isolate
- `isolate` 을 간단하게 써보기
1. `isolate` 생성
2. 만들어진 곳에 함수 돌리기
3. 결과 갓챠하기
4. 메인 isolate에 반환하기
5. 작업끝나면 `isolate` 닫기
6. 컴플리션 이벤트나 에러이벤트 메인이 처리하기

<br/>

#

### `isolate` 만들어보기

```dart
const String filename = 'with_keys.json';

void main() async {
  // Read some data.
  final jsonData = await Isolate.run(_readAndParseJson);

  // Use that data.
  print('Number of JSON keys: ${jsonData.length}');
}

Future<Map<String, dynamic>> _readAndParseJson() async {
  final fileData = await File(filename).readAsString();
  final jsonData = jsonDecode(fileData) as Map<String, dynamic>;
  return jsonData;
}
```
- `run()` 메서드를 통해 백그라운드 스레드를 만들어준다.
- 만든 아이솔레이트에 비동기 함수를 넣어준다.

<br/>

#

### 함수 따로만들지않고 클로저로 직접 넣기

```dart
const String filename = 'with_keys.json';

void main() async {
  // Read some data.
  final jsonData = await Isolate.run(() async {
    final fileData = await File(filename).readAsString();
    final jsonData = jsonDecode(fileData) as Map<String, dynamic>;
    return jsonData;
  });

  // Use that data.
  print('Number of JSON keys: ${jsonData.length}');
}
```
- 클로저를 쓰면 장점이 마치 일반 코드처럼 보이지만 실제로는 병렬로 실행되는 코드를 작성할 수 있다.

<br/>

#

### 만든 isolate에 이벤트 여러개 보내기 (feat. ports)

<img width="450" alt="image" src="https://github.com/suojae3/flutter_dart_docs/assets/126137760/7e236cbd-a7c1-41f5-a880-14b3a86892a0">

<br/>
<br/>

```dart
void main() async {
  // 1. 메인 이솔레이트에서 ReceivePort 생성
  ReceivePort mainReceivePort = ReceivePort();

  // 2. Isolate.spawn()을 사용하여 워커 이솔레이트 생성
  Isolate workerIsolate = await Isolate.spawn(workerIsolateFunction, mainReceivePort.sendPort);

  // 3. 메인 이솔레이트의 SendPort를 워커 이솔레이트로 전달
  SendPort mainSendPort = mainReceivePort.sendPort;

  // 6. 메인 이솔레이트에서 메시지 처리
  mainReceivePort.listen((message) {
    if (message is SendPort) {
      // 워커 이솔레이트로부터 받은 SendPort
      SendPort workerSendPort = message;
      // 워커 이솔레이트로 메시지 전송
      workerSendPort.send('Hello from main isolate!');
    } else {
      // 워커 이솔레이트로부터 받은 메시지 출력
      print('Message from worker isolate: $message');
      // 이솔레이트 종료
      mainReceivePort.close();
      workerIsolate.kill(priority: Isolate.immediate);
    }
  });
}

void workerIsolateFunction(SendPort mainSendPort) async {
  // 4. 워커 이솔레이트에서 ReceivePort 생성
  ReceivePort workerReceivePort = ReceivePort();

  // 5. 워커 이솔레이트의 SendPort를 메인 이솔레이트로 전달
  mainSendPort.send(workerReceivePort.sendPort);

  // 워커 이솔레이트에서 메시지 처리
  await for (var message in workerReceivePort) {
    print('Message from main isolate: $message');
    workerReceivePort.close();
    // 메인 이솔레이트로 메시지 전송
    mainSendPort.send('Hello from worker isolate!');
  }
}
```



<br/>


<img width="450" alt="image" src="https://github.com/suojae3/flutter_dart_docs/assets/126137760/e54518fc-9953-4f65-8d3c-19cb33846420">

<br/>
<br/>

```dart
import 'dart:isolate';

void main() async {
  // 1. 메인 이솔레이트에서 ReceivePort 생성
  ReceivePort mainReceivePort = ReceivePort();

  // 2. Isolate.spawn()을 사용하여 워커 이솔레이트 생성
  Isolate.spawn(workerIsolateFunction, mainReceivePort.sendPort);

  // 4. 메인 이솔레이트에서 메시지 처리
  mainReceivePort.listen((message) {
    if (message is SendPort) {
      // 워커 이솔레이트로부터 받은 SendPort
      SendPort workerSendPort = message;

      // 6. 워커 이솔레이트로 메시지 전송
      workerSendPort.send(10); // 예: 10을 계산 작업으로 전송
    } else {
      // 8. 워커 이솔레이트로부터 받은 메시지 출력
      print('Result from worker isolate: $message');
      mainReceivePort.close(); // 작업 완료 후 ReceivePort 닫기
    }
  });
}

void workerIsolateFunction(SendPort mainSendPort) {
  // 3. 워커 이솔레이트에서 ReceivePort 생성
  ReceivePort workerReceivePort = ReceivePort();

  // 5. 워커 이솔레이트의 SendPort를 메인 이솔레이트로 전달
  mainSendPort.send(workerReceivePort.sendPort);

  // 7. 워커 이솔레이트에서 메시지 처리
  workerReceivePort.listen((message) {
    if (message is int) {
      int result = message * 2; // 예: 간단한 계산 작업 (입력값을 두 배로)

      // 결과를 메인 이솔레이트로 전송
      mainSendPort.send(result);

      workerReceivePort.close(); // 작업 완료 후 ReceivePort 닫기
    }
  });
}
```




