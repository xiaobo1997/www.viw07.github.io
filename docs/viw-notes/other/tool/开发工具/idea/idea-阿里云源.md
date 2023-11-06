
# 1.


```xml

<settings xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0  
                               http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <localRepository>/Users/jbkj/Documents/mavan-localRepository</localRepository>
	<servers>
        <server>
            <id>公司的-releases</id>
            <username>xxx</username>
            <password>xxxx</password>
        </server>
        <server>
            <id>公司的-snapshots</id>
            <username>xxx</username>
            <password>xxx</password>
        </server>
        <server>
            <id>公司的-dev</id>
            <username>xxx</username>
            <password>xxx</password>
        </server>
    </servers>

    <mirrors>
        <mirror>
            <id>jdb-dev</id>
            <mirrorOf>central</mirrorOf>
            <name>JDB Maven Repository</name>
            <url>公司的</url>
        </mirror>

        <mirror>
            <id>alimaven</id>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
 
        <mirror>
            <id>uk</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://uk.maven.org/maven2/</url>
        </mirror>
 
        <mirror>
            <id>CN</id>
            <name>OSChina Central</name>
            <url>http://maven.oschina.net/content/groups/public/</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
 
        <mirror>
            <id>nexus</id>
            <name>internal nexus repository</name>
            <url>http://repo.maven.apache.org/maven2</url>
            <mirrorOf>central</mirrorOf>
        </mirror>
    </mirrors>

    <profiles>
         <profile>
          <id>jdb-dev</id>
          <repositories>
              <repository>
                  <id>jdb-dev</id>
                  <name>JDB Maven Repository</name>
                  <url>公司的</url>
                  <layout>default</layout>
              </repository>
          </repositories>
          <activation>
              <activeByDefault>true</activeByDefault>
          </activation>
        </profile>
        <profile>  
    <id>downloadSources</id>  
    <properties>  
        <downloadSources>true</downloadSources>  
        <downloadJavadocs>true</downloadJavadocs>             
    </properties>  
</profile>
    </profiles>
<activeProfiles>  
  <activeProfile>downloadSources</activeProfile>  
</activeProfiles> 
</settings>



```