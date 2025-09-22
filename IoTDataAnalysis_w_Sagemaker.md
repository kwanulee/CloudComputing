# 클라우드 기반 AI 서비스와 IoT 데이터 분석 (Amazon SageMaker 활용)

## 학습목표
- IoT 디바이스에서 수집된 데이터를 S3에 저장하고, SageMaker를 통해 간단한 ML 모델을 학습/배포한다.
- REST API 호출을 통해 IoT 플랫폼에서 AI 분석 결과를 연동하는 방법을 익힌다.

## 사전준비
1. AWS 계정 로그인 (https://console.aws.amazon.com)
2. IoT Core에 연결된 센서 데이터 → DynamoDB 저장까지 완료
3. 실습용 샘플 데이터셋 준비 (온도·습도 로그, CSV 형식 제공)

## Step 1. IoT 데이터 S3 업로드
1. AWS 콘솔에서 S3 서비스 선택
2. 새로운 버킷 생성 (예: iot-ai-data-bucket)
    - 리전: 서울(ap-northeast-2)
    - 기본 설정 유지 후 생성
3. 센서 로그 CSV 파일 업로드 (예: sensor_data.csv)

    예시 데이터 구조:
    ```
    timestamp,temperature,humidity
    2025-08-01 10:00,27.5,60
    2025-08-01 10:05,27.8,62
    2025-08-01 10:10,29.2,85
    ```

## Step 2. SageMaker Notebook Instance 생성
1. AWS 콘솔 → Amazon SageMaker → "Notebook instances"
2. 새 인스턴스 생성
    - 이름: iot-ai-notebook
    - 인스턴스 유형: ml.t2.medium
    - IAM Role: S3(bucket) 접근 권한 부여
3. 인스턴스 실행 후 Jupyter Notebook 열기

## Step 3. 데이터 불러오기 및 탐색
Jupyter Notebook에서 Python 코드 실행:

```python
import pandas as pd

# S3에 업로드된 CSV 파일 불러오기
import boto3
s3 = boto3.client('s3')
bucket = "iot-ai-data-bucket"
file_key = "sensor_data.csv"

obj = s3.get_object(Bucket=bucket, Key=file_key)
df = pd.read_csv(obj['Body'])

# 데이터 확인
print(df.head())
```

출력 예시:

```yaml
   timestamp  temperature  humidity
0 2025-08-01 10:00       27.5        60
1 2025-08-01 10:05       27.8        62
2 2025-08-01 10:10       29.2        85
```

## Step 4. 간단한 이상치 탐지 모델 (Isolation Forest)

```python
from sklearn.ensemble import IsolationForest

# 입력 데이터 (온도, 습도만 사용)
X = df[['temperature', 'humidity']]

# 모델 학습
model = IsolationForest(contamination=0.1, random_state=42)
df['anomaly'] = model.fit_predict(X)

# 결과 확인 (-1: 이상치, 1: 정상)
print(df[['temperature','humidity','anomaly']].head())
```

## Step 5. 모델 저장 및 배포
1. 학습된 모델을 pickle로 저장
2. SageMaker Endpoint로 배포 → API 형태로 IoT 플랫폼에서 호출 가능
```
import joblib
joblib.dump(model, "iot_model.pkl")
```
(수업에서는 Endpoint 배포까지는 안내만 하고, 간단한 로컬 예측 실습까지 진행)

## Step 6. 예측 테스트
```python
# 새로운 데이터 포인트 테스트
test_data = [[30.0, 90]]  # 온도 30도, 습도 90%
result = model.predict(test_data)
print("예측 결과:", "정상" if result[0] == 1 else "이상치")
```

## Step 7. 결과 시각화
```python
import matplotlib.pyplot as plt

plt.scatter(df['temperature'], df['humidity'], c=df['anomaly'], cmap='coolwarm')
plt.xlabel("Temperature")
plt.ylabel("Humidity")
plt.title("IoT Sensor Data Anomaly Detection")
plt.show()
```

## Step 8. 실습 과제
- 자신의 센서 데이터(또는 제공된 샘플 데이터)를 활용하여 이상 탐지 결과를 시각화
- 결과 그래프 캡처 및 “AI 기능 아이디어 제안서(1p)” 작성 후 보고서 제출
