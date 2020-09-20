aws iam create-role --role-name lambda-http-ping-role --assume-role-policy-document file://policy-document.json
{
    "Role": {
        "Path": "/",
        "RoleName": "lambda-http-ping-role",
        "RoleId": "AROA5V7ZTVN42TJQWKUOY",
        "Arn": "arn:aws:iam::940584446841:role/lambda-http-ping-role",
        "CreateDate": "2020-09-19T14:50:56+00:00",
        "AssumeRolePolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Principal": {
                        "Service": "lambda.amazonaws.com"
                    },
                    "Action": "sts:AssumeRole"
                }
            ]
        }
  }
}




aws lambda create-function --function-name http-ping --zip-file fileb://http-metrics.zip --handler index.handler --runtime nodejs12.x --role arn:aws:iam::940584446841:role/lambda-http-ping-role
{
    "FunctionName": "http-ping",
    "FunctionArn": "arn:aws:lambda:us-east-1:940584446841:function:http-ping",
    "Runtime": "nodejs12.x",
    "Role": "arn:aws:iam::940584446841:role/lambda-http-ping-role",
    "Handler": "index.handler",
    "CodeSize": 5986758,
    "Description": "",
    "Timeout": 3,
    "MemorySize": 128,
    "LastModified": "2020-09-19T15:08:01.165+0000",
    "CodeSha256": "BS+AiRdnEwmUG3kQYyVBQNBHpS10XudAxVuxXeTemEA=",
    "Version": "$LATEST",
    "TracingConfig": {
        "Mode": "PassThrough"
    },
    "RevisionId": "de22f185-862a-4452-941c-8030a6937de6",
    "State": "Active",
    "LastUpdateStatus": "Successful"
}


aws iam put-role-policy --role-name lambda-http-ping-role  --policy-name AWSLambdaBasicExecutionRole-http-ping-role --policy-document file://iam-policy.json


aws lambda update-function-configuration --function-name http-ping --environment "Variables={SERVICE_NAME=\"MyBlog\",URL=\"http://example.com\"}"


aws lambda list-functions

aws events put-rule --name http-ping-event-rule --schedule-expression 'rate(1 minute)' 
{
    "RuleArn": "arn:aws:events:us-east-1:940584446841:rule/http-ping-event-rule"
}

aws lambda add-permission --function-name http-ping --statement-id http-ping-event --action 'lambda:InvokeFunction' --principal events.amazonaws.com --source-arn arn:aws:events:us-east-1:940584446841:rule/http-ping-event-rule
{
    "Statement": "{\"Sid\":\"http-ping-event\",\"Effect\":\"Allow\",\"Principal\":{\"Service\":\"events.amazonaws.com\"},\"Action\":\"lambda:InvokeFunction\",\"Resource\":\"arn:aws:lambda:us-east-1:940584446841:function:http-ping\",\"Condition\":{\"ArnLike\":{\"AWS:SourceArn\":\"arn:aws:events:us-east-1:940584446841:rule/http-ping-event-rule\"}}}"
}

Criar targets.json  com o arn da funcao 



aws events put-targets --rule http-ping-event-rule --targets file://targets.json


ver resultado em CloudWatch Metrics

(nao funcionou:)
aws cloudwatch get-metric-statistics --namespace "MyApp/Service1" --metric-name "Latency" --start-time 2020-09-10T00:00:00Z --end-time 2020-09-19T23:59:59Z --period 600 --statistics Average --unit Milliseconds




