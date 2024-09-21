### Çerez Nedir?
---
Çerez genellikle bir kullanıcıyı tanımlamak için kullanılır. Çerez, sunucunun kullanıcının bilgisayarına yerleştirdiği küçük bir dosyadır. Aynı bilgisayar bir tarayıcı ile bir sayfayı her istediğinde, çerezi de gönderecektir. PHP ile çerez değerlerini hem oluşturabilir hem de geri alabilirsiniz.

`setcookie()` fonksiyonu ile bir çerez oluşturulur.

### Syntax
---

```PHP title:'setcookie() syntax'
setcookie(isim, deger, gecerlilik, yol, domain, guvenlik, httponly);
```

Yalnızca `isim` parametresi gereklidir. Diğer tüm parametreler isteğe bağlıdır.

### Çerez Oluşturma/Geri Getirme
---
Aşağıdaki örnek, "Berat MASAT" değerine sahip "kullanici" adlı bir çerez oluşturur. Çerezin süresi 30 gün sonra dolacaktır (86400 * 30). "`/`", çerezin tüm web sitesinde mevcut olduğu anlamına gelir (aksi takdirde, tercih ettiğiniz dizini seçin).

Daha sonra "kullanici" çerezinin değerini alıyoruz (superglobal değişken `$_COOKIE`'yi kullanarak). Ayrıca çerezin ayarlanıp ayarlanmadığını öğrenmek için `isset()` fonksiyonunu kullanıyoruz:

```PHP title:'Çerez oluşturma ve çereze erişme'
<?php
$cerez_ismi = "kullanici";
$cerez_degeri = "Berat MASAT";
setcookie($cerez_ismi, $cerez_degeri, time() + (86400 * 30), "/"); // 86400 = 1 gün
?>

<html>
<body>

<?php
if(!isset($_COOKIE[$cerez_ismi])) {
  echo $cookie_name . "' isimli çerez oluşturulmamış!";
} else {
  echo $cookie_name . "' isimli çerez oluşturulmuş!<br>";
  echo "Kullanıcı İsmi: " . $_COOKIE[$cookie_name];
}
?>

</body>
</html>
```

**Not:** `setcookie()` fonksiyonu `<html>` etiketinden ÖNCE tanımlanmalıdır.

**Not:** Çerezin değeri, çerez gönderilirken otomatik olarak URL'ye kodlanır ve alındığında otomatik olarak kodu çözülür (URL kodlamasını önlemek için bunun yerine `setrawcookie()` fonksiyonunu kullanın).

### Çerez Değerini Güncelleme
---
Bir çerezi değiştirmek için `setcookie()` fonksiyonunu (tekrar) kullanarak çerezi güncellemeniz mümkün:

```PHP title:'Çerez değerini güncelleme'
<?php
$cerez_ismi = "kullanici";
$cerez_degeri = "Furkan MASAT";
setcookie($cerez_ismi, $cerez_degeri, time() + (86400 * 30), "/"); // 86400 = 1 gün
?>

<html>
<body>

<?php
if(!isset($_COOKIE[$cerez_ismi])) {
  echo $cookie_name . "' isimli çerez oluşturulmamış!";
} else {
  echo $cookie_name . "' isimli çerez oluşturulmuş!<br>";
  echo "Kullanıcı İsmi: " . $_COOKIE[$cookie_name];
}
?>

</body>
</html>
```

### Çerez Silme
---
Bir çerezi silmek için `setcookie()` fonksiyonunu geçmişte bir son kullanma tarihi ile kullanın:

```PHP title:'Çerezleri temizleme'
<?php
setcookie("kullanici", "", time() - 3600);
?>

<html>
<body>

<?php
echo "Çerez silindi.";
?>

</body>
</html>
```

### Çerezlerin Etkin Olup Olmadığını Kontrol Et
---
Aşağıdaki örnek, çerezlerin etkin olup olmadığını kontrol eden küçük bir kod oluşturur. Önce `setcookie()` fonksiyonuyla bir test çerezi oluşturmayı deneyin, ardından `$_COOKIE` dizi değişkenini sayın:

```PHP title:'Çerezleri kontrol etme'
<?php
setcookie("test_cerez", "test", time() + 3600, '/');
?>
<html>
<body>

<?php
if(count($_COOKIE) > 0) {
  echo "Çerezler etkin.";
} else {
  echo "Çerezler kapalı.";
}
?>

</body>
</html>
```