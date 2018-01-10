Jenkins安装、配置、构建Git搭建的Maven项目

Centos6.7安装Jenkins
一、配置jenkins的yum仓库源，将下载repo存放到指定路径/etc/yum.repos.d/jenkins.repo下
 wget -O /etc/yum.repos.d/jenkins.repo http://jenkins-ci.org/redhat/jenkins.repo

二、进入到/etc/yum.repos.d路径中对jenkins的YUM仓库源进行确认，确保里面是否有内容

三、导入秘钥文件
rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key

四、使用yum install jenkins命令进行安装，安装前请确认系统JDK已安装

五、进入默认安装路径，进行端口配置
vi /etc/sysconfig/jenkins，修改JENKINS_PORT=10080（默认8080）

六、启动Jenkins
service jenkins start

然后，报错 Starting Jenkins bash: /usr/bin/java: 没有那个文件或目录

七、执行命令：echo $JAVA_HOME
查看jdk安装路径：/opt/migu/jdk

八、vi /etc/init.d/jenkins
找到这段代码
candidates="
/etc/alternatives/java
/usr/lib/jvm/java-1.6.0/bin/java
/usr/lib/jvm/jre-1.6.0/bin/java
/usr/lib/jvm/java-1.7.0/bin/java
/usr/lib/jvm/jre-1.7.0/bin/java
/usr/lib/jvm/java-1.8.0/bin/java
/usr/lib/jvm/jre-1.8.0/bin/java
/usr/bin/java
我们在这段代码后面加上
/opt/migu/jdk/bin/java

九、启动
service jenkins start

十、浏览器访问：http://10.5.2.242:11080


Jenkins构建Git搭建的Maven项目
一、Jenkins配置安装好之后，安装相关插件

二、git配置

源码安装

检测当前git版本是否是2.7.4以上

git --version

如果没有安装git直接源码安装即可，如果安装了先删除原来的git。

yum -y remove git

先安装编译git需要的包。

yum install zlib-devel perl-CPAN gettext curl-devel expat-devel gettext-devel openssl-devel
下载&安装

mkdir /tmp/git && cd /tmp/git
curl --progress https://www.kernel.org/pub/software/scm/git/git-2.9.0.tar.gz | tar xz
cd git-2.9.0
./configure
make
make prefix=/usr/local install


查看git安装到什么地方

which git

可以看到git安装在如下目录

/usr/local/bin/git

配置

在Jenkins->Global Tool Configuration下配置git。

Path to Git executable：填写git的安装路径


三、java配置

在服务器上执行echo $JAVA_HOME便可看到java home。


四、maven配置

安装 下载

wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
解压

tar zxvf apache-maven-3.3.9-bin.tar.gz
复制到安装目录

mv ./apache-maven-3.3.9 /opt/soft/
配置环境变量

vim /etc/profile
maven环境变量

export MAVEN_HOME=/opt/soft/apache-maven-3.3.9
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin
使环境变量立刻生效

source /etc/profile
验证是否配置成功

mvn -v
配置
 
五、email配置

配置邮箱，在构建失败的时候会向指定邮箱发送告知邮件。
 
六、构建配置
 
 
 
 
Poll SCM：定时检查源码变更（根据SCM软件的版本号），如果有更新就checkout最新code下来，然后执行构建动作。我的配置如下：

*/5 * * * *  （每5分钟检查一次源码变化）
Build periodically：周期进行项目构建（它不管care源码是否发生变化），配置如下：

0 2 * * *  （每天2:00 必须build一次源码）

 

到这里就可以进行构建了，左边菜单有个立刻构建按钮，点击便可以构建。
第一次构建会有点慢，maven会去下很多插件和jar包。

七、查看构建日志
Started by an SCM change
Building in workspace /var/lib/jenkins/workspace/deerTest
 > /usr/local/bin/git rev-parse --is-inside-work-tree # timeout=10
Fetching changes from the remote Git repository
。。。。。。。。。。
[INFO] --- maven-install-plugin:2.5.2:install (default-install) @ springboot8 ---
[INFO] Installing /var/lib/jenkins/workspace/deerTest/target/springboot8-0.0.1-SNAPSHOT.war to /var/lib/jenkins/.m2/repository/com/zhuowang/springboot8/0.0.1-SNAPSHOT/springboot8-0.0.1-SNAPSHOT.war
[INFO] Installing /var/lib/jenkins/workspace/deerTest/pom.xml to /var/lib/jenkins/.m2/repository/com/zhuowang/springboot8/0.0.1-SNAPSHOT/springboot8-0.0.1-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 12.445 s
[INFO] Finished at: 2018-01-10T16:41:26+08:00
[INFO] Final Memory: 39M/306M
[INFO] ------------------------------------------------------------------------
Finished: SUCCESS

八、查看构建结果
ll /var/lib/jenkins/.m2/repository/com/zhuowang/springboot8/0.0.1-SNAPSHOT/springboot8-0.0.1-SNAPSHOT.war

九、后续操作
Jenkins安装Deploy to container Plugin插件，直接把构建后的war包进行部署
