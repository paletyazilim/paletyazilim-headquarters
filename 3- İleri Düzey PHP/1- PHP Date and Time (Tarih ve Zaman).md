### `date()` Fonksiyonu
---
PHP `date()` fonksiyonu bir zaman damgasını daha okunabilir bir tarih ve saate biçimlendirir.

#### Syntax
---

```PHP title:'date() Fonksiyonu Syntax'
date(format,timestamp)
```

| Parametre | Açıklama |
| ---- | ---- |
| format | Zorunludur, zaman damgasının biçimini belirtir |
| timestamp | İsteğe bağlı, bir zaman damgası belirtir. Varsayılan değer geçerli tarih ve saattir<br> |

Zaman damgası, belirli bir olayın gerçekleştiği tarih ve/veya saati gösteren bir karakter dizisidir.

### Tarih Alma
---
`date()` fonksiyonunun gerekli format parametresi, tarihin (veya saatin) nasıl biçimlendirileceğini belirtir.

İşte tarihler için yaygın olarak kullanılan bazı karakterler:

d - Ayın gününü temsil eder (01 ila 31)
m - Bir ayı temsil eder (01 ila 12)
Y - Bir yılı temsil eder (dört basamaklı olarak)
l (küçük harf 'L') - Haftanın gününü temsil eder
Ek biçimlendirme eklemek için karakterler arasına "/", "." veya "-" gibi diğer karakterler de eklenebilir.

Aşağıdaki örnekte bugünün tarihi üç farklı şekilde biçimlendirilmiştir:

```PHP title='Tarih formatlarına basit bir örnek'
<?php
echo "Bu günün tarihi: " . date("Y/m/d") . "<br>";
echo "Bu günün tarihi " . date("Y.m.d") . "<br>";
echo "Bu günün tarihi " . date("Y-m-d") . "<br>";
echo "Gün: " . date("l");
?>
```

### Otomatik Telif Hakkı Yılı
---
Web sitenizdeki telif hakkı yılını otomatik olarak güncellemek için `date()` fonksiyonunu kullanın:

```PHP title:'Ufak bir teknik'
&copy; 2010-<?php echo date("Y");?>
```

### Zaman Alma
---
İşte zamanlar için yaygın olarak kullanılan bazı karakterler:

H - Bir saatin 24 saatlik biçimi (00 ila 23)
h - Başında sıfırlar olan bir saatin 12 saatlik biçimi (01 ila 12)
i - Başında sıfırlar olan dakikalar (00 ila 59)
s - Başında sıfırlar olan saniyeler (00 ila 59)
a - Küçük harf (Ante meridiem ve Post meridiem) (am veya pm)

Aşağıdaki örnek, belirtilen formatta geçerli saatin çıktısını verir:

```PHP title='Zaman formatlarına basit bir örnek'
<?php
echo "Saat: " . date("H:i:s");
?>
```

PHP `date()` fonksiyonunun sunucunun geçerli tarih/saatini döndüreceğini unutmayın!

### Saat Dilimi Alma
---
Koddan geri aldığınız saat doğru değilse, bunun nedeni muhtemelen sunucunuzun başka bir ülkede olması veya farklı bir saat dilimine göre ayarlanmış olmasıdır.

Dolayısıyla, saatin belirli bir konuma göre doğru olması gerekiyorsa, kullanmak istediğiniz saat dilimini ayarlayabilirsiniz.

Aşağıdaki örnek, saat dilimini "America/New_York" olarak ayarlar ve ardından geçerli saati belirtilen biçimde çıktı olarak verir:

```PHP title='Saat dilimini değiştirme'
<?php
date_default_timezone_set("America/New_York");
echo "The time is " . date("H:i:s");
?>
```

### `mktime()` ile Tarih Oluşturma
---
`date()` fonksiyonundaki isteğe bağlı timestamp parametresi bir zaman damgası belirtir. Atlanırsa, geçerli tarih ve saat kullanılır (yukarıdaki örneklerde olduğu gibi).

PHP `mktime()` fonksiyonu bir tarih için Unix zaman damgasını döndürür. Unix zaman damgası, Unix Epoch (1 Ocak 1970 00:00:00 GMT) ile belirtilen saat arasındaki saniye sayısını içerir.

#### Syntax
---

```PHP title:'mktime() Fonksiyonu Syntax'
mktime(hour, minute, second, month, day, year)
```

Aşağıdaki örnek, `mktime()` fonksiyonundaki bir dizi parametreden `date()` fonksiyonuyla bir tarih ve saat oluşturur:

```PHP title:'mktime() ile tarih oluşturma'
<?php
$d=mktime(11, 14, 54, 8, 12, 2014);
echo "Oluşturulan Tarih: " . date("Y-m-d H:i:s", $d);
?>
```

### `Strtotime` ile Dizeyle Tarih Oluşturma
---
PHP `strtotime()` fonksiyonu insan tarafından okunabilir bir tarih dizesini Unix zaman damgasına (1 Ocak 1970 00:00:00 GMT'den bu yana geçen saniye sayısı) dönüştürmek için kullanılır.

#### Syntax
---

```PHP title:'strtotime() Fonkisyonu Syntax'
strtotime(time, now)
```

Aşağıdaki örnek `strtotime()` fonksiyonundan bir tarih ve saat oluşturur:

```PHP title:'strtotime() ile tarih oluşturma'
<?php
$d=strtotime("10:30pm April 15 2014");
echo "Oluşturulan Tarih: " . date("Y-m-d H:i:s", $d);
?>
```

PHP bir dizeyi tarihe dönüştürme konusunda oldukça zekidir, böylece çeşitli değerler girebilirsiniz:

```PHP title:'strtotime() alternatifleri'
<?php
$d=strtotime("tomorrow");
echo date("Y-m-d H:i:s", $d) . "<br>";

$d=strtotime("next Saturday");
echo date("Y-m-d H:i:s", $d) . "<br>";

$d=strtotime("+3 Months");
echo date("Y-m-d H:i:s", $d) . "<br>";
?>
```

> `strtotime()`  içine koyduğunuz dizeleri kontrol etmeyi unutmayın.

