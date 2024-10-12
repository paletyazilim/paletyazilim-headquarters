Operatörler, bir veya birden fazla operand (işlenen) üzerinde işlem yapmak için kullanılan özel karakterler veya karakter gruplarıdır. Operatörler, matematikte kullanılan operatörlere benzer şekilde çalışır ve programda çeşitli işlemleri gerçekleştirmemizi sağlar.

PHP operatörleri aşağıdaki gruplara ayırır:

- Aritmetik operatörler
- Atama operatörleri
- Karşılaştırma operatörleri
- Arttırma/Azaltma operatörleri
- Mantıksal operatörler
- Dize operatörleri
- Dizi operatörleri
- Koşullu atama operatörleri

### Aritmetik Operatörler
---
Aritmetik işleçler, toplama, çıkarma, çarpma gibi yaygın aritmetik işlemleri gerçekleştirmek için sayısal değerlerle kullanılır.

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| + | Toplama | $x + $y | `$x` ve `$y`'nin toplamı |
| - | Çıkarma | $x - $y | `$x` ve `$y`'nin farkı |
| * | Çarpma | $x * $y | `$x` ve `$y`'nin çarpımı |
| / | Bölme | $x / $y | `$x` ve `$y`'nin bölümü |
| % | Kalan | $x % $y | `$x` ve `$y`'nin kalanı |
| ** | Üs Alma | $x ** $y | `$x`'i `$y`'inci kuvvete yükseltmenin sonucu |

### Atama Öperatörleri
---
Atama işleçleri, bir değişkene değer yazmak için sayısal değerlerle kullanılır.

PHP'deki temel atama operatörü "`=`" dir. Bu, soldaki işlenenin sağdaki atama ifadesinin değerine ayarlanacağı anlamına gelir.

| Operatör | Aynı... | Açıklama |
| ---- | ---- | ---- |
| x = y | x = y | Sol operand sağdaki ifadenin değerine ayarlanır |
| x += y | x = x + y | Toplama |
| x -= y	 | x = x - y | Çıkarma |
| x *= y	 | x = x * y | Çarpma |
| x /= y	 | x = x / y | Bölme |
| x %= y	 | x = x % y | Kalan |

### Karşılaştırma Operatörleri
---
PHP karşılaştırma işleçleri iki değeri (sayı veya dize) karşılaştırmak için kullanılır:

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| ==	 | Eşit | $x == $y | Eğer `$x`, `$y`'ye eşitse `true` döndürür |
| ===	 | Aynı | $x === $y | Eğer `$x`, `$y`'ye eşitse ve aynı veri tipindeyse `true` döndürür |
| !=	 | Eşit Değil | $x != $y | Eğer `$x`, `$y`'ye eşit değilse `true` döndürür |
| <> | Eşit Değil | $x <> $y | Eğer `$x`, `$y`'ye eşit değilse `true` döndürür |
| !== | Aynı Değil | $x !== $y | Eğer `$x`, `$y`'ye eşit değilse veya aynı veri tipinden değilse `true` döndürür |
| > | Daha Büyük | $x > $y | Eğer `$x`, `$y`'den büyükse `true` döndürür |
| < | Daha Küçük | $x < $y | Eğer `$x`, `$y`'den küçükse `true` döndürür |
| >= | Daha Büyük veya Eşit | $x >= $y | `$x` `$y`'den büyük veya eşitse `true` döndürür |
| <= | Daha Küçük veya Eşit | $x <= $y | `$x` `$y`'den küçük veya eşitse `true` döndürür |
| <=> | Uzay Gemisi | $x <=> $y | `$x`'in `$y`'den küçük, eşit veya büyük olmasına bağlı olarak sıfırdan küçük, eşit veya büyük bir tamsayı döndürür. PHP 7'de tanıtılmıştır. |

### Arttırma / Azaltma Operatörleri
---
Arttırma / azaltma işleçleri bir değişkenin değerini arttırmak için kullanılır.

| Operatör | Açıklama |
| ---- | ---- |
| ++$x | 	`$x` değerini bir artırır, sonra `$x` değerini döndürür |
| $x++ | `$x` değerini döndürür, ardından `$x` değerini bir artırır |
| --$x	 | `$x` değerini bir azaltır, sonra `$x` değerini döndürür |
| $x--	 | `$x` değerini döndürür, ardından `$x` değerini bir azaltır |

### Mantıksal Operatörler
---
Mantıksal işleçler koşullu ifadeleri birleştirmek için kullanılır.

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| and | Ve | $x and $y | Hem `$x` hem de `$y` doğruysa `true` |
| or | Veya | $x or $y | `$x` veya `$y` değerlerinden biri doğruysa `true` |
| xor | Özelleştirilmiş Veya | $x xor $y | `$x` veya `$y` değerlerinden biri doğruysa `true`, ancak ikisi birden doğruysa `false` |
| &&	 | Ve | $x && $y | Hem `$x` hem de `$y` doğruysa `true` |
| \|\| | Veya | $x \|\| $y | `$x` veya `$y` değerlerinden biri doğruysa `true` |
| ! | Değil | !$x | Eğer `$x` doğru değilse `true` |

### Dize Operatörleri
---
PHP, dizeler için özel olarak tasarlanmış iki operatöre sahiptir.

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| . | Birleştirme | 	$txt1 . $txt2 | `$txt1` ve `$txt2`'nin birleştirilmesi |
| .= | Birleştirme Ataması | $txt1 .= $txt2 | `$txt2`'yi `$txt1`'e ekler |

### Dizi Operatörleri
---
PHP dizi işleçleri dizileri karşılaştırmak için kullanılır.

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| +	 | Birleştirme | $x + $y | `$x` ve `$y`'nin birleşimi |
| == | Eşit | $x == $y | `$x` ve `$y` aynı anahtar/değer çiftlerine sahipse `true` döndürür |
| === | Aynı | $x === $y | Eğer `$x` ve `$y` aynı veri tipine ve aynı anahtar/değer çiftlerine sahipse `true` döndürür |
| != | Eşit  Değil | $x != $y | Eğer `$x`, `$y`'ye eşit değilse `true` döndürür |
| <> | Eşit Değil | $x <> $y | Eğer `$x`, `$y`'ye eşit değilse `true` döndürür |
| !==	 | Aynı Değil | $x !== $y	 | Eğer `$x` ve `$y` aynı veri tipine ve aynı anahtar/değer çiftlerine sahip değilse `true` döndürür |

### Koşullu Atama Operatörleri
---
PHP koşullu atama işleçleri, koşullara bağlı olarak bir değer ayarlamak için kullanılır:

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| ?: | Ternary (Üçlü Koşul) | $x = expr1 ? expr2 : expr3 | `$x` değerini döndürür:<br>expr1 = `true` ise `$x`'in değeri expr2 olur.<br><br>expr1 = `false` ise `$x`'in değeri expr3 olur. |
| ??	 | 	Null Coalescing | $x = expr1 ?? expr2 | `$x` değerini döndürür:<br>expr1 varsa ve NULL değilse, `$x`'in değeri expr1 olur.<br><br>expr1 yoksa veya NULL ise `$x`'in değeri expr2 olur.<br><br>PHP 7'de tanıtıldı |
