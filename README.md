# R on Spark

[![Build Status](https://travis-ci.org/amplab-extras/SparkR-pkg.png?branch=master)](https://travis-ci.org/amplab-extras/SparkR-pkg)

SparkR is an R package that provides a light-weight frontend to use Spark from
R.

## Installing SparkR

### Requirements
SparkR requires Scala 2.10 and Spark version >= 0.9.0. Note that as
Spark 0.9.0 has not yet been released the current build uses the latest release
candidate from the Apache staging repositories. You can also build SparkR against a
different Spark version (>= 0.9) by modifying `pkg/src/build.sbt`.

SparkR also requires the R package `rJava` to be installed. To install `rJava`,
you can run the following command in R:

    install.packages("rJava")


### Package installation
To develop SparkR, you can build the scala package and the R package using (also check if your environment varialbes are properly set - see below)

    ./install-dev.sh

Imnportant environment variables must be set:
JAVA_HOME must set to the JAVA SDK installed location (Note: not the JRE) 
SCALA_HOME must set to SCALA installed location 

If you see WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform ..
You may be on a 64bit platform and is using  a 32bit lib. see [3]
export LD_LIBRARY_PATH=/usr/lib/hadoop-0.20-mapreduce/lib/native/Linux-amd64-64

If you wish to try out the package directly from github, you can use `install_github` from `devtools`

    library(devtools)
    install_github("amplab-extras/SparkR-pkg", subdir="pkg")

SparkR by default links to Hadoop 1.0.4. To use SparkR with other Hadoop
versions, you will need to rebuild SparkR with the same version that [Spark is
linked
to](http://spark.incubator.apache.org/docs/latest/index.html#a-note-about-hadoop-versions). 
For example to use SparkR with a CDH 4.2.0 MR1 cluster, you can run

    SPARK_HADOOP_VERSION=2.0.0-mr1-cdh4.2.0 ./install-dev.sh

By default, SparkR uses [sbt](http://www.scala-sbt.org) to build an assembly
jar. If you wish to use [maven](http://maven.apache.org/) instead, you can set
the environment variable `USE_MAVEN=1`. For example

    USE_MAVEN=1 ./install-dev.sh
    
If you are building SparkR from behind a proxy, you can [setup maven](https://maven.apache.org/guides/mini/guide-proxies.html) to use the right proxy
server.


## Running sparkR
If you have cloned and built SparkR, you can start using it by launching the SparkR
shell with

    ./sparkR

If you have installed it directly from github, you can include the SparkR
package and then initialize a SparkContext. For example to run with a local
Spark master you can launch R and then run

    library(SparkR)
    sc <- sparkR.init(master="local")

To increase the memory used by the driver you can export the SPARK\_MEM
environment variable. For example to use 1g, you can run

    SPARK_MEM=1g ./sparkR

In a cluster settting to set the amount of memory used by the executors you can
pass the variable `spark.executor.memory` to the SparkContext constructor.

    library(SparkR)
    sc <- sparkR.init(master="spark://<master>:7077",
                      sparkEnvir=list(spark.executor.memory="1g"))

Note: 
In case you are seeing classNotfound problem: 
You may also consider adding the libs of Java and Scala to sparkR file like the following

  javaHome <- Sys.getenv("JAVA_HOME")
  scalaHome <- Sys.getenv("SCALA_HOME")
  .libPaths( c(paste(projecHome,"/lib", sep=""), 
	       paste(scalaHome,"/lib", sep=""),
           .libPaths()))

Reference to 
[1] (http://www.r-statistics.com/2012/08/how-to-load-the-rjava-package-after-the-error-java_home-cannot-be-determined-from-the-registry/)
[2] (https://sagebionetworks.jira.com/wiki/display/SYNR/Troubleshooting+Installation+Problems)
[3] (http://stackoverflow.com/questions/19943766/hadoop-unable-to-load-native-hadoop-library-for-your-platform-error-on-centos)
[4] (http://cran.r-project.org/web/packages/rJava/rJava.pdf)

## Examples, Unit tests

SparkR comes with several sample programs in the `examples` directory.
To run one of them, use `./sparkR <filename> <args>`. For example:

    ./sparkR examples/pi.R local[2]  

You can also run the unit-tests for SparkR by running

    ./run-tests.sh

## Running on EC2

Instructions for running SparkR on EC2 can be found in the
[SparkR wiki](https://github.com/amplab-extras/SparkR-pkg/wiki/SparkR-on-EC2).
