Java中有一个不太常用的关键字**assert**，是jdk1.4中加入的，平时开发中见的很少，不过在一些框架的源码里面的测试类里面，出现过不少它的踪迹。
assert意为断言的意思，**这个关键字可以判断布尔值的结果是否和预期的一样，如果一样就正常执行，否则会抛出AssertionError**。
assert的作用类似下面的一段代码：  

    boolean flag=false;;
    assert flag;

等于下面的代码：
    
    if (!flag)
    throw new AssertionError();

assert有两种用法：
- asset expression1;
- asset expression1 : expression2;

第一种用法在刚才的例子里面已经看到了，第二种用法，其实后面的表达式是允许我们自定义一个异常错误信息抛出。
一个例子：
   
    public static void checkName(String name){
       assert name!=null:"name is empty";
    }

执行：

    checkName(null);

结果如下：

    Exception in thread "main" java.lang.AssertionError: name is empty
    at basic.assert_test.DemoAssert.checkName(DemoAssert.java:19)
    at basic.assert_test.DemoAssert.main(DemoAssert.java:29)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke(Method.java:498)
    at com.intellij.rt.execution.application.AppMain.main(AppMain.java:147)


注意，在运行的时候，正常情况下assert是不会生效的，因为运行时assert是关闭的，想要使用，必须在VM启动参数中开启：
    
    -enableassertions 或者 -ea

这里基本能够看出来，使用assert命令来检验变量是比较精简的，不用像if语句那样。

既然assert能够精简的判断一些case，那么是不是所有的判断都应该使用assert判断呢？

实际上并不是这样，因为在运行时默认是关闭assert的，因为开启assert校验，也会损耗一定的性能，并且如果在关键部分的校验使用了assert验证，但是忘记开启assert功能，那么肯定会造成重大的失误，所以在生产环境下的代码，是不建议使用assert功能的，一般都是在测试类里面使用的比较多。
那么，既然没法在生产环境中使用assert功能，那么有没有替代的assert的组件？
答案是肯定的，这里面分几种情况：
- 在spring环境中，可以直接使用工具类Assert：
    
    Assert.notNull(obj, "object was null");
- 如果不在spring中，在普通的Java项目中可以使用jdk7中自带的Objects工具类：
    Objects.requireNonNull()

- 如果觉得Objects工具类功能较弱，可以引入junit工具类里面的Assert类：
    Assert.assertNotNull();


本文主要介绍了Java中assert关键字的功能和其用法，虽然assert关键字使用起来比较方便，但是由于其自身的缺点，所以不推荐在生产环境中使用，不过我们使用其他的替代方案来完成同样的功能，这一点需要注意。

