Statik metotlar, sınıfın bir örneğini oluşturmadan doğrudan çağrılabilir.

Statik metotlar `static` anahtar sözcüğü ile bildirilir:

```PHP title:'Static Method syntax'
<?php
class Sinif {
  public static function statikMetot() {
    echo "Merhaba Dünya!";
  }
}
?>
```

Statik bir metota erişmek için sınıf adını, çift iki nokta üst üste (`::`) ve metot adını kullanın:

```PHP title:'Static Method Access Syntax'
Sinif::statikMetot();
```

Bir örneğe bakalım:

```PHP title:'Statik metotlara ufak bir örnek' hl:4-6,10
<?php
class OrnekSinif {
  // Statik Metotu Oluşturma
  public static function merhaba() {
    echo "Merhaba Dünya!";
  }
}

// Statik Metotu Çağırma
OrnekSinif::merhaba();
?>
```

Burada, statik bir metot bildiriyoruz: `merhaba()`. Ardından, sınıf adını, çift iki nokta üst üste (`::`) ve metot adını kullanarak (sınıfın bir örneğini oluşturmadan) statik yöntemi çağırıyoruz.

### Statik Metotlar Hakkında Daha Fazlası
---
Bir sınıf hem statik hem de statik olmayan metotlara sahip olabilir. Statik bir metota aynı sınıftaki bir metottan `self` anahtar sözcüğü ve çift iki nokta üst üste (::) kullanılarak erişilebilir:

```PHP title:'Sınıf içinde statik metota erişim' hl:8
<?php
class OrnekSinif {
  public static function merhaba() {
    echo "Merhaba Dünya!";
  }

  public function __construct() {
    self::merhaba();
  }
}

new OrnekSinif();
?>
```

Statik metotlar diğer sınıflardaki metotlardan da çağrılabilir. Bunu yapmak için, statik metot `public` olmalıdır:

```PHP title:'Başka sınıflardan statik metota erişim' hl:3-5,10
<?php
class A {
  public static function merhaba() {
    echo "Merhaba Dünya!";
  }
}

class B {
  public function mesaj() {
    A::merhaba();
  }
}

$nesne = new B();
echo $nesne -> mesaj();
?>
```

Bir alt sınıftan statik bir metot çağırmak için, alt sınıfın içinde `parent` anahtar sözcüğünü kullanın. Burada, statik yöntem `public` veya `protected` olabilir.

```PHP title:'Alt sınıftan üst sınıftaki statik metota erişme' hl:3-5,11
<?php
class AnaSinif {
  protected static function merhaba() {
    return "Merhaba Dünya!";
  }
}

class AltSinif extends AnaSinif {
  public $mesaj;
  public function __construct() {
    $this->mesaj = parent::merhaba();
  }
}

$nesne = new AltSinif;
echo $nesne -> mesaj;
?>
```