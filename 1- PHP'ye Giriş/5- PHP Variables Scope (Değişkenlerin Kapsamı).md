Programlama dillerinde *scope* (kapsam), bir değişkenin veya fonksiyonun erişilebilir olduğu alandır. Bir değişkenin veya fonksiyonun kapsamı, tanımlandığı yer ve tanımlandıktan sonra açık olan bloklar tarafından belirlenir.

PHP'nin üç farklı değişken kapsamı vardır:

- local (Yerel)
- global (Genel)
- static (Statik)

### Yerel ve Genel Kapsamlar
---
Bir işlevin **dışında** bildirilen bir değişken genel kapsama sahiptir ve yalnızca bir işlevin dışından erişilebilir:

```PHP title:"Genel kapsama sahip bir değişken" hl:1 error:3-6
$x = 5; // Genel kapsam

function myTest() {
  // Bu fonksiyonun içinde x değişkenini kullanmak bir hata oluşturacaktır!
  echo "<p>Fonksiyon içindeki x değişkeni: $x</p>";
}

myTest();

echo "<p>Fonksiyon dışındaki x değişkeni: $x</p>";
```

Bir işlev **içinde** bildirilen bir değişken yerel kapsama sahiptir ve yalnızca o işlev içinde erişilebilir:

```PHP title:"Yerel kapsama sahip bir değişken" hl:2 error:8
function myTest() {
  $x = 5; // Yerel kapsam
  echo "<p>Fonksiyon içindeki x değişkeni: $x</p>";
}
myTest();

// x değişkeninin fonksiyon dışında kullanılması hata oluşturur!
echo "<p>Fonksiyon dışındaki x değişkeni: $x</p>";
```

**Not:** Farklı fonksiyonlarda aynı isimde yerel değişkenlere sahip olabilirsiniz, çünkü yerel değişkenler yalnızca bildirildikleri fonksiyon tarafından tanınır.

### PHP'de `global` Anahtar Sözcüğü
---
`global` anahtar sözcüğü, bir fonksiyon içinden global (genel) bir değişkene erişmek için kullanılır.

Bunu yapmak için, değişkenlerden önce (fonksiyonun içinde) `global` anahtar sözcüğünü kullanın:

```PHP title:"global anahtar sözcüğü" hl:5 
$x = 5;
$y = 10;

function myTest() {
  global $x, $y; // x = 5, y = 10 (global değişkenler alındı)
  $y = $x + $y;
}

myTest();

echo $y; // Çıktı: 15
```

PHP ayrıca tüm global (genel) değişkenleri `$GLOBALS[index]` adlı bir dizide saklar. Dizin değişkenin adını tutar. Bu diziye işlevler içinden de erişilebilir ve küresel değişkenleri doğrudan güncellemek için kullanılabilir.

Yukarıdaki örnek şu şekilde yeniden yazılabilir:

```PHP title:"$GLOBALS dizisi" hl:5
$x = 5;
$y = 10;

function myTest() {
  $GLOBALS['y'] = $GLOBALS['x'] + $GLOBALS['y']; // x = 5, y = 10
}

myTest();

echo $y; // Çıktı: 15
```

### PHP'de `static` Anahtar Sözcüğü
---
Normalde, bir fonksiyon tamamlandığında/çalıştırıldığında, tüm değişkenler bellekten silinir. Ancak bazen bir yerel değişkenin silinmemesini isteriz. Başka bir iş için ona ihtiyacımız vardır.

Bunu yapmak için, değişkeni ilk bildirdiğinizde `static` anahtar sözcüğünü kullanın:

```PHP title:"static anahtar sözcüğü" hl:2
function myTest() {
  static $x = 0;
  echo $x;
  $x++;
}

myTest(); // Çıktı: 0
myTest(); // Çıktı: 1
myTest(); // Çıktı: 2

```

Daha sonra, fonksiyon her **çağrıldığında**, bu değişken fonksiyonun son çağrıldığı zamandan itibaren içerdiği bilgilere sahip olmaya devam edecektir.

**Not:** Değişken hala fonksiyon için local'dir (yereldir).