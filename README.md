# LAPORAN TUGAS AKHIR
# PERANCANGAN DAN IMPLEMENTASI DATABASE SISTEM E-COMMERCE

---

**Mata Kuliah:** Basis Data  
**Dosen Pengampu:** [Nama Dosen]  
**Disusun Oleh:** [Nama Mahasiswa]  
**NIM:** [NIM]  
**Kelas:** [Kelas]  
**Tanggal:** [Tanggal Pengumpulan]

---


## DAFTAR ISI

1. [BAB 1 — PENDAHULUAN](#bab-1--pendahuluan)
   - 1.1 Latar Belakang
   - 1.2 Deskripsi Sistem E-Commerce
   - 1.3 Ruang Lingkup Database
   - 1.4 Tujuan Perancangan

2. [BAB 2 — ENHANCED ENTITY-RELATIONSHIP DIAGRAM (EERD)](#bab-2--enhanced-entity-relationship-diagram-eerd)
   - 2.1 Daftar Entitas dan Atribut
   - 2.2 Daftar Relasi dan Kardinalitas
   - 2.3 Weak Entity
   - 2.4 Specialization (Overlap, Total)
   - 2.5 Union Type (Category)
   - 2.6 Diagram EER Lengkap

3. [BAB 3 — SKEMA RELASIONAL](#bab-3--skema-relasional)
   - 3.1 Proses Mapping EER ke Relational Schema
   - 3.2 Daftar Tabel Hasil Mapping
   - 3.3 Penjelasan Foreign Key dan Constraint
   - 3.4 Normalisasi

4. [BAB 4 — IMPLEMENTASI SQL](#bab-4--implementasi-sql)
   - 4.1 Data Definition Language (DDL)
   - 4.2 Data Manipulation Language (DML)
   - 4.3 Constraint dan Referential Integrity

5. [BAB 5 — QUERY SQL](#bab-5--query-sql)
   - 5.1 Query dengan WHERE Clause
   - 5.2 Query dengan ORDER BY dan DISTINCT
   - 5.3 Query dengan Aggregate Functions
   - 5.4 Query dengan GROUP BY dan HAVING
   - 5.5 Query dengan JOIN
   - 5.6 Query dengan SUBQUERY
   - 5.7 Query UPDATE dan DELETE

6. [BAB 6 — KESIMPULAN](#bab-6--kesimpulan)
   - 6.1 Ringkasan Hasil Perancangan
   - 6.2 Insight dan Pembelajaran
   - 6.3 Kendala yang Dihadapi
   - 6.4 Saran Pengembangan

---


# BAB 1 — PENDAHULUAN

## 1.1 Latar Belakang

Perkembangan teknologi informasi telah mengubah cara masyarakat berbelanja, dari model konvensional ke platform e-commerce digital. Platform seperti Tokopedia, Shopee, dan Lazada telah menjadi bagian integral dari kehidupan sehari-hari, memfasilitasi jutaan transaksi antara penjual dan pembeli. Di balik kesuksesan platform-platform ini, terdapat sistem database yang kompleks dan terstruktur dengan baik untuk mengelola data pengguna, produk, transaksi, pengiriman, dan berbagai aspek bisnis lainnya.

Perancangan database yang baik menjadi fondasi penting dalam membangun sistem e-commerce yang scalable, reliable, dan maintainable. Database harus mampu menangani berbagai entitas yang saling berelasi, seperti customer, seller, produk, order, pembayaran, pengiriman, review, dan voucher. Selain itu, database juga harus mendukung fitur-fitur modern seperti sistem rating, loyalty points, multi-kategori produk, dan voucher dari berbagai sumber (seller maupun platform).

Tugas akhir ini bertujuan untuk merancang dan mengimplementasikan database sistem e-commerce yang komprehensif menggunakan pendekatan Enhanced Entity-Relationship (EER) Diagram, kemudian memetakannya ke skema relasional, dan mengimplementasikannya dalam SQL. Perancangan ini mencakup berbagai konsep advanced database seperti specialization, weak entity, union type, serta berbagai jenis atribut (composite, multivalued, derived).

## 1.2 Deskripsi Sistem E-Commerce

Sistem e-commerce yang dirancang dalam tugas ini adalah platform marketplace yang menghubungkan penjual (seller) dengan pembeli (customer). Sistem ini memiliki karakteristik sebagai berikut:

### Karakteristik Utama:

1. **Multi-User System**
   - Pengguna dapat mendaftar sebagai customer (pembeli), seller (penjual), atau keduanya (overlap specialization)
   - Setiap user memiliki profil lengkap dengan data personal dan kontak

2. **Katalog Produk**
   - Seller dapat mendaftarkan produk dengan informasi lengkap (nama, harga, stok, gambar)
   - Produk dapat masuk ke banyak kategori (relasi many-to-many)
   - Sistem rating dan review untuk setiap produk

3. **Sistem Transaksi**
   - Customer dapat membuat order yang berisi banyak produk (order detail)
   - Setiap order memiliki satu record pembayaran dengan berbagai metode (e-wallet, transfer bank, COD, kartu kredit)
   - Setiap order memiliki satu record pengiriman yang ditangani oleh perusahaan kurir pihak ketiga

4. **Logistik dan Pengiriman**
   - Tidak ada self-shipping — semua pengiriman ditangani oleh courier company (JNE, SiCepat, J&T, dll)
   - Tracking pengiriman dengan nomor resi dan status real-time

5. **Sistem Promosi**
   - Voucher dapat diterbitkan oleh seller (voucher toko) atau platform admin (voucher platform)
   - Customer dapat menggunakan voucher untuk mendapatkan diskon
   - Voucher bersifat multi-use (dapat digunakan oleh banyak customer)

6. **Loyalty Program**
   - Customer mendapatkan loyalty points dari setiap transaksi
   - Points dapat digunakan untuk reward atau promosi khusus

## 1.3 Ruang Lingkup Database

Database yang dirancang mencakup 19 tabel yang terbagi dalam beberapa kategori:

### 1. Tabel Entitas Utama (Strong Entity)
- **USER**: Superclass untuk semua pengguna terdaftar
- **CUSTOMER**: Subclass untuk pembeli
- **SELLER**: Subclass untuk penjual
- **PRODUCT**: Produk yang dijual
- **CATEGORY**: Kategori produk
- **ORDER**: Pesanan customer
- **PAYMENT**: Data pembayaran
- **SHIPMENT**: Data pengiriman
- **REVIEW**: Ulasan produk
- **COURIER_COMPANY**: Perusahaan kurir
- **PLATFORM_ADMIN**: Admin platform
- **VOUCHER**: Voucher promosi

### 2. Tabel Weak Entity
- **ORDER_DETAIL**: Detail item dalam order (dependent pada ORDER)

### 3. Tabel Junction (M:N)
- **PRODUCT_CATEGORY**: Relasi produk dengan kategori
- **CUSTOMER_VOUCHER**: Penggunaan voucher oleh customer

### 4. Tabel Multivalued Attribute
- **USER_PHONE**: Nomor telepon user (multivalued)
- **PRODUCT_IMAGE**: Gambar produk (multivalued)
- **REVIEW_PHOTO**: Foto ulasan (multivalued)

### 5. Tabel Union Type
- **VOUCHER_ISSUER**: Union type dari SELLER dan PLATFORM_ADMIN

## 1.4 Tujuan Perancangan

Tujuan dari perancangan database ini adalah:

1. **Memenuhi Requirement Akademik**
   - Minimal 8 entitas (tercapai: 13 entitas)
   - Weak entity dengan relasi identifikasi
   - Specialization dengan constraint overlap dan total
   - Union type yang valid secara konsep
   - Semua jenis atribut (composite, multivalued, derived)

2. **Mencerminkan Realitas Bisnis**
   - Desain yang sesuai dengan cara kerja platform e-commerce nyata
   - Struktur data yang mendukung fitur-fitur modern
   - Skenario penggunaan yang realistis

3. **Implementasi yang Efisien**
   - Normalisasi hingga 3NF untuk menghindari redundansi
   - Constraint yang menjaga integritas data
   - Index untuk optimasi query
   - Foreign key dengan action yang tepat (CASCADE, RESTRICT)

4. **Skalabilitas dan Maintainability**
   - Struktur yang mudah dikembangkan
   - Pemisahan concern yang jelas antar entitas
   - Dokumentasi yang lengkap

---


# BAB 2 — ENHANCED ENTITY-RELATIONSHIP DIAGRAM (EERD)

## 2.1 Daftar Entitas dan Atribut

Sistem e-commerce ini terdiri dari 13 entitas dengan berbagai jenis atribut.

### 2.1.1 USER (Superclass)

**Deskripsi:** Entitas superclass yang merepresentasikan semua pengguna terdaftar di platform.

**Atribut:**
- `user_id` — **Key Attribute** (Primary Key)
- `nama` — **Composite Attribute** → dipecah menjadi:
  - `nama_depan`
  - `nama_belakang`
- `no_telepon` — **Multivalued Attribute** (satu user bisa punya banyak nomor)
- `email` — Simple Attribute
- `tgl_daftar` — Simple Attribute

**Specialization:** USER memiliki dua subclass (CUSTOMER dan SELLER) dengan constraint:
- **Overlap (o):** Satu user bisa menjadi CUSTOMER dan SELLER sekaligus
- **Total (t):** Setiap USER harus menjadi minimal satu subclass

### 2.1.2 CUSTOMER (Subclass dari USER)

**Deskripsi:** Subclass yang merepresentasikan pengguna yang berbelanja di platform.

**Atribut:**
- `customer_id` — Key Attribute
- `loyalty_points` — Simple Attribute
- `total_belanja` — **Derived Attribute** (dihitung dari SUM order yang selesai)

**Mewarisi dari USER:** user_id, nama_depan, nama_belakang, email, tgl_daftar

### 2.1.3 SELLER (Subclass dari USER)

**Deskripsi:** Subclass yang merepresentasikan pengguna yang berjualan di platform.

**Atribut:**
- `seller_id` — Key Attribute
- `nama_toko` — Simple Attribute
- `rating_toko` — **Derived Attribute** (dihitung dari AVG rating produk)

**Mewarisi dari USER:** user_id, nama_depan, nama_belakang, email, tgl_daftar

**Peran Tambahan:** SELLER adalah member class dari union type VOUCHER_ISSUER

### 2.1.4 PRODUCT

**Deskripsi:** Produk yang dijual oleh seller di platform.

**Atribut:**
- `product_id` — Key Attribute
- `nama_produk` — Simple Attribute
- `harga` — Simple Attribute
- `stok` — Simple Attribute
- `gambar` — **Multivalued Attribute** (satu produk bisa punya banyak gambar)
- `rating_avg` — **Derived Attribute** (dihitung dari AVG review)

### 2.1.5 CATEGORY

**Deskripsi:** Kategori produk dalam sistem (Elektronik, Fashion, Makanan, dll).

**Atribut:**
- `category_id` — Key Attribute
- `nama_kategori` — Simple Attribute
- `deskripsi` — Simple Attribute

### 2.1.6 ORDER

**Deskripsi:** Pesanan yang dibuat oleh customer.

**Atribut:**
- `order_id` — Key Attribute
- `status` — Simple Attribute (pending, dikonfirmasi, dikirim, selesai, dibatalkan)
- `tgl_order` — Simple Attribute
- `total_harga` — **Derived Attribute** (dihitung dari SUM ORDER_DETAIL.subtotal)

### 2.1.7 ORDER_DETAIL (Weak Entity)

**Deskripsi:** Detail item dalam setiap order. Weak entity dengan owner ORDER.

**Atribut:**
- `no_urut` — **Partial Key** (digambar dengan garis putus-putus)
- `harga_satuan` — Simple Attribute
- `jumlah` — Simple Attribute
- `subtotal` — **Derived Attribute** (harga_satuan × jumlah)

**Owner:** ORDER (relasi identifikasi dengan double diamond)

### 2.1.8 PAYMENT

**Deskripsi:** Data pembayaran untuk setiap order.

**Atribut:**
- `payment_id` — Key Attribute
- `metode` — Simple Attribute (transfer_bank, e_wallet, cod, kartu_kredit)
- `status_pay` — Simple Attribute (pending, berhasil, gagal)
- `tgl_bayar` — Simple Attribute
- `jumlah_bayar` — Simple Attribute

### 2.1.9 SHIPMENT

**Deskripsi:** Data pengiriman untuk setiap order.

**Atribut:**
- `shipment_id` — Key Attribute
- `no_resi` — Simple Attribute
- `tgl_kirim` — Simple Attribute
- `status_kirim` — Simple Attribute (pending, dalam_perjalanan, tiba, gagal)
- `est_tiba` — **Derived Attribute** (tgl_kirim + estimasi_hari dari courier)

### 2.1.10 REVIEW

**Deskripsi:** Ulasan produk yang ditulis oleh customer.

**Atribut:**
- `review_id` — Key Attribute
- `rating` — Simple Attribute (1-5)
- `komentar` — Simple Attribute
- `tgl_review` — Simple Attribute
- `foto_ulasan` — **Multivalued Attribute** (satu review bisa punya banyak foto)

### 2.1.11 COURIER_COMPANY

**Deskripsi:** Perusahaan kurir pihak ketiga yang menyediakan layanan pengiriman.

**Atribut:**
- `company_id` — Key Attribute
- `nama_kurir` — Simple Attribute
- `kode_kurir` — Simple Attribute

### 2.1.12 COURIER_SERVICE

**Deskripsi:** Layanan spesifik yang ditawarkan oleh courier company (JNE REG, JNE YES, SiCepat HALU, dll).

**Atribut:**
- `service_id` — Key Attribute
- `nama_layanan` — Simple Attribute
- `tarif_per_kg` — Simple Attribute
- `estimasi_hari` — Simple Attribute

**Relasi:** COURIER_SERVICE berelasi N:1 dengan COURIER_COMPANY (satu company punya banyak layanan)

### 2.1.13 PLATFORM_ADMIN

**Deskripsi:** Admin platform yang mengelola sistem.

**Atribut:**
- `admin_id` — Key Attribute
- `nama_admin` — Simple Attribute
- `level_akses` — Simple Attribute (super_admin, admin, moderator)
- `divisi` — Simple Attribute

**Peran Tambahan:** PLATFORM_ADMIN adalah member class dari union type VOUCHER_ISSUER

### 2.1.14 VOUCHER

**Deskripsi:** Voucher/promosi yang diterbitkan oleh SELLER atau PLATFORM_ADMIN.

**Atribut:**
- `voucher_id` — Key Attribute
- `kode_voucher` — Simple Attribute
- `nilai_diskon` — Simple Attribute
- `tgl_mulai` — Simple Attribute
- `tgl_berakhir` — Simple Attribute
- `tipe` — Simple Attribute (persentase, nominal)

## 2.2 Daftar Relasi dan Kardinalitas

| No | Relasi | Dari | Kardinalitas | Ke | Keterangan |
|----|--------|------|--------------|-----|------------|
| 1 | MEMBUAT | CUSTOMER | 1 : N | ORDER | Satu customer bisa membuat banyak order |
| 2 | MEMILIKI | ORDER | 1 : N | ORDER_DETAIL | **Relasi identifikasi** (weak entity) |
| 3 | MERUJUK | ORDER_DETAIL | N : 1 | PRODUCT | Banyak order detail merujuk satu produk |
| 4 | MENJUAL | SELLER | 1 : N | PRODUCT | Satu seller menjual banyak produk |
| 5 | TERMASUK | PRODUCT | M : N | CATEGORY | Produk bisa masuk banyak kategori |
| 6 | MEMBAYAR | ORDER | 1 : 1 | PAYMENT | Setiap order punya tepat satu pembayaran |
| 7 | MENGIRIM | ORDER | 1 : 1 | SHIPMENT | Setiap order punya tepat satu pengiriman |
| 8 | MENAWARKAN | COURIER_COMPANY | 1 : N | COURIER_SERVICE | Satu company menawarkan banyak layanan |
| 9 | DITANGANI | SHIPMENT | N : 1 | COURIER_SERVICE | Banyak shipment menggunakan satu layanan |
| 10 | DITULIS | CUSTOMER | 1 : N | REVIEW | Satu customer bisa tulis banyak review |
| 11 | UNTUK | PRODUCT | 1 : N | REVIEW | Satu produk menerima banyak review |
| 12 | MENGGUNAKAN | CUSTOMER | M : N | VOUCHER | Customer bisa pakai banyak voucher |
| 13 | DITERBITKAN_OLEH | VOUCHER | N : 1 | VOUCHER_ISSUER | Voucher diterbitkan oleh SELLER atau ADMIN |

## 2.3 Weak Entity

### ORDER_DETAIL

**Mengapa ORDER_DETAIL adalah Weak Entity?**

1. **Tidak Memiliki Key Sendiri yang Unik**
   - `no_urut` saja tidak cukup untuk mengidentifikasi ORDER_DETAIL secara unik
   - Contoh: `no_urut = 1` bisa ada di banyak order berbeda

2. **Bergantung pada Owner Entity (ORDER)**
   - ORDER_DETAIL tidak bisa eksis tanpa ORDER
   - Jika ORDER dihapus, semua ORDER_DETAIL terkait harus ikut terhapus (ON DELETE CASCADE)

3. **Primary Key Gabungan**
   - PK ORDER_DETAIL = (order_id, no_urut)
   - `order_id` berasal dari owner (ORDER)
   - `no_urut` adalah partial key (digambar dengan garis putus-putus)

4. **Relasi Identifikasi**
   - Relasi MEMILIKI antara ORDER dan ORDER_DETAIL adalah relasi identifikasi
   - Digambar dengan **double diamond** (◆◆)
   - Total participation dari sisi ORDER_DETAIL (garis tebal)

**Contoh Data:**
```
ORDER (order_id=1) → ORDER_DETAIL (1,1), (1,2), (1,3)
ORDER (order_id=2) → ORDER_DETAIL (2,1), (2,2)
```

## 2.4 Specialization (Overlap, Total)

### USER → CUSTOMER, SELLER

**Constraint Specialization:**

1. **Overlap (o)**
   - Satu instance superclass bisa menjadi anggota lebih dari satu subclass
   - Contoh: Ahmad adalah CUSTOMER (berbelanja) sekaligus SELLER (berjualan)
   - Digambar dengan huruf **"o"** di dalam lingkaran specialization

2. **Total (t)**
   - Setiap instance superclass harus menjadi anggota minimal satu subclass
   - Tidak ada USER yang bukan CUSTOMER atau SELLER
   - Digambar dengan huruf **"t"** di dalam lingkaran specialization
   - Garis dari superclass ke lingkaran adalah **garis tebal** (total participation)

**Mengapa Overlap?**
- Dalam e-commerce nyata, banyak pengguna yang awalnya customer kemudian membuka toko (menjadi seller)
- Contoh: Pengguna Tokopedia bisa berbelanja sekaligus berjualan
- Overlap memungkinkan fleksibilitas ini

**Mengapa Total?**
- Setiap pengguna yang mendaftar harus memiliki peran (customer atau seller)
- Tidak ada user "kosong" tanpa peran
- Ini memastikan setiap user memiliki fungsi dalam sistem

**Implementasi:**
- Menggunakan pendekatan **Class Hierarchy**
- Tabel USER (superclass) + Tabel CUSTOMER + Tabel SELLER
- FK dari CUSTOMER.user_id dan SELLER.user_id ke USER.user_id
- Satu user_id bisa muncul di kedua tabel subclass (overlap)

## 2.5 Union Type (Category)

### VOUCHER_ISSUER = SELLER ∪ PLATFORM_ADMIN

**Definisi:**
VOUCHER_ISSUER adalah union type yang merepresentasikan pihak yang dapat menerbitkan voucher dalam sistem e-commerce.

**Member Classes:**
1. **SELLER** — Menerbitkan voucher toko (contoh: "Diskon 20% Toko Elektronik Jaya")
2. **PLATFORM_ADMIN** — Menerbitkan voucher platform (contoh: "Cashback 50% ShopeePay")

**Mengapa Union Type Valid?**

| Kriteria Validitas | Status | Penjelasan |
|--------------------|--------|------------|
| Member classes berbeda fundamental | ✓ | SELLER adalah user yang berjualan; PLATFORM_ADMIN adalah pengelola sistem |
| Tidak berbagi superclass | ✓ | SELLER mewarisi dari USER; PLATFORM_ADMIN adalah entitas independen |
| Ada entitas yang perlu berelasi | ✓ | VOUCHER harus berelasi dengan penerbitnya (SELLER atau ADMIN) |
| Constraint disjoint | ✓ | Satu voucher diterbitkan oleh SELLER **atau** ADMIN, tidak keduanya |
| Partial participation | ✓ | Tidak semua SELLER/ADMIN menerbitkan voucher |

**Contoh Penggunaan:**

1. **Voucher Toko (dari SELLER)**
   - Kode: `ELEKTRONIK20`
   - Nilai: Rp 200.000
   - Penerbit: Elektronik Jaya (seller_id = 1)
   - Berlaku: Hanya di toko Elektronik Jaya

2. **Voucher Platform (dari PLATFORM_ADMIN)**
   - Kode: `CASHBACK50`
   - Nilai: Rp 50.000
   - Penerbit: Admin Marketing (admin_id = 2)
   - Berlaku: Semua toko di platform

**Implementasi:**
- Tabel VOUCHER_ISSUER dengan surrogate key `issuer_id`
- Discriminator `issuer_type` (SELLER atau ADMIN)
- FK `seller_id` dan `admin_id` (salah satu harus NULL)
- CHECK constraint untuk enforce disjoint

**Mengapa Bukan Relasi Biasa?**
- Tanpa union type, VOUCHER harus punya dua FK nullable (seller_id dan admin_id)
- Union type memberikan abstraksi yang lebih bersih
- Memudahkan query: "Siapa yang menerbitkan voucher ini?" tanpa perlu COALESCE

## 2.6 Diagram EER Lengkap

Diagram EER lengkap dapat dilihat pada file `eer_union_valid.html` yang telah disertakan. Diagram tersebut menampilkan:

- ✓ 13 Entitas (strong entity)
- ✓ 1 Weak Entity (ORDER_DETAIL dengan double diamond)
- ✓ Specialization dengan constraint overlap (o) dan total (t)
- ✓ Union Type (VOUCHER_ISSUER) dengan simbol ∪
- ✓ Semua jenis atribut:
  - Key attribute (underline)
  - Composite attribute (nama → nama_depan, nama_belakang)
  - Multivalued attribute (no_telepon, gambar, foto_ulasan)
  - Derived attribute (total_belanja, rating_avg, subtotal)
- ✓ Relasi dengan kardinalitas lengkap (1:1, 1:N, M:N)
- ✓ Total participation (garis tebal)
- ✓ Partial participation (garis tunggal)

**Catatan Penting:**
- Union COURIER yang sebelumnya ada (SELLER ∪ COURIER_COMPANY) telah dihapus karena tidak valid secara konsep
- SHIPMENT sekarang berelasi langsung dengan COURIER_COMPANY (N:1)
- Desain ini lebih logis dan sesuai dengan asumsi "no self-shipping"

---


# BAB 3 — SKEMA RELASIONAL

## 3.1 Proses Mapping EER ke Relational Schema

Proses mapping dari EER Diagram ke skema relasional mengikuti aturan standar dengan urutan yang sistematis untuk memastikan semua konsep EER terimplementasi dengan benar.

### 3.1.1 Urutan Mapping

1. **Strong Entity** → Setiap strong entity menjadi satu tabel dengan PK
2. **Weak Entity** → Tabel dengan PK gabungan (PK owner + partial key)
3. **Relasi 1:1** → FK di salah satu sisi dengan constraint UNIQUE
4. **Relasi 1:N** → FK di sisi N (many side)
5. **Relasi M:N** → Junction table dengan PK gabungan
6. **Multivalued Attribute** → Tabel terpisah dengan PK gabungan
7. **Specialization** → Class Hierarchy (tabel terpisah untuk setiap subclass)
8. **Union Type** → Tabel surrogate dengan discriminator

### 3.1.2 Mapping Strong Entity

Setiap strong entity menjadi satu tabel. Atribut composite dipecah, multivalued dimapping terpisah, derived tidak disimpan.

**Contoh: PRODUCT**
```
EER: PRODUCT(product_id, nama_produk, harga, stok, gambar*, rating_avg')
     * = multivalued, ' = derived

Relational:
PRODUCT(product_id, nama_produk, harga, stok, seller_id)
  PK: product_id
  FK: seller_id REFERENCES SELLER(seller_id)
```

**Penjelasan:**
- `gambar` (multivalued) → tabel PRODUCT_IMAGE
- `rating_avg` (derived) → tidak disimpan, dihitung saat query
- `seller_id` (FK) → dari relasi MENJUAL (1:N)

### 3.1.3 Mapping Weak Entity

Weak entity menjadi tabel dengan PK gabungan: PK owner + partial key.

**Contoh: ORDER_DETAIL**
```
EER: ORDER_DETAIL(no_urut, harga_satuan, jumlah, subtotal')
     Owner: ORDER
     Partial Key: no_urut (garis putus-putus)

Relational:
ORDER_DETAIL(order_id, no_urut, harga_satuan, jumlah, product_id)
  PK: (order_id, no_urut)
  FK: order_id REFERENCES ORDER(order_id) ON DELETE CASCADE
  FK: product_id REFERENCES PRODUCT(product_id)
```

**Penjelasan:**
- PK gabungan: `order_id` (dari owner) + `no_urut` (partial key)
- ON DELETE CASCADE: jika ORDER dihapus, ORDER_DETAIL ikut terhapus
- `subtotal` (derived) tidak disimpan

### 3.1.4 Mapping Relasi 1:1

FK ditempatkan di salah satu sisi (pilih sisi dengan total participation) dengan constraint UNIQUE.

**Contoh: ORDER 1:1 PAYMENT**
```
EER: ORDER —[MEMBAYAR]— PAYMENT (1:1)

Relational:
PAYMENT(..., order_id)
  FK: order_id REFERENCES ORDER(order_id) UNIQUE
```

**Penjelasan:**
- FK ditempatkan di PAYMENT (setiap payment pasti terkait order)
- UNIQUE constraint memastikan satu order = satu payment

### 3.1.5 Mapping Relasi 1:N

FK ditempatkan di sisi N (many side).

**Contoh: SELLER 1:N PRODUCT**
```
EER: SELLER —[MENJUAL]— PRODUCT (1:N)

Relational:
PRODUCT(..., seller_id)
  FK: seller_id REFERENCES SELLER(seller_id)
```

**Penjelasan:**
- Satu seller bisa menjual banyak produk
- FK di sisi N (PRODUCT)

### 3.1.6 Mapping Relasi M:N

Buat junction table dengan PK gabungan dari kedua FK.

**Contoh: PRODUCT M:N CATEGORY**
```
EER: PRODUCT —[TERMASUK]— CATEGORY (M:N)

Relational:
PRODUCT_CATEGORY(product_id, category_id)
  PK: (product_id, category_id)
  FK: product_id REFERENCES PRODUCT(product_id)
  FK: category_id REFERENCES CATEGORY(category_id)
```

**Penjelasan:**
- Junction table untuk relasi many-to-many
- PK gabungan mencegah duplikasi

### 3.1.7 Mapping Multivalued Attribute

Buat tabel terpisah dengan PK gabungan: PK entity + atribut multivalued (atau surrogate).

**Contoh: USER.no_telepon**
```
EER: USER(user_id, nama, no_telepon*, email)
     * = multivalued

Relational:
USER(user_id, nama_depan, nama_belakang, email, tgl_daftar)
USER_PHONE(user_id, no_telepon)
  PK: (user_id, no_telepon)
  FK: user_id REFERENCES USER(user_id) ON DELETE CASCADE
```

**Penjelasan:**
- Satu user bisa punya banyak nomor telepon
- PK gabungan mencegah duplikasi nomor untuk user yang sama

### 3.1.8 Mapping Specialization (Overlap, Total)

Menggunakan pendekatan **Class Hierarchy** — tabel terpisah untuk superclass dan setiap subclass.

**Contoh: USER → CUSTOMER, SELLER**
```
EER: USER (superclass)
     ├─ CUSTOMER (subclass)
     └─ SELLER (subclass)
     Constraint: Overlap (o), Total (t)

Relational:
USER(user_id, nama_depan, nama_belakang, email, tgl_daftar)
  PK: user_id

CUSTOMER(customer_id, loyalty_points, user_id)
  PK: customer_id
  FK: user_id REFERENCES USER(user_id) ON DELETE CASCADE

SELLER(seller_id, nama_toko, user_id)
  PK: seller_id
  FK: user_id REFERENCES USER(user_id) ON DELETE CASCADE
```

**Penjelasan:**
- **Overlap:** Satu user_id bisa muncul di CUSTOMER dan SELLER
- **Total:** Setiap user_id harus ada di minimal satu subclass (enforced di aplikasi)
- Class Hierarchy dipilih karena overlap (Single Table akan banyak NULL)

### 3.1.9 Mapping Union Type

Buat tabel surrogate dengan discriminator dan FK ke setiap member class.

**Contoh: VOUCHER_ISSUER = SELLER ∪ PLATFORM_ADMIN**
```
EER: VOUCHER_ISSUER (union type)
     Member classes: SELLER, PLATFORM_ADMIN
     Constraint: Disjoint, Partial

Relational:
VOUCHER_ISSUER(issuer_id, issuer_type, seller_id, admin_id)
  PK: issuer_id
  FK: seller_id REFERENCES SELLER(seller_id)
  FK: admin_id REFERENCES PLATFORM_ADMIN(admin_id)
  CHECK: (issuer_type='SELLER' AND seller_id IS NOT NULL AND admin_id IS NULL)
      OR (issuer_type='ADMIN' AND admin_id IS NOT NULL AND seller_id IS NULL)
```

**Penjelasan:**
- `issuer_id`: surrogate key (PK)
- `issuer_type`: discriminator ('SELLER' atau 'ADMIN')
- CHECK constraint: hanya satu FK yang terisi (disjoint)
- Partial: tidak semua SELLER/ADMIN menerbitkan voucher

## 3.2 Daftar Tabel Hasil Mapping

### 3.2.1 Tabel Strong Entity (12 tabel)

1. **USER**(user_id, nama_depan, nama_belakang, email, tgl_daftar)
2. **CUSTOMER**(customer_id, loyalty_points, user_id)
3. **SELLER**(seller_id, nama_toko, user_id)
4. **PRODUCT**(product_id, nama_produk, harga, stok, seller_id)
5. **CATEGORY**(category_id, nama_kategori, deskripsi)
6. **ORDER**(order_id, status, tgl_order, customer_id)
7. **PAYMENT**(payment_id, metode, status_pay, tgl_bayar, jumlah_bayar, order_id)
8. **SHIPMENT**(shipment_id, no_resi, tgl_kirim, status_kirim, order_id, company_id)
9. **REVIEW**(review_id, rating, komentar, tgl_review, customer_id, product_id)
10. **COURIER_COMPANY**(company_id, nama_kurir, kode_kurir, estimasi_hari_default)
11. **VOUCHER**(voucher_id, kode_voucher, nilai_diskon, tgl_mulai, tgl_berakhir, tipe, issuer_id)
12. **PLATFORM_ADMIN**(admin_id, nama_admin, level_akses, divisi)

### 3.2.2 Tabel Weak Entity (1 tabel)

13. **ORDER_DETAIL**(order_id, no_urut, harga_satuan, jumlah, product_id)
    - PK: (order_id, no_urut)

### 3.2.3 Tabel Junction M:N (2 tabel)

14. **PRODUCT_CATEGORY**(product_id, category_id)
15. **CUSTOMER_VOUCHER**(customer_id, voucher_id, tgl_pakai, order_id)

### 3.2.4 Tabel Multivalued (3 tabel)

16. **USER_PHONE**(user_id, no_telepon)
17. **PRODUCT_IMAGE**(product_id, gambar_url, urutan)
18. **REVIEW_PHOTO**(review_id, foto_url, urutan)

### 3.2.5 Tabel Union Type (1 tabel)

19. **VOUCHER_ISSUER**(issuer_id, issuer_type, seller_id, admin_id)

**Total: 19 tabel**

## 3.3 Penjelasan Foreign Key dan Constraint

### 3.3.1 Foreign Key dengan ON DELETE CASCADE

Digunakan untuk weak entity dan tabel multivalued — jika parent dihapus, child ikut terhapus.

**Contoh:**
```sql
-- Weak Entity
ORDER_DETAIL.order_id → ORDER.order_id ON DELETE CASCADE

-- Multivalued
USER_PHONE.user_id → USER.user_id ON DELETE CASCADE
PRODUCT_IMAGE.product_id → PRODUCT.product_id ON DELETE CASCADE
REVIEW_PHOTO.review_id → REVIEW.review_id ON DELETE CASCADE

-- Specialization
CUSTOMER.user_id → USER.user_id ON DELETE CASCADE
SELLER.user_id → USER.user_id ON DELETE CASCADE
```

### 3.3.2 Foreign Key dengan ON DELETE RESTRICT

Digunakan untuk mencegah penghapusan data yang masih direferensi (default behavior).

**Contoh:**
```sql
-- Tidak bisa hapus PRODUCT jika masih ada ORDER_DETAIL yang merujuk
ORDER_DETAIL.product_id → PRODUCT.product_id ON DELETE RESTRICT

-- Tidak bisa hapus ORDER jika masih ada PAYMENT
PAYMENT.order_id → ORDER.order_id ON DELETE RESTRICT

-- Tidak bisa hapus COURIER_COMPANY jika masih ada SHIPMENT
SHIPMENT.company_id → COURIER_COMPANY.company_id ON DELETE RESTRICT
```

### 3.3.3 UNIQUE Constraint

Digunakan untuk relasi 1:1 dan atribut yang harus unik.

**Contoh:**
```sql
-- Relasi 1:1
PAYMENT.order_id UNIQUE  -- Satu order = satu payment
SHIPMENT.order_id UNIQUE -- Satu order = satu shipment

-- Atribut unik
USER.email UNIQUE
SELLER.nama_toko UNIQUE
VOUCHER.kode_voucher UNIQUE
COURIER_COMPANY.nama_kurir UNIQUE
```

### 3.3.4 CHECK Constraint

Digunakan untuk validasi nilai atribut.

**Contoh:**
```sql
-- Validasi range
REVIEW.rating CHECK (rating BETWEEN 1 AND 5)
PRODUCT.harga CHECK (harga >= 0)
CUSTOMER.loyalty_points CHECK (loyalty_points >= 0)

-- Validasi format
USER.email CHECK (email LIKE '%_@__%.__%')

-- Validasi tanggal
VOUCHER CHECK (tgl_berakhir >= tgl_mulai)

-- Union type disjoint
VOUCHER_ISSUER CHECK (
  (issuer_type='SELLER' AND seller_id IS NOT NULL AND admin_id IS NULL) OR
  (issuer_type='ADMIN' AND admin_id IS NOT NULL AND seller_id IS NULL)
)
```

### 3.3.5 NOT NULL Constraint

Digunakan untuk atribut yang wajib diisi.

**Contoh:**
```sql
USER.nama_depan NOT NULL
USER.email NOT NULL
PRODUCT.nama_produk NOT NULL
PRODUCT.harga NOT NULL
ORDER.status NOT NULL
```

### 3.3.6 DEFAULT Constraint

Digunakan untuk nilai default atribut.

**Contoh:**
```sql
USER.tgl_daftar DEFAULT CURRENT_DATE
CUSTOMER.loyalty_points DEFAULT 0
ORDER.status DEFAULT 'pending'
PAYMENT.status_pay DEFAULT 'pending'
PRODUCT.stok DEFAULT 0
```

## 3.4 Normalisasi

Semua tabel dalam skema relasional ini sudah dalam bentuk **Third Normal Form (3NF)**.

### 3.4.1 First Normal Form (1NF)

**Syarat:** Tidak ada atribut multivalued atau composite dalam tabel.

✓ **Terpenuhi:**
- Atribut composite `nama` dipecah menjadi `nama_depan` dan `nama_belakang`
- Atribut multivalued `no_telepon` dipindah ke tabel USER_PHONE
- Atribut multivalued `gambar` dipindah ke tabel PRODUCT_IMAGE
- Atribut multivalued `foto_ulasan` dipindah ke tabel REVIEW_PHOTO

### 3.4.2 Second Normal Form (2NF)

**Syarat:** Sudah 1NF + tidak ada partial dependency (semua non-key atribut bergantung penuh pada PK).

✓ **Terpenuhi:**
- Tabel dengan PK tunggal: otomatis 2NF
- Tabel dengan PK gabungan:
  - ORDER_DETAIL(order_id, no_urut): `harga_satuan`, `jumlah`, `product_id` bergantung pada (order_id, no_urut) secara penuh
  - PRODUCT_CATEGORY(product_id, category_id): tidak ada non-key atribut
  - CUSTOMER_VOUCHER(customer_id, voucher_id, tgl_pakai): `order_id` bergantung pada ketiganya

### 3.4.3 Third Normal Form (3NF)

**Syarat:** Sudah 2NF + tidak ada transitive dependency (tidak ada non-key atribut yang bergantung pada non-key atribut lain).

✓ **Terpenuhi:**
- Tidak ada atribut derived yang disimpan (total_belanja, rating_avg, subtotal, est_tiba)
- Setiap non-key atribut hanya bergantung pada PK, tidak pada atribut lain
- Contoh: PRODUCT.harga tidak bergantung pada PRODUCT.nama_produk, tetapi langsung pada PRODUCT.product_id

**Contoh Pencegahan Transitive Dependency:**

❌ **Tidak Dinormalisasi:**
```
ORDER(order_id, customer_id, customer_nama, customer_email, ...)
```
Masalah: `customer_nama` dan `customer_email` bergantung pada `customer_id`, bukan `order_id` (transitive dependency)

✓ **Dinormalisasi (3NF):**
```
ORDER(order_id, customer_id, ...)
CUSTOMER(customer_id, user_id, ...)
USER(user_id, nama_depan, nama_belakang, email, ...)
```

### 3.4.4 Keuntungan Normalisasi

1. **Menghindari Redundansi**
   - Data customer tidak disimpan berulang di setiap order
   - Data seller tidak disimpan berulang di setiap produk

2. **Menghindari Anomali**
   - **Insert Anomaly:** Tidak bisa insert order tanpa customer yang valid
   - **Update Anomaly:** Update email customer cukup di satu tempat (tabel USER)
   - **Delete Anomaly:** Hapus order tidak menghapus data customer

3. **Integritas Data**
   - Foreign key constraint menjaga konsistensi referensi
   - CHECK constraint menjaga validitas nilai

4. **Fleksibilitas**
   - Mudah menambah atribut baru tanpa mengubah struktur besar
   - Mudah menambah entitas baru dengan relasi yang jelas

---


# BAB 4 — IMPLEMENTASI SQL

## 4.1 Data Definition Language (DDL)

DDL adalah bahasa SQL yang digunakan untuk mendefinisikan struktur database, termasuk CREATE, ALTER, dan DROP statement. Dalam tugas ini, DDL digunakan untuk membuat database dan semua tabel beserta constraint-nya.

### 4.1.1 CREATE DATABASE

```sql
DROP DATABASE IF EXISTS ecommerce_db;
CREATE DATABASE ecommerce_db;
USE ecommerce_db;
```

**Penjelasan:**
- `DROP DATABASE IF EXISTS`: Menghapus database lama jika ada (untuk testing)
- `CREATE DATABASE`: Membuat database baru dengan nama `ecommerce_db`
- `USE`: Mengaktifkan database untuk digunakan

### 4.1.2 CREATE TABLE — Contoh Implementasi

#### Contoh 1: Tabel dengan Composite Attribute

```sql
CREATE TABLE USER (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    nama_depan VARCHAR(50) NOT NULL,
    nama_belakang VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    tgl_daftar DATE NOT NULL DEFAULT (CURRENT_DATE),
    
    CONSTRAINT chk_email_format CHECK (email LIKE '%_@__%.__%')
) COMMENT 'Superclass untuk semua pengguna terdaftar';
```

**Fitur:**
- Composite attribute `nama` dipecah menjadi `nama_depan` dan `nama_belakang`
- `AUTO_INCREMENT`: PK otomatis increment
- `NOT NULL`: Atribut wajib diisi
- `UNIQUE`: Email tidak boleh duplikat
- `DEFAULT`: Tanggal daftar otomatis hari ini
- `CHECK`: Validasi format email
- `COMMENT`: Dokumentasi tabel

#### Contoh 2: Tabel Specialization (Subclass)

```sql
CREATE TABLE CUSTOMER (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    loyalty_points INT NOT NULL DEFAULT 0,
    user_id INT NOT NULL UNIQUE,
    
    CONSTRAINT fk_customer_user 
        FOREIGN KEY (user_id) REFERENCES USER(user_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    
    CONSTRAINT chk_loyalty_points CHECK (loyalty_points >= 0)
) COMMENT 'Subclass USER - data customer yang berbelanja';
```

**Fitur:**
- FK `user_id` ke superclass USER
- `ON DELETE CASCADE`: Jika USER dihapus, CUSTOMER ikut terhapus
- `ON UPDATE CASCADE`: Jika user_id di USER berubah, otomatis update di CUSTOMER
- `UNIQUE` pada user_id: Satu user hanya bisa jadi satu customer

#### Contoh 3: Weak Entity

```sql
CREATE TABLE ORDER_DETAIL (
    order_id INT NOT NULL,
    no_urut INT NOT NULL,
    harga_satuan DECIMAL(12, 2) NOT NULL,
    jumlah INT NOT NULL,
    product_id INT NOT NULL,
    
    PRIMARY KEY (order_id, no_urut),
    
    CONSTRAINT fk_detail_order 
        FOREIGN KEY (order_id) REFERENCES `ORDER`(order_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    
    CONSTRAINT fk_detail_product 
        FOREIGN KEY (product_id) REFERENCES PRODUCT(product_id)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    
    CONSTRAINT chk_harga_satuan CHECK (harga_satuan >= 0),
    CONSTRAINT chk_jumlah CHECK (jumlah > 0)
) COMMENT 'Weak entity - detail item dalam order';
```

**Fitur:**
- **PK gabungan**: (order_id, no_urut)
- **ON DELETE CASCADE** untuk owner (ORDER)
- **ON DELETE RESTRICT** untuk PRODUCT (tidak bisa hapus produk yang sudah terjual)
- **DECIMAL(12, 2)**: Tipe data untuk uang (presisi tinggi)

#### Contoh 4: Relasi 1:1

```sql
CREATE TABLE PAYMENT (
    payment_id INT AUTO_INCREMENT PRIMARY KEY,
    metode ENUM('transfer_bank', 'e_wallet', 'cod', 'kartu_kredit') NOT NULL,
    status_pay ENUM('pending', 'berhasil', 'gagal') NOT NULL DEFAULT 'pending',
    tgl_bayar DATETIME,
    jumlah_bayar DECIMAL(12, 2) NOT NULL,
    order_id INT NOT NULL UNIQUE,
    
    CONSTRAINT fk_payment_order 
        FOREIGN KEY (order_id) REFERENCES `ORDER`(order_id)
        ON DELETE RESTRICT
        ON UPDATE CASCADE,
    
    CONSTRAINT chk_jumlah_bayar CHECK (jumlah_bayar > 0)
) COMMENT 'Data pembayaran untuk setiap order (relasi 1:1)';
```

**Fitur:**
- **ENUM**: Tipe data untuk nilai terbatas
- **UNIQUE** pada order_id: Enforce relasi 1:1
- **DATETIME**: Tipe data untuk tanggal + waktu

#### Contoh 5: Union Type

```sql
CREATE TABLE VOUCHER_ISSUER (
    issuer_id INT AUTO_INCREMENT PRIMARY KEY,
    issuer_type ENUM('SELLER', 'ADMIN') NOT NULL,
    seller_id INT,
    admin_id INT,
    
    CONSTRAINT fk_issuer_seller 
        FOREIGN KEY (seller_id) REFERENCES SELLER(seller_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    
    CONSTRAINT fk_issuer_admin 
        FOREIGN KEY (admin_id) REFERENCES PLATFORM_ADMIN(admin_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    
    CONSTRAINT chk_issuer_disjoint CHECK (
        (issuer_type = 'SELLER' AND seller_id IS NOT NULL AND admin_id IS NULL) OR
        (issuer_type = 'ADMIN' AND admin_id IS NOT NULL AND seller_id IS NULL)
    )
) COMMENT 'Union type: penerbit voucher (SELLER atau PLATFORM_ADMIN)';
```

**Fitur:**
- **Surrogate key**: issuer_id sebagai PK
- **Discriminator**: issuer_type untuk membedakan member class
- **CHECK constraint**: Enforce disjoint (hanya satu FK yang terisi)
- Dua FK nullable: seller_id dan admin_id

#### Contoh 6: Junction Table (M:N)

```sql
CREATE TABLE PRODUCT_CATEGORY (
    product_id INT NOT NULL,
    category_id INT NOT NULL,
    
    PRIMARY KEY (product_id, category_id),
    
    CONSTRAINT fk_pc_product 
        FOREIGN KEY (product_id) REFERENCES PRODUCT(product_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    
    CONSTRAINT fk_pc_category 
        FOREIGN KEY (category_id) REFERENCES CATEGORY(category_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
) COMMENT 'Junction table M:N - produk dan kategori';
```

**Fitur:**
- **PK gabungan**: (product_id, category_id)
- **ON DELETE CASCADE**: Jika produk atau kategori dihapus, relasi ikut terhapus
- Tidak ada atribut tambahan (pure junction table)

#### Contoh 7: Multivalued Attribute

```sql
CREATE TABLE USER_PHONE (
    user_id INT NOT NULL,
    no_telepon VARCHAR(20) NOT NULL,
    
    PRIMARY KEY (user_id, no_telepon),
    
    CONSTRAINT fk_phone_user 
        FOREIGN KEY (user_id) REFERENCES USER(user_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    
    CONSTRAINT chk_phone_format CHECK (no_telepon REGEXP '^[0-9+()-]+$')
) COMMENT 'Multivalued attribute - nomor telepon user';
```

**Fitur:**
- **PK gabungan**: (user_id, no_telepon)
- **REGEXP**: Validasi format nomor telepon
- **ON DELETE CASCADE**: Jika user dihapus, semua nomor teleponnya ikut terhapus

### 4.1.3 Index untuk Optimasi

```sql
CREATE INDEX idx_product_seller ON PRODUCT(seller_id);
CREATE INDEX idx_order_customer ON `ORDER`(customer_id);
CREATE INDEX idx_order_status ON `ORDER`(status);
CREATE INDEX idx_shipment_company ON SHIPMENT(company_id);
CREATE INDEX idx_review_product ON REVIEW(product_id);
```

**Penjelasan:**
- Index mempercepat query dengan WHERE, JOIN, dan ORDER BY
- Dibuat pada kolom yang sering digunakan untuk filtering dan joining
- Trade-off: Mempercepat SELECT, memperlambat INSERT/UPDATE

### 4.1.4 Ringkasan Constraint yang Digunakan

| Constraint | Jumlah | Contoh Penggunaan |
|------------|--------|-------------------|
| PRIMARY KEY | 19 | Semua tabel |
| FOREIGN KEY | 24 | Relasi antar tabel |
| NOT NULL | 50+ | Atribut wajib diisi |
| UNIQUE | 12 | email, nama_toko, kode_voucher, order_id di PAYMENT/SHIPMENT |
| CHECK | 15+ | rating 1-5, harga >= 0, format email, disjoint union |
| DEFAULT | 10+ | status, tanggal, loyalty_points |
| ON DELETE CASCADE | 12 | Weak entity, multivalued, specialization |
| ON DELETE RESTRICT | 12 | Mencegah penghapusan data penting |

## 4.2 Data Manipulation Language (DML)

DML adalah bahasa SQL yang digunakan untuk memanipulasi data dalam database, termasuk INSERT, UPDATE, DELETE, dan SELECT.

### 4.2.1 INSERT — Strategi Pengisian Data

Data diisi dengan urutan yang memperhatikan foreign key dependency:

1. **Tabel tanpa FK** (independent): USER, CATEGORY, COURIER_COMPANY, PLATFORM_ADMIN
2. **Tabel dengan FK level 1**: CUSTOMER, SELLER (FK ke USER)
3. **Tabel dengan FK level 2**: PRODUCT (FK ke SELLER), VOUCHER_ISSUER (FK ke SELLER/ADMIN)
4. **Tabel dengan FK level 3**: ORDER (FK ke CUSTOMER), VOUCHER (FK ke VOUCHER_ISSUER)
5. **Tabel dengan FK level 4**: ORDER_DETAIL (FK ke ORDER dan PRODUCT), PAYMENT, SHIPMENT, REVIEW
6. **Tabel junction dan multivalued**: PRODUCT_CATEGORY, CUSTOMER_VOUCHER, USER_PHONE, dll

### 4.2.2 Contoh INSERT Data

#### Insert dengan Composite Attribute

```sql
INSERT INTO USER (user_id, nama_depan, nama_belakang, email, tgl_daftar) VALUES
(1, 'Budi', 'Santoso', 'budi.santoso@email.com', '2024-01-15'),
(2, 'Siti', 'Nurhaliza', 'siti.nurhaliza@email.com', '2024-01-20');
```

**Penjelasan:**
- Composite attribute `nama` dipecah menjadi `nama_depan` dan `nama_belakang`
- `tgl_daftar` bisa di-skip (akan pakai DEFAULT)

#### Insert Specialization (Overlap)

```sql
-- User yang hanya customer
INSERT INTO CUSTOMER (customer_id, loyalty_points, user_id) VALUES
(1, 150, 1);  -- Budi (customer only)

-- User yang customer sekaligus seller (overlap)
INSERT INTO CUSTOMER (customer_id, loyalty_points, user_id) VALUES
(3, 0, 3);    -- Ahmad (customer + seller)

INSERT INTO SELLER (seller_id, nama_toko, user_id) VALUES
(1, 'Elektronik Jaya', 3);  -- Ahmad (customer + seller)
```

**Penjelasan:**
- user_id = 3 (Ahmad) muncul di CUSTOMER dan SELLER (overlap)
- Ini valid karena specialization constraint adalah overlap

#### Insert Weak Entity

```sql
-- Insert ORDER dulu (owner)
INSERT INTO `ORDER` (order_id, status, tgl_order, customer_id) VALUES
(1, 'selesai', '2024-04-01 10:30:00', 1);

-- Baru insert ORDER_DETAIL (weak entity)
INSERT INTO ORDER_DETAIL (order_id, no_urut, harga_satuan, jumlah, product_id) VALUES
(1, 1, 650000.00, 1, 2),    -- Item pertama
(1, 2, 850000.00, 1, 3);    -- Item kedua
```

**Penjelasan:**
- ORDER_DETAIL tidak bisa diinsert tanpa ORDER yang valid
- PK gabungan: (order_id, no_urut)

#### Insert Union Type

```sql
-- Insert member classes dulu
INSERT INTO SELLER (seller_id, nama_toko, user_id) VALUES
(1, 'Elektronik Jaya', 3);

INSERT INTO PLATFORM_ADMIN (admin_id, nama_admin, level_akses, divisi) VALUES
(2, 'Fitri Handayani', 'admin', 'Marketing');

-- Insert union type
INSERT INTO VOUCHER_ISSUER (issuer_id, issuer_type, seller_id, admin_id) VALUES
(1, 'SELLER', 1, NULL),   -- Voucher dari seller
(5, 'ADMIN', NULL, 2);    -- Voucher dari admin

-- Insert voucher yang merujuk union type
INSERT INTO VOUCHER (voucher_id, kode_voucher, nilai_diskon, tgl_mulai, tgl_berakhir, tipe, issuer_id) VALUES
(1, 'ELEKTRONIK20', 200000.00, '2024-04-01', '2024-05-31', 'nominal', 1),
(5, 'CASHBACK50', 50000.00, '2024-04-01', '2024-12-31', 'nominal', 5);
```

**Penjelasan:**
- CHECK constraint memastikan hanya satu FK yang terisi
- VOUCHER merujuk ke VOUCHER_ISSUER, tidak langsung ke SELLER/ADMIN

#### Insert Multivalued Attribute

```sql
-- Insert user dulu
INSERT INTO USER (user_id, nama_depan, nama_belakang, email) VALUES
(1, 'Budi', 'Santoso', 'budi.santoso@email.com');

-- Insert nomor telepon (multivalued)
INSERT INTO USER_PHONE (user_id, no_telepon) VALUES
(1, '081234567890'),
(1, '087654321098');  -- User bisa punya banyak nomor
```

### 4.2.3 Ringkasan Data yang Diinsert

| Tabel | Jumlah Data | Keterangan |
|-------|-------------|------------|
| USER | 10 | Pengguna terdaftar |
| CUSTOMER | 8 | 3 overlap dengan SELLER |
| SELLER | 5 | 3 overlap dengan CUSTOMER |
| CATEGORY | 7 | Kategori produk |
| PRODUCT | 25 | 5 produk per seller |
| ORDER | 10 | Berbagai status |
| ORDER_DETAIL | 19 | Weak entity |
| PAYMENT | 10 | Relasi 1:1 dengan ORDER |
| SHIPMENT | 10 | Relasi 1:1 dengan ORDER |
| REVIEW | 10 | Hanya untuk order selesai |
| COURIER_COMPANY | 6 | JNE, SiCepat, J&T, dll |
| PLATFORM_ADMIN | 5 | Admin platform |
| VOUCHER_ISSUER | 7 | 4 dari SELLER, 3 dari ADMIN |
| VOUCHER | 7 | Voucher toko dan platform |
| PRODUCT_CATEGORY | 28 | M:N |
| CUSTOMER_VOUCHER | 8 | M:N |
| USER_PHONE | 13 | Multivalued |
| PRODUCT_IMAGE | 17 | Multivalued |
| REVIEW_PHOTO | 10 | Multivalued |

**Total: 19 tabel dengan data realistis dan konsisten**

## 4.3 Constraint dan Referential Integrity

### 4.3.1 Referential Integrity

Referential integrity memastikan bahwa nilai FK harus ada di tabel yang direferensi (atau NULL jika diizinkan).

**Contoh:**
```sql
-- ✓ Valid: customer_id = 1 ada di tabel CUSTOMER
INSERT INTO `ORDER` (order_id, status, tgl_order, customer_id) VALUES
(1, 'pending', CURRENT_TIMESTAMP, 1);

-- ✗ Error: customer_id = 999 tidak ada di tabel CUSTOMER
INSERT INTO `ORDER` (order_id, status, tgl_order, customer_id) VALUES
(2, 'pending', CURRENT_TIMESTAMP, 999);
-- Error: Cannot add or update a child row: a foreign key constraint fails
```

### 4.3.2 ON DELETE Actions

#### CASCADE
```sql
-- Jika USER dihapus, CUSTOMER ikut terhapus
DELETE FROM USER WHERE user_id = 1;
-- Otomatis menghapus CUSTOMER dengan user_id = 1
```

#### RESTRICT
```sql
-- Tidak bisa hapus PRODUCT jika masih ada ORDER_DETAIL yang merujuk
DELETE FROM PRODUCT WHERE product_id = 2;
-- Error: Cannot delete or update a parent row: a foreign key constraint fails
```

### 4.3.3 CHECK Constraint

```sql
-- ✓ Valid: rating dalam range 1-5
INSERT INTO REVIEW (review_id, rating, customer_id, product_id) VALUES
(1, 5, 1, 2);

-- ✗ Error: rating di luar range
INSERT INTO REVIEW (review_id, rating, customer_id, product_id) VALUES
(2, 6, 1, 3);
-- Error: Check constraint 'chk_rating' is violated
```

### 4.3.4 UNIQUE Constraint

```sql
-- ✓ Valid: email belum ada
INSERT INTO USER (nama_depan, nama_belakang, email) VALUES
('John', 'Doe', 'john.doe@email.com');

-- ✗ Error: email sudah ada
INSERT INTO USER (nama_depan, nama_belakang, email) VALUES
('Jane', 'Doe', 'john.doe@email.com');
-- Error: Duplicate entry 'john.doe@email.com' for key 'email'
```

### 4.3.5 Keuntungan Constraint

1. **Data Integrity**: Mencegah data tidak valid masuk ke database
2. **Business Rules**: Enforce aturan bisnis di level database
3. **Error Prevention**: Mendeteksi error lebih awal
4. **Documentation**: Constraint adalah dokumentasi aturan data
5. **Consistency**: Semua aplikasi yang akses database harus ikuti aturan yang sama

---


# BAB 5 — QUERY SQL

Bab ini menjelaskan berbagai jenis query SQL yang digunakan untuk mengakses dan memanipulasi data dalam database e-commerce. Query dibagi menjadi beberapa kategori berdasarkan fungsi dan kompleksitasnya.

## 5.1 Query dengan WHERE Clause

WHERE clause digunakan untuk filtering data berdasarkan kondisi tertentu.

### 5.1.1 BETWEEN — Rentang Nilai

**Query: Produk dengan Harga di Rentang Tertentu**
```sql
SELECT product_id, nama_produk, harga, stok
FROM PRODUCT
WHERE harga BETWEEN 100000 AND 500000
ORDER BY harga ASC;
```

**Fungsi:** Menampilkan produk dengan harga antara 100.000 hingga 500.000 rupiah, berguna untuk filter produk berdasarkan budget customer.

**Hasil:** Produk seperti Kemeja Pria (250.000), Powerbank (280.000), Dress Wanita (320.000), dll.

### 5.1.2 LIKE — Pattern Matching

**Query: Pencarian Produk Berdasarkan Nama**
```sql
SELECT product_id, nama_produk, harga, stok
FROM PRODUCT
WHERE nama_produk LIKE '%Gaming%';
```

**Fungsi:** Mencari produk yang mengandung kata "Gaming" dalam namanya, berguna untuk fitur search bar di aplikasi e-commerce.

**Hasil:** Mouse Gaming Logitech G502, Laptop ASUS ROG (gaming), dll.

### 5.1.3 IN — Multiple Values

**Query: Order dengan Status Tertentu**
```sql
SELECT order_id, status, tgl_order, customer_id
FROM `ORDER`
WHERE status IN ('pending', 'dikonfirmasi', 'dikirim')
ORDER BY tgl_order DESC;
```

**Fungsi:** Menampilkan order yang masih aktif (belum selesai atau dibatalkan), berguna untuk monitoring order yang perlu ditindaklanjuti.

**Hasil:** Order dengan status pending, dikonfirmasi, atau dikirim.

### 5.1.4 Comparison Operators

**Query: Customer dengan Loyalty Points Tinggi**
```sql
SELECT c.customer_id, u.nama_depan, u.nama_belakang, c.loyalty_points
FROM CUSTOMER c
JOIN USER u ON c.user_id = u.user_id
WHERE c.loyalty_points > 200
ORDER BY c.loyalty_points DESC;
```

**Fungsi:** Menampilkan customer dengan loyalty points lebih dari 200, berguna untuk program reward atau promosi khusus member premium.

**Hasil:** Siti (320 poin), Rina (580 poin), Dewi (420 poin), Maya (250 poin).

## 5.2 Query dengan ORDER BY dan DISTINCT

### 5.2.1 ORDER BY — Sorting

**Query: Produk Termahal ke Termurah**
```sql
SELECT product_id, nama_produk, harga, stok, seller_id
FROM PRODUCT
ORDER BY harga DESC;
```

**Fungsi:** Menampilkan semua produk diurutkan dari harga tertinggi ke terendah, berguna untuk menampilkan produk premium atau sorting di katalog.

**Hasil:** Laptop ASUS (15 juta) di urutan pertama, diikuti Smartphone Samsung (5,5 juta), dst.

### 5.2.2 DISTINCT — Unique Values

**Query: Daftar Customer yang Pernah Melakukan Order**
```sql
SELECT DISTINCT c.customer_id, u.nama_depan, u.nama_belakang, u.email
FROM CUSTOMER c
JOIN USER u ON c.user_id = u.user_id
JOIN `ORDER` o ON c.customer_id = o.customer_id
ORDER BY c.customer_id;
```

**Fungsi:** Menampilkan customer unik yang pernah membuat order (tanpa duplikasi), berguna untuk menghitung jumlah active customer.

**Hasil:** 8 customer unik yang pernah order (Budi, Siti, Ahmad, Rina, Maya, Lina, Rudi, Dewi).

## 5.3 Query dengan Aggregate Functions

Aggregate functions melakukan kalkulasi pada sekumpulan nilai dan mengembalikan satu nilai.

### 5.3.1 SUM — Total

**Query: Total Pendapatan Platform**
```sql
SELECT 
    SUM(jumlah_bayar) AS total_pendapatan,
    COUNT(*) AS jumlah_transaksi
FROM PAYMENT
WHERE status_pay = 'berhasil';
```

**Fungsi:** Menghitung total pendapatan dari semua pembayaran yang berhasil, berguna untuk laporan keuangan dan dashboard revenue.

**Hasil:** Total pendapatan sekitar 28 juta rupiah dari 9 transaksi berhasil.

### 5.3.2 AVG — Rata-rata

**Query: Rata-rata Harga Produk per Kategori**
```sql
SELECT 
    cat.nama_kategori,
    COUNT(DISTINCT p.product_id) AS jumlah_produk,
    AVG(p.harga) AS rata_rata_harga,
    MIN(p.harga) AS harga_termurah,
    MAX(p.harga) AS harga_termahal
FROM CATEGORY cat
JOIN PRODUCT_CATEGORY pc ON cat.category_id = pc.category_id
JOIN PRODUCT p ON pc.product_id = p.product_id
GROUP BY cat.category_id, cat.nama_kategori
ORDER BY rata_rata_harga DESC;
```

**Fungsi:** Menghitung rata-rata harga produk untuk setiap kategori, berguna untuk analisis pricing strategy per kategori.

**Hasil:** Kategori Elektronik memiliki rata-rata harga tertinggi, diikuti Fashion, Makanan & Minuman, dll.

### 5.3.3 COUNT, MIN, MAX

**Query: Statistik Review per Produk**
```sql
SELECT 
    p.product_id,
    p.nama_produk,
    COUNT(r.review_id) AS jumlah_review,
    AVG(r.rating) AS rating_rata_rata,
    MIN(r.rating) AS rating_terendah,
    MAX(r.rating) AS rating_tertinggi
FROM PRODUCT p
JOIN REVIEW r ON p.product_id = r.product_id
GROUP BY p.product_id, p.nama_produk
ORDER BY rating_rata_rata DESC, jumlah_review DESC;
```

**Fungsi:** Menampilkan statistik review untuk setiap produk yang pernah direview, berguna untuk menampilkan rating produk di halaman detail.

**Hasil:** Produk dengan rating tertinggi (5.0) seperti Mouse Gaming, Smartphone Samsung, Sepatu Nike, dll.

## 5.4 Query dengan GROUP BY dan HAVING

GROUP BY mengelompokkan baris berdasarkan nilai kolom tertentu, HAVING memfilter hasil group.

### 5.4.1 GROUP BY dengan HAVING

**Query: Seller dengan Jumlah Produk Lebih dari 5**
```sql
SELECT 
    s.seller_id,
    s.nama_toko,
    COUNT(p.product_id) AS jumlah_produk
FROM SELLER s
JOIN PRODUCT p ON s.seller_id = p.seller_id
GROUP BY s.seller_id, s.nama_toko
HAVING COUNT(p.product_id) > 5
ORDER BY jumlah_produk DESC;
```

**Fungsi:** Menampilkan seller yang memiliki lebih dari 5 produk terdaftar, berguna untuk identifikasi seller aktif dengan katalog lengkap.

**Hasil:** Tidak ada seller dengan > 5 produk (semua seller punya 5 produk).

### 5.4.2 HAVING dengan Aggregate

**Query: Customer dengan Total Belanja di Atas 1 Juta**
```sql
SELECT 
    c.customer_id,
    u.nama_depan,
    u.nama_belakang,
    COUNT(o.order_id) AS jumlah_order,
    SUM(p.jumlah_bayar) AS total_belanja
FROM CUSTOMER c
JOIN USER u ON c.user_id = u.user_id
JOIN `ORDER` o ON c.customer_id = o.customer_id
JOIN PAYMENT p ON o.order_id = p.order_id
WHERE p.status_pay = 'berhasil'
GROUP BY c.customer_id, u.nama_depan, u.nama_belakang
HAVING SUM(p.jumlah_bayar) > 1000000
ORDER BY total_belanja DESC;
```

**Fungsi:** Menampilkan customer yang total belanjanya lebih dari 1 juta rupiah, berguna untuk program loyalitas atau promosi khusus big spender.

**Hasil:** Rudi (16,2 juta), Ahmad (6,2 juta), Rina (1,65 juta), Budi (1,5 juta).

### 5.4.3 HAVING dengan AVG

**Query: Produk dengan Rating Rata-rata di Atas 4.0**
```sql
SELECT 
    p.product_id,
    p.nama_produk,
    COUNT(r.review_id) AS jumlah_review,
    AVG(r.rating) AS rating_rata_rata
FROM PRODUCT p
JOIN REVIEW r ON p.product_id = r.product_id
GROUP BY p.product_id, p.nama_produk
HAVING AVG(r.rating) > 4.0
ORDER BY rating_rata_rata DESC, jumlah_review DESC;
```

**Fungsi:** Menampilkan produk yang memiliki rating rata-rata lebih dari 4.0, berguna untuk menampilkan "Produk Terbaik" atau "Best Seller".

**Hasil:** 8 produk dengan rating > 4.0 (Mouse Gaming, Smartphone, Sepatu Nike, dll).

## 5.5 Query dengan JOIN

JOIN menggabungkan data dari dua atau lebih tabel berdasarkan relasi.

### 5.5.1 JOIN 2 Tabel

**Query: Daftar Produk dengan Nama Toko Seller**
```sql
SELECT 
    p.product_id,
    p.nama_produk,
    p.harga,
    p.stok,
    s.nama_toko
FROM PRODUCT p
JOIN SELLER s ON p.seller_id = s.seller_id
ORDER BY p.product_id;
```

**Fungsi:** Menampilkan produk beserta informasi toko yang menjualnya, berguna untuk halaman katalog produk dengan info seller.

**Hasil:** Laptop ASUS dari Elektronik Jaya, Kemeja Pria dari Fashion Trendy, dll.

### 5.5.2 JOIN 3 Tabel

**Query: Detail Order Lengkap (Order, Customer, Payment)**
```sql
SELECT 
    o.order_id,
    o.status AS status_order,
    o.tgl_order,
    u.nama_depan,
    u.nama_belakang,
    u.email,
    p.metode AS metode_bayar,
    p.status_pay,
    p.jumlah_bayar
FROM `ORDER` o
JOIN CUSTOMER c ON o.customer_id = c.customer_id
JOIN USER u ON c.user_id = u.user_id
JOIN PAYMENT p ON o.order_id = p.order_id
ORDER BY o.tgl_order DESC;
```

**Fungsi:** Menampilkan informasi lengkap order termasuk customer dan pembayaran, berguna untuk laporan transaksi lengkap.

**Hasil:** Order terbaru dengan detail customer (nama, email) dan metode pembayaran.

### 5.5.3 JOIN 4+ Tabel

**Query: Detail Pengiriman Lengkap (Order, Shipment, Courier, Customer)**
```sql
SELECT 
    o.order_id,
    o.status AS status_order,
    s.no_resi,
    s.status_kirim,
    s.tgl_kirim,
    cc.nama_kurir,
    cc.estimasi_hari_default,
    u.nama_depan,
    u.nama_belakang
FROM `ORDER` o
JOIN SHIPMENT s ON o.order_id = s.order_id
JOIN COURIER_COMPANY cc ON s.company_id = cc.company_id
JOIN CUSTOMER c ON o.customer_id = c.customer_id
JOIN USER u ON c.user_id = u.user_id
ORDER BY s.tgl_kirim DESC;
```

**Fungsi:** Menampilkan informasi lengkap pengiriman termasuk order, kurir, dan customer, berguna untuk tracking pengiriman detail.

**Hasil:** Pengiriman terbaru dengan nomor resi, status, nama kurir, dan nama customer.

### 5.5.4 JOIN dengan Union Type

**Query: Penggunaan Voucher Lengkap (Customer, Voucher, Issuer)**
```sql
SELECT 
    u.nama_depan,
    u.nama_belakang,
    v.kode_voucher,
    v.nilai_diskon,
    v.tipe,
    vi.issuer_type,
    cv.tgl_pakai,
    cv.order_id
FROM CUSTOMER_VOUCHER cv
JOIN CUSTOMER c ON cv.customer_id = c.customer_id
JOIN USER u ON c.user_id = u.user_id
JOIN VOUCHER v ON cv.voucher_id = v.voucher_id
JOIN VOUCHER_ISSUER vi ON v.issuer_id = vi.issuer_id
ORDER BY cv.tgl_pakai DESC;
```

**Fungsi:** Menampilkan penggunaan voucher dengan info customer dan penerbit voucher (SELLER atau ADMIN), berguna untuk analisis efektivitas voucher.

**Hasil:** Dewi pakai FLASHSALE20 (dari ADMIN), Rudi pakai ELEKTRONIK20 (dari SELLER), dll.

## 5.6 Query dengan SUBQUERY

Subquery adalah query di dalam query, digunakan untuk filtering atau kalkulasi kompleks.

### 5.6.1 Subquery di WHERE

**Query: Produk dengan Harga di Atas Rata-rata**
```sql
SELECT product_id, nama_produk, harga, stok
FROM PRODUCT
WHERE harga > (SELECT AVG(harga) FROM PRODUCT)
ORDER BY harga DESC;
```

**Fungsi:** Menampilkan produk yang harganya lebih tinggi dari rata-rata semua produk, berguna untuk identifikasi produk premium.

**Hasil:** Laptop ASUS (15 juta), Smartphone Samsung (5,5 juta), Monitor LG (2,1 juta), dll.

### 5.6.2 Subquery dengan NOT IN

**Query: Customer yang Belum Pernah Review**
```sql
SELECT c.customer_id, u.nama_depan, u.nama_belakang, u.email
FROM CUSTOMER c
JOIN USER u ON c.user_id = u.user_id
WHERE c.customer_id NOT IN (SELECT DISTINCT customer_id FROM REVIEW)
ORDER BY c.customer_id;
```

**Fungsi:** Menampilkan customer yang sudah order tapi belum pernah menulis review, berguna untuk campaign "Tulis Review Dapat Poin".

**Hasil:** Customer yang belum review (jika ada).

### 5.6.3 Subquery di FROM

**Query: Seller dengan Produk Terlaris**
```sql
SELECT 
    s.seller_id,
    s.nama_toko,
    produk_terjual.jumlah_terjual
FROM SELLER s
JOIN (
    SELECT 
        p.seller_id,
        COUNT(DISTINCT od.order_id) AS jumlah_terjual
    FROM PRODUCT p
    JOIN ORDER_DETAIL od ON p.product_id = od.product_id
    GROUP BY p.seller_id
) AS produk_terjual ON s.seller_id = produk_terjual.seller_id
ORDER BY produk_terjual.jumlah_terjual DESC;
```

**Fungsi:** Menampilkan seller beserta jumlah produk yang pernah terjual, berguna untuk ranking seller berdasarkan penjualan.

**Hasil:** Seller dengan penjualan terbanyak di urutan pertama.

### 5.6.4 Subquery dengan NOT EXISTS

**Query: Produk yang Belum Pernah Terjual**
```sql
SELECT p.product_id, p.nama_produk, p.harga, p.stok, s.nama_toko
FROM PRODUCT p
JOIN SELLER s ON p.seller_id = s.seller_id
WHERE NOT EXISTS (
    SELECT 1 FROM ORDER_DETAIL od WHERE od.product_id = p.product_id
)
ORDER BY p.product_id;
```

**Fungsi:** Menampilkan produk yang belum pernah masuk ke order detail, berguna untuk identifikasi produk yang perlu promosi.

**Hasil:** Produk yang belum pernah terjual (jika ada).

### 5.6.5 Subquery dengan Aggregate

**Query: Order dengan Total Belanja Tertinggi**
```sql
SELECT 
    o.order_id,
    o.tgl_order,
    u.nama_depan,
    u.nama_belakang,
    total_order.total_belanja
FROM `ORDER` o
JOIN CUSTOMER c ON o.customer_id = c.customer_id
JOIN USER u ON c.user_id = u.user_id
JOIN (
    SELECT order_id, SUM(harga_satuan * jumlah) AS total_belanja
    FROM ORDER_DETAIL
    GROUP BY order_id
) AS total_order ON o.order_id = total_order.order_id
ORDER BY total_order.total_belanja DESC
LIMIT 5;
```

**Fungsi:** Menampilkan 5 order dengan total belanja paling tinggi, berguna untuk identifikasi transaksi terbesar.

**Hasil:** Order dengan total belanja tertinggi (Laptop ASUS + Webcam = 16,2 juta).

## 5.7 Query UPDATE dan DELETE

### 5.7.1 UPDATE — Modifikasi Data

**Query 1: Update Stok Produk Setelah Penjualan**
```sql
UPDATE PRODUCT
SET stok = stok - 1
WHERE product_id = 2 AND stok > 0;
```

**Fungsi:** Mengurangi stok produk setelah ada penjualan, simulasi inventory management.

**Query 2: Update Status Order**
```sql
UPDATE `ORDER`
SET status = 'selesai'
WHERE order_id = 3 AND status = 'dikirim';
```

**Fungsi:** Mengubah status order dari 'dikirim' menjadi 'selesai' setelah barang tiba.

**Query 3: Update Loyalty Points Customer**
```sql
UPDATE CUSTOMER
SET loyalty_points = loyalty_points + 50
WHERE customer_id = 1;
```

**Fungsi:** Menambah loyalty points customer setelah transaksi berhasil, simulasi reward program.

**Query 4: Update Harga Produk (Diskon Massal)**
```sql
UPDATE PRODUCT p
JOIN PRODUCT_CATEGORY pc ON p.product_id = pc.product_id
SET p.harga = p.harga * 0.9
WHERE pc.category_id = 2;  -- Fashion
```

**Fungsi:** Memberikan diskon 10% untuk semua produk di kategori Fashion, simulasi flash sale.

### 5.7.2 DELETE — Penghapusan Data

**Query 1: Delete Review Spam**
```sql
DELETE FROM REVIEW
WHERE review_id = 9;
```

**Fungsi:** Menghapus review yang dianggap spam atau melanggar aturan, simulasi content moderation.

**Query 2: Delete Voucher Kadaluarsa**
```sql
DELETE FROM VOUCHER
WHERE tgl_berakhir < CURRENT_DATE;
```

**Fungsi:** Menghapus voucher yang sudah melewati tanggal berakhir, berguna untuk maintenance database.

**Query 3: Delete Order Dibatalkan (Cascade)**
```sql
-- Buat order dummy
INSERT INTO `ORDER` (order_id, status, tgl_order, customer_id) 
VALUES (999, 'dibatalkan', CURRENT_TIMESTAMP, 1);

-- Hapus order (akan cascade ke PAYMENT, SHIPMENT, ORDER_DETAIL)
DELETE FROM `ORDER` WHERE order_id = 999;
```

**Fungsi:** Menghapus order yang dibatalkan beserta semua data terkait (payment, shipment, detail), demonstrasi ON DELETE CASCADE.

**Query 4: Delete Produk Tidak Aktif**
```sql
DELETE FROM PRODUCT
WHERE stok = 0 
  AND product_id NOT IN (SELECT DISTINCT product_id FROM ORDER_DETAIL);
```

**Fungsi:** Menghapus produk yang stoknya 0 dan tidak pernah terjual, cleanup data tidak terpakai.

### 5.7.3 Ringkasan Query

| Kategori | Jumlah Query | Contoh |
|----------|--------------|--------|
| WHERE (BETWEEN, LIKE, IN) | 5 | Filter produk, order, customer |
| ORDER BY & DISTINCT | 6 | Sorting dan unique values |
| AGGREGATE (SUM, AVG, COUNT) | 4 | Total pendapatan, rata-rata harga |
| GROUP BY + HAVING | 5 | Seller aktif, customer big spender |
| JOIN 2 Tabel | 5 | Produk + Seller, Order + Customer |
| JOIN 3+ Tabel | 5 | Order + Customer + Payment + Shipment |
| SUBQUERY | 5 | Harga > rata-rata, produk belum terjual |
| UPDATE | 5 | Update stok, status, loyalty points |
| DELETE | 5 | Delete review, voucher, order |

**Total: 45+ query dengan berbagai kompleksitas dan use case**

---


# BAB 6 — KESIMPULAN

## 6.1 Ringkasan Hasil Perancangan

Tugas akhir ini telah berhasil merancang dan mengimplementasikan database sistem e-commerce yang komprehensif, mulai dari tahap konseptual (EER Diagram) hingga implementasi fisik (SQL). Berikut adalah ringkasan pencapaian:

### 6.1.1 Pencapaian Requirement Akademik

| Requirement | Target | Tercapai | Keterangan |
|-------------|--------|----------|------------|
| Jumlah Entitas | Min. 8 | ✓ 14 | USER, CUSTOMER, SELLER, PRODUCT, CATEGORY, ORDER, ORDER_DETAIL, PAYMENT, COURIER_COMPANY, COURIER_SERVICE, SHIPMENT, REVIEW, VOUCHER, PLATFORM_ADMIN |
| Weak Entity | Min. 1 | ✓ 1 | ORDER_DETAIL dengan owner ORDER |
| Specialization | Min. 1 | ✓ 1 | USER → CUSTOMER, SELLER (overlap, total) |
| Union Type | Min. 1 | ✓ 1 | VOUCHER_ISSUER = SELLER ∪ PLATFORM_ADMIN |
| Composite Attribute | Min. 1 | ✓ 1 | nama → nama_depan, nama_belakang |
| Multivalued Attribute | Min. 1 | ✓ 3 | no_telepon, gambar, foto_ulasan |
| Derived Attribute | Min. 1 | ✓ 5 | total_belanja, rating_avg, subtotal, total_harga, est_tiba |
| Relasi 1:1 | Min. 1 | ✓ 2 | ORDER-PAYMENT, ORDER-SHIPMENT |
| Relasi 1:N | Min. 1 | ✓ 8 | CUSTOMER-ORDER, SELLER-PRODUCT, COURIER_COMPANY-COURIER_SERVICE, dll |
| Relasi M:N | Min. 1 | ✓ 2 | PRODUCT-CATEGORY, CUSTOMER-VOUCHER |

**Semua requirement akademik terpenuhi dengan baik.**

### 6.1.2 Hasil Implementasi

1. **EER Diagram**
   - 13 entitas dengan berbagai jenis atribut
   - Relasi lengkap dengan kardinalitas yang tepat
   - Specialization dengan constraint overlap dan total
   - Union type yang valid secara konsep
   - Weak entity dengan relasi identifikasi

2. **Skema Relasional**
   - 20 tabel hasil mapping yang benar
   - Normalisasi hingga 3NF
   - Foreign key dengan action yang tepat (CASCADE, RESTRICT)
   - Constraint lengkap (PK, FK, UNIQUE, CHECK, NOT NULL, DEFAULT)

3. **Implementasi SQL**
   - DDL lengkap dengan 20 tabel
   - 26 foreign key dengan referential integrity
   - 17+ CHECK constraint untuk validasi
   - 13 UNIQUE constraint
   - 10+ DEFAULT constraint
   - Index untuk optimasi query

4. **Data dan Query**
   - Data realistis untuk 20 tabel
   - 50+ query dengan berbagai kompleksitas
   - Mencakup semua jenis query (WHERE, JOIN, SUBQUERY, AGGREGATE, UPDATE, DELETE)
   - Use case yang sesuai dengan bisnis e-commerce nyata

## 6.2 Insight dan Pembelajaran

### 6.2.1 Pentingnya Desain yang Tepat

Proses perancangan database ini memberikan pemahaman mendalam tentang pentingnya desain yang tepat sejak awal. Beberapa insight penting:

1. **Union Type Harus Bermakna**
   - Union type COURIER (SELLER ∪ COURIER_COMPANY) yang awalnya dirancang ternyata tidak valid karena SELLER tidak berperan sebagai pengirim dalam asumsi "no self-shipping"
   - Union type VOUCHER_ISSUER (SELLER ∪ PLATFORM_ADMIN) lebih valid karena kedua entitas memang bisa menerbitkan voucher
   - **Pembelajaran:** Union type harus digunakan ketika benar-benar ada kebutuhan abstraksi dari entitas yang fundamentally different

2. **Specialization Overlap vs Disjoint**
   - Overlap specialization (USER → CUSTOMER, SELLER) sangat sesuai dengan realitas e-commerce di mana pengguna bisa berbelanja sekaligus berjualan
   - Implementasi dengan Class Hierarchy memungkinkan fleksibilitas ini
   - **Pembelajaran:** Pilih constraint specialization (overlap/disjoint, partial/total) berdasarkan business rules yang sebenarnya

3. **Weak Entity untuk Dependency**
   - ORDER_DETAIL sebagai weak entity sangat tepat karena detail item tidak bisa eksis tanpa order
   - ON DELETE CASCADE memastikan integritas data
   - **Pembelajaran:** Weak entity cocok untuk entitas yang existence-dependent dan tidak memiliki key sendiri yang meaningful

4. **Normalisasi Mencegah Anomali**
   - Pemisahan atribut multivalued ke tabel terpisah mencegah redundansi
   - Pemisahan superclass dan subclass memungkinkan overlap tanpa NULL
   - **Pembelajaran:** Normalisasi bukan hanya teori, tetapi praktik penting untuk data integrity

### 6.2.2 Realitas vs Teori

Perancangan database ini juga mengajarkan bagaimana menyeimbangkan teori akademik dengan realitas bisnis:

1. **Derived Attribute**
   - Secara teori, derived attribute tidak disimpan
   - Dalam praktik, kadang perlu disimpan untuk performa (dengan trade-off konsistensi)
   - Solusi: Gunakan view atau trigger untuk maintain derived attribute

2. **Constraint Enforcement**
   - Total participation dalam specialization sulit dienforce di level database
   - Solusi: Enforce di level aplikasi atau gunakan trigger

3. **Performance vs Normalization**
   - 3NF ideal untuk integritas, tetapi kadang perlu denormalisasi untuk performa
   - Solusi: Gunakan index, materialized view, atau caching

### 6.2.3 Konsep EER dalam Praktik

1. **Specialization**
   - Sangat berguna untuk modeling hierarki (USER → CUSTOMER, SELLER)
   - Implementasi dengan Class Hierarchy, Single Table, atau Concrete Table tergantung constraint

2. **Union Type**
   - Konsep yang powerful untuk abstraksi entitas yang berbeda
   - Implementasi dengan surrogate key dan discriminator
   - Harus digunakan dengan hati-hati (hanya ketika benar-benar diperlukan)

3. **Weak Entity**
   - Konsep yang jelas untuk entitas dependent
   - Implementasi dengan PK gabungan dan ON DELETE CASCADE

4. **Multivalued Attribute**
   - Selalu dimapping ke tabel terpisah
   - Penting untuk menjaga 1NF

## 6.3 Kendala yang Dihadapi

### 6.3.1 Kendala Konseptual

1. **Pemilihan Union Type yang Tepat**
   - **Masalah:** Awalnya menggunakan union type COURIER yang tidak valid
   - **Solusi:** Analisis ulang business rules dan menemukan union type VOUCHER_ISSUER yang lebih tepat
   - **Pembelajaran:** Union type harus benar-benar diperlukan, bukan dipaksakan

2. **Constraint Specialization**
   - **Masalah:** Menentukan apakah specialization harus overlap atau disjoint, total atau partial
   - **Solusi:** Analisis use case nyata (user bisa jadi customer dan seller sekaligus)
   - **Pembelajaran:** Constraint harus mencerminkan business rules yang sebenarnya

3. **Atribut Derived**
   - **Masalah:** Menentukan atribut mana yang derived dan tidak perlu disimpan
   - **Solusi:** Identifikasi atribut yang bisa dihitung dari atribut lain
   - **Pembelajaran:** Derived attribute mengurangi redundansi tetapi perlu kalkulasi saat query

### 6.3.2 Kendala Implementasi

1. **Foreign Key Dependency**
   - **Masalah:** Urutan INSERT harus memperhatikan FK dependency
   - **Solusi:** Insert data dengan urutan yang benar (parent dulu, child kemudian)
   - **Pembelajaran:** Perencanaan urutan INSERT penting untuk menghindari FK constraint error

2. **ON DELETE Action**
   - **Masalah:** Menentukan kapan menggunakan CASCADE vs RESTRICT
   - **Solusi:** CASCADE untuk weak entity dan multivalued, RESTRICT untuk data penting
   - **Pembelajaran:** ON DELETE action harus sesuai dengan business rules

3. **CHECK Constraint untuk Union Type**
   - **Masalah:** Membuat CHECK constraint yang enforce disjoint union type
   - **Solusi:** Gunakan OR condition dengan kombinasi IS NOT NULL dan IS NULL
   - **Pembelajaran:** CHECK constraint bisa enforce business rules kompleks

### 6.3.3 Kendala Query

1. **Query Union Type**
   - **Masalah:** Query untuk mendapatkan penerbit voucher perlu JOIN ke VOUCHER_ISSUER lalu LEFT JOIN ke SELLER dan ADMIN
   - **Solusi:** Gunakan COALESCE untuk mendapatkan nama penerbit
   - **Pembelajaran:** Union type menambah kompleksitas query tetapi memberikan abstraksi yang bersih

2. **Derived Attribute dalam Query**
   - **Masalah:** Menghitung derived attribute (total_harga, rating_avg) memerlukan subquery atau aggregate
   - **Solusi:** Gunakan subquery di FROM atau view untuk simplifikasi
   - **Pembelajaran:** Derived attribute trade-off antara storage dan computation

## 6.4 Saran Pengembangan

### 6.4.1 Fitur Tambahan

1. **Sistem Rating Seller**
   - Tambahkan tabel SELLER_RATING untuk rating seller (terpisah dari rating produk)
   - Berguna untuk trust score dan ranking seller

2. **Wishlist dan Cart**
   - Tambahkan tabel WISHLIST dan CART untuk fitur save for later
   - Relasi M:N antara CUSTOMER dan PRODUCT

3. **Notification System**
   - Tambahkan tabel NOTIFICATION untuk notifikasi order, promo, dll
   - Relasi dengan USER atau CUSTOMER

4. **Return dan Refund**
   - Tambahkan tabel RETURN untuk pengembalian barang
   - Relasi dengan ORDER dan SHIPMENT

5. **Chat dan Customer Service**
   - Tambahkan tabel CHAT_MESSAGE untuk komunikasi customer-seller
   - Relasi dengan USER (sender dan receiver)

### 6.4.2 Optimasi

1. **Indexing**
   - Tambahkan index pada kolom yang sering digunakan untuk filtering dan sorting
   - Contoh: INDEX pada PRODUCT.harga, ORDER.tgl_order, REVIEW.rating

2. **Partitioning**
   - Partition tabel ORDER berdasarkan tgl_order (monthly atau yearly)
   - Berguna untuk performa query pada data historis

3. **Materialized View**
   - Buat materialized view untuk derived attribute yang sering diakses
   - Contoh: VIEW untuk total_belanja customer, rating_avg produk

4. **Caching**
   - Implementasi caching untuk data yang jarang berubah (kategori, kurir)
   - Gunakan Redis atau Memcached

### 6.4.3 Keamanan

1. **User Authentication**
   - Tambahkan kolom password_hash di tabel USER
   - Implementasi hashing dengan bcrypt atau argon2

2. **Role-Based Access Control**
   - Tambahkan tabel ROLE dan PERMISSION
   - Implementasi RBAC untuk kontrol akses

3. **Audit Log**
   - Tambahkan tabel AUDIT_LOG untuk tracking perubahan data
   - Berguna untuk compliance dan debugging

4. **Data Encryption**
   - Enkripsi data sensitif (email, nomor telepon)
   - Gunakan encryption at rest dan in transit

### 6.4.4 Skalabilitas

1. **Database Sharding**
   - Shard database berdasarkan seller_id atau customer_id
   - Berguna untuk horizontal scaling

2. **Read Replica**
   - Implementasi read replica untuk query read-heavy
   - Master untuk write, replica untuk read

3. **Microservices**
   - Pisahkan database berdasarkan domain (user service, product service, order service)
   - Implementasi event-driven architecture

---

## PENUTUP

Tugas akhir perancangan database sistem e-commerce ini telah memberikan pemahaman mendalam tentang proses perancangan database dari konsep hingga implementasi. Dimulai dari analisis requirement, pembuatan EER Diagram dengan berbagai konsep advanced (specialization, weak entity, union type), mapping ke skema relasional, hingga implementasi dalam SQL dengan data dan query yang realistis.

Database yang dirancang tidak hanya memenuhi requirement akademik, tetapi juga mencerminkan realitas bisnis e-commerce dengan fitur-fitur seperti sistem rating, loyalty points, voucher dari berbagai sumber, dan logistik yang terintegrasi. Desain ini dapat menjadi fondasi untuk pengembangan aplikasi e-commerce yang scalable dan maintainable.

Pembelajaran terpenting dari tugas ini adalah pentingnya desain yang tepat sejak awal, pemahaman mendalam tentang business rules, dan kemampuan untuk menyeimbangkan teori akademik dengan praktik industri. Database yang baik bukan hanya tentang struktur tabel yang benar, tetapi juga tentang bagaimana struktur tersebut mendukung kebutuhan bisnis dan dapat berkembang seiring waktu.

---

**LAMPIRAN**

File-file yang disertakan dalam tugas ini:
1. `eer_union_valid.html` — EER Diagram interaktif
2. `TAHAP_1_RELATIONAL_SCHEMA.md` — Dokumentasi mapping EER ke relational schema
3. `TAHAP_2_DDL_CREATE_TABLE.sql` — Script DDL untuk membuat database dan tabel
4. `TAHAP_3_DML_INSERT_DATA.sql` — Script DML untuk insert data
5. `TAHAP_4_QUERY_SQL.sql` — Kumpulan query SQL (45+ query)
6. `LAPORAN_LENGKAP_DATABASE_ECOMMERCE.md` — Laporan lengkap (file ini)

---

**DAFTAR PUSTAKA**

1. Elmasri, R., & Navathe, S. B. (2015). *Fundamentals of Database Systems* (7th ed.). Pearson.
2. Silberschatz, A., Korth, H. F., & Sudarshan, S. (2019). *Database System Concepts* (7th ed.). McGraw-Hill.
3. Date, C. J. (2003). *An Introduction to Database Systems* (8th ed.). Addison-Wesley.
4. Connolly, T., & Begg, C. (2014). *Database Systems: A Practical Approach to Design, Implementation, and Management* (6th ed.). Pearson.
5. MySQL Documentation. (2024). Retrieved from https://dev.mysql.com/doc/
6. PostgreSQL Documentation. (2024). Retrieved from https://www.postgresql.org/docs/

---

**TERIMA KASIH**

Terima kasih kepada dosen pengampu mata kuliah Basis Data yang telah memberikan bimbingan dan arahan dalam pengerjaan tugas akhir ini. Semoga laporan ini dapat bermanfaat sebagai referensi pembelajaran perancangan database sistem e-commerce.

---

**© 2024 — Tugas Akhir Basis Data**

