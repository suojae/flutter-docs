### 공식문서 링크
[https://docs.flutter.dev/data-and-backend/serialization/json]()

<br/>

## JSON and serialization

<br/>

### Which JSON serialization method is right for me?
직렬화 방법에는 크게 두 가지 방법이 있다. 수동직렬화와 코드젠 기반 자동 직렬화이다.
1. **수동직렬화**
```dart
import 'dart:convert';

class User {
  final String name;
  final String email;

  User(this.name, this.email);

  // JSON -> 객체 변환
  User.fromJson(Map<String, dynamic> json)
      : name = json['name'],
        email = json['email'];

  // 객체 -> JSON 변환
  Map<String, dynamic> toJson() => {
        'name': name,
        'email': email,
      };
}

void main() {
  String jsonString = '{"name": "John Smith", "email": "john@example.com"}';
  Map<String, dynamic> userMap = jsonDecode(jsonString);
  User user = User.fromJson(userMap);

  print(user.name); // John Smith

  String encodedJson = jsonEncode(user.toJson());
  print(encodedJson); // {"name": "John Smith", "email": "john@example.com"}
}
```
- `dart:convert` 패키지의 `jsonDecode()`와 `jsonEncode()`를 사용
- JSON 데이터를 `Map<String, dynamic>` 형태로 변환한 후 직접 파싱
- JSON 키를 직접 입력해야 하므로 오타가 발생할 가능성이 있음
- JSON 데이터가 복잡해질수록 코드가 길어지고 유지보수가 어려워짐


2. **코드 젠 기반 자동 직렬화**
```dart
import 'package:json_annotation/json_annotation.dart';

part 'user.g.dart'; // 자동 생성 파일

@JsonSerializable()
class User {
  final String name;
  final String email;

  User(this.name, this.email);

  // JSON -> 객체 변환
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);

  // 객체 -> JSON 변환
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```
- `json_serializable` 패키지를 사용하여 자동으로 JSON 직렬화 코드 생성
- `build_runner`를 실행하여 자동으로 `.g.dart` 파일을 생성
- JSON 키를 직접 입력할 필요가 없어 오타를 방지할 수 있음
- 타입 안전성을 보장하며, JSON 필드를 잘못 참조하면 컴파일 타임 오류 발생
- 코드젠하기가 매우 귀찮다는 단점이 있음
- 공식문서에서 작은 프로젝트는 메뉴얼한 직렬화, 대형 프로젝트는 코드젠을 통한 직렬화 권고

<br/>

### Is there a GSON/Jackson/Moshi equivalent in Flutter?

```dart
import 'package:json_annotation/json_annotation.dart';

part 'user.g.dart';

@JsonSerializable()
class User {
  final String name;
  final String email;

  User(this.name, this.email);

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```
- Flutter에는 GSON/Jackson/Moshi 같은 런타임 기반 JSON 변환 라이브러리가 없다.
- 그 이유는 Flutter가 런타임 리플렉션(Reflection)을 지원하지 않기 때문이며,대신 컴파일 타임(Code Generation) 방식으로 이를 해결한다.
- Flutter에서는 GSON/Jackson/Moshi 같은 런타임 리플렉션 대신, 컴파일 타임에 JSON 직렬화 코드를 자동으로 생성하는 방식을 사용한다.

<br/>

### Serializing JSON manually using dart:convert

```dart
// inline 방식 직렬화/역직렬화
import 'dart:convert';

void main() {
  // JSON 문자열
  String jsonString = '{"name": "John Smith", "email": "john@example.com"}';

  // 1️⃣ JSON을 Map<String, dynamic>으로 변환
  Map<String, dynamic> user = jsonDecode(jsonString);

  // 2️⃣ Map에서 데이터 추출
  print('이름: ${user['name']}');
  print('이메일: ${user['email']}');

  // 3️⃣ 다시 JSON 문자열로 변환
  String encodedJson = jsonEncode(user);
  print('JSON 문자열: $encodedJson');
}
```

- `@JsonSerializable()` → 이 클래스가 JSON 직렬화/역직렬화 대상임을 나타냄.
- `factory User.fromJson()` → JSON을 User 객체로 변환하는 코드가 자동 생성됨.
- `Map<String, dynamic> toJson()` → User 객체를 JSON으로 변환하는 코드가 자동 생성됨.

<br/>

```dart
// 모델 클래스 방식 직렬화/역직렬화
import 'dart:convert';

class User {
  final String name;
  final String email;

  User(this.name, this.email);

  // JSON -> Dart 객체 변환 (fromJson)
  factory User.fromJson(Map<String, dynamic> json) {
    return User(
      json['name'] as String, 
      json['email'] as String
    );
  }

  // Dart 객체 -> JSON 변환 (toJson)
  Map<String, dynamic> toJson() {
    return {
      'name': name,
      'email': email,
    };
  }
}

void main() {
  String jsonString = '{"name": "John Smith", "email": "john@example.com"}';

  // 1️⃣ JSON -> Dart 객체 변환
  Map<String, dynamic> userMap = jsonDecode(jsonString);
  User user = User.fromJson(userMap);

  // 2️⃣ 데이터 출력
  print('이름: ${user.name}');
  print('이메일: ${user.email}');

  // 3️⃣ Dart 객체 -> JSON 변환
  String encodedJson = jsonEncode(user.toJson());
  print('JSON 문자열: $encodedJson');
}
```
- Flutter에서 JSON을 다루는 가장 기본적인 방법은 dart:convert 라이브러리를 사용하는 것이다.
- `dart:convert`는 Flutter에 기본적으로 포함된 패키지로, 외부 라이브러리를 설치할 필요 없이 JSON을 인코딩(Encoding) 및 디코딩(Decoding)할 수 있다.

<br/>


### Creating model classes the json_serializable way

```dart
import 'package:json_annotation/json_annotation.dart';

// 자동 생성된 JSON 변환 파일과 연결
part 'user.g.dart';

@JsonSerializable()
class User {
  User(this.name, this.email);

  String name;
  String email;

  // JSON → 객체 변환 (fromJson)
  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);

  // 객체 → JSON 변환 (toJson)
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```


```dart
import 'package:json_annotation/json_annotation.dart';

part 'user.g.dart';

@JsonSerializable()
class User {
  @JsonKey(name: 'full_name')  // JSON 필드명과 Dart 변수명 다름
  final String name;

  @JsonKey(name: 'user_email')
  final String email;

  User(this.name, this.email);

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```

- API의 JSON 키와 Dart 클래스 필드 이름이 다를 경우, @JsonKey(name:)을 사용하면 자동 매핑이 가능하다.
- 위처럼 API의 JSON 키가 user_email인데, Dart에서는 email을 사용하고 싶다면 @JsonKey(name:)을 활용하면 된다.

<br/>

```dart
class User {
  @JsonKey(defaultValue: false) // 기본값 지정
  final bool isAdult;

  @JsonKey(required: true) // JSON에 반드시 포함되어야 함
  final String id;

  @JsonKey(ignore: true) // JSON 변환에서 제외
  final String verificationCode;

  User(this.isAdult, this.id, this.verificationCode);

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```
- 서버에서 데이터가 확실하지 않을 때, @JsonKey를 활용하면 데이터 검증 및 예외 처리도 가능하다.
- `@JsonKey(defaultValue: false)` → isAdult 값이 없거나 null이면 기본값 false 적용.
- `@JsonKey(required: true)` → JSON에 id 필드가 없으면 예외 발생.
- `@JsonKey(ignore: true)` → verificationCode 필드는 JSON 변환에서 제외됨.

<br/>


```dart
@JsonSerializable(fieldRename: FieldRename.snake)
class User {
  String firstName;
  String lastName;

  User(this.firstName, this.lastName);

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}
```
- `@JsonSerializable(fieldRename: FieldRename.snake)`을 사용하면 자동 변환도 할 수 있다.
- 코드에서 API의 JSON 키가 `first_name`, `last_name`처럼 snake_case로 오면, 자동으로 Dart의 `firstName`, `lastName`으로 매핑된다.

<br/>

### Generating code for nested classes

**📌 explicitToJson: true 없이 실행하면?**
```dart
@JsonSerializable() // ❌ 기본 설정 (explicitToJson 없음)
class User {
  User(this.name, this.address);

  String name;
  Address address;

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this); // ❌ Address 객체 변환 문제 발생
}

// ❌ Address 객체가 toString()으로 변환되어 JSON이 제대로 출력되지 않음! 🚨
{
  "name": "John",
  "address": "Instance of 'Address'"
}
```

<br/>

**📌 올바른 User 클래스 정의 (explicitToJson: true 추가)**
```dart
import 'package:json_annotation/json_annotation.dart';
import 'address.dart';

part 'user.g.dart';

@JsonSerializable(explicitToJson: true) // ✅ 중첩된 객체도 JSON으로 변환하도록 설정
class User {
  User(this.name, this.address);

  String name;
  Address address;

  factory User.fromJson(Map<String, dynamic> json) => _$UserFromJson(json);
  Map<String, dynamic> toJson() => _$UserToJson(this);
}

// 📌 출력 결과 (문제 해결됨!)
{
  "name": "John",
  "address": {
    "street": "My st.",
    "city": "New York"
  }
}
```

- explicitToJson: true 옵션을 사용하면, 중첩된 객체의 toJson() 메서드가 자동으로 호출된다
- 중첩된 객체를 자동 변환하면 불필요한 JSON 변환 코드가 많아져서 성능이 저하될 수 있다.
- 따라서, Flutter는 기본적으로 객체를 자동으로 JSON으로 변환하지 않도록 설정하고, 개발자가 필요할 때 explicitToJson: true를 추가하도록 설계했다.




