---
lab:
  title: Membuat dan meningkatkan kode dengan Azure OpenAI Service
---

# Membuat dan meningkatkan kode dengan Azure OpenAI Service

Model Azure OpenAI Service dapat menghasilkan kode untuk Anda menggunakan perintah bahasa alami, memperbaiki bug dalam kode lengkap, dan memberikan komentar kode. Model-model ini juga dapat menjelaskan dan menyederhanakan kode yang ada untuk membantu Anda memahami apa yang dilakukannya dan cara meningkatkannya.

Dalam skenario latihan ini, Anda akan berperan sebagai pengembang perangkat lunak yang mengeksplorasi cara menggunakan AI generatif untuk membuat tugas pengodean lebih mudah dan efisien. Teknik yang digunakan dalam latihan dapat diterapkan ke file kode lain, bahasa pemrograman, dan kasus penggunaan.

Latihan ini akan memakan waktu sekitar **25** menit.

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

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Wilayah yang tercantum mencakup kuota default untuk tipe model yang digunakan dalam latihan ini. Memilih wilayah secara acak akan mengurangi risiko satu wilayah mencapai batas kuota dalam skenario di mana Anda berbagi langganan dengan pengguna lain. Jika batas kuota tercapai nanti dalam latihan, ada kemungkinan Anda perlu membuat sumber daya lain di wilayah berbeda. Anda perlu membuat sumber daya lain di wilayah berbeda.

3. Tunggu hingga penerapan selesai. Kemudian buka sumber daya Azure OpenAI yang disebarkan di portal Microsoft Azure.

## Terapkan model

Azure OpenAI menyediakan portal berbasis web bernama **Azure OpenAI Studio**, yang dapat Anda gunakan untuk menyebarkan, mengelola, dan menjelajahi model. Anda akan memulai eksplorasi Azure OpenAI dengan menggunakan Azure OpenAI Studio untuk menyebarkan model.

1. Pada halaman **Gambaran umum** untuk sumber daya Azure OpenAI Anda, gunakan tombol **Buka Azure OpenAI Studio** untuk membuka Azure OpenAI Studio di tab browser baru.
2. Di Azure OpenAI Studio, pada halaman **Penyebaran**, lihat penyebaran model yang sudah ada. Jika Anda belum memilikinya, buat penyebaran baru model **gpt-35-turbo-16k** dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik pilihan Anda*
    - **Model**: gpt-35-turbo-16k *(jika model 16k tidak tersedia, pilih gpt-35-turbo)*
    - **Versi model**: Pembaruan otomatis ke default
    - **Tipe penyebaran**: Standar
    - **Batas tarif token per menit**: 5K\*
    - **Filter konten**: Default
    - **Aktifkan kuota dinamis**: Diaktifkan

    > \*Batas tarif 5.000 token per menit sudah lebih dari cukup untuk menyelesaikan latihan ini dan masih menyisakan kapasitas untuk orang lain yang menggunakan langganan yang sama.

## Hasilkan kode di playground obrolan

Sebelum menggunakan di aplikasi Anda, periksa bagaimana Azure OpenAI dapat menghasilkan dan menjelaskan kode di taman bermain obrolan.

1. Di **Azure OpenAI Studio** di `https://oai.azure.com`, di bagian **Playground**, pilih halaman **Obrolan**. Halaman playground **Obrolan** terdiri dari tiga bagian utama:
    - **Penyiapan** - digunakan untuk mengatur konteks respons model.
    - **sesi Obrolan** - digunakan untuk mengirimkan pesan obrolan dan melihat respons.
    - **Configuration** - digunakan untuk mengonfigurasi pengaturan untuk penyebaran model.
2. Di bagian **Konfigurasi**, pastikan bahwa penyebaran model Anda telah dipilih.
3. Di area **Penyiapan**, atur pesan sistem ke `You are a programming assistant helping write code` dan terapkan perubahannya.
4. Dalam**Sesi obrolan**, masukkan kueri berikut:

    ```
    Write a function in python that takes a character and a string as input, and returns how many times the character appears in the string
    ```

    Model kemungkinan akan merespons dengan fungsi, dengan beberapa penjelasan tentang apa yang dilakukan fungsi dan cara memanggilnya.

5. Selanjutnya, kirim perintah `Do the same thing, but this time write it in C#`.

    Model ini kemungkinan merespons dengan sangat mirip seperti pertama kalinya, tetapi kali ini pengodean di C#. Anda dapat memintanya lagi untuk bahasa pilihan Anda yang berbeda, atau fungsi untuk menyelesaikan tugas yang berbeda seperti mengembalikan string input.

6. Selanjutnya, mari kita jelajahi menggunakan AI untuk memahami kode. Kirim perintah berikut sebagai pesan pengguna.

    ```
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

7. Masukkan perintah `Can you simplify the function?`.

    Model harus menulis versi fungsi yang lebih sederhana.

8. Masukkan perintah: `Add some comments to the function.`

    Model menambahkan komentar ke kode.

## Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Sekarang mari kita jelajahi bagaimana Anda dapat membuat aplikasi kustom yang menggunakan Azure OpenAI Service untuk menghasilkan kode. Anda akan mengembangkan aplikasi menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda telah mengkloning repositori **mslearn-openai**, buka di kode Visual Studio. Atau, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/mslearn-openai` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai pembuatnya** di pop-up.

4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan, serta sampel file teks yang akan Anda gunakan untuk menguji ringkasan. Kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda akan menyelesaikan beberapa bagian utama aplikasi untuk mengaktifkan menggunakan sumber daya Azure OpenAI Anda.

1. Di Visual Studio Code, pada panel **Penjelajah**, telusuri folder **Labfiles/04-code-generation** dan luaskan folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi yang akan Anda integrasikan dengan fungsionalitas Azure OpenAI.
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
    - **Nama penyebaran** yang Anda tentukan untuk penyebaran model Anda (tersedia di halaman**Penyebaran** di Azure OpenAI Studio).
5. Simpan file konfigurasi.

## Menambahkan kode untuk menggunakan model Azure OpenAI Service Anda

Sekarang Anda siap menggunakan Azure OpenAI SDK untuk menggunakan model yang Anda sebarkan.

1. Di panel **Penjelajah**, di folder **CSharp** atau **Python**, buka file kode untuk bahasa pilihan Anda. Dalam fungsi yang memanggil model Azure OpenAI, di bagian komentar ***Format dan kirim permintaan ke model***, tambahkan kode untuk memformat dan mengirimkan permintaan ke model.

    **C#**: Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemPrompt),
            new ChatRequestUserMessage(userPrompt)
        },
        Temperature = 0.7f,
        MaxTokens = 1000,
        DeploymentName = oaiDeploymentName
    };

    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);

    ChatCompletions completions = response.Value;
    string completion = completions.Choices[0].Message.Content;
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

4. Simpan perubahan terhadap file kode.

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
