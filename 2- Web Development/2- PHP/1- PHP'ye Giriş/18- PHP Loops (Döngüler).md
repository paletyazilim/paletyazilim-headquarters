Genellikle kod yazarken, aynı kod bloğunun belirli veya bilinmeyen sayıda tekrar tekrar çalışmasını isteyebilirsiniz. Bu nedenle, bir komut dosyasına kod satırını manuel eklemek yerine, döngüleri kullanabiliriz.

Döngüler, belirli bir koşul doğru olduğu sürece aynı kod bloğunu tekrar tekrar çalıştırmak için kullanılır.

PHP'de aşağıdaki döngü türlerine sahibiz:

- `while` - belirtilen koşul doğru olduğu sürece bir kod bloğu boyunca döner
- `do`-`while` - bir kod bloğu boyunca bir kez döner ve ardından belirtilen koşul doğru olduğu sürece döngüyü tekrarlar
- `for` - bir kod bloğu üzerinde belirli sayıda döngü yapar
- `foreach` - bir dizideki her öğe için bir kod bloğu boyunca döngüler

### `while` Döngüsü
---
`while` döngüsü, belirtilen koşul doğru olduğu sürece bir kod bloğunu yürütür.

```PHP title:'while Syntax'
while (kosul) {
  // Koşul doğru ise çalıştırılacak kod bloğu
}
```

```PHP title:'Basit bir örnek'
$i = 1;
while ($i < 6) {
  echo $i;
  $i++;
}
```

**Not:** `$i` değerini artırmayı unutmayın, aksi takdirde döngü sonsuza kadar devam eder.

`while` döngüsü belirli bir sayıda çalışmaz, ancak her yinelemeden sonra koşulun hala doğru olup olmadığını kontrol eder.

Koşulun bir sayaç olması gerekmez, bir işlemin durumu veya doğru ya da yanlış olarak değerlendirilen herhangi bir koşul olabilir.

#### `break` Anahtar Sözcüğü
----
`break` deyimi ile koşul hala doğru olsa bile döngüyü durdurabiliriz:

```PHP title:'Döngüyü Kırma' hl:3
$i = 1;
while ($i < 6) {
  if ($i == 3) break;
  echo $i;
  $i++;
}
```

#### `continue` Anahtar Sözcüğü
---
`continue` deyimi ile mevcut yinelemeyi durdurabilir (mevcut satırdan sonraki satırları yok sayar) ve bir sonraki ile devam edebiliriz:

```PHP title:'Döngüde Atlama' hl:4
$i = 0;
while ($i < 6) {
  $i++;
  if ($i == 3) continue;
  echo $i;
}
```

#### Alternatif Syntax
---
`while` döngüsü sözdizimi aşağıdaki gibi `endwhile` deyimi ile de yazılabilir:

```PHP title:'Alternatif Syntax' hl:2,5
$i = 1;
while ($i < 6):
  echo $i;
  $i++;
endwhile;
```

### `do while` Döngüsü
---
`do`-`while` döngüsü her zaman kod bloğunu en az bir kez çalıştırır, ardından koşulu kontrol eder ve belirtilen koşul doğruysa döngüyü tekrarlar.

```PHP title:'do-while Syntax'
do {
  // Koşul doğru ise çalıştırlacak kod bloğu
} while (kosul);
```

```PHP title:'Basit bir örnek'
$i = 1;

do {
  echo $i;
  $i++;
} while ($i < 6);
```

**Not:** Bir `do`-`while` döngüsünde koşul, döngü içindeki ifadeler yürütüldükten **SONRA** test edilir. Bu, `do`-`while` döngüsünün, koşul yanlış olsa bile, deyimlerini en az bir kez yürüteceği anlamına gelir.

```PHP title:'Yanlış Koşul'
$i = 8;

do {
  echo $i;
  $i++;
} while ($i < 6);
```

>Koşul hiçbir zaman doğru olmasa bile kod bir kez çalıştırılacaktır.

#### `break` Anahtar Sözcüğü
---
`break` deyimi ile koşul hala doğru olsa bile döngüyü durdurabiliriz:

```PHP title:'Döngüyü Kırma' hl:4
$i = 1;

do {
  if ($i == 3) break;
  echo $i;
  $i++;
} while ($i < 6);
```

#### `continue` Anahtar Sözcüğü
---
`continue` deyimi ile mevcut yinelemeyi durdurabilir (mevcut satırdan sonraki satırları yok sayar) ve bir sonraki ile devam edebiliriz:

```PHP title:'Döngüyü Atlama' hl:5
$i = 0;

do {
  $i++;
  if ($i == 3) continue;
  echo $i;
} while ($i < 6);
```

### `for` Döngüsü
---
`for` döngüsü, kodun kaç kez çalıştırılması gerektiğini bildiğinizde kullanılır.

```PHP title:'for Syntax'
for (ifade1, ifade2, ifade3) {
  // Kod Bloğu
}
```

For şu şekilde çalışıyor:

- iafde1 bir kez değerlendirilir
- ifade2 her iterasyondan önce değerlendirilir
- ifade3 her iterasyondan sonra değerlendirilir

```PHP title:'Basit bir örnek'
for ($x = 0; $x <= 10; $x++) {
  echo "X: $x <br>";
}
```

Örnek Açıklaması
- **İlk ifade:** `$x = 0;`, bir kez değerlendirilir ve sayacı 0'a ayarlar.
- **İkinci ifade:** `$x <= 10;`, her yinelemeden önce değerlendirilir ve kod bloğu yalnızca bu ifade `true` olarak değerlendirilirse yürütülür. Bu örnekte, `$x` değeri 10'dan küçük veya 10'a eşit olduğu sürece ifade doğrudur.
- **Üçüncü ifade:** `$x++;`, her yinelemeden sonra değerlendirilir ve bu örnekte, ifade her yinelemede `$x` değerini bir artırır.

#### `break` Anahtar Sözcüğü
---
`break` deyimi ile koşul hala doğru olsa bile döngüyü durdurabiliriz:

```PHP title:'Döngüyü Kırma' hl:2
for ($x = 0; $x <= 10; $x++) {
  if ($i == 3) break;
  echo "X: $x <br>";
}
```

#### `continue` Anahtar Sözcüğü
---
`continue` deyimi ile mevcut yinelemeyi durdurabilir (mevcut satırdan sonraki satırları yok sayar) ve bir sonraki ile devam edebiliriz:

```PHP title:'Döngüyü Atlama' hl:2
for ($x = 0; $x <= 10; $x++) {
  if ($x == 3) continue;
  echo "X: $x <br>";
}
```

#### Arttırma Miktarı
---
Bu örnek 100'e kadar onar onar sayar:

```PHP title:'Arttırma Miktarı Ayarlama' hl:1
for ($x = 0; $x <= 100; $x+=10) {
  echo "X: $x <br>";
}
```

### `foreach` Döngüsü
---
`foreach` döngüsü bir dizideki her öğe veya bir nesnedeki her özellik için bir kod bloğu döngüler.

```PHP title:'foreach Syntax'
foreach (object as value)
{
  // Kod Bloğu
}
```

```PHP title:'Basit bir örnek'
$renkler = array("kırmızı", "yeşil", "mavi", "sarı");

foreach ($renkler as $renk) {
  echo "$renk <br>";
}
```

Her döngü yinelemesinde, geçerli dizi öğesinin değeri `$x` değişkenine atanır. Yineleme, son dizi elemanına ulaşana kadar devam eder.

#### Anahtarlar ve Değerler
---
Yukarıdaki dizi, ilk öğenin `0` anahtarına, ikincinin `1` anahtarına sahip olduğu ve bu şekilde devam eden tipik bir dizidir.

İlişkisel diziler farklıdır, ilişkisel diziler kendilerine atadığınız adlandırılmış anahtarları kullanır ve ilişkisel diziler arasında döngü yaparken değerin yanı sıra anahtarı da tutmak isteyebilirsiniz.

Bu, `foreach` tanımında aşağıdaki gibi hem anahtar hem de değer belirtilerek yapılabilir:

```PHP title:'İlişkisel Dizilerde foreach Döngüsü'
$uyeler = array("Berat"=>"20", "Metin"=>"25", "Ahmet"=>"20");

foreach ($uyeler as $anahtar => $deger) {
  echo "$anahtar : $deger <br>";
}
```

#### Nesnelerde `foreach` Döngüsü
---
`foreach` döngüsü, bir nesnenin özellikleri arasında döngü yapmak için de kullanılabilir:

```PHP title:'Nesnelerde foreach Döngüsü'
class Araba {
  public $renk;
  public $model;
  public function __construct($renk, $model) {
    $this->renk = $renk;
    $this->model = $model;
  }
}

$arabam = new Araba("kırmızı", "Volvo");

foreach ($arabam as $degisken => $deger) {
  echo "$degisken: $deger <br>";
}
```

#### `break` Anahtar Sözcüğü
---
`break` deyimi ile koşul hala doğru olsa bile döngüyü durdurabiliriz:

```PHP title:'Döngüyü Kırma' hl:4
$renkler = array("Kırmızı", "Yeşil", "Mavi", "Sarı");

foreach ($renkler as $renk) {
  if ($renk == "Mavi") break;
  echo "$x <br>";
}
```

#### `continue` Anahtar Sözcüğü
---
`continue` deyimi ile mevcut yinelemeyi durdurabilir (mevcut satırdan sonraki satırları yok sayar) ve bir sonraki ile devam edebiliriz:

```PHP title:'Döngüyü Atlama' hl:4
$renkler = array("Kırmızı", "Yeşil", "Mavi", "Sarı");

foreach ($renkler as $renk) {
  if ($renk == "Mavi") continue;
  echo "$x <br>";
}
```

#### `foreach` Byref
---
Dizi öğeleri arasında döngü yaparken, dizi öğesinde yapılan herhangi bir değişiklik varsayılan olarak orijinal diziyi ETKİLEMEYECEKTİR:

```PHP
$renkler = array("Kırmızı", "Yeşil", "Mavi", "Sarı");

foreach ($renkler as $renk) {
  if ($renk == "Mavi") $renk = "Pembe";
}

var_dump($renkler);
```

ANCAK, `foreach` bildiriminde & karakteri kullanılarak, dizi öğesi referans olarak atanır, bu da dizi öğesinde yapılan herhangi bir değişikliğin orijinal dizide de yapılmasına neden olur:

```PHP hl:3
$renkler = array("Kırmızı", "Yeşil", "Mavi", "Sarı");

foreach ($renkler as &$renk) {
  if ($renk == "Mavi") $renk = "Pembe";
}

var_dump($renkler);
```

####  Alternatif Syntax
---
`foreach` döngüsü sözdizimi, `endforeach` deyimi ile aşağıdaki gibi de yazılabilir:

```PHP title:'Alternatif Syntax' hl:3,5
$renkler = array("Kırmızı", "Yeşil", "Mavi", "Sarı");

foreach ($renkler as $renk) :
  echo "$renk <br>";
endforeach;
```

