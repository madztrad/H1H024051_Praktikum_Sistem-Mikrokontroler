# Percobaan 3A: UART
### 1. Jelaskan proses dari input keyboard hingga LED menyala/mati!
> Ketika pengguna mengetik perintah 1 atau 0 pada keyboard di Serial Monitor, data dikirim ke Arduino melalui komunikasi serial (UART). Arduino kemudian membaca data tersebut menggunakan Serial.read(). Jika data yang diterima adalah '1', maka Arduino memberikan logika HIGH pada pin LED sehingga LED menyala. Jika data '0', maka diberikan logika LOW sehingga LED mati.
### 2. Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika baris tersebut dihilangkan?
> Berfunsi untuk mengembalikan jumlah byte yang siap dibaca. Jika nilainya lebih besar dari 0, berarti ada pesan yang menunggu untuk diproses, jika dihilangkan program akan terus-menerus mencoba membaca data pada setiap iterasi, saat tidak ada data masuk, Serial.read() akan mengembalikan nilai -1.
### 3. Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi
jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan berikan penjelasan disetiap baris kode nya
```cpp
#include <Arduino.h>        // Library utama Arduino

const int PIN_LED = 12;     // Menentukan pin LED di pin 12
bool blinkMode = false;     // Variabel untuk menyimpan mode blink (true/false)

void setup() {
  Serial.begin(9600);      // Memulai komunikasi serial dengan baud rate 9600
  Serial.println("Ketik '1' ON, '0' OFF, '2' BLINK"); // Instruksi ke user
  pinMode(PIN_LED, OUTPUT); // Mengatur pin LED sebagai output
}

void loop() {
  // Mengecek apakah ada data yang masuk dari Serial Monitor
  if (Serial.available() > 0) {
    char data = Serial.read(); // Membaca data 1 karakter dari serial

    if (data == '1') {         // Jika input '1'
      blinkMode = false;       // Matikan mode blink
      digitalWrite(PIN_LED, HIGH); // Nyalakan LED
      Serial.println("LED ON");    // Tampilkan status
    } 
    else if (data == '0') {    // Jika input '0'
      blinkMode = false;       
      digitalWrite(PIN_LED, LOW);  // Matikan LED
      Serial.println("LED OFF");
    } 
    else if (data == '2') {    // Jika input '2'
      blinkMode = true;        // Aktifkan mode blink
      Serial.println("LED BLINK");
    }
  }

  // Jika mode blink aktif
  if (blinkMode) {
    digitalWrite(PIN_LED, HIGH); // LED nyala
    delay(500);                  // Tunggu 500 ms
    digitalWrite(PIN_LED, LOW);  // LED mati
    delay(500);                  // Tunggu 500 ms
  }
}
```
### 4. Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap sistem
> Program ini menggunakan delay() karena lebih sederhana untuk membuat efek kedip. Namun, delay() bersifat blocking sehingga Arduino tidak dapat melakukan proses lain selama delay berlangsung. Jika menggunakan millis(), program menjadi non-blocking sehingga bisa multitasking.
## Percobaan 3B: I2C
### 1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD!
> I2C menggunakan dua kabel (SDA untuk data, SCL untuk detak jam). Arduino bertindak sebagai Master yang mengirimkan alamat unik (0x27) untuk memanggil LCD (Slave). Setelah terhubung, Master mengirimkan data instruksi atau karakter secara berurutan lewat jalur SDA yang disinkronkan oleh pulsa SCL.
### 2. Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan
pin kanan tertukar
> Ya, karena data dari potensiometer adalah tegangan kontinu (0-5V) yang perlu diubah menjadi angka digital (0-1023) melalui ADC Arduino. Jika Pin Kiri & Kanan Tertukar, arah putaran akan terbalik. Jika sebelumnya diputar ke kanan nilai bertambah besar, setelah tertukar, diputar ke kanan justru nilai akan mengecil menuju nol
## 3. Modifikasi program (gabungan UART + I2C output)
```cpp
#include <Wire.h>                // Library komunikasi I2C
#include <LiquidCrystal_I2C.h>   // Library LCD I2C
#include <Arduino.h>             // Library utama Arduino

LiquidCrystal_I2C lcd(0x27, 16, 2); // Inisialisasi LCD (alamat 0x27, 16x2)

const int pinPot = A0;           // Pin analog untuk potensiometer

void setup() {
  Serial.begin(9600);           // Memulai komunikasi serial
  lcd.init();                   // Inisialisasi LCD
  lcd.backlight();              // Mengaktifkan lampu latar LCD
}

void loop() {
  int nilai = analogRead(pinPot); // Membaca nilai analog (0-1023)

  float volt = nilai * (5.0 / 1023.0); // Konversi ke tegangan (Volt)
  int persen = map(nilai, 0, 1023, 0, 100); // Konversi ke persen (0-100%)

  // Menampilkan data ke Serial Monitor (UART)
  Serial.print("ADC: ");
  Serial.print(nilai);
  Serial.print(" Volt: ");
  Serial.print(volt);
  Serial.print(" V Persen: ");
  Serial.print(persen);
  Serial.println("%");

  // Menampilkan data ke LCD baris pertama
  lcd.setCursor(0, 0);          // Set posisi kolom 0, baris 0
  lcd.print("ADC:");
  lcd.print(nilai);             
  lcd.print("   ");             // Menghapus sisa karakter lama

  // Membuat progress bar di baris kedua
  lcd.setCursor(0, 1);          // Pindah ke baris kedua
  int panjangBar = map(nilai, 0, 1023, 0, 16); // Mapping ke panjang bar

  for (int i = 0; i < 16; i++) { // Loop sepanjang 16 kolom LCD
    if (i < panjangBar) {
      lcd.print((char)255);     // Karakter blok penuh
    } else {
      lcd.print(" ");           // Kosong
    }
  }

  delay(200);                   // Delay agar tampilan stabil
}
```
## Dokumentasi

