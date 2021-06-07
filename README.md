# About the project
It is the clone of [AWS Sample repository](https://github.com/aws-samples/aws-lex-web-ui) at version 0.18.0.

Changes that was done in the repository:

* Now it is possible to use [wildcard](https://bitbucket.org/lincolnlabs/aws-lex-web-ui/src/bacf8f606c499c4c485826e0cd14eb4348495e48/src/website/index.html#lines-78) for IFrame communications, so we can setup the chat on any website.
* Design changes (done roughly due to limited time).
* Additional options to disable input field on cards with buttons response.
* Special placeholder for input field on disabled state on buttons response.
* Hide initial utterance with initialUtterance options.

# Development
Project has one more repository inside with the core part builded on Vue 2.
To have changes from lex-web-ui part you have to run npm run build-dist and rerun dev or build script on the main repository.

# Deploy
To enable BitBucket pipeline (deploy part) you have to setup S3 Bucket, CloudFront and additional domains (optional).

### Cloudfront URLs

* Staging: [chatbot-stage.getciville.com](https://chatbot-stage.getciville.com/)
* Production: [chatbot.getciville.com](https://chatbot.getciville.com/)

# How to setup AWS environment
This repository is mostly about UI part of bot, that require Lex bot name and Congnito poolId to work.
To get the full system we can use Lex bot directly or setup QnABot stack from AWS.

The first implementation was done as a prototype to check the idea of form in the chatbot and test the QnABot stack features.
It was found that QnABot not convenient for custom requirements, has some bugs, and lacks detailed documentation.
The next iteration is good to try to implement using Lex and Lambda directly.

## AWS QnABot
To setup it from scratch you can click on the Lauch Stack button by the [link](https://aws.amazon.com/ru/blogs/machine-learning/creating-a-question-and-answer-bot-with-amazon-lex-and-amazon-alexa/).
Go throug setup process and got some system with Kibana/Elastic stack for statistic, UI with some custom framework around original AWS lex, several AWS Lambdas and Lex bots that required for the framework to correct work.

### Setup QnABot

* Import QnA Bot config from aws/qnabot-basic/basic-flow.json
* Import QnA Bot Labdas from aws/qnabot-basic/labmda
* Import QnA Bot additional Lex bots from aws/qnabot-basic/lex
* Decrease Elastic nodes to 1 from 4 to reduce costs
* Set ELICIT_RESPONSE_MAX_RETRIES to 1 from 3 in the QnABot Admin

### Possible improvements

* Rename questions Id's in the QnABot Admin from, for example 01.chaining.demo.lambda to  01.civille.basic.start.
* Rename namespace for variables from demo to basic (it requires to update lambda's code)
* Remove Elastic dependency (currently some internal code relies on Elastic availability)
