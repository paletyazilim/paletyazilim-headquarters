Hazır deyim, aynı (veya benzer) SQL deyimlerini yüksek verimlilikle tekrar tekrar çalıştırmak için kullanılan bir özelliktir.

Hazırlanan beyanlar temel olarak şu şekilde çalışır:

- **Hazırlayın:** Bir SQL deyimi şablonu oluşturulur ve veritabanına gönderilir. Belirli değerler belirtilmeden bırakılır, bunlara parametre denir ("?" olarak etiketlenir). Örnek: INSERT INTO Ziyaretciler VALUES(?, ?, ?)
- **Veritabanı:** SQL deyim şablonunu ayrıştırır, derler ve sorgu optimizasyonunu gerçekleştirir ve sonucu çalıştırmadan saklar
- **Çalıştır:** Daha sonraki bir zamanda, uygulama değerleri parametrelere bağlar ve veritabanı deyimi yürütür. Uygulama, deyimi farklı değerlerle istediği kadar çalıştırabilir

SQL deyimlerini doğrudan çalıştırmakla karşılaştırıldığında, hazır deyimlerin üç ana avantajı vardır:

- Hazırlanan deyimler, sorgu üzerindeki hazırlık yalnızca bir kez yapıldığı için ayrıştırma süresini azaltır (deyim birden çok kez yürütülmesine rağmen)
- Bağlı parametreler, her seferinde tüm sorguyu değil yalnızca parametreleri göndermeniz gerektiğinden sunucuya giden bant genişliğini en aza indirir
- Hazırlanmış ifadeler SQL enjeksiyonlarına karşı çok kullanışlıdır, çünkü daha sonra farklı bir protokol kullanılarak iletilen parametre değerlerinin doğru bir şekilde öncelenmesi gerekmez. Orijinal deyim şablonu harici girdiden türetilmemişse, SQL enjeksiyonu gerçekleşemez.

```PHP title:'PDO ile MySQL Hazır Deyimler'
<?php
$servername = "localhost";
$username = "username";
$password = "password";
$dbname = "ornekDB";

try {
  $conn = new PDO("mysql:host=$servername;dbname=$dbname", $username, $password);
  // İstisna modunu ayarlama
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

  // Hazır Deyimi ayarlama
  $hazirDeyim = $conn->prepare("INSERT INTO Ziyaretciler (isim, soyisim, eposta)
  VALUES (:isim_p, :soyisim_p, :eposta_p)");
  $hazirDeyim->bindParam(':isim_p', $isim);
  $hazirDeyim->bindParam(':soyisim_p', $soyisim);
  $hazirDeyim->bindParam(':eposta_p', $eposta);

  // Kayıt #1
  $isim = "Berat";
  $soyisim = "Masat";
  $eposta = "beratmasat@ornek.com";
  $hazirDeyim->execute();

  // Kayıt #2
  $isim = "Furkan";
  $soyisim = "Masat";
  $eposta = "furkanmasat@ornek.com";
  $hazirDeyim->execute();

  // Kayıt #3
  $isim = "Osman";
  $soyisim = "Masat";
  $eposta = "osmanmasat@ornek.com";
  $hazirDeyim->execute();

  echo "Kayıtlar başarılı bir şekilde oluşturuldu.";
} catch(PDOException $e) {
  echo "Hata: " . $e->getMessage();
}
$conn = null;
?>
```