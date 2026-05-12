# Percobaan 1
## Jawaban Pertanyaan Praktikum
## 1. Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
Ketiga task berjalan secara bergantian karena FreeRTOS menggunakan scheduler preemptive berbasis prioritas dan time-slicing, sehingga task dengan prioritas sama akan memperoleh giliran CPU secara bergiliran, sementara vTaskDelay() membuat task masuk ke kondisi Blocked agar CPU dapat digunakan oleh task lain sehingga tampak berjalan bersamaan.

## 2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!
Untuk menambahkan task keempat pada FreeRTOS, deklarasikan terlebih dahulu prototipe fungsi task baru, tambahkan xTaskCreate() di dalam setup() sebelum vTaskStartScheduler(), lalu buat implementasi fungsi task menggunakan perulangan while(1) dan vTaskDelay() agar penggunaan CPU tetap teratur.

## 3. Modifikasilah program dengan menambah sensor (potensiometer), lalu gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya?
link https://wokwi.com/projects/463828756837075969
<img width="797" height="595" alt="image" src="https://github.com/user-attachments/assets/1940bf65-e5a2-4793-8c28-21cd883e6639" />
## Kode Modifikasi
```cpp
#include <Arduino_FreeRTOS.h>

volatile int dly = 500; // blinkDelay -> dly

void tB1(void *p); // TaskBlink1 -> tB1
void tB2(void *p);
void tPr(void *p);
void tPo(void *p);

void setup() {
  Serial.begin(9600);

  xTaskCreate(tB1, "B1", 100, NULL, 1, NULL);
  xTaskCreate(tB2, "B2", 100, NULL, 1, NULL);
  xTaskCreate(tPr, "PR", 100, NULL, 1, NULL);
  xTaskCreate(tPo, "PO", 100, NULL, 1, NULL);

  vTaskStartScheduler();
}

void loop() {}

// Task Potensio
void tPo(void *p) {
  for(;;) {
    dly = map(analogRead(A0), 0, 1023, 50, 1000);
    vTaskDelay(100 / portTICK_PERIOD_MS);
  }
}

// Task LED 1 (Pin 8)
void tB1(void *p) {
  pinMode(8, OUTPUT);
  for(;;) {
    digitalWrite(8, !digitalRead(8)); // Toggle state biar lebih singkat
    vTaskDelay(dly / portTICK_PERIOD_MS);
  }
}

// Task LED 2 (Pin 7)
void tB2(void *p) {
  pinMode(7, OUTPUT);
  for(;;) {
    digitalWrite(7, !digitalRead(7)); 
    vTaskDelay((dly + 100) / portTICK_PERIOD_MS);
  }
}

// Task Serial Print
void tPr(void *p) {
  int c = 0; // counter -> c
  for(;;) {
    Serial.print("C:"); Serial.print(++c);
    Serial.print(" | D:"); Serial.println(dly);
    vTaskDelay(1000 / portTICK_PERIOD_MS);
  }
}
```

# Percobaan 2 
## 1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!
Kedua task beroperasi secara bergantian namun memberikan kesan concurrent karena task read_data mengirimkan data ke queue melalui xQueueSend() lalu melakukan delay selama 100 ms sehingga masuk kondisi Blocked dan CPU digunakan oleh task display, sedangkan task display menunggu data dari queue menggunakan xQueueReceive() dengan portMAX_DELAY, sehingga sinkronisasi antar task terjamin dan data tidak terlewat maupun terbaca lebih dari sekali.

## 2. Apakah program ini berpotensi mengalami race condition? Jelaskan!
Tidak, program ini tidak berpotensi mengalami race condition karena kondisi race condition umumnya muncul ketika dua atau lebih task mengakses memori atau sumber daya bersama secara simultan tanpa mekanisme sinkronisasi yang memadai, sedangkan pada program ini seluruh pertukaran data antara task read_data dan task display dilakukan sepenuhnya melalui queue bawaan FreeRTOS.

## 3. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga informasi yang ditampilkan dinamis. Bagaimana hasilnya?
# Kode Modifikasi 
```cpp
#include <DHT.h>

#define DHTPIN 2      // Pin data sensor (S) terhubung ke pin D2
#define DHTTYPE DHT11 

DHT dht(DHTPIN, DHTTYPE);

// Variabel pengganti Scheduler RTOS
unsigned long waktuSebelumnya = 0;
const long jedaWaktu = 2000; // Jeda 2 detik antar pembacaan

void setup() {
  Serial.begin(9600);
  dht.begin();
  Serial.println("Sistem Mulai Membaca Sensor..."); 
}

void loop() {
  // Mengecek waktu saat ini (sebagai pengganti Task RTOS)
  unsigned long waktuSekarang = millis();

  // Jika sudah berlalu 2 detik, jalankan eksekusi
  if (waktuSekarang - waktuSebelumnya >= jedaWaktu) {
    waktuSebelumnya = waktuSekarang;

    // Membaca data dari sensor DHT
    float t = dht.readTemperature();
    float h = dht.readHumidity();

    // Memastikan data valid (berperan seperti QueueReceive)
    if (!isnan(t) && !isnan(h)) {
      Serial.print("Temperature: ");
      Serial.print(t);
      Serial.println(" °C");
      
      Serial.print("Humidity: ");
      Serial.print(h);
      Serial.println(" %");
      Serial.println("---");
    } else {
      Serial.println("Gagal membaca sensor DHT!");
    }
  }
}
```
# Dokumentasi
https://github.com/user-attachments/assets/d4fbf89c-a606-4772-935f-c68eafb6fc15

<img width="2160" height="2880" alt="image" src="https://github.com/user-attachments/assets/1dcb6636-856b-4007-9e68-967261bbb162" />

