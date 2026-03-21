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

Dalam tutorial BambangShop ini, kita menggunakan Push Model dari Observer Pattern.

Hal ini terlihat dari cara kerja sistem:

Publisher (Program / NotificationService) langsung mengirim payload lengkap (Notification) ke Subscriber
Subscriber tidak perlu meminta data tambahan lagi

Artinya, semua informasi seperti:

-product_title
-product_type
-product_url
-status

jadi sudah “di-push” langsung ke subscriber melalui HTTP POST.

2.

Jika menggunakan Pull Model, maka alurnya akan berbeda:

Publisher hanya mengirim notifikasi sederhana (misalnya ID produk atau sinyal perubahan)
Subscriber harus melakukan request ulang ke server untuk mengambil data lengkap

-Kelebihan Pull Model:
Data selalu up-to-date karena diambil langsung saat dibutuhkan
Payload dari publisher lebih kecil (lebih ringan)
Lebih fleksibel jika data sering berubah

-Kekurangan Pull Model:
Membutuhkan request tambahan dari subscriber (lebih kompleks)
Menambah beban server karena banyak request masuk
Latency lebih tinggi (lebih lambat)
Implementasi lebih ribet dibanding push

Kesimpulan:

Untuk kasus BambangShop, Push Model lebih cocok karena:

-Sistem notifikasi sederhana
-Data tidak terlalu besar
-Tidak butuh real-time update ulang dari subscriber

3.

Jika kita tidak menggunakan multi-threading dalam proses notifikasi:

 Yang akan terjadi:
Notifikasi dikirim secara sequential (satu per satu)
Jika satu subscriber lambat / down → seluruh proses ikut terhambat
Waktu respon API menjadi lebih lama

Contoh:

Misalnya ada 100 subscriber:

-Tanpa thread → kirim 1 per 1 → lama banget
-Dengan thread → kirim paralel → jauh lebih cepat

-Dampak negatif tanpa multi-threading:
Performa menurun drastis
Scalability buruk
User experience jelek (delay tinggi)

Keuntungan multi-threading:
-Pengiriman notifikasi berjalan paralel
-Tidak saling blocking
-Lebih cepat dan efisien
