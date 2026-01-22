## 1. Dasar Pemrograman
### Pemrograman OOP
#### i. OOP

##### 1. **Class**

**blueprint / cetakan**.
class merupakan definisi,
di dalam class, kitsa nentuin **atribut (data)** dan **method (fungsi)** yang dimiliki suatu entitas.[^1]

[^1]:[Suryomujahid.school:Mengenal Class OOP](https://suryomujahid.school.blog/2020/08/20/mengenal-class-object-attribute-dan-method-dalam-oop/#:~:text=Pengertian%20Class,kali%20untuk%20membuat%20objek%20serupa.)

secara teknis di C++:
class itu mendefinisikan **user-defined data type**.

```cpp
class Drone {
public:
    int altitude;
    void takeOff() {
        altitude = 10;
    }
};
```

di sini `Drone` belum memiliki arti. hnaya struktur aja. belum makan memori runtime (kecuali static).

##### 2. **Object**

object itu **_instance_ dari class**.
jika class adalah konsep, object sudah memiliki arti di _memory_

secara teknis:
object punya **alokasi memori sendiri**, sesuai isi class.

Berikut contoh penggunaan Object:

```cpp
int main() {
    Drone uav1;
    uav1.altitude = 0;
    uav1.takeOff();
}
```

`uav1` itu object.
kalau bikin `Drone uav2;` lagi, memorinya beda, walaupun class-nya sama.

##### 3. **Abstraction**

abstraction itu berarti **menyembunyikan kompleksitas**, abstraction berguna untuk menunjukkan yang difokuskan.
user nggak perlu tau gimana detail dalemannya bekerja.

di C++, abstraction biasanya dicapai lewat:

* class
* function
* interface (pakai abstract class)

contoh abstraction:

```cpp
class FlightController {
public:
    void stabilize() {
        // algoritma PID, sensor fusion, dll
    }
};
```

user cukup fokus:
“oh panggil `stabilize()`, drone stabil”
tanpa mikir PID, Kalman, atau sensor IMU di dalemnya.

_in summarry_ abstraction fokus ke **what**, bukan **how**.

##### 4. **Encapsulation**

_Encapsulation_ (Enkapsulasi) merupakan konsep fundamental dalam Pemrograman Berorientasi Objek (OOP) yang menggabungkan `data (variabel)` dan `metode (fungsi)` yang mengoperasikannya menjadi `satu unit tunggal (kelas)`, plus ngatur aksesnya.
ini penting biar data nggak diacak-acak sembarangan. [^1]
[^1]:[Ciputra:Encapsulation](https://www.ciputra.ac.id/isb/memahami-konsep-encapsulation-dalam-java-dan-manfaatnya-dalam-oop/)

di C++ pake:

* `private`
* `protected`
* `public`

contoh:

```cpp
class Battery {
private:
    float voltage;

public:
    void setVoltage(float v) {
        if (v > 0) voltage = v;
    }

    float getVoltage() {
        return voltage;
    }
};
```

di sini `voltage` nggak bisa diakses langsung.
harus lewat **method**. ini mencegah error logika.

_in summary_ encapsulation = **data hiding + controlled access**.

##### 5. **Inheritance**

Inheritance (Pewarisan) dalam OOP adalah mekanisme di mana sebuah kelas baru (subclass/kelas anak) dapat mewarisi atribut (data) dan metode (fungsi) dari kelas yang sudah ada (superclass/kelas induk), memungkinkan penggunaan ulang kode (code reuse), pembentukan hierarki kelas, dan menghindari duplikasi kode, serta memfasilitasi polymorphism.[^2]
[^2]:[Kumparan:inheritance](https://kumparan.com/how-to-tekno/pengertian-inheritance-dalam-bahasa-pemrograman-1xbCKrvQzbg)

Inheritance **class mewarisi sifat class lain**.
dipakai untuk reuse code dan bikin hierarki.

contoh sederhananya:

```cpp
class UAV {
public:
    void arm() {
        // siap terbang
    }
};

class FixedWing : public UAV {
public:
    void cruise() {
        // terbang lurus jarak jauh
    }
};
```

`FixedWing` otomatis punya fungsi `arm()` dari `UAV`.

secara teknis:

* base class → parent
* derived class → child
* inheritance mendukung **is-a relationship**

`FixedWing is a UAV`

##### 6. **Polymorphism**

Polimorfisme dalam OOP (Object-Oriented Programming) adalah kemampuan sebuah objek, fungsi, atau variabel untuk memiliki banyak bentuk atau perilaku yang berbeda dalam konteks yang berbeda.[^3]
[^3]:[School of Computer Science:Pokymorphism](https://socs.binus.ac.id/2021/12/12/object-oriented-programming-polymorphism/)

Polymorphism itu satu interface, tapi **perilaku bisa beda-beda**.
biasanya digunakan bersamaan dengan inheritance.

di C++ biasanya ada untuk:

* `virtual function`
* `function overriding`

contohnya ialah:

```cpp
class UAV {
public:
    virtual void takeOff() {
        cout << "UAV take off" << endl;
    }
};

class Quadcopter : public UAV {
public:
    void takeOff() override {
        cout << "Vertical take off" << endl;
    }
};

class FixedWing : public UAV {
public:
    void takeOff() override {
        cout << "Runway take off" << endl;
    }
};
```

pemakaiannya:

```cpp
UAV* drone1 = new Quadcopter();
UAV* drone2 = new FixedWing();

drone1->takeOff();
drone2->takeOff();
```

walaupun tipenya `UAV*`,
yang dipanggil tetap fungsi sesuai object aslinya.

secara teknis ini disebut **runtime polymorphism**, dan bergantung ke **dynamic binding**.


##### In Summary (OOP)

* **class**: definisi tipe data buatan.
* **object**: instance nyata di memori.
* **abstraction**: fokus ke fungsi utama, sembunyikan detail.
* **encapsulation**: bungkus data + kontrol akses.
* **inheritance**: pewarisan properti dan method.
* **polymorphism**: satu interface, banyak perilaku.

#### ii. Design pattern sederhana dalam case Refactor

Saya akan fokus ke Observer karena observer pattern memiliki konsep ;

* Ada subject (sumber data)
* Ada Observer (Pihak yang bereaksi saat data berubah)
* Subjeknya cuman notify, dan identitas observer tidak jadi masalah.

Dalam cases ini sahya menganalis bahwa :
* TelemetryProcessor = Subjeknya
* Alert system, Logger, Analyzer = Observer

Saya akan merapihkan struktur Class terlebih dahulu :
* TelemetryData
→ struktur data, satu record telemetry

* TelemetryObserver
→ interface observer
→ semua “reaksi” terhadap telemetry harus nurut kontrak ini

* TelemetryProcessor (Subject)
→ baca file
→ tiap data masuk, dia notify observer
→ dia nggak peduli observer itu ngapain

Pipeline yang saya tangkap:

> File → TelemetryProcessor → notify() → Observer bereaksi

```cpp
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
#include <sstream>

//Telemetry Data
struct TelemetryData {
    float altitude;
    float speed;
    float battery;
};

//Observer Interface
class TelemetryObserver {
public:
    virtual void onTelemetryUpdate(const TelemetryData& data) = 0;
    virtual void onTelemetryFinish(int totalCount) {}
    virtual ~TelemetryObserver() = default;
};

//Alert Observer
class AlertObserver : public TelemetryObserver {
public:
    void onTelemetryUpdate(const TelemetryData& data) override {
        if (data.battery < 20.0f) {
            std::cout << "Low battery alert: "
                      << data.battery << "%" << std::endl;
        }

        if (data.altitude > 100.0f) {
            std::cout << "High altitude alert: "
                      << data.altitude << "m" << std::endl;
        }
    }
};

//Statistics Observer

class StatisticsObserver : public TelemetryObserver {
private:
    float totalAltitude = 0.0f;
    float totalSpeed = 0.0f;
    float totalBattery = 0.0f;
    int count = 0;

public:
    void onTelemetryUpdate(const TelemetryData& data) override {
        totalAltitude += data.altitude;
        totalSpeed += data.speed;
        totalBattery += data.battery;
        count++;
    }

    void onTelemetryFinish(int) override {
        if (count == 0) return;

        std::cout << "avg altitude: "
                  << totalAltitude / count << "m" << std::endl;
        std::cout << "avg speed: "
                  << totalSpeed / count << " m/s" << std::endl;
        std::cout << "avg battery: "
                  << totalBattery / count << "%" << std::endl;
    }
};

//Telemetry Processor (Subject)
class TelemetryProcessor {
private:
    std::vector<TelemetryObserver*> observers;

public:
    void addObserver(TelemetryObserver* observer) {
        observers.push_back(observer);
    }

    void processTelemetry(const std::string& filename) {
        std::ifstream file(filename);
        if (!file.is_open()) {
            std::cerr << "error membuka " << filename << std::endl;
            return;
        }

        std::string line;
        int count = 0;

        while (std::getline(file, line)) {
            std::istringstream iss(line);
            TelemetryData data;

            if (iss >> data.altitude >> data.speed >> data.battery) {
                for (auto* obs : observers) {
                    obs->onTelemetryUpdate(data);
                }
                count++;
            }
        }

        for (auto* obs : observers) {
            obs->onTelemetryFinish(count);
        }
    }
};

// Main

int main() {
    TelemetryProcessor processor;

    AlertObserver alertObserver;
    StatisticsObserver statisticsObserver;

    processor.addObserver(&alertObserver);
    processor.addObserver(&statisticsObserver);

    processor.processTelemetry("lampiran/telemetry_data.txt");

    return 0;
}
```

### 2. Bahasa C++

#### 1. `#include <file_name>` vs `#include "file_name"`

dua-duanya buat **nyisipin file lain ke kode**.
Tapi memiliki perbedaan.[^4]
[^4]:[Stack Overflow: #include](https://stackoverflow.com/questions/21593/what-is-the-difference-between-include-filename-and-include-filename?utm_source=chatgpt.com)

* `#include <file_name>`
  ini biasanya untuk **library standar** atau library eksternal. compiler akan mencari dulu di **system include path**, seperti STL (`<iostream>`, `<vector>`).

* `#include "file_name"`
  ini buat **file buatan sendiri**. compiler akan mencari di folder project, baru ke system path jika tidak ketemu.[^4]
[^4]: [GeeksforGeeks:Difference between include](https://www.geeksforgeeks.org/cpp/difference-between-include-and-include-in-c-c-with-examples/?utm_source=chatgpt.com)

secara teknis, ini ngaruh ke **urutan pencarian file**, bukan ke isinya. isinya sama aja disalin pas preprocessing.

#### 2. `#ifdef`, `#ifndef`, `#endif`, dan `#pragma once`

ini semua urusannya **preprocessor**, sebelum kode dikompilasi.

biasanya dipakai di **header file** buat mencegah *multiple inclusion*.[^5]
[^5]:[Include guard / header guard” di Wikipedia](https://en.wikipedia.org/wiki/Include_guard?utm_source=chatgpt.com)

contoh biasanya seperti berikut:

```cpp
#ifndef TELEMETRY_H
#define TELEMETRY_H

// isi header

#endif
```

artinya:

* `#ifndef` (kalau belum pernah didefinisikan)
* `#define` (tandai bahwa header ini udah dipakai)
* `#endif` (tutup kondisi)

Jika headrr di-include dua kali, compiler nggak bakal ngulang isinya.

`#pragma once` itu versi modernnya. lebih simpel:

```cpp
#pragma once
```

compiler akan menerima bahwa: “file ini cukup di-include sekali”.

#### 3. Namespace dan scope resolution operator `::`

**namespace** dipakai buat **nghindarin tabrakan nama**.

misalnya banyak library punya fungsi `init()`, kalau nggak pake namespace, ribet.

```cpp
namespace uav {
    void init() {}
}
```

cara manggilnya:

```cpp
uav::init();
```

nah `::` itu namanya **scope resolution operator**.
fungsinya buat nunjuk **ruang lingkup** mana yang kita maksud.

contoh lain:

```cpp
std::cout << "hello";
```

`std::` artinya `cout` milik namespace `std`.

juga bisa dipakai buat:

* class → method
* class → static variable
* global variable

---

#### 4. Perbedaan `#define` dan `using`

dua-duanya sering dipakai buat “penyingkatan”, tapi beda level.

`#define` itu **macro**, kerja di tahap preprocessor.
nggak kenal tipe data, cuma teks diganti mentah.

```cpp
#define PI 3.14
```

bahaya karena:

* nggak type-safe
* susah di-debug

`using` itu **bahasa C++ beneran**, type-safe.

```cpp
using ull = unsigned long long;
```

atau buat namespace:

```cpp
using std::cout;
```

secara modern C++, `using` jauh lebih disarankan daripada `#define` buat alias.

---

#### 5. Pointer (`*`) dan address-of (`&`)

pointer itu **variabel yang nyimpen alamat memori**, bukan nilai langsung.

```cpp
int x = 10;
int* p = &x;
```

* `&x` → ambil alamat memori dari `x`
* `p` → nyimpen alamat itu
* `*p` → dereference, ambil nilai di alamat itu

jadi:

* `x` = 10
* `*p` = 10
* `p` ≠ 10 (dia alamat)

pointer penting buat:

* efisiensi
* dynamic memory
* pass by reference manual
* struktur data kompleks

#### 6. Pass by value vs pass by reference

**pass by value**
nilai dikopi. fungsi dapet salinan.

```cpp
void foo(int x) {
    x = 5;
}
```

variabel asli di luar **nggak berubah**.
aman, tapi bisa mahal buat data besar.

**pass by reference**
fungsi dapet akses langsung ke variabel asli.

```cpp
void foo(int& x) {
    x = 5;
}
```

variabel luar **ikut berubah**.

biasanya dipakai buat:

* efisiensi
* modifikasi data
* output parameter

di C++ modern, sering digabung sama `const &` buat aman:

```cpp
void print(const Data& d);
```

#### 7. `std::unique_ptr` vs `std::shared_ptr`

ini soal **smart pointer**, buat ngatur memori otomatis.

**`std::unique_ptr`**

* satu owner
* nggak bisa di-copy
* paling ringan dan aman

```cpp
std::unique_ptr<int> p = std::make_unique<int>(10);
```

cocok kalau:

* kepemilikan jelas
* lifecycle sederhana
* performa penting (misalnya embedded, UAV)

**`std::shared_ptr`**

* banyak owner
* pakai reference counting
* objek dihapus kalau owner terakhir hilang

```cpp
std::shared_ptr<int> p1 = std::make_shared<int>(10);
std::shared_ptr<int> p2 = p1;
```

cocok kalau:

* banyak bagian program butuh akses objek yang sama
* lifecycle susah ditebak

tapi lebih berat, dan rawan memory cycle kalau salah pakai.


#### In Summarry

saya dapat menyimpulkan secara abstrak bahwa :

* `< >` vs `" "` → beda tempat nyari file
* `#ifndef` & `#pragma once` → cegah include dobel
* `namespace` + `::` → atur ruang lingkup nama
* `#define` vs `using` → macro vs type-safe
* `*` dan `&` → alamat memori dan isinya
* pass by value vs reference → copy vs akses langsung
* `unique_ptr` vs `shared_ptr` → kepemilikan tunggal vs bersama

### 3. Multithreading

#### i. Konsep Multithreading

Multithreading adalah kemampuan satu program buat ngerjain lebih dari satu tugas dalam waktu yang sama.
bukan berarti CPU-nya dobel, tapi sistem operasi menjadwalkan ubtuk beberapa thread agar bersinergi.[^5]
[^5]:[C++ Multithreading Explained - The Chemo](https://www.youtube.com/watch?v=wXBcwHwIt_I)


Satu program bisa memiliki:

* **1 process**
* Namun banyak **thread** di dalamnya

tiap thread punya:

* instruction sendiri
* Tetapi **berbagi memori yang sama**

Oleh sebab itu, Multithreading cocok buat _case_ seperti:

* GCS nerima telemetry
* sambil nerima video
* sambil update UI

jika tanpa multithreading, akan terjadi _bottleneck_

#### ii. Simulasi GCS: video stream + telemetry stream

di sini saya akan bikin **2 thread**:

* **thread 1 (video)**
  cetak `"Receiving video frame..."` tiap 2 detik

* **thread 2 (telemetry)**
  baca file `telemetry.txt`
  cetak tiap baris, delay 3 detik per baris

keduanya jalan **barengan**, tanpa saling nunggu.


**contoh isi `telemetry.txt`**

```
50 10 80
60 12 75
70 15 15
120 18 70
55 11 85
45 9 90
80 14 50
110 16 30
65 13 60
75 17 40
100 20 25
40 8 95
90 19 35
55 12 70
85 15 55
```

(based on telemetry_data.txt RSC THT 26)
#### iii. Source Codenya

```cpp
#include <iostream>
#include <thread>
#include <fstream>
#include <string>
#include <chrono>

// thread untuk simulasi video stream
void videoStream() {
    while (true) {
        std::cout << "[VIDEO] Receiving video frame..." << std::endl;
        std::this_thread::sleep_for(std::chrono::seconds(2));
    }
}

// thread untuk simulasi telemetry stream
void telemetryStream() {
    std::ifstream file("telemetry.txt");
    std::string line;

    while (true) {
        file.clear();
        file.seekg(0); // balik ke awal file

        while (std::getline(file, line)) {
            std::cout << "[TELEMETRY] " << line << std::endl;
            std::this_thread::sleep_for(std::chrono::seconds(3));
        }
    }
}

int main() {
    std::thread videoThread(videoStream);
    std::thread telemetryThread(telemetryStream);

    videoThread.join();
    telemetryThread.join();

    return 0;
}
```


##### contoh output (±10 detik runtime)

```
[VIDEO] Receiving video frame...
[TELEMETRY] ALT:50 SPD:10 BAT:80
[VIDEO] Receiving video frame...
[TELEMETRY] ALT:60 SPD:12 BAT:75
[VIDEO] Receiving video frame...
[VIDEO] Receiving video frame...
[TELEMETRY] ALT:70 SPD:15 BAT:65
[VIDEO] Receiving video frame...
[TELEMETRY] ALT:80 SPD:18 BAT:55
[VIDEO] Receiving video frame...
```

terlihat:

* video muncul tiap 2 detik
* telemetry muncul tiap 3 detik
* **saling selang-seling**, bukan nunggu salah satu selesai

ini nunjukkin dua thread **jalan paralel secara logis**.

## Jurusan Ground Control Station (GCS)
#### 1. MAVLink
##### Struktur pesan MAVLink
MAVLink adalah protokol komunikasi ringan antara UAV dan GCS, dia ngirim data lewat **paket pesan biner**. Setiap pesan punya bagian-bagian dasar seperti:

* **header** yang nunjukkin awal pesan
* **system id** dan **component id**
* **message id** (yang nunjukin tipe pesan)
* **payload** (data sebenarnya)
* **checksum** buat ngecek integritas data¹

Jadi sebelum datanya dipakai, si penerima bisa baca dulu siapa yang ngirim, apa pesannya, dan pastiin nggak rusak waktu transit.

Contohnya heartbeat, itu juga punya struktur khusus tapi tetep pakai format umum MAVLink[^5].

[^5]:["Heartbeat/Connection Protocol | MAVLink Guide"](https://mavlink.io/en/services/heartbeat.html?utm_source=chatgpt.com)


##### Sistem ID dan Komponen ID

Di MAVLink, setiap perangkat punya dua angka penting:

* **System ID** unik buat satu **sistem** (misalnya satu UAV atau satu GCS). jadi kalo kamu punya 2 drone, sistem ID mereka beda¹.
* **Component ID** bagian di dalam satu sistem, misalnya flight controller, GPS, kamera, dll¹.

Ini membuat pesan MAVLink bisa **ditujukan ke entitas yang tepat**, misalnya dari GCS ke kamera tapi bukan ke flight controller, atau sebaliknya[^5].

kalau UAS (Unmanned Aircraft System) punya banyak bagian, yang di-pakein ID biar nggak tabrakan.


##### Cara kerja sistem heartbeat dalam MAVLink

**Heartbeat** itu semacam “aku masih hidup” yang dikirim terus-menerus. setiap komponen (drone, GCS, sensor) ngirim heartbeat secara periodik supaya yang lain tahu dia masih **terhubung**¹.

fungsi heartbeat:

* bikin GCS tau kalau UAV masih aktif
* nentuin status koneksi: kalau heartbeat berhenti, dianggap disconnect¹
* pas UI GCS muncul, mereka bisa **tampil sesuai tipe kendaraan** (quad, fixed wing, dll) dari data heartbeat¹

jadi heartbeat itu kayak *stay alive signal*, tanpa bagian data yang rumit tapi penting banget.[^5]

##### Jenis-jenis pesan MAVLink yang umum

MAVLink punya *banyak* tipe pesan yang udah didefinisiin di file XML protokolnya (common.xml, ardupilotmega.xml, dll). beberapa pesan umum banget dipake di operasi UAV:

* **HEARTBEAT** untuk memberikan arti (masih aktif) ke link lainnya[^5]
* **MISSION_ITEM**  buat ngirim waypoint atau misi waypoint ke UAV[^5]

[^5]:[Message list dan struktur message MAVLink: daftar message common.xml:](https://mavlink.io/en/messages/common.html?utm_source=chatgpt.com)
* **GLOBAL_POSITION_INT**  mengirim posisi GPS UAV (lat, lon, alt) ke GCS¹
* **LOCAL_POSITION_NED** posisi lokal (North, East, Down)¹
* **COMMAND_LONG** pesan buat ngirim perintah kaya takeoff/land¹
* **ATTITUDE** informasi orientasi drone (roll, pitch, yaw)¹

ada ratusan pesan lain, tergantung aplikasi dan kebutuhan sistem UAV[^5].


#### 2. Arsitektur basic untuk GCS UAV

Diagram sederhana arsitekturnya:
```dgrm
[ Browser (User) ]
        |
        |  HTTP / WebSocket
        v
[ Web Server / Backend ]
        |
        |  API / Message Queue
        v
[ GCS Core / MAVLink Handler ]
        |
        |  Telemetry Link (Radio / UDP / Serial)
        v
[ UAV ]
```

##### 1. Browser
Ini adalah aplikasi web yang dibuka user lewat browser (Chrome, Firefox, dll).
Fungsinya:

* Menampilkan data UAV (altitude, speed, battery, posisi)
* Menyediakan kontrol (arm, takeoff, land, waypoint)
* Menerima update secara real-time

Biasanya dibuat pakai **HTML, CSS, JavaScript**.


##### 2. Web Server / Backend

* Menerima request dari browser (HTTP)
* Mengirim data real-time (biasanya pakai **WebSocket**)
* Mengelola autentikasi user
* Jadi perantara antara web dan sistem GCS

Contoh: backend Python (Flask/FastAPI) atau Node.js.

##### 3. GCS Core / MAVLink Handler

Bagian ini fokus ke **logika UAV**:

* Parsing pesan MAVLink
* Mengirim command ke UAV
* Menerjemahkan data UAV jadi format yang bisa dipakai web

Biasanya backend **tidak langsung** bicara ke UAV, tapi lewat layer ini supaya rapi dan aman.

##### 4. Telemetry Link

Ini jalur komunikasi fisik / network:

* Radio telemetry
* UDP over WiFi
* Serial / USB

Data dari UAV masuk lewat sini → diteruskan ke GCS core.

##### 5. UAV

Sumber data utama:

* Mengirim telemetry (heartbeat, GPS, battery)
* Menerima command dari GCS

##### **Alur kerja singkat**

1. UAV kirim telemetry
2. GCS core decode MAVLink
3. Backend kirim data ke browser via WebSocket
4. User klik tombol (misalnya takeoff)
5. Command dikirim balik ke UAV


#### Konsep dasar Docker

##### 1. Perbedaan Container vs Virtual Machine**

**Virtual Machine (VM):**

* Setiap VM punya **OS sendiri**
* Berat (RAM & storage besar)
* Booting lama

**Container (Docker):**

* Berbagi kernel OS yang sama
* Ringan & cepat
* Isinya cuma aplikasi + dependensi

_in Summarry_

> VM = satu komputer penuh
> Container = satu aplikasi terisolasi

---

##### 2. Cara kerja Dockerfile

Dockerfile adlah dasar blueprint atau "resep" untuk bikin container.

Isinya yaitu sebagai berikut:

* Base image (misalnya Ubuntu atau Python)
* Install dependency
* Copy source code
* Tentukan perintah saat container jalan

Contoh alur:

1. Docker baca Dockerfile
2. Docker build image
3. Image dijalankan jadi container

Sekali build, bisa dijalankan di mana pun **tanpa beda hasil**.

##### 3. Manfaat Docker untuk aplikasi web GCS

Docker cocok untuk GCS sebab:

* **Environment**
  Backend GCS jalan sama persis di laptop, server, atau cloud, jkemudian.

* **Mudah deployment**
  Tinggal `docker run`, nggak ribet install manual, juga

* **Isolasi sistem**
  Error di backend web nggak ganggu sistem lain.


**In Summary**

* Arsitektur web GCS = browser + backend + GCS core ↔ UAV
* Docker bikin sistem GCS **portable, konsisten, dan mudah dikembangin**.

#### 4. Aplikasi Web Sederhana untuk GCS
WIP
#### 5. Pengintegrasian MAVLink dengan Aplikasi Web saya
WIP