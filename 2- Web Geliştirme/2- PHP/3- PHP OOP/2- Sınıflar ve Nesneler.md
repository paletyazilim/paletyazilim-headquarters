Meyve adında bir sınıfımız olduğunu varsayalım. Bir Meyve isim, renk, ağırlık gibi özelliklere sahip olabilir. Bu özelliklerin değerlerini tutmak için `$isim`, `$renk` ve `$agirlik` gibi değişkenler tanımlayabiliriz.

Tek tek nesneler (elma, muz, vb.) oluşturulduğunda, sınıftan tüm özellikleri ve davranışları miras alırlar, ancak her nesne özellikler için farklı değerlere sahip olacaktır.

### Sınıf Tanımlama
---
Bir sınıf, `class` anahtar sözcüğü ve ardından sınıfın adı ve bir çift küme parantezi (`{}`) kullanılarak tanımlanır. Sınıfın tüm özellikleri ve metotları parantezlerin içinde yer alır:

```PHP title:'Meyve sınıfının tanımlanması' hl:2-4
<?php
class Meyve {
  // Özellikler ve Metodlar...
}
?>
```

Aşağıda, iki özellikten (`$isim` ve `$renk`) ve `$isim` özelliğini ayarlamak ve almak için iki `set_name()` ve `get_name()` metodundan oluşan Meyve adlı bir sınıf bildiriyoruz:

```PHP title:'Sınıf özelliklerinin ve metotlarının tanımlanması' hl:3-5,7-14
<?php
class Fruit {
  // Özellikler
  public $isim;
  public $renk;

  // Metotlar
  function set_name($isim) {
    $this->isim = $isim;
  }
  function get_name() {
    return $this->isim;
  }
}
?>
```

**Not:** Bir sınıfta değişkenlere özellik, fonksiyonlara ise metot denir!

### Nesne Tanımlama
---
Nesneler olmadan sınıflar bir hiçtir! Bir sınıftan birden fazla nesne oluşturabiliriz. Her nesne sınıfta tanımlanan tüm özelliklere ve metotlara sahiptir, ancak farklı özellik değerlerine sahip olacaklardır.

Bir sınıfın nesneleri `new` anahtar sözcüğü kullanılarak oluşturulur.

Aşağıdaki örnekte, $elma ve $muz, Meyve sınıfının nesneleridir:

```PHP title:'Nesne tanımlama' hl:16,17
<?php
class Meyve {
  // Özellikler
  public $isim;
  public $renk;

  // Metotlar
  function set_name($isim) {
    $this->isim = $isim;
  }
  function get_name() {
    return $this->isim;
  }
}

$elma = new Meyve();
$muz = new Meyve();

$elma->set_name('Elma');
$muz->set_name('Muz');

echo $elma->get_name();
echo "<br>";
echo $muz->get_name();
?>
```

Aşağıdaki örnekte, Fruit sınıfına `$color` özelliğini ayarlamak ve almak için iki metot daha ekliyoruz:

```PHP title:'$renk özelliğinin tanımlanması' hl:14-19
<?php
class Meyve {
  // Özellikler
  public $isim;
  public $renk;

  // Metotlar
  function set_name($isim) {
    $this->isim = $isim;
  }
  function get_name() {
    return $this->isim;
  }
  function set_color($renk) {
    $this->renk = $renk;
  }
  function get_color() {
    return $this->renk;
  }
}

$elma = new Meyve();
$elma->set_name('Apple');
$elma->set_color('Red');

echo "İsim: " . $elma->get_name();
echo "<br>";
echo "Renk: " . $elma->get_color();
?>
```

### `$this` - Anahtar Sözcüğü
---
Bu anahtar sözcük geçerli nesneyi ifade eder ve yalnızca metotların içinde kullanılabilir.

Aşağıdaki örneğe bakın:

```PHP
<?php
class Meyve {
  public $isim;
}
$isim = new Meyve();
?>
```

Peki, `$isim` özelliğinin değerini nerede değiştirebiliriz? Bunun iki yolu vardır:

1. Sınıfın içinde (bir `set_name()` metotu ekleyerek ve `$this` kullanarak):
   
```PHP title:'$isim özelliğini metot ile değiştirmek' hl:7-9
<?php
class Meyve {
  //Özellikler
  public $isim;

  // Metotlar
  function set_name($isim) {
    $this->isim = $isim;
  }
}

$elma = new Meyve();
$elma->set_name("Elma");

echo $elma->isim;
?>
```

2. Sınıfın dışında (özellik değerini doğrudan değiştirerek):
   
```PHP title:'$isim özelliğini sınıf dışında değiştirme' hl:7
<?php
class Meyve {
  public $isim;
}

$elma = new Meyve();
$elma->meyve = "Elma";

echo $apple->isim;
?>
```

### `instanceof` - Anahtar Sözcüğü
---
Bir nesnenin belirli bir sınıfa ait olup olmadığını kontrol etmek için `instanceof` anahtar sözcüğünü kullanabilirsiniz:

```PHP title:'Nesnenin Meyve sınıfına ait olduğunu öğrenme'
<?php
$elma = new Meyve();
var_dump($elma instanceof Meyve);
?>
```