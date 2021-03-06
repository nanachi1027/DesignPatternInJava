## JavaPoet Notes

### What's JavaPoet ?
JavaPoet is a Java API for generating .java source file tools.

### The Generated Sources?
Source files generated by this tools can be usefule when doing things like:
> * annotation processing;
> * interacting with metadata files(db schemas, protocol formats like pb, thrift).

By generating codes, you eliminate the need to write boilerplate while also keeping a single source of truth for the metadata.

### Which popular project adopt this tool? (like hadoop, hive, zk, kafka?)
None of these projects... 

### How to use this? 
Here is a demonstration:
```$java
package com.nanachi.proxy.utils;

import com.squareup.javapoet.JavaFile;
import com.squareup.javapoet.MethodSpec;
import com.squareup.javapoet.TypeSpec;

import javax.lang.model.element.Modifier;
import java.io.IOException;

/**
 * In javapoet there are a series of instances constract a class:
 *
 * TypeSpec: specification of class, interface and enum.
 *
 * MethodSpec: specification of method
 *  - modifiers: {PUBLIC, PROTECTED, PRIVATE, STATIC, DEFAULT, FINAL, VOLATILE,
 *                SYNCHRONIZED, TRANSACTION, ABSTRACT, FINAL, STRICTFP(float strictly observes IEEE)}
 *
 * ParameterSpec: parameters pass to method.
 *
 * TypeName: name of variants
 *
 * CodeBlock: representation of code logic in method
 *
 * JavaFile: class dump to which file
 */
public class JavaPoetDemo {
    public static void main(String[] args) throws IOException {
        // here we declare a method with the name of 'main' in 'HelloWorld' class
        MethodSpec main = MethodSpec
                // name of the method
                .methodBuilder("main")
                // key words decorate the method same as modifiers in java reflect
                .addModifiers(Modifier.PUBLIC, Modifier.STATIC)
                // method return type
                .returns(void.class)
                // paramters' type and name (if multiple parameters will passing we adopt the ParameterSpec)
                .addParameter(String [].class, "args")
                //
                .addStatement("$T.out.println($S)", System.class, "Hello. JavaPoet!")
                // here we know that the MethodSpec use the Builder Pattern
                .build();

        // declare a class with the name of HelloWorld
        TypeSpec helloWorld = TypeSpec
                // declare the name of class
                .classBuilder("HelloWorld")
                // declare modifiers of thhe class it is public and final
                .addModifiers(Modifier.FINAL, Modifier.PUBLIC)
                // add methods in this class
                .addMethod(main)
                // call build method return instance of TypeSpec
                .build();

        // declare a output file name which stores the class info
        // we know that the JavaFile also design in builder pattern
        JavaFile javaFile = JavaFile
                // in builder method,
                // first param is the package name which gonna write to the generated class file
                // second param is the instance of TypeSpec
                .builder("com.nanachi.javapoet.generated", helloWorld)
                .build();
        // re-direct the output stream to console, we can also re-direct it to file
        javaFile.writeTo(System.out);
    }
}
```

#### Important component in JavaPoet 
- Note: all these instances are create in Builder Pattern
* `TypeSpec`: specification of class, interface and enum;
* `TypeName`: 
* `MethodSpec`: specification of method declared in specific class;
* `FieldSpec`: specify member variants in specific class;
* `ParameterSpec`: specific a series of parameters, if multiple params need to sending to a method;
* `CodeBlock`: support plants of ways to specify the values for their placeholders;
* `ClassName`: extends TypeName is adopted frequently to identify any declared class;
* `JavaFile`: generates output stream to output object, it supports re-direct stream to console, file ...

#### PlaceHolders in JavaPoet
* `S` represents Strings 
* `T` represents Types
* `N` represents Names for variants declared inside a class/interface created by `TypeSpec` by JavaPoet.

#### UML of this module shown as below
![uml image](https://raw.githubusercontent.com/nanachi1027/DesignPatternInJava/master/proxy-performance/doc/uml.png)

