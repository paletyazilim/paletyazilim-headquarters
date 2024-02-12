Soyut sınıflar ve metotlar, ana sınıfın adlandırılmış bir metota sahip olduğu, ancak görevleri yerine getirmek için alt sınıf(lar)a ihtiyaç duyduğu durumlardır.

Soyut sınıf, en az bir soyut metot içeren bir sınıftır. Soyut bir metot, bildirilen ancak kodda uygulanmayan bir yöntemdir.

Soyut bir sınıf veya metot `abstract` anahtar sözcüğü ile tanımlanır:

```PHP title:'abstract Syntax'
<?php
abstract class AnaSinif {
  abstract public function method1();
  abstract public function method2($isim, $renk);
  abstract public function method3() : string;
}
?>
```

Soyut bir sınıftan miras alınırken, alt sınıf metotu aynı adla ve aynı veya daha az kısıtlı bir erişim belirleyici tanımlanmalıdır. Yani, soyut metod `protected` olarak tanımlanmışsa, alt sınıf metotu `protected` veya `public` olarak tanımlanmalıdır, ancak `private` olarak tanımlanmamalıdır. Ayrıca, gerekli parametrelerin türü ve sayısı da aynı olmalıdır. Ancak, alt sınıflar ek olarak isteğe bağlı parametrelere sahip olabilir.

Dolayısıyla, bir çocuk sınıf soyut bir sınıftan miras alındığında, aşağıdaki kurallara sahip oluruz:

- Alt sınıf metotu aynı adla tanımlanmalıdır ve üst soyut metotu yeniden bildirir
- Alt sınıf metotu aynı veya daha az kısıtlı bir erişim belirleyicisi ile tanımlanmalıdır
- Gerekli parametrelerin sayısı aynı olmalıdır. Ancak, alt sınıf ek olarak isteğe bağlı bağımsız parametrelere sahip olabilir

Bir örneğe bakalım:

```PHP title:'Soyut sınıflara örnek'
<?php
// Ana Sınıf
abstract class Araba {
  public $isim;
  public function __construct($isim) {
    $this->isim = $isim;
  }
  abstract public function mesaj() : string;
}

// Çocuk Sınıflar
class Audi extends Araba {
  public function mesaj() : string {
    return "Alman mühendisliği harikası $this->isim!";
  }
}

class Volvo extends Araba {
  public function mesaj() : string {
    return "İsveç çeliğini güvenlikle birleştiren $this->isim!";
  }
}

class Lamborghini extends Araba {
  public function mesaj() : string {
    return "Bir boğa kadar güçlü $this->isim!";
  }
}

// Çocuk sınıflardan nesneler tanımlayalım
$audi = new Audi("Audi");
echo $audi->mesaj();
echo "<br>";

$volvo = new Volvo("Volvo");
echo $volvo->mesaj();
echo "<br>";

$lamborghini = new Lamborghini("Lamborghini");
echo $lamborghini->mesaj();
?>
```

Audi, Volvo ve Lamborghini sınıfları Araba sınıfından miras alınmıştır. Bu, Audi, Volvo ve Lamborghini sınıflarının kalıtım nedeniyle Araba sınıfının public `__construct()` metotunun yanı sıra public `$isim` özelliğini de kullanabileceği anlamına gelir.

Ancak, `mesaj()` tüm alt sınıflarda tanımlanması gereken soyut bir metottur ve bir dize döndürmelidir.

Soyut yöntemin bir parametre sahip olduğu ve alt sınıfın ebeveynin soyut metotunda tanımlanmayan iki isteğe bağlı parametreye sahip olduğu başka bir örneğe bakalım:

```PHP title:'Parametreli soyut metotlar'
<?php
abstract class AnaSinif {
  // Soyut metotumuz bir adet parametreye sahip
  abstract protected function mesaj($isim);
}

class CocukSinif extends AnaSinif {
  // Çocuk sınıfta ise metotumuz opsiyonel 2 adet parametreye sahip
  public function mesaj($isim, $karsilama = "Sayın", $cinsiyet = null) {
    if($cinsiyet == "e") {
      $hitap = "Bey";
    }
    elseif($cinsiyet == "k")
    {
      $hitap = "Hanım";
    }
    else
    {
      $hitap = null;
    }
    return "{$karsilama} {$isim} {$hitap}";
  }
}

$isim = new CocukSinif;
echo $isim->mesaj("Berat MASAT", "Bey", "Sayın");
?>
```

