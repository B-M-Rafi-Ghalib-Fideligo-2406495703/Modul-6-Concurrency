# Modul-6-Concurrency

# Commit 1 Reflection Notes

Di dalam fungsi handle_connection , kita menggunakan BufReader untuk membungkus TcpStream agar pembacaan data dari aliran koneksi menjadi lebih efisien. Request HTTP yang masuk kemudian dibaca baris per baris menggunakan method .lines().
Karena aturan format header HTTP selalu diakhiri dengan baris kosong, kita menggunakan .take_while(|line| !line.is_empty()) agar program tahu kapan harus berhenti membaca.
Terakhir, semua baris header yang sudah dibaca tersebut dikumpulkan menjadi struktur data Vec menggunakan .collect() , lalu isinya di-print ke terminal agar kita bisa melihat detail request yang dikirimkan oleh browser.

# Commit 2 Reflection Notes

Pada milestone ini, fungsi handle_connection dimodifikasi agar dapat mengirimkan respons HTTP yang valid ke browser, bukan hanya mencetak request ke terminal.

Perubahan utama yang dilakukan:
1. Menambahkan modul s dari standard library untuk membaca file dari disk.
2. Membaca isi file hello.html menggunakan s::read_to_string("hello.html").unwrap().
3. Menyusun respons HTTP lengkap dengan format yang benar: status line (HTTP/1.1 200 OK), header Content-Length yang berisi panjang konten, dua baris CRLF sebagai pemisah antara header dan body, lalu isi HTML-nya.
4. Mengirimkan respons tersebut ke browser menggunakan stream.write_all(response.as_bytes()).unwrap().

Header Content-Length penting agar browser tahu berapa byte yang harus dibaca dari response body. Tanpa header ini, browser mungkin tidak menampilkan halaman dengan benar. Format HTTP response harus mengikuti standar: setiap header dipisahkan oleh CRLF (\r\n), dan ada satu baris kosong (dua CRLF) antara header dan body.

![Commit 2 screen capture](assets/images/commit2.png)

# Commit 3 Reflection Notes

Pada milestone ini, server diperbarui agar dapat membedakan request yang valid dan tidak valid, lalu merespons secara selektif.

Perubahan utama yang dilakukan:
1. Hanya membaca baris pertama dari request HTTP (equest_line) menggunakan .lines().next().unwrap().unwrap(), karena kita hanya perlu tahu path yang diminta.
2. Menggunakan if/else untuk mengecek isi equest_line:
   - Jika requestnya adalah GET / HTTP/1.1, server merespons dengan status 200 OK dan mengirim hello.html.
   - Untuk semua request lainnya, server merespons dengan status 404 NOT FOUND dan mengirim 404.html.
3. Menggunakan tuple (status_line, filename) agar logika pemilihan respons terpisah dari logika pengiriman respons — ini adalah bentuk refactoring sederhana yang membuat kode lebih bersih dan mudah dibaca.

Pemisahan ini penting karena pada server production, logika routing dan pengiriman respons sebaiknya tidak digabung. Dengan pola tuple ini, kode pengiriman hanya ditulis sekali dan dapat digunakan ulang untuk berbagai macam respons.

![Commit 3 screen capture - Hello page](assets/images/commit3-hello.png)
![Commit 3 screen capture - Oops page](assets/images/commit3-oops.png)
