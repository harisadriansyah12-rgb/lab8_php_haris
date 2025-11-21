# Lab8 php database
# Haris Adriansyah
# 312410334
# TI.24. A4
# Pemograman Web
# Agung Nugroho, S.Kom., M.Kom.

# Tugas

<img width="914" height="275" alt="Cuplikan layar 2025-11-20 143419" src="https://github.com/user-attachments/assets/7aae5a47-69cf-417c-8e59-66a4297244c1" />

## 1. membuat database di XAMPP
Buka Aplikasi XAMPP lalu pergi ke `phpmyadmin` 
masukan sql
```python
CREATE TABLE data_barang (
id_barang int(10) auto_increment Primary Key,
kategori varchar(30),
nama varchar(30),
gambar varchar(100),
harga_beli decimal(10,0),
harga_jual decimal(10,0),
stok int(4)
);
```
Setelah Membuat `CREATE TABLE` Lalu Tambahkan data dengan kode:
```python
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);
```
### hasilnya

<img width="1822" height="999" alt="Cuplikan layar 2025-11-21 002553" src="https://github.com/user-attachments/assets/56c07957-f114-4c11-bd8a-64767ce0d735" />

## 2. Membuat Program CRUD
Kemudian untuk mengakses direktory tersebut pada web server dengan mengakses URL:
`http://localhost/lab8_php_databse/`
kode `index.php`
```python 
<?php
include("koneksi.php");

// Query untuk menampilkan data
$sql = "SELECT * FROM data_barang";
$result = mysqli_query($conn, $sql);
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Data Barang</title>
</head>
<body>
    <div class="container">
        <h1>Data Barang</h1>

        <!-- Tombol Tambah Data -->
        <a href="tambah.php" class="btn-tambah">+ Tambah Barang</a>

        <div class="main">
            <table>
                <tr>
                    <th>Gambar</th>
                    <th>Nama Barang</th>
                    <th>Kategori</th>
                    <th>Harga Jual</th>
                    <th>Harga Beli</th>
                    <th>Stok</th>
                    <th>Aksi</th>
                </tr>

                <?php if ($result && mysqli_num_rows($result) > 0): ?>
                    <?php while ($row = mysqli_fetch_assoc($result)): ?>
                        <tr>
                            <td>
                                <img src="gambar/<?= $row['gambar']; ?>" 
                                     alt="<?= $row['nama']; ?>" width="100">
                            </td>
                            <td><?= $row['nama']; ?></td>
                            <td><?= $row['kategori']; ?></td>
                            <td><?= $row['harga_jual']; ?></td>
                            <td><?= $row['harga_beli']; ?></td>
                            <td><?= $row['stok']; ?></td>
                            <td>
                                <a href="edit.php?id=<?= $row['id_barang']; ?>">Edit</a> |
                                <a href="hapus.php?id=<?= $row['id_barang']; ?>" 
                                   onclick="return confirm('Yakin ingin menghapus?')">
                                   Hapus
                                </a>
                            </td>
                        </tr>
                    <?php endwhile; ?>
                <?php else: ?>
                    <tr>
                        <td colspan="7">Belum ada data</td>
                    </tr>
                <?php endif; ?>
            </table>
        </div>
    </div>
</body>
</html>
```
di tambah dengan kode `koneksi.php`
```python
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "latihan1";
$conn = mysqli_connect($host, $user, $pass, $db);
if ($conn == false)
{
echo "Koneksi ke server gagal.";
die();
} #else echo "Koneksi berhasil";
?>
```
Lalu tinggal tambah gambar handphone yang sesuai nama file `jpg`
### hasilnya

<img width="1099" height="615" alt="Cuplikan layar 2025-11-21 002608" src="https://github.com/user-attachments/assets/e6b8a832-f6f3-4e9a-865f-1afc2be92ab3" />


Tambah kode `tambah.php`
```python
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

if (isset($_POST['submit'])) {
    $nama        = $_POST['nama'];
    $kategori    = $_POST['kategori'];
    $harga_jual  = $_POST['harga_jual'];
    $harga_beli  = $_POST['harga_beli'];
    $stok        = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];

    $gambar = null;

    // Upload gambar jika tidak ada error
    if ($file_gambar['error'] === 0) {
        $filename    = str_replace(' ', '_', $file_gambar['name']);
        $destination = dirname(__FILE__) . '/gambar/' . $filename;

        if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
            $gambar = 'gambar/' . $filename;
        }
    }

    // Query insert data
    $sql = "
        INSERT INTO data_barang (nama, kategori, harga_jual, harga_beli, stok, gambar)
        VALUES ('{$nama}', '{$kategori}', '{$harga_jual}', '{$harga_beli}', '{$stok}', '{$gambar}')
    ";

    mysqli_query($conn, $sql);

    header('Location: index.php');
    exit;
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css">
    <title>Tambah Barang</title>
</head>
<body>
    <div class="container">
        <h1>Tambah Barang</h1>
        <div class="main">

            <form method="post" action="tambah.php" enctype="multipart/form-data">
                
                <div class="input">
                    <label>Nama Barang</label>
                    <input type="text" name="nama" required>
                </div>

                <div class="input">
                    <label>Kategori</label>
                    <select name="kategori" required>
                        <option value="Komputer">Komputer</option>
                        <option value="Elektronik">Elektronik</option>
                        <option value="Hand Phone">Hand Phone</option>
                    </select>
                </div>

                <div class="input">
                    <label>Harga Jual</label>
                    <input type="number" name="harga_jual" required>
                </div>

                <div class="input">
                    <label>Harga Beli</label>
                    <input type="number" name="harga_beli" required>
                </div>

                <div class="input">
                    <label>Stok</label>
                    <input type="number" name="stok" required>
                </div>

                <div class="input">
                    <label>File Gambar</label>
                    <input type="file" name="file_gambar" accept="image/*">
                </div>

                <div class="submit">
                    <input type="submit" name="submit" value="Simpan">
                </div>

            </form>

        </div>
    </div>
</body>
</html>
```
tambah kode `ubah.php`
```python
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

if (isset($_POST['submit'])) {
    $id          = $_POST['id'];
    $nama        = $_POST['nama'];
    $kategori    = $_POST['kategori'];
    $harga_jual  = $_POST['harga_jual'];
    $harga_beli  = $_POST['harga_beli'];
    $stok        = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];
    $gambar      = null;

    // Upload gambar jika ada
    if ($file_gambar['error'] == 0) {
        $filename    = str_replace(' ', '_', $file_gambar['name']);
        $destination = dirname(__FILE__) . '/gambar/' . $filename;

        if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
            $gambar = 'gambar/' . $filename;
        }
    }

    // Query update
    $sql  = "UPDATE data_barang SET ";
    $sql .= "nama = '{$nama}', kategori = '{$kategori}', ";
    $sql .= "harga_jual = '{$harga_jual}', harga_beli = '{$harga_beli}', ";
    $sql .= "stok = '{$stok}' ";

    if (!empty($gambar)) {
        $sql .= ", gambar = '{$gambar}' ";
    }

    $sql .= "WHERE id_barang = '{$id}'";

    $result = mysqli_query($conn, $sql);

    header('location: index.php');
    exit;
}

// Ambil data berdasarkan ID
$id     = $_GET['id'];
$sql    = "SELECT * FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);

if (!$result) die('Error: Data tidak tersedia');

$data = mysqli_fetch_array($result);

// Helper untuk select dropdown
function is_select($val, $current) {
    return $val == $current ? 'selected="selected"' : '';
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Ubah Barang</title>
</head>
<body>
<div class="container">
    <h1>Ubah Barang</h1>

    <div class="main">
        <form method="post" action="ubah.php" enctype="multipart/form-data">

            <div class="input">
                <label>Nama Barang</label>
                <input type="text" name="nama" value="<?php echo $data['nama']; ?>" />
            </div>

            <div class="input">
                <label>Kategori</label>
                <select name="kategori">
                    <option value="Komputer"   <?php echo is_select('Komputer', $data['kategori']); ?>>Komputer</option>
                    <option value="Elektronik" <?php echo is_select('Elektronik', $data['kategori']); ?>>Elektronik</option>
                    <option value="Hand Phone" <?php echo is_select('Hand Phone', $data['kategori']); ?>>Hand Phone</option>
                </select>
            </div>

            <div class="input">
                <label>Harga Jual</label>
                <input type="text" name="harga_jual" value="<?php echo $data['harga_jual']; ?>" />
            </div>

            <div class="input">
                <label>Harga Beli</label>
                <input type="text" name="harga_beli" value="<?php echo $data['harga_beli']; ?>" />
            </div>

            <div class="input">
                <label>Stok</label>
                <input type="text" name="stok" value="<?php echo $data['stok']; ?>" />
            </div>

            <div class="input">
                <label>File Gambar</label>
                <input type="file" name="file_gambar" />
            </div>

            <div class="submit">
                <input type="hidden" name="id" value="<?php echo $data['id_barang']; ?>" />
                <input type="submit" name="submit" value="Simpan" />
            </div>

        </form>
    </div>
</div>
</body>
</html>
```
dan terakhir tambah kode `hapus.php`
```python
<?php
include_once 'koneksi.php';

// Ambil ID dari URL
$id = $_GET['id'];

// Query hapus data
$sql = "DELETE FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);

// Redirect ke halaman utama
header('Location: index.php');
exit;
?>
```
### hasil akhir

<img width="1099" height="615" alt="Cuplikan layar 2025-11-21 002608" src="https://github.com/user-attachments/assets/ae7f027c-8bfe-4970-9b8b-cc346dba16a2" />
