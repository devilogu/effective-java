## 아이템1. 생성자 대신 정적 팩터리 메서드를 고려하라


인스턴스를 생성하는 방법에는 **public 생성자**를 이용하는 방법과 **정적 팩터리 메서드**를 이용하는 방법이 있습니다. 

public 생성자를 이용할 경우 **new Ticket();** 을 통해 인스턴스를 얻을 수 있습니다. 

```
public class Ticket {
	public Ticket() {}
}
```

정적 팩터리 메서드를 이용할 경우 **Ticket.getInstance();** 을 이용하여 인스턴스를 얻을 수 있습니다. 

```
public class Ticket {
   public static Ticket getInstance() {
        return ticket;
    } 
}
```

기본적으로 생성자를 이용하여 인스턴스를 생성하게 되는데, 정적 팩터리 메서드를 이용하여 생성하면 아래와 같은 장점이 있습니다. 

#### 👍 장점 1. 이름을 가질 수 있다.

생성자를 이용하여 인스턴스를 얻어 올 경우에는 반환 될 객체의 특성을 제대로 파악할 수 없습니다. 하지만 정적 팩터리 메서드의 경우, **직접 네이밍을 할 수 있기 때문에 반환 될 객체의 특성을 쉽게 파악할 수 있습니다.**

또한, 생성자를 이용하면 같은 매개변수 타입을 가진 생성자는 하나만 만들 수 있다는 문제점이 있습니다. 물론 매개변수의 순서를 다르게 하여 또 다른 생성자를 만들 수도 있지만 그렇게 되면 그런 API를 사용하는 개발자는 각 생성자가 어떤 역할을 하는지 정확히 알기 어렵습니다. 하지만 정적 팩터리 메서드를 이용하여 구현하게 되면, 이름을 통해 각 함수의 역할을 나타낼 수 있으므로 이러한 문제점을 해결할 수 있습니다. 

#### 👍 장점 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

반복되는 요청에 같은 객체를 반환하는 정적 팩터리 방식의 클래스는 **언제 어느 인스턴스를 살아 있게 할지를 철저히 통제** 할 수 있습니다.

인스턴스를 통제하면 해당 클래스로 만들어질 객체의 수량을 조절 할 수 있습니다.

 - 객체를 1개로 제한: Singleton(싱글턴)

 - 객체를 생성하지 못하고 정적으로만 사용 가능: 인스턴스화 불가

_인스턴스 통제는 객체가 없다면 만들고, 있다면 그 객체를 공유하는 플라이 웨이트 패턴의 근간이 됩니다._

#### 👍 장점 3. 반환 타입의 하위 타입 개체를 반환할 수 있는 능력이 있다.

반환할 객체의 클래스를 자유롭게 선택할 수 있게 하는 유연성을 제공합니다. (리턴 타입의 상속을 받은 모든 클래스 가능)

즉, **인터페이스로 정적 팩터리 메소드를 구현하면 다형성을 가진 객체를 제공해줄 수 있습니다.**

_인터페이스를 정적 팩터리 메서드의 반환 타입으로 사용하는 인터페이스 기반 프레임워크의 핵심 기술입니다._

#### **👍 장점 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.**

**반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환하든 상관 없습니다.** 클라이언트는 클래스들의 존재를 모르며, 기존 버전에서 다음 릴리즈로 넘어갈 때 기존의 타입이 유용하지 못하다면 삭제 후 다른 타입을 반환해줄 수 있습니다.

```
public class Ticket {
	public static Ticket getTicketByType(String type) { 
    	if (type.equals("vip")) { 
        	return VipTicket.INSTANCE; 
		} 
		
        if (type.equals("general")){ 
        	return GeneralTicket.INSTANCE; 
        } 
        
		new IllegalArgumentException("잘못된 타입입니다!"); 
	} 
}
```
 예시 출처 : [오늘의 개발](https://a1010100z.tistory.com/entry/아이템-1-생성자-대신-정적-팩터리-메서드를-고려하라)


#### **👍 장점 5. 정적 팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.**

****인터페이스나 클래스가 만들어 지는 시점에서 하위 타입의 클래스가 존재 하지 않아도 나중에 만들 클래스가 기존의 인터페이스나 클래스를 상속 받는 상황이라면 언제든지 의존성을 주입 받아서 사용이 가능합니다**.** 반환값이 인터페이스여도 되며, 정적 팩터리 메서드의 변경 없이 구현체를 바꿔 끼울 수 있습니다. 

```
import java.util.ArrayList; 
import java.util.List; 

public class TicketStore { 
	/** TicketSeller는 인터페이스이고 구현체가 없음에도 아래와 같은 메서드 작성이 가능하다.**/ 
	public static List<TicketSeller> getSellers(){ 
		return new ArrayList<>(); 
	} 
}
```

예시 출처 : [오늘의 개발](https://a1010100z.tistory.com/entry/아이템-1-생성자-대신-정적-팩터리-메서드를-고려하라)

_이러한 유연함은 서비스 제공자 프레임워크(Service provider framework)를 만드는 근간이 됩니다._

**서비스 제공자 프레임워크란?**

다양한 서비스 제공자들이 하나의 서비스를 구성하는 시스템으로, 클라이언트가 실제 구현된 서비스를 이용할 수 있도록 하는데, 클라이언트는 세부적인 구현 내용을 몰라도 서비스를 이용할 수 있습니다. JDBC는 mysql, oracle 등의 서비스 제공자들이 JDBC라는 하나의 서비스를 구성합니다. 

**서비스 제공자 프레임워크의 구성**

1\. 구현체의 동작을 정의하는 **서비스 인터페이스** (필수)

2\. 제공자가 구현체를 등록할 때 사용하는 **제공자 등록 API**  (필수)

3.클라이언트가 서비스의 인스턴스를 얻을 때 사용하는 **서비스 접근 API**(필수)

이 서비스 접근 API가 바로 서비스 제공자 프레임워크의 근간이라고 한 **유연한 정적 팩터리의 실체**입니다.

4\. 서비스 인터페이스의 인스턴스를 생성하는 팩터리 객체를 설명해주는**서비스 제공자 인터페이스** (선택)

**JDBC 예시**

더 자세한 설명은 이 [링크](https://devyongsik.tistory.com/294)를 참고해주세요. 

JDBC(Java Database Connectivity)의 경우 서비스 제공자 프레임워크에서의 제공자는 서비스 구현체입니다. 이 구현체들을 클라이언트에 제공하는 역할을 프레임워크가 통제하여, 클라이언트를 구현체로부터 분리해줍니다.

서비스 인터페이스 : Connection

제공자 등록 API : DriverManager.registerDrive()

서비스 접근 API : DriverManager.getConnection()

서비스 제공자 인터페이스 : Driver

_이를 활용하여 JDBC라는 서비스의 규칙을 지킨(Connection 인터페이스를 구현한 DB회사의 라이브러리 등) 여러 DB를 사용 가능 하게됩니다._

그렇다면 정적 팩터리 메서드의 단점은 무엇일까요?

#### 👎 단점 1. 상속을 하려면 public이나 protected 생성자가 필요하기 때문에 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

그러나 이 제약은 상속보다 컴포지션을 사용하도록 유도하고, 불변 타입으로 만들려면 이 제약을 지켜야 한다는 점에서 오히려 장점으로 받아들일 수도 있습니다.

#### 👎 단점 2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

생성자처럼 API 설명에 명확히 드러나지 않기 때문에 사용자는 API 문서를 잘 써놓고 메서드 이름도 널리 알려진 규약을 따라 짓는 식으로 문제를 완화해주어야 합니다. 

**정적 팩토리에서 흔히 사용하는 명명 방식**

<table style="border-collapse: collapse; width: 93.2558%; height: 307px;" border="1" data-ke-style="style12"><tbody><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;"><b>명명 규칙</b></span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;"><b>설명</b></span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">from</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메소드.</span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">of</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메소드.</span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">valueOf</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">from 과 of 의 더 자세한 버전</span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">instance or getInstance</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">(매개 변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만 같은 인스턴스임을 보장하지는 않는다.</span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">create or newInstance</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">instance 혹은 getInstance와 같지만 매번 새로운 인스턴스를 생성해 반환함을 보장한다.</span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">getType</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩토리 메소드를 정의할 때 쓴다.&nbsp;</span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">newType</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩토리 메소드를 정의할 때 쓴다.</span></p></td></tr><tr><td style="width: 25.9161%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">type</span></p></td><td style="width: 73.8069%;"><p><span style="font-family: 'Noto Sans Demilight', 'Noto Sans KR'; color: #000000;">getType과 newType의 간결한 버전</span></p></td></tr></tbody></table>


> ✔ 핵심 정리  
>  
> 정적 팩토리 메소드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋습니다.  
> 그렇다고 하더라도 정적 팩토리를 사용하는 게 유리한 경우가 더 많으므로 무작정 pulbic 생성자를 제공하던 습관이 있다면 고치도록 합니다.  
