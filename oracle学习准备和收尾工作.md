# Oracle学习的准备工作
1. 将instanceclient安装目录下的tnsnames.ora重命名为tnsnames.ora.linux，将instanceclient安装目录下的tnsnames.ora.windows重命名为tnsnames.ora。
2. 将环境变量path中的，`%ORACLE_HOME%\bin;`删除，并将instanceclient的安装目录添加到path中
3. 打开VMware中的oracle虚拟机，打开Xshell连接上oracle虚拟机，输入`su - oracle`，切换到oracle用户登录
4. 将oracle用户的环境变量文件`/home/oracle/.bash_profile`中的SID改为orcl，即：
		
		export ORACLE_SID=orcl
5. 将/u01/app/oracle/product/11.2.0/db_1/network/admin/listener.ora中有如下配置：

		SID_LIST_LISTENER=
		  (SID_LIST=
		      (SID_DESC=
		                      #BEQUEATH CONFIG
		         (GLOBAL_DBNAME=orcl)
		         (SID_NAME=orcl)
		         (ORACLE_HOME=/u01/app/oracle/product/11.2.0/db_1)
		                      #PRESPAWN CONFIG
		        (PRESPAWN_MAX=20)
		        (PRESPAWN_LIST=
		          (PRESPAWN_DESC=(PROTOCOL=tcp)(POOL_SIZE=2)(TIMEOUT=1))
		        )
		       )
注意：
	
		(GLOBAL_DBNAME=orcl)
         (SID_NAME=orcl)
6. 用oracle用户登录，先后输入如下命令：
	
	该命令用于开启oracle的数据库服务：

		[oracle@oracle ~]$ sqlplus /nolog
		SQL> conn /as sysdba
		SQL> startup
		SQL> quit
	该命令用于开启监听orcl的服务：
		
		[oracle@oracle ~]$ lsnrctl start
*****************
这时就可以在本机上用pl/sql和命令行来连接虚拟机上的oracle数据库了。

命令行连接oracle数据库orcl的命令为：
	
	D:\>sqlplus scott/tiger@192.168.0.10:1521/orcl
# Oracle学习的收尾工作
1. 收尾工作主要任务是，测试用命令行sqlplus连接本地Oracle服务的orcl数据库，以及用ql/sql连接本地Oracle服务的orcl数据库，以便上班使用。
2. 使用本机Oracle服务的tnsnames.ora文件：

	&#160;&#160;&#160;&#160;&#160;&#160;将instanceclient安装目录下的tnsnames.ora重命名为tnsnames.ora.linux；将instanceclient安装目录下的tnsnames.ora.windows重命名为tnsnames.ora。
3. 添加本机Oracle服务器的环境变量，并让系统使用%ORACLE_HOME%\bin\sqlplus.exe命令，而不是instanceclient安装目录下的sqlplus.exe命令：
	
	&#160;&#160;&#160;&#160;&#160;&#160;在环境变量path中的添加`%ORACLE_HOME%\bin;`，并将instanceclient在path中的环境变量删除。
4. 启动listener_1服务，以监听instanceclient的连接。
5. 启动plsql，并以scott/tiger登录orcl数据库，连接成功。
6. 打开命令行，输入：
		
		D:\>sqlplus scott/tiger
连接成功。
