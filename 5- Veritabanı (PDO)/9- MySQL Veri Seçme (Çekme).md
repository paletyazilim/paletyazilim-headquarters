`SELECT` deyimi, bir veya daha fazla tablodan veri seçmek için kullanılır:

```MySQL 
SELECT sutun_isimleri FROM tablo_ismi
```

veya bir tablodan TÜM sütunları seçmek için * karakterini kullanabiliriz:

```MySQL
SELECT * FROM tablo_ismi
```

Aşağıdaki örnekte hazır deyimler kullanılmaktadır.

Ziyaretciler tablosundan id, isim ve soyisim sütunlarını seçer ve bir HTML tablosunda görüntüler:

```PHP title:'PDO ile MySQL verileri seçme + Hazır Deyimler'
<?php
echo "<table>";

echo "<tr><th>Id</th><th>İsim</th><th>Soyisim</th></tr>";

class TabloSatirlari extends RecursiveIteratorIterator {
  function __construct($it) {
    parent::__construct($it, self::LEAVES_ONLY);
  }

  function current() {
    return "<td>" . parent::current(). "</td>";
  }

  function beginChildren() {
    echo "<tr>";
  }

  function endChildren() {
    echo "</tr>" . "\n";
  }
}

$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "ornekDB";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
  // İstisna modunu ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
  
  $hazirDeyim = $conn->prepare("SELECT id, isim, soyisim FROM Ziyaretciler");
  $hazirDeyim->execute();

  // Ortaya çıkan diziyi ilişkisel dizi olarak ayarlama
  $sonuc = $hazirDeyim->setFetchMode(PDO::FETCH_ASSOC);
  
  foreach(new TabloSatirlari(new RecursiveArrayIterator($hazirDeyim->fetchAll())) as $anahtar=>$deger) {
    echo $deger;
  }
  
} catch(PDOException $e) {
  echo "Hata: " . $e->getMessage();
}
$conn = null;

echo "</table>";
?>
```