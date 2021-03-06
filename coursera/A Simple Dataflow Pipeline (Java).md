# A Simple Dataflow Pipeline (Java)
- Coursera: Data Engineering on Google Cloud Platform
- Levels: advanced
- Link: https://googlecoursera.qwiklabs.com/catalog_lab/1368
- https://github.com/GoogleCloudPlatform/training-data-analyst


## Summary
- 자바 소스에서 import 만 필터링하여 bucket에 저장하는 예제

## mvn archetype:generate
~~~bash
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
cd ~/training-data-analyst/courses/data_analysis/lab2
mvn archetype:generate \
  -DarchetypeArtifactId=google-cloud-dataflow-java-archetypes-starter \
  -DarchetypeGroupId=com.google.cloud.dataflow \
  -DgroupId=com.example.pipelinesrus.newidea \
  -DartifactId=newidea \
  -Dversion="[1.0.0,2.0.0]" \
  -DinteractiveMode=false
~~~

## mvn compile -e exec:java
~~~
cd ~/training-data-analyst/courses/data_analysis/lab2

export PATH=/usr/lib/jvm/java-8-openjdk-amd64/bin/:$PATH
cd ~/training-data-analyst/courses/data_analysis/lab2/javahelp
mvn compile -e exec:java \
 -Dexec.mainClass=com.google.cloud.training.dataanalyst.javahelp.Grep
~~~

## Upload to Bucket
~~~
gsutil cp ../javahelp/src/main/java/com/google/cloud/training/dataanalyst/javahelp/*.java gs://$BUCKET/javahelp
~~~

## Run
- run_oncloud.sh
~~~bash
#!/bin/bash

if [ "$#" -ne 3 ]; then
   echo "Usage:   ./run_oncloud.sh project-name  bucket-name  mainclass-basename"
   echo "Example: ./run_oncloud.sh cloud-training-demos  cloud-training-demos  JavaProjectsThatNeedHelp"
   exit
fi

PROJECT=$1
BUCKET=$2
MAIN=com.google.cloud.training.dataanalyst.javahelp.$3

echo "project=$PROJECT  bucket=$BUCKET  main=$MAIN"

export PATH=/usr/lib/jvm/java-8-openjdk-amd64/bin/:$PATH
mvn compile -e exec:java \
 -Dexec.mainClass=$MAIN \
      -Dexec.args="--project=$PROJECT \
      --stagingLocation=gs://$BUCKET/staging/ \
      --tempLocation=gs://$BUCKET/staging/ \
      --input=gs://$BUCKET/javahelp/ \
      --outputPrefix=gs://$BUCKET/javahelp/output \
      --runner=DataflowRunner"
~~~