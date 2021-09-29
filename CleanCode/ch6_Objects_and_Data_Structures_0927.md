# Objects and Data Structures

> 시스템을 구현할 때, 상황에 맞게 객체나 자료구조를 선택해야 한다.

```python
Objects expose behavior and hide data.
Data Structure expose data and have no significant behavior.

시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 적합하다.
반면, 새로운 동작을 추가하는 유연성이 필요하면 자료 구조와 절차적 코드가 적합하다.
```



## 결론 => 무슨 말인지 잘 모르겠다!

객체는 동작을 공개하고 자료를 숨긴다. 그래서 기존 동작을 변경하지 않으면서 새 객체 타입을 추가하기는 쉬운 반면, 기존 객체에 새 동작을 추가하기는 어렵다.

자료 구조는 별다른 동작 없이 자료를 노출한다. 그래서 기존 자료 구조에 새 동작을 추가하기는 쉬우나, 기존 함수에 새 자료 구조를 추가하기는 어렵다.

(어떤) 시스템을 구현할 때, 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 더 적합하다. 다른 경우로 새로운 동작을 추가하는 유연성이 필요하면 자료 구조와 절차적인 코드가 더 적합하다. 우수한 소프트웨어 개발자는 편견 없이 이 사실을 이해해 직면한 문제에 최적인 해결책을 선택한다.

Objects expose behavior and hide data. This makes it easy to add new kinds of objects without changing existing behaviors. It also makes it hard to add new behaviors to existing objects. Data structures expose data and have no significant behavior. This makes it easy to add new behaviors to existing data structures but makes it hard to add new data structures to existing functions.

In any given system we will sometimes want the flexibility **to add new data types,** and so we prefer objects for that part of the system. Other times we will want the flexibility **to add new behaviors**, and so in that part of the system we prefer data types and procedures. Good software developers understand these issues without prejudice and choose the approach that is best for the job at hand.



---

정리자료 참고 @ [yeramii](https://github.com/yeramii)/**[Books](https://github.com/yeramii/Books)** 

---



### Data Abstraction 

In both of the above cases the second option is preferable. We do not want to expose the details of our data. Rather we want to express our data in abstract terms. This is not merely accomplished by using interfaces and/or getters and setters. Serious thought needs to be put into the best way to represent the data that an object contains. The worst option is to blithely add getters and setters.

- 추상화 (=구현을 숨기는 것)
  - 단순히 변수를 private으로 선언하고, getter와 setter를 제공하는 것 X
  - 추상 인터페이스를 제공해, 사용자가 구현을 모른 채 자료의 핵심을 조작 가능한 것 O
- 데이터의 세부사항을 노출하기보다, 추상적인 용어를 사용
  - `getFuelTankCapacityInGallons()`, `getGallonsOfGasoline()` 보다 `getPercentFuelRemaining()` 이 좋다.
- 아래 예시 둘 다 point 를 나타내지만, 6-1은 구현을 노출하고, 6-2는 구현을 숨긴다.

###### 6-1 Concrete Point

```python
# Concrete Point
public class Point { 
  	public double x; 
  	public double y;
}
```

###### 6-2 Abstract Point

```python
# Abstract Point
public interface Point {
  	double getX();
  	double getY();
  	void setCartesian(double x, double y); 
  	double getR();
  	double getTheta();
  	void setPolar(double r, double theta); 
}
```



### Data/Object Anti-Symmetry 

Procedural code makes it hard to add new data structures because all the functions must change. OO code makes it hard to add new functions because all the classes must change.

1. **객체**는 추상화 뒤로 자료를 숨기고, 자료를 다루는 **함수만 공개**한다.
2. **자료구조**는 **자료를 공개**하고, 별다른 함수는 제공하지 않는다.

객체와 자료구조는 정반대이므로, 상황에 따라 적절하게 사용하는 것이 좋다.

- **절차적인 코드**는 기존 자료 구조를 변경하지 않으면서, 새 함수 추가가 쉽다.
- **객체 지향 코드**는 기존 함수를 변경하지 않으면서, 새 클래스 추가가 쉽다.

###### 6-5 Procedural Shape - 절차 지향 & 자료구조

```python
// 각 클래스에는 도형의 public 데이터를 작성
public class Square { ... }
public class Rectangle { ... }
public class Circle { ... }

// 모든 도형 클래스의 넓이 연산을 위한 public 함수를 작성 (조건문으로 각 도형 분기)
public class Geometry {
  public double area(Object shape) throws NoSuchShapeException { ... }
    throw new NoSuchShapeException(); 
  }
}
```

###### 6-6 Polymorphic Shapes - 객체 지향 & 클래스

```python
// 각 클래스에 도형의 private 데이터를, 넓이 연산을 위한 public 함수를 함께 작성
public class Square implements Shape { ... }
public class Rectangle implements Shape { ... }
public class Circle implements Shape { ... }
```



### The Law of Demeter

- 모듈은 자신이 조작하는 객체의 내부를 몰라야 한다.
- 클래스 *C* 의 메서드 *f* 는 다음과 같은 객체의 메서드만 호출해야 한다
  - *C*
  - *f* 가 생성한 객체
  - *f* 의 인수로 넘어온 객체
  - *C* 의 인스턴스 변수에 저장된 객체

##### Train Wrecks

- 여러 객차가 한 줄로 이어진 것 처럼 보이는 데서 명명

  ```python
   final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
   // 조잡해보이므로, 아래와 같이 나눠 쓰는 것이 좋다.
   
   Options opts = ctxt.getOptions();
   File scratchDir = opts.getScratchDir();
   final String outputDir = scratchDir.getAbsolutePath();
  ```

- 위 변수들이 객체라면 내부 구조를 숨겨야 하므로 디미터 법칙 위반 O, 자료구조라면 위반 X.



### Hybrids

- 반은 객체고 반은 자료구조인 혼합 구조는, 새로운 함수와 자료구조 둘 다 추가하기 어렵게 만든다. (최악)

##### Hiding Structure

- 위 예제에서

   

  ```
  ctxt
  ```

  가 객체라면 내부구조를 물어볼 게 아니라, 뭔가 하라고 지시해야 한다. (not ask about internals, tell to do something~! )

  - 경로가 왜 필요한지 & 경로 가져다 어디에 쓸건지 파악 => 임시 파일 생성 위함
  - 해당 용도에 대한 명령 직접 내림 => `ctxt` 객체에 파일생성 메서드 호출
  - 결국 `ctxt`의 내부 구조 드러내지 않으며, 모듈은 알 필요 없는 객체 탐색 X => 디미터 법칙 만
