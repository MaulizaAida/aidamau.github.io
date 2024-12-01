# Penjelasan Kode: Migrasi Data dari PostgreSQL ke MySQL

Kode ini bertujuan untuk memindahkan data terbaru dari database PostgreSQL ke MySQL dengan menggunakan Python. Proses ini melibatkan pengambilan data dari PostgreSQL dan memasukkannya ke MySQL dengan ID yang lebih besar dari ID terakhir yang ada di MySQL.

## Langkah-langkah Kode

### 1. Membaca Konfigurasi dari File `.env`

Di bagian pertama kode, data koneksi database untuk PostgreSQL dan MySQL dibaca dari file `.env`. Ini adalah pendekatan yang baik untuk menyembunyikan informasi sensitif seperti username dan password database.

```python
import os
from dotenv import load_dotenv

load_dotenv()

db1_name = os.getenv('DB1_NAME')
db1_user = os.getenv('DB1_USER')
db1_password = os.getenv('DB1_PASSWORD')
db1_host = os.getenv('DB1_HOST')
db1_port = os.getenv('DB1_PORT')
```

### 2. Membuat Koneksi ke Database
Kode ini membuat dua koneksi ke PostgreSQL: satu untuk mengambil data, dan satu lagi untuk menyimpan data ke database tujuan di PostgreSQL.

```python
pg_conn = psycopg.connect(
    dbname=db1_name,  
    user=db1_user,      
    password=db1_password,  
    host=db1_host,  
    port=db1_port        
)

pg_conn_dest = psycopg.connect(
    dbname=db2_name,  
    user=db2_user,      
    password=db2_password,  
    host=db2_host,  
    port=db2_port        
)
```

### 3. Mengeksekusi Query untuk Menarik dan Memasukkan Data
Langkah selanjutnya adalah mengecek ID terbesar yang ada di MySQL dan menarik data terbaru dari PostgreSQL berdasarkan ID tersebut. Setelah itu, data tersebut dimasukkan ke dalam MySQL.

```python
# Cek ID terakhir di MySQL
pg_cursor_dest.execute("SELECT MAX(id) FROM mqtt_data")
max_id_mysql = pg_cursor_dest.fetchone()[0]

# Ambil data terbaru dari PostgreSQL
pg_cursor.execute("SELECT id, topic, payload, received_at FROM mqtt_data WHERE id > %s", (max_id_mysql,))

# Masukkan data ke MySQL
insert_query = """
INSERT INTO mqtt_data (id, topic, payload, received_at)
VALUES (%s, %s, %s, %s)
"""

pg_cursor_dest.executemany(insert_query, new_data)
pg_conn_dest.commit()
```

### 4. Menutup Koneksi
Setelah data berhasil dipindahkan, koneksi ke kedua database ditutup untuk menghindari kebocoran sumber daya.

```python
pg_cursor.close()
pg_conn.close()
pg_cursor_dest.close()
pg_conn_dest.close()
```

## Kesimpulan
Kode ini adalah contoh yang sangat berguna untuk mentransfer data antar database secara otomatis menggunakan Python, PostgreSQL, dan MySQL. Dengan memanfaatkan pustaka seperti psycopg dan mysql.connector, kode ini bisa dengan mudah dimodifikasi untuk berbagai tujuan migrasi data lainnya.


### Penjelasan Struktur:
1. **Judul dan Deskripsi**: Memberikan penjelasan umum mengenai tujuan kode.
2. **Langkah-langkah Kode**: Memecah kode ke dalam beberapa bagian dan menjelaskan tiap bagian, dengan setiap bagian diberi penjelasan singkat dan kode yang relevan.
3. **Kesimpulan**: Menyimpulkan bagaimana kode tersebut berguna untuk migrasi data.

