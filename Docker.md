# Steps to create Docker image for Spark with Python binding

**1. Download Spark Binary**

The command to download Spark binary.
```shell
wget https://archive.apache.org/dist/spark/spark-3.2.3/spark-3.2.3-bin-hadoop3.2.tgz
```
```shell
tar -xzf spark-3.2.3-bin-hadoop3.2.tgz
```

**2. Build Command**

The command to build the image will look like this:
```bash
./bin/docker-image-tool.sh -r <your_repository> -t v3.2.3 -p kubernetes/dockerfiles/spark/bindings/python/Dockerfile build
```
```shell
./bin/docker-image-tool.sh -r <your_repository> -t v3.2.3 -p kubernetes/dockerfiles/spark/bindings/python/Dockerfile push
```

# Steps to create Docker image for Livy

**1. Build Livy code**
Build Livy code using following command.

```shell
cd incubator-livy
mvn package
```

Copy generated Livy binary into `/tmp` directory.

```shell
ls -l $SRC_DIR/assembly/target/apache-livy-0.9.0-incubating-SNAPSHOT_2.12-bin.zip
```

**2. Create Docker file**

Create Dockerfile at `/tmp`

```Dockerfile
FROM <your_repository>/spark-py:v3.2.3
ENV LIVY_VERSION            0.9.0-incubating-SNAPSHOT
ENV LIVY_PACKAGE            apache-livy-${LIVY_VERSION}_2.12-bin
ENV LIVY_HOME               /opt/livy
ENV LIVY_CONF_DIR           /conf
ENV PATH                    $PATH:$LIVY_HOME/bin

USER root

COPY $LIVY_PACKAGE.zip /
RUN apt-get update && apt-get install -y unzip && \
unzip /$LIVY_PACKAGE.zip -d / && \
mv /$LIVY_PACKAGE /opt/ && \
rm -rf $LIVY_HOME && \
ln -s /opt/$LIVY_PACKAGE $LIVY_HOME && \
rm -f /$LIVY_PACKAGE.zip

RUN mkdir /var/log/livy  && \
ln -s /var/log/livy $LIVY_HOME/logs

WORKDIR $LIVY_HOME

ENTRYPOINT [ "livy-server" ]
```

**3. Build docker image**

Use following command to build create docker image.

```shell
cd /tmp
docker build -t <your_repository>/livy:spark3.2.3 .
docker push <your_repository>/livy:spark3.2.3
rm -rfv apache-livy-0.9.0-incubating-SNAPSHOT_2.12-bin.zip
```
