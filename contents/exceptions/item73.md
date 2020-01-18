## 아이템71. 표준 예외를 사용하라
숙련자는 더 많은 코드를 재사용합니다. 그중 하나가 예외입니다. <br> 
표준예외를 재사용하면 장점이 많습니다. <br>
<code>**읽기 좋은 코드**</code>  <code>**사용하기 좋은 API**</code> 를 만들기 좋습니다.<br>
자바 라이브러리는 충분한 예외를 제공하니 표준 예외를 잘 활용해봅시다.

### 자주 쓰이는 표준 예외
다음 표는 널리 재사용되는 예외를 정리했습니다.

|예외|발생 상황|특징|
|:---:|:---:|:---:|
|IllegalArgumentException|들어온 인수가 허용값이 아닐때|Null문제 예외|
|IllegalStatementException|객체가 메서드 수행이 가능한 상황이 아닐때|초기화가 안된 객체|
|NullPointException|Null을 비허용하는 메서드에 null을 건넸을때|초기화가 안된 변수|
|IndexOfBoundsException|인덱스 허용 범위 초과 시||
|ConcurrentModificationException|허용하지 않는 동시 수정 발생 시|싱글thread환경 맞춤설계된 객체를 멀티thread에서 동시 수정 발생시|
|UnsupportedOperationException|요청 동작을 객체가 지원하지 않을때||

<br>
다른 표준 예외를 사용할때는 다음을 주의하세요. <br>
이 예외들은 다른 예외들의 상위 클래스입니다. 직접 재사용하면 테스트가 불편해집니다.
- Exception
- RuntimeException
- Throwable
- Error

<br><br>

이외에 필요한 예외들은 API 문서를 참고합시다.
https://docs.oracle.com/javase/7/docs/api/java/lang/Exception.html

