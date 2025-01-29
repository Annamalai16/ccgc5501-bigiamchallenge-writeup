# Challenge 2

## Challenge Statement
We created our own analytics system specifically for this challenge. We think it's so good that we even used it on this page. What could go wrong? Join our queue and get the secret flag.

## IAM Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "sqs:SendMessage",
                "sqs:ReceiveMessage"
            ],
            "Resource": "arn:aws:sqs:us-east-1:092297851374:wiz-tbic-analytics-sqs-queue-ca7a1b2"
        }
    ]
}
```
### write a short analysis about the IAM policy here
```
* What do I have access to?

1. SQS SendMessage Action
    
We can send messages to the specified SQS queue. This means you we can use the SendMessage API action to add messages to the queue

2. SQS ReceiveMessage Action

We can receive messages from the specified SQS queue. This means we can use the ReceiveMessage API action to fetch messages from the queue to process or handle them.

* What don't I have access to?

We dont have access to any action other than sending or receiving msgs in a queue.
Eg. 
* DeleteMessage
* ChangeMessageVisibility
* PurgeQueue

The send or receive is restricted to that particular SQS queue. We wont be able to do the actions in any other queue.

* What do I find interesting?
```

## Solution
* Enter the AWS CLI command to receive the messages in a SQS service
* Got the messages as the response.
* There was only one message, and a link to an html page was present in the body of the ony message.
* When opened the link, The flag value was present.
* Copied the value and pasted it in the portal, it gave success.

## Reflection
* What was your approach?

```
The first thing was to identify the servie involved to find the flag value.
After finding that its, SQS service, read the documentation for the command to receive messages from a SQS queue. When executed the command, got the link in the only message that was there in the queue. When opened the link, got the flag value.

```

* What was the biggest challenge?

```
Identified that its an SQS service through the IAM Policy. After identifying that it was SQS service, Didnt know what to do next. Was kind of blank on what to do the next step. Proceeding after that was relatively the biggest challenge. 
```

* How did you overcome the challenges?

```
Reading the documentation of the SQS service in AWS and the AWS CLI commands to Send and Receieve messages in the queue was the way to overcome the challenge I faced while solving it.
 
```

* What led to the break through?

```
When dig deep through the IAM policy, I had access to send/receieve messages, That was the biggest breakthrough to know that one of this would give the flag value.
```

* On the blue side, how can the learning be used to properly defend the important assets? 

```
"Effect": "Allow",
"Principal": "*",

These two things, gives access to anyone on the Internet.
The principal should not be * anytime. Its a big vulnerablility to the security of the resources.
IAM groups and users have to be mentioned specifically for the principal key.
```
