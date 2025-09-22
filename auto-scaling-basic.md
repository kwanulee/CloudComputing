## Auto Scaling 소개

### 1. Auto Scaling이란?
- 애플리케이션의 로드에 따라 지정된 범위의 EC2 인스터스 수를 보유하도록 보장하면서 EC2 인스턴스를 자동으로 새로이 시작하거나 종료시킴
	- Auto Scaling 그룹의 최소, 최대 인스턴스 수를 지정하면 지정된 범위 내로 그룹의 크기 유지
	- 원하는 용량을 지정한 경우 그룹을 생성한 다음에는 언제든지 Auto Scaling에서 해당 그룹에서 지정된 만큼의 인스턴스를 보유
	- 확장조정 정책을 지정했다면 Auto Scaling에서는 애플리케이션의 늘어나거나 줄어드는 수요에 따라 인스턴스를 시작하거나 종료 가능

	![](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/images/asg-basic-arch.png)

- **Auto Scaling 구성 요소**
	- **Auto Scaling 그룹**
		- 확장 혹은 축소될 EC2 인스턴스들의 논리적인 단위로, 인스턴스의 최소 및 최대 수와 원하는 인스턴스 수를 지정
	- **시작 구성 (Launch Configuration)**
		- Auto Scaling 그룹의 EC2 인스턴스용 템플릿
			- 인스턴스의 AMI, 인스턴트 유형, 키페어, 보안그룹 등 지정
	- **확장계획**
		- Auto Scaling에 확장을 수행하는 시기와 방식 설정
		- 예, 지정된 조건에 따른 확장, 일정에 따른 확장

### 2. Auto Scaling 이점
- Auto Scaling를 애플리케이션 아키텍처에 추가하는 것은 AWS 클라우드의 이점을 극대화할 수 있는 한 방법임
	- 내결함성(Fault Tolerance) 향상.
		- Auto Scaling에서는 인스턴스가 비정상 상태일 때 이를 감지하여 종료한 다음 이를 대체할 인스턴스를 시작할 수 있습니다.
		- 복수의 가용 영역을 사용하도록 Auto Scaling을 구성할 수도 있습니다. 하나의 가용 영역이 사용 불가 상태가 되면 Auto Scaling에서는 다른 가용 영역에서 새 인스턴스를 시작하여 이에 대처할 수 있습니다.
	- 가용성(Availability) 향상.
		- Auto Scaling을 통해 애플리케이션이 항상 현재 트래픽 요구를 처리할 수 있는 적절한 용량을 갖출 수 있습니다.
	- 비용 관리 개선.
		- Auto Scaling는 필요에 따라 용량을 동적으로 확장 및 축소할 수 있습니다. 사용한 EC2 인스턴스에 대해서만 비용을 지불하므로, 실제로 인스턴스가 필요할 때 이를 시작하고 필요 없어지면 종료함으로써 비용을 절감합니다.

- Auto Scaling의 이점에 대한 자세한 내용은 [여기](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/auto-scaling-benefits.html)를 참조하십시오.

### 3. 인스턴스 분산
- Auto Scaling은 인스턴스를 Auto Scaling 그룹에 대해 활성화된 가용 영역 간에 고르게 분산하려고 시도
	- 이 시도가 실패하는 경우 성공할 때까지 Auto Scaling는 다른 가용 영역에서 인스턴스 시작을 계속 시도.
	- Auto Scaling는 VPC에서 시작하는 각 인스턴스에 대해 가용 영역에서 서브넷을 무작위로 선택

 	![](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/images/as-sample-web-architecture-diagram-with-asgs-and-azs.png)

### 4. 재분배 활동
- 특정 작업 및 조건으로 인해 Auto Scaling 그룹이 가용 영역 간에 불균형하게 분배될 수 있습니다. Auto Scaling은 **다음 조건 중 어느 하나의 상황에서 재분배 활동을 생성**하여 이에 대처합니다.
	- 그룹의 가용 영역에 대한 변경 요청을 발급한 경우
	- 그룹의 불균형을 야기하는 특정 인스턴스의 종료를 명시적으로 요청한 경우
	- 이전에 용량 부족을 겪었던 가용 영역이 복구되어 추가적인 가용 용량이 확보된 경우
	- 이전에 스팟 시장 가격이 스팟 입찰 가격보다 높았던 가용 영역은 시장 가격이 하락하여 입찰 가격 아래로 떨어지는 경우
- **재분배 시** Auto Scaling에서는 **이전 인스턴스를 종료하기 전에 새 인스턴스를 시작하여 재분배로 인해 애플리케이션의 성능이나 가용성이 저하되지 않도록** 합니다.
	- AWS 시스템에서는 재분배 활동 중에 그룹의 지정된 최대 용량을 10% 여유(또는 인스턴스 1개의 여유 중 큰 쪽)만큼 일시적으로 초과할 수 있습니다

### 5. Auto Scaling 수명 주기
- Auto Scaling 그룹의 EC2 인스턴스별로 수명 주기가 있음
- 다음 그림은 Auto Scaling 수명 주기에서 인스턴스 상태 간 전환을 보여 줌

	![](https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/images/auto_scaling_lifecycle.png)

	- **Scale out(확장)**
		- 확장 이벤트 발생 조건
			- 그룹의 크기를 수동 늘림
			- 지정된 수요 증가에 따른 동적 확장
			- 일정 기반으로 크기 조정
		- 확장 이벤트가 발생하면 Auto Scaling 그룹이 할당된 시작 구성을 사용하여 필요한 수의 EC2 인스턴스를 시작
		- 인스턴스는 **Pending(보류)** 상태에서 시작

	- **Scale in(축소)**
	 	- 축소 이벤트 발생 조건
			- 그룹의 크기를 수동 줄임
			- 지정된 수요 증가에 따른 동적 축소
			- 일정 기반으로 크기 축소
		- 축소 이벤트가 발생하면 Auto Scaling 그룹에서 하나 이상의 인스턴스를 분리
		- Auto Scaling 그룹에서 분리되어 종료 중인 인스턴스는 **Terminating(종료)** 상태로 들어가며, 다시 서비스 상태로 돌아갈 수 없습니다

---
### 참고자료
1. Amazon EC2 Auto Scaling 사용 설명서, https://docs.aws.amazon.com/ko_kr/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html


## Auto Scaling 시작하기

### 1. 시작 구성 생성
시작 구성은 Auto Scaling이 사용자를 위해 EC2 인스턴스 시작에 사용할 Amazon 머신 이미지(AMI) ID, 해당 인스턴스 유형, 키 페어, 보안 그룹, 블록 디바이스 매핑 등의 정보를 포함하여 시작 구성을 생성합니다.

1. https://console.aws.amazon.com/ec2/에서 Amazon EC2 콘솔을 엽니다.
2. 화면 상단의 탐색 모음에서 로드 밸런서를 만들 때 사용한 리전을 선택합니다.
3. 왼쪽 탐색 창 메뉴의 [**AUTO SCALING**] 아래에서 [**시작 구성**]를 선택합니다.
4. [**Auto Scaling 그룹 생성**]을 선택합니다.
5. [**시작 구성 생성**]을 선택합니다.
6. [**1. AMI 선택**] 단계에서 **Amazon Linux AMI 2017.09.1 (HVM), SSD Volume Type - ami-5e1ab730** 를 선택합니다.
7. [**2. 인스턴스 유형 선택**] 단계에서 인스턴스의 하드웨어 구성을 선택한 다음 [**다음: 세부 정보 구성**]를 선택합니다.
8. [**3. 세부 정보 구성**] 단계에서 [**이름**]에 시작 구성의 이름을 입력: 예 *Lab-Config*
9. [**검토로 이동**]를 선택.
10. [**6. 검토**] 단계에서 페이지의 내용을 검토후 [**시작 구성 생**] 클릭
11. [**기존 키 페어 선택 또는 새 키 페어 생성**] 페이지에서 나열된 옵션 중 하나를 선택합니다. 승인 확인란을 선택한 다음 [**시작 구성 생성**]을 선택합니다.
	- 새로운 시작 구성이 생성되고, Auto Scaling 그룹 생성을 위한 페이지가 시작됩니다.

### 2. Auto Scaling 그룹 생성
Auto Scaling 그룹은 EC2 인스턴스의 모음으로, 항상 유지 관리해야 하는 인스턴스 수를 지정합니다.

[**Auto Scaling 그룹 생성**] 페이지에서 다음을 수행.

1. [**그룹 이름**]]에 Auto Scaling 그룹 이름을 입력합니다(예: *lab-group*).
2. [**그룹 크기**]를 기본값인 1 인스턴스로 둠.
3. [**네트워크**]에서 기본값 선택
4. [**서브넷**]에서 필드를 클릭한 다음 나타나는 첫번째 서브넷을 선택
5. [**다음:조정 정책 구성**] 클릭
6. [**이 그룹을 초기 크기로 유지**] 항목을 선택한 채로 [**검토**] 클릭
7. 선택항목을 검토 한 다음 [**Auto Scaling 그룹 생성**] 클릭
8. [**닫기**] 클릭

### 3. Auto Scaling 그룹 확인
1. 왼쪽 탐색 창 메뉴의 [**AUTO SCALING**] 아래에서 [**Auto Scaling 그룹**] 선택
2. 앞에서 생성한 Auto Scaling 그룹을 선택하고, 아래 [**세부 정보**] 탭에서 자세한 정보를 조회
3. [**활동 기록**] 탭에서 현재 Auto Scaling 그룹의 활동 내역을 조회할 수 있음. EC2 인스턴스가 시작되면 **성공**으로 변경됨

	![](images/auto-scaling-activity-record.png)

4. [**인스턴스**] 탭에서 Auto Scaling 그룹에 의해서 시작된 인스턴스에 대한 정보 조회

	![](images/auto-scaling-instance-status.png)

### 4. Auto Scaling 테스트
현재 설정된 Auto Scaling 그룹은 최소 크기가 1로 설정되어 있으므로, 단 하나의 실행 중인 인스턴스를 종료시키면 Auto Scaling은 새로운 인스턴스를 자동으로 시작시키게 된다.

1. 왼쪽 탐색 창 메뉴의 [**인스턴스**] 아래에서 [**인스턴스**] 선택
2. 실행중인 인스턴스를 **종료** 시킴
3. 왼쪽 탐색 창 메뉴의 [**AUTO SCALING**] 아래에서 [**Auto Scaling 그룹**] 선택
4. [**인스턴스**] 탭에서 다음 상태를 차례로 확인
	- 초기에는 수명주기가 **종료** 상태
		![](images/auto-scaling-instance-terminating.png)

	- 다음으로 수명주기가 **보류** 상태
		![](images/auto-scaling-instance-pending.png)

	- 최종적으로 수명주기가 **InService** 상태
		![](images/auto-scaling-instance-inservice.png)
5. [**활동 기록**] 탭에서 지금까지의 활동 내역을 조회

	 ![](images/auto-scaling-launch-anew.png)
