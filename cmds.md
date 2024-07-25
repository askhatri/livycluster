export WORKING_DIR=/Users/asif.khatri/tmp/dir1

rm -rfv $WORKING_DIR
mkdir -p $WORKING_DIR
cd $WORKING_DIR || exit

wget https://archive.apache.org/dist/spark/spark-3.2.3/spark-3.2.3-bin-hadoop3.2.tgz

tar -xzf spark-3.2.3-bin-hadoop3.2.tgz



export WORKING_DIR=/Users/asif.khatri/tmp/dir1
export SPARK_HOME=$WORKING_DIR/spark-3.2.3-bin-hadoop3.2

cd $SPARK_HOME || exit

./bin/docker-image-tool.sh -r asifkhatri -t v3.2.3 -p kubernetes/dockerfiles/spark/bindings/python/Dockerfile build

./bin/docker-image-tool.sh -r asifkhatri -t v3.2.3 -p kubernetes/dockerfiles/spark/bindings/python/Dockerfile push


export SRC_DIR=/Users/asif.khatri/Workspace/jira/71211/incubator-livy
export MAVEN_OPTS=-XX:ReservedCodeCacheSize=2048M
export JAVA_HOME=/usr/local/Cellar/openjdk@8/1.8.0-412/libexec/openjdk.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH

cd $SRC_DIR || exit
mvn -e -Pthriftserver -Pscala-2.12 -Pspark3 -pl -:livy-python-api clean install -DskipITs -DskipTests -Dmaven.wagon.http.pool=false -Dhttp.keepAlive=false -Drat.numUnapprovedLicenses=100

ls -l $SRC_DIR/assembly/target/apache-livy-0.9.0-incubating-SNAPSHOT_2.12-bin.zip


export SRC_DIR=/Users/asif.khatri/Workspace/jira/71211/incubator-livy
export WORKING_DIR=/Users/asif.khatri/Workspace/jira/livyk8s

cp -rfv $SRC_DIR/assembly/target/apache-livy-0.9.0-incubating-SNAPSHOT_2.12-bin.zip $WORKING_DIR

cd $WORKING_DIR || exit

docker build -t asifkhatri/livy:spark3.2.3 .

docker push asifkhatri/livy:spark3.2.3

rm -rfv apache-livy-0.9.0-incubating-SNAPSHOT_2.12-bin.zip


