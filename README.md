# 1. Cloud Native Qumulo on AWS(CNQ on AWS)는 무엇인가요?
- CNQ on AWS는 Qumulo의 Cloud 제품으로 기존 On Premise Qumulo의 장점을 그대로 수용함
- 노드를 늘릴수록 Scale-out한 성능, 구성한 모든 저장 공간이 단일 파일 시스템
  - 클러스터에 최대 1800경의 파일 저장 가능, 단일 경로에 최대 43억개 파일 저장 가능
  - NFS, SMB, S3, FTP, REST등의 멀티 프로토콜 지원
- AWS의 EC2를 컴퓨팅 노드로 사용하고 S3를 백엔드(Backend) 저장소로 활용하여 유연한 구성 변경 가능
  - Scale-out/in: CNQ 클러스터의 컴퓨팅 노드를 추가/제거
  - Scale-up/down: CNQ 클러스터의 컴퓨팅 노드를 상위/하위 인스턴스로 교체	
- Global Name Space 기능을 사용하여 다른 CNQ 클러스터, On Premise Qumulo 클러스터등과 네임 스페이스의 확장이 가능함
- CNQ 웹페이지: https://qumulo.com/ko/product/aws/


  
# 2. 설치 목표 및 목표 구성도
- 윈도우즈 OS 환경에서 Terraform을 이용하여 AWS상에 Cloud Native Qumulo(CNQ) 클러스터 구성
- 테스트 환경을 전제로 하며, 실제 운영 환경에서는 환경에 맞게 수정 필요
- 목표 구성도
  - 아래 구성도에서 CNQ와 S3 백엔드 저장소를 제외하고는 사전 구성이 필요함
    - <a href="images/aws목표구성.png"> <img src="images/aws목표구성.png" alt="aws목표구성" width="50%"> </a>

# 3. CNQ 설치 파일 준비
- Qumulo 담당자와 Contact하여 원하는 설치 버전에 맞는 아래 3개의 파일 준비
  - aws-terraform-cnq-<x.y>.zip (x.y는 버전)
  - host_configuration.tar.gz
  - qumulo-core.deb


# 4. CNQ 설치 파일을 S3 버킷(CNQ를 위한 Util 버킷)에 업로드
- **(중요) 이 과정에서 생성하는 S3 버킷은 CNQ를 위한 S3 백엔드 저장소와는 별개이며, 단순히 설치 파일을 업로드 하기 위한 공간**
- **(중요) 설치 이후에도 지우지 않고 그대로 둘것을 권고**
- AWS 매니지먼트 콘솔에 접속 후 S3 메뉴로 이동
- 아래 예시와 같이 버킷 생성 및 파일 업로드
  - (예시) Amazon S3 > Buckets > ypark-cnq-utilbucket > cnq-install-files/ > qumulo-core-install/ > 7.2.3.1/
   - ypark-cnq-utilbucket : 원하는 이름 지정
   - cnq-install-files/ : 원하는 이름 지정
   - qumulo-core-install/ : 정확하게 입력
   - 7.2.3.1/ : 설치하려는 CNQ 버전을 정확하게 입력 (예를들어 7.2.3.1를 설치한다면 7.2.3.1/)
- 전달 받은 qumulo-core.deb 파일을 CNQ 버전 디렉토리에 업로드
- 전달 받은 host_configuration.tar.gz 파일을 CNQ 버전 디렉토리에 업로드
  - 이 파일은 압축을 풀지 않고 host_configuration.tar.gz 파일 그대로 업로드
- 업로드 완료된 예시 이미지
  - <a href="images/cnq install file.png"> <img src="images/cnq install file.png" alt="cnq install file" width="45%"> </a>

# 5. 필요한 AWS 사전 구성
- **(중요)아래 2가지 조건이 만족되지 않으면 설치 실패함**
  - CNQ가 구성될 Private subnet은 필요한 패키지를 설치하기 위해 반드시 NAT gateway 또는 다른 라우팅을 통해서 인터넷에 접근이 가능해야 함
  - CNQ가 구성될 Private subnet은 S3 Gatewaway를 통해 S3 백엔드 저장소와 통신해야함. S3 Gateway endpoint를 설정하면 CNQ에서 생성되는 S3 트래픽이 인터넷을 통하지 않고 AWS 내부망으로 통신하게 되어 S3 트래픽 비용이 대폭 절감됨
- 사전 구성이 필요한 리소스
  - VPC 1개
    - Internet gateway 1개
    - S3 Gateway Endpoint 1개
    - EC2 Keypair 1개
    - Public subnet 1개
      - NAT gateway 1개
    - Private subnet 1개
    **(중요) Private subnet의 Subnet mask는 /24 비트로 구성하는 것을 권고**
- 리소스들 간의 라우팅
  - Public subnet의 디폴트 라우팅을 위한 목적지: Internet gateway
  - Private subnet의 디폴트 라우팅을 위한 목적지: NAT gateway
  - Private subnet의 S3 통신을 위한 목적지: S3 Gateway endpoint
- S3 Gateway endpoint 설정
  - **(중요)S3 Gateway endpoint를 설정하면 CNQ에서 생성되는 S3 트래픽이 인터넷을 통하지 않고 AWS 내부망으로 통신하게 되어 S3 트래픽 비용이 대폭 절감됨**
  - S3 Gateway endpoint 설정을 위해 VPC > Endpoints > Create endpoint 실행
  - Service category: AWS services 선택
  - Services에서 아래와 같이 S3 입력 후 설치할 Region의 S3 서비스 선택
    - <a href="images/s3 gw endpoint-service.png"> <img src="images/s3 gw endpoint-service.png" alt="s3 gw endpoint-service" width="25%"> </a>
  - Type에서 Gateway 선택 및 VPC 선택
    - <a href="images/s3 gw endpoint - type, vpc.png"> <img src="images/s3 gw endpoint - type, vpc.png" alt="s3 gw endpoint - type, vpc" width="65%"> </a>
  - Route tables에서 CNQ를 설치할 Private subnet 선택
    - <a href="images/s3 gw endpoint rt지정.png"> <img src="images/s3 gw endpoint rt지정.png" alt="s3 gw endpoint rt지정" width="65%"> </a>
  - Policy는 Full access 선택
  - Create endpoint 클릭하여 S3 Gateway endpoint 생성
  - S3 Gateway endpoint 동작 검증 방법
    - https://repost.aws/knowledge-center/vpc-check-traffic-flow


# 6. CNQ 설치 및 모니터링(CBM)을 위한 방화벽 정책 허용
- 목적지:api.nexus.qumulo.com, 포트: 443
- 목적지:ep1.qumulo.com, 포트: 443
- 목적지:api.missionq.qumulo.com, 포트: 443
- 목적지:missionq-dumps.s3.amazonaws.com	, 포트: 443
- 목적지:monitor.qumulo.com, 포트: 443
  - 관련 링크: https://docs.qumulo.com/administrator-guide/monitoring-and-metrics/enabling-cloud-based-monitoring-remote-support.html
  
  
# 7. 명령어 실행 도구 및 Terraform 변수 파일 편집 도구
- 윈도우즈 기본 명령어 실행도구인 PowerShell 사용 가능, 윈도우 기본 텍스트 에디터인 메모장 사용 가능
- VS Code와 같은 개발 도구 설치 권장 (https://code.visualstudio.com/)
  - VS Code에서 Terraform 사용을 위해서는 HashiCorp Terraform extension 설치 필요 


  
# 8. 필요 어플리케이션 설치
- 아래의 VS Code나 PowerShell 실행시에는 "관리자 권한으로 실행"이 필요
- 설치 필요한 어플리케이션 목록
  - CLI 기반의 패키지 관리 툴
  - AWS CLI
  - Terraform
- Chocolatey와 같은 CLI 기반의 패키지 관리 툴 설치 권장
  - Chocolatey (https://chocolatey.org/install) 방문 후 설치 안내 참고하여 설치
  - 설치 후 `choco --version` 명령어로 Chocolatey 버전 확인
  ```powershell
  # Chocolatey 버전 확인
  choco --version
  # 결과 예시
  2.3.0
- `choco install awscli` 명령어로 awscli 설치
- `choco install terraform` 명령어로 terraform 설치
- `aws --version` 명령어로 awscli 버전 확인  
  ```powershell
  # awscli 버전 확인
  aws --version
  # 결과 예시 
  aws-cli/2.17.32 Python/3.11.9 Windows/10 exe/AMD64
- `terraform -version` 명령어로 terraform 버전 확인
  ```powershell
  # Terraform 버전 확인
  terraform -version
  # 결과 예시
  Terraform v1.9.8
  on windows_amd64

# 9. CNQ 구성 1/2단계 -  S3 백엔드 저장소 생성
- **(중요)설치 단계는 총 2단계이며 1단계에서는 S3 백엔드 저장소를 생성하고, 2단계에서는 컴퓨팅 노드들(EC2+Qumulo OS)을 생성 후, S3 백엔드 저장소와 결합하여 클러스터를 최종 구성함**
- **(중요)이 과정을 통해 생성되는 4개의 버킷은 "hashing"되어 서로 다른 AWS S3 파티션에 할당되어 성능을 최대화 함**
- aws-terraform-cnq-<x.y>.zip 파일을 원하는 경로에 압축 해제
- 압축 해제 후 aws-terraform-cnq-<x.y>\persistent-storage\terraform.tfvars 파일을 텍스트 에디터로 열기
  - Terraform은 terraform apply를 실행하는 경로의 terraform.tfvars 파일을 자동으로 찾아 실행됨  
  - S3 백엔드 저장소 생성을 위한 terraform.tfvars는 aws-terraform-cnq-<x.y>\persistent-storage 경로에 위치 함
  - CNQ 클러스터 생성을 위한 terraform.tfvars는 aws-terraform-cnq-<x.y>\ 경로에 위치 함

- 아래 예시를 참고하여 terraform.tfvars 파일의 변수 수정 후 저장
  ```terraform
  # deployment_name: 원하는 deployment_name 지정(32글자 이하)
  deployment_name = "ypark-cnq7231-3nodes-s3be"
  # aws_region: 설치 Region 지정
  aws_region = "ap-northeast-2"
  # prevent_destroy: 이 값이 True이면 "terraform destory"를 수행하여도 버킷이 삭제되지 않아 실수를 방지 할 수 있음, 운영을 위한 배포시에는 이 값을 True로 하는 것을 권고, 이 값이 false이면 "terraform destory" 명령어로 데이터가 있는 버킷도 모두 삭제됨
  prevent_destroy     = false
  # soft_capacity_limit: S3 백엔드 저장소의 용량, 설정상의 용량으로 과금과는 무관(과금은 리소스의 사용량과 상관), TB 단위로 지정하며 최소 500TB에서 최대 10000TB(10PB)까지 설정가능함, 이 후 필요시 늘릴 수 있음
  soft_capacity_limit = 500
  # tags: 필요시 수정
  tags = null

- CLI 툴을 열고 aws-terraform-cnq-<x.y>\persistent-storage 경로로 이동
- 아래의 순서로 Terraform을 이용하여 CNQ가 사용할 S3 백엔드 저장소 생성
- Terraform 작업 환경 초기화
  ```terraform
  # Terraform 작업 환경 초기화
  terraform init
  # 결과 예시
  .... 생략 ....
  Terraform has been successfully initialized!
- Terraform plan으로 변경 사항 예측, 영향도 확인, 에러 검증
  ```terraform
  # 변경 사항 예측, 영향도 확인, 에러 검증
  terraform plan
  #결과 예시
  .... 생략 ....
  aws_ssm_parameter.bucket-region: Refreshing state... [id=/qumulo/ypark-cnq7231-3nodes-s3be-WO6XIZSF1WV/bucket-region]
  aws_s3_bucket.cnq_bucket[2]: Refreshing state... [id=f7favyoar5c-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-3]
  aws_s3_bucket.cnq_bucket[1]: Refreshing state... [id=dj7bynqnzpv-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-2]
  aws_s3_bucket.cnq_bucket[0]: Refreshing state... [id=x3jbivvuwds-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-1]
  aws_s3_bucket.cnq_bucket[3]: Refreshing state... [id=1xhlnlmxtph-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-4]
  .... 생략 ....
  Plan: 4 to add, 1 to change, 0 to destroy.
- Terraform apply를 실행하여 리소스 생성
  ```terraform
  # 리소스 생성
  terraform apply
  # 실행 여부 재확인: yes 입력
  Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.
    Enter a value: yes
  #결과 예시
  .... 생략
  Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

  Outputs:
  
  bucket_names = [
    "x3jbivvuwds-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-1",
    "dj7bynqnzpv-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-2",
    "f7favyoar5c-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-3",
    "1xhlnlmxtph-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-4",
  ]
  deployment_unique_name = "ypark-cnq7231-3nodes-s3be-WO6XIZSF1WV"
  prevent_destroy = false
  soft_capacity_limit = "500 TB"

- **(중요)위 결과에서 "ypark-cnq7231-3nodes-s3be-WO6XIZSF1WV"은 S3 백엔드 저장소의 deployment_unique_name이며, 이 값을 CNQ 구성 2/2단계에서 사용함**
- AWS 매지니먼트 콘솔에서도 이 값이 포함되어 생성된 4개의 버킷을 확인 할 수 있음

# 10. CNQ 구성 2/2단계 -  클러스터 구성
- aws-terraform-cnq-<x.y>\ 경로의 terraform.tfvars 파일을 텍스트 에디터로 열기
- **(중요)  "q_cluster_floating_ips = 24" 부분은 디폴트값 그대로 24로 설정할 것을 권고**

- 아래 예시를 참고하여 terraform.tfvars 변수 수정 후 저장
    ```terraform
      # ****************************** Required ******************************
      # ***** Terraform Variables *****
      # deployment_name: 원하는 deployment_name 지정(32글자 이하)
      deployment_name = "ypark-cnq7231-3nodes"

      # ***** S3 Bucket Variables
      # s3_bucket_name: "CNQ 설치 파일을 S3 버킷(CNQ를 위한 Util 버킷)에 업로드" 부분에서 처음 생성한 버킷의 이름
      s3_bucket_name = "ypark-cnq-utilbucket"
      # s3_bucket_prefix: "CNQ 설치 파일을 S3 버킷(CNQ를 위한 Util 버킷)에 업로드" 부분에서 처음 생성한 버킷의 하단에 추가한 prefix의 이름
      s3_bucket_prefix = "cnq-install-files/"
      # s3_bucket_region:  이 버킷이 구성된 Region
      s3_bucket_region = "ap-northeast-2"
      
      # ***** AWS Variables *****
      # aws_region: CNQ 클러스터를 구성할 Region
      aws_region = "ap-northeast-2"
      # aws_vpc_id: CNQ 클러스터를 구성하기 위해 사전에 생성한 VPC의 ID
      aws_vpc_id = "vpc-0309fda56d67b0d8b"
      # ec2_key_pair: Region 내에서 사전에 구성된 EC2 키 페어
      ec2_key_pair = "ypark-keypair-ppkfirst"
      # private_subnet_id: CNQ 클러스터를 구성하기 위해 사전에 생성한 Private subnet의 ID
      private_subnet_id = "subnet-033873efdc3efdb4e"
      # term_protection: true로 설정하면, AWS 매니지먼트 콘솔에서 삭제를 시도할때 알림 표시(운영 환경에서는 true로 변경후 클러스터 생성 권고), Terraform의 EC2 destroy(삭제)는 막지 못함
      term_protection = false

      # ***** Qumulo Cluster Variables *****
      # q_ami_id: null로 설정하면 최신 Ubuntu AMI를 찾아 설치함(null로 둘 것일 대부분 권장)
      q_ami_id = null
      # q_shared_ami: false로 설정
      q_shared_ami = false
      # q_shared_ami: true로 설정
      q_debian_package = true
      # q_cluster_admin_password: CNQ 클러스터의 admin 암호
      q_cluster_admin_password = "abcde12345!@#$%"
      # q_cluster_name: 클러스터의 이름
      q_cluster_name = "ypark-cnq7231"
      # q_cluster_version: "CNQ 설치 파일을 S3 버킷(CNQ를 위한 Util 버킷)에 업로드" 부분에서 업로드한 경로(7.2.3.1/)와 설치파일 그리고 아래 설치할 버전이 모두 정확히 일치해야 함
      q_cluster_version = "7.2.3.1"

      # ***** Qumulo Cluster Config Options *****
      # q_persistent_storage_deployment_unique_name: S3 백엔드 저장소 생성 시 부여된 deployment_unique_name 입력
      q_persistent_storage_deployment_unique_name = "ypark-cnq7231-3nodes-s3be-WO6XIZSF1WV"
      # q_persistent_storage_type: hot_s3_int(기본값), hot_s3_std, cold_s3_ia, cold_s3_gir 입력이 가능하며, 많은 경우 hot_s3_int 사용을 권장
      q_persistent_storage_type = "hot_s3_int"
      # q_instance_type: m6idn.2xlarge 이상, i4i.2xlarge 이상, i3en.2xlarge 이상의 인스턴스 타입 설정이 필요하며, Seoul Region의 경우 i4i 인스턴스 타입만 사용 가능
      q_instance_type = "i4i.2xlarge"
      # q_node_count: 클러스터의 노드 수 설정
      q_node_count = 3

      # ****************************** Optional ******************************
      # ***** Environment and Tag Options *****
      # check_provisioner_shutdown: CNQ를 설치하기 위해 생성되는 provisioner EC2를 CNQ 설치 완료 후에 shutdown 할지 여부, true로 설정을 권고
      check_provisioner_shutdown = true
      # dev_environment: false로 두는 것을 권장
      dev_environment = false
      # tags: 필요시 설정
      tags = null

      # ***** Qumulo REPLACEMENT Cluster Options *****
      # q_replacement_cluster: 초기 설치시에는 false
      q_replacement_cluster = false
      # q_existing_deployment_unique_name: 초기 설치시에는 false
      q_existing_deployment_unique_name = null

      # ***** Qumulo Cluster Misc Options *****
      # kms_key_id: null로 설정
      kms_key_id = null
      # q_audit_logging: false로 설정
      q_audit_logging = false
      # q_cluster_additional_sg_cidrs: null로 설정
      q_cluster_additional_sg_cidrs = null
      # q_cluster_additional_sg_ids: null로 설정
      q_cluster_additional_sg_ids = null
      # q_cluster_floating_ips: 부하 분산을 위한 floating_ip 개수, 24개 그대로 둘것을 권고, 클러스터당 최소 4개~최대 147개 설정 가능
      q_cluster_floating_ips = 24
      # q_permissions_boundary: null로 설정
      q_permissions_boundary = null
      # q_persistent_storage_bucket_policy: true로 설정
      q_persistent_storage_bucket_policy = true

      # ***** OPTIONAL module 'rout53-phz' *****
      # 'rout53-phz' 모듈은 이 테스트에서는 사용하지 않으며, 모든 값을 아래와 같이 유지
      q_fqdn_name = "my-dns-name.local"
      q_record_name = "qumulo"
      q_route53_provision = false

      # ***** OPTIONAL module 'nlb-qumulo' *****
      # 'nlb-qumulo' 모듈은 이 테스트에서는 사용하지 않으며, 모든 값을 아래와 같이 유지
      q_nlb_cross_zone = false
      q_nlb_override_subnet_id = null
      q_nlb_provision = false
      q_nlb_stickiness = true

- CLI 툴을 열고 aws-terraform-cnq-<x.y>\ 경로로 이동
- 아래의 순서로 Terraform을 이용하여 CNQ 클러스터 구성
- Terraform 작업 환경 초기화
    ```terraform
    # Terraform의 작업 환경 초기화
    terraform init
    # 결과 예시
    .... 생략 ....
    Terraform has been successfully initialized!
- Terraform plan으로 변경 사항 예측, 영향도 확인, 에러 검증
    ```terraform
    # 변경 사항 예측, 영향도 확인, 에러 검증
    terraform plan
    #결과 예시
    .... 생략 ....
     Plan: 61 to add, 0 to change, 0 to destroy.

    Changes to Outputs:
      + cluster_provisioned             = (known after apply)
      + deployment_unique_name          = (known after apply)
      + max_cluster_size                = (known after apply)
      + min_cluster_size                = (known after apply)
      + persistent_storage_bucket_names = [
          + "x3jbivvuwds-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-1",
          + "dj7bynqnzpv-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-2",
          + "f7favyoar5c-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-3",
          + "1xhlnlmxtph-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-4",
        ]
      + qumulo_floating_ips             = (known after apply)
      + qumulo_knowledge_base           = "https://care.qumulo.com/hc/en-us/categories/115000637447-KNOWLEDGE-BASE"
      + qumulo_primary_ips              = [
          + (known after apply),
          + (known after apply),
          + (known after apply),
        ]
      + qumulo_private_NFS              = "<custom.dns>:/<NFS Export Name>"
      + qumulo_private_SMB              = "\\<custom.dns>\\<SMB Share Name>"
      + qumulo_private_url              = "https://<custom.dns>"
      + qumulo_private_url_node1        = (known after apply)

- Terraform apply를 실행하여 리소스 생성
    ```terraform
    # 리소스 생성
    terraform apply
    # 실행 여부 재확인: yes 입력
    Do you want to perform these actions?
    Terraform will perform the actions described above.
    Only 'yes' will be accepted to approve.
  
    Enter a value: yes
    #결과 예시
    .... 생략 ....
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Waiting for node 1 to run Qumulo Core. Package location: s3://ypark-cnq-utilbucket/cnq-install-files/qumulo-core-install/7.2.3.1/
    module.qprovisioner.null_resource.provisioner_status[0]: Still creating... [2m30s elapsed]
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Qumulo Core running on node 1. Waiting for other nodes to run Qumulo Core.
    module.qprovisioner.null_resource.provisioner_status[0]: Still creating... [2m40s elapsed]
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Qumulo Core running on all nodes.
    .... 생략 ....
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Qumulo Core running on node 1. Waiting for other nodes to run Qumulo Core.
    module.qprovisioner.null_resource.provisioner_status[0]: Still creating... [2m40s elapsed]
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Qumulo Core running on all nodes.
    module.qprovisioner.null_resource.provisioner_status[0]: Still creating... [2m50s elapsed]
    module.qprovisioner.null_resource.provisioner_status[0]: Still creating... [3m0s elapsed]
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Forming first quorum and configuring cluster
    module.qprovisioner.null_resource.provisioner_status[0]: Still creating... [3m10s elapsed]
    .... 생략 ....
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Tagging EBS volumes & updating EBS IOPS/Tput if applicable
    module.qprovisioner.null_resource.provisioner_status[0]: Still creating... [3m50s elapsed]
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): Shutting down provisioning instance
    module.qprovisioner.null_resource.provisioner_status[0] (local-exec): *****CNQ Cluster Successfully Provisioned*****
    module.qprovisioner.null_resource.provisioner_status[0]: Creation complete after 3m57s [id=1517890628290139747]
    module.qprovisioner.data.aws_ssm_parameter.qprovisioner[0]: Reading...
    module.qprovisioner.data.aws_ssm_parameter.qprovisioner[0]: Read complete after 0s [id=/qumulo/ypark-cnq7231-3nodes-OW6ELGCN9TX/last-run-status]

    Apply complete! Resources: 61 added, 0 changed, 0 destroyed.

    Outputs:

    cluster_provisioned = "Success"
    deployment_unique_name = "ypark-cnq7231-3nodes-OW6ELGCN9TX"
    max_cluster_size = "6"
    min_cluster_size = "3"
    persistent_storage_bucket_names = tolist([
      "x3jbivvuwds-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-1",
      "dj7bynqnzpv-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-2",
      "f7favyoar5c-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-3",
      "1xhlnlmxtph-ypark-cnq7231-3nodes-s3be-wo6xizsf1wv-qps-4",
    ])
    qumulo_floating_ips = [
      "172.17.17.13",
      "172.17.17.15",
      "172.17.17.229",
      "172.17.17.248",
      "172.17.17.252",
      "172.17.17.58",
    ]
    qumulo_knowledge_base = "https://care.qumulo.com/hc/en-us/categories/115000637447-KNOWLEDGE-BASE"
    qumulo_primary_ips = [
      "172.17.17.99",
      "172.17.17.21",
      "172.17.17.123",
    ]
    qumulo_private_NFS = "<custom.dns>:/<NFS Export Name>"
    qumulo_private_SMB = "\\<custom.dns>\\<SMB Share Name>"
    qumulo_private_url = "https://<custom.dns>"
    qumulo_private_url_node1 = "https://172.17.17.99"

- 설치가 정상적으로 완료되면 위와 같은 형태로 결과가 출력됨
- AWS 매니지먼트 콘솔의 EC2 항목에서 아래와 같이 3개의 EC2가 설치된 것을 확인
  - <a href="images/cnq ec2.png"> <img src="images/cnq ec2.png" alt="cnq ec2" width="50%"> </a>

- 설치를 마친 뒤 EC2를 추가 구성하여 Qumulo GUI, Qumulo CLI에 대한 접근 테스트와 SMB, NFS, S3등을 테스트 할 수 있음

<!--
=============================

# 11. 1노드 구성

Add this variable to terraform.tfvars in the /persistent-storage directory
single_node_cluster = true
save the file
deploy the persistent-storage (you will get 1 bucket and 100TB capacity limit)

In the top-level directory for Terraform edit the file variables.tf
Search for q_node_count
Swap the commented lines to look like below
Delete “THIS IS NOT SUPPORTED” at the end of the line you uncommented and save









When you deploy just set q_node_count in your terraform.tfvars file to ‘1’



- **(중요) CNQ는 Private subnet에 위치할 예정이며, 이 Private subnet에는 CNQ외에 다른 리소스가 없음, 그러므로 생성되는 모든 S3 트래픽은 S3 백엔드 저장소를 위한 트래픽이므로 인터넷을 거칠 이유가 없으며, 만약 인터넷을 거치게 된다면, S3 트래픽으로 인한 비용이 발생하게 됨**

 Terraform 결과에도 CNQ클러스터의 deployment_unique_name이 출력됨, deployment_unique_name = "ypark-cnq7231-3nodes-OW6ELGCN9TX"- 

- CNQ 클러스터 구성을 위한 tfvars의 경로는 aws-terraform-cnq-<x.y>\persistent-storage\가 아니라 aws-terraform-cnq-<x.y> 인것에 유의**

- # AWS 로그인
- AWS 액세스 포털등을 이용하여 로그인
  - https://docs.aws.amazon.com/ko_kr/singlesignon/latest/userguide/using-the-portal.html
- 파워쉘을 열고 `aws sts get-caller-identity` 명령어로 로그인 정상 여부 확인
  ```powershell 
  # aws 로그인 정보 확인
  aws sts get-caller-identity
  # 출력 예시
  {
      "UserId": "AIDXXXXXXXXXX",
      "Account": "123456789012",
      "Arn": "arn:aws:iam::123456789012:user/username"
  }

-->


