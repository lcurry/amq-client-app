# Fuse AMQP Client Application

A Spring Boot application written with Red Hat Fuse components that acts as an AMQP message producer and consumer.


## Pre-requisites 

You will need the following installed on your local development environment. 

1. Java JDK 1.8 or higher 
2. Maven 3.x 
    https://maven.apache.org/install.html


You will need to have 'javac' compiler and 'mvn' command line (build) tool on your PATH.


## Configure for you environment 

Edit the configuration file 
```
./src/main/resources/application.properties
```
Edit the following properties in configuration:

amqp.producer.uri
amqp.consumer.uri

```
# amqp producer properties
amqp.producer.enabled=true
amqp.producer.uri=amqp://iof-amq-0-svc.iof.svc.cluster.local:61617
# Comment above line and uncoment below 
# This URL will come from the corresponding Openshift route. To view in console got to 
# Networking->Routes Select the URL under "Location" for the route with "\<your namespace\>-amq-0-svc-rte
# amqp.producer.uri=amqps://iof-amq-0-svc-rte-iof.apps.cluster-gj7vv.gj7vv.sandbox1370.opentlc.com:443?transport.verifyHost=false

amqp.producer.user=admin
amqp.producer.password=admin
amqp.producer.address=simple.amqp.test

# amqp consumer properties
amqp.consumer.enabled=true
amqp.consumer.uri=amqp://iof-amq-0-svc.iof.svc.cluster.local:61617
# Comment above line and uncoment below 
# This URL will come from the corresponding Openshift route. To view in console got to 
# Networking->Routes Select the URL under "Location" for the route with "\<your namespace\>-amq-0-svc-rte
# amqp.consumer.uri=amqps://iof-amq-0-svc-rte-iof.apps.cluster-gj7vv.gj7vv.sandbox1370.opentlc.com:443?transport.verifyHost=false


```


## Building the application

If your settings file is not set to pull package dependencies fro the Red Hat middleware (Fuse)
artifact repos, you can use the included 'settins.xml' that is included in this repo. 

First backup existing Maven settings file if it exists: 
```
copy C:\user\<userID>\.m2\settings.xml C:\user\<userID>\.m2\settings.xml.bak
```
Replace with the settings file that includes the Red Hat artifact repos:

```
copy configuration\settings.xml C:\user\<userID>\.m2\settings.xml 
```

To build the application run the following from the root of this project:

```
mvn clean install
```
If maven has troule downloading dependencies, you may need to instruct maven to ignore certificates:

```
mvn  -Dmaven.wagon.http.ssl.insecure=true clean install 
```

## Run the application 

To run the application you need a keystore containing the certificate of the broker
in a local trusted keystore file that can be used by the client. You can download the 'client.ts' certificate 
from a secret from the openshift namespace where AMQ Broker is deployed. 
To get the client trustore certificate, find the secret in the openshift
namespace where AMQ Broker is deployed.  The secret will be named:

${BROKER-NAME}-secret

You can also find  the value of the password for the client truststore in that secret.

This will allow you to run the following command, specifying the location of the trustore and the 
password of the trustore:


```
java  -Djavax.net.ssl.trustStore=C:\path to truststore\client.ts -Djavax.net.ssl.trustStorePassword=${TRUSTORE_PASSWORD} -jar target/fuse-amq-client-1.0-SNAPSHOT.jar
```

If still having issues, make sure the password specified in application.properties is correct. 
Also you can check the values in the client.ts with the following command:
```
keytool -list -v -keystore client.ts  
```
When prompted for the password use the password for the client truststore in the secret ${BROKER-NAME}-secret.
This will at least tell you if the truststore and password to truststore is correct.  
