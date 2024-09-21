### İsimleri Doğrulama
---
Aşağıdaki kod, ad alanının yalnızca harf, tire, kesme işareti ve boşluk içerip içermediğini kontrol etmenin basit bir yolunu göstermektedir. Ad alanının değeri geçerli değilse, bir hata mesajı gönderin:

```PHP title='Basit bir isim doğrulama'
$name = veri_kontrol($_POST["isim"]);
if (!preg_match("/^[a-zA-Z-' ]*$/",$name)) {
  $nameErr = "Yalnızca karakter ve boşluk";
}
```

`preg_match()` fonksiyonu bir dizede desen arar, desen varsa `true`, yoksa `false` döndürür.

### E-posta Doğrulama
---
Bir e-posta adresinin iyi biçimlendirilmiş olup olmadığını kontrol etmenin en kolay ve en güvenli yolu PHP'nin `filter_var()` fonksiyonunu kullanmaktır.

Aşağıdaki kodda, e-posta adresi iyi biçimlendirilmemişse, bir hata mesajı gönderin:

```PHP title:'E-posta doğrulama için en iyi yol'
$email = veri_kontrol($_POST["eposta"]);
if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
  $emailErr = "Yanlış e-posta formatı";
}
```


### URL Doğrulama
---
Aşağıdaki kod, bir URL adresi sözdiziminin geçerli olup olmadığını kontrol etmenin bir yolunu gösterir (bu RegEx URL'de tire işaretlerine de izin verir). URL adresi sözdizimi geçerli değilse, bir hata mesajı depolar:

```PHP title:'URL adresi doğrulama'
$website = veri_kontrol($_POST["website"]);
if (!preg_match("/\b(?:(?:https?|ftp):\/\/|www\.)[-a-z0-9+&@#\/%?=~_|!:,.;]*[-a-z0-9+&@#\/%=~_|]/i",$website)) {
  $websiteErr = "Geçersiz URL";
}
```

Şimdi kodumuz bu şekilde gözükmeli:

```PHP title:'Yakalanan verilerde ufak bir değişiklik'
<?php
// Karışıklık olmaması için bu değişkenleri ingilizce tanımladık.
$nameErr = $emailErr = $genderErr = $websiteErr = "";
$name = $email = $gender = $comment = $website = "";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
  if (empty($_POST["isim"])) {
    $nameErr = "Bu alan zorunludur";
  } else {
    $name = test_input($_POST["isim"]);

    if (!preg_match("/^[a-zA-Z-' ]*$/",$name)) {
      $nameErr = "Yalnızca karakter ve boşluk";
    }
  }

  if (empty($_POST["eposta"])) {
    $emailErr = "Bu alan zorunludur";
  } else {
    $email = test_input($_POST["eposta"]);

    if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
      $emailErr = "Yanlış e-posta formatı";
    }
  }

  if (empty($_POST["website"])) {
    $website = "";
  } else {
    $website = test_input($_POST["website"]);

    if (!preg_match("/\b(?:(?:https?|ftp):\/\/|www\.)[-a-z0-9+&@#\/%?=~_|!:,.;]*[-a-z0-9+&@#\/%=~_|]/i",$website)) {
      $websiteErr = "Geçersiz URL";
    }
  }

  if (empty($_POST["yorum"])) {
    $comment = "";
  } else {
    $comment = test_input($_POST["yorum"]);
  }

  if (empty($_POST["cinsiyet"])) {
    $genderErr = "Bu alan zorunludur";
  } else {
    $gender = test_input($_POST["cinsiyet"]);
  }
}
?>
```