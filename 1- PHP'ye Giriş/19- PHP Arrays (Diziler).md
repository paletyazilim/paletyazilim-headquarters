## Dizilere Giriş
---
Dizi, tek bir ad altında birçok değeri tutabilen özel bir değişkendir ve değerlere bir dizin numarasına veya ada başvurarak erişebilirsiniz.

Bu eğitimde dizilerle nasıl çalışacağınızı öğreneceksiniz:

- Diziler Oluşturma
- Dizilere Erişim
- Dizileri Güncelleme
- Dizi Öğelerini Kaldırma
- Dizileri Sıralama

### Dizi Türleri
---
PHP'de üç tür dizi türü vardır:

- **İndeksli Diziler:** Sayısal indekse sahip diziler
- **İlişkisel Diziler:** Adlandırılmış anahtarlara sahip diziler
- **Çok Boyutlu Diziler:** Bir veya daha fazla dizi içeren diziler

### Dizi Öğeleri
---
Dizi öğeleri herhangi bir veri türünde olabilir. En yaygın olanları dizeler ve sayılardır (int, float), ancak dizi öğeleri nesneler, işlevler ve hatta diziler de olabilir.

Aynı dizi içinde farklı veri türleri olabilir.

```PHP title:'Örnek Dizi'
$myArr = array("Volvo", 15, ["elma", "muz"], fonksiyon);
```

### Dizi Fonksiyonlar
---
PHP dizilerinin gerçek gücü, dizi öğelerini saymak için `count()` fonksiyonu gibi yerleşik dizi işlevleridir:

```PHP title:'Dizide ki öğe sayısı'
$arabalar = array("Volvo", "BMW", "Toyota");
echo count($arabalar);
```

## İndeksli Diziler
---
İndeksli dizilerde her öğenin bir indeks numarası vardır.

Varsayılan olarak, ilk öğe 0 indeksine, ikinci öğe 1 indeksine, vb. sahiptir.

```PHP title:'Basit bir örnek'
$arabalar = array("Volvo", "BMW", "Toyota");
var_dump($arabalar);
```

### İndeksli Dizilere Erişme
---
Bir dizi öğesine erişmek için dizin numarasına başvurabilirsiniz.

```PHP title:'Diziye Erişim'
$arabalar = array("Volvo", "BMW", "Toyota");
echo $arabalar[0];
```

### Dizi Öğesini Değişme
---
Bir dizi öğesinin değerini değiştirmek için dizin numarasını kullanın:

```PHP title:'Dizi Öğesini Değiştirme'
$arabalar = array("Volvo", "BMW", "Toyota");
$arabalar[1] = "Ford";
var_dump($arabalar);
```

### İndeksli Dizilerde Döngü 
---
İndeksli bir dizinin tüm değerleri arasında döngü oluşturmak ve bunları yazdırmak için aşağıdaki gibi bir `foreach` döngüsü kullanabilirsiniz:

```PHP title:'Dizi öğelerini döngü ile alma'
$arabalar = array("Volvo", "BMW", "Toyota");
foreach ($arabalar as $araba) {
  echo "$araba <br>";
}
```

### İndeks Numarası
---
İndekslenmiş bir dizinin anahtarı bir sayıdır, varsayılan olarak ilk öğe 0 ve ikincisi 1'dir, ancak istisnalar vardır.

Yeni öğeler bir sonraki indeks numarasını alır, yani mevcut en yüksek indeksten bir üstünü alır.

Yani şöyle bir diziniz varsa:

```PHP
$arabalar = array();

$arabalar[0] = "Volvo";
$arabalar[1] = "BMW";
$arabalar[2] = "Toyota";
```

Ve yeni bir öğe eklemek için `array_push()` fonksiyonunu kullanırsanız, yeni öğe 3. indeksi alacaktır:

```PHP
array_push($arabalar, "Ford");
var_dump($arabalar);
```

Ancak, bunun gibi rastgele dizin numaralarına sahip bir diziniz varsa:

```PHP
$arabalar = array();

$arabalar[4] = "Volvo";
$arabalar[9] = "BMW";
$arabalar[17] = "Toyota";
```

Ve yeni bir öğe eklemek için `array_push()` fonksiyonunu kullanırsanız, yeni öğe en büyük indeksten sonraki sayıyı alacaktır:

```PHP
array_push($arabalar, "Ford");
var_dump($arabalar);
```

## İlişkisel Diziler
---
İlişkisel diziler, kendilerine atadığınız adlandırılmış anahtarları kullanan dizilerdir.

```PHP
$araba = array("marka"=>"Ford", "model"=>"Mustang", "yıl"=>1964);
var_dump($araba);
```

### İlişkisel Dizilere Erişim
---
Bir dizi öğesine erişmek için anahtar adına başvurabilirsiniz.

```PHP title:'Dizi öğesine erişim'
$araba = array("marka"=>"Ford", "model"=>"Mustang", "yıl"=>1964);
echo $araba['marka'];
```

### Dizi Öğesini Değişme
---
Bir dizi öğesinin değerini değiştirmek için anahtar adını kullanın:

```PHP title:'Dizi öğesinin değerini değiştirme'
$araba = array("marka"=>"Ford", "model"=>"Mustang", "yıl"=>1964);
$araba['yıl'] = 1998;
var_dump($araba);
```

### İlişkisel Dizilerde Döngü
---
İlişkisel bir dizinin tüm değerleri arasında döngü oluşturmak ve bunları yazdırmak için aşağıdaki gibi bir `foreach` döngüsü kullanabilirsiniz:

```PHP title:'Dizi öğelerini döngü ile alma'
$araba = array("marka"=>"Ford", "model"=>"Mustang", "yıl"=>1964);

foreach ($araba as $anahtar => $deger) {
  echo "$anahtar: $deger <br>";
}
```

## Dizi Oluşturma
---
`array()` fonksiyonunu kullanarak diziler oluşturabilirsiniz:

```PHP title:'Dizi oluşturma'
$arabalar = array("Volvo", "BMW", "Toyota");
```

Ayrıca `[]` parantezlerini kullanarak daha kısa bir syntax'da kullanabilirsiniz:

```PHP title:'Alternatif bir syntax'
$arabalar = ["Volvo", "BMW", "Toyota"];
```

### Çoklu Satır
---
Satır sonları önemli değildir, bu nedenle bir dizi bildirimi birden çok satıra yayılabilir ve son öğeden sonra virgül konmasına izin verilir:

```PHP title:'Çok satırlı dize tanımlama'
$arabalar = [
  "Volvo",
  "BMW",
  "Toyota",
];
```

### Dizi Anahtarları
---
İndeksli diziler oluşturulurken anahtarlar otomatik olarak verilir, 0'dan başlar ve her öğe için 1 artırılır, bu nedenle yukarıdaki dizi anahtarlarla da oluşturulabilir:

```PHP
$arabalar = [
  0 => "Volvo",
  1 => "BMW",
  2 =>"Toyota"
];
```

Gördüğünüz gibi, indeksli diziler ilişkisel dizilerle aynıdır, ancak ilişkisel dizilerde sayılar yerine isimler vardır:

```PHP
$araba = [
  "marka" => "Ford",
  "model" => "Mustang",
  "yıl" => 1964
];
```

### Dizi Anahtarlarını Karıştırma
---
Hem indeksli hem de adlandırılmış anahtarlara sahip dizileriniz olabilir:

```PHP
$dizi = [];
$dizi[0] = "elma";
$dizi[1] = "muz";
$dizi["meyve"] = "kiraz";
```