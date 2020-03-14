# Hibernate

**1. 为什么要使用 hibernate？**

* hibernate 是对 jdbc 的封装，大大简化了数据访问层的繁琐的重复性代码。
* hibernate 是一个优秀的 ORM 实现，很多程度上简化了 DAO 层的编码功能。
* 可以很方便的进行数据库的移植工作。
* 提供了缓存机制，是程序执行更改的高效。

**2. 什么是 ORM 框架？**

ORM（Object Relation Mapping）对象关系映射，是把数据库中的关系数据映射成为程序中的对象。

使用ORM 的优点：提高了开发效率降低了开发成本、开发更简单更对象化、可移植更强。

**3. hibernate 中如何在控制台查看打印的 SQL 语句？**

在Config 里面把 hibernate. show\_SQL 设置为 true 就可以。但不建议开启，开启之后会降低程序的运行效率。

**4. hibernate 有几种查询方式？**

三种：hql、原生 SQL、条件查询 Criteria。

**5. hibernate 实体类可以被定义为 final 吗？**

实体类可以定义为final 类，但这样的话就不能使用 hibernate 代理模式下的延迟关联提供性能了，所以不建议定义实体类为 final。

**6. 在 hibernate 中使用 Integer 和 int 做映射有什么区别？**

Integer 类型为对象，它的值允许为 null，而 int 属于基础数据类型，值不能为 null。

**7. hibernate 是如何工作的？**

* 读取并解析配置文件。
* 读取并解析映射文件，创建

  SessionFactory。

* 打开

  Session。

* 创建事务。
* 进行持久化操作。
* 提交事务。
* 关闭

  Session。

* 关闭

  SessionFactory。

**8. get\(\)和 load\(\)的区别？**

* 数据查询时，没有

  OID 指定的对象，get\(\) 返回 null；load\(\) 返回一个代理对象。

* load\(\)支持延迟加载；get\(\) 不支持延迟加载。

**9. 说一下 hibernate 的缓存机制？**

hibernate 常用的缓存有一级缓存和二级缓存：

一级缓存：也叫Session 缓存，只在 Session 作用范围内有效，不需要用户干涉，由 hibernate 自身维护，可以通过：evict\(object\)清除 object 的缓存；clear\(\)清除一级缓存中的所有缓存；flush\(\)刷出缓存；

二级缓存：应用级别的缓存，在所有Session 中都有效，支持配置第三方的缓存，如：EhCache。

**10. hibernate 对象有哪些状态？**

* 临时

  /瞬时状态：直接 new 出来的对象，该对象还没被持久化（没保存在数据库中），不受 Session 管理。

* 持久化状态：当调用

  Session 的 save/saveOrupdate/get/load/list 等方法的时候，对象就是持久化状态。

* 游离状态：

  Session 关闭之后对象就是游离状态。

**11. 在 hibernate 中 getCurrentSession 和 openSession 的区别是什么？**

* getCurrentSession 会绑定当前线程，而 openSession 则不会。
* getCurrentSession 事务是 Spring 控制的，并且不需要手动关闭，而 openSession 需要我们自己手动开启和提交事务。

**12. hibernate 实体类必须要有无参构造函数吗？为什么？**

hibernate 中每个实体类必须提供一个无参构造函数，因为 hibernate 框架要使用 reflection api，通过调用 ClassnewInstance\(\) 来创建实体类的实例，如果没有无参的构造函数就会抛出异常。

**13.hibernate控制反转的作用？inverse属性作用**

要的。在一对多的时候在 一 的这一方要控制，就是为了不让hibernate重新执行sql语句。

inverse属性的作用 hibernate配置文件中有这么一个属性inverse，它是用来指定关联的控制方的。 inverse属性默认是false，若为false，则关联由自己控制，若为true，则关联 由对方控制，下面举例介绍：

```text
public class Item implements Serializable {  
    private Integer itemId;  
    private String itemName;  

    private Collection<Bid> bids = new ArrayList<Bid>();  

    //set/get省略
```

```text
/** 
* 出价 
* @author partner4java 
* 
*/  
public class Bid implements Serializable {  
    private Integer bidId;  
    private Double bidMoeny;  

    private Item item;  

    //set/get省略
```

配置文件XML

```text
<class name="Item" table="ITEM">  
     <id name="itemId" column="ITEM_ID" type="integer">  
         <generator class="native"/>  
     </id>  
     <property name="itemName" type="string" column="ITEM_NAME"/>  

<property name="bids" cascade="save-update">  
<!-- 注意column="ITEM_ID_M"名称故意和Item主键不一致，这里是维护端生成的字段名称 -->  
<key column="ITEM_ID_M"></key>  
<one-to-many class="Bid"/>  
</property>  

</class>
```

执行插入：

```text
Item item = new Item("hello");  
Bid bid = new Bid(12D);  
item.getBids().add(bid);  
//不添加这个报错：not-null property references a null or transient value（因为添加了不能为空）  
//要知道这个东西和cascade="save-update"并不想干，cascade只是负责是否级联保存的，和表之间的关系由谁维护无关  
bid.setItem(item);  
session.save(item);
```

```text
hibernate打印sql： 
Hibernate: insert into ITEM (ITEM_NAME) values (?) 
Hibernate: insert into BID (bidMoeny, ITEM_ID_M) values (?, ?) 
Hibernate: update BID set ITEM_ID_M=? where BID_ID=? 
那么你看到了多打印了一条sql，最后一条是荣誉的。 
原因： 
在操作两个实例之间的连接时，如果没有inverse属性，hibernate会试图执行两个不同的SQL语句，这两者更新 
同一个外键列。通过指定inverse="true"，显示地告诉hibernate连接的哪一端不应该与数据库同步。在这个例子 
中，告诉hibernate他应该把在关联的Bid端所做的变化传播到数据库，忽略只对bids集合所做的变化。
```

如下：

```text
<class name="Item" table="ITEM">  
     <id name="itemId" column="ITEM_ID" type="integer">  
         <generator class="native"/>  
     </id>  
     <property name="itemName" type="string" column="ITEM_NAME"/>  

//<name="bids" inverse="true" cascade="save-update">也决定了，维护端是Bid的item属性，所以要告诉Bid的item属性item的存在         
<property name="bids" inverse="true" cascade="save-update">  
<!-- 注意column="ITEM_ID_M"名称故意和Item主键不一致，这里是维护端生成的字段名称 -->  
<key column="ITEM_ID_M"></key>  
<one-to-many class="Bid"/>  
</property>  

</class>
```

再次执行相同插入：

```text
Item item = new Item("hello");  
Bid bid = new Bid(12D);  
item.getBids().add(bid);  
bid.setItem(item);  
session.save(item);
```

hibernate打印sql：

```text
Hibernate: insert into ITEM (ITEM_NAME) values (?) 
Hibernate: insert into BID (bidMoeny, ITEM_ID_M) values (?, ?)
```

那么有些时候，是不能添加inverse="true"的，比如保存有序的list集合：

添加：insert="false" update="false"

但是这么做还是会打印三条sql，不添加则会报错

Repeated column in mapping for entity: cn.partner4java.hibernate.onetomany2.Bid column: ITEM\_ID \(should be mapped with insert="false" update="false"\)

如：

```text
<class name="Item" table="ITEM">  
      <id name="itemId" column="ITEM_ID" type="integer">  
          <generator class="native"/>  
      </id>  
      <property name="itemName" type="string" column="ITEM_NAME"/>  
      <!-- 没有在集合中加入inverse="true，因为hibernate忽略反向集合的状态！  
         但是这一次，集合包含了正确的更新数据库所需要的信息：它的元素的位置。  
         如果只有每个Bid实例的状态被认为是同步的，集合又是方向的并且被忽略，  
         那么hibernate就没有值给BID_POSITOIN了-->  
<list name="bids" cascade="save-update">  
    <key column="ITEM_ID" not-null="true"></key>  
    <list-index column="BID_POSITOIN"></list-index>  
    <one-to-many class="Bid"/>  
</list>                 
  </class>  


  <class name="Bid" table="BID">  
      <id name="bidId" column="BID_ID" type="integer">  
          <generator class="native"/>  
      </id>  
<property name="bidMoeny" type="double"></property>       
<!-- 如果通过被索引的集合映射双向的一对多实体关联（映射和数组也是这样），  
    就必须转换为方向段。无法被索引的集合变成inverse="true"。集合变成了  
    负责状态同步，并且一的端Bid必须变成反向。然而，多对一的映射没有  
    inverse="true"，因此需要在<many-to-one>中模拟这一模拟这一属性：  
    设置insert="false" update="false"  
    这俩个属性一起使用，实际上使属性变成了只读。关联的这一端因此被任何写操作忽略，  
    当内存状态与数据库同步时，集合的状态就是相关的状态。你已经转换了关联的方向/非方向端，  
    如果从set或者bag转换为list，这个是必要的条件。  
     -->  
<many-to-one name="item" column="ITEM_ID_M"   
    class="Item" not-null="true"   
    insert="false" update="false"></many-to-one>          
</class>
```

**双向对多对时：**

```text
public class Category implements Serializable {  
    private Integer categoryId;  
    private String name;  
    private Set<Item> items = new HashSet<Item>();  


public class Item implements Serializable {  
    private Integer itemId;  
    private String itemName;  
    private Set<Category> categories = new HashSet<Category>();
```

配置文件：

```text
<class name="Category" table="Category">  
    <id name="categoryId" column="CATEGORY_ID" type="integer">  
        <generator class="native"/>  
    </id>  
    <property name="name" type="string" column="name"/>  
    <set name="items" table="CATEGORY_ITEM" inverse="true" cascade="save-update">  
        <key column="CATEGORY_ID"></key>  
        <many-to-many column="ITEM_ID" class="Item"></many-to-many>  
    </set>  
</class> 




<class name="Item" table="ITEM">  
    <id name="itemId" column="ITEM_ID" type="integer">  
        <generator class="native"/>  
    </id>  
    <property name="itemName" type="string" column="ITEM_NAME"/>  
    <set name="categories" table="CATEGORY_ITEM" cascade="save-update">  
        <key column="ITEM_ID"></key>  
        <many-to-many column="CATEGORY_ID" class="Category"></many-to-many>  
    </set>  
</class>
```

插入代码：

```text
public class HelloWorld {  

    public static void main(String[] args) {  
//      insert();  

//      insert2();  

        insert3();  
    }  

    private static void insert() {  
        Configuration configuration = new Configuration().configure();  
        SessionFactory sessionFactory = configuration.buildSessionFactory();  
        Session session = sessionFactory.openSession();  
        Transaction tr = session.beginTransaction();  

        Item item = new Item("item");  
        Category category1 = new Category("category1");  
        item.getCategories().add(category1);  
        Category category2 = new Category("category2");  
        item.getCategories().add(category2);  


        session.save(item);  
//      Hibernate: insert into ITEM (ITEM_NAME) values (?)  
//      Hibernate: insert into Category (name) values (?)  
//      Hibernate: insert into Category (name) values (?)  
//      Hibernate: insert into CATEGORY_ITEM (ITEM_ID, CATEGORY_ID) values (?, ?)  
//      Hibernate: insert into CATEGORY_ITEM (ITEM_ID, CATEGORY_ID) values (?, ?)  


        tr.commit();  
        session.close();  

        sessionFactory.close();  
    }  

    private static void insert2() {  
        Configuration configuration = new Configuration().configure();  
        SessionFactory sessionFactory = configuration.buildSessionFactory();  
        Session session = sessionFactory.openSession();  
        Transaction tr = session.beginTransaction();  

        Category category = new Category("category1");  
        Item item1 = new Item("item1");  
        category.getItems().add(item1);  
        Item item2 = new Item("item1");  
        category.getItems().add(item2);  


        session.save(category);  
        //inverse="true"的一端，忽略对中间库的影响  
//      Hibernate: insert into Category (name) values (?)  
//      Hibernate: insert into ITEM (ITEM_NAME) values (?)  
//      Hibernate: insert into ITEM (ITEM_NAME) values (?)  


        tr.commit();  
        session.close();  

        sessionFactory.close();  
    }  

    private static void insert3() {  
        Configuration configuration = new Configuration().configure();  
        SessionFactory sessionFactory = configuration.buildSessionFactory();  
        Session session = sessionFactory.openSession();  
        Transaction tr = session.beginTransaction();  

        Category category = new Category("category1");  
        Item item1 = new Item("item1");  
        category.getItems().add(item1);  
        Item item2 = new Item("item1");  
        category.getItems().add(item2);  

        //必须存在，才会影响中间表，因为inverse="false"的一端，才会影响中间库的变化  
        item1.getCategories().add(category);  
        item2.getCategories().add(category);  

        session.save(category);  
//      Hibernate: insert into Category (name) values (?)  
//      Hibernate: insert into ITEM (ITEM_NAME) values (?)  
//      Hibernate: insert into ITEM (ITEM_NAME) values (?)  
//      Hibernate: insert into CATEGORY_ITEM (ITEM_ID, CATEGORY_ID) values (?, ?)  
//      Hibernate: insert into CATEGORY_ITEM (ITEM_ID, CATEGORY_ID) values (?, ?)  


        tr.commit();  
        session.close();  

        sessionFactory.close();  
    }  
}
```

