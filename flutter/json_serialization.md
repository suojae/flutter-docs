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




