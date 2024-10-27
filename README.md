# 설치 목표
- Terraform 이용하여 AWS 상에 Cloud Native Qumulo(CNQ)클러스터 구성

# 설치 파일 준비
- Qumulo 담당자와 Contact하여 아래 3개의 파일 준비
  - aws-terraform-cnq.x.x.zip (x.x는 버전)
  - host_configuration.tar.gz
  - qumulo-core.deb

# 필요 어플리케이션 설치

- MAC의 경우 Homebrew 와 같은 명령어 기반의 패키지 관리 툴 설치 권장
- 윈도우의 경우 Chocolatey와 같은 명령어 기반의 패키지 관리 툴 설치 권장
  - Chocolatey (https://chocolatey.org/install) 방문 후 설치 안내 참고하여 설치
  - 파워쉘을 관리자 권한으로 열기 (이후 모든 작업은 파워쉘을 관리자 권한으로 열고 수행)
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

# AWS 계정 준비
- AWS 액세스 포털등을 이용하여 AWS 자격 증명 가져오기
  - 참고 문서: https://docs.aws.amazon.com/ko_kr/singlesignon/latest/userguide/using-the-portal.html
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

![image](https://github.com/user-attachments/assets/b4808567-6f70-4914-9bba-fffa7dcf4eb6)




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
