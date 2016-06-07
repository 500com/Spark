# CDH5.6.0 升级至 CDH5.7.0
> 名词释义
> 
- ***CDH**: hadoop是一个开源项目，所以很多公司在这个基础进行商业化，Cloudera对hadoop做了相应的改变。Cloudera公司的发行版，我们将该版本称为CDH。*
- ***CM**: Cloudera Manager，可以简化CDH的安装配置过程，自动在集群节点上安装hadoop相关组件，并进行管理和监控*
- ***Pheonix**: OLTP and operational analytics for Apache Hadoop* 

> 相关资料
> 
- *[Phoenix parcels 下载](http://archive.cloudera.com/cloudera-labs/phoenix/parcels/ "http://archive.cloudera.com/cloudera-labs/phoenix/parcels/")*
- *[CDH parcels 下载](https://archive.cloudera.com/cdh5/parcels/ "https://archive.cloudera.com/cdh5/parcels/")*
- *[CM 下载](http://www.cloudera.com/downloads/manager/5-7-0.html "http://www.cloudera.com/downloads/manager/5-7-0.html")*
- *[Cloudera文档：CM 升级](http://www.cloudera.com/documentation/enterprise/5-6-x/topics/cm_ag_upgrading_cm.html "http://www.cloudera.com/documentation/enterprise/5-6-x/topics/cm_ag_upgrading_cm.html")*
- *[Cloudera文档：CDH 安装](http://www.cloudera.com/documentation/enterprise/5-6-x/topics/installation.html "http://www.cloudera.com/documentation/enterprise/5-6-x/topics/installation.html")*
- *[Cloudera文档：CDH 升级](http://www.cloudera.com/documentation/enterprise/5-6-x/topics/cm_mc_upgrading_cdh.html#cmig_topic_10 "http://www.cloudera.com/documentation/enterprise/5-6-x/topics/cm_mc_upgrading_cdh.html#cmig_topic_10")* 

> 本文档只覆盖了公司在特定情景下CDH升级一部分需要注意的事项，完整详细文档请查看Cloudera官方文档

## 背景
1. 安装Pheonix，需求`Hbase`版本为1.1.x
2. 升级之前的使用CDH和CM版本都为5.6.0，CM和CDH最高版本为5.7.0。CDH5.6.0所含Hbase版本为1.0.x
3. 为了升级`Hbase`版本，如果使用`parcel`方式，需使用CM升级CDH整个套件
4. 使用CM升级CDH，需要保证升级后的CDH版本不大于CM版本
5. 线下集群，5台主机，系统CentOS6.2


## 步骤
###升级CM
> 升级CM增加一些新产品特性,会需求一些额外步骤和新配置，但不会移除已有配置。

> Note: 当升级CM增加新功能(for example, Sqoop 2, WebHCat, and so on)支持的时候，并不会直接安装这些功能或者这些功能所需的依赖，还需另外增加一些服务来启动这些功能。

##### 升级需知
The process for upgrading Cloudera Manager varies depending on the starting point. Categories of tasks to be completed include the following:

- Install databases required for the release. In Cloudera Manager 5, the Host Monitor and Service Monitor roles use an internal database that provides greater capacity and flexibility. You do not need to configure an external database for these roles. If you are upgrading from Cloudera Manager 4, this transition is handled automatically. If you are upgrading a Free Edition installation and you are running a MapReduce service, you are asked to configure an additional database for the Activity Monitor that is part of Cloudera Express.

- 升级Cloudera Manager Server.

- 升级the Cloudera Manager Agent. You can use an upgrade wizard that is invoked when you connect to the Admin Console or manually install the Cloudera Manager Agent packages.

##### 具体步骤
一般情况，升级CM不会影响CDH正常运行
###### 备份使用到的数据库
包括Cloudera Manager所用数据库，hive元数据库，hue所用数据库。

PostgreSql备份：
> pg_dump -h hostname -p 7432 -U scm > /tmp/scm_server_db_backup.$(date +%Y%m%d)
###### 关闭相关角色和服务
- 如果有Cloudera Management Service，就关闭它
- 如果有服务（hive，impala，hue，Oozie，Sentry）使用的是CM自带的PostgreSQL
###### 停止Cloudera Manager Server, Database, 和 Agent
1. Use the Admin Console to stop any running commands. These include commands a user runs and commands Cloudera Manager automatically triggers in response to a state change or a schedule. You can either wait for commands to complete or abort any running commands. For more information on viewing and aborting running commands, see Viewing Running and Recent Commands. If you do not stop all commands, the Cloudera Manager Server will fail to start after upgrade.
2. On the host running the Cloudera Manager Server, stop the Cloudera Manager Server: 
	>$ sudo service cloudera-scm-server stop
3. If you are using the embedded PostgreSQL database for Cloudera Manager, stop the database:
	> $ sudo service cloudera-scm-server-db stop
4. If the Cloudera Manager host is also running the Cloudera Manager Agent, stop the Cloudera Manager Agent:
	> $ sudo service cloudera-scm-agent stop

###### Packages方式升级Cloudera Manager Server

>*通过package方式升级Cloudera Manager server，需要使用Cloudera仓库 [https://archive.cloudera.com/cm5/](https://archive.cloudera.com/cm5/ "https://archive.cloudera.com/cm5/") 或者自己创建仓库（具体请参考[Understanding Custom Installation Solutions](http://www.cloudera.com/documentation/enterprise/5-6-x/topics/cm_ig_custom_installation.html#cmig_topic_21)）。如果没有网络或者网速太慢或不稳定，建议自己建立仓库。*

> *这里我们通过自己建立仓库升级CM。*

1. 在[https://archive.cloudera.com/cm5/](https://archive.cloudera.com/cm5/) 找到符合自己操作系统的Cloudera repo file，这里我们服务器为CentOs6.2，故使用https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/ 下的repo file。此文件中包含仓库的base URL和GPG key文件的地址。其内容如下：

		[cloudera-manager]
		# Packages for Cloudera Manager, Version 5, on RHEL or CentOS 6 x86_64
		name=Cloudera Manager
		baseurl=https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5/
		gpgkey = https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/RPM-GPG-KEY-cloudera
		gpgcheck = 1
2. 找到上一步中的repo file，并将其复制到操作系统的/etc/yum.repos.d/ 目录下。如果不自建仓库，请直接略过3，4步骤，直接进行5步骤
3. 建立web服务器作为Cloudera 仓库的镜像。
4. 根据3步骤中web服务器的域名，更改2步骤中复制到/etc/yum.repos.d/的repo file内容。形式如下：

		name=Cloudera Manager
		mirrorlist=http://xxx.xxx.xxx/cm5/redhat/6/x86_64/cm/5/
		#baseurl=https://archive.cloudera.com/cm5/redhat/6/x86_64/cm/5/
		gpgcheck=1
		...
5. 在相应主机上执行以下命令:
	>$ sudo yum clean all

	>$ sudo yum upgrade cloudera-manager-server cloudera-manager-daemons cloudera-manager-server-db-2 cloudera-manager-agent

	*Note:*
		
	- *yum clean all cleans yum cache directories, ensuring that you download and install the latest versions of the packages.*
	- *If your system is not up to date, any underlying system components must be upgraded before yum update can succeed. yum indicates which components must be upgraded.*



6. 经过上述步骤后，确认相应主机上的cloudera manager版本:
	>$ rpm -qa 'cloudera-manager-*'

		cloudera-manager-repository-5.0-1.noarch
		cloudera-manager-server-5.6.1-0.cm561.p0.3.el6.x86_64
		cloudera-manager-server-db-2-5.6.1-0.cm561.p0.3.el6.x86_64
		cloudera-manager-agent-5.6.1-0.cm561.p0.3.el6.x86_64
		cloudera-manager-daemons-5.6.1-0.cm561.p0.3.el6.x86_64
######启动 Cloudera Manager Server
在安装Cloudera Manager Server的相应主机上，做以下操作：

1. 如果使用自带的内嵌PostgrelSQL，需要启动数据库：
	> $ sudo service cloudera-scm-server-db start
2. 启动Cloudera Manager Server：
	> $ sudo service cloudera-scm-server start

###### 升级与启动Cloudera Manager Agents
1. 登录Cloudera Manager Admin的web Console
2. 使用CM安装Agent software
	
	a. 选择Yes, I would like to upgrade the Cloudera Manager Agent packages now，点击Continue
	b. 选择Custom Repository，选择正确的Agent版本
	c. 按照界面提示，选择所需要配置，点击继续
	d. 输入具有root权限的ssh账户和密码，进行等待自动升级完成。

###升级CDH

#####升级之前
- 如果需要不停止集群进行升级，请参考[rolling upgrade](http://www.cloudera.com/documentation/enterprise/latest/topics/cm_mc_rolling_upgrade.html#cmug_topic_7_12),此功能要求CM付费使用。
- 请仔细阅读cloudera官方文档[Before You Begin](http://www.cloudera.com/documentation/enterprise/latest/topics/install_upgrade_to_cdh57.html#xd_583c10bfdbd326ba-590cb1d1-149e9ca9886--7a4e)部分，了解所需安装的CDH版本和CM版本的特性。

#####升级
1. 将下载好的CDH版本的parcel包和其hash文件放入cloudera manager server 的/opt/cloudera/parcel-repo/目录
2. 在CM管理界面，进行parcel更新检查，发现新parcel包并进行分配。
3. 停止所有服务，并使用pg_dump进行hive/cloudera manager sever/cloudera manager service元数据库备份，可以忽略在此前升级CM已备份的相关数据库；
4. hdfs元数据文件备份，找到namenode存储目录，并打包备份。e.g

		# cd /data/dfs/nn
		# tar -cvf /root/nn_backup_data.tar .
5. 在cm web管理界面运行升级面板，进行升级
		
	1. 登录Cloudera Manager Admin web界面
	2. 在**Home > Status**区，点击cluster名字下面的向下箭头，选择升级**Upgrade Cluster**
	3. 选择合适的CDH Parcels版本进行分配升级
	4. 选择Cloudera Manager upgrade进行全集群的升级和重启
	
6. 等待升级完成，并配置升级后新版本中需要配置的新属性。详细请查看[Upgrade Wizard Actions](http://www.cloudera.com/documentation/enterprise/latest/topics/install_upgrade_to_cdh57_parcels.html#concept_r5x_wfx_jq_section)部分
