**MIdterm**

예상 : MPU9250 IMU Sensor -> smbus(I2C) -> Jupyter -> AWS 저장 
1. 라즈베리파이 연결 후 CMD 창에서 가상환경 생성 및 실행, 필수 라이브러리 설치
    python3 -m venv venv
    source venv/bin/activate
    
    pip install smbus
    pip install imusensor
    pip install boto3

2. 라즈베리파이에서 실행하는 IMU 센서 데이터 읽기 코드
    import smbus
    from imusensor.MPU9250 import MPU9250
    from time import sleep
    
    bus = smbus.SMBus(1)
    address = 0x68
    
    imu = MPU9250.MPU9250(bus, address)
    imu.begin()
    
    while True:
        imu.readSensor()
    
        print("Temp:", imu.Temp)
        print("Gyro:", imu.GyroVals)
    
        sleep(1)
    
3. AWS 연결 설정 (boto3 설정)
    import boto3
    dynamodb = boto3.resource(
        'dynamodb',
        aws_access_key_id='YOUR_ACCESS_KEY',
        aws_secret_access_key='YOUR_SECRET_KEY',
        region_name='ap-northeast-2'
    )
    table = dynamodb.Table('YourTableName')

4. 센서 데이터를 DynamoDB에 저장
    from datetime import datetime
    while True:
        imu.readSensor()
    
        data = [
            {"parameter": "T", "value": imu.Temp},
            {"parameter": "Gx", "value": imu.GyroVals[0]},
            {"parameter": "Gy", "value": imu.GyroVals[1]},
            {"parameter": "Gz", "value": imu.GyroVals[2]},
        ]
    
        for d in data:
            table.put_item(
                Item={
                    'parameter': d["parameter"],
                    'Time': datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
                    'value': str(d["value"])
                }
            )
    
        sleep(1)
    
5. 실행 순서 정리
(1) 가상환경 실행
    source venv/bin/activate
(2) 코드 실행
    python sensor_to_aws.py
(3) AWS 콘솔 접속
DynamoDB → Tables → 데이터 확인
---
CMD에서 IP 바로 찾는 방법 
    for /L %i in (1,1,50) do ping 172.20.10.%i -n 1 -w 100

---
CMD에서 Jupyter 접속하는 방법
    j1
(내 노트북은 Jupyterlab 설치를 할 때, 지속적으로 충돌이 발생하여, 가상환경을 생성 후 그 안에서 주피터랩을 설치함. 즉, 항상 가상 환경을 통해 접속해야하는 불편함이 존재함으로 
echo "alias jl='source ~/myenv/bin/activate && jupyter lab --ip=0.0.0.0 --port=8888 --no-browser'" >> ~/.bashrc
source ~/.bashrc
을 저장하였기에 앞으로는 jl 만 입력하면 바로 Jupyter에 접속됨)
* Jupyterlab 실행을 종료할 때는 ctrl + c 누르고 y 누르기

---
About CMD
1. 가상환경 생성
   python -m venv myenv
2. 가상환경 활성화
   myenv\Scripts\activate
3. Jupyter Notebook 실행
   jupyter notebook --ip=0.0.0.0 --port=8888 --no-browser
   -> url(http://127.0.0.1:8888/?token=xxxxxxxx) 나오면 노트북 브라우저에 넣기(http://라즈베리파이IP주소:8888/?token=토큰값)
---
About AWS
 1. Sign in using root user email 로 로그인하기
