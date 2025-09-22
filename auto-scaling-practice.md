## Auto Scaling 동적 조정 실습

- 사전 준비
- 1단계: 시작 구성 생성
- 2단계: Auto Scaling 그룹 생성
		- 로드 밸런서 사용
		- Scaling  정책 설정
- 3단계: Auto Scaling 그룹 확인
- 4단계: 테스트

---
### 사전준비
- Amazon Linux 운영체제에 Apache 웹서버와 PHP가 설치된 EC2 인스턴스를 실행
- 아래와 같이 nano 에디터로 /var/www/html/index.php 파일을 생성하여 저장

	```
	[ec2-user ~]$ sudo nano /var/www/html/index.php
	```

	```
	<?php
	   echo "PHP 서버 1000000만번 루프돌기 작업 시작<br>";
	   for($i=0; $i<1000000; $i++) { echo $i; }
	   echo "<br>작업종료";
	?>

	```
	- 웹브라우저에서 EC2 인스턴스의 DNS 주소로 접근하여 결과 확인

- EC2 인스턴스로부터 AMI 생성
- 로드 밸런서 생성
	- **클래식 로드 밸런서** 생성
		- **단계4: 상태 검사 구성** 에서 **Ping 경로**를 **/** 로 설정

		![](images/auto-scaling-classic-lb-creation.png)

		- Auto Scaling 그룹에 로드 밸런서를 사용하려는 경우, 로드 밸런서에 **EC2 인스턴스를 등록할 필요가 없습니다.**

---
### 1단계: 시작 구성 생성
- 시작 구성은 Auto Scaling이 사용자를 위해 EC2 인스턴스 시작에 사용할 **Amazon 머신 이미지(AMI) ID, 해당 인스턴스 유형, 키 페어, 보안 그룹, 블록 디바이스 매핑 등의 정보를 포함하여 시작 구성을 생성**


1. https://console.aws.amazon.com/ec2/ 에서 Amazon EC2 콘솔을 염.
2. 화면 상단의 탐색 모음에서 로드 밸런서를 만들 때 사용한 리전을 선택.
3. 왼쪽 탐색 창 메뉴의 [**AUTO SCALING**] 아래에서 [**시작 구성**]를 클릭.
4. [**Auto Scaling 그룹 생성**] 클릭 후, 다음 페이지에서 [**시작 구성 생성**]을 클릭.
5. [**1.AMI 선택**] 페이지에서 [**내 AMI**]를 클릭하고, 준비된 EC2 인스턴스 AMI를 선택.
6. [**2.인스턴스 유형 선택**] 페이지에서 인스턴스의 하드웨어 구성을 선택한 다음 [**다음: 세부정보 구성**]를 클릭.
7. [**3. 세부 정보 구성**] 페이지의 [**이름**]에 시작 구성의 이름을 입력 (예, *lc\_autoscaled\_webserver*) 후, [**검토로 이동**]를 클릭.
8. [**6.검토**] 페이지의 **보안 그룹**의  [**보안 그룹 편집**]을 클릭
	- HTTP와 SSH 를 허용하는 기존 보안 그룹 선택 후 [**검토**]와 [**시작 구성 생성**]을 차례로 클릭
9. [**기존 키 페어 선택**] 페이지에서 나열된 옵션 중 하나를 선택. 승인 확인란을 선택한 다음 [**시작 구성 생성**]을 클릭.


---
### 2단계: Auto Scaling 그룹 생성
- Auto Scaling 그룹은 EC2 인스턴스의 모음으로, 인스턴스를 시작하는 데 사용하려는 시작 구성과 항상 유지 관리해야 하는 인스턴스 수를 지정

1. [**1. Auto Scaling 그룹 세부 정보 구성**] 페이지에서 다음을 수행.

	- [**그룹 이름**]에 Auto Scaling 그룹 이름을 입력합니다(예: *asg-webserver*).
	2. [**그룹 크기**]를 기본값인 1 인스턴스로 둠.
	3. [**네트워크**]에서 [vpc-*****(default)]를 선택
	4. [**서브넷**]에서 하나 이상의 서브넷을 선택
		![](images/auto-scaling-subnet-config.png)
	
	- [**고급 세부 정보**]를 확장하여,
		- **하나 이상의 로드 밸런서에서 트래픽 수신**의 체크박스를 체크
		- **클래식 로드 밸런서**의 빈 칸을 클릭하여, 생성된 클래식 로드 밸런서를 선택
		- 다른 설정은 그대로 두고 [**다음:조정 정책 구성**]을 클릭
		![](images/auto-scaling-advanced-details.png)

6. [**2. 조정 정책 구성**]  페이지에서
	- 그룹의 크기를 자동으로  조정하고자 하는 경우, [**조정 정책을 사용하여 이 그룹의 용량 조정**]을 선택하여, 조정 정책을 설정
	- Auto Scaling 그룹의 최소 크기 및 최대 크기를 지정
		![](images/auto-scaling-size.png)

	- [**단계 또는 단순 조정 정책을 사용하여 Auto Scaling 그룹 조정**] 클릭
		![](images/auto-scaling-adjusting-group-size.png)
		
	- [**새 경보 추가**] 클릭 후, 아래와 같이 정보 입력후, [**Create Alarm**] 클릭
	
		![](images/auto-scaling-create-alarm.png)
	
	- [**작업 수행**] 부분에서 추가된 인스턴스 갯수 설정 (예, 1)
	 	![](images/auto-scaling-instance-add.png)
	 	
	- 실습을 간단히 하기 위해서, [**그룹 크기 감소**] 부분에서 X 표시를 눌러 삭제
		- 그룹 크기 감소에 대한 조정을 위해서는 이 부분을 설정하면 됨
	
	<!--
	- **그룹 크기 조정** (대상 추적 조정 정책을 가진 Auto Scaling 그룹 만들기)
		- **지표 유형** 항목 중에 한 가지 선택. 예, *평균 CPU 사용률*
		- **대상 값** 지정, 예, *20*
-->
	- [**검토**] 클릭.

7. [**Auto Scaling 그룹 생성**] 클릭

---
### 3단계: Auto Scaling 그룹 확인
1. [**Auto Scaling 그룹**] 페이지에서 방금 생성한 Auto Scaling 그룹을 선택합니다.
2. [**활동 기록**] 탭의 [**상태**] 열에 Auto Scaling 그룹에서 인스턴스를 시작했는지 여부가 표시됩니다.
3. [**인스턴스**] 탭의 [**수명 주기**] 열에 인스턴스의 상태가 표시됩니다. 인스턴스를 시작하는 데 약간 시간이 걸립니다. 인스턴스가 시작되면 해당 수명 주기 상태가 **InService**로 변경됩니다.
	- [**상태**] 열에 해당 인스턴스에 대한 EC2 인스턴스 상태 확인 결과가 표시됩니다.
5. [**조정 정책**] 탭에서 Auto Scaling 그룹에 대해 만든 정책을 확인할 수 있습니다.

---
### 4단계: 테스트

#### 아파치 웹서버 성능검사도구 : ab (apache benchmarking)
- 형식

	ab [-n request] [ -c concurrency] http://hostname

- 예: http://load-balancer-dns-addresss로 동시 1000개의 요청을 10000번 보냄

	ab –n 10000 –c 1000 http://load-balancer-dns-addresss

![](images/apache-benchmarking.png)

#### 테스트 절차
1. Auto scaling에 의해 생성된 EC2 인스턴스에 연결하고, **top** 명령어를 실행하여 현재 **CPU 사용률**을 포함하여 시스템의 분석정보를 모니터링 한다.
	
	[ec2-user@ip-xxx-xx-xx-xx ~]$ top
	
	![](images/auto-scaling-top.png)
	
1. Apache가 설치된 EC2 인스턴스를 실행시키고, 이 인스턴스에 ssh로 접속한다.
2. Auto Scaling 그룹에 설정된 클래식 로드 밸런서 DNS 주소로 밴치마킹 요청을 보냄

	[ec2-user@ip-xxx-xx-xx-xx ~]$ ab –n 100000 –c 3 http://*load-balancer-dns-addresss*/index.php

3. AWS의 CloudWatch로 접속
	- 화면 상단의 Services를 클릭하여 전체 서비스 목록이 보이면, **관리도구** 하위에 **CloudWatch** 가 있음

4. **경보**가 발생되면, [**AUTO SCALING**]의 [**Auto Scaling 그룹**]에서 인스턴스가 확장되었는지 확인
	![](images/auto-scaling-result.png)

	- ([**인스턴스**]의 [**인스턴스**] 화면에서도 새로이 확장된 인스턴스들을 확인할 수 있음)

	

