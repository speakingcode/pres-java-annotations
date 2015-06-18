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

## Predefined Annotation Types

### Used by the Java Language

- ```@Deprecated``` - indicates that the marked element is deprecated and should no longer be used. The compiler generates a warning whenever a program uses a method, class, or field with the @Deprecated annotation

```java
   // Javadoc comment follows
    /**
     * @deprecated
     * explanation of why it was deprecated
     */
    @Deprecated
    static void deprecatedMethod() { }
}
```

- ```@Override``` - informs the compiler that the element is meant to override an element declared in a superclass. If a method marked with @Override fails to correctly override a method in one of its superclasses, the compiler generates an error.

```java
   // mark method as a superclass method
   // that has been overridden
   @Override 
   int overriddenMethod() { }
```

- ```@SuppressWarnings``` - tells the compiler to suppress specific warnings that it would otherwise generate. In the following example, a deprecated method is used, and the compiler usually generates a warning.

   // use a deprecated method and tell 
   // compiler not to generate a warning
   @SuppressWarnings("deprecation")
    void useDeprecatedMethod() {
        // deprecation warning
        // - suppressed
        objectOne.deprecatedMethod();
    }

- ```@SafeVarargs``` - when applied to a method or constructor, asserts that the code does not perform potentially unsafe operations on its varargs parameter. When this annotation type is used, unchecked warnings relating to varargs usage are suppressed.

- ```@FunctionalInterface``` (Java 1.8) - indicates that the type declaration is intended to be a functional interface, as defined by the Java Language Specification.

### Annotations That Apply to Other Annotations (Meta-annotations)

 - ```@Retention``` - specifies how the marked annotation is stored:
  - ```RetentionPolicy.SOURCE``` – The marked annotation is retained only in the source level and is ignored by the compiler.
  - ```RetentionPolicy.CLASS``` – The marked annotation is retained by the compiler at compile time, but is ignored by the Java Virtual Machine (JVM).
  - ```RetentionPolicy.RUNTIME``` – The marked annotation is retained by the JVM so it can be used by the runtime environment.

- ```@Documented``` - indicates that whenever the specified annotation is used those elements should be documented using the Javadoc tool. (By default, annotations are not included in Javadoc.) For more information, see the Javadoc tools page.

- ```@Target``` - marks another annotation to restrict what kind of Java elements the annotation can be applied to. A target annotation specifies one of the following element types as its value:
  - ```ElementType.ANNOTATION_TYPE``` can be applied to an annotation type.
  - ```ElementType.CONSTRUCTOR``` can be applied to a constructor.
  - ```ElementType.FIELD``` can be applied to a field or property.
  - ```ElementType.LOCAL_VARIABLE``` can be applied to a local variable.
  - ```ElementType.METHOD``` can be applied to a method-level annotation.
  - ```ElementType.PACKAGE``` can be applied to a package declaration.
  - ```ElementType.PARAMETER``` can be applied to the parameters of a method.
  - ```ElementType.TYPE``` can be applied to any element of a class.

- ```@Inherited``` - indicates that the annotation type can be inherited from the super class. (This is not true by default.) When the user queries the annotation type and the class has no annotation for this type, the class' superclass is queried for the annotation type. This annotation applies only to class declarations.

- ```@Repeatable``` (Java 1.8) - indicates that the marked annotation can be applied more than once to the same declaration or type use. For more information, see Repeating Annotations.

## Declaring an Annotation Type

Annotation type definitions looks similar to an interface definition with the at-sign (@ = AT, as in annotation type). Annotation types are a form of interface.

The body of annotation definitions contains *annotation type element* declarations, which look a lot like methods. Note that they can define optional default values.

```java
public class Generation3List extends Generation2List {

   // Author: John Doe
   // Date: 3/17/2002
   // Current revision: 6
   // Last modified: 4/12/2004
   // By: Jane Doe
   // Reviewers: Alice, Bill, Cindy

   // class code goes here

}
```

Same metadata, with an annotation:

```java
@interface ClassPreamble {
   String author();
   String date();
   int currentRevision() default 1;
   String lastModified() default "N/A";
   String lastModifiedBy() default "N/A";
   // Note use of array
   String[] reviewers();
}
```

Use:

```java
@ClassPreamble (
   author = "John Doe",
   date = "3/17/2002",
   currentRevision = 6,
   lastModified = "4/12/2004",
   lastModifiedBy = "Jane Doe",
   // Note array notation
   reviewers = {"Alice", "Bob", "Cindy"}
)
public class Generation3List extends Generation2List {
// class code goes here
}
```

Note: To make the information in @```ClassPreamble``` appear in Javadoc-generated documentation, you must annotate the ```@ClassPreamble``` definition with the ```@Documented``` annotation:

```java
import java.lang.annotation.*;

@Documented
@interface ClassPreamble {
   // Annotation element definition
}
```
## Do Something Useful with Annotations

Let's make a simple implmentation of J-Unit's ```@Test``` annotation.

### Define the Annotation

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
   //to specify expected Exceptions
   Class expected();
}
```

### Write a Parser
Basic idea: use Java reflections to access the annotation information/attributes

```java
public class TestAnnotationParser {
   public void parse(Class<?> klass) throws Exception {
      Method[] methods = klass.getMethods();
      int pass = 0;
      int fail = 0;
      for (Method method : methods) {
         if (method.isAnnotationPresent(Test.class)) {
            // this is how you access the annotation elements
            Test test = method.getAnnotation(Test.class);
            Class expected = test.expected();
            try {
               method.invoke(null);
               pass++;
            } catch (Exception e) {
               if (Exception.class != expected) {
                  fail++;
               } else {
                  System.out.println("Expected exception encountered. No worries, man!");
                  pass++;
               }
            }
         }
      }
   }
}
```

### Use the Annotation

```java
class MyTest {
   @Test(expected NullPointerException)
   public void foo() throws NullPointerException{
      throw new NullPointerException()
   }
}
```

### Put it Together

```java
public class Demo {
   public static void main(String[] args) throws Exception {
      TestAnnotationParser parser = new TestAnnotationParser();
      parser.parse(MyTest.class);
   }
}
```


Sources:
- https://docs.oracle.com/javase/tutorial/java/annotations/
- http://isagoksu.com/2009/creating-custom-annotations-and-making-use-of-them/
