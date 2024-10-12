# java中的Map

## 常用示例
### 1、判断map中存在的key

```
第一种方法：

HashMap map = new HashMap();
map.put("1", "value1");
map.put("2", "value2");
 
Iterator keys = map.keySet().iterator();
while(keys.hasNext()){
    String key = (String)keys.next();
    if("2".equals(key)){
        System.out.println("存在key");
    }
}

第二种方法：
boolean flag=map.containsKey("opt")

```

### 2、遍历Map中的元素

```java
// 方法一：在for-each循环中使用entries来遍历
Map<Integer, Integer> map = new HashMap<Integer, Integer>();
for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
    System.out.println("Key = " + entry.getKey());
    System.out.println("Value = " + entry.getValue());
}

// 方法二：在for-each循环中遍历keys或values。
Map<Integer, Integer> map = new HashMap<Integer, Integer>();
//遍历map中的键
for (Integer key : map.keySet()) {
    System.out.println("Key = " + key);
}
//遍历map中的值
for (Integer value : map.values()) {
    System.out.println("Value = " + value);
}


// 方法三：使用Iterator遍历
// 不使用泛型
Iterator entries = map.entrySet().iterator();
while (entries.hasNext()) {
    Map.Entry entry = (Map.Entry) entries.next();
    String key = (String) entry.getKey();
    String value = (String) entry.getValue();
    System.out.println("key = " + key + ", value = " + value);
}

// 使用泛型
Map<Integer, Integer> map = new HashMap<Integer, Integer>();
Iterator<Map.Entry<Integer, Integer>> entries = map.entrySet().iterator();
while (entries.hasNext()) {
    Map.Entry<Integer, Integer> entry = entries.next();
    System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
 
}

// 方法四：Java8 Stream 遍历Map
map.forEach((k, v) -> {
    System.out.println(k + " : " + v);
});


```