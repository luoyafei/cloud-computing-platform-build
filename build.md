### 前提
```
docker创建一个独立的虚拟的网络, 可以提供DNS解析功能
docker network create --driver=bridge hadoop
docker network ls
```

1. 创建linux容器
```shell
docker pull ubuntu:16.04
```

2. 进入容器
```sehll
docker run -it ubuntu:16.04 /bin/bash
```

3. 修改apt源
```shell
cp /etc/apt/sources.list /etc/apt/sources_init.list
rm /etc/apt/sources.list
echo "deb http://mirrors.aliyun.com/ubuntu/ xenial main  
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main 
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main
deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security universe" > /etc/apt/sources.list
阿里源:
deb http://mirrors.aliyun.com/ubuntu/ xenial main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main

deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main

deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates universe

deb http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security universe
```

4. apt update

5. apt install openjdk-8-jdk
```java
java -version
```

6. apt install scala
```scala
>scala
```

7. 安装Hadoop
```shell
apt install vim
apt install net-tools
apt-get install openssh-server
apt-get install openssh-client
ssh-keygen -t rsa -P ""
cat .ssh/id_rsa.pub >> .ssh/authorized_keys
启动ssh服务
service ssh start
将ssh加入自启动：
“service ssh start” >> ~/.bashrc
安装Hadoop
wget http://mirrors.hust.edu.cn/apache/hadoop/common/hadoop-3.2.0/hadoop-3.2.0.tar.gz
tar -zxvf hadoop-3.2.0.tar.gz -C /usr/local/
cd /usr/local/
mv hadoop-3.2.0 hadoop
修改环境变量
vi /etc/profile
#java
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export JRE_HOME=${JAVA_HOME}/jre    
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib    
export PATH=${JAVA_HOME}/bin:$PATH
#hadoop
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_PREFIX=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export HADOOP_COMMON_HOME=$HADOOP_HOME 
export HADOOP_HDFS_HOME=$HADOOP_HOME 
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME 
export HADOOP_INSTALL=$HADOOP_HOME 
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native 
export HADOOP_CONF_DIR=$HADOOP_HOME 
export HADOOP_LIBEXEC_DIR=$HADOOP_HOME/libexec 
export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH
export HADOOP_CONF_DIR=$HADOOP_PREFIX/etc/hadoop
export HDFS_DATANODE_USER=root
export HDFS_DATANODE_SECURE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export HDFS_NAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
生效：source /etc/profile
```

8. 配置Hadoop配置信息
```xml
cd /usr/local/hadoop/etc/hadoop
vi hadoop-env.sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HDFS_NAMENODE_USER=root
export HDFS_DATANODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
export YARN_RESOURCEMANAGER_USER=root
export YARN_NODEMANAGER_USER=root
vi core-site.xml
<configuration>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://h01:9000</value>
    </property>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>/home/hadoop3/hadoop/tmp</value>
    </property>
</configuration>
vi hdfs-site.xml
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>/home/hadoop3/hadoop/hdfs/name</value>
    </property>
    <property>
        <name>dfs.namenode.data.dir</name>
        <value>/home/hadoop3/hadoop/hdfs/data</value>
    </property>
</configuration>
vi mapred-site.xml
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>mapreduce.application.classpath</name>
        <value>
            /usr/local/hadoop/etc/hadoop,
            /usr/local/hadoop/share/hadoop/common/*,
            /usr/local/hadoop/share/hadoop/common/lib/*,
            /usr/local/hadoop/share/hadoop/hdfs/*,
            /usr/local/hadoop/share/hadoop/hdfs/lib/*,
            /usr/local/hadoop/share/hadoop/mapreduce/*,
            /usr/local/hadoop/share/hadoop/mapreduce/lib/*,
            /usr/local/hadoop/share/hadoop/yarn/*,
            /usr/local/hadoop/share/hadoop/yarn/lib/*
        </value>
    </property>
</configuration>
vi yarn-site.xml
<configuration>
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>h01</value>
    </property>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
vi workers
h01
h02
h03
h04
h05
```

9. 提交镜像
```shell
docker commit -a 'yafei10@staff.weibo.com' -m 'init create' 963a2b2318a3 registry.api.weibo.com/yafei10/ubuntu-hadoop:0.0.1
```

10. 启动5个终端
首先启动master节点, h01用来做master, 需要暴露端口, 提供访问web页面
```shell
docker run -it --network hadoop -h "h01" --name "h01" -p 9870:9870 -p 8088:8088 registry.api.weibo.com/yafei10/ubuntu-hadoop:0.0.1 /bin/bash
```

说明: 指定网络为新创建的虚拟桥接网络:`hadoop` <br/>
接者启动`4`个从
```shell
docker run -it --network hadoop -h "h02" --name "h02" registry.api.weibo.com/yafei10/ubuntu-hadoop:0.0.1 /bin/bash
docker run -it --network hadoop -h "h03" --name "h03" registry.api.weibo.com/yafei10/ubuntu-hadoop:0.0.1 /bin/bash
docker run -it --network hadoop -h "h04" --name "h04" registry.api.weibo.com/yafei10/ubuntu-hadoop:0.0.1 /bin/bash
docker run -it --network hadoop -h "h05" --name "h05" registry.api.weibo.com/yafei10/ubuntu-hadoop:0.0.1 /bin/bash
```

10. 启动Hadoop
进入`h01` <br/>
首先进行`namenode`的格式化, 否则`hdfs`会起不来
```shell
./usr/local/hadoop/bin/hadoop namenode -format
```

启动
```shell
./usr/local/hadoop/sbin/start-all.sh
```

```
log:
Starting namenodes on [h01]
h01: Warning: Permanently added 'h01,172.18.0.7' (ECDSA) to the list of known hosts.
Starting datanodes
h05: Warning: Permanently added 'h05,172.18.0.11' (ECDSA) to the list of known hosts.
h02: Warning: Permanently added 'h02,172.18.0.8' (ECDSA) to the list of known hosts.
h03: Warning: Permanently added 'h03,172.18.0.9' (ECDSA) to the list of known hosts.
h04: Warning: Permanently added 'h04,172.18.0.10' (ECDSA) to the list of known hosts.
h03: WARNING: /usr/local/hadoop/logs does not exist. Creating.
h05: WARNING: /usr/local/hadoop/logs does not exist. Creating.
h02: WARNING: /usr/local/hadoop/logs does not exist. Creating.
h04: WARNING: /usr/local/hadoop/logs does not exist. Creating.
Starting secondary namenodes [h01]
Starting resourcemanager
Starting nodemanagers
root@h01:/usr/local/hadoop/sbin#
说明：访问本机的 8088 与 9870 端口就可以看到监控信息了
使用命令 ./hadoop dfsadmin -report 可查看分布式文件系统的状态
```

11. 安装Spark
```shell
wget http://mirrors.tuna.tsinghua.edu.cn/apache/spark/spark-2.4.3/spark-2.4.3-bin-hadoop2.7.tgz
tar -zxvf spark-2.4.3-bin-hadoop2.7.tgz  -C /usr/local/
cd /usr/local/
mv spark-2.4.3-bin-hadoop2.7 spark-2.4.0
修改Spark的环境变量
vi /etc/profile
export SPARK_HOME=/usr/local/spark-2.4.3
export PATH=$PATH:$SPARK_HOME/bin
source /etc/profile
(
    wget http://archive.apache.org/dist/spark/spark-2.4.0/spark-2.4.0-bin-hadoop2.7.tgz
    tar -zxvf spark-2.4.0-bin-hadoop2.7.tgz  -C /usr/local/
    mv spark-2.4.0-bin-hadoop2.7 spark-2.4.0
)
```

12. 修改Spark配置
```shell
cd /usr/local/spark-2.4.3/conf
mv spark-env.sh.template spark-env.sh
修复spark-env.sh,   追加
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop
export SCALA_HOME=/usr/share/scala

export SPARK_MASTER_HOST=h01
export SPARK_MASTER_IP=h01
export SPARK_WORKER_MEMORY=4g
修改文件名
mv slaves.template slaves
修改slaves
h01
h02
h03
h04
h05
同步其他机器的spark配置
scp -r /usr/local/spark-2.4.0 root@h02:/usr/local/
```
