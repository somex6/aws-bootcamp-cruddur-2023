# Week 0 — Billing and Architecture

## Introduction

The bootcamp training started with guide on how to do a technical presentation on a company's proposed architecture that will be implemented so it can be reviewed by the fractional CTO of which the presentation must include a technical architectural diagram and breakdown of possible services used along with their justification. And the company wants to generally know what spend we expect to encounter and how we will ensure we keep our spending low.

The infrastructure we will be setting up which I recreated with Lucid chart is shown below:

![Cruddur Infrastructure](https://lucid.app/lucidchart/3d13a83b-ea51-42d1-8676-61031edf8512/edit?viewport_loc=-187%2C341%2C1700%2C722%2C0_0&invitationId=inv_72f4ff8a-98c8-4708-8111-3db005dbab53)

So to practicalize this, the following shows how I setup a budget of a zero-spend and a billing alarm to notify me when I'm above the budget.
Firstof, Let's setup our account by attaching MFA to root Account, create an IAM user to use instead of my root account and setup health notification

## Attaching MFA To Root Account
Entering IAM on the search bar from the AWS console and selecting IAM

click on the "Add MFA" button and that takes you to the page where to assign MFA to the root user

Clicking on the "Assign MFA" button takes us to the page where I have to configure my device to be able to authenticate with it

After which I click on the "Add MFA" button and the MFA device will be assigned

## Creating An IAM User
From the IAM dashboard, clicking on user by the left hand side pane will take us to the page where we have to add the add the user by clicking on the "Add Users" button

Entering the name and other credentials in the first step

Attaching policies to the user on the second step, I gave this user an Admin permission

Reviewing the setup and create

## Creating IAM Role
From the same IAM dashboard, clicking on "Roles" by left side of the pane will take us to the page where I have to create a role by clicking on "Create role" button

Selecting the trusted entity type in the first step - in my case I selected AWS service and use case to be EC2

Attaching permission to the role in the second step

Reveiwing and creating the role

## Setting Up EventBridge to hookup Health Dashboard to SNS and send notification when there is a service health issue

Entering SNS on the searchbox and selecting Amazon SNS will take you to the dashboard

I entered the SNS topic name and clicked next which takes me to the configuration page

To create the EnventBrigde to hook it up to Health, Entering Health on searchbox will take you to the dashboard

Clicking on the "configure" button under Amazon EventBridge rule will take you to the page where the first step will be to define the rule

Second step is to configure the event pattern

Third step is to select the target to invoke the event of which in my case is to SNS topic and the SNS topic that I created before was selected

Review and create 

Event created

## Creating A Billing Alarm

Entering Billing on the searchbox will pop out "Billing", selecting it will take you to the AWS Billing dashboard

Before alarm can be created for our estimated charges, the billing alerts has to be enabled, so that we can monitor our estimated AWS charges and create an alarm using billing metric data.
To enable it, Clicking on "Billing Preferences" in the navigation pane will take us to the Billing Preferences settings

Entering my email and selecting "Receive Billing Alerts" and then click on "Save preferences" button to enable it.

Next is to setup Billing alarm by going to the cloudwatch console.

Clicking on Alarms and selecting All alarms in the navigation pane. And then clicking on "Create alarm".

Choosing Select metric. Then In Browse tab, selecting Billing, and then choosing Total Estimated Charge.

Selecting the box for usd for the EstimatedCharges metric, and then clicking on Select metric.

For Statistic, I selected Maximum. For Period, I selected 6 hours. For Threshold type, I selected choose Static. For Whenever EstimatedCharges is . . ., I slected Greater. Then I define my threshold value that triggers my alarm. after which i click on next

Under Notification, selecting an Amazon SNS topic to be notified when my alarm is in the ALARM state and creating a new Amazon SNS topic.

Entering a name for my alarm under Name and description

Then Preview and create the alarm

## Creating Budget

From the Billing page, selecting Budgets in the navigation pane and clicking on "Create budget" will take you to the Budget page

In my case I selected the "Zero spend budget" and defined the name of the budget and entering the email to receive the notification from and then create

Budget created

