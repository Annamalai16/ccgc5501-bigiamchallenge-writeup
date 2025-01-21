# Challenge 1

## Challenge Statement
**Buckets of Fun** - We all know that public buckets are risky. But can you find the flag?

## IAM Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b/*"
        },
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::thebigiamchallenge-storage-9979f4b",
            "Condition": {
                "StringLike": {
                    "s3:prefix": "files/*"
                }
            }
        }
    ]
}
```
### write a short analysis about the IAM policy here

* What do I have access to?
```
I had access to read/download the contents from the S3 Bucket
and List files access to /files/ directory
```

* What don't I have access to?
```
I dont have the access to write, update and delete
and no access to list the files other than /files/ directory
```

* What do I find interesting?
```
Though I did not have access to list the files other than /files/, I simply tried to check and i was able to list.
According to the policy it shouldnt have listed and thrown me Access denied error. 
It made even more interesting and also made me question the concept I understood
```

* etc
```
When i dug even more to find the reason, I found that there might be some other policy that allows to make that happen.
I tried fetching the other policies using AWS CLI commands, but the access was denied.
So i just assumed it must be due to the same and left.
```

## Solution
* Read the AWS CLI Documentation
* Listed the files in the public AWS S3 bucket
* Found the flag1.txt file
* cat command was not possible, so did it using cp and - to view the contents of the file
* Got the flag value and cross verified in the portal, it was right!


## Reflection
* What was your approach?
```
As I was not introduced to AWS CLI before, every single thing was new to me. So had to surf a lot, understand and then proceed
Read the official documentation, which gave me the base idea on what is AWS CLI and how to proceed with it.
```

* What was the biggest challenge?
```
The official documentation had so much in it, but what i wanted to learn for this particular change was less. 
The real challenge was to filter out and find the commands that will solve the challenge
```

* How did you overcome the challenges?
```
I spent more time on it, surfed and understood the basics of it. The more I understood and got familiar I was able to relate and find out whoch commands can be be used where. I got the use cases of each command and i knew the steps to get the flag. So with both, I eventually overcame it.
```

* What led to the break through?
```
The official documentation helped me so much, I was able to get IAM policies, AWS CLI, commands to manage the S3 bucket and some time added to it was the break through
```

* On the blue side, how can the learning be used to properly defend the important assets? 
```
The policies has to be defined properly and given access to only specific roles and users. It is never okay to leave it publicly available like how it it configured for that S3 bucket. Any user who is connected to internet can access that bucket, which is very vulnerable.
```