## 아이템11. equals를 재정의하려거든 hashcode도 재정의하라

equals() : 두 객체의 내용이 같은지, 논리적 동등성(equality)를 비교하는 함수

hashCode() : 두 객체가 같은 객체인지, 동일성(identity)를 비교하는 함수

보통 같은 필드를 가진 두 개의 객체를 같다고 판단하기 위해 equals를 재정의(Override)하여 사용합니다. 이 때, equals만 재정의하고  hashCode를 재정의 하지 않으면 각 객체는 다른 hashcode를 가지고 있게 되고, 이는 HashMap, HashSet과 같은 해시 기반의 컬렉션에서 오류를 발생시키는 원인이 됩니다. 

> equals(object)가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 한다.  
> _Object 명세 중 hashCode에 관한 규약_

때문에 **equals를 재정의 하면 hashcode 역시 재정의 해주어야 합니다. **

### **1️⃣ hash(), hashCode()란?**

-   **HashMap/HashTable**  
    -   연관배열구조를 이용한 **key에 value를 저장**하는 자료구조입니다.
        -   연관배열구조란 key 1개와 value 1개가 1:1로 연관되어있는 자료구조로, key값을 이용하여 value를 도출할 수 있습니다.
    -   키(Key), 해시함수(Hash Function), 해시(Hash), 값(value), 저장소(Bucket, Slot)로 이루어져 있습니다.
        -   Key : 고유한 값으로 해시 함수의 input이 됩니다. 다양한 길이의 key를 저장소에 저장하려면 다양한 길이만큼의 저장소를 구성해두어야 하므로 해시함수를 통해 일정한 길이로 바꾸어 저장시킵니다.
        -   Value : 저장소(bucket)에 최종적으로 저장되는 값으로 키와 매치되어 저장, 삭제, 검색, 접근이 가능해야합니다.
        -   hash: 해시함수의 결과물이며, 저장소(bucket)에서 값과 매칭되어 저장됩니다. 
-   **hash()**
    -   **임의의 길이를 가진 데이터를 입력받아 일정한 길이의 비트열(해시값)로 반환**시켜주는 함수입니다.
        -   조금 더 쉽게 말하자면 위의 그림에서 Key를 Hash로 바꾸어주는 역할을 합니다.
    -   다양한 길이를 일정한 길이인 해시로 변경하여 저장소를 효율적으로 운영할 수 있도록 도와줍니다.
    -   Hash()에 의해 반환된 데이터 고유의 숫자 값을 hashCode라고 합니다.
-   **hashCode()**
    -   **객체의 hashCode값을 반환**시켜주는 함수입니다.
    -   반환된 HashCode는 Object를 식별할 수 있는 하나의 Integer값을 의미합니다.  
        -   즉, 두 개의 객체가 서로 같다면(equals), 해당 객체들의 hashCode는 동일한 값을 가집니다.
        

### **2️⃣ 올바른 hashcode() 작성법**

올바른 hashCode 메서드는 **서로 다른 인스턴스에 다른 해시코드를 반환**하도록 작성해야합니다.

> 이상적인 해시 함수는 주어진 서로 다른 인스턴스들을 32비트 정수 범위에 균일하게 분배해야한다.   
> _\- Object 명세 중 hashCode에 관한 규약_

**좋은 hashcCode 작성 요령**

```java
@Override
public int hashCode() {
    int c = 31;
    //1. int변수 result를 선언한 후 첫번째 핵심 필드에 대한 hashcode로 초기화 한다.
    int result = Integer.hashCode(firstNumber);

    //2. 기본타입 필드라면 Type.hashCode()를 실행한다
    //Type은 기본타입의 Boxing 클래스이다.
    result = c * result + Integer.hashCode(secondNumber);

    //3. 참조타입이라면 참조타입에 대한 hashcode 함수를 호출 한다.
    //4. 값이 null이면 0을 더해 준다.
    result = c * result + address == null ? 0 : address.hashCode();

    //5. 필드가 배열이라면 핵심 원소를 각각 필드처럼 다룬다.
    for (String elem : arr) {
      result = c * result + elem == null ? 0 : elem.hashCode();
    }

    //6. 배열의 모든 원소가 핵심필드이면 Arrays.hashCode를 이용한다.
    result = c * result + Arrays.hashCode(arr);

    //7. result = 31 * result + c 형태로 초기화 하여 
    //result를 리턴한다.
    return result;
}
```

출처 : [Carrey's 기술 블로그](https://jaehun2841.github.io/2019/01/12/effective-java-item11/#%EC%A2%8B%EC%9D%80-%ED%95%B4%EC%8B%9C-%ED%95%A8%EC%88%98-%EB%A7%8C%EB%93%A4%EA%B8%B0) 

### **3️⃣ hashCode 편하게 구현하기**

**1\. Object.hash**

hash()함수를 이용하여 구현하면 간편하지만, 내부적으로 AutoBoxing이 일어나기 때문에 속도는 더 느리다. 

**2\. @EqualsAndHashCode**

Lombok의 @EqualsAndHashCode

**3\. @AutoValue**

Google의 @AutoValue

### **4️⃣ 재 정의시 주의할 점**

1\. 불변 객체에 대해 hashcode 생성비용이 많이 든다면, 캐싱을 고려하자

지연 초기화로 구현할 경우 스레드 안정성까지 고려하자. 

2\. 성능을 높이기 위해 핵심필드를 제외하고 hashCode를 계산하지 말자 

필드에 따라 해시 코드를 골고루 퍼트려주는 효과가 있을 수 있다. 

속도는 빨라져도 hash품질이 나빠져 해시 테이블 성능을 떨어트릴 수 있다.

3\. Hashcode가 반환하는 값의 생성규칙을 API 사용자에게 자세히 공표하지 말자

클라이언트가 hashcode값에 의지해 코드를 짜지 않기 위함이다