/*
   Программа и стройство «Датчик расстояния» предназначены для 
измерений дистанции от макета летательного аппарата до
объекта при условии предварительной пеленгации объекта.
   Результаты подаются на серводвигатель (вращение по
азимуту) и на линейку светодиодных индикаторов
   Здесь представлена полная схема, в составе которой 
функционирует «Датчик расстояния».
   Программа «Датчик расстояния» Copyright © 2023. 
Авторы: Анастасия Станиславовна Медведева, 
Алексей Владимирович Ерпелев, Максим Константинович Каторгин, 
Владимир Львович Симонов.
   Эта программа является свободным программным обеспечением: 
вы можете распространять ее и/или модифицировать
на условиях Стандартной общественной лицензии GNU, опубликованной
Free Software Foundation, либо версию 3 Лицензии, либо
(на ваш выбор) любой более поздней версии.

Эта программа распространяется в надежде, что она будет полезна,
но БЕЗ КАКИХ-ЛИБО ГАРАНТИЙ; даже без подразумеваемой гарантии
КОММЕРЧЕСКАЯ ПРИГОДНОСТЬ или ПРИГОДНОСТЬ ДЛЯ ОПРЕДЕЛЕННОЙ ЦЕЛИ. См.
Стандартная общественная лицензия GNU для более подробной информации.

Вы должны были получить копию Стандартной общественной лицензии GNU.
вместе с этой программой. Если нет, см. <www.gnu.org/licenses/>.
*/

#include <Servo.h>
Servo myservo;
int lightPin1 = A0; // Аналоговый пин с фоторезистором
int lightPin2 = A1;
int light1 = 0; // освещенность 1 канала
int light2 = 0; // освещенность 2 канала
int diff = 0;
int pos = 0;
int pinLed1=2;
int pinLed2=3;
int pinLed3=4;
int pinLed4=5;
int pinLed5=6;
int senPin=A2;
//int TrigPin=8;
void setup()
{
myservo.attach(9);
Serial.begin(9600);
// pinMode(TrigPin, OUTPUT);
// pinMode(EchoPin,INPUT);
pinMode(pinLed1, OUTPUT);
pinMode(pinLed2, OUTPUT);
pinMode(pinLed3, OUTPUT);
pinMode(pinLed4, OUTPUT);
pinMode(pinLed5, OUTPUT);
}

// Подготовка к фильтрации
float distFilt = 0;
float light1Filt = 0;
float light2Filt = 0;

void loop()
{
float volt=analogRead(senPin)*0.0048828125;
float dist=65*pow(volt, -1.10);
distFilt += (dist - distFilt) * 0.1; // фильтруем
dist=constrain(dist, 10, 120);

light1 = analogRead(lightPin1);
light1Filt += (light1 - light1Filt) * 0.01; // фильтруем

light2 = analogRead(lightPin2);
light2Filt += (light2 - light2Filt) * 0.01; // фильтруем

diff = light1Filt - light2Filt;


// Serial.print(light1Filt); Serial.print('\t');
// Serial.print(light2Filt); Serial.print('\t');
Serial.print(diff); Serial.print('\t');
//Serial.print(dist);
Serial.println();

if(dist<10){
digitalWrite(pinLed1, HIGH);
digitalWrite(pinLed2, HIGH);
digitalWrite(pinLed3, HIGH);
digitalWrite(pinLed4, HIGH);
digitalWrite(pinLed5, HIGH);
}
else if(dist<25){
digitalWrite(pinLed1, HIGH);
digitalWrite(pinLed2, HIGH);
digitalWrite(pinLed3, HIGH);
digitalWrite(pinLed4, HIGH);
digitalWrite(pinLed5, LOW);
}
else if(dist<50){
digitalWrite(pinLed1, HIGH);
digitalWrite(pinLed2, HIGH);
digitalWrite(pinLed3, HIGH);
digitalWrite(pinLed4, LOW);
digitalWrite(pinLed5, LOW);
}
else if(dist<75){
digitalWrite(pinLed1, HIGH);
digitalWrite(pinLed2, HIGH);
digitalWrite(pinLed3, LOW);
digitalWrite(pinLed4, LOW);
digitalWrite(pinLed5, LOW);
}
else if(dist<100){
digitalWrite(pinLed1, HIGH);
digitalWrite(pinLed2, LOW);
digitalWrite(pinLed3, LOW);
digitalWrite(pinLed4, LOW);
digitalWrite(pinLed5, LOW);
}
else{
digitalWrite(pinLed1, LOW);
digitalWrite(pinLed2, LOW);
digitalWrite(pinLed3, LOW);
digitalWrite(pinLed4, LOW);
digitalWrite(pinLed5, LOW);
}

if(diff>7){
myservo.write(++pos);
delay(15);
}
else if(diff<-7){
myservo.write(--pos);
delay(15);
}

delay(10);
}
