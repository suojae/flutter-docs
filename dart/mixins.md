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

- `mixin`은 다양한 계층 클래스에서 코드를 재사용하기 위해서 정의하는 타입이다.
- `mixin`을 사용할 때는 `with` 키워드를 붙여서 사용한다. 
- `mixin`은 `extend` 할 수 없고 생성자 함수도 만들 수 없다.


<br/>

### Specify members a mixin can call on itself

```dart
mixin Musician {
  void playInstrument(String instrumentName); // Abstract method.

  void playPiano() {
    playInstrument('Piano');
  }
  void playFlute() {
    playInstrument('Flute');
  }
}

class Virtuoso with Musician { 
  void playInstrument(String instrumentName) { // Subclass must define.
    print('Plays the $instrumentName beautifully');
  }  
}
```

- `mixin`은 생성자가 없기 때문에 파라미터를 가지면 반드시 채택한 클래스에서 구체적으로 구현해줄 수 밖에 없다. 따라서 믹스인 그 자체로 완전한 기능을 제공하지 못하고 `mixin`이 채택될 클래스에서 메서드를 정의해주어야한다.

<br/>

```dart
/// Can be applied to any type with a [name] property and provides an
/// implementation of [hashCode] and operator `==` in terms of it.
mixin NameIdentity {
  String get name;

  int get hashCode => name.hashCode;
  bool operator ==(other) => other is NameIdentity && name == other.name;
}

class Person with NameIdentity {
  final String name;

  Person(this.name);
}
```

- 프로퍼티를 정의해줄 때도 메서드와 마찬가지이다. `mixin`내부에서는 절대 생성자를 사용하지 못하기 때문에 외부에서 프로퍼티 값을 얻고싶다면 위와같이 get 읽기 속성으로 선언뒤에 채택하는 곳에서 넣어준다. 

<br/>

### Implement an interface

```dart
/// Can be applied to any type with a [name] property and provides an
/// implementation of [hashCode] and operator `==` in terms of it.
mixin NameIdentity {
  String get name;

  int get hashCode => name.hashCode;
  bool operator ==(other) => other is NameIdentity && name == other.name;
}

class Person with NameIdentity {
  final String name;

  Person(this.name);
}
```

- `mixin`도 인터페이스 클래스를 채택할 수 있다. 이때는 `with`가 아닌 `implements` 키워드를 사용한다는 것을 기억하자.
- 여기서도 알 수 있듯이 `mixin`은 인터페이스가 아니다!!! 주의하자..

<br/>

### Use the on clause to declare a superclass

```dart
class Musician {
  musicianMethod() {
    print('Playing music!');
  }
}

mixin MusicalPerformer on Musician {
  perfomerMethod() {
    print('Performing music!');
    super.musicianMethod();
  }
}

class SingerDancer extends Musician with MusicalPerformer { }

main() {
  SingerDance().performerMethod();
}
```
- `mixin`은 놀랍게도 클래스도 상속받는다. 이때는 `on`키워드를 사용한다. 
- 상속받은 메서드와 프로퍼티를 정의해주어야하며 내부에서 `super`호출을 할 수 있다.

<br/>

### class, mixin, or mixin class?

```dart
mixin class Musician {
  // ...
}

class Novice with Musician { // Use Musician as a mixin
  // ...
}

class Novice extends Musician { // Use Musician as a class
  // ...
}
```

- 이럴수가. `mixin class` 를 사용하면 위와 같이 `mixin`, `class` 두 가지 용도로 모두 쓸 수 있다. 
- 믹스인은 인터페이스가 아니다, 믹스인은 독립적으로 인스턴스화할 수 없기 때문이다.