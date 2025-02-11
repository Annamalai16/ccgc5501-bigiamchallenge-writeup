# Challenge 3

## Challenge Statement
**Admin only?** We learned from our mistakes from the past. Now our bucket only allows access to one specific admin user. Or does it?

## IAM Policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::thebigiamchallenge-admin-storage-abf1321/*"
        },
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::thebigiamchallenge-admin-storage-abf1321",
            "Condition": {
                "StringLike": {
                    "s3:prefix": "files/*"
                },
                "ForAllValues:StringLike": {
                    "aws:PrincipalArn": "arn:aws:iam::133713371337:user/admin"
                }
            }
        }
    ]
}
```
### write a short analysis about the IAM policy here

* What do I have access to?

```
The policy grants public access to the GetObject action for all objects in the thebigiamchallenge-admin-storage-abf1321 bucket, allowing anyone to read objects in it.
The ListBucket action is allowed for objects within the files/ prefix, but with a restriction on the principal.

```

* What don't I have access to?

```
I don't have the ability to list the entire bucket, as the ListBucket action is conditional on the principal being the specific admin user (arn:aws:iam::133713371337:user/admin).
Only the specified admin can list objects in the files/ prefix; others are restricted.
```

* What do I find interesting?

```The ListBucket action is quite restricted to one admin user via the aws:PrincipalArn condition, which seems secure. However, the GetObject action is left open for public access to all objects, which might be an oversight.
The challenge lies in trying to bypass these restrictions, even though it seems designed to only give the admin access.
```

## Solution
* 
```
Initially, I tried listing the objects in the files/ directory using the following command:

aws s3 ls s3://thebigiamchallenge-admin-storage-abf1321/files/

However, it failed with an AccessDenied error due to the IAM condition on aws:PrincipalArn.

```
* 
```
Realizing that I didn’t have the admin credentials, I needed to bypass the IAM authentication. That’s when I came across the --no-sign-request flag, which allows the request to be sent as an anonymous user and bypasses the IAM conditions.

I retried the same command with the --no-sign-request flag:

aws s3 ls s3://thebigiamchallenge-admin-storage-abf1321/files/ --no-sign-request

```

* 
```
This time, I was able to successfully list the files:
2023-06-07 19:15:43          42 flag-as-admin.txt
2023-06-08 19:20:01      81889 logo-admin.png
```

* 
```
To view the contents of the flag-as-admin.txt file, I executed the following command:

aws s3 cp s3://thebigiamchallenge-admin-storage-abf1321/files/flag-as-admin.txt - --no-sign-request

The contents were successfully retrieved, and I copied and pasted the flag into the portal for success.
```

* 
```
Out of curiosity, I tried listing the root of the bucket with the --no-sign-request:

aws s3 ls s3://thebigiamchallenge-admin-storage-abf1321/ --no-sign-request

But this returned an Access Denied error, which I understood to be due to the s3:prefix condition that limits access to only the files/* prefix.
```

## Reflection
* What was your approach?

```
My approach was to first understand the IAM policy's restrictions and then figure out how to bypass them. The key was realizing the potential for using the --no-sign-request flag to circumvent IAM conditions that require signed requests.
```

* What was the biggest challenge?

```
The biggest challenge was identifying how to bypass the IAM conditions without admin credentials. Once I found the --no-sign-request flag, it was much easier to proceed with the solution.
```

* How did you overcome the challenges?

```
I overcame the challenge by researching ways to bypass IAM conditions, and the --no-sign-request flag proved to be the perfect tool.
```

* What led to the break through?

```
The breakthrough came when I realized the IAM policy was restricting access to certain actions (like listing the bucket) but left the GetObject action open to the public. By using the --no-sign-request flag, I was able to bypass the authentication requirements and gain access.
```

* On the blue side, how can the learning be used to properly defend the important assets? 

```
This challenge highlights the importance of applying more granular security controls. Specifically, for highly sensitive data in a public cloud, we should:

* Avoid leaving GetObject actions open to the public.

* Use more restrictive access controls such as limiting permissions to specific users or roles, rather than using a blanket Principal: "*".

* Ensure that IAM conditions (like aws:PrincipalArn) are properly enforced for all types of access, including listing and retrieving objects.
```

