# Java

## JVM이란
> https://asfirstalways.tistory.com/158

- Java Virtual Machine으로 OS와 JAVA사이의 중개자 역활을 한다.
- 스택기반의 VM, ARM 아키텍쳐 같은 하드웨어 는 레지스터 기반
- JVM OS로 부터 MEM 할당 -> `.java` file -> `javac`(자바 컴파일러) -> `.class`(바이트 코드) -> `Class loader`를 통해 Class 파일들 `JVM`으로 로딩 -> `Execution Engine`을 통한 해석 -> 해석된 바이트코드 `Runtime Data Areas`에 배치 -> `execution`

<img src="https://t1.daumcdn.net/cfile/tistory/25616D45576B854C3F"/>


- `구성`
    - Class loader(컴파일러 역활)
        - 바이트 코드를 읽어 JVM으로 로딩 시킨다.
        - Runtime 시에 동적으로(런타임)) 클래스를 로드한다. **(동적코드<-> 정적코드=컴파일 타임)**
            - 즉 클래스를 처음 참조할 때, 해당 클래스를 로드하고 링크한다.
        - 구성 (3)
            - Loading: .class 파일 을 JVM에 로딩
            - Linking: JAVA Class Library와 Link
            - Initializing: Runtime Data Areas의 Method Area 등에 초기화 작업

    - Execution Engine
        - Class Loader가 Loading으로 만들어 JVM 런타임 데이터 영역에 바이트 코드를 배치시킨 class를 실행시킨다.
        - 바이트코드를 JVM내부에서 실행될 기계어로 변경

        - 구성(3)
            - Interpreter: 자바 바이트코드 기본적으로 명령어 단위로(한줄) 읽어드려 느리다.
            - JIT(just in time)
                - 인터프리터 방식 진행하다, 적절한 시점에 바이트 코드 전체를 컴파일 하여 `네이티브 코드화`
                - 네이티브 코드는 캐시에 저장되어 한번 컴파일 된 코드는 매우 빠르다.
                - 컴파일 시간이 `인터프리팅`에 비해 오래걸린다.
                - JVM 내부적으로 해당 method가 얼마나 자주 사용되는지 체크하여 JIT 전환을 이뤄야 한다.
            - GC
    - runtime data area
        - Method area(=Class Area = Static Area, 클래스 데이터)
            - 클래스 정보를 처음 mem에 올릴 때 초기화 되는 대상을 저장하기 위한 메모리 공간
            - 저장되는 정보 종류(3)
                - Field: 데이터 타입, 접근 제어자, 멤버변수 이름(특정 class와 연결되는 variable), 전역변수와 Static 변수(정적 멤버변수)
                - Method: 메소드 이름, 리턴 타입, 매개변수, 접근 제어자
                - Type: Class/ Interface / Super class 
        
        - Heap ( 객체를 위한 공간 )
            - eden / survivor 1-2 / Old / Permanent
        
        - JVM Stack 영역(Threads)
            - Java Thread들은 단독의 Pc-register와 stack 그리고 Native Method Stack을가진다. (os도 마찬가지)
                
            - c.f) Native Method Stack
                - 자바 프로그램이 컴파일 되어 생성되는 바이트 코드가 아닌 실제 실행할 수 있는 기계어로 작성된 프로그램을 실행시키는 영역(JVM 내의 별도의 Stack영역)
                - JAVA가 아닌 다른 언어 코드를 위한 공간
                - JNI를 통해 바이트코드 전환 후 저장됨

## JDK JRE JVM
`JDK`
`JRE`
`JVM`



                
        






## Java GC
> https://d2.naver.com/helloworld/1329
- 자바의 3가지 메모리 구조에서 GC는 Heap에 속한다.
- `가비지 컬렉션 과정`
    - `stop-the-wrold`: GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것
        - 대개 GC튜닝이란 이 `stop-the-world`시간을 줄이는 것
    - `weak generational hypothesis`
        - `die young or are likely to live forever`
        - 이 가설을 극대화하기 위해 `HotSpot VM`에서 `Young`, `Old` 두 영역을 구분
            - Young: 새로 생성 객체 위치, 해당 객체가 사라질 때 Minor GC 발생
            - Old: 살아남은 객체, 대부분 Young보다 크게 할당, GC가 상대적으로 적게 발생, Major GC(Full GC)발생
            - Permanent(Perm): Method Area, 
    - `Card Table`
        -만약 old 영역에 있는 객체가 Young영역 객체 참조 시, Old 영역을 모두 탐색하지 않고 Card Table만 뒤져서 GC 대상인지 식별
        - 512바이트 덩어리
        - `write barrier`를 통해 관리된다.
            - write barrier is a mechanism for enforcing a particular ordering in a sequence of writes to a storage system in a computer system
            - 요약하면 young에서 old로 이동하거나 old에서 삭제해줄 때, card table의 update를 강제해준다.
    
    - `Young` 영역의 구성(3)
        - Eden
        - Survivor 1~2
    
    - `Minor GC 순서`
        - 처음 객체가 생성되면 eden에 입성
        - eden에서 GC 한번 일어나면, 살아남은 객체를 Survivor 1~2중 하나로 이동 시킴
        - 이후 Eden에서 GC 발생 할때마다, 사용중인 Survivor에 stack
        - 영역이 가득차면 GC를 돌려 살아남은 객체를 다른 Empty Survivor에 저장 후, 다른 녀석은 Empty상태 유지
        - 계속 반복하다 살아남은 객체는 Old 입성
    
    - `HotSpot VM`의 eden 관리 기술 2가지
        - `bump-the-pointer`
            - Eden Top(마지막 할당된 객체)을 추적
            - 다음 생성 객체를 Eden영역에 넣기 적당한지 파악
            - 빠른 메모리 할당 구현 가능
        - `TLABS`(Thread-Local Allocation Buffers)
            - `bump-the-pointer`가 멀티 스레드 환경에서 Thread-safe하기 위해서는 Eden에 Lock을 걸어야 하는데 이는 `lock-contention`(lock waiting)을 일으켜 속도 저하
            - 위의 문제를 해결하기 위해, 각 스레드 별로 에덴을 쪼개 덩어리(TLAB)를 생성하여 관리한다. 이를 통해 Lock을 사용하지 않아도 된다. (대신 eden GC가 많이 일어나겠네, 그리고 치우치는 현상도 가능할 듯)

    - `Major GC`(Old 영역 GC) (5)
        - Serial GC
            - Cpu 코어 하나 또는 매우 적을 때 적합
            - `mark-sweep-compact`: Mark 하고 kill(Sweep)한뒤 Heap영역을 Compact 시킨다. 
                - 이는 mark & sweep 방식이 `Fragmentation`을 만들기 때문에 compact시켜준다.
                - 대신 Compaction 작업 이후 살아남은 Object들의 reference를 update 시켜주는 작업이 필요
                - 그리하여 OS Fragmentation은 Compaction 사용 안함, 이 기간동안 CPU가 놀게 되니까
        - Parallel GC
            - 멀티 쓰레딩 GC
        - Parallel Old GC(Parallel Compacting GC)
            - Old GC방식만 다르다. 
            - `Mark-Summary-Compaction`
        - Concurrent Mark & Sweep GC(이하 CMS)
            - Low Latency High Fragmentation
            - 다른 Thread와 GC가 concurrent하게 진행하여 stop-the-world를 줄였다.
            - compaction을 하게되면 stop-the-world가 길게 발생하여 Compaction을 하지 않는다. 이로 인하여 Fragmentation현상이 발생    
        - G1(Garbage First) GC
            - 지금까지의 Young과 Old를 잊어라.
            - 바둑판 형식으로 Heap을 나누어, 바둑판 각 영역에 객체를 할당하고 해당 영역이 꽉차면 다른 영역으로 객체를 할당하고 GC를 실행한다.


## `Java Reference(Reachability)와 GC`
> https://d2.naver.com/helloworld/329631

**GC의 기능: `Root Set`에서부터 tracing(ref를 타고 들어감, bfs같은 개념)을 하여 `Reachable` 여부를 판단한다. 이후 `Unreachable`한 데이터들은 Sweep해준다.(sweep phase)**
```
요약
    - Java GC는 GC 대상 객체를 찾고, 대상 객체를 처리(finalization)하고, 할당된 메모리를 회수하는 작업으로 구성된다.
    - 애플리케이션은 사용자 코드에서 객체의 reachability를 조절하여 Java GC에 일부 관여할 수 있다.
    - 객체의 reachability를 조절하기 위해서 java.lang.ref 패키지의 SoftReference, WeakReference, PhantomReference, ReferenceQueue 등을 사용한다.  
```

- `Root Set`
    - 자바의 객체가 참조 받을 가능성은 총 4가지이다.
    - c.f 자바의 메모리 구조는 크게 4가지(`Java 스택`(Thread영역의 Stack: 지역변수, 파라미터에 의한 ref), `Native 스택`(JNI:Java Native Interface에 의해 생성된 객체 ref) , `Method Area`(클래스 정보, 이때 static variable에 저장된 ref) , `Heap영역`(힙 내부의 객체 끼리의 ref))
    - 이때 heap영역을 제외한 3가지 영역에 대해서 `Root Set`이라 지징한다.

- `Reference 와 Reachability` [GfG about java reference](https://www.geeksforgeeks.org/types-references-java/)

    - `Reference` 상태 (5): **일종의 Reachability를 관리해주기 위한 클래스라고 생각하면 쉽다.**
        
        - 자바에서는 총 5가지 `Reference` 상태가 주어진다.
        - Strong reference: `Gfg g = new Gfg();`
        - Soft reference: `SoftReference<Gfg> softref = new SoftReference<Gfg>(g);`
        - Weak reference: `WeakReference<Sample> wr = new WeakReference<Sample>(new Sample());`
        - Phantom reference: 
            - `ReferenceQueue`의 생성이 필수적
            ```java
            ReferenceQueue<Object> rq = new ReferenceQueue<Object>(); 
            PhantomReference<Object> pr = new PhantomReference<Object>(referent, rq);
            ```

    - `Reachability`(5): GC관리를 위한 객체 상태
        - strongly reachable
            - `Root set`에서 바로 연결되는 객체연결이 하나라도 있는 객체
            - 일반적으로 객체 생성하면 default로 Strong Reference Class상태가 된다.
            - 변수에 null하지 않는이상, GC에 의해 제거되지 않는다.

        - softly reachable
            - strongly reachable이 아니면서 `Root set`으로 부터 `soft reference` class만 통과하여 닿을 수 있는 객체
            - `GC threshold`까지 GC 되지 않는다.
            - threshold = `(마지막 strong reference가 GC된 때로부터 지금까지의 시간) > (옵션 설정값 N) * (힙에 남아있는 메모리 크기)`

            ```java
            Gfg g = new Gfg();  
            SoftReference<Gfg> softref = new SoftReference<Gfg>(g);
            g = null;
            // softref is still alive! 
            ```
            - heap 메모리가 적게 남아있을 수록 우변의 값이 작아져 대부분의 softly reachable객체는 메모리에 회수되어 `OutofMemoryError`를 막을 수 있게 된다.

            - 
        - weakly reachable
            - strong, soft 객체 아닌 것들 중에서, `weak reference class`만 통과해서 닿을 수 있는 객체
            - 수행할 때마다 회수 대상이 된다. (회수 체크, 회수)
            - 그러나 GC알고리즘에 따라 객체 회수시기가 다르므로 **GC 수행될 때마다 반드시 메모리까지 회수된다는 보장x(softly, unreachable)**

            - **`LRU Cache`를 구현할때는 `Weak Reference`를 사용한다.**, softly reachable 객체는 힙에 남아 있는 메모리가 많을 수록 회수 가능성이 낮아져 GC되지 않고 일정 부분 계속 점유하게 되는데 이 경우 전체 메모리 사용량이 높아져 GC가 더 자주 일어나게 되고, GC에 걸리는 시간도 상대적으로 길어지게 된다.

        - phantomly reachable
            - 앞선 3가지와 unreachable이 아닌 객체
            - 이 객체는 `finalize`되었지만, 아직 메모리가 회수되지 않은 상태이다.
            - finalize 된 상태를 구별해주기 위해서 사용한다고 파악됨
            - `finalize`: GC는 크게 2단계로 1) GC대상 객체를 처리하는 작업(finalize) 2) GC알고리즘에 따라 할당된 메모리 회수

            - `ReferenceQueue`
                - soft,weak reference class = null시키면, referenceQueue에 enqueue되고, phantomReference는 객체 내부의 참조를 null설정하지 않고, 참조된 객체를 phantomly reachable 객체로 만든이후 enqueue된다.
                - 이를 통해 app은 객체의 finalize 이후 필요한 작업들을 처리할 수 있게 된다.
            
            - **`phantomly reachable`은 앞선 상태와 달리 파이널라이즈와 메모리 회수 사이에 관여한다.**
            - 어떤 객체를 가리키는 ref가 `PhantomReference`만 존재한다면 해당 객체는 finalize된 것으로 간주한다. (즉 finalize가 필요없다.)

        - unreachable
            - Root set으로부터 참조사슬로 참조되지 않는 객체
            - 무조건 GC대상

- GC가 객체를 처리하는 순서
    
    - 해당 객체에 대해서 strong references 여부파악
    - 해당 객체에 대해서 soft references 여부파악
    - 해당 객체에 대해서 weak references 여부파악
    - 해당 객체에 대해서 finalize (reference 클래스들 null시키고 Reference Queue에 enqueue)
    - 만약 해당 객체를 가리키는 `PhantomReference`가 존재한다면 phantom references를  Reference Queue에 넣어 finalize 시키고 메모리 회수 지연
    
