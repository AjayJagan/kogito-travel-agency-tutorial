# Kogito Visas


NOTE: This requires Kogito 0.3.0 that is not yet released and that's why it refers to snapshot versions
for both Kogito and Quarkus

## Description

This is another service of the Kogito Travel Agency application. It's responsible for processing visa applications
that are sent during travel requests. 

In it's current state it simply accepts incoming visa applications from Kafka topic and process them

* automatically if the rules match the visa application details
* manually otherwise

Response is logged into the visa application and the processing ends. Further evolutions of the service
will add additional hanlding to the approved or rejected visa applications.

## Activities to perform

* Create project using Quarkus Maven plugin with following extensions
	* Kogito
	* OpenApi
* Import project into Eclipse IDE - requires BPMN modeller plugin installed
* Create data model	
	* VisaApplication
* Create decision logic
	* Visa approvals
* Create business logic
	* Public business process to deal with visa applications
* Configure messaging and events
* Create or import UI components
* Add custom metrics based on the approved and rejected visa applications
* Create dashboard based on metrics

## Data model

Kogito Travel Agency booking system will be based on following data model

**Visa Application**

A basic visa application information required to process


## Decision logic

The decision logic will be implemented as a set of rules in DRL format. These rules will be responsible for automatic visa application approval


## Business logic

Business logic will be based on business processes

Public process that will be responsible for processing visa application

<p align="center"><img width=75% height=50% src="docs/images/visa-applications-process.png"></p>


# Try out the complete service

## Installing and Running

### Prerequisites

You will need:
  - Java 1.8.0+ installed
  - Environment variable JAVA_HOME set accordingly
  - Maven 3.5.4+ installed

When using native image compilation, you will also need:
  - GraalVM installed
  - Environment variable GRAALVM_HOME set accordingly
  - Note that GraalVM native image compilation typically requires other packages (glibc-devel, zlib-devel and gcc) to be installed too, please refer to GraalVM installation documentation for more details.

### Infrastructure requirements

#### Infinispan

This application requires an Inifinispan server to be available and by default expects it to be on default port and localhost.

You can install Inifinispan server by downloading it from [official website](https://infinispan.org/download) version to be used in 10.0.0.Beta4


#### Apache Kafka

This application requires a Apache Kafka installed and following topics created

* `visaapplications` - used to send visa application that are consumed and processed by Kogito Visas service
* `kogito-processinstances-events` - used to emit events by kogito that can be consumed by data index service and other services


### Compile and Run in Local Dev Mode

```
mvn clean package quarkus:dev    
```

NOTE: With dev mode of Quarkus you can take advantage of hot reload for business assets like processes, rules and decision
tables and java code. No need to redeploy or restart your running application.During this workshop we will create a software system for a startup travel agency called Kogito Travel Agency. The first iteration of the system will consist of a set of services that are able to deal with travel requests and the booking of hotels and flights.


### Compile and Run using Local Native Image
Note that this requires GRAALVM_HOME to point to a valid GraalVM installation

```
mvn clean package -Pnative
```

To run the generated native executable, generated in `target/`, execute

```
./target/kogito-visas-{version}-runner
```

## Known issues


## User interface

Kogito Visas comes with basic UI that allows to


### list currently opened visa applications

<p align="center"><img width=75% height=75% src="docs/images/list-visas.png"></p>


### approve/reject selected visa application

<p align="center"><img width=75% height=75% src="docs/images/approve-reject-visas.png"></p>

To start Kogito Travel Agency UI just point your browser to [http://localhost:8090](http://localhost:8090)

## REST API

Once the service is up and running, you can use the following examples to interact with the service.


### GET /visaApplications

Returns list of travel requests currently active:

```sh
curl -X GET http://localhost:8090/visaApplications
```

As response an array of travels is returned.

### GET /visaApplications/{id}

Returns travel request with given id (if active):

```sh
curl -X GET http://localhost:8090/visaApplications/{uuid}
```

As response a single travel request is returned if found, otherwise no content (204) is returned.

### DELETE /visaApplications/{id}

Cancels travel request with given id

```sh
curl -X DELETE http://localhost:8090/visaApplications/{uuid}
```

### GET /visaApplications/{id}/tasks

Returns currently assigned user tasks for give travel request:

```sh
curl -X GET http://localhost:8090/visaApplications/{uuid}/tasks
```

### GET /visaApplications/{id}/ApplicationApproval/{taskId}

Returns visa application task information:

```sh
curl -X GET http://localhost:8090/visaApplications/{uuid}/ApplicationApproval/{task-uuid}
```

### POST /visaApplications/{id}/ApplicationApproval/{taskId}

Completes visa application task

```sh
curl -H "Content-Type: application/json" -H "Accept: application/json" -X POST http://localhost:8090/visaApplications/435aa429-5ddb-4480-9219-ad9c51bbf885/ApplicationApproval/98796868-d94e-4363-8e8f-6a1d31cbbff6 -d @- << EOF
{
	"application" : {
		"firstName" : "Jan",
		"lastName" : "Kowalski",
		"nationality" : "Polish",
		"city" : "New York",
		"country" : "US",
		"passportNumber" : "ABC09876",
		"duration" : 25,
		"approved" : true
	}
}
EOF
```
