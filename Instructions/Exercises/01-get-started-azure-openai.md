---
lab:
  title: Mulai menggunakan layanan Azure OpenAI
  status: stale
---

# Mulai menggunakan layanan Azure OpenAI

Azure OpenAI Service menghadirkan model AI generatif yang dikembangkan oleh OpenAI ke platform Azure, memungkinkan Anda mengembangkan solusi AI canggih yang mendapat manfaat dari keamanan, skalabilitas, dan integrasi layanan yang disediakan oleh platform cloud Azure. Dalam latihan ini, Anda akan mempelajari cara memulai Azure OpenAI dengan menyediakan layanannya sebagai sumber daya Azure dan menggunakan Azure AI Foundry untuk menyebarkan dan menjelajahi model AI generatif.

Dalam skenario untuk latihan ini, Anda akan melakukan peran pengembang perangkat lunak yang telah ditugaskan untuk menerapkan agen AI yang dapat menggunakan AI generatif untuk membantu organisasi pemasaran meningkatkan efektivitasnya dalam menjangkau pelanggan dan mengiklankan produk baru. Teknik yang digunakan dalam latihan dapat diterapkan pada skenario apa pun di mana organisasi ingin menggunakan model AI generatif untuk membantu karyawan menjadi lebih efektif dan produktif.

Latihan ini memakan waktu sekitar **30** menit.

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

## Menggunakan playground Obrolan

Setelah menyebarkan model, Anda dapat menggunakannya untuk menghasilkan respons berdasarkan perintah bahasa alami. Area bermain *Obrolan* di portal Azure AI Foundry menyediakan antarmuka chatbot untuk model GPT 4 dan yang lebih tinggi.

> **Catatan:** Playground *Obrolan* menggunakan API *ChatCompletions* daripada API *Penyelesaian* yang lebih lama yang digunakan oleh playground *Penyelesaian*. Playground Penyelesaian disediakan untuk kompatibilitas dengan model yang lebih lama.

1. Di bagian **Playground**, pilih halaman **Obrolan**. Halaman playground **Obrolan** terdiri dari sederet tombol dan dua panel utama (yang dapat disusun dari kanan ke kiri secara horizontal, atau dari atas ke bawah secara vertikal bergantung pada resolusi layar Anda):
    - **Konfigurasi** - digunakan untuk memilih penyebaran Anda, menentukan pesan sistem, dan mengatur parameter untuk berinteraksi dengan penyebaran Anda.
    - **sesi Obrolan** - digunakan untuk mengirimkan pesan obrolan dan melihat respons.
1. Di bagian **Penyebaran**, pastikan bahwa penyebaran model gpt-4o Anda dipilih.
1. Tinjau **Pesan sistem** default, yang seharusnya *Anda adalah asisten AI yang membantu orang menemukan informasi.* Pesan sistem disertakan dalam perintah yang dikirimkan ke model, dan menyediakan konteks untuk respons model; menetapkan ekspektasi tentang bagaimana agen AI berdasarkan model harus berinteraksi dengan pengguna.
1. Di panel **Sesi obrolan**, masukkan kueri pengguna `How can I use generative AI to help me market a new product?`

    > **Catatan**: Anda mungkin menerima respons bahwa penyebaran API belum siap. Jika ya, tunggu beberapa menit dan coba lagi.

1. Tinjau responsnya, perhatikan bahwa model telah menghasilkan jawaban bahasa alami yang kohesif dan relevan dengan kueri yang diminta.
1. Masukkan kueri pengguna `What skills do I need if I want to develop a solution to accomplish this?`.
1. Tinjau responsnya, perhatikan bahwa sesi obrolan tetap mempertahankan konteks percakapan (jadi "ini" diartikan sebagai solusi AI generatif untuk pemasaran). Kontekstualisasi ini dicapai dengan menyertakan riwayat percakapan terbaru dalam setiap pengiriman permintaan berturut-turut, sehingga permintaan yang dikirim ke model untuk kueri kedua menyertakan kueri dan respons asli serta input pengguna baru.
1. Pada bilah alat panel **Sesi obrolan**, pilih **Hapus obrolan** dan konfirmasikan bahwa Anda ingin memulai ulang sesi obrolan.
1. Masukkan kueri `Can you help me find resources to learn those skills?` dan tinjau tanggapannya, yang seharusnya merupakan jawaban bahasa alami yang valid, namun karena riwayat obrolan sebelumnya telah hilang, jawabannya mungkin adalah tentang menemukan sumber daya keterampilan umum dan bukan terkait dengan keterampilan khusus yang diperlukan untuk membangun solusi pemasaran AI generatif.

## Bereksperimenlah dengan pesan sistem, perintah, dan contoh singkat

Sejauh ini, Anda telah terlibat dalam percakapan obrolan dengan model Anda berdasarkan pesan sistem default. Anda dapat menyesuaikan pengaturan sistem untuk memiliki kontrol lebih besar terhadap jenis respons yang dihasilkan oleh model Anda.

1. Di toolbar utama, pilih **Sampel perintah**, dan gunakan templat perintah **Asisten Penulisan Pemasaran**.
1. Tinjau pesan sistem baru, yang menjelaskan bagaimana agen AI harus menggunakan model untuk merespons.
1. Di panel **Sesi obrolan**, masukkan kueri pengguna `Create an advertisement for a new scrubbing brush`.
1. Tinjau respons, yang harus menyertakan salinan iklan untuk sikat gosok. Salinannya mungkin cukup luas dan kreatif.

    Dalam skenario nyata, seorang profesional pemasaran kemungkinan besar sudah mengetahui nama produk sikat pembersih serta memiliki beberapa gagasan tentang fitur-fitur utama yang harus ditonjolkan dalam sebuah iklan. Untuk mendapatkan hasil yang paling berguna dari model AI generatif, pengguna perlu merancang perintah mereka untuk memasukkan sebanyak mungkin informasi relevan.

1. Masukkan perintah `Revise the advertisement for a scrubbing brush named "Scrubadub 2000", which is made of carbon fiber and reduces cleaning times by half compared to ordinary scrubbing brushes`.
1. Tinjau respons, yang harus mempertimbangkan informasi tambahan yang Anda berikan tentang produk sikat pembersih.

    Responsnya sekarang seharusnya lebih berguna, namun untuk memiliki kontrol yang lebih besar terhadap output model, Anda dapat memberikan satu atau beberapa contoh *few-shot* yang menjadi dasar respons.

1. Di bawah kotak teks **Pesan sistem**, perluas menu dropdown untuk **Tambahkan bagian** dan pilih **Contoh**. Kemudian ketik pesan dan respons berikut di kotak yang ditentukan:

    **Pengguna:**

    ```prompt
    Write an advertisement for the lightweight "Ultramop" mop, which uses patented absorbent materials to clean floors.
    ```

    **Asisten**:

    ```prompt
    Welcome to the future of cleaning!
    
    The Ultramop makes light work of even the dirtiest of floors. Thanks to its patented absorbent materials, it ensures a brilliant shine. Just look at these features:
    - Lightweight construction, making it easy to use.
    - High absorbency, enabling you to apply lots of clean soapy water to the floor.
    - Great low price.
    
    Check out this and other products on our website at www.contoso.com.
    ```

1. Gunakan tombol **Terapkan perubahan** untuk menyimpan contoh dan memulai sesi baru.
1. Di bagian **Sesi obrolan**, masukkan kueri pengguna `Create an advertisement for the Scrubadub 2000 - a new scrubbing brush made of carbon fiber that reduces cleaning time by half`.
1. Tinjau respons, yang seharusnya merupakan iklan baru untuk "Scrubadub 2000" yang meniru contoh "Ultramop" yang disediakan dalam pengaturan sistem.

## Bereksperimenlah dengan parameter

Anda telah menjelajahi bagaimana pesan sistem, contoh, dan perintah dapat membantu menyempurnakan respons yang diberikan oleh model. Anda juga dapat menggunakan parameter untuk mengontrol perilaku model.

1. Di panel **Konfigurasi**, pilih tab **Parameter** dan tetapkan nilai parameter berikut:
    - **Respons maksimal**: 1000
    - **Suhu**: 1

1. Di bagian **Sesi obrolan**, gunakan tombol **Hapus obrolan** untuk mengatur ulang sesi obrolan. Lalu masukkan kueri pengguna `Create an advertisement for a cleaning sponge` dan tinjau responsnya. Salinan iklan yang dihasilkan harus menyertakan maksimal 1000 token teks, dan menyertakan beberapa elemen kreatif - misalnya, model mungkin telah menemukan nama produk untuk spons dan membuat beberapa klaim tentang fitur-fiturnya.
1. Gunakan tombol **Hapus obrolan** untuk mengatur ulang sesi obrolan lagi, lalu masukkan kembali kueri yang sama seperti sebelumnya (`Create an advertisement for a cleaning sponge`) dan tinjau responsnya. Respons mungkin berbeda dengan respons sebelumnya.
1. Pada panel **Konfigurasi**, pada tab **Parameter**, ubah nilai parameter **Suhu** menjadi 0.
1. Di bagian **Sesi obrolan**, gunakan tombol **Hapus obrolan** untuk mengatur ulang sesi obrolan lagi, lalu masukkan kembali kueri yang sama seperti sebelumnya (`Create an advertisement for a cleaning sponge`) dan tinjau responsnya. Kali ini, responsnya mungkin tidak terlalu kreatif.
1. Gunakan tombol **Hapus obrolan** untuk mengatur ulang sesi obrolan sekali lagi, lalu masukkan kembali kueri yang sama seperti sebelumnya (`Create an advertisement for a cleaning sponge`) dan tinjau responsnya; yang seharusnya sangat mirip (jika tidak identik) dengan respons sebelumnya.

    Parameter **Suhu** mengontrol sejauh mana model dapat berkreasi dalam menghasilkan respons. Nilai yang rendah menghasilkan respons yang konsisten dengan sedikit variasi acak, sedangkan nilai yang tinggi mendorong model untuk menambahkan elemen kreatif pada keluarannya; yang dapat mempengaruhi keakuratan dan realisme respons.

## Menyebarkan model Anda ke aplikasi web

Sekarang setelah Anda menjelajahi beberapa kemampuan model AI generatif di playground Azure AI Foundry, Anda dapat menyebarkan aplikasi web Azure untuk menyediakan antarmuka agen AI dasar yang dapat digunakan pengguna untuk mengobrol dengan model tersebut.

> **Catatan**: Untuk beberapa pengguna, penyebaran ke aplikasi web tidak dapat disebarkan karena bug dalam templat di studio. Jika demikian, lewati bagian ini.

1. Di kanan atas halaman playground **Obrolan**, pada menu **Sebarkan ke**, pilih **Aplikasi web baru**.
1. Dalam kotak dialog **Sebarkan ke aplikasi web**, buat aplikasi web baru dengan pengaturan berikut:
    - **Nama**: *Nama unik*
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Locations**: *Wilayah tempat Anda menyediakan sumber daya Azure OpenAI*
    - **Paket harga**: Gratis (F1) - *Jika tidak tersedia, pilih Dasar (B1)*
    - **Aktifkan riwayat obrolan di aplikasi web**: **Satu** dipilih
    - **Saya menyetujui bahwa aplikasi web akan dikenakan penggunaan pada akun saya**: Dipilih
1. Sebarkan aplikasi web baru dan tunggu hingga penyebaran selesai (mungkin memerlukan waktu sekitar 10 menit)
1. Setelah aplikasi web Anda berhasil disebarkan, gunakan tombol di kanan atas halaman playground **Obrolan** untuk meluncurkan aplikasi web. Aplikasi mungkin memerlukan waktu beberapa menit untuk diluncurkan. Jika diminta, terima permintaan izin.
1. Di aplikasi web, masukkan pesan obrolan berikut:

    ```prompt
    Write an advertisement for the new "WonderWipe" cloth that attracts dust particulates and can be used to clean any household surface.
    ```

1. Tinjau responsnya.

    > **Catatan**: Anda menyebarkan *model* ke aplikasi web, tetapi penyebaran ini tidak menyertakan setelan sistem dan parameter yang Anda tetapkan di playground; jadi responsnya mungkin tidak mencerminkan contoh yang Anda berikan di playground. Dalam skenario nyata, Anda akan menambahkan logika ke aplikasi Anda untuk mengubah perintah sehingga mencakup data kontekstual yang sesuai untuk jenis respons yang ingin Anda hasilkan. Penyesuaian semacam ini berada di luar cakupan latihan tingkat pengantar ini, namun Anda dapat mempelajari teknik rekayasa cepat dan API Azure OpenAI dalam latihan lain dan dokumentasi produk.

1. Setelah Anda selesai bereksperimen dengan model Anda di aplikasi web, tutup tab aplikasi web di browser Anda untuk kembali ke portal Azure AI Foundry.

## Penghapusan

Setelah Anda selesai dengan sumber daya Azure OpenAI, ingatlah untuk menghapus penyebaran atau seluruh sumber daya pada **portal Microsoft Azure** di `https://portal.azure.com`.
