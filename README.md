# PNU 임베디드설계및실험 10조 - 스마트 주차장 (Bluetooth를 이용한 스마트 주차장)
![front](https://github.com/user-attachments/assets/19ec48bb-e409-4655-8d29-6a334706d317)

## 프로젝트 개요
좁은 공간(골목, 오피스텔 등)에서 효율적으로 주차 공간을 확보하고, 주차장 이용자의 불편함을 해소하기 위해 **스마트 주차장**을 기획하였습니다.  
이를 위해 블루투스 모듈, 초음파 센서, 압력 센서, 스텝모터 등 다양한 센서를 활용하여 다음과 같은 기능을 구현했습니다.

이 프로젝트의 시연 영상을 보려면 아래 링크를 클릭하세요:
[시연 영상](https://drive.google.com/file/d/1cQRDizoORHhEeaE2diS0kh74wwEAZ1Nh/view?usp=sharing)

---

## 주요 기능

1. **주차장 입차 기능**  
   - 차량이 입구 압력센서를 밟으면 초음파 센서가 활성화되어 주차 가능 공간을 탐색합니다.  
   - 주차 완료 시 초음파 센서 값(5cm 이하로 인식)을 통해 LED 표시(주황색/빨간색)로 주차 상태를 나타냅니다.  
   - 사람이 출구로 나간 뒤에는 스텝모터를 작동시켜 비어 있는 칸을 자동으로 1층에 배치합니다.  

2. **주차장 안전장치**  
   - 차량이 들어온 후, 사람이 출구 압력 센서를 밟아 외부로 완전히 빠져나가기 전까지 엘리베이터(스텝모터)가 동작하지 않도록 설정했습니다.  

3. **주차장 출차 기능**  
   - 블루투스를 통해 자신의 차량 위치를 전송하면, 주차한 층의 칸을 1층으로 내려서 바로 출차할 수 있게 합니다.  

4. **주차장 현황 확인 기능**  
   - 블루투스를 통해 현재 주차장이 얼마나 차 있는지, 각각의 층/칸에 주차가 되어 있는지를 확인할 수 있습니다(1은 주차 상태, 0은 비어 있음).

---

## 하드웨어 / 부품

| 명칭           | 수량 | 역할                                                  |
| -------------- | ---- | ----------------------------------------------------- |
| **압력 센서**    | 2개  | 차량/사람의 입·출구 인식                             |
| **초음파 센서**  | 9개  | 주차 공간 내 차량 존재 여부 판단                     |
| **외부 전압(6V)** | 1개  | 전원 보충 (스텝모터 등 다수 부품 사용 시 전력 부족 해결) |
| **신호등(LED)**  | 3개  | 주차 상태 표시 (예: 주황/빨강)                       |
| **스텝모터**     | 3개  | 엘리베이터(주차장 층 이동) 구동                      |
| **블루투스 모듈** | 1개  | 스마트폰과의 무선 통신                              |
| **STM32 보드**   | 1개  | 전체 로직 제어 (USART2로 블루투스 모듈 연결)         |

> 추가로 케이블, 폼보드, 색종이(프로토타입 제작용), 납땜도구 등을 활용했습니다.

---

## 시스템 동작 흐름

### 1. 초기 실행
1. 모든 센서(압력, 초음파), 모듈(블루투스, 스텝모터, LED 등)을 보드와 연결합니다.  
2. 전원을 인가(6V 외부 전압 포함)한 뒤, STM32 보드에서 초기 설정(센서 인터럽트, 타이머 등)을 수행합니다.

### 2. 입차 과정
1. **차량 입구 통과**: 차량이 입구의 압력 센서를 밟으면(임계값 초과) 초음파 센서가 활성화됩니다.  
2. **차량 주차 인식**: 주차 칸의 초음파 센서가 거리(5cm 이하)로 차량 유무를 확인하면, 해당 칸에 차량이 주차되었음을 LED로 표시합니다.  
3. **사람 나감 인식**: 사람이 출구 압력 센서를 밟으면(임계값 초과), 차량이 안전하게 주차된 것을 확인한 뒤 스텝모터 작동을 허가합니다.  
4. **엘리베이터 이동**: 비어있는 주차칸을 1층으로 재배치하여 다음 차량 주차 시 대기 없이 이용할 수 있게 만듭니다.

### 3. 출차 과정
1. **블루투스 신호 전송**: 스마트폰에서 앱(또는 시리얼 통신 어플 등)을 통해 자신의 차량이 있는 층/칸 번호를 보드에 전송합니다.  
2. **위치 확인 & 1층 이동**: 입력값(층/칸 번호)이 유효한지 확인 후, 스텝모터를 통해 해당 주차 칸을 1층으로 내려줍니다.  
3. **주차칸 현황 갱신**: 차량이 출차되면 주차 칸 상태가 0(빈 칸)으로 업데이트되어, 휴대폰에서도 확인할 수 있습니다.

### 4. 주차장 현황 확인
1. **현황 출력 명령**: 스마트폰에서 특정 명령(예: `STATUS`)을 전송하면, 현재 주차 가능한 칸(0) 및 주차된 칸(1)의 상태를 한눈에 확인할 수 있습니다.

---

## 개발 일정
- **주차장 설계**: 센서 및 모터 배치 계획 수립  
- **하드웨어 연결**: STM32 & 센서(압력/초음파), 모듈(블루투스) 연결 및 납땜  
- **펌웨어 개발**: 인터럽트/타이머/USART2 설정 및 디버깅  
- **주차장 모델링**: 폼보드 및 색종이를 활용한 구조물 제작  
- **테스트 & 수정**: 실제 차량(모형)으로 입차·출차·현황 확인 기능 검증  

---

## 문제점 및 해결방안

1. **전력 부족**  
   - 스텝모터, LED 등 다양한 장치를 동시에 구동하면서 전력이 부족해지는 현상이 발생했습니다.  
   - 해결: 6V 외부 전압을 추가 공급하여 전력 문제를 해결했습니다.

2. **`printf()` 디버깅 문제**  
   - 디버깅 과정에서 `printf()` 함수 사용 시, 리소스 사용이 커지고 센서 값이 정상적으로 읽히지 않는 문제가 발생했습니다.  
   - 해결: 디버깅이 끝나면 `printf()`를 주석 처리하거나 최소화하여 정상 동작을 확보했습니다.

3. **인터럽트 우선순위 및 타이머 활용**  
   - 여러 센서에서 인터럽트가 동시에 발생하여 예기치 못한 동작이 일어났습니다.  
   - 해결: 인터럽트 우선순위를 재설정하고, 타이머 인터럽트를 이용해 센서 측정을 일정 주기로 제한하도록 수정했습니다.