# LED-and-RGB-Lighting-Control-using-Arduino
Developed Arduino-based LED and RGB lighting projects with button-controlled color changes, running light patterns, and basic lighting automation using Arduino UNO and LEDs.

#include <IRremote.h>

#define IR_RECEIVE_PIN 2

// Define Pins
int yellowLed = 5;
int greenLed = 6;
int blueLed = 7;
int redLed = 8;
int orangeLed = 9;
int redRGB = 3;
int blueRGB = 10;
int greenRGB = 11;

// Running mode flag
bool runningMode = false;
bool rgbMode = false;

// Running lights function
void runningLights() {
  int leds[] = {yellowLed, greenLed, blueLed, redLed, orangeLed,redRGB , blueRGB , greenRGB};

  for (int i = 0; i < 5; i++) {
    digitalWrite(leds[i], HIGH);
    delay(150);
    digitalWrite(leds[i], LOW);
  }
  //rgb sequence
  setColor(255,0,0);
  delay(150);
  setColor(0,255,0);
  delay(150);
  setColor(0,0,255);
  delay(150);
  setColor(0,0,0);
}

 void RGB() {
    setColor(255,0,0);//red
      delay(500);
      setColor(0,255,0);//blue
        delay(500);
      setColor(0,0,255);//green 
        delay(500);
  } 
  
void setColor(int r,int b,int g){
  analogWrite(redRGB,r);
  analogWrite(blueRGB,b);
  analogWrite(greenRGB,g); }

void setup() {
  Serial.begin(9600);

  IrReceiver.begin(IR_RECEIVE_PIN, ENABLE_LED_FEEDBACK);

  pinMode(yellowLed, OUTPUT);
  pinMode(greenLed, OUTPUT);
  pinMode(blueLed, OUTPUT);
  pinMode(redLed, OUTPUT);
  pinMode(orangeLed, OUTPUT);
  pinMode(redRGB , OUTPUT);
  pinMode(blueRGB , OUTPUT);
  pinMode(greenRGB , OUTPUT);
  
}

void loop() {

  // Check IR input
  if (IrReceiver.decode()) {

    // Ignore repeat signals
    if (IrReceiver.decodedIRData.flags & IRDATA_FLAGS_IS_REPEAT) {
      IrReceiver.resume();
      return;
    }

    unsigned long value = IrReceiver.decodedIRData.decodedRawData;
    Serial.println(value, HEX);

    switch (value) {

      case 0xEF10BF00:   // Button 1
        Serial.println("1");
        runningMode = false;
       rgbMode = false;
        digitalWrite(yellowLed, HIGH);
        break;

      case 0xEE11BF00:   // Button 2
        Serial.println("2");
        runningMode = false;
      rgbMode = false;
        digitalWrite(greenLed, HIGH);
    
        break;

      case 0xED12BF00:   // Button 3
        Serial.println("3");
        runningMode = false;
       rgbMode = false;
        digitalWrite(blueLed, HIGH);
      
        break;

      case 0xEB14BF00:   // Button 4
        Serial.println("4");
        runningMode = false;
       rgbMode = false;
        digitalWrite(redLed, HIGH);
     
        break;

      case 0xEA15BF00:   // Button 5
        Serial.println("5");
        runningMode = false;
       rgbMode = false;
        digitalWrite(orangeLed, HIGH);
        break;

      case 0xE916BF00:   // Button 6 - rgb 
        Serial.println("6 - RGB");
        runningMode = false;
     rgbMode = true;
        break;
      
     
      case 0xE718BF00:   // Button 7 - Running lights on 
        Serial.println("7 - Running Lights");
      rgbMode = false;
        runningMode = true;
      setColor(0,0,0);
      
        break;
      
      case 0xF30CBF00:   // Button 0 → OFF all
        Serial.println("0 - OFF");
        runningMode = false;
       rgbMode = false;
  

        digitalWrite(yellowLed, LOW);
        digitalWrite(greenLed, LOW);
        digitalWrite(blueLed, LOW);
        digitalWrite(redLed, LOW);
        digitalWrite(orangeLed, LOW);
      digitalWrite(redRGB, LOW);
      digitalWrite(blueRGB, LOW);
      digitalWrite(greenRGB, LOW);
        break;

      default:
        break;
    }

    IrReceiver.resume();
  }

  // Run animation continuously if enabled
  if (runningMode) {
    runningLights();}
    if (rgbMode) {
      RGB();}
  
}