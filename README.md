# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
Reflection Publisher-1

1.Dalam teori Observer Pattern, Subscriber biasanya didefinisikan sebagai interface (atau trait di Rust) agar Publisher tidak bergantung pada implementasi konkret, sesuai dengan prinsip Dependency Inversion.

Namun, dalam kasus BambangShop ini, penggunaan trait tidak terlalu diperlukan karena:

-Subscriber hanya memiliki satu bentuk implementasi, yaitu sebagai representasi endpoint (URL) untuk menerima notifikasi.

-Tidak ada variasi perilaku Subscriber yang berbeda-beda.

Sehingga, penggunaan satu struct Subscriber saja sudah cukup untuk memenuhi kebutuhan sistem saat ini. Meskipun demikian, jika di masa depan terdapat berbagai jenis subscriber dengan perilaku berbeda, maka penggunaan trait akan menjadi lebih relevan untuk menjaga fleksibilitas dan extensibility.

2.Karena id pada Program dan url pada Subscriber bersifat unik, maka penggunaan struktur data sangat penting.

Jika menggunakan Vec:

-Pencarian dan penghapusan membutuhkan iterasi (O(n)) -Tidak efisien untuk skala besar

Sedangkan dengan DashMap:

-Mendukung akses berbasis key (O(1)) -Memastikan keunikan data secara langsung -Lebih efisien untuk operasi CRUD

Oleh karena itu, penggunaan DashMap lebih tepat dibandingkan Vec, karena: Lebih efisien , lebih sesuai dengan kebutuhan data yang memiliki key unik, mendukung performa yang lebih baik

3.Dalam kasus ini, DashMap digunakan untuk memastikan thread safety karena aplikasi dapat berjalan secara concurrent (misalnya saat mengirim notifikasi ke banyak subscriber).

Singleton Pattern memang dapat digunakan untuk memastikan hanya ada satu instance dari data (SUBSCRIBERS). Namun:

-Singleton tidak menjamin thread safety -singleton hanya mengatur jumlah instance, bukan mekanisme akses concurrent

Sebaliknya, DashMap:

-Secara bawaan sudah thread-safe -Mendukung concurrent read/write tanpa race condition

Sehingga: -DashMap tetap diperlukan untuk menjamin keamanan akses data -Singleton saja tidak cukup tanpa mekanisme tambahan seperti Mutex atau RwLock

Kesimpulannya, dalam konteks ini, DashMap lebih tepat digunakan dibanding hanya mengandalkan Singleton Pattern, karena mampu memenuhi kebutuhan thread safety sekaligus efisiensi akses data.

#### Reflection Publisher-2

1.Dalam konsep dasar MVC, Model memang mencakup penyimpanan data dan business logic. Namun, dalam praktik pengembangan modern, tanggung jawab tersebut sering dipisahkan menjadi Service dan Repository untuk mengikuti prinsip desain seperti Single Responsibility Principle (SRP).

-Repository bertanggung jawab khusus untuk akses dan manipulasi data (CRUD). -Service bertanggung jawab untuk business logic dan alur proses aplikasi. -Model hanya merepresentasikan struktur data.

Pemisahan ini membuat:

-Kode lebih modular dan mudah dipahami -Lebih mudah untuk diuji (unit testing) -Lebih fleksibel untuk perubahan di masa depan

Tanpa pemisahan ini, Model akan menjadi terlalu kompleks dan sulit untuk dipelihara.

2.Jika hanya menggunakan Model tanpa memisahkan Service dan Repository, maka setiap Model akan menangani:

-Struktur data -Penyimpanan data -Business logic -Interaksi dengan Model lain

Hal ini akan menyebabkan:

-Tight coupling antar model (Program, Subscriber, Notification saling bergantung langsung) -Kode menjadi sulit dibaca dan dipahami -Perubahan kecil dapat berdampak besar ke banyak bagian sistem -Sulit untuk melakukan testing karena logic bercampur

Sebagai contoh:

-Model Program harus tahu cara menyimpan Subscriber -Model Subscriber harus tahu cara mengirim notifikasi -Model Notification harus tahu cara diproses dan dikirim

Akibatnya, kompleksitas meningkat drastis dan melanggar prinsip desain seperti Separation of Concerns.

3.Postman adalah tools yang sangat membantu dalam menguji API yang sedang dikembangkan.

Dalam proyek ini, Postman membantu untuk:

-Mengirim HTTP request seperti GET, POST, DELETE ke endpoint yang dibuat -Menguji apakah endpoint bekerja sesuai dengan yang diharapkan -Melihat response dari server secara langsung

Fitur Postman yang berguna:

-Collection → menyimpan kumpulan endpoint untuk testing terstruktur -Environment variables → memudahkan penggantian URL atau parameter -Body (JSON) → mempermudah pengiriman data seperti Subscriber -Response viewer → melihat hasil response dengan jelas -History → melacak request yang sudah pernah dilakukan

Postman sangat membantu dalam proses debugging dan memastikan API berjalan dengan benar, serta akan sangat berguna dalam proyek kelompok maupun pengembangan aplikasi di masa depan.

#### Reflection Publisher-3

1. Model Variasi Observer Pattern: Push Model
Dalam kasus tutorial BambangShop ini, variasi Observer Pattern yang diterapkan adalah Push Model. Hal ini terkonfirmasi melalui alur kerja di mana pihak publisher (dalam hal ini NotificationService) secara aktif mengirimkan data atau payload lengkap berupa objek Notification kepada seluruh subscriber yang terdaftar. Pihak publisher memegang kendali penuh dalam mendistribusikan informasi segera setelah terjadi perubahan status pada produk, seperti saat proses pembuatan (create) atau penghapusan (delete). Struktur data yang dikirimkan pun sudah mencakup detail lengkap seperti judul produk, tipe, URL, dan statusnya melalui metode HTTP POST. Dengan demikian, subscriber bersifat pasif karena mereka langsung menerima informasi yang dibutuhkan tanpa harus melakukan permintaan data tambahan kembali ke server.

2. Analisis Penggunaan Pull Model: Kelebihan dan Kekurangan
Jika tutorial ini beralih menggunakan Pull Model, maka mekanisme komunikasi akan berubah secara signifikan. Dalam model ini, publisher hanya akan mengirimkan sinyal notifikasi minimal atau sekadar identitas unik (ID) produk yang berubah, kemudian subscriberlah yang bertanggung jawab untuk melakukan pemanggilan balik (request) ke server guna mengambil detail informasi tersebut.

Kelebihan utama dari Pull Model terletak pada konsistensi data; subscriber akan mendapatkan versi data yang paling mutakhir tepat pada saat mereka melakukan pengambilan (pull). Selain itu, beban transmisi data awal dari publisher menjadi lebih ringan karena ukuran payload yang dikirimkan sangat kecil. Namun, kekurangannya cukup krusial dalam konteks aplikasi web seperti BambangShop. Pull Model akan menciptakan fenomena network chattiness karena diperlukan dua kali proses komunikasi (notifikasi dan penjemputan data). Hal ini akan meningkatkan beban kerja pada server (CPU dan RAM) karena harus melayani banyak permintaan GET secara bersamaan dari para subscriber sesaat setelah notifikasi dikirimkan, serta menyebabkan latensi yang lebih tinggi bagi pengguna akhir.

3. Dampak Penonaktifan Multi-threading pada Proses Notifikasi
Implementasi multi-threading (menggunakan tokio::spawn atau thread::spawn) sangat vital bagi performa program secara keseluruhan. Jika proses pengiriman notifikasi dilakukan secara single-threaded atau sequential, maka program akan mengirimkan notifikasi kepada subscriber satu per satu secara berurutan. Hal ini menciptakan risiko blocking yang besar; apabila terdapat satu subscriber yang memiliki koneksi lambat atau sedang mengalami gangguan (down), maka seluruh alur eksekusi program akan terhenti sementara menunggu proses tersebut selesai.

Akibatnya, waktu respons API utama (misalnya saat admin menambah produk) akan menjadi sangat lambat karena harus menunggu seluruh proses distribusi notifikasi ke semua pihak selesai. Tanpa multi-threading, skalabilitas aplikasi akan sangat buruk karena semakin banyak jumlah subscriber, semakin lama pula waktu yang dibutuhkan untuk menyelesaikan satu kali aksi pada produk. Penggunaan multi-threading memastikan bahwa proses notifikasi berjalan di latar belakang secara paralel, sehingga tidak menghalangi fungsionalitas utama aplikasi dan menjaga pengalaman pengguna tetap responsif.
