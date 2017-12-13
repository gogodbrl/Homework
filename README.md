TFIDF 파일을 사용하시면 됩니다.


### Hadoop으로 tf-idf결과값 구하기

tf-idf는 어떤 문서에서 나온 단어가 얼마나 필요한 단어인가를 알아내는 방법입니다.
어떤 단어가(term이) 특정 doc에 많이 나올수록 가중치가 높아지고, 다양한 doc에 나올수록 가중치는 0에 수렴하게 됩니다.
가중치를 구하는 방식은 tf * log(N/n) 입니다. 여기서 N은 전체 doc의 갯수, n은 단어가 나온 doc의 갯수입니다.
(즉 어떤 단어가 전체 doc에 다나오면 N=n이므로 log1=0이기 때문에 가중치는 0이되는데, 중요하지 않은 단어일 가능성이 높다는 의미입니다.)

=====================================
### Vagrant 설치하기(windows에서 실행)

1. oracle Virtual Box가 없다면 설치해주세요.(https://www.virtualbox.org/wiki/Downloads)
2. vagrant window 버전을 설치합니다. (https://www.vagrantup.com/downloads.html) - Mac일 경우에는 mac용을..
3. 설치 후 cmd 창을 열고 <br>
C:\\> md Project<br>
C:\\> cd Project<br>
C:\\Project> vagrant box add ubuntu/trusty64 (후에 vagrant box list 하면 현재 설치된 box를 볼 수 있습니다.)<br>
```
C:\Project>vagrant box add ubuntu/trusty64
==> box: Loading metadata for box 'ubuntu/trusty64'
    box: URL: https://atlas.hashicorp.com/ubuntu/trusty64
==> box: Adding box 'ubuntu/trusty64' (v14.04) for provider: virtualbox
어쩌고 저쩌고....
```
<br>
C:\\Project> vagrant init ubuntu/trusty64 (Vagrantfile이라는 환경파일이 만들어집니다.)
<br>

```
C:\Project>vagrant init ubuntu/trusty64
A 'Vagrantfile' has been placed in this directory. You are now
ready to 'vagrant up' your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
'vagrantup.com' for more information on using Vagrant.
```
<br>
### 환경파일 설정하기
Vargrantfile 을 다음과 같이 설정합니다.

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

setup.sh 을 다음과 같이 만듭니다.<br>
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

 C:\\Project> vagrant up을 해줍니다. 
 (Vagrantfile과 setup.sh파일이 있는 경로에서 해주세요. 오래걸립니다.)
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
    이렇게 쭉쭉쭉 약 30분정도 소요. 메모리는 넉넉한게 짱...
```
* 후에 VM을 실행시키면 Master 1대와 Slave 2대가 실행중임을 알 수 있습니다. 


* superputty와 putty를 깔고 superputty를 실행시켜 3개를 띄워줍니다.<br>
  (Host : 127.0.0.1:2222 Login:vagrant Password:vagrant)<br>
  (Host : 127.0.0.1:2200 Login:vagrant Password:vagrant)<br>
  (Host : 127.0.0.1:2201 Login:vagrant Password:vagrant)<br>
 2222포트는 master포트이고 2200부터 1씩 증가하는 것은 slave포트입니다. <br>
2200포트로 접속했을 때 에러가나면 2201 2202 식으로 포트를 늘리면서 접속해보십시오.<br>

==================================
### Hadoop Setting 하기(linux 가상머신에서 실행)
유저 비밀번호를 설정합니다.(Master와 slave1, slave2 모두 바꾸어 줍니다.) <br>
```
  vagrant@master:~$ su
  Password: vagrant

  root@master:/home/vagrant# passwd hadoop
  Enter new UNIX password: hadoop
  Retype new UNIX password: hadoop
  passwd: password updated successfully 
```

master, slave1,slave2 의 호스트 파일을 수정합니다.
```
  root@master:/home/vagrant# vi /etc/hosts
  #127.0.0.1 master master
  root@slave1:/home/vagrant# vi /etc/hosts
  #127.0.0.1 slave1 slave1
  root@slave2:/home/vagrant# vi /etc/hosts
  #127.0.0.1 slave2 slave2
```
ssh 접근제어 설정합니다.(master에서만 실행하세요!) enter키만 치시면 됩니다.
```
root@master:/home/vagrant# su hadoop
hadoop@master:/home/vagrant$ cd
hadoop@master:/home/hadoop$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/hadoop//.ssh/id_rsa): [enter]
Created directory '/home/hadoop//.ssh'.
Enter passphrase (empty for no passphrase): [enter]
Enter same passphrase again: [enter]
Your identification has been saved in /home/hadoop//.ssh/id_rsa.
Your public key has been saved in /home/hadoop//.ssh/id_rsa.pub.
The key fingerprint is: 
9c:3e:e5:1b:85:85:ad:63:18:59:f5:35:da:b9:3e:0d hadoop@master
The key's randomart image is:
+--[ RSA 2048]----+
|          ...  ..|
|         o o .o.o|
|        o . o..o |
|       . + +    .|
|        S * . E. |
|       . + o  ...|
|        o o    o.|
|         . o    .|
|          .      |
+-----------------+
```
ssh 인증키를 배포합니다.(master에서만 실행하세요.)
slave1, slave2에게 아래와 같이 해줍니다.
```
(자기 자신의 공개키를 authorized_keys에 저장해줍니다.)
hadoop@master:/home/hadoop$ cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

(ssh-copy-id 명령어로 slave1, slave2에게 공개키를 배포한다.)

hadoop@master:/home/hadoop$ ssh-copy-id -i ~/.ssh/id_rsa.pub hadoop@slave1(slave2에게는 hadoop@slave2로 해줍니다.)                                             
The authenticity of host 'slave1 (192.168.200.10)' can't be established.
ECDSA key fingerprint is e7:1b:73:bb:4d:00:8e:ce:26:53:67:12:c6:26:4f:cb.
Are you sure you want to continue connecting (yes/no)? yes (yes적어주시면 됩니다.)
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
hadoop@slave1's password: hadoop

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'hadoop@slave1'"
and check to make sure that only the key(s) you wanted were added.



hadoop@master:/home/hadoop$ ssh slave1 했을 때 
hadoop@slave1:~$ 암호요구 없이 이렇게 되면 정상입니다.

hadoop@slave1:~$exit 로 나옵니다.

```

Hadoop을 다시 수정합니다.

master의 경우
```
hadoop@master:/home/hadoop$ cd
hadoop@master:/home/hadoop$ rm -rf ~/hdfs  <기존에 생성된 ~/hdfs폴더를 삭제한다.>
hadoop@master:/home/hadoop$ mkdir -p ./hdfs/name  <namenode의 저장소 > 
hadoop@master:/home/hadoop$ chmod 755 -R ./hdfs   < 폴더의 권한을 755로 설정해준다. >
```

slave1, slave2의 경우 
```
root일 경우
root@slave1:~# su hadoop
hadoop@slave1:/root$ cd

hadoop@slave1:/home/hadoop$ rm -rf ./hdfs
hadoop@slave1:/home/hadoop$ mkdir -p ./hdfs/data
hadoop@slave1:/home/hadoop$ chmod 755 -R ./hdfs
```

Hadoop 환경변수를 세팅합니다.(이제부터 master에서만 사용합니다.)
```
hadoop@master:/home/hadoop$ echo $JAVA_HOME
/home/hadoop/tools/jdk (현재 기본 path로 되어있는 jdk입니다. 복사해두세요.)
hadoop@master:/home/hadoop$ cd tools/hadoop/conf
```
  hadoop-env.sh 환경설정
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ vi hadoop-env.sh
# The java implementation to use.  Required.
export JAVA_HOME= /home/hadoop/tools/jdk (JAVA_HOME의 주석을 제거해주고 자신의 JDK 설치 경로로 바꿔준다.)
export HADOOP_HOME=/home/hadoop/tools/hadoop
export HADOOP_HOME_WARN_SUPPRESS=“TRUE”
(위 2줄을 추가해준다. 똑같이 입력하면 된다.) 
export HADOOP_OPTS=-server
(HADOOP_OPTS 의 주석을 해제해준다.)
```
  core-site.xml 환경설정(다른건 그대로 두시고 configuration안에만 수정해줍니다.)
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ vi core-site.xml
<configuration>
        <property>
                <name>fs.default.name</name>
                <value>hdfs://master:9000</value>
        </property>
</configuration>
```
  hdfs-site.xml 환경설정(다른건 그대로 두시고 configuration안에만 수정해줍니다.)
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
  mapred-site.xml 환경설정(다른건 그대로 두시고 configuration안에만 수정해줍니다.)
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ vi mapred-site.xml
<configuration>
    <property>
        <name>mapred.job.tracker</name>
        <value>master:9001</value>
    </property>
</configuration>


```
  masters 환경설정
```
localhost를 지우고 master로 변경
```
  slaves 환경설정(다른건 그대로 두시고 configuration안에만 수정해줍니다.)
```
localhost를 지우고 
slave1
slave2
로 변경
```
conf 파일중 수정된 파일만 slave1, slave2로 보내줍니다.
```
hadoop@master:/home/hadoop/tools/hadoop/conf$ rsync -av /home/hadoop/tools/hadoop/conf slave1:/home/hadoop/tools/hadoop
(여기서 slave1을 slave2로 바꾸셔서 한번 더 실행하시면 됩니다.)
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
master node로 가서 namenode를 포맷해줍니다.
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
Re-format filesystem in /home/hadoop/hdfs/name ? (Y or N) Y(반드시 Y대문자!!!!)
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

bin 디렉토리로 들어가서 start-all.sh 명령어로 master와 slave1,2를 실행합니다. 
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
jps 명령어를 사용해 master와 slave1,2 에 제대로 되었는지 확인합니다.
master의 경우 이렇게 4개가 뜨면 됩니다.
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ jps
12882 SecondaryNameNode
12956 JobTracker
13070 Jps
12652 NameNode

```
slave1, slave2의 경우 이렇게 3개가 뜨면 됩니다.
```
hadoop@slave2:/home$ jps
13337 DataNode
13463 TaskTracker
13529 Jps
```
혹시 4개와 3개가 안뜰경우에는 ./stop-all.sh을 실행시키시고 <br>
(하둡을 다시 수정합니다. )로 가서 hdfs폴더를 rm-rf하시고 namenode를 다시 포맷하신 후에 ./start-all.sh로 다시 시작하세요.

==================================
###maven 설치하기(linux 가상머신에서 실행)
####master에서만 실행하세요.
메이븐 다운로드 <br>

hadoop@master:/home/hadoop$ su root (password :vagrant) <br>
root@master:/home/hadoop# cd <br>
root@master:~# vi /etc/sudoers <br>

```
sudo ALL=(ALL:ALL) ALL 
아래
hadoop  ALL=(ALL:ALL) ALL 
을 추가해줍니다. (hadoop에게 sudo 권한을 줍니다.)
```
root@master:~# su hadoop <br>
hadoop@master:/root$ cd <br>
hadoop@master:/home/hadoop$ sudo apt-get install maven <br>
hadoop@master:/home/hadoop$ wget http://mirrors.gigenet.com/apache/maven/maven-3/3.0.5/binaries/apache-maven-3.0.5-bin.tar.gz<br>
hadoop@master:/home/hadoop$ tar -zxvf apache-maven-3.0.5-bin.tar.gz -C ./ <br>
hadoop@master:/home/hadoop$ ln -s apache-maven-3.0.5 ./maven <br>
hadoop@master:/home/hadoop$ vi .bash_profile 

```
(.bash_profile 내에 추가해줍니다.)
  export M2_HOME=/home/hadoop/maven
  export PATH=$PATH:$M2_HOME/bin
```

hadoop@master:/home/hadoop$ source .bash_profile (환경변수 새로고침) <br>
mvn -version을 사용했을 때 이렇게 나오면 maven 설치가 완료된 것입니다.
```
hadoop@master:/home/hadoop$ mvn -v
Apache Maven 3.0.5 (r01de14724cdef164cd33c7c8c2fe155faf9602da; 2013-02-19 13:51:28+0000)
Maven home: /home/hadoop/maven
Java version: 1.7.0_65, vendor: Oracle Corporation
Java home: /usr/lib/jvm/java-7-openjdk-amd64/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "3.13.0-44-generic", arch: "amd64", family: "unix"
```

=====================================
###Git으로 파일 불러오기(linux 가상머신에서 실행)
####master에서만 실행하세요.

1. hadoop@master:/home/hadoop$ sudo apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \libz-dev libssl-dev
2. hadoop@master:/home/hadoop$ sudo apt-get install git
3. hadoop@master:/home/hadoop$ git clone https://github.com/gogodbrl/Homework.git <br>
   (↑이거 혹시 root면 삽질하니까 반드시 hadoop@master계정으로!!!) <br>

=======================================
###wordfrequency구하기

hadoop@master:/home/hadoop$ cd Homework/TFIDF <br>
hadoop@master:/home/hadoop/Homework/TFIDF$ mvn package <br>
```
어쩌고 저쩌고,, 
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 13.640s
[INFO] Finished at: Mon Jan 26 16:53:34 UTC 2015
[INFO] Final Memory: 19M/59M
[INFO] ------------------------------------------------------------------------
```
 hadoop@master:/home/hadoop$ cd /home/hadoop/tools/hadoop/bin <br>
 hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -put [shakespeare의 압축을 풀어놓은 경로] shakespeare <br>
```
저의 경우에는 
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -put /home/hadoop/Homework/data/shakespeare shakespeare
로 했습니다.
```
 ./hadoop jar [/home/에서 부터 tfidf-SNAPShot-0.0.1-jar-with-dependency.jar까지의 경로] [mainclass경로(패키지명.클래스명)] 
```
저의 경우에는 
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop jar /home/hadoop/Homework/TFIDF/target/tfidf-0.0.1-SNAPSHOT-jar-with-dependencies.jar tfidf.MyFrequency
로 했습니다.
```

 Map과 reduce가 완성되면 ./hadoop dfs -ls 를 입력했을 때 1-word-freq라는 디렉토리가 완성된 것을 볼 수 있습니다.
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -ls
Found 2 items
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:56 /user/hadoop/1-word-freq
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:52 /user/hadoop/shakespeare
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -cat 1-word-freq/part-r-00000
하면 콘솔창에 찍어볼 수 있습니다.

======================================
###documentcount구하기

 ./hadoop jar [/home/에서 부터 tfidf-SNAPShot-0.0.1-jar-with-dependency.jar까지의 경로] [mainClass 경로]
```
(저의 경우는
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop jar /home/hadoop/Homework/TFIDF/target/tfidf-0.0.1-SNAPSHOT-jar-with-dependencies.jar tfidf.MyCounts
로 했습니다.)
15/01/26 17:05:41 INFO input.FileInputFormat: Total input paths to process : 1
15/01/26 17:05:41 INFO util.NativeCodeLoader: Loaded the native-hadoop library
15/01/26 17:05:41 WARN snappy.LoadSnappy: Snappy native library not loaded
15/01/26 17:05:42 INFO mapred.JobClient: Running job: job_201501261614_0002
15/01/26 17:05:43 INFO mapred.JobClient:  map 0% reduce 0%
15/01/26 17:05:53 INFO mapred.JobClient:  map 100% reduce 0%
15/01/26 17:06:01 INFO mapred.JobClient:  map 100% reduce 33%
15/01/26 17:06:05 INFO mapred.JobClient:  map 100% reduce 100%
15/01/26 17:06:06 INFO mapred.JobClient: Job complete: job_201501261614_0002
이런식으로 뜨면 됩니다.

```
 Map과 reduce가 완성되면 ./hadoop dfs -ls 를 입력했을 때 2-word-counts라는 디렉토리가 완성된 것을 볼 수 있습니다.
```
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop dfs -ls
Found 3 items
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:56 /user/hadoop/1-word-freq
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 17:06 /user/hadoop/2-word-counts
drwxr-xr-x   - hadoop supergroup          0 2015-01-26 16:52 /user/hadoop/shakespeare
```

=================================
###tf-idf값 구하기

 ./hadoop jar [/home/에서 부터 tfidf-SNAPShot-0.0.1-jar-with-dependency.jar까지의 경로]
```
저의 경우
hadoop@master:/home/hadoop/tools/hadoop/bin$ ./hadoop jar /home/hadoop/Homework/TFIDF/target/tfidf-0.0.1-SNAPSHOT-jar-with-dependencies.jar tfidf.MyTfIdf
```
 ./hadoop dfs -ls 3-tf-idf 하면 3-tf-idf의 디렉토리 목록을 볼 수 있습니다. <br>
 ./hadoop dfs -cat 3-tf-idf/part-r-00000하면 tf-idf 값을 볼 수 있습니다.
```
zealous@comedies        0.0
zealous@histories       0.0
zealous@poems   0.0
zeals@tragedies 1.6094379124341003
zed@tragedies   1.6094379124341003
zenelophon@comedies     1.6094379124341003
zenith@comedies 1.6094379124341003
zephyrs@tragedies       1.6094379124341003
zir@tragedies   3.2188758248682006
zo@tragedies    1.6094379124341003
zodiac@tragedies        1.6094379124341003
zodiacs@comedies        1.6094379124341003
zone@tragedies  1.6094379124341003
zounds@histories        10.39720770839918
zounds@tragedies        4.1588830833596715
zwaggered@tragedies     1.6094379124341003

이런식으로 나옵니다.
```
