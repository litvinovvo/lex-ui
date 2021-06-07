# About the project
It is the clone of [AWS Sample repository](https://github.com/aws-samples/aws-lex-web-ui) at version 0.18.0.

Changes that was done in the repository:

* Now it is possible to use [wildcard](https://bitbucket.org/lincolnlabs/aws-lex-web-ui/src/bacf8f606c499c4c485826e0cd14eb4348495e48/src/website/index.html#lines-78) for IFrame communications, so we can setup the chat on any website.
* Design changes (done roughly due to limited time).
* Additional options to disable input field on cards with buttons response.
* Special placeholder for input field on disabled state on buttons response.
* Hide initial utterance with initialUtterance options.

# How to setup AWS environment
This repository is mostly about UI part of bot, that require Lex bot name and Congnito poolId to work.

## AWS QnABot
First version was done with customized QnABot stack from AWS.
To setup it from scratch you can click on the Lauch Stack button by the [link](https://aws.amazon.com/ru/blogs/machine-learning/creating-a-question-and-answer-bot-with-amazon-lex-and-amazon-alexa/).
Go throug setup process and got some system with Kibana/Elastic stack for statistic, UI with some custom framework around original AWS lex, several AWS Lambdas and Lex bots that required for the framework to correct work.

Changes that was done over the stack:

* Elastic nodes decreased to 1 from 4 to reduce costs.
* ELICIT_RESPONSE_MAX_RETRIES reduced to 1 from 3 in the QnABot Admin.
* Created additional lambdas for QnABot service.
* Created additional Lex bots for QnABot services.

### Setup

* Import QnA Bot config from aws/qnabot-basic/basic-flow.json
* Import QnA Bot Labdas from aws/qnabot-basic/labmda
* Import QnA Bot additional Lex bots from aws/qnabot-basic/lex

# Develop
Project has one more repository inside with the core part builded on Vue 2.
To have changes from lex-web-ui part you have to run npm run build-dist and rerun dev or build script on the main repository.

# Deploy
To enable BitBucket pipeline (deploy part) you have to setup S3 Bucket, CloudFront and additional domains (optional).

### Cloudfront URLs

* Staging: [chatbot-stage.getciville.com](https://chatbot-stage.getciville.com/)
* Production: [chatbot.getciville.com](https://chatbot.getciville.com/)
