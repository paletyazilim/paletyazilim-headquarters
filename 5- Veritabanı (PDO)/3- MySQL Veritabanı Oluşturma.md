Bir veritabanı bir veya daha fazla tablodan oluşur.

Bir MySQL veritabanı oluşturmak veya silmek için özel CREATE ayrıcalıklarına ihtiyacınız olacaktır.

```PHP title:'PDO ile MySQL Veritabanı Oluşturma'
<?php
$servername = "localhost";
$username = "username";
$password = "password";

try {
  $conn = new PDO("mysql:host=$servername", $username, $password);
  // İstisna modunu ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
  
  $sorgu = "CREATE DATABASE ornekDB";
  // Sonuç döndürülmeyeceği için exec() fonksiyonunu kullanın
  $conn->exec($sorgu);
  echo "Veritabanı başarılı bir şekilde oluşturuldu!<br>";
  
} catch(PDOException $e) {
  echo $sorgu . "<br>" . $e->getMessage();
}

$conn = null;
?>
```