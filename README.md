# AWS - New Relic log ingestion

AWS Serverless Application that sends log data from CloudWatch Logs and S3 to New Relic Infrastructure (Cloud integrations) and New Relic Logging.

## Pre-requisites

To forward data to New Relic: [New Relic License Key](https://docs.newrelic.com/docs/accounts/install-new-relic/account-setup/license-key)

## Configuration

You can configure forwarding of logs to New Relic Infrastructure and/or New Relic Logging using the following two environment variables.

| Env. Variable | Behavior | Value |
|---------------|----------|----------------------------|
| LICENSE_KEY | Needed for sending data to both Infrastructure and Logging. If set, enables forwarding logs to New Relic Infrastructure by default | Your New Relic license key, which you can get by following [these instructions](https://docs.newrelic.com/docs/accounts/install-new-relic/account-setup/license-key).|
| LOGGING_ENABLED | determines if logs are forwarded to New Relic Logging | "false" by default
| INFRA_ENABLED | determines if logs are forwarded to New Relic Infrastructure | "true" by default


### Optional configuration

| Env. Variable | Decription | Default Value |
|---------------|------------|---------------------------|
| NR_LOGGING_ENDPOINT |  New Relic ingestion endpoint for Logging | `https://log-api.newrelic.com/log/v1` |
| NR_INFRA_ENDPOINT |  New Relic ingestion endpoint for Infra | `https://cloud-collector.newrelic.com` |


## Configure a Lambda trigger for logging
- To get your logs streaming to New Relic you will need to attach a trigger to the lambda.
- To add a trigger, go under Configuration on the lambda management and select CloudWatch.
- Select your desired log group and set the filter name.
- Add the trigger and save the lambda.

## Configure Retries

You can configure the number of retries you want to perform in case the function fails to send the data in case of communication issues.

Please be aware that more number of retries can make the function run for longer time and therefore increases the probability of having higher costs for Lambda. On the contrary, decreasing the number of retries could increase the probability of data loss.

Recommended number is 3 retries, but you can change the retry behavior by changing the below parameters:

```python
MAX_RETRIES = 3  # Defines the number of retries after lambda failure to deliver data
INITIAL_BACKOFF = 1  # Defines the initial wait seconds until next retry is executed
BACKOFF_MULTIPLIER = 2  # Time multiplier between the retries
```

As an example, in default above configuration, first retry will happen after 1 second, second retry after 2 seconds and third retry will happen after 4 seconds.

## Resources created by the SAM template

There are few resources that will be created when you create the application from the repository:

- The Lambda function itself
- A Role used to give execution permissions to the Lambda function based in CloudWatch Logs.

**_All other lambda configurations not listed in the steps above can be left as the defaults._**