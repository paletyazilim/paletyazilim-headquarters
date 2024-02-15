Bu bölümde Tamsayılar, Ondalıklı Sayılar ve Sayı Dizeleri konularını derinlemesine inceleyeceğiz.

### Sayısal Türlere Genel Bakış
---
PHP'de üç ana sayısal veri türü vardır bunlar:

- Integer (Tamsayı)
- Float (Ondalıklı)
- Numerical Strings (Metinsel Sayılar)

Buna ek olarak, PHP sayılar için kullanılan iki veri türüne daha sahiptir:

- Infinity (Sonsuz)
- NaN

Sayısal türdeki değişkenler, onlara bir değer atadığınızda oluşturulur:

```PHP
$a = 5;
$b = 5.34;
$c = "25";
```

### Tam Sayılar
---

2, 256, -256, 10358, -179567, tamamı tamsayıdır.

Tam sayı, ondalık kısmı olmayan bir sayıdır.

32 bit sistemlerde tamsayı veri tipi -2147483648 ile 2147483647 arasındaki, 64 bit sistemlerde ise -9223372036854775808 ile 9223372036854775807 arasındaki ondalık olmayan sayılardır. Bu limiti aşan değerler tamsayı sınırını aştıkları için ondalıklı (float) olarak saklanır.

**Not:** `4 * 2.5` işleminin sonucu 10 olsa bile, sayılardan biri (2.5) ondalıklı olduğu için sonuç ondalık (Float) olarak saklanır.

**Tamsayılar için bazı kurallar:**

- En az bir basamağa sahip olmalıdır.
- Ondalık noktası olmamalıdır.
- Pozitif veya negatif olabilir.
- Ondalık (baz 10), onaltılık (baz 16 - 0x ile başlayan), sekizlik (baz 8 - 0 ile başlayan) veya ikilik (baz 2 - 0b ile başlayan) olmak üzere üç formatta belirtilebilir.

**PHP'de tamsayılar için aşağıdaki ön tanımlı sabitler bulunur:**

- `PHP_INT_MAX` - Desteklenen en büyük tamsayı
- `PHP_INT_MIN` - Desteklenen en küçük tamsayı
- `PHP_INT_SIZE` - Bir tamsayının boyutunun byte cinsinden değeri

**PHP'de bir değişkenin türünün tamsayı olup olmadığını kontrol etmek için aşağıdaki fonksiyonlar kullanılır:**

- `is_int()`
- `is_integer()` - `is_int()`'in alternatifi
- `is_long()` - `is_int()`'in alternatifi

```PHP
$x = 5985;
var_dump(is_int($x));

$x = 59.85;
var_dump(is_int($x));
```

### Ondalıklı Sayılar
---
Bir float, ondalık noktası olan veya üstel formda bir sayıdır. Örneğin:

* 2.0
* 256.4
* 10.358
* 7.64E+5 (7.64 x 10 üzeri 5)
* 5.56E-5 (5.56 x 10 üzeri -5)

Float veri türü genellikle 1.7976931348623E+308'e kadar bir değeri saklayabilir (platforma bağlıdır) ve maksimum 14 basamaklı bir hassasiyete sahiptir.

PHP programlama dilinde float sayıları için önceden tanımlanmış bazı sabitler vardır (PHP 7.2 itibariyle):

* `PHP_FLOAT_MAX`: Gösterilebilecek en büyük float sayı.
* `PHP_FLOAT_MIN`: Gösterilebilecek en küçük pozitif float sayı.
* `PHP_FLOAT_DIG`: Bir float sayıya yuvarlanıp kayıpsız bir şekilde geri dönüştürülebilecek ondalık basamak sayısı.
* `PHP_FLOAT_EPSILON`: Gösterilebilecek en küçük pozitif sayı `x`, öyle ki `x + 1.0 != 1.0`.

Bir değişkenin float olup olmadığını kontrol etmek için şu fonksiyonları kullanabilirsiniz:

* `is_float()`
* `is_double()`'in diğer alternatifi

```PHP
$x = 10.365;
var_dump(is_float($x));
```

### Infinity
---
 PHP'de, `PHP_FLOAT_MAX` sabitinden daha büyük olan sayısal değerler sonsuz (infinite) olarak kabul edilir.

Bir sayısal değerin sonlu mu yoksa sonsuz mu olduğunu kontrol etmek için PHP'de şu fonksiyonlar bulunur:

- `is_finite()` Bir değerin sonlu olup olmadığını kontrol eder. Sonlu ise True, değilse False döndürür.
- `is_infinite()` Bir değerin sonsuz olup olmadığını kontrol eder. Sonsuz ise True, değilse False döndürür.

Ancak,` var_dump()` fonksiyonu, bir değerin veri türü ve değerini göstermek için kullanılabilir. Bu, bir değerin sonsuz olup olmadığını belirlemek için de kullanılabilir.


```php
$x = 1.9e411;
var_dump($x);
```

### NaN (Not a Number)
---
NaN, Not a Number (Sayı Değil) anlamına gelir.

NaN, gerçekleştirilmesi imkansız matematiksel işlemler için kullanılır.

PHP, bir değerin sayı olup olmadığını kontrol etmek için şu fonksiyona sahiptir:

- `is_nan()`

```PHP
$x = acos(8);
var_dump($x);
```

### Numerical Strings
---
PHP'de `is_numeric()` fonksiyonu bir değişkenin sayısal olup olmadığını bulmak için kullanılabilir. İşlev, değişken bir sayı veya sayısal bir dize ise `true`, değilse `false` döndürür.

```PHP
$x = 5985;
var_dump(is_numeric($x));

$x = "5985";
var_dump(is_numeric($x));
$x = "59.85" + 100;
var_dump(is_numeric($x));

$x = "Hello";
var_dump(is_numeric($x));
```

> Bilginize: PHP 7.0'dan itibaren: `is_numeric()` fonksiyonu onaltılık biçimdeki sayısal dizeler (örneğin 0xf4c3b00c) için `false` döndürür, çünkü bunlar artık sayısal dize olarak kabul edilmezler.

### PHP'de Sayıları Dönüştürme
---
Bazen kodunuzda bir sayısal değeri farklı bir veri türüne dönüştürmeniz gerekebilir. Örneğin, bir metin dizesini veya ondalık sayıyı (float) tam sayıya (integer) dönüştürmeniz gerekebilir.

`(int)`, `(integer)` ve `intval()` fonksiyonları genellikle bir değeri tamsayıya dönüştürmek için kullanılır:

```PHP
// Ondalıklı Veriyi Tam Sayıya dönüştürme
$float_veri = 23465.768;
$int_veri = (int)$float_veri;
echo $int_veri;

echo "<br>";

// Dizesel Veriyi Tam Sayıya Dönüştürme
$dize_veri = "23465.768";
$int_veri = (int)$dize_veri;
echo $int_veri;
```





