OOP'de Miras = Bir sınıfın başka bir sınıftan türetilmesi.

Çocuk sınıf, ana sınıftaki tüm genel ve korumalı özellikleri ve yöntemleri devralacaktır. Ayrıca, kendi özelliklerine ve yöntemlerine de sahip olabilir.

Miras alınan bir sınıf `extends` anahtar sözcüğü kullanılarak tanımlanır.

Bir örneğe bakalım:

```PHP title:'Meyve sınıfından miras alan Elma sınıfı' hl:16-20,23-24
<?php
class Meyve {
  public $isim;
  public $renk;
  
  public function __construct($isim, $renk) {
    $this->isim = $isim;
    $this->renk = $renk;
  }
  public function ozellikler() {
    echo "Meyve İsmi: {$this->isim}</br>Renk: {$this->renk}";
  }
}

// Meyve sınıfımızın özelliklerini ve metotlarını alıyor.
class Elma extends Meyve {
  public function mesaj() {
    echo "Ben bir elmayım!";
  }
}

$yesilElma = new Elma("Elma", "Yeşil");
$yesilElma->ozellikler();
$yesilElma->mesaj();
?>
```

Elma sınıfı, Meyve sınıfından miras alınmıştır.

Bu, Elma sınıfının kalıtım nedeniyle Meyve sınıfının public `$isim` ve `$renk` özelliklerinin yanı sıra `public __construct()` ve `ozellikler()` metotlarını kullanabileceği anlamına gelir.

Elma sınıfının da kendi metotu vardır: `mesaj().`

### Kalıtım ve Korumalı Erişim Belirleyicisi
---
Bir önceki bölümde, korunan özelliklere veya metotlaraa sınıf içinde ve bu sınıftan türetilen sınıflar tarafından erişilebileceğini öğrendik. Bu ne anlama geliyor?

Bir örneğe bakalım:

```PHP title:'Miras alınan sınıflarda protected erişim belirleyicisi' hl:10-12 error:23
<?php
class Meyve {
  public $isim;
  public $renk;
  
  public function __construct($isim, $renk) {
    $this->isim = $isim;
    $this->renk = $renk;
  }
  protected function ozellikler() {
    echo "Meyve İsmi: {$this->isim}</br>Renk: {$this->renk}";
  }
}

// Meyve sınıfımızın özelliklerini ve metotlarını alıyor.
class Elma extends Meyve {
  public function mesaj() {
    echo "Ben bir elmayım!";
  }
}

$yesilElma = new Elma("Elma", "Yeşil"); 
$yesilElma->ozellikler(); // HATA
$yesilElma->mesaj();
?>
```

Yukarıdaki örnekte, sınıf dışından `protected` bir metotu (`ozellikler()`) çağırmaya çalışırsak hata alacağımızı görüyoruz. `public` metotlar sorunsuz çalışacaktır!

Başka bir örneğe bakalım:

```PHP title:'Miras alınan sınıflarda protected erişim belirleyicisi' hl:16-20
<?php
class Meyve {
  public $isim;
  public $renk;
  
  public function __construct($isim, $renk) {
    $this->isim = $isim;
    $this->renk = $renk;
  }
  protected function ozellikler() {
    echo "Meyve İsmi: {$this->isim}</br>Renk: {$this->renk}";
  }
}

// Meyve sınıfımızın özelliklerini ve metotlarını alıyor.
class Elma extends Meyve {
  public function mesaj() {
    $this->ozellikler();
    echo "Ben bir elmayım!";
  }
}

$yesilElma = new Elma("Elma", "Yeşil"); 
$yesilElma->mesaj();
?>
```

Yukarıdaki örnekte sorunsuz çalıştığını görüyoruz! Bunun nedeni, `protected` metotu (`ozellikler()`) türetilmiş sınıfın (Elma) içinden çağırmamızdır.

### Miras Alınan Metotları Geçersiz Kılma
---
Miras alınan metotlar, alt sınıfta metotlar yeniden tanımlanarak (aynı isim kullanılarak) geçersiz kılınabilir.

Aşağıdaki örneğe bakın. Alt sınıftaki (Elma) `__construct()` ve `ozellikler()` metotları, üst sınıftaki (Meyve) `__construct()` ve `ozellikler()` metotlarını geçersiz kılacaktır:

```PHP title:'Miras alınmış metotları yeniden oluşturma'
<?php
class Meyve {
  public $isim;
  public $renk;
  
  public function __construct($isim, $renk) {
    $this->isim = $isim;
    $this->renk = $renk;
  }
  public function ozellikler() {
    echo "Meyve İsmi: {$this->isim}</br>Renk: {$this->renk}";
  }
}

class Elma extends Meyve {
  public $agirlik;
  
  public function __construct($isim,$renk,$agirlik) {
    $this->isim = $isim;
    $this->renk = $renk;
    $this->agirlik = $agirlik;
  }
  public function ozellikler() {
    echo "Meyve İsmi: {$this->isim}</br>Renk: {$this->renk}</br>Ağırlık: {$this->agirlik}";
  }
}

$yesilElma = new Elma("Elma", "Yeşil", 100);
$yesilElma->ozellikler();
?>
```

### `final` - Anahtar Sözcüğü
---
`final` anahtar sözcüğü, sınıf kalıtımını veya metotları geçersiz kılmayı önlemek için kullanılabilir.

Aşağıdaki örnekte sınıf kalıtımının nasıl önleneceği gösterilmektedir:

```PHP title:'Sınıfı miras almayı önlemek' hl:2-4, error:7-9
<?php
final class Meyve {
  // Özellikler ve metotlar...
}

// HATA
class Elma extends Meyve {
  // Özellikler ve metotlar
}
?>
```

Aşağıdaki örnekte, metotu geçersiz kılınmasının nasıl önleneceği gösterilmektedir:

```PHP title:'Metotu geçersiz kılmayı önlemek' hl:3-5 error:10-12
<?php
class Meyve {
  final public function ozellikler() {
    // Kodlar...
  }
}

class Elma extends Meyve {
  // HATA
  public function ozellikler() {
    // Kodlar...
  }
}
?>
```