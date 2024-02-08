`$_SERVER["PHP_SELF"]` değişkeni bilgisayar korsanları tarafından kullanılabilir!

Sayfanızda PHP_SELF kullanılıyorsa, kullanıcı bir eğik çizgi (`/`) girebilir ve ardından bazı Çapraz Site Komut Dosyası (XSS) komutları çalıştırabilir. Siteler arası komut dosyası oluşturma (XSS), genellikle Web uygulamalarında bulunan bir bilgisayar güvenliği açığı türüdür. XSS, saldırganların diğer kullanıcılar tarafından görüntülenen Web sayfalarına istemci tarafı komut dosyası enjekte etmesini sağlar.

"test_form.php" adlı bir sayfada aşağıdaki forma sahip olduğumuzu varsayalım:

```HTML
<form method="post" action="<?php echo $_SERVER["PHP_SELF"];?>">
```

Şimdi, bir kullanıcı adres çubuğuna "http://www.ornek.com/test_form.php" gibi normal bir URL girerse, yukarıdaki kod şu şekilde çevrilecektir:

```HTML
<form method="post" action="test_form.php">
```

Şimdiye kadar çok iyi.

Ancak, bir kullanıcının adres çubuğuna aşağıdaki URL'yi girdiğini düşünün:

```
http://www.ornek.com/test_form.php/%22%3E%3Cscript%3Ealert('Hacklendiniz')%3C/script%3E
```

Bu durumda, yukarıdaki kod şu şekilde çevrilecektir:

```HTML
<form method="post" action="test_form.php/"><script>alert('Hacklendiniz')</script>
```

Bu kod bir script etiketi ve bir alert komutu ekler. Ve sayfa yüklendiğinde JavaScript kodu çalıştırılacaktır (kullanıcı bir uyarı kutusu görecektir). Bu sadece PHP_SELF değişkeninin nasıl kullanılabileceğine dair basit ve zararsız bir örnektir.

`<script>` etiketinin içine herhangi bir JavaScript kodu eklenebileceğini unutmayın! Bir bilgisayar korsanı kullanıcıyı başka bir sunucudaki bir dosyaya yönlendirebilir ve bu dosya, örneğin global değişkenleri değiştirebilecek veya kullanıcı verilerini kaydetmek için formu başka bir adrese gönderebilecek kötü amaçlı kod içerebilir.

### `$_SERVER["PHP_SELF"]` Açıklarından Nasıl Kaçınılır?
---
`$_SERVER["PHP_SELF"]` açıkları `htmlspecialchars()` fonksiyonu kullanılarak önlenebilir.

Form kodu aşağıdaki gibi görünmelidir:

```HTML
<form method="post" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>">
```

`htmlspecialchars()` fonksiyonu özel karakterleri HTML varlıklarına dönüştürür. Şimdi kullanıcı PHP_SELF değişkenini kullanmaya çalışırsa, aşağıdaki çıktı ile sonuçlanacaktır:

```HTML
<form method="post" action="test_form.php/&quot;&gt;&lt;script&gt;alert('Hacklendiniz')&lt;/script&gt;">
```

İstismar girişimi başarısız olur ve hiçbir zarar gelmez!

### `$_SERVER["PHP_SELF"]` Nedir?
---
`$_SERVER["PHP_SELF"]` o anda çalışmakta olan betiğin dosya adını döndüren bir süper global değişkendir.

Böylece, `$_SERVER["PHP_SELF"]` gönderilen form verilerini farklı bir sayfaya atlamak yerine sayfanın kendisine gönderir. Bu şekilde, kullanıcı hata mesajlarını formla aynı sayfada alacaktır.

### `htmlspecialchars()` Fonksiyonu Nedir?
---
`htmlspecialchars()` fonksiyonu özel karakterleri HTML varlıklarına dönüştürür. Bu, `<` ve `>` gibi HTML karakterlerini `&lt;` ve `&gt;` ile değiştireceği anlamına gelir. Bu, saldırganların formlara HTML veya Javascript kodu enjekte ederek (Siteler arası komut dosyası saldırıları) kodu istismar etmesini önler.
