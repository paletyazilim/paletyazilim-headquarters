Session (Oturum), birden fazla sayfada kullanılmak üzere bilgileri (değişkenler halinde) saklamanın bir yoludur.

Çerezden farklı olarak, bilgiler kullanıcıların bilgisayarında saklanmaz.

Bir uygulama ile çalışırken, onu açarsınız, bazı değişiklikler yaparsınız ve sonra kapatırsınız. Bu bir Oturum'a çok benzer. Bilgisayar sizin kim olduğunuzu bilir. Uygulamayı ne zaman başlattığınızı ve ne zaman bitirdiğinizi bilir. Ancak internette bir sorun vardır: web sunucusu kim olduğunuzu ya da ne yaptığınızı bilmez, çünkü HTTP adresi durumu korumaz.

Session değişkenleri, birden fazla sayfada kullanılmak üzere kullanıcı bilgilerini depolayarak bu sorunu çözer (örn. kullanıcı adı, favori renk, vb.). Varsayılan olarak, session değişkenleri kullanıcı tarayıcıyı kapatana kadar sürer.

Yani; Session değişkenleri tek bir kullanıcı hakkında bilgi tutar ve bir uygulamadaki tüm sayfalar tarafından kullanılabilir.

>İpucu: Kalıcı bir depolama alanına ihtiyacınız varsa, verileri bir veritabanında saklamak isteyebilirsiniz.

### Session (Oturum) Başlatma
---
Bir oturum `session_start()` fonksiyonu ile başlatılır.

Session değişkenleri PHP global değişkeni ile ayarlanır: `$_SESSION`.

Şimdi, "demo_session1.php" adında yeni bir sayfa oluşturalım. Bu sayfada yeni bir PHP oturumu başlatıyoruz ve bazı oturum değişkenlerini ayarlıyoruz:

```PHP title:'Oturum başlatma ve değer atama'
<?php
session_start();
?>

<!DOCTYPE html>
<html>
<body>

<?php
$_SESSION["favori_renk"] = "Yeşil";
$_SESSION["favori_hayvan"] = "Kedi";
echo "Oturum bilgilerini kaydedilmiştir.";
?>

</body>
</html>
```

**Not:** `session_start()` fonksiyonu `<html>` etiketinden ÖNCE tanımlanmalıdır.

### Session (Oturum) Değişkenlerini Alma
---
Ardından, "demo_session2.php" adında başka bir sayfa oluşturuyoruz. Bu sayfadan, ilk sayfada ("demo_session1.php") ayarladığımız oturum bilgilerine erişeceğiz.

Session değişkenlerinin her yeni sayfaya ayrı ayrı aktarılmadığına, bunun yerine her sayfanın başında açtığımız oturumdan alındığına dikkat edin (`session_start()`).

Ayrıca tüm oturum değişkeni değerlerinin superglobal `$_SESSION` değişkeninde saklandığına dikkat edin:

```PHP title:'Oturum bilgilerine erişme'
<?php
session_start();
?>

<!DOCTYPE html>
<html>
<body>

<?php
echo "Favori Renginiz: " . $_SESSION["favori_renk"] . ".<br>";
echo "Favori Hayvanınız: " . $_SESSION["favori_hayvan"] . ".";
?>

</body>
</html>
```

Bir kullanıcı oturumu için tüm oturum değişkeni değerlerini göstermenin bir başka yolu da aşağıdaki kodu çalıştırmaktır:

```PHP title:'Oturum değişkenlerine erişmenin alternatif yolu'
<?php
session_start();
?>

<!DOCTYPE html>
<html>
<body>

<?php
print_r($_SESSION);
?>

</body>
</html>
```

>Nasıl çalışıyor? Ben olduğumu nasıl anlıyor?
>
>Çoğu oturum, kullanıcının bilgisayarında şuna benzer bir kullanıcı anahtarı ayarlar: 765487cf34ert8dede5a562e4f3a7e12. Daha sonra, bir oturum başka bir sayfada açıldığında, kullanıcı anahtarı için bilgisayarı tarar. Eğer bir eşleşme varsa, o oturuma erişir, yoksa yeni bir oturum başlatır.

### Session (Oturum) Bilgilerini Değiştirme
---
Bir oturum değişkenini değiştirmek için üzerine yazmanız yeterlidir:

```PHP title:'Oturum bilgilerini güncelleme'
<?php
session_start();
?>

<!DOCTYPE html>
<html>
<body>

<?php
$_SESSION["favori_renk"] = "Sarı";
print_r($_SESSION);
?>

</body>
</html>
```

### Session (Oturum) Bilgilerini Silme
---
Tüm global oturum değişkenlerini kaldırmak ve oturumu yok etmek için `session_unset()` ve `session_destroy()` fonksiyonlarını kullanın:

```PHP title:'Oturum silme'
<?php
session_start();
?>

<!DOCTYPE html>
<html>
<body>

<?php
// Session değişkenlerini temizler
session_unset();

// Oturumu sonlandırır
session_destroy();
?>

</body>
</html>
```

