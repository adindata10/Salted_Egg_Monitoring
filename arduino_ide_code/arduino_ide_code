//CONNECT TO BLYNK
#define BLYNK_PRINT Serial
#define BLYNK_TEMPLATE_ID "TMPL1UAmjr9w"
#define BLYNK_DEVICE_NAME "Monitoring Kematangan Telur Asin"
#define BLYNK_AUTH_TOKEN  "lWxioXbNdMRAIdpjevRRoGP-eD1m8NzG"

//PEMANGGILAN LIBRARY 
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include <DHT.h> 
#include <SPI.h>
#include <Wire.h>
#include <Adafruit_GFX.h> 
#include <Adafruit_SSD1306.h>
#include <TimeLib.h>

//PENDEKLARASIAN
#define DHTPIN D0 
#define DHTTYPE DHT11 
DHT dht(DHTPIN, DHTTYPE);
Adafruit_SSD1306 display = Adafruit_SSD1306(128, 64, &Wire);
#define SCREEN_ADDRESS 0x3C
#define LED D1
#define BUZZ D2
#define FAN D3
int sensorValue; //adc value
float outputValueConductivity; //conductivity value
float outputValueTDS; //TDS value

//MEMASUKKAN SSID DAN PASSWORD YANG DIGUNAKAN
char auth[] = BLYNK_AUTH_TOKEN ; 
char ssid[] = "wisata masa depan"; 
char pass[] = "elina yuka"; 

BlynkTimer timer; //UNTUK PEMANGGILAN BLYNK

void sendSensor(){
  float t = dht.readTemperature(); 
  float h = dht.readHumidity();

  Serial.print("% Temperature: ");
  Serial.print(t);
  Serial.println(" C");
  Serial.print("% Kelembaban: ");
  Serial.print(h);
  Serial.println(" %");
  Serial.println();
  
  if(t > 15 && h > 80){
    Serial.println("--------------------------------");
    Serial.println("Suhu dan Kelembaban Tidak Sesuai");
    Serial.println("--------------------------------");
    Serial.println();
    digitalWrite(LED, HIGH);
    digitalWrite(BUZZ, HIGH);
    digitalWrite(FAN, HIGH);
    delay(100);
  }
  else{
    digitalWrite(LED, LOW);
    digitalWrite(BUZZ, LOW);
    delay(100);
  }

  display.setTextSize(1); // menentukan ukuran teks
  display.setCursor(0,0); // mengatur posisi teks
  display.print ("Kelembaban"); // menampilkan teks ke OLED
  display.setTextSize(2); // menentukan ukuran teks
  display.setCursor(0,10); // mengatur posisi teks
  display.print(h); // menampilkan pembacaan kelembaban
  display.print(" %"); // menampilkan teks ke OLED
  display.setTextSize(1); // menentukan ukuran teks
  display.setCursor(0,35); // mengatur posisi teks
  display.print("Temperature"); // menampilkan teks ke OLED
  display.setTextSize(2); // menampilkan ukuran teks
  display.setCursor(0,45); // mengatur posisi teks
  display.print(t); // menampilkan pembacaan suhu
  display.print(" C"); // menampilkan teks ke OLED
  delay(10); // jeda untuk menyalakan OLED
  display.display(); // menampilkan isi OLED
  display.clearDisplay(); // membersihkan tampilan
  
  //menampilkan tanggal pada OLED
  display.setTextSize(1);
  display.setCursor(0,0);
  display.print("Tanggal : ");
  display.setTextSize(2);
  display.setCursor(0,10);
  printkurangdarinol(day());
  display.print(":");
  printkurangdarinol(month());
  display.print(":");
  printkurangdarinol(year());

  //menampilkan waktu pada OLED
  display.setTextSize(1);
  display.setCursor(0,35);
  display.print("Pukul : ");
  display.setTextSize(2);
  display.setCursor(0,45);
  printkurangdarinol(hour());
  display.print(":");
  printkurangdarinol(minute());
  display.print(":");
  printkurangdarinol(second());
  delay(1000);
  yield();
  display.display();
  display.clearDisplay();
 
  sensorValue = analogRead(A0);
  //PROGRAM SENSOR SALINITAS
  //Mathematical Conversion from ADC to conductivity (uSiemens)
  //rumus berdasarkan datasheet
  outputValueConductivity = (0.2142*sensorValue)+494.93;
  
  //Mathematical Conversion from ADC to TDS (ppm)
  //rumus berdasarkan datasheet
  outputValueTDS = (0.3417*sensorValue)+281.08;
  
  //print the results to the serial monitor:
  Serial.print("sensor ADC = ");
  Serial.print(sensorValue);
  Serial.print("  conductivity (uSiemens)= ");
  Serial.print(outputValueConductivity);
  Serial.print("  TDS(ppm)= ");
  Serial.println(outputValueTDS);
  Serial.println();

  display.setTextSize(1); // menentukan ukuran teks
  display.setCursor(0,0); // mengatur posisi teks
  display.print ("Kadar Garam"); // menampilkan teks ke OLED
  display.setTextSize(2); // menentukan ukuran teks
  display.setCursor(0,10); // mengatur posisi teks
  display.print(outputValueConductivity); // menampilkan pembacaan kelembaban
//  display.print(" %"); // menampilkan teks ke OLED
  display.setTextSize(1); // menentukan ukuran teks
  display.setCursor(0,35); // mengatur posisi teks
  display.print("TDS"); // menampilkan teks ke OLED
  display.setTextSize(2); // menampilkan ukuran teks
  display.setCursor(0,45); // mengatur posisi teks
  display.print(outputValueTDS); // menampilkan pembacaan suhu
//  display.print(" C"); // menampilkan teks ke OLED
  delay(10); // jeda untuk menyalakan OLED
  display.display(); // menampilkan isi OLED
  display.clearDisplay(); // membersihkan tampilan

  Blynk.virtualWrite(V0, t); //mengirimkan data temperatur ke Virtual pin VO 
  Blynk.virtualWrite(V1, h); //mengirimkan data kelemaban ke Virtual pin V1 
  Blynk.virtualWrite(V2, outputValueConductivity); //mengirimkan data konduktivitas ke Virtual pin V2
  Blynk.virtualWrite(V3, outputValueTDS); //mengirimkan data TDS ke Virtual pin V3
  Blynk.virtualWrite(V4, LED);
  Blynk.virtualWrite(V5, BUZZ);
  Blynk.virtualWrite(V6, day());
  Blynk.virtualWrite(V7, month());
  Blynk.virtualWrite(V8, year());
  Blynk.virtualWrite(V9, hour());
  Blynk.virtualWrite(V10, minute());
  Blynk.virtualWrite(V11, second());
}

void setup(){
  Serial.begin(115200);
  pinMode(LED,OUTPUT);
  pinMode(BUZZ,OUTPUT);
  dht.begin();
  
  if(!display.begin(SSD1306_SWITCHCAPVCC, SCREEN_ADDRESS)){
    Serial.println(F("SSD1306 allocation failed"));
    for(;;); // Don't proceed, loop forever
  }
    // ujicoba display teks OLED
  display.setTextSize(2); //ukuran teks
  display.setTextColor(SSD1306_WHITE); //warna OLED (putih)
  display.clearDisplay(); //membersihkan display oled
  setTime(19,00,00,19,12,22);
  // jam, menit, detik, tanggal, bulan, tahun
  delay(1000);
  Blynk.begin(auth, ssid, pass); //memulai Blynk
  timer.setInterval(1000L, sendSensor); //Mengaktifkan timer untuk pengiriman data 1000ms
}

void loop(){
  Blynk.run(); //menjalankan blynk
  timer.run(); //menjalankan timer
}

//ini adalah function untuk nilai 0 hingga 9 agar diberikan angka nol di depannya
void printkurangdarinol(int data){
  if (data >= 0 && data < 10){ //0 - 9
    display.write('0'); //
    }
    display.print(data);
}
