Jenkins��װ�����á�����Git���Maven��Ŀ

Centos6.7��װJenkins
һ������jenkins��yum�ֿ�Դ��������repo��ŵ�ָ��·��/etc/yum.repos.d/jenkins.repo��
 wget -O /etc/yum.repos.d/jenkins.repo http://jenkins-ci.org/redhat/jenkins.repo

�������뵽/etc/yum.repos.d·���ж�jenkins��YUM�ֿ�Դ����ȷ�ϣ�ȷ�������Ƿ�������

����������Կ�ļ�
rpm --import http://pkg.jenkins-ci.org/redhat/jenkins-ci.org.key

�ġ�ʹ��yum install jenkins������а�װ����װǰ��ȷ��ϵͳJDK�Ѱ�װ

�塢����Ĭ�ϰ�װ·�������ж˿�����
vi /etc/sysconfig/jenkins���޸�JENKINS_PORT=10080��Ĭ��8080��

��������Jenkins
service jenkins start

Ȼ�󣬱��� Starting Jenkins bash: /usr/bin/java: û���Ǹ��ļ���Ŀ¼

�ߡ�ִ�����echo $JAVA_HOME
�鿴jdk��װ·����/opt/migu/jdk

�ˡ�vi /etc/init.d/jenkins
�ҵ���δ���
candidates="
/etc/alternatives/java
/usr/lib/jvm/java-1.6.0/bin/java
/usr/lib/jvm/jre-1.6.0/bin/java
/usr/lib/jvm/java-1.7.0/bin/java
/usr/lib/jvm/jre-1.7.0/bin/java
/usr/lib/jvm/java-1.8.0/bin/java
/usr/lib/jvm/jre-1.8.0/bin/java
/usr/bin/java
��������δ���������
/opt/migu/jdk/bin/java

�š�����
service jenkins start

ʮ����������ʣ�http://10.5.2.242:11080


Jenkins����Git���Maven��Ŀ
һ��Jenkins���ð�װ��֮�󣬰�װ��ز��

����git����

Դ�밲װ

��⵱ǰgit�汾�Ƿ���2.7.4����

git --version

���û�а�װgitֱ��Դ�밲װ���ɣ������װ����ɾ��ԭ����git��

yum -y remove git

�Ȱ�װ����git��Ҫ�İ���

yum install zlib-devel perl-CPAN gettext curl-devel expat-devel gettext-devel openssl-devel
����&��װ

mkdir /tmp/git && cd /tmp/git
curl --progress https://www.kernel.org/pub/software/scm/git/git-2.9.0.tar.gz | tar xz
cd git-2.9.0
./configure
make
make prefix=/usr/local install


�鿴git��װ��ʲô�ط�

which git

���Կ���git��װ������Ŀ¼

/usr/local/bin/git

����

��Jenkins->Global Tool Configuration������git��

Path to Git executable����дgit�İ�װ·��


����java����

�ڷ�������ִ��echo $JAVA_HOME��ɿ���java home��


�ġ�maven����

��װ ����

wget http://mirrors.hust.edu.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
��ѹ

tar zxvf apache-maven-3.3.9-bin.tar.gz
���Ƶ���װĿ¼

mv ./apache-maven-3.3.9 /opt/soft/
���û�������

vim /etc/profile
maven��������

export MAVEN_HOME=/opt/soft/apache-maven-3.3.9
export PATH=$PATH:$JAVA_HOME/bin:$MAVEN_HOME/bin
ʹ��������������Ч

source /etc/profile
��֤�Ƿ����óɹ�

mvn -v
����
 
�塢email����

�������䣬�ڹ���ʧ�ܵ�ʱ�����ָ�����䷢�͸�֪�ʼ���
 
������������
 
 
 
 
Poll SCM����ʱ���Դ����������SCM����İ汾�ţ�������и��¾�checkout����code������Ȼ��ִ�й����������ҵ��������£�

*/5 * * * *  ��ÿ5���Ӽ��һ��Դ��仯��
Build periodically�����ڽ�����Ŀ������������careԴ���Ƿ����仯�����������£�

0 2 * * *  ��ÿ��2:00 ����buildһ��Դ�룩

 

������Ϳ��Խ��й����ˣ���߲˵��и����̹�����ť���������Թ�����
��һ�ι������е�����maven��ȥ�ºܶ�����jar����

�ߡ��鿴������־
Started by an SCM change
Building in workspace /var/lib/jenkins/workspace/deerTest
 > /usr/local/bin/git rev-parse --is-inside-work-tree # timeout=10
Fetching changes from the remote Git repository
��������������������
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

�ˡ��鿴�������
ll /var/lib/jenkins/.m2/repository/com/zhuowang/springboot8/0.0.1-SNAPSHOT/springboot8-0.0.1-SNAPSHOT.war

�š���������
Jenkins��װDeploy to container Plugin�����ֱ�Ӱѹ������war�����в���
