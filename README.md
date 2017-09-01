# Migration Process into Salesforce 
#### Inchcape ####

This batch process recovers data from CRM systems where are based flat files with determined format. 
A specific format could facilitate and provide quick integration with this batch process. 

### Connect with SFTP 

### Connect with Anypoint MQ 

### How it works ### 

This application accepts CSV files or flat files which contains account information, then validates minimal data format to finally uploads the message to Anypoint MQ. 

The [Secure File Connector](https://docs.mulesoft.com/mule-user-guide/v/3.8/sftp-connector) polls the input folder for new files on specific dates. When it spots a new file, it reads it and mark as processed to pass the content to the [Dataweave Transformer](https://docs.mulesoft.com/mule-user-guide/v/3.8/dataweave). This one will automatically maps the input file records to output files for Anypoint MQ. When all the records in the file has been converted, a collection of object will be used to send to the topics in [Anypoint MQ](https://docs.mulesoft.com/anypoint-mq/).

### Documentation ###

This batch could be extended. 

### Go further ###

- Inchcape [About](https://www.inchcape.co.uk/about-us/) 