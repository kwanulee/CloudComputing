
# Amazon RDS 
- Amazon RDS 란?
- [Amazon RDS 실습](#practice)

## 1. Amazon RDS (Relational Database Service) 란?
Amazon RDS(Amazon Relational Database Service)는 **AWS(Amazon Web Services)**에서 제공하는 관리형 관계형 데이터베이스 서비스입니다. RDS는 사용자가 데이터베이스 관리의 복잡성을 줄이고, 고가용성과 확장성을 갖춘 관계형 데이터베이스를 손쉽게 구축, 운영, 확장할 수 있도록 지원합니다. 이를 통해 데이터베이스의 설치, 패치, 백업, 복구, 모니터링 등을 자동화하여 사용자는 애플리케이션 개발과 데이터 활용에 집중할 수 있습니다.

### 1.1 주요 특징

1. **관리형 서비스:**
    - Amazon RDS는 데이터베이스의 설치, 유지 관리, 백업, 소프트웨어 패치 등을 자동으로 처리합니다. 사용자는 데이터베이스 서버의 설정이나 운영 체제 관리에 신경 쓰지 않고, 애플리케이션 개발과 데이터베이스 사용에만 집중할 수 있습니다.
    - 데이터베이스의 자동 백업, 복구 지점(Point-in-time Recovery) 기능을 통해 데이터 복구를 쉽게 수행할 수 있습니다.
2. **다양한 데이터베이스 엔진 지원:**
    - Amazon RDS는 여러 인기 있는 관계형 데이터베이스 엔진을 지원합니다. 이를 통해 사용자는 자신에게 가장 적합한 데이터베이스 엔진을 선택할 수 있습니다. 지원하는 주요 엔진은 다음과 같습니다:
      - Amazon Aurora (MySQL 호환 및 PostgreSQL 호환)
      - MySQL
      - PostgreSQL
      - MariaDB
      - Oracle Database
      - Microsoft SQL Server
	 - Amazon Aurora는 AWS가 개발한 RDS의 확장형 데이터베이스로, MySQL과 PostgreSQL과 호환되며, 고성능과 자동 확장 기능을 제공합니다.
3. **고가용성 및 자동 복구:**
    - RDS는 Multi-AZ 배포 기능을 통해 고가용성을 제공합니다. 데이터베이스 인스턴스를 기본(primary) 영역과 대기(standby) 영역에 자동으로 복제하여 장애 발생 시 대기 인스턴스로 자동으로 장애 조치를 수행합니다.
      - 다음 다이어그램은 Amazon RDS가 다른 가용 영역에서 동기식 대기 복제본을 자동으로 프로비저닝하고 유지 관리하는 다중 AZ DB 인스턴스 배포를 보여줍니다. 복제본 데이터베이스는 읽기 트래픽을 제공하지 않습니다.

      ![](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/images/con-multi-AZ.png)

    - Multi-AZ 배포는 데이터베이스의 자동 복구와 **장애 조치(Failover)**를 지원하여 서비스 중단을 최소화합니다.
    - 읽기 복제본(Read Replica) 기능을 통해 데이터베이스의 읽기 부하를 분산시킬 수 있으며, 이는 읽기 집중형 애플리케이션의 성능을 향상시키는 데 도움이 됩니다.
        - 다음 다이어그램은 동일한 AWS 리전에 있는 3개의 개별 가용 영역에 라이터 DB 인스턴스와 2개의 리더 DB 인스턴스가 있는 다중 AZ DB 클러스터 배포를 보여줍니다. 3개의 DB 인스턴스 모두 읽기 트래픽을 처리할 수 있습니다.
      ![](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/images/multi-az-db-cluster.png)
4. **확장성:**
    - RDS는 데이터베이스의 스토리지 및 컴퓨팅 리소스를 자동으로 확장할 수 있는 기능을 제공하여, 애플리케이션의 요구에 맞게 용량을 조정할 수 있습니다.
      - **스토리지 자동 확장**: 이 기능은 특히 Amazon Aurora 및 RDS for MySQL, MariaDB, PostgreSQL 같은 엔진에서 사용할 수 있으며, 데이터베이스의 용량을 수동으로 관리할 필요 없이 자동으로 스토리지 용량을 늘릴 수 있습니다. 사용자는 최대 스토리지 크기를 미리 설정할 수 있으며, 설정된 용량에 도달할 때마다 필요한 만큼 추가 스토리지가 할당됩니다. 이렇게 하면 갑작스러운 데이터 증가에도 애플리케이션의 안정성이 유지됩니다.
      - **컴퓨팅 리소스의 확장**: RDS의 일부인 Amazon Aurora Serverless는 특히 서버리스 환경에서 컴퓨팅 리소스를 자동으로 조정할 수 있는 옵션을 제공합니다. Aurora Serverless를 사용하면 데이터베이스의 워크로드에 따라 자동으로 용량이 확장 및 축소되므로, 사용자가 명시적으로 인스턴스 크기를 선택하지 않아도 됩니다.서버리스 모드에서는 데이터베이스가 활성화되면 필요한 만큼의 컴퓨팅 리소스를 자동으로 할당하고, 트래픽이 적거나 없을 때는 자동으로 리소스를 축소하여 비용을 절감할 수 있습니다.
    - RDS는 데이터베이스 인스턴스의 크기를 조정하거나 스토리지를 늘리는 과정에서 다운타임이 최소화되도록 설계되었습니다.

5. **보안:**
    - RDS는 **AWS Identity and Access Management (IAM)**와 통합되어, 사용자가 데이터베이스 인스턴스에 접근할 수 있는 권한을 제어할 수 있습니다.
    - **VPC(가상 프라이빗 클라우드)**와 통합되어 RDS 인스턴스를 네트워크 내에서 안전하게 배포할 수 있으며, VPC 보안 그룹을 사용해 데이터베이스에 대한 접근을 제한할 수 있습니다.

      - 다음 예제에서는 ec2-rds-x VPC 보안 그룹을 사용하여 클라이언트 애플리케이션의 IP 주소를 소스로 사용하는 인바운드 규칙을 정의합니다. 애플리케이션 서버는 이 보안 그룹에 속합니다. rds-ec2-x라는 이름의 보조 보안 그룹은 ec2-rds-x를 소스로 지정하고 RDS DB 인스턴스에 연결합니다. 보안 그룹 규칙에 따르면 클라이언트 애플리케이션은 DB 인스턴스에 직접 액세스할 수 없지만, EC2 인스턴스는 DB 인스턴스에 액세스할 수 있습니다.
      ![](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/images/con-VPC-sec-grp.png)
    - **TLS(Transport Layer Security)**를 통해 데이터 전송 중 암호화를 제공하며, **AWS KMS(AWS Key Management Service)**를 사용하여 데이터베이스 암호화를 지원합니다.
    - 데이터베이스 인스턴스, 자동 백업, 스냅샷, 리드 복제본 등도 암호화할 수 있습니다.
5. **자동 백업 및 스냅샷:**
    - RDS는 자동 백업 기능을 제공하여 데이터베이스를 일정 주기마다 자동으로 백업합니다. 이를 통해 특정 시점으로 데이터베이스를 복원할 수 있습니다.
    - 사용자는 데이터베이스 스냅샷을 수동으로 생성할 수 있으며, 스냅샷은 필요할 때 새로운 데이터베이스 인스턴스로 복원할 수 있습니다.
    - 백업은 AWS의 내구성이 높은 S3 스토리지에 저장되며, 복원 요청 시 빠르게 데이터를 복원할 수 있습니다.


## 1.2 Amazon RDS 구성요소

Amazon RDS(Amazon Relational Database Service)의 구성 요소들은 RDS 인스턴스의 생성, 관리, 확장, 백업, 복구 등의 다양한 작업을 수행할 수 있도록 설계되었습니다. 이를 통해 RDS는 사용자가 손쉽게 데이터베이스를 운영할 수 있는 환경을 제공하며, 각 요소는 서로 긴밀하게 통합되어 RDS의 기능을 최적화합니다. 아래에서 Amazon RDS의 주요 구성 요소들을 자세히 설명하겠습니다.

- [DB 인스턴스](#1.2.1)
- [보안그룹](#1.2.2)

<a name="1.2.1"></a>

#### 1.2.1 DB 인스턴스

- **Amazon RDS의 기본 구성요소**로서, 클라우드에 있는 격리된 데이터베이스 환경
  - 사용자가 만든 여러 데이터베이스 포함
  - 기존 도구 및 애플리케이션 (예, 표준 SQL 클라이언트 애플리케이션)을 통해 DB 인스턴스에 접근 가능
  - AWS 명령행 인터페이스 (CLI), Amazon RDS API, AWS Management Console을 사용해 DB 인스턴스를 만들고 수정할 수 있음.
- 하나의 DB 인스턴스는 선택한 **데이터베이스 엔진** (MySQL, PostgreSQL, MariaDB, Oracle, SQL Server, Amazon Aurora)을 실행하며, 이 인스턴스 위에 여러 데이터베이스를 생성할 수 있습니다.
- **DB 인스턴스 클래스**
  - DB 인스턴스의 CPU 및 메모리 용량 결정
- **DB 인스턴스의 스토리지**
  - 5GB에서 6TB까지 용량 선택 가능
  - 마그네틱, 범용(SSD) 및 프로비저닝된 IOPS(SSD) 등 세 가지 유형
- Amazon의 Virtual Private Cloud(VPC) 서비스를 사용해 가상 사설 클라우드에서 DB 인스턴스를 실행 가능

<a name="1.2.2"></a>

#### 1.2.2 보안그룹

- DB 인스턴스에 대한 액세스를 제어
  - 사용자가 지정한 IP 주소 범위 또는 Amazon EC2 인스턴스에서 액세스할 수 있도록 허용하는 방법으로 제어
- Amazon RDS는 **VPC 보안 그룹** 및 **EC2 보안 그룹**을 사용
  - **VPC 보안 그룹**은 VPC 내부의 DB 인스턴스에 대한 액세스를 제어하고,
  - **EC2 보안 그룹**은 EC2 인스턴스에 대한 액세스를 제어하며, DB 인스턴스와 함께 사용
  
  ![](figures\rds-security.png?msec=1728522773172)
  

----
<a name="practice"></a>
## 2. Amazon RDS 실습
### 2.1 [MySQL DB 인스턴스 생성 및 해당 인스턴스에 연결](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.MySQL.html)
![](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/UserGuide/images/getting-started-mysql.png)

### 2.2 MySQL DB 인스턴스 생성 및 PHP 웹서버 생성

1. [EC2 인스턴스 생성](#2.2.1)
2. [PHP와 함께 Apache 웹서버 설치](#2.2.2)
3. [VPC 보안 그룹 생성](#2.2.3)
1. [MySQL DB인스턴스 생성](#2.2.4)
2. [DB 인스턴스에 PHP 웹 애플리케이션 연결](#2.2.5)
3. [DB인스턴스의 데이터베이스에 연결](#2.2.6)
4. [DB 인스턴스 삭제](#2.2.7)

<a name="2.2.1"></a>
#### 2.2.1 EC2 인스턴스 생성
1. AWS Management Console에 로그인한 다음 https://console.aws.amazon.com/ec2/ 에서 Amazon EC2 콘솔을 엽니다
2. [EC2 대시보드]를 선택한 다음, [인스턴스 시작]를 선택합니다
3. [이름 및 태그] 섹션에서 이름(예, *tutorial-webserver*)을 입력합니다.
4. [애플리케이션 및 OS 이미지] 섹션에서 *Amazon Linux 2023 AMI*를 선택합니다.
5. [인스턴스 유형] 섹션에서 *t3.micro* 인스턴스 유형을 선택합니다.
6. [키페어(로그인)] 섹션에서 기존 키페어를 선택하거나 새 키페어를 생성합니다.
7. [네트워크 설정] SSH와 HTTP의 인바운드 트래픽을 허용하는 보안그룹을 생성하거나 기존 보안 그룹 중에서 선택합니다.
8. 다른 부분은 기본 설정 그대로 유지한 채, [인스턴스 시작]을 선택합니다.

<a name="2.2.2"></a>
#### 2.2.2 PHP와 함께 Apache 웹서버 설치

1. EC2 인스턴스에 연결하고 PHP가 포함된 Apache 웹 서버를 설치한다.
  - 최신 버그 수정 및 보안 업데이트를 수행
    ```
    [ec2-user ~]$ sudo yum update
    ```
  - 업데이트 완료 후 yum install 명령을 사용하여 PHP 소프트웨어 패키지가 포함된 Apache 웹 서버를 설치
    ```
    [ec2-user ~]$ sudo yum install -y httpd php php-mysqlnd     
    ```
  - 웹 서버를 시작
    ```
    [ec2-user ~]$ sudo service httpd start
    ```
  - chkconfig 명령을 사용하여 웹 서버가 시스템 부팅 때마다 시작되도록 구성
    ```
    [ec2-user ~]$ sudo chkconfig httpd on
    ```
2. Apache 웹 서버에 대한 파일 권한을 설정한다.
  - 사용자(이 경우는 ec2-user)를 apache 그룹에 추가
    ```
    [ec2-user ~]$ sudo usermod -a -G apache ec2-user
    ```
  - 로그아웃
    ```
    [ec2-user ~]$ exit
    ```
  - apache 그룹의 멤버십을 확인하려면 인스턴스에 다시 연결한 후 다음 명령을 실행.
    ```
    [ec2-user ~]$ groups
    ec2-user adm wheel apache systemd-journal
    ```
  - /var/www 및 그 콘텐츠의 그룹 소유권을 apache 그룹으로 변경
    ```
    [ec2-user ~]$ sudo chown -R ec2-user:apache /var/www
    ```
  - 그룹 쓰기 권한을 추가하여 나중에 하위 디렉터리에 대한 그룹 ID를 설정하려면 /var/www와 그 하위 디렉터리의 **디렉터리 권한을 변경**
    ```
    [ec2-user ~]$ sudo chmod 2775 /var/www
    [ec2-user ~]$ find /var/www -type d -exec sudo chmod 2775 {} \;
    ```
  - 그룹 쓰기 권한을 추가하려면 /var/www 및 그 하위 디렉터리의 **파일 권한을 반복하여 변경**
    ```
    [ec2-user ~]$ find /var/www -type f -exec sudo chmod 0664 {} \;
    ```

<a name="2.2.3"></a>
#### 2.2.3 VPC 보안그룹 생성
- DB 인스턴스는 VPC에서 생성될 가능성이 가장 높습니다
- VPC 보안 그룹을 생성하여 VPC 내부의 DB 인스턴스에 대한 액세스를 제공
- 절차
	1. AWS Management Console에 로그인한 후 https://console.aws.amazon.com/vpc 에서 Amazon VPC 콘솔을 엽니다.
	2. AWS Management Console 상단 오른쪽 모서리에서 VPC 보안 그룹과 DB 인스턴스를 생성할 리전을 선택
	3. 탐색 창에서 [보안 그룹]를 클릭
	4. [보안 그룹 생성]을 클릭
	5. [보안 그룹 생성] 창에서 보안 그룹의 Name 태그, 그룹 이름 및 설명을 입력합니다.
    - [그룹 이름] : *tutorial-db-security-group*
    - [설명]: *security group for tutorial db*
    - DB 인스턴스를 생성하려는 VPC를 선택합니다. 
	6. 인바운드 규칙 색션에서 [규칙추가]를 클릭
    - [유형] 목록에서 *사용자 지정 TCP*을 선택
    - [포트 범위] 텍스트 상자에 DB 인스턴스에 사용할 포트 값(*3306*)을 입력
    - [소스] 목록에서 *사용자 지정*을 선택하고,  텍스트 상자 목록에서 *보안 그룹 이름*을 선택 (DB인스턴스에 접속하는 애플리케이션의 EC2인스턴스의 보안그룹 이름 선택)
	7. [태크 선택 사항] 섹션에서 [키] 텍스트 상자에 *Name*을 입력하고 [값] 텍스트 상자에 *tutorial-db-security-group* 입력 
	8. 모두 마쳤으면 [보안 그룹 생성]을 클릭합니다.

<a name="2.2.4"></a>
#### 2.2.4 MySQL DB인스턴스 생성

1. AWS Management Console에 로그인한 다음 https://console.aws.amazon.com/rds/ 에서 Amazon RDS 콘솔을 엽니다
2. Amazon RDS 콘솔의 오른쪽 위 모서리에서 DB 인스턴스를 만들 리전을 선택
3. 탐색 창에서 [데이터베이스]를 선택
4. [데이터베이스 생성]을 선택합니다. 
5. [데이터베이스 생성 방식 선택]에서 *표준 생성*을 선택
6. [엔진 옵션]에서 *MySQL* 선택
7. [템플릿]에서 *프리티어* 선택
8. [설정]에서 다른 설정은 그대로 두고, 아래 세 부분에 대해서만 수정
    - DB 인스턴스 식별자 : 선택한 리전의 계정에 대해 고유한 DB 인스턴스의 이름을 입력
      - 예, *tutorial-db-instance*
    - 마스터 사용자 이름 : 1-16자의 영숫자 문자를 사용해 DB 인스턴스에 로그온하기 위해 마스터 사용자 이름으로 사용할 이름을 입력
      - 예, *tutorial_user*
    - [마스터 암호] 및 [마스터 암호 확인]: 마스터 사용자 암호로 인쇄 가능한 ASCII 문자(/, " 및 @ 제외) 8-41자를 포함하는 암호를 입력
      - 예, *master_password*
9. [인스턴스 구성]에서 기본 설정 유지 (버스터블 클래스(t클래스 포함 - db.t4g.micro))
10. [스토리지]에서 기본 설정 유지
    - 스토리지 유형 : *범용(SSD)*을 선택
    - 할당된 스토리지 : 400GB 할당
11. [연결]에서 이전에 생성한 EC2 인스턴스와의 연결을 설정합니다.  
    - [컴퓨팅 리소스]에서 *EC2 컴퓨팅 리소스에 연결* 선택
    - [EC2 인스턴스]에서 연결할 EC2 인스턴스를 선택
    - [VPC 보안 그룹]에서 이 DB 인스턴스에 사용할 VPC 보안 그룹을 선택
      - 기존 보안 그룹(예: tutorial-db-security-group) 선택
    - 나머지 설정은 그대로 유지
12. [추가 구성]에서 
    - 데이터베이스 이름 : 기본 데이터베이스의 이름을 1~64자의 영숫자 문자로 입력 (*sample* 입력)
    - DB 파라미터 그룹: [default.mysql8.0]의 기본값을 유지
    - Option Group: [default:mysql-8-0]의 기본값을 유지
    - 자동 백업 활성화 여부 선택
    - 암호화 활성화 여부 선택
    - 마이너 버전 자동 업그레이드 사용 여부 선택
13. [데이터베이스 생성]을 클릭
14. RDS 콘솔의 데이터베이스 목록에 새 DB 인스턴스가 나타남
    - DB 인스턴스를 만들고 사용할 준비가 될 때까지 DB 인스턴스의 상태는 **생성 중** 상태가 **사용 가능**으로 변경되면 DB 인스턴스의 데이터베이스에 연결할 수 있음.

<a name="2.2.5"></a>
#### 2.2.5 DB인스턴스에 PHP 웹 애플리케이션 연결
1. EC2 인스턴스에  연결하고, 디렉터리를 **/var/www**로 변경하고, inc라는 새로운 하위 디렉터리를 생성합니다.
  ```
  [ec2-user ~]$ cd /var/www
  [ec2-user ~]$ mkdir inc
  [ec2-user ~]$ cd inc
  ```
2. dbinfo.inc라는 inc 디렉터리에서 새 파일을 생성한 다음 nano 또는 선택한 편집기를 호출하여 다음 콘덴츠로 파일을 편집하고 저장하고 닫는다.
  ```
  [ec2-user ~]$ >dbinfo.inc
  [ec2-user ~]$ nano dbinfo.inc
  ```
  ```
  <?php
  	define('DB_SERVER', '생성된 DB 인스턴스의 엔드포인트로 대체');
  	define('DB_USERNAME', 'tutorial_user');
  	define('DB_PASSWORD', 'master_password');
  	define('DB_DATABASE', 'sample');
  ?>
  ```
3. 디렉터리를 /var/www/html로 변경하고, SamplePage.php라는 html 디렉터리에서 새 파일을 생성한 다음 nano 또는 선택한 편집기를 호출하여 파일을 편집합니다
  ```
  [ec2-user ~]$ cd /var/www/html
  [ec2-user ~]$ >SamplePage.php
  [ec2-user ~]$ nano SamplePage.php
  ```
4. 다음 링크의 콘덴츠를 SamplePage.php 파일에 추가
  - https://github.com/kwanulee/AWSExample/blob/master/RDS-PHP/SamplePage.php
5. SamplePage.php 파일을 저장하고 닫습니다
6. 웹 브라우저를 열고 http://*EC2_instance_endpoint*/SamplePage.php 입력하여 결과를 확인   
  - 예: http://ec2-52-79-51-167.ap-northeast-2.compute.amazonaws.com/SamplePage.php 를 검색하여 웹 서버에서 RDS MariaDB DB 인스턴스에 제대로 연결되는지 확인

<a name="2.2.6"></a>
#### 2.2.6 MySQL DB인스턴스의 데이터베이스에 연결
- 표준 SQL 클라이언트 애플리케이션을 사용해 DB 인스턴스에 있는 데이터베이스에 연결할 수 있습니다.
- mysql 명령줄 도구를 사용하여 DB 인스턴스의 데이터베이스에 연결하는 방법
  1. MySQL이 설치된 EC2 인스턴스로 접속 혹은  MySQL 설치
    ```
    [ec2-user ~]$ sudo yum install -y mariadb105
    ```
  2. 터미널에서 다음 입력
    ```
    [ec2-user ~]$ mysql -h <endpoint> -P 3306 -u <mymasteruser> -p
    ```
    - \<endpoint\>를  DB 인스턴스의 DNS 이름으로 대체 (port 번호 제외)
    - \<mymasteruser\> 를 마스터 사용자 이름으로 대체
  3. 암호를 입력하라는 요청을 받으면 사용한 마스터 암호 입력
    ```
    [ec2-user~]$ mysql -h tutorial-db-instance.cwpfd82zeivm.ap-northeast-2.rds.amazonaws.com -P 3306 -u tutorial_user -p
    Enter password:
    ```
  4. 데이터베이스 확인
    ```
    mysql> show databases;
    ```
  5. 데이터베이스 사용 선언
    ```
    mysql> use sample;
    ```
  6. Select SQL문 사용
    ```
    mysql>select * from Employees;
    ```

---
<a name="2.2.7"></a>
#### 2.2.7 DB 인스턴스 삭제
1. AWS Management Console에 로그인한 다음 https://console.aws.amazon.com/rds/ 에서 Amazon RDS 콘솔을 엽니다.
2. [Instances]에서 삭제할 DB 인스턴스를 선택합니다.
3. [Instance Actions]에 대해 [Delete]를 선택합니다.
4. [Create final Snapshot?]에 대해 [No]를 선택합니다.
5. [Yes, Delete]를 선택합니다.