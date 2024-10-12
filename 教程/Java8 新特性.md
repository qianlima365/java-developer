## 教程
[官网特性介绍](https://www.oracle.com/java/technologies/javase/8-whats-new.html)


## 小特性
#### 在Java8的foreach()中使用return/break/continue
使用lambda表达式处理集合时，在使用foreach()处理集合时不能使用break和continue这两个方法，也就是说不能按照普通的for循环遍历集合时那样根据条件来中止遍历，而如果要实现在普通for循环中的效果时，可以使用return来达到，也就是说如果你在一个方法的lambda表达式中使用return时，这个方法是不会返回的，而只是执行下一次遍历，看如下的测试代码：
```java
List<String> list = Arrays.asList("123", "45634", "7892", "abch", "sdfhrthj", "mvkd");
list.stream().forEach(e ->{
	if(e.length() >= 5){
		return;
	}
	System.out.println(e);
});
// 输出：
// 123
// 7892
// abch
// mvkd
```
在foreach中使用return来结束本次遍历。
