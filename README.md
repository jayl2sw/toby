# Toby's Spring

## Design Pattern

### Template Method Pattern

상속을 통해서 슈퍼클래스의 기능을 확장할 때 사용되는 가장 대표적인 방법이다. 불변 기능은 슈퍼클래스에 만들어두고 변경이 잦거나 확장이 필요한 기능은 서브클래스에서 구현하도록 한다. 슈퍼 클래스에서는 미리 추상 메소드 또는 오버리아드 가능한 메소드를 정의하두고 이를 활용해 코드의 기본 알고리즘을 담고있는 템플릿 메소드를 만든다. 슈퍼클래스에서 디폴트 기능을 정의해두거나 비워두었다가 서브클래스에서 선택적으로 오버라이드 할 수 있도록 만들어둔 메소드를 훅 메소드라고 한다.

```java
// Super.java - 추상 클래스
public abstract class Super {
    
    public void templateMethod(){
        // basic code
        
        hookMethod(); // 선택적으로 오버라이드 가능한 훅 메소드
        abstractMethod(); // 필수적으로 구현해야하는 추상 메소드
    }
    
   	protected void hookMethod();
    public abstract void abstractMethod();
}
```

```java
// Sub1.java - 구현체
public class Sub1 extends Super {
    
    @Override
    protected void hookMethod() {
        ...
    }
    @Override
    public void abstractMethod() {
        ...
    }
}
```

#### 장단점

* 장점

  * 상속을 통해 관심사항이 다른 코드를 분리해내고, 서로 독립적을로 변경 또는 확장 할 수 있다.

* 단점

  * 자바는 클래스의 다중 상속을 허용하지 않기 때문에 후에 다른 목적의 상속이 불가능하다.
  * 슈퍼 클래스의 변화가 모든 서브클래스를 변경시킬 수 있기 때문에 이에 대한 후처리가 필요할 수 있다.

  * 확장된 메서드를 다른 비슷한 타입의 클래스에서 사용할 수 없으므로 중복 코드의 위험이 있다.

---

### Factory Method Pattern

상속을 통해 기능을 확장하게 하는 패턴. 슈퍼클래스 코드에서는 서브 클래스에서 구현할 메소드를 호출해서 필요한 타입의 오브젝트를 가져와 사용한다. 주로 인터페이스 타입으로 오브젝트를 리턴하므로 서브클래스에서 정확히 어떤 클래스의 오브젝트를 만들어 리턴할지는 슈퍼클래스에서는 알지 못한다.

#### Product

```java
public interface Super {
    void create();
}
```

```java
public Sub1 extends Super {
    @Override
    public void create() {
        ...
    }
}
```

#### Creator(Factory)

```java
// SuperFactory.java - 추상클래스로 정의, newInstance로 Super객체를 생성할 수 있다.
public abstract class SuperFactory {
    public Super newInstance() {
        Super s = createSuper();
        s.create();
        return s;
    }
    
    protected abstract Super createSuper();
}
```

```java
public class Sub1Factory extends SuperFactory {
    @Override
    protected Super createSuper() {
        return new Sub1();
    }
}
```

#### Client

```java
SuperFactory sub1Factory = new Sub1Factory();
Super super = sub1Factory.newInstance(); // 이 때, super는 Sub1 오브젝트이다.
```



팩토리 메서드 패턴의 장점은 확장 시 기존코드의 변경이 없어도 된다는 점이다. 만약 Sub2라는 Super 구현체가 새로 추가되어야 할 때 Product와 Creator만 새로 만들어주면 된다.

#### Product

```java
public class Sub2 implements Super {
    @Override
    public void create() {
        ...
    }
}
```

#### Creator(Factory)

```java
public class Sub2Factory extends SuperFactory {
    @Override
    protected Super createSuper() {
        return new Sub2();
    }
}
```

이렇게 두개를 추가해준다면 클라이언트 입장에서는 다음과 같이 사용할 수 있습니다.

```java
SuperFactory sub2Factory = new Sub2Factory();
Super super = sub2Factory.newInstance(); // 이 때, super는 Sub2 오브젝트이다.
```



#### 장단점 

- 장점: Factory Method 패턴의 가장 큰 장점은 지금까지 본 것처럼 수정에 닫혀있고 확장에는 열려있는 OCP 원칙을 지킬 수 있다는 점입니다.
- 단점: 간단한 기능을 사용할 때보다 많은 클래스를 정의해야 하기 때문에 코드량이 증가합니다.