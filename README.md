### Hadoop���� tf-idf����� ���ϱ�

tf-idf�� � �������� ���� �ܾ �󸶳� �ʿ��� �ܾ��ΰ��� �˾Ƴ��� ����Դϴ�.
� �ܾ(term��) Ư�� doc�� ���� ���ü��� ����ġ�� ��������, �پ��� doc�� ���ü��� ����ġ�� 0�� �����ϰ� �˴ϴ�.
����ġ�� ���ϴ� ����� tf * log(N/n) �Դϴ�. ���⼭ N�� ��ü doc�� ����, n�� �ܾ ���� doc�� �����Դϴ�.
(�� � �ܾ ��ü doc�� �ٳ����� N=n�̹Ƿ� log1=0�̱� ������ ����ġ�� 0�̵Ǵµ�, �߿����� ���� �ܾ��� ���ɼ��� ���ٴ� �ǹ��Դϴ�.)

=====================================
### Vagrant ��ġ�ϱ�(windows���� ����)

1. oracle Virtual Box�� ���ٸ� ��ġ���ּ���.(https://www.virtualbox.org/wiki/Downloads)
2. vagrant window ������ ��ġ�մϴ�. (https://www.vagrantup.com/downloads.html) 
3. ��ġ �� cmd â�� ���� <br>
C:\\> md Project<br>
C:\\> cd Project<br>
C:\\Project> vagrant box add ubuntu/trusty64 (�Ŀ� vagrant box list �ϸ� ���� ��ġ�� box�� �� �� �ֽ��ϴ�.)<br>
```
C:\Project>vagrant box add ubuntu/trusty64
==> box: Loading metadata for box 'ubuntu/trusty64'
    box: URL: https://atlas.hashicorp.com/ubuntu/trusty64
==> box: Adding box 'ubuntu/trusty64' (v14.04) for provider: virtualbox
��¼�� ��¼��....
```
<br>
C:\\Project> vagrant init ubuntu/trusty64 (Vagrantfile�̶�� ȯ�������� ��������ϴ�.)
```
C:\Project>vagrant init ubuntu/trusty64
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```
####ȯ������ �����ϱ�
Vargrantfile �� ������ ���� �����մϴ�.<br>
```
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what # you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
# master node
  config.vm.define "master" do |master|
    master.vm.provider "virtualbox" do |v|
      v.name = "master"
      v.memory = 4096
      v.cpus = 1
    end
    master.vm.box = "ubuntu/trusty64"
    master.vm.hostname = "master"
    master.vm.network "private_network", ip: "192.168.200.2"
    master.vm.provision "shell", path: "./setup.sh"
  end

  # slave1 node
  config.vm.define "slave1" do |slave1|
    slave1.vm.provider "virtualbox" do |v|
      v.name = "slave1"
      v.memory = 2048
      v.cpus = 1
    end
    slave1.vm.box = "ubuntu/trusty64"
    slave1.vm.hostname = "slave1"
    slave1.vm.network "private_network", ip: "192.168.200.10"
    slave1.vm.provision "shell", path: "./setup.sh"
  end
 #slave2 node
  config.vm.define "slave2" do |slave2|
    slave2.vm.provider "virtualbox" do |v|
      v.name = "slave2"
      v.memory = 2048
      v.cpus = 1
    end
    slave2.vm.box = "ubuntu/trusty64"
    slave2.vm.hostname = "slave2"
    slave2.vm.network "private_network", ip: "192.168.200.11"
    slave2.vm.provision "shell", path: "./setup.sh"
end

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   sudo apt-get update
  #   sudo apt-get install -y apache2
  # SHELL
end
```

setup.sh �� ������ ���� ����ϴ�.<br>
```
#!/bin/bash

# Variables
tools=/home/hadoop/tools
JH=/home/hadoop/tools/jdk
HH=/home/hadoop/tools/hadoop

# Install jdk
apt-get install -y openjdk-7-jre-headless
apt-get install -y openjdk-7-jdk
apt-get install -y expect

# Add group and user
addgroup hadoop
useradd -g hadoop -d /home/hadoop/ -s /bin/bash -m hadoop

# Download hadoop
mkdir -p /home/hadoop/hdfs/name
mkdir -p /home/hadoop/hdfs/data
mkdir $tools
cd $tools
wget http://ftp.daum.net/apache//hadoop/common/hadoop-1.2.1/hadoop-1.2.1.tar.gz
tar xvf hadoop-1.2.1.tar.gz
ln -s $tools/hadoop-1.2.1 $tools/hadoop
ln -s /usr/lib/jvm/java-1.7.0-openjdk-amd64 $tools/jdk
chown -R hadoop:hadoop /home/hadoop
chmod 755 -R /home/hadoop

# Setting environment
echo "" >> ~hadoop/.bashrc
echo "export JAVA_HOME=$JH" >> ~hadoop/.bashrc
echo "export PATH=\$PATH:\$JAVA_HOME/bin:\$HH/bin" >> ~hadoop/.bashrc

# Setting Hosts
echo "192.168.200.2 master" >> /etc/hosts
echo "192.168.200.10 slave1" >> /etc/hosts
echo "192.168.200.11 slave2" >> /etc/hosts
```

 C:\\Project> vagrant up�� ���ݴϴ�. 
 (Vagrantfile�� setup.sh������ �ִ� ��ο��� ���ּ���. �����ɸ��ϴ�.)
```
C:\Project>vagrant up
Bringing machine 'master' up with 'virtualbox' provider...
Bringing machine 'slave1' up with 'virtualbox' provider...
Bringing machine 'slave2' up with 'virtualbox' provider...
==> master: Importing base box 'ubuntu/trusty64'...
==> master: Matching MAC address for NAT networking...
==> master: Checking if box 'ubuntu/trusty64' is up to date...
==> master: Setting the name of the VM: master
==> master: Clearing any previously set forwarded ports...
==> master: Clearing any previously set network interfaces...
==> master: Preparing network interfaces based on configuration...
    master: Adapter 1: nat
    master: Adapter 2: hostonly
==> master: Forwarding ports...
    master: 22 => 2222 (adapter 1)
==> master: Running 'pre-boot' VM customizations...
==> master: Booting VM...
==> master: Waiting for machine to boot. This may take a few minutes...
    master: SSH address: 127.0.0.1:2222
    master: SSH username: vagrant
    master: SSH auth method: private key
    �̷��� ������ �� 30������ �ҿ�. �޸� 8�Ⱑ ���Ͻø� ���������ϴ�.
```
* �Ŀ� VM�� �����Ű�� Master 1��� Slave 2�밡 ���������� �� �� �ֽ��ϴ�. 


* superputty�� putty�� ��� superputty�� ������� 3���� ����ݴϴ�.<br>
  (Host : 127.0.0.1:2222 Login:vagrant Password:vagrant)<br>
  (Host : 127.0.0.1:2200 Login:vagrant Password:vagrant)<br>
  (Host : 127.0.0.1:2201 Login:vagrant Password:vagrant)<br>
 2222��Ʈ�� master��Ʈ�̰� 2200���� 1�� �����ϴ� ���� slave��Ʈ�Դϴ�. <br>
2200��Ʈ�� �������� �� ���������� 2201 2202 ������ ��Ʈ�� �ø��鼭 �����غ��ʽÿ�.<br>

==================================
### Hadoop Setting �ϱ�(linux ����ӽſ��� ����)
���� ��й�ȣ�� �����մϴ�.(Master�� slave1, slave2 ��� �ٲپ� �ݴϴ�.) <br>
```
  vagrant@master:~$ su
  Password: vagrant

  root@master:/home/vagrant# passwd hadoop
  Enter new UNIX password: hadoop
  Retype new UNIX password: hadoop
  passwd: password updated successfully 
```

master, slave1,slave2 �� ȣ��Ʈ ������ �����մϴ�.
```
  root@master:/home/vagrant# vi /etc/hosts
  #127.0.0.1 master master
  root@slave1:/home/vagrant# vi /etc/hosts
  #127.0.0.1 slave1 slave1
  root@slave2:/home/vagrant# vi /etc/hosts
  #127.0.0.1 slave2 slave2
```
ssh �������� �����մϴ�.(master������ �����ϼ���!) enterŰ�� ġ�ø� �˴ϴ�.
```
root@master~#su hadoop <hadoop ������ �α��� �Ѵ�.>
[hadoop@master]$ ssh-keygen -t rsa 
Generating public/private rsa key pair. 
Enter file in which to save the key (/home/hadoop/.ssh/id_rsa): [enter] 
Created directory '/home/hadoop/.ssh'. 
Enter passphrase (empty for no passphrase): [enter]
Enter same passphrase again: [enter] 
Your identification has been saved in /home/hadoop/.ssh/id_rsa. 
Your public key has been saved in /home/hadoop/.ssh/id_rsa.pub. 
The key fingerprint is:
ae:53:56:1a:d2:98:8e:09:bf:8e:a9:15:9b:3e:d6:e5 hadoop@master
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|                 |
|       +         |
|  .   + o .      |
|  .o + .S+       |
|   ++ o.+        |
|  +. + o.        |
| oooo E.         |
|.o+o. ..         |
+-----------------+
```
ssh ����Ű�� �����մϴ�.(master������ �����ϼ���.)
slave1, slave2���� �Ʒ��� ���� ���ݴϴ�.
```
(�ڱ� �ڽ��� ����Ű�� authorized_keys�� �������ݴϴ�.)
hadoop@master:/home/vagrant$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

(ssh-copy-id ��ɾ�� slave1,2���� ����Ű�� �����Ѵ�.)
hadoop@master:~$ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@slave1

hadoop@master:/home/vagrant$ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@slave1(slave2���Դ� hadoop@slave2�� ���ݴϴ�.)                                             
The authenticity of host 'slave1 (192.168.200.10)' can't be established.
ECDSA key fingerprint is e7:1b:73:bb:4d:00:8e:ce:26:53:67:12:c6:26:4f:cb.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
hadoop@slave1's password: hadoop

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'hadoop@slave1'"
and check to make sure that only the key(s) you wanted were added.
```

Hadoop�� �ٽ� �����մϴ�.

master�� ���
```
hadoop@master:/home/hadoop$ rm -rf ~/hdfs  <������ ������ ~/hdfs������ �����Ѵ�.>
hadoop@master:/home/hadoop$ mkdir -p ./hdfs/name  <namenode�� ����� > 
hadoop@master:/home/hadoop$ chmod 755 -R ./hdfs   < ������ ������ 755�� �������ش�. >
```

slave1,2�� ���
```
hadoop@slave1:/home/hadoop$ rm -rf ./hdfs
hadoop@slave1:/home/hadoop$ mkdir -p ./hdfs/data
hadoop@slave1:/home/hadoop$ chmod 755 -R ./hdfs
```

Hadoop ȯ�溯���� �����մϴ�.
```
hadoop@master:/home/hadoop$ echo $JAVA_HOME
/home/hadoop/tools/jdk (���� �⺻ path�� �Ǿ��ִ� jdk�Դϴ�. �����صμ���.)
hadoop@master:/home/hadoop$ cd tools/hadoop/conf
```
  hadoop-env.sh ȯ�漳��
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ vi hadoop-env.sh
# The java implementation to use.  Required.
export JAVA_HOME= /home/hadoop/tools/jdk
(JAVA_HOME�� �ּ��� �������ְ� �ڽ��� JDK ��ġ ��η� �ٲ��ش�.)

export HADOOP_HOME=/home/hadoop/tools/hadoop
export HADOOP_HOME_WARN_SUPPRESS=��TRUE��
(�� 2���� �߰����ش�. �Ȱ��� �Է��ϸ� �ȴ�.) 

export HADOOP_OPTS=-server
(HADOOP_OPTS �� �ּ��� �������ش�.)
```
  core-site.xml ȯ�漳��(�ٸ��� �״�� �νð� configuration�ȿ��� �������ݴϴ�.)
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ vi core-site.xml
<configuration>
        <property>
                <name>fs.default.name</name>
                <value>hdfs://master:9000</value>
        </property>
</configuration>
```
  hdfs-site.xml ȯ�漳��(�ٸ��� �״�� �νð� configuration�ȿ��� �������ݴϴ�.)
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ vi hdfs-site.xml
<configuration>
        <property>
                <name>dfs.name.dir</name>
                <value>/home/hadoop/hdfs/name</value>
        </property>

        <property>
                <name>dfs.data.dir</name>
                <value>/home/hadoop/hdfs/data</value>
        </property>

        <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property>
</configuration>

```
  mapred-site.xml ȯ�漳��(�ٸ��� �״�� �νð� configuration�ȿ��� �������ݴϴ�.)
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ vi mapred-site.xml
<property>
 <name>mapred.job.tracker</name>
 <value>master:9001</value>
</property>

```
  masters ȯ�漳��
```
localhost�� ����� master�� ����
```
  slaves ȯ�漳��(�ٸ��� �״�� �νð� configuration�ȿ��� �������ݴϴ�.)
```
localhost�� ����� 
slave1
slave2
�� ����
```
conf ������ ������ ���ϸ� slave1,2�� �����ݴϴ�.
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ rsync -av /home/hadoop/tools/hadoop/conf slave1:/home/hadoop/tools/hadoop
sending incremental file list
conf/
conf/core-site.xml
conf/hadoop-env.sh
conf/hdfs-site.xml
conf/mapred-site.xml
conf/masters
conf/slaves

sent 2,602 bytes  received 188 bytes  1,860.00 bytes/sec
total size is 34,456  speedup is 12.35
```
master node�� ���� namenode�� �������ݴϴ�.
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ cd /home/hadoop/tools/hadoop/bin
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop namenode -format

15/01/26 12:21:58 INFO namenode.NameNode: STARTUP_MSG:
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = master/192.168.200.2
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 1.2.1
STARTUP_MSG:   build = https://svn.apache.org/repos/asf/hadoop/common/branches/branch-1.2 -r 1503152; compiled by 'mattf' on Mon Jul 22 15:23:09 PDT 2013
STARTUP_MSG:   java = 1.7.0_65
************************************************************/
Re-format filesystem in /home/hadoop/hdfs/name ? (Y or N) Y(�ݵ�� Y�빮��!!!!)
15/01/26 12:22:06 INFO util.GSet: Computing capacity for map BlocksMap
15/01/26 12:22:06 INFO util.GSet: VM type       = 64-bit
15/01/26 12:22:06 INFO util.GSet: 2.0% max memory = 1013645312
15/01/26 12:22:06 INFO util.GSet: capacity      = 2^21 = 2097152 entries
15/01/26 12:22:06 INFO util.GSet: recommended=2097152, actual=2097152
15/01/26 12:22:06 INFO namenode.FSNamesystem: fsOwner=hadoop
15/01/26 12:22:06 INFO namenode.FSNamesystem: supergroup=supergroup
15/01/26 12:22:06 INFO namenode.FSNamesystem: isPermissionEnabled=true
15/01/26 12:22:06 INFO namenode.FSNamesystem: dfs.block.invalidate.limit=100
15/01/26 12:22:06 INFO namenode.FSNamesystem: isAccessTokenEnabled=false accessKeyUpdateInterval=0 min(s), accessTokenLifetime=0 min(s)
15/01/26 12:22:06 INFO namenode.FSEditLog: dfs.namenode.edits.toleration.length = 0
15/01/26 12:22:06 INFO namenode.NameNode: Caching file names occuring more than 10 times
15/01/26 12:22:06 INFO common.Storage: Image file /home/hadoop/hdfs/name/current/fsimage of size 112 bytes saved in 0 seconds.
15/01/26 12:22:07 INFO namenode.FSEditLog: closing edit log: position=4, editlog=/home/hadoop/hdfs/name/current/edits
15/01/26 12:22:07 INFO namenode.FSEditLog: close success: truncate to 4, editlog=/home/hadoop/hdfs/name/current/edits
15/01/26 12:22:07 INFO common.Storage: Storage directory /home/hadoop/hdfs/name has been successfully formatted.
15/01/26 12:22:07 INFO namenode.NameNode: SHUTDOWN_MSG:
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at master/192.168.200.2
************************************************************/
```

bin ���丮�� ���� start-all.sh ��ɾ�� master�� slave1,2�� �����մϴ�. 
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./start-all.sh
starting namenode, logging to /home/hadoop/tools/hadoop/logs/hadoop-hadoop-namenode-master.out
slave2: starting datanode, logging to /home/hadoop/tools/hadoop/logs/hadoop-hadoop-datanode-slave2.out
slave1: starting datanode, logging to /home/hadoop/tools/hadoop/logs/hadoop-hadoop-datanode-slave1.out
master: starting secondarynamenode, logging to /home/hadoop/tools/hadoop/logs/hadoop-hadoop-secondarynamenode-master.out
starting jobtracker, logging to /home/hadoop/tools/hadoop/logs/hadoop-hadoop-jobtracker-master.out
slave1: starting tasktracker, logging to /home/hadoop/tools/hadoop/logs/hadoop-hadoop-tasktracker-slave1.out
slave2: starting tasktracker, logging to /home/hadoop/tools/hadoop/logs/hadoop-hadoop-tasktracker-slave2.out
```
jps ��ɾ ����� master�� slave1,2 �� ����� �Ǿ����� Ȯ���մϴ�.
master�� ��� �̷��� 4���� �߸� �˴ϴ�.
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ jps
12882 SecondaryNameNode
12956 JobTracker
13070 Jps
12652 NameNode

```
slave1, slave2�� ��� �̷��� 3���� �߸� �˴ϴ�.
```
hadoop@slave2:/home$ jps
13337 DataNode
13463 TaskTracker
13529 Jps
```
Ȥ�� 4���� 3���� �ȶ��쿡�� (5.�ϵ��� �ٽ� �����մϴ�. )�� ���� hdfs������ rm-rf�ϴ°ͺ��� �ٽ� �����ϼ���.

==================================
###maven ��ġ�ϱ�(linux ����ӽſ��� ����)
####master������ �����ϼ���.
���̺� �ٿ�ε�
 wget http://mirrors.gigenet.com/apache/maven/maven-3/3.0.5/binaries/apache-maven-3.0.5-bin.tar.gz
 su root  (root �������� login�մϴ�.)
 Password : vagrant
 
 tar -zxvf apache-maven-3.0.5-bin.tar.gz -C /opt/ �� ������ opt ������ Ǯ���ݴϴ�.
 ln -s /opt/apache-maven-3.0.5 /opt/maven ���� ����Ʈ��ũ�� �ɾ��ݴϴ�.
 vi /etc/profile ���� ȯ�漳���� ���ݴϴ�.<br>
```
(/etc/profile ���� �ƹ����̳� �߰����ݴϴ�.)
  export M2_HOME=/opt/maven
  export PATH=$PATH:$M2_HOME/bin
```
 source /etc/profile ���� ȯ�溯���� ���ΰ�ħ���ݴϴ�.
 mvn -version�� ������� �� �̷��� ������ maven ��ġ�� �Ϸ�� ���Դϴ�.
```
root@master:/home/hadoop# mvn -version
Apache Maven 3.0.5 (r01de14724cdef164cd33c7c8c2fe155faf9602da; 2013-02-19 13:51:28+0000)
Maven home: /opt/maven
Java version: 1.7.0_65, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-7-openjdk-amd64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.13.0-44-generic", arch: "amd64", family: "unix"
```
=====================================
###Git���� ���� �ҷ�����(linux ����ӽſ��� ����)
####master������ �����ϼ���.
1. root���� apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \libz-dev libssl-dev �� �Է��մϴ�.
2. apt-get install git ���� ���� ��ġ�մϴ�. 
3. hadoop@master:/home/hadoop$ git clone https://github.com/gogodbrl/Homework.git <br>
   (���̰� root�� �����ϴϱ� �ݵ�� hadoop@master��������!!!) <br>
�ϸ� ���� ��ġ�Ǿ� �ִ� �̸� Homework�� �����ɴϴ�.

=======================================
###wordfrequency���ϱ�

1. hadoop@master:/home/hadoop/Homework/mfreq$ mvn package
```
��¼�� ��¼��,, 
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 13.640s
[INFO] Finished at: Mon Jan 26 16:53:34 UTC 2015
[INFO] Final Memory: 19M/59M
[INFO] ------------------------------------------------------------------------
```
2. hadoop@master:/home/hadoop$ cd /home/hadoop/tools/hadoop/bin
3. hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -put [shakespeare�� ������ Ǯ����� ���] shakespeare
```
���� ��쿡�� 
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -put /home/hadoop/Homework/mfreq/files/shakespeare shakespeare
�� �߽��ϴ�.
```
4. ./hadoop jar [/home/���� ���� mFreq-SNAPShot-0.0.1-jar-with-dependency.jar������ ���] 
```
���� ��쿡�� 
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -put /home/hadoop/Homework/mfreq/target/mfreq-0.0.1-SNAPSHOT-jar-with-dependencies.jar shakespeare
�� �߽��ϴ�.
```

5. Map�� reduce�� �ϼ��Ǹ� ./hadoop dfs -ls �� �Է����� �� 1-word-freq��� ���丮�� �ϼ��� ���� �� �� �ֽ��ϴ�.
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -ls
Found 2 items
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:56 /user/hadoop/1-word-freq
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:52 /user/hadoop/shakespeare
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -cat 1-word-freq/part-r-00000
�ϸ� �ܼ�â�� �� �� �ֽ��ϴ�.

======================================
###wordcount���ϱ�

1. hadoop@master:/home/hadoop/Homework/mcount$ mvn package
2. hadoop@master:/home/hadoop/Homework/mcount$ cd /home/hadoop/tools/hadoop/bin
(�Ʊ� hdfs�� put���� shakespeare�� �����Ƿ� �ٷ� jar���Ϸ� �Է��غ��ϴ�.)
3. ./hadoop jar [/home/���� ���� mFreq-SNAPShot-0.0.1-jar-with-dependency.jar������ ���]
(���� ����
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop jar /home/hadoop/Homework/mcount/target/mcount-0.0.1-SNAPSHOT-jar-with-dependencies.jar
�� �߽��ϴ�.)
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop jar /home/hadoop/Homework/mcount/target/mcount-0.0.1-SNAPSHOT-jar-with-dependencies.jar
15/01/26 17:05:41 INFO input.FileInputFormat: Total input paths to process : 1
15/01/26 17:05:41 INFO util.NativeCodeLoader: Loaded the native-hadoop library
15/01/26 17:05:41 WARN snappy.LoadSnappy: Snappy native library not loaded
15/01/26 17:05:42 INFO mapred.JobClient: Running job: job_201501261614_0002
15/01/26 17:05:43 INFO mapred.JobClient:  map 0% reduce 0%
15/01/26 17:05:53 INFO mapred.JobClient:  map 100% reduce 0%
15/01/26 17:06:01 INFO mapred.JobClient:  map 100% reduce 33%
15/01/26 17:06:05 INFO mapred.JobClient:  map 100% reduce 100%
15/01/26 17:06:06 INFO mapred.JobClient: Job complete: job_201501261614_0002
�̷������� �߸� �˴ϴ�.
```
4. Map�� reduce�� �ϼ��Ǹ� ./hadoop dfs -ls �� �Է����� �� 2-word-counts��� ���丮�� �ϼ��� ���� �� �� �ֽ��ϴ�.
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -ls
Found 3 items
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:56 /user/hadoop/1-word-freq
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 17:06 /user/hadoop/2-word-counts
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:52 /user/hadoop/shakespeare

```

=================================
###tf-idf�� ���ϱ�


1. hadoop@master:/home/hadoop/Homework/mtfidf$ mvn package
2. hadoop@master:/home/hadoop/Homework/mtfidf$ cd /home/hadoop/tools/hadoop/bin
3. ./hadoop jar [/home/���� ���� mFreq-SNAPShot-0.0.1-jar-with-dependency.jar������ ���]
```
���� ���
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop jar /home/hadoop/Homework/mtfidf/target/mtfidf-0.0.1-SNAPSHOT-jar-with-dependencies.jar
```
4. ./hadoop dfs -ls 3-tf-idf �ϸ� 3-tf-idf�� ���丮 ����� �� �� �ֽ��ϴ�.


===========================
###���
 ./hadoop dfs -cat 3-tf-idf/part-r-00000�ϸ� tf-idf ���� �� �� �ֽ��ϴ�.
 
