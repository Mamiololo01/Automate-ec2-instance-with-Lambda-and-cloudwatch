# Automate-ec2-instance-with-Lambda-and-cloudwatch
Step by step guides to automating ec2 instances to start and stop using Lambda and event notification using cloud watch

Create an IAM policy and execution role for your Lambda function

1.    Create an IAM policy using the JSON policy editor. Copy and paste the following JSON policy document into the policy editor:


2.    Create an IAM role for Lambda.

Important: When attaching a permissions policy to Lambda, make sure that you choose the IAM policy that you just created.

Create Lambda functions that stop and start your EC2 instances
1.    Open the Lambda console, and then choose Create function.

2.    Choose Author from scratch.

3.    Under Basic information, add the following information:

For Function name, enter a name that identifies it as the function that's used to stop your EC2 instances. For example, "StopEC2Instances".
For Runtime, choose Python 3.9.
Under Permissions, expand Change default execution role.
Under Execution role, choose Use an existing role.
Under Existing role, choose the IAM role that you created.

4.    Choose Create function.

5.    Under Code, Code source, copy and paste the following code into the editor pane in the code editor: (lambda_function). This code stops the EC2 instances that you identify.

Example function code to stop EC2 instances

import boto3
region = 'us-west-1'
instances = ['i-12345cb6de4f78g9h', 'i-08ce9b2d7eccf6d26']
ec2 = boto3.client('ec2', region_name=region)

def lambda_handler(event, context):
    ec2.stop_instances(InstanceIds=instances)
    print('stopped your instances: ' + str(instances))
Important: For region, replace "us-west-1" with the AWS Region that your instances are in. For instances, replace the example EC2 instance IDs with the IDs of the specific instances that you want to stop and start.

6.    Choose Deploy.

7.    On the Configuration tab, choose General configuration, Edit. Set Timeout to 10 seconds, and then choose Save.

Note: Configure the Lambda function settings as needed for your use case. For example, to stop and start multiple instances, you might use a different value for Timeout and Memory.

8.    Repeat steps 1-7 to create another function. Complete the following steps differently so that this function starts your EC2 instances:

In step 3, enter a different Function name than the one that you used before. For example, "StartEC2Instances".
In step 5, copy and paste the following code into the editor pane in the code editor: ( lambda_function).

Example function code to start EC2 instances

import boto3

region = 'us-west-1'

instances = ['i-12345cb6de4f78g9h', 'i-08ce9b2d7eccf6d26']

ec2 = boto3.client('ec2', region_name=region)

def lambda_handler(event, context):

ec2.start_instances(InstanceIds=instances)

print('started your instances: ' + str(instances))

Important: For region and instances, use the same values that you used for the code to stop your EC2 instances.

Test your Lambda functions

1.    Open the Lambda console, and then choose Functions.

2.    Choose one of the functions that you created.

3.    Choose the Code tab.

4.    In the Code source section, choose Test.

5.    In the Configure test event dialog box, choose Create new test event.

6.    Enter an Event name. Then, choose Create.

Note: Don't change the JSON code for the test event. The function doesn't use it.

7.    Choose Test to run the function.

8.    Repeat steps 1-7 for the other function that you created.

Check the status of your EC2 instances

AWS Management Console

You can check the status of your EC2 instances before and after testing to confirm that your functions work as expected.

AWS CloudTrail

You can use CloudTrail to check for events to confirm that the Lambda function stopped or started the EC2 instance.

1.    Open the CloudTrail console.

2.    In the navigation pane, choose Event history.

3.    Choose the Lookup attributes dropdown list, and then choose Event name.

4.    In the search bar, enter StopInstances to review the results.

5.     In the search bar, enter StartInstances to review the results.

<img width="991" alt="Screenshot 2023-06-08 at 19 03 24" src="https://github.com/Mamiololo01/Automate-ec2-instance-with-Lambda-and-cloudwatch/assets/67044030/09dbc199-4170-4fb2-95af-21d52b719193">

<img width="1032" alt="Screenshot 2023-06-08 at 19 03 42" src="https://github.com/Mamiololo01/Automate-ec2-instance-with-Lambda-and-cloudwatch/assets/67044030/f54d635f-1753-4702-973f-fb60328ce0ef">

If there are no results, then the Lambda function didn't stop or start the EC2 instances.

Create EventBridge rules that run your Lambda functions

1.    Open the EventBridge console.

2.    Select Create rule.

3.    Enter a Name for your rule, such as "StopEC2Instances". (Optional) Enter a description for the rule in Description.

4.    For Rule type, choose Schedule, and then choose Continue in EventBridge Scheduler.

5.    For Schedule pattern, choose Recurring schedule. Complete one of the following steps:

Under Schedule pattern, for Occurrence, choose Recurring schedule. Then complete one of the following steps:

When Schedule type is Rate-based schedule, for Rate expression, enter a rate value and choose an interval of time in minutes, hours, or days.

When Schedule type is Cron-based schedule, for Cron expression, enter an expression that tells Lambda when to stop your instance. For information on expression syntax, see Schedule expressions for rules.

Note: Cron expressions are evaluated in UTC. Make sure that you adjust the expression for your preferred time zone.

6.    In Select targets, choose Lambda function from the Target dropdown list.

7.    For Function, choose the function that stops your EC2 instances.

8.    Choose Skip to review and create, and then choose Create.

9.    Repeat steps 1-8 to create a rule to start your EC2 instances. Complete the following steps differently:

Enter a name for your rule, such as "StartEC2Instances".
(Optional) In Description, enter a description for your rule, such as "Starts EC2 instances every morning at 7 AM."

In step 5, for Cron expression, enter an expression that tells Lambda when to start your instances.

In step 7, for Function, choose the function that starts your EC2 instances.
