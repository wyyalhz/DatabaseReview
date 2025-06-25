# 三、数据库控制技术

## 1.数据库安全性

### 1.1 数据库的安全性

·数据共享带来数据库的安全性问题

·**数据库的安全性**是指在数据库应用系统的不同层面（数据库系统层、操作系统层、网络层）提供安全防范措施，保护数据库不收恶意访问，<font color=red>完全杜绝几乎是不可能的</font>

·TCSED/TDI安全级别划分ABCD四组7个等级：A1，B3，B2，B1，C2，C1，D

### 1.2 数据库系统采用的安全保护措施

访问控制技术（用户标识与鉴别）

存取控制技术

视图

数据加密

数据库审计


### 1.3 了解存取控制中用户在不同对象上有哪些权限

·存取控制：只允许用户进行权限范围内的数据存取操作，最有效的安全手段，包括定义用户权限和合法权限检查，分为自主存取控制（C2级）和强制存取控制（B1级）

各个数据对象所允许的操作：

| 数据对象 | 操作类型 |
| --- | --- |
| 模式 | CREATE SCHEMA，DROP |
| 基本表 | CREATE TABLE，ALTER TABLE，DROP |
| 视图 | CREATE VIEW，DROP |
| 索引 | CREATE INDEX，DROP|
| 表和视图的元组 | SELECT，INSERT，UPDATE，DELETE，REFERENCES，ALL PRIVILEGES |
| 属性列 | SELECT，INSERT，UPDATE，DELETE，REFERENCES，ALL PRIVILEGES |

### 1.4 SQL自主存取控制DAC

**数据库角色**：<font color=red>权限的集合</font>

```sql
//创建一个角色
CREATE ROLE R1;

//授予角色R1拥有三个关系的查询权限
GRANT SELECT ON TABLE STUDENT, SC, COURSE TO R1;

//将角色R1授予用户Li和Zhao，使它们具有角色R1所包含的全部权限
GRANT R1 TO Li, Zhao;

```

### 1.5 SQL强制存取控制MAC

·分为主体和客体，主体包括DBMS所管理的实际用户和代表用户的各进程，客体包括文件、数据表、视图、记录、属性列等等

·主体的安全分类级别成为许可证级别，客体的安全分类级别成为密集

·控制规则：

- 仅当主题的许可证级别<font color=red>大于或等于</font>客体的密集时，主体才能读取客体（上读）
- 仅当主题的许可证级别<font color=red>等于或小于</font>客体的密集时，主体才能写客体（下写）

·一般用户或程序不能修改系统安全收取那状态，只有特定的系统权限管理员才可以这么做，提供了更高级别的安全性，但也有诸多不便

### 1.6 视图机制

·主要功能是**提供数据独立性**，同时<font color=red>也是一种重要的自主授权机制</font>，安全保护功能并不精细

·视图可实现的安全保护：
- 将用户限定在数据表中特定的数据行上
- 将用户限定在数据表中特定的数据列上
- 将多个表中的列连接起来，使他们看起来像一个数据表
- 提供聚合信息而非详细信息

·视图与授权机制配合
- 用视图屏蔽掉一部分保密数据，进一步定义存取权限
- 通过定义不同的视图及有选择地授予视图上的权限，可以将用户、组、角色限制在不同的数据子集内

### 1.7 什么是角色、角色对权限管理有何用处

**数据库角色**：<font color=red>权限的集合</font>

·用户数量比较大的情况下，可以为一组组具有相同权限的用户创建一个角色，简化授权的过程

## 2.数据库完整性

### 2.1 定义，关系模型必须满足的完整性约束条件

·含义：正确性、有效性、相容性（表示同一事实的两个数据应相同）

·关系数据库对数据的各种限制是以完整性约束条件的形式<font color=red>在关系数据库模式中</font>指定的

·关系数据库完整性约束条件分为：
- 实体完整性约束
- 参照完整性约束
- 其他（用户定义的）完整性约束

·关系数据库中，**完整性控制策略**包括默认值、规则、<font color=red>约束（优先于触发器、规则和默认值）</font>、<font color=red>触发器（编程的方法）</font>和存储过程等

·数据完整性另一分类：
- 声明式数据完整性（非编程方式）：约束、默认值、规则
- 过程式数据完整性（编程方式）：触发器、存储过程

### 2.2 理解静态约束和动态约束含义

### 2.3 完整性约束条件作用对象

- 关系（关系约束）：一个关系的各个元组之间、或者多个关系之间存在的各种联系或约束。常见的关系约束有：实体完整性约束、参照完整性约束、函数依赖约束、统计约束等
- 元组（元组约束）：元组中各个属性之间的约束关系
- 属性列（列级约束）：对数据类型、格式、值域、空值的约束

#### 实体完整性

·规定：主键的值唯一，不能取空值

·用PRIMARY KEY定义，可以定义为列级约束条件或标记约束条件，对多个属性构成的主键只能定义为表级约束条件

```sql
CREATE TABLE SC (
    Sno CHAR(10) NOT NULL,
    Cno CHAR(4) NOT NULL,
    Grade SMALLINT,
    PRIMARY KEY (Sno, Cno) //只能在表级定义主键
);
```

·系统一般会在主键属性上自动创建唯一的索引来强制唯一性约束

#### 参照完整性

·规定：不允许引用不存在的元组；外键字段的值要么为空值要么是被引用关系中元组的对应值

- FOREIGN KEY定义哪些列为外键，
- REFERENCES短语指明外键参照哪些表的主键

```sql
CREATE TABLE SC (
    Sno CHAR(10) NOT NULL,
    Cno CHAR(4) NOT NULL,
    Grade SMALLINT,
    PRIMARY KEY (Sno, Cno),
    FOREIGN KEY (Sno) REFERENCES STUDENT(Sno),
    FOREIGN KEY (Cno) REFERENCES COURSE(Cno)
);
```

·维护参照完整性的策略：

1.参照关系中，需要定义外键是否允许为空值

2.参照关系中，插入元组有**受限插入**和**递归插入**（级联CASCADE插入）两种

3.参照关系中，修改元组：先删除、再插入

4.被参照关系中，删除元组有级联删除CASCADES、受限删除RESTRICTED、置空值删除

5.被参照关系中，修改主键：先删除、再插入

·违反参照完整性，系统一般选用默认策略，即拒绝执行。如果想让系统采用其他策略，则必须在创建表时加以说明

### 2.4 用户定义的完整性约束条件

·是限定某一具体应用的数据必须满足的语义要求，由RDBMS提供定义和检验用户定义的完整性的机制

·用户定义的完整性的定义：
- 属性上的约束条件的兴义：唯一（UNIQUE）、非空（NOT NULL）、CHECK约束、默认值DEFAULT等
- 元组上的约束条件的定义：用CHECK短语，元组级的限制可以设置不同属性之间的相互约束条件

```sql
//创建table1，指定c1字段不能包含重复值，c2字段只能取特定值
CREATE TABLE table1 (
    c1 CHAR(2) UNIQUE,
    c2 CHAR(2) CHECK (c2 IN ('00', '01', '02', '03')),
    c3 INT DEFAULT 1
    );

//定义CONSTRANT约束
CRAETE TABLE Student (
    Sno NUMBERIC(6) CONSTRAINT C1 CHECK (Sno BETWEEN 100000 AND 999999),
    Sname CHAR(20) CONSTRAINT C2 NOT NULL,
    Sage NUMBERIC(3) CONSTRAINT C3 CHECK (Sage BETWEEN 18 AND 60),
    Ssex CHAR(2) CONSTRAINT C4 CHECK (Ssex IN ('男', '女')),
    CONSTRAINT PK_Student PRIMARY KEY (Sno)
);

//修改表中的完整性限制：先删除原来的约束条件，再加新的
//修改表Student中的约束条件，年龄改为小于40
ALTER TABLE Student DROP CONSTRAINT C3;
ALTER TABLE Student ADD CONSTRAINT C3 CHECK (Sage < 40);
```


### 2.5 通过触发器定义完整性约束条件

| 约束 | 触发器 |
| --- | --- |
| 声明式、静态的 | 过程式、动态的 |
| 只能实现简单动作如拒绝操作 | 可以实现复杂的完整性约束 |
| | 由事件驱动的特殊过程，在对表或视图发出UPDATE、INSERT或DELETE时自动执行 |
| | 也叫<font color=red>事件-条件-动作</font>规则 |

·示例
```sql
CREATE TRIGGER SC_T
  AFTER UPDATE OF Grade ON SC 
  REFERENCING
    OLD row AS OldTuple,
    NEW row AS NewTuple
  FOR EACH ROW
  WHEN (NewTuple.Grade >= 1.1*OldTuple.Grade)
    INSERT INTO
      SC_U(Sno, Cno, OldGrade, NewGrade)
    VALUES
      (OldTuple.Sno, OldTuple.Cno, OldTuple.Grade, NewTuple.Grade);

```

1.触发器的创建者：表拥有者或创建表的用户才可以在表上创建触发器，一个表上可以创建多个触发器

2.表名：触发器的目标表，更新时触发器才被激活

3.触发事件：INSERT、UPDATE、DELETE，或三者组合

4.触发时间：BEFORE、AFTER、INSTEAD OF

5.触发器类型：按照<font color=red>触发动作的间隔</font>分为
- 行级触发器FOR EACH ROW：对每一个修改的元组都会触发触发器
- 语句级触发器FOR EACH STATEMENT：只在SQL语句执行时触发

6.触发条件：由WHEN子句指定

7.触发动作体

8.引用
REFERENCING NEW | OLD ROW AS <变量>，行级触发器，在过程体中引用事件之后的新值和事件之前的旧值

REFERENCING NEW | OLD TABLE AS <变量>，语句级触发器，<font color=red>仅适用于AFTER触发器</font>，在过程体中引用整个表的新值和旧值

·实例
```sq;
//定义一个BEFORE行级触发器，为教师表Teacher定义完整性规则“教授的工资不得低于4000元，如果低于4000元，自动改为4000元”

CREATE TRIGGER Insert_Or_Update_Sal
  BEFORE INSERT OR UPDATE ON Teacher
  FOR EACH ROW
  BEGIN
    IF (new.Job = '教授') AND (new.Salary < 4000)
      THEN new.Sal := 4000;//:=表示赋值
    END IF;
  END;

//将每次对表Student的插入操作所增加的学生个数记录到表StudentInsertLog中

CREATE TRIGGER Student_Count(
    AFTER INSERT ON Student
    REFERENCING NEW TABLE AS Delta
    FOR EACH STATEMENT
      INSERT INTO StudentInsertLog(Numbers)
      SELECT COUNT(*) FROM Delta;
)

//删除触发器
DROP TRIGGER Student_Count ON Student;

```

### 2.6 违约处理具体措施

·触发器执行顺序：
- 1.执行该表上的BEFORE触发器
- 2.激活触发器的SQL语句（触发事件）
- 3.执行该表上的AFTER触发器

·如果激活触发器的SQL语句违反了约束条件，则不会执行AFTER触发器

### 2.7 SQL Server中完整性的实现

·<font color=red>使用约束CONSTRANT优先于使用触发器、规则和默认值</font>：

·<font color=red>CHECK约束中不能包含子查询</font>：

```sql
ALTER TABLE Employees 
  ADD CONSTRAINT CK_birthdate 
  CHECK (BirthDate > '01-01-1900' AND BirthDate < getdate());

ALTER TABLE Employees
  ADD CONSTRAINT FK_Orders_Customers
  FOREIGN KEY (CustomerID) 
  REFERENCES Customers(CustomerID)

ALTER TABLE Employees
  ADD CONSTRAINT DF_contactname
    DEFAULT 'unknown'
  FOR ContactName;

ALTER TABLE Employees
  ADD CONSTRAINT U_CompanyName
    UNIQUE (CompanyName);
```

·UNIQUE约束
- 使用唯一性约束的字段允许为空值
- 唯一性约束用于强制在指定字段上创建一个唯一性索引

#### SQL Server的触发器

·与之前学的触发器格式稍有不同

·<font color=red> 临时虚拟表 </font>:
- INSERTED表：保存INSERT操作中新插入的数据或UPDATE操作中更新后的数据
- DELETED表：保存DELETE操作中删除的数据或UPDATE操作更新前的数据
- 触发器操作完后相关临时表自动删除

```sql
//创建删除触发器，当删除一本书时，先检查这本书是否已经被卖过了，即是否和订单关联，如果已经有关联则概述的信息并不能被删除，删除动作需要回滚。

CREATE TRIGGER Products_Delete
  ON Products FOR DELETE
AS
  IF(SELECT COUNT(*) 
    FROM [Order Details] INNER JOIN deleted
      ON [Order Details].ProductID = deleted.ProductID) > 0
  BEGIN
    RAISERROR('该书已经被卖出，不能删除', 10, 1)
    ROLLBACK TRANSACTION
  END

//创建限制更新数据的触发器，限制将SC表中不及格学生的成绩改为及格

CREATE TRIGGER tri_grade
  ON SC FOR UPDATE
AS
  IF UPDATE (Grade)
    IF EXISTS (SELECT * FROM INSERTED JOIN DELETED ON
      INSERTED.Sno = DELETED.Sno WHERE INSERTED.Grade >= 60 AND DELETED.Grade < 60)
    BEGIN
      RAISERROR('不及格学生的成绩不能修改')
      ROLLBACK
    END

```

## 3.数据库恢复技术

### 3.1 事务的基本概念，如何定义，性质

**事务**：用户定义的一个数据库操作序列，不可分割的工作单位，要么全做，要么全不做。在关系数据库中，一个事务可以是一条SQL语句，一组SQL语句或整个程序

·定义事务
```sql
BEGIN TRANSACTION
  //事务内的SQL语句
  //……
COMMIT | ROLLBACK
```
- COMMIT表示提交，事务中所有操作结果被保存
- ROLLBACK表示回滚，事务中所有已完成的操作都被撤销，数据库回到事务开始前的状态

```sql
//例：银行转账事务的定义
BEGIN TRANSACTION
  read(A);
  A:=A-1000;
  wirte(A);
  if(A<0) ROLLBACK;
  else{
    read(B);
     B:=B+1000;
     write(B);
     COMMIT;
  }
```

·事务的性质：<font color=red> ACID特性 </font>，即
- 原子性：事务不可分割，要么全部完成，要么全部不完成
- 一致性：事务必须使数据库从一个一致性状态变到另一个一致性状态。**一致性状态**：数据库中只包含成功执行的事务所提交的结果
- 隔离性：一个事务的执行不能影响另一个事务，保证并发执行结果正确
- 持久性：一个事务一旦提交，它对数据库的改变就应该永久保存下来

### 3.2 数据恢复的基本原理：数据冗余

·原理：利用存储在系统其他地方的冗余数据来重建数据库中已被破坏或不正确的那部分数据

### 3.3 建立冗余数据最常用的技术

·**数据转储**和**登记日志文件**。通常一起使用

### 3.4 了解数据转储的基本方法

·DBA定期地将整个数据库复制到磁带或领一个磁盘上保存，后背副本，只能将数据库恢复到转储时的状态

<img src="\image\3-3-4-1.jpg" width=800>

- 静态存储：在系统中无运行事务时进行存储，但过程中不允许存取、修改数据库

- 动态存储：与用户事务并发进行，期间允许存取、修改数据库，但不能保证副本数据正确有效，需要配合日志恢复

- 海量存储：每次转储全部数据库

- 增量存储：只转储自上次转储后<font color=red>更新过的数据</font>

### 3.5 日志的内容，记日志的规则

·内容：事务标识、操作类型、操作对象、更新前后的数据值

·登记日志文件时必须遵循两条原则：
- 登记的次序严格按并行事务执行的时间次序；
- 必须先写日志文件，厚些数据库

### 3.6 故障的种类，对不同故障的恢复策略

#### 事务故障

·事务在运行至正常终止点前被终止

·恢复方法：利用日志文件撤销事务对数据的更改，系统回滚到事务执行前的状态。

·事务故障的恢复由<font color=red>系统自动完成</font>

#### 系统故障

·一些未完成事务对数据库的更新已写入数据库/一些已提交事务对数据库的更新还留在缓冲区没来得及写入数据库，造成数据库不一致状态

·恢复方法：正向扫描日志建立Redo和Undo队列，Undo未完成的事务，Redo已完成的事务

·由系统在<font color=blue>重新启动时</font> <font color=red>自动完成</font>，不需要用户干预

#### 介质故障

·介质上存储的数据受到破坏

·恢复方法：重装数据库，使数据库恢复到某个一致性状态，重做已完成的事务

·单用日志不能恢复，需要后背副本；<font color=red>需要DBA介入，具体恢复操作仍由DBMS完成</font>

### 3.7 检查点恢复技术
