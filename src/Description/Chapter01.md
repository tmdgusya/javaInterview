# Java InterView

## Java 개발환경 ?

JRE(Java Runtime Enviroment) : 자바 실행환경, 자바로 런타임이 실행되기 위한 최소 환경
    - 자바 애플리케이션을 실행시키기 위한 배포판, 핵심 라이브러리가 거의전부 들어가 있음.
JDK(Java Development kit) : 자바 개발도구

- javac.exe : 자바 컴파일러, 자바 소스코드를 바이트 코드로 컴파일한다.
    - javac 에는 여러가지 옵션을 붙일 수 있는데 그중 뭐 간단하게 많이 사용할 것 같은 옵션들을 기재 해보려고 한다.
    - classpath <path> : 유저가 사용하는 클래스가 어디있는지 경로를 지정해주는 것이다.
- java.exe : 자바 인터프리터, 컴파일러가 생성한 바이트코드를 해석하고 실행한다.
- javap : javap 는 클래스 파일을 disassembles 할 수 있는 기능이다.
    - javap ClassName : 그냥 해당 클래스 정보가 나옴
    - javap -c ClassName : 해당 코드가 disassemble 되고, 자바 바이트코드를 reflect 한다.

- 자바에서 모든 코드는 반드시 클래스 안에 존재해야 하며, 서로 관련된 코드들을 그룹으로 나누어 별도의 클래스를 구성하게 된다.

- 자 궁금하니까 자바 Class 를 까보자.

`Instances of the class Class represent classes and interfaces in a running Java application.
An enum is a kind of class and an annotation is a kind of interface.
Every array also belongs to a class that is reflected as a Class object that is shared by all arrays with the same element type and number of dimensions.
The primitive Java types (boolean, byte, char, short, int, long, float, and double), and the keyword void are also represented as Class objects.`
이런식으로 나와있는데, 대략 요약하자면 모든 것은 Class 이다 라고 말하고 있다.

`Class has no public constructor. Instead Class objects are constructed automatically by the Java Virtual Machine as
classes are loaded and by calls to the defineClass method in the class loader.`

이런 부분도 나오는데 아마 해당 부분이, Reflection 에서 동적으로 클래스의 생성자를 Call 할때 이런 Class Loder 을 방식을 이용하는 것이 아닐까 싶다.

```java
public void static main(String[] args){}
```

위의 코드는 언어적인 것을 공부를 했다면, 프로그램 진입점이라는 사실을 알 수 있을 것이다. 자바에서도 똑같은 main 이라는 진입점을 약속으로 두고 사용하고 있다.
Java 어플리케이션은 Main 메서드의 호출로 시작해서 main 메서드의 첫 문장부터 마지막 문장까지 수행을 마치면 종료한다.

그러면 우리가 돌리고 있는 서버들도 결국에 내부적인 특정 반복(?) 에 의해서 계속 돌고 있는 상태로 유지되는 것이기에 꺼지지 않는 것일까?
    - 이슈화 시켜서 나중에 알아보자 !

자바는 파일 이름과, public Class 의 이름이 같아야 한다. 만약에 public Class 가 없다면 이름이 같지 않아도 된다!

## JVM

> 이건 중요하기도 하고 많이 나오니까 따로 분리했다.

JVM 은 바이트 코드를 실행한 표준이다. 그리고 특정 벤더가 JVM 을 구현한다. 예시로는 오라클의 JVM 벤더가 있다.

JVM 은 플랫폼에 종속적이다. 왜냐면 Native 코드로 변환되어 실행시켜야 하는데, 그건 OS 환경에 맞춰서 실행시켜야 되기때문이다.

그래서 JVM 자체로 배포되지 않고, JRE 로 배포된다.

JVM 은 우리가 OS 와의 소통을 위해서 필요한 것들 중 하나이다. 자바 프로그램은 JVM 위에서 실행되므로, JVM 을 거쳐서 통신을 하게 된다.
즉 우리가 짠 코드들은 JVM 을 통해서 컴퓨터가 이해할 수 있는 코드로 변환되는 것이다. 그렇기에 Java 가 플랫폼에 구애 받지 않고 사용할 수 있는 것이다.
번역해줄 JVM 만 있다면!

여러 그림들도 있는데, 그림은 넣지 않겠다. 간단히 JVM 은 우리가 java 소스파일을 바이트 코드로 컴파일하고, 해당 파일을 실행시키면.

클래스 로더에의해 로딩되고, 연결되고 해당 클래스들이 초기화된다. 이때 생성된 클래스들의 객체는 Runtime Data Area 로 들어가게 되는데, 이는 GC 의
영향을 받아 수거 될수도 있고, 생존할 수도 있다. (해당 방식은 GC 단원에서 설명하겠다.)

**Excution Engine**

- 클래스를 실행시키는 역할이며, 클래스 로더가 JVM 내의 Runtime Data Area 영역에 바이트 코드를 배치시키고, 이것은 실행엔진에 의해서 실행된다.
자바 바이트 코드는 기계가 바로 수행할 수 있는 언어가 아닌, 조금은 인간이 보기편한언어이다. 아까 위에서 봤듯이 javac 으로 바이트 코드로 컴파일 한뒤 한번 보아라.
인간이 보기 쉬운 언어인지는 잘 모르겠다. javap -c 로 보면 조금 볼만하기는 하다.

**JIT**

인터프리터 방식의 단점을 보완하기 위해서 등장한 JIT 컴파일러이며, 인터프리터 방식으로 실행하다가 적절한 시점에 바이트 코드를 전체를 컴파일하여 네이티브 코드로 변경하고,
이후에는 해당 파일을 더 이상 인터프리팅 하지않고, 네이티브 코드로 직접 실행하는 방식이다. JIT 컴파일러를 사용하는 JVM 들이 해당 메서드가 얼마나 자주 수행되는지 체크하고,
해당 빈도수가 높다면 JIT 을 이용해 컴파일을 수행한다.

**Runtime Data Area**

런타임 데이터 Area 를 보면 Thread 마다 별도의 Stack 영역그리고 PC Register 영역을 가지는데, 이는 프로세스와 스레드에 대한 기반지식이 있으면 이해하기 편한데,
스레드는 프로세스 내의 하나의 흐름이므로, Stack 을 제외한 전영역을 공유한다. 그래서 Thread 간 통신에는 TCB(Thread Controll Block) 을 사용하는데, 이는
Process 보다는 오버헤드가 덜하다. 하지만 알아둬야 할건 덜하다는 것 뿐이지, 단순히 스레드가 많아지면 TCB 로 인한 성능 저하가 일어날 수도 있다.

Thread 가 가지는 PC Register 부분은 Thread 가 어떤 부분을 명령으로 실행해야 할 지에 대한 기록을 하는 부분으로 현재 수행중인 JVM 명령의 주소를 갖는다.

그니까 Thread 가가지는 스택 영역은 곧, 프로그램의 실행과정에서 임시로 할당되었다가 메소드를 빠져나오면 바로 소멸되는 특성의 데이터를 저장하기 위한 공간이다.
