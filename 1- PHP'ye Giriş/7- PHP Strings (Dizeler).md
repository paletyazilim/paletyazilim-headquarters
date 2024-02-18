## Dizelerin Temel Özellikleri
---
Dizi, harflerin sıralanmış halidir, tıpkı "Merhaba dünya!" gibi.

```PHP
echo "Merhaba";
echo 'Merhaba';
```

**Not:** PHP'de çift tırnak ile tek tırnak arasında büyük bir fark vardır. Çift tırnak özel karakterleri işler, tek tırnak işlemez.

### Çift Tırnak ile Tek Tırnak Farkı
---
Çift veya tek tırnak kullanabilirsiniz, ancak ikisi arasındaki farkları bilmelisiniz. 

Çift tırnaklı dizeler özel karakterler üzerinde işlem yapabilir. Örneğin, dizenizin içinde bir değişken olduğunda, değişkenin değerini döndürür:

```PHP
$x = "Berat";
echo "Merhaba $x"; // Çıktı: Merhaba Berat
```

Ancak tek tırnaklı dizeler bu tür eylemleri gerçekleştirmez, dizeyi değişken adıyla birlikte yazıldığı gibi döndürür:

```PHP
$x = "Berat";
echo 'Merhaba $x';
```

Tek tırnak ve çift tırnak kullanımlarındaki bu fark, PHP programlama dilinde metin ve değişkenleri birbirinden ayırmak için kullanılır. Bu sayede, programcı, metni **olduğu** gibi göstermek istediğinde veya değişkenin değerini göstermek istediğinde, bunu kolaylıkla yapabilir.

### Dize Uzunluğu
---
PHP'de `strlen()` fonksiyonu bir dizenin karakter uzunluğunu döndürür.

```PHP
echo strlen("Merhaba Dünya!"); // Çıktı: 14
```

### Kelime Miktarı
---
PHP'de `str_word_count()` fonksiyonu bir dizedeki kelime miktarını sayar.

```PHP
echo str_word_count("Merhaba Dünya!"); // Çıktı: 2
```

### Dizelerde Arama
---
PHP `strpos()` fonksiyonu bir dize içinde belirli bir metni arar.

Bir eşleşme bulunursa, fonksiyon ilk eşleşmenin karakter konumunu döndürür. Eşleşme bulunamazsa, `false` değerini döndürür. İlk karakteri **HER ZAMAN** 0 olarak ele alıp aramaya başlar.

```PHP
echo strpos("Merhaba Dünya!", "Dünya"); // Çıktı: 8 
```

## Dizeleri Değiştirme
---
PHP, dizeleri değiştirmek için kullanabileceğiniz bir dizi yerleşik fonksiyona sahiptir.

### `strtoupper()`
---
`strtoupper()` fonksiyonu dizeyi büyük harf olarak döndürür:

```PHP title:'Bir dizedeki bütün harfleri büyük harf yapma'
$x = "Merhaba Dünya!";
echo strtoupper($x);
```

### `strtolower()`
---
`strtolower()` fonksiyonu dizeyi küçük harf olarak döndürür:

```PHP title:'Bir dizedeki bütün harfleri küçük harf yapma'
$x = "Merhaba Dünya!";
echo strtolower($x);
```

### `str_replace()`
---
`str_replace()` fonksiyonu bir dizedeki bazı karakterleri başka karakterlerle değiştirir.

```PHP title:'Dizede kelime değiştirme'
$x = "Merhaba Dünya!";
echo str_replace("Dünya", "PHP", $x);
```

### `strrev()`
---
`strrev()` fonksiyonu bir dizeyi tersine çevirir.

```PHP title:'Dizeyi ters çevirme'
$x = "Merhaba Dünya!";
echo strrev($x);
```

### `trim()`
---
Boşluk, asıl metinden önceki ve/veya sonraki boşluktur ve çoğu zaman bu boşluğu kaldırmak istersiniz.

`trim()` fonksiyonu, baştaki veya sondaki tüm boşlukları kaldırır:

```PHP title:'Bir dizedeki gereksiz boşlukları kaldırma'
$x = " Merhaba Dünya! ";
echo trim($x);
```

### `explode()`
---
PHP `explode()` fonksiyonu bir dizeyi bir diziye böler.

`explode()` fonksiyonunun ilk parametresi "ayırıcı"yı temsil eder. "Ayırıcı", dizenin nereye bölüneceğini belirtir. 

**Not:** Ayırıcı gereklidir.

```PHP title:'Dizeyi diziye dönüştürme'
$x = "Merhaba Dünya!";
$y = explode(" ", $x); // Ayırıcı boşluktur

// Sonucu görmek için print_r() fonksiyonunu kullanıyoruz
print_r($y);
```

## Dizeleri Birleştirme
---
İki dizeyi birleştirmek veya bir araya getirmek için `.` işlecini kullanabilirsiniz:

```PHP title:'İki dizeyi tek bir dizede birleştirme'
$x = "Merhaba";
$y = "Dünya";
$z = $x . $y;
echo $z;
```

Yukarıdaki örneğin sonucu, iki kelime arasında boşluk olmadan MerhabaDünya'dır.

Böyle boşluk karakteri ekleyebilirsiniz:

```PHP title:'İki dizeyi tek bir dizede birleştirme ve boşluk bırakma'
$x = "Merhaba";
$y = "Dünya";
$z = $x . " " . $y;
echo $z;
```

Daha kolay ve daha iyi bir yol ise çift tırnak işaretlerini kullanmaktır.

İki değişkeni aralarında boşluk bırakarak çift tırnak içine aldığınızda, boşluk sonuçta da mevcut olacaktır:

```PHP title:'Çift tırnakla iki dizeyi birleştirme'
$x = "Merhaba";
$y = "Dünya";
$z = "$x $y";
echo $z;
```

## Dizeleri Bölme
---

### `substr()` - Dizeyi Bölme
---
`substr()` fonksiyonunu kullanarak bir karakter aralığı döndürebilirsiniz.

Başlangıç indeksini ve döndürmek istediğiniz karakter sayısını belirtin.

```PHP title:'Dizeyi bölme'
$x = "Merhaba Dünya!";
echo substr($x, 7, 7);
```

**Not:** İlk karakter 0 indeksine sahiptir.

### `substr()` - Dizeyi Sonuna Kadar Bölme
---
`length` parametresini dışarıda bıraktığınızda, aralık sonuna kadar gidecektir:

```PHP title:'Dizeyi sonuna kadar bölme'
$x = "Merhaba Dünya!";
echo substr($x, 7);
```

### `substr()` - Dizeyi Sonundan Bölme
---
Dilimi dizenin sonundan başlatmak için negatif indeksler kullanın:

```PHP title:'Dizeyi sonundan bölme'
$x = "Merhaba Dünya!";
echo substr($x, -7, 6);
```

**Not:** Son karakter -1 indeksine sahiptir.

### `substr()` - Negatif Uzunluk
---
Dizenin sonundan başlayarak kaç karakter atlanacağını belirtmek için negatif uzunluk kullanın:

```PHP title:'Negatif uzunluk ile bölme'
$x = "Merhaba Dünya!";
echo substr($x, 5, -3);
```

## Kaçış Karakterleri
---
Bir dizeye eklemek istediğiniz özel karakterleri eklemek için bir kaçış (yok sayma) karakteridir.

Kaçış karakteri, ters eğik çizgi \ ve ardından eklemek istediğiniz karakterdir.

| Kaçış Karakteri | Açıklama | Örnek |
| ---- | ---- | ---- |
| `\'` | Tek tırnak karakterinden kaçmak için kullanılır. | `'Bu bir \'alıntı''` |
| `\"` | Çift tırnak karakterinden kaçmak için kullanılır. | `"Bu bir \"alıntı""` |
| `\$` | PHP değişkenlerinden kaçmak için kullanılır. | `\$isim` |
| `\n` | Yeni satır eklemek için kullanılır. | `Bu birinci satır.\nBu ikinci satır.` |
| `\r` | Satır başı eklemek için kullanılır. | `\rBir paragrafa satır başı uygulandı.` |
| `\t` | Tab boşluğu eklemek için kullanılır. | `Bir \tTab boşluğu kadar.` |

```PHP error:1
$x = "Çift tırnak kullanımı olan bir dizede "alıntı" yapmak için kullandığınız başka bir çift tırnak hataya sebep olur.";
```

Bu yüzden kaçış karakterini bu metin içerisinde ekstra uğraşa girmeden kullanabiliriz:

```PHP
$x = "Çift tırnak kullanımı olan bir dizede \"alıntı\" yapmak için kullandığınız başka bir çift tırnak hataya sebep olur.";
```
