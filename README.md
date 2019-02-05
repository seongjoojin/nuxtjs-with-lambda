# Nuxt.js with AWS Lambda

This is a tiny example to show how to deploy the Nuxt.js app on AWS Lambda using [Apex Up](https://up.docs.apex.sh).

## Nuxt.js initial setting

```
$ yarn create nuxt-app <my-project>
``` 

or

```
$ npx create-nuxt-app <project-name>
```

Choose server-side framework Express.

## Nuxt.js initial install

```
$ cd <project-name>
$ npm i
```

or

```
$ cd <project-name>
$ yarn
```

## Install Apex Up

```
$ curl -sf https://up.apex.sh/install | sh

# verify the installation
$ up version
```

## AWS credentials

You need to have one AWS account and recommend to use IAM with programmatic way for security and convenience. You are having `~/.aws/credentials`(windows:`%UserProfile%\.aws\credentials`) which is storing AWS credentials. It allows you to use `AWS_PROFILE` environment. If you don't please make one and save it with your account `access key` and `security key` in it.

```
# ~/.aws/credentials

[my-aws-account-for-lambda]
aws_access_key = xxxxxxxxxxxxx
aws_secret_access_key = xxxxxxxxxxxxxxxxxxxx
```

## IAM policy for Up CLI

IAM policy allows the Up to access your AWS resources in order to deploy your Nuxt.js app on Lambda. Go to [AWS IAM](https://aws.amazon.com/iam/) and make the new policy and link it up to your AWS account.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "acm:*",
        "cloudformation:Create*",
        "cloudformation:Delete*",
        "cloudformation:Describe*",
        "cloudformation:ExecuteChangeSet",
        "cloudformation:Update*",
        "cloudfront:*",
        "cloudwatch:*",
        "ec2:*",
        "ecs:*",
        "events:*",
        "iam:AttachRolePolicy",
        "iam:CreatePolicy",
        "iam:CreateRole",
        "iam:DeleteRole",
        "iam:DeleteRolePolicy",
        "iam:GetRole",
        "iam:PassRole",
        "iam:PutRolePolicy",
        "lambda:AddPermission",
        "lambda:Create*",
        "lambda:Delete*",
        "lambda:Get*",
        "lambda:InvokeFunction",
        "lambda:List*",
        "lambda:RemovePermission",
        "lambda:Update*",
        "logs:Create*",
        "logs:Describe*",
        "logs:FilterLogEvents",
        "logs:Put*",
        "logs:Test*",
        "route53:*",
        "route53domains:*",
        "s3:*",
        "ssm:*",
        "sns:*"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "apigateway:*",
      "Resource": "arn:aws:apigateway:*::/*"
    }
  ]
}
```

## Create `up.json` file

```
{
  "name": "nextjs-example",
  // aws account profile in ~/.aws/credentials
  "profile": "my-aws-account-for-lambda",
  "regions": ["ap-northeast-2"],
  "lambda": {
    // min 128, default 512
    "memory": 256,
    // AWS Lambda supports node.js 8.10 latest
    "runtime": "nodejs8.10"
  },
  "proxy": {
    "command": "npm start",
    "timeout": 25,
    "listen_timeout": 15,
    "shutdown_timeout": 15
  },
  "stages": {
    "development": {
      "proxy": {
        "command": "npm dev"
      }
    }
  },
  "environment": {
    // you can hydrate env variables as you want.
    "NODE_ENV": "production"
  },
  "error_pages": {
    "variables": {
      "support_email": "admin@my-email.com",
      "color": "#2986e2"
    }
  }
}
```

## Build the Nuxt.js app before deploy

```
$ yarn build
```

## Create `.upignore` file

The Up will inspect your files to compose and deploy to lambda. Firstly The up will read `.gitignore` and ignore files written in `.gitignore`. And after that, `.upignore` will be read. The Up, by default, ignores dotfiles, so needs to negate `.nuxt` directory in `.upignore` in order for the Up will build the package with it.

```
# .upignore

!.nuxt
```

## Deploy

```
$ up
```

## Special Thanks

@mattdamon108 (https://github.com/mattdamon108/nextjs-with-lambda)
