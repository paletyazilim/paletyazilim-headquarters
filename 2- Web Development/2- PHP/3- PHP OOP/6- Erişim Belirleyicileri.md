Özellikler ve metotlar, bunlara nereden erişilebileceğini kontrol eden erişim belirleyicilere sahip olabilir.

Üç erişim belirleyicisi vardır:

- **public:** Özelliğe veya metota her yerden erişilebilir. (Varsayılan)
- **protected:** Özelliğe veya metota sınıf içinde ve bu sınıftan türetilen sınıflar tarafından erişilebilir
- **private:** Özelliğe veya metota SADECE sınıf içinden erişilebilir

Aşağıdaki örnekte üç özelliğe (`$isim`, `$renk` ve `$agirlik`) üç farklı erişim belirleyicisi ekledik. Burada, `$isim` özelliğini ayarlamaya çalışırsanız sorun olmayacaktır (çünkü `$isim` özelliği herkese açıktır ve her yerden erişilebilir). Ancak, `$renk` veya `$agirlik` özelliğini ayarlamaya çalışırsanız ölümcül bir hatayla sonuçlanacaktır (çünkü `$renk` ve `$agirlik` özellikleri korumalı ve özeldir):

```PHP title:'Eşirim belirleyicisi olan özellikler' hl:4-5, error:10-11
<?php
class Meyve {
  public $isim;
  protected $renk;
  private $agirlik;
}

$mango = new Meyve();
$mango->isim = 'Mango';
$mango->renk = 'Sarı'; // HATA
$mango->agirlik = '300'; // HATA
?>
```

Bir sonraki örnekte iki metota erişim belirleyici ekledik. Burada, `set_color()` veya `set_weight()` metotunu çağırmaya çalışırsanız, tüm özellikler public olsa bile ölümcül bir hatayla sonuçlanacaktır (çünkü bu iki fonksiyon `protected` ve `private` olarak kabul edilir):

```PHP title:'Erişim belirleyicisi olan metotlar' hl:10-15 error:20-21
<?php
class Meyve {
  public $isim;
  public $renk;
  public $agirlik;

  function set_name($isim) {
    $this->isim = $isim;
  }
  protected function set_color($renk) {
    $this->renk = $renk;
  }
  private function set_weight($agirlik) {
    $this->agirlik = $agirlik;
  }
}

$mango = new Meyve();
$mango->set_name('Mango');
$mango->set_color('Sarı'); // HATA
$mango->set_weight('300'); // HATA
?>
```

