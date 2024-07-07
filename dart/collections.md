
## Collections

<br/>

### List 

```dart
var list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);

list[1] = 1;
assert(list[1] == 1);
```
- `list` 는 배열에 해당하는 타입이다.

<br/>

```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1; // This line will cause an error.
```
- 위 코드를 보면 알 수 있듯이 `const` 를 미리 작성해줌으로써 값을 컴파일 타임에 고정시킬 수 있다.

<br/>

#

### Sets 

```dart
var names = <String>{};
// Set<String> names = {}; // This works, too.
// var names = {}; // Creates a map, not a set.
```
- 집합에 해당하는 `Set`은 위 코드와 같이 선언할 수 있다.
- 주의할 점은 그냥 `{}`으로 선언할 경우 집합이 아닌 딕셔너리(`map`)가 만들어진다는 점
- 만약 그냥 `{}` 으로 선언하면 `Map<dynamic, dynamic>` 타입으로 선언된다.

<br/>

```dart
void main() {
  var elements = <String>{};

  // 단일 요소 추가
  elements.add('fluorine');
  print(elements); // {fluorine}

  // 여러 요소 추가
  var halogens = {'chlorine', 'fluorine', 'bromine', 'iodine'};
  elements.addAll(halogens);
  print(elements); // {fluorine, chlorine, bromine, iodine}

  // 중복 요소 추가 시도
  elements.add('fluorine');
  print(elements); // {fluorine, chlorine, bromine, iodine}
}

var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);
assert(elements.length == 4);
```
- `Set` 에 단일요소를 추가할 땐 `add` 메서드를, 다수의 요소를 추가할 때는 `addAll` 메서드를 사용한다 
- 또한 `length` 메서드를 통해 몇개의 요소가 있는지 확인할 수 있다.

<br/>

```dart
final constantSet = const {
  'fluorine',
  'chlorine',
  'bromine',
  'iodine',
  'astatine',
};
// constantSet.add('helium'); // This line will cause an error.
```

- `list`와 마찬가지로 `Set`도 값을 컴파일 타임에 위와 같이 고정시킬 수 있다.



<br/>

#

### Maps

```dart
var gifts = Map<String, String>();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = Map<int, String>();
nobleGases[2] = 'helium';
nobleGases[10] = 'neon';
nobleGases[18] = 'argon';

var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
```
- dictionary에 해당되는 `map`은 위 코드와 같이 선언할 수 있다.
- 주의할 점은 선언할 땐 `{}`로 선언하더라도 값을 빼올 땐 `[]` 를 사용해야한다.
- Swift와 다른점은 딕셔너리 value값으로 하나밖에 못가진다. 만약 값을 여러개 가지고 싶다면 value타입을 또다른 collecions 타입으로 선언해주어야한다.


<br/>

```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);

final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

- `Set` 과 마찬가지로 length를 통해 key-value 페어의 갯수를 알 수 있다.
- `Set` 과 마찬가지로 `const`를 통해 값을 컴파일타임에 고정시킬 수 있다.
- 

<br/>

#

### Operators

```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);

var list2 = [0, ...?list];
assert(list2.length == 1);
```
- 다트는 **Spread Operator**(...) 를 지원한다. 위와같이 ... 을 통해 배열을 선언해줄 수 있다. 
- 만약 ... 다음에 올 값이 `null` 일 수도 있다면 **null-aware spread operator**(...?) 를 써줄 수 있다.

<br/>

```dart
var nav = ['Home', 'Furniture', 'Plants', if (promoActive) 'Outlet'];

var listOfInts = [1, 2, 3];
var listOfStrings = ['#0', for (var i in listOfInts) '#$i'];
assert(listOfStrings[1] == '#1');
```
- 다트는 컬렉션 타입 내부에서도 위와같이 control-flow 연산자를 사용할 수 있다.
