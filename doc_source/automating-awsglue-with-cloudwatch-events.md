# Automating AWS Glue with CloudWatch Events<a name="automating-awsglue-with-cloudwatch-events"></a>

You can use Amazon CloudWatch Events to automate your AWS services and respond automatically to system events such as application availability issues or resource changes\. Events from AWS services are delivered to CloudWatch Events in near real time\. You can write simple rules to indicate which events are of interest to you, and what automated actions to take when an event matches a rule\. The actions that can be automatically triggered include the following:
+ Invoking an AWS Lambda function
+ Invoking Amazon EC2 Run Command
+ Relaying the event to Amazon Kinesis Data Streams
+ Activating an AWS Step Functions state machine
+ Notifying an Amazon SNS topic or an Amazon SQS queue

Some examples of using CloudWatch Events with AWS Glue include the following:
+ Activating a Lambda function when an ETL job succeeds
+ Notifying an Amazon SNS topic when an ETL job fails

The following CloudWatch Events are generated by AWS Glue\.
+ Events for `"detail-type":"Glue Job State Change"` are generated for `SUCCEEDED`, `FAILED`, `TIMEOUT`, and `STOPPED`\.
+ Events for `"detail-type":"Glue Job Run Status"` are generated for `RUNNING`, `STARTING`, and `STOPPING` job runs when they exceed the job delay notification threshold\.
+ Events for `"detail-type":"Glue Crawler State Change"` are generated for `Started`, `Succeeded`, and `Failed`\.
+ Events for `"detail-type":"Glue Data Catalog Database State Change"` are generated for `CreateDatabase`, `DeleteDatabase`, `CreateTable`, `DeleteTable` and `BatchDeleteTable`\. For example, if a table is created or deleted, a notification is sent to CloudWatch Events\. Note that you cannot write a program that depends on the order or existence of notification events, as they might be out of sequence or missing\. Events are emitted on a best effort basis\. In the details of the notification:
  + The `typeOfChange` contains the name of the API operation\.
  + The `databaseName` contains the name of the affected database\.
  + The `changedTables` contains up to 100 names of affected tables per notification\. When table names are long, multiple notifications might be created\.
+ Events for `"detail-type":"Glue Data Catalog Table State Change"` are generated for `UpdateTable`, `CreatePartition`, `BatchCreatePartition`, `DeletePartition` and `BatchDeletePartition`\. For example, if a table or partition is updated, a notification is sent to CloudWatch Events\. Note that you cannot write a program that depends on the order or existence of notification events, as they might be out of sequence or missing\. Events are emitted on a best effort basis\. In the details of the notification:
  + The `typeOfChange` contains the name of the API operation\.
  + The `databaseName` contains the name of the database that contains the affected resources\.
  + The `tableName` contains the name of the affected table\.
  + The `changedPartitions` specifies up to 100 affected partitions in one notification\. When partition names are long, multiple notifications might be created\.

    For example if there are two partition keys, `Year` and `Month`, then `"2018,01", "2018,02"` modifies the partition where `"Year=2018" and "Month=01"` and the partition where `"Year=2018" and "Month=02"`\.

    ```
    {
        "version":"0",
        "id":"abcdef00-1234-5678-9abc-def012345678",
        "detail-type":"Glue Data Catalog Table State Change",
        "source":"aws.glue",
        "account":"123456789012",
        "time":"2017-09-07T18:57:21Z",
        "region":"us-west-2",
        "resources":["arn:aws:glue:us-west-2:123456789012:database/default/foo"],
        "detail":{
            "changedPartitions": [
                "2018,01",
                "2018,02"
            ],
            "databaseName": "default",
            "tableName": "foo",
            "typeOfChange": "BatchCreatePartition"
            }
    }
    ```

For more information, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\. For events specific to AWS Glue, see [AWS Glue Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/EventTypes.html#glue-event-types)\.