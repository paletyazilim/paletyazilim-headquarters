Bir veritabanı tablosunun kendine özgü bir adı vardır, tablo sütun ve satırlardan oluşur.

`{mysql icon} CREATE TABLE` deyimi MySQL'de bir tablo oluşturmak için kullanılır.

Beş sütunlu "Ziyaretciler" adında bir tablo oluşturacağız: "id", "isim", "soyisim", "eposta" ve "kayit_tarihi":

```MySQL title:'Ziyaretciler Tablosu'
CREATE TABLE Ziyaretciler (
id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
isim VARCHAR(30) NOT NULL,
soyisim VARCHAR(30) NOT NULL,
eposta VARCHAR(50),
kayit_tarihi TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
)
```

Yukarıdaki tabloya ilişki:

Veri türü, sütunun ne tür verileri tutabileceğini belirtir.

Veri türünden sonra, her sütun için diğer isteğe bağlı öznitelikleri belirtebilirsiniz:

- NOT NULL - Her satır o sütun için bir değer içermelidir, null değerlere izin verilmez
- DEFAULT value - Başka bir değer geçilmediğinde eklenen varsayılan bir değer ayarlayın
- UNSIGNED - Sayı türleri için kullanılır, depolanan verileri pozitif sayılar ve sıfır ile sınırlar
- AUTO INCREMENT - MySQL her yeni kayıt eklendiğinde alanın değerini otomatik olarak 1 artırır
- PRIMARY KEY - Bir tablodaki satırları benzersiz bir şekilde tanımlamak için kullanılır. PRIMARY KEY ayarına sahip sütun genellikle bir ID numarasıdır ve genellikle AUTO_INCREMENT ile kullanılır

Her tablo bir birincil anahtar sütununa sahip olmalıdır (bu durumda: "id" sütunu). Değeri tablodaki her kayıt için benzersiz olmalıdır.

Aşağıdaki örnekte PHP'de tablonun nasıl oluşturulacağını göstermektedir:

```PHP title:'PDO ile MySQL tablosu oluşturma'
<?php
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "ornekDB";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
  // İstisna modunu ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

  // Tablo oluşturma sorgumuz
  $sorgu = "CREATE TABLE Ziyaretciler (
  id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
  isim VARCHAR(30) NOT NULL,
  soyisim VARCHAR(30) NOT NULL,
  eposta VARCHAR(50),
  kayit_tarihi TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
  )";

  // Sonuç döndürülmeyeceği için exec() fonksiyonunu kullanın
  $conn->exec($sorgu);
  
  echo "Ziyaretciler tablonuz başarılı bir şekilde oluşturulmuştur.";
} catch(PDOException $e) {
  echo $sql . "<br>" . $e->getMessage();
}

$conn = null;
?>
```