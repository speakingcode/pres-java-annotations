# pres-java-annotations
An Crashcourse in Java Annotations

**Annotations**
- form of metadata
- provide data about a program that is not part of the program itself
- no direct effect on the operation of the code they annotate

**Useful for**
- information for compiler — detect errors or suppress warnings
- compile-time/deployment-time processing — generate code, XML files, etc
- runtime processing

## Basics

### Format of Annotations
Basic format:
```java
@Entity
```

ex:
```java
@Override
void mySuperMethod() { ... }
```

Can include *elements* and values for them:
```java
@Author(
   name = "Benjamin Franklin",
   date = "3/27/2003"
)
class MyClass() { ... }
```

or:

```java
@SuppressWarnings(value = "unchecked")
void myMethod() { ... }
```

If there is just one element named 'value', the name can be omitted:
```java
@SuppressWarnings("unchecked")
void myMethod() { ... }
```

Multiple annotations on the same declaration:

```java
@Author(name = "Jane Doe")
@EBook
class MyClass { ... }
```

Repeating annotations (Java 1.8):
```java
@Author(name = "Jane Doe")
@Author(name = "John Smith")
class MyClass { ... }
```

### Where Annotations Can Be Used

**Declarations**
- classes
- fields
- methods
- 'other program elements'

**convention:** When used on a declaration, each annotation often appears on its own line

**Type** (Java 1.8)
Annotations can be applied to the use of types

Class instance creation expression:
```java
    new @Interned MyObject();
```

Type cast:
```java
    myString = (@NonNull String) str;
```

implements clause:

```java
  class UnmodifiableList<T> implements @Readonly List<@Readonly T> { ... }
```

Thrown exception declaration:
```java
    void monitorTemperature() throws
        @Critical TemperatureException { ... }
```

This form of annotation is called a type annotation

Sources:
https://docs.oracle.com/javase/tutorial/java/annotations/
