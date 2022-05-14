# How to build JDK8,Eclipse and Tomcat
## OS
Ubuntu 20......I forget
## JDK8
> Decompress
>>1. http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html jdk-8u321-linux-x64.tar.gz
>>2. sudo mkdir /usr/lib/jvm 
>>3. ...~/Downloads$ sudo mv jdk-8u121-linux-x64.tar.gz /usr/lib/jvm
>>4. cd /usr/lib/jvm 
>>5. sudo tar zxvf jdk-8u121-linux-x64.tar.gz -C /usr/lib/jvm

> Configuring JDK's environment variables
>>6. sudo vi /etc/profile
>>7. Add the following statement to the end of the file:
>>>     export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_321
>>>     export JRE_HOME=${JAVA_HOME}/jre
>>>     export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
>>>     export PATH=＄{JAVA_HOME}/bin:$PATH
>>8. source /etc/profile ---> java -version 

## Eclipse
> Decompress
>>1. http://www.eclipse.org/downloads/packages/eclipse-ide-java-developers/heliossr1/ ---> choose "Installer" ---> eclipse-inst-jre-linux64.tar.gz
>>2. ...~/Downloads$ sudo mv eclipse-inst-jre-linux64.tar.gz /opt
>>3. cd /opt
>>4. sudo tar zxvf eclipse-inst-jre-linux64.tar.gz -C /opt/

> Install IDE
>>5. open /opt/eclipse-installer ---> open eclipse-inst
>>6. choose "Eclipse IDE for Enterprise Java Developers"
>>7. install 
>>8. open it on ***/home/luo/eclipse/jee-2022-03/eclipse/eclipse*** or AppMenu

## Tomcat 9
> Decompress
>>1. https://tomcat.apache.org/ ---> choose Tomcat 9 ---> choose tar.gz
>>2. sudo mkdir /usr/local/tomcat
>>3. ...~/Downloads$ sudo cp /apache-tomcat-9.0.62.tar.gz /usr/local/tomcat
>>4. cd /usr/local/tomcat
>>5. sudo tar zxvf apache-tomcat-9.0.62.tar.gz

> Configuration
>>6. entitle:sudo chmod 755 -R apache-tomcat-9.0.62
>>7. cd apache-tomcat-9.0.62/bin
>>8. sudo gedit startup.sh
>>9. Add the following statement before the last line:
>>>     #set java environment
>>>     export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_321
>>>     export JRE_HOME=${JAVA_HOME}/jre
>>>     export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
>>>     export PATH=${JAVA_HOME}/bin:$PATH
>>>
>>>     #tomcat
>>>     export TOMCAT=/usr/local/tomcat/apache-tomcat-9.0.62
>>10. sudo gedit shutdown.sh
>>11. Add the following statement before the last line:
>>>     #set java environment
>>>     export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_321
>>>     export JRE_HOME=${JAVA_HOME}/jre
>>>     export CLASSPATH=.:%{JAVA_HOME}/lib:%{JRE_HOME}/lib
>>>     export PATH=${JAVA_HOME}/bin:$PATH
>>>
>>>     #tomcat
>>>     export TOMCAT=/usr/local/tomcat/apache-tomcat-9.0.62

> open/close Tomcat
>> sudo /usr/local/tomcat/apache-tomcat-9.0.62/bin/startup.sh
>> sudo /usr/local/tomcat/apache-tomcat-9.0.62/bin/shutdown.sh
>> open "localhost:8080" on your browser can check its status 
