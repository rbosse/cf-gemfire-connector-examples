# cf-gemfire-connector-examples

Example applications that use the GemFire on Pivotal Cloud Foundry service.

For an application to connect to a provisioned GemFire cluster in Cloud Foundry, it has to create a GemFire ClientCache which connects to the GemFire locators. The application also has to provide an implementation of AuthInitialize which will send the client credentials to the server. The credentials and the locator information is present in the VCAP_SERVICES environment variable. This repository has three examples of how these environment variables can be parsed.

There are 3 example applications in this repository:
- Java main application
- Java main application using spring cloud connectors
- Spring boot application using spring cloud connectors

Following are the common steps for all examples to create a GemFire Service and create a Region:
- create a GemFire service instance named "MyService"
  `cf create-service p-gemfire GemFireServicePlan1 MyService`
- install the gemfire plugin for the `cf` command
- create a region named "test"
  - connect `gfsh` to the service instance
    `cf show-gfsh MyService` will print the command to run in gfsh to connect to the cluster
  - `gfsh>create region --name=test --type=PARTITION` will create a region named "test"

## Java main application

application name: java-app
This application uses jackson to parse the environment variable in the EnvParser class. ClientAuthInitialize is an implementation of AuthInitialize that is used for handling client authenticatin. The main class, MyJavaApplication creates a ClientCache that uses the ClientAuthInitialize for authentication and uses EnvParser to get the locator information.

To deploy the application:
- deploy the application to cloud foundry using the command
  `cf push -f app.yml`

## Java main application using spring cloud connector

application name: java-app-spring-cloud
To use the spring connector, your application does *not* have to be spring application. Just specify spring-cloud-core and spring-cloud-gemfire-cloudfoundry-connector as your project dependencies and let the connector parse the VCAP_SERVICES environment variable for you. In the application you can get ServiceInfo like so:

```
    CloudFactory cloudFactory = new CloudFactory();
    Cloud cloud = cloudFactory.getCloud();
    GemfireServiceInfo myService = (GemfireServiceInfo) cloud.getServiceInfo("MyService");
```
You will still need to provide an implementation of AuthInitialize like in the previous example. Please refer to the application.

## Spring Application


