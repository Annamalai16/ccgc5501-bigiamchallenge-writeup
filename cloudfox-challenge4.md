# Cloudfoxable Challenge 4 - Its Another Secret

## Solution

To solve this challenge, my first step was to create a new profile in AWS to assume the `Ertz` role. I edited my AWS config file (`~/.aws/config`) as follows:

```
[profile ertz]
region = us-west-2
role_arn = arn:aws:iam::047719646211:role/Ertz
source_profile = cloudfoxable
```

After saving the file, I verified that I could assume the `Ertz` role by running the following command:

```
aws --profile ertz sts get-caller-identity
```

The output confirmed that I was successfully assuming the role:

```
{
    "UserId": "AROAQWHCQAQBTDUKGU3W5:botocore-session-1743032685",
    "Account": "047719646211",
    "Arn": "arn:aws:sts::047719646211:assumed-role/Ertz/botocore-session-1743032685"
}
```

Next, I attempted to list the secrets accessible by this profile using the command:

```
aws --profile ertz secretsmanager list-secrets
```

However, I received an access denied error:

```
An error occurred (AccessDeniedException) when calling the ListSecrets operation: User: arn:aws:sts::047719646211:assumed-role/Ertz/botocore-session-1743032685 is not authorized to perform: secretsmanager:ListSecrets because no identity-based policy allows the secretsmanager:ListSecrets action
```

To investigate the permissions of the `Ertz` role, I used the `cloudfox` tool, as I had done in previous challenges, but this time specifying the `ertz` principal:

```
cloudfox aws -p cloudfoxable permissions --principal ertz -v2
```

The output showed that the `Ertz` role was allowed to perform the `ssm:GetParameter` action on the `cloudfoxable` secret:

```
| Type │ Name │ Policy  │        Policy Name        │ Effect │      Action      │                                     Resource                                      │ Condition │
├──────┼──────┼─────────┼───────────────────────────┼────────┼──────────────────┼───────────────────────────────────────────────────────────────────────────────────┼───────────┤
│ Role │ Ertz │ Managed │ its-another-secret-policy │ Allow  │ ssm:GetParameter │ arn:aws:ssm:us-west-2:047719646211:parameter/cloudfoxable/flag/its-another-secret │ No 
```

From my previous experience, I knew that `ssm:GetParameter` was the action I needed to use to retrieve the secret. I ran the following command to fetch the secret value:

```
aws --profile ertz --region us-west-2 ssm get-parameter --with-decryption --name /cloudfoxable/flag/its-another-secret
```

The output returned the flag:

```
{
    "Parameter": {
        "Name": "/cloudfoxable/flag/its-another-secret",
        "Type": "SecureString",
        "Value": "FLAG{ItsAnotherSecret::ThereWillBeALotOfAssumingRolesInThisCTF}",
        "Version": 1,
        "LastModifiedDate": "2025-02-12T09:26:39.812000-05:00",
        "ARN": "arn:aws:ssm:us-west-2:047719646211:parameter/cloudfoxable/flag/its-another-secret",
        "DataType": "text"
    }
}
```
The value was the secret flag

## Reflection

* **What was your approach?**

  My approach was systematic: I first assumed the role, verified the permissions using the AWS CLI, and then used the available actions and roles to get access to the secret.

* **What was the biggest challenge?**

  The biggest challenge was figuring out what permissions the `Ertz` role had. Initially, I thought I might need to access the Secrets Manager, but the error I encountered showed that the role wasn't authorized for that action.

* **How did you overcome the challenges?**

  I overcame this by using the `cloudfox` tool to inspect the permissions of the `Ertz` role. This helped me understand that the role was authorized for `ssm:GetParameter`, leading me to retrieve the secret using AWS Systems Manager.

* **What led to the breakthrough?**

  The breakthrough came when I realized that the role's permission to access `ssm:GetParameter` was key. By retrieving the parameter from AWS Systems Manager, I was able to access the flag.

* **On the blue side, how can the learning be used in the future?**

  This experience reinforced the importance of understanding and inspecting role permissions. In future engagements, I will be more efficient in using tools like `cloudfox` to analyze permissions and track down hidden secrets or flags more quickly.