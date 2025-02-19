# Challenge 1

## Challenge Statement
First Flag

## Solution
1. **Created a Free AWS Account**.

2. **Created a non-root user** with administrative access. Using a non-root user is a best practice to limit exposure and ensure better security control.

3. **Generated an access key for the newly created user**. This key was essential for programmatic access to AWS resources via the CLI.

4. **Installed AWS CLI, Terraform** and ensured the binary was in the system path. Terraform was used to automate infrastructure deployment and configuration.

5. Verify the Installation using below commands
```bash
aws help
terraform help
```
It gave the options and the parameters that can be passed with each command

6. **Configure AWS CLI** with the account that we created in first step
```bash
aws configure
```
It asked for the region, Access Key, Client ID. 

7. To check if the use was set correctly, executed the below command
```bash
aws sts get-caller-identity
```
It gave an output in the below format
```json
{
    "UserId": "<UID>",
    "Account": "<AID>",
    "Arn": "<ARN>"
}
```
confirmed that my credentials were correctly configured and allowed me to proceed with confidence.

8. Cloned the CloudFoxable repository
```
git clone https://github.com/BishopFox/cloudfoxable
```
It Downloaded the repository files into a local directory.

9. Navigated to the AWS directory and Copied the example Terraform variables file
```bash
cp terraform.tfvars.example terraform.tfvars
```

10. Initialized Terraform
```bash
terraform init
```
Downloaded Terraform providers and initialized the working directory.

11. Edited the variable file to add my local AWS PROFILE
```tf
aws_local_profile = "<MY_LOCAL_PROFILE_NAME>"
```

12. Ran a plan check to see the resources that were going to be created
```bash
terraform plan
```
It displayed the actions Terraform would take to create the infrastructure.

13. Created the Resources in AWS
```bash
terraform apply
```
Created AWS resources and displayed key information, including credentials for ctf-starting-user.
```
CTF_Start_User_Access_Key_Id = "AKIAQWHCQAQBYNU4MEMG"
CTF_Start_User_Secret_Access_Key = <sensitive>
```

14. Created a new profile as instructed in the output using the below command
```bash
echo "" >> ~/.aws/credentials && echo "[cloudfoxable]" >> ~/.aws/credentials && echo "aws_access_key_id = `terraform output -raw CTF_Start_User_Access_Key_Id`" >> ~/.aws/credentials && echo "aws_secret_access_key = `terraform output -raw CTF_Start_User_Secret_Access_Key`" >> ~/.aws/credentials && echo "region = us-west-2" >> ~/.aws/credentials
```

15. Set the Env variables
```
AWS_ACCESS_KEY_ID=`terraform output CTF_Start_User_Access_Key_Id`
AWS_SECRET_ACCESS_KEY=`terraform output CTF_Start_User_Secret_Access_Key`
AWS_REGION=us-west-2
```

16. Verified that the credentials are working
```bash
aws sts get-caller-identity --profile cloudfoxable
```

17. Below was the flag value in the output
```
FLAG{congrats_you_are_now_a_terraform_expert_happy_hunting}
```
Copy pasted it. And it gave `Success`.



## Reflection
* What was your approach?

```
My approach was to methodically follow each step, ensuring proper setup of AWS CLI, Terraform, and security tools before proceeding. I paid close attention to Terraform output and leveraged enumeration tools to uncover useful information.
```

* What was the biggest challenge?

```
Ensuring all required tools were installed and configured correctly, particularly managing IAM permissions and switching profiles effectively.
```
* How did you overcome the challenges?

```
I used debugging commands like aws sts get-caller-identity to verify my configuration, read Terraform output carefully, and leveraged AWS enumeration tools to locate misconfigurations.
```

* What led to the break through?

```
The key breakthrough came from analyzing Terraform output, which hinted at an accessible S3 bucket. By using AWS CLI, I was able to list its contents and retrieve the flag.
```

* On the blue side, how can the learning be used to properly defend the important assets? 

```
This experience reinforced the importance of:

* Enforcing least privilege access for IAM users.

* Auditing and restricting public access to S3 buckets.

* Regularly reviewing Terraform configurations to prevent misconfigurations.

* Monitoring cloud environments for unauthorized access and exposed sensitive data.
```
