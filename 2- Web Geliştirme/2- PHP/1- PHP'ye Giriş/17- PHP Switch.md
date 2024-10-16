`switch` deyimi, farklı koşullara bağlı olarak farklı eylemler gerçekleştirmek için kullanılır.

Yürütülecek birçok kod bloğundan birini seçmek için `switch` deyimini kullanın.

```PHP title:'switch Syntax' hl:1,5,7,11
switch (ifade) {
  case durum1:
    //Kod Bloğu
    break;
  case durum2:
    //Kod Bloğu
    break;
  case durum3:
    //Kod Bloğu
    break;
  default:
    //Kod Bloğu
}
```

Switc böyle çalışıyor:

- İfade bir kez değerlendirilir
- İfadenin değeri her bir durumun değerleriyle karşılaştırılır
- Bir eşleşme varsa, ilgili kod bloğu yürütülür
- `break` anahtar sözcüğü anahtar bloğundan çıkar
- `default` kod bloğu, eşleşme yoksa yürütülür

```PHP title:'Basit bir örnek'
$favcolor = "kırmızı";

switch ($favcolor) {
  case "kırmızı":
    echo "Favori renginiz: Kırmızı!";
    break;
  case "mavi":
    "Favori renginiz: Mavi!";
    break;
  case "yeşil":
    echo "Favori renginiz: Yeşil!";
    break;
  default:
    echo "Favori renginiz kırmızı,mavi veya yeşil değil.";
}
```

### `break` Anahtar Sözcüğü
---
PHP bir `break` anahtar sözcüğüne ulaştığında, `switch` bloğundan çıkar.

Bu, daha fazla kodun yürütülmesini durduracak ve daha fazla durum eşleştirilmeye çalışılmayacaktır.

Son bloğun `break`'e ihtiyacı yoktur, blok zaten orada kırılır (biter).

Uyarı: Eğer `break` ifadesini bir durumda atlarsanız ve bir eşleşme alırsa eşleşmese bile bir sonraki durumun kod bloğu da yürütülür!

```PHP title:'Hatalı bir örnek' error:4-6
$favcolor = "kırmızı";

switch ($favcolor) {
  case "kırmızı":
    echo "Favori renginiz: Kırmızı!";
    // Kodun burada bitmesi için break ifadesi gerekir!
  case "mavi":
    "Favori renginiz: Mavi!";
    break;
  case "yeşil":
    echo "Favori renginiz: Yeşil!";
    break;
  default:
    echo "Favori renginiz kırmızı,mavi veya yeşil değil.";
}
```

### `default` Anahtar Sözcüğü
---
`default` anahtar sözcüğü, durum eşleşmesi yoksa çalıştırılacak kodu belirtir:

```PHP title:'Basit bir örnek'
$d = 4;

switch ($d) {
  case 6:
    echo "Bu gün Cumartesi";
    break;
  case 7:
    echo "Bu gün Pazar";
    break;
  default:
    echo "Haftaiçi bir gün girilmiş";
}
```

`default`, bir switch bloğundaki son durum olmak zorunda değildir:

 >Bloğunun sonundan başka bir yere yerleştirilmesine izin verilir, ancak önerilmez.

```PHP hl:6
$d = 4;

switch ($d) {
  default:
    echo "Haftaiçi bir gün girilmiş";
    break;
  case 6:
    echo "Bu gün Cumartesi";
    break;
  case 7:
    echo "Bu gün Pazar";
}
```

**Not:** Eğer `default` switch bloğundaki son blok değilse, `default` bloğunu bir `break` deyimiyle bitirmeyi unutmayın.

### Ortak Kod Blokları
---
Birden fazla durumun aynı kod bloğunu kullanmasını istiyorsanız, durumları şu şekilde belirtebilirsiniz:

```PHP title:'Basit bir örnek'
$d = 3;

switch ($d) {
  case 1:
  case 2:
  case 3:
  case 4:
  case 5:  
    echo "Haftaiçi bir gün";
    break;
  case 6:
  case 7:
    echo "Haftasonu bir gün";
    break;
  default:
    echo "Hata!";
}
```



