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