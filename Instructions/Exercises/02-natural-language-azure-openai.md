---
lab:
  title: Menggunakan Azure OpenAI SDK di aplikasi Anda
  status: stale
---

# Menggunakan Azure OpenAI API di aplikasi Anda

Dengan Azure OpenAI Service, pengembang dapat membuat chatbot, model bahasa, dan aplikasi lain yang unggul dalam memahami bahasa manusia alami. Azure OpenAI menyediakan akses ke model AI yang telah dilatih sebelumnya, serta serangkaian API dan alat untuk menyesuaikan dan menyempurnakan model ini untuk memenuhi persyaratan spesifik aplikasi Anda. Dalam latihan ini, Anda akan mempelajari cara menyebarkan model di Azure OpenAI dan menggunakannya di aplikasi Anda sendiri.

Dalam skenario latihan ini, Anda akan berperan sebagai pengembang perangkat lunak yang ditugaskan untuk mengimplementasikan aplikasi yang dapat menggunakan AI generatif untuk membantu memberikan rekomendasi pendakian. Teknik yang digunakan dalam latihan dapat diterapkan ke aplikasi apa pun yang ingin menggunakan Azure OpenAI API.

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

## Bersiap untuk mengembangkan aplikasi di Visual Studio Code

Anda akan mengembangkan aplikasi Azure OpenAI menggunakan Visual Studio Code. File kode untuk aplikasi Anda telah disediakan di repositori GitHub.

> **Tips**: Jika Anda telah membuat klon repositori **mslearn-openai**, buka klon tersebut di Visual Studio Code. Atau, ikuti langkah-langkah ini untuk mengkloningnya ke lingkungan pengembangan Anda.

1. Memulai Visual Studio Code.
2. Buka palet perintah (SHIFT+CTRL+P) atau **Lihat** > ** palet perintah...**) lalu jalankan perintah **Git: Clone** untuk mengkloning `https://github.com/MicrosoftLearning/mslearn-openai`repositori ke folder lokal (tidak masalah folder mana pun).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.

    > **Catatan**: Jika Visual Studio Code menampilkan pesan pop-up yang meminta Anda memercayai kode yang Anda buka, klik opsi **Ya, saya memercayai pembuatnya** di pop-up.

4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## Mengonfigurasi aplikasi Anda

Aplikasi untuk C# dan Python telah disediakan. Kedua aplikasi memiliki fungsionalitas yang sama. Pertama, Anda akan menyelesaikan beberapa bagian utama aplikasi untuk mengaktifkan menggunakan sumber daya Azure OpenAI Anda.

1. Di Visual Studio Code, di panel **Explorer**, telusuri folder **Labfiles/02-azure-openai-api** dan perluas folder **CSharp** atau **Python** tergantung pada preferensi bahasa Anda. Setiap folder berisi file khusus bahasa untuk aplikasi tempat Anda akan mengintegrasikan fungsionalitas Azure OpenAI.
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

## Menambahkan kode untuk menggunakan Azure OpenAI Service

Sekarang Anda siap menggunakan Azure OpenAI SDK untuk menggunakan model yang Anda sebarkan.

1. Pada panel **Explorer**, di folder **CSharp** atau **Python**, buka file kode untuk bahasa antarmuka pilihan pengguna, lalu ganti komentar ***Tambahkan paket Azure OpenAI*** dengan kode untuk menambahkan pustaka Azure OpenAI SDK:

    **C#**: Program.cs

    ```csharp
    // Add Azure OpenAI packages
    using Azure.AI.OpenAI;
    using OpenAI.Chat;
    ```

    **Python**: test-openai-model.py

    ```python
    # Add Azure OpenAI package
    from openai import AzureOpenAI
    ```

1. Dalam kode aplikasi untuk bahasa Anda, ganti komentar ***Inisialisasi klien Azure OpenAI...*** dengan kode berikut untuk menginisialisasi klien dan menentukan pesan sistem kami.

    **C#**: Program.cs

    ```csharp
    // Initialize the Azure OpenAI client
    AzureOpenAIClient azureClient = new (new Uri(oaiEndpoint), new ApiKeyCredential(oaiKey));
    ChatClient chatClient = azureClient.GetChatClient(oaiDeploymentName);
    
    // System message to provide context to the model
    string systemMessage = "I am a hiking enthusiast named Forest who helps people discover hikes in their area. If no area is specified, I will default to near Rainier National Park. I will then provide three suggestions for nearby hikes that vary in length. I will also share an interesting fact about the local nature on the hikes when making a recommendation.";
    ```

    **Python**: test-openai-model.py

    ```python
    # Initialize the Azure OpenAI client
    client = AzureOpenAI(
            azure_endpoint = azure_oai_endpoint, 
            api_key=azure_oai_key,  
            api_version="2024-02-15-preview"
            )
    
    # Create a system message
    system_message = """I am a hiking enthusiast named Forest who helps people discover hikes in their area. 
        If no area is specified, I will default to near Rainier National Park. 
        I will then provide three suggestions for nearby hikes that vary in length. 
        I will also share an interesting fact about the local nature on the hikes when making a recommendation.
        """
    ```

1. Ganti komentar ***Tambahkan kode untuk mengirim permintaan...*** dengan kode yang diperlukan untuk membangun permintaan; menentukan berbagai parameter untuk model Anda seperti `Temperature` dan `MaxOutputTokenCount`.

    **C#**: Program.cs

    ```csharp
    // Add code to send request...
    // Get response from Azure OpenAI
    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        [
            new SystemChatMessage(systemMessage),
            new UserChatMessage(inputText)
        ],
        chatCompletionOptions
    );

    Console.WriteLine($"{completion.Role}: {completion.Content[0].Text}");
    ```

    **Python**: test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=400,
        messages=[
            {"role": "system", "content": system_message},
            {"role": "user", "content": input_text}
        ]
    )
    generated_text = response.choices[0].message.content

    # Print the response
    print("Response: " + generated_text + "\n")
    ```

1. Simpan perubahan pada file kode Anda.

## Menguji aplikasi Anda

Setelah dikonfigurasi, kini saatnya menjalankan aplikasi Anda untuk mengirimkan permintaan ke model Anda dan mengamati responsnya.

1. Di panel terminal interaktif, pastikan konteks folder adalah folder untuk bahasa pilihan Anda. Lalu masukkan perintah berikut untuk menjalankan aplikasi.

    - **C#:** `dotnet run`
    - **Python**: `python test-openai-model.py`

    > **Tips**: Anda dapat menggunakan ikon **Maksimalkan ukuran panel** (**^**) di bar alat terminal untuk melihat teks konsol lainnya.

1. Saat diminta, masukkan teks `What hike should I do near Rainier?`.
1. Amati output, perhatikan bahwa respons mengikuti panduan yang disediakan dalam pesan sistem yang Anda tambahkan ke array*pesan*.
1. Berikan perintah `Where should I hike near Boise? I'm looking for something of easy difficulty, between 2 to 3 miles, with moderate elevation gain.` dan amati output.
1. Dalam file kode untuk bahasa pilihan Anda, ubah nilai parameter *temperature* dalam permintaan Anda menjadi **1.0** dan simpan file.
1. Jalankan aplikasi lagi menggunakan perintah di atas, dan amati output.

Meningkatkan suhu sering menyebabkan respons bervariasi, bahkan ketika disediakan teks yang sama, karena peningkatan keacakan. Anda dapat menjalankannya beberapa kali untuk melihat bagaimana output dapat berubah. Coba gunakan nilai yang berbeda untuk suhu Anda dengan input yang sama.

## Mempertahankan riwayat percakapan

Di sebagian besar aplikasi dunia nyata, kemampuan untuk mereferensikan bagian percakapan sebelumnya memungkinkan interaksi yang lebih realistis dengan agen AI. API Azure OpenAI tanpa status berdasarkan desain, tetapi dengan memberikan riwayat percakapan dalam perintah Anda, Anda mengaktifkan model AI untuk mereferensikan pesan sebelumnya.

1. Jalankan aplikasi lagi dan berikan perintah `Where is a good hike near Boise?`.
1. Amati output, lalu berikan perintah `How difficult is the second hike you suggested?`.
1. Respons dari model kemungkinan besar akan menunjukkan tidak memahami pendakian yang Anda maksud. Untuk memperbaikinya, kita dapat mengaktifkan model untuk memiliki pesan percakapan sebelumnya untuk referensi.
1. Dalam aplikasi Anda, kita perlu menambahkan perintah dan respons sebelumnya ke perintah di masa mendatang yang kita kirim. Di bawah definisi **pesan sistem**, tambahkan kode berikut.

    **C#**: Program.cs

    ```csharp
    // Initialize messages list
    var messagesList = new List<ChatMessage>()
    {
        new SystemChatMessage(systemMessage),
    };
    ```

    **Python**: test-openai-model.py

    ```python
    # Initialize messages array
    messages_array = [{"role": "system", "content": system_message}]
    ```

1. Di bawah komentar ***Tambahkan kode untuk mengirim permintaan...***, ganti semua kode dari komentar ke akhir perulangan **while** dengan kode berikut lalu simpan file. Kode sebagian besar sama, tetapi sekarang menggunakan array pesan untuk menyimpan riwayat percakapan.

    **C#**: Program.cs

    ```csharp
    // Add code to send request...
    // Build completion options object
    messagesList.Add(new UserChatMessage(inputText));

    ChatCompletionOptions chatCompletionOptions = new ChatCompletionOptions()
    {
        Temperature = 0.7f,
        MaxOutputTokenCount = 800
    };

    ChatCompletion completion = chatClient.CompleteChat(
        messagesList,
        chatCompletionOptions
    );

    // Return the response
    string response = completion.Content[0].Text;

    // Add generated text to messages list
    messagesList.Add(new AssistantChatMessage(response));

    Console.WriteLine("Response: " + response + "\n");
    ```

    **Python**: test-openai-model.py

    ```python
    # Add code to send request...
    # Send request to Azure OpenAI model
    messages_array.append({"role": "user", "content": input_text})

    response = client.chat.completions.create(
        model=azure_oai_deployment,
        temperature=0.7,
        max_tokens=1200,
        messages=messages_array
    )
    generated_text = response.choices[0].message.content
    # Add generated text to messages array
    messages_array.append({"role": "assistant", "content": generated_text})

    # Print generated text
    print("Summary: " + generated_text + "\n")
    ```

1. Simpan file. Dalam kode yang Anda tambahkan, perhatikan bahwa kami sekarang menambahkan input dan respons sebelumnya ke array prompt yang memungkinkan model untuk memahami riwayat percakapan kami.
1. Di panel terminal, masukkan perintah berikut untuk menjalankan aplikasi.

    - **C#:** `dotnet run`
    - **Python**: `python test-openai-model.py`

1. Jalankan aplikasi lagi dan berikan perintah `Where is a good hike near Boise?`.
1. Amati output, lalu berikan perintah `How difficult is the second hike you suggested?`.
1. Anda kemungkinan akan mendapatkan respons tentang pendakian kedua yang disarankan model, yang menyediakan percakapan yang jauh lebih realistis. Anda dapat mengajukan pertanyaan tindak lanjut tambahan yang mereferensikan jawaban sebelumnya, dan setiap kali riwayat menyediakan konteks untuk dijawab oleh model.

    > **Tips**: Jumlah token hanya diatur ke 800, jadi jika percakapan berlanjut terlalu lama, aplikasi akan kehabisan token yang tersedia, menghasilkan prompt yang tidak lengkap. Dalam penggunaan produksi, membatasi panjang riwayat ke input dan respons terbaru akan membantu mengontrol jumlah token yang diperlukan.

## Penghapusan

Setelah Anda selesai dengan sumber daya Azure OpenAI, ingatlah untuk menghapus penyebaran atau seluruh sumber daya pada **portal Microsoft Azure** di `https://portal.azure.com`.
