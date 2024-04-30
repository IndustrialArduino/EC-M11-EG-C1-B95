#define RXD2 4
#define TXD2 2
#define PIN 5
#define RES 19
#include <Adafruit_ADS1015.h>

Adafruit_ADS1115 ads1(0x49);
String adcString[8];

void setup() {
  // initialize both serial ports:
  Serial.begin(9600);
  Serial2.begin(9600, SERIAL_8N1, RXD2, TXD2);
  pinMode(PIN, OUTPUT);
  pinMode(RES, OUTPUT);

  digitalWrite(RES, HIGH);
  digitalWrite(PIN, HIGH);

  Wire.begin(16, 17);  // Initialize I2C communication

  pinMode(35, INPUT);  // Digital Input 1
  pinMode(34, INPUT);  // Digital Input 2

  pinMode(13, OUTPUT);  // RS-485 flow control
  digitalWrite(13, HIGH);

  ads1.begin();
  ads1.setGain(GAIN_ONE);

  Serial.println("Initialization complete");
}

void loop() {
  // Read from and write to the main serial port (Serial2)
  if (Serial2.available()) {
    int inByte = Serial2.read();
    Serial.write(inByte);
  }

  // Read from and write to serial port 1 (Serial)
  if (Serial.available()) {
    int inByte = Serial.read();
    Serial2.write(inByte);
  }

  // Additional code from the second sketch:
  Serial.print("I1: ");
  Serial.println(digitalRead(35));
  Serial.print("I2: ");
  Serial.println(digitalRead(34));
  delay(800);

  printAnalog();

  delay(800);
  Serial1.println("RS-485 TX TEST");
}

void printAnalog() {
  for (int i = 0; i < 4; i++) {
    adcString[i] = String(ads1.readADC_SingleEnded(i));
    delay(10);
  }

  Serial.print("A1: ");
  Serial.print(adcString[0]);
  Serial.print("  ");
  Serial.print("A2: ");
  Serial.println(adcString[1]);
  Serial.print("A3: ");
  Serial.print(adcString[2]);
  Serial.print("  ");
  Serial.print("A4: ");
  Serial.println(adcString[3]);

  Serial.println("____________________________________");
}
