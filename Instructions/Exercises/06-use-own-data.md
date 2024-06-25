---
lab:
  title: Menerapkan Retrieval Augmented Generation (RAG) dengan Azure OpenAI Service
---

# Menerapkan Retrieval Augmented Generation (RAG) dengan Azure OpenAI Service

Azure OpenAI Service memungkinkan Anda menggunakan data Anda sendiri dengan kecerdasan LLM yang mendasarinya. Anda dapat membatasi model untuk hanya menggunakan data Anda untuk topik yang berkaitan, atau memadukannya dengan hasil dari model yang telah dilatih sebelumnya.

Dalam skenario untuk latihan ini, Anda akan melakukan peran pengembang perangkat lunak yang bekerja untuk Biro Perjalanan Margie. Anda akan mempelajari cara menggunakan AI generatif untuk membuat tugas pengkodean lebih mudah dan efisien. Teknik yang digunakan dalam latihan dapat diterapkan ke file kode lain, bahasa pemrograman, dan kasus penggunaan.

Latihan ini akan memakan waktu sekitar **20** menit.

## Provisi sumber daya Azure OpenAI

Jika Anda belum memilikinya, provisikan sumber daya Azure OpenAI di langganan Azure Anda.

1. Masuk ke **portal Microsoft Azure** di `https://portal.azure.com`.
2. Buat sumber daya **Azure OpenAI** dengan pengaturan berikut:
    - **Subscription**: *Pilih langganan Azure yang telah disetujui untuk akses ke layanan Azure OpenAI*
    - **Grup sumber daya**: *Memilih atau membuat grup sumber daya*
    - **Wilayah**: *Buat **pilihan acak** dari salah satu wilayah berikut*\*
        - Australia Timur
        - Kanada Timur
        - AS Timur
        - AS Timur 2
        - Prancis Tengah
        - Jepang Timur
        - AS Tengah Bagian Utara
        - Swedia Tengah
        - Swiss Utara
        - UK Selatan
    - **Nama**: *Nama unik pilihan Anda*
    - **Tingkat harga**: Standar S0

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

3. Tunggu hingga penerapan selesai. Kemudian buka sumber daya Azure OpenAI yang disebarkan di portal Microsoft Azure.

## Terapkan model

Azure OpenAI menyediakan portal berbasis web bernama **Azure OpenAI Studio**, yang dapat Anda gunakan untuk menyebarkan, mengelola, dan menjelajahi model. Anda akan memulai eksplorasi Azure OpenAI dengan menggunakan Azure OpenAI Studio untuk menyebarkan model.

1. Pada halaman **Gambaran umum** untuk sumber daya Azure OpenAI Anda, gunakan tombol **Buka Azure OpenAI Studio** untuk membuka Azure OpenAI Studio di tab browser baru.
2. Di Azure OpenAI Studio, pada halaman **Penyebaran**, lihat penyebaran model yang sudah ada. Jika Anda belum memilikinya, buat penyebaran baru model **gpt-35-turbo-16k** dengan pengaturan berikut:
    - **Model**: gpt-35-turbo-16k* (harus model ini untuk menggunakan fitur dalam latihan ini)*
    - **Versi model**: Pembaruan otomatis ke default
    - **Nama penyebaran**: *Nama unik pilihan Anda. Anda akan menggunakan nama ini nanti di lab.*
    - **Opsi tingkat lanjut**
        - **Filter konten**: Default
        - **Tipe penyebaran**: Standar
        - **Batas tarif token per menit**: 5K\*
        - **Aktifkan kuota dinamis**: Diaktifkan

    > \*Batas tarif 5.000 token per menit sudah lebih dari cukup untuk menyelesaikan latihan ini dan masih menyisakan kapasitas untuk orang lain yang menggunakan langganan yang sama.

## Mengamati perilaku obrolan normal tanpa menambahkan data Anda sendiri

Sebelum menyambungkan Azure OpenAI ke data Anda, mari kita amati terlebih dahulu bagaimana model dasar merespons kueri tanpa data dasar apa pun.

1. Di **** Azure OpenAI Studio di `https://oai.azure.com`, di bagian **Playground**, pilih halaman **Obrolan**. Halaman **Obrolan** playground terdiri dari tiga bagian utama:
    - **Penyiapan** - digunakan untuk mengatur konteks respons model.
    - **sesi Obrolan** - digunakan untuk mengirimkan pesan obrolan dan melihat respons.
    - **Configuration** - digunakan untuk mengonfigurasi pengaturan untuk penyebaran model.
2. Di bagian **Konfigurasi**, pastikan bahwa penyebaran model Anda dipilih.
3. Di area**Penyiapan**, pilih templat pesan sistem default untuk mengatur konteks untuk sesi obrolan. Pesan sistem default adalah *Anda adalah asisten AI yang membantu orang menemukan informasi*.
4. Dalam**Sesi obrolan**, kirimkan kueri berikut, dan tinjau responsnya:

    ```prompt
    I'd like to take a trip to New York. Where should I stay?
    ```

    ```prompt
    What are some facts about New York?
    ```

    Cobalah pertanyaan serupa tentang pariwisata dan tempat menginap untuk lokasi lain yang akan disertakan dalam data grounding kami, seperti London, atau San Francisco. Anda mungkin akan mendapatkan respons lengkap tentang area atau lingkungan, dan beberapa fakta umum tentang kota.

## Menyambungkan data Anda di taman bermain obrolan

Sekarang Anda akan menambahkan beberapa data untuk perusahaan agen perjalanan fiktif bernama *Margie's Travel*. Kemudian Anda akan melihat bagaimana model Azure OpenAI merespons saat menggunakan brosur dari Margie's Travel sebagai data grounding.

1. Di tab browser baru, unduh arsip data brosur dari `https://aka.ms/own-data-brochures`. Ekstrak brosur ke folder di PC Anda.
1. Di Azure OpenAI Studio, di **Chat** playground, di bagian **Penyiapan**, pilih **Tambahkan data Anda**.
1. Pilih **Tambahkan** sumber data dan pilih **Unggah file**.
1. Anda harus membuat akun penyimpanan dan sumber daya Azure AI Search. Di bawah menu dropdown untuk sumber daya penyimpanan, pilih **Buat sumber daya penyimpanan Azure Blob baru**, dan buat akun penyimpanan dengan pengaturan berikut. Apa pun yang tidak ditentukan biarkan sebagai default.

    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih grup sumber daya yang sama dengan*sumber daya Azure OpenAI Anda
    - **Nama akun penyimpanan**: *Masukkan nama yang unik*
    - **Wilayah**: *Pilih wilayah yang sama dengan*sumber daya Azure OpenAI Anda
    - **Redundansi**: Penyimpanan redundan secara lokal (LRS)

1. Saat sumber daya akun penyimpanan sedang dibuat, kembali ke Azure OpenAI Studio dan pilih **Buat sumber daya Azure AI Search baru** dengan pengaturan berikut. Apa pun yang tidak ditentukan biarkan sebagai default.

    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih grup sumber daya yang sama dengan*sumber daya Azure OpenAI Anda
    - **Nama layanan**: *Masukkan nama yang unik*
    - **Lokasi**: *Pilih lokasi yang sama dengan*sumber daya Azure OpenAI Anda
    - **Tingkat harga**: Dasar

1. Tunggu hingga sumber daya pencarian Anda disebarkan, lalu beralih kembali ke Azure AI Studio.
1. Di **Tambahkan data**, masukkan nilai berikut untuk sumber data Anda, lalu pilih **Berikutnya**.

    - **Pilih sumber data**: Unggah file
    - **Subscription**: Langganan Azure Anda
    - **Pilih sumber daya penyimpanan Azure Blob**: *Gunakan tombol**Refresh** untuk mengisi ulang daftar, lalu pilih sumber daya penyimpanan yang Anda buat*
        - Aktifkan CORS saat diminta
    - **Pilih sumber daya Azure AI Search**: *Gunakan tombol **Refresh** untuk mengisi ulang daftar, lalu pilih sumber daya pencarian yang Anda buat*
    - **Masukkan nama indeks**: `margiestravel`
    - **Tambahkan pencarian vektor ke sumber daya pencarian ini**: tidak dicentang
    - **saya mengakui bahwa menyambungkan ke akun Azure AI Search akan menimbulkan penggunaan ke akun saya** : diperiksa

1. Pada halaman **Unggah file**, unggah PDF yang Anda unduh, lalu pilih **Berikutnya**.
1. Pada halaman **Manajemen data** pilih jenis pencarian **Kata Kunci** dari menu drop-down, lalu pilih **Berikutnya**.
1. Pada halaman **Tinjau dan selesaikan** pilih **Simpan dan tutup**, yang akan menambahkan data Anda. Ini mungkin memakan waktu beberapa menit, di mana Anda perlu membiarkan jendela Anda terbuka. Setelah selesai, Anda akan melihat sumber data, sumber daya pencarian, dan indeks yang ditentukan di bagian **Penyiapan**.

    > **Tips**: Terkadang koneksi antara indeks pencarian baru Anda dan Azure OpenAI Studio membutuhkan waktu terlalu lama. Jika Anda telah menunggu selama beberapa menit dan masih belum tersambung, periksa sumber daya Pencarian AI Anda di portal Microsoft Azure. Jika Anda melihat indeks yang telah selesai, Anda dapat memutuskan koneksi data di Azure OpenAI Studio dan menambahkannya kembali dengan menentukan sumber data Azure AI Search dan memilih indeks baru Anda.

## Mengobrol dengan model yang di-grounded dalam data Anda

Sekarang setelah Anda menambahkan data, ajukan pertanyaan yang sama seperti yang Anda lakukan sebelumnya, dan lihat perbedaan responsnya.

```prompt
I'd like to take a trip to New York. Where should I stay?
```

```prompt
What are some facts about New York?
```

Anda akan melihat respons yang sangat berbeda kali ini, dengan spesifikasi tentang hotel tertentu dan penyebutan Margie's Travel, serta referensi ke tempat informasi yang diberikan berasal. Jika Anda membuka referensi PDF yang tercantum dalam respons, Anda akan melihat hotel yang sama dengan model yang disediakan.

Coba tanyakan tentang kota-kota lain yang termasuk dalam data grounding, yaitu Dubai, Las Vegas, London, dan San Francisco.

> **Catatan**: **Menambahkan** data Anda masih dalam pratinjau dan mungkin tidak selalu bertingkah seperti yang diharapkan untuk fitur ini, seperti memberikan referensi yang salah untuk kota yang tidak disertakan dalam data grounding.

## Menyambungkan aplikasi Anda ke data Anda sendiri

Selanjutnya, mari kita jelajahi cara menghubungkan aplikasi Anda untuk menggunakan data Anda sendiri.

### Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Sekarang mari kita jelajahi penggunaan data Anda sendiri di aplikasi yang menggunakan SDK layanan Azure OpenAI. Anda akan mengembangkan aplikasi menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda telah mengkloning repositori **mslearn-openai**, buka di kode Visual Studio. Atau, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/mslearn-openai` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda untuk memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai penulisnya** pada pop-up tersebut.

4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan, dan kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda akan menyelesaikan beberapa bagian utama aplikasi untuk mengaktifkan menggunakan sumber daya Azure OpenAI Anda.

1. Di Visual Studio Code, di panel **Penjelajah**, telusuri folder **Labfiles/06-use-own-data** dan perluas folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi tempat Anda akan mengintegrasikan fungsionalitas Azure OpenAI.
2. Klik kanan folder **CSharp** atau **Python** yang berisi file kode Anda dan buka terminal terintegrasi. Kemudian instal paket Azure OpenAI SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**:

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**:

    ```
    pip install openai==1.13.3
    ```

3. Di panel **Penjelajah**, di folder **CSharp** atau **Python**, buka file konfigurasi untuk bahasa pilihan Anda

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Perbarui nilai konfigurasi untuk menyertakan:
    - **titik akhir** dan **kunci** dari sumber daya Azure OpenAI yang Anda buat (tersedia di halaman **Kunci dan Titik Akhir** untuk sumber daya Azure OpenAI Anda di portal Microsoft Azure)
    - **Nama penyebaran** Anda tentukan untuk penyebaran model Anda (tersedia di halaman**Penyebaran** di Azure OpenAI Studio).
    - Titik akhir untuk layanan pencarian Anda (nilai **Url** pada halaman gambaran umum untuk sumber daya pencarian Anda di portal Microsoft Azure).
    - **Kunci** untuk sumber daya pencarian Anda (tersedia di halaman **Kunci** untuk sumber daya pencarian Anda di portal Microsoft Azure - Anda dapat menggunakan salah satu kunci admin)
    - Nama indeks pencarian (yang harus `margiestravel`).
1. Simpan file konfigurasi.

### Menambahkan kode untuk menggunakan layanan Azure OpenAI

Sekarang Anda siap menggunakan Azure OpenAI SDK untuk menggunakan model yang Anda sebarkan.

1. Di panel **Penjelajah**, di folder **CSharp** atau **Python**, buka file kode untuk bahasa pilihan Anda, dan ganti komentar ***Konfigurasikan sumber data Anda*** dengan kode untuk menambahkan pustaka Azure OpenAI SDK:

    **C#**: ownData.cs

    ```csharp
    // Configure your data source
    AzureSearchChatExtensionConfiguration ownDataConfig = new()
    {
            SearchEndpoint = new Uri(azureSearchEndpoint),
            Authentication = new OnYourDataApiKeyAuthenticationOptions(azureSearchKey),
            IndexName = azureSearchIndex
    };
    ```

    **Python**: ownData.py

    ```python
    # Configure your data source
    extension_config = dict(dataSources = [  
            { 
                "type": "AzureCognitiveSearch", 
                "parameters": { 
                    "endpoint":azure_search_endpoint, 
                    "key": azure_search_key, 
                    "indexName": azure_search_index,
                }
            }]
        )
    ```

2. Tinjau kode lainnya, dengan mencatat penggunaan *ekstensi* dalam isi permintaan yang digunakan untuk memberikan informasi tentang pengaturan sumber data.

3. Simpan perubahan terhadap file kode.

## Jalankan aplikasi Anda

Sekarang setelah aplikasi Anda dikonfigurasi, jalankan untuk mengirim permintaan Anda ke model Anda dan amati responsnya. Anda akan melihat satu-satunya perbedaan antara opsi yang berbeda adalah konten perintah, semua parameter lain (seperti jumlah token dan suhu) tetap sama untuk setiap permintaan.

1. Di panel terminal interaktif, pastikan konteks folder adalah folder untuk bahasa pilihan Anda. Lalu masukkan perintah berikut untuk menjalankan aplikasi.

    - **C#:** `dotnet run`
    - **Python**: `python ownData.py`

    > **Tips**: Anda dapat menggunakan ikon **Maksimalkan ukuran panel** (**^**) di bar alat terminal untuk melihat teks konsol lainnya.

2. Tinjau respons terhadap perintah `Tell me about London`, yang harus menyertakan jawaban serta beberapa detail data yang digunakan untuk membumikan permintaan, yang diperoleh dari layanan pencarian Anda.

    > **Tips**: Jika Anda ingin melihat kutipan dari indeks pencarian Anda, atur variabel ***menampilkan kutipan*** di dekat bagian atas file kode untuk **true**.

## Penghapusan

Setelah selesai dengan sumber daya Azure OpenAI Anda, ingatlah untuk menghapus sumber daya di**portal Azure** di `https://portal.azure.com`. Pastikan juga untuk menyertakan akun penyimpanan dan sumber daya pencarian, karena dapat dikenakan biaya yang relatif besar.
