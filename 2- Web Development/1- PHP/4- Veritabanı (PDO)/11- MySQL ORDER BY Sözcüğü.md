ORDER BY sözcüğü, sonuç kümesini artan veya azalan sırada sıralamak için kullanılır.

ORDER BY sözcüğü, varsayılan olarak kayıtları artan sırada sıralar. Kayıtları azalan sırada sıralamak için DESC anahtar sözcüğünü kullanın.

```MySQL
SELECT sutun_ismi FROM tablo_ismi ORDER BY sutun_ismi ASC|DESC 
```

Aşağıdaki örnekte hazır deyimler kullanılmaktadır.

Burada Ziyaretciler tablosundan id, isim ve soyisim sütunlarını seçiyoruz. Kayıtlar soyisim sütununa göre sıralanacak ve bir HTML tablosunda görüntülenecektir:

```PHP title:'PDO ile MySQL verileri sıralama + Hazır Deyimler'
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
  
  $hazirDeyim = $conn->prepare("SELECT id, isim, soyisim FROM Ziyaretciler ORDER BY soyisim");
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