Koşullu ifadeler, farklı koşullara bağlı olarak farklı eylemler gerçekleştirmek için kullanılır.

### Koşullu İfadeler
---
Kod yazarken çoğu zaman farklı koşullar için farklı eylemler gerçekleştirmek istersiniz. Bunu yapmak için kodunuzda koşullu ifadeler kullanabilirsiniz.

PHP'de aşağıdaki koşullu ifadelere sahibiz:

- **`if` Deyimi:** Bir koşul doğruysa bazı kodları çalıştırır.
- **`if`-`else` Deyimi:** Bir koşul doğruysa bazı kodları ve bu koşul yanlışsa başka bir kodu çalıştırır.
- **`if`-`elseif`-`else` Deyimi:** İkiden fazla koşul için farklı kodlar çalıştırır
- **`switch` Deyimi:** Çalıştırılacak birçok kod bloğundan birini seçer.

### `if` Koşulu
---

```PHP title:'if Syntax' hl:1
if (kosul) {
  // Koşul doğru ise çalıştırılacak kodlar.
}
```

```PHP title:'Basit bir örnek'
if (5 > 3) {
  echo "Doğru: 5 3'ten Büyüktür!";
}
```

Ayrıca `if` deyiminde değişkenler de kullanabiliriz:

```PHP title:'Değişken kullanılan bir örnek555'
$t = 14;

if ($t < 20) {
  echo "Doğru: $t 20'den Küçüktür!";
}
```

### `if` Operatörleri
---
`if` deyimleri genellikle iki değeri karşılaştıran koşullar içerir.

```PHP
$t = 14;

if ($t == 14) {
  echo "$t 14'le Eşittir!";
}
```

İşte `if` deyimlerinde kullanılacak PHP karşılaştırma operatörleri:

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| == | Eşit | $x == $y | Eğer `$x`, `$y`'ye eşitse `true` döndürür |
| === | Aynı | $x === $y | Eğer `$x`, `$y`'ye eşitse ve aynı veri tipindeyse `true` döndürür |
| != | Eşit Değil | $x != $y | Eğer `$x`, `$y`'ye eşit değilse `true` döndürür |
| <> | Eşit Değil | $x <> $y | Eğer `$x`, `$y`'ye eşit değilse `true` döndürür |
| !== | Aynı Değil | $x !== $y | Eğer `$x`, `$y`'ye eşit değilse veya aynı veri tipinden değilse `true` döndürür |
| > | Daha Büyük | $x > $y | Eğer `$x`, `$y`'den büyükse `true` döndürür |
| < | Daha Küçük | $x < $y | Eğer `$x`, `$y`'den küçükse `true` döndürür |
| >= | Daha Büyük veya Eşit | $x >= $y | `$x` `$y`'den büyük veya eşitse `true` döndürür |
| <= | Daha Küçük veya Eşit | $x <= $y | `$x` `$y`'den küçük veya eşitse `true` döndürür |
| <=> | Uzay Gemisi | $x <=> $y | `$x`'in `$y`'den küçük, eşit veya büyük olmasına bağlı olarak sıfırdan küçük, eşit veya büyük bir tamsayı döndürür. PHP 7'de tanıtılmıştır. |

Birden fazla koşulu kontrol etmek için `&&` operatörü gibi mantıksal operatörler kullanabiliriz:

```PHP title:"$a'nın $b'den büyük olup olmadığını VE $a'nın $c'den küçük olup olmadığını kontrol edin"
$a = 200;
$b = 33;
$c = 500;

if ($a > $b && $a < $c ) {
  echo "Bütün koşullar doğru!";
}
```

İşte `if` deyimlerinde kullanılacak PHP mantıksal operatörleri:

| Operatör | İsim | Örnek | Açıklama |
| ---- | ---- | ---- | ---- |
| and | Ve | $x and $y | Hem `$x` hem de `$y` doğruysa `true` |
| or | Veya | $x or $y | `$x` veya `$y` değerlerinden biri doğruysa `true` |
| xor | Özelleştirilmiş Veya | $x xor $y | `$x` veya `$y` değerlerinden biri doğruysa `true`, ancak ikisi birden doğruysa `false` |
| && | Ve | $x && $y | Hem `$x` hem de `$y` doğruysa `true` |
| \|\| | Veya | $x \|\| $y | `$x` veya `$y` değerlerinden biri doğruysa `true` |
| ! | Değil | !$x | Eğer `$x` doğru değilse `true` |

### `if`-`else` Koşulu
---
`if`-`else` deyimi, bir koşul doğruysa bazı kodları ve bu koşul yanlışsa başka bir kodu çalıştırır.

```PHP title:'if-else Syntax' hl:1,3
if (kosul) {
  // Koşul doğru ise çalıştırılacak kodlar.
} else {
  // Koşul yanlış ise çalıştırılacak kodlar.
}
```

```PHP title:'Basit bir örnek'
$t = date("H"); // Saati alır

if ($t < "20") {
  echo "İyi Günler!";
} else {
  echo "İyi Akşamlar!";
}
```

### `if`-`elseif`-`else` Koşulu
---
`if`-`elseif`-`else` deyimi ikiden fazla koşul için farklı kodlar çalıştırır.

```PHP title:'if-elseif-else Syntax' hl:1,3,5
if (kosul) {
  // Eğer bu koşul doğru ise çalıştırılacak kodlar.
} elseif (kosul) {
  // Diğer koşul yanlış ise bu koşul doğru ise çalıştırılacak kodlar.
} else {
  // Tüm koşullar yanlış ise çalıştırılacak kodlar.
}
```

```PHP title:'Basit bir örnek'
$t = date("H");

if ($t < "10") {
  echo "Günaydın!";
} elseif ($t < "20") {
  echo "İyi günler!";
} else {
  echo "İyi Akşamlar!";
}
```

### Tek Satırlı Koşullar
---
Daha kısa kod yazmak için `if` deyimlerini tek satırda yazabilirsiniz.

```PHP hl:3
$a = 5;

if ($a < 10) $b = "Merhaba";

echo $b
```

`if`-`else` ifadeleri de tek satırda yazılabilir, ancak syntax biraz farklıdır.

```PHP hl:3
$a = 13;

$b = $a < 10 ? "Merhaba" : "Güle Güle";

echo $b;
```

>Bu teknik Ternary (Üçlü Operatörler) olarak bilinir.

### İç İçe Koşullar
---
`if` deyimlerinin içinde `if` deyimleri olabilir, buna iç içe `if` deyimleri denir.

```PHP title:'Basit bir örnek'
$a = 13;

if ($a > 10) {
  echo "Değer 10'dan büyük";
  if ($a > 20) {
    echo " ve 20'den Büyük";
  } else {
    echo " ancak 20'den Küçük";
  }
}
```