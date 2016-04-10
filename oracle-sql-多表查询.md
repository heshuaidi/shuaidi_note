# 多表查询
## 多表查询的基础：笛卡尔集
1. 两张表的笛卡尔集等于这两张表的列数相加，行数相乘
2. 笛卡尔集的结果不都是正确的
3. 多表查询就是要根据合适的条件，从笛卡尔集中找到正确的结果
4. 有了连接条件就可以避免使用笛卡尔全集（有错误记录）
5. 连接条件的个数和表的个数的关系为n-1，即：n张表要进行多表查询，至少需要n-1个连接条件
6. 讲多表查询，实际上就是讲连接条件
## 等值连接
就是用等于号做为连接条件，例如：
	
	SQL> --等值连接
	SQL> --查询员工信息：员工号 姓名 月薪 部门名称
	SQL> select e.empno,e.ename,e.sal,d.dname
	  2  from emp e,dept d
	  3  where e.deptno=d.deptno;
	
	 EMPNO ENAME       SAL DNAME
	------ --------- ----- -----------------------
	  7782 CLARK      2450 ACCOUNTING
	  7839 KING       5000 ACCOUNTING
	  7934 MILLER     1300 ACCOUNTING
	  7566 JONES      2975 RESEARCH
	  7902 FORD       3000 RESEARCH
	  7876 ADAMS      1100 RESEARCH
	  7369 SMITH       800 RESEARCH
	  7788 SCOTT      3000 RESEARCH
	  7521 WARD       1250 SALES
	  7844 TURNER     1500 SALES
	  7499 ALLEN      1600 SALES
	  7900 JAMES       950 SALES
	  7698 BLAKE      2850 SALES
	  7654 MARTIN     1250 SALES
# 不等值连接
	SQL> --不等值连接
	SQL> --查询员工信息：员工号 姓名 月薪 工资级别
	SQL>  select e.empno,e.ename,e.sal,g.grade
	  2  from emp e,salgrade g
	  3  where e.sal between g.losal and g.hisal;
	
	     EMPNO ENAME                       SAL      GRADE
	---------- -------------------- ---------- ----------
	      7369 SMITH                       800          1
	      7900 JAMES                       950          1
	      7876 ADAMS                      1100          1
	      7521 WARD                       1250          2
	      7654 MARTIN                     1250          2
	      7934 MILLER                     1300          2
	      7844 TURNER                     1500          3
	      7499 ALLEN                      1600          3
	      7782 CLARK                      2450          4
	      7698 BLAKE                      2850          4
	      7566 JONES                      2975          4
	
	     EMPNO ENAME                       SAL      GRADE
	---------- -------------------- ---------- ----------
	      7788 SCOTT                      3000          4
	      7902 FORD                       3000          4
	      7839 KING                       5000          5
# 外连接
	SQL> --外连接
	SQL> --按部门统计员工人数；部门号 部门名称 人数
	SQL> select d.deptno 部门号,d.dname 部门名称,count(e.empno) 人数
	  2  from emp e,dept d
	  3  where e.deptno=d.deptno
	  4  group by d.deptno,d.dname;
	
	     部门号 部门名称                            人数
	---------- ---------------------------- ----------
	        10 ACCOUNTING                            3
	        20 RESEARCH                              5
	        30 SALES                                 6
**以上结果是错误的**，因为还有一个40号部门，虽然该部门没有员工，但依然希望其显示在结果中，这时候就要使用外连接

所以，外连接的使用场景是：**对于某些不成立的记录，任然希望包含在最后的结果中**

Oracle中，外连接分为**左外连接**和**右外连接**

- 左外连接：当`where e.deptno=d.deptno`不成立的时候，等号**左**边的表任然被包含
	
	写法：`where e.deptno=d.deptno(+)`
- 右外连接：当`where e.deptno=d.deptno`不成立的时候，等号**右**边的表任然被包含

	写法：`where e.deptno(+)=d.deptno(+)`

需要**注意**：左外连接再等号右边写`(+)`，右外连接在等号左边写`(+)`

正确的写法为：

	SQL> select d.deptno 部门号,d.dname 部门名称,count(e.empno) 人数
	  2  from emp e,dept d
	  3  where e.deptno(+)=d.deptno
	  4  group by d.deptno,d.dname;
	
	    部门号 部门名称                           人数
	---------- ---------------------------- ----------
	        10 ACCOUNTING                            3
	        40 OPERATIONS                            0
	        20 RESEARCH                              5
	        30 SALES                                 6
> **丢东西比错了更麻烦，因为有数据丢了你可能意识不到，而数据错了你可以看出来**
## 自连接
定义：通过表的别名，将同一张表视为多张表

例如：
	
	SQL> --自连接
	SQL> --查询员工信息：员工姓名 老板姓名
	SQL> select e.ename 员工姓名,b.ename 老板姓名
	  2  from emp e,emp b
	  3  where e.mgr=b.empno;
	
	员工姓名             老板姓名
	-------------------- --------------------
	FORD                 JONES
	SCOTT                JONES
	TURNER               BLAKE
	ALLEN                BLAKE
	WARD                 BLAKE
	JAMES                BLAKE
	MARTIN               BLAKE
	MILLER               CLARK
	ADAMS                SCOTT
	BLAKE                KING
	JONES                KING
	
	员工姓名             老板姓名
	-------------------- --------------------
	CLARK                KING
	SMITH                FORD
	
	已选择 13 行。
**自连接不适合操作大表**，如果表的记录非常多，笛卡尔集的条数就是表记录数的平方，立方等，效率太低。
## 层次查询
1. 层次查询中只有一张表，因为只有一张表时，不会产生笛卡尔集
2. 层次查询是在遍历一个树，层次查询中必须给出起始条件
3. 会从起始条件开始遍历以其为根节点的树
4. 用**伪列level**来表示树的层次
		
		SQL> --层次查询
		SQL> --查询员工信息：员工号 员工姓名 老板姓名
		SQL> select level,empno,ename,mgr
		  2  from emp
		  3  connect by prior empno=mgr
		  4  start with mgr is null
		  5  order by 1;
		
		     LEVEL      EMPNO ENAME                       MGR
		---------- ---------- -------------------- ----------
		         1       7839 KING
		         2       7566 JONES                      7839
		         2       7698 BLAKE                      7839
		         2       7782 CLARK                      7839
		         3       7902 FORD                       7566
		         3       7521 WARD                       7698
		         3       7900 JAMES                      7698
		         3       7934 MILLER                     7782
		         3       7499 ALLEN                      7698
		         3       7788 SCOTT                      7566
		         3       7654 MARTIN                     7698
		
		     LEVEL      EMPNO ENAME                       MGR
		---------- ---------- -------------------- ----------
		         3       7844 TURNER                     7698
		         4       7876 ADAMS                      7788
		         4       7369 SMITH                      7902
		
		已选择 14 行。
**只要查询的数据满足是棵树，就可以用层次查询来取代自连接，每种查询有自己的优缺点。**
