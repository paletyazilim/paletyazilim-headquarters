PHP'nin gerçek gücü işlevlerinden gelir.

PHP'nin 1000'den fazla yerleşik işlevi vardır ve bunlara ek olarak kendi özel işlevlerinizi de oluşturabilirsiniz.

Yerleşik PHP işlevlerinin yanı sıra, kendi işlevlerinizi oluşturmanız da mümkündür.

- Fonksiyon, bir programda tekrar tekrar kullanılabilen bir deyimler bloğudur.
- Bir fonksiyon, sayfa yüklendiğinde otomatik olarak çalışmaz.
- Bir fonksiyon, fonksiyona yapılan bir çağrı ile çalıştırılır.

### Fonksiyon Oluşturma
---
Kullanıcı tanımlı bir fonksiyon bildirimi `function` anahtar sözcüğü ile başlar ve ardından fonksiyonun adı gelir:

```PHP title:'Fonksiyon oluşturma'
function mesajYazma() {
  echo "Merhaba Dünya!";
}
```

**Not:** Bir fonksiyon adı bir harf veya alt çizgi ile başlamalıdır. Fonksiyon adları büyük/küçük harfe duyarlı DEĞİLDİR.

>İpucu: Fonksiyona, fonksiyonun ne yaptığını yansıtan bir isim verin!

### Fonksiyon Çağırma
---
Fonksiyonunuzu çağırmak için adını ve ardından parantez `()` işaretini yazmanız yeterlidir:

```PHP title:'Fonksiyon çağırma'
function mesajYazma() {
  echo "Merhaba Dünya!";
}

mesajYazma();
```

Örneğimizde `mesajYazma()` adında bir fonksiyon oluşturuyoruz.

Açılış küme parantezi `{` fonksiyon kodunun başlangıcını, kapanış küme parantezi `}` ise fonksiyonun sonunu belirtir.

İşlev "Merhaba dünya!" çıktısını verir.

### Test
