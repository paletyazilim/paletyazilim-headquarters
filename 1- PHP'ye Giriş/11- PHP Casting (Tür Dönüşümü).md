Bazen bir değişkeni bir veri türünden diğerine değiştirmeniz gerekir ve bazen de bir değişkenin belirli bir veri türüne sahip olmasını istersiniz. Bu, tür dönüşümü ile yapılabilir.

PHP'de dönüşümler bu ifadelerle yapılır:

- `(string)` - String veri türüne dönüştürür
- `(int)` - Integer veri türüne dönüştürür
- `(float)` - Float veri türüne dönüştürür
- `(bool)` - Boolean veri türüne dönüştürür
- `(array)` - Array veri türüne dönüştürür
- `(object)` - Object veri türüne dönüştürür
- `(unset)` - NULL veri türüne dönüştürür

### String Tür Dönüşümü
---

```PHP
$a = 5;       // Integer
$b = 5.34;    // Float
$c = "merhaba"; // String
$d = true;    // Boolean
$e = NULL;    // NULL

$a = (string) $a;
$b = (string) $b;
$c = (string) $c;
$d = (string) $d;
$e = (string) $e;

var_dump($a);
var_dump($b);
var_dump($c);
var_dump($d);
var_dump($e);
```

### Integer Tür Dönüşümü
---

```PHP
$a = 5;       // Integer
$b = 5.34;    // Float
$c = "25 kilometre"; // String
$d = "kilometre: 25"; // String
$e = "merhaba"; // String
$f = true;    // Boolean
$g = NULL;    // NULL

$a = (int) $a;
$b = (int) $b;
$c = (int) $c;
$d = (int) $d;
$e = (int) $e;
$f = (int) $f;
$g = (int) $g;
```

### Float Tür Dönüşümü
---

```PHP
$a = 5;       // Integer
$b = 5.34;    // Float
$c = "25 kilometre"; // String
$d = "kilometre: 25"; // String
$e = "merhaba"; // String
$f = true;    // Boolean
$g = NULL;    // NULL

$a = (float) $a;
$b = (float) $b;
$c = (float) $c;
$d = (float) $d;
$e = (float) $e;
$f = (float) $f;
$g = (float) $g;
```

### Boolean Tür Dönüşümü
---

>Bir değer 0, NULL, `false` veya boş ise, (bool) değeri `false`'a, aksi takdirde `true`'ya dönüştürür.
>
>-1 bile doğruya dönüşür.

```PHP
$a = 5;       // Integer
$b = 5.34;    // Float
$c = 0;       // Integer
$d = -1;      // Integer
$e = 0.1;     // Float
$f = "merhaba"; // String
$g = "";      // String
$h = true;    // Boolean
$i = NULL;    // NULL

$a = (bool) $a;
$b = (bool) $b;
$c = (bool) $c;
$d = (bool) $d;
$e = (bool) $e;
$f = (bool) $f;
$g = (bool) $g;
$h = (bool) $h;
$i = (bool) $i;
```

### Array Tür Dönüşümü
---

```PHP
$a = 5;       // Integer
$b = 5.34;    // Float
$c = "merhaba"; // String
$d = true;    // Boolean
$e = NULL;    // NULL

$a = (array) $a;
$b = (array) $b;
$c = (array) $c;
$d = (array) $d;
$e = (array) $e;
```

Dizilere dönüştürülürken, çoğu veri türü tek elemanlı indeksli bir diziye dönüştürülür.

NULL değerler boş bir dizi nesnesine dönüştürülür.

Nesneler, özellik adlarının anahtarlar ve özellik değerlerinin değerler olduğu ilişkisel dizilere dönüştürülür:

```PHP
class Araba {
  public $renk;
  public $model;
  public function __construct($renk, $model) {
    $this->renk = $renk;
    $this->model = $model;
  }
  public function mesaj() {
    return "Benim arabam " . $this->renk . " bir " . $this->model . "!";
  }
}

$arabam = new Araba("kırmızı", "Volvo");

$arabam = (array) $arabam;
var_dump($arabam);
```

### Object Tür Dönüşümü
---

```PHP
$a = 5;       // Integer
$b = 5.34;    // Float
$c = "merhaba"; // String
$d = true;    // Boolean
$e = NULL;    // NULL

$a = (object) $a;
$b = (object) $b;
$c = (object) $c;
$d = (object) $d;
$e = (object) $e;
```

Nesnelere dönüştürülürken, çoğu veri türü, karşılık gelen değerle birlikte "skaler" olarak adlandırılan bir özelliğe sahip bir nesneye dönüştürülür.

NULL değerler boş bir nesneye dönüşür.

Dizinli diziler, özellik adı olarak dizin numarası ve özellik değeri olarak değer ile nesnelere dönüştürülür.

İlişkisel diziler, anahtarları özellik adları ve değerleri özellik değerleri olan nesnelere dönüşür.

```PHP
$a = array("Volvo", "BMW", "Toyota");
$b = array("Berat"=>"20", "Metin"=>"31", "Furkan"=>"29");

$a = (object) $a;
$b = (object) $b;
```
### NULL Tür Dönüşümü
---

```PHP
$a = 5;       // Integer
$b = 5.34;    // Float
$c = "merhaba"; // String
$d = true;    // Boolean
$e = NULL;    // NULL

$a = (unset) $a;
$b = (unset) $b;
$c = (unset) $c;
$d = (unset) $d;
$e = (unset) $e;
```