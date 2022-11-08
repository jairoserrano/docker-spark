[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/big-data-europe/Lobby)
[![Build Status](https://travis-ci.org/big-data-europe/docker-spark.svg?branch=master)](https://travis-ci.org/big-data-europe/docker-spark)
[![Twitter](https://img.shields.io/twitter/follow/BigData_Europe.svg?style=social)](https://twitter.com/BigData_Europe)
# Spark docker

Docker images to:
* Setup a standalone [Apache Spark](https://spark.apache.org/) cluster running one Spark Master and multiple Spark workers
* Build Spark applications in Java, Scala or Python to run on a Spark cluster

<details open>
<summary>Currently supported versions:</summary>

* Spark 3.3.1 for Hadoop 3.3 with OpenJDK 8 and Scala 2.12

</details>

```
git clone https://github.com/jairoserrano/jairoserrano-docker-spark.git docker-spark
cd docker-spark
sh build.sh
docker compose up
```


## Launch a Spark application
Building and running your Spark application on top of the Spark cluster is as simple as extending a template Docker image. Check the template's README for further documentation.
* [Maven template](template/maven)
* [Python template](template/python)
* [Sbt template](template/sbt)

## Kubernetes deployment
The BDE Spark images can also be used in a Kubernetes enviroment.

To deploy a simple Spark standalone cluster issue

`kubectl apply -f https://raw.githubusercontent.com/big-data-europe/docker-spark/master/k8s-spark-cluster.yaml`

This will setup a Spark standalone cluster with one master and a worker on every available node using the default namespace and resources. The master is reachable in the same namespace at `spark://spark-master:7077`.
It will also setup a headless service so spark clients can be reachable from the workers using hostname `spark-client`.

Then to use `spark-shell` issue

`kubectl run spark-base --rm -it --labels="app=spark-client" --image bde2020/spark-base:3.3.0-hadoop3.3 -- bash ./spark/bin/spark-shell --master spark://spark-master:7077 --conf spark.driver.host=spark-client`

To use `spark-submit` issue for example

`kubectl run spark-base --rm -it --labels="app=spark-client" --image bde2020/spark-base:3.3.0-hadoop3.3 -- bash ./spark/bin/spark-submit --class CLASS_TO_RUN --master spark://spark-master:7077 --deploy-mode client --conf spark.driver.host=spark-client URL_TO_YOUR_APP`

You can use your own image packed with Spark and your application but when deployed it must be reachable from the workers.
One way to achieve this is by creating a headless service for your pod and then use `--conf spark.driver.host=YOUR_HEADLESS_SERVICE` whenever you submit your application.
