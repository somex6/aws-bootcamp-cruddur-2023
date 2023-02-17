# Week 0 — Billing and Architecture

## Introduction

The bootcamp training started with guide on how to do a technical presentation on a company's proposed architecture that will be implemented so it can be reviewed by the fractional CTO of which the presentation must include a technical architectural diagram and breakdown of possible services used along with their justification. And the company wants to generally know what spend we expect to encounter and how we will ensure we keep our spending low.

The infrastructure we will be setting up which I recreated with Lucid chart is shown below:

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/cruddur-infra.png)

[Cruddur Infrastructure](https://lucid.app/lucidchart/3d13a83b-ea51-42d1-8676-61031edf8512/edit?viewport_loc=-187%2C341%2C1700%2C722%2C0_0&invitationId=inv_72f4ff8a-98c8-4708-8111-3db005dbab53)

So to practicalize this, the following shows how I setup a budget of a zero-spend and a billing alarm to notify me when I'm above the budget.
Firstof, Let's setup our account by attaching MFA to root Account, create an IAM user to use instead of my root account and setup health notification

## Attaching MFA To Root Account
Entering IAM on the search bar from the AWS console and selecting IAM

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/10.iam%20from%20searchbox.png)

click on the "Add MFA" button and that takes you to the page where to assign MFA to the root user

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/11iam%20dashboard.png)
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/12.mfa%20dashboard.png)

Clicking on the "Assign MFA" button takes us to the page where I have to configure my device to be able to authenticate with it

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/13.assigning%20mfa.png)

After which I click on the "Add MFA" button and the MFA device will be assigned

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/15.mfa%20setup%20successfully.png)

## Creating An IAM User

- From the IAM dashboard, clicking on user by the left hand side pane will take us to the page where we have to add the add the user by clicking on the "Add Users" button

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/16.creating%20the%20user.png)

- Entering the name and other credentials in the first step

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/17.creating%20the%20user%202.png)

- Attaching policies to the user on the second step, I gave this user an Admin permission

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/18.attaching%20policy.png)

- Reviewing the setup and create

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/19.review%20and%20create.png)

- User created

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/20.user%20created.png)

## Creating IAM Role

- From the same IAM dashboard, clicking on "Roles" by left side of the pane will take us to the page where I have to create a role by clicking on "Create role" button

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/21.role%20dashboard.png)

- Selecting the trusted entity type in the first step - in my case I selected AWS service and use case to be EC2

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/22.create%20role.png)

- Attaching permission to the role in the second step

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/23.attaching%20policy%20to%20it.png)

- Giving the role a name then Reveiwing and creating the role

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/24.naming%20the%20role.png)

- Role created

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/25.role%20created.png)

## Setting Up EventBridge to hookup Health Dashboard to SNS and send notification when there is a service health issue

- Entering SNS on the searchbox and selecting Amazon SNS will take you to the dashboard

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/26.sns%20dashboard.png)

- I entered the SNS topic name and clicked next which takes me to the configuration page

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/27.create%20sns%20topic.png)

- To create the EnventBrigde to hook it up to Health, Entering Health on searchbox will take you to the dashboard

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/28.aws%20health%20dashboard.png)

- Clicking on the "configure" button under Amazon EventBridge rule will take you to the page where the first step will be to define the rule

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/29.define%20rule.png)

- Second step is to configure the event pattern

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/30.build%20event%20pattern.png)

- Third step is to select the target to invoke the event of which in my case is to SNS topic and the SNS topic that I created before was selected

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/31.selecting%20targets.png)

- Review and create 

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/32.review%20and%20create.png)

- Event created

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/33.eventbridge%20created.png)

## Creating A Billing Alarm

- Entering Billing on the searchbox will pop out "Billing", selecting it will take you to the AWS Billing dashboard

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/34.budjet%20dashboard.png)

- Before alarm can be created for our estimated charges, the billing alerts has to be enabled, so that we can monitor our estimated AWS charges and create an alarm using billing metric data.
- To enable it, Clicking on "Billing Preferences" in the navigation pane will take us to the Billing Preferences settings
- Entering my email and selecting "Receive Billing Alerts" and then click on "Save preferences" button to enable it.

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/37.enable%20billing%20alarm.png)

- Next is to setup Billing alarm by going to the cloudwatch console.

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/37.cloudwatch%20dashboard.png)

- Clicking on Alarms and selecting All alarms in the navigation pane. And then clicking on "Create alarm".

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/38.create%20alarm.png)

- Choosing Select metric. Then In Browse tab, selecting Billing, and then choosing Total Estimated Charge.

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/39.creating%20metric.png)

- Selecting the box for usd for the EstimatedCharges metric, and then clicking on Select metric.

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/41.setting%20metric.png)

- For Statistic, I selected Maximum. For Period, I selected 6 hours. For Threshold type, I selected choose Static. For Whenever EstimatedCharges is . . ., I slected Greater. Then I define my threshold value that triggers my alarm. after which i click on next

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/42.setting%20metric%202.png)

- Under Notification, selecting an Amazon SNS topic to be notified when my alarm is in the ALARM state and creating a new Amazon SNS topic.

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/43.adding%20notification.png)

- Entering a name for my alarm under Name and description

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/44.naming%20the%20alert.png)

- Then Preview and create the alarm

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/45.review%20and%20create.png)

- Alarm created

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/46.alarm%20created.png)

## Creating Budget

- From the Billing page, selecting Budgets in the navigation pane and clicking on "Create budget" will take you to the Budget page

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/34.budjet%20dashboard.png)

- In my case I selected the "Zero spend budget" and defined the name of the budget and entering the email to receive the notification from and then create

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/35.setting%20up%20budget.png)

- Budget created

![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/36.budget%20created.png)

## Creating Budget From CLI Using Gitpod

- Updating our .gitpod.yml to include the following task.
```
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```

- Setting my AWS credentials to be able to execute AWSCLI
```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```
- To make Gitpod remember these credentials when relaunching my workspaces
```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```
- Executing the following command to test it: `aws sts get-caller-identity`
- Activating the Gitpod environment from aws bootcamp repo
- Creating a folder and sub folder called aws/json
- Creating the following files in the json subfolder:

**budget-notifications-with-subscribers.json** file
```
[
  {
      "Notification": {
          "ComparisonOperator": "GREATER_THAN",
          "NotificationType": "ACTUAL",
          "Threshold": 80,
          "ThresholdType": "PERCENTAGE"
      },
      "Subscribers": [
          {
              "Address": "example@example.com",
              "SubscriptionType": "EMAIL"
          }
      ]
  }
]
```

**budget.json** file
```
{
  "BudgetLimit": {
      "Amount": "100",
      "Unit": "USD"
  },
  "BudgetName": "Example Tag Budget",
  "BudgetType": "COST",
  "CostFilters": {
      "TagKeyValue": [
          "user:Key$value1",
          "user:Key$value2"
      ]
  },
  "CostTypes": {
      "IncludeCredit": true,
      "IncludeDiscount": true,
      "IncludeOtherSubscription": true,
      "IncludeRecurring": true,
      "IncludeRefund": true,
      "IncludeSubscription": true,
      "IncludeSupport": true,
      "IncludeTax": true,
      "IncludeUpfront": true,
      "UseBlended": false
  },
  "TimePeriod": {
      "Start": 1477958399,
      "End": 3706473600
  },
  "TimeUnit": "MONTHLY"
}
```
- Fetching my Account ID and saving it in environment variable to use in a command:
```
export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
```

- Executing the following command to create the budget:
```
aws budgets create-budget \
    --account-id $AWS_Account_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/46.creating%20budget%20from%20cli.png)

**Budget Created**
![](https://github.com/somex6/aws-bootcamp-cruddur-2023/blob/my-rough/journal/images/week0/47.budget%20created.png)

