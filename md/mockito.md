## [Mockito](https://site.mockito.org/)

### Overview
The Mockito library enables mock creation, verification and stubbing.

### Install
Maven
```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-all</artifactId>
    <version>1.10.19</version>
    <scope>test</scope>
</dependency>
```

### Let's verify some behaviour!
* mock a List
* are familiar with the interface (such as the ***add()***, ***get()***, ***clear()*** methods).

```java
//Let's import Mockito statically so that the code looks clearer
 import static org.mockito.Mockito.*;

 //mock creation
 List mockedList = mock(List.class);

 //using mock object
 mockedList.add("one");
 mockedList.clear();

 //verification
 verify(mockedList).add("one");
 verify(mockedList).clear();
```

### How about some stubbing?
* all methods that return a value
* mock will return either null, a primitive/primitive wrapper value, or an empty collection For example 0 for an int/Integer and false for a boolean/Boolean.
* Stubbing can be overridden
* Once stubbed, the method will always return a stubbed value, regardless of how many times it is called.
* Last stubbing is more important - when you stubbed the same method with the same arguments many times

```java
//You can mock concrete classes, not just interfaces
 LinkedList mockedList = mock(LinkedList.class);

 //stubbing
 when(mockedList.get(0)).thenReturn("first");
 when(mockedList.get(1)).thenThrow(new RuntimeException());

 //following prints "first"
 System.out.println(mockedList.get(0));

 //following throws runtime exception
 System.out.println(mockedList.get(1));

 //following prints "null" because get(999) was not stubbed
 System.out.println(mockedList.get(999));

 //Although it is possible to verify a stubbed invocation, usually it's just redundant
 //If your code cares what get(0) returns, then something else breaks (often even before verify() gets executed).
 //If your code doesn't care what get(0) returns, then it should not be stubbed.
 verify(mockedList).get(0);
```
### Argument matchers

```java
//stubbing using built-in anyInt() argument matcher
 when(mockedList.get(anyInt())).thenReturn("element");

 //stubbing using custom matcher (let's say isValid() returns your own matcher implementation):
 when(mockedList.contains(argThat(isValid()))).thenReturn(true);

 //following prints "element"
 System.out.println(mockedList.get(999));

 //you can also verify using an argument matcher
 verify(mockedList).get(anyInt());

 //argument matchers can also be written as Java 8 Lambdas
 verify(mockedList).add(argThat(someString -> someString.length() > 5));

```

### Verifying exact number of invocations / at least x / never
* times(1) is the default

```java
 //using mock
 mockedList.add("once");

 mockedList.add("twice");
 mockedList.add("twice");

 mockedList.add("three times");
 mockedList.add("three times");
 mockedList.add("three times");

 //following two verifications work exactly the same - times(1) is used by default
 verify(mockedList).add("once");
 verify(mockedList, times(1)).add("once");

 //exact number of invocations verification
 verify(mockedList, times(2)).add("twice");
 verify(mockedList, times(3)).add("three times");

 //verification using never(). never() is an alias to times(0)
 verify(mockedList, never()).add("never happened");

 //verification using atLeast()/atMost()
 verify(mockedList, atMostOnce()).add("once");
 verify(mockedList, atLeastOnce()).add("three times");
 verify(mockedList, atLeast(2)).add("three times");
 verify(mockedList, atMost(5)).add("three times");
```

### Stubbing void methods with exceptions
```java
doThrow(new RuntimeException()).when(mockedList).clear();

//following throws RuntimeException:
mockedList.clear();
```

### Verification in order
* ***you don't have to verify all*** interactions one-by-one but only those that you are interested in testing in order.

```java
// A. Single mock whose methods must be invoked in a particular order
List singleMock = mock(List.class);

//using a single mock
singleMock.add("was added first");
singleMock.add("was added second");

//create an inOrder verifier for a single mock
InOrder inOrder = inOrder(singleMock);

//following will make sure that add is first called with "was added first", then with "was added second"
inOrder.verify(singleMock).add("was added first");
inOrder.verify(singleMock).add("was added second");

// B. Multiple mocks that must be used in a particular order
List firstMock = mock(List.class);
List secondMock = mock(List.class);

//using mocks
firstMock.add("was called first");
secondMock.add("was called second");

//create inOrder object passing any mocks that need to be verified in order
InOrder inOrder = inOrder(firstMock, secondMock);

//following will make sure that firstMock was called before secondMock
inOrder.verify(firstMock).add("was called first");
inOrder.verify(secondMock).add("was called second");

// Oh, and A + B can be mixed together at will
```

### Making sure interaction(s) never happened on mock
```java
//using mocks - only mockOne is interacted
mockOne.add("one");

//ordinary verification
verify(mockOne).add("one");

//verify that method was never called on a mock
verify(mockOne, never()).add("two");
```

### Finding redundant invocations
```java
//using mocks
mockedList.add("one");
mockedList.add("two");

verify(mockedList).add("one");

//following verification will fail
verifyNoMoreInteractions(mockedList);
```

### Shorthand for mocks creation - @Mock annotation
```java
public class ArticleManagerTest {

    @Mock private ArticleCalculator calculator;
    @Mock private ArticleDatabase database;
    @Mock private UserProvider userProvider;
}
```

### Stubbing consecutive calls (iterator-style stubbing)
```java
when(mock.someMethod("some arg"))
   .thenThrow(new RuntimeException())
   .thenReturn("foo");

 //First call: throws runtime exception:
 mock.someMethod("some arg");

 //Second call: prints "foo"
 System.out.println(mock.someMethod("some arg"));

 //Any consecutive call: prints "foo" as well (last stubbing wins).
 System.out.println(mock.someMethod("some arg"));
```

```java
 when(mock.someMethod("some arg"))
   .thenReturn("one", "two", "three");

//First call: one
//Second call: two
//Any consecutive call: three
```

if instead of chaining .thenReturn() calls, multiple stubbing with the same matchers or arguments is used
```java
//All mock.someMethod("some arg") calls will return "two"
when(mock.someMethod("some arg"))
    .thenReturn("one")
when(mock.someMethod("some arg"))
    .thenReturn("two")
```
### Stubbing with callbacks
Stubbing with generic Answer interface.
* We recommend simply stubbing with ***thenReturn()*** or ***thenThrow()***

```java
when(mock.someMethod(anyString())).thenAnswer(
     new Answer() {
         public Object answer(InvocationOnMock invocation) {
             Object[] args = invocation.getArguments();
             Object mock = invocation.getMock();
             return "called with arguments: " + Arrays.toString(args);
         }
 });

 //Following prints "called with arguments: [foo]"
 System.out.println(mock.someMethod("foo"));
```

### Family of methods 
* ***doReturn(Object)***
* ***doThrow(Throwable...)***
* ***doThrow(Class)***
* ***doAnswer(Answer)*** 
* ***doNothing()*** 
* ***doCallRealMethod()***

```java
doThrow(new RuntimeException()).when(mockedList).clear();

//following throws RuntimeException:
mockedList.clear();
```

### Spying on real objects

```java
List list = new LinkedList();
List spy = spy(list);

//optionally, you can stub out some methods:
when(spy.size()).thenReturn(100);

//using the spy calls *real* methods
spy.add("one");
spy.add("two");

//prints "one" - the first element of a list
System.out.println(spy.get(0));

//size() method was stubbed - 100 is printed
System.out.println(spy.size());

//optionally, you can verify
verify(spy).add("one");
verify(spy).add("two"); 
```

### Important gotcha on spying real objects!

```java
List list = new LinkedList();
List spy = spy(list);

//Impossible: real method is called so spy.get(0) throws IndexOutOfBoundsException (the list is yet empty)
when(spy.get(0)).thenReturn("foo");

//You have to use doReturn() for stubbing
doReturn("foo").when(spy).get(0);
```

### Changing default return values of unstubbed invocations

```java
Foo mock = mock(Foo.class, Mockito.RETURNS_SMART_NULLS);
Foo mockTwo = mock(Foo.class, new YourOwnAnswer());
```

### Capturing arguments for further assertions
* custom argument matcher is not likely to be reused
* you just need it to assert on argument values to complete verification

```java
ArgumentCaptor<Person> argument = ArgumentCaptor.forClass(Person.class);
verify(mock).doSomething(argument.capture());
assertEquals("John", argument.getValue().getName());
```

###  Real partial mocks

```java
//you can create partial mock with spy() method:
List list = spy(new LinkedList());

//you can enable partial mock capabilities selectively on mocks:
Foo mock = mock(Foo.class);
//Be sure the real implementation is 'safe'.
//If real implementation throws exceptions or depends on specific state of the object then you're in trouble.
when(mock.someMethod()).thenCallRealMethod();
```

### Resetting mocks
* **Don't harm yourself**. ***reset()*** in the middle of the test method is a code smell (you're probably testing too much).

```java
List mock = mock(List.class);
when(mock.size()).thenReturn(10);
mock.add(1);

reset(mock);
//at this point the mock forgot any interactions and stubbing
```

### Aliases for behavior driven development
* ***BDD (Behavior Driven Development)*** style of writing tests uses //given //when //then comments as fundamental parts of your test methods. 
* Start learning about BDD [here](https://en.wikipedia.org/wiki/Behavior-driven_development)

```java
import static org.mockito.BDDMockito.*;

Seller seller = mock(Seller.class);
Shop shop = new Shop(seller);

public void shouldBuyBread() throws Exception {
   //given
   given(seller.askForBread()).willReturn(new Bread());

   //when
   Goods goods = shop.buyBread();

   //then
   assertThat(goods, containBread());
}
```

### New annotations
* ***@Captor*** simplifies creation of ArgumentCaptor
* ***@Spy*** you can use it instead spy(Object).
* ***@InjectMocks*** injects mock or spy fields into tested object automatically.
* **Note:** ***@InjectMocks*** can also be used in combination with the ***@Spy*** annotation, it means that Mockito will inject mocks into the partial mock under test

###  Verification with timeout
* verifying with timeout. It causes a verify to wait for a specified period of time

```java
//passes when someMethod() is called no later than within 100 ms
//exits immediately when verification is satisfied (e.g. may not wait full 100 ms)
verify(mock, timeout(100)).someMethod();
//above is an alias to:
verify(mock, timeout(100).times(1)).someMethod();

//passes as soon as someMethod() has been called 2 times under 100 ms
verify(mock, timeout(100).times(2)).someMethod();

//equivalent: this also passes as soon as someMethod() has been called 2 times under 100 ms
verify(mock, timeout(100).atLeast(2)).someMethod();
```

###  Automatic instantiation
* now try to instantiate ***@Spy***
* instantiate ***@InjectMocks*** fields using **constructor** injection, **setter** injection, or **field** injection.

```java
public class ArticleManagerTest extends SampleBaseTestCase {

    @Mock private ArticleCalculator calculator;
    @Mock(name = "database") private ArticleDatabase dbMock; // note the mock name attribute
    @Spy private UserProvider userProvider = new ConsumerUserProvider();

    @InjectMocks private ArticleManager manager;

    @Test public void shouldDoSomething() {
        manager.initiateArticle();
        verify(database).addListener(any(ArticleListener.class));
    }
}

public class SampleBaseTestCase {

    private AutoCloseable closeable;

    @Before public void openMocks() {
        closeable = MockitoAnnotations.openMocks(this);
    }

    @After public void releaseMocks() throws Exception {
        closeable.close();
    }
}
```
```java
// constructor injection
public class ArticleManager {
    ArticleManager(ArticleCalculator calculator, ArticleDatabase database) {
        // parameterized constructor
    }
}
```
```java
// setter injection
public class ArticleManager {
    // no-arg constructor
    ArticleManager() {  }

    // setter
    void setDatabase(ArticleDatabase database) { }

    // setter
    void setCalculator(ArticleCalculator calculator) { }
}
```
```java
// Field injection
public class ArticleManager {
    private ArticleDatabase database;
    private ArticleCalculator calculator;
}
```

### Verification ignoring stubs
* ***verifyNoMoreInteractions()***
* ***inOrder()***

```java
verify(mock).foo();
verify(mockTwo).bar();

//ignores all stubbed methods:
verifyNoMoreInteractions(ignoreStubs(mock, mockTwo));

//creates InOrder that will ignore stubbed
InOrder inOrder = inOrder(ignoreStubs(mock, mockTwo));
inOrder.verify(mock).foo();
inOrder.verify(mockTwo).bar();
inOrder.verifyNoMoreInteractions();
```

## Mocking details
```java
//To identify whether a particular object is a mock or a spy:
Mockito.mockingDetails(someObject).isMock();
Mockito.mockingDetails(someObject).isSpy();

//Getting details like type to mock or default answer:
MockingDetails details = mockingDetails(mock);
details.getMockCreationSettings().getTypeToMock();
details.getMockCreationSettings().getDefaultAnswer();

//Getting invocations and stubbings of the mock:
MockingDetails details = mockingDetails(mock);
details.getInvocations();
details.getStubbings();

//Printing all interactions (including stubbing, unused stubs)
System.out.println(mockingDetails(mock).printInvocations());
```

###  Delegate calls to real instance
* Final classes
* Already custom proxied object
* Special objects with a finalize method

```java
import static org.mockito.AdditionalAnswers.delegatesTo;

final class DontYouDareToMockMe implements list { ... }

DontYouDareToMockMe awesomeList = new DontYouDareToMockMe();

List mock = mock(List.class, delegatesTo(awesomeList));
```

* if you use regular ***when().then()*** stubbing, called this might have some side effects.
* should use the ***doReturn|Throw|Answer|CallRealMethod*** stubbing style

```java
List listWithDelegate = mock(List.class, AdditionalAnswers.delegatesTo(awesomeList));

//Impossible: real method is called so listWithDelegate.get(0) throws IndexOutOfBoundsException (the list is yet empty)
when(listWithDelegate.get(0)).thenReturn("foo");

//You have to use doReturn() for stubbing
doReturn("foo").when(listWithDelegate).get(0);
```

### MockMaker API 
Google Android guys Mockito now offers an extension point that allows replacing the proxy generation engine
* default byte-buddy/asm/objenesis implementation
* can work with Dalvik virtual machine (.dex files)

### BDD style verification
Behavior Driven Development (BDD) style verification

```java
 given(dog.bark()).willReturn(2);

 // when
 ...

 then(person).should(times(2)).ride(bike);
```

### Spying or mocking abstract classes

```java
//convenience API, new overloaded spy() method:
SomeAbstract spy = spy(SomeAbstract.class);

//Mocking abstract methods, spying default methods of an interface (only available since 2.7.13)
Function<Foo, Bar> function = spy(Function.class);

//Robust API, via settings builder:
OtherAbstract spy = mock(OtherAbstract.class, withSettings()
.useConstructor().defaultAnswer(CALLS_REAL_METHODS));

//Mocking an abstract class with constructor arguments (only available since 2.7.14)
SomeAbstract spy = mock(SomeAbstract.class, withSettings()
.useConstructor("arg1", 123).defaultAnswer(CALLS_REAL_METHODS));

//Mocking a non-static inner abstract class:
InnerAbstract spy = mock(InnerAbstract.class, withSettings()
.useConstructor().outerInstance(outerInstance).defaultAnswer(CALLS_REAL_METHODS));
```

### Better generic support with deep stubs
```java
class Lines extends List<Line> {
    // ...
}
lines = mock(Lines.class, RETURNS_DEEP_STUBS);

// Now Mockito understand this is not an Object but a Line
Line line = lines.iterator().next();
```

### Java 8 Lambda Matcher Support
```java
// verify a list only had strings of a certain length added to it
// note - this will only compile under Java 8
verify(list, times(2)).add(argThat(string -> string.length() < 5));

// more complex Java 8 example - where you can specify complex verification behaviour functionally
verify(target, times(1)).receiveComplexObject(argThat(obj -> obj.getSubObject().get(0).equals("expected")));

// this can also be used when defining the behaviour of a mock under different inputs
// in this case if the input list was fewer than 3 items the mock returns null
when(mock.someMethod(argThat(list -> list.size()<3))).thenReturn(null);
```

###  Java 8 Custom Answer Support

```java
// answer by returning 12 every time
doAnswer(invocation -> 12).when(mock).doSomething();

// answer by using one of the parameters - converting into the right
// type as your go - in this case, returning the length of the second string parameter
// as the answer. This gets long-winded quickly, with casting of parameters.
doAnswer(invocation -> ((String)invocation.getArgument(1)).length())
    .when(mock).doSomething(anyString(), anyString(), anyString());
```
```java
// Example interface to be mocked has a function like:
void execute(String operand, Callback callback);

// the example callback has a function and the class under test
// will depend on the callback being invoked
void receive(String item);

// Java 8 - style 1
doAnswer(AdditionalAnswers.<String,Callback>answerVoid((operand, callback) -> callback.receive("dummy"))
    .when(mock).execute(anyString(), any(Callback.class));

// Java 8 - style 2 - assuming static import of AdditionalAnswers
doAnswer(answerVoid((String operand, Callback callback) -> callback.receive("dummy"))
    .when(mock).execute(anyString(), any(Callback.class));

// Java 8 - style 3 - where mocking function to is a static member of test class
private static void dummyCallbackImpl(String operation, Callback callback) {
    callback.receive("dummy");
}

doAnswer(answerVoid(TestClass::dummyCallbackImpl)
    .when(mock).execute(anyString(), any(Callback.class));

// returning a value is possible with the answer() function
// and the non-void version of the functional interfaces
// so if the mock interface had a method like
boolean isSameString(String input1, String input2);

// this could be mocked
// Java 8
doAnswer(AdditionalAnswers.<Boolean,String,String>answer((input1, input2) -> input1.equals(input2))))
    .when(mock).execute(anyString(), anyString());
```

### Meta data and generic type retention
```java
@MyAnnotation
class Foo {
    List<String> bar() { ... }
}

Class<?> mockType = mock(Foo.class).getClass();
assert mockType.isAnnotationPresent(MyAnnotation.class);
assert mockType.getDeclaredMethod("bar").getGenericReturnType() instanceof ParameterizedType;
```

### Mocking final types, enums and final methods
* Mocking final types and enums is incompatible with mock settings like 
    * explicitly serialization support ***withSettings().serializable()***
    * extra-interfaces ***withSettings().extraInterfaces()***
* Some methods cannot be mocked
    * Package-visible methods of java.*
    * native methods
### Mocking static methods
* possible to mock static method invocations within the ***current thread*** and a ***user-defined scope***.

```java
assertEquals("foo", Foo.method());
try (MockedStatic mocked = mockStatic(Foo.class)) {
    mocked.when(Foo::method).thenReturn("bar");
    assertEquals("bar", Foo.method());
    mocked.verify(Foo::method);
}
assertEquals("foo", Foo.method());
```

### Mocking object construction
* possible to generate mocks on constructor invocations within the ***current thread*** and a ***user-defined scope***

```java
assertEquals("foo", new Foo().method());
try (MockedConstruction mocked = mockConstruction(Foo.class)) {
    Foo foo = new Foo();
    when(foo.method()).thenReturn("bar");
    assertEquals("bar", foo.method());
    verify(foo).method();
}
assertEquals("foo", new Foo().method());
```

###  Mark classes as unmockable
* ***@DoNotMock*** for any class/interface you own that is problematic to mock