#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define OLED_RESET 4
Adafruit_SSD1306 display(OLED_RESET);

#define TRIGGER_PIN_1 4 // pin trigger sensor ultrasonik 1
#define ECHO_PIN_1 5    // pin echo sensor ultrasonik 1
#define TRIGGER_PIN_3 6 // pin trigger sensor ultrasonik 2
#define ECHO_PIN_3 7    // pin echo sensor ultrasonik 2

void setup() {
  pinMode(TRIGGER_PIN_1, OUTPUT);
  pinMode(ECHO_PIN_1, INPUT);
  pinMode(TRIGGER_PIN_3, OUTPUT);
  pinMode(ECHO_PIN_3, INPUT);
  
  Serial.begin(9600);
  
  display.begin(SSD1306_SWITCHCAPVCC, 0x3C);
  display.clearDisplay();
  display.setTextColor(WHITE);
  display.setTextSize(1);
}

void loop() {
  double duration_1, distance_1, distance_2;
  double duration_3, distance_3;
  int maximumRange = 150;
  
  
  // Mengukur jarak menggunakan sensor ultrasonik pertama
  digitalWrite(TRIGGER_PIN_1, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIGGER_PIN_1, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIGGER_PIN_1, LOW);
  duration_1 = pulseIn(ECHO_PIN_1, HIGH);
  distance_1 = (duration_1 * 0.034 / 2);
  distance_2 = maximumRange - distance_1;

  // Mengukur jarak menggunakan sensor ultrasonik kedua
  digitalWrite(TRIGGER_PIN_3, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIGGER_PIN_3, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIGGER_PIN_3, LOW);
  duration_3 = pulseIn(ECHO_PIN_3, HIGH);
  distance_3 = (duration_3 * 0.034 / 2);

  // Menghitung sudut kemiringan menggunakan fungsi atan
  double angle1 = atan(distance_2 / distance_3) * 180/PI;

  // Menampilkan hasil pada OLED
  display.clearDisplay();
  display.setCursor(0, 0);
  display.println("Sensor X: " + String(distance_1) + " cm");
  display.println("Delta X: " + String(distance_2) + " cm");
  display.println("Sensor Y: " + String(distance_3) + " cm");
  display.println("Sudut: " + String(angle1) + " derajat");
  display.display();

  // Menampilkan hasil pada Serial Monitor
  Serial.print("Sensor 1: ");
  Serial.print(distance_1);
  Serial.print("Delta X: ");
  Serial.print(distance_2);
  Serial.print(" cm, Sensor 2: ");
  Serial.print(distance_3);
  Serial.print(" cm, Angle: ");
  Serial.print(angle1);
  Serial.println(" degrees");

  delay(2000);
}
