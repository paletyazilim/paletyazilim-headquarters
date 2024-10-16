**Regex (Regular Expressions)**: programlama dillerinde metin işlemleri için kullanılan bir desen eşleştirme dilidir. Metinlerde belirli kalıpları aramanızı, bulmanızı ve değiştirmenizi sağlar. RegEx kullanarak metin işleme görevlerini daha hızlı ve kolay bir şekilde yapabilirsiniz.

RegEx ifade tek bir karakter veya daha karmaşık bir desen olabilir.

RegEx her türlü metin arama ve metin değiştirme işlemlerini gerçekleştirmek için kullanılabilir.

### Syntax
---
PHP'de RegEx, sınırlayıcılar, bir desen ve isteğe bağlı değiştiricilerden oluşan dizelerdir.

```PHP
$exp = "/php/i";
```

Yukarıdaki örnekte, `/` sınırlayıcıdır, php aranan desendir ve `i` aramayı büyük/küçük harfe duyarsız hale getiren bir değiştiricidir.

Sınırlayıcı, harf, sayı, ters eğik çizgi veya boşluk olmayan herhangi bir karakter olabilir. En yaygın olarak kullanılan sınırlayıcı eğik çizgidir (`/`), ancak deseniniz eğik çizgiler içerdiğinde `#` veya `~` gibi diğer sınırlayıcılarıda kullanabilirsiniz.

### RegEx Fonksiyonları
---
PHP, RegEx kullanmanıza olanak tanıyan çeşitli fonksiyonlar sağlar.

En yaygın işlevler şunlardır:

| Fonksiyon | Açıklama |
| ---- | ---- |
| preg_match() | Dize içinde desen bulunursa 1, bulunmazsa 0 döndürür. |
| preg_match_all() | Dizede desenin kaç kez bulunduğunu döndürür; bu sayı 0 da olabilir. |
| preg_replace() | Eşleşen desenlerin başka bir dizeyle değiştirildiği yeni bir dize döndürür. |

#### `preg_match()`
---
`preg_match()` fonksiyonu, bir dizenin bir desenle eşleşip eşleşmediğini size söyleyecektir.

```PHP title:'preg_match() fonksiyonunun kullanımı'
$metin = "PHP, web geliştirme alanında oldukça popüler bir programlama dilidir. PHP, sunucu taraflı bir dil olup, dinamik web siteleri ve web uygulamaları oluşturmak için kullanılır. PHP, genellikle veritabanıyla etkileşimli web sayfaları oluşturmak için kullanılır. Özellikle MySQL gibi veritabanlarıyla entegrasyonu sağlam ve esnek bir dil olan PHP, geniş bir geliştirici topluluğuna sahiptir.";

$desen = "/php/i";
echo preg_match($desen, $metin);
```

#### `preg_match_all()`
---
`preg_match_all()` fonksiyonu, bir dizede bir desen için kaç eşleşme bulunduğunu size söyleyecektir.

```PHP title:'preg_match_all() fonksiyonunun kullanımı'
$metin = "PHP, web geliştirme alanında oldukça popüler bir programlama dilidir. PHP, sunucu taraflı bir dil olup, dinamik web siteleri ve web uygulamaları oluşturmak için kullanılır. PHP, genellikle veritabanıyla etkileşimli web sayfaları oluşturmak için kullanılır. Özellikle MySQL gibi veritabanlarıyla entegrasyonu sağlam ve esnek bir dil olan PHP, geniş bir geliştirici topluluğuna sahiptir.";

$desen = "/php/i";
echo preg_match($desen, $metin);
```

#### `preg_replace()`
---
`preg_replace()` fonksiyonu, bir dizedeki desenin tüm eşleşmelerini başka bir dizeyle değiştirir.

```PHP title:'preg_replace() fonksiyonunun kullanımı'
$metin = "Osman MASAT!";
$desen = "/osman/i";
echo preg_replace($desen, "Berat", $metin);
```

### RegEx Değiştiricileri
---
Değiştiriciler bir aramanın nasıl gerçekleştirileceğini değiştirebilir.

| Değiştirici | Açıklama |
| ---- | ---- |
| i | Büyük/küçük harfe duyarlı olmayan bir arama gerçekleştirir. |
| m | Çok satırlı arama gerçekleştirir. (bir dizenin başında veya sonunda eşleşme arayan desenler artık her satırın başında veya sonunda eşleşecektir) |
| u | UTF-8 kodlu desenlerin doğru eşleştirilmesini sağlar |

### RegEx Desenleri
---
Köşeli parantezler bir karakter aralığını bulmak için kullanılır:

| Desen | Açıklama |
| ---- | ---- |
| `[abc]` | Parantez içindeki karakterlerden birini veya birçoğunu bulun. |
| `[^abc]` | Parantezler içinde OLMAYAN herhangi bir karakteri bulun |
| `[a-z]` | İki harf arasında herhangi bir karakteri alfabetik olarak bulma |
| `[A-z]` | Belirtilen bir büyük harf ile belirtilen bir küçük harf arasındaki herhangi bir karakteri alfabetik olarak bulma |
| `[A-Z]` | İki büyük harf arasında alfabetik olarak herhangi bir karakteri bulun. |
| `[123]` | Parantez içindeki rakamlardan birini veya birçoğunu bulun |
| `[0-5]` | İki sayı arasındaki herhangi bir rakamı bulun |
| `[0-9]`	 | Herhangi bir rakamı bulun |

### Metakarakterler (Metacharacters)
---
Metakarakterler özel bir anlamı olan karakterlerdir:

| Metakarakter | Açıklama |
| ---- | ---- |
| `\|` | Kedi`\|`köpek`\|`balık gibi `\|` ile ayrılmış kalıplardan herhangi biri için bir eşleşme bulun |
| `.` | Herhangi bir karakteri bul |
| `^` | Bir dizenin başlangıcı olarak bir eşleşme bulur: `^Merhaba` |
| `$` | Dizenin sonunda bir eşleşme bulur: `Dünya$` |
| `\d` | Herhangi bir rakamı bulun |
| `\D` | Rakam olmayanları bulun |
| `\s` | Herhangi bir boşluk karakterini bulma |
| `\S` | Boşluk olmayan herhangi bir karakteri bulma |
| `\w` | Herhangi bir alfabetik harfi (a'dan Z'ye) ve rakamı (0'dan 9'a) bulun |
| `\W` | Alfabetik olmayan ve rakam olmayan herhangi bir karakteri bulun |
| `\b` | Bunun gibi bir kelimenin başında bir eşleşme bulun: \bKELİME veya bir kelimenin sonunda şöyle bir eşleşme bulun: KELİME\b |
| `\uxxxx` | Onaltılık sayısı ile belirtilen Unicode karakterini bulun |

### Niceleyiciler (Quantifiers)
---
Nicelik belirteçleri nicelikleri tanımlar:

| Niceleyici | Açıklama |
| ---- | ---- |
| n+ | En az bir *n* içeren herhangi bir dizeyle eşleşir |
| n* | *n* sıfır veya daha fazla tekrarını içeren herhangi bir dizeyle eşleşir |
| n? | *n*'nin sıfır veya bir tekrarını içeren herhangi bir dizeyle eşleşir |
| n{3} | Bir dizi 3 *n* içeren herhangi bir dizeyle eşleşir |
| n{2, 5}	 | En az 2, ancak en fazla 5 *n*'den oluşan bir dizi içeren herhangi bir dizeyle eşleşir |
| n{3,} | En az 3 *n*'den oluşan bir dizi içeren herhangi bir dizeyle eşleşir |

**Not:** Deseniniz özel karakterlerden birini araması gerekiyorsa, bunlardan kaçmak için ters eğik çizgi ( `\` ) kullanabilirsiniz. Örneğin, bir veya daha fazla soru işareti aramak için aşağıdaki ifadeyi kullanabilirsiniz: `$desen = '/\?+/';`

### Gruplama
---
Niceleyicileri tüm desenlere uygulamak için parantezleri `( )` kullanabilirsiniz. Ayrıca eşleşme olarak kullanılacak desen parçalarını seçmek için de kullanılabilirler.

```PHP title:'Desenleri gruplama'
$metin = "Apples and bananas.";
$desen = "/ba(na){2}/i";
echo preg_match($desen, $metin);
```