#<center>**Hadoop安装**</center>#

##线下环境##
###系统环境###
集群由三台服务器组成，三台机器操作系统均为64位Linux。
> <strong>机器A：</strong>  
> 1. **OS**： `CentOS release 5.6 (Final)`  
> 2. **IP**:`192.168.41.225` ||||  **HOSTNAME**:`s-master`  
> 3. **CPU**：16 * `Intel(R) Xeon(R) CPU E5620  @ 2.40GHz` |||| **MEM**：`32G`  
> <strong>机器B:</strong>   
> 1. **OS**：`CentOS release 6.2 (Final)`    
> 2. **IP**：`192.168.41.177` ||||  **HOSTNAME**:`s-node-4`   
> 3. **CPU**: 8 * `Intel(R) Xeon(R) CPU E5405  @ 2.00GHz` |||| **MEM**：`8G`  
> <strong>机器C:</strong>  
> 1. **OS**：`CentOS release 6.2 (Final)`      
> 2. **IP**：`192.168.41.178` ||||  **HOSTNAME**:`s-node-5`  
> 3. **CPU**: 8 * `Intel(R) Xeon(R) CPU E5405  @ 2.00GHz` |||| **MEM**：`8G`
  


  

##安装##
###软件与知识储备###
####1. ssh、sshd、ssh-agent####
	
	查看sshd服务是否启动：ps -ef|grep ssh
	启动sshd服务：service sshd start 或者 cd /etc/init.d  && sshd start
	
  - 一般linux发行版都已默认开启sshd服务。
  - ssh 是客户端  
  - sshd 应该是服务器端的  
  - ssh-agent是一种控制用来保存公钥身份验证所使用的私钥的程序。
 
####2. Java版本选择sun-jdk1.7.0_67####
下载地址：[jdk1.7.0_79.rpm](http://www.oracle.com/technetwork/java/javase/downloads/index-jsp-138363.html "jdk1.7.0_79")

####3. hadoop版本选定为hadoop-2.6.0-cdh5.4.3####
下载地址：[http://archive.cloudera.com/cdh5/cdh/5/](http://archive.cloudera.com/cdh5/cdh/5/ "cdh5")  
官方文档：[http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.4.3/](http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.4.3/ "文档")

### 机器承载功能规划 ###
<table>
<tr><td>机器</td><td>承载功能</td></tr>
<tr><td>机器A</td><td> <a>NameNode</a>|<a>ResourceManager</a>|<a>JobHistoryServer</a></td></tr>
<tr><td>机器B</td><td><a>NodeManager</a>|<a>DataNode</a></td></tr>
<tr><td>机器C</td><td><a>NodeManager</a>|<a>DataNode</a></td></tr>
</table>

***

###步骤###
####统一用户及目录####
	分别在集群的每台机器上增加hadoop用户和用户组
  - 增加hadoop用户组： `groupadd hadoop`
  - 增加hadoop用户： `useradd -g hadoop -d /home/hadoop hadoop `
  - 如果hadoop用户或者用户组已存在，可以省略对应步骤。 
 
####修改hosts文件让集群机器彼此了解####
	host文件路径： /etc/hosts
	文件末尾增加内容： 
		###hadoop hosts add by sunwq
	    192.168.41.225 s-master
		192.168.41.177 s-node-4
		192.168.41.178 s-node-5	
####机器之间建立ssh无密码登录互信####
	1. 进入用户的home目录下有.ssh文件夹（没有的请运行ssh，会自动生成此目录），查看是否已经生成公私钥id_rsa.pub和id_rsa。
		cd ~/.ssh/ && ls -al |grep id_rsa
	2. 没有钥对的可以运行如下命令，然后回车确认一直到生成id_rsa.pub和id_rsa：
		ssh-keygen -t rsa
	3. 如果A机器想无密码ssh登录B机器，则需将A机器的公钥id_rsa.pub的内容append到B机器的.ssh目录下的authorized_keys文件末尾（没有的请创建）。这里将三机器的公钥考入master的authorized_keys中，然后再分别scp到node机器中。
		cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
		scp hadoop@s-node-4：~/.ssh/id_rsa.pub ~/.ssh/id_rsa.pub.node4
		cat ~/.ssh/id_rsa.pub.node4 >> ~/.ssh/authorized_keys
		scp hadoop@s-node-5：~/.ssh/id_rsa.pub ~/.ssh/id_rsa.pub.node5
		cat ~/.ssh/id_rsa.pub.node5 >> ~/.ssh/authorized_keys
		scp ~/.ssh/authorized_keys hadoop@s-node-4：~/.ssh/
		scp ~/.ssh/authorized_keys hadoop@s-node-5：~/.ssh/
	4. 最后确定各机器.ssh文件夹和其中的文件的权限。
		id_rsa 600
		id_rsa.pub 644
		authorized_keys 644
		known_hosts 644
		. 700
		.. 700

####安装java####
	rpm -ivh  jdk-7u67-linux-x64.rpm --安装jdk
	cd /usr/java
	ln -s jdk1.7.0_67 jdk  --建立软链
	vi /etc/profile  
	在profile文件末尾添加环境变量：
		export JAVA_HOME=/user/java/jdk
		export PATH=$JAVA_HOME/bin:$PATH
		export CLASSPATH=$JAVA_HOME/lib 
						
####解压hadoop####
	将下载下来的hadoop压缩包放入/usr/local/文件夹下
	 	tar -zxvf hadoop-2.6.0-cdh5.4.2.tar -C /usr/local
	更改hadoop目录所有权
		chown -R hadoop:hadoop /usr/local/hadoop-2.6.0-cdh5.4.2
	建立软链接
		ln -s /usr/local/hadoop-2.6.0-cdh5.4.2 /usr/local/hadoop
	修改环境变量
		vi /etc/profile
		在profile文件末尾添加：
			export HADOOP_HOME=/usr/local/hadoop
			export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
			export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop
			export HADOOP_LOG_DIR=/usr/log
			export YARN_LOG_DIR=/usr/log
			export PATH=$HADOOP_HOME/sbin:$HADOOP_HOME/bin:$PATH
	使环境变量立即生效
			source /etc/profile

####修改配置文件####
	修改$HADOOP_CONF_DIR 文件夹下的hadoop-env.sh、core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml、masters、slaves
	1. hadoop-env.sh 修改：
		export JAVA_HOME=/usr/java/jdk
	2. core-site.xml 加入属性：
		<property>
                <name>fs.defaultFS</name>
                <value>hdfs://s-master:9000</value>
        </property>

        <property>
                <name>io.file.buffer.size</name>
                <value>131072</value>
        </property>
        <property>
                 <name>fs.tachyon.impl</name>
                 <value>tachyon.hadoop.TFS</value>
        </property>
        <property>
                <name>fs.tachyon-ft.impl</name>
                <value>tachyon.hadoop.TFS</value>
        </property>
	3. hdfs-site.xml 加入属性：
		<property>
                <name>dfs.namenode.name.dir</name>
                <value>/usr/data/dfs/namemode/data</value>
        </property>
        <property>
                <name>dfs.blocksize</name>
                <value>134217728</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>/usr/data/dfs/datanode/data</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>2</value>
        </property>
	4. mapred-site.xml 加入属性：
		<property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>mapreduce.map.memory.mb</name>
                <value>1536</value>
        </property>
        <property>
                <name>mapreduce.map.java.opts</name>
                <value>-Xmx1024M</value>
        </property>
        <property>
                <name>mapreduce.reduce.memory.mb</name>
                <value>2048</value>
        </property>
        <property>
                <name>mapreduce.reduce.java.opts</name>
                <value>-Xmx1536M</value>
        </property>
        <property>
                <name>mapreduce.task.io.sort.mb</name>
                <value>512</value>
        </property>
        <property>
                <name>mapreduce.task.io.sort.factor</name>
                <value>100</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.intermediate-done-dir</name>
                <value>$DATA_PATH/MR/intermediate/</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.done-dir</name>
                <value>$DATA_PATH/MR/done/</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>s-master</value>
        </property>
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>s-master</value>
        </property>
		
	5. yarn-site.xml 加入属性：
		<property>
                <name>yarn.resourcemanager.hostname</name>
                <value>s-master</value>
        </property>
        <property>
                <name>yarn.resourcemanager.scheduler.class</name>  
                <value>org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler</value>
        </property>
        <property>
                <name>yarn.scheduler.minimum-allocation-mb</name>
                <value>64</value>
        </property>
        <property>
                <name>yarn.scheduler.maximum-allocation-mb</name>
                <value>512</value>
        </property>
        <property>
                <name>yarn.nodemanager.resource.memory-mb</name>
                <value>512</value>
        </property>
        <property>
                <name>yarn.nodemanager.vmem-pmem-ratio</name>
                <value>1.5</value>
        </property>
        <property>
                <name>yarn.nodemanager.local-dirs</name>
                <value>/usr/log/yarn/local-data</value>
        </property>
	
	6. masters中加入一行：
		s-master
	7. slaversz中加入两行:
		s-node-4
		s-node-5	 

####修改权限####
	建立log日志路径,并更改权限
		mkdir -p /usr/log
		chown -R hadoop:hadoop /usr/log
		chmod -R 775 /usr/log
	建立数据保存路径,并更改权限
		mkdir -p /usr/data
		chown -R hadoop:hadoop /usr/data
		chmod -R 775 /usr/data

####启动及验证####
1、在三台机器中，分别重复以上步骤。 对于修改的文件可以直接复制分发，减少重复劳动。
  
2、在s-master上进行namenode格式化：  
> hdfs namenode -format <cluster_name>  

3、在s-master上启动namenode：  
> hadoop-daemon.sh --config $HADOOP\_CONF\_DIR --script hdfs start namenode

4、在s-node-4和s-node-5上分别启动datanode:
> hadoop-daemon.sh --config $HADOOP\_CONF\_DIR --script hdfs start datanode

5、在s-master上启动resourcemanaer:
> yarn-daemon.sh --config $HADOOP\_CONF\_DIR start resourcemanager

6、在s-node-4和s-node-5上分别启动nodemanager: 
> yarn-daemon.sh --config $HADOOP\_CONF\_DIR start nodemanager 

7、在s-master上启动MR的JobHistory Server：
> mr-jobhistory-daemon.sh start historyserver --config $HADOOP\_CONF\_DIR 

8、分别在s-master、s-node-4和s-node-5上，执行jps命令，需要看到如下进程执行才是正式完全启动Hadoop：  
	
	s-master: JobHistoryServer、NameNode、ResourceManager
	s-node-4: DataNode、NodeManager
	s-node-5: DataNode、NodeManager
在[http://s-master:8088](http://s-master:8088 "s-master:8088")可以看到yarn的web ui界面  
在[http://s-master:50070](http://s-master:50070 "dfs")可以看到dfs的web ui界面   
	