---
title: "Create your first AWS Lambda function with Terraform"
date: 2019-02-08T12:22:30-08:00
draft: false
layout: 'posts'
tags: ["terraform", "aws", "lambda"]
---

## Create your first AWS Lambda function with Terraform
![aws-lambda](http://www.digiclarity.com/images/technology/templates/lambda.jpg)

This post will go over how to create your first AWS Lambda function which uses the API Gateway to return "Hello World!".

1. Start with creating a directory called `lambda-test` and change to it. You can do that in your cmd with the following:

        mkdir lambda-test
        cd lambda-test

2. Create a new file called `main.js` and place the following content inside.
    ```
    'use strict'

    exports.handler = function(event, context, callback) {
    var response = {
        statusCode: 200,
        headers: {
        'Content-Type': 'text/html; charset=utf-8'
        },
        body: '<p>Hello world!</p>'
    }
    callback(null, response)
    }
    ```

3. We need to create a .zip file from the source code. While inside the `lambda-test` folder. Do the following:

        $ zip /lambda-test.zip main.js

4. It's now time to create a test bucket.

        $ aws s3api create-bucket --bucket=terraform-serverless-lambda-test --region=us-east-1

5. Once the bucket has been created, it's time for us to upload our zip file to that bucket.

        $ aws s3 cp lambda-test.zip s3://terraform-serverless-lambda-test/v1.0.0/lambda-test.zip


## Create Lambda function
6. It's time for Terraform! Create a new file called `lambda.tf` and place the following inside:

        provider "aws" {
         region = "us-east-1"
        }

        resource "aws_lambda_function" "example" {
        function_name = "ServerlessExample"

        s3_bucket = "terraform-serverless-lambda-test"
        s3_key    = "v1.0.0/lambda-test.zip"

        # "main" is the filename within the zip file (main.js) and "handler"
        # is the name of the property under which the handler function was
        # exported in that file.
        handler = "main.handler"
        runtime = "nodejs6.10"

        role = "${aws_iam_role.lambda_exec.arn}"
        }

        # IAM role which dictates what other AWS services the Lambda function
        # may access.
        resource "aws_iam_role" "lambda_exec" {
        name = "serverless_example_lambda"

        assume_role_policy = <<EOF
        {
        "Version": "2012-10-17",
        "Statement": [
            {
            "Action": "sts:AssumeRole",
            "Principal": {
                "Service": "lambda.amazonaws.com"
            },
            "Effect": "Allow",
            "Sid": ""
            }
        ]
        }
        EOF
        }

        resource "aws_lambda_permission" "apigw" {
            statement_id  = "AllowAPIGatewayInvoke"
            action        = "lambda:InvokeFunction"
            function_name = "${aws_lambda_function.example.arn}"
            principal     = "apigateway.amazonaws.com"

            # The /*/* portion grants access from any method on any resource
            # within the API Gateway "REST API".
            source_arn = "${aws_api_gateway_deployment.example.execution_arn}/*/*"
            }


7. We can run the the following invoke command to see if our function is working.

        $ aws lambda invoke --region=us-east-1 --function-name=ServerlessExample output.txt

## API Gateway
8. Create a new file called `api_gateway.tf` next to our lambda.tf file.

        resource "aws_api_gateway_rest_api" "example" {
            name        = "ServerlessExample"
            description = "Terraform Serverless Application Example"
        }

9. Define proxy resource:

        resource "aws_api_gateway_resource" "proxy" {
            rest_api_id = "${aws_api_gateway_rest_api.example.id}"
            parent_id   = "${aws_api_gateway_rest_api.example.root_resource_id}"
            path_part   = "{proxy+}"
        }

        resource "aws_api_gateway_method" "proxy" {
            rest_api_id   = "${aws_api_gateway_rest_api.example.id}"
            resource_id   = "${aws_api_gateway_resource.proxy.id}"
            http_method   = "ANY"
            authorization = "NONE"
        }

        resource "aws_api_gateway_integration" "lambda" {
            rest_api_id = "${aws_api_gateway_rest_api.example.id}"
            resource_id = "${aws_api_gateway_method.proxy.resource_id}"
            http_method = "${aws_api_gateway_method.proxy.http_method}"

            integration_http_method = "POST"
            type                    = "AWS_PROXY"
            uri                     = "${aws_lambda_function.example.invoke_arn}"
        }

        resource "aws_api_gateway_method" "proxy_root" {
            rest_api_id   = "${aws_api_gateway_rest_api.example.id}"
            resource_id   = "${aws_api_gateway_rest_api.example.root_resource_id}"
            http_method   = "ANY"
            authorization = "NONE"
        }

        resource "aws_api_gateway_integration" "lambda_root" {
            rest_api_id = "${aws_api_gateway_rest_api.example.id}"
            resource_id = "${aws_api_gateway_method.proxy_root.resource_id}"
            http_method = "${aws_api_gateway_method.proxy_root.http_method}"

            integration_http_method = "POST"
            type                    = "AWS_PROXY"
            uri                     = "${aws_lambda_function.example.invoke_arn}"
            }

        resource "aws_api_gateway_deployment" "example" {
        depends_on = [
            "aws_api_gateway_integration.lambda",
            "aws_api_gateway_integration.lambda_root",
        ]

        rest_api_id = "${aws_api_gateway_rest_api.example.id}"
        stage_name  = "test"
        }

        output "base_url" {
            value = "${aws_api_gateway_deployment.example.invoke_url}"
            }

10. run `terraform init` and `terraform apply` and you should get a similar output:

        Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

        Outputs:

        base_url = https://gjind8zsgc.execute-api.us-east-1.amazonaws.com/test

11. Click on the base_url to see your Hello World application =)
