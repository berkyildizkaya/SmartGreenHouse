# Smart Greenhouse - Akıllı Sera Arduino Projesi


## Alınacaklar Lisetesi

| ÜRÜN        | ADET
| ------------- |:-------------:|
| 2X16 LCD       | 1
| KIRMIZI/YEŞİL/SARI LED | 10
| DHT11   | 1
| LM35 | 1
| BREADBORD TEKLİ| 1
| DİRENÇ | 16
| 10K POT  | 1
| LDR 5mm  | 1
| HC-SR04 ULTRASONİC SENSÖR   | 1
| Duman Gaz Sensörü  | 1
| RFID KİTİ          | 1
| SG90 9G SERVO | 1
| PIR hareket sensörü | 1
| SIVI SEVİYE  | 1
| Arduino UNO veya MEGA | 1

## Görevler

### 1. Sıcaklık ile fan kontrolü

Sıcaklık 25 dereceden fazla ise fan çalışacak.

### 2. Işık sensörü kontrolü

1. Işık seviyesi ölçülüp , gündüz servo motora bağlı çatı perdesi açacak, gece kapatacak.

2. LCD yazılacak.

### 3. Su seviyesi kontrölü
1. Su tankındaki su seviyesi ölçülecek, ve LCD yazılacak uyarı verilecek.

### 4. Ultrasonic sensör ile hırsız kontrolü
1. 80 cm yakın obje bulunursa buzzer ötecek,LCD HIRSIZ VAR yazacak.

### 5. PIR hareket sensörü ile böcek algılanması
1. Bir oda karanlık olunca LED yakılacak.

### 6. Gaz sensörü
1. Duman ve karbondioksit seviyesini ölçen system yapılıp değerler LCD gösterilecek.
2. Eğer belli seviyenin üstündeyse ve sıcaklıkta yüksekse, yangın alarmı buzzer ile verilecek.

### 7. RFID giriş sistemi
1. RFID ile en son kişi girişlerin ad soyad bilgilerini lcd üzerinde gösterin.




# Kodlar ve Bağlantılar
 ## ISI 
![LM35](https://user-images.githubusercontent.com/48087914/58386678-ea92de00-800b-11e9-8f87-b82a58a447c5.png)

```C
#include <LiquidCrystal.h>
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
const int buzzer = 8; 
int lm35Pin = A0;

void setup() {
  lcd.begin(16, 2);
  Serial.begin(9600);
 analogReference(INTERNAL); 
  pinMode(buzzer, OUTPUT); 
}

void loop() {
 int sicaklikVolt = analogRead(lm35Pin);
  float sicaklikC = sicaklikC = sicaklikVolt / 9.31; 
    lcd.setCursor(0, 1);
    lcd.print(sicaklikC);
    lcd.setCursor(6,1);
    lcd.print("7C");
    delay(100);
}
```

## Işık
![LDR](https://user-images.githubusercontent.com/48087914/58386712-4198b300-800c-11e9-9b03-161718e303b0.png)
```C
#include <Servo.h>
Servo myservomotor;
int ledPin = 5;  
int LDRPin = A1;
void setup(){
Serial.begin(9600);
pinMode(ledPin, OUTPUT);
digitalWrite(ledPin,LOW);
myservomotor.attach(8);  // arduinonun 8. pinini çıkış yaptık.
//myservomotor.write(0);
}
void loop(){
  int isik_degeri = analogRead(LDRPin); 
Serial.println(isik_degeri); 
delay(100);  
if(isik_degeri>=200) 
{
    digitalWrite(ledPin,LOW);
    // 90 derecelik açı yap çatı aç
myservomotor.write(90); 
  // Çatıyı açmak için dereceyi arttırmak
 }
 else
 {
      digitalWrite(ledPin,HIGH);
      myservomotor.write(0);
 }
}
```

## Su Seviye
![WaterSensor](https://user-images.githubusercontent.com/48087914/58386739-bc61ce00-800c-11e9-9d36-b1d637dff5d7.png)

<a href="https://www.robotistan.com/su-seviyesi-yagmur-sensoru-water-level-rain-sensor">Bu linkdeki sensör kullanılmıştır.</a>

```C
#include <LiquidCrystal.h>
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
const int read = A3; 
int seviye; 
void setup()
{ Serial.begin(9600);
  lcd.begin(16, 2);
}
void loop()
{
seviye = analogRead(read); 
if (seviye<=300){ 
Serial.println("Su seviyesi cok az");
lcd.setCursor(0, 1);
lcd.clear();
lcd.print("Su Seviye az");
}
else if (seviye>300 && seviye<=490){ 
Serial.println("Orta seviye su seviyesi"); 
lcd.setCursor(0, 1);
lcd.clear();
lcd.print("Su Seviye orta");
}
else if (seviye>490 && seviye<=700){ 
Serial.println("İdeal su seviyesi");
lcd.setCursor(0, 1);
lcd.clear();
lcd.print("Su Seviye ideal"); 
}
else if (seviye>700){ 
Serial.println("Maksimum su seviyesi");
lcd.setCursor(0, 1);
lcd.clear();
lcd.print("maksimum"); 
} 
delay(4000); 
}
```

## Ultrasonic Sensör İle Hırsız
![Ultrasonic](https://user-images.githubusercontent.com/48087914/58386755-0b0f6800-800d-11e9-82b4-04b768705223.png)

```C
#define trigPin 6
#define echoPin 7
#include <LiquidCrystal.h>

// Arayüz pinlerinin numaraları ile kütüphanemizi başlatıyoruz.
LiquidCrystal lcd(11, 12, 13, 9, 4, 3);

int Buzzer =5 ; // Connect buzzer pin to 8
int duration, distance; 
void setup() {
        Serial.begin (9600); 
        //Çıkış ve girişleri belirtiyoz
        pinMode(trigPin, OUTPUT); 
        pinMode(echoPin, INPUT);
        pinMode(Buzzer, OUTPUT);       
        lcd.begin(16, 2);
}
void loop() {
    digitalWrite(trigPin, HIGH);
    //delayMicroseconds(1000);
    digitalWrite(trigPin, LOW);
    duration = pulseIn(echoPin, HIGH);
    distance = (duration/2) / 29.1;
    lcd.clear();
  if (distance >= 50 || distance <= 0) 
        {
        lcd.setCursor(0,0);
        lcd.print("");
        lcd.print("Relaxx");
        Serial.println("Obje yok");
        digitalWrite(Buzzer,LOW);
        noTone(Buzzer);       
        }
  else {
        lcd.setCursor(0,1);
        lcd.print("");       
        lcd.print("Hirsiz");       
        Serial.println("obje Bulundu \n");
        Serial.print("distance= ");              
        Serial.print(distance);        //Mesafeyi yazma
        tone(Buzzer,400);              // 400 HZ de ses bas
          }
}
```


## Pır Sensörü
![PIR](https://user-images.githubusercontent.com/48087914/58386770-41e57e00-800d-11e9-999e-2c788c48e681.png)
```C
int sensor = 2;
int led = 13;
int buzzer = 10;
int sensordurum = LOW;
int deger = 0;
void setup() {
  pinMode(sensor, INPUT);
  pinMode(led, OUTPUT);
  pinMode(buzzer, OUTPUT);
  Serial.begin(9600);
}
void loop() {
  deger = digitalRead(sensor);
  if (deger == HIGH) {
    digitalWrite(led, HIGH);
    playTone(300, 160);
    delay(100);
    if (sensordurum == LOW) {
      Serial.println("Hareket Tespit Edildi!");
      sensordurum = HIGH;
    }
  } else {
    digitalWrite(led, LOW);
    playTone(0, 0);
    delay(100);
    if (sensordurum == HIGH) {

      Serial.println("Hareket Sonlandi!");

      sensordurum = LOW;
    }
  }
}
void playTone(long duration, int freq) {
  duration *= 1000;
  int period = (1.0 / freq) * 1000000;
  long elapsed_time = 0;
  while (elapsed_time < duration) {
    digitalWrite(buzzer, HIGH);
    delayMicroseconds(period / 2);
    digitalWrite(buzzer, LOW);
    delayMicroseconds(period / 2);
    elapsed_time += (period);
  }
}
```

## Gaz Sensörü
![GAZ](https://user-images.githubusercontent.com/48087914/58386779-912bae80-800d-11e9-85da-c733a5b5797c.png)
```C
#include <dht11.h> //DHT11 Sıcaklık Sensörü Kütüphanesi
dht11 DHT; // DHT11 Kütüphane Fonksiyonu
#define DHT11_PIN 13 // DHT11 Sinyal pini ( Dijital 6 ) 
const int buzzer=12; // Buzzer Sinyal Pini
const int AOUTpin=A1; // Duman Sensörü Analog Pini
const int DOUTpin=7; // Duman Sensörü Dijital Pini
// RGB Led
const int kirmizi=3; // RGB Led Kırmızı Işık Pini
const int yesil=4; // RGB Led Yeşil Işık Pini
int sicaklik; // Sıcaklık Değişkeni
int limit; // Duman Sensörü Dijital Değişkeni
int value; // Duman Sensörü Alanlog Değişkeni
void setup()
{
Serial.begin(9600); // Seri Bağlantı Hızı
pinMode(DOUTpin, INPUT); // Duman Sensörü Dijital Pin Girişi
pinMode(kirmizi, OUTPUT); // RGB Led Kırmızı Işık Çıkışı
pinMode(yesil, OUTPUT); // RGB Led Yeşil Işık Çıkışı
pinMode(buzzer, OUTPUT); // Buzzer Çıkışı
beep(50);
beep(50);
beep(50);
delay(1000);
}
void loop()
{
sicaklik = DHT.read(DHT11_PIN); // Sıcaklık Değeri Okunuyor
value= analogRead(AOUTpin); // Duman Sensörü Analog Değeri Okunuyor
limit= digitalRead(DOUTpin); // Duman Sensörü Dijital Değeri Okunuyor
Serial.print("Duman Seviyesi: \t"); // Serial Monitör Yazısı
Serial.println(value,1); // Serial Monitör Çıktısı
Serial.print("Sicaklik: \t"); // Serial Monitör Yazısı
Serial.print(DHT.temperature,1); // Serial Monitör Çıktısı
Serial.print(",\t"); // Boşluk Karakteri 
delay(300);
if (value > 200){
beep(200);
//Kullanılan RGB Led Anot (Pozitif) Uçlu Olduğudan LOW Değeri Ledi Yakar
Serial.println("Duman derecesi 240 dan büyük" );
digitalWrite(kirmizi, LOW); // Kırmızı Işık Açık
digitalWrite(yesil, HIGH); // Yeşil Işık Kapalı
}
else if (DHT.temperature > 24){
beep(200);
Serial.print("Sıcaklık 24 den büyük ");
digitalWrite(kirmizi, LOW); // Kırmızı Işık Açık
digitalWrite(yesil, HIGH); // Yeşil Işık Kapalı
}
else {
digitalWrite(kirmizi, HIGH); // Kırmızı Işık Kapalı
digitalWrite(yesil, LOW); // Yeşil Işık Açık
}
}
// Buzzer Alarm Fonksiyonu
void beep(unsigned char delayms){
digitalWrite(buzzer, HIGH); // Buzzer Ses Verir
delay(delayms); // Belirlilen MS cinsinden bekletme
digitalWrite(buzzer, LOW); // Buzzer Sesi Kapatır
delay(delayms); // Belirlilen MS cinsinden bekletme
}
}
```


## RFİD
![RFİD](https://user-images.githubusercontent.com/48087914/58386811-f2ec1880-800d-11e9-8be9-5ff0bba99bdf.png)

### RFİD Boşalt
```C
#include <EEPROM.h>
void setup() {
  pinMode(13, OUTPUT);
  for (int i = 0 ; i < EEPROM.length() ; i++) {
    EEPROM.write(i, 0);
  }
  digitalWrite(13, HIGH);
}
void loop() {
}
```

### RFİD Kart Kaydet
```C
#include <LiquidCrystal.h>
// Arayüz pinlerinin numaraları ile kütüphanemizi başlatıyoruz.
LiquidCrystal lcd(8, 7, 6, 5, 4, 3);
#include <SPI.h> //RC522 modülü SPI protokolünü kullanır.
#include <MFRC522.h> //Mifare RC522 kütüphanesi
#include <EEPROM.h> //Okuma ve yazma işini UID ile EEPROM a kaydedicez.
#define RST_PIN 9
#define SS_PIN 10
byte readCard[4]; // 32 bitlik veri okumaya yarayan veri tibi
int successRead;
MFRC522 mfrc522(SS_PIN, RST_PIN);
MFRC522::MIFARE_Key key;
void setup()
{ 
Serial.begin(9600);
lcd.begin(16,2);
  lcd.clear();
SPI.begin();
mfrc522.PCD_Init();
lcd.print("1.Kart Okutun");
Serial.println("RFID KART KAYDETME");
Serial.println("***************************");
Serial.println("Lutfen 1 numarali karti okutun");
Serial.println();
do {
//okuma başarılı olana kadar getID fonksiyonunu çağır
successRead = getID();
}
while (!successRead);
for ( int i = 0; i < mfrc522.uid.size; i++ )
{
//kartın UID'sini EEPROM'a kaydet
EEPROM.write(i, readCard[i] );
}
lcd.clear();
lcd.print("KART KAYDEDILDI"); 
Serial.println("Kart EEPROM'a kaydedildi.");
Serial.println();
delay(1000);
lcd.clear();
lcd.print("2.Karti okutun");
Serial.println("Lutfen 2 numarali karti okutun.");
Serial.println();
do {
successRead = getID();
}
while (!successRead);
for ( int i = 0; i < mfrc522.uid.size; i++ )
{
EEPROM.write(i + 4, readCard[i] );
}
lcd.clear();
lcd.print("Kart Kaydedildi");
delay(1000);
Serial.println("Kart EEPROM'a kaydedildi.");
Serial.println();
lcd.clear();
lcd.print("3.Kart Okutun");
Serial.println("Lutfen 3 numarali karti okutun.");
Serial.println();
do {
successRead = getID();
}
while (!successRead);
for ( int i = 0; i < mfrc522.uid.size; i++ )
{
EEPROM.write(i + 8, readCard[i] );
}
lcd.clear();
lcd.print("Kaydedildi");
Serial.println("Kart EEPROM'a kaydedildi.");
Serial.println();
Serial.println("Kart kayit islemi basarili!");
}
void loop()
{
}
int getID() {
//yeni bir kart okunmadıysa 0 döndür
if ( ! mfrc522.PICC_IsNewCardPresent()) {
return 0;
}
if ( ! mfrc522.PICC_ReadCardSerial()) {
return 0;
}
Serial.print("Kart UID'si: ");
//kartın UID'sini byte byte oku ve seri monitöre yaz
for (int i = 0; i < mfrc522.uid.size; i++) { //
readCard[i] = mfrc522.uid.uidByte[i];
Serial.print(readCard[i], HEX);
}
Serial.println("");
//kart okumayı durdur ve 1 döndür (okuma başarılı)
mfrc522.PICC_HaltA();
return 1;
}
```
### RFİD Kart Tanıma
```C
#include <LiquidCrystal.h>
// Arayüz pinlerinin numaraları ile kütüphanemizi başlatıyoruz.
LiquidCrystal lcd(8, 7, 6, 5, 4, 3);
#include <SPI.h>
#include <MFRC522.h>
#include <EEPROM.h>
#define RST_PIN 9
#define SS_PIN 10
#define ledPin 7
MFRC522 mfrc522(SS_PIN, RST_PIN);
String lastRfid = "";
String kart1 = "";
String kart2 = "";
String kart3 = "";
MFRC522::MIFARE_Key key;
void setup()
{
  Serial.begin(9600);
  lcd.begin(16,2);
  SPI.begin();
  mfrc522.PCD_Init();
  pinMode(ledPin, OUTPUT);
  Serial.println("RFID KART OKUMA UYGULAMASI");
  Serial.println("--------------------------");
  Serial.println();
  //EEPROM'dan kart bilgisini oku
  readEEPROM();
}
void loop()
{
  //yeni kart okununmadıkça devam etme
  if ( ! mfrc522.PICC_IsNewCardPresent())
  {
    return;
  }
  if ( ! mfrc522.PICC_ReadCardSerial())
  {
    return;
  }
  //kartın UID'sini oku, rfid isimli string'e kaydet
  String rfid = "";
  for (byte i = 0; i < mfrc522.uid.size; i++)
  {
    rfid += mfrc522.uid.uidByte[i] < 0x10 ? " 0" : " ";
    rfid += String(mfrc522.uid.uidByte[i], HEX);
  }
  //string'in boyutunu ayarla ve tamamını büyük harfe çevir
  rfid.trim();
  rfid.toUpperCase();
  
  if (rfid == lastRfid)
    return;
  lastRfid = rfid;
  Serial.print("Kart 1: ");
  Serial.println(kart1);
  Serial.print("Kart 2: ");
  Serial.println(kart2);
  Serial.print("Kart 3: ");
  Serial.println(kart3);
  Serial.print("Okunan: ");
  Serial.println(rfid);
  Serial.println();
  //1 nolu kart okunduysa LED'i yak, 2 nolu kart okunduysa LED'i söndür
  if (rfid == kart1)
  {
    lcd.print("Hosgeldin X");
    delay(2000);
    lcd.clear();
    Serial.println("Hoşgeldin X");
  }
  else if (rfid == kart2)
  {
    lcd.print("Hosgeldin Y");
    delay(2000);
    lcd.clear();
    Serial.println("Hoşgeldin Y");
  }
  else if (rfid == kart3)
  {
    lcd.print("Hosgeldin Z");
    delay(2000);
    lcd.clear();
    Serial.println("Hoşgeldin Z");
  }
  else{
    lcd.print("Yetkisiz Giriş");
    delay(2000);
    lcd.clear();
    Serial.println("Yetkisiz Giriş");
  }
  Serial.println();
  delay(200);
}
void readEEPROM()
{
  //EEPROM'dan ilk kartın UID'sini oku (ilk 4 byte)
  for (int i = 0 ; i < 4 ; i++)
  {
    kart1 += EEPROM.read(i) < 0x10 ? " 0" : " ";
    kart1 += String(EEPROM.read(i), HEX);
    
  }
  //EEPROM'dan ikinci kartın UID'sini oku
  for (int i = 4 ; i < 8 ; i++)
  {
    kart2 += EEPROM.read(i) < 0x10 ? " 0" : " ";
    kart2 += String(EEPROM.read(i), HEX);
  }
  for (int i = 8 ; i < 12 ; i++)
  {
    kart3 += EEPROM.read(i) < 0x10 ? " 0" : " ";
    kart3 += String(EEPROM.read(i), HEX);
  }
  //Okunan stringleri düzenle
  kart1.trim();
  kart1.toUpperCase();
  kart2.trim();
  kart2.toUpperCase();
  kart3.trim();
  kart3.toUpperCase();
}
```