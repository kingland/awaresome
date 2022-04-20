## [AssertJ](https://assertj.github.io/doc/)

### Overview

AssertJ module
* **Core module** assertions for JDK types (String, Iterable, Stream, Path, File, Map…​)
* **Guava module** assertions for Guava types (Multimap, Optional…​)
* **Joda Time module** assertions for Joda Time types (DateTime, LocalDateTime)
* **Neo4J module** assertions for Neo4J types (Path, Node, Relationship…​)
* **DB module** assertions for relational database types (Table, Row, Column…​)
* **Swing module** API for functional testing of Swing user interfaces 

Supported Java versions
* **Core 3.x** requires **Java 8 or higher**
* **Core 2.x** requires Java 7 or higher

### Install
Maven
```xml
<dependency>
  <groupId>org.assertj</groupId>
  <artifactId>assertj-core</artifactId>
  <!-- use 2.9.1 for Java 7 projects -->
  <version>3.22.0</version>
  <scope>test</scope>
</dependency>
```

### Use Assertions class 
Assertions class is the only class you need to start using AssertJ

```java
import static org.assertj.core.api.Assertions.*;

// Or

import static org.assertj.core.api.Assertions.assertThat;  // main one
import static org.assertj.core.api.Assertions.atIndex; // for List assertions
import static org.assertj.core.api.Assertions.entry;  // for Map assertions
import static org.assertj.core.api.Assertions.tuple; // when extracting several properties at once
import static org.assertj.core.api.Assertions.fail; // use when writing exception tests
import static org.assertj.core.api.Assertions.failBecauseExceptionWasNotThrown; // idem
import static org.assertj.core.api.Assertions.filter; // for Iterable/Array assertions
import static org.assertj.core.api.Assertions.offset; // for floating number assertions
import static org.assertj.core.api.Assertions.anyOf; // use with Condition
import static org.assertj.core.api.Assertions.contentOf; // use with File assertions
```

### Alternative entry points
* ***WithAssertions*** interface
* ***BDDAssertions*** for BDD style 

```java
// WithAssertions example

import org.assertj.core.api.WithAssertions;

public class WithAssertionsExamples extends AbstractAssertionsExamples implements WithAssertions {

  // the data used are initialized in AbstractAssertionsExamples.

  @Test
  public void withAssertions_examples() {

    // assertThat methods come from WithAssertions - no static import needed
    assertThat(frodo.age).isEqualTo(33);
    assertThat(frodo.getName()).isEqualTo("Frodo").isNotEqualTo("Frodon");

    assertThat(frodo).isIn(fellowshipOfTheRing);
    assertThat(frodo).isIn(sam, frodo, pippin);
    assertThat(sauron).isNotIn(fellowshipOfTheRing);

    assertThat(frodo).matches(p -> p.age > 30 && p.getRace() == HOBBIT);
    assertThat(frodo.age).matches(p -> p > 30);
  }
}
```

```java
// BDDAssertions example

import static org.assertj.core.api.BDDAssertions.then;

public class BDDAssertionsExamples extends AbstractAssertionsExamples {

  // the data used are initialized in AbstractAssertionsExamples.

  @Test
  public void withAssertions_examples() {

    // then methods come from BDDAssertions.then static
    then(frodo.age).isEqualTo(33);
    then(frodo.getName()).isEqualTo("Frodo").isNotEqualTo("Frodon");

    then(frodo).isIn(fellowshipOfTheRing);
    then(frodo).isIn(sam, frodo, pippin);
    then(sauron).isNotIn(fellowshipOfTheRing);

    then(frodo).matches(p -> p.age > 30 && p.getRace() == HOBBIT);
    then(frodo.age).matches(p -> p > 30);
  }
}
```

### Supported type assertions
* **Common types**
```java
class BigDecimal;
class BigInteger;
class CharSequence;
class Class;
class Date;
class File;
class Future;
class CompletableFuture;
class InputStream;
class Iterable; //(including any kind of Collection)
class Iterator;
class List;
class Map;
class Object;
var Object[]; var Object[][]
class Optional;
class OptionalInt;
class OptionalLong;
class OptionalDouble;
class Path;
class Predicate;
class Stream;
class String;
class Throwable;
class Exception;
```
* **Primitive types and Wrapper**
```java
short;
class Short;
int; 
class Integer;
long; 
class Long;
byte; 
class Byte;
char;
class Character;
float;
class Float;
double;
class Double;
short[]
short[][]
int[]
int[][]
long[]
long[][]
byte[]
byte[][]
char[]
char[][]
float[]
float[][]
double[]
double[][]
```
* **Java 8 Temporal types**
```java
class Instant;
class LocalDate;
class LocalDateTime;
class LocalTime;
class OffsetDateTime
class OffsetTime;
class ZonedDateTime;
class Period;
```

* **Atomic types**
```java
//Atomic basic types
class AtomicInteger;
class AtomicLong;
class AtomicBoolean;

//Atomic array types
class AtomicIntegerArray;
class AtomicLongArray;

//Atomic reference types
class AtomicMarkableReference;
class AtomicStampedReferenceAssert;

//Atomic updater types
class AtomicIntegerFieldUpdater;
class AtomicLongFieldUpdater;
class AtomicReferenceFieldUpdater;

//Adder types
class LongAdder;
```

### Assertion description
description with ***as(String description, Object…​ args)***

```java
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, Race.HOBBIT);

// failing assertion, remember to call as() before the assertion!
assertThat(frodo.getAge()).as("check %s's age", frodo.getName())
                          .isEqualTo(100);
```

print each assertion description (when it is set), to do so call ***Assertions.setPrintAssertionsDescription(true);***. and ***Consumer***

```java
// initialize the description consumer
final StringBuilder descriptionReportBuilder = new StringBuilder(String.format("Assertions:%n"));
Consumer<Description> descriptionConsumer = desc -> descriptionReportBuilder.append(String.format("-- %s%n", desc));

// use the description consumer for any following assertions descriptions.
Assertions.setDescriptionConsumer(descriptionConsumer);

// execute some assertions
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, Race.HOBBIT);
assertThat(frodo.getName()).as("check name")
                          .isEqualTo("Frodo");
assertThat(frodo.getAge()).as("check age")
                          .isEqualTo(33);

// get the report
String descriptionReport = descriptionReportBuilder.toString();
```

### Overriding error message
with ***overridingErrorMessage()*** or ***withFailMessage()***.

```java
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, Race.HOBBIT);
TolkienCharacter sam = new TolkienCharacter("Sam", 38, Race.HOBBIT);
// failing assertion, remember to call withFailMessage/overridingErrorMessage before the assertion!
assertThat(frodo.getAge()).withFailMessage("should be %s", frodo)
                          .isEqualTo(sam);

//print:
//java.lang.AssertionError: should be TolkienCharacter [name=Frodo, age=33, race=HOBBIT]
```

error message is expensive to build, use the overloaded methods taking a Supplier<String> instead of a String

```java
//Lazy error message overriding
assertThat(player.isRookie()).overridingErrorMessage(() -> "Expecting Player to be a rookie but was not.")
                             .isTrue();

assertThat(player.isRookie()).withFailMessage(() -> "Expecting Player to be a rookie but was not.")
                             .isTrue();
```

###  Configuring AssertJ
TODO::

**Configuring single properties**
```java
Assertions.setAllowComparingPrivateFields(true);
Assertions.setAllowExtractingPrivateFields(false);
Assertions.setExtractBareNamePropertyMethods(false);
Assertions.setLenientDateParsing(true);
Assertions.setMaxElementsForPrinting(100);
Assertions.setMaxLengthForSingleLineDescription(250);
Assertions.setRemoveAssertJRelatedElementsFromStackTrace(true);
Assertions.useRepresentation(myRepresentation);
Assertions.registerCustomDateFormat(myCustomDateFormat);
Assertions.setPrintAssertionsDescription(true);
Assertions.setConsumerDescription(description -> writeToFile(description, report));
```
**AssertJ Configuration**

Since 3.13.0, AssertJ exposes a org.assertj.core.configuration.Configuration

```java
Configuration configuration = new Configuration();

configuration.setBareNamePropertyExtraction(false);
configuration.setComparingPrivateFields(false);
configuration.setExtractingPrivateFields(false);
configuration.setLenientDateParsing(true);
configuration.setMaxElementsForPrinting(1001);
configuration.setMaxLengthForSingleLineDescription(81);
configuration.setRemoveAssertJRelatedElementsFromStackTrace(false);

// don't forget to apply it!
configuration.applyAndDisplay();
```

### Common assertions
Abstract2DArrayAssert, AbstractBooleanAssert, AbstractCharSequenceAssert, AbstractClassAssert, AbstractCompletableFutureAssert, AbstractDateAssert, AbstractEnumerableAssert, AbstractFileAssert, AbstractFutureAssert, AbstractInputStreamAssert, AbstractIterableAssert, AbstractIteratorAssert, AbstractLongAdderAssert, AbstractObjectArrayAssert, AbstractObjectAssert, AbstractOptionalAssert, AbstractOptionalDoubleAssert, AbstractOptionalIntAssert, AbstractOptionalLongAssert, AbstractPeriodAssert, AbstractPredicateAssert, AbstractPredicateLikeAssert, AbstractSpliteratorAssert, AbstractTemporalAssert, AbstractUriAssert, AbstractUrlAssert, AtomicBooleanAssert, AtomicIntegerAssert, AtomicLongAssert, AtomicReferenceArrayAssert, AtomicReferenceAssert, RecursiveComparisonAssert
```java
package org.assertj.core.api;

public abstract class AbstractAssert<self extend AbstractAssert<self,actual>, actual> 
implements Assert<self,​actual> {
    ...
}

interface Assert<self extends Assert<self,actual>,actual> {
    // Verifies that the actual value does not have the same class as the given object.
    public self doesNotHaveSameClassAs​(Object other);

    // Verifies that the actual object does not have the same hashCode as the given object.
    public self doesNotHaveSameHashCodeAs​(Object other);

    //Verifies that actual actual.toString() is not equal to the given String.
    public self doesNotHaveToString​(String otherToString);

    // Verifies that the actual value has the same class as the given object.
    public self	hasSameClassAs​(Object other);

    // Verifies that the actual object has the same hashCode as the given object.
    public self hasSameHashCodeAs​(Object other);

    // Verifies that actual actual.toString() is equal to the given String.
    public self hasToString​(String expectedToString);

    // Verifies that the actual value is equal to the given one.
    public self isEqualTo​(Object expected);

    // Verifies that the actual value is exactly an instance of the given type.
    public self isExactlyInstanceOf​(Class<?> type);

    // Verifies that the actual value is present in the given iterable.
    public self isIn​(Iterable<?> values);

    // Verifies that the actual value is present in the given array of values.
    public self isIn​(Object... values);

    // Verifies that the actual value is an instance of the given type.
    public self isInstanceOf​(Class<?> type);

    // Verifies that the actual value is an instance of any of the given types.
    public self isInstanceOfAny​(Class<?>... types);

    // Verifies that the actual value is an instance of the given type satisfying the given requirements expressed as a Consumer.
    public <T> self isInstanceOfSatisfying​(Class<T> type, Consumer<T> requirements);

    // Verifies that the actual value is not equal to the given one.
    public self isNotEqualTo​(Object other);

    // Verifies that the actual value is not exactly an instance of given type.
    public self isNotExactlyInstanceOf​(Class<?> type);

    // Verifies that the actual value is not present in the given iterable.
    public self isNotIn​(Iterable<?> values);

    // Verifies that the actual value is not present in the given array of values.
    public self isNotIn​(Object... values);

    // Verifies that the actual value is not an instance of the given type.
    public self isNotInstanceOf​(Class<?> type);

    // Verifies that the actual value is not an instance of any of the given types.
    public self isNotInstanceOfAny​(Class<?>... types);

    // Verifies that the actual value is not null.
    public self	isNotNull();

    // Verifies that the actual value type is not in given types.
    public self isNotOfAnyClassIn​(Class<?>... types);

    // Verifies that the actual value is not the same as the given one, ie using == comparison.
    public self isNotSameAs​(Object other);

    // Verifies that the actual value is null.
    public void isNull();

    // Verifies that the actual value type is in given types.
    public self isOfAnyClassIn​(Class<?>... types);

    // Verifies that the actual value is the same as the given one, ie using == comparison.
    public self	isSameAs​(Object expected);

    ...
}
```

### Object assertions
AbstractAtomicFieldUpdaterAssert, AbstractAtomicReferenceAssert, AbstractComparableAssert, AbstractMapAssert, AbstractThrowableAssert, ObjectAssert, ThrowableAssertAlternative

```java
package org.assertj.core.api;

public abstract class AbstractObjectAssert<self extends AbstractObjectAssert<self,actual>,actual> extends AbstractAssert<self,actual> {
    public <T> self doesNotReturn​(T expected, Function<actual,​T> from);
    public self	hasAllNullFieldsOrProperties();
    public self hasAllNullFieldsOrPropertiesExcept​(String... propertiesOrFieldsToIgnore);
    public self hasFieldOrProperty​(String name);
    public self hasFieldOrPropertyWithValue​(String name, Object value);
    public self	hasNoNullFieldsOrProperties();
    public self hasNoNullFieldsOrPropertiesExcept​(String... propertiesOrFieldsToIgnore);
    public self hasOnlyFields​(String... expectedFieldNames);
    public self returns​(T expected, Function<actual,​T> from);
    public <T> self usingComparatorForFields​(Comparator<T> comparator, String... propertiesOrFields);
    public self 	usingComparatorForType​(Comparator<? super T> comparator, Class<T> type);

    ...
}
```

### String assertions
AbstractStringAssert, CharSequenceAssert

```java
public abstract class AbstractCharSequenceAssert<self extends AbstractCharSequenceAssert<self,actual>,actual extends CharSequence>
extends AbstractAssert<self,actual>
implements EnumerableAssert<self,​Character> {
    public self contains​(CharSequence... values);
    public self contains​(Iterable<? extends CharSequence> values);
    public self containsAnyOf​(CharSequence... values);
    public self	containsIgnoringCase​(CharSequence sequence);
    public self containsIgnoringWhitespaces​(CharSequence... values);
    public self containsOnlyDigits();
    public self containsOnlyOnce​(CharSequence sequence);
    public self containsOnlyWhitespaces();
    public self containsPattern​(CharSequence regex);
    public self containsPattern​(Pattern pattern);
    public self containsSequence​(CharSequence... values);
    public self containsSequence​(Iterable<? extends CharSequence> values);
    public self containsSubsequence​(CharSequence... values);
    public self containsSubsequence​(Iterable<? extends CharSequence> values);
    public self containsWhitespaces();
    public self doesNotContain​(CharSequence... values);
    public self doesNotContain​(Iterable<? extends CharSequence> values);
    public self doesNotContainAnyWhitespaces();
    public self doesNotContainIgnoringCase​(CharSequence... values);
    public self doesNotContainOnlyWhitespaces();
    public self doesNotContainPattern​(CharSequence pattern);
    public self doesNotContainPattern​(Pattern pattern);
    public self doesNotEndWith​(CharSequence suffix);
    public self doesNotMatch​(CharSequence regex);
    public self doesNotMatch​(Pattern pattern);
    public self	doesNotStartWith​(CharSequence prefix);
    public self endsWith​(CharSequence suffix);
    public self hasLineCount​(int expectedLineCount);
    public self hasSameSizeAs​(CharSequence other);
    public self hasSameSizeAs​(Iterable<?> other);
    public self hasSameSizeAs​(Object other);
    public self hasSize​(int expected);
    public self hasSizeBetween​(int lowerBoundary, int higherBoundary);
    public self hasSizeGreaterThan​(int expected);
    public self hasSizeGreaterThanOrEqualTo​(int expected);
    public self hasSizeLessThan​(int expected);
    public self hasSizeLessThanOrEqualTo​(int expected);
    public self inHexadecimal();
    public self inUnicode();
    public self isBlank();
    public void isEmpty();
    public self isEqualToIgnoringCase​(CharSequence expected);
    public self isEqualToIgnoringNewLines​(CharSequence expected);
    public self isEqualToIgnoringWhitespace​(CharSequence expected);
    public self isEqualToNormalizingNewlines​(CharSequence expected);
    public self isEqualToNormalizingPunctuationAndWhitespace​(CharSequence expected);
    public self isEqualToNormalizingUnicode​(CharSequence expected);
    public self isEqualToNormalizingWhitespace​(CharSequence expected);
    public self isLowerCase();
    public self isMixedCase();
    public self isNotBlank();
    public self isNotEmpty();
    public self isNotEqualToIgnoringCase​(CharSequence expected);
    public self isNotEqualToIgnoringWhitespace​(CharSequence expected);
    public self isNotEqualToNormalizingWhitespace​(CharSequence expected);
    public self isNotJavaBlank();
    public self isNullOrEmpty();
    public self isSubstringOf​(CharSequence sequence);
    public self isUpperCase();
    public self isXmlEqualTo​(CharSequence expectedXml);
    public self isXmlEqualToContentOf​(File xmlFile);
    public self matches​(CharSequence regex);
    public self matches​(Pattern pattern);
    public self startsWith​(CharSequence prefix);
    public self usingComparator​(Comparator<? super actual> customComparator);
    public self usingComparator​(Comparator<? super ACTUAL> customComparator, String customComparatorDescription);
    public self usingDefaultComparator();
    
    ...
}
```

### Iterable assertions
AbstractCollectionAssert, ClassBasedNavigableIterableAssert, FactoryBasedNavigableIterableAssert

```java
public abstract class AbstractIterableAssert<self extends AbstractIterableAssert<self, actual, el, el_as>, actual extends Iterable<? extends el>, el, el_as extends AbstractAssert<el_as, el>>
extends AbstractAssert<self, actual>
implements ObjectEnumerableAssert<self, el> {
    public self allMatch​(Predicate<? super el> predicate);
    public self allMatch​(Predicate<? super el> predicate, String predicateDescription);
    public self allSatisfy​(Consumer<? super el> requirements);
    public selfallSatisfy​(ThrowingConsumer<? super el> requirements);
    public self anyMatch​(Predicate<? super el> predicate);
    public self anySatisfy​(Consumer<? super el> requirements);
    public self anySatisfy​(ThrowingConsumer<? super el> requirements);
    public self are​(Condition<? super el> condition);
    public self areAtLeast​(int times, Condition<? super el> condition);
    public self areAtLeastOne​(Condition<? super el> condition);
    public self areAtMost​(int times, Condition<? super el> condition);
    public self areExactly​(int times, Condition<? super el> condition);
    public self areNot​(Condition<? super el> condition);
    public self contains​(el... values);
    public self containsAll​(Iterable<? extends el> iterable);
    public self containsAnyElementsOf​(Iterable<? extends el> iterable);
    public self containsAnyOf​(el... values);
    public self containsExactly​(el... values);
    public self containsExactlyElementsOf​(Iterable<? extends el> iterable);
    protected self containsExactlyForProxy​(el[] values);
    public self containsExactlyInAnyOrder​(el... values);
    public self containsExactlyInAnyOrderElementsOf​(Iterable<? extends el> values);
    protected self containsExactlyInAnyOrderForProxy​(el[] values);
    protected self containsForProxy​(el[] values);
    public self containsNull();
    public self containsOnly​(el... values);
    protected self containsOnlyForProxy​(el[] values);
    public self containsOnlyNulls();
    public self containsOnlyOnce​(el... values);
    public self containsOnlyOnceElementsOf​(Iterable<? extends el> iterable);
    public self containsSequence​(el... sequence);
    public self containsSequence​(Iterable<? extends el> sequence);
    protected self containsSequenceForProxy​(el[] sequence);
    public self containsSubsequence​(el... subsequence);
    public self containsSubsequence​(Iterable<? extends el> subsequence);
    public self doesNotContain​(el... values);
    public self doesNotContainAnyElementsOf​(Iterable<? extends el> iterable);
    public self doesNotContainNull();
    public self doesNotContainSequence​(el... sequence);
    public self doesNotContainSequence​(Iterable<? extends el> sequence);
    public self doesNotContainSubsequence​(el... subsequence);
    public self doesNotContainSubsequence​(Iterable<? extends el> subsequence);
    public self doesNotHave​(Condition<? super actual> condition);
    public self doesNotHaveAnyElementsOfTypes​(Class<?>... unexpectedTypes);
    public self doesNotHaveDuplicates();
    public self doesNotHaveSameClassAs​(Object other);
    public self doNotHave​(Condition<? super el> condition);
    public el_as element​(int index);
    public self elements​(int... indices);
    public self endsWith​(el[] sequence);
    public self endsWith​(el first, el... rest);
    public self has​(Condition<? super actual> condition);
    public self hasAtLeastOneElementOfType​(Class<?> expectedType);
    public self hasExactlyElementsOfTypes​(Class<?>... types);
    public self hasOnlyElementsOfType​(Class<?> expectedType);
    public self hasOnlyElementsOfTypes​(Class<?>... types);
    public self hasSameClassAs​(Object other);
    public self hasSameSizeAs​(Iterable<?> other);
    public self hasSameSizeAs​(Object other);
    public self hasSize​(int expected);
    public self hasSizeBetween​(int lowerBoundary, int higherBoundary);
    public self hasSizeGreaterThan​(int boundary);
    public self hasSizeGreaterThanOrEqualTo​(int boundary);
    public self hasSizeLessThan​(int boundary);
    public self hasSizeLessThanOrEqualTo​(int boundary);
    public self hasToString​(String expectedToString);
    public self have​(Condition<? super el> condition);
    public self haveAtLeast​(int times, Condition<? super el> condition);
    public self haveAtLeastOne​(Condition<? super el> condition);
    public self haveAtMost​(int times, Condition<? super el> condition);
    public self haveExactly​(int times, Condition<? super el> condition);
    public self is​(Condition<? super actual> condition);
    public self isEmpty();
    public self isEqualTo​(Object expected);
    public self isExactlyInstanceOf​(Class<?> type);
    public self isIn​(Iterable<?> values);
    public self isIn​(Object... values);
    public self isInstanceOf​(Class<?> type);
    public self isInstanceOfAny​(Class<?>... types)
    public self isNot​(Condition<? super actual> condition)
    public self isNotEmpty()
    public self isNotEqualTo​(Object other)
    public self isNotExactlyInstanceOf​(Class<?> type)
    public selfisNotIn​(Iterable<?> values);
    public self isNotIn​(Object... values)
    public self isNotInstanceOf​(Class<?> type);
    public self isNotInstanceOfAny​(Class<?>... types);
    public self isNotNull()
    public self isNotOfAnyClassIn​(Class<?>... types);
    public self isNotSameAs​(Object other);
    public self isNullOrEmpty();
    public self isOfAnyClassIn​(Class<?>... types);
    public self isSameAs​(Object expected);
    public self isSubsetOf​(ELEMENT... values);
    public self isSubsetOf​(Iterable<? extends ELEMENT> values);
    ....
}
```

### Array assertions
ObjectArrayAssert

```java
public abstract class AbstractObjectArrayAssert<self extends AbstractObjectArrayAssert<self,el>, el>
extends AbstractAssert<self, el[]>
implements IndexedObjectEnumerableAssert<AbstractObjectArrayAssert<self,​ el>,​ el>, ArraySortedAssert<AbstractObjectArrayAssert<self, ​el>, ​el> {
    public self allMatch​(Predicate<? super el> predicate)
    public self allMatch​(Predicate<? super el> predicate, String predicateDescription)
    public self allSatisfy​(Consumer<? super el> requirements)
    public self allSatisfy​(ThrowingConsumer<? super el> requirements)
    public self anyMatch​(Predicate<? super el> predicate)
    public self anySatisfy​(Consumer<? super el> requirements)
    public self anySatisfy​(ThrowingConsumer<? super el> requirements)
    public self are​(Condition<? super el> condition)
    public self areAtLeast​(int times, Condition<? super el> condition)
    public self areAtLeastOne​(Condition<? super el> condition)
    public self areAtMost​(int times, Condition<? super el> condition)
    public self areExactly​(int times, Condition<? super el> condition)
    public self areNot​(Condition<? super el> condition)
    public self contains​(el... values)
    public self contains​(el value, Index index)
    public self containsAll​(Iterable<? extends el> iterable)
    public self containsAnyElementsOf​(Iterable<? extends el> iterable)
    public self containsAnyOf​(el... values)
    public self containsExactly​(el... values)
    public self containsExactlyElementsOf​(Iterable<? extends el> iterable)
    public self containsExactlyInAnyOrder​(el... values)
    public self containsExactlyInAnyOrderElementsOf​(Iterable<? extends el> values)
    public self containsNull()
    public self containsOnly​(el... values)
    public self containsOnlyNulls()
    public self containsOnlyOnce​(el... values)
    public self containsOnlyOnceElementsOf​(Iterable<? extends el> iterable)
    public self containsSequence​(el... sequence)
    public self containsSequence​(Iterable<? extends el> sequence)
    public self containsSubsequence​(el... subsequence)
    public self containsSubsequence​(Iterable<? extends el> subsequence)
    public self doesNotContain​(el... values)
    public selfdoesNotContain​(el value, Index index)
    public self doesNotContainAnyElementsOf​(Iterable<? extends el> iterable)
    public self doesNotContainNull()
    public self doesNotContainSequence​(el... sequence)
    public self doesNotContainSequence​(Iterable<? extends el> sequence)
    public self doesNotContainSubsequence​(el... subsequence)
    public self doesNotContainSubsequence​(Iterable<? extends el> subsequence)
    public self doesNotHaveAnyElementsOfTypes​(Class<?>... unexpectedTypes)
    public self doesNotHaveDuplicates()
    public self doNotHave​(Condition<? super el> condition)
    public self endsWith​(el[] sequence)
    public self endsWith​(el first, el... sequence)
    public self hasAtLeastOneElementOfType​(Class<?> type);
    public self hasExactlyElementsOfTypes​(Class<?>... expectedTypes)
    public self hasOnlyElementsOfType​(Class<?> type)
    public self hasOnlyElementsOfTypes​(Class<?>... types)
    public self hasOnlyOneElementSatisfying​(Consumer<? super el> elementAssertions)
    public self hasSameSizeAs​(Iterable<?> other)
    public self hasSameSizeAs​(Object other)
    public self hasSize​(int expected)
    public self hasSizeBetween​(int lowerBoundary, int higherBoundary)
    public self hasSizeGreaterThan​(int boundary)
    public self hasSizeGreaterThanOrEqualTo​(int boundary)
    public self hasSizeLessThan​(int boundary)
    public self hasSizeLessThanOrEqualTo​(int boundary)
    public self have​(Condition<? super el> condition)
    public self haveAtLeast​(int times, Condition<? super el> condition)
    public self haveAtLeastOne​(Condition<? super el> condition)
    public self haveAtMost​(int times, Condition<? super el> condition)
    public self haveExactly​(int times, Condition<? super el> condition)
    public self inBinary()
    public self inHexadecimal()
    public void isEmpty();
    public self isNotEmpty();
    public void isNullOrEmpty();
    public self isSorted();
    public self isSortedAccordingTo​(Comparator<? super el> comparator);
    public self isSubsetOf​(el... values);
    public self isSubsetOf​(Iterable<? extends el> values);
    public self noneMatch​(Predicate<? super el> predicate);
    public self noneSatisfy​(Consumer<? super el> restrictions);
    public self noneSatisfy​(ThrowingConsumer<? super el> restrictions);
    public self satisfiesExactly​(Consumer<? super el>... requirements);
    public self satisfiesExactly​(ThrowingConsumer<? super el>... requirements);
    public self satisfiesExactlyInAnyOrder​(Consumer<? super el>... requirements);
    public self satisfiesExactlyInAnyOrder​(ThrowingConsumer<? super el>... requirements);

    ...
}
```

### focus features 
* checking ***iterables/arrays*** content
* Running assertions on some elements (***any, all, none***)
* ***Navigating*** to a given element to check it
* ***Filtering*** elements before asserting]
* ***Extracting/mapping*** elements before asserting
* ***Comparing elements*** with a specific comparator

### checking iterables/arrays content
* ***contains*** Verifies that the actual iterable/array contains the given values in any order
* ***containsOnly***​ Verifies that the actual group contains only the given values and nothing else in any order and ignoring duplicates 
* ***containsExactly​*** Verifies that the actual iterable/array contains exactly the given values and nothing else **in order**
* ***containsExactlyInAnyOrder***​ Verifies that the actual iterable/array contains exactly the given values and nothing else **in any order**
* ***containsSequence***​ Verifies that the actual group contains the given sequence in the correct order and **without extra values between the sequence values**
* ***containsSubsequence***​ Verifies that the actual group contains the given subsequence in the correct order **possibly with other values between them**
* ***containsOnlyOnce*** Verifies that the actual iterable/array contains the given values only once
* ***containsAnyOf***​ Verifies that the actual iterable/array contains at least one of the given values (like an or operator on the given values)

### Satisfy
* ***allSatisfy*** 
* ***anySatisfy***
* ***conversely*** 
* ***noneSatisfy***

```java
List<TolkienCharacter> hobbits = list(frodo, sam, pippin);

// all elements must satisfy the given assertions
assertThat(hobbits).allSatisfy(character -> {
  assertThat(character.getRace()).isEqualTo(HOBBIT);
  assertThat(character.getName()).isNotEqualTo("Sauron");
});

// at least one element must satisfy the given assertions
assertThat(hobbits).anySatisfy(character -> {
  assertThat(character.getRace()).isEqualTo(HOBBIT);
  assertThat(character.getName()).isEqualTo("Sam");
});

// no element must satisfy the given assertions
assertThat(hobbits).noneSatisfy(character -> assertThat(character.getRace()).isEqualTo(ELF));
```

### Match
* ***Predicate*** 
* ***allMatch*** 
* ***anyMatch*** 
* ***conversely*** 
* ***noneMatch***

```java
List<TolkienCharacter> hobbits = list(frodo, sam, pippin);

assertThat(hobbits).allMatch(character -> character.getRace() == HOBBIT, "hobbits")
                   .anyMatch(character -> character.getName().contains("pp"))
                   .noneMatch(character -> character.getRace() == ORC);
```

### First / last / element(index)
* ***first*** 
* ***last*** 
* ***element(index)***
* ***InstanceOfAssertFactory*** (option)

```java
// only object assertions available after navigation
Iterable<TolkienCharacter> hobbits = list(frodo, sam, pippin);
assertThat(hobbits).first().isEqualTo(frodo);
assertThat(hobbits).element(1).isEqualTo(sam);
assertThat(hobbits).last().isEqualTo(pippin);

// strongly typed String assertions after navigation
Iterable<String> hobbitsName = list("frodo", "sam", "pippin");

// STRING is an InstanceOfAssertFactory from org.assertj.core.api.InstanceOfAssertFactories.STRING
// as() is just synthetic sugar for readability
assertThat(hobbitsName).first(as(STRING))
                       .startsWith("fro")
                       .endsWith("do");
assertThat(hobbitsName).element(1, as(STRING))
                       .startsWith("sa")
                       .endsWith("am");
assertThat(hobbitsName).last(as(STRING))
                       .startsWith("pip")
                       .endsWith("pin");

// alternative for strongly typed assertions
assertThat(hobbitsName, StringAssert.class).first()
                                           .startsWith("fro")
                                           .endsWith("do");
```


### Filtering with a Predicate
* ***filteredOn***
* ***containsOnly***

```java
assertThat(fellowshipOfTheRing).filteredOn( character -> character.getName().contains("o") )
                               .containsOnly(aragorn, frodo, legolas, boromir);
```

### Filtering on a property or a field
* specify the property/field name to filter 
* filter first tries to get the value from a property, field
* private fields is supported by default, disabled by ***Assertions.setAllowExtractingPrivateFields(false)***.
* supports nested properties/fields. if ***"address.street"*** is null. ***"address.street.name"*** will return null 
* ***not*** (operation)
* ***in*** (operation)
* ***notIn*** (operation)

```java
import static org.assertj.core.api.Assertions.in;
import static org.assertj.core.api.Assertions.not;
import static org.assertj.core.api.Assertions.notIn;
...

// filters use introspection to get property/field values
assertThat(fellowshipOfTheRing).filteredOn("race", HOBBIT)
                               .containsOnly(sam, frodo, pippin, merry);

// nested properties are supported
assertThat(fellowshipOfTheRing).filteredOn("race.name", "Man")
                               .containsOnly(aragorn, boromir);

// you can apply different comparison
assertThat(fellowshipOfTheRing).filteredOn("race", notIn(HOBBIT, MAN))
                               .containsOnly(gandalf, gimli, legolas);

assertThat(fellowshipOfTheRing).filteredOn("race", in(MAIA, MAN))
                               .containsOnly(gandalf, boromir, aragorn);

assertThat(fellowshipOfTheRing).filteredOn("race", not(HOBBIT))
                               .containsOnly(gandalf, boromir, aragorn, gimli, legolas);

// you can chain multiple filter criteria
assertThat(fellowshipOfTheRing).filteredOn("race", MAN)
                               .filteredOn("name", not("Boromir"))
                               .containsOnly(aragorn);
```

### Filtering on a function return value
* ***not*** (no support)
* ***in*** (no support)
* ***notIn*** (no support)
```java
assertThat(fellowshipOfTheRing).filteredOn(TolkienCharacter::getRace, HOBBIT)
                               .containsOnly(sam, frodo, pippin, merry);
```

### Filtering on null value
* ***filteredOnNull***

```java
TolkienCharacter pippin = new TolkienCharacter("Pippin", 28, HOBBIT);
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, HOBBIT);
TolkienCharacter merry = new TolkienCharacter("Merry", 36, HOBBIT);
TolkienCharacter mysteriousHobbit = new TolkienCharacter(null, 38, HOBBIT);

List<TolkienCharacter> hobbits = list(frodo, mysteriousHobbit, merry, pippin);

assertThat(hobbits).filteredOnNull("name"))
                   .singleElement()
                   .isEqualTo(mysteriousHobbit);
```

### Filtering elements matchin given assertions
```java
TolkienCharacter pippin = new TolkienCharacter("Pippin", 28, HOBBIT);
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, HOBBIT);
TolkienCharacter merry = new TolkienCharacter("Merry", 36, HOBBIT);
TolkienCharacter sam = new TolkienCharacter("Sam", 38, HOBBIT);

List<TolkienCharacter> hobbits = list(frodo, sam, merry, pippin);

assertThat(hobbits).filteredOnAssertions(hobbit -> assertThat(hobbit.age).isLessThan(34))
                   .containsOnly(frodo, pippin);
```

### Filtering with a Condition
* org.assertj.core.api.Condition

```java
import org.assertj.core.api.Condition;

Condition<Player> mvpStats= new Condition<Player>(player -> {
    return player.pointsPerGame() > 20 && (player.assistsPerGame() >= 8 || player.reboundsPerGame() >= 8);
  }, "mvp");

List<Player> players;
players.add(rose); // Derrick Rose : 25 ppg - 8 assists - 5 rebounds
players.add(lebron); // Lebron James : 27 ppg - 6 assists - 9 rebounds
players.add(noah); // Joachim Noah : 8 ppg - 5 assists - 11 rebounds

// noah does not have more than 20 ppg
assertThat(players).filteredOn(mvpStats)
                   .containsOnly(rose, lebron);
```

### Extracting elements values
 called some service and got a list (or an array) of TolkienCharacter
```java
List<TolkienCharacter> fellowshipOfTheRing = tolkienRespository.findHeroes();  // frodo, sam, aragorn ...

// requires creation of frodo and aragorn, the expected TolkienCharacters
assertThat(fellowshipOfTheRing).contains(frodo, aragorn);
```
extract the fields/properties
```java
// extract the names ...
List<String> names = fellowshipOfTheRing.stream().map(TolkienCharacter::getName).collect(toList());
// ... and finally assert something
assertThat(names).contains("Boromir", "Gandalf", "Frodo", "Legolas");
```

### Extracting single value per element
* specify the field/property to extract (or pass a Function)
* can access private fields/properties which is handy to check internals not exposed with public methods (lambda won’t work here)

```java
// "name" needs to be either a property or a field of the TolkienCharacter class
assertThat(fellowshipOfTheRing).extracting("name")
                               .contains("Boromir", "Gandalf", "Frodo", "Legolas")
                               .doesNotContain("Sauron", "Elrond");

// specifying nested field/property is supported
assertThat(fellowshipOfTheRing).extracting("race.name")
                               .contains("Man", "Maia", "Hobbit", "Elf");

// same thing with a lambda which is type safe and refactoring friendly:
assertThat(fellowshipOfTheRing).extracting(TolkienCharacter::getName)
                               .contains("Boromir", "Gandalf", "Frodo", "Legolas");

// same thing map an alias of extracting:
assertThat(fellowshipOfTheRing).map(TolkienCharacter::getName)
                               .contains("Boromir", "Gandalf", "Frodo", "Legolas");

// to have type safe extracting, use the second parameter to pass the expected property type:
assertThat(fellowshipOfTheRing).extracting("name", String.class)
                               .contains("Boromir", "Gandalf", "Frodo", "Legolas")
                               .doesNotContain("Sauron", "Elrond");
```

### Extracting multiple values
* extract several values from the elements to ***org.assertj.core.api.Assertions.tuple***

```java
// when checking several properties/fields you have to use tuples:
import static org.assertj.core.api.Assertions.tuple;

// extracting name, age and and race.name nested property
assertThat(fellowshipOfTheRing).extracting("name", "age", "race.name")
                               .contains(tuple("Boromir", 37, "Man"),
                                         tuple("Sam", 38, "Hobbit"),
                                         tuple("Legolas", 1000, "Elf"));

// same assertion with functions for type safety:
assertThat(fellowshipOfTheRing).extracting(TolkienCharacter::getName,
                                            tolkienCharacter -> tolkienCharacter.age,
                                            tolkienCharacter -> tolkienCharacter.getRace().getName())
                                .contains(tuple("Boromir", 37, "Man"),
                                          tuple("Sam", 38, "Hobbit"),
                                          tuple("Legolas", 1000, "Elf"));
```

### Extracting and flattening multiple values per element

```java
//teamMates property returning a List<Player>
Player jordan = ... // initialized with Pippen and Kukoc team mates
Player magic = ... // initialized with Jabbar and Worthy team mates
List<Player> reallyGoodPlayers = list(jordan, magic);

// check all team mates by specifying the teamMates property (Player has a getTeamMates() method):
assertThat(reallyGoodPlayers).flatExtracting("teamMates")
                             .contains(pippen, kukoc, jabbar, worthy);

// alternatively, you can use a Function for type safety:
assertThat(reallyGoodPlayers).flatExtracting(BasketBallPlayer::getTeamMates)
                             .contains(pippen, kukoc, jabbar, worthy);

// flatMap is an alias of flatExtracting:
assertThat(reallyGoodPlayers).flatMap(BasketBallPlayer::getTeamMates)
                             .contains(pippen, kukoc, jabbar, worthy);

// if you use extracting instead of flatExtracting the result would be a list of list of players so the assertion becomes:
assertThat(reallyGoodPlayers).extracting("teamMates")
                             .contains(list(pippen, kukoc), list(jabbar, worthy));

// extract a list of values, flatten them and use contains assertion
assertThat(fellowshipOfTheRing).flatExtracting("name", "race.name")
                               .contains("Frodo", "Hobbit", "Legolas", "Elf");

// same assertions with Functions:
assertThat(fellowshipOfTheRing).flatExtracting(TolkienCharacter::getName,
                                               tc -> tc.getRace().getName())
                               .contains("Frodo", "Hobbit", "Legolas", "Elf");
```

### Comparing elements with a specific comparator
```java
List<TolkienCharacter> fellowshipOfTheRing = list(frodo, sam, merry, pippin, gandald, legolas, boromir, aragorn, gimli);

// the fellowshipOfTheRing includes Gandalf but not Sauron ...
assertThat(fellowshipOfTheRing).contains(gandalf)
                               .doesNotContain(sauron);

// ... but if we compare only races, Sauron is in fellowshipOfTheRing since he's a Maia like Gandalf
assertThat(fellowshipOfTheRing).usingElementComparator((t1, t2) -> t1.getRace().compareTo(t2.getRace()))
                               .contains(sauron);
```

###  Exception assertions
* Checking the exception message
* Checking the exception cause and root cause
* BDD assertion style with catchThrowable and catchThrowableOfType
* assertThatThrownBy(code)
* assertThatExceptionOfType(exception class)
* Testing that no exception is thrown

### Checking exception message
Most of the assertions expecting a ***String*** can use the ***String.format*** syntax.

```java
Throwable throwable = new IllegalArgumentException("wrong amount 123");

assertThat(throwableWithMessage).hasMessage("wrong amount 123")
                                .hasMessage("%s amount %d", "wrong", 123)
                                // check start
                                .hasMessageStartingWith("wrong")
                                .hasMessageStartingWith("%s a", "wrong")
                                // check content
                                .hasMessageContaining("wrong amount")
                                .hasMessageContaining("wrong %s", "amount")
                                .hasMessageContainingAll("wrong", "amount")
                                // check end
                                .hasMessageEndingWith("123")
                                .hasMessageEndingWith("amount %s", "123")
                                // check with regex
                                .hasMessageMatching("wrong amount .*")
                                // check does not contain
                                .hasMessageNotContaining("right")
                                .hasMessageNotContainingAny("right", "price")
```

### Checking the cause
better approach is to navigate to the cause with ***getCause()*** and take advantage of all existing exception assertions

```java
// direct cause assertion are limited
NullPointerException cause = new NullPointerException("boom!");
Throwable throwable = new Throwable(cause);

assertThat(throwable).hasCause(cause) // <- direct cause
                     // hasCauseInstanceOf will match inheritance.
                     .hasCauseInstanceOf(NullPointerException.class)
                     .hasCauseInstanceOf(RuntimeException.class)
                     // hasCauseExactlyInstanceOf will match only exact same type
                     .hasCauseExactlyInstanceOf(NullPointerException.class);
```
```java
// navigate before checking
assertThat(throwable).getCause()
                     .hasMessage("boom!")
                     .hasMessage("%s!", "boom")
                     .hasMessageStartingWith("bo")
                     .hasMessageEndingWith("!")
                     .hasMessageContaining("boo")
                     .hasMessageContainingAll("bo", "oom", "!")
                     .hasMessageMatching("b...!")
                     .hasMessageNotContaining("bam")
                     .hasMessageNotContainingAny("bam", "bim")
                     // isInstanceOf will match inheritance.
                     .isInstanceOf(NullPointerException.class)
                     .isInstanceOf(RuntimeException.class)
                     // isExactlyInstanceOf will match only exact same type
                     .isExactlyInstanceOf(NullPointerException.class);
```

```java
// using assertThatExceptionOfType combined with havingCause
assertThatExceptionOfType(RuntimeException.class)
         .isThrownBy(() -> { throw new RuntimeException(new IllegalArgumentException("boom!")); })
         .havingCause()
         .withMessage("boom!");
```
### Checking the root cause
check the root cause directly with ***hasRootCause***, ***hasRootCauseMessage*** and ***hasRootCauseInstanceOf​*** ,better way is to navigate to the root cause with ***getRootCause()*** and take advantage of all existing exception assertions.
```java
NullPointerException rootCause = new NullPointerException("null!");
Throwable throwable = new Throwable(new IllegalStateException(rootCause));

// direct root cause check
assertThat(throwable).hasRootCause(rootCause)
                     .hasRootCauseMessage("null!")
                     .hasRootCauseMessage("%s!", "null")
                     // hasRootCauseInstanceOf will match inheritance
                     .hasRootCauseInstanceOf(NullPointerException.class)
                     .hasRootCauseInstanceOf(RuntimeException.class)
                     // hasRootCauseExactlyInstanceOf will match only exact same type
                     .hasRootCauseExactlyInstanceOf(NullPointerException.class);

// navigate to root cause and check
assertThat(throwable).getRootCause()
                     .hasMessage("null!")
                     .hasMessage("%s!", "null")
                     .hasMessageStartingWith("nu")
                     .hasMessageEndingWith("!")
                     .hasMessageContaining("ul")
                     .hasMessageContainingAll("nu", "ull", "l!")
                     .hasMessageMatching("n...!")
                     .hasMessageNotContaining("NULL")
                     .hasMessageNotContainingAny("Null", "NULL")
                     // isInstanceOf will match inheritance.
                     .isInstanceOf(NullPointerException.class)
                     .isInstanceOf(RuntimeException.class)
                     // isExactlyInstanceOf will match only exact same type
                     .isExactlyInstanceOf(NullPointerException.class);
```
```java
// using assertThatExceptionOfType combined with havingRootCause
assertThatExceptionOfType(RuntimeException.class)
         .isThrownBy(() -> { throw new RuntimeException(new IllegalArgumentException(new NullPointerException("root error"))); })
         .havingRootCause()
         .withMessage("root error");
```

### No cause
verify that a Throwable does not have a cause with ***hasNoCause()**

### BDD style
BDD aficionados can separate ***WHEN*** and ***THEN*** steps
* ***catchThrowable(ThrowingCallable)***
* ***Throwable*** 
* ***ThrowingCallable*** is a functional interface which can be expressed as a lambda

```java
// GIVEN
String[] names = { "Pier ", "Pol", "Jak" };
// WHEN
Throwable thrown = catchThrowable(() -> System.out.println(names[9]));
// THEN
then(thrown).isInstanceOf(ArrayIndexOutOfBoundsException.class)
            .hasMessageContaining("9");

// assertThat is also available but is less "BDD style"
assertThat(thrown).isInstanceOf(ArrayIndexOutOfBoundsException.class)
                  .hasMessageContaining("9");
```
* ***catchThrowableOfType*** for custom exception fields/properties

```java
class TextException extends Exception {
   int line;
   int column;

   public TextException(String msg, int line, int column) {
     super(msg);
     this.line = line;
     this.column = column;
   }
 }

 TextException textException = catchThrowableOfType(() -> { throw new TextException("boom!", 1, 5); },
                                                    TextException.class);
 // assertions succeed
 assertThat(textException).hasMessageContaining("boom");
 assertThat(textException.line).isEqualTo(1);
 assertThat(textException.column).isEqualTo(5);

 // fails as TextException is not a RuntimeException
 catchThrowableOfType(() -> { throw new TextException("boom!", 1, 5); }, RuntimeException.class);
```
* ***catchException***
* ***catchIllegalArgumentException***
* ***catchIllegalStateException***
* ***catchIndexOutOfBoundsException***
* ***catchIOException***
* ***catchNullPointerException***
* ***catchReflectiveOperationException***
* ***catchRuntimeException***

```java
IOException iOException = catchIOException(() -> {throw new IOException("boom!");});
// assertions succeed
assertThat(iOException).hasMessage("boom!");

// succeeds as catchIOException returns null when the code does not throw any exceptions
assertThat(catchIOException(() -> {})).isNull();

// fails as the thrown instance is not an IOException
catchIOException(() -> {throw new Exception("boom!");});
```

### assertThatThrownBy
***assertThatThrownBy(ThrowingCallable)*** is an alternative to ***catchThrowable***, use it if you find more readable.

```java
assertThatThrownBy(() -> { throw new Exception("boom!"); })
                    .isInstanceOf(Exception.class)
                    .hasMessageContaining("boom");
```

### assertThatExceptionOfType
***assertThatExceptionOfType*** is an alternative syntax that some people find more natural.
```java
assertThatExceptionOfType(IOException.class).isThrownBy(() -> { throw new IOException("boom!"); })
                                            .withMessage("%s!", "boom")
                                            .withMessageContaining("boom")
                                            .withNoCause();
```
Similarly to ***catchThrowableOfType***:
* assertThatNullPointerException
* assertThatIllegalArgumentException
* assertThatIllegalStateException
* assertThatIOException

```java
assertThatIOException().isThrownBy(() -> { throw new IOException("boom!"); })
                       .withMessage("%s!", "boom")
                       .withMessageContaining("boom")
                       .withNoCause();
```

### No exception is thrown
```java
// standard style
assertThatNoException().isThrownBy(() -> System.out.println("OK"));
// BDD style
thenNoException().isThrownBy(() -> System.out.println("OK"));
```
or similarly:

```java
// standard style
assertThatCode(() -> System.out.println("OK")).doesNotThrowAnyException();
// BDD style
thenCode(() -> System.out.println("OK")).doesNotThrowAnyException();
```

###  Field by field recursive comparison
* ***usingRecursiveComparison()***

```java
public class Person {
  String name;
  double height;
  Home home = new Home();
}

public class Home {
  Address address = new Address();
  Date ownedSince;
}

public static class Address {
  int number;
  String street;
}

Person sherlock = new Person("Sherlock", 1.80);
sherlock.home.ownedSince = new Date(123);
sherlock.home.address.street = "Baker Street";
sherlock.home.address.number = 221;

Person sherlock2 = new Person("Sherlock", 1.80);
sherlock2.home.ownedSince = new Date(123);
sherlock2.home.address.street = "Baker Street";
sherlock2.home.address.number = 221;

// assertion succeeds as the data of both objects are the same.
assertThat(sherlock).usingRecursiveComparison()
                    .isEqualTo(sherlock2);

// assertion fails as Person equals only compares references.
assertThat(sherlock).isEqualTo(sherlock2);
```

* ***isNotEqualTo***
```java
// equals not overridden in TolkienCharacter
TolkienCharacter frodo = new TolkienCharacter("Frodo", 33, HOBBIT);
TolkienCharacter frodoClone = new TolkienCharacter("Frodo", 33, HOBBIT);
TolkienCharacter youngFrodo = new TolkienCharacter("Frodo", 22, HOBBIT);

// Pass as equals compares object references
assertThat(frodo).isNotEqualTo(frodoClone);

// Fail as frodo and frodoClone are equals when doing a field by field comparison.
assertThat(frodo).usingRecursiveComparison()
                 .isNotEqualTo(frodoClone);

// Pass as one the age fields differ between frodo and youngFrodo.
assertThat(frodo).usingRecursiveComparison()
                 .isNotEqualTo(youngFrodo);
```

* ***withStrictTypeChecking()***
```java
// compare can be of different types but must have the same properties/fields.
public class Person {
  String name;
  double height;
  Person bestFriend;
}

Person sherlock = new Person("Sherlock", 1.80);
sherlock.bestFriend = new Person("Watson", 1.70);

Person sherlockClone = new Person("Sherlock", 1.80);
sherlockClone.bestFriend = new Person("Watson", 1.70);

// assertion succeeds as sherlock and sherlockClone have the same data and types
assertThat(sherlock).usingRecursiveComparison()
                    .withStrictTypeChecking()
                    .isEqualTo(sherlockClone);

// Let's now define a data structure similar to Person

public class PersonDTO {
  String name;
  double height;
  PersonDTO bestFriend;
}

PersonDTO sherlockDto = new PersonDTO("Sherlock", 1.80);
sherlockDto.bestFriend = new PersonDTO("Watson", 1.70);

// assertion fails as Person and PersonDTO are not compatible even though they have the same data
assertThat(sherlock).usingRecursiveComparison()
                    .withStrictTypeChecking()
                    .isEqualTo(sherlockDto);

// Let's define a subclass of Person

public class Detective extends Person {
  boolean busy;
}

Detective detectiveSherlock = new Detective("Sherlock", 1.80);
detectiveSherlock.bestFriend = new Person("Watson", 1.70);
detectiveSherlock.busy = true;

// assertion succeeds as Detective inherits from Person and
// only Person's fields are included into the comparison.
assertThat(sherlock).usingRecursiveComparison()
                    .withStrictTypeChecking()
                    .isEqualTo(detectiveSherlock);
```

### Ignoring fields in the comparison
* directly with ***ignoringFields(String…​ fieldsToIgnore)***
* regexes with ***ignoringFieldsMatchingRegexes(String…​ regexes)***
* types with ***ignoringFieldsOfTypes(Class…​ typesToIgnore)***

```java
Person sherlock = new Person("Sherlock", 1.80);
sherlock.home.address.street = "Baker Street";
sherlock.home.address.number = 221;

// strangely moriarty and sherlock have the same height!
Person moriarty = new Person("Moriarty", 1.80);
moriarty.home.address.street = "Crime Street";
moriarty.home.address.number = 221;

// assertion succeeds as name and home.address.street fields are ignored in the comparison
assertThat(sherlock).usingRecursiveComparison()
                    .ignoringFields("name", "home.address.street")
                    .isEqualTo(moriarty);

// assertion succeeds as once a field is ignored, its subfields are too
assertThat(sherlock).usingRecursiveComparison()
                    .ignoringFields("name", "home")
                    .isEqualTo(moriarty);

// ignoring fields matching regexes: name and home match .*me
assertThat(sherlock).usingRecursiveComparison()
                    .ignoringFieldsMatchingRegexes(".*me")
                    .isEqualTo(moriarty);

// ignoring null fields example:
sherlock.name = null;
sherlock.home.address.street = null;
assertThat(sherlock).usingRecursiveComparison()
                    .ignoringActualNullFields()
                    .isEqualTo(moriarty);

// ignore height and address fields by type:
Person tallSherlock = new Person("sherlock", 2.10);
tallSherlock.home.address.street = "Long Baker Street";
tallSherlock.home.address.number = 222;
assertThat(sherlock).usingRecursiveComparison()
                    .ignoringFieldsOfTypes(double.class, Address.class)
                    .isEqualTo(tallSherlock);
```

### Ignoring overridden equals
* ***ignoringOverriddenEqualsForTypes(Class…​)*** Any fields of these classes are compared recursively
* ***ignoringOverriddenEqualsForFields(String…​)*** Any given fields are compared recursively
* ***ignoringOverriddenEqualsForFieldsMatchingRegexes(String…​)*** Any fields matching one of these regexes are compared recursively
* ***ignoringAllOverriddenEquals()*** except for java types, all fields are compared field by field recursively

```java
public class Person {
  String name;
  double height;
  Home home = new Home();
}

public class Home {
  Address address = new Address();
}

public static class Address {
  int number;
  String street;

  // only compares number, ouch!
  @Override
  public boolean equals(final Object other) {
    if (!(other instanceof Address)) return false;
    Address castOther = (Address) other;
    return Objects.equals(number, castOther.number);
  }
}

Person sherlock = new Person("Sherlock", 1.80);
sherlock.home.address.street = "Baker Street";
sherlock.home.address.number = 221;

Person sherlock2 = new Person("Sherlock", 1.80);
sherlock2.home.address.street = "Butcher Street";
sherlock2.home.address.number = 221;

// assertion succeeds but that's not what we expected since the home.address.street fields differ
// but the equals implementation in Address does not compare them.
assertThat(sherlock).usingRecursiveComparison()
                    .isEqualTo(sherlock2);

// to avoid the previous issue, we force a recursive comparison on the Address type
// now this assertion fails as expected since the home.address.street fields differ.
assertThat(sherlock).usingRecursiveComparison()
                    .ignoringOverriddenEqualsForTypes(Address.class)
                    .isEqualTo(sherlock2);
```
### Ignoring all expected null fields
* ***ignoringExpectedNullFields()*** the recursive comparison will exclude from the comparison any null fields in the expected object.

```java
public class Person {
  String name;
  double height;
  Home home = new Home();
}
public class Home {
  Address address = new Address();
}
public static class Address {
  int number;
  String street;
}

Person sherlock = new Person("Sherlock", 1.80);
sherlock.home.address.street = "Baker Street";
sherlock.home.address.number = 221;

Person noName = new Person(null, 1.80);
noName.home.address.street = null;
noName.home.address.number = 221;

// assertion succeeds as name and home.address.street fields are ignored in the comparison
assertThat(sherlock).usingRecursiveComparison()
                    .ignoringExpectedNullFields()
                    .isEqualTo(noName);

// assertion fails as name and home.address.street fields are populated for sherlock but not for noName.
assertThat(noName).usingRecursiveComparison()
                  .ignoringExpectedNullFields()
                  .isEqualTo(sherlock);
```

### Ignoring all actual empty optional fields
* ***ignoringActualEmptyOptionalFields()*** makes the recursive comparison to ignore all actual empty optional fields (including ***Optional, OptionalInt, OptionalLong*** and ***OptionalDouble***).

```java
public class Person {
  String name;
  OptionalInt age;
  OptionalLong id;
  OptionalDouble height;
  Home home = new Home();
}

public class Home {
  String address;
  Optional<String> phone;
}

Person homerWithoutDetails = new Person("Homer Simpson");
homerWithoutDetails.home.address.street = "Evergreen Terrace";
homerWithoutDetails.home.address.number = 742;
homerWithoutDetails.home.phone = Optional.empty();
homerWithoutDetails.age = OptionalInt.empty();
homerWithoutDetails.id = OptionalLong.empty();
homerWithoutDetails.height = OptionalDouble.empty();

Person homerWithDetails = new Person("Homer Simpson");
homerWithDetails.home.address.street = "Evergreen Terrace";
homerWithDetails.home.address.number = 742;
homerWithDetails.home.phone = Optional.of("(939) 555-0113");
homerWithDetails.age = OptionalInt.of(39);
homerWithDetails.id = OptionalLong.of(123456);
homerWithDetails.height = OptionalDouble.of(1.83);

// assertion succeeds as phone is ignored in the comparison
assertThat(homerWithoutDetails).usingRecursiveComparison()
                               .ignoringActualEmptyOptionalFields()
                               .isEqualTo(homerWithDetails);

// assertion fails as phone, age, id and height are not ignored and are populated for homerWithDetails but not for homerWithoutDetails.
assertThat(homerWithDetails).usingRecursiveComparison()
                            .ignoringActualEmptyOptionalFields()
                            .isEqualTo(homerWithoutDetails);
```

### Specifying how to compare specific types or fields in the comparison
* ***withEqualsForFields(BiPredicate, String…​)*** or ***withComparatorForFields(Comparator, String…​)*** for one or multiple fields
* ***withEqualsForType(BiPredicate, Class)*** or ***withComparatorForType(Comparator, Class)*** for a given type
* using ***withEqualsForFields/withEqualsForType***, providing a ***BiPredicate*** is simpler than a Comparator

```java
public class TolkienCharacter {
  String name;
  double height;
}

TolkienCharacter frodo = new TolkienCharacter("Frodo", 1.2);
TolkienCharacter tallerFrodo = new TolkienCharacter("Frodo", 1.3);
TolkienCharacter reallyTallFrodo = new TolkienCharacter("Frodo", 1.9);

BiPredicate<Double, Double> closeEnough = (d1, d2) -> Math.abs(d1 - d2) <= 0.5;
// same comparison expressed with a Comparator:
// Comparator<Double> closeEnough = (d1, d2) -> Math.abs(d1 - d2) <= 0.5 ? 0 : 1;

// assertion succeeds
assertThat(frodo).usingRecursiveComparison()
                 .withEqualsForFields(closeEnough, "height")
                 .isEqualTo(tallerFrodo);

assertThat(frodo).usingRecursiveComparison()
                 .withEqualsForType(closeEnough, Double.class)
                 .isEqualTo(tallerFrodo);

// assertions fail
assertThat(frodo).usingRecursiveComparison()
                 .withEqualsForFields(closeEnough, "height")
                 .isEqualTo(reallyTallFrodo);

assertThat(frodo).usingRecursiveComparison()
                 .withEqualsForType(closeEnough, Double.class)
                 .isEqualTo(reallyTallFrodo);
```

### Overriding error messages for specific fields or types
* ***withErrorMessageForFields***
* ***withErrorMessageForType***

```java
//  override messages for all fields of a given type, example for Double:
withErrorMessageForType("Double field differ", Double.class)

// if Foo has a Bar field and both have an id field, one can register a message for Foo and Bar id
withErrorMessageForFields("id values differ", "foo.id", "foo.bar.id")

public class TolkienCharacter {
  String name;
  double height;
}

TolkienCharacter frodo = new TolkienCharacter("Frodo", 1.2);
TolkienCharacter tallerFrodo = new TolkienCharacter("Frodon", 1.4);

String message = "The field 'height' differ.";

// assertion fails
assertThat(frodo).usingRecursiveComparison()
                 .withErrorMessageForFields(message, "height")
                 .isEqualTo(tallerFrodo);
```

### Recursive comparison for iterable assertions
* ***usingRecursiveFieldByFieldElementComparator​(RecursiveComparisonConfiguration)*** enables the recursive comparison for any iterable

```java
public class Person {
  String name;
  boolean hasPhd;
}

public class Doctor {
  String name;
  boolean hasPhd;
}

Doctor drSheldon = new Doctor("Sheldon Cooper", true);
Doctor drLeonard = new Doctor("Leonard Hofstadter", true);
Doctor drRaj = new Doctor("Raj Koothrappali", true);

Person sheldon = new Person("Sheldon Cooper", false);
Person leonard = new Person("Leonard Hofstadter", false);
Person raj = new Person("Raj Koothrappali", false);
Person howard = new Person("Howard Wolowitz", false);

List<Doctor> doctors = list(drSheldon, drLeonard, drRaj);
List<Person> people = list(sheldon, leonard, raj);

RecursiveComparisonConfiguration configuration = RecursiveComparisonConfiguration.builder()
                                                                                 .withIgnoredFields("hasPhd")
                                                                                 .build();

// assertion succeeds as both lists contains equivalent items in order.
assertThat(doctors).usingRecursiveFieldByFieldElementComparator(configuration)
                   .contains(sheldon);

// assertion fails because leonard names are different.
leonard.setName("Leonard Ofstater");
assertThat(doctors).usingRecursiveFieldByFieldElementComparator(configuration)
                   .contains(leonard);

// assertion fails because howard is missing and leonard is not expected.
people = list(howard, sheldon, raj)
assertThat(doctors).usingRecursiveFieldByFieldElementComparator(configuration)
                   .contains(howard);
```
### Soft assertions
collects all assertion errors instead of stopping at the first one.

```java
@Test
void basic_soft_assertions_example() {
  SoftAssertions softly = new SoftAssertions(); 

  softly.assertThat("George Martin").as("great authors").isEqualTo("JRR Tolkien");  
  softly.assertThat(42).as("response to Everything").isGreaterThan(100); 
  softly.assertThat("Gandalf").isEqualTo("Sauron"); 

  // Don't forget to call assertAll() otherwise no assertion errors are reported!
  softly.assertAll(); 
}
```

### BDD Soft assertions
```java
@Test
void basic_bdd_soft_assertions_example() {
  BDDSoftAssertions softly = new BDDSoftAssertions();

  softly.then("George Martin").as("great authors").isEqualTo("JRR Tolkien");
  softly.then(42).as("response to Everything").isGreaterThan(100);
  softly.then("Gandalf").isEqualTo("Sauron");

  // Don't forget to call assertAll() otherwise no assertion errors are reported!
  softly.assertAll();
}
```

### Assumptions
if the assumptions are met the test is executed normally, if they don’t the test is aborted and marked as ignored.
* ***assumeThat()*** instead of ***assertThat()***

```java
@Test
public void when_an_assumption_is_not_met_the_test_is_ignored() {
  // since this assumption is obviously false ...
  assumeThat(frodo.getRace()).isEqualTo(ORC);
  // ... this assertion is not performed
  assertThat(fellowshipOfTheRing).contains(sauron);
}
```

```java
@Test
public void when_all_assumptions_are_met_the_test_is_run_normally() {
  // since this assumption is true ...
  assumeThat(frodo.getRace()).isEqualTo(HOBBIT);
  // ... this assertion is performed
  assertThat(fellowshipOfTheRing).doesNotContain(sauron);
}
```

### Extending assertions
* ***not(Condition)***: given condition must not be met
* ***allOf(Condition…​)***: all given conditions must be met
* ***anyOf(Condition…​)***: one of the given conditions must be met
* ***are(condition)/have(condition)***: all elements must meet the given condition
* ***areAtLeast(n, condition)/haveAtLeast(n, condition)***: at least n elements must meet the given condition
* ***areAtMost(n, condition)/haveAtMost(n, condition)***: no more than n elements must meet the given condition
* ***areExactly(n, condition)/haveExactly(n, condition)***: exactly n elements must meet the given condition
* ***is/isNot, have/doesNotHave, are/areNot***

```java
import static org.assertj.core.util.Sets.newLinkedHashSet;

static Set<String> JEDIS = newLinkedHashSet("Luke", "Yoda", "Obiwan");

// implementation with Java 8 lambda
Condition<String> jediPower = new Condition<>(JEDIS::contains, "jedi power");

// implementation with Java 7
Condition<String> jedi = new Condition<String>("jedi") {
  @Override
  public boolean matches(String value) {
    return JEDIS.contains(value);
  }
};
```

### Combining Conditions
combined with ***allOf(Condition…​)*** (logical and) or ***anyOf(Condition…​)*** (logical or), ***not*** can be used to invert one.

```java
List<String> SITHS = list("Sidious", "Vader", "Plagueis");
Condition<String> sith = new Condition<>(SITHS::contains, "sith");

assertThat("Vader").is(anyOf(jedi, sith));
assertThat("Solo").is(allOf(not(jedi), not(sith)));
```

### Custom Assertions
create a class inheriting from ***AbstractAssert*** and add your custom assertions methods.

* Creating your own assertion class
* Providing an entry point for all your custom assertions
* Providing an entry point for all your custom assertions and AssertJ ones ?
* Enabling soft assertions on your custom assertions

### Creating your own assertion class

```java
// getter/setter omitted for brevity
public class TolkienCharacter {
  private String name;
  private Race race; // Race is an enum
  private int age;
}
```
AbstractAssert will give you all the basic assertions : ***isEqualTo, isNull, satisfies, …​***
```java
public class TolkienCharacterAssert extends AbstractAssert<TolkienCharacterAssert, TolkienCharacter> { 

  public TolkienCharacterAssert(TolkienCharacter actual) { 
    super(actual, TolkienCharacterAssert.class);
  }

  public static TolkienCharacterAssert assertThat(TolkienCharacter actual) { 
    return new TolkienCharacterAssert(actual);
  }

  public TolkienCharacterAssert hasName(String name) { 
    // check that actual TolkienCharacter we want to make assertions on is not null.
    isNotNull();
    // check assertion logic
    if (!Objects.equals(actual.getName(), name)) {
      failWithMessage("Expected character's name to be <%s> but was <%s>", name, actual.getName());
    }
    // return this to allow chaining other assertion methods
    return this;
  }

  public TolkienCharacterAssert hasAge(int age) { 
    // check that actual TolkienCharacter we want to make assertions on is not null.
    isNotNull();
    // check assertion logic
    if (actual.getAge() != age) {
      failWithMessage("Expected character's age to be <%s> but was <%s>", age, actual.getAge());
    }
    // return this to allow chaining other assertion methods
    return this;
  }
}
```

### Using our custom assertion class

```java
// use assertThat from TolkienCharacterAssert to check TolkienCharacter
TolkienCharacterAssert.assertThat(frodo).hasName("Frodo");

// code is more elegant when TolkienCharacterAssert.assertThat is imported statically :
assertThat(frodo).hasName("Frodo");
```

### Providing an entry point for all custom assertions
```java
public class MyProjectAssertions {

  // give access to TolkienCharacter assertion
  public static TolkienCharacterAssert assertThat(TolkienCharacter actual) {
    return new TolkienCharacterAssert(actual);
  }

  // give access to TolkienCharacter Race assertion
  public static RaceAssert assertThat(Race actual) {
    return new RaceAssert(actual);
  }
}
```
Usage:
```java
import static my.project.MyProjectAssertions.assertThat;
import static org.assertj.core.api.Assertions.assertThat;
...

@Test
public void successful_custom_assertion_example() {
  // assertThat(TolkienCharacter) comes from my.project.MyProjectAssertions.assertThat
  assertThat(frodo).hasName("Frodo");

  // assertThat(String) comes from org.assertj.core.api.Assertions.assertThat
  assertThat("frodo").contains("do");
}
```