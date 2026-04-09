
# Tugas Modifikasi Praktikum Modul II: Pemrograman GPIO

**Nama:** Muhammad Faiz Mubarok  
**NIM:** H1H024051    




Repositori ini berisi kode program hasil modifikasi untuk **Percobaan 2A (Seven Segment Mundur)** dan **Percobaan 1B (Kontrol Counter dengan 2 Push Button)** beserta penjelasan baris per baris (*line-by-line*).

---

## 1. Modifikasi Percobaan 2A: Seven Segment (F ke 0)
**Tujuan:** Memodifikasi perulangan agar *Seven Segment* menampilkan karakter berjalan mundur dari F (15) hingga 0.

### Kode Program
```cpp
#include <Arduino.h>

const int segmentPins = {7, 6, 5, 11, 10, 8, 9, 4};

byte digitPattern = {
  {1,1,1,1,1,1,0,0}, {0,1,1,0,0,0,0,0}, {1,1,0,1,1,0,1,0}, {1,1,1,1,0,0,1,0},
  {0,1,1,0,0,1,1,0}, {1,0,1,1,0,1,1,0}, {1,0,1,1,1,1,1,0}, {1,1,1,0,0,0,0,0},
  {1,1,1,1,1,1,1,0}, {1,1,1,1,0,1,1,0}, {1,1,1,0,1,1,1,0}, {0,0,1,1,1,1,1,0},
  {1,0,0,1,1,1,0,0}, {0,1,1,1,1,0,1,0}, {1,0,0,1,1,1,1,0}, {1,0,0,0,1,1,1,0} 
};

void displayDigit(int num) {
  for(int i = 0; i < 8; i++) {
    digitalWrite(segmentPins[i], digitPattern[num][i]);
  }
}

void setup() {
  for(int i = 0; i < 8; i++) {
    pinMode(segmentPins[i], OUTPUT);
  }
}

void loop() {
  for(int i = 15; i >= 0; i--) {
    displayDigit(i);
    delay(1000);
  }
}
```

### Penjelasan *Line-by-Line*
| Baris Kode | Penjelasan |
| :--- | :--- |
| `#include <Arduino.h>` | Memasukkan *library* standar Arduino agar fungsi dasar dapat dikenali. |
| `const int segmentPins = {...};` | Deklarasi *array* berisi 8 nomor pin Arduino (7, 6, 5, 11, 10, 8, 9, 4) yang terhubung ke segmen a-g dan dp. |
| `byte digitPattern = {...};` | Deklarasi *array* 2 dimensi penyimpan pola biner 0 dan 1 untuk menyalakan LED *Common Cathode* (karakter 0 hingga F). |
| `void displayDigit(int num) {` | Mendeklarasikan fungsi bernama `displayDigit` yang menerima parameter angka tunggal (`num`). |
| `for(int i = 0; i < 8; i++) {` | Memulai perulangan sebanyak 8 kali untuk mengakses kedelapan pin segmen satu per satu. |
| `digitalWrite(..., ...);` | Mengirim sinyal `HIGH` (1) atau `LOW` (0) ke pin segmen sesuai pola di dalam *array* `digitPattern`. |
| `void setup() {` | Fungsi inisialisasi yang dijalankan hanya satu kali saat Arduino pertama kali dihidupkan. |
| `pinMode(segmentPins[i], OUTPUT);` | Mengatur kedelapan pin pada *array* `segmentPins` agar berfungsi sebagai pin keluaran (*OUTPUT*). |
| `void loop() {` | Fungsi utama yang dieksekusi secara berulang-ulang (*looping* kontinu). |
| `for(int i = 15; i >= 0; i--) {` | **[MODIFIKASI]** Perulangan mundur (*decrement*). Inisialisasi awal diubah menjadi 15 (indeks 'F'), berjalan selama nilai `i >= 0`, dan dikurangi 1 di setiap siklus. |
| `displayDigit(i);` | Memanggil fungsi penampilan karakter ke layar dengan memberikan nilai `i` saat itu (15, 14, 13... dst). |
| `delay(1000);` | Memberikan jeda waktu 1000 milidetik (1 detik) sebelum program berpindah ke angka berikutnya. |

---

## 2. Modifikasi Percobaan 2B: Kontrol Counter 2 Push Button
**Tujuan:** Memodifikasi sistem dengan dua *push button* untuk fungsi penambahan (*increment*) dan pengurangan (*decrement*) angka pada *Seven Segment*.

### Kode Program
```cpp
#include <Arduino.h>

const int segmentPins = {7, 6, 5, 11, 10, 8, 9, 4};
const int btnUp = 2;    
const int btnDown = 3;  

byte digitPattern = {
  {1,1,1,1,1,1,0,0}, {0,1,1,0,0,0,0,0}, {1,1,0,1,1,0,1,0}, {1,1,1,1,0,0,1,0},
  {0,1,1,0,0,1,1,0}, {1,0,1,1,0,1,1,0}, {1,0,1,1,1,1,1,0}, {1,1,1,0,0,0,0,0},
  {1,1,1,1,1,1,1,0}, {1,1,1,1,0,1,1,0}, {1,1,1,0,1,1,1,0}, {0,0,1,1,1,1,1,0},
  {1,0,0,1,1,1,0,0}, {0,1,1,1,1,0,1,0}, {1,0,0,1,1,1,1,0}, {1,0,0,0,1,1,1,0}
};

int currentDigit = 0;
bool lastUpState = HIGH;
bool lastDownState = HIGH;

void displayDigit(int num) {
  for(int i = 0; i < 8; i++) {
    digitalWrite(segmentPins[i], digitPattern[num][i]);
  }
}

void setup() {
  for(int i = 0; i < 8; i++) {
    pinMode(segmentPins[i], OUTPUT);
  }
  pinMode(btnUp, INPUT_PULLUP);
  pinMode(btnDown, INPUT_PULLUP);
  displayDigit(currentDigit);
}

void loop() {
  bool upState = digitalRead(btnUp);
  bool downState = digitalRead(btnDown);

  if(lastUpState == HIGH && upState == LOW) {
    currentDigit++;
    if(currentDigit > 15) currentDigit = 0;
    displayDigit(currentDigit);
  }
  
  if(lastDownState == HIGH && downState == LOW) {
    currentDigit--;
    if(currentDigit < 0) currentDigit = 15;
    displayDigit(currentDigit);
  }
  
  lastUpState = upState;
  lastDownState = downState;
}
```

### Penjelasan *Line-by-Line*
| Baris Kode | Penjelasan |
| :--- | :--- |
| `const int btnUp = 2;` | Mendeklarasikan pin 2 Arduino sebagai pin tombol tambah (*increment*). |
| `const int btnDown = 3;` | **[MODIFIKASI]** Mendeklarasikan pin 3 Arduino sebagai pin tombol kurang (*decrement*). |
| `int currentDigit = 0;` | Membuat variabel untuk menyimpan status digit angka yang sedang ditampilkan saat ini. |
| `bool lastUpState = HIGH;` | Variabel pembantu untuk menyimpan status (*state*) tombol up di siklus sebelumnya (untuk *edge detection*). |
| `bool lastDownState = HIGH;` | **[MODIFIKASI]** Variabel pembantu untuk menyimpan status tombol down di siklus sebelumnya. |
| `pinMode(..., INPUT_PULLUP);` | Mengatur pin `btnUp` dan `btnDown` sebagai *input*, serta mengaktifkan resistor *pull-up* internal mikrokontroler. |
| `displayDigit(currentDigit);` | Menampilkan angka '0' di layar sesaat setelah Arduino dinyalakan. |
| `bool upState = digitalRead(btnUp);` | Membaca status tegangan digital (`HIGH`/`LOW`) saat ini dari pin tombol up. |
| `bool downState = digitalRead(btnDown);` | **[MODIFIKASI]** Membaca status tegangan digital dari pin tombol down. |
| `if(lastUpState == HIGH && upState == LOW)` | Deteksi *Falling Edge*: Kondisi yang hanya bernilai benar saat tombol **baru saja ditekan** (transisi dari lepas ke tekan). |
| `currentDigit++;` | Menambahkan nilai variabel digit sebanyak 1 angka (*increment*). |
| `if(currentDigit > 15) currentDigit = 0;` | Proteksi *overflow*: Jika angka lebih besar dari 15 ('F'), maka siklus direset kembali ke 0. |
| `if(lastDownState == HIGH && downState == LOW)` | **[MODIFIKASI]** Deteksi transisi untuk tombol *down* (baru saja ditekan). |
| `currentDigit--;` | **[MODIFIKASI]** Mengurangi nilai variabel digit sebanyak 1 angka (*decrement*). |
| `if(currentDigit < 0) currentDigit = 15;` | **[MODIFIKASI]** Proteksi *underflow*: Jika angka lebih kecil dari 0, paksa kembali ke nilai 15 ('F'). |
| `lastUpState = upState;` | Memperbarui variabel penyimpanan riwayat tombol up dengan status terbarunya. |
| `lastDownState = downState;` | **[MODIFIKASI]** Memperbarui variabel penyimpanan riwayat tombol down dengan status terbarunya. |
