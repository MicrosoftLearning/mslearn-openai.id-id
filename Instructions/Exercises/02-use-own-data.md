---
lab:
  title: Menerapkan Retrieval Augmented Generation (RAG) dengan Azure OpenAI Service
  status: new
---

# Menerapkan Retrieval Augmented Generation (RAG) dengan Azure OpenAI Service

Azure OpenAI Service memungkinkan Anda menggunakan data Anda sendiri dengan kecerdasan LLM yang mendasarinya. Anda dapat membatasi model untuk hanya menggunakan data Anda untuk topik yang berkaitan, atau memadukannya dengan hasil dari model yang telah dilatih sebelumnya.

Dalam skenario untuk latihan ini, Anda akan melakukan peran pengembang perangkat lunak yang bekerja untuk Biro Perjalanan Margie. Anda akan menjelajahi cara menggunakan Pencarian Azure AI untuk mengindeks data Anda sendiri dan menggunakannya dengan Azure OpenAI untuk menambah permintaan.

Latihan ini akan memakan waktu sekitar **30** menit.

## Penyediaan sumber daya Azure

Untuk menyelesaikan latihan ini, Anda perlu:

- Sumber daya Azure OpenAI.
- Sumber daya Pencarian Azure AI
- Sumber daya Akun Azure Storage.

1. Masuk ke **portal Microsoft Azure** di `https://portal.azure.com`.
2. Buat sumber daya **Azure OpenAI** dengan pengaturan berikut:
    - **Subscription**: *Pilih langganan Azure yang telah disetujui untuk akses ke Azure OpenAI Service*
    - **Grup sumber daya**: *Memilih atau membuat grup sumber daya*
    - **Wilayah**: *Buat **pilihan acak** dari salah satu wilayah berikut*\*
        - US Timur
        - US Timur 2
        - US Tengah Utara
        - US Tengah Selatan
        - Swedia Tengah
        - US Barat
        - US Barat 3
    - **Nama**: *Nama unik pilihan Anda*
    - **Tingkat harga**: Standar S0

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

3. Saat sumber daya Azure OpenAI sedang disediakan, buat sumber daya**Pencarian Azure AI** dengan pengaturan berikut:
    - **Langganan**: *Langganan tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Grup sumber daya**: *Grup sumber daya tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Nama layanan**: *Nama unik pilihan Anda*
    - **Lokasi**: *Wilayah tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Tingkat harga**: Dasar
4. Saat sumber daya Azure AI Search sedang disediakan, buat sumber daya **akun Penyimpanan** dengan pengaturan berikut:
    - **Langganan**: *Langganan tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Grup sumber daya**: *Grup sumber daya tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Nama akun penyimpanan**: *Nama yang unik sesuai pilihan Anda*
    - **Wilayah**: *Wilayah tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Layanan utama**: Azure Blob Storage atau Azure Data Lake Storage Gen 2
    - **Performa**: Standar
    - **Redundansi**: Penyimpanan redundan secara lokal (LRS)
5. Setelah ketiga sumber daya berhasil disebarkan di langganan Azure Anda, tinjau di portal Azure dan kumpulkan informasi berikut (yang akan Anda butuhkan nanti dalam latihan):
    - **Titik akhir** dan **kunci** dari sumber daya Azure OpenAI yang Anda buat (tersedia di halaman **Kunci dan Titik Akhir** untuk sumber daya Azure OpenAI Anda di portal Microsoft Azure)
    - Titik akhir untuk layanan Pencarian Azure AI Anda (nilai **Url** pada halaman gambaran umum untuk sumber daya pencarian Anda di portal Microsoft Azure).
    - Sebuah **kunci admin utama ** untuk sumber daya Azure AI Search Anda (tersedia di halaman **Kunci** untuk sumber daya Azure AI Search Anda di portal Azure).

## Unggah data Anda

Anda akan membumikan permintaan yang Anda gunakan dengan model AI generatif dengan menggunakan data Anda sendiri. Dalam latihan ini, data terdiri dari kumpulan brosur perjalanan dari perusahaan fiksi *Margies Travel*.

1. Di tab browser baru, unduh arsip data brosur dari `https://aka.ms/own-data-brochures`. Ekstrak brosur ke folder di PC Anda.
1. Di portal Azure, navigasikan ke akun penyimpanan Anda dan lihat halaman **browser.Penyimpanan**.
1. Pilih **Kontainer blob** lalu tambahkan kontainer baru bernama `margies-travel`.
1. Pilih kontainer **margies-travel**, lalu unggah brosur pdf yang Anda ekstrak sebelumnya ke folder akar kontainer blob.

## Sebarkan model AI

Anda akan menggunakan dua model AI dalam latihan ini:

- Model penyematan teks untuk *memvektorisasi* teks dalam brosur sehingga dapat diindeks secara efisien untuk digunakan dalam perintah grounding.
- Model GPT yang dapat digunakan aplikasi Anda untuk menghasilkan respons terhadap perintah yang di-grounded dalam data Anda.

## Terapkan model

Selanjutnya, Anda akan menyebarkan model Azure OpenAI dari Cloud Shell.

1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***Bash***. Cloud shell menyediakan antarmuka baris perintah dalam panel di bagian bawah portal Azure.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *PowerShell* , alihkan ke ***Bash***.

```dotnetcli
az cognitiveservices account deployment create \
   -g <your_resource_group> \
   -n <your_OpenAI_resource> \
   --deployment-name text-embedding-ada-002 \
   --model-name text-embedding-ada-002 \
   --model-version "2"  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

> **Catatan**: Kapasitas Sku diukur dalam ribuan token per menit. Batas tarif 5.000 token per menit sudah lebih dari cukup untuk menyelesaikan latihan ini dan masih menyisakan kapasitas untuk orang lain yang menggunakan langganan yang sama.

Setelah model penyematan teks diterapkan, buat penerapan baru model **gpt-4o** dengan pengaturan berikut:

```dotnetcli
az cognitiveservices account deployment create \
   -g <your_resource_group> \
   -n <your_OpenAI_resource> \
   --deployment-name gpt-4o \
   --model-name gpt-4o \
   --model-version "2024-05-13" \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

## Buat indeks

Untuk memudahkan penggunaan data Anda sendiri dalam perintah, Anda akan mengindeksnya menggunakan Pencarian Azure AI. Anda akan menggunakan model penyematan teks untuk *memvektorisasi* data teks (yang menghasilkan setiap token teks dalam indeks diwakili oleh vektor numerik - membuatnya kompatibel dengan cara model AI generatif mewakili teks)

1. Di portal Azure, navigasikan ke sumber daya Pencarian Azure AI Anda.
1. Pada halaman **Gambaran Umum**, pilih **Impor dan vektorisasi data**.
1. Di **halaman Atur koneksi data Anda**, pilih **Azure Blob Storage** dan konfigurasikan sumber data dengan pengaturan berikut:
    - **Langganan**: Langganan Azure tempat Anda memprovisikan akun penyimpanan Anda.
    - **Akun penyimpanan blob**: Pilih akun penyimpanan yang Anda buat sebelumnya.
    - **Kontainer blob**: margies-travel
    - **Folder blob**: *Biarkan kosong*
    - **Aktifkan pelacakan penghapusan**: Tidak dipilih
    - **Autentikasi menggunakan identitas terkelola**: Tidak dipilih
1. Pada halaman **Vektorisasi teks Anda**, pilih pengaturan berikut:
    - **Jenis**: Azure OpenAI
    - **Langganan**: Langganan Azure tempat Anda menyediakan layanan Azure OpenAI Anda.
    - **Layanan Azure OpenAI**: Sumber daya Layanan Azure OpenAI Anda
    - **Penyebaran model**: text-embedding-ada-002
    - **Jenis autentikasi**: Kunci API
    - **Saya mengakui bahwa menyambungkan ke layanan Azure OpenAI akan dikenakan biaya tambahan ke akun saya**: Dipilih
1. Pada halaman berikutnya, **jangan** pilih opsi untuk memvektorisasi gambar atau mengekstrak data dengan keterampilan AI.
1. Pada halaman berikutnya, aktifkan peringkat semantik dan jadwalkan pengindeks untuk dijalankan sekali.
1. Pada halaman akhir, atur **Awalan nama objek** ke `margies-index` dan lalu buat indeks.

## Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Sekarang mari kita jelajahi penggunaan data Anda sendiri di aplikasi yang menggunakan SDK layanan Azure OpenAI. Anda akan mengembangkan aplikasi menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda telah membuat klon repositori **mslearn-openai**, buka klon tersebut di Visual Studio Code. Atau, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) atau **Lihat** > **Palet Perintah...**) dan jalankan perintah **Git: Clone** untuk mengkloning `https://github.com/MicrosoftLearning/mslearn-openai`repositori ke folder lokal (folder mana pun tidak masalah).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai pembuatnya** di pop-up.

4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan, dan kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda akan menyelesaikan beberapa bagian utama aplikasi untuk mengaktifkan menggunakan sumber daya Azure OpenAI Anda.

1. Di Visual Studio Code, di panel **Penjelajah**, telusuri folder **Labfiles/02-use-own-data** dan perluas folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi tempat Anda akan mengintegrasikan fungsionalitas Azure OpenAI.
2. Klik kanan folder **CSharp** atau **Python** yang berisi file kode Anda dan buka terminal terintegrasi. Kemudian, instal paket Azure OpenAI SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**:

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
    dotnet add package Azure.Search.Documents --version 11.6.0
    ```

    **Python**:

    ```powershell
    pip install openai==1.65.2
    ```

3. Pada panel **Explorer**, di folder **CSharp** atau **Python**, buka file konfigurasi untuk bahasa antarmuka pilihan pengguna

    - **C#**: appsettings.json
    - **Python**: .env

4. Perbarui nilai konfigurasi untuk menyertakan:
    - **Titik akhir** dan **kunci** dari sumber daya Azure OpenAI yang Anda buat (tersedia di halaman **Kunci dan Titik Akhir** untuk sumber daya Azure OpenAI Anda di portal Microsoft Azure)
    - **Nama penyebaran** yang Anda tentukan untuk penerapan model gpt-4o Anda (harus `gpt-4o`.
    - Titik akhir untuk layanan pencarian Anda (nilai **Url** pada halaman gambaran umum untuk sumber daya pencarian Anda di portal Microsoft Azure).
    - **Kunci** untuk sumber daya pencarian Anda (tersedia di halaman **Kunci** untuk sumber daya pencarian Anda di portal Microsoft Azure - Anda dapat menggunakan salah satu kunci admin)
    - Nama indeks pencarian (yang harus `margies-index`).
5. Simpan file konfigurasi.

### Menambahkan kode untuk menggunakan Azure OpenAI Service

Sekarang Anda siap menggunakan Azure OpenAI SDK untuk menggunakan model yang Anda sebarkan.

1. Di panel **Penjelajah**, di folder **CSharp** atau folder **Python**, buka file kode untuk bahasa pilihan Anda, dan ganti komentar ***Konfigurasikan sumber data Anda*** dengan kode ke indeks Anda sebagai sumber data untuk penyelesaian obrolan:

    **C#**: ownData.cs

    ```csharp
    // Configure your data source
    // Extension methods to use data sources with options are subject to SDK surface changes. Suppress the warning to acknowledge this and use the subject-to-change AddDataSource method.
    #pragma warning disable AOAI001
    
    ChatCompletionOptions chatCompletionsOptions = new ChatCompletionOptions()
    {
        MaxOutputTokenCount = 600,
        Temperature = 0.9f,
    };
    
    chatCompletionsOptions.AddDataSource(new AzureSearchChatDataSource()
    {
        Endpoint = new Uri(azureSearchEndpoint),
        IndexName = azureSearchIndex,
        Authentication = DataSourceAuthentication.FromApiKey(azureSearchKey),
    });
    ```

    **Python**: ownData.py

    ```python
    # Configure your data source
    text = input('\nEnter a question:\n')
    
    completion = client.chat.completions.create(
        model=deployment,
        messages=[
            {
                "role": "user",
                "content": text,
            },
        ],
        extra_body={
            "data_sources":[
                {
                    "type": "azure_search",
                    "parameters": {
                        "endpoint": os.environ["AZURE_SEARCH_ENDPOINT"],
                        "index_name": os.environ["AZURE_SEARCH_INDEX"],
                        "authentication": {
                            "type": "api_key",
                            "key": os.environ["AZURE_SEARCH_KEY"],
                        }
                    }
                }
            ],
        }
    )
    ```

1. Simpan perubahan terhadap file kode.

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
