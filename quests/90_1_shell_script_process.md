## **✅ 문제 : 간단한 서버 관리 스크립트 작성**

### **🔧 요구사항**

* `start`: 포트 8000에서 `http.server`를 백그라운드로 실행 (`nohup`, 로그는 `server.log`)

* `stop`: 실행 중인 프로세스를 찾아 종료

* `status`: 프로세스가 실행 중인지 확인하여 출력

* `restart`: 중지 후 다시 실행

  ### **🎯 실행 예시**

  $ ./webserver.sh start  
  서버가 백그라운드에서 시작되었습니다.  
    
  $ ./webserver.sh status  
  서버 실행 중입니다. PID: 13579  
    
  $ ./webserver.sh stop  
  서버가 종료되었습니다.  
    
  $ ./[webserver.sh](http://webserver.sh) tail\_log  
  … log message 확인


문제 모두 조건에 따라:

* `if [ "$1" == "start" ]` 식으로 흐름 제어

* 변수 `PORT`, `PID`, `LOGFILE` 등을 정의해 구성 가능


### 작성

#### 서버 동작
#nohup {명령어} & background 동작 // 
nohup python3 -m http.server 8000 --bind 0.0.0.0 > server.log &

```shell
vi webserver.sh 


#!/bin/bash 

##############################
#
# 2025.07.28  
# mklee
# study practice
# 
#
##############################

input=$(echo $1 | tr 'A-Z' 'a-z')

#pid
pid=$(ps aux | grep http | grep python3 | tr -s " " | cut -d" " -f2)


if [ $input == "start" ]; then
        nohup python3 -m http.server 8000 --bind 0.0.0.0 &>> server.log &
        echo "서버가 백그라운드에서 시작되었습니다."
elif [ $input  == "status" ]; then
        echo "서버가 실행 중 입니다. PID: $pid"
elif [ $input  == "stop" ]; then
        kill -9 $pid
        echo "서버가 종료 되었습니다."
elif [ $input  == "tail_log" ]; then
          echo "=로그 메시지 확인=."
                tail -f server.log
elif [ $input  == "restart" ]; then
        kill -9 $pid
        sleep 5
        nohup python3 -m http.server 8000 --bind 0.0.0.0 &>> server.log &
        echo "서버가 재시작 되었습니다."
else
        echo "올바른 명령어를 입력해야합니다.(start,status,stop,tail_log,restart)"
fi

```

### 결과

```shell


```