## Experiment 1.2: Understanding how it works

![Experiment 1.2](images/exp1-2.png)

Pada eksperimen ini, saya menambahkan println! setelah pemanggilan spawner.spawn(...). Output tambahan tersebut muncul sebelum isi async task selesai seluruhnya. Hal ini terjadi karena spawn hanya memasukkan task ke queue executor, bukan langsung menjalankan semua isi task sampai selesai. Program utama masih lanjut mengeksekusi baris setelah spawn. Task async baru benar-benar diproses ketika executor.run() dijalankan. Karena timer membutuhkan waktu dua detik, output done! muncul belakangan setelah future dibangunkan kembali oleh waker.