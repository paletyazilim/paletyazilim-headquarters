Supergloballer PHP 4.1.0'da tanıtılmıştır ve tüm kapsamlarda her zaman kullanılabilir olan yerleşik değişkenlerdir.

PHP'deki bazı ön tanımlı değişkenler, yani kapsamdan bağımsız olarak her zaman erişilebilirdirler - ve özel bir şey yapmanıza gerek kalmadan herhangi bir fonksiyondan, sınıftan veya dosyadan bunlara erişebilirsiniz.

PHP'nin Supergloballeri şunlardır:

- `$GLOBALS`
- `$_SERVER`
- `$_REQUEST`
- `$_POST`
- `$_GET`
- `$_FILES`
- `$_ENV`
- `$_COOKIE`
- `$_SESSION`

## `$GLOBALS`
---
`$GLOBALS` tüm global değişkenleri içeren bir dizidir.

Global değişkenler, herhangi bir kapsamdan erişilebilen değişkenlerdir.

En dış kapsamdaki değişkenler otomatik olarak global değişkenlerdir ve herhangi bir kapsam tarafından, örneğin bir fonksiyon içinde kullanılabilir.

Bir fonksiyon içinde global bir değişken kullanmak için ya `global` anahtar sözcüğü ile global olarak tanımlamanız ya da `$GLOBALS` sözdizimini kullanarak referans vermeniz gerekir.

```PHP title:'$GLOBALS Superglobaline ait bir örnek' hl:4
$x = 75;
  
function testFonksiyon() {
  echo $GLOBALS['x'];
}

testFonksiyon()
```

Bu, global değişkenlerin özellikle `global` olarak belirtilmeden kullanılabildiği diğer programlama dillerinden farklıdır. PHP'de `$GLOBALS` sözdizimi olmadan bir global değişkene atıfta bulunduğunuzda hiçbir şey almazsınız (veya hata alırsınız):

```PHP title:'Global değişkene atıfta bulunmamak' error:4
$x = 75;
  
function testFonksiyon() {
  echo $x;
}

testFonksiyon()
```

Global değişkenleri `global` anahtar sözcüğü ile global olarak tanımlayarak fonksiyonların içinde de bu değişkenlere başvurabilirsiniz.

```PHP title:'Bir fonksiyon içinde $x değerini global olarak tanımlanması' hl:4
$x = 75;
  
function testFonksiyon() {
  global $x;
  echo $x;
}

testFonksiyon()
```

### Global (Genel) Değişkenler Oluşturma
---
En dış kapsamda oluşturulan değişkenler `$GLOBALS` sözdizimi kullanılarak oluşturulmuş olsun ya da olmasın global değişkenlerdir:

```PHP title:'Global bir değişken oluşturma'
$x = 100;

echo $GLOBALS["x"];
echo $x;
```

Bir fonksiyon içinde oluşturulan değişkenler sadece o fonksiyona aittir, ancak `$GLOBALS` sözdizimini kullanarak bir fonksiyon içinde global değişkenler oluşturabilirsiniz:

```PHP title:'Bir fonksiyon içinde global bir değişken oluşturma'
function testFonksiyon() {
  $GLOBALS["x"] = 100;
}

testFonksiyon();

echo $GLOBALS["x"];
echo $x;
```

## `$_SERVER`
---
`$_SERVER`, başlıklar, yollar ve betik konumları hakkında bilgi tutan bir PHP süper global değişkenidir.

Aşağıdaki örnek `$_SERVER` içindeki bazı öğelerin nasıl kullanılacağını göstermektedir:

```PHP title:'$_SERVER Superglobaline ait bir örnek'
echo $_SERVER['PHP_SELF'];
echo $_SERVER['SERVER_NAME'];
echo $_SERVER['HTTP_HOST'];
echo $_SERVER['HTTP_REFERER'];
echo $_SERVER['HTTP_USER_AGENT'];
echo $_SERVER['SCRIPT_NAME'];
```

Aşağıdaki tablo `$_SERVER` içine girilebilecek en önemli öğeleri listelemektedir:

| Element/Kod | Açıklama |
| ---- | ---- |
| `$_SERVER['PHP_SELF']` | Yürütülmekte olan betiğin dosya adını döndürür. |
| `$_SERVER['GATEWAY_INTERFACE']` | Sunucunun kullandığı Ortak Ağ Geçidi Arayüzünün (CGI) sürümünü döndürür. |
| `$_SERVER['SERVER_ADDR']` | Ana sunucunun IP adresini döndürür. |
| `$_SERVER['SERVER_NAME']` | Ana sunucunun adını döndürür (www.php.net gibi) |
| `$_SERVER['SERVER_SOFTWARE']` | Sunucu tanımlama dizesini döndürür (Apache/2.2.24 gibi). |
| `$_SERVER['SERVER_PROTOCOL']` | Bilgi protokolünün adını ve revizyonunu döndürür (HTTP/1.1 gibi). |
| `$_SERVER['REQUEST_METHOD']` | Sayfaya erişmek için kullanılan istek yöntemini döndürür (POST gibi). |
| `$_SERVER['REQUEST_TIME']` | İsteğin başlangıcının zaman damgasını döndürür (1377687496 gibi). |
| `$_SERVER['QUERY_STRING']` | Sayfaya bir sorgu dizesi aracılığıyla erişiliyorsa sorgu dizesini döndürür. |
| `$_SERVER['HTTP_ACCEPT']` | Geçerli istekten "Accept" başlığını döndürür. |
| `$_SERVER['HTTP_ACCEPT_CHARSET']` | Geçerli istekten "Accept_Charset" başlığını döndürür (utf-8,ISO-8859-1 gibi). |
| `$_SERVER['HTTP_HOST']` | Geçerli isteğin Ana Bilgisayar başlığını döndürür. |
| `$_SERVER['HTTP_REFERER']` | Geçerli sayfanın tam URL'sini döndürür (tüm kullanıcı aracıları desteklemediği için güvenilir değildir). |
| `$_SERVER['HTTPS']` | Komut dosyası güvenli bir HTTP protokolü aracılığıyla mı sorgulanıyor. |
| `$_SERVER['REMOTE_ADDR']` | Kullanıcının geçerli sayfayı görüntülediği IP adresini döndürür. |
| `$_SERVER['REMOTE_HOST']`	 | Kullanıcının geçerli sayfayı görüntülediği Ana Bilgisayar adını döndürür. |
| `$_SERVER['REMOTE_PORT']`	 | Web sunucusuyla iletişim kurmak için kullanıcının makinesinde kullanılan bağlantı noktasını döndürür. |
| `$_SERVER['SCRIPT_FILENAME']`	 | Yürütülmekte olan betiğin mutlak yol adını döndürür. |
| `$_SERVER['SERVER_ADMIN']`	 | Web sunucusu yapılandırma dosyasında `SERVER_ADMIN` yönergesine verilen değeri döndürür (betiğiniz bir sanal hostta çalışıyorsa, bu sanal host için tanımlanan değer olacaktır) (birisi@xyz.com gibi).<br> |
| `$_SERVER['SERVER_PORT']`	 | Web sunucusu tarafından iletişim için kullanılan sunucu makinesindeki bağlantı noktasını döndürür (80 gibi)<br> |
| `$_SERVER['SERVER_SIGNATURE']`	 | Sunucu tarafından oluşturulan sayfalara eklenen sunucu sürümünü ve sanal ana bilgisayar adını döndürür. |
| `$_SERVER['PATH_TRANSLATED']`	 | Geçerli betiğin dosya sistemi tabanlı yolunu döndürür. |
| `$_SERVER['SCRIPT_NAME']`	 | Geçerli betiğin yolunu döndürür. |
| `$_SERVER['SCRIPT_URI']`	 | Geçerli sayfanın URI'sini döndürür. |

## `$_REQUEST`
---
`$_REQUEST` gönderilen form verilerini ve tüm çerez verilerini içeren bir PHP süper global değişkenidir.

Başka bir deyişle, `$_REQUEST`, `$_GET`, `$_POST` ve `$_COOKIE`'den veri içeren bir dizidir.

Bu verilere `$_REQUEST` anahtar sözcüğü ve ardından form alanının veya çerezin adı ile aşağıdaki gibi erişebilirsiniz:

```PHP title:'$_REQUEST öeğelerine erişim'
$_REQUEST['kullanici_adi']
```

### `$_POST` İsteklerinde `$_REQUEST` Kullanma
---
POST isteği genellikle bir HTML formundan gönderilen verilerdir.

İşte bir HTML formunun nasıl görünebileceğine dair bir örnek:

```HTML title:'Basit bir form örneği'
<html>
<body>

<form method="post" action="demo_request.php">
  Kullanıcı Adı: <input type="text" name="kullaniciadi">
  <input type="submit">
</form>

</body>
</html>
```

Kullanıcı gönder düğmesine tıkladığında, form verileri `<form>` etiketinin `action` niteliğinde belirtilen bir PHP dosyasına gönderilir.

Eylem dosyasında, giriş alanının değerini yakalamak için `$_REQUEST` değişkenini kullanabiliriz.

```PHP title:'$_REQUEST ile yakalanan veriyi değişkene atama'
$kadi = $_REQUEST['kullaniciadi'];
echo $kadi;
```

Aşağıdaki örnekte HTML formunu ve PHP kodunu aynı PHP dosyasına koyduk.

Ayrıca güvenlik için bazı ekstra dokunuşlar yaptık.

```PHP title:'POST yöntemi ile gönderilen verileri yakalama'
<html>
<body>

<form method="post" action="<?php echo $_SERVER['PHP_SELF'];?>">
  Kullanıcı Adı: <input type="text" name="kullaniciadi">
  <input type="submit">
</form>

<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
  $kadi = htmlspecialchars($_REQUEST['kullaniciadi']);
  if (empty($kadi)) {
    echo "Kullanıcı Adı Alanı Boş";
  } else {
    echo $name;
  }
}
?>

</body>
</html>
```

### `$_GET` İsteklerinde `$_REQUEST` Kullanma
---
GET isteği, yukarıdaki örnekte olduğu gibi, HTML `<form>` öğesinin `method` niteliği GET olarak ayarlanmış form gönderimleri olabilir.

GET istekleri ayrıca bir sorgu dizesinden (bir URL adresinden sonra eklenen bilgiler) veri olabilir.

İşte bir HTML bağlantısının sorgu dizesiyle birlikte nasıl görünebileceğine dair bir örnek:

```HTML
<html>
<body>

<a href="get_methodu.php?dil=PHP&web=php.net">Test $GET</a>

</body>
</html>
```

Kullanıcı bağlantıya tıkladığında, sorgu dizesi verileri `get_methodu.php` adresine gönderilir.

PHP dosyasında sorgu dizesinin değerini yakalamak için `$_REQUEST` değişkenini kullanabiliriz.

```PHP
<html>
<body>

<?php
echo $_REQUEST['subject'] . "'nin Orijinal Web Adresi: " . $_REQUEST['web'];
?>

</body>
</html>
```
## `$_POST`
---
`$_POST`, HTTP POST yöntemi aracılığıyla alınan değişkenlerin bir dizisini içerir.

HTTP POST yöntemi aracılığıyla değişkenleri göndermenin iki ana yolu vardır:

- HTML formları
- JavaScript HTTP istekleri

### HTML Formlarında `$_POST`
---
Bir HTML formu, formun `method` niteliği "`POST`" olarak ayarlanmışsa HTTP POST yöntemi aracılığıyla bilgi gönderir.

Bunu göstermek için basit bir HTML formu oluşturarak başlıyoruz:

```HTML title:'HTML ile HTTP POST isteği göndermek'
<html>
<body>

<form method="POST" action="kullanici_giris.php">
  Kullanıcı Adı: <input type="text" name="kullaniciadi">
  <input type="submit">
</form>

</body>
</html>
```

Kullanıcı gönder düğmesine tıkladığında, form verileri `<form>` etiketinin `action` niteliğinde belirtilen bir PHP dosyasına gönderilir.

Eylem dosyasında, giriş alanının değerini yakalamak için `$_POST` değişkenini kullanabiliriz.

```PHP title:'$_POST ile yakalanan veriyi değişkene atama'
$kadi = $_POST['kullaniciadi'];
echo $kadi;
```

Aşağıdaki örnekte HTML formunu ve PHP kodunu aynı PHP dosyasına koyduk.

Ayrıca güvenlik için bazı ekstra hatlar ekledik.

```PHP title:'POST yöntemi ile gönderilen verileri yakalama'
<html>
<body>

<form method="POST" action="<?php echo $_SERVER['PHP_SELF'];?>">
  Kullanıcı Adı: <input type="text" name="kullaniciadi">
  <input type="submit">
</form>

<?php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
  $kadi = htmlspecialchars($_POST['kullaniciadi']);
  if (empty($name)) {
    echo "Kullanıcı Adı Boş";
  } else {
    echo $name;
  }
}
?>

</body>
</html>
```

### JavaScript HTTP İsteklerinde `$_POST`
---
JavaScript'te bir HTTP isteği gönderirken, HTTP yönteminin POST olduğunu belirtebilirsiniz.

Bunu göstermek için bir HTTP isteği içeren bir JavaScript işlevi oluşturarak başlıyoruz:

```JS title:'JavaScript ile HTTP POST isteği göndermek' hl:5-7
function istekFonksiyonu() {
  const xhttp = new XMLHttpRequest();
  xhttp.open("POST", "kullanici_giris.php");
  xhttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
  xhttp.onload = function() {
    document.getElementById("test").innerHTML = this.responseText;
  }
  xhttp.send("kullaniciadi=Berat");
  }
}
```

Yukarıdaki kod:

- Bir HTTP isteği başlatın
- HTTP yöntemini POST olarak ayarlayın.
- Geçerli bir istek başlığı ayarlayın.
- İstek tamamlandığında çalıştırılacak bir fonksiyon oluşturun.
- HTTP isteğini `kullaniciadi` değişkeni `Berat` olarak ayarlanmış olarak gönderilir.
- İstek tamamlandığında yürütülecek olan işaretlenmiş kod parçası, alınan yanıtı `id="test"` ile bir HTML öğesine yazmaya çalışacaktır.

Böyle bir öğe ve ayrıca fonksiyonu çalıştıran bir düğme içeren bir HTML sayfası yapalım:

```HTML
<html>
<script>
function istekFonksiyonu() {
  const xhttp = new XMLHttpRequest();
  xhttp.open("POST", "kullanici_giris.php");
  xhttp.setRequestHeader("Content-type", "application/x-www-form-urlencoded");
  xhttp.onload = function() {
    document.getElementById("test").innerHTML = this.responseText;
  }
  xhttp.send("kullaniciadi=Berat");
  }
}
</script>
<body>

<button onclick="istekFonksiyonu()">Bana Tıkla!</button>

<h1 id="test"></h1>

</body>
</html>
```

Bu HTTP isteğini alan PHP dosyasında (`kullanici_giris.php`), `kullaniciadi` değişkenini almak için `$_POST` değişkenini kullanırız ve bunu bir yanıt olarak yazarız:

```PHP title:'JavaScript ile gönderilen değeri ekrana yazdırma'
$kadi = $_POST['kullaniciadi'];
echo $kadi;
```

## `$_GET`
---
`$_GET`, HTTP GET yöntemi aracılığıyla alınan değişkenlerden oluşan bir dizi içerir.

HTTP GET yöntemi aracılığıyla değişken göndermenin iki ana yolu vardır:

- URL'deki sorgu dizeleri
- HTML Formları

### URL'deki Sorgu Dizeleri
---
Sorgu dizesi, bir URL'nin sonuna eklenen verilerdir. Aşağıdaki bağlantıda, `?` işaretinden sonraki her şey sorgu dizesinin bir parçasıdır:

```HTML title:'Örnek bir URL sorgu dizesi'
<a href="kullanici_giris.php?kullaniciadi=Berat&sife=123456">Test $GET</a>
```

Yukarıdaki sorgu dizesi iki anahtar/değer çifti içerir ve her biri `&` ile ayrılır:

- `kullaniciadi=Berat`
- `sifre=123456`

PHP dosyasında sorgu dizesinin değerlerini yakalamak için `$_GET` superglobalini kullanabiliriz.

```PHP title:'URL sorgularını yakalamak'
<html>
<body>

<?php
echo "Kullanıcı Adı: " . $_GET['kullaniciadi'] . "<br> Şifre:" . $_GET['sifre'];
?>

</body>
</html>
```

### HTML Formlarında `$_GET`
---
Bir HTML formu, formun `method` niteliği "GET" olarak ayarlanmışsa HTTP GET yöntemi aracılığıyla bilgi gönderir.

Bunu göstermek için basit bir HTML formu oluşturarak başlıyoruz:

```HTML title:'HTML ile HTTP GET isteği göndermek'
<html>
<body>

<form action="kullanici_giris.php" method="GET">
  Kullanıcı Adı: <input type="text" name="kullaniciadi">
  E-posta: <input type="text" name="eposta">
  <input type="submit">
</form>

</body>
</html>
```

Kullanıcı gönder düğmesine tıkladığında, form verileri `<form>` etiketinin `action` niteliğinde belirtilen bir PHP dosyasına gönderilir.

Form alanları, girdilerinizle birlikte PHP dosyasına sorgu dizeleri olarak gönderilir:

```
kullanici_giris.php?kullaniciadi=Berat&eposta=birisi@ornek.com
```

Eylem dosyasında, giriş alanlarının değerini yakalamak için `$_GET` superglobalini kullanabiliriz.

```PHP title:'$_GET ile yakalanan veriyi değişkene atama'
<html>
<body>

Merhaba <?php echo $_GET["kullaniciadi"]; ?><br>
E-Postanız: <?php echo $_GET["eposta"]; ?>

</body>
</html>
```

>PHP formlarını işlerken GÜVENLİĞİ düşünün!
>
>Yukarıdaki örnek herhangi bir form doğrulaması içermez, sadece form verilerini nasıl gönderebileceğinizi ve alabileceğinizi gösterir.
>
>PHP formlarının güvenlik göz önünde bulundurularak işlenmesi hakkında daha fazla bilgiyi Form Doğrulama bölümünde bulabilirsiniz.
>
>Form verilerinin doğru şekilde işlenmesi, formunuzu bilgisayar korsanlarından ve spam gönderenlerden korumak için önemlidir!