# Smart-Blind-Stick-Using-IoT

#include <SoftwareSerial.h>

#define pingPin 2        //trig pin of sr04
#define echoPin 3

SoftwareSerial mySerial(9,10);

char msg;
char call;
int buttonpin=13 ;



void setup()
{
   Serial.begin(9600); // Starting Serial Terminal
   pinMode(pingPin,OUTPUT); 
   pinMode(echoPin,INPUT);
   pinMode(12,OUTPUT);   //pin12 is used as GND pin  for buzzer since arduino nano has only two GND pins
   pinMode(A3,OUTPUT);   //pin A3 provides the output on buzzer


  pinMode(buttonpin,INPUT);
  mySerial.begin(9600);   // Setting the baud rate of GSM Module  
  Serial.begin(9600);// Setting the baud rate of Serial Monitor (Arduino)
  Serial.println("press button");


   
}

void loop()
{  /// buzzer distance code
  long duration, cm;
  digitalWrite(12, LOW);   //Buzzer GND is always low



   //send a signal at ping pin at an interval of 0.002 seconds to check for an object
   digitalWrite(pingPin, LOW);
   delayMicroseconds(2);    
   digitalWrite(pingPin, HIGH);
   delayMicroseconds(10);
   digitalWrite(pingPin, LOW);

  
   duration = pulseIn(echoPin, HIGH);  //check time using pulseIn function
   
   cm = microsecondsToCentimeters(duration);   //functin call to find distance
   
  /* Serial.print(cm);
   Serial.print("cm");
   Serial.println();
   delay(100);
   
   for debugging
   */

   
   if (cm<90&&cm>60) 
                         {analogWrite(A3,255); 
                          delay(1000); 
                          analogWrite(A3,0); 
                          delay(1000); }    //sound buzzer every second if obstacle distance is between 20-30cm. 
  
   
   else if (cm<60&&cm>30) {analogWrite(A3,255); 
                           delay(500); 
                           analogWrite(A3,0); 
                           delay(500); }   //sound buzzer every 0.5 seconds if obstacle distance is between 10-20cm. 
  
   
   else if (cm<30&&cm>0)  {analogWrite(A3,255); 
                           delay(100); 
                           analogWrite(A3,0);
                           delay(100); }    //sound buzzer every 0.1 seconds if obstacle distance is between 0-10cm. 
  
   
   else                     analogWrite(A3,0); //do not sound the buzzer








/// pressing button

  if(digitalRead(buttonpin)==HIGH)
  {
    
    Serial.println("button pressed");
    delay(2000);
    makeCall();
    SendMessage();  
  }

 if (mySerial.available()>0)
 Serial.write(mySerial.read());
}


//function to return distance in cm from microseconds
long microsecondsToCentimeters(long microseconds)
 {
   return microseconds / 29 / 2;
}


void SendMessage()
{
  mySerial.println("AT+CMGF=1");    //Sets the GSM Module in Text Mode
  delay(1000);  // Delay of 1000 milli seconds or 1 second

  mySerial.println("AT+CMGS=\"+918618047919\"\r"); //  mobile number
  delay(1000);

  mySerial.println("NEED HELP EMERGENCY!!!!!");// The SMS text you want to send
  delay(100);
   mySerial.println((char)26);// ASCII code of CTRL+Z
  delay(1000);
}

void makeCall()
{
  Serial.println("calling....");
  mySerial.println("ATD +918618047919;");
  delay(20000); //20 sec delay
  mySerial.println("ATH");
  delay(1000); //1 sec delay
}
