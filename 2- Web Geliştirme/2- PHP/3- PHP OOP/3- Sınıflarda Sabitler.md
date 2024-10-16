Bir sınıf içinde bazı sabit veriler tanımlamanız gerekiyorsa, sınıf sabitleri yararlı olabilir.

Bir sınıf sabiti, `const` anahtar sözcüğü ile bir sınıfın içinde bildirilir.

Bir sabit bildirildikten sonra değiştirilemez.

Sınıf sabitleri büyük/küçük harfe duyarlıdır. Ancak, sabitlerin tümünün büyük harflerle adlandırılması önerilir.

Bir sabite sınıf dışından erişmek için sınıf adını ve ardından kapsam çözümleme operatörünü (::) ve burada olduğu gibi sabit adını kullanabiliriz:

```PHP title:'Sınıflarda sabitler'
<?php
class Mesajlar {
  const AYRILMA_MESAJI = "Sayfamıza uğradığınız için teşekkürler :)";
}

echo Mesajlar::AYRILMA_MESAJI;
?>
```

Ya da `self` anahtar sözcüğünü ve ardından kapsam çözümleme operatörünü (`::`) ve ardından sabit adını kullanarak sınıfın içinden bir sabite erişebiliriz, burada olduğu gibi:

```PHP title:'self Anahtar Sözcüğü'
class Mesajlar {
  const AYRILMA_MESAJI = "Sayfamıza uğradığınız için teşekkürler";
  
  public function ayrilma() {
    echo self::AYRILMA_MESAJI;
  }
}

$gorusuruz = new Mesajlar();
$gorusuruz->ayrilma();
?>
```