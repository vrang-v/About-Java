Optional 올바르게 사용하기
===

### 1. ```Optional```변수에 절대로 ```null```을 할당하지 말자

#### 잘못된 방법 : 직접적인 ```null``` 반환
```java
public Optional<User> findUserById(String userId)
{
    // find user from db
    if (user == null) {
        return null;
    }
    return Optional.of(user);
}
```

#### 올바른 방법 : ```empty( )```를 사용하여 ```null```을 감싸는 ```Optional```객체 반환
```java
public Optional<User> findUserById(String userId)
{
    // find user from db
    if (user == null) {
        return Optional.empty( );
    }
    return Optional.of(user);
}
```

### 2. ```Optional.get( )``` 사용을 피하되 사용시에는 값의 존재여부를 꼭 확인하자
#### 잘못된 방법 : 값의 존재 여부를 확인하지 않고 ```get( )```을 사용
```java
Optional<User> user = userService.findUserById("test");
String         name = user.get( ).getName( );
```

#### 추천하지 않는 방법 : ```isPresent( )``` 및 ```isEmpty( )```와 ```get( )```사용
```java
Optional<User> user = userService.findUserById("test");
if (user.isPresent( )) {
    String name = user.get( ).getName( );
}
```

#### 추천하는 방법 : ```orElse```, ```orElseGet```, ```orElseThrow```를 사용
```java
User user = optional.orElse(defaultUser);
User user = optional.orElseGet(User::new);
User user = optional.orElseThrow(NullPointerException::new);
```

### 새로운 객체 생성시에 orElse와 orElseGet의 차이점
```java
User user = optional.orElse(new User( ));
User user = optional.orElseGet(User::new);
```
위 상황에서 ```orElse( )```는 ```Optional```객체가 비어있더라도 생성자가 호출되지만, 

```orElseGet( )```은 ```Optional```이 비어있는 경우에만 람다식이 실행되기때문에 생성자가 호출되지 않는다.

그러므로 값이 비싼 객체를 생성해야할 경우 ```orElseGet( )```을 사용하는 것이 바람직하다.
### 3. 값이 있는 경우에 값을 이용하는 동작만이 필요하다면 ```ifPresent( )```를 활용하자
```java
userService.findUserById("test")
           .ifPresent(user -> System.out.println(user.getName( )));
```

### 4. ```Optional```을 반환값을 제외하고 사용하지 말자  
불필요하게 ```Optional```을 사용하는 경우를 줄이자. 과용은 언제나 해롭다.

```Optional```을 사용하지 않고 가독성이 더 좋은 코드를 충분히 만들어낼 수 있다.
- 클래스 필드
```java
class User
{
    Optional<String> name; // X
    String name;           // O
}
```
- 메서드 매개변수
```java
public User findUserById(Optional<String> Id) { ... }  // X
public User findUserById(String Id) { ... }            // O
```
- 비어있는 집합 자료형  
```empty```상태의 ```Optional```이 아니라 비어있는 집합 자체를 반환하면 된다.
```java
public Optional<List<User>> findUsersById(String id) // X
{   
    // do something ...
    if (userList == null) {
        return Optional.empty( ); 
    }
}

public List<User> findUserById(String id) // O
{
    // do something ...
    if (userList == null) {
        return new ArrayList<User>( );
    }
}
```

### 5. 원시타입에는 특화된 ```Optional```타입을 사용하자
```Optional```을 사용할때에 비해서 오버헤드가 줄어드는 장점이 있다.
```java
OptionalInt    optionalInt    = OptionalInt.of(1);
OptionalLong   optionalLong   = OptionalLong.of(2L);
OptionalDouble optionalDouble = OptionalDouble.empty( );
```

### 6. ```Optional``` 내부의 값을 비교할때는 값을 꺼낼 필요 없이 ```equals( )```를 사용하자
```Optional```의 동치성은 내부의 값이 결정하도록 구현되어 있다.
```java
Optional<User> user1;
Optional<User> user2;
```
#### 잘못된 방법
```java
user1.get( ).equals(user2.get( ));
```
#### 올바른 방법
```java
user1.equals(user2);
```

### 7. ```map( )```, ```flatMap( )```, ```filter( )```를 사용하여 함수형 스타일로 코드를 작성해보자
```Optional```을 사용할 때 함수형 프로그래밍을 사용하면 가독성이 좋아지는 경우가 많다.