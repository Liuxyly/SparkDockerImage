FROM java:openjdk-8-jdk

ENV hadoop_ver 2.7.7
ENV hadoop_sub_ver 2.7
ENV spark_ver 2.3.1

# Get Hadoop from US Apache mirror and extract just the native
# libs. (Until we care about running HDFS with these containers, this
# is all we need.)
RUN mkdir -p /opt && \
    cd /opt && \
    curl http://www.us.apache.org/dist/hadoop/common/hadoop-${hadoop_ver}/hadoop-${hadoop_ver}.tar.gz | \
        tar -zx hadoop-${hadoop_ver}/lib/native && \
    ln -s hadoop-${hadoop_ver} hadoop && \
    echo Hadoop ${hadoop_ver} native libraries installed in /opt/hadoop/lib/native

# Get Spark from US Apache mirror.
RUN mkdir -p /opt && \
    cd /opt && \
    echo http://www.us.apache.org/dist/spark/spark-${spark_ver}/spark-${spark_ver}-bin-hadoop${hadoop_sub_ver}}.tgz \
    curl http://www.us.apache.org/dist/spark/spark-${spark_ver}/spark-${spark_ver}-bin-hadoop${hadoop_sub_ver}}.tgz | \
        tar -zx && \
    ln -s spark-${spark_ver}-bin-hadoop${hadoop_sub_ver}} spark && \
    echo Spark ${spark_ver} installed in /opt

# Add the GCS connector.
# RUN cd /opt/spark/lib && \
#   curl -O https://storage.googleapis.com/hadoop-lib/gcs/gcs-connector-latest-hadoop2.jar
ADD gcs-connector-latest-hadoop2.jar /opt/spark/lib

# if numpy is installed on a driver it needs to be installed on all
# workers, so install it everywhere
RUN apt-get update && \
    apt-get install -y python-numpy && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ADD log4j.properties /opt/spark/conf/log4j.properties
ADD start-common.sh start-worker start-master /
ADD core-site.xml /opt/spark/conf/core-site.xml
ADD spark-defaults.conf /opt/spark/conf/spark-defaults.conf
ENV PATH $PATH:/opt/spark/bin
