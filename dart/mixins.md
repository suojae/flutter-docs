공식문서 링크<br/>
[https://dart.dev/language/mixins](https://dart.dev/language/mixins)

<br/>

## Mixins

<br/>

선언
```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

채택
```dart
class Musician extends Performer with Musical {
  // ···
}

class Maestro extends Person with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
```

- `Mixin`은 클래스 계층 어디서든 코드를 재사용할 수 있는 방법이다.
- `mixin`은 `with` 키워드를 통해 사용할 수 있다.
- `mixin`은 `extend` 할 수 없고 생성자 함수도 만들 수 없다.
