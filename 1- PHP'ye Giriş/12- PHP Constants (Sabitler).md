Sabitler, programın çalışması boyunca değiştirilemeyen değerlerdir. Sabitler, programların daha okunabilir, anlaşılır ve bakımı kolay olmasını sağlar.

Sabit, basit bir değer için bir tanımlayıcıdır (ad). Kod sırasında değer değiştirilemez.

Geçerli bir sabit adı bir harf veya alt çizgi ile başlar (sabit adından önce `$` işareti bulunmaz).

**Not:** Değişkenlerin aksine, sabitler tüm kod boyunca otomatik olarak globaldir.

### Sabit Oluşturma
---
Bir sabit oluşturmak için `define()` fonksiyonunu kullanın.

```PHP title:'Const Syntax'
define(name, value, case-insensitive);
```

Parametreler:
- **name:** Sabitin adını belirtir
- **değer:** Sabitin değerini belirtir
- **case-insensitive**: Sabit adın büyük/küçük harfe duyarsız olup olmayacağını belirtir. Öntanımlı olarak `false` değerini alır.

**Bilginize:** Büyük/küçük harfe duyarsız sabitler tanımlamak PHP 7.3'te kullanımdan kaldırılmıştır. PHP 8.0 sadece `false` değerini kabul eder, `true` değeri bir uyarı üretecektir.

```PHP title:'Büyük-Küçük Harfe Duyarlı Sabit Oluşturma'
define("KARSILAMA_MESAJI", "Anasayfaya Hoşgeldiniz!");
echo KARSILAMA_MESAJI;
```

```PHP title:'Büyük-Küçük Harfe Duyarsız Sabit Oluşturma'
define("KARSILAMA_MESAJI", "Anasayfaya Hoşgeldiniz!", true);
echo KARSILAMA_MESAJI;
```

### `const` Anahtar Sözcüğü
---
`const` anahtar sözcüğünü kullanarak da bir sabit oluşturabilirsiniz.

```PHP
const ARABAM = "Volvo";
echo ARABAM;
```

### `const` ile `define()` Farkı
---
- `const` her zaman büyük/küçük harfe duyarlıdır
- `define()` işlevinin büyük/küçük harfe duyarlı olmayan bir seçeneği vardır.
- `const`, bir fonksiyonun veya if deyiminin içinde olduğu gibi başka bir blok kapsamının içinde oluşturulamaz.
- `define()` başka bir blok kapsamı içinde oluşturulabilir.

### Sabit Diziler
---
PHP7'den itibaren `define()` fonksiyonunu kullanarak bir Dizi (Array) sabiti oluşturabilirsiniz.

```PHP
define("arabalar", [
  "Alfa Romeo",
  "BMW",
  "Toyota"
]);
echo arabalar[0];
```

### Sabitler Globaldir
---
Sabitler otomatik olarak globaldir ve tüm kod kapsamında kullanılabilir.

```PHP
define("KARSILAMA_MESAJI", "Anasayfaya Hoşgeldiniz!");

function testFonksiyon() {
  echo KARSILAMA_MESAJI;
}

testFonksiyon();
```

