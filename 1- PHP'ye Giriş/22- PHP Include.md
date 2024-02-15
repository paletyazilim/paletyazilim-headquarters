`include` (veya `require`) deyimi, belirtilen dosyada var olan tüm metni/kodu/işaretlemeyi alır ve `include` deyimini kullanan dosyaya kopyalar.

Dosyaları dahil etmek, bir web sitesinin birden fazla sayfasına aynı PHP, HTML veya metni dahil etmek istediğinizde çok kullanışlıdır.

### PHP `include` ve `require` İfadeleri
---
Bir PHP dosyasının içeriğini başka bir PHP dosyasına (sunucu çalıştırmadan önce) `include` veya `require` deyimleriyle eklemek mümkündür.

`include` ve `require` ifadeleri, verdikleri hatalar dışında aynıdır:

- `require` ölümcül bir hata (E_COMPILE_ERROR) üretecek ve betiği durduracaktır
- `include` yalnızca bir uyarı (E_WARNING) üretecek ve kod devam edecektir

Bu nedenle, `include` dosyası eksik olsa bile yürütmenin devam etmesini ve kullanıcılara çıktıyı göstermesini istiyorsanız, `include` deyimini kullanın. Aksi takdirde, FrameWork, CMS veya karmaşık bir PHP uygulaması kodlaması durumunda, yürütme akışına bir anahtar dosya eklemek için her zaman `require` deyimini kullanın. Bu, yanlışlıkla bir anahtar dosyanın eksik olması durumunda uygulamanızın güvenliğini ve bütünlüğünü tehlikeye atmaktan kaçınmanıza yardımcı olacaktır.

Dosyaları dahil etmek çok fazla iş tasarrufu sağlar. Bu, tüm web sayfalarınız için standart bir üstbilgi, altbilgi veya menü dosyası oluşturabileceğiniz anlamına gelir. Örneğin, üstbilginin güncellenmesi gerektiğinde, yalnızca üstbilgi dosyasını güncelleyebilirsiniz.
#### Syntax
---

```PHP title:'include ve require Syntax'
include 'dosya_adi';

require 'dosya_adi';
```

### Örnek Bir Uygulama
---

"footer.php" adında, aşağıdaki gibi görünen standart bir altbilgi dosyamız olduğunu varsayalım:


```PHP title:'footer.php'
<?php
echo "<p>Copyright &copy; 1999-" . date("Y") . " XYZ</p>";
?>
```

Altbilgi dosyasını bir sayfaya dahil etmek için `include` deyimini kullanın:

```PHP title:'index.php'
<html>
<body>

<h1>Anasayfamıza Hoşgeldiniz!</h1>
<p>Örnek yazılar...</p>
<?php include 'footer.php';?>

</body>
</html>
```
