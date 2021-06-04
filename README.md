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

TODO: add QnABot config to the repository

Changes that was done over the stack:

* Elastic nodes decreased to 1 from 4 to reduce costs.
* ELICIT_RESPONSE_MAX_RETRIES reduced to 1 from 3 in the QnABot Admin.
* Created additional lambdas for QnABot service. TODO: rename lambdas/add sources to the repo
* Created additional Lex bots for QnABot services. TODO: add sources/details to the repo/readme

# Develop
Project has one more repository inside with the core part builded on Vue 2.

# Deploy
In AWS created S3 Buckets, CloudFront points and additoinal domains for stage and production.
Setupped access to provide pipelines in BitBucket.

### Cloudfront URLs
* Staging: [chatbot-stage.getciville.com](https://chatbot-stage.getciville.com/)
* Production: [chatbot.getciville.com](https://chatbot.getciville.com/)
