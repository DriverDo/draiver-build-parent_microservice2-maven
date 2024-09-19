# Draiver Consul Support

## Getting Started
Adding [Consul](https://www.consul.io/) support to Draiver microservices is as easy as extending [ConsulAwareController](https://github.com/Draiversolutionsllc/car-utility-microservice-java/blob/master/src/main/java/com/Draiver/microservice/ConsulAwareControler.java) on your controller class that uses the @RestController attribute. 

With this base class you get Consul Key/Value support, service registry and a default /healthCheck endpoint. 

**Sample REST Controller**

```java
PUT SAMPLE CODE HERE
```

## bootstrap.yml
You need to make sure you have a file named **bootstrap.yml** in your **src/main/resources** folder. That file needs to have the following contents:

```yml
spring:
   application:
      name:  @consul.application.name@
```

The **@consul.application.name@** maven parameter is supplied by the [parent pom](https://github.com/Draiversolutionsllc/car-build-parent_microservice-maven/blob/master/pom.xml). This property tells Consul the microservice name to register inside the service registry. It also is the default path location for any key/value information. There is also another bootstrap.yml file located in [car-utility-microservice-java](https://github.com/Draiversolutionsllc/car-utility-microservice-java/blob/master/src/main/resources/config/bootstrap.yml) that defines the default location for Consul as well as default health check endpoint and interval checks. 

## Key/Value
All controllers that use the ConsulAwareController are registered in the Consul Key/Value path of [Draiver/microservices](https://consul.reconvelocity.com/ui/dc1/kv/Draiver/microservices/). By default you app will look for keys in two different locations:

* Draiver/microservices/\<your app name\>
* Draiver/microservices/shared

Your class must use the **@RefreshScope** and **@Configuration** annotations to tell spring-boot that the class intends to talk to Consul to fetch Key/Value pairs and that if a key is changed in Consul to update the microservice on-the-fly. 

**Sample Data Fetch Class**

```java
package com.Draiver.microservice;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.context.annotation.Configuration;

@RefreshScope
@Configuration
public class ConsulAwareControllerProperties {

	@Value("${awsSettings}")
	private String awsSettings;

	@Value("${awsKinesisStreamName}")
	private String awsKinesisStreamName;

	public String getAwsSettigns() {
		return this.awsSettings;
	}

	public String getAwsKinesisStreamName() {
		return this.awsKinesisStreamName;
	}

}
```
Assuming the above class was part of the car-microservice-audit microservice the code would look at the following paths for the above settings in the followign order. The first match will be returned. 

awsSettings
* /Draiver/microservices/car-microservice-audit/awsSettings
* /Draiver/microservices/shared/awsSettings

awsKinesisStreamName
* /Draiver/microservices/car-microservice-audit/awsKinesisStreamName
* /Draiver/microservices/shared/awsKinesisStreamName



## References
* [Spring Cloud Consul](https://spring.io/projects/spring-cloud-consul)