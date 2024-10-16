Birden fazla SQL ifadesi `beginTransaction()` fonksiyonu ile yürütülmelidir.

```PHP title:'PDO ile MySQL çoklu veri ekleme' hl:13,30
<?php
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "ornekDB";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
  // İstisna modunu ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

  // İşlemi Başlatıyoruz
  $conn->beginTransaction();
  
  // Kayıt ekleme sorgularımız
  $conn->exec("INSERT INTO Ziyaretciler (isim, soyisim, eposta)
  VALUES ('Berat', 'Masat', 'beratmasat@ornek.com')");
  $conn->exec("INSERT INTO Ziyaretciler (isim, soyisim, eposta)
  VALUES ('Furkan', 'Masat', 'furkanmasat@ornek.com')");
  $conn->exec("INSERT INTO Ziyaretciler (isim, soyisim, eposta)
  VALUES ('Osman', 'Masat', 'osmanmasat@ornek.com')");

  // İşlemi Gerçekleştir
  $conn->commit();
  
  echo "Kayıtlar başarılı bir şekilde eklendi!";
} catch(PDOException $e) {

  // Herhangi bir işlem yanlış giderse geri al
  $conn->rollback();
  
  echo "Error: " . $e->getMessage();
}

$conn = null;
?>
```