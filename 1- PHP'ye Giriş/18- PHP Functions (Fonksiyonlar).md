PHP'nin gerçek gücü işlevlerinden gelir.

PHP'nin 1000'den fazla yerleşik işlevi vardır ve bunlara ek olarak kendi özel işlevlerinizi de oluşturabilirsiniz.

Yerleşik PHP işlevlerinin yanı sıra, kendi işlevlerinizi oluşturmanız da mümkündür.

- Fonksiyon, bir programda tekrar tekrar kullanılabilen bir deyimler bloğudur.
- Bir fonksiyon, sayfa yüklendiğinde otomatik olarak çalışmaz.
- Bir fonksiyon, fonksiyona yapılan bir çağrı ile çalıştırılır.

### Fonksiyon Oluşturma
---
Kullanıcı tanımlı bir fonksiyon bildirimi `function` anahtar sözcüğü ile başlar ve ardından fonksiyonun adı gelir:

```PHP title:'Fonksiyon oluşturma'
function mesajYazma() {
  echo "Merhaba Dünya!";
}
```

**Not:** Bir fonksiyon adı bir harf veya alt çizgi ile başlamalıdır. Fonksiyon adları büyük/küçük harfe duyarlı DEĞİLDİR.

>İpucu: Fonksiyona, fonksiyonun ne yaptığını yansıtan bir isim verin!

### Fonksiyon Çağırma
---
Fonksiyonunuzu çağırmak için adını ve ardından parantez `()` işaretini yazmanız yeterlidir:

```PHP title:'Fonksiyon çağırma'
function mesajYazma() {
  echo "Merhaba Dünya!";
}

mesajYazma();
```

Örneğimizde `mesajYazma()` adında bir fonksiyon oluşturuyoruz.

Açılış küme parantezi `{` fonksiyon kodunun başlangıcını, kapanış küme parantezi `}` ise fonksiyonun sonunu belirtir.

İşlev "Merhaba dünya!" çıktısını verir.

### Fonksiyonlarda Argümanlar
---
Fonksiyonlara argümanlar aracılığıyla bilgi aktarılabilir. Bir argüman tıpkı bir değişken gibidir.

Bağımsız değişkenler fonksiyon adından sonra, parantez içinde belirtilir. İstediğiniz kadar argüman ekleyebilirsiniz, bunları virgülle ayırmanız yeterlidir.

Aşağıdaki örnekte bir argümanı (`$fname`) olan bir fonksiyon bulunmaktadır. `aileIsmi()`fonksiyonu çağrıldığında, bir isim de iletiriz, örneğin `("Berat")` ve bu isim fonksiyonun içinde kullanılır, bu da birkaç farklı ilk isim, ancak eşit bir soyadı çıktısı verir:

```PHP title:'Fonksiyonlarda argüman'
function aileIsmi($isim) {
  echo "$isim Masat.<br>";
}

familyName("Berat");
familyName("Furkan");
familyName("Osman");
```

Aşağıdaki örnekte iki bağımsız değişkenli (`$isim`, `$yil`) bir fonksiyon bulunmaktadır:

```PHP title:'Fonksiyonlarda argümanlar'
function aileIsmi($isim,$yil) {
  echo "$isim Masat<br>Doğum Tarihi: $yil";
}

familyName("Berat",2003);
familyName("Furkan",1999);
familyName("Osman",1995);
```

#### Argümana Varsayılan Atama
---
Aşağıdaki örnekte varsayılan bir parametrenin nasıl kullanılacağı gösterilmektedir. `boyAyarla()` fonksiyonunu argüman olmadan çağırırsak, argüman varsayılan değeri alır:

```PHP title:'Argümana varsayılan değer atama'
function boyAyarla($boy = 1.70) {
  echo "Boyunuz: $boy <br>";
}

setHeight(1.76);
setHeight(); // Varsayılan Değer: 1.70
```

### Değer Döndürme
---
Değer döndürme, bir fonksiyonun çalıştırılmasının ardından bir değer veya veri kümesini ana koda geri gönderme işlemidir. Bu sayede fonksiyonlar, karmaşık işlemleri gerçekleştirerek sonuçlarını ana koda aktarabilir ve programın farklı bölgelerinde kullanılabilir hale getirebilir.

Bir fonksiyonun bir değer döndürmesini sağlamak için `return` deyimini kullanın:

```PHP title:'Değer Döndürme'
function topla($x, $y) {
  $z = $x + $y;
  return $z;
}

$dondurulenDeger = topla(15,25); // Sonuç $dondurulenDeger değişkenine atandı

echo '15 + 25 = ' . $dondurulenDeger;
```

### Referans Verme
---
PHP'de argümanlar genellikle değer olarak aktarılır, bu da değerin bir kopyasının fonksiyonda kullanıldığı ve fonksiyona aktarılan değişkenin **değiştirilemeyeceği** anlamına gelir.

Bir fonksiyon argümanı referans olarak tanımlandığın da, argümanda yapılan değişiklikler aktarılan değişkeni de değiştirir. Bir fonksiyon argümanını referansa dönüştürmek için `&` operatörü kullanılır:

```PHP title:'Referans ile değişken değişme'
function bes_ekle(&$deger) {
  $deger += 5;
}

$sayi = 2;
bes_ekle($sayi);
echo $sayi;
```

### Değişken Fonksiyonlar
---
Fonksiyon parametresinin önünde `...` operatörü kullanılarak, fonksiyon bilinmeyen sayıda argüman kabul eder. Buna değişken fonksiyon da denir.

Değişken Fonksiyon argümanı bir dizi haline gelir:

```PHP title:'Değişken Fonksiyon'
function sayilariTopla(...$sayilar) {
  $toplanan = 0;
  $uzunluk = count($sayilar);
  for($i = 0; $i < $uzunluk; $i++) {
    $toplanan += $sayilar[$i];
  }
  return $toplanan;
}

$hesapla = sayilariTopla(5, 2, 6, 2, 7, 7);
echo $hesapla;
```

Değişken uzunlukta yalnızca bir argümanınız olabilir ve bu **SON** argüman olmalıdır.

```PHP title:'Değişken Fonksiyon 2'
function aileAgaci($soyisim, ...$isim) {
  $yazi = "";
  $uzunluk = count($isim);
  for($i = 0; $i < $uzunluk; $i++) {
    $yazi = $yazi . ($i + 1) . " Kişi:, $isim[$i] $soyisim.<br>";
  }
  return $yazi;
}

$kisiler = aileAgaci("Masat", "Osman", "Furkan", "Berat");
echo $kisiler;
```

Değişken argüman son argüman değilse **hata** alırsınız.

```PHP title:'Değişken Fonksiyon 3' error:1
function aileAgaci(...$isim, $soyisim) {
  $yazi = "";
  $uzunluk = count($isim);
  for($i = 0; $i < $uzunluk; $i++) {
    $yazi = $yazi . ($i + 1) . " Kişi:, $isim[$i] $soyisim.<br>";
  }
  return $yazi;
}

$kisiler = aileAgaci("Masat", "Osman", "Furkan", "Berat");
echo $kisiler;
```

### `strict` (Katı Beyan)
---
PHP gevşek tipli bir dildir, PHP, değişkene değerine bağlı olarak otomatik olarak bir veri türü atar. Veri tipleri kesin bir şekilde belirlenmediğinden, bir tamsayıya string eklemek gibi şeyleri hataya neden olmadan yapabilirsiniz.

PHP 7'de tip bildirimleri eklenmiştir. Bu bize bir fonksiyon bildirirken beklenen veri türünü belirtme seçeneği sunar ve `strict` (katı) bildirim ekleyerek, veri türü uyuşmazsa bir **"Fatal Error"** atar.

Aşağıdaki örnekte, `strict` kullanmadan fonksiyona hem bir sayı hem de bir dize göndermeyi deniyoruz:

```PHP title:'Katı Beyan'
function topla(int $a, int $b) {
  return $a + $b;
}
echo topla(5, "5 gün");
// strict etkinleştirilmediği için "5 gün" int(5) olarak değiştirilir ve 10 döndürür.
```

`strict` özelliğini etkinleştirmek için `declare(strict_types=1);` ayarını yapmamız gerekir. Bu PHP dosyasının ilk satırında olmalıdır.

Aşağıdaki örnekte, fonksiyona hem bir sayı hem de bir dize göndermeye çalışıyoruz, ancak burada `strict` bildirimini de ekledik:

```PHP
<?php declare(strict_types=1); // Katı Beyan Etkinleştirildi

function topla(int $a, int $b) {
  return $a + $b;
}
echo topla(5, "5 gün");
// strict etkin olduğundan ve "5 gün" bir tamsayı olmadığından, bir hata atılacaktır.
?>
```

>Katı beyan, her şeyin amaçlanan şekilde kullanılmasını zorunlu kılar.

### Dönüş Türü Bildirimi
---
PHP 7 ayrıca `return` deyimi için tür bildirimlerini de destekler. İşlev argümanları için tür bildiriminde olduğu gibi, katı beyanı etkinleştirerek, tür uyuşmazlığında bir **"Fatal Error"** atacaktır.

Fonksiyon dönüşü için bir tür bildirmek için, fonksiyonu bildirirken iki nokta üst üste ( `:` ) ve açılış küme parantezinden hemen önce türü ekleyin.

```PHP title:'Dönüş Türü Bildirme' hl:1,3
<?php declare(strict_types=1); // strict requirement
function topla(float $a, float $b) : int {
  return (int)($a + $b);
}
echo topla(1.2, 5.2);
?>
```

Argüman türlerinden farklı bir dönüş türü belirtebilirsiniz, ancak dönüşün doğru türde olduğundan emin olun aksi takdirde **"Fatal Error"** atacaktır.

```PHP title:'Dönüş Türü Bildirme 2' error:3
<?php declare(strict_types=1); // strict requirement
function topla(float $a, float $b) : int {
  return $a + $b; // Gelen argümanlar float olduğu için hata verecektir.
}
echo topla(1.2, 5.2);
?>
```

