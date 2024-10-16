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

### Dizi Fonksiyonlarıı
---
PHP dizilerinin gerçek gücü, dizi öğelerini saymak için `count()` fonksiyonu gibi yerleşik dizi işlevleridir:

```PHP title:'Dizide ki öğe sayısı'
$arabalar = array("Volvo", "BMW", "Toyota");
echo count($arabalar);
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

## Diziye Erişme
---
Bir dizi öğesine erişmek için, dizinli diziler için indeks numarasına ve ilişkisel diziler için anahtar adına başvurabilirsiniz.

```PHP title:'İndeksli bir diziye erişim'
$arabalar = array("Volvo", "BMW", "Toyota");
echo $arabalar[2];
```

**Not:** İlk öğenin indeksi 0'dır.

İlişkisel bir dizideki öğelere erişmek için anahtar adını kullanın:

```PHP title:'İlişkisel bir diziye erişim'
$araba = array("marka" => "Ford", "model" => "Mustang", "yıl" => 1964);
echo $araba["yıl"];
```

Bir diziye erişirken hem çift hem de tek tırnak kullanabilirsiniz:

```PHP title:'Tek tırnak ve çift tırnak kullanımı'
echo $araba["model"];
echo $araba['model'];
```

### Fonksiyon Öğesine Erişme
---
Dizi öğeleri, fonksiyon da dahil olmak üzere herhangi bir veri türünü barındırabilir.

Fonksiyon tipi veri türlerini çalıştırmak için indeks numarasını ve ardından parantez `()` işaretini kullanın:

```PHP title:'İndeksli dizide fonksiyon öğesine erişme'
function testFonksiyon() {
  echo "Ben bir fonksiyon öğesiyim!";
}

$dizi = array("Volvo", 15, testFonksiyon);

$dizi[2]();
```

Fonksiyon bir ilişkisel dizideki bir öğe olduğunda anahtar adını kullanarak çağırabilirsiniz:

```PHP title:'İlişkisel dizide fonksiyon öğesine erişme'
function testFonksiyon() {
  echo "Ben bir fonksiyon öğesiyim!";
}

$dizi = array("araba" => "Volvo", "yaş" => 15, "fonksiyon" => testFonksiyon);

$dizi["fonksiyon"]();
```

## Dizi Güncelleme
---
Varolan bir dizi öğesini güncelleştirmek için, indeksli diziler için indeks numarasına, ilişkisel diziler için anahtar adına başvurabilirsiniz.

```PHP title:'İndeksli bir dizinin öğesini güncelleme'
$arabalar = array("Volvo", "BMW", "Toyota");
$arabalar[1] = "Ford";
```

>**Not:** İlk öğenin indeksi 0'dır.

İlişkisel bir dizideki öğeleri güncellemek için anahtar adını kullanın:

```PHP title:'İlişkisel bir dizinin öğesini güncelleme'
$araba = array("marka" => "Ford", "model" => "Mustang", "yıl" => 1964);
$cars["marka"] = 2024;
```

### Döngü Kullanarak Dizi Güncelleme
---
Bir `foreach` döngüsünde öğe değerlerini değiştirirken kullanılacak farklı teknikler vardır.

Bunun bir yolu, öğe değerini referans olarak atamak için atamaya `&` karakterini eklemek ve böylece döngü içindeki dizi öğesinde yapılan herhangi bir değişikliğin orijinal dizide yapılmasını sağlamaktır:

```PHP title='Döngü kullanarak dizi öğesini güncelleme' hl:3
$arabalar = array("Volvo", "BMW", "Toyota");

foreach ($arabalar as &$araba) {
  $araba = "Ford";
}
unset($araba);
var_dump($arabalar);
```

**Not:** Döngüden sonra `unset()` fonksiyonunu kullanmayı unutmayın.

`unset($araba)` fonksiyonu kullanmamanız durumunda, `$araba` değişkeni son dizi öğesine referans olarak kalacaktır.

Bunu göstermek için `foreach` döngüsünden sonra `$araba` değerini değiştirdiğimizde ne olduğuna bakın:

```PHP title:'Verilen referansın kaldırılmaması'
$arabalar = array("Volvo", "BMW", "Toyota");

foreach ($arabalar as &$araba) {
  $araba = "Ford";
}
$araba = "BMW";

var_dump($arabalar);
```

## Dizi Silme
---
Bir diziden mevcut bir öğeyi silmek için `unset()` fonksiyonunu kullanabilirsiniz.

`unset()` fonksiyonu belirtilen değişkenleri siler ve bu nedenle dizi öğelerini silmek için kullanılabilir:

```PHP title:'İndeksli dizide öğe silme'
$arabalar = array("Volvo", "BMW", "Toyota");
unset($arabalar[1]);
```

İlişkisel bir diziden öğe kaldırmak için `unset()` işlevini kullanabilirsiniz, ancak indeks numarası yerine anahtar adına atıfta bulunabilirsiniz.

```PHP title:'İlişkisel dizide öğe silme'
$araba = array("marka" => "Ford", "model" => "Mustang", "yıl" => 1964);
unset($araba["model"]);
```

### Çoklu Silme
---
`unset()` fonksiyonu sınırsız sayıda bağımsız argüman alır ve bu nedenle birden çok dizi öğesini silmek için kullanılabilir:

```PHP title:'Çoklu dizi öğesi silme'
$arabalar = array("Volvo", "BMW", "Toyota");
unset($arabalar[0], $arabalar[1]);
```

Not: `unset()` fonksiyonu indeksleri yeniden **DÜZENLEMEZ**, silinen öğeler yeniden sıralanmaz kalan öğe taşıdığı indeks numarasında kalır.

### `array_splice` Fonksiyonu
---
Silinen öğelerin yeniden düzenlemesini istiyorsanız, `array_splice()` fonksiyonunu kullanabilirsiniz.

`array_splice()` fonksiyonu ile indeksi (nereden başlayacağınızı) ve kaç öğe silmek istediğinizi belirtirsiniz.

```PHP title:'array_splice fonksiyonu ile silme işlemi'
$arabalar = array("Volvo", "BMW", "Toyota");
array_splice($arabalaar, 1, 1);
```

Silme işleminden sonra, dizi `0` indeksinden başlayarak otomatik olarak yeniden indekslenir ve boş olan indeks bulunursa kaydırma işlemi yapılır.

## Dizileri Sıralama
---
Bir dizideki öğeler alfabetik veya sayısal sıraya göre, azalan veya artan şekilde sıralanabilir. Sıralamak için önceden tanımlı fonksiyonları kullanabilirsiniz.

Bu bölümde, aşağıdaki PHP dizi sıralama işlevlerini inceleyeceğiz:

- `sort()` - dizileri sıralar
- `rsort()` - dizileri ters sıralar
- `asort()` - ilişkisel dizileri değere göre sıralar
- `ksort()` - ilişkisel dizileri anahtara göre sıralar
- `arsort()` - ilişkisel dizileri değere göre ters sıralar
- `krsort()` - ilişkisel dizileri anahtara göre ters sıralar

### İndeksli Dizilerde Sıralama
---
#### `sort()`
---
Aşağıdaki örnek `$arabalar` dizisinin öğeleri alfabetik sıraya göre sıralar:

```PHP title:'Alfabetik sıralama'
$arabalar = array("Volvo", "BMW", "Toyota");
sort($arabalar);
```

Aşağıdaki örnek `$sayilar` öğeleri küçükten-büyüğe doğru sıralar:

```PHP title:'Sayısal sıralama'
$sayilar = array(4, 6, 2, 22, 11);
sort($sayilar);
```

Bir dizide hem dize hem de sayısal öğeler varsa önce alfabetik daha sonra sayısal sıralama yapılır:

```PHP title:'Karmaşık sıralama'
$dizi = array("Volvo", "BMW","Toyota",4,6,2,22,11);
sort($dizi);
```
#### `rsort()`
---
Aşağıdaki örnek `$arabalar` dizisinin öğeleri azalan alfabetik sıraya göre sıralar:

```PHP title:'Alfabetik sıralama'
$arabalar = array("Volvo", "BMW", "Toyota");
rsort($arabalar);
```

Aşağıdaki örnek `$sayilar` dizisinin elemanlarını büyükten-küçüğe doğru sıralar:

```PHP title:'Sayısal sıralama'
$sayilar = array(4, 6, 2, 22, 11);
rsort($sayilar);
```

Bir dizide hem dize hem de sayısal öğeler varsa önce sayısal daha sonra alfabetik sıralama yapılır:

```PHP title:'Karmaşık sıralama'
$dizi = array("Volvo", "BMW","Toyota",4,6,2,22,11);
rsort($dizi);
```

### İlişkisel Dizilerde Sıralama
---
#### `asort()`
---
Aşağıdaki örnek, bir ilişkisel diziyi değere göre sıralar:

```PHP title:'Öğe değerine göre sıralama'
$yas = array("Berat"=>20, "Furkan"=>29, "Osman"=>32);
asort($yas);
```

#### `arsort()`
---
Aşağıdaki örnek, bir ilişkisel diziyi değere göre ters sıralar:

```PHP 'Öğe değerine göre tersine sıralama'
$yas = array("Berat"=>20, "Furkan"=>29, "Osman"=>32);
arsort($yas);
```

#### `ksort()`
---
Aşağıdaki örnek, bir ilişkisel diziyi anahtara göre sıralar:

```PHP title:'Öğe anahtarına göre sıralama'
$yas = array("Berat"=>20, "Furkan"=>29, "Osman"=>32);
ksort($yas);
```

#### `krsort()`
---
Aşağıdaki örnek, bir ilişkisel diziyi anahtara göre ters sıralar:

```PHP title:'Öğe anahtarına göre ters sıralama'
$yas = array("Berat"=>20, "Furkan"=>29, "Osman"=>32);
krsort($yas);
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

## Çok Boyutlu Diziler
---
Önceki sayfalarda, anahtar/değer çiftlerinden oluşan tek bir liste olan dizileri tanımladık.

Ancak, bazen birden fazla anahtar içeren değerleri saklamak istersiniz. Bunun için çok boyutlu dizilerimiz vardır.

Çok boyutlu dizi, bir veya daha fazla dizi içeren bir dizidir.

PHP iki, üç, dört, beş veya daha fazla derinliğe sahip çok boyutlu dizileri destekler. Ancak, üç seviyeden daha derin dizileri yönetmek çoğu insan için zordur.

Bir dizinin boyutu, bir öğeyi seçmek için ihtiyacınız olan indis sayısını gösterir.

- İki boyutlu bir dizide bir elemanı seçmek için iki indise ihtiyacınız vardır.
- Üç boyutlu bir dizide bir elemanı seçmek için üç indise ihtiyacınız vardır.

### İki Boyutlu Diziler
---
İki boyutlu bir dizi, dizilerden oluşan bir dizidir.

İlk olarak, aşağıdaki tabloya bir göz atın:

| İsim | Stok | Satılan |
| ---- | ---- | ---- |
| Elma | 20 | 6 |
| Armut | 30 | 12 |
| Kiraz | 10 | 3 |
Yukarıdaki tablodaki verileri aşağıdaki gibi iki boyutlu bir dizide saklayabiliriz:

```PHP title:'Basit bir iki boyutlu dizi'
$meyveler = array (
  array("Elma",20,6),
  array("Armut",30,12),
  array("Kiraz",10,3),
);
```

Şimdi iki boyutlu `$meyveler` dizisi üç dizi içerir ve iki indisi vardır: satır ve sütun.

`$meyveler` dizisinin öğelerine erişmek için iki indisi (satır ve sütun) işaret etmeliyiz:

```PHP title:'İki boyutlu dizinin öğelerine erişme'
echo $meyveler[0][0].": Stokta: ".$meyveler[0][1].", Satıldı: ".$meyveler[0][2].".<br>";
echo $meyveler[1][0].": Stokta: ".$meyveler[1][1].", Satıldı: ".$meyveler[1][2].".<br>";
echo $meyveler[2][0].": Stokta: ".$meyveler[2][1].", Satıldı: ".$meyveler[2][2].".<br>";
```

Ayrıca `$meyveler` dizisinin öğelerini almak için başka bir `for` döngüsünün içine bir `for` döngüsü koyabiliriz (hala iki indise işaret etmemiz gerekir):

```PHP title:'Döngü ile iki boyutlu bir dizinin öğelerine  erişme'
for ($satir = 0; $satir < 3; $satir++) {
  echo "<p><b>Satır Numarası $satir</b></p>";
  echo "<ul>";
    for ($sutun = 0; $sutun < 3; $sutun++) {
      echo "<li>".$meyveler[$satir][$sutun]."</li>";
    }
  echo "</ul>";
}
```

