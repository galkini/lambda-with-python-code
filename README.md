![Alt text](/Lambda_with_Python_Code.png)

# AWS Lambda and S3: Organize S3 Objects by Date

This repository provides a solution for automatically organizing AWS S3 objects into date-based folders using AWS Lambda and Python.

## Project Overview

The solution utilizes an AWS Lambda function to move any newly uploaded objects in an S3 bucket into a folder named with the current date (format: YYYYMMDD). This solution helps in maintaining an organized structure within the S3 bucket.

### Key Features

- **AWS Lambda Function:** Automatically triggered by new object uploads to organize them into date-specific folders.
- **Python Script:** Handles the logic for moving the objects.
- **AWS S3 Bucket:** The storage for both uploaded objects and organized folders.
- **IAM Roles and Permissions:** Secure access management for AWS services.
  
## Repository Contents

- **Reference Diagram**: A visual representation of the architecture.
- **Deployment Scripts**: Necessary scripts to set up and deploy the solution.

## Prerequisites

Ensure you have the following installed on your computer:

- [Python](https://www.python.org/downloads/)
- [Boto3](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/quickstart.html)
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- [Visual Studio Code (VSC)](https://code.visualstudio.com/)
- Extensions for VSC: Python, Jupyter, Pylance
- [Git](https://git-scm.com/)
- [GitHub Account](https://github.com/)

## Setup Instructions

Follow the steps below to set up the project:

1. **Install Python**
   ```sh
   # Install Python (Assuming Python 3.7+ is required)
   sudo apt-get update
   sudo apt-get install python3 python3-pip
   ```

2. **Install Boto3**
   ```sh
   pip install boto3
   ```

3. **Create IAM User with Programmatic Access**

4. **Install AWS CLI**
   ```sh
   curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
   sudo installer -pkg AWSCLIV2.pkg -target /
   ```

5. **Configure AWS CLI**
   ```sh
   aws configure
   # Enter your Access Key ID, Secret Access Key, region, and output format
   ```

6. **Install Visual Studio Code and Necessary Extensions**

7. **Sign Up and Set Up GitHub and Git**
   ```sh
   sudo apt-get install git
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   # Add the SSH key to GitHub
   ```

8. **Create S3 Buckets**
   - `organize-s3-objects` for storing the files to be sorted.
   - Another bucket to store the Lambda function code.

9. **Clone the GitHub Repository**
   ```sh
   git clone git@github.com:your-username/python-projects.git
   cd python-projects
   ```

10. **Write and Convert the Python Script**

11. **Create IAM Role for Lambda**

12. **Create Lambda Function and Trigger**
   - Upload the Python script to Lambda.
   - Configure the Lambda handler and set up a trigger for the S3 bucket.

13. **Test the Lambda Function**
   - Upload new files to the `organize-s3-objects` bucket.
   - Verify the objects are moved to the appropriate folder.

14. **Clean Up Resources**
   - Delete the Lambda function and S3 buckets to avoid any charges.

## Python Script

```python
import boto3
from datetime import datetime

def lambda_handler(event, context):
    s3_client = boto3.client('s3')
    bucket_name = "organize-s3-objects"
    today = datetime.today()
    todays_date = today.strftime("%Y%m%d")
    
    # returns metadata regarding the objects in a bucket. Includes objects names, and date of creation
    list_objects_response = s3_client.list_objects_v2(Bucket=bucket_name)
    get_contents = list_objects_response.get("Contents", [])
    get_all_s3_objects_and_folder_names = [item.get("Key") for item in get_contents]
    
    directory_name = todays_date + "/"
    if directory_name not in get_all_s3_objects_and_folder_names:
        s3_client.put_object(Bucket=bucket_name, Key=(directory_name))
    
    for item in get_contents:
        object_creation_date = item.get("LastModified").strftime("%Y%m%d") + "/"
        object_name = item.get("Key")
        if object_creation_date == directory_name and "/" not in object_name:
            s3_client.copy_object(Bucket=bucket_name, CopySource=bucket_name+"/"+object_name, Key=directory_name+object_name)
            s3_client.delete_object(Bucket=bucket_name, Key=object_name)
```
