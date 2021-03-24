

# Quick reference

-	**Where to get help**:
  * [ODM Documentation](https://www.ibm.com/support/knowledgecenter/en/SSQP76_8.10.x/com.ibm.odm.kube/topics/con_k8s_licensing_metering.html)
  * [ODM Developer Center community](https://developer.ibm.com/odm/)

-	**Where to file issues**:  
  https://github.com/ODMDev/decisions-metering/issues

-	**Maintained by**:  IBM ODM Team.

-	**Supported architectures**:  
 `amd64`
-	**Source of this description**:
        https://github.com/ODMDev/decisions-metering/tree/master/docker

-	**Supported Docker versions**:  
	[latest release](https://github.com/docker/docker-ce/releases/latest) (up to version 19, on a best-effort basis)


# Overview

The Operational Decision Manager usage metering service image produces license files that are compliant with the [IBM License Metric Tool](https://www.ibm.com/support/knowledgecenter/SS8JFY_9.2.0/com.ibm.lmt.doc/welcome/LMT_welcome.html) inventories IBM software. It's based on the observed usage of Operational Decision Manager software.

See the license section below for restrictions on the use of this image. This image is used 

  # Usage

The image contains a server that is preconfigured with a database accessible through HTTP port 9080 and HTTPS port 9443.
You must accept the license before you launch the image. The license is available at the bottom of this page.

```console
docker run -e LICENSE=accept  -p 8888:8888 -p 9999:9999 ibmcom/odm-metering-service:8.10-amd64
```

To avoid losing the container data when you delete the Docker image container, store the databases outside of the ODM Metering Docker image container, in a local mounted host volume (-v $PWD/DB:/config/storage/DB). You can also modify the default metering properties by providing your own mybootstrap.properties file (-v $PWD/mybootstrap.properties:/config/bootstrap.properties). The default bootstrap.properties is containing the following properties :

```console
# The log level that is used by the application. Possible values include ERROR, WARN, INFO, DEBUG, and TRACE.
METERING_LOGGINGLEVEL=INFO
# The rate in milliseconds at which usage is processed and written to the license files.
METERING_PROCESSINGRATE=60000
# The delay in milliseconds before the first processing occurs after the service is started.
METERING_PROCESSING_INITIAL_DELAY=6000
```

You can also store the ILMT files by providing a volume (-v $PWD/ILMT:/config/storage/ILMT).

The metering service is provided with an HTTPS secured protocol.
The default certificate is compliant with the ODM Docker images https://github.com/ODMDev/odm-ondocker
If you want to provide your own certificate, you can set 2 volumes for server.crt certificate (-v $PWD/mycompany.crt:/config/resources/certificate/server.crt) and server.key private key  (-v $PWD/mycompany.key:/config/resources/certificate/server.key) files.
For example :

 ```console
openssl req -x509 -nodes -days 1000 -newkey rsa:2048 -keyout mycompany.key -out mycompany.crt -subj "/CN=*.mycompany.com/OU=it/O=mycompany/L=Paris/C=FR"
```


To do so, run the following docker command from an empty local folder:

 ```console
docker run -e LICENSE=accept  -p 8888:8888 -p 9999:9999 -v $PWD/DB:/config/storage/DB -v $PWD/ILMT:/config/storage/ILMT -v $PWD/mybootstrap.properties:/config/bootstrap.properties -v $PWD/mycompany.crt:/config/resources/certificate/server.crt -v $PWD/mycompany.key:/config/resources/certificate/server.key ibmcom/odm-metering-service:8.10-amd64
```

When you first run this command, it creates the metering files in your local folder. The following times, it reads and updates these files.

When the server is started, use the URL http://localhost:8888 or https://localhost:9999 to display a welcome page.


  # License

  The Docker files and associated scripts are licensed under [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0.html).
