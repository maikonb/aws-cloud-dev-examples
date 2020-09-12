``` 
# create a generic role for lambda execution
aws iam create-role --role-name lambda-hello-world --assume-role-policy-document file://role-policy.json
Output:
>> {
>>     "Role": {
>>         "Path": "/",
>>         "RoleName": "lambda-hello-world",
>>         "RoleId": "AROAWZZVCO7NA5MLDCXGP",
>>         "Arn": "arn:aws:iam::467725875162:role/lambda-hello-world",
>>         "CreateDate": "2020-09-12T13:07:34+00:00",
>>         "AssumeRolePolicyDocument": {
>>             "Version": "2012-10-17",
>>             "Statement": [
>>                 {
>>                     "Effect": "Allow",
>>                     "Principal": {
>>                         "Service": "lambda.amazonaws.com"
>>                     },
>>                     "Action": "sts:AssumeRole"
>>                 }
>>             ]
>>         }
>>     }
>> }

# attach a policy to the role
aws iam attach-role-policy --role-name lambda-hello-world --policy-arn  arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole


aws iam list-roles

# create the function
aws lambda create-function --function-name hello-world --zip-file fileb://function.zip --handler index.handler --runtime nodejs12.x --role arn:aws:iam::467725875162:role/lambda-hello-world
>> Output:
>> {
>>     "Role": {
>>         "Path": "/",
>>         "RoleName": "lambda-hello-world",
>>         "RoleId": "AROAWZZVCO7NA5MLDCXGP",
>>         "Arn": "arn:aws:iam::467725875162:role/lambda-hello-world",
>>         "CreateDate": "2020-09-12T13:07:34+00:00",
>>         "AssumeRolePolicyDocument": {
>>             "Version": "2012-10-17",
>>             "Statement": [
>>                 {
>>                     "Effect": "Allow",
>>                     "Principal": {
>>                         "Service": "lambda.amazonaws.com"
>>                     },
>>                     "Action": "sts:AssumeRole"
>>                 }
>>             ]
>>         }
>>     }
>> }


aws lambda invoke --function-name hello-world out --log-type Tail --query 'LogResult' --output text | base64 -d

aws lambda list-functions
``` 
