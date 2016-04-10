# spool命令：
- 录屏的命令
- 开始录屏：spool 文件全路径名
- 结束录屏：spool off	
# 设置行宽：
- 显示行宽：show linesize
- 设置：set linesize 120	
# 设置列宽：
- 设置字符串列的宽度(a8代表宽度为8个字符)：col 列名（大小写不敏感） for a8
- 设置数字列的宽度(9999代表宽度为四个数字)：col 列名（大小写不敏感 for 9999
	
# /:
- 代表执行上一条SQL语句	
# ed:
- 将上一条SQL语句输入到默认文本编辑工具中，并打开，按Ctrl+s 保存后自动将该语句返回到控制台	
# c命令(change)：
步骤：
		
1. 直接输入要修改的上条命令的行号
2. 回车
3. c /要改的改行命令中的某个单词/改变后的单词
4. /
5. 回车执行修改后上条的命令
# ***SQL优化的原则：***
1. 尽量使用列名而不是*来查询
2. where解析的顺序：从右往左
		
	即：	
	
		where condition1 and condition2
		
  	这条语句先判断condition2，再判断condition1
3. 在`where`和`having`可以替换的情况下，尽量使用`where`来代替`having`；因为`having`是先分组再过滤，`where`是先过滤再分组
	
	
# SQL中的null值：
1. 包含null的表达式都为null
2. null永远不等于null,应该用该语句查询null值的列
	
		where comm is null;

3. 如果集合中含有null，不能使用not in；但可以使用in
4. null的排序

		nulls last
		
	Oracle数据库中null值最大
5. 	组函数会自动滤空；可以嵌套**滤空函数**来屏蔽他的滤空功能
# 滤空函数：
- **nvl(a,b)**:如果a为空返回b，如果不为空返回a自己
# 别名(alias)：
示例：


	select empno as "员工号",ename "姓名",sal 月薪,sal*12 年薪,comm 奖金,sal*12+nvl(comm,0) 年收入
	from emp
- 形式一：empno as "员工号"
- 形式二：ename "姓名"
- 形式三：sal 月薪
- 形式一和形式二没有区别，形式三中，如果别名是数字、别名含有关键字、别名中有空格，都是不允许的	
# distinct关键字：
示例：

	select distinct deptno,job from emp;
**distinct**作用于其后的所有列
	
# 注意：
1. SQL语言大小写不敏感
2. SQL可以写在一行或多行
3. 关键字不能被缩写也不能分行
4. 各子句一般要分行写
5. 使用缩进提高语句的可读性
	
# 定义空值：
1. 空值是无效的，未定义的，未知的或不可预知的值
2. 空值不是空格或者0
3. 包含空值的数学表达式都为空值
	
# 连接符 ||：
连接符 || 的功能和concat()的功能是一样的

示例：

	select concat('hello',' world') from dual;

等价于

	select 'hello'||' world' 字符串 from dual;	
	
# dual表（伪表）：
存在的意义仅仅是为了满足语句的完整，因为oracle中的SQL语句在select后面必须要有from关键字
	
# 字符串：
1. 打印到屏幕上的都是字符串，但数据本身有自己的类型，好比用System.out.println()输出的内容都是字符串
2. 字符串可以是select列表中的一个字符，数字，日期
3. **日期和字符只能在单引号中出现（双引号表示列的别名）**
4. 每当返回一行时，字符串被输出一次
	
# SQL和SQL*Plus：
1. SQL*Plus是oracle提供的一个工具，可以执行SQL语句
2. 区分一个命令是SQL命令还是SQL*Plus命令
		
	- __SQL__命令：
			
		* select
		* insert
		* update
		* delete
	
	- __SQL*Plus__命令：
		* ed
		* c
		* for	:format的缩写，与col一起用
		* col	:colum的缩写，表示某列
		* desc
	- __SQL命令的关键字不能缩写，SQL*Plus的关键字可以缩写__
		
# 修改日期格式：
- 查询参数设置：

		select * from v$nls_parameters;
- 修改日期格式：

	修改当前会话：

		alter session set NLS_DATE_FORMAT='yyyy-mm-dd';
	修改整个系统（以管理员登陆时才能修改）：

		alter system set NLS_DATE_FORMAT='yyyy-mm-dd';
# 排序的规则：
- order by作用于后面所有的列，先按照第一列排序；再按照第二列排序；以此类推
- desc:只作用于距离最近的那一列
	
# a命令(append)：
&#160;&#160;&#160;&#160;&#160;**a命令后面的空格至少两个**
# group by:
Oracle中的`group by`语句有如下语法要求：
	
	select a,b,c,组函数(x)
	from table
	group by a,b,c,d,e;
即：

1. 在`select`语句中所有没有包含在组函数中的列，都要在`group by`的子句中，如句中的`a,b,c`
1. 但是，包含在`group by`子句中的列不必包含在`select`列表中，如句中的`d,e`
3. 多个列的分组，先按照`group by`后的第一个列分组，第一列相同的，再按照第二列分组，如果所有列都相同，就分到同一组中，计算该组的分组函数值
4. 过滤分组`having`：
		
		SQL> --求平均工资大于2000的部门
		SQL> select deptno,avg(sal)
  			2  from emp
 			3  group by deptno
  			4  having avg(sal)>2000;

 		DEPTNO   AVG(SAL)
		------- ----------
     		20       2175
     		10 2916.66667
	`where`和`having`的区别：`where`子句不能使用多行函数（组函数）

	换句话说：在没有组函数的情况下，`where`和`having`可以通用

		SQL> --查询10号部门的平均工资
		SQL> select deptno,avg(sal)
		  2  from emp
		  3  group by deptno
		  4  having deptno=10;
		
		 DEPTNO   AVG(SAL)
		------- ----------
     		10 2916.66667

		SQL> select deptno,avg(sal)
		  2  from emp
		  3  where deptno=10
		  4  group by deptno;
		
		 DEPTNO   AVG(SAL)
		------- ----------
     		10 2916.66667
5. **`group by`的增强：结合`rollup()`进行工资报表**

		SQL> /*
		SQL> group by的增强：
		SQL> select deptno,job,sum(sal) from emp group by deptno,job;
		SQL> +
		SQL> select deptno,sum(sal) from emp group by deptno;
		SQL> +
		SQL> select sum(sal) from emp;
		SQL> =
		SQL> select deptno,job,sum(sal) from emp group by rollup(deptno,job);
		SQL>
		SQL> 抽象
		SQL>
		SQL> group by rollup(a,b)
		SQL> =
		SQL> group by a,b
		SQL> +
		SQL> group by a
		SQL> +
		SQL> group by null
		SQL> */
报表执行结果如下：
		
		SQL> break on deptno skip 2;
		SQL> select deptno,job,sum(sal) from emp group by rollup(deptno,job);
		
		 DEPTNO JOB          SUM(SAL)
		------- ---------- ----------
		     10 CLERK            1300
		        MANAGER          2450
		        PRESIDENT        5000
		                         8750
		
		
		     20 CLERK            1900
		        ANALYST          6000
		        MANAGER          2975
		                        10875
		
		
		     30 CLERK             950
		        MANAGER          2850
		        SALESMAN         5600
		                         9400
		
		
		 DEPTNO JOB          SUM(SAL)
		------- ---------- ----------
		
		                        29025
`break on deptno skip 2`用于将数据格式显示成报表形式；`break on deptno`的意思是相同的列`deptno`只显示一次；`skip 2`的意思是不同的部门号之间跳过2行；最后要通过`break on null`这条指令，来取消格式