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
