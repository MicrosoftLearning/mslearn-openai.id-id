---
lab:
  title: Menggunakan rekayasa perintah di aplikasi Anda
---

# Menggunakan rekayasa perintah di aplikasi Anda

Saat bekerja dengan Azure OpenAI Service, respons model AI generatif sangat dipengaruhi oleh cara pengembang menyusun perintah mereka. Model Azure OpenAI dapat menyesuaikan dan memformat konten jika diminta dengan cara yang jelas dan ringkas. Dalam latihan ini, Anda akan mempelajari bagaimana perintah yang berbeda untuk konten serupa dapat membantu membentuk respons model AI untuk memenuhi kebutuhan Anda dengan lebih baik.

Dalam skenario untuk latihan ini, Anda akan bertindak sebagai pengembang perangkat lunak yang sedang mengerjakan kampanye pemasaran penyelamatan margasatwa. Anda sedang menjelajahi cara menggunakan AI generatif untuk mengoptimalkan email iklan dan mengategorikan artikel yang mungkin berlaku untuk tim Anda. Teknik rekayasa perintah yang digunakan dalam latihan ini dapat diterapkan secara serupa untuk berbagai kasus penggunaan.

Latihan ini akan memakan waktu sekitar **30** menit.

## Provisi sumber daya Azure OpenAI

Jika Anda belum memilikinya, provisikan sumber daya Azure OpenAI di langganan Azure Anda.

1. Masuk ke **portal Microsoft Azure** di `https://portal.azure.com`.
2. Buat sumber daya **Azure OpenAI** dengan pengaturan berikut:
    - **Subscription**: *Pilih langganan Azure yang telah disetujui untuk akses ke Azure OpenAI Service*
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

Azure menyediakan portal berbasis web bernama **Azure AI Studio** yang dapat Anda gunakan untuk menyebarkan, mengelola, dan menjelajahi berbagai model. Anda akan memulai penjelajahan Azure OpenAI dengan menggunakan Azure AI Studio untuk menyebarkan satu model.

> **Catatan**: Saat Anda menggunakan Azure AI Studio, kotak pesan yang menyarankan tugas untuk Anda lakukan dapat ditampilkan. Anda dapat menutup ini dan mengikuti langkah-langkah dalam latihan ini.

1. Di portal Azure, pada **halaman Ikhtisar** untuk sumber daya Azure OpenAI Anda, gulir ke bawah ke bagian  **Memulai** dan pilih tombol untuk masuk ke **AI Studio**.
1. Di Azure OpenAI Studio, di panel sebelah kiri, pilih halaman **Penyebaran** dan lihat penyebaran model yang sudah ada. Jika Anda belum memilikinya, buat penyebaran baru model **gpt-35-turbo-16k** dengan pengaturan berikut:
    - **Nama penyebaran**: *Nama unik pilihan Anda*
    - **Model**: gpt-35-turbo-16k *(jika model 16k tidak tersedia, pilih gpt-35-turbo)*
    - **Versi model**: *Gunakan versi default*
    - **Tipe penyebaran**: Standar
    - **Batas tarif token per menit**: 5K\*
    - **Filter konten**: Default
    - **Aktifkan kuota dinamis**: Dinonaktifkan

    > \*Batas tarif 5.000 token per menit sudah lebih dari cukup untuk menyelesaikan latihan ini dan masih menyisakan kapasitas untuk orang lain yang menggunakan langganan yang sama.

## Menjelajahi teknik rekayasa perintah

Mari kita mulai dengan menjelajahi beberapa teknik rekayasa perintah di playground Obrolan.

1. Di bagian **Playground**, pilih halaman **Obrolan**. Halaman playground **Obrolan** terdiri dari sederet tombol dan dua panel utama (yang dapat disusun dari kanan ke kiri secara horizontal, atau dari atas ke bawah secara vertikal bergantung pada resolusi layar Anda):
    - **Konfigurasi** - digunakan untuk memilih penyebaran Anda, menentukan pesan sistem, dan mengatur parameter untuk berinteraksi dengan penyebaran Anda.
    - **sesi Obrolan** - digunakan untuk mengirimkan pesan obrolan dan melihat respons.
2. Di bawah **Penyebaran**, pastikan bahwa penyebaran model gpt-35-turbo-16k Anda dipilih.
1. Tinjau **Pesan sistem** default, yang seharusnya *Anda adalah asisten AI yang membantu orang menemukan informasi.*
4. Dalam**Sesi obrolan**, masukkan kueri berikut:

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    Responsnya memberikan deskripsi artikel. Namun, misalkan Anda menginginkan format yang lebih spesifik untuk kategorisasi artikel.

5. Di bagian **Konfigurasi**, ubah pesan sistem menjadi `You are a news aggregator that categorizes news articles.`

6. Di bawah pesan sistem baru, pilih tombol **Tambah bagian**, lalu pilih **Contoh**. Kemudian, tambahkan contoh berikut.

    **Pengguna:**
    
    ```prompt
    What kind of article is this?
    ---
    New York Baseballers Wins Big Against Chicago
    
    New York Baseballers mounted a big 5-0 shutout against the Chicago Cyclones last night, solidifying their win with a 3 run homerun late in the bottom of the 7th inning.
    
    Pitcher Mario Rogers threw 96 pitches with only two hits for New York, marking his best performance this year.
    
    The Chicago Cyclones' two hits came in the 2nd and the 5th innings but were unable to get the runner home to score.
    ```
    
    **Asisten:**
    
    ```prompt
    Sports
      ```

7. Tambahkan contoh lain dengan teks berikut.

    **Pengguna:**
    
    ```prompt
    Categorize this article:
    ---
    Joyous moments at the Oscars
    
    The Oscars this past week where quite something!
    
    Though a certain scandal might have stolen the show, this year's Academy Awards were full of moments that filled us with joy and even moved us to tears.
    These actors and actresses delivered some truly emotional performances, along with some great laughs, to get us through the winter.
    
    From Robin Kline's history-making win to a full performance by none other than Casey Jensen herself, don't miss tomorrows rerun of all the festivities.
    ```
    
    **Asisten:**
    
    ```prompt
    Entertainment
    ```

8. Gunakan tombol **Terapkan perubahan** di bagian atas bagian **Konfigurasi** untuk menyimpan perubahan Anda.

9. Di bagian **Sesi obrolan**, masukkan ulang perintah berikut:

    ```prompt
    What kind of article is this?
    ---
    Severe drought likely in California
    
    Millions of California residents are bracing for less water and dry lawns as drought threatens to leave a large swath of the region with a growing water shortage.
    
    In a remarkable indication of drought severity, officials in Southern California have declared a first-of-its-kind action limiting outdoor water use to one day a week for nearly 8 million residents.
    
    Much remains to be determined about how daily life will change as people adjust to a drier normal. But officials are warning the situation is dire and could lead to even more severe limits later in the year.
    ```

    Kombinasi pesan sistem yang lebih spesifik serta beberapa contoh kueri dan respons yang diharapkan akan menghasilkan format yang konsisten untuk hasilnya.

10. Ubah pesan sistem kembali ke templat default, yakni `You are an AI assistant that helps people find information.` tanpa disertai contoh. Setelah itu, terapkan perubahannya.

11. Di bagian **Sesi obrolan**, masukkan ulang perintah berikut:

    ```prompt
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    Untuk memenuhi perintah, model kemungkinan akan merespons dengan jawaban, yang dibagi menjadi daftar bernomor. Ini adalah respons yang tepat, tetapi bagaimana jika Anda sebenarnya ingin agar model menulis program Python yang melakukan tugas yang sudah jelaskan?

12. Ubah pesan sistem menjadi `You are a coding assistant helping write python code.` dan terapkan perubahannya.
13. Masukkan ulang perintah berikut ke model:

    ```
    # 1. Create a list of animals
    # 2. Create a list of whimsical names for those animals
    # 3. Combine them randomly into a list of 25 animal and name pairs
    ```

    Model akan merespons secara tepat dengan kode python yang melakukan permintaan yang ada di komentar.

## Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Sekarang mari kita jelajahi rekayasa perintah di aplikasi yang menggunakan Azure OpenAI Service SDK. Anda akan mengembangkan aplikasi menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda telah membuat klon repositori **mslearn-openai**, buka klon tersebut di Visual Studio Code. Atau, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/mslearn-openai` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai pembuatnya** di pop-up.

4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan, dan kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda harus menyelesaikan beberapa bagian utama aplikasi untuk memungkinkan penggunaan sumber daya Azure OpenAI dengan panggilan API asinkron.

1. Di Visual Studio Code, pada panel **Penjelajah**, telusuri folder **Labfiles/03-prompt-engineering** dan luaskan folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi yang akan Anda integrasikan dengan fungsionalitas Azure OpenAI.
2. Klik kanan folder **CSharp** atau **Python** yang berisi file kode Anda dan buka terminal terintegrasi. Kemudian, instal paket Azure OpenAI SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**:

    ```
    dotnet add package Azure.AI.OpenAI --version 1.0.0-beta.14
    ```

    **Python**:

    ```
    pip install openai==1.13.3
    ```

3. Pada panel **Explorer**, di folder **CSharp** atau **Python**, buka file konfigurasi untuk bahasa antarmuka pilihan pengguna

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Perbarui nilai konfigurasi untuk menyertakan:
    - **Titik akhir** dan **kunci** dari sumber daya Azure OpenAI yang Anda buat (tersedia di halaman **Kunci dan Titik Akhir** untuk sumber daya Azure OpenAI Anda di portal Microsoft Azure)
    - **Nama penyebaran** yang Anda tentukan untuk penyebaran model Anda (tersedia di halaman**Penyebaran** di Azure OpenAI Studio).
5. Simpan file konfigurasi.

## Menambahkan kode untuk menggunakan Azure OpenAI Service

Sekarang Anda siap menggunakan Azure OpenAI SDK untuk menggunakan model yang Anda sebarkan.

1. Pada panel **Explorer**, di folder **CSharp** atau **Python**, buka file kode untuk bahasa antarmuka pilihan pengguna, lalu ganti komentar ***Tambahkan paket Azure OpenAI*** dengan kode untuk menambahkan pustaka Azure OpenAI SDK:

    **C#**: Program.cs

    ```csharp
    // Add Azure OpenAI package
    using Azure.AI.OpenAI;
    ```

    **Python**: prompt-engineering.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. Dalam file kode, temukan komentar ***Konfigurasikan klien Azure OpenAI***, lalu tambahkan kode untuk mengonfigurasi klien Azure OpenAI:

    **C#**: Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    OpenAIClient client = new OpenAIClient(new Uri(oaiEndpoint), new AzureKeyCredential(oaiKey));
    ```

    **Python**: prompt-engineering.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. Dalam fungsi yang memanggil model Azure OpenAI, di bagian komentar ***Format dan kirim permintaan ke model***, tambahkan kode untuk memformat dan mengirimkan permintaan ke model.

    **C#**: Program.cs

    ```csharp
    // Format and send the request to the model
    var chatCompletionsOptions = new ChatCompletionsOptions()
    {
        Messages =
        {
            new ChatRequestSystemMessage(systemMessage),
            new ChatRequestUserMessage(userMessage)
        },
        Temperature = 0.7f,
        MaxTokens = 800,
        DeploymentName = oaiDeploymentName
    };
    
    // Get response from Azure OpenAI
    Response<ChatCompletions> response = await client.GetChatCompletionsAsync(chatCompletionsOptions);
    ```

    **Python**: prompt-engineering.py

    ```python
    # Format and send the request to the model
    messages =[
        {"role": "system", "content": system_message},
        {"role": "user", "content": user_message},
    ]
    
    print("\nSending request to Azure OpenAI model...\n")

    # Call the Azure OpenAI model
    response = await client.chat.completions.create(
        model=model,
        messages=messages,
        temperature=0.7,
        max_tokens=800
    )
    ```

4. Simpan perubahan terhadap file kode.

## Jalankan aplikasi Anda

Sekarang setelah aplikasi Anda dikonfigurasi, jalankan untuk mengirim permintaan Anda ke model Anda dan amati responsnya. Anda akan melihat bahwa satu-satunya perbedaan antara berbagai opsinya adalah konten perintahnya, semua parameter lainnya (seperti jumlah token dan temperatur) tetap sama untuk setiap permintaan.

1. Di folder bahasa antarmuka pilihan pengguna, buka `system.txt` di Visual Studio Code. Untuk setiap iterasi, masukkan **Pesan sistem** dalam file ini dan simpan. Setiap iterasi akan dijeda terlebih dahulu agar Anda dapat mengubah pesan sistem.
1. Di panel terminal interaktif, pastikan konteks folder adalah folder untuk bahasa pilihan Anda. Lalu masukkan perintah berikut untuk menjalankan aplikasi.

    - **C#:** `dotnet run`
    - **Python**: `python prompt-engineering.py`

    > **Tips**: Anda dapat menggunakan ikon **Maksimalkan ukuran panel** (**^**) di bar alat terminal untuk melihat teks konsol lainnya.

1. Untuk iterasi pertama, masukkan perintah berikut:

    **Pesan sistem**

    ```prompt
    You are an AI assistant
    ```

    **Pesan pengguna:**

    ```prompt
    Write an intro for a new wildlife Rescue
    ```

1. Amati output. Model AI kemungkinan akan menghasilkan pendahuluan generik yang baik untuk penyelamatan margasatwa.
1. Selanjutnya, masukkan perintah berikut yang menentukan format untuk responsnya:

    **Pesan sistem**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Pesan pengguna:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants 
    - Call for donations to be given at our website
    ```

    > **Tip**: Anda mungkin mendapati bahwa pengetikan otomatis pada VM tidak berfungsi dengan baik dengan prompt multiline. Jika itu masalah Anda, salin seluruh perintah lalu tempelkan ke Visual Studio Code.

1. Amati output. Kali ini, Anda mungkin akan melihat format email yang berisi spesies hewan tertentu serta ajakan untuk berdonasi.
1. Selanjutnya, masukkan perintah berikut yang juga menentukan kontennya:

    **Pesan sistem**

    ```prompt
    You are an AI assistant helping to write emails
    ```

    **Pesan pengguna:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Amati outputnya, dan lihat bagaimana emailnya telah berubah sesuai dengan instruksi yang jelas dari Anda.
1. Selanjutnya, masukkan perintah berikut yang telah kami beri tambahan berupa detail tentang nada pada pesan sistem:

    **Pesan sistem**

    ```prompt
    You are an AI assistant that helps write promotional emails to generate interest in a new business. Your tone is light, chit-chat oriented and you always include at least two jokes.
    ```

    **Pesan pengguna:**

    ```prompt
    Write a promotional email for a new wildlife rescue, including the following: 
    - Rescue name is Contoso 
    - It specializes in elephants, as well as zebras and giraffes 
    - Call for donations to be given at our website 
    \n Include a list of the current animals we have at our rescue after the signature, in the form of a table. These animals include elephants, zebras, gorillas, lizards, and jackrabbits.
    ```

1. Amati output. Kali ini Anda mungkin akan melihat email dalam format yang sama, tetapi dengan nada yang jauh lebih informal. Bahkan, Anda mungkin akan melihat lelucon di dalamnya!
1. Untuk iterasi akhir, kami akan sedikit menyimpang dari pembuatan email dan mencoba *konteks grounding*. Di sini, Anda akan memberikan pesan sistem sederhana, dan mengubah aplikasi untuk memberikan konteks grounding sebagai awal perintah pengguna. Aplikasi kemudian akan menambahkan input pengguna, dan mengekstrak informasi dari konteks grounding untuk menjawab perintah pengguna kami.
1. Buka file `grounding.txt` dan baca secara singkat konteks grounding yang akan Anda sisipkan.
1. Di aplikasi Anda, tepat setelah komentar ***Format dan kirim permintaan ke model*** dan sebelum kode yang ada, tambahkan cuplikan kode berikut untuk membaca teks dari `grounding.txt` guna memperkaya perintah pengguna dengan konteks grounding.

    **C#**: Program.cs

    ```csharp
    // Format and send the request to the model
    Console.WriteLine("\nAdding grounding context from grounding.txt");
    string groundingText = System.IO.File.ReadAllText("grounding.txt");
    userMessage = groundingText + userMessage;
    ```

    **Python**: prompt-engineering.py

    ```python
    # Format and send the request to the model
    print("\nAdding grounding context from grounding.txt")
    grounding_text = open(file="grounding.txt", encoding="utf8").read().strip()
    user_message = grounding_text + user_message
    ```

1. Simpan file dan jalankan ulang aplikasi Anda.
1. Masukkan perintah berikut (dengan tetap memasukkan dan menyimpan **pesan sistem** dalam `system.txt`).

    **Pesan sistem**

    ```prompt
    You're an AI assistant who helps people find information. You'll provide answers from the text provided in the prompt, and respond concisely.
    ```

    **Pesan pengguna:**

    ```prompt
    What animal is the favorite of children at Contoso?
    ```

> **Tips**: Jika ingin melihat respons lengkap dari Azure OpenAI, Anda dapat mengatur variabel **printFullResponse** ke `True`, lalu menjalankan ulang aplikasi.

## Penghapusan

Setelah Anda selesai dengan sumber daya Azure OpenAI, ingatlah untuk menghapus penyebaran atau seluruh sumber daya pada **portal Microsoft Azure** di `https://portal.azure.com`.
