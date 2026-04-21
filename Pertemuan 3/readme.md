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
