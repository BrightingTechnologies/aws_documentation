## Creating Alarms With CDK

## Purpose

This document provides guidelines for creating CloudWatch alarms using AWS CDK (Cloud Development Kit). It includes an example of a reusable CDK construct that creates a CloudWatch alarm to monitor Lambda function errors and sends notifications via SNS when the error count exceeds the specified threshold.

## Contents

1. [Purpose](#purpose)
2. [Creating Alarms With CDK](#creating-alarms-with-cdk)
   - [Example CDK Construct](#example-cdk-construct)
   - [Integration with Incident Management Services](#integration-with-incident-management-services)


Following is an example of an Alarm as a CDK construct.

Code defines a reusable CDK construct that creates a CloudWatch alarm to monitor Lambda function errors and sends notifications via SNS when the error count exceeds the specified threshold.

```typescript

export interface InvocationErrorsAlarmProps {
  lambda: LambdaNodeJs;
  componentName: string;
  project: string;
  threshold?: number;
  evaluationPeriods?: number;
  actionTopic: Topic;
}

const NAME = 'InvocationErrors';

export class InvocationErrorsAlarm extends Construct {
  constructor(scope: Construct, id: string, props: InvocationErrorsAlarmProps) {
    super(scope, id);

    const alarm = props.lambda
      .metricErrors({statistic: 'sum'})
      .createAlarm(this, NAME, {
        alarmName: `${props.lambda.functionName}-${NAME}`,
        threshold: props.threshold || 3,
        evaluationPeriods: 2,
        treatMissingData: TreatMissingData.NOT_BREACHING,
      });

    alarm.addAlarmAction(new SnsAction(props.actionTopic));
  }
}
```

When an alarm triggers, it sends an event to an SNS topic. This example uses a Lambda listener that executes an API call to PagerDuty to create an incident. Similarly, other incident management services can be integrated in the same way by modifying the Lambda function to call their respective APIs. Additionally, AWS provides native integrations for various incident management tools, such as Opsgenie and ServiceNow, through EventBridge and other AWS services.

