HTML (HyperText Markup Language) Web'in en temel yapı taşıdır. Web içeriğinin anlamını ve yapısını tanımlar. HTML dışındaki diğer yardımcı teknolojiler genellikle bir web sayfasının görünümünü/sunumunu (CSS) veya işlevselliğini/davranışını (JavaScript) tanımlamak için kullanılır.

- HTML, Hiper Metin İşaretleme Dili anlamına gelir. 
- HTML, Web sayfaları oluşturmak için kullanılan standart işaretleme dilidir.
- HTML, bir Web sayfasının yapısını tanımlar.
- HTML, bir dizi öğeden oluşur HTML öğeleri, tarayıcıya içeriğin nasıl görüntüleneceğini söyler.
- HTML öğeleri, "bu bir başlıktır", "bu bir paragraftır", "bu bir bağlantıdır" vb. gibi içerik parçalarını etiketler.

```HTML title:"Basit bir HTML dokümanı"
<!DOCTYPE html>
<html>

<head></head>

<body></body>

</html>
```

Yukarıdaki *etiketler* şu anlama gelir:

- `<!DOCTYPE html>` bildirimi bu belgenin bir HTML5 belgesi olduğunu tanımlar.
- `<html>` etiketi bir HTML sayfasının kök etiketidir. 
- `<head>` etiketi HTML sayfası hakkında meta bilgiler içerir.
- `<body>` etiketi belgenin gövdesini tanımlar ve başlıklar, paragraflar, resimler, bağlantılar, tablolar, listeler vb. gibi tüm görünür içerikler için bir kapsayıcıdır.

## HTML'de Etiket Nedir?
---
Bir HTML etiketi bir başlangıç etiketi, etiketin arasına yerleştirilecek bir içerik ve bir bitiş etiketi ile tanımlanır:

`<etiketismi>` İçerik `</etiketismi>`

HTML öğesi ise başlangıç etiketinden kapanış etiketine kadardır.

| Başlangıç Etiketi | Öğe İçeriği     | Kapanış Etiketi |
| ----------------- | --------------- | --------------- |
| `<h1>`            | Bu bir başlık   | `</h1>`         |
| `<p>`             | Bu bir paragraf | `</p>`          |
| `<br>`            | -               | -               |

>**NOT:** Bazı HTML etiketleri içeriğe veya kapanış etiketine (örn: `br` etiketi gibi) ihtiyaç duymaz. Bu etiketlere *boş etiketler* denir.

## Web Tarayıcılar
---
Web tarayıcıların temel amacı World Wide Web (WWW) kaynaklarını edinme ve kullanıcıya göstermektir. Bunun için ortak bir standart olan HTML dokümanlarını okuyup sayfayı gelen bilgiye göre kullanıcıya çıktılamaktır.

Diğer bir deyişle tarayıcılar yazılan HTML etiketlerini tarayıcıda kullanıcıya olduğu gibi göstermek yerine yazılan etiketin anlamına göre etiketin içindeki içeriği biçimlendirerek doğru bir şekilde kullanıcıya gösterir.

Örneğin bu HTML kodu:

```HTML
<!DOCTYPE html>
<html>

<head>
    <title>Sayfa Başlığı</title>
</head>

<body>
    <h1>Sayfa Başlığı</h1>
    <p>Merhaba Dünya!</p>
</body>

</html>
```

Çıktısı bu şekilde olacaktır:

![[screencapture-file-D-Repos-html-example-html-2024-10-04-17_21_54.png]]

## HTML Sayfa Yapısı
---
HTML'i iç içe yerleştirilmiş kutucuklar olarak düşünebilirsiniz, böyle düşündüğünüzde en büyük kutu `html` etiketi olurken diğer tüm kutucuklar onun içinde olacaktır.

<div class="ws-grey" style="width:99%;border:1px solid grey;padding:3px;margin:0;">&lt;html&gt;
<div style="width:90%;border:1px solid grey;padding:3px;margin:20px">&lt;head&gt;
<div style="width:90%;border:1px solid grey;padding:5px;margin:20px">&lt;title&gt;Sayfa Başlığı&lt;/title&gt;
</div>
&lt;/head&gt;
</div>
<div class="ws-grey" style="width:90%;border:1px solid grey;padding:3px;margin:20px;">&lt;body&gt;
<div class="w3-white" style="width:90%;border:1px solid grey;padding:3px;margin:20px;">
<div style="width:90%;border:1px solid grey;padding:5px;margin:20px">&lt;h1&gt;Sayfa Başlığı&lt;/h1&gt;</div>
<div style="width:90%;border:1px solid grey;padding:5px;margin:20px">&lt;p&gt;Merhaba Dünya!&lt;/p&gt;</div>
</div>
&lt;/body&gt;
</div>
&lt;/html&gt;
</div>

> **NOT:** `<body>` etiketinin içindeki içerik tarayıcıda görüntülenecektir. `<title>` öğesinin içindeki içerik tarayıcının başlık çubuğunda veya sayfanın sekmesinde gösterilecektir.