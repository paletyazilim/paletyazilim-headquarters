**Syntax (Sözdizimi)**, bir dilde kullanılan kelime ve sembollerin doğru bir şekilde düzenlenmesi ve kombinasyonunu ifade eder. Özellikle programlama dillerinde ve markup dillerinde doğru syntax kullanımı önemlidir. Doğru syntax, kodun işlevselliği, okunabilirliği ve web sayfalarının doğru görüntülenmesi için gereklidir.

Programlama dillerinde **syntax**, bir programın nasıl yazılacağı ve çalıştırılacağıyla ilgili kuralları belirler. Örneğin, bir programlama dilinde değişken tanımlamak için `var` kelimesini ve değişkenin adını kullanmamız gerekiyorsa, bu kural syntax'ın bir parçasıdır.

### PHP'de Basit Bir Syntax
---
Bir PHP betiği belge içinde herhangi bir yere yerleştirilebilir.

Bir PHP betiği `<?php` ile başlar ve `?>` ile biter:

```PHP title:"Basit bir PHP sözdizimi"
<?php
// PHP kodları buraya gelecek.
?>
```

PHP dosyaları için varsayılan dosya uzantısı "`.php`" dir.

Bir PHP dosyası normalde HTML etiketleri ve bazı PHP betik kodları içerir.

Aşağıda, bir web sayfasında "Merhaba Dünya!" metninin çıktısını almak için yerleşik bir PHP fonksiyonu olan "`echo`"yu kullanan bir PHP betiği içeren basit bir PHP dosyası örneği bulunmaktadır:

```PHP title:"Hem HTML kodu hem de PHP kodu içeren basit bir '.php' dosyası:" hl:8 
<!DOCTYPE html>
<html>
<body>

<h1>Merhaba İlk PHP Sayfam!</h1>

<?php
echo "Merhaba Dünya!";
?>

</body>
</html>
```

**Not:** PHP deyimleri noktalı virgül (`;`) ile biter.

### PHP'de Büyük/Küçük Harf Duyarlılığı
---
PHP'de **keywords** (anahtar sözcükler) (örn. `if`, `else`, `while`, `echo`, vb.), sınıflar, fonksiyonlar ve kullanıcı tanımlı fonksiyonlar büyük/küçük harf duyarlı değildir.

> **Keywords (Anahatar Sözcükler):** derleyici veya yorumlayıcı tarafından özel bir anlama sahip olarak yorumlanan *önceden tanımlanmış* kelimelerdir. Anahtar sözcükler, programın yapısını ve işleyişini belirlemek için kullanılır.
> 
>Anahtar sözcüklerin temel özellikleri şunlardır:
>
>**Özel anlamlar taşırlar:** Anahtar sözcükler, derleyici veya yorumlayıcı tarafından özel bir anlama sahip olarak yorumlanır. Örneğin, if anahtar sözcüğü bir koşullu ifadeyi temsil eder.
>**Tanımlayıcı olarak kullanılamazlar:** Anahtar sözcükler, değişken, fonksiyon, sınıf gibi tanımlayıcılar olarak kullanılamaz.
>**Küçük harfle yazılır:** Anahtar sözcükler, genellikle **küçük harfle** yazılır.

Aşağıdaki örnekte, aşağıdaki üç echo ifadesinin tümü eşit ve *yasaldır*:

```PHP title:"ECHO, echo ile aynıdır:" hl:6-8 
<!DOCTYPE html>
<html>
<body>

<?php
ECHO "Merhaba Dünya!<br>";
echo "Merhaba Dünya!<br>";
EcHo "Merhaba Dünya!<br>";
?>

</body>
</html>
```

>Yukarıda ki, "*yasaldır*" sözcüğü, sözdizimsel olarak geçerli (**legal**) anlamında kullanılmıştır. Bu cümlede verilen üç `echo` ifadesi de, sözdizimsel olarak geçerlidir. Yani, bu ifadeler, herhangi bir derleyici veya yorumlayıcı tarafından kabul edilir ve yürütülür.

Ancak; tüm değişkenler büyük/küçük harfe **duyarlıdır**!

Aşağıdaki örneğe bakın; sadece ilk ifade `$renk` değişkeninin değerini gösterecektir! Bunun nedeni `$renk`, `$RENK` ve `$reNK` değişkenlerinin üç **farklı** değişken olarak ele alınmasıdır:

```PHP title:"$RENK, $renk ile aynı değildir:" hl:7-9
<!DOCTYPE html>
<html>
<body>

<?php
$renk = "kırmızı";
echo "Benim arabam " . $renk . "<br>";
echo "Benim evim " . $RENK . "<br>";
echo "Teknem " . $reNK . "<br>";
?>

</body>
</html>
```

