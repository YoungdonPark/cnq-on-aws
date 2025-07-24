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
