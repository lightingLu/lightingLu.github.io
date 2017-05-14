## 提高Java编程质量的若干个方法

### 通用

##### 1.java变长参数的方法

> 参数必须是最后一个，一个方法只能有一个变长参数

```JAVA
public void call(int people,int... num){
  System.out.ptrintln("CALL");  
}
```

##### 2.少用静态导入

会让代码变的难以维护，尽量使用类引用,把相同类型的变量放到一个类里面。

正确的

```JAVA
public double calCircleArea(double r){
  return Math.PI*r*r;
}
```

错误的

```java
public double calCircleArea(double r){
  return PI*r*r;
}
```

##### 3.三目运算符操作类型必须一致

```java
int i=100;
s=String.valueOf(i<100?90:100);
s2=String.valueOf(i<100?90:100.0);
System.out.println(s.equal(s2));
```

上面的代码输入为false,s=90,而s2为90.0所以两个值不相同。因为三目运算符判断过程中做了类型的转换

##### 4.序列化显示声明UID

由此JVM可以判断版本的改变

```JAVA
private static final long serialVersionUID=55799L;
```

##### 5.避免在序列化类在构造函数中为不变量赋值

```java
public class Person implements Serializable{
  public final String name;
  public Person(){
    name="混世魔王";
  }
}
  SerializationUtils.writeObject(new Person());

```

序列化后我们修改name的值

```java
public class Person implements Serializable{
  public final String name;
  public Person(){
    name="大天使";
  }
}
  Person p=SerializationUtils.readObject();
  Sysetem.out.println(p.name); 
```

那么我们再次打印那么值依旧是“混世魔王”，原因是：范序列化时构造函数不会执行

在序列化的过程中，不要使用构造函数为final变量赋值。