PHP'de çıktı alma işlemi için iki temel yöntem bulunmaktadır: echo ve print. Bu derste neredeyse her örnekte echo veya print kullanıyoruz. Bu nedenle, bu bölümde bu iki çıktı ifadesi hakkında daha fazla bilgi verilmektedir.

### PHP `echo` ve `print` İfadeleri
---
`echo` ve `print`, büyük ölçüde benzer işlevlere sahiptir. Her ikisi de verileri ekrana yazdırmak için kullanılır.

Aralarındaki farklar küçüktür:

- `echo`, değer döndürmez, ancak `print` 1 değerini döndürür, bu nedenle ifadelerde kullanılabilir.
- `echo`, birden fazla parametre alabilir (bu tür kullanımlar nadir olsa da), ancak `print` yalnızca bir parametre alabilir.
- `echo`, `print`'ten biraz daha hızlıdır.

#### PHP `echo` İfadesi
---
`echo` ifadesi, parantezli veya parantezsiz kullanılabilir: `echo` veya `echo()`.

##### Metin Görüntüleme
---
Aşağıdaki örnek, `echo` komutuyla nasıl metin çıktısı alınacağını göstermektedir (metnin HTML işaretlemesi içerebileceğini unutmayın):

```PHP 
echo "<h2>PHP Eğlencelidir!</h2>";
echo "Merhaba dünya!<br>";
echo "PHP öğrenmek üzereyim!<br>";
echo "Bu ", "metin ", "birden ", "fazla ", "parametreyle oluşturuldu.";
```

##### Değişkenleri Görüntüleme
---
Aşağıdaki örnek, `echo` ifadesiyle metin ve değişkenlerin nasıl çıktı alınacağını göstermektedir:

```PHP
$metin1 = "PHP Öğrenin";
$metin2 = "php.net";
$x = 5;
$y = 4;

echo "<h2>" . $metin1 . "</h2>";
echo "PHP öğrenin: " . $metin2 . "<br>";
echo $x + $y;
```

#### PHP `print` İfadesi
---
`print` ifadesi, parantezli veya parantezsiz kullanılabilir: `print` veya `print()`.

#### Metin Görüntüleme
---
Aşağıdaki örnek, `print` komutuyla nasıl metin çıktısı alınacağını göstermektedir (metnin HTML işaretlemesi içerebileceğini unutmayın):

```PHP
print "<h2>PHP Eğlencelidir!</h2>";
print "Merhaba dünya!<br>";
print "PHP öğrenmek üzereyim!";
```

#### Değişkenleri Görüntüleme
---
Aşağıdaki örnek, `print` ifadesiyle metin ve değişkenlerin nasıl çıktı alınacağını göstermektedir:

```PHP
$metin1 = "PHP Öğrenin";
$metin2 = "php.net";
$x = 5;
$y = 4;

print "<h2>" . $metin1 . "</h2>";
print "PHP öğrenin: " . $metin2 . "<br>";
print $x + $y;
```