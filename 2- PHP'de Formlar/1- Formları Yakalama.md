PHP supergloballeri `$_GET` ve `$_POST` form verilerini toplamak için kullanılır.

Kullanıcı formu doldurup gönder düğmesine tıkladığında, form verileri işlenmek üzere bir PHP dosyasına gönderilir. Form verileri `method` niteliğinde belirlenen HTTP yöntemi ile gönderilir.

### `GET`
---

```HTML title:'Formumuz'
<html>
<body>

<form action="hosgeldin.php" method="GET">
İsim: <input type="text" name="isim"><br>
E-posta: <input type="text" name="eposta"><br>
<input type="submit">
</form>

</body>
</html>
```

```PHP title:'Hoşgeldin Sayfamız'
<html>
<body>

Merhaba <?php echo $_GET["isim"]; ?><br>
E-posta Adresiniz: <?php echo $_GET["eposta"]; ?>

</body>
</html>
```

Çıktı şuna benzer:

``` title:'Çıktı'
Merhaba Berat
E-posta Adresiniz: beratmasat@birisi.com
```

### `GET`
---

```HTML title:'Formumuz'
<html>
<body>

<form action="hosgeldin.php" method="POST">
İsim: <input type="text" name="isim"><br>
E-posta: <input type="text" name="eposta"><br>
<input type="submit">
</form>

</body>
</html>
```

```PHP title:'Hoşgeldin Sayfamız'
<html>
<body>

Merhaba <?php echo $_POST["isim"]; ?><br>
E-posta Adresiniz: <?php echo $_POST["eposta"]; ?>

</body>
</html>
```

Çıktı şuna benzer:

``` title:'Çıktı'
Merhaba Berat
E-posta Adresiniz: beratmasat@birisi.com
```

### Güvenlik
---
Yukarıdaki kodlar oldukça basittir. Ancak, en önemli şey eksiktir. Kodunuzu kötü amaçlı kodlardan korumak için form verilerini doğrulamanız gerekir.

PHP formlarını işlerken **GÜVENLİĞİ** düşünün!

Bu sayfa herhangi bir form doğrulaması içermez, sadece form verilerini nasıl gönderebileceğinizi ve alabileceğinizi gösterir.

Ancak, sonraki sayfalarda PHP formlarının güvenlik göz önünde bulundurularak nasıl işleneceği gösterilecektir! Form verilerinin doğru şekilde doğrulanması, formunuzu bilgisayar korsanlarından ve spam gönderenlerden korumak için önemlidir!

### GET ve POST Farkı
---
Hem GET hem de POST bir dizi oluşturur (örneğin dizi( anahtar1 => değer1, anahtar2 => değer2, anahtar3 => değer3, ...)). Bu dizi, anahtarların form kontrollerinin adları ve değerlerin kullanıcıdan gelen giriş verileri olduğu anahtar/değer çiftlerini tutar.

Hem GET hem de POST, `$_GET` ve `$_POST` olarak değerlendirilir. Bunlar superglobaldir, yani kapsamdan bağımsız olarak her zaman erişilebilirler ve özel bir şey yapmanıza gerek kalmadan herhangi bir fonksiyondan, sınıftan veya dosyadan erişebilirsiniz.

`$_GET`, URL parametreleri aracılığıyla geçerli komut dosyasına aktarılan değişkenlerin bir dizisidir. Yani gönderilen veriler URL çubuğunda gözükür.

`$_POST`, HTTP POST yöntemi aracılığıyla geçerli komut dosyasına aktarılan bir değişkenler dizisidir.

#### Ne Zaman GET Kullanılmalı
---
GET yöntemiyle bir formdan gönderilen bilgiler herkes tarafından görülebilir (tüm değişken adları ve değerleri URL'de görüntülenir). GET'in gönderilecek bilgi miktarı konusunda da sınırları vardır. Sınırlama yaklaşık 2000 karakterdir. Ancak, değişkenler URL'de görüntülendiğinden, sayfayı yer imlerine eklemek mümkündür. Bu bazı durumlarda faydalı olabilir.

GET, hassas olmayan verilerin gönderilmesi için kullanılabilir.

>**Not:** GET ASLA parola veya diğer hassas bilgileri göndermek için kullanılmamalıdır!

#### Ne Zaman POST Kullanılmalı
---
POST yöntemiyle bir formdan gönderilen bilgiler başkaları tarafından görülmez (tüm adlar/değerler HTTP isteğinin gövdesine gömülür) ve gönderilecek bilgi miktarı konusunda herhangi bir sınırlama yoktur.

Ayrıca POST, dosyaları sunucuya yüklerken çok parçalı ikili giriş desteği gibi gelişmiş işlevleri destekler.

Ancak değişkenler URL'de gösterilmediği için sayfayı yer imlerine eklemek mümkün değildir.

Geliştiriciler form verilerini göndermek için POST'u tercih eder.

