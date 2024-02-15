Statik özellikler, bir sınıf örneği oluşturmadan doğrudan çağrılabilir.

Statik özellikler `static` anahtar sözcüğü ile bildirilir:

```PHP title:'static Properties Syntax'
<?php
class Sinif {
  public static $staticOzellik = "Statik bir özelliğim!";
}
?>
```

Statik bir özelliğe erişmek için sınıf adını, çift iki nokta üst üste (`::`) ve özellik adını kullanın:

```PHP title:'Static Properties Access Syntax'
Sinif::$statikOzellik;
```

Bir örneğe bakalım:

```PHP title:'Statik özelliklere ufak bir örnek'
<?php
class pi {
  public static $deger = 3.14159;
}

// Statik özelliğe erişim
echo pi::$deger;
?>
```

Burada, statik bir özellik bildiriyoruz: `$deger`. Ardından, sınıf adı, çift iki nokta üst üste (`::`) ve özellik adını kullanarak (sınıfın örneğini oluşturmadan) statik özelliğin değerini yazdırıyoruz.

### Statik Özellikler Hakkında Daha Fazlası
---
Bir sınıf hem statik hem de statik olmayan özelliklere sahip olabilir. Statik bir özelliğe aynı sınıftaki bir yöntemden `self` anahtar sözcüğü ve iki nokta üst üste (`::`) kullanılarak erişilebilir:

```PHP title:'Sınıf içinde statik özelliğe erişim'
<?php
class pi {
  public static $deger = 3.14159;

  public function degeriDondur() {
    return self::$deger;
  }
}


$pi = new pi();
echo $pi->degeriDondur();
?>
```

Bir alt sınıftan statik bir özelliği çağırmak için, alt sınıfın içinde `parent` anahtar sözcüğünü kullanın:

```PHP title:'Alt sınıftan üst sınıftaki statik özelliğe erişme'
<?php
class pi {
  public static $deger = 3.14159;
}

class AltSinif extends pi {
  public function piDondur() {
    return parent::$deger;
  }
}

// Statik özelliği doğrudan alt sınıftan alma
echo AltSinif::$deger;

// yada Alt sınıftaki metot aracılığıyla alın
$pi = new AltSinif();
echo $pi->piDondur();
?>
```
