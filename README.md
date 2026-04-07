# Microprocessor-Application-Design
Microprocessor Application Design

CMD 에서 IP 찾는 방법

for /L %i in (1,1,50) do ping 172.20.10.%i -n 1 -w 100

내 컴퓨터의 경우 주피터랩 설치에 충돌이 지속적으로 발생하여 가상 환경 안에서 주피터 랩을 다운받음
즉, 항상 가상 환경을 통해 접속해야하는 불편함이 존재함으로 

echo "alias jl='source ~/myenv/bin/activate && jupyter lab --ip=0.0.0.0 --port=8888 --no-browser'" >> ~/.bashrc
source ~/.bashrc

을 저장 후 
앞으로는
jl
만 입력하면 접속됨


주피터 랩 실행 종료 할 때 컨드롤+C 누르고 y 누르기
