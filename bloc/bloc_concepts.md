공식문서 링크 <br/>
[https://bloclibrary.dev/bloc-concepts/](https://bloclibrary.dev/bloc-concepts/)

## Bloc Concepts


### Streams

<br/>

#

### Cubit

<img width="400" alt="image" src="https://github.com/suojae3/flutter_dart_docs/assets/126137760/d0d13c9a-e8c7-4268-bdbf-259747ea4c7d">

<br/>
<br/>

- **Cubit**은 Bloc 라이브러리의 일부로, 상태 관리를 더욱 간단하게 하기 위해 설계된 클래스이다.
- UI컴포넌트는 **Cubit**에게 현재상태를 전달하고 **Cubit**은 상태변화를 전달하는 UI컴포넌트에게 전달한다.

<br/>

#### Creating a Cubit

```dart
class CounterCubit extends Cubit<int> {
  CounterCubit() : super(0);
}
```

