# 설치 목표
- 윈도우즈 OS 환경에서 Terraform을 이용하여 AWS상에 Cloud Native Qumulo(CNQ) 클러스터 구성
# 사전 필요 AWS 구성
- 구성이 필요한 리소스
  - VPC 1개
    - Internet gateway 1개
    - S3 Gateway Endpoint 1개
    - Public subnet 1개
      - NAT gateway 1개
    - Private subnet 1개
- 라우팅
  - xx
  - xx
# 설치 파일 준비
- Qumulo 담당자와 Contact하여 원하는 설치 버전에 맞는 아래 3개의 파일 준비
  - aws-terraform-cnq-.x.x.zip
  - host_configuration.tar.gz
  - qumulo-core.deb
# CNQ 설치 파일을 S3 버킷에 업로드
- AWS 매니지먼트 콘솔(AWS 웹페이지)에 접속 후 S3 메뉴로 이동
- Create Bucket 버튼을 누르고 아래 예시와 같이 버킷을 생성
  - 예시) Amazon S3 > Buckets > ypark-cnq-utilbucket > cnq-install-files/ > qumulo-core-install/ > 7.2.3.1/
    - ypark-cnq-utilbucket : 원하는 이름 지정
    - cnq-install-files/ : 원하는 이름 지정
    - qumulo-core-install/ : 정확하게 입력
    - 7.2.3.1/ : 설치하려는 CNQ 버전을 정확하게 입력 (예를들어 7.2.3.1를 설치한다면 7.2.3.1/)
- 전달 받은 qumulo-core.deb 파일을 CNQ 버전 디렉토리에 업로드
- 전달 받은 host_configuration.tar.gz 파일을 CNQ 버전 디렉토리에 업로드
  - 이 파일은 압축을 풀지 않고 host_configuration.tar.gz 파일 그대로 업로드
 - 업로드 완료된 예시 이미지
   - ![image](https://github.com/user-attachments/assets/afe3c5ca-625a-4d6e-9e72-9cb376882000)
# Terraform 변수 파일 편집 및 실행 환경
- VS Code와 같은 개발 도구 설치 권장 (https://code.visualstudio.com/)
- 또는 윈도우즈 파워쉘과 같은 기본 CLI 툴 사용 가능
# 필요 어플리케이션 설치
- 설치 필요한 어플리케이션 목록
  - CLI 기반 패키지 관리 툴
  - AWS CLI
  - Terraform
- Chocolatey와 같은 CLI 기반의 패키지 관리 툴 설치 권장
  - Chocolatey (https://chocolatey.org/install) 방문 후 설치 안내 참고하여 설치
  - CLI 툴(ex:윈도우 PowerShell)을 열고 아래 명령어 수행
  - `choco --version` 명령어로 Chocolatey 버전 확인
    ```powershell
    # Chocolatey 버전 확인
    choco --version
    # 출력 예시
    2.3.0
  - `choco install awscli` 명령어로 awscli 설치
  - `choco install terraform` 명령어로 terraform 설치
  - `aws --version` 명령어로 awscli 버전 확인
    ```powershell
    # awscli 버전 확인
    aws --version
    # 출력 예시
    aws-cli/2.17.32 Python/3.11.9 Windows/10 exe/AMD64
  - `terraform -version` 명령어로 terraform 버전 확인
    ```powershell 
    # Terraform 버전 확인
    terraform -version
    # 출력 예시
    Terraform v1.9.8
    on windows_amd64




![image](https://github.com/user-attachments/assets/eeb9df8d-9c33-47cc-ad37-a05f4848c21b)



# CNQ를 위한 S3 백엔드 스토리지 생성
- aws-terraform-cnq-.x.x.zip 파일을 원하는 경로에 압축 해제
- 압축 해제 후 aws-terraform-cnq-5.0\persistent-storage\terraform.tfvars 파일을 텍스트 에디터로 열기
- 아래 예시를 참고하여 해당 파일의 아래 파라미터만 수정하고 나머지는 default 값으로 유지
    ```powershell
    # 원하는 deployment_name 지정(32글자 이하)
    deployment_name = "ypark-cnq7231-3nodes-s3be"
    # 설치 Region 지정
    aws_region = "ap-northeast-2"
    # Terraform이 리소스를 destroy 할 수 있도록 설정(운영 환경에서는 true를 권고)
    prevent_destroy     = false
    # S3 백엔드 스토리지의 버킷 용량 (최소값은 500TB)
    soft_capacity_limit = 500
- CLI 툴을 열고 aws-terraform-cnq-5.0\persistent-storage 경로로 이동
- 아래의 명령어들을 실행하여 S3 백엔드 스토리지 생성
    ```powershell
    # Terraform 초기화
    terraform init
    # 테라폼의 변경 사항 예측, 영향도 확인, 에러 검증
    terraform plan
    # terraform plan 수행 후 
    # 


- 결과 예시




=============================
- 

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



# CNQ를 위한 S3 백엔드 스토리지 구성
- 


# CNQ를 위한 S3 백엔드 스토리지 구성






==========================



#### 결과:
```bash
# 현재 디렉토리의 파일 목록을 출력합니다
ls -al


```python
# 이 함수는 두 수를 더합니다
def add(a, b):
    return a + b






- 명령어줄 기반 패키지 관리 툴 설치 권장
-
-
- 윈도우 사용자의 경우 Chocolatey 사용 권장
  - 


 



- 항목 1
- 항목 2
  - 하위 항목
    - 더 하위 항목

**굵게**
*기울임*
~~취소선~~



> 이건 인용문입니다.


:::note
이것은 중요 정보입니다.
:::

```python
def hello_world():
    print("Hello, World!")


| 제목 1 | 제목 2 | 제목 3 |
| ------ | ------ | ------ |
| 내용 1 | 내용 2 | 내용 3 |
| 내용 4 | 내용 5 | 내용 6 |


명령어 기반의 Windows 패키지 관리툴을 설치 권장합니다. 이 가이드는 명령어 기반의 Windows 패키지 관리툴 중 하나인 Chocolatey 기반으로 작성되었습니다. https://chocolatey.org/install 로 접속하여 안내에 따라 Chocolatey 설치를 완료합니다. 



# 설치 준비물
AWS 접속 계정  
설치할 VPC
VPC내에 Private Subnet

# 




test

# test
## test
### test

''' code

'''xxx
''' xxx
```markdown
# External NAT Gateway IPs

``` 
## External NAT Gateway IPs
External NAT Gateway IPs
