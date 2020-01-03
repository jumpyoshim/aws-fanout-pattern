# aws-fanout-pattern

## Description

aws-fanout-pattern is a sample implementation of a [_Fanout Pattern_ of a cloud design pattern](http://aws.clouddesignpattern.org/index.php/CDP:Fanout%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B3)

![sequence](https://user-images.githubusercontent.com/24784855/71701961-24cd2500-2e10-11ea-826b-7d1dee34ecaf.png)

## Production

### prerequisites

You have to prepare credentials with proper policies.

And,

- install [aws-cli](https://github.com/aws/aws-cli)
- install [aws-sam-cli](https://github.com/awslabs/aws-sam-cli). Docker is also required. Follow the instruction [here](https://github.com/awslabs/aws-sam-cli#installation).
- install [direnv](https://github.com/direnv/direnv)
- install [saw](https://github.com/TylerBrock/saw)
  - you can watch CloudWatch logs on your terminal
- set environment variables to [.envrc.sample](./.envrc.sample) and remove _.sample_.
  - _WEBHOOK_URL_ Incoming Webhook URL of Slack. You can get URL at [this page](https://api.slack.com/incoming-webhooks).
  - _CHANNEL_ where the Lambda③'ll post message in Slack
  - _USER_NAME_ by whom the message is posted in Slack
  - _ICON_ message sender's icon like :piggy:
  - _FILE_BUCKET_ S3 bucket where you upload your file. It's _S3_ in sequence and hould be unique globally.
  - _STACK_BUCKET_ is S3 bucket name for artifacts of SAM and should be unique globally.

### deploy

```
$ dep ensure                       # to resolve dependency
$ aws s3 mb "s3://${STACK_BUCKET}" # for artifacts of SAM
$ make deploy
```

Now, you can check a behavior of this architecture by uploading file to S3.

```
$ saw groups
/aws/lambda/stack-s3-sns-sqs-lambda-slack-go-sa-WriteExtLambda-XXXXXXXXXXXX
/aws/lambda/stack-s3-sns-sqs-lambda-slack-WriteFileNameLambda-XXXXXXXXXXXX
/aws/lambda/stack-s3-sns-sqs-lambda-slack-go-sa-NotifierLambda-XXXXXXXXXXXX

$ saw watch /aws/lambda/stack-s3-sns-sqs-lambda-slack-go-sa-WriteExtLambda-XXXXXXXXXXXX &
$ saw watch /aws/lambda/stack-s3-sns-sqs-lambda-slack-WriteFileNameLambda-XXXXXXXXXXXX &
$ saw watch /aws/lambda/stack-s3-sns-sqs-lambda-slack-go-sa-NotifierLambda-XXXXXXXXXXXX &

# open another window
$ aws s3 cp ./README.md "s3://${FILE_BUCKET}"
```

### delete

In this architecture, Lambdas execute _long polling_ to SQS and it's billable. So you should delete your stack by executing the command below after trying deploy.

```
$ make delete
```

## Articles (Japanese)

- [Go で学ぶ AWS Lambda（PDF、ePub セット版）](https://toshi0607.booth.pm/items/1034858)
  - This architecture is explained in detail in this book.
- [技術書典 5 で『Go で学ぶ AWS Lambda』を出展します #技術書典](http://toshi0607.com/programming/learning-aws-lambda-with-go/)
- [技術書典 5 の『Go で学ぶ AWS Lambda』の振り返りとフィードバックのお願い #技術書典](http://toshi0607.com/event/review-of-tbf5/)
