# AWS Lambda and Serverless Framework ⚡️ Bolt for Python

> Slack app example for deploying to AWS Lambda using the Serverless Framework with Bolt for Python

## Overview

This is an example app that updates the [Getting Started ⚡️ Bolt for Python app][bolt-app] to be deployed to [AWS Lambda][aws-lambda] using the [Serverless Framework][serverless-framework].

Before you begin, you may want to follow our [Getting Started guide][bolt-guide] to learn how to build your first Slack app using the [Bolt for Python framework][bolt-website].

## Getting started

1. [Set up AWS credentials](#1-set-up-aws-credentials)
1. [Set up local project](#2-set-up-local-project)
1. [Create a Slack app](#3-create-a-slack-app)
1. [Deploy to AWS Lambda](#4-deploy-to-aws-lambda)
1. [Update Slack app settings](#5-update-slack-app-settings)
1. [Test your Slack app](#6-test-your-slack-app)
1. [Run on local machine](#7-run-on-local-machine)

## 1. Set up AWS credentials

### Install AWS CLI

Follow the guide to [install the AWS CLI for macOS, Windows, or Linux][aws-cli-install].

### Configure AWS CLI

Follow the guide to [create a new IAM User][aws-cli-configure-user] then configure your local machine with the command:

```zsh
aws configure
```

## 2. Set up local project

For local development, we recommend using a [Python virtual environment][venv] to manage your project’s dependencies.

```zsh
python3 -m venv .venv
source .venv/bin/activate
pip install -U pip
pip install -r requirements.txt
```

You may also want to [install ngrok][ngrok-install] to start a local tunnel for local development.

## 3. Create a Slack app

### Create an app on api.slack.com

1. Go to https://api.slack.com/apps
1. Select **Create New App**
    * Name your app, _don't worry you can change it later!_
1. Select **OAuth & Permissions**
    1. Scroll down to **Bot Token Scopes**
        1. Add the scope `app_mentions:read`
        1. Add the scope `chat:write`
    1. Select **Install App to Workspace** at the top of the page

### Export environment variables

```zsh
export SLACK_SIGNING_SECRET=<your-signing-secret> # Slack app settings > "Basic Information"
export SLACK_BOT_TOKEN=<your-xoxb-bot-token>      # Slack app settings > "OAuth & Permissions"
```

## 4. Deploy to AWS Lambda

Run the following the command to deploy to AWS Lambda:

```zsh
# As using Docker for deployments with serverless is a bit slow,
# this example uses ./lib directory instead
pip install -r requirements.txt -t lib
npx serverless deploy
# ...
# endpoints:
#  POST - https://d5c0t1xad4.execute-api.us-east-1.amazonaws.com/dev/slack/events
```

_Please note the endpoint `https://{your-domain}.amazonaws.com/dev/slack/events` because we'll use it in the next section._

## 5. Update Slack app settings

Now that your Slack app is deployed, you can register your AWS Lambda endpoint with the Slack API:

1. Go to https://api.slack.com/apps
1. Select your app
1. Select **Event Subscriptions**
    1. Enable **Events**
    1. Set the **Request URL** to `https://{your-domain}.amazonaws.com/dev/slack/events`
    1. Scroll down to **Subscribe to Bot Events**
    1. Add the following bot events:
        - `app_mention`
    1. Select **Save Changes**

## 6. Test your Slack app

You can test your app by opening a Slack workspace and mentioning your app:

> 💬 Howdy @MySlackBot!
>
> 🤖 Hey there, @Jane!

_Remember, your app must be in the channel or DM where you mention it._

## 7. Run on local machine

Open a terminal session to listen for incoming requests:

```zsh
python handler.py
```

Open another terminal session to proxy Slack API requests locally:

```zsh
# -subdomain= is avalable only for paid accounts
ngrok http 3000 -subdomain=my-unique-name
```

Update your Slack app settings to use your ngrok address:
1. **Event Subscriptions**
    1. Set the **Request URL** to `https://my-unique-name.ngrok.io/slack/events`

Follow the steps to [test your app](#6-test-your-slack-app).

[aws-cli-install]: https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html
[aws-cli-configure]: https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html
[aws-cli-configure-user]: https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-creds
[aws-lambda]: https://aws.amazon.com/lambda/
[venv]: https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/#creating-a-virtual-environment
[bolt-app]: https://github.com/slackapi/bolt-python/tree/main/examples/getting_started
[bolt-guide]: https://slack.dev/bolt-python/tutorial/getting-started
[bolt-website]: https://slack.dev/bolt-python/
[ngrok-install]: https://api.slack.com/tutorials/tunneling-with-ngrok
[serverless-framework]: https://serverless.com/
[slack-app-settings]: https://api.slack.com/apps
