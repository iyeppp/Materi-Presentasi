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
**Daftar Lengkap File yang Menerapkan:**
- **Seluruh Kelas Entitas Database (Folder `model/`):**
  - `auth/model/UserProfile.java`
  - `calculator/model/CalcHistory.java`
  - `calories/model/CalorieLog.java`
  - `forum/model/ForumThread.java`
  - `forum/model/ForumReply.java`
  - `nutrition/model/Nutrition.java`
- **Seluruh Kelas Objek Transfer Data (Folder `dto/`):**
  - `auth/dto/UserProfileResponse.java`
  - `auth/dto/ApiResponse.java`
  - `calculator/dto/CalcHistoryRequest.java`, `CalcHistoryResponse.java`
  - `calories/dto/CalorieRequest.java`, `CalorieResponse.java`
  - `forum/dto/ForumThreadRequest.java`, `ForumThreadResponse.java`
  - `forum/dto/ForumReplyRequest.java`, `ForumReplyResponse.java`
- **Penjelasan Teknis:** Pada file-file Model dan DTO di atas, semua atribut (seperti `title`, `calories`, `userId`) dienkapsulasi menggunakan access modifier `private`. Kelas di luar tidak bisa mengubah nilainya sembarangan. Akses harus melewati pintu resmi yaitu metode *Getter* dan *Setter* (yang di-generate menggunakan `@Data` dari Lombok).

---

## Slide 6: Komponen OOP dalam Sistem - Abstraction (Abstraksi)
**Definisi:** Menyembunyikan kompleksitas implementasi dan hanya menampilkan fitur-fitur esensial.
**Daftar Lengkap File yang Menerapkan:**
- **Seluruh Kelas Antarmuka Akses Data (Folder `repository/`):**
  - `auth/repository/UserProfileRepository.java`
  - `calculator/repository/CalcHistoryRepository.java`
  - `calories/repository/CalorieLogRepository.java`
  - `forum/repository/ForumThreadRepository.java`
  - `forum/repository/ForumReplyRepository.java`
  - `nutrition/repository/NutritionRepository.java`
- **Penjelasan Teknis:** File-file repository di atas berbentuk `interface`. Mereka mengabstraksikan dan menyembunyikan kerumitan penulisan query SQL (seperti *INSERT*, *SELECT*) di balik method abstrak sederhana bawaan dari Spring Data JPA, sehingga Service tidak perlu tahu detail implementasi *database*-nya.

---

## Slide 7: Komponen OOP dalam Sistem - Polymorphism (Polimorfisme)
**Definisi:** Kemampuan suatu objek atau metode untuk mengambil banyak bentuk (Pattern Interface-Implementation).
**Daftar Lengkap File yang Menerapkan:**
- **Seluruh Kelas Layanan Bisnis (Folder `service/`):**
  - `auth/service/AuthService.java` **(Interface)** ➡️ `AuthServiceImpl.java` **(Implementasi)**
  - `calculator/service/CalculatorService.java` ➡️ `CalculatorServiceImpl.java`
  - `calories/service/CalorieService.java` ➡️ `CalorieServiceImpl.java`
  - `forum/service/ForumThreadService.java` ➡️ `ForumThreadServiceImpl.java`
  - `nutrition/service/NutritionService.java` ➡️ `NutritionServiceImpl.java`
- **Penjelasan Teknis:** Ini adalah wujud polimorfisme di mana Controller di layer atas cukup memanggil *Interface* (contoh: `CalorieService`), tanpa mempedulikan bentuk konkrit implementasinya. Spring Boot akan secara dinamis menyuntikkan (inject) wujud nyatanya yaitu kelas `CalorieServiceImpl` saat program berjalan (*runtime*).

---

## Slide 8: Komponen OOP dalam Sistem - Inheritance (Pewarisan)
**Definisi:** Kelas turunan mewarisi atribut dan metode dari kelas induk.
**Daftar Lengkap File yang Menerapkan:**
- **Pewarisan pada Layer Repository (`extends JpaRepository`):**
  - `calories/repository/CalorieLogRepository.java` 
  - `forum/repository/ForumThreadRepository.java` 
  - Serta file repository lainnya.
- **Pewarisan pada Penanganan Error Global:**
  - Kelas exception khusus dalam sistem seperti mewarisi sifat dari `RuntimeException`.
- **Penjelasan Teknis:** Pewarisan (*Inheritance*) diterapkan luas di kelas Repository. Sebagai contoh `CalorieLogRepository` di-deklarasikan dengan `extends JpaRepository<CalorieLog, UUID>`. Melalui pewarisan ini, repository turunan tersebut secara otomatis mewarisi puluhan metode bawaan (seperti `findAll()`, `save()`, `deleteById()`) dari induknya tanpa harus ditulis satu per satu.

---

## Tips Presentasi 💡
- **Di menit awal:** Tampilkan antarmuka unik *Retro Desktop* Anda untuk menarik perhatian audiens, sebutkan teknologi Frontend.
- **Di pertengahan:** Tunjukkan struktur diagram layer MVC Backend (Controller -> Service -> Repo). Jelaskan alur DTO.
- **Di bagian akhir:** Hubungkan bagaimana teori *Object Oriented Programming* (Encapsulation, Polymorphism) benar-benar Anda aplikasikan secara nyata dan profesional di proyek ini (tunjukkan sedikit cuplikan kode model dengan *getter-setter*, atau kelas `*ServiceImpl`).
