Bir constructor (yapıcı), nesne oluşturulduktan sonra nesnenin özelliklerini başlatmanıza olanak tanır.

Bir `__construct()` fonksiyonu oluşturursanız, bir sınıftan bir nesne oluşturduğunuzda PHP bu işlevi otomatik olarak çağıracaktır.

Yapıcı fonksiyonunun iki alt çizgi (`__`) ile başladığına dikkat edin!

Aşağıdaki örnekte, bir kurucu kullanmanın bizi `set_name()` yöntemini çağırmaktan kurtardığını ve kod miktarını azalttığını görüyoruz:

```PHP title:'Constructor (Yapıcı) Metotlar' hl:6-8
<?php
class Meyve {
  public $isim;
  public $renk;

  function __construct($isim) {
    $this->isim = $isim;
    $this->renk = $renk;
  }
  function get_name() {
    return $this->isim;
  }
  function get_color() {
    return $this->renk;
  }
}

$elma = new Fruit("Elma","Kırmızı");

echo $elma->get_name();
echo "<br>";
echo $elma->get_color();
?>
```

