# pres-java-annotations
java annotations intro

*Annotations*
- form of metadata
- provide data about a program that is not part of the program itself
- no direct effect on the operation of the code they annotate

*Useful for*
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


Sources:
https://docs.oracle.com/javase/tutorial/java/annotations/
