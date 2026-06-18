# Materi Presentasi Laporan Akhir: Piring Sehat

*(Dokumen ini disusun per *slide* untuk memudahkan pemindahan materi ke dalam file PowerPoint/PPT)*

---

## Slide 1: Judul & Pengantar
**Piring Sehat: Solusi Pencatatan Kalori & Kesehatan Berbasis Web**
- **Deskripsi:** Aplikasi berbasis web bergaya *Retro Desktop* untuk mencatat asupan kalori, menghitung kebutuhan gizi (BMI, Protein, Prediksi Genetik), dan berdiskusi di forum komunitas.
- **Tujuan Sistem:** Membantu pengguna memantau kesehatan melalui pencatatan yang mudah dan antar pengguna dapat saling berinteraksi.

---

## Slide 2: Struktur Aplikasi (Architecture Structure)
**Pendekatan Sistem:** *Client-Server Architecture* dengan pemisahan Frontend dan Backend secara tegas.

**1. Frontend (Antarmuka Klien)**
- Dibangun menggunakan **React.js (Vite)** dan **Tailwind CSS**.
- Mengusung UI bertema **Retro Desktop OS** (seperti Windows 95) dengan *Window Manager* mandiri.
- Mengelola state dan *request API* melalui antarmuka yang dinamis.

**2. Backend (REST API Server)**
- Dibangun dengan **Java Spring Boot**.
- Menerapkan arsitektur **Layered/N-Tier (MVC)**:
  - **Controller:** Menerima HTTP request dan membalas dengan JSON.
  - **Service:** Menjalankan logika bisnis (Business Logic).
  - **Repository:** Mengakses *Database* menggunakan Spring Data JPA.
  - **Model & DTO:** Memisahkan representasi data *database* (Model) dan komunikasi *network* (DTO).

**3. Database & Autentikasi**
- **PostgreSQL / Supabase:** Penyimpanan data terpusat dan manajemen JWT otentikasi.

---

## Slide 3: Alur Program (Data Flow) - Konsep Umum
Bagaimana aplikasi bekerja dari ujung ke ujung:

1. **Autentikasi (Gerbang Masuk):** Pengguna *login* melalui antarmuka Desktop. Aplikasi mengambil token **JWT** dari Supabase.
2. **Interaksi Pengguna (Trigger):** Pengguna memilih "Aplikasi" di desktop (contoh: *Calorie Tracker*) dan mengisi form, lalu menekan "Submit".
3. **Pengiriman Request (Frontend -> Backend):** Frontend mengemas data menjadi objek JSON, menempelkan token otorisasi (JWT), lalu mengirimkannya ke Server Spring Boot.
4. **Pemrosesan (Backend):** Request diproses oleh *Controller*, dikalkulasi di *Service*, lalu disimpan ke database oleh *Repository*.
5. **Respons (Backend -> Frontend):** Server membalas dengan status sukses/gagal (JSON ApiResponse). Frontend me-render ulang UI dengan data terbaru (misal tabel progres bertambah).

---

## Slide 4: Alur Program - Detail Layer Backend
(Siklus hidup data di dalam Server/Backend)

- **Masuk (Input):**
  `JSON (Klien)` ➡️ **Controller** ➡️ *Mapping ke DTO (Data Transfer Object)* ➡️ **Service** (Logika Bisnis & Validasi) ➡️ *Mapping ke Model/Entitas* ➡️ **Repository** ➡️ `Database`
- **Keluar (Output):**
  `Database` ➡️ **Repository** ➡️ *Model/Entitas* ➡️ **Service** ➡️ *Mapping ke DTO Respons* ➡️ **Controller** ➡️ `JSON (Kembali ke Klien)`

---

## Slide 5: Komponen OOP dalam Sistem - Encapsulation (Pengkapsulan)
**Definisi:** Menyembunyikan detail data dan hanya mengizinkan akses melalui metode tertentu.
**Penerapan dalam Sistem:**
- **Penggunaan Model & Entitas:** Kelas seperti `CalorieLog` dan `ForumThread` memiliki atribut *private* (`private UUID id`, `private String title`). Atribut ini hanya bisa dibaca/diubah melalui metode *getter* dan *setter*.
- **Penggunaan DTO (Data Transfer Object):** Struktur database internal dienkapsulasi dan tidak dibocorkan ke *client*. Data difilter dan dikemas ke dalam kelas `CalorieRequest` atau `ForumThreadResponse` yang hanya berisi field yang boleh dikonsumsi publik.

---

## Slide 6: Komponen OOP dalam Sistem - Abstraction (Abstraksi)
**Definisi:** Menyembunyikan kompleksitas implementasi dan hanya menampilkan fitur-fitur esensial.
**Penerapan dalam Sistem:**
- **Interface di Repository:** Penggunaan Spring Data JPA di mana kita hanya mendefinisikan interface (contoh: `CalorieLogRepository extends JpaRepository`). Logika dasar query SQL seperti `INSERT`, `SELECT`, `UPDATE` diabstraksi total oleh framework, sehingga developer cukup menggunakan metode `.save()`, `.findAll()`, dll.
- **REST Controller:** Klien (Frontend) diabstraksi dari cara backend memproses data; klien hanya perlu memanggil *endpoint URL* yang disediakan.

---

## Slide 7: Komponen OOP dalam Sistem - Polymorphism (Polimorfisme)
**Definisi:** Kemampuan suatu objek atau metode untuk mengambil banyak bentuk.
**Penerapan dalam Sistem:**
- **Interface-Implementation Pattern di Layer Service:** Adanya interface `CalorieService` yang berisi kerangka/kontrak metode (seperti `addCalorieLog()`), lalu metode tersebut direalisasikan isinya secara spesifik pada kelas `CalorieServiceImpl`. 
- **Keuntungan:** Controller hanya berinteraksi dengan tipe (bentuk) antarmuka abstrak (`CalorieService`), tanpa peduli bentuk konkret/implementasi aslinya. Hal ini sangat memudahkan modifikasi dan *unit testing* di kemudian hari.

---

## Slide 8: Komponen OOP dalam Sistem - Inheritance (Pewarisan)
**Definisi:** Kelas turunan mewarisi atribut dan metode dari kelas induk.
**Penerapan dalam Sistem:**
- **Layer Repository:** Semua *Interface Repository* (contoh: `ForumThreadRepository`) mewarisi fitur-fitur bawaan dari induknya `JpaRepository<T, ID>`. Sehingga mereka mendapatkan puluhan metode standar pencarian data secara gratis tanpa perlu ditulis ulang.
- **Penanganan Exception Global:** Kelas-kelas *Error/Exception* kustom aplikasi (jika ada) mewarisi sifat dari `RuntimeException`.
- **Komponen Frontend (React):** Di frontend (OOP di sisi JS), arsitektur berbasis komponen di mana fungsionalitas UI yang lebih kompleks dapat menggunakan atau merangkai properti-properti turunan dari komponen UI dasar.

---

## Tips Presentasi 💡
- **Di menit awal:** Tampilkan antarmuka unik *Retro Desktop* Anda untuk menarik perhatian audiens, sebutkan teknologi Frontend.
- **Di pertengahan:** Tunjukkan struktur diagram layer MVC Backend (Controller -> Service -> Repo). Jelaskan alur DTO.
- **Di bagian akhir:** Hubungkan bagaimana teori *Object Oriented Programming* (Encapsulation, Polymorphism) benar-benar Anda aplikasikan secara nyata dan profesional di proyek ini (tunjukkan sedikit cuplikan kode model dengan *getter-setter*, atau kelas `*ServiceImpl`).
