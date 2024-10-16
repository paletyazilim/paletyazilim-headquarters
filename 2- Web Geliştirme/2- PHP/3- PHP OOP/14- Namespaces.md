Namespaces iki farklı sorunu çözen niteleyicilerdir:

- Bir görevi yerine getirmek için birlikte çalışan sınıfları gruplandırarak daha iyi bir organizasyon sağlarlar.
- Aynı ismin birden fazla sınıf için kullanılmasına izin verirler.

Örneğin, bir HTML tablosunu tanımlayan Table; Row ve Cell gibi bir dizi sınıfa sahipken, mobilyaları tanımlayan Table; Chair ve Bed gibi başka bir dizi sınıfınız olabilir. Ad alanları, sınıfları iki farklı grup halinde düzenlemek ve aynı zamanda Table ve Table sınıflarının birbirine karışmasını önlemek için kullanılabilir.

Namepsace bir dosyanın başında `namespace` anahtar sözcüğü kullanılarak bildirilir:

```PHP title:'namespace Syntax'
<?php
namespace Html;
?>
```

Daha fazla organizasyon için, iç içe isim alanlarına sahip olmak mümkündür:

```PHP title:'Nested namespace Syntax'
<?php
namespace Code\Html;
?>
```

**Not:** Bir `namespace` bildirimi PHP dosyasındaki ilk şey olmalıdır. Aşağıdaki kod geçersiz olacaktır:

```PHP title:'Hatalı bir namespace bildirme' error:1-5
<?php
echo "Merhaba Dünya!";
namespace Html;
//...
?>
```

### Namespace Bildirme
---
Bu dosyada bildirilen sabitler, sınıflar ve fonksiyonlar `Html` namespace'ine ait olacaktır:

```PHP title:"namespace'te Table adında sınıf oluşturma" hl:2
<?php
namespace Html;

class Table {
  public $baslik = "";
  public $satirSayisi = 0;
  public function mesaj() {
    echo "<p>'{$this->satirSayisi}' başlıklı tablo {$this->satirSayisi} satıra sahip.</p>";
  }
}

$table = new Table();
$table->baslik = "Örnek Tablo";
$table->satirSayisi = 10;
?>

<!DOCTYPE html>
<html>
<body>

<?php
$table->mesaj();
?>

</body>
</html>
```

### Namespace'leri Kullanma
---
Bir `namespace` bildirimini takip eden tüm kodlar namespace içinde çalışır, bu nedenle namespace ait sınıflar herhangi bir niteleyici olmadan örneklenebilir. Sınıflara bir namespace dışından erişmek için, sınıfın isim alanının kendisine eklenmiş olması gerekir.

```PHP title:"Html namespace'inde ki sınıflara erişim" hl:2
<?php
$table = new Html\Table();
?>
```

Aynı namespace'den birçok sınıf aynı anda kullanılacağında, `namespace` anahtar sözcüğünü kullanmak daha kolaydır:

```PHP title:'Html\(sinif) olmadan hızlıca Html sınıflarını kullanma' hl:2
<?php
namespace Html;

$table = new Table();
?>
```

### Namespacelere Takma Ad
---
Yazmayı kolaylaştırmak için bir namespace veya sınıfa takma ad vermek yararlı olabilir. Bu `use` anahtar sözcüğü ile yapılır:

```PHP title:"namespace'e takma ad vermek"
<?php
use Html as H;

$table = new H\Table();
?>
```

```PHP title:"namespace sınıfına takma ad vermek"
<?php
use Html\Table as T;

$table = new T();
?>
```


