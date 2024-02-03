PHP'de, kullanıldıkları yere göre değer değiştiren ve bu nedenle "magic constanst" (sihirli sabitler) olarak adlandırılan dokuz adet önceden tanımlı sabit vardır.

Bu sihirli sabitler, `ClassName::class` sabiti hariç, başında ve sonunda çift alt çizgi ile yazılır.

**Not:** Sihirli sabitler büyük/küçük harf duyarsızdır, yani `__SABIT__`, `__sabit__` ile aynı sonucu verir.

| Sabit | Açıklama |
| ---- | ---- |
| `__CLASS__` | Bir sınıfın içinde kullanılırsa, sınıf adı döndürülür.<br> |
| `__DIR__` | Dosyanın bulunduğu dizin. |
| `__FILE__` | Tam yolu içeren dosya adı. |
| `__FUNCTION__` | Bir fonksiyonun içindeyse, fonksiyon adı döndürülür. |
| `__LINE__` | Geçerli satır numarası. |
| `__METHOD__` | Bir sınıfa ait bir fonksiyonun içinde kullanılırsa, hem sınıf hem de fonksiyon adı döndürülür. |
| `__NAMESPACE__` | Bir ad alanı içinde kullanılırsa, ad alanının adı döndürülür. |
| `__TRAIT__` | Bir özellik içinde kullanılırsa, özellik adı döndürülür. |
| `ClassName::class` | Belirtilen sınıfın adını ve varsa isim alanının adını döndürür. |

