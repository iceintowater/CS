# mybatis
## 0、定义与背景
一个半ORM（对象关系映射）框架，它内部封装了JDBC，开发时只需要关注SQL语句本身，不需要花费精力去处理加载驱动、创建连接、创建statement等繁杂的过程。程序员直接编写原生态sql，可以严格控制sql执行性能，灵活度高。
## 1、原理
mapper.xml的方法使用的是代理技术。
### 接口层：
（1）mapper.xml变成接口，每一个<mapper> 节点抽象为一个 Mapper 接口方法，即<select|update|delete|insert> 节点的id值为Mapper 接口中的方法名称
    接口层提供了用户操作数据库的api(crud).
    
    根据MyBatis 的配置规范配置好后，通过SqlSession.getMapper(XXXMapper.class) 方法，
    MyBatis 会根据相应的接口声明的方法信息，通过动态代理机制生成一个Mapper 实例，我们使用Mapper 接口的某一个方法时，
    MyBatis 会根据这个方法的方法名和参数类型，确定Statement Id，底层还是通过SqlSession.select("statementId",parameterObject);
    或者SqlSession.update("statementId",parameterObject); 等等来实现对数据库的操作

### 数据处理层：
（1）参数映射和动态sql生成
动态sql生成：通过传入的参数值，使用 Ognl 来动态地构造SQL语句。
参数映射：对于java 数据类型和jdbc数据类型之间的转换：这里有包括两个过程：查询阶段，我们要将java类型的数据，转换成jdbc类型的数据，通过 preparedStatement.setXXX() 来设值；另一个就是对resultset查询结果集的jdbcType 数据转换成java 数据类型

（2）sql语句执行，以及封装结果集合list<E>

### 框架支撑层
事务管理、连接池管理、缓存管理

## 2、代码级别组件间关系

sqlsession->executor->statehandler  ( param result type) + boundsql(mapperstatement(sqlsource+resultmap))

jdbc resultmap statement

### 定义
SqlSession           作为MyBatis工作的主要顶层API，表示和数据库交互的会话，完成必要数据库增删改查功能

Executor             MyBatis执行器，是MyBatis 调度的核心，负责SQL语句的生成和查询缓存的维护

StatementHandler     封装了JDBC Statement操作，负责对JDBC statement （sql 操作）的 操作，如设置参数、将Statement结果集转换成List集合。

ParameterHandler     负责对用户传递的参数转换成JDBC Statement 所需要的参数，

ResultSetHandler     负责将JDBC返回的ResultSet结果集对象转换成List类型的集合；

TypeHandler          负责java数据类型和jdbc数据类型之间的映射和转换

MappedStatement      MappedStatement维护了一条<select|update|delete|insert>节点的封装，

SqlSource            负责根据用户传递的parameterObject，动态地生成SQL语句，将信息封装到BoundSql对象中，并返回

BoundSql             表示动态生成的SQL语句以及相应的参数信息

Configuration        MyBatis所有的配置信息都维持在Configuration对象之中

### 与heibernate之间的区别

    1、 在项目开发过程当中，就速度而言：

            hibernate开发中，sql语句已经被封装，直接可以使用，加快系统开发；

            Mybatis 属于半自动化，sql需要手工完成，稍微繁琐；

        但是，凡事都不是绝对的，如果对于庞大复杂的系统项目来说，发杂语句较多，选择hibernate 就不是一个好方案。

    2、 sql优化方面

        Hibernate 自动生成sql,有些语句较为繁琐，会多消耗一些性能；

        Mybatis 手动编写sql，可以避免不需要的查询，提高系统性能；
