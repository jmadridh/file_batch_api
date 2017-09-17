# Global Batch Application 

Developed for Inchcape PLC.


## Description

This Batch API provides a mechanism to migrate data into Salesforce Cloud from a hierarchy folder SFTP server. 

## Table of Content 

- [Pre-requirements](#Pre-requirements)
- [Endpoints](#Endpoints)
- [Connectors and Processors](#Connectors and Processors)
- [Limitations](#Limitations)
- [Go Further](#Go Further)

### Pre-requirements
---

There are a number of pre-requirements to run this batch api. 
By group;
> * File
> * System API
> * Queueing Mechanism


In the first group of pre-requirements, it involves:
1. The file **MUST** be stored previously in a folder SFTP.
2. The file **FOLLOW** a pattern name with a *CSV* extension.
3. The file **HAS** 'read/write' permissions.
4. The file **HAS** a file structure human readable.

In the second group of pre-requirements, it is related to the bridge system which serves to operate functions to push data into Salesforce Cloud. 
1. The System API **SHOULD** be running and listening on the HTTP port.
2. The System API **MUST** performs the operation required to push data in a secured channel communication.
3. The System API **SHOULD** track each transaction in a logging system.

In the final set of pre-requirements, those are enclosed to the queue mechanism: 
1. To persist records from the file, the batch api *WILL* publish them to an Exchange in Anypoint MQ Service configured to Inchcape.
2. The Queue *SHOULD HAVE* a TTL in order to let the retry mechanism to process all those records in case of unexpected errors. 
3. The Exchange and Queue *SHOULD HAVE* a pattern name that correlate with the batch interface. 
4. The group of exchange and queue *SHOULD HAVE* previously configured.


### Endpoints

#### Settings

`http://globalbatchapi.cloudhub.io/batch/setting`

Description

Endpoint to set parameter configurations.

*HTTP Method*

`POST`

##### Body
```
{
	"object": "account",
	"country": "Greece", 
	"business": "distribution",
	"schedule": {
		"poll": {
			"cron" : {
				"seconds": "0/15",
				"minutes": "*",
				"hours": "*",
				"dayOfMonth": "?",
				"month": "*",
				"dayOfWeek": "*",
				"year": "*"
			},
			"timestandard": "UTC"
		}
	},
	"reconnectionStrategyTargetSystem": {
		"frequencyMilliseconds": 60000,
		"attempts": 5
	},
	"isParent": true,
	"parent": "parent"
}
```

##### Response
```
{
    "server": "LUK-HK43PF2.corp.capgemini.com",
    "batchInterface": "Greecedistributionaccount",
    "dirs": {
        "input": "//input/Greece/distribution/account",
        "output": "//output/Greece/distribution/account",
        "error": "//error/Greece/distribution/account",
        "archive": "//archive/Greece/distribution/account"
    },
    "executionTime": "0/15 * * ? * * *",
    "timezone": "UTC",
    "targetSystemAPI": "account_soap_sys_api",
    "timeSetting": "08-09-2017 22:32:06"
}
```



*Reporting*

`http://globalbatchapi.cloudhub.io/batch/report`

Endpoint to retrieve latest execution of batch interface.

*HTTP Method*

`GET`

#### Body
```
Under construction
```

#### Response
```
Under construction
```
### Batch Structure
---
**Load and Dispatch**

*Poll*

As a result from configuration parameters using resource **/batch/setting**

**Example**: 
```
	"schedule": {
		"poll": {
			"cron" : {
				"seconds": "0/15",
				"minutes": "*",
				"hours": "*",
				"dayOfMonth": "?",
				"month": "*",
				"dayOfWeek": "*",
				"year": "*"
			},
			"timestandard": "UTC"
		}
	}
```
**Result**:

`0/15 * * ? * * *`
means every 15 seconds through minutes, hours, month and years. **Note**: it is required parameter: `timestandard` of execution.

Follow the syntax [Free Formatter Cron Expression](https://www.freeformatter.com/cron-expression-generator-quartz.html) 

*Watermark*

Pattern to select the file is commanded by: 
> File CSV 
> File Age: 60000ms (**1min**)


**Batch Steps**

*Queueing and Target System API*

1. Route message to the proper exchange in Anypoint MQ according to the name of Batch Interface configured.
Tag the message ID with an random UUID. 
2. Route message to the Target System API according to the name of Batch Interface configured.
3. Retrieve message from the Queue respectively. One of the following action is possible: 
	HTTP Status response was successfully, then send ACK
	HTTP Status response is not successful, then send message to Error Queue

*Failures*

1. Route messages to Error Queue because message was malformed


**On Complete**

*Report*

Write on logs the result of execution

### Connectors and Processors  
---
The [Secure File Connector](https://docs.mulesoft.com/mule-user-guide/v/3.8/sftp-connector)

The [HTTPS Reference](https://docs.mulesoft.com/api-manager/https-reference)

The [Anypoint MQ](https://docs.mulesoft.com/anypoint-mq/)

The [Dataweave Transformer](https://docs.mulesoft.com/mule-user-guide/v/3.8/dataweave)

### Limitations 
---
Endpoint `batch/setting` can only set one batch interface at time.

### Go further ###

- Inchcape [About](https://www.inchcape.co.uk/about-us/) 