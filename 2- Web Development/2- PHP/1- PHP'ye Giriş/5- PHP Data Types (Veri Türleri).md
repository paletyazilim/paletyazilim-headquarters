Değişkenler, farklı türlerde veriyi saklayabilir ve farklı veri türleri farklı şeyler yapabilir. PHP aşağıdaki veri türlerini destekler:

| Veri Türü | Açıklama                                                                    | Örnek                     |
| --------- |:--------------------------------------------------------------------------- | ------------------------- |
| String    | Metin karakter dizilerini temsil eder.                                      | Merhaba Dünya!            |
| Integer   | Tam sayıları (pozitif, negatif veya sıfır) temsil eder.                     | 10, -25, 0                |
| Float     | Ondalıklı sayıları temsil eder.                                             | 3.14, 9.81, -1.25         |
| Boolean   | DOĞRU (true) veya YANLIŞ (false) değerlerini temsil eder.                   | true, false               |
| Array     | Birden fazla değeri sıralı bir şekilde tutabilir.                           | ["elma", "armut", "muz"]  |
| Object    | Karmaşık veri yapıları oluşturmak için kullanılır.                          | new Kullanici("Bard", 25) |
| NULL      | Hiçbir değer içermediğini belirtir.                                         | null                      |
| Resource  | Harici kaynaklara (örneğin, veri dosyaları, ağ bağlantıları) erişim sağlar. | fopen("dosya.txt", "r")   |

### Veri Türünü Öğrenme
---
`var_dump()` fonksiyonunu kullanarak herhangi bir nesnenin veri türünü elde edebilirsiniz.

```PHP hl:2
$x = 5;
var_dump($x); // Çıkıt: int(5)
```

### Dizeler
---
Dizi, harflerin sıralanmış halidir, tıpkı "Merhaba dünya!" gibi.

Bir dizi, tırnak işaretleri içindeki herhangi bir metin olabilir. Tekli veya çift tırnak kullanabilirsiniz:

```PHP
$x = "Merhaba dünya!";
$y = 'Merhaba dünya!';

var_dump($x);
echo "<br>";
var_dump($y);
```

### Tam Sayılar
---
Tam sayı veri türü, -2.147.483.648 ile 2.147.483.647 arasında olan bir ondalık olmayan sayıdır. Tam sayının belli başlı kuralları vardır bunlar:

- Tam sayının en az bir basamağı olmalıdır.
- Tam sayının ondalık noktaşı olmamalıdır.
- Tam sayı pozitif veya negatif olabilir.
- Tam sayılar ondalık (taban 10), onaltılı (taban 16), sekizli (taban 8) veya ikili (taban 2) sayı sistemlerinde belirtilebilir.

```PHP
$x = 5985;
var_dump($x);
```

### Ondalıklı Sayılar
---
Float, ondalık noktası olan bir sayı veya üslü formdaki bir sayıdır.

```PHP
$x = 10.365;
var_dump($x);
```

### Boolean
---
Bir Boolean iki olası durumu temsil eder: DOĞRU veya YANLIŞ.

```PHP
$x = true;
var_dump($x);
```

Boolean'lar genellikle koşullu ifadeler de kullanılır.

### Diziler
---
Bir dizi, birden fazla değeri tek bir değişkende depolar.

```PHP
$arabalar = array("Volvo","BMW","Toyota");
var_dump($arabalar);
```

### Nesneler
---
Sınıflar ve nesneler, nesne yönelimli programlamanın iki ana unsurudur.

Bir sınıf, nesneler için bir şablon ve bir nesne de bir sınıfın örneğidir.

Tek tek nesneler oluşturulduğunda, sınıftan tüm özellikleri ve davranışları miras alırlar, ancak her nesne özellikler için farklı değerlere sahip olacaktır.

Model, renk vb. özelliklere sahip `Araba` adında bir sınıfımız olduğunu varsayalım. Bu özelliklerin değerlerini tutmak için `$model`, `$renk` ve benzeri değişkenler tanımlayabiliriz.

Tek tek nesneler (Volvo, BMW, Toyota, vb.) oluşturulduğunda, sınıftan tüm özellikleri ve davranışları miras alırlar, ancak her nesne özellikler için farklı değerlere sahip olacaktır.

Bir `__construct()` fonksiyonu oluşturursanız, bir sınıftan bir nesne oluşturduğunuzda PHP bu işlevi otomatik olarak çağıracaktır.

```PHP
class Araba {
  public $renk;
  public $model;
  public function __construct($renk, $model) {
    $this->renk = $renk;
    $this->model = $model;
  }
  public function message() {
    return "Benim arabam " . $this->color . " bir " . $this->model . "!";
  }
}

$arabam = new Araba("kırmızı", "Volvo");
var_dump($arabam);
```

### NULL
---
Null, yalnızca bir değere sahip olabilen özel bir veri türüdür: `null`.

NULL veri türündeki bir değişken, kendisine hiçbir değer atanmamış bir değişkendir.

**İpucu:** Bir değişken değer atanmadan oluşturulursa, ona otomatik olarak NULL değeri atanır.

Değişkenler, değer NULL olarak atanarak da boşaltılabilir:

```PHP
$x = "Merhaba Dünya!";
$x = null;
var_dump($x);
```

### Resource
---
Resource veri türü gerçek bir veri türü değildir. PHP dışındaki işlevlere ve kaynaklara bir referansın saklanmasıdır.

Kaynak veri türünü kullanmanın yaygın bir örneği bir veritabanına referans vermektir.

İlerleyen derslerde Resource veri türüne değineceğiz.

### Veri Türünü Değişme
---
Bir değişkene bir tamsayı değeri atarsanız, tür otomatik olarak bir tamsayı olacaktır.

Aynı değişkene bir dize atarsanız, tür bir dize olarak değişecektir:

```PHP
$x = 5;
var_dump($x);

$x = "Merhaba";
var_dump($x);
```


Veri türünü değiştirmek, ancak değeri değiştirmemek istiyorsanız, dönüştürmeyi kullanabilirsiniz. Dönüştürme, değişkenler üzerinde veri türünü değiştirmenize izin verir:

```PHP hl:2
$x = 5;
$x = (string) $x;
var_dump($x);
```