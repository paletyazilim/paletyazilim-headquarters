AUTO_INCREMENT alanı olan bir tabloda INSERT veya UPDATE işlemi yaparsak, son eklenen/güncellenen kaydın ID'sini hemen alabiliriz.

"Ziyaretciler" tablosunda, "id" sütunu bir AUTO_INCREMENT alanıdır:

Aşağıdaki örnekte, son eklenen kaydın ID'sini almak için tek bir kod satırı eklememiz dışında, önceki sayfadaki (PHP MySQL'e Veri Ekleme) örneğiyle aynıdır. Ayrıca son eklenen ID'yi de döndürebiliyoruz:


```PHP title:"PDO ile MySQL'e eklenen son verinin ID'sini alma" hl:19
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

  $son_id = $conn->lastInsertId();
  
  echo "{$son_id} Numaralı kayıt başarılı bir şekilde oluşturuldu";
} catch(PDOException $e) {
  echo $sorgu . "<br>" . $e->getMessage();
}

$conn = null;
?>
```

