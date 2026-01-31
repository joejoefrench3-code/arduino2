# arduino2
This board usually gives a quantity of how far an object is from the two sensors on the edge (in meters). In the picture, the battery is a greater distance away than the maximum threshold to provide a numerical value, so it reads "FAR m."  I had helped assemble several wires onto the board in the appropriate manner.
Distance Meter 
*/ 
#include <LiquidCrystal.h> 
int LCD_PinRS = 8;
int LCD_PinE = 9;                  
int LCD_PinD4 = 10;
int LCD_PinD5 = 11;
int LCD_PinD6 = 12;
int LCD_PinD7 = 13;
int trigPin = 6;
int echoPin = 7;                  
int resolution = 1024;
float fullscale = 5.0;
int counts = 0;                    
float measvolts = 0.0;            
int closelimit = 100;              
int farlimit = 300;                
long duration = 0;
long distance = 0;
                 //
 LCD resistor select pin (RS) 
// LCD enable pin (E) 
                //
                //
                //
                //
                   //
 LCD data pin D4 
 LCD data pin D5 
 LCD data pin D6 
 LCD data pin D7 
 ultrasonic distance sensor trigger pin 
 // ultrasonic distance sensor echo pin 
             // # of counts over full range 
             // 
maximum voltage reading 
// counts reading for analog input 
 // voltage reading 
// distance limit for close alarm (mm) 
// fdistance limit for far alarm (mm) 
                 // time duration value used with distance sensor 
                 // distance measured by ultrasonic sensor 
LiquidCrystal lcd(LCD_PinRS, LCD_PinE, LCD_PinD4, LCD_PinD5, LCD_PinD6, LCD_PinD7); 
 10 
void setup() { 
  pinMode(trigPin, OUTPUT); 
  pinMode(echoPin, INPUT); 
  lcd.begin(16,2); 
  lcd.clear(); 
  lcd.setCursor(5,0); 
  lcd.print("ARDUINO"); 
  lcd.setCursor(0,1); 
  lcd.print("Voltage: "); 
  lcd.print("Distance: "); 
  lcd.setCursor(14,1); 
  lcd.print("mV"); 
} 
 
void loop() { 
  Readvoltage(); 
  Readdistance(); 
  delay(250); 
} 
 
void Readvoltage() { 
  counts = analogRead(A0); 
  measvolts = (counts * fullscale)/resolution; 
  lcd.setCursor(9,1); 
  lcd.print(measvolts); 
} 
 
void Readdistance() {   
  delayMicroseconds(2); 
  digitalWrite(trigPin, HIGH); 
  delayMicroseconds(10); 
  digitalWrite(trigPin, LOW); 
  duration = pulseIn(echoPin, HIGH); 
  distance = duration*0.340/2; 
  lcd.setCursor(10,1); 
  if(distance > farlimit) { 
    lcd.print(" FAR  "); 
  } 
  else if (distance < closelimit) { 
    lcd.print("CLOSE "); 
  } 
  else { 
    lcd.print(distance); 
    lcd.print(" mm "); 
  } 
} 
