# arduino2
/*
 * Arduino Ultrasonic Distance Measurement System
 * Sensor: HC-SR04
 * Output: distance in millimeters on LCD
 *
 * Threshold behavior (intentional design constraints):
 *   < 100 mm  →  displays "CLOSE"
 *   > 300 mm  →  displays " FAR "
 *   100–300mm →  displays numeric value in mm
 *
 * The 300 mm far limit is a calibrated design choice:
 * beyond this range, echo reliability degrades for the
 * specific enclosure geometry used in this deployment.
 */

#include <LiquidCrystal.h>

// ── LCD Pins ──────────────────────────────────────────────
const int LCD_PinRS = 8;
const int LCD_PinE  = 9;
const int LCD_PinD4 = 10;
const int LCD_PinD5 = 11;
const int LCD_PinD6 = 12;
const int LCD_PinD7 = 13;

// ── Sensor Pins ───────────────────────────────────────────
const int trigPin = 6;
const int echoPin = 7;

// ── Distance Thresholds (mm) ──────────────────────────────
const int CLOSE_LIMIT = 100;
const int FAR_LIMIT   = 300;

// ── State ─────────────────────────────────────────────────
long duration = 0;
long distance = 0;

LiquidCrystal lcd(LCD_PinRS, LCD_PinE, LCD_PinD4, LCD_PinD5, LCD_PinD6, LCD_PinD7);

void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  digitalWrite(trigPin, LOW);

  lcd.begin(16, 2);
  lcd.clear();
  lcd.setCursor(3, 0);
  lcd.print("DISTANCE");
  lcd.setCursor(0, 1);
  lcd.print("Dist:        mm");
}

void loop() {
  readDistance();
  displayDistance();
  delay(250);
}

void readDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  // 30ms timeout prevents hang if no echo returns
  duration = pulseIn(echoPin, HIGH, 30000);

  // Convert: speed of sound 0.340 mm/us, round trip /2
  distance = (duration == 0) ? FAR_LIMIT + 1 : duration * 0.340 / 2;
}

void displayDistance() {
  lcd.setCursor(6, 1);

  if (distance < CLOSE_LIMIT) {
    lcd.print(" CLOSE  ");
  } else if (distance > FAR_LIMIT) {
    lcd.print("  FAR   ");
  } else {
    // Fixed-width prevents leftover characters from longer previous values
    char buf[8];
    sprintf(buf, "%4ld    ", distance);
    lcd.print(buf);
  }
}
