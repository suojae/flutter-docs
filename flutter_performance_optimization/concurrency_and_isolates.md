공식문서 링크<br/>
[https://docs.flutter.dev/perf/isolates](https://docs.flutter.dev/perf/isolates)

<br/>

## Concurrency and isolates

- 메인함수 자체가 싱글 isolate이기 때문에 모든 다트 코드는 `isolate` 한 환경(독립적인 메모리 공간)에서 동작한다.
- 상태를 직접적으로 공유하지 않고 오직 메세지를 통해 간접적으로 공유한다.
- `isolate`는 각자의 
