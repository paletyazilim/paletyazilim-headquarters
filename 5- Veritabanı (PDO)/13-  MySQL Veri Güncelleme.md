UPDATE sözcüğü, bir tablodaki mevcut kayıtları güncellemek için kullanılır:

```MySQL
UPDATE tablo_ismi
SET sutun1 = deger, sutun2 = deger2,...
WHERE sutun_ismi = deger 
```

UPDATE sözdizimindeki WHERE sözcüğüne dikkat edin: WHERE sözcüğü hangi kayıt veya kayıtların güncellenmesi gerektiğini belirtir. WHERE sözcüğünü atlarsanız, tüm kayıtlar güncellenecektir!

```PHP title:'PDO ile MySQL veri güncelleme'
<?php
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "ornekDB";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
  
  // İstisna modunu ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

  $sorgu = "UPDATE Ziyaretciler SET isim='Ay' WHERE id=2";

  // Sorguyu hazırlama
  $stmt = $conn->prepare($sorgu);

  // execute the query
  $stmt->execute();

  // echo a message to say the UPDATE succeeded
  echo $stmt->rowCount() . " records UPDATED successfully";
} catch(PDOException $e) {
  echo $sql . "<br>" . $e->getMessage();
}

$conn = null;
?>
```


