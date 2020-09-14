```
npm install
zip -r chaos-monkey.zip index.js node_modules/ package.json package-lock.json

aws iam create-role --role-name lambda-chaos-monkey --assume-role-policy-document file://policy-document.json

# attach a policy to the role from file
aws iam put-role-policy --role-name lambda-chaos-monkey  --policy-name AWSLambdaBasicExecutionRole-chaos-monkey --policy-document file://iam-policy.json

aws iam list-roles

# create the function
aws lambda create-function --function-name chaos-monkey --zip-file fileb://chaos-monkey.zip --handler index.handler --runtime nodejs12.x --role arn:aws:iam::467725875162:role/lambda-chaos-monkey 

{
    "FunctionName": "chaos-monkey",
    "FunctionArn": "arn:aws:lambda:us-east-1:467725875162:function:chaos-monkey",
    "Runtime": "nodejs12.x",
    "Role": "arn:aws:iam::467725875162:role/lambda-chaos-monkey",
    "Handler": "index.handler",
    "CodeSize": 6203130,
    "Description": "",
    "Timeout": 3,
    "MemorySize": 128,
    "LastModified": "2020-09-12T19:55:15.766+0000",
    "CodeSha256": "sXZ5/8mnkNkdAzU4fUdubFGp4sjkMQ2yAvjIu+eq8Lo=",
    "Version": "$LATEST",
    "TracingConfig": {
        "Mode": "PassThrough"
    },
    "RevisionId": "70cf767f-6be3-4068-a7e3-36f04ce8ccc0",
    "State": "Active",
    "LastUpdateStatus": "Successful"
}



aws lambda invoke --function-name hello-world out --log-type Tail --query 'LogResult' --output text | base64 -d

aws lambda list-functions
``` 



>>> start  ec2 instances using elastic beanstalk  (01-elastic-beanstalk)



Add CloudWatch Event

```
aws events put-rule --name chaos-monkey-event-rule --schedule-expression 'rate(2 minutes)'
{
    "RuleArn": "arn:aws:events:us-east-1:940584446841:rule/chaos-monkey-event-rule"
}

aws lambda add-permission --function-name chaos-monkey --statement-id chaos-monkey-event --action 'lambda:InvokeFunction' --principal events.amazonaws.com  --source-arn arn:aws:events:us-east-1:940584446841:rule/chaos-monkey-event-rule
{
    "Statement": "{\"Sid\":\"chaos-monkey-event\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},\"Action\":\"lambda:InvokeFunction\",\"Resource\":\"arn:aws:lambda:us-east-1:940584446841:function:chaos-monkey\",\"Condition\":{\"ArnLike\":{\"AWS:SourceArn\":\"arn:aws:events:us-east-1:940584446841:rule/chaos-monkey-event-rule\"}}}"
}

aws events put-targets --rule chaos-monkey-event-rule --targets file://targets.json
{
    "FailedEntryCount": 0,
    "FailedEntries": []
}

```


LOGs

```
aws logs describe-log-groups --query logGroups[*].logGroupName 
[
    "/aws/lambda/chaos-monkey"
]

aws logs describe-log-streams --log-group-name "/aws/lambda/chaos-monkey" --query logStreams[*].logStreamName
[
    "2020/09/14/[$LATEST]359b4276365e487e8679ba612ecc7350",
    "2020/09/14/[$LATEST]6693cdaa7b6d4f9fb0faa826e3a09926"
]

aws logs get-log-events --log-group-name "/aws/lambda/chaos-monkey" --log-stream-name '2020/09/14/[$LATEST]359b4276365e487e8679ba612ecc7350'

```

Disabling Rule for the CloudWatch Event
``` 

aws events list-rules
{
    "Rules": [
        {
            "Name": "chaos-monkey-event-rule",
            "Arn": "arn:aws:events:us-east-1:940584446841:rule/chaos-monkey-event-rule",
            "State": "ENABLED",
            "ScheduleExpression": "rate(1 minute)",
            "EventBusName": "default"
        },
        {
            "Name": "chaos-monkey-rule",
            "Arn": "arn:aws:events:us-east-1:940584446841:rule/chaos-monkey-rule",
            "State": "DISABLED",
            "ScheduleExpression": "rate(5 minutes)",
            "EventBusName": "default"
        }
    ]
}

aws events disable-rule --name chaos-monkey-event-rule

aws events list-rules
{
    "Rules": [
        {
            "Name": "chaos-monkey-event-rule",
            "Arn": "arn:aws:events:us-east-1:940584446841:rule/chaos-monkey-event-rule",
            "State": "DISABLED",
            "ScheduleExpression": "rate(1 minute)",
            "EventBusName": "default"
        },
        {
            "Name": "chaos-monkey-rule",
            "Arn": "arn:aws:events:us-east-1:940584446841:rule/chaos-monkey-rule",
            "State": "DISABLED",
            "ScheduleExpression": "rate(5 minutes)",
            "EventBusName": "default"
        }
    ]
}


```
