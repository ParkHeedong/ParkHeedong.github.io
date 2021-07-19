# Hadoop-2.10.1 설치

## CentOS 7



### 1. root로 로그인(Linux)

### 2. 기존 Java package 삭제

`yum remove javapackages-tools-3.4.1-11.el7.noarch`

`yum remove python-javapackages-3.4.1-11.el7.noarch`

`yum remove tzdata-java-2018e-3.el7.noarch`



### 3. Java 설치

`yum install java-1.8.0-openjdk-devel.x86_64`



### 4. 압축 해제

### 5. JDK 환경설정(.bashrc 안에 JAVA_HOME 및 PATH 설정)

`vi .bashrc`

`source ~/.bashrc` //변경된 환경설정 바로 적용

### 6. Hadoop 설치

`[root@localhost ~]# wget https://archive.apache.org/dist/hadoop/common/hadoop-2.10.1/hadoop-2.10.1/tar/gz`



*다운로드 후 /opt 아래에 풀어줌*

`tar -zxvf hadoop-2.10.1.tar.gz -C /opt`

*풀어준 파일을 hadoop 디렉토리 안으로 옮김*

`[root@localhost opt]# mv hadoop-2.10.1/ hadoop`

*vi 편집기로 hadoop-env.sh 수정 -> JAVA_HOME 설정*

`[root@localhost opt]# vi hadoop/etc/hadoop/hadoop-env.sh`

**export JAVA_HOME=$(readlink -f /usr/bin/javac)**



![image-20210622172044465](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622172044465.png)



### 7. hadoop 계정 생성

![image-20210622172147449](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622172147449.png)



### 8. 생성한 계정으로 ssh 통신용 키 생성

![image-20210622172421259](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622172421259.png)



*최초 키 인증*

![image-20210622172500309](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622172500309.png)



### 9. Hadoop 환경설정 (.bashrc 파일에 아래 내용 추가)

`vi .bashrc`

**하둡 설치 경로 : /opt/hadoop**

![image-20210622172706326](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622172706326.png)

`source ~/.bashrc`



### 10. Hadoop의 환경설정에서 JAVA_HOME 설정

`vi $HADOOP_HOME/etc/hadoop/hadoop-env.sh`

**export JAVA_HOME =/usr/bin/javac**



### 11. /opt/hadoop의 사용자, 그룹 권한를 hadoop으로 변경

`sudo chown -R hadoop: /opt/hadoop`

### 12. ~로 가서 namenode, datanode 디렉토리 생성

`[hadoop@localhost ~]$ sudo mkdir -p /opt/hadoop/hdfs/namenode`

`[hadoop@localhost ~]$ sudo mkdir -p /opt/hadoop/hdfs/datanode`

### 13.  설정파일 vi로 편집

`[hadoop@localhost ~]$ vi $HADOOP_HOME/etc/hadoop/core-site.xml`



![image-20210628095005812](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210628095005812.png)



`[hadoop@localhost ~]$ vi $HADOOP_HOME/etc/hadoop/hdfs-site.xml`



![image-20210628095033780](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210628095033780.png)



`[hadoop@localhost ~]$ vi $HADOOP_HOME/etc/hadoop/mapred-site.xml.template`



![image-20210628095115900](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210628095115900.png)



`[hadoop@localhost ~]$ vi $HADOOP_HOME/etc/hadoop/yarn-site.xml`



![image-20210628095156920](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210628095156920.png)





### 14. Name Node 포맷

`[hadoop@localhost ~]$ hdfs namenode -format`

### 15. Hadoop Cluster 시작

`[hadoop@localhost ~]$ $HADOOP_HOME/sbin/start-dfs.sh`



### 16. yarn 시작

`[hadoop@localhost ~]$ $HADOOP_HOME/sbin/start-yarn.sh`



### 17. jps 확인



![image-20210622173922291](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622173922291.png)

### 18. Hadoop 서비스 확인

*1) 네임노드 확인용 포털*

http://localhost:50070/

![image-20210622174117923](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622174117923.png)



*2)클러스터와 어플리케이션 확인용 포털*

http://localhost:8088/

![image-20210622174130569](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622174130569.png)



*3)데이터노드 확인용 포털*

http://localhost:50075/

![image-20210622174137652](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622174137652.png)



### 19. wordcount test

**맵리듀스 job을 실행하기 위해 HDFS 디렉토리 생성**

`hdfs dfs -mkdir /user `

`hdfs dfs -mkdir /user/root `

`hdfs dfs -mkdir /user/root/conf `

`hdfs dfs -mkdir /input `

`hdfs dfs -copyFromLocal $HADOOP_HOME/README.txt /input `

`hdfs dfs -ls /input`



**단어 개수 분석 프로그램 실행**

`hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.10.1.jar wordcount /input/README.txt ~/wordcount-output`



![image-20210622174417157](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622174417157.png)



**결과 확인**

`hdfs dfs -ls ~/wordcount-output`

`hdfs dfs -cat ~/wordcount-output/part-r-00000`



![image-20210622174636343](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622174636343.png)





![image-20210622174705181](C:\Users\User\AppData\Roaming\Typora\typora-user-images\image-20210622174705181.png)



**HDFS의 특정 폴더 및 디렉토리 삭제**

`hdfs dfs -rm -R ~/wordcount-output`

*-R : 특정 디텍로리 이하의 폴더 모두 제거*



### 20. Hadoop cluster 중지

`$HADOOP_HOME/sbin/stop-dfs.sh`

`$HADOOP_HOME/sbin/stop-yarn.sh`

