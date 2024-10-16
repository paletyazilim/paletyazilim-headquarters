Nesne yok edildiğinde veya kod durdurulduğunda ya da koddan çıkıldığında bir yıkıcı metot çağrılır.

Eğer bir `__destruct()` metotu oluşturursanız, PHP betiğin sonunda bu işlevi otomatik olarak çağıracaktır.

Destruct işlevinin iki alt çizgi (`__`) ile başladığına dikkat edin!

Aşağıdaki örnekte, bir sınıftan bir nesne oluşturduğunuzda otomatik olarak çağrılan bir `__construct()` metotu ve kodun sonunda otomatik olarak çağrılan bir `__destruct()` metotu vardır:

```PHP title:'Destructor (Yıkıcı) Metotlar' hl:10-12
<?php
class Meyve {
  public $isim;
  public $renk;

  function __construct($isim) {
    $this->isim = $isim;
    $this->renk = $renk;
  }
  function __destruct() {
    echo "{$this->name} meyvesi {$this->color} renktedir."
  }
}

$elma = new Fruit("Elma","Kırmızı");
?>
```