## Configure AWS Credentials

Go to the ~/.aws/credentials file and add a profile for your IAM user
export the AWS_PROFILE or use the configuration like this:

```
sls config credentials --provider aws --key --secret
```
## Create a new Service based on a template from python
1. create a folder for our new app and change directory
```
mkdir tramboApp
cd tramboApp
```
2. Run the following commands to create a new project
```
sls create --help
sls create -t aws-python
```

## Review the created files
1. handler.py contains our function for lambda
2. serverless.yaml contains the configuration of our serverless project

## Deploy your first lambda with serverless framework!

```
sls deploy
```

## Create Environment variables for your functions

1. Let's create a new function for that, add the following code to the serverless.yml
```
envVariables:
  handler: envVar.handler
  environment:
    envVar: 'tramboCloud rules!'
```
2. Duplicate the handler.py and rename it envVar.py. Update the handler name from hello to handler and replace the body with this
```
body = {
    "message": 'The environment variable value is ' + os.environ['envVar']
}
```
**please import OS library on your envVar.py**

3. Let's check the **AWS console** and test our functions

4. Test your functions **locally**
```
sls invoke --function hello
sls invoke --function envVariables
```
**Note: Some resources like SES or S3 cannot be emulated locally, DynamoDB can be emulated.**

## API Gateway

1. Lets create our api gateway for our envVariables function

In order to do so, add the following code to our function definition
```
envVariables:
  handler: envVar.handler
  events:
    - http:
        path: /envVariables
        method: get
  environment:
    envVar: 'tramboCloud rules!'
```
**This will create an api gateway and the method for GET calls**


## Create AWS Resources with Serverless

Add the following code to your serverless.yml in the `resources` section
```
resources:
  Resources:
    s3bucket:
     Type: AWS::S3::Bucket
     Properties:
       BucketName: ${self:service}-${self:provider.stage}-bucket
    usersTable:
      Type: AWS::DynamoDB::Table
      Properties:
        TableName: usersTable
        AttributeDefinitions:
          - AttributeName: email
            AttributeType: S
        KeySchema:
          - AttributeName: email
            KeyType: HASH
        ProvisionedThroughput:
          ReadCapacityUnits: 1
          WriteCapacityUnits: 1
```
