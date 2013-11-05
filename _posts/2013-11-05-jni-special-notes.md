---
layout: post
title: "JNI special notes"
description: ""
category: "technical"
tags: [java, jni, c, c++, utf-8]
---
{% include JB/setup %}

Java Native Interface (JNI) is the interface for C/C++ to interact with Java JVM.

Full JNI spec is located [here](http://docs.oracle.com/javase/6/docs/technotes/guides/jni/spec/jniTOC.html). I'm reading this and trying to implement a convenient Java-C++ accessor library.

Here are some special things that should be noticed in JNI.

### JNI is binary compatible with COM

Quote from JNI spec.

> Although Java objects are not exposed to the native code as COM objects, the JNI interface itself is binary-compatible with COM.

I don't know how to use this feature. However, it gives me some special ideas on how to call JVM functions. Maybe it could be used in some tricky code.

Plus, per JNI spec, COM IUnknown interface methods are NOT implemented. JNI just reserves spaces in function table. JVM vendor may fill these spaces with real implementation.

### Method ID could be invalided

Method ID is the data structure representing a Java method. If the class owns the method is alive, the ID is valid.

But when will a class be unloaded? I don't know. There is no python equivalent `del` in Java. Anyway, it implies that Java class is not always available after imported. It's an invisible JVM implementation detail for Java code, but it must be considered for any JNI code.

### JNI characters are encoded in a modified UTF-8 format

JNI characters (type `const char *`) are encoded in a modified UTF-8. It's widely used in JNI functions related to class/function/property names. It can be converted to Java Unicode string by JNI function `NewStringUTF`.

There are two differences comparing with standard UTF-8.

1. The null character is encoded to two bytes in Java. It means there is no zero byte in Java string.
2. Only one, two and three bytes characters are encoded in standard UTF-8. Four bytes and higher are encoded in a Java flavor encoding.

I don't know why Java doesn't prefer to standard UTF-8. The only reason I can guess is to eliminate zero in byte stream. But the reason for second difference is still unknown.

### Avoid using weak reference

JNI spec doesn't explicitly say "don't" in Weak Global References section. It just mentions plenty of down side of weak reference.

Maybe weak reference is a bit lighter than other references, but there is too many restrictions to use it correctly.

So my recommendation is to avoid it.

### Java constructor's return type is void

Before calling `NewObject`, caller must get class constructor's method ID. Construcotr's method name is formated in standard mangling format. The return value must be void (the `V`).

### CallNonvirtual<type>Method vs. Call<type>Method

`CallNonvirtual<type>Method` and `Call<type>Method` are different. The former calls functions defined on Java class. So `CallNonvirtual<type>Method` can skip proxy class logic. The later calls function just like Java code.

Although there is "Nonvirtual" in former's name, there is no hint saying virtual function cannot called by this funciton. I guess the `virtual` here means its original meaning, not Java keywork `abstract`. It's a bit confusing for C++ programmers like me when I glance at its name.

Quote from JNI spec.

> Call<type>Method routines invoke the method based on the class of the object, while CallNonvirtual<type>Method routines invoke the method based on the class, designated by the clazz parameter, from which the method ID is obtained. The method ID must be obtained from the real class of the object or from one of its superclasses.

### JNI *Critical functions

There are some functions end with `Critical`, e.g. `GetPrimitiveArrayCritical`. When calling such critical functions, no JNI function can be called until function return value is released.

JNI spec doesn't state the reason explicitly. I guess it's due to its implementation detail. The *Critical functions may return internal buffer directly to caller without any decoration, e.g. mark it as local/global reference. As JNI functions may trigger gc, this internal buffer may become invalid.

I have no data saying how these *Critical function perform against other non-critical and similar functions. Maybe *Critical function perform much better. It may be the only reason to use them.