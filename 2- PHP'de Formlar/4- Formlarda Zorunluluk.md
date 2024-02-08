Önceki sayfadaki doğrulama kuralları tablosundan "Ad", "E-posta" ve "Cinsiyet" alanlarının gerekli olduğunu belirtmiştik. Bu alanlar boş bırakılamaz ve HTML formunda doldurulmalıdır.

| Alan | Doğrulama Kuralı |
| ---- | ---- |
| İsim | Zorunlu + Yalnızca harf ve boşluk içermelidir |
| E-posta | Zorunlu + Geçerli bir E-posta içermelidir (@ ve .) |
| Websayfa | İsteğe bağlı. Mevcutsa, geçerli bir URL içermelidir |
| Yorum | İsteğe bağlı. Çok satırlı giriş alanı (textarea) |
| Cinsiyet | Zorunlu. Bir tane seçmelisiniz |

Önceki bölümde, tüm giriş alanları isteğe bağlıydı.

Aşağıdaki kodda bazı yeni değişkenler ekledik: `$nameErr`, `$emailErr`, `$genderErr` ve `$websiteErr`. Bu hata değişkenleri, gerekli alanlar için hata mesajlarını tutacaktır. Ayrıca her `$_POST` değişkeni için bir if else deyimi ekledik. Bu, `$_POST` değişkeninin boş olup olmadığını kontrol eder (PHP `empty()` fonksiyonu ile). Boşsa, farklı hata değişkenlerinde bir hata mesajı saklanır ve boş değilse, kullanıcı girdi verilerini `veri_kontrol()` fonksiyonu aracılığıyla gönderir:

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
  }

  if (empty($_POST["eposta"])) {
    $emailErr = "Bu alan zorunludur";
  } else {
    $email = test_input($_POST["eposta"]);
  }

  if (empty($_POST["website"])) {
    $website = "";
  } else {
    $website = test_input($_POST["website"]);
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

### Hata Mesajının Gösterilmesi
---
Daha sonra HTML formunda, her gerekli alandan sonra, gerektiğinde doğru hata mesajını üreten küçük bir komut dosyası ekliyoruz (yani kullanıcı gerekli alanları doldurmadan formu göndermeye çalışırsa):

```PHP title:'Formumuzun son hali'
<form method="POST" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>">

İsim: <input type="text" name="isim">
<span>* <?php echo $nameErr;?></span>
<br><br>
E-posta:
<input type="text" name="email">
<span>* <?php echo $emailErr;?></span>
<br><br>
Websayfa:
<input type="text" name="website">
<span><?php echo $websiteErr;?></span>
<br><br>
Yorum: <textarea name="yorum" rows="5" cols="40"></textarea>
<br><br>
Cinsiyet:
<input type="radio" name="cinsiyet" value="kadin">Female
<input type="radio" name="cinsiyet" value="erkek">Male
<input type="radio" name="cinsiyet" value="diger">Other
<span class="error">* <?php echo $genderErr;?></span>
<br><br>
<input type="submit" name="submit" value="Gönder">

</form>
```