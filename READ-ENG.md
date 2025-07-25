# 1. What is Cloud Native Qumulo on AWS (CNQ on AWS)?
- CNQ on AWS is Qumulo’s cloud product that fully inherits the strengths of the existing on-premises Qumulo.
- As you add more nodes, you gain scale-out performance, and all provisioned storage is treated as a single file system.
  - A cluster can store up to 18 quintillion files, and up to 4.3 billion files can be stored in a single directory.
  - Supports multi-protocol access including NFS, SMB, S3, FTP, and REST.
- EC2 is used as the compute node, and S3 is used as the backend storage, allowing for flexible configuration changes.
  - Scale-out/in: Add or remove compute nodes in the CNQ cluster.
  - Scale-up/down: Replace compute nodes with higher or lower instance types.
- Global Namespace feature allows namespace expansion across other CNQ clusters and on-prem Qumulo clusters.
- CNQ product page: https://qumulo.com/ko/product/aws/

# 2. Target Architecture
- Build a Cloud Native Qumulo (CNQ) cluster on AWS using Terraform from a Windows OS environment.
- This guide assumes a test environment; modify accordingly for production use.
- Target architecture:
  - Except for CNQ and the S3 backend storage, all other components in the diagram must be pre-configured.
    - <a href="images/aws목표구성.png"> <img src="images/aws목표구성.png" alt="aws target architecture" width="50%"> </a>

# 3. Prepare CNQ Installation Files
- Contact your Qumulo representative and obtain the following three files corresponding to the version you want to install:
  - aws-terraform-cnq-<x.y>.zip (x.y is the version)
  - host_configuration.tar.gz
  - qumulo-core.deb
 
# 4. Upload CNQ Installation Files to S3 Bucket (CNQ Utility Bucket)
- **(Important) The S3 bucket created in this step is separate from the S3 backend storage used by CNQ. This bucket is solely for uploading installation files.**
- **(Important) It is recommended not to delete this bucket even after the installation is complete.**
- Log in to the AWS Management Console and navigate to the S3 service.
- Create a bucket and upload files as shown in the example below:
  - (Example) Amazon S3 > Buckets > ypark-cnq-utilbucket > cnq-install-files/ > qumulo-core-install/ > 7.2.3.1/
    - `ypark-cnq-utilbucket`: Any name you prefer
    - `cnq-install-files/`: Any name you prefer
    - `qumulo-core-install/`: Must be entered exactly as shown
    - `7.2.3.1/`: Enter the exact CNQ version you intend to install (e.g., 7.2.3.1/)
- Upload the provided `qumulo-core.deb` file to the CNQ version directory.
- Upload the provided `host_configuration.tar.gz` file to the same directory **without extracting the archive**.
- Example image of completed upload:
  - <a href="images/cnq install file.png"> <img src="images/cnq install file.png" alt="cnq install file" width="45%"> </a>

# 5. Required AWS Preconfiguration
- **(Important) The following two conditions must be met, or the CNQ installation will fail:**
  - The **Private subnet** where CNQ will be deployed **must have internet access** (e.g., via a NAT gateway or alternative routing) in order to install required packages.
  - The **Private subnet** must be able to communicate with the S3 backend storage via **S3 Gateway Endpoint**. This ensures that S3 traffic is routed through the AWS internal network rather than the internet, **greatly reducing S3 data transfer costs**.

## Resources to be preconfigured:
- **1 VPC**
  - **1 Internet Gateway**
  - **1 S3 Gateway Endpoint**
  - **1 EC2 Keypair**
  - **1 Public Subnet**
    - Includes **1 NAT Gateway**
  - **1 Private Subnet**  
    **(Important) It is recommended to configure the Private subnet with a /24 subnet mask.**

## Routing Configuration:
- Default route of the **Public Subnet** → Internet Gateway
- Default route of the **Private Subnet** → NAT Gateway
- S3 traffic route of the **Private Subnet** → S3 Gateway Endpoint

## S3 Gateway Endpoint Setup:
- **(Important)** Enabling the S3 Gateway Endpoint allows CNQ to communicate with S3 over AWS’s internal network, minimizing S3 traffic costs.
- Navigate to: **VPC > Endpoints > Create endpoint**
- Select **Service category: AWS services**
- Under **Services**, search for `S3` and select the appropriate S3 service for the region:
  - <a href="images/s3 gw endpoint-service.png"> <img src="images/s3 gw endpoint-service.png" alt="s3 gw endpoint-service" width="25%"> </a>
- Under **Type**, select **Gateway** and choose the target **VPC**:
  - <a href="images/s3 gw endpoint - type, vpc.png"> <img src="images/s3 gw endpoint - type, vpc.png" alt="s3 gw endpoint - type, vpc" width="65%"> </a>
- Under **Route tables**, select the route table associated with the Private Subnet where CNQ will be installed:
  - <a href="images/s3 gw endpoint rt지정.png"> <img src="images/s3 gw endpoint rt지정.png" alt="s3 gw endpoint rt지정" width="65%"> </a>
- For **Policy**, choose **Full access**
- Click **Create endpoint** to complete the creation

## To verify S3 Gateway endpoint functionality:
- Refer to: [https://repost.aws/knowledge-center/vpc-check-traffic-flow](https://repost.aws/knowledge-center/vpc-check-traffic-flow)

