java中，使用关键字enum来定义枚举类，枚举类是一个特殊的类，大部分功能和普通类是一样的，区别为：
1. 枚举类继承了java.lang.Enum类，而不是默认的Object类。而java.lang.Enum类实现了java.lang.Serializable和java.lang.Comparable接口。
2. 非抽象的枚举类默认会使用final修饰，因此不能派生子类

<b>java中枚举类不可以被继承</b>

枚举类使用enum定义后在编译后默认继承了java.lang.Enum类，而不是普通的继承Object类。enum声明类继承了Serializable和Comparable两个接口。且采用enum声明后，该类会被编译器加上final声明(同String)，故该类是无法继承的。

枚举类的内部定义的枚举值就是该类的实例(且必须在第一行定义，当类初始化时，这些枚举值会被实例化)。

Java 5新增的enum关键词，可以定义枚举类。该类是一个特殊的类，可以定义自己的field、方法、可以实现接口，也可以定义自己的构造器。
