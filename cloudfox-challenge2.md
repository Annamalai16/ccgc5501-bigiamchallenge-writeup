## Solution - Detailed Steps

1. **Enable Bastion Host in Terraform**  
   In the `terraform.tfvars` file located in the `cloudfoxable/aws/` directory, enable the bastion host by adding:
   ```hcl
   bastion_enabled = true
   ```
   
2. **Run Terraform Apply**  
   Execute `terraform apply` to apply the changes. This will throw an error indicating invalid JSON format in the policy files due to the `USER_IP` variable.

3. **Identify the Issue with IP Fetching**  
   The issue was traced to the `USER_IP` variable, which was supposed to dynamically fetch the public IP using:
   ```hcl
   data "http" "current_ip" {
     url = "https://ifconfig.me"
   }

   locals {
     user_ip = var.user_ip != "" ? chomp(var.user_ip) : chomp(data.http.current_ip.body)
   }
   ```
   The response returned was HTML instead of the expected IP, breaking the JSON format.

4. **Fix the IP Issue**  
   Manually retrieve your public IP using `curl ifconfig.me` and hardcode it into the `terraform.tfvars` file.

5. **Rerun Terraform Apply**  
   With the correct IP now hardcoded, rerun `terraform apply`, and it should apply without errors.

6. **Install Cloudfox**  
   Install Cloudfox by running:
   ```bash
   brew install cloudfox
   ```

7. **Find Instance ID**  
   Run the command to list instances and find the instance ID:
   ```bash
   cloudfox aws -p cloudfoxable instances -v2
   ```

8. **Obtain the SSM Command**  
   Access the loot file generated and stored locally, which contains the command needed to access the instance via AWS Systems Manager (SSM).

9. **Install Session Manager Plugin**  
   Follow the official AWS documentation to install the Session Manager plugin on your machine.

10. **Connect to the Instance**  
   Use the command from the loot file to SSM into the instance.

11. **Check Permissions**  
   Run the following command to check the instance’s permissions:
   ```bash
   cloudfox aws -p cloudfoxable permissions --principal [NameOfInstanceRole]
   ```
   Discover that the instance has access to an S3 bucket.

12. **Access the S3 Bucket**  
   List the contents of the S3 bucket:
   ```bash
   aws s3 ls s3://<bucket-name>
   ```
   Find the `flag.txt` file.

13. **Retrieve the Flag**  
   Display the contents of the `flag.txt` file to get the flag.
   List the contents of the S3 bucket:
   ```bash
   aws s3 cp s3://<bucket-name>/file.txt -
   ```

14. **Submit the Flag**  
   Paste the flag in the Cloudfoxable portal to complete the challenge.

## Reflection  
### What was your approach?  
My approach was methodical: I started by investigating the Terraform error and followed a debugging process to resolve it. Once I identified the issue with fetching the IP address dynamically, I manually inputted my IP to move forward. After Terraform applied successfully, I used Cloudfox to find the instance ID and then followed the steps to access the machine, retrieve permissions, and finally obtain the flag.

### What was the biggest challenge?  
The biggest challenge was the issue with fetching the IP address dynamically in Terraform. Initially, I couldn't understand why the variable `USER_IP` was returning HTML instead of my public IP. It took a lot of time debugging and trying different methods before I realized the response from `https://ifconfig.me` was not in the expected format.

### How did you overcome the challenges?  
I overcame the challenge by manually retrieving my public IP using `curl ifconfig.me` and hardcoding it into the Terraform variables. This bypassed the issue with the dynamic IP fetching and allowed Terraform to proceed without errors.

### What led to the breakthrough?  
The breakthrough came when I decided to check if the variables were resolving correctly and traced the issue to the public IP retrieval logic. Once I realized that the response from `ifconfig.me` was an HTML page instead of my IP address, I immediately figured out that manually hardcoding the IP would allow me to proceed.

### On the blue side, how can the learning be used?  
This experience highlights the importance of validating external dependencies, especially when working with cloud infrastructure. Ensuring that your dynamic inputs (like public IP retrieval) are reliable and robust can prevent issues in automation pipelines. From a defense perspective, it's crucial to monitor and validate incoming data, particularly when dealing with external services, to prevent unexpected behavior like malformed inputs or unexpected content that could lead to system failures or vulnerabilities. Regularly reviewing Terraform configurations, error logs, and external service interactions can help identify potential weak points early and improve resilience in production environments.