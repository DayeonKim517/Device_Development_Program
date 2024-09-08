## RC_CAR_Practice
                                                                                                                                                                                                                                              ### ####// 2023 RC CAR 제어 
####// 블루투스 통신 + 안드로이드 스마트폰 
####// DAEJIN University 
####// speed  =  64 96 128 160 192 255 
####// ultrasonic distance 
 
 
#include <SoftwareSerial.h>        //시리얼통신 라이브러리 호출 
SoftwareSerial mySerial(8, 7);     //시리얼 통신을 위한 객체선언 (RX,TX)  아두이노 포트 RX은 블루투스 TX에 연결

int max_pwm = 128;
int count=0;
    
#define TRIG A1    //TRIG 핀 설정 (초음파 보내는 핀)
#define ECHO A0    //ECHO 핀 설정 (초음파 받는 핀)



//-------------------------------------
void setup() 
{
  boolean speed_set;
  
  pinMode(2, OUTPUT);              // 5번핀을 출력모드로 설정합니다.
  pinMode(3, OUTPUT);              // 6번핀을 출력모드로 설정합니다.
  pinMode(4, OUTPUT);              // 9번핀을 출력모드로 설정합니다.

  pinMode(11, OUTPUT); 
  pinMode(12, OUTPUT); 
  pinMode(A0, INPUT); 
  pinMode(A1, OUTPUT); 
        
  digitalWrite(2,LOW); 
  digitalWrite(3,LOW); 
  digitalWrite(4,LOW); 
  digitalWrite(11,LOW); 
  digitalWrite(12,LOW); 

  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);
  
  analogWrite(5, 0);               // PWM 출력 = 0 : 오른쪽 모터 정지  
  analogWrite(6, 0);
  analogWrite(9, 0);               // 왼쪽 모터 정지 
  analogWrite(10, 0);
  
  Serial.begin(9600);             //  시리얼모니터 통신 속도
  mySerial.begin(9600);           //  블루투스 시리얼  통신 속도   
          
}
//-------------------------------------


//-------------------------------------
void loop() {
    byte data;
    long echo_duration, distance;
    
    //--------------------------------
    if (mySerial.available()) {       
      data = mySerial.read();    // 블루투스 데이터 받기 
      //Serial.write(data);        // 블루투스측 내용을 시리얼모니터에 출력
    }
    //-----------------------------
         
       digitalWrite(2,HIGH);  // led back 
       //delay(300);

       digitalWrite(4,HIGH);  // led back 
       //delay(300);

       digitalWrite(3,HIGH);  // led front
       //delay(300);
       
       digitalWrite(12,HIGH);  // led front 
       delay(600);
       
       digitalWrite(12,LOW);
       digitalWrite(2,LOW);
       digitalWrite(4,LOW);
       digitalWrite(3,LOW);
       
      left_forward(max_pwm);
      right_forward(max_pwm);      
      delay(600); 

      right_stop();
      left_stop();
      delay(200); 
      
      left_backward(max_pwm);
      right_backward(max_pwm); 
      delay(600);  

      right_stop();
      left_stop();
      delay(200);

// ultrasonic 
       digitalWrite(11,HIGH);  // buzzer
       delay(50);
       digitalWrite(11,LOW);
       
       digitalWrite(TRIG, LOW);    
       delayMicroseconds(2);
       digitalWrite(TRIG, HIGH);  // 초음파 거리 측정 시작 펄스 내보내기 
       delayMicroseconds(10);
       digitalWrite(TRIG, LOW);

       echo_duration = pulseIn (ECHO, HIGH);  // 시간 측정 
       distance = echo_duration * 17 / 1000;  // [cm]
       
       Serial.print(distance);
       Serial.println(" [cm]");
       
       mySerial.print(distance);
       mySerial.println(" [cm]");

      // if (mySerial.available()) {       
      //   Serial.write(mySerial.read());  //블루투스측 내용을 시리얼모니터에 출력
      // }
  
      // if (Serial.available()) {         
      //   mySerial.write(Serial.read());  //시리얼 모니터 내용을 블루투스 측에 WRITE
      // }

       if ( distance < 10 ) {
              delay(30);
              digitalWrite(11,HIGH);  // buzzer
              delay(30);
              digitalWrite(11,LOW);
              delay(30);   
              digitalWrite(11,HIGH);  // buzzer
              delay(30);
              digitalWrite(11,LOW);
              delay(30);            
              digitalWrite(11,HIGH);  // buzzer
              delay(30);
              digitalWrite(11,LOW);
              delay(30);
              digitalWrite(11,HIGH);  // buzzer
              delay(30);
              digitalWrite(11,LOW);
              delay(30);   
              digitalWrite(11,HIGH);  // buzzer
              delay(30);
              digitalWrite(11,LOW);
              delay(30);
       }
       
}    // loop end 
//-------------------------------------



//-----------------------------------
void right_forward(int pwm) {
      analogWrite(5, 0);                
      analogWrite(6, pwm); 
      delay(10);
}
//-----------------------------------
void left_forward(int pwm) {      
      analogWrite(9, 0);                
      analogWrite(10, pwm);       
      delay(10);  
}
//-----------------------------------
void right_backward(int pwm) {
      analogWrite(5, pwm);                
      analogWrite(6, 0); 
      delay(10);
}
//-----------------------------------
void left_backward(int pwm) {      
      analogWrite(9, pwm);                
      analogWrite(10, 0);       
      delay(10);  
}
//-----------------------------------
void right_stop() {
      analogWrite(5, 0);                
      analogWrite(6, 0); 
      delay(10);
}
//-----------------------------------
void left_stop() {      
      analogWrite(9, 0);                
      analogWrite(10, 0);       
      delay(10);  
}
//--------------------------------------
