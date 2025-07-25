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

