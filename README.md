# Setting Up Hadoop on Linux with Java 8

This guide outlines the steps to set up Hadoop on a Linux system with Java 8. Follow these steps to install, configure, and run Hadoop.

## Step 1: Update the System and Install Java 8

```bash
sudo apt-get update
sudo apt-get install openjdk-8-jdk openjdk-8-jre -y
sudo addgroup hadoop
sudo adduser hduser --ingroup hadoop
sudo adduser hduser sudo
sudo su hduser
ssh-keygen
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
ssh localhost
wget https://dlcdn.apache.org/hadoop/common/stable/hadoop-3.3.5.tar.gz
tar -xzvf hadoop-3.3.5.tar.gz
sudo mv hadoop-3.3.5 /usr/local/hadoop
sudo chown hduser:hadoop -R /usr/local/hadoop
echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64' >> ~/.bashrc
echo 'export HADOOP_HOME=/usr/local/hadoop' >> ~/.bashrc
echo 'export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin' >> ~/.bashrc
echo 'export HADOOP_MAPRED_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export HADOOP_COMMON_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export HADOOP_HDFS_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export YARN_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop' >> ~/.bashrc
source ~/.bashrc
echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64' >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
echo 'export HADOOP_LOG_DIR=/var/log/hadoop' >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
sudo mkdir /var/log/hadoop
sudo chown hduser:hadoop -R /var/log/hadoop
echo '<property>' >> $HADOOP_CONF_DIR/core-site.xml
echo '  <name>fs.defaultFS</name>' >> $HADOOP_CONF_DIR/core-site.xml
echo '  <value>hdfs://localhost:54310</value>' >> $HADOOP_CONF_DIR/core-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/core-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <name>mapreduce.jobtracker.address</name>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <value>localhost:54311</value>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <name>mapreduce.framework.name</name>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <value>yarn</value>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/mapred-site.xml
sudo mkdir -p /usr/local/hadoop_store/hdfs/namenode
sudo mkdir -p /usr/local/hadoop_store/hdfs/datanode
sudo chown hduser:hadoop -R /usr/local/hadoop_store
echo '<property>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '  <name>dfs.replication</name>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '  <value>1</value>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '  <name>dfs.namenode.name.dir</name>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '  <value>file:/usr/local/hadoop_store/hdfs/namenode</value>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '  <name>dfs.datanode.data.dir</name>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '  <value>file:/usr/local/hadoop_store/hdfs/datanode</value>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/hdfs-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <name>yarn.nodemanager.aux-services</name>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <value>mapreduce_shuffle</value>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <name>yarn.nodemanager.env-whitelist</name>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/yarn-site.xml
hdfs namenode -format
start-dfs.sh
start-yarn.sh
jps
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/hduser
hdfs dfs -put hadoop-3.3.5.tar.gz /user/hduser
yarn jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-*examples*.jar pi 5 10
