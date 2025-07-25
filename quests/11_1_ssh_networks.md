# **Network Shell Script 실습 문제**

## **문제 환경 설정**

실습을 위해 다음 파일들을 생성하세요:

\# 1\. 네트워크 로그 파일 생성

cat \> network.log \<\< 'EOF'

2024-01-15 10:30:25 192.168.1.100 CONNECT success

2024-01-15 10:30:30 192.168.1.101 CONNECT failed

2024-01-15 10:31:15 192.168.1.102 CONNECT success

2024-01-15 10:31:20 192.168.1.100 DISCONNECT success

2024-01-15 10:32:10 192.168.1.103 CONNECT success

2024-01-15 10:32:15 192.168.1.101 CONNECT success

2024-01-15 10:33:25 192.168.1.104 CONNECT failed

2024-01-15 10:33:30 192.168.1.102 DISCONNECT success

EOF

\# 3\. 접속 통계 파일 생성

cat \> connections.txt \<\< 'EOF'

192.168.1.100 5

192.168.1.101 12

192.168.1.102 8

192.168.1.103 3

192.168.1.104 15

192.168.1.105 7

EOF

---

## **문제 1: 네트워크 연결 상태 분석기**

**요구사항:**

* `network.log` 파일을 분석하여 연결 성공/실패 통계를 출력하는 스크립트 작성  
* 전체 연결 시도 수, 성공 수, 실패 수를 계산  
* 성공률을 백분율로 표시 (소수점 제거)

**출력 형태:**

\=== 네트워크 연결 분석 결과 \===

전체 연결 시도: X건

성공: Y건

실패: Z건

성공률: W%

**제한사항:**

* if문과 변수만 사용  
* grep, wc, cut 명령어 활용  
* 파일명은 스크립트 실행 시 첫 번째 인자로 받기


### 작성 

```shell

vi network_analyze.sh

#!/bin/bash


#####################################################################
#
# [ Script Information ]
# Filename    : network_analyze.sh
# Description : Top list of connections by IP address
# Usage       : ./network_analyze.sh
# Example     : ./network_analyze.sh
#
# [ Author Information ]
# Author      : Mklee
# Created On  : 2025-07-24
# Last Update : 2025-07-25
# Version     : 1.0
#
# [ Notes ]
# - Output total number of connection attempts, successes, and failures
#       Display success rate as a percentage
#
#
#####################################################################

#Variable declaration
attemConn=$(grep . network.log | grep -v DIS  | wc -l)
success=$(grep success network.log | grep -v DIS | wc -l)
failed=$(grep failed network.log | wc -l)
rate=$((success * 100 / attemConn))

echo "== Network Analyze=="
#전체 연결 시도: X건
echo "Attempted connection: $attemConn"

#성공: Y건
echo "Successful: $success"

#실패: Z건
echo "Failed: $failed"

#성공률: W%
echo "Success rate: $rate %"

```

### 결과
```shell

== Network Analyze==
Attempted connection: 6
Successful: 4
Failed: 2
Success rate: 66 %


```

---

## **문제 2: IP 주소별 접속 빈도 상위 리스트**

**요구사항:**

* `network.log`에서 IP 주소별 접속 횟수를 계산  
* 접속 횟수 기준으로 내림차순 정렬하여 상위 3개만 출력  
* 각 IP의 첫 접속 시간도 함께 표시

**출력 형태:**

\=== 접속 빈도 TOP 3 \===

1위: 192.168.1.XXX (X회) \- 첫 접속: 10:XX:XX

2위: 192.168.1.XXX (X회) \- 첫 접속: 10:XX:XX  

3위: 192.168.1.XXX (X회) \- 첫 접속: 10:XX:XX

**제한사항:**

* if문과 변수만 사용  
* cut, sort, uniq, grep 명령어 활용  
* head나 tail로 결과 제한

### 작성

```shell
vi ip_conn_top_list.sh

#!/bin/bash

#####################################################################
#
# [ Script Information ]
# Filename    : ip_conn_top_list.sh
# Description : Network Connection Status Analyzer
# Usage       : ./ip_conn_top_list.sh
# Example     : ./ip_conn_top_list.sh
#
# [ Author Information ]
# Author      : Mklee
# Created On  : 2025-07-24
# Last Update : 2025-07-25
# Version     : 1.0
#
# [ Notes ]
# - Calculate the number of connections by IP address
#	Only top 3 outputs in descending order based on number of connections
#	Displays the first connection time of each IP as well
#
#
#####################################################################

list=$(grep . network.log | cut -d " " -f3 | sort)

top1=$(grep . network.log | grep -v DIS | cut -d " " -f3 | sort | uniq -c | sort -nr | head -n 3 | awk "NR==1")
top1Ip=$(echo $top1 | cut -d " " -f2)
top1Count=$(echo $top1 | cut -d " " -f1)
top1Conn=$(grep . network.log | grep $top1Ip | cut -d " " -f2 | sort -n | head -n 1)

top2=$(grep . network.log | grep -v DIS | cut -d " " -f3 | sort | uniq -c | sort -nr | head -n 3 | awk "NR==2")
top2Ip=$(echo $top2 | cut -d " " -f2)
top2Count=$(echo $top2 | cut -d " " -f1)
top2Conn=$(grep . network.log | grep $top2Ip | cut -d " " -f2 | sort -n | head -n 1)

top3=$(grep . network.log | grep -v DIS | cut -d " " -f3 | sort | uniq -c | sort -nr | head -n 3 | awk "NR==3")
top3Ip=$(echo $top3 | cut -d " " -f2)
top3Count=$(echo $top3 | cut -d " " -f1)
top3Conn=$(grep . network.log | grep $top3Ip | cut -d " " -f2 | sort -n | head -n 1)

#1위: 192.168.1.XXX (X회) \- 첫 접속: 10:XX:XX
echo "1st: $top1Ip (Count: $top1Count) - First Connection : $top1Conn"
#2위: 192.168.1.XXX (X회) \- 첫 접속: 10:XX:XX
echo "2nd: $top2Ip (Count: $top2Count) - First Connection : $top2Conn"
#3위: 192.168.1.XXX (X회) \- 첫 접속: 10:XX:XX
echo "3rd: $top3Ip (Count: $top3Count) - First Connection : $top3Conn"

```

### 결과
```shell
1st: 192.168.1.101 (Count: 2) - First Connection : 10:30:30
2nd: 192.168.1.104 (Count: 1) - First Connection : 10:33:25
3rd: 192.168.1.103 (Count: 1) - First Connection : 10:32:10

```

---

## **문제 3: 서버 상태 점검 스크립트**

**요구사항:**

* `servers.sh` 실행해 각 서버에 대해 ping 테스트 실행  
* 응답 있는 서버와 없는 서버를 구분하여 출력  
* 응답 시간이 100ms 이상인 서버는 "느림" 표시

**입력 형태:**

	**\~$ [servers.sh](http://servers.sh) 123.92.0.12**

**출력 형태:**

\=== 서버 상태 점검 결과 \===

\[정상\] web01 (**123.92.0.12**) \- 응답시간: XXms

OR

\=== 서버 상태 점검 결과 \===

\[오프라인\] db01 (**123.92.0.11**) \- 응답없음

...

**제한사항:**

* if문과 변수만 사용  
* cut, ping 명령어 활용  
* ping은 1회만 실행 (`ping -c 1`)


```shell

vi server_health_check.sh

#!/bin/bash

#####################################################################
#
# [ Script Information ]
# Filename    : server_health_check.sh
# Description : Checks server status using a given IP address
# Usage       : ./server_health_check.sh {ip_addr}
# Example     : ./server_health_check.sh 192.168.0.10
#
# [ Author Information ]
# Author      : Mklee
# Created On  : 2025-07-24
# Last Update : 2025-07-25
# Version     : 1.0
#
# [ Notes ]
# - Running a ping test for each server
#	Outputs servers with and without responses
#	Servers with response times greater than 100 ms display "slow"
#
#
#####################################################################

pingStatus=$(ping -c 1 $1 | grep received)

echo "$pingStatus" | grep -q "error" && 
  echo -e "===Server Health Check===\necho "[OFFLINE] $1 - No Response" || 
  echo -e "===Server Health Check===\necho "[ONLINE] $1 - Response: $(echo "$pingStatus" | awk '{print $NF}') 
$( [ $(echo "$pingStatus" | awk '{print int($NF)}') -ge 100 ] && echo '(Slow)' )"

```

### 결과
```shell

=== Server Health Check ===
[ONLINE] 192.168.0.8 - Response: 0ms


=== Server Health Check ===
[OFFLINE] 192.168.0.9 - No Response


```

---

## **문제 4: 네트워크 트래픽 임계값 모니터링**

**요구사항:**

* `connections.txt`에서 접속 수가 10 이상인 IP를 "높음", 5-9는 "보통", 4 이하는 "낮음"으로 분류  
* 각 분류별로 개수 계산하여 출력  
* "높음" 분류의 IP들만 별도로 나열

**출력 형태:**

\=== 트래픽 분석 결과 \===

높음(10회 이상): X개

보통(5-9회): Y개  

낮음(4회 이하): Z개

\[주의 필요 IP 목록\]

192.168.1.XXX (XX회)

192.168.1.XXX (XX회)

**제한사항:**

* if문과 변수만 사용  
* cut, sort 명령어 활용  
* 숫자 비교를 위한 조건문 사용

```shell
vi traffic_monitor.sh

#!/bin/bash

#####################################################################
#
# [ Script Information ]
# Filename    : traffic_monitor.sh
# Description : Monitoring Network Traffic Thresholds
# Usage       : ./traffic_monitor.sh
# Example     : ./traffic_monitor.sh
#
# [ Author Information ]
# Author      : Mklee
# Created On  : 2025-07-24
# Last Update : 2025-07-25
# Version     : 1.0
#
# [ Notes ]
# - IPs with more than 10 connections are classified as "high", 5-9 as "moderate", and 4 or less as "low"
#	Count and output for each classification
#	List only IPs in the "high" category separately
#
#
#####################################################################

# need loop --- 

```

### 결과
```shell



```

---

## **문제 5: 현재 시스템 네트워크 정보 수집기**

**요구사항:**

* 현재 시스템의 IP 주소, 기본 게이트웨이, 활성 인터페이스 개수를 출력  
* 인터넷 연결 상태 확인 (8.8.8.8로 ping 테스트)  
* 모든 정보를 보기 좋게 정리하여 출력

**출력 형태:**

\=== 시스템 네트워크 정보 \===

내부 IP: 192.168.1.XXX

기본 게이트웨이: 192.168.1.X

활성 인터페이스: X개

인터넷 연결: 정상/차단

**제한사항:**

* if문과 변수만 사용  
* ip, hostname, ping, grep, wc 명령어 활용  
* 각 정보를 변수에 저장 후 출력

```shell

vi network_info.sh

#!/bin/bash

#####################################################################
#
# [ Script Information ]
# Filename    : network_info.sh
# Description : Current System Network Information Collector
# Usage       : ./network_info.sh
# Example     : ./network_info.sh
#
# [ Author Information ]
# Author      : Mklee
# Created On  : 2025-07-24
# Last Update : 2025-07-25
# Version     : 1.0
#
# [ Notes ]
# - Output the IP address, default gateway, and number of active interfaces of the current system
#	Check Internet connection status (ping test to 8.8.8.8) Outputs all information in a good way
#
#
#####################################################################

ip_addr=$(ifconfig | grep inet | grep boardcast | tr -s " " | cut -d " " -f3)
default_gw=$(ip route | grep default | cut -d " " -f3)
activeInterface=$(ip -br addr | grep -i up | wc -l)

echo "===System Network Info==="
echo "Private IP: $ip_addr"
echo "Default G/W: $default_gw"
echo "Activate Interface: $activeInterface ea"

[ "$(ping -c 1 8.8.8.8 | grep received | awk -F "," '{print int($2)}')" -eq 1 ] && echo "Network: Connected" || echo "Network: Disconnected"

```

### 결과
```shell

===System Network Info===
Private IP: 192.168.0.11
Default G/W: 192.168.0.1
Activate Interface: 1 ea
Network: Connected


```

---

## **실행 방법**

각 문제의 스크립트를 작성한 후 다음과 같이 실행:

\# 실행 권한 부여

chmod \+x script\_name.sh

\# 스크립트 실행

./script\_name.sh \[인자\]

## **주의사항**

* 모든 스크립트는 `#!/bin/bash`로 시작  
* 변수 선언 시 공백 없이 작성: `var=value`  
* if문 조건 확인 시 `[ ]` 사용  
* 명령어 결과를 변수에 저장할 때 `$(command)` 사용  
* 파일 존재 여부 확인: `[ -f filename ]`

