PHP süzgeçleri harici girdiyi doğrulamak ve sterilize etmek için kullanılır.

PHP filtre eklentisi, kullanıcı girdisini kontrol etmek için gereken fonksiyonların çoğuna sahiptir ve veri doğrulamayı daha kolay ve daha hızlı hale getirmek için tasarlanmıştır.

PHP süzgeç eklentisinin sunduklarını listelemek için `filter_list()` fonksiyonu kullanılabilir:

```PHP title:'Bütün filtre seçenekleri'
<table>
  <tr>
    <td>Filter Name</td>
    <td>Filter ID</td>
  </tr>
  <?php
  foreach (filter_list() as $id =>$filter) {
    echo '<tr><td>' . $filter . '</td><td>' . filter_id($filter) . '</td></tr>';
  }
  ?>
</table>
```

### Neden Filtreleri Kullanırız?
---
Birçok web uygulaması harici girdi alır. Harici girdi/veri şunlar olabilir:

- Bir formdan kullanıcı girişi
- Çerezler
- Web hizmetleri verileri
- Sunucu değişkenleri
- Veritabanı sorgu sonuçları

>**Harici verileri her zaman doğrulamalısınız!**
>Geçersiz gönderilen veriler güvenlik sorunlarına yol açabilir ve web sayfanızı bozabilir!
>PHP filtrelerini kullanarak uygulamanızın doğru girdiyi aldığından emin olabilirsiniz!

### `filter_var` - Fonksiyonu
---
`filter_var()` fonksiyonu verileri hem doğrular hem de sterilize eder.

`filter_var()` fonksiyonu tek bir değişkeni belirtilen bir filtre ile filtreler. İki veri parçası alır:

- Kontrol etmek istediğiniz değişken
- Kullanılacak filtreleme türü

### Dizeyi Temizleme
---
Aşağıdaki örnek, bir dizeden tüm HTML etiketlerini kaldırmak için `filter_var()` fonksiyonunu kullanır:

```PHP title:'Dizeleri filtreleme'
<?php
$metin = "<h1>Merhaba Dünya!</h1>";
$filtreli_metin = filter_var($metin, FILTER_SANITIZE_STRING);
echo $filtreli_metin;
?>
```

### Tamsayı Doğrulama
---
Aşağıdaki örnek, `$sayi` değişkeninin bir tamsayı olup olmadığını kontrol etmek için `filter_var()` fonksiyonunu kullanır. Eğer `$sayi` bir tamsayı ise, aşağıdaki kodun çıktısı şöyle olacaktır: "Veri bir tamsayıdır". Eğer `$sayi` bir tamsayı değilse, çıktı şöyle olacaktır: "Veri tamsayı değildir":

```PHP title:'Tamsayı filtreleme'
<?php
$sayi = 100;

if (!filter_var($sayi FILTER_VALIDATE_INT) === false) {
  echo("Veri bir tamsayıdır");
} else {
  echo("Veri tamsayı değildir");
}
?>
```

#### `filter_var()` ve 0 Sayısı ile İlgili Sorun
---
Yukarıdaki örnekte, $sayi 0 olarak ayarlanmışsa, yukarıdaki fonksiyon "Veri tamsayı değildir" sonucunu döndürecektir. Bu sorunu çözmek için aşağıdaki kodu kullanın:

```PHP title:'0 Problemi'
<?php
$sayi = 100;

if (!filter_var($sayi FILTER_VALIDATE_INT) === false || filter_var($sayi, FILTER_VALIDATE_INT) === 0) {
  echo("Veri bir tamsayıdır");
} else {
  echo("Veri tamsayı değildir");
}
?>
```

### IP Adresi Doğrulama
---
Aşağıdaki örnek, `$ip` değişkeninin geçerli bir IP adresi olup olmadığını kontrol etmek için `filter_var()` fonksiyonunu kullanır:

```PHP title:'IP Adresi Filtreleme'
<?php
$ip = "127.0.0.1";

if (!filter_var($ip, FILTER_VALIDATE_IP) === false) {
  echo("$ip geçerli bir IP adresidir");
} else {
  echo("$ip IP adresi geçerli değildir");
}
?>
```

### E-posta Adresini Temizleme ve Doğrulama
---
Aşağıdaki örnekte `filter_var()` fonksiyonu kullanılarak önce `$eposta` değişkenindeki tüm özel karakterler kaldırılmakta, ardından da geçerli bir e-posta adresi olup olmadığı kontrol edilmektedir:

```PHP title:'E-posta Adresi Filtreleme'
<?php
$eposta = "berat_masat@ornek.com";

// Bütün özel karakterler ve boşluklar temizlenir
$email = filter_var($eposta, FILTER_SANITIZE_EMAIL);

// E-posta adresinin geçerliliği kontrol edilir
if (!filter_var($eposta, FILTER_VALIDATE_EMAIL) === false) {
  echo("$eposta geçerli bir e-posta adresidir.");
} else {
  echo("$eposta geçerli olmayan bir e-posta adresidir.");
}
?>
```

### URL'yi Temizleme ve Doğrulama
---
Aşağıdaki örnek, önce bir URL'den tüm özel karakterleri kaldırmak için `filter_var()` fonksiyonunu kullanır, ardından `$url` öğesinin geçerli bir URL olup olmadığını kontrol eder:

```PHP title:'URL Filtreleme'
<?php
$url = "https://www.php.net";

// Bütün özel karakterler ve boşluklar temizlenir
$url = filter_var($url, FILTER_SANITIZE_URL);

// URL'nin geçerliliği kontrol edilir
if (!filter_var($url, FILTER_VALIDATE_URL) === false) {
  echo("$url geçerli bir bağlantıdır.");
} else {
  echo("$url geçerli olmayan bir bağlantıdır.");
}
?>
```

## Gelişmiş Filtreleme
---
Çevrilecek...

