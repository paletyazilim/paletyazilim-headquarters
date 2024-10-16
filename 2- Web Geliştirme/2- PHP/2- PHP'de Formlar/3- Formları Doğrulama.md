Bu sayfalar PHP formlarının güvenlik göz önünde bulundurularak nasıl işleneceğini gösterecektir. Form verilerinin doğru şekilde doğrulanması, formunuzu bilgisayar korsanlarından ve spam gönderenlerden korumak için önemlidir!

Bu bölümlerde üzerinde çalışacağımız HTML formu çeşitli girdi alanları içerir: gerekli ve isteğe bağlı metin alanları, radyo düğmeleri ve bir gönder düğmesi:

<body>  
<h2>PHP Form Doğrulama Örneği</h2>
<p><span>* zorunlu</span></p>
<form>  
  İsim: <input type="text" name="name" value="">
  <span>*</span>
  <br><br>
  E-posta: <input type="text" name="email" value="">
  <span>*</span>
  <br><br>
  Websayfa: <input type="text" name="website" value="">
  <span></span>
  <br><br>
  Yorum: <textarea name="comment" rows="5" cols="40"></textarea>
  <br><br>
  Cinsiyet:
  <input type="radio" name="gender" value="female">Kadın
  <input type="radio" name="gender" value="male">Erkek
  <input type="radio" name="gender" value="other">Diğer  
  <span>*</span>
  <br><br>
  <input type="submit" name="submit" value="Gönder">  
</form>
</body>

Yukarıdaki form için doğrulama kuralları aşağıdaki gibi olsun:

| Alan | Doğrulama Kuralı |
| ---- | ---- |
| İsim | Zorunlu + Yalnızca harf ve boşluk içermelidir |
| E-posta | Zorunlu + Geçerli bir E-posta içermelidir (@ ve .) |
| Websayfa | İsteğe bağlı. Mevcutsa, geçerli bir URL içermelidir |
| Yorum | İsteğe bağlı. Çok satırlı giriş alanı (textarea) |
| Cinsiyet | Zorunlu. Bir tane seçmelisiniz |
İlk olarak form için düz HTML koduna bakacağız:

### Giriş Alanları
---
Ad, e-posta ve web sitesi alanları metin giriş öğeleridir ve yorum alanı bir textarea'dır. HTML kodu aşağıdaki gibi görünür:

```HTML title:'Metin girdilerinin HTML kodu'
İsim: <input type="text" name="isim">
E-posta: <input type="text" name="eposta">
Websayfa: <input type="text" name="website">
Yorum: <textarea name="yorum" rows="5" cols="40"></textarea>
```

### Radio Düğmeleri
---
Cinsiyet alanları radio düğmeleridir ve HTML kodu şu şekildedir:

```HTML title:'Radio girdilerinin HTML kodu'
Cinsiyet:
<input type="radio" name="cinsiyet" value="kadin">Kadın
<input type="radio" name="cinsiyet" value="erkek">Erkek
<input type="radio" name="cinsiyet" value="diger">Diğer
```

### Form Öğesi
---
Formun HTML kodu aşağıdaki gibi görünür:

```HTML title:'Form etiketinin HTML kodu'
<form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>">
```

Form gönderildiğinde, form verileri `method="post"` ile gönderilir.

### PHP ile Form Verilerini Doğrulama
---
Yapacağımız ilk şey, tüm değişkenleri PHP'nin `htmlspecialchars()` fonksiyonundan geçirmektir.

`htmlspecialchars()` fonksiyonunu kullandığımızda; bir kullanıcı bir metin alanına aşağıdakileri göndermeye çalışırsa:

`<script>location.href('http://www.hacked.com')</script>`

bu çalıştırılmayacaktır, çünkü HTML kaçış kodu olarak kaydedilecektir, bunun gibi:

`&lt;script&gt;location.href('http://www.hacked.com')&lt;/script&gt;`

Kod artık bir sayfada veya bir e-postanın içinde görüntülenmek için güvenlidir.

Ayrıca kullanıcı formu gönderdiğinde iki şey daha yapacağız:

Kullanıcı girdi verilerinden gereksiz karakterleri (fazladan boşluk, sekme, satırsonu) ayıklayın (PHP `trim()` fonksiyonuyla)
Kullanıcı girdi verilerinden ters eğik çizgileri (`\`) kaldırın (PHP `stripslashes()` fonksiyonuyla)
Bir sonraki adım, bizim için tüm kontrolleri yapacak bir fonksiyon oluşturmaktır (bu, aynı kodu tekrar tekrar yazmaktan çok daha kullanışlıdır).

Fonksiyona `veri_kontrol()` adını vereceğiz.

Şimdi, her `$_POST` değişkenini `veri_kontrol()` fonksiyonuyla kontrol edebiliriz ve kod aşağıdaki gibi görünür:

```PHP title:'Yakalanan verileri doğrulama'
<?php
// Karışıklık olmaması için bu değişkenleri ingilizce tanımladık
$name = $email = $gender = $comment = $website = "";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
  $name = veri_kontrol($_POST["isim"]);
  $email = veri_kontrol($_POST["eposta"]);
  $website = veri_kontrol($_POST["website"]);
  $comment = veri_kontrol($_POST["yorum"]);
  $gender = veri_kontrol($_POST["cinsiyet"]);
}

function veri_kontrol($veri) {
  $veri = trim($veri);
  $veri = stripslashes($veri);
  $veri = htmlspecialchars($veri);
  return $veri;
}
?>
```

Kodun başında, formun `$_SERVER["REQUEST_METHOD"]` kullanılarak gönderilip gönderilmediğini kontrol ettiğimize dikkat edin. REQUEST_METHOD POST ise, form gönderilmiştir ve doğrulanması gerekir. Gönderilmemişse, doğrulamayı atlayın ve boş bir form görüntüleyin.

Ancak yukarıdaki örnekte, tüm giriş alanları isteğe bağlıdır. Kullanıcı herhangi bir veri girmese bile kod sorunsuz çalışır.

Bir sonraki adım, giriş alanlarını zorunlu hale getirmek ve gerekirse hata mesajları oluşturmaktır.

