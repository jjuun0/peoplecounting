# peoplecounting
사람의 인원수를 측정하여 인원 제한수를 기준으로 LED를 ON / OFF 제어(미구현)    
또한 어플에서 강제로 ON/OFF 할 수 있는 기능을 추가해 예기치 않은 상황에도 제어가능

# 활용 방향
주차장 : 현재 주차장안 차량의 수를 파악  
쇼핑매장 : 방문객과 실제 매출을 비교할 수 있고, 시간대별로 인원수도 파악 가능  
실내 공간 : 인원의 수를 제한하는 공간이 있다면 그 공간안의 인원수를 파악  

# 구조


# 아두이노 회로
사진 첨부
Arduino MKRWiFi1010 2개, 초음파센서(HC-SR04) 2개, LED 1개를 사용했습니다.  
(아두이노가 두개인데 하나는 입구, 다른 하나는 출구쪽 아두이노입니다.)

# 사용법 및 기능
1. 아두이노를 위의 회로도 처럼 연결을 하고 아두이노 코드를 실행시킨다.  
아두이노 코드내에 arduino.secrets.h에서 본인의 디바이스와 환경에 맞게 설정 필요함  
(입구 아두이노 - aws_iot_entrance.ino, 출구 아두이노 - aws_iot_exit.ino 실행)

2. 초음파센서가 측정한 거리가 15 미만일때 AWS DynamoDB에 저장이 된다.  
Entrance, Exit 테이블에 입구, 출구 아두이노에서 측정한 거리가 따로 저장(time값도 동시에 저장이 됩니다.)  
이때 LogTable 테이블에 현재 인원수도 저장해줍니다.(EntranceCurrent와 ExitCurrent 테이블의 인원수 값을 가져와 계산을 통하여 현재 인원수를 측정)
  * DB 테이블 
    - Entrance : 입구 아두이노에서 초음파센서가 측정한 거리(distance), timestamp(time), 들어온 사람수(people), 날짜형식의 시간(timestamp)이 저장됩니다.
    - Exit : 출구 아두이노에서 초음파센서가 측정한 거리(distance), timestamp(time), 나간 사람수(people), 날짜형식의 시간(timestamp)이 저장됩니다.
    - EntranceCurrent : Entrance 테이블 마지막 행이 저장되어 최신의 정보만 가지고 있는 테이블 입니다.
    - ExitCurrent : Exit 테이블 마지막 행이 저장되어 최신의 정보만 가지고 있는 테이블 입니다.
    - LogTable : 현재 인원의 수(people), LED 상태 정보(LED), 인원 제한 수(limit), timestamp(time), 날짜형식의 시간(timestamp)이 저장됩니다.
    - LogTableCurrent : LogTable 테이블 마지막 행이 저장되어 최신의 정보만 가지고 있는 테이블 입니다.

3. [미구현] 인원 제한수 < 현재 인원수가 된다면 LED를 ON으로 바꿔 더이상 사람들이 들어오지 못하도록 알려줍니다. (인원 제한수 > 현재 인원수인 경우 LED는 OFF상태)  

4. [디바이스 제어] 만약 담당자가 LED를 강제로 제어하고 싶은경우(Break Time, Lunch Time,,등등) 어플에서 강제로 LED 상태 제어가 가능합니다.  
또한 인원 제한수를 어플에서 값을 변경이 가능합니다.
 
