PHP 5 sürümüyle beraber MySQL veritabanı ile çalışabilir:

- MySQLi uzantısı ("i" improved (geliştirilmiş) anlamına gelir)
- PDO (PHP Data Objects)

PHP'nin önceki sürümleri MySQL eklentisini kullanıyordu. Ancak, bu uzantı 2012 yılında kullanımdan kaldırılmıştır.

### MySQLi mi PDO mu Kullanmalıyım?
---
Hem MySQLi hem de PDO'nun avantajları vardır:

PDO 12 farklı veritabanı sisteminde çalışırken, MySQLi yalnızca MySQL veritabanlarıyla çalışacaktır.

Dolayısıyla, projenizi başka bir veritabanı kullanmak üzere değiştirmek zorunda kalırsanız, PDO işlemi kolaylaştırır. Sadece bağlantı dizesini ve birkaç sorguyu değiştirmeniz yeterlidir. MySQLi ile, sorgular dahil tüm kodu yeniden yazmanız gerekecektir.

Her ikisi de nesne yönelimlidir, ancak MySQLi aynı zamanda prosedürel bir API sunar.

Her ikisi de Hazırlanmış İfadeleri destekler. Hazırlanmış İfadeler SQL enjeksiyonundan korur ve web uygulaması güvenliği için çok önemlidir.

### MySQL Bağlantısını Açma
---
MySQL veritabanındaki verilere erişebilmemiz için önce sunucuya bağlanabilmemiz gerekir:

```PHP title:'PDO ile MySQL Veritabanına Bağlanma'
<?php
$sunucuIsmi = "localhost";
$kullaniciAdi = "username";
$parola = "password";

try {
  $conn = new PDO("mysql:host=$sunucuIsmi;dbname=myDB", $kullaniciAdi, $parola);
  // PDO hata modunu istisna olarak ayarlıyoruz
  $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
  echo "Başarılı bir şekilde veritabanına bağlanıldı";
} catch(PDOException $e) {
  echo "Veritabanına bağlantı başarısız: " . $e->getMessage();
}
?>
```

**Not:** Yukarıdaki PDO örneğinde ayrıca bir veritabanı (`myDB`) belirttik. PDO bağlanmak için geçerli bir veritabanı gerektirir. Herhangi bir veritabanı belirtilmezse, bir istisna atılır.

**İpucu:** PDO'nun en büyük avantajlarından biri, veritabanı sorgularımızda oluşabilecek sorunları ele almak için bir istisna sınıfına sahip olmasıdır. `try` bloğu içinde bir istisna atılırsa, kod yürütülmeyi durdurur ve doğrudan ilk `catch()` bloğunu çalıştırır.

### MySQL Bağlantısını Kapatma
---
Kod sona erdiğinde bağlantı otomatik olarak kapanacaktır. Bağlantıyı daha önce kapatmak için aşağıdakileri kullanın:

```PHP title:'PDO ile bağlantıyı kapatma'
$conn = null;
```