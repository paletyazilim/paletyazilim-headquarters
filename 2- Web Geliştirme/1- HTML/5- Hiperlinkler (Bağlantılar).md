Bağlantılar bütün web sayfalarında bulunur ve web sayfası içinde gezinti yapmamızı sağlar. HTML'de dokümanınızda bağlantı oluşturmak için `{html icon} <a>` etiketi kullanılır.

`{html icon} <a>` etiketi *Çapa veya Demir* anlamına gelen *Anchor* kelimesinin ilk harfini temsil etmektedir. `href` niteliği ile web sayfalarına, dosyalara, e-posta adreslerine veya aynı sayfadaki başka bir konuma yönlendirebilirsiniz.

## Göreceli (Relative) ve Mutlak (Absolute) Bağlantılar
---
`href` niteliğine belirtebileceğiniz temel olarak iki farklı bağlantı türü vardır, göreceli (relative) bağlantılar dosya bazında arama yaparken, mutlak (absolute) bağlantılar normal bir URL içerir.

```html 
<a href="/example.html">Diğer Sayfaya Git</a>
```

`href` niteliğinde belirtilen `/example.html` dosyasını proje dizininde arayıp sayfa içinde yönlendirme yapacaktır.

```html
<a href="https://www.youtube.com">YouTube'a Git</a>
```

`href` niteliğinde belirtilen `https://www.youtube.com` bağlantısını ziyaret edecektir.

## `target` Niteliği
---
`target` niteliği bağlantıya tıklandığında bağlantı içeriğinin nasıl davranması gerektiğini belirten bir niteliktir. `target` niteliği ile bağlantıyı aynı sekmede (varsayılan), yeni pencerede açılmasını sağlayabilir. 

| Niteliğin Alabileceği Değer | Açıklama                                                                                                                          |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `_self` (Varsayılan)        | Geçerli pencerede bağlantıyı açar.                                                                                                |
| `_blank`                    | Yeni bir pencerede bağlantıyı açar.                                                                                               |
| `_parent`                   | Mevcut bağlantı bir `iframe` içerisinde tıklanılırsa mevcut sayfada açar ancak `iframe` içerisinde değilse `_self` gibi davranır. |
