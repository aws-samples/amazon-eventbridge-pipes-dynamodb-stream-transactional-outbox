## Transactional Outbox Pattern using AWS serverless services

This is a sample AWS SAM (Serverless Application Model) template that creates an API Gateway, multiple Lambdas, DynamoDB Table, SQS Queues, and an EventBridge EventBus in order to implement a Transactional Outbox Pattern. The following steps guide you on how to use this template.

## Prerequisites
1. Install [AWS CLI](https://aws.amazon.com/cli/).
2. Install [AWS SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html).
3. Configure AWS CLI with your credentials.
4. Python 3.10 runtime is required for the Lambda functions.

## Steps to Deploy
1. **Clone the repository**: Clone the repository that contains the AWS SAM template.

2. **Build the SAM application**: Navigate to the directory that contains your SAM file and run the following command:
    ```
    sam build
    ```
    This command compiles the code and pulls in the necessary dependencies.

3. **Package the SAM application**: After the build completes, package the application artifacts and upload them to an S3 bucket. Replace `<s3-bucket-name>` with the name of your bucket:
    ```
    sam package --output-template-file packaged.yaml --s3-bucket <s3-bucket-name>
    ```
    This command returns a new packaged.yaml file which references the code stored in the S3 bucket.

4. **Deploy the SAM application**: Finally, deploy the application using the following command. Replace `<stack-name>` with a name for your CloudFormation stack.
    ```
    sam deploy --template-file packaged.yaml --stack-name <stack-name> --capabilities CAPABILITY_IAM
    ```
    This command creates a CloudFormation stack and deploys your resources.

## Post Deployment Steps
Post deployment, your API Gateway endpoint URL can be retrieved from the Outputs section of your CloudFormation stack. You can test your endpoint using tools such as curl or Postman.

## Implementation Details
The implemented stack is based on a serverless architecture, where API Gateway is used to trigger a DynamoDB operation via a POST request. The change in the DynamoDB triggers an EventBridge EventBus via AWS Pipes. This EventBus invokes the relevant Lambda function through an SQS Queue, depending on whether an order has been created or updated.

Please note that the Lambda functions require Python 3.10 and the code must be placed in a `src/` directory. The specific Lambda function handlers to be executed are `order_created_publisher_one.lambda_handler` and `order_created_publisher_two.lambda_handler`.

## Cleanup
To delete the serverless application, you can use the AWS Management Console, AWS CLI, or AWS CloudFormation to delete the stack. It is generally recommended to delete the CloudFormation stack as it will take care of deleting all the related resources.

Example AWS CLI command:
```
aws cloudformation delete-stack --stack-name <stack-name>
```
Replace `<stack-name>` with the name of your CloudFormation stack.

**NOTE**: If you are using AWS resources for real-world usage, remember to monitor usage and associated cost to avoid unexpected charges.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

