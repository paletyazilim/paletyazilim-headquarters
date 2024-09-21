JSON, JavaScript Object Notation (JavaScript Nesne Gösterimi) anlamına gelir ve veri depolamak ve değiş tokuş etmek için kullanılan bir sözdizimidir.

JSON formatı metin tabanlı bir format olduğundan, bir sunucuya ve sunucudan kolayca gönderilebilir ve herhangi bir programlama dili tarafından veri formatı olarak kullanılabilir.

PHP, JSON'u işlemek için bazı yerleşik fonksiyonlara sahiptir.

İlk olarak, aşağıdaki iki fonksiyona bakacağız:

- `json_encode()`
- `json_decode()`

### `json_encode()` - Fonksiyonu
---

```PHP title:'İlişkisel diziyi JSON olarak kodlama'
<?php
$personeller = array("Berat"=>20, "Furkan"=>28, "Osman"=>33);

echo json_encode($personeller);
?>
```

### `json_decode()` - Fonksiyonu
---
`json_decode()` fonksiyonu bir JSON nesnesini bir PHP nesnesine veya bir ilişkisel diziye çözümlemek için kullanılır.


```PHP title:'JSON verisini nesneye çözümleme'
<?php
$jsonVeri = '{"Berat":20,"Furkan":28,"Osman":33}';

var_dump(json_decode($jsonVeri));
?>
```

`json_decode()` fonksiyonu varsayılan olarak bir nesne döndürür. `json_decode()` fonksiyonunun ikinci bir parametresi vardır ve true olarak ayarlandığında, JSON nesneleri ilişkisel dizilere dönüştürülür.

```PHP title:'JSON verisini ilişkisel diziye çözümleme'
<?php
$jsonVeri = '{"Berat":20,"Furkan":28,"Osman":33}';

var_dump(json_decode($jsonVeri, true));
?>
```

### Çözümlenmiş Verilere Erişim
---
Burada, bir nesneden ve bir ilişkisel diziden çözülmüş değerlere nasıl erişileceğine dair iki örnek verilmiştir:

```PHP title:'Çözümlenmiş veriye erişim [Nesne]'
<?php
$jsonVeri = '{"Berat":20,"Furkan":28,"Osman":33}';

$nesne = json_decode($jsonVeri);

echo $nesne->Berat;
echo $nesne->Furkan;
echo $nesne->Osman;
?>
```

```PHP title:'Çözümlenmiş veriye erişim [Dizi]'
<?php
$jsonVeri = '{"Berat":20,"Furkan":28,"Osman":33}';

$dizi = json_decode($jsonVeri, true);

echo dizi['Berat'];
echo dizi['Furkan'];
echo dizi['Osman'];
?>
```