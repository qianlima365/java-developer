# JAVA中的List

## List接口

List接口是Collection接口的子接口，它是一个有序的集合，可以存储重复的元素。List接口提供了以下方法：

- `add(E e)`：将指定的元素添加到此列表的末尾。
- `add(int index, E element)`：将指定的元素插入此列表中的指定位置。
- `addAll(Collection<? extends E> c)`：将指定集合中的所有元素添加到此列表的末尾，按照指定集合的迭代器所指定的顺序添加这些元素。
- `addAll(int index, Collection<? extends E> c)`：将指定集合中的所有元素插入到此列表中的指定位置，按照指定集合的迭代器所指定的顺序添加这些元素。
- `clear()`：从此列表中移除所有元素。
- `contains(Object o)`：如果此列表包含指定的元素，则返回 true。

## 使用
### 排序
#### 方法1：简单的排序方法
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
public class sort {
　　public static void main(String[] args) {
　　    List<Integer> list = new ArrayList<Integer>();
　　    list.add(new Integer(3));
　　    list.add(new Integer(14));
　　    list.add(new Integer(6));
　　    list.add(new Integer(10));
       // 简单的int类似数据，可以使用Collections的sort方法即可完成排序
　　    Collections.sort(list);
　　    System.out.println(list.toString());
　　}
}
// 输出结果：
// [3,6,10,14]
```

#### 方案2：参数相对复杂的排序方式，参数为对象的情况
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
public class sort {
　　public static void main(String[] args) {
　　    List<User> list = new ArrayList<User>();
　　    list.add(new User("张三", 5));
　　    list.add(new User("李四", 30));
　　    list.add(new User("王五", 19));
　　    list.add(new User("王麻子", 17));
       // 按年龄排序
　　    Collections.sort(list); 
　　    System.out.println(list.toString());
　　}
}


class User implements Comparable<User>{

　　private String name; //姓名
　　private int age; // 年龄

　　public User(String name, int age) {
　　　　this.name = name;
　　　　this.age = age;
　　}
　　// getter && setter
　　public String getName() {
　　　　return name;
　　}
　　public void setName(String name) {
　　　　this.name = name;
　　}
　　public int getAge() {
　　　　return age;
　　}
　　public void setAge(int age) {
　　this.age = age;
　　}
　　@Override
　　public String toString() {
　　　　return "User [name=" + name + ", age=" + age + "]";
　　}

　　@Override
　　public int compareTo(User user) {           
      // 重写Comparable接口的compareTo方法，根据年龄升序排列，降序修改相减顺序即可
　　　　return this.age - user.getAge();
　　}
}
// 输出：
// [User [name=张三, age=5], User [name=王麻子, age=17], User [name=王五, age=19], User [name=李四, age=30]]

```

#### 方案3：使用匿名内部类实现排序
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
public class sort {
　　public static void main(String[] args) {
　　    List<User> list = new ArrayList<User>();
　　    list.add(new User("张三", 5));
　　    list.add(new User("李四", 30));
　　    list.add(new User("王五", 19));
　　    list.add(new User("王麻子", 17));
　　    Collections.sort(list, new Comparator<User>() {
　　　　    @Override
　　　　    public int compare(User u1, User u2) {
　　　　　　    int diff = u1.getAge() - u2.getAge();
　　　　　　    if (diff > 0) {
　　　　　　　　    return 1;
　　　　　　    }else if (diff < 0) {
　　　　　　　　    return -1;
　　　　　　    }
　　　　　　    return 0; //相等为0
　　　　    }
　　    }); // 按年龄排序
　　    System.out.println(list.toString());
　　}
}
// 输出：[User [name=张三, age=5], User [name=王麻子, age=17], User [name=王五, age=19], User [name=李四, age=30]]

```

#### 方案4：也是最简介的方案
```java
import java.util.Collections;
import java.util.List;
public class sort {
    public static void main(String[] args) {
　　    List<User> list = new ArrayList<User>();
　　    list.add(new User("张三", 5));
　　    list.add(new User("李四", 30));
　　    list.add(new User("王五", 19));
　　    list.add(new User("王麻子", 17));
       // 这一行代码即可完成排序
　　    list.sort(Comparator.comparing(User::getAge));
　　    System.out.println(list.toString());
　　}
}
// 输出：
// [User [name=张三, age=5], User [name=王麻子, age=17], User [name=王五, age=19], User [name=李四, age=30]]

```

总结：以上就是list排序经常用的几种方法，前两种方法都是通过实体实现Comparable接口并重写compareTo方法，第一种方式之所以没实现接口并实现compareTo方法，通过查看Interger类源码可以看到，java已经帮我们实现好了。