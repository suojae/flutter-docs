#### 공식문서 링크<br/>
[https://dart.dev/language/methods](https://dart.dev/language/methods)


<br/>
<br/>

## Methods

<br/>

### Instance method

```dart
import 'dart:math';

class Point {
  final double x;
  final double y;

  // Sets the x and y instance variables
  // before the constructor body runs.
  Point(this.x, this.y);

  double distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

- 일반적으로 클래스의 인스턴스를 통해 호출되는 메서드를 인스턴스 메서드라고 부른다.
- 같은 클래스내 프로퍼티에 접근할 수 있다.

<br/>

### Operator 

```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  @override
  bool operator ==(Object other) =>
      other is Vector && x == other.x && y == other.y;

  @override
  int get hashCode => Object.hash(x, y);
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```
- 본래 Vector 객체는 +,-연산을 할 수 없지만 위와같이 재정의를 통해서 연산할 수 있다.
- `==` operator를 재정의할 때는 반드시 `hash code` 도 재정의 해야함을 기억하자. 안그러면 같은 value라도 메모리 주소로 판단해서 같지 않다고 판단한다.

<br/>

### Getters and setters

```dart
class Rectangle {
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  double get right => left + width;
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

- Getter로 선언된 `right`은 사각형의 오른쪽 끝 x좌표, `bottom`은 사각형 아래쪽 끝의 y좌표를 나타낸다.
- Setter에는 right값이 들어오면 왼쪽값을 조정해주는 식이 들어간다(그래야 사각형이 제대로 그려지니까).
- 예시에서 현재 right는 23인데 right가 12로 되면서 left는 -8이 되며 사각형이 왼쪽으로 움직여진다.

<br/>

### Abstract methods

```dart
abstract class Doer {
  // Define instance variables and methods...

  void doSomething(); // Define an abstract method.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // Provide an implementation, so the method is not abstract here...
  }
}
```
- 추상화 메서드는 method body대신 `;`로 끝맺는다.

