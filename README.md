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

Dalam buku *Head First Design Patterns*, Subscriber didefinisikan sebagai sebuah interface dalam diagram *Observer pattern*. Namun, apakah dalam konteks BambangShop kita masih memerlukan interface atau trait dalam Rust, atau cukup dengan satu Model struct saja?  

Menurut pemahaman saya, dalam kasus BambangShop saat ini, cukup menggunakan satu Model struct untuk *Subscriber* tanpa perlu mendefinisikan sebuah trait. Hal ini karena sistem saat ini hanya memiliki satu entitas yang bisa di-*subscribe*, yaitu *Product*, yang memiliki atribut *product_type* sebagai pembeda. Namun, jika ke depannya BambangShop menambahkan model lain yang juga dapat di-*subscribe* oleh *Subscriber*, maka akan lebih baik jika kita menggunakan sebuah interface atau trait untuk meningkatkan fleksibilitas dan skalabilitas sistem.  

#### **Unik pada `id` dalam Product dan `url` dalam Subscriber: Apakah `Vec` Cukup atau `DashMap` Diperlukan?**  

Baik `id` dalam *Product* maupun `url` dalam *Subscriber* bersifat unik, sehingga muncul pertanyaan apakah kita bisa menggunakan `Vec` sebagai struktur data atau tetap memerlukan `DashMap`. Dalam kasus ini, penggunaan `DashMap` tetap lebih optimal.  

Alasannya adalah karena *Subscriber* disimpan berdasarkan *product_type* dari *Product*. Jika kita menggunakan `Vec`, kita perlu menambahkan logika pemetaan antara indeks `Vec` dan *product_type*, yang pada akhirnya hanya akan meningkatkan kompleksitas kode. Selain itu, dalam hal penghapusan *Subscriber*, `DashMap` lebih efisien karena mendukung akses data dalam waktu konstan (*O(1)*), sementara `Vec` memerlukan pencarian linear (*O(n)*) untuk menemukan elemen yang akan dihapus. Oleh karena itu, `DashMap` tetap menjadi pilihan yang lebih baik untuk kasus ini.  

#### **Apakah `DashMap` Masih Diperlukan atau Bisa Digantikan dengan Singleton?**  

Rust memiliki aturan yang sangat ketat dalam memastikan program yang dibuat *thread-safe*, termasuk dalam kasus penyimpanan daftar *Subscriber* dalam variabel statis `SUBSCRIBERS`. Dalam implementasi saat ini, digunakan `DashMap` sebagai *thread-safe HashMap*. Namun, apakah kita bisa menggantinya dengan pola *Singleton*?  

Secara teori, kita bisa menerapkan *Singleton pattern* untuk menyimpan daftar *Subscriber*, tetapi implementasinya di Rust cukup kompleks karena adanya batasan terkait variabel statis dan *mutability*. Rust mengharuskan penggunaan mekanisme *synchronization* seperti `Mutex` atau `RwLock` dalam *Singleton*, yang bisa menambah overhead dalam pengelolaan data. Dalam kasus ini, karena tujuan utama kita adalah menyimpan daftar *Subscriber* yang terorganisir berdasarkan *product_type*, penggunaan `DashMap` lebih sederhana dan lebih efisien dibandingkan harus mengimplementasikan *Singleton* dari nol.  

#### Reflection Publisher-2

#### **Mengapa Kita Perlu Memisahkan “Service” dan “Repository” dari Model?**  

Dalam pola *Model-View-Controller* (MVC), Model secara tradisional menangani baik penyimpanan data maupun logika bisnis. Namun, untuk menjaga prinsip *Single Responsibility*, kita perlu memisahkan *Service* dan *Repository* dari Model.  

Dengan melakukan pemisahan ini, Model hanya bertanggung jawab untuk merepresentasikan data, sedangkan penyimpanan data menjadi tanggung jawab *Repository*, dan logika bisnis ditangani oleh *Service*. Hal ini membantu menjaga modularitas, mempermudah pemeliharaan kode, serta meningkatkan keterbacaan dan skalabilitas sistem.  

#### **Apa yang Terjadi Jika Kita Hanya Menggunakan Model?**  

Jika kita tidak memisahkan *Service* dan *Repository*, Model akan menjadi sangat kompleks karena harus menangani berbagai tanggung jawab secara bersamaan. Sebagai contoh, dalam sistem yang melibatkan *Product*, *Subscriber*, dan *Notification*, setiap Model harus menangani berbagai interaksi, seperti:  

- *Subscriber* dapat *subscribe* dan *unsubscribe* terhadap produk tertentu.  
- *Notification* harus dikirim setiap kali ada perubahan pada produk yang di-*subscribe*.  
- Semua interaksi ini harus dikelola dalam satu Model, yang membuat kode menjadi sulit untuk dipahami dan dikelola.  

Dengan memisahkan *Service* dan *Repository*, kita dapat mengurangi kompleksitas ini dengan mendistribusikan tanggung jawab ke dalam komponen yang lebih spesifik dan terorganisir.  

#### **Bagaimana Postman Membantu dalam Pengujian?**  

Saya belum terlalu mendalami fitur-fitur Postman secara menyeluruh, tetapi sejauh ini, Postman sangat membantu dalam pengujian API yang saya buat. Alat ini memungkinkan saya untuk menguji berbagai jenis HTTP request, bukan hanya `GET` dan `POST`, tetapi juga `PUT`, `DELETE`, dan lainnya, sehingga saya bisa memastikan endpoint hanya menerima tipe request yang sesuai.  

Selain itu, fitur *Headers* dan *Authorization* dalam Postman sangat berguna, terutama untuk proyek yang memerlukan autentikasi atau penggunaan *custom headers*. Dengan Postman, saya bisa menguji API dengan cepat dan efisien tanpa harus menulis skrip tambahan atau mengandalkan browser. Secara keseluruhan, Postman adalah alat yang sangat praktis untuk pengujian API dalam proyek pengembangan perangkat lunak.  

#### Reflection Publisher-3
