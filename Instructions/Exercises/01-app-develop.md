---
lab:
  title: Mengembangkan Aplikasi dengan Azure OpenAI Service
---

# Mengembangkan Aplikasi dengan Azure OpenAI Service

Dengan Azure OpenAI Service, pengembang dapat membuat bot obrolan, model bahasa, dan aplikasi lain yang unggul dalam memahami bahasa manusia alami melalui penggunaan REST API atau SDK khusus bahasa. Saat bekerja dengan model bahasa ini, bagaimana pengembang membentuk prompt mereka sangat memengaruhi bagaimana model AI generatif akan merespons. Model Azure OpenAI dapat menyesuaikan dan memformat konten jika diminta dengan cara yang jelas dan ringkas. Dalam latihan ini, Anda akan mempelajari cara menghubungkan aplikasi Anda ke Azure OpenAI dan melihat bagaimana perintah yang berbeda untuk konten serupa dapat membantu membentuk respons model AI untuk memenuhi kebutuhan Anda dengan lebih baik.

Dalam skenario untuk latihan ini, Anda akan bertindak sebagai pengembang perangkat lunak yang sedang mengerjakan kampanye pemasaran margasatwa. Anda sedang menjelajahi cara menggunakan AI generatif untuk mengoptimalkan email iklan dan mengategorikan artikel yang mungkin berlaku untuk tim Anda. Teknik rekayasa perintah yang digunakan dalam latihan ini dapat diterapkan secara serupa untuk berbagai kasus penggunaan.

Latihan ini akan memakan waktu sekitar **30** menit.

## Mengkloning repositori untuk kursus ini

Jika Anda belum melakukannya, Anda harus mengkloning repositori kode untuk kursus ini:

1. Memulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/mslearn-openai` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## Provisi sumber daya Azure OpenAI

Jika Anda belum memilikinya, provisikan sumber daya Azure OpenAI di langganan Azure Anda.

1. Masuk ke **portal Microsoft Azure** di `https://portal.azure.com`.

1. Buat sumber daya **Azure OpenAI** dengan pengaturan berikut:
    - **Subscription**: *Pilih langganan Azure yang telah disetujui untuk akses ke Azure OpenAI Service*
    - **Grup sumber daya**: *Memilih atau membuat grup sumber daya*
    - **Wilayah**: *Buat **pilihan acak** dari salah satu wilayah berikut*\*
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

Selanjutnya, Anda akan menerapkan sumber daya model Azure OpenAI dari CLI. Lihat contoh ini dan ganti variabel berikut dengan nilai Anda sendiri dari atas:

```dotnetcli
az cognitiveservices account deployment create \
   -g *Your resource group* \
   -n *Name of your OpenAI service* \
   --deployment-name gpt-35-turbo \
   --model-name gpt-35-turbo \
   --model-version 0125  \
   --model-format OpenAI \
   --sku-name "Standard" \
   --sku-capacity 5
```

    > \* Sku-capacity is measured in thousands of tokens per minute. A rate limit of 5,000 tokens per minute is more than adequate to complete this exercise while leaving capacity for other people using the same subscription.

> [!NOTE]
> Jika Anda melihat peringatan tentang kerangka kerja net7.0 yang sudah tidak didukung, Anda dapat mengabaikannya untuk latihan ini.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan, dan kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda harus menyelesaikan beberapa bagian utama aplikasi untuk memungkinkan penggunaan sumber daya Azure OpenAI dengan panggilan API asinkron.

1. Di Visual Studio Code, pada panel **Penjelajah**, telusuri folder **Labfiles/01-code-generation** dan luaskan folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi yang akan Anda integrasikan dengan fungsionalitas Azure OpenAI.
2. Klik kanan folder **CSharp** atau **Python** yang berisi file kode Anda dan buka terminal terintegrasi. Kemudian, instal paket Azure OpenAI SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**:

    ```
    dotnet add package Azure.AI.OpenAI --version 2.0.0
    ```

    **Python**:

    ```
    pip install openai==1.54.3
    ```

3. Pada panel **Explorer**, di folder **CSharp** atau **Python**, buka file konfigurasi untuk bahasa antarmuka pilihan pengguna

    - **C#**: appsettings.json
    - **Python**: .env
    
4. Perbarui nilai konfigurasi untuk menyertakan:
    - **Titik akhir** dan **kunci** dari sumber daya Azure OpenAI yang Anda buat (tersedia di halaman **Kunci dan Titik Akhir** untuk sumber daya Azure OpenAI Anda di portal Microsoft Azure)
    - **Nama penerapan** yang Anda tentukan untuk penerapan model Anda.
5. Simpan file konfigurasi.

## Menambahkan kode untuk menggunakan Azure OpenAI Service

Sekarang Anda siap menggunakan Azure OpenAI SDK untuk menggunakan model yang Anda sebarkan.

1. Pada panel **Explorer**, di folder **CSharp** atau **Python**, buka file kode untuk bahasa antarmuka pilihan pengguna, lalu ganti komentar ***Tambahkan paket Azure OpenAI*** dengan kode untuk menambahkan pustaka Azure OpenAI SDK:

    **C#**: Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python**: application.py

    ```python
    # Add Azure OpenAI package
    from openai import AsyncAzureOpenAI
    ```

2. Dalam file kode, temukan komentar ***Konfigurasikan klien Azure OpenAI***, lalu tambahkan kode untuk mengonfigurasi klien Azure OpenAI:

    **C#**: Program.cs

    ```csharp
    // Configure the Azure OpenAI client
    AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
    ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
    ```

    **Python**: application.py

    ```python
    # Configure the Azure OpenAI client
    client = AsyncAzureOpenAI(
        azure_endpoint = azure_oai_endpoint, 
        api_key=azure_oai_key,  
        api_version="2024-02-15-preview"
        )
    ```

3. Dalam fungsi yang memanggil model Azure OpenAI, di bagian komentar ***Dapatkan respons dari Azure OpenAI***, tambahkan kode untuk memformat dan mengirimkan permintaan ke model.

    **C#**: Program.cs

    ```csharp
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        [
            new SystemChatMessage(systemMessage),
            new UserChatMessage(userMessage)
        ],
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    ```

    **Python**: application.py

    ```python
    # Get response from Azure OpenAI
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
    - **Python**: `python application.py`

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

    **Python**: application.py

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
