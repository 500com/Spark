#<center>spark安装</center>#
_本文档中，spark安装部分基于之前完成hadoop集群。未安装Hadoop集群的童鞋可以按照 hadoop安装 进行安装。_

##规划##
1. Spark的master进程放在机器s-master上  
2. worker放在s-node-4和s-node-5上
3. spark和scala解压后放在用户home目录下的.local文件夹中

##Spark和scala版本选定##
根据Hadoop版本，选择 **spark-1.4.0-bin-hadoop2.6**   
下载地址：[http://www.apache.org/dyn/closer.cgi/spark/spark-1.4.0/spark-1.4.0-bin-hadoop2.6.tgz](http://www.apache.org/dyn/closer.cgi/spark/spark-1.4.0/spark-1.4.0-bin-hadoop2.6.tgz "镜像下载")  

Scala选择scala-2.11.6
下载地址：[http://www.scala-lang.org/download/2.11.6.html](http://www.scala-lang.org/download/2.11.6.html "scala下载地址")



##部署步骤##
1. 建立spark用户,并加入hadoop组  
	`useradd -g hadoop -d /home/spark spark`
2. 在各机器之间建立spark用户的ssh互信  
	`请参考hadoop安装文档中hadoop用户建立ssh互信`
3. 解压Spark,更改目录权限,做好软链接   
	`mkdir -p /home/spark/.local`
	`tar -zxvf spark-1.4.0-bin-hadoop2.6.tgz -C /home/spark/.local`
	`chown -R spark:hadoop /home/spark/.local`
	`ln -s /home/spark/.local/spark-1.4.0-bin-hadoop2.6 /home/spark/.local/spark`
4. 解压Scala，更改目录权限,做好软链接  
	`tar -zxvf scala-2.11.6.tgz -C /home/spark/.local`
	`chown -R spark:hadoop /home/spark/.local/scala-2.11.6.tgz`
	`ln -s /home/spark/.local/scala-2.11.6 /home/spark/.  local/scala`
5. 添加用户环境变量,修改~/.bash_profile文件,添加如下内容，并source执行  
	`export JAVA_HOME=/usr/java/jdk`
	`export PATH=$PATH:$JAVA_HOME/bin`
	`export HADOOP_HOME=/usr/local/hadoop`
	`export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop`
	`export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop`
	`export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin`  
	`export SCALA_HOME=/home/spark/.local/scala`  
	`export PATH=$PATH:$SCALA_HOME/bin`  
	`export SPARK_HOME=/home/spark/.local/spark`  
	`export SPARK_CONF_DIR=$SPARK_HOME/conf`
	`export PATH=$PATH:$SPARK_HOME/sbin:$SPARK_HOME/bin`

6. 	修改$SPARK_HOME/conf/spark-env.sh  
	`export JAVA_HOME=/usr/java/jdk`
	`export SCALA_HOME=/home/spark/.local/scala`
	`export SPARK_HOME=/home/spark/.local/spark`
	`export SPARK_MASTER_OPTS=-Xmx1024m`
	`export SPARK_MASTER_IP=s-master`
	`export SPARK_WORKER_INSTANCES=2`
	`export SPARK_WORKER_CORES=3`
	`export SPARK_WORKER_MEMORY=1024m`
	`export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop`

7.  修改$SPARK_HOME/conf/slaves,添加节点信息：  
	`s-node-4`  
	`s-node-5`
	
8.  将配置好的spark分发到其他节点  
	`scp -r /home/spark/.local/spark s-node-4:/home/spark/.local/`  
	`scp -r /home/spark/.local/spark s-node-5:/home/spark/.local/`  

##启动##
1. standalone模式启动，在s-master上执行:
	`cd /home/spark/.local/spark/bin`  
	`./start-all.sh`
2. yarn cluster模式启动：  
	`./bin/spark-submit --class`   `org.apache.spark.examples.SparkPi \`  
	`--master yarn-cluster \`  
	`--num-executors 3 \`  
	`--driver-memory 1g \`  
	`--executor-memory 1g \`  
	`--executor-cores 1 \`  
	`--queue thequeue \`  
	`lib/spark-examples*.jar \`  
	`10`  
`