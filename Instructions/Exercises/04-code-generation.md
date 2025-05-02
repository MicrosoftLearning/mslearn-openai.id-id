---
lab:
  title: Membuat dan meningkatkan kode dengan Azure OpenAI Service
  status: stale
---

# Membuat dan meningkatkan kode dengan Azure OpenAI Service

Model Azure OpenAI Service dapat menghasilkan kode untuk Anda menggunakan perintah bahasa alami, memperbaiki bug dalam kode lengkap, dan memberikan komentar kode. Model-model ini juga dapat menjelaskan dan menyederhanakan kode yang ada untuk membantu Anda memahami apa yang dilakukannya dan cara meningkatkannya.

Dalam skenario latihan ini, Anda akan berperan sebagai pengembang perangkat lunak yang mengeksplorasi cara menggunakan AI generatif untuk membuat tugas pengodean lebih mudah dan efisien. Teknik yang digunakan dalam latihan dapat diterapkan ke file kode lain, bahasa pemrograman, dan kasus penggunaan.

Latihan ini akan memakan waktu sekitar **25** menit.

## Provisi sumber daya Azure OpenAI

Jika Anda belum memilikinya, provisikan sumber daya Azure OpenAI di langganan Azure Anda.

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

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai nanti dalam latihan, ada kemungkinan Anda perlu membuat sumber daya lain di wilayah berbeda. Anda perlu membuat sumber daya lain di wilayah berbeda.

3. Tunggu hingga penerapan selesai. Kemudian buka sumber daya Azure OpenAI yang disebarkan di portal Microsoft Azure.

## Terapkan model

Azure menyediakan portal berbasis web bernama **portal Azure AI Foundry** yang dapat Anda gunakan untuk menerapkan, mengelola, dan menjelajahi berbagai model. Anda akan memulai penjelajahan Azure OpenAI dengan menggunakan portal Azure AI Foundry untuk menerapkan satu model.

> **Catatan**: Saat Anda menggunakan portal Azure AI Foundry , kotak pesan yang menyarankan tugas untuk Anda lakukan mungkin bisa ditampilkan. Anda dapat menutup ini dan mengikuti langkah-langkah dalam latihan ini.

1. Di portal Azure, pada halaman **Ikhtisar** untuk sumber daya Azure OpenAI Anda, gulir ke bawah ke bagian **Memulai** dan pilih tombol untuk masuk ke **portal AI Foundry** (sebelumnya AI Studio).
1. Di portal Azure AI Foundry, di panel sebelah kiri, pilih halaman **Penyebaran** dan lihat penyebaran model yang sudah ada. Jika Anda belum memilikinya, buat penyebaran baru model **gpt-4o** dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik pilihan Anda*
    - **Model**: gpt-4o
    - **Versi model**: *Gunakan versi default*
    - **Tipe penyebaran**: Standar
    - **Batas tarif token per menit**: 5K\*
    - **Filter konten**: Default
    - **Aktifkan kuota dinamis**: Dinonaktifkan

    > \*Batas tarif 5.000 token per menit sudah lebih dari cukup untuk menyelesaikan latihan ini dan masih menyisakan kapasitas untuk orang lain yang menggunakan langganan yang sama.

## Hasilkan kode di playground obrolan

Sebelum menggunakan di aplikasi Anda, periksa bagaimana Azure OpenAI dapat menghasilkan dan menjelaskan kode di taman bermain obrolan.

1. Di bagian **Playground**, pilih halaman **Obrolan**. Halaman playground **Obrolan** terdiri dari sederet tombol dan dua panel utama (yang dapat disusun dari kanan ke kiri secara horizontal, atau dari atas ke bawah secara vertikal bergantung pada resolusi layar Anda):
    - **Konfigurasi** - digunakan untuk memilih penyebaran Anda, menentukan pesan sistem, dan mengatur parameter untuk berinteraksi dengan penyebaran Anda.
    - **sesi Obrolan** - digunakan untuk mengirimkan pesan obrolan dan melihat respons.
1. Di bagian **Penyebaran**, pastikan bahwa penyebaran model Anda dipilih.
1. Di area **Pesan sistem**, atur pesan sistem ke `You are a programming assistant helping write code` dan terapkan perubahannya.
1. Dalam**Sesi obrolan**, masukkan kueri berikut:

    ```prompt
    Write a function in python that takes a character and a string as input, and returns how many times the character appears in the string
    ```

    Model kemungkinan akan merespons dengan fungsi, dengan beberapa penjelasan tentang apa yang dilakukan fungsi dan cara memanggilnya.

1. Selanjutnya, kirim perintah `Do the same thing, but this time write it in C#`.

    Model ini kemungkinan merespons dengan sangat mirip seperti pertama kalinya, tetapi kali ini pengodean di C#. Anda dapat memintanya lagi untuk bahasa pilihan Anda yang berbeda, atau fungsi untuk menyelesaikan tugas yang berbeda seperti mengembalikan string input.

1. Selanjutnya, mari kita jelajahi menggunakan AI untuk memahami kode. Kirim perintah berikut sebagai pesan pengguna.

    ```prompt
    What does the following function do?  
    ---  
    def multiply(a, b):  
        result = 0  
        negative = False  
        if a < 0 and b > 0:  
            a = -a  
            negative = True  
        elif a > 0 and b < 0:  
            b = -b  
            negative = True  
        elif a < 0 and b < 0:  
            a = -a  
            b = -b  
        while b > 0:  
            result += a  
            b -= 1      
        if negative:  
            return -result  
        else:  
            return result  
    ```

    Model harus menjelaskan apa yang dilakukan fungsi, yaitu mengalikan dua angka bersama-sama dengan menggunakan perulangan.

1. Masukkan perintah `Can you simplify the function?`.

    Model harus menulis versi fungsi yang lebih sederhana.

1. Masukkan perintah: `Add some comments to the function.`

    Model menambahkan komentar ke kode.

## Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Sekarang mari kita jelajahi bagaimana Anda dapat membuat aplikasi kustom yang menggunakan Azure OpenAI Service untuk menghasilkan kode. Anda akan mengembangkan aplikasi menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda telah membuat klon repositori **mslearn-openai**, buka klon tersebut di Visual Studio Code. Atau, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
2. Buka palet perintah (SHIFT+CTRL+P) atau **Lihat** > ** palet perintah...**) lalu jalankan perintah **Git: Clone** untuk mengkloning `https://github.com/MicrosoftLearning/mslearn-openai`repositori ke folder lokal (tidak masalah folder mana pun).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai pembuatnya** di pop-up.

4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan, beserta contoh file teks yang akan Anda gunakan untuk menguji peringkasan. Kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda akan menyelesaikan beberapa bagian utama aplikasi untuk mengaktifkan menggunakan sumber daya Azure OpenAI Anda.

1. Di Visual Studio Code, pada panel **Penjelajah**, telusuri folder **Labfiles/04-code-generation** dan luaskan folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi yang akan Anda integrasikan dengan fungsionalitas Azure OpenAI.
2. Klik kanan folder **CSharp** atau **Python** yang berisi file kode Anda dan buka terminal terintegrasi. Kemudian, instal paket Azure OpenAI SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**:

    ```powershell
    dotnet add package Azure.AI.OpenAI --version 2.1.0
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
    - **Nama penyebaran** yang Anda tentukan untuk penyebaran model Anda (tersedia di halaman**Penyebaran** di portal Azure AI Foundry).
5. Simpan file konfigurasi.

## Menambahkan kode untuk menggunakan model Azure OpenAI Service Anda

Sekarang Anda siap menggunakan Azure OpenAI SDK untuk menggunakan model yang Anda sebarkan.

1. Di panel **Penjelajah**, di folder **CSharp** atau **Python**, buka file kode untuk bahasa pilihan Anda. Dalam fungsi yang memanggil model Azure OpenAI, di bagian komentar ***Format dan kirim permintaan ke model***, tambahkan kode untuk memformat dan mengirimkan permintaan ke model.

    **C#**: Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };
    
    // Get response from Azure OpenAI
    ChatCompletion response = await chatClient.CompleteChatAsync(
        [
            new SystemChatMessage(systemPrompt),
            new UserChatMessage(userPrompt),
        ],
        chatCompletionsOptions);
    ```

    **Python**: code-generation.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    # Call the Azure OpenAI model
    response = client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=1000
    )
    ```

1. Simpan perubahan terhadap file kode.

## Jalankan aplikasi

Setelah aplikasi Anda dikonfigurasi, jalankan untuk mencoba membuat kode untuk setiap kasus penggunaan. Kasus penggunaan diberi nomor dalam aplikasi, dan dapat dijalankan dalam urutan apa pun.

> **Catatan**: Beberapa pengguna mungkin mengalami pembatasan laju jika memanggil model terlalu sering. Jika Anda mengalami kesalahan tentang batas laju token, tunggu sebentar lalu coba lagi.

1. Di panel **Penjelajah**, perluas folder **Labfiles/04-code-generasi/sample-code** dan tinjau fungsi dan aplikasi *go-fish* untuk bahasa Anda. File-file ini akan digunakan untuk tugas di aplikasi.
2. Di panel terminal interaktif, pastikan konteks folder adalah folder untuk bahasa pilihan Anda. Lalu masukkan perintah berikut untuk menjalankan aplikasi.

    - **C#:** `dotnet run`
    - **Python**: `python code-generation.py`

    > **Tips**: Anda dapat menggunakan ikon **Maksimalkan ukuran panel** (**^**) di bar alat terminal untuk melihat teks konsol lainnya.

3. Pilih opsi **1** untuk menambahkan komentar ke kode Anda dan masukkan perintah berikut. Perhatikan, respons mungkin memakan waktu beberapa detik untuk setiap tugas ini.

    ```prompt
    Add comments to the following function. Return only the commented code.\n---\n
    ```

    Hasilnya akan dimasukkan ke dalam hasil **/app.txt**. Buka file tersebut, dan bandingkan dengan file fungsi di **sample-code**.

4. Selanjutnya, pilih opsi **2** untuk menulis pengujian unit untuk fungsi yang sama dan masukkan perintah berikut.

    ```prompt
    Write four unit tests for the following function.\n---\n
    ```

    Hasilnya akan menggantikan apa yang ada di **result/app.txt**, dan merinci empat pengujian unit untuk fungsi tersebut.

5. Selanjutnya, pilih opsi **3** untuk memperbaiki bug di aplikasi untuk memainkan Go Fish. Masukkan perintah berikut.

    ```prompt
    Fix the code below for an app to play Go Fish with the user. Return only the corrected code.\n---\n
    ```

    Hasilnya akan menggantikan apa yang ada di **result/app.txt**, dan harus memiliki kode yang sangat mirip dengan beberapa hal yang diperbaiki.

    - **C#**: Perbaikan dibuat pada baris 30 dan 59
    - **Python**: Perbaikan dibuat pada baris 18 dan 31

    Aplikasi untuk Go Fish di **sample-code** dapat dijalankan jika Anda mengganti baris yang berisi bug dengan respons dari Azure OpenAI. Jika Anda menjalankannya tanpa perbaikan, itu tidak akan berfungsi dengan benar.

    > **Catatan**: Penting untuk dicatat bahwa meskipun kode untuk aplikasi Go Fish ini diperbaiki untuk beberapa sintaks, itu bukan representasi yang benar-benar akurat dari game. Jika Anda melihat lebih dekat, ada masalah dengan tidak memeriksa apakah dek kosong saat menggambar kartu, tidak menghapus pasangan dari tangan pemain ketika mereka mendapatkan pasangan, dan beberapa bug lain yang memerlukan pemahaman tentang permainan kartu untuk diwujudkan. Ini adalah contoh yang bagus tentang bagaimana model AI generatif yang berguna dapat membantu pembuatan kode, tetapi tidak dapat dipercaya sebagai benar dan perlu diverifikasi oleh pengembang.

    Jika ingin melihat respons lengkap dari Azure OpenAI, Anda dapat mengatur variabel **printFullResponse** ke `True`, lalu menjalankan ulang aplikasi.

## Penghapusan

Setelah Anda selesai dengan sumber daya Azure OpenAI, ingatlah untuk menghapus penyebaran atau seluruh sumber daya pada **portal Microsoft Azure** di `https://portal.azure.com`.
