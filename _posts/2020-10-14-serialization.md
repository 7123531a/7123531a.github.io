## 序列化与反序列化

序列化是将对象转化为字节序列，反序列化是把字节序列还原为对象。

## 为什么需要序列化与反序列化

RPC是微服务的核心，服务之间的调用都是远程调用，RPC框架封装了远程调用的复杂性，让服务之间的调用看起来像是本地调用一样。那调用之间的数据是怎么交换的呢？ 这就用到了序列化与反序列化。调用端把数据序列化成字节序列以便于在网络传输，然后被调用方收到字节序列，还原数据。

## 序列化的方式

为了更高的数据传输效率，我们需要做到让序列化后的数据尽可能的精简，以便获得更少的网络传输时间。不同的序列化方案，具有不同的性能，下面将会介绍几种序列化方式。假设我们的domain对象是Person。

```java
package org.jinhe;

import java.io.Serializable;
import java.util.Objects;

public class Person implements Serializable {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Person person = (Person) o;
        return age == person.age &&
                Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}


```

### JDK 本身的序列化机制

```java
package org.jinhe;

import java.io.*;

public class JDKSerializer {

    //serialize
    public byte[] to(Person person) throws IOException {
        ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
        ObjectOutputStream stream = new ObjectOutputStream(byteArrayOutputStream);
        stream.writeObject(person);
        return byteArrayOutputStream.toByteArray();
    }

    //deserialize
    public Person from(byte[] bytes) throws IOException, ClassNotFoundException {
        ObjectInputStream objectInputStream = new ObjectInputStream(new ByteArrayInputStream(bytes));
        return (Person) objectInputStream.readObject();
    }

    public static void main(String[] args) throws IOException, ClassNotFoundException {
        Person person = new Person("eric", 20);
        JDKSerializer jdkSerializer = new JDKSerializer();
        byte[] bytes = jdkSerializer.to(person);

        Person restoredPerson = jdkSerializer.from(bytes);

        System.out.println("size: " + bytes.length);

        System.out.println(person == restoredPerson); //false

        System.out.println(person.equals(restoredPerson)); //true
    }
}

```
Java 本身提供了`ObjectOutputStream/ObjectInputStream`来序列化和反序列对象，只要对象实现`Serializable`标记接口。

优点是简单，不需要很多额外的工作，缺点是只适用Java平台，不能跨平台，序列化后的数据不够精简。将起另一篇文章来描述为何序列后的数据大。

### 自定义序列化

#### 文本格式 - Json

Restful 风格的API, 数据的载体通常是Json。

```javascript
{
    name: "eric",
    age: 10
}

```

#### Binary格式 - google protobuf

文本格式json的好处是易读易处理，但是还是不够精简，为了追求更高的效率，binary格式就诞生了。如何使用google protobuf就不展开了， 但他的原理就是， 还以前面的Person为例，序列化后的字节序列前几个字节表示name，后面的字节表示age，没有额外的空间浪费。