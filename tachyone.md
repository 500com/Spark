#<center>Tachyon安装</center>#
_本文档中，tachyon安装部分基于之前完成hadoop集群。未安装Hadoop集群的童鞋可以按照 hadoop安装 进行安装。_

##规划##
1. Tachyon的TachyonMaster进程放在机器s-master上  
2. TachyonWorker放在s-node-4和s-node-5上
3. Tachyon放于路径/usr/local

##Tachyon版本选定##
根据Hadoop版本，选择 **tachyon-0.6.4**   


##部署步骤##
1. 建立tachyon用户,并加入hadoop组  
	`useradd -g hadoop -d /home/spark spark`  

2. 使tachyon用户拥有sudo权限(危险操作，请注意)  
	`chmod u+w /etc/sudoers`  
	`vi /etc/sudoers`  
	添加  tachyon    ALL=(ALL)       ALL  
	`chmod u-w /etc/sudoers`

2. 在各机器之间建立tachyon用户的ssh互信  
	`请参考hadoop安装文档中hadoop用户建立ssh互信`

3. 解压tachyon,更改目录权限,做好软链接   
	`tar -zxvf tachyon-0.6.4-bin.tar.gz -C /usr/local`
	`chown -R tachyon:hadoop /usr/local/tachyon-0.6.4`
	`ln -s /usr/local/tachyon-0.6.4 /usr/local/tachyon`

5. 添加用户环境变量,修改~/.bash_profile文件,添加如下内容，并source执行  
	`export TACHYON_HOME=/usr/local/tachyon`
	`export TACHYON_CONF_DIR=$TACHYON_HOME/conf`
	`export PATH=$PATH:$TACHYON_HOME/bin`  

6. 	修改$TACHYON_HOME/conf/spark-env.sh  
	`export JAVA="$JAVA_HOME/bin/java`
	`export CLASSPATH=.`  
	`export TACHYON_MASTER_ADDRESS=s-master`
	`export TACHYON_UNDERFS_ADDRESS=hdfs://s-master:9000`
	`export TACHYON_WORKER_MEMORY_SIZE=1GB`
	`export TACHYON_UNDERFS_HDFS_IMPL=org.apache.hadoop.hdfs.DistributedFileSystem`
	

7.  修改$TACHYON_HOME/conf/workers,添加节点信息：  
	`s-node-4`  
	`s-node-5`
	
8.  将配置好的Tachyon分发到其他节点  
	`scp -r /usr/local/tachyon s-node-4:/usr/local/`  
	`scp -r /home/spark/.local/spark s-node-5:/usr/local/`  

##启动##
1. 格式化并启动，在s-master上执行:
	`./bin/tachyon format`  
	`./bin/tachyon-start.sh all SudoMount`

启动Tachyon有不同的选项：
  
- *bin/tachyon-start.sh all Mount*   
自动挂载TachyonWorker所使用的RamFS，然后启动TachyonMaster和所有TachyonWorker。因为有mount操作，具有root权限的用户才能用此命令正常启动。 

- *bin/tachyon-start.sh all SudoMount*   
 	在启动前自动挂载TachyonWorker所使用的RamFS，然后启动TachyonMaster和所有TachyonWorker。由于使用sudo mount命令，所以需要用户有sudo权限

- *bin/tachyon-start.sh all NoMount*  	     
	认为RamFS已经挂载好，不执行挂载操作，只启动TachyonMaster和所有TachyonWorker

##验证启动##
- jps命令后，可以看到TachyonMaster和TachyonWorker进程。
- web ui [http://s-master:19999](http://s-master:19999 "tachyon Web UI") ,可以观察Tachyon状态
- 能顺利执行命令 `./bin/tachyon runTests` 