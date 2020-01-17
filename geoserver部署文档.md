GeoServer可以用来预览发布HBase中的数据。
Geoserver服务器根路径: 192.168.1.172   /opt/software/geoserver-2.13.2.
外网访问路径： 223.223.200.50：9400/Geoserver
用户名: admin  密码:geoserver

1.部署准备
---
项目依赖

- JAVA JDK8
- Hadoop HDFS
- HBase
- GeoMesa-HBase

配置好GeoMesa-HBase后，然后开始部署Geoserver.


2.项目打包
---
可以从Geoserver官网下载最新发布版本，目前服务器上部署版本为GeoServer2.13.2。


3.项目配置
---
为了能够让Geoserver支持HBase数据加载预览，需要导入一下jar包。根据官网提示，导入部分java jar文件后，可能会遇到类未定义异常或类找不到异常等等。其原因皆为缺少jar文件依赖。需要将依赖导入到 "/opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib"目录中。

另外，经过测试发现，HBase1.2.6的版本jar 包与高版本Geoserver 不匹配，所以对于HBase的jar包依赖，使用了HBase1.3.1版本。
```
#导入hadoop依赖
cd /opt/software/hadoop-3.0.0/share/hadoop
cp hdfs/lib/hadoop-annotations-3.0.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp hdfs/lib/hadoop-auth-3.0.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp common/hadoop-common-3.0.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp mapreduce/hadoop-mapreduce-client-core-3.0.0.jar  /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp yarn/hadoop-yarn-api-3.0.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp yarn/hadoop-yarn-common-3.0.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp yarn/lib/htrace-core-3.1.0-incubating.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp common/lib/commons-cli-1.2.jar  /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp common/lib/commons-io-2.4.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

cp hdfs/lib/netty-3.10.5.Final.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp hdfs/lib/netty-all-4.0.23.Final.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

cp hdfs/hadoop-hdfs-3.0.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp hdfs/hadoop-hdfs-client-3.0.0.jar  /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib


#导入HBase依赖
cd /opt/source/hbase-1.3.1/lib
cp hbase-common-1.3.1.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp hbase-client-1.3.1.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp hbase-server-1.3.1.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp hbase-protocol-1.3.1.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp metrics-core-2.2.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib
cp zookeeper-3.4.6.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib 
cp commons-configuration-1.6.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

#导入Geoserver-HBase 的Geoserver插件
cd /opt/software/geomesa-hbase_2.11-2.0.2/dist/gs-plugins
tar -xvf geomesa-hbase-gs-plugin_2.11-2.0.2-install.tar.gz
mv geomesa-hbase-gs-plugin_2.11-2.0.2-shaded.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

#链接HBase配置文件
ln -s /opt/software/hbase-1.2.6/conf/hbase-site.xml /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/classes/hbase-site.xml


#上面的jar包导入完后，直接运行Geoserver，能够看到数据源中已经显示了HBase，但是导入HBase中的数据会出现异常，需要添加以下的jar文件。这些jar文件可以从系统中进行搜索出来。没有的话，需要到官网下载。

cp /opt/source/woodstox-core-asl-4.4.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

cp /opt/software/hadoop-3.0.0/share/hadoop/hdfs/lib/stax2-api-3.1.4.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

cp /opt/software/hadoop-3.0.0/share/hadoop/hdfs/lib/commons-configuration2-2.1.1.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

cp /opt/software/hadoop-3.0.0/share/hadoop/hdfs/lib/protobuf-java-2.5.0.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

cp /opt/software/hadoop-3.0.0/share/hadoop/hdfs/lib/htrace-core4-4.1.0-incubating.jar /opt/software/geoserver-2.13.2/webapps/geoserver/WEB-INF/lib

```

3.服务路径
---
项目部署在192.168.1.172上，根目/opt/software/geoserver-2.13.2


4.服务启动
---
进入Geoserver根目录后，启动执行脚本，为了能够后台启动，可以在screen中执行命令。
./bin/startup.sh

修改根目录下的start.ini文件，可以修改项目监听端口。

5.其它策略
---
无

