# Setting Up Hadoop on Linux with Java 8

This guide outlines the steps to set up Hadoop on a Linux system with Java 8. Follow these steps to install, configure, and run Hadoop.

## Prerequisites

Before you begin, ensure you have the following prerequisites in place:

- A Linux-based system (e.g., Ubuntu) with root access.
- Java 8 (OpenJDK) installed.

## Step 1: Update the System and Install Java 8

```bash
sudo apt-get update
sudo apt-get install openjdk-8-jdk openjdk-8-jre -y
## Step 2: Create a Hadoop User
bash
Copy code
sudo addgroup hadoop
sudo adduser hduser --ingroup hadoop
sudo adduser hduser sudo
sudo su hduser
## Step 3: Configure SSH Key
bash
Copy code
ssh-keygen
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
ssh localhost
## Step 4: Download and Install Hadoop
bash
Copy code
wget https://dlcdn.apache.org/hadoop/common/stable/hadoop-3.3.5.tar.gz
tar -xzvf hadoop-3.3.5.tar.gz
sudo mv hadoop-3.3.5 /usr/local/hadoop
sudo chown hduser:hadoop -R /usr/local/hadoop
## Step 5: Set Environment Variables
bash
Copy code
echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64' >> ~/.bashrc
echo 'export HADOOP_HOME=/usr/local/hadoop' >> ~/.bashrc
echo 'export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin' >> ~/.bashrc
echo 'export HADOOP_MAPRED_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export HADOOP_COMMON_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export HADOOP_HDFS_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export YARN_HOME=$HADOOP_HOME' >> ~/.bashrc
echo 'export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop' >> ~/.bashrc
source ~/.bashrc
## Step 6: Update hadoop-env.sh
bash
Copy code
echo 'export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64' >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
echo 'export HADOOP_LOG_DIR=/var/log/hadoop' >> $HADOOP_HOME/etc/hadoop/hadoop-env.sh
sudo mkdir /var/log/hadoop
sudo chown hduser:hadoop -R /var/log/hadoop
## Step 7: Configure Hadoop Configuration Files
core-site.xml
bash
Copy code
echo '<property>' >> $HADOOP_CONF_DIR/core-site.xml
echo '  <name>fs.defaultFS</name>' >> $HADOOP_CONF_DIR/core-site.xml
echo '  <value>hdfs://localhost:54310</value>' >> $HADOOP_CONF_DIR/core-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/core-site.xml
mapred-site.xml
bash
Copy code
echo '<property>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <name>mapreduce.jobtracker.address</name>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <value>localhost:54311</value>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <name>mapreduce.framework.name</name>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '  <value>yarn</value>' >> $HADOOP_CONF_DIR/mapred-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/mapred-site.xml
hdfs-site.xml
bash
Copy code
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
yarn-site.xml
bash
Copy code
echo '<property>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <name>yarn.nodemanager.aux-services</name>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <value>mapreduce_shuffle</value>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '<property>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <name>yarn.nodemanager.env-whitelist</name>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>' >> $HADOOP_CONF_DIR/yarn-site.xml
echo '</property>' >> $HADOOP_CONF_DIR/yarn-site.xml
## Step 8: Format Namenode and Start Hadoop Services
bash
Copy code
hdfs namenode -format
start-dfs.sh
start-yarn.sh
## Step 9: Check Running Processes
bash
Copy code
jps
## Step 10: Create HDFS Directories and Upload a File
bash
Copy code
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/hduser
hdfs dfs -put hadoop-3.3.5.tar.gz /user/hduser
## Step 11: Run a Hadoop Example Job (Pi Calculation)
bash
Copy code
yarn jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-*examples*.jar pi 5 10
## Step 12: Access the Hadoop NameNode WebUI 
Access the Hadoop NameNode WebUI at http://localhost:9870.
