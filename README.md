# Arduino Elevator

// Arduino code snippet for an elevator system
#define FLOORS 5
int callButtons[FLOORS] = {2, 3, 4, 5, 6}; // 가정: 호출 버튼은 핀 2부터 6까지 연결
int floorLEDs[FLOORS] = {7, 8, 9, 10, 11}; // 각 층의 호출 상태 LED 핀 번호
int currentPositionLED = 12; // 현재 위치를 나타내는 LED 핀
int motorUp = 13; // 엘리베이터 상승 제어 핀
int motorDown = 14; // 엘리베이터 하강 제어 핀
int currentFloor = 0;
int targetFloor = -1; // 목적지 층, 초기값은 -1로 설정하여 호출이 없는 상태를 나타냄
bool moving = false; // 엘리베이터 이동 상태
bool directionUp = true; // 엘리베이터 이동 방향

void setup() {
  for (int i = 0; i < FLOORS; i++) {
    pinMode(callButtons[i], INPUT_PULLUP); // 호출 버튼 입력 설정
    pinMode(floorLEDs[i], OUTPUT); // 호출 상태 LED 출력 설정
  }
  pinMode(currentPositionLED, OUTPUT); // 현재 위치 LED 출력 설정
  pinMode(motorUp, OUTPUT); // 모터 제어 핀 출력 설정
  pinMode(motorDown, OUTPUT); // 모터 제어 핀 출력 설정
  digitalWrite(motorUp, LOW);
  digitalWrite(motorDown, LOW);
}

void loop() {
  readButtons();
  moveElevator();
  updateLEDs();
  delay(100); // 딜레이를 추가하여 버튼 입력을 안정적으로 받음
}

void readButtons() {
  for (int i = 0; i < FLOORS; i++) {
    if (digitalRead(callButtons[i]) == LOW) { // 버튼이 눌렸을 때
      digitalWrite(floorLEDs[i], HIGH); // 해당 층의 호출 상태 LED를 켬
      if (targetFloor == -1 || (directionUp && i > targetFloor) || (!directionUp && i < targetFloor)) {
        targetFloor = i; // 목적지 층 업데이트
      }
    }
  }
}

void moveElevator() {
  if (targetFloor != -1) { // 목적지가 설정된 경우
    moving = true;
    if (currentFloor < targetFloor) {
      digitalWrite(motorUp, HIGH); // 엘리베이터 상승
      digitalWrite(motorDown, LOW);
      directionUp = true;
      currentFloor++;
    } else if (currentFloor > targetFloor) {
      digitalWrite(motorUp, LOW);
      digitalWrite(motorDown, HIGH); // 엘리베이터 하강
      directionUp = false;
      currentFloor--;
    } else {
      // 도착했을 때
      digitalWrite(motorUp, LOW);
      digitalWrite(motorDown, LOW);
      digitalWrite(floorLEDs[currentFloor], LOW); // 호출 상태 LED를 끔
      moving = false;
      targetFloor = -1; // 다음 호출 대기
    }
  }
}

void updateLEDs() {
  for (int i = 0; i < FLOORS; i++) {
    if (i == currentFloor && moving) {
      digitalWrite(currentPositionLED, HIGH); // 현재 위치 LED를 켬
    } else {
      digitalWrite(currentPositionLED, LOW); // 현재 위치 LED를 끔
    }
  }
}


