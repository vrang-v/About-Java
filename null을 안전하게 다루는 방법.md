Java에서 null을 안전하게 다루는 방법
===
null의 문제점
---
특별한 참조 값이 없음을 나타내기 위해 null을 도입하였고 이 값을 사용하면 오류를 발생하도록 설계되었다.

null을 사용하는 경우 의미가 모호해 지는 경우가 있다.

- 초기화되지 않은 상태


- 정의되지 않은 상태


- 의도적으로 값을 비워놓은 상태

위와 같은 상태에 대해서 명확하게 구분하기 힘들다.

---
assertion (단정문)
---
```assert``` 키워드를 이용한 단정문은 ```JDK 1.4``` 부터 지원되는 기능이다.

```java
assert booleanExpression;

assert booleanExpression : description;
```
위 ```booleanExpression```이 참일 경우 **Pass**, 거짓일 경우 **AssertionError**를 발생시킨다.

### 단정문의 한계점
자바 실행시에 -ea 옵션을 설정해주어야 하며, default 실행 환경에서 단정문은 모두 제외된다. 그러므로 공개 메서드에서 사용하기 힘들다.

```java
java -ea
```

Objects 유틸리티 클래스
---
```Java 8```와 ```Java 9 ```에서 추가된 유틸리티 메서드를 이용하여 null 체크를 할 수 있다.
``` java
Java 8

Objects.isNull(Object obj)    // null일 경우 true 아닐 경우 false를 리턴

Objects.nonNull(Object obj)   // null일 경우 false 아닐 경우 true를 리턴

Objects.requireNonNull(T obj) // null일 경우 NullPointerException 발생

Java 9

Objects.requireNonNullElse(T obj, Supplier<? extends T> supplier)
```


Optional
---
관련 글 : [Optional 올바르게 사용하기](https://github.com/vrang-v/About-Java/blob/main/Optional%20%EC%98%AC%EB%B0%94%EB%A5%B4%EA%B2%8C%20%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0.md)  

optional 관련 주의사항
- 절대로 ```Optional``` 변수와 반환값에 ```null```을 사용하지 말 것


- ```Optional.isPresent( )```, ```Optional.isEmpty( )```, ```Optional.get( )```을 되도록 사용하지 말 것


- ```Optional```을 ***필드, 메서드 매개변수, 집합 자료형**에 사용하지 말 것*


Annotation
---
```JSR-308```의 ```@Nonnull```, ```@Nullable``` 어노테이션을 이용하여 null 체크를 할 수 있다.
### 기본 null 정책
기본적으로 필드, 매개변수, 반환값등을 ```@Nonnull```로 취급하며 예외적으로 ```@Nullable```을 사용하면 된다.

---
null 잘 쓰는 법
---

### API에 null을 최대한 사용하지 말아라
- null로 지나치게 유연한 메서드를 만드는 대신 명시적인 메서드를 추가로 생성하라


- 절대로 null을 직접적으로 반환하지 말아라

    - return 값이 꼭 있어야 하는 경우에는 null 반환이 아닌 예외를 던져라
    - 빈 반환 값이나 빈 컬렉션은  'Null 객체'를 활용하라
    - 반환 값이 없을 수도 있다면 ```Optional```을 반환하라
    
### null의 범위를 지역에 제한하라
클래스와 메서드를 작게 만들어서 null을 캡슐화 해서 파급 효과를 최소화 하라