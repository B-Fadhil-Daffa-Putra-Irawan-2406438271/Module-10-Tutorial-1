## Experiment 1.2: Understanding how it works

![Experiment 1.2](images/exp1-2.png)

Pada eksperimen ini, saya menambahkan println! setelah pemanggilan spawner.spawn(...). Output tambahan tersebut muncul sebelum isi async task selesai seluruhnya. Hal ini terjadi karena spawn hanya memasukkan task ke queue executor, bukan langsung menjalankan semua isi task sampai selesai. Program utama masih lanjut mengeksekusi baris setelah spawn. Task async baru benar-benar diproses ketika executor.run() dijalankan. Karena timer membutuhkan waktu dua detik, output done! muncul belakangan setelah future dibangunkan kembali oleh waker.

## Experiment 1.3: Multiple Spawn and removing drop

![Experiment 1.3 - multiple spawn](images/exp1-3-multiple-spawn.png)

Pada eksperimen multiple spawn, saya menjalankan tiga async task menggunakan spawner yang sama. Setiap task mencetak pesan awal, lalu menunggu timer dua detik, kemudian mencetak pesan akhir. Pesan awal dari beberapa task dapat muncul lebih dulu karena semua task dimasukkan ke queue executor. Setelah timer selesai, masing-masing task membangunkan executor melalui waker sehingga task dapat dipoll kembali. Hal ini menunjukkan bahwa spawning digunakan untuk mendaftarkan beberapa future agar dapat dikelola oleh executor.

![Experiment 1.3 - without drop](images/exp1-3-without-drop.png)

Ketika drop(spawner) dihapus, program tidak berhenti secara normal walaupun task sudah selesai. Hal ini terjadi karena channel pengirim task masih dianggap hidup. Executor terus menunggu kemungkinan adanya task baru dari channel. Dengan memanggil drop(spawner);, kita memberi sinyal bahwa tidak akan ada task baru yang dikirim. Setelah semua task selesai dan channel tertutup, executor dapat keluar dari loop dan program berhenti.