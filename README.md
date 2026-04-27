#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>
#include <MPU6050.h>
#include <TinyGPS++.h>
#include <HardwareSerial.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64

#define BUZZER 25
#define BUTTON 14

Adafruit_SSD1306 display(SCREEN_WIDTH, SCREEN_HEIGHT, &Wire, -1);
MPU6050 mpu;
TinyGPSPlus gps;

HardwareSerial gpsSerial(1);
HardwareSerial sim800(2);

// 🔴 CHANGE THIS
String phoneNumber = "+919XXXXXXXXX";

// STATE
bool accidentDetected = false;
bool smsSent = false;

unsigned long lastOLED = 0;
unsigned long buttonPressTime = 0;

// 📡 GPS READ (NON-BLOCKING)
void readGPS() {
  while (gpsSerial.available()) {
    gps.encode(gpsSerial.read());
  }
}

// 📩 SMS
void sendSMS(float lat, float lng) {
  sim800.println("AT+CMGF=1");
  delay(200);

  sim800.print("AT+CMGS=\"");
  sim800.print(phoneNumber);
  sim800.println("\"");
  delay(300);

  sim800.println("ACCIDENT ALERT");
  sim800.print("Location: https://maps.google.com/?q=");
  sim800.print(lat, 6);
  sim800.print(",");
  sim800.print(lng, 6);

  delay(300);
  sim800.write(26);

  delay(4000); // ensure sending complete
}

// 📞 CALL
void makeCall() {
  sim800.print("ATD");
  sim800.print(phoneNumber);
  sim800.println(";");

  delay(15000); // ring time

  sim800.println("ATH"); // hang up
  delay(3000);
}

// 🔘 BUTTON (LONG PRESS)
void handleButton() {
  if (digitalRead(BUTTON) == LOW) {
    if (buttonPressTime == 0) {
      buttonPressTime = millis();
    }
    if (millis() - buttonPressTime > 2000) {
      accidentDetected = true;
    }
  } else {
    buttonPressTime = 0;
  }
}

void setup() {
  Serial.begin(115200);

  Wire.begin(21, 22);

  // OLED
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextSize(1);
  display.setTextColor(WHITE);

  // MPU
  mpu.initialize();
  mpu.setSleepEnabled(false);

  // GPS
  gpsSerial.begin(9600, SERIAL_8N1, 32, 33);

  // SIM
  sim800.begin(9600, SERIAL_8N1, 26, 27);

  pinMode(BUZZER, OUTPUT);
  pinMode(BUTTON, INPUT_PULLUP);

  display.setCursor(0, 0);
  display.println("SYSTEM READY");
  display.display();
  delay(2000);

  // SIM INIT
  sim800.println("AT");
  delay(500);
  sim800.println("AT+CMGF=1");
  delay(500);
  sim800.println("AT+CLIP=1");
  delay(500);
}

void loop() {

  // 📡 Always read GPS
  readGPS();

  // 🔘 Button check
  handleButton();

  // 📊 MPU
  int16_t ax, ay, az;
  mpu.getAcceleration(&ax, &ay, &az);

  bool accident = (abs(ax) > 15000 || abs(ay) > 15000 || abs(az) > 20000);

  if (accident) {
    accidentDetected = true;
  }

  // 🚨 ALERT
  if (gps.location.isValid() &&
      gps.satellites.value() > 3 &&
      accidentDetected &&
      !smsSent) {

    float lat = gps.location.lat();
    float lng = gps.location.lng();

    digitalWrite(BUZZER, HIGH);
    delay(200);
    digitalWrite(BUZZER, LOW);

    sendSMS(lat, lng);
    delay(3000); // important gap
    makeCall();

    smsSent = true;
  }

  // 🔄 RESET
  if (!accident && digitalRead(BUTTON) == HIGH) {
    accidentDetected = false;
    smsSent = false;
  }

  // 📺 OLED UPDATE
  if (millis() - lastOLED > 500) {

    display.clearDisplay();
    display.setCursor(0, 0);

    if (accidentDetected) {
      display.println("ALERT ACTIVE");
    } else {
      display.println("STATUS: NORMAL");
    }

    display.print("Sat: ");
    display.println(gps.satellites.value());

    if (gps.location.isValid()) {
      display.println("GPS LOCKED");
      display.print("Lat:");
      display.println(gps.location.lat(), 5);
      display.print("Lng:");
      display.println(gps.location.lng(), 5);
    } else {
      display.println("SEARCHING GPS...");
    }

    display.display();
    lastOLED = millis();
  }
}
