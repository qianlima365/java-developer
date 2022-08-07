## EJB3 QL查询
EJB3的查询语言是一种和SQL非常类似的中间性和对象化查询语言。它可以被编译成不同的底层数据库能接受的SQL，从而屏蔽不同数据库的差异，确保用EJB3 QL查询语言编写的代码可在不同的数据库上运行。比起EJB 2.1的查询语言，EJB3可以运行期构造，支持多态，远远比EJB 2.1的查询更灵活和功能强大。在程序中使用EJB3 QL可以使用大写(SELECT)或者小写(select)，但不要大小写(比如:Select)混合使用。

### Query接口
javax.persistence.Query是EJB3查询操作的接口。
进行查询，首先要通过EntityManager 获得Query对象。
```
public class test() {
    public Query createQuery(String ejbqlString);                                 
    // 下面我们做一个最简单的查询，查询所有的com.redsoft.samples.Order类。                    
    final Query query = entityManager.createQuery( "select o from Order o");    
    final List result = query.getResultList();    
    final Iterator iterator = result.iterator();    
    while( iterator.hasNext() ){
        //　处理Order    
    }
}

```
"Order"在EJB3查询中称为com.redsoft.samples.Order类的abstract schema Type。查询Entity在EJB3 QL中都是针对Entity的Abstract Schema Type进行查询。 

在同一个EntityManagerFactory中，不允许同时有两个Abstract Schema Type相同的Entity类。比如不允许同时有com.redsoft.samples.Order和com.redsoft.foo.Order。
Query返回一个List的集合结果，我们可以用Iterator或者List.get( int )的方法来获得每个符合条件的Entity。

### Liberator EJB3 Persistence运行环境的Query查询
在构造Query的时候的只是把EJB3 QL编译成相应的SQL，但并不执行。只有当应用代码第一次调用Iterator.next(),Iterator.hasNext()或者List.get( int )方法的时候,编译后的SQL才会被真正的执行。

在Liberator EJB3 Persistence运行环境返回的结果集合中，并不保存所有的结果，而只是保持一个指向JDBC ResultSet或者缓存ResultSet的一个行(row)指针。只有当用户确实需要获得Entity实例的时候，才会从ResultSet中获取数据并填充到Entity实例中返回给应用。

如果查询结果结合中包含所有符合条件的Entity, Liberator EJB3 Persistence运行环境默认会自动缓存每次查询的结果。这样下次同样的查询操作就无需访问数据库，而直接从缓存中返回结果集合。但如果在下次查询操作之前，有针对被缓存的Entity类进行update/insert/delete操作，则缓存的结果集合会自动被清空，这样下次查询就会从数据库获得数据， 确保查询总是获得正确的结果，避免缓存脏数据。有时候查询会返回海量的数据。

Liberator EJB3运行环境采用了自适应的弱引用POJO管理机制，可以处理海量的数据。在我们的测试中和客户的环境可以处千万级别的数据量。但在处理大数据量的时候，注意关闭对集合结果的缓存。    
```
// 假设返回的结果数量巨大    
final Query query = entityManager.createQuery( "select o from Order o");        
// 关闭对查询结果的缓存    
query.setHint( Constants.QUERY_RESULT_CACHE, "false");    
final List result = query.getResultList();    
final Iterator iterator = result.iterator();        
// 这里我们可以处理海量的数据    
while( iterator.hasNext() ){        
    //　处理Order    
}
```

简单查询下面是一个简单查询的例子，可以看到和SQL的使用方法很类似。
```
final Query query = entityManager.createQuery( "select o from Order o where o.id = 1");
final Query query = entityManager.createQuery( "select o from Order o where o.id = 1 and o.confirm = 'true' ");
final Query query = entityManager.createQuery( "select o from Order o where o.id = 1 or o.customer = 'foo' ");
// address是Order类上的一个对象变量属性，Address有一个streetNumber的属性
final Query query = entityManager.createQuery( "select o from Order o where o.address.streetNumber >= 123" );
```
注意条件语句中查询的是Entity的属性，属性的名字需要和Entity中的属性变量名字一致。使用参数查询参数查询也和SQL中的参数查询类似。

EJB3 QL支持两种方式的参数定义方式: 命名参数和位置参数。
在同一个查询中只允许使用一种参数定义方式。

命名参数:
```
final Query query = entityManager.createQuery( "select o from Order o where o.id = :myId");
// 设置查询中的参数query.setParameter( "myId", 2 );
// 可以使用多个参数
final Query query = entityManager.createQuery( "select o from Order o where o.id = :myId and o.customer = :customerName" );
// 设置查询中的参数
query.setParameter( "myId", 2 );
query.setParameter( "customerName", "foo" );
```
注意不允许在同一个查询中使用两个相同名字的命名参数。

位置参数：
```
final Query query = entityManager.createQuery( "select o from Order o where o.id = ?1");
// 设置查询中的参数query.setParameter( 1, 2 );
// 1表示第一个参数，2是参数的值
//或者
final Query query = entityManager.createQuery( "select o from Order o where o.id = ?1").setParameter( 1, 2 );
// 可以使用多个参数
final Query query = entityManager.createQuery( "select o from Order o where o.id = ?1 and o.customer = ?2" );
// 设置查询中的参数query.setParameter( 1, 2 );
query.setParameter( 2, "foo" );
```
如果在未来需要在不同的EJB3 运行环境中运行，请使用位置参数，保证应用是可移植的。

### 排序(order by)
下面是一个简单查询的例子，可以看到和SQL的使用方法很类似。"ASC"和"DESC"分别为升序和降序，如果不显式注明，

EJB3 QL中默认为asc升序。
```
// 不注明的话，默认为asc为升序,
final Query query = entityManager.createQuery( "select o from Order o order by o.id");
final Query query = entityManager.createQuery( "select o from Order o order by o.address.streetNumber desc");
// desc为降序
final Query query = entityManager.createQuery( "select o from Order o order by o.id, o.address.streetNumber");
```
查询部分属性在前面的例子中，都是对针对Entity类的查询，返回的也是被查询的Entity类的实体。

EJB3 QL也允许我们直接查询返回我们需要的属性，而不是返回整个Entity。
在一些Entity中属性特别多的情况，这样的查询可以提高性能。
```
// 直接查询我们感兴趣的属性(列）
final Query query = entityManager.createQuery( "select o.id, o.customerName, o.address.streetNumber from Order o order by o.id");
// 集合中的不再是Order,而是一个Object[]对象数组
final List result = query.getResultList();// 第一个行Object[] row = result.get( 0 );
// 数组中的第一个值是id
int id = Integer.parseInt( row[0].toString() );
String customerName = row[1].toString();
String streetNumber = Integer.parseInt( row[2].toString() );
```

### 查询中使用构造器(Constructor)EJB3 
QL支持将查询的属性结果直接作为一个java class的构造器参数，并产生实体作为结果返回。
```
// 我们把需要的三个属性作为一个class( OrderHolder )的构造器参数，并使用new函数。
Query query = entityManager.createQuery("select new com.redsoft.ejb3.dummy.OrderHolder ( o.id, o.vender, o.partNumber ) FROM Order AS o");
// 集合中的结果是Order
HolderList result = query.getResultList();
```
该java class不需要是Entity Class。NEW要求java class使用全名。聚合查询(Aggregation)象大部分的SQL一样,

### 聚合函数
目前EJB QL支持的聚合函数包括：
	* AVG
	* SUM
	* COUNT
	* MAX
	* MIN
```
final Query query = entityManager.createQuery( "select MAX( o.id ) from Order where o.customerName='foo'");
// 如果我们知道结果是单个，我们可以用getSingleResult()获得结果
final Object result = query.getSingleResult();
// 由于Order中id的类型为long,
final Long max = (Long)result;
// 在一些数据库中max函数返回的结果的类型不一定于id对应的列的类型相符，更安全的方式可以采用string来转型
fina long max = Long.parseLong( result.toString() );
```

聚合函数也可以作为被查询的一个属性返回。

```
// 返回所有的订单的生产厂商和他们的订单价值总额
final Query query= entityManager.createQuery( "select o.vender, sum(o.amount) FROM Order o　group by o.vender");");
```

和SQL一样，如果聚合函数不是select...from的唯一一个返回列，需要使用"GROUP BY"语句。"GROUP BY"应该包含select语句中除了聚合函数外的所有属性。

```
// 返回所有的订单的生产厂商的的名字，货物号码和每种货物的订单价值总额
// 注意group by后面必须包含o.vender和o.partNumber
final Query query= entityManager.createQuery( "select o.vender, o.partNumber, sum(o.amount) FROM Order o　group by o.vender，o.partNumber");
```

如果还需要加上查询条件，需要使用"HAVING"条件语句而不是"WHERE"语句。

```
// 返回所有的订单的生产厂商是"foo"的货物号码和每种货物的订单价值总额
// 这里"having o.vender = 'foo'为条件
final Query query= entityManager.createQuery( "select o.vender, o.partNumber, sum(o.amount) FROM Order o　group by o.vender，o.partNumber having o.vender='foo'");
```

在"HAVING"语句里可以跟"WHERE"语句一样使用参数。

```
// 返回所有的订单的生产厂商是"foo"的货物号码和每种货物的订单价值总额
// 这里"having o.vender = 'foo'为条件
String sql = "select o.vender, o.partNumber, sum(o.amount) FROM Order o　group by o.vender，o.partNumber having o.vender=?1";
final Query query= entityManager.createQuery(sql);
query.setParameter( 1, "foo" );
final List result = query.getResultList();
```

关联(join)在EJB3 QL中，大部分的情况下，使用对象属性都隐含了关联(join)。

例如在以下查询中：
```
final Query query = entityManager.createQuery( "select o from Order owhere o.address.streetNumber=2000 order by o.id");
```

当这个句EJB3 QL编译成以下的SQL时就会自动包含了关联,EJB3 QL编译成SQL时关联默认取左关联(left join)。

```
select o.id, o.vender, o.partNumber, o.amount, addressTable.id, addressTable.streetNumberfrom orderTable as o left join addressTable where addressTable.streetNumber = 2000
```

但在一些情况下，我们仍然需要对关联做精确的控制。
因此EJB3 QL仍然支持和SQL中类似的关联语法：
```
	* left out join/left join
	* inner join
	* left join/inner join fetch
```

left join, left out join等义，都是允许符合条件的右边表达式中的Entiies为空。

```
// 返回所有地址为2000的Order纪录，不管Order中是否有OrderItem
final Query query = entityManager.createQuery( "select o from Order oleft join o.orderItems where o.address.streetNumber=2000 order by o.id");
```

由于EJB3 QL默认采用left join。这样的查询和以下的EJB3 QL其实是等价的。

```
// 返回所有地址为2000的Order纪录，不管Order中是否有OrderItem
final Query query = entityManager.createQuery( "select o from Order owhere o.address.streetNumber=2000 order by o.id");
```

需要显式使用left join/left outer join的情况会比较少。inner join要求右边的表达式必须返回Entities。

```
// 返回所有地址为2000的Order纪录，Order中必须有OrderItem
final Query query = entityManager.createQuery( "select o from Order oinner join o.orderItems where o.address.streetNumber=2000 order by o.id");
```

### left/left out/inner join 
fetch提供了一种灵活的查询加载方式来提高查询的性能。在默认的查询中，Entity中的集合属性默认不会被关联，集合属性默认是缓加载( lazy-load )。

```
// 默认EJB3 QL编译后不关联集合属性变量(orderItems)对应的表
final Query query = entityManager.createQuery( "select o from Order oinner join o.orderItems where o.address.streetNumber=2000 order by o.id");final List result = query.getResultList();

// 这时获得Order实体中orderItems( 集合属性变量 )为空
final Order order = (Order)result.get( 0 )

// 当应用需要时，EJB3 Runtime才会执行一条SQL语句来加载属于当前Order的OrderItems
Collection orderItems = order.getOrderItems();
```

这样的查询性能上有不足的地方。为了查询N个Order，我们需要一条SQL语句获得所有的Order的原始/对象属性，但需要另外N条语句获得每个Order的orderItems集合属性。为了避免N+1的性能问题，我们可以利用join fetch一次过用一条SQL语句把Order的所有信息查询出来。

```
// 返回所有地址为2000的Order纪录，Order中必须有OrderItem
final Query query = entityManager.createQuery( "select o from Order oinner join fetch o.orderItems where o.address.streetNumber=2000 order by o.id");
```

由于使用了fetch,这个查询只会产生一条SQL语句，比原来需要N+1条SQL语句在性能上有了极大的提升。比较Entity在查询中使用参数查询时，参数类型除了String, 原始数据类型( int, double等)和它们的对象类型( Integer, Double等),也可以是Entity的实例。
```
final Query query = entityManager.createQuery( "select o from Order o where o.address = ?1 order by o.id");
final Address address = new Address( 2001, "foo street", "foo city", "foo province" );
// 直接把address对象作为参数。
query.setParameter( 1, address );
```

### 批量更新(Batch Update)
```
Query query = managerNew.createQuery("update Order as o set o.vender=:newvender, o.partNumber='fooPart' where o.vender = 'foo'");
query.setParameter("newvender", "barVender");
// update的记录数
int result = query.executeUpdate();
```
### 批量删除(Batch Remove)
```
Query query = managerNew.createQuery("DELETE FROM Order");
int result = query.executeUpdate();Query query = managerNew.createQuery("DELETE FROM Order AS o WHERE o.vender='redsoft'");int result = query.executeUpdate();
```
### 使用操作符NOT
```
// 查询所有vender不等于"foo"的Order
Query query = managerNew.createQuery("SELECT FROM Order AS o WHERE not(o.vender='foo')");
List result = query.getResultList();
// 删除所有vender不等于"foo"的Order
Query query = managerNew.createQuery("DELETE FROM Order AS o WHERE not(o.vender='foo')");
int result = query.executeUpdate();
```
### 使用操作符BETWEEN
```
// 查询所有价值amount在５和10之间的(包含5,10)的Order
Query query = managerNew.createQuery("select o FROM Order AS o left join o.orderItems ot where o.amount BETWEEN 5 AND 10 order by o.vender desc");List result = query.getResultList();
```
### 使用操作符IN
```
//查询所有vender是"foo1", "foo2"或者"foo3"的Order
Query query = managerNew.createQuery("select o FROM Order AS o left join o.orderItems ot where o.vender in ( 'foo1', 'foo2', 'foo3' ) order by o.vender desc");
List result = query.getResultList();
```
### 使用操作符LIKE
```
// 查询所有vender以字符串"foo"开头的Order 
Query query = managerNew.createQuery("select o FROM Order as o where o.vender like 'foo%' order by o.vender desc");
List result = query.getResultList();

// 查询所有vender以字符串"foo"结尾的Order
Query query = managerNew.createQuery("select o FROM Order as o where o.vender like '%foo' order by o.vender desc");
List result = query.getResultList();

// 可以结合NOT一起使用，比如查询所有vender不以以字符串"foo"结尾的Order
Query query = managerNew.createQuery("select o FROM Order as o where o.vender not like '%foo' order by o.vender desc");
List result = query.getResultList();

// 可以结合escape使用，比如查询所有vender以"foo"开始的Order并忽略'3'字符。
// 如果vender是"foo1", "foo2", "foo3"符合这个条件, 另外"3foo1", "f3oo4"也符合条件。
Query query = managerNew.createQuery("select o FROM Order as o where o.vender like '%foo' escape '3' order by o.vender desc");
List result = query.getResultList();
```

### 使用操作符IS NULL
```
// 查询所有没有地址的
OrderQuery query = managerNew.createQuery("select o FROM Order as o where o.address is null");
List result = query.getResultList();// 查询所有地址非空的Order
Query query = managerNew.createQuery("select o FROM Order as o where o.address is not null");
List result = query.getResultList();
```
### 使用操作符IS EMPTY
IS EMPTY是针对集合属性(Collection)的操作符。可以和NOT一起使用。
```
// 查询orderItems集合为空的Order
Query query = managerNew.createQuery("select o FROM Order o where o.orderItems is empty by o.vender desc");
List result = query.getResultList();

// 查询orderItems集合非空的Order
Query query = managerNew.createQuery("select o FROM Order o where o.orderItems is not empty by o.vender desc");
List result = query.getResultList();
```
### 使用操作符EXISTS[NOT]
EXISTS需要和子查询配合使用。
```
Query query = manager.createQuery("select o FROM Order o where exists (select o from Order o where o.partNumber=?1) order by o.vender desc");
query.setParameter(1, "partNumber");
Query query = manager.createQuery("select o FROM Order o where o.vender='partNumber' and not exists (select o from Order o where o.partNumber=?1) order by o.vender desc");
query.setParameter(1, "partNumber");
```
### 使用操作符ALL/SOME/ANY
```
String sql = "select emp from EmployeeA emp where emp.salary > all ( select m.salary from Manager m where m.department = emp.department)";
Query query = managerNew.createQuery(sql);
List result = query.getResultList();

String sql1 = "select emp from EmployeeA emp where emp.salary > any ( select m.salary from Manager m where m.department = emp.department)"; 
Query query = managerNew.createQuery(sql1);
List result = query.getResultList();

String sql2 = "select emp from EmployeeA emp where emp.salary > some ( select m.salary from Manager m where m.department = emp.department)";
Query query = managerNew.createQuery(sql2);
List result = query.getResultList();
```

### 字符串函数

EJB3 QL定义了内置函数，这些函数的使用方法和SQL中相应的函数方法类似。

EJB3 QL中定义的字符串函数包括：
	* CONCAT　字符串拼接
	* SUBSTRING　字符串截取
	* TRIM 去掉空格
	* LOWER　转换成小写
	* UPPER　装换成大写
	* LENGTH 字符串长度
	* LOCATE 字符串定位
```
// concat将参数中的两个字符串并结成一个字符串,这里firstName是"foo", lastName是"bar"
String sql = "select concat( o.owner.firstName, o.owner.lastName ) FROM Order AS o left outer join o.orderItems as oi where o.owner.firstName='foo'";
Query query = entityManager.createQuery(sql);
List result = query.getResultList();
assertEquals("foobar", result.get(0).toString());

// firstName是"fooBar",结果应该返回"oo"
String sql1 = "select o.vender,substring( o.owner.firstName, 1, 3 ), o.owner.info.age FROM Order AS o left outer join o.orderItems as oi where o.owner.firstName='charles'";
Query query = entityManager.createQuery(sql1);
List result = query.getResultList();
Object[] row1 = (Object[]) result.get(0);assertEquals("oo", row1[1].toString());

// 获得"ar"在firstName中地起始位置
String sql2 = "SELECT emp.firstName , emp.salary , locate( emp.firstName, 'ar') FROM EmployeeA as emp where emp.firstName='charles1111'";
Query query = managerNew.createQuery(sql2);
List result = query.getResultList();
```

计算函数EJB3 QL中定义的计算函数包括：
	* ABS　绝对值
	* SQRT 平方根
	* MOD 取余数
	* SIZE 取集合的数量
```
Query query = entityManager.createQuery("select o.vender, size( o.orderItems ) FROM Order o where o.owner.firstName = 'charles' group by o.vender order by o.vender desc");
List result = query.getResultList();

// 函数也可以用在条件中
Query query = managerNew.createQuery("select o.vender, sum(o.amount) FROM Order AS o left join o.orderItems ot group by o.vender having size(o.orderItems) = 0 or lower( o.vender ) = 'foo' order by o.vender desc");
List result = query.getResultList();

// 取余数
Query query = managerNew.createQuery("select mod( o.owner.info.age, 10 ) FROM Order o where exists ( select o from Order o where o.partNumber= :name ) and o.vender='order1' and exists ( select o from Order o where o.amount= :name1 ) order by o.vender desc");
```

子查询子查询可以用于WHERE和HAVING条件语句中。
```
Query query = managerNew.createQuery("select emp from EmployeeA as emp where ( select count(m) from Manager as m where m.department = emp.department) > 0 ");
List result = query.getResultList();
```
