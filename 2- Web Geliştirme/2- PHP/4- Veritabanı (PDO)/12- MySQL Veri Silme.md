DELETE sözcüğü bir tablodan kayıtları silmek için kullanılır:

```MySQL
DELETE FROM tablo_ismi
WHERE sutun_ismi = deger
```

DELETE sözdizimindeki WHERE sözcüğüne dikkat edin: WHERE sözcüğü hangi kayıt ya da kayıtların silinmesi gerektiğini belirtir. WHERE cümlesini atlarsanız, tüm kayıtlar silinecektir!

```PHP title:'PDO ile MySQL veri silme'
<?php
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "ornekDB";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
  
  // İstisna Modunu Ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

  // Kayıt silme sorgusu
  $sorgu = "DELETE FROM Ziyaretciler WHERE id=3";

  // Sonuç döndürülmeyeceği için exec() fonksiyonunu kullanın
  $conn->exec($sorgu);
  
  echo "Kayıt başarılı bir şekilde silindi";
  
} catch(PDOException $e) {
  echo $sorgu . "<br>" . $e->getMessage();
}

$conn = null;
?>
```