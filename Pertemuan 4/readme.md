# Percobaan 1(4A): Analog to Digital Converter (ADC)
1. Apa fungsi perintah analogRead() pada rangkaian praktikum ini?
> analogRead() adalah fungsi pada Arduino yang digunakan untuk membaca tegangan analog dari pin input (A0–A5) dan mengubahnya menjadi nilai digital dengan rentang 0–1023. Pada praktikum ini digunakan untuk membaca input dari potensiometer.

2. Mengapa diperlukan fungsi map() dalam program tersebut?
> Fungsi map() digunakan untuk mengubah rentang nilai tertentu ke rentang nilai lain. Pada praktikum ini, map() digunakan untuk mengkonversi nilai ADC (0–1023) menjadi sudut servo (misalnya 0–180 derajat) agar sesuai dengan kebutuhan output.

3. Modifikasi program berikut agar servo hanya bergerak dalam rentang 30° hingga 150°, meskipun potensiometer tetap memiliki rentang ADC 0–1023. Jelaskan program pada file README.md
>pada saat potensiometer menghasilkan output maksimal (1023), sudut motor servo hanya akan mencapai batas maksimal 150 derajat, begitu pula pada (0) ke 30 derajat
```cpp
pos = map(val,
            0,    // min ADC
            1023, // max ADC
            30,    // sudut min
            150   // sudut max
            );
```
Full Code :
```
#include <Servo.h> // library untuk servo motor

Servo myservo; // membuat objek servo

// ===================== PIN SETUP =====================
const int potensioPin = A0;   // pin analog input
const int servoPin = 9;       // pin PWM servo

// ===================== VARIABEL =====================
int pos = 0; // sudut servo
int val = 0; // nilai ADC

void setup() {

  // Hubungkan servo ke pin
  myservo.attach(servoPin);

  // Serial monitor
  Serial.begin(9600);

}

void loop() {

  // ===================== PEMBACAAN ADC =====================
  val = analogRead(potensioPin);

  // ===================== KONVERSI DATA =====================
  pos = map(val,
            0,    // min ADC
            1023, // max ADC
            30,    // sudut min
            150   // sudut max
            );

  // ===================== OUTPUT SERVO =====================
  myservo.write(pos);

  // ===================== MONITORING =====================
  Serial.print("ADC Potensio: ");
  Serial.print(val);

  Serial.print(" | Sudut Servo: ");
  Serial.println(pos);

  // ===================== STABILISASI =====================
  delay(100);
}
```
# Percobaan 2(4B): Pulse Width Modulation (PWM)
1. Jelaskan mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()!
> LED dapat diatur kecerahannya menggunakan fungsi analogWrite() karena Arduino menghasilkan sinyal PWM (Pulse Width Modulation), yaitu sinyal digital yang dinyalakan dan dimatikan dengan cepat. Kecerahan LED ditentukan oleh duty cycle, yaitu perbandingan lama waktu sinyal HIGH terhadap satu periode. Semakin besar nilai PWM, semakin lama sinyal berada pada kondisi HIGH sehingga tegangan rata-rata yang diterima LED meningkat dan LED terlihat lebih terang.

2. Apa hubungan antara nilai ADC (0–1023) dan nilai PWM (0–255)?
>Nilai ADC memiliki rentang 0–1023 karena menggunakan resolusi 10-bit, sedangkan PWM memiliki rentang 0–255 karena menggunakan resolusi 8-bit. Oleh karena itu, diperlukan proses scaling (menggunakan fungsi map()) untuk menyesuaikan nilai ADC agar dapat digunakan sebagai nilai PWM.

3. Modifikasilah program berikut agar LED hanya menyala pada rentang kecerahan sedang, yaitu hanya ketika nilai PWM berada pada rentang 50 sampai 200. Jelaskan program pada file README.md.
```
  pwm = map(nilaiADC,
            0,     // ADC min
            1023,  // ADC max
            0,     // PWM min
            255    // PWM max
            );

  // ===================== OUTPUT PWM =====================
  if (pwm >= 50 && pwm <= 200){ //ketika rentan nilai pwm 50-200
  	analogWrite(ledPin, pwm);   // led menyala sesuai nilai pwm
  } else {
	analogWrite(ledPin, 0);       // led mati diluar rentang nilai
  }
kode lengkapnya

// ===================== PIN SETUP =====================
const int potPin = A0;   // pin analog potensiometer
const int ledPin = 9;    // pin PWM LED

// ===================== VARIABEL =====================
int nilaiADC = 0;  // nilai pembacaan ADC
int pwm = 0;       // nilai PWM

void setup() {

  // ===================== OUTPUT SETUP =====================
  pinMode(ledPin, OUTPUT);

  // ===================== SERIAL MONITOR =====================
  Serial.begin(9600);
}

void loop() {

  // ===================== PEMBACAAN SENSOR =====================
  nilaiADC = analogRead(potPin);

  // ===================== PEMROSESAN DATA =====================
  pwm = map(nilaiADC,
            0,     // ADC min
            1023,  // ADC max
            0,     // PWM min
            255    // PWM max
            );

  // ===================== OUTPUT PWM =====================
  if (pwm >= 50 && pwm <= 200){ //ketika rentan nilai pwm 50-200
  	analogWrite(ledPin, pwm);   // led menyala sesuai nilai pwm
  } else {
	analogWrite(ledPin, 0);       // led mati diluar rentang nilai
  }
  // ===================== MONITORING =====================
  Serial.print("ADC: ");
  Serial.print(nilaiADC);

  Serial.print(" | PWM: ");
  Serial.println(pwm);

  // ===================== STABILISASI =====================
  delay(50);
}
```
# Dokumentasi
<img width="500" height="500" alt="IMG_4189" src="https://github.com/user-attachments/assets/f9252d3a-a832-4f8c-9a13-952115b8c8af" /><img width="500" height="500" alt="image" src="https://github.com/user-attachments/assets/288f2f8b-6178-46af-9891-960cdc216e8f" />
https://github.com/user-attachments/assets/50cd1a36-1fe4-47ff-8418-6eda47419863




