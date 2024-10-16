Bir iterable, `foreach()` döngüsü ile içinden geçilebilen herhangi bir değerdir.

`iterable` sözde türü PHP 7.1'de tanıtılmıştır ve işlev değiştirgeleri ve işlev dönüş değerleri için bir veri türü olarak kullanılabilir.

### Iterables'in Kullanımı
---
`iterable` anahtar sözcüğü, bir fonksiyon parametresi veya bir fonksiyonun dönüş türü olarak kullanılabilir:

```PHP title:'Fonksiyon parametresi olan iterable'
<?php
function iterableYazdir(iterable $iterableDeger) {
  foreach($iterableDeger as $oge) {
    echo $oge;
  }
}

$dizi = ["a", "b", "c"];
iterableYazdir($oge);
?>
```

```PHP title:'iterable döndüren fonksiyon'
<?php
function iterableDondur():iterable {
  return ["a", "b", "c"];
}

$iterableDeger = iterableDondur();

foreach($iterableDeger as $oge) {
  echo $oge;
}
?>
```

### Iterable Oluşturma
---

#### Diziler

Tüm diziler iterabledir, bu nedenle herhangi bir dizi iterable gerektiren bir fonksiyonun değişkeni olarak kullanılabilir.
#### Iterables

`Iterator` arayüzünü uygulayan herhangi bir nesne, bir iterable gerektiren bir fonksiyonun parametresi olarak kullanılabilir.

Bir iterable, öğelerin bir listesini içerir ve bunlar arasında döngü için yöntemler sağlar. Listedeki öğelerden birine bir işaretçi tutar. Listedeki her öğe, öğeyi bulmak için kullanılabilecek bir anahtara sahip olmalıdır.

Bir iterator bu metotlara sahip olmalıdır:

- `current()`: İşaretçinin o anda işaret ettiği öğeyi döndürür. Herhangi bir veri türü olabilir
- `key()`: Listedeki geçerli öğeyle ilişkili anahtarı döndürür. Yalnızca bir tamsayı, float, boolean veya string olabilir
- `next()`: İşaretçiyi listedeki bir sonraki öğeye taşır
- `rewind()`: İşaretçiyi listedeki ilk öğeye taşır
- `valid()`: Dahili işaretçi herhangi bir öğeyi göstermiyorsa (örneğin, `next()` listenin sonunda çağrılmışsa), bu `false` döndürmelidir. Diğer tüm durumlarda `true` döndürür

```PHP title:'Bir iterator sınfı'
<?php
// Iterator Sınıfımızı Oluşturuyoruz
class IteratorSinif implements Iterator {
  private $ogeler = [];
  private $isaretci = 0;

  public function __construct($ogeler) {
    // array_values() fonksiyonu dizinin numerik dizi olduğunu doğrular
    $this->ogeler = array_values($ogeler);
  }

  public function current() {
    return $this->ogeler[$this->isaretci];
  }

  public function key() {
    return $this->isaretci;
  }

  public function next() {
    $this->isaretci++;
  }

  public function rewind() {
    $this->isaretci = 0;
  }

  public function valid() {
    // count() fonkisyonu dizide bulunun öğelerin sayısını döndürür
    return $this->isaretci < count($this->ogeler);
  }
}

// Iterator kullanan bir fonksiyon
function iterableYazdir(iterable $iterableDeger) {
  foreach($iterableDeger as $oge) {
    echo $oge;
  }
}

// Iteratoru iterable fonksiyon ile kullanmak
$iterator = new IteratorSinif(["a", "b", "c"]);
iterableYazdir($iterator);
?>
```