Arayüzler, bir sınıfın hangi metotları uygulaması gerektiğini belirtmenizi sağlar.

Arayüzler, çeşitli farklı sınıfların aynı şekilde kullanılmasını kolaylaştırır. Bir veya daha fazla sınıf aynı arayüzü kullandığında, bu polymorphism "çok biçimlilik" olarak adlandırılır.

Arayüzler `interface` anahtar sözcüğü ile bildirilir:

```PHP title:'interface Syntax'
<?php
interface ArayuzSinif {
  public function metot1();
  public function metot2($isim, $renk);
  public function metot3() : string;
}
?>
```

### PHP - Arayüzler ve Soyut Sınıflar
---
Arayüzler soyut sınıflara benzer. Arayüzler ve soyut sınıflar arasındaki farklar şunlardır:

- Arayüzler özelliklere sahip olamazken, soyut sınıflar sahip olabilir.
- Tüm arayüz yöntemleri `public` olmalıdır, soyut sınıf yöntemleri ise `public` veya `protected` olabilir.
- Bir arayüzdeki tüm metotlar soyuttur, bu nedenle kodda uygulanamazlar ve `abstract` anahtar sözcüğüne ihtiyaç yoktur.
- Sınıflar bir arayüzü uygularken aynı zamanda başka bir sınıftan miras alabilir.

### Arayüzleri Kullanma
---
Bir arayüzü uygulamak için, bir sınıf `implements` anahtar sözcüğünü kullanmalıdır.

Bir arayüzü uygulayan bir sınıf, arayüzün tüm metotlarını uygulamalıdır.

Örnek:

```PHP title:'Interface kullanımı'
<?php
interface Hayvan {
  public function sesCikar();
}

class Kedi implements Hayvan {
  public function sesCikar() {
    echo "Miyav";
  }
}

$hayvan = new Kedi();
$hayvan->sesCikar();
?>
```

Yukarıdaki örnekten yola çıkarak, bir grup hayvanı yöneten bir yazılım yazmak istediğimizi varsayalım. Tüm hayvanların yapabileceği eylemler var, ancak her hayvan bunu kendi yöntemiyle yapıyor.

Arayüzleri kullanarak, her hayvan farklı davransa bile tüm hayvanlar için çalışabilecek bazı kodlar yazabiliriz:

```PHP title:'Birden fazla arayüz kullanan sınıflar'
<?php
// Arayüzü tanımlama
interface Hayvan {
  public function sesCikar();
}

// Sınıfları tanımlama
class Kedi implements Hayvan {
  public function sesCikar() {
    echo " Miyav ";
  }
}

class Kopek implements Hayvan {
  public function sesCikar() {
    echo " Hav ";
  }
}

class Karga implements Hayvan {
  public function sesCikar() {
    echo " Gaak ";
  }
}

// Hayvanların listesini oluşturalım
$kedi = new Kedi();
$kopek = new Kopek();
$karga = new Karga();
$hayvanlar = array($kedi, $kopek, $karga);

// Her bir hayvanın sesini çıkarma
foreach($hayvanlar as $hayvan) {
  $hayvan->sesCikar();
}
?>
```

Kedi, Köpek ve Karga, Hayvan arayüzünü uygulayan sınıflardır; bu da hepsinin `sesCikar()` metotunu kullanarak ses çıkarabileceği anlamına gelir. Bu nedenle, her birinin ne tür bir hayvan olduğunu bilmesek bile tüm hayvanlar arasında döngü oluşturabilir ve onlara ses çıkarmalarını söyleyebiliriz.

Arayüz, sınıflara yöntemi nasıl uygulayacaklarını söylemediğinden, her hayvan kendi metotuyla kendine ait sesi çıkarabilir.

