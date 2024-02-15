PHP yalnızca tekli kalıtımı destekler: bir alt sınıf yalnızca tek bir üst sınıftan kalıtım alabilir.

Peki, bir sınıfın birden fazla davranışı miras alması gerekiyorsa ne olacak? `trait` bu sorunu çözer.

`trait`, birden fazla sınıfta kullanılabilecek metotları bildirmek için kullanılır. Nitelikler birden fazla sınıfta kullanılabilen yöntemleri ve soyut yöntemleri olabilir ve yöntemler herhangi bir erişim belirleyicisine (`public`, `private` veya `protected`) sahip olabilir.

Nitelikler `trait` anahtar sözcüğü ile bildirilir:

```PHP title:'trait Syntax'
<?php
trait OzellikIsmi{
  // Kodlar...
}
?>
```

Bir sınıfta bir trait kullanmak için `use` anahtar sözcüğünü kullanın:

```PHP title:'use anahtar sözcüğü' hl:3
<?php
class SinifIsmi {
  use OzellikIsmi;
}
?>
```

Bir örneğe bakalım:

```PHP title:"Ufak bir trait örneği" hl:2-6,9
<?php
trait mesajOzelligi {
public function mesaj1() {
    echo "OOP çok eğlenceli! ";
  }
}

class Sinif {
  use mesajOzelligi;
}

$nesne = new Sinif();
$nesne->mesaj1();
?>
```

Burada, bir nitelik bildiriyoruz: `mesajOzelligi`. Ardından, bir sınıf oluşturuyoruz: `Sinif` bu niteliği kullanır ve nitelikteki tüm metotlar sınıfta kullanılabilir.

Başka sınıfların `mesaj1()` metotunu kullanması gerekiyorsa, bu sınıflarda `mesajOzelligi` niteliğini kullanmanız yeterlidir. Bu, kod tekrarını azaltır, çünkü aynı yöntemi tekrar tekrar bildirmeye gerek yoktur.

### Birden Fazla Nitelik Kullanma
---
Başka bir örneğe bakalım:

```PHP title:'Çoklu trait kullanımı' hl:2-6,8-12,15,19
<?php
trait mesajOzelligi1{
  public function mesaj1() {
    echo "OOP çok eğlenceli! ";
  }
}

trait mesajOzelligi2 {
  public function mesaj2() {
    echo "OOP kendimi tekrar etmememi sağlıyor!";
  }
}

class Sinif1 {
  use mesajOzelligi1;
}

class Sinif2 {
  use mesajOzelligi1, mesajOzelligi2;
}

$nesne1 = new Sinif1();
$nesne1->mesaj1();
echo "<br>";

$nesne2 = new Sinif2();
$nesne2->mesaj1();
$nesne2->mesaj2();
?>
```

Burada iki nitelik bildiriyoruz: `mesajOzelligi1` ve `mesajOzelligi2`. Ardından iki sınıf oluşturuyoruz: `Sinif1` ve `Sinif2`. İlk sınıf (`Sinif1`) `mesajOzelligi1` niteliği kullanır ve ikinci sınıf (`Sinif2`) hem `mesajOzelligi1` hem de `mesajOzelligi2` niteliklerini kullanır (birden fazla nitelik virgülle ayrılır).

