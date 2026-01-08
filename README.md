# UAS-PMROWEB
# 📚 PINK LIBRARY - Sistem Perpustakaan
**Tugas UAS Pemrograman Web**

**Nama:** Ghefira Azka Fardani  

**NIM:** 312410521 

**Kelas:** TI.24.A5 

---

## 📌 Deskripsi Project

Aplikasi web sistem perpustakaan yang dibangun menggunakan **PHP Native** dengan konsep:
- ✅ **OOP (Object Oriented Programming)**
- ✅ **MVC Sederhana (Model-View-Controller)**
- ✅ **Routing menggunakan .htaccess**
- ✅ **Responsive Design** (Mobile First) dengan Bootstrap
- ✅ **Login Multi-Role** (Admin & User)
- ✅ **CRUD Lengkap** (Create, Read, Update, Delete)
- ✅ **Search & Pagination**

---

## 🎯 Fitur Utama

### 🔐 1. Sistem Login dengan Role
- **Admin** dapat melakukan CRUD (tambah, edit, hapus buku)
- **User** hanya dapat melihat dan mencari buku

### 👑 2. Dashboard Admin
- Menampilkan statistik (total buku & user)
- Form tambah/edit buku
- Fitur search buku
- Pagination (5 buku per halaman)
- Aksi edit & hapus buku

### 👤 3. Dashboard User
- Melihat daftar buku
- Search buku berdasarkan judul/penulis
- Pagination

### 🎨 4. Desain Responsif
- Menggunakan **Bootstrap 5.3.2**
- Warna konsisten: `#D01760` (Pink), `#FFF5F7` (Background)
- Modern dengan gradient, shadow, dan hover effect

---

## 📁 Struktur Folder Project
```
perpustakaan-app/
│
├── app/
│   ├── Config.php           → Koneksi database (OOP)
│   └── Controller.php       → Logika CRUD & Login (OOP)
│
├── public/
│   ├── css/
│   │   └── style.css        → Styling custom
│   ├── index.php            → Router utama (satu pintu)
│   └── .htaccess            → URL rewriting (routing)
│
└── views/
    ├── layout/
    │   ├── header.php       → Template atas (navbar)
    │   └── footer.php       → Template bawah
    │
    ├── login.php            → Halaman login
    ├── daftar.php           → Halaman registrasi
    ├── dashboard.php        → Dashboard user
    └── admin_dashboard.php  → Dashboard admin (CRUD)
```

---

## 🔧 Penjelasan File Utama 

### 1️⃣ **OOP - Koneksi Database** (`app/Config.php`)

File ini menggunakan konsep **Class** untuk koneksi database.
```php
<?php
class Config {
    protected $conn;

    public function __construct() {
        $this->conn = mysqli_connect("localhost", "root", "", "db_perpustakaan");

        if (!$this->conn) {
            die("Koneksi database gagal: " . mysqli_connect_error());
        }
    }
}
```

**Penjelasan:**
- Menggunakan **OOP** dengan class `Config`
- Property `$conn` untuk menyimpan koneksi database
- Constructor `__construct()` otomatis dijalankan saat class dipanggil

---

### 2️⃣ **OOP - Controller/Model** (`app/Controller.php`)

File ini berisi **semua logika bisnis** seperti login, CRUD, search.
```php
<?php
class Controller extends Config {

    // Login
    public function login($username, $password) {
        $q = mysqli_query($this->conn,
            "SELECT * FROM users 
             WHERE username='$username' AND password='$password'"
        );
        return mysqli_fetch_assoc($q);
    }

    // Tambah Buku
    public function tambahBuku($judul, $penulis, $stok) {
        return mysqli_query($this->conn,
            "INSERT INTO buku VALUES (NULL,'$judul','$penulis','$stok')"
        );
    }

    // Edit Buku
    public function editBuku($id, $judul, $penulis, $stok) {
        return mysqli_query($this->conn,
            "UPDATE buku SET judul='$judul', penulis='$penulis', stok='$stok' 
             WHERE id='$id'"
        );
    }

    // Hapus Buku
    public function hapusBuku($id) {
        return mysqli_query($this->conn, "DELETE FROM buku WHERE id='$id'");
    }

    // Get Buku dengan Search & Pagination
    public function getBuku($search = '', $limit = 5, $offset = 0) {
        $where = $search ? "WHERE judul LIKE '%$search%' OR penulis LIKE '%$search%'" : "";
        return mysqli_query($this->conn, 
            "SELECT * FROM buku $where LIMIT $limit OFFSET $offset"
        );
    }
}
```

**Penjelasan:**
- Class `Controller` **extends** (mewarisi) dari `Config`
- Semua method menggunakan `$this->conn` dari parent class
- Terdapat method untuk **login, daftar, CRUD, search, pagination**

---

### 3️⃣ **Routing dengan .htaccess** (`public/.htaccess`)

File ini mengubah URL menjadi SEO-friendly tanpa `.php`.
```apache
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php?url=$1 [L,QSA]
```

**Penjelasan:**
- `RewriteEngine On` → mengaktifkan URL rewriting
- Jika file/folder tidak ditemukan, arahkan ke `index.php?url=...`
- Contoh: `http://localhost/perpustakaan-app/public/login` → `index.php?url=login`

---

### 4️⃣ **Router Utama** (`public/index.php`)

File ini adalah **pintu masuk satu-satunya** (single entry point).
```php
<?php
session_start();

require_once '../app/Config.php';
require_once '../app/Controller.php';

$url = isset($_GET['url']) ? $_GET['url'] : 'login';

switch ($url) {
    case 'login':
        include '../views/login.php';
        break;

    case 'daftar':
        include '../views/daftar.php';
        break;

    case 'dashboard':
        include '../views/dashboard.php';
        break;

    case 'admin':
        include '../views/admin_dashboard.php';
        break;

    case 'logout':
        session_destroy();
        header("Location: login");
        break;

    default:
        echo "<h3>404 - Halaman tidak ditemukan</h3>";
}
```

**Penjelasan:**
- Semua request masuk ke sini (karena .htaccess)
- `switch` mengarahkan ke view yang sesuai
- **MVC Pattern**: Router (index.php) → Controller → View

---

### 5️⃣ **Modular - Header & Footer** (`views/layout/`)

Setiap halaman menggunakan template yang sama.

**`views/layout/header.php`:**
```php
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📚 Pink Library</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="/perpustakaan-app/public/css/style.css">
</head>
<body>
<nav class="navbar">
    <div class="container">
        <span class="navbar-brand text-white">📚 Pink Library</span>
        <?php if(isset($_SESSION['username'])): ?>
            <a href="logout" class="btn btn-light btn-sm">Logout</a>
        <?php endif; ?>
    </div>
</nav>
<div class="container mt-4">
```

**`views/layout/footer.php`:**
```php
</div>
<footer class="text-center py-4 mt-5">
    <p class="text-pink mb-0 fw-bold">
        📚 Tugas UAS Pemrograman Web - Pink Library
    </p>
</footer>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**Penjelasan:**
- Setiap view tinggal panggil `include 'layout/header.php'` dan `include 'layout/footer.php'`
- Navbar otomatis muncul di semua halaman
- **DRY Principle** (Don't Repeat Yourself)

---

### 6️⃣ **Login Multi-Role** (`views/login.php`)

Login yang membedakan admin dan user.
```php
<?php
$ctrl = new Controller();

if (isset($_POST['login'])) {
    $user = $ctrl->login($_POST['username'], $_POST['password']);

    if ($user) {
        $_SESSION['username'] = $user['username'];
        $_SESSION['role'] = $user['role'];
        
        if ($user['role'] == 'admin') {
            header("Location: admin");
        } else {
            header("Location: dashboard");
        }
        exit;
    } else {
        $error = "Username atau password salah!";
    }
}
?>
```

**Penjelasan:**
- Menggunakan `$ctrl->login()` dari Controller (OOP)
- Jika `role = admin` → ke `admin_dashboard.php`
- Jika `role = user` → ke `dashboard.php`
- Menggunakan **session** untuk menyimpan data login

---

### 7️⃣ **CRUD Lengkap** (`views/admin_dashboard.php`)

Admin bisa tambah, edit, hapus buku.

**Fitur:**
- ✅ Form tambah/edit buku
- ✅ Tabel daftar buku
- ✅ Button edit & hapus
- ✅ Search buku
- ✅ Pagination
```php
// TAMBAH BUKU
if (isset($_POST['tambah'])) {
    if ($ctrl->tambahBuku($_POST['judul'], $_POST['penulis'], $_POST['stok'])) {
        $success = "Buku berhasil ditambahkan!";
    }
}

// EDIT BUKU
if (isset($_POST['edit'])) {
    if ($ctrl->editBuku($_POST['id'], $_POST['judul'], $_POST['penulis'], $_POST['stok'])) {
        $success = "Buku berhasil diupdate!";
    }
}

// HAPUS BUKU
if (isset($_GET['hapus'])) {
    if ($ctrl->hapusBuku($_GET['hapus'])) {
        $success = "Buku berhasil dihapus!";
    }
}
```

---

### 8️⃣ **Search & Pagination** (Filter Pencarian)

Fitur search berdasarkan judul/penulis + pagination 5 buku per halaman.
```php
$limit = 5;
$page = isset($_GET['page']) ? (int)$_GET['page'] : 1;
$offset = ($page - 1) * $limit;
$search = isset($_GET['search']) ? $_GET['search'] : '';

$data = $ctrl->getBuku($search, $limit, $offset);
$total = $ctrl->countBuku($search);
$totalPages = ceil($total / $limit);
```

**Penjelasan:**
- User bisa search buku
- Data ditampilkan 5 per halaman
- Ada navigasi halaman (1, 2, 3, ...)

---

## 🗄️ Database

**Nama Database:** `db_perpustakaan`

**Tabel `users`:**
```sql
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE,
    password VARCHAR(50),
    role ENUM('admin','user') DEFAULT 'user'
);

INSERT INTO users VALUES 
(1, 'admingepi', '222', 'admin'),
(2, 'gepiimut', '111', 'user');
```

**Tabel `buku`:**
```sql
CREATE TABLE buku (
    id INT PRIMARY KEY AUTO_INCREMENT,
    judul VARCHAR(100),
    penulis VARCHAR(100),
    stok INT DEFAULT 0
);

INSERT INTO buku VALUES 
(1, 'Laskar Pelangi', 'Andrea Hirata', 15),
(2, 'Bumi Manusia', 'Pramoedya Ananta Toer', 8),
(3, 'Hujan', 'Tere Liye', 20);
```

---

## 🎨 File Tambahan (Styling)

### `public/css/style.css`

File CSS untuk mempercantik tampilan dengan:
- Gradient background
- Button hover effect
- Card shadow & animation
- Table hover
- Responsive design

**Warna Utama:**
- `#D01760` → Pink utama (navbar, button, text)
- `#FFF5F7` → Background halaman
- `#FFDAE9` → Aksen (border, badge)

---

## 📸 Screenshot Aplikasi

### 1️⃣ Halaman Login
**Deskripsi:** Tampilan form login dengan design modern, ada icon, gradient button.  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/login.png)

---

### 2️⃣ Halaman Registrasi/Daftar
**Deskripsi:** Form daftar akun baru dengan validasi username.  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/daftar.png)

---

### 3️⃣ Dashboard User
**Deskripsi:** User hanya bisa **melihat** daftar buku, ada search & pagination.  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/dashboard%20user%201.png)
![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/dashboard%20user%202.png)

---

### 4️⃣ Dashboard Admin - Statistik
**Deskripsi:** Admin melihat total buku & user dalam card statistik.  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/dashboard%20admin%202.png)

---

### 5️⃣ Dashboard Admin - Form Tambah Buku
**Deskripsi:** Form untuk menambah buku baru (judul, penulis, stok).  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/dashboard%20admin%201.png)

---

### 6️⃣ Dashboard Admin - Tabel CRUD
**Deskripsi:** Tabel daftar buku dengan tombol **Edit** dan **Hapus**.  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/dashboard%20admin%202.png)

---

### 7️⃣ Edit Buku
**Deskripsi:** Saat klik "Edit", form otomatis terisi data buku yang mau diedit.  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/edit%20buku.png)

---

### 8️⃣ Search Buku
**Deskripsi:** User/Admin bisa search buku berdasarkan judul atau penulis.  

![foto](https://github.com/azkaa-pixel/UAS-PMROWEB/blob/f0b443faf12440d7c9ed85996308a7ee262bf9fc/uas%20pemrograman%20web/cari.png)

---

## ✅ Checklist Tugas

- ✅ **OOP** → `Config.php` & `Controller.php` menggunakan class
- ✅ **Modular** → Struktur folder MVC sederhana
- ✅ **Routing** → `.htaccess` + `index.php` sebagai router
- ✅ **Responsive Design** → Bootstrap + Mobile First
- ✅ **Login Multi-Role** → Admin & User berbeda akses
- ✅ **CRUD** → Create, Read, Update, Delete buku
- ✅ **Search** → Filter berdasarkan judul/penulis
- ✅ **Pagination** → 5 data per halaman

---

## 👨‍💻 Teknologi yang Digunakan

- **PHP 7.4+** → Backend logic
- **MySQL** → Database
- **Bootstrap 5.3.2** → CSS Framework
- **Font Awesome 6.4** → Icon
- **Apache .htaccess** → URL Routing

---

## 📝 Kesimpulan

Project ini berhasil memenuhi semua ketentuan tugas UAS:
1. Menggunakan **OOP** untuk koneksi dan logika
2. Struktur **MVC sederhana** yang modular
3. **Routing** dengan `.htaccess` untuk URL clean
4. **Login multi-role** dengan session
5. **CRUD lengkap** dengan search & pagination
6. Design **responsive** dan modern

