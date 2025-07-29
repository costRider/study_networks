### **🧪 문제 1: 특정 IP 차단 상태 확인 후 차단 설정**

#### **✅ 실행 예시**

$ sudo ./problem1.sh

\[INFO\] 현재 rich rule 목록에 192.168.0.100 차단 룰이 존재하지 않습니다.

\[INFO\] 차단 룰을 추가합니다...

success

또는

$ sudo ./problem1.sh

\[INFO\] 192.168.0.100은 이미 차단되어 있습니다.

\[SKIP\] 추가 작업을 수행하지 않습니다.

---
```shell
#!/bin/bash

###########################
#
#       2025.07.29
#       mklee && ssh
#       study practice
#
#
###########################

#vi problem1.sh
input="$1"
sudo firewall-cmd --list-all | grep -w $1

#if 조건절에 들어갈 내용 - 0이 grep의 찾은 결과값(true)1은 grep의 못 찾은 결과값(false)
if [ $? -eq 1 ]; then
   sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" source address="$input" reject'
        echo "[info] IP reject"
        echo "[info] add $input"
        echo "success"
else
        echo "[info] 이미 차단"
        echo "[skip] 추가 작업 무시"
fi

```

### **🔒 문제 2: 포트 8080이 열려 있다면 닫기**

#### **✅ 실행 예시**

$ sudo ./problem2.sh

\[INFO\] 포트 8080/tcp 이 열려 있습니다. 제거합니다...

success

또는

$ sudo ./problem2.sh

\[INFO\] 포트 8080/tcp 이 열려 있지 않습니다. 아무 작업도 수행하지 않습니다.

---
```shell
#!/bin/bash

###########################
#
#	2025.07.29
#	mklee && ssh
#	study practice
#
#
###########################

#vi problem2.sh

$input="$1"
sudo firewall-cmd --list-all | grep -w $1

#if 조건절에 들어갈 내용 - 0이 grep의 찾은 결과값(true)1은 grep의 못 찾은 결과값(false)
if [ $? -eq 0 ]; then
	echo "[info] [INFO] 포트 8080/tcp 이 열려 있습니다. 제거합니다..."
	sudo firewall-cmd --remove-port="$input"
	echo "success"
else
	echo "[info] [INFO] 포트 8080/tcp 이 열려 있지 않습니다. 아무 작업도 수행하지 않습니다."
fi
```