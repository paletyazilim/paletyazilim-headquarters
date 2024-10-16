**Değişken:** Programda kullanılan verilerin saklandığı bellek alanlarıdır. Her değişkenin bir adı ve bir veri tipi vardır. Ad, değişkene verilen sembolik bir kimliktir. Veri tipi ise değişkenin tutabileceği verinin türünü belirtir. Değişkenler, programın akışını kontrol etmek, verileri işlemek ve hesaplamalar yapmak için kullanılır.

### PHP Değişkenleri
---
Bir değişken kısa bir isme (`$x` ve `$y` gibi) veya daha açıklayıcı bir isme (`$yas`, `$arabaismi`, `$toplam_hesap`) sahip olabilir.

PHP değişkenleri için kurallar:

- Bir değişken `$` işareti ile başlar ve ardından değişkenin adı gelir.
- Bir değişken adı bir harfle veya alt çizgi karakteriyle başlamalıdır.
- Bir değişken adı sayı ile başlayamaz.
- Bir değişken adı yalnızca alfa sayısal karakterler ve alt çizgiler (A-z, 0-9 ve _ ) içerebilir. Bir diğer diyişle Türkçe karakter içeremez (ş,ğ,ö,ç vb.)
- Değişken adları büyük/küçük harfe duyarlıdır (`$yas` ve `$YAS` iki farklı değişkendir).

PHP değişken adlarının büyük/küçük harfe **duyarlı** olduğunu unutmayın!

#### Değişken Türleri
---
PHP'de değişken bildirmek için bir komut yoktur ve veri türü değişkenin değerine bağlıdır.

```PHP title:"Değişken Türleri"
$x = 5;      // $x sayısal bir değişken
$y = "Berat"; // $y metinsel bir değişken
echo $x;
echo $y;
```

PHP aşağıdaki veri türlerini destekler:

- String
- Integer
- Float
- Boolean
- Array
- Object
- NULL
- Resource

### PHP Değişkeni Oluşturma (Tanımlama)
---
PHP'de bir değişken `$` işareti ile başlar ve ardından değişkenin adı gelir:

```PHP title:"Değişken oluşturma"
$x = 5;
```

Yukarıdaki örnekte, `$x` değişkeni 5 değerini tutacaktır.

Diğer programlama dillerinden farklı olarak PHP'de değişken bildirimi için bir komut yoktur. Değişken, ona ilk kez bir değer atadığınız anda oluşturulur.

### Dize Türünde Bir Değişken Oluşturma (Tanımlama)
---
Bir değişkene dize atamak, değişken adının ardından bir eşittir işareti ve tırnak içinde ile yapılır:

```PHP title:"Değişken oluşturma"
$x = "Berat";
echo $x;
```

Dize değişkenleri çift ya da tek tırnak kullanılarak bildirilebilir, ancak aralarındaki farkları bilmeniz gerekir. 
### Değişkenleri Yazdırma
---
PHP'de `echo` ifadesi genellikle ekrana veri çıktısı vermek için kullanılır.

Aşağıdaki örnekte metin ve değişkenin çıktısının nasıl birlikte alınacağı gösterilmektedir:

```PHP title:"Değişkeni çıktılama"
$yazi = "PHP";
echo "$yazi'yi Seviyorum!";
```

Aşağıdaki örnek, yukarıdaki örnekle aynı çıktıyı üretecektir:

```PHP title:"Değişkeni çıktılama"
$yazi = "PHP";
echo $yazi . "'yi Seviyorum!";
```

Aşağıdaki örnek iki değişkenin toplamının çıktısını verecektir:

```PHP title:"Değişkeni çıktılama"
$x = 5;
$y = 4;
echo $x + $y;
```

### PHP Gevşek Tipli Bir Dildir
---
Yukarıdaki örneklerde, PHP'ye değişkenlerin hangi veri türünde olduğunu söylemek zorunda olmadığımıza dikkat edin.

PHP, değişkene değerine bağlı olarak otomatik olarak bir veri türü atar. Veri tipleri kesin bir şekilde belirlenmediğinden, bir tamsayıya string eklemek gibi şeyleri hataya neden olmadan yapabilirsiniz.

PHP 7'de tür bildirimleri eklenmiştir. Bu, bir işlev bildirirken beklenen veri türünü belirtme seçeneği sunar ve katı gereksinimi etkinleştirerek, tür uyuşmazlığında bir **"Fatal Error"** atar.