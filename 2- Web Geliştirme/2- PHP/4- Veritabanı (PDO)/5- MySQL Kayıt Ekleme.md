Bir veritabanı ve bir tablo oluşturulduktan sonra, bunlara veri eklemeye başlayabiliriz.

İşte uyulması gereken bazı sözdizimi kuralları:

- SQL sorgusu PHP'de alıntılanmalıdır
- SQL sorgusu içindeki dize değerleri tırnak içine alınmalıdır
- Sayısal değerler tırnak içine alınmamalıdır
- NULL kelimesi alıntılanmamalıdır

INSERT INTO deyimi, bir MySQL tablosuna yeni kayıtlar eklemek için kullanılır:

```MySQL 
INSERT INTO Ziyaretciler (isim, soyisim, eposta)
VALUES ('Berat', 'Masat', 'beratmasat@ornek.com')
```

Önceki bölümde beş sütunlu "Ziyaretciler" adında boş bir tablo oluşturduk: "id", "isim", "soyisim", "eposta" ve "kayit_tarihi". Şimdi, tabloyu yukarıdaki verilerle dolduralım.

**Not:** Bir sütun `AUTO_INCREMENT` ("id" sütunu gibi) veya `CURRENT_TIMESTAMP` varsayılan değerli TIMESTAMP ("kayit_tarihi" sütunu gibi) ise, SQL sorgusunda belirtilmesine gerek yoktur; MySQL değeri otomatik olarak ekleyecektir.

```PHP title:'PDO ile MySQL tablosuna veri ekleme'
<?php
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "ornekDB";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
  // İstisna modunu ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

  // Kayıt ekleme sorgumuz
  $sorgu = "INSERT INTO MyGuests (isim, soyisim, eposta)
  VALUES ('John', 'Doe', 'john@example.com')";
  
  // Sonuç döndürülmeyeceği için exec() fonksiyonunu kullanın
  $conn->exec($sorgu);
  
  echo "Yeni kayıt başarılı bir şekilde oluşturuldu";
} catch(PDOException $e) {
  echo $sorgu . "<br>" . $e->getMessage();
}

$conn = null;
?>
```