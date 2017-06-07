//==================ph==============

#define SensorPin A2            //pH meter Analog output to Arduino Analog Input 2
#define Offset 0.00            //deviation compensate
#define LED 13
#define samplingInterval 20
#define printInterval 800
#define ArrayLenth  40    //times of collection
int pHArray[ArrayLenth];   //Store the average value of the sensor feedback
int pHArrayIndex=0;   
  static float pHValue,voltage;

//=================end ph===================

//=================temp=====================
float tempValue;
int tempPin = 1;

#include <dht.h>

dht DHT;

#define DHT21_PIN 2

 float humValue=1;
 float  temp2Value=1;
//================end temp==================

//==================5110==================

#include <SPI.h>
#include <Adafruit_GFX.h>
#include <Adafruit_PCD8544.h>

// Software SPI (slower updates, more flexible pin options):
// pin 7 - Serial clock out (SCLK)
// pin 6 - Serial data out (DIN)
// pin 5 - Data/Command select (D/C)
// pin 4 - LCD chip select (CS)
// pin 3 - LCD reset (RST)
Adafruit_PCD8544 display = Adafruit_PCD8544(7, 6, 5, 4, 3);

#include <EEPROM.h>
#define EEPROM_write(address, p) {int i = 0; byte *pp = (byte*)&(p);for(; i < sizeof(p); i++) EEPROM.write(address+i, pp[i]);}
#define EEPROM_read(address, p)  {int i = 0; byte *pp = (byte*)&(p);for(; i < sizeof(p); i++) pp[i]=EEPROM.read(address+i);}

#define ReceivedBufferLength 20
char receivedBuffer[ReceivedBufferLength+1];   // store the serial command
byte receivedBufferIndex = 0;

#define SCOUNT  30           // sum of sample point
int analogBuffer[SCOUNT];    //store the sample voltage
int analogBufferIndex = 0;

#define VREF 5000  //for arduino uno, the ADC reference is the power(AVCC), that is 5000mV

//=====================end 5110=======================

//==================co2=============================


#define cosensor 0
int coValue=0;
int coMap=0;

void setup()
{
  Serial.begin(9600);
  display.begin();
  display.clearDisplay();
  display.setContrast(50);
  Serial.println(DHT_LIB_VERSION);
}
int num2=10;

void loop(){
int boton=analogRead(3);
if(boton>512){
num2++;
if(num2==16){num2=10;}
}
text(num2);
Serial.print(num2);
ph();
}

void text(int num3){
switch(num3){
  case 10:
  display.clearDisplay();
  display.setTextSize(2);
  display.setTextColor(BLACK);
  display.setCursor(0,0);
  display.println("viajes");
  display.setTextSize(3);
  display.setTextColor(WHITE, BLACK); // 'inverted' text
  display.println("AGUA");
  display.display();
  break; 
    
  case 11:
  display.clearDisplay();
  display.setTextSize(3);
  display.setTextColor(BLACK);
  display.setCursor(0,0);
  display.println("pH:");
  display.setTextColor(WHITE, BLACK); // 'inverted' text
  display.println(pHValue);
  display.display();
  break;
 
  case 12:
  temp35();
  display.clearDisplay();
  display.setTextSize(3);
  display.setTextColor(BLACK);
  display.setCursor(0,0);
  display.println("Tem1"); 
  display.setTextColor(WHITE, BLACK); // 'inverted' text
  display.println(tempValue);
  display.display();
  break;

case 13:
  DHT21();
  display.clearDisplay();
  display.setTextSize(3);
  display.setTextColor(BLACK);
  display.setCursor(0,0);
  display.println("Tem2"); 
  display.setTextColor(WHITE, BLACK); // 'inverted' text
  display.println(temp2Value);
  display.display();
break;

case 14:
 DHT21();
    display.clearDisplay();
  display.setTextSize(3);
  display.setTextColor(BLACK);
  display.setCursor(0,0);
  display.println("Hume"); 
  display.setTextColor(WHITE, BLACK); // 'inverted' text
  display.println(humValue);
  display.display();
  break;
  
case 15:
co2();
    display.clearDisplay();
  display.setTextSize(3);
  display.setTextColor(BLACK);
  display.setCursor(0,0);
  display.println("CO 2"); 
  display.setTextColor(WHITE, BLACK); // 'inverted' text
   // display.setTextSize(2);
  display.println(coMap);
  display.display();
  break;  
  }
}

void ph()
{
static unsigned long samplingTime = millis();
  static unsigned long printTime = millis();
  if(millis()-samplingTime > samplingInterval)
  {
      pHArray[pHArrayIndex++]=analogRead(SensorPin);
      if(pHArrayIndex==ArrayLenth)pHArrayIndex=0;
      voltage = avergearray(pHArray, ArrayLenth)*5.0/1024;
      pHValue = 3.5*voltage+Offset;
      samplingTime=millis();
  }
 if(millis() - printTime > printInterval)   //Every 800 milliseconds, print a numerical, convert the state of the LED indicator
  {
	Serial.print("Voltage:");
        Serial.print(voltage,2);
        Serial.print("    pH value: ");
	Serial.println(pHValue,2);
        printTime=millis();
  }
}
double avergearray(int* arr, int number){
  int i;
  int max,min;
  double avg;
  long amount=0;
  if(number<=0){
    Serial.println("Error number for the array to avraging!/n");
    return 0;
  }
  if(number<5){   //less than 5, calculated directly statistics
    for(i=0;i<number;i++){
      amount+=arr[i];
    }
    avg = amount/number;
    return avg;
  }else{
    if(arr[0]<arr[1]){
      min = arr[0];max=arr[1];
    }
    else{
      min=arr[1];max=arr[0];
    }
    for(int i=2;i<number;i++){
      if(arr[i]<min){
        amount+=min;        //arr<min
        min=arr[i];
      }else {
        if(arr[i]>max){
          amount+=max;    //arr>max
          max=arr[i];
        }else{
          amount+=arr[i]; //min<=arr<=max
        }
      }//if
    }//for
    avg = (double)amount/(number-2);
  }//if
  return avg;
}

void temp35(){
tempValue = analogRead(tempPin);

tempValue = tempValue * 0.48828125;

Serial.print(tempValue);

Serial.print("*C");

Serial.println();

delay(1000);

}

void DHT21(){
    Serial.print("DHT21, \t");
    int chk = DHT.read21(DHT21_PIN);
    switch (chk)
    {
    case DHTLIB_OK:
        Serial.print("OK,\t");
        break;
    case DHTLIB_ERROR_CHECKSUM:
        Serial.print("Checksum error,\t");
        break;
    case DHTLIB_ERROR_TIMEOUT:
        Serial.print("Time out error,\t");
        break;
    case DHTLIB_ERROR_CONNECT:
        Serial.print("Connect error,\t");
        break;
    case DHTLIB_ERROR_ACK_L:
        Serial.print("Ack Low error,\t");
        break;
    case DHTLIB_ERROR_ACK_H:
        Serial.print("Ack High error,\t");
        break;
    default:
        Serial.print("Unknown error,\t");
        break;
    }
    // DISPLAY DATA
    humValue=DHT.humidity;
    temp2Value=DHT.temperature;
    Serial.print(humValue, 1);
    Serial.print(",\t");
    Serial.println(temp2Value, 1);
  

    delay(1000);

}

void co2(){
coValue=analogRead(cosensor);
coMap=map(coValue,0,1023,10,1000);
delay(1000);
}

