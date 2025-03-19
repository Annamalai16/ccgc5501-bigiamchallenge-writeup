# Challenge 3

## Challenge Statement
Its a Secret

## Solution

1. **Confirm User and Profile**:  
   First, I confirmed the AWS user and profile I was using by running the following command:
   ```
   aws --profile cloudfoxable sts get-caller-identity
   ```
   The output showed that the correct user, `ctf-starting-user`, was being used:

   ```
   {
       "UserId": "AIDAQWHCQAQBZIFG4SU4G",
       "Account": "047719646211",
       "Arn": "arn:aws:iam::047719646211:user/ctf-starting-user"
   }
   ```

2. **Check for Secrets**:  
   I then used CloudFox to check for any secrets within the environment by running:
   ```
   cloudfox aws -p cloudfoxable secrets -v2
   ```
   The output showed several secrets and parameters. One of the secrets caught my attention, which was `/cloudfoxable/flag/its-a-secret`.

   ```
   │ SSM            │ us-west-2 │ /cloudfoxable/flag/its-a-secret       │                                  │
   ```

3. **Identify Relevant Secret**:  
   I noted the secret `/cloudfoxable/flag/its-a-secret` in the output. CloudFox also saved the command to access the secrets in the following file:
   ```
   [secrets][cloudfoxable] Loot written to [/Users/annamalai/.cloudfox/cloudfox-output/aws/cloudfoxable-047719646211/loot/pull-secrets-commands.txt]
   ```

4. **Check Access Permissions**:  
   To ensure I had the necessary permissions to access the secret, I ran the following command:
   ```
   cloudfox aws -p cloudfoxable permissions --principal ctf-starting-user -v2
   ```
   The output confirmed that the user `ctf-starting-user` had the `ssm:GetParameter` permission for the secret:

   ```
   │ Type │       Name        │ Policy  │      Policy Name      │ Effect │                             Action                              │                                  Resource                                   │ Condition │
   ├──────┼───────────────────┼─────────┼───────────────────────┼────────┼─────────────────────────────────────────────────────────────────┼─────────────────────────────────────────────────────────────────────────────┼───────────┤
   │ User │ ctf-starting-user │ Managed │ its-a-secret-policy   │ Allow  │ ssm:GetParameter                                                │ arn:aws:ssm:us-west-2:047719646211:parameter/cloudfoxable/flag/its-a-secret │ No 
   ```

5. **Retrieve the Secret Value**:  
   With the permission confirmed, I ran the following AWS CLI command to retrieve the secret value with decryption:
   ```
   aws --profile cloudfoxable --region us-west-2 ssm get-parameter --with-decryption --name /cloudfoxable/flag/its-a-secret
   ```
   The output showed the flag value:
   ```
   {
       "Parameter": {
           "Name": "/cloudfoxable/flag/its-a-secret",
           "Type": "SecureString",
           "Value": "FLAG{ItsASecret::IsASecretASecretIfTooManyPeopleHaveAccessToIt?}",
           "Version": 1,
           "LastModifiedDate": "2025-02-12T09:26:40.932000-05:00",
           "ARN": "arn:aws:ssm:us-west-2:047719646211:parameter/cloudfoxable/flag/its-a-secret",
           "DataType": "text"
       }
   }
   ```

6. **Flag Retrieved**:  
   The flag was:  
   `FLAG{ItsASecret::IsASecretASecretIfTooManyPeopleHaveAccessToIt?}`


## Reflection

* **What was your approach?**  
  My approach was to follow a systematic process of confirming the correct user, searching for secrets, and ensuring I had the necessary permissions to retrieve the secret. I used **CloudFox** to gather information about secrets and then checked if the user had the right permissions to access them.

* **What was the biggest challenge?**  
  The biggest challenge was ensuring that the user had the proper permissions to access the secret. Without the right permissions, accessing the sensitive information would have been impossible.

* **How did you overcome the challenges?**  
  I overcame the challenge by first verifying the permissions associated with the user using CloudFox’s permission-checking command. Once I confirmed that `ctf-starting-user` had `ssm:GetParameter` permissions, I proceeded to retrieve the secret.

* **What led to the breakthrough?**  
  The breakthrough came when I successfully retrieved the list of secrets from CloudFox and identified the one that seemed most likely to contain the flag. From there, it was just a matter of checking permissions and executing the command to access the secret.

* **On the blue side, how can the learning be used in future?**  
  This experience can be applied to future cloud security assessments or CTF challenges. Understanding how to use tools like CloudFox and AWS CLI to find secrets and verify permissions can be crucial for both red and blue team exercises. Additionally, this process reinforces the importance of secure secret management and access control in cloud environments.