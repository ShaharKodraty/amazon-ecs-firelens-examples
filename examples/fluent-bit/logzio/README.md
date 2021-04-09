For documentation on sending your FireLens log data to [Logz.io](https://logz.io) Logs, please refer to: [Fluent Bit and Firelens in Logz.io](https://docs.logz.io/shipping/log-sources/fargate.html).

For documentation for Fluent Bit with Logz.io plugin, please refer to [Fluent Bit with Logz.io plugin](https://docs.logz.io/shipping/log-sources/fluent-bit.html).

Task definition will include a log configuration as provided here in this README.
    `LISTENER-HOST`: You can find your listener in our [Regions Page](https://docs.logz.io/user-guide/accounts/account-region.html).
    `LOG-SHIPPING-TOKEN`: You'll need to specify your Logz.io's shipping token provided in your account settings page.

```
"logConfiguration": {
        "logDriver": "awsfirelens",
        "options": {
          "Name": "Http",
          "Host": "<<LISTENER-HOST>>",
          "URI": "/?token=<<LOG-SHIPPING-TOKEN>>&type=fargate",
          "Port": "8071",
          "tls": "on",
          "tls.verify": "off",
          "Format": "json_lines"
        }
}
```

You can use this code block as an example to replace the default JSON in your task definition:
```
{
  "family": "logzio-fargate-task",
  "requiresCompatibilities": [ "FARGATE" ],
  "containerDefinitions": [
    {
      "name": "logzio-log-router",
      "image": "amazon/aws-for-fluent-bit:latest",
      "essential": true,
      "firelensConfiguration": {
        "type": "fluentbit",
        "options": { "enable-ecs-log-metadata": "true" }
      },
      "logConfiguration": {
        "logDriver": "awslogs",
        "options": {
          "awslogs-group": "/aws/ecs/logzio-fargate-logs",
          "awslogs-region": "<<AWS-REGION>>",
          "awslogs-stream-prefix": "aws/ecs"
        }
      }
    },
    {
      "name": "app",
      "essential": true,
      "image": "<<YOUR-APP-IMAGE>>",
      "logConfiguration": {
        "logDriver": "awsfirelens",
        "options": {
          "Name": "Http",
          "Host": "<<LISTENER-HOST>>",
          "URI": "/?token=<<LOG-SHIPPING-TOKEN>>&type=fargate",
          "Port": "8071",
          "tls": "on",
          "tls.verify": "off",
          "Format": "json_lines"
        }
      }
    }
  ],
  "cpu": "256",
  "executionRoleArn": "arn:aws:iam::<<AWS-ACCOUNT-ID>>:role/logzioEcsTaskExecutionRole",
  "memory": "512",
  "volumes": [ ],
  "placementConstraints": [ ],
  "networkMode": "awsvpc",
  "tags": [ ]
}
```
