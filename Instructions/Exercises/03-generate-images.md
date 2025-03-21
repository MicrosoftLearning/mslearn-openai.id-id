---
lab:
  title: Hasilkan gambar dengan AI
  description: Pelajari cara menggunakan model DALL-E OpenAI untuk menghasilkan gambar.
  status: new
---

# Hasilkan gambar dengan AI

Dalam latihan ini, Anda menggunakan model AI generatif OpenAI DALL-E untuk menghasilkan gambar. Anda akan mengembangkan aplikasi dengan menggunakan Azure AI Foundry dan Azure OpenAI Service.

Latihan ini memakan waktu sekitar **30** menit.

## Membuat proyek Azure OpenAI

Mari kita mulai dengan membuat proyek Azure AI Foundry.

1. Di browser web, buka [portal Azure AI Foundry](https://ai.azure.com) di `https://ai.azure.com` dan masuk menggunakan kredensial Azure Anda. Tutup semua tip atau panel mulai cepat yang terbuka saat pertama kali Anda masuk, dan jika perlu, gunakan logo **Azure AI Foundry** di kiri atas untuk menavigasi ke halaman beranda, yang terlihat sama dengan gambar berikut:

    ![Tangkapan layar portal Azure AI Foundry.](../media/ai-foundry-home.png)

1. Di beranda, pilih **+ Buat proyek**.
1. Di wizard **Buat proyek**, masukkan nama proyek yang sesuai untuk (misalnya, `my-ai-project`) lalu tinjau sumber daya Azure yang akan dibuat secara otomatis untuk mendukung proyek Anda.
1. Pilih **Kustomisasi** dan tentukan pengaturan berikut untuk hub Anda:
    - **Nama hub**: *Nama unik - misalnya `my-ai-hub`*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya dengan nama unik (misalnya, `my-ai-resources`), atau pilih yang sudah ada*
    - **Lokasi**: Pilih **Bantu saya memilih** lalu pilih **dalle** di jendela Pembantu lokasi dan gunakan wilayah yang direkomendasikan\*
    - **Menyambungkan Layanan Azure AI atau Azure OpenAI**: *Membuat sumber daya Layanan AI baru dengan nama yang sesuai (misalnya, `my-ai-services`) atau menggunakan yang sudah ada*
    - **Menyambungkan Azure AI Search**: Lewati koneksi

    > \* Sumber daya Azure OpenAI dibatasi oleh kuota regional. Jika batas kuota tercapai di akhir latihan, Anda mungkin perlu membuat sumber daya lain di wilayah yang berbeda.

1. Pilih **Berikutnya** dan tinjau konfigurasi Anda. Lalu pilih **Buat** dan tunggu hingga prosesnya selesai.
1. Saat proyek Anda dibuat, tutup tips apa pun yang ditampilkan dan tinjau halaman proyek di portal Azure AI Foundry, yang akan terlihat mirip dengan gambar berikut:

    ![Tangkapan layar detail proyek Azure AI di portal Azure AI Foundry.](../media/ai-foundry-project.png)

## Menyebarkan model DALL-E

Sekarang Anda siap untuk menyebarkan model DALL-E untuk mendukung pembuatan gambar.

1. Di toolbar di kanan atas halaman proyek Azure AI Foundry Anda, gunakan ikon **Fitur pratinjau** untuk mengaktifkan fitur **Sebarkan model ke layanan inferensi model Azure AI**.
1. Di panel sebelah kiri untuk proyek Anda, di bagian **Aset saya**, pilih halaman **Model + titik akhir**.
1. Pada halaman **Model + titik akhir** , di tab **Penyebaran model** di menu **+ Sebarkan model** pilih **Sebarkan model dasar**.
1. Cari model **dall-e-3** dari daftar, pilih dan konfirmasi.
1. Setujui perjanjian lisensi jika diminta, lalu sebarkan model dengan pengaturan berikut dengan memilih **Sesuaikan** dalam detail penyebaran:
    - **Nama penyebaran** : *Nama unik untuk penyebaran model Anda - misalnya `dall-e-3` (ingat nama yang Anda tetapkan, Anda akan membutuhkannya nanti*)
    - **Tipe penyebaran**: Standar
    - **Detail penyebaran**: *Gunakan pengaturan default*
1. Tunggu hingga status penyediaan penyebaran menjadi **Selesai**.

## Menguji model di playground

Sebelum membuat aplikasi klien, mari kita uji model DALL-E di playground.

1. Di halaman untuk model DALL-E yang Anda sebarkan, pilih **Buka di playground** (atau di halaman **Playground**, buka **Playground Gambar**).
1. Pastikan penyebaran model DALL-E Anda dipilih. Kemudian, di kotak **Perintah** , masukkan perintah seperti `Create an image of an robot eating spaghetti`.
1. Tinjau gambar yang dihasilkan di playground:

    ![Tangkapan layar playground gambar dengan gambar yang dihasilkan.](../media/images-playground.png)

1. Masukkan perintah tindak lanjut, seperti `Show the robot in a restaurant` dan tinjau gambar yang dihasilkan.
1. Lanjutkan pengujian dengan perintah baru untuk menyempurnakan gambar sampai Anda puas dengannya.

## Membuat aplikasi klien

Model ini tampaknya bekerja di playground. Sekarang Anda dapat menggunakan SDK Azure OpenAI untuk menggunakannya dalam aplikasi klien.

> **Tips**: Anda dapat memilih untuk mengembangkan solusi Anda sendiri menggunakan Python atau Microsoft C#. Ikuti instruksi di bagian yang sesuai untuk bahasa yang Anda pilih.

### Menyiapkan konfigurasi aplikasi

1. Di portal Azure AI Foundry, lihat halaman **Gambaran Umum** untuk proyek Anda.
1. Di area **Detail proyek**, perhatikan **string koneksi Proyek**. Anda akan menggunakan string koneksi ini untuk menyambungkan ke proyek Anda di aplikasi klien.
1. Buka tab browser baru (biarkan portal Azure AI Foundry tetap terbuka di tab yang sudah ada). Kemudian di tab baru, telusuri [Portal Azure](https://portal.azure.com) di `https://portal.azure.com`; masuk menggunakan kredensial Azure Anda jika diminta.
1. Gunakan tombol **[\>_]** di sebelah kanan bilah pencarian di bagian atas halaman untuk membuat Cloud Shell baru di portal Azure, dengan memilih lingkungan ***PowerShell***. Cloud shell menyediakan antarmuka baris perintah dalam panel di bagian bawah portal Azure.

    > **Catatan**: Jika sebelumnya Anda telah membuat cloud shell yang menggunakan lingkungan *Bash* , alihkan ke ***PowerShell***.

1. Di toolbar cloud shell, di menu **Pengaturan**, pilih **Buka versi Klasik** (ini diperlukan untuk menggunakan editor kode).

    > **Tips**: Saat Anda menempelkan perintah ke cloudshell, ouput mungkin mengambil sejumlah besar buffer layar. Anda dapat menghapus layar dengan memasukkan `cls` perintah untuk mempermudah fokus pada setiap tugas.

1. Di panel PowerShell, masukkan perintah berikut untuk mengkloning repositori GitHub untuk latihan ini:

    ```
    rm -r mslearn-openai -f
    git clone https://github.com/microsoftlearning/mslearn-openai mslearn-openai
    ```

> **Catatan**: Ikuti langkah-langkah untuk bahasa pemrograman yang Anda pilih.

1. Setelah repositori dikloning, navigasikan ke folder yang berisi file kode aplikasi:  

    **Python**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/Python
    ```

    **C#**

    ```
   cd mslearn-openai/Labfiles/03-image-generation/CSharp
    ```

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menginstal pustaka yang akan Anda gunakan:

    **Python**

    ```
   pip install python-dotenv azure-identity azure-ai-projects openai requests
    ```

    *Anda dapat mengabaikan kesalahan tentang versi pip dan jalur lokal*

    **C#**

    ```
   dotnet add package Azure.Identity
   dotnet add package Azure.AI.Projects --prerelease
   dotnet add package Azure.AI.OpenAI
    ```

1. Masukkan perintah berikut untuk mengedit file konfigurasi yang telah disediakan:

    **Python**

    ```
   code .env
    ```

    **C#**

    ```
   code appsettings.json
    ```

    File dibuka dalam editor kode.

1. Dalam file kode, ganti tempat penampung **your_project_endpoint** dengan string koneksi untuk proyek Anda (disalin dari halaman **Gambaran Umum** proyek di Portal Azure AI Foundry), dan tempat penampung **your_model_deployment** dengan nama yang Anda tetapkan ke penyebaran model dall-e-3 Anda.
1. Setelah Anda mengganti tempat penampung, gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda lalu gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell tetap terbuka.

### Menulis kode untuk menyambungkan ke proyek Anda dan mengobrol dengan model Anda

> **Tips**: Saat Anda menambahkan kode, pastikan untuk mempertahankan indentasi yang benar.

1. Masukkan perintah berikut untuk mengedit file kode yang telah disediakan:

    **Python**

    ```
   code dalle-client.py
    ```

    **C#**

    ```
   code Program.cs
    ```

1. Dalam file kode, perhatikan pernyataan yang sudah ada yang telah ditambahkan di bagian atas file untuk mengimpor namespace SDK yang diperlukan. Kemudian, di bawah komentar **Tambahkan referensi**, tambahkan kode berikut untuk mereferensikan namespace di pustaka yang Anda instal sebelumnya:

    **Python**

    ```
   from dotenv import load_dotenv
   from azure.identity import DefaultAzureCredential
   from azure.ai.projects import AIProjectClient
   from openai import AzureOpenAI
   import requests
    ```

    **C#**

    ```
   using Azure.Identity;
   using Azure.AI.Projects;
   using Azure.AI.OpenAI;
   using OpenAI.Images;
    ```

1. Dalam fungsi **utama**, di bawah komentar **Dapatkan pengaturan konfigurasi**, perhatikan bahwa kode memuat string koneksi proyek dan nilai nama penyebaran model yang Anda tentukan dalam file konfigurasi.
1. Di bawah komentar **Inisialisasi klien proyek**, tambahkan kode berikut untuk menghubungkan proyek Azure AI Foundry Anda dengan menggunakan kredensial Azure yang Anda gunakan untuk masuk saat ini:

    **Python**

    ```
   project_client = AIProjectClient.from_connection_string(
        conn_str=project_connection,
        credential=DefaultAzureCredential())
    ```

    **C#**

    ```
   var projectClient = new AIProjectClient(project_connection,
                        new DefaultAzureCredential());
    ```

1. Di bawah komentar **Dapatkan klien OpenAI**, tambahkan kode berikut untuk membuat objek klien untuk mengobrol dengan model:

    **Python**

    ```
   openai_client = project_client.inference.get_azure_openai_client(api_version="2024-06-01")

    ```

    **C#**

    ```
   ConnectionResponse connection = projectClient.GetConnectionsClient().GetDefaultConnection(ConnectionType.AzureOpenAI, withCredential: true);

   var connectionProperties = connection.Properties as ConnectionPropertiesApiKeyAuth;

   AzureOpenAIClient openAIClient = new(
        new Uri(connectionProperties.Target),
        new AzureKeyCredential(connectionProperties.Credentials.Key));

   ImageClient openAIimageClient = openAIClient.GetImageClient(model_deployment);

    ```

1. Perhatikan bahwa kode menyertakan perulangan untuk memungkinkan pengguna memasukkan perintah hingga mereka memasukkan "berhenti". Selanjutnya, di bagian perulangan, di bawah komentar **Hasilkan sebuah gambar**, tambahkan kode berikut untuk mengirimkan perintah dan mengambil URL untuk gambar yang dihasilkan dari model Anda:

    **Python**

    ```python
   result = openai_client.images.generate(
        model=model_deployment,
        prompt=input_text,
        n=1
    )

    json_response = json.loads(result.model_dump_json())
    image_url = json_response["data"][0]["url"] 
    ```

    **C#**

    ```
   var imageGeneration = await openAIimageClient.GenerateImageAsync(
            input_text,
            new ImageGenerationOptions()
            {
                Size = GeneratedImageSize.W1024xH1024
            }
   );
   imageUrl= imageGeneration.Value.ImageUri;
    ```

1. Perhatikan bahwa kode di sisa fungsi **utama** meneruskan URL gambar dan nama file ke fungsi yang disediakan, yang mengunduh gambar yang dihasilkan dan menyimpannya sebagai file .png.

1. Gunakan perintah **CTRL+S** untuk menyimpan perubahan Anda ke file kode lalu gunakan perintah **CTRL+Q** untuk menutup editor kode sambil menjaga baris perintah cloud shell tetap terbuka.

### Jalankan aplikasi klien

1. Di panel baris perintah cloud shell, masukkan perintah berikut untuk menjalankan aplikasinya:

    **Python**

    ```
   python dalle-client.py
    ```

    **C#**

    ```
   dotnet run
    ```

1. Saat diminta, masukkan permintaan untuk gambar, seperti `Create an image of a robot eating pizza`. Setelah beberapa saat, aplikasi harusnya mengonfirmasi bahwa gambar telah disimpan.
1. Coba beberapa perintah lagi. Setelah selesai, tekan enter `quit` untuk mengakhiri program.

    > **Catatan**: Dalam aplikasi sederhana ini, kita belum menerapkan logika untuk mempertahankan riwayat percakapan; sehingga model akan memperlakukan setiap perintah sebagai permintaan baru tanpa konteks perintah sebelumnya.

1. Untuk mengunduh dan melihat gambar yang dihasilkan oleh aplikasi Anda, di toolbar untuk panel cloud shell, gunakan tombol **Unggah/Unduh file** untuk mengunduh file, lalu buka. Untuk mengunduh file, selesaikan jalur filenya di antarmuka unduhan; misalnya:

    **Python**

    /beranda/*pengguna*`/mslearn-openai/Labfiles/03-image-generation/Python/images/image_1.png`

    **C#**

    /beranda/*pengguna*`/mslearn-openai/Labfiles/03-image-generation/CSharp/images/image_1.png`

## Ringkasan

Dalam latihan ini, Anda menggunakan Azure AI Foundry dan SDK Azure OpenAI untuk membuat aplikasi klien menggunakan model DALL-E untuk menghasilkan gambar.

## Penghapusan

Setelah selesai menjelajahi DALL-E, Anda harus menghapus sumber daya yang telah Anda buat dalam latihan ini untuk menghindari biaya Azure yang tidak perlu.

1. Kembali ke tab browser yang berisi portal Azure (atau buka kembali [portal Azure](https://portal.azure.com) di `https://portal.azure.com` tab browser baru) dan lihat konten grup sumber daya tempat Anda menyebarkan sumber daya yang digunakan dalam latihan ini.
1. Pada toolbar pilih **Hapus grup sumber daya**.
1. Masukkan nama grup sumber daya untuk mengonfirmasi bahwa Anda ingin menghapusnya, dan pilih Hapus.
