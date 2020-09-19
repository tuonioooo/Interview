# MyBatis

[Mybatis源码解读](https://tuonioooo.gitbooks.io/application-framework/content/mybatispian.html)

**1. MyBatis 中 \#{}和 ${}的区别是什么？**

* \#{}是预编译处理，在使用 \#{}时，MyBatis 会将 SQL 中的\#{}替换成“?”，配合 PreparedStatement 的 set 方法赋值，这样可以有效的防止 SQL 注入，保证程序的运行安全。
* ${}是字符替换。${}是 Properties文件中的变量占位符，它可以用于标签属性值和sql内部，属于静态文本替换，比 如 ${driver}会被静态替换为 com.mysql.jdbc.Driver

**2. MyBatis 有几种分页方式？**

分页方式：逻辑分页和物理分页。

* **逻辑分页：**

   使用 MyBatis 自带的 RowBounds 进行分页，它是一次性查询很多数据，然后在数据中再进行检索。

* **物理分页：**

   自己手写 SQL 分页或使用分页插件 PageHelper，去数据库查询指定条数的分页数据的形式。

**3. RowBounds 是一次性查询全部结果吗？为什么？**

RowBounds 表面是在“所有”数据中检索数据，其实并非是一次性查询出所有数据，因为 MyBatis 是对 jdbc 的封装，在 jdbc 驱动中有一个 Fetch Size 的配置，它规定了每次最多从数据库查询多少条数据，假如你要查询更多数据，它会在你执行 next\(\)的时候，去查询更多的数据。就好比你去自动取款机取 10000 元，但取款机每次最多能取 2500 元，所以你要取 4 次才能把钱取完。只是对于 jdbc 来说，当你调用 next\(\)的时候会自动帮你完成查询工作。这样做的好处可以有效的防止内存溢出。

Fetch Size 官方相关文档：http://t. cn/EfSE2g3

**4. MyBatis 逻辑分页和物理分页的区别是什么？**

* 逻辑分页是一次性查询很多数据，然后再在结果中检索分页的数据。这样做弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。
* 物理分页是从数据库查询指定条数的数据，弥补了一次性全部查出的所有数据的种种缺点，比如需要大量的内存，对数据库查询压力较大等问题。

**5. MyBatis 是否支持延迟加载？延迟加载的原理是什么？**

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是 一对一，collection指的就是一对多查询。在 Mybatis配置文件中，可以配置是否启用延迟加载 lazyLoadingEnabled=true\|false 。

延迟加载的原理是调用的时候触发加载，而不是在初始化的时候就加载信息。比如调用a. getB\(\). getName\(\)，这个时候发现 a. getB\(\) 的值为 null，此时会单独触发事先保存好的关联 B 对象的 SQL，先查询出来 B，然后再调用 a. setB\(b\)，而这时候再调用 a. getB\(\). getName\(\) 就有值了，这就是延迟加载的基本原理。

**6. 说一下 MyBatis 的一级缓存和二级缓存？**

* 一级缓存：基于

  PerpetualCache 的 HashMap 本地缓存，它的声明周期是和 SQLSession 一致的，有多个 SQLSession 或者分布式的环境中数据库操作，可能会出现脏数据。当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认一级缓存是开启的。

* 二级缓存：也是基于

  PerpetualCache 的 HashMap 本地缓存，不同在于其存储作用域为 Mapper 级别的，如果多个SQLSession之间需要共享缓存，则需要使用到二级缓存，并且二级缓存可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现 Serializable 序列化接口\(可用来保存对象的状态\)。

> PrepetualCache的详细讲解：[http://www.mybatis.cn/archives/762.html](http://www.mybatis.cn/archives/762.html)

开启二级缓存数据查询流程：二级缓存-&gt; 一级缓存 -&gt; 数据库。

缓存更新机制：当某一个作用域\(一级缓存 Session/二级缓存 Mapper\)进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。

**7. MyBatis 和 hibernate 的区别有哪些？**

* 灵活性：

  MyBatis 更加灵活，自己可以写 SQL 语句，使用起来比较方便。

* 可移植性：

  MyBatis 有很多自己写的 SQL，因为每个数据库的 SQL 可以不相同，所以可移植性比较差。

* 学习和使用门槛：

  MyBatis 入门比较简单，使用门槛也更低。

* 二级缓存：

  hibernate 拥有更好的二级缓存，它的二级缓存可以自行更换为第三方的二级缓存。

**8. MyBatis 有哪些执行器（Executor）？**

MyBatis 有三种基本的Executor执行器：

* SimpleExecutor：每执行一次 update 或 select 就开启一个 Statement 对象，用完立刻关闭 Statement 对象；
* ReuseExecutor：执行 update 或 select，以 SQL 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后不关闭 Statement 对象，而是放置于 Map 内供下一次使用。简言之，就是重复使用 Statement 对象；
* BatchExecutor：执行 update（没有 select，jdbc 批处理不支持 select），将所有 SQL 都添加到批处理中（addBatch\(\)），等待统一执行（executeBatch\(\)），它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch\(\)完毕后，等待逐一执行 executeBatch\(\)批处理，与 jdbc 批处理相同。

**9. MyBatis 分页插件的实现原理是什么？**

分页插件的基本原理是使用MyBatis 提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 SQL，然后重写 SQL，根据 dialect 方言，添加对应的物理分页语句和物理分页参数。

**10. MyBatis 如何编写一个自定义插件？**

**自定义插件实现原理**

MyBatis 自定义插件针对 MyBatis 四大对象（Executor、StatementHandler、ParameterHandler、ResultSetHandler）进行拦截：

* * Executor：拦截内部执行器，它负责调用 StatementHandler 操作数据库，并把结果集通过 ResultSetHandler 进行自动映射，另外它还处理了二级缓存的操作；
  * StatementHandler：拦截 SQL 语法构建的处理，它是 MyBatis 直接和数据库执行 SQL 脚本的对象，另外它也实现了 MyBatis 的一级缓存；
  * ParameterHandler：拦截参数的处理；
  * ResultSetHandler：拦截结果集的处理。

**自定义插件实现关键**

MyBatis 插件要实现 Interceptor 接口，接口包含的方法，如下：

```text
public interface Interceptor {   
   Object intercept(Invocation invocation) throws Throwable;       
   Object plugin(Object target);    
   void setProperties(Properties properties);
}
```

* setProperties 方法是在 MyBatis 进行配置插件的时候可以配置自定义相关属性，即：接口实现对象的参数配置；
* plugin 方法是插件用于封装目标对象的，通过该方法我们可以返回目标对象本身，也可以返回一个它的代理，可以决定是否要进行拦截进而决定要返回一个什么样的目标对象，官方提供了示例：return Plugin. wrap\(target, this\)；
* intercept 方法就是要进行拦截的时候要执行的方法。

**自定义插件实现示例**

官方插件实现：

```text
@Intercepts({@Signature(type = Executor. class, method = "query",
        args = {MappedStatement. class, Object. class, RowBounds. class, ResultHandler. class})})public class TestInterceptor implements Interceptor {
   public Object intercept(Invocation invocation) throws Throwable {
     Object target = invocation. getTarget(); //被代理对象
     Method method = invocation. getMethod(); //代理方法
     Object[] args = invocation. getArgs(); //方法参数
     // do something . . . . . .  方法拦截前执行代码块
     Object result = invocation. proceed();
     // do something . . . . . . . 方法拦截后执行代码块
     return result;
   }
   public Object plugin(Object target) {
     return Plugin. wrap(target, this);
   }
}
```

#### **11.**Mybatis执行批量插入，能返回数据库主键列表吗? 

答 ：能，JDBC都能，Mybatis当然也能。

#### 12.Mybatis动态sql是做什么的？都有哪些动态sq l?能简述一下动态 sql的执行原理不？

答 ：Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql,完成逻辑判断和动态拼接sql 的功 能 ，Mybatis提供了9种动态sql标 签 trim \|where\|set\|foreach\|if\|c h o o se \|w h e n \|o th e rw ise \|b in d o 其执行原理为，使用OGNL从 sql参数对象中计算表达式的值，根据表达式的值动态拼接s q l,以此来 完成动态sql的功能。

#### 13.Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些 映射形式？

答：第一种是使用＜resultMap＞标签，逐一定义列名和对象属性名之间的映射关系。第二种是使用sql 列的别名功能，将列别名书写为对象属性名，比如T\_NAMEASNAME,对象属性名一般是nam e,小 写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以 写成T\_NAME AS NaMe, Mybatis —样可以正常工作。

#### **14.**Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

答：不同的Xm l映射文件，如果配置了 namespace,那么id 可以重复；如果没有配置namespace, 那 么id 不能重复；毕竟namespace不是必须的，只是最佳实践而已。 原 因 就 是 namespace+id是 作 为 Map的 k e y 使 用 的 ，如果没有 namespace, 就剩下i d ,那么，id 重复会导致数据互相覆盖。有了 namespace, 自然id 就可以重复， namespace不同，namespace+id自然也就不同。

#### 15.Mybatis中如何执行批处理？

使用BatchExecutor完成批处理。

#### 16.Mybatis中如何指定使用哪一种Executor执行器? 

 答 ：在 Mybatis配 置 文 件 中 ，可 以 指 定 默 认 的 ExecutorType执行器

#### 17.Mybatis是否可以映射Enum枚举类？

Mybatis可以映射枚举类，不单可以映射枚举类，Mybatis可以映射任何对象到表的一列上。映射 方式为自定义一个 TypeHandler, 实现 TypeHandler 的 setParameter\(\)和 getResult\(\)接口方法。TypeHandler有两个作用，一是完成从javalype至 jdbciype的转换，二是完成jdbcType至 javaType的转换，体现为setParameter\(\)和 getR esult\(\)两个方法，分别代表设置sq l问号占位符参数和获取列查询结果。

#### 18.Mybatis映射文件中，如果A 标签通过include引用了 B 标签的内 容，请问，B标签能否定义在A标签的后面，还是说必须定义在A标签的 前面？

虽然Mybatis解析Xml映射文件是按照顺序解析的，但是，被引用的B标签依然可以定义在任何 地方，Mybatis都可以正确识别。 原理是，Mybatis解析A 标签，发现A 标签引用了 B 标签，但是B 标签尚未解析到，尚不存在，此 时，Mybatis会将A 标签标记为未解析状态，然后继续解析余下的标签，包含B标签，待所有标签解析 完毕，Mybatis会重新解析那些被标记为未解析的标签，此时再解析A 标签时，B标签已经存在，A 标 签也就可以正常解析完成了。

#### 19.简述Mybatis的Xml映射文件和Mybatis内部数据结构之间的映射关系？

Mybatis将所有Xm l配置信息都封装到All-In-One重量级对象Configuration内部。在 Xm l映射 文 件 中 ，&lt;parameterMap&gt;标 签 会 被 解 析 为 ParameterMap对 象 ，其 每 个 子 元 素 会 被 解 析 为 ParameterMapping对象。&lt;resultMap&gt;标签会被解析为ResultMap对象，其每个子元素会被解析为 ResultM apping 对 象 。每一个 &lt; select&gt; 、&lt; insert&gt; 、&lt;update&gt;、 &lt; delete&gt; 标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象。

#### 20.为什么说Mybatis是半自动映射工具，它与全自动的区别在哪里？

Hibernate属于全自动QRM 映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以 根据对象关系模型直接获取，所以它是全自动的。而 Mybatis在查询关联对象或关联集合对象时，需要 手动编写sql来完成，所以，称之为半自动ORM映射工具。 面试题看似都很简单，但是想要能正确回答上来，必定是研究过源码且深入的人，而不是仅会使用的人 或者用的很熟的人，以上所有面试题及其答案所涉及的内容，在我的Mybatis系列博客中都有详细讲解 和原理分析。













