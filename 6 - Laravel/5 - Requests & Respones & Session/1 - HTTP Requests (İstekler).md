# `#` Giriş
---
Laravel'ın `Illuminate\Http\Request` sınıfı, uygulamanız tarafından işlenen mevcut HTTP isteğiyle etkileşimde bulunmanın ve istekle birlikte gönderilen girişleri, çerezleri ve dosyaları almanın nesne tabanlı bir yolunu sağlar.

# `#` İstekle (Request) Etkileşime Girmek
---

## İsteğe (Request) Erişme

Laravel service container tarafından otomatik olarak enjekte edilecek gelen istek örneği için bağımlılık enjeksiyonu yoluyla mevcut HTTP isteğinin bir örneğini elde etmek için rotanızın closure'na veya controller metoduna `Illuminate\Http\Request` sınıfını tip belirtimi olarak kullanmalısınız.

```php
<?php
 
namespace App\Http\Controllers;
 
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
 
class UserController extends Controller
{
    /**
     * Store a new user.
     */
    public function store(Request $request): RedirectResponse
    {
        $name = $request->input('name');
 
        // Store the user...
 
        return redirect('/users');
    }
}
```

Bahsedildiği gibi, bir rota kapanışında `Illuminate\Http\Request` sınıfını da yazabilirsiniz. Service container, closure çalıştırıldığında gelen isteği otomatik olarak kapanışa enjekte edecektir.

```php
Route::get('/', function (Request $request) {
    // ...
});
```

### Bağımlılık Enjeksiyonu ve Rota Parametreleri

Eğer controller metodunuz aynı zamanda bir rota parametresi içeriyorsa, önce bağımlılıklarınız ardından da rota parametrelerinizi listeleyin. Örneğin, rota aşağıdaki gibi tanımlanmışsa:

```php
Route::put('/user/{id}', [UserController::class, 'update']);
```

`Illuminate\Http\Request`'i hala tür belirteci olarak kullanabilir ve controller metodunuzu aşağıdaki gibi tanımlayarak `id` rota parametrenize erişebilirsiniz:

```php
public function update(Request $request, string $id): RedirectResponse
    {
        // Update the user...
 
        return redirect('/users');
    }
```

## İsteğin (Request) Yolu (Path), Ana Makine (Host), Metodu (Method) 

`Illuminate\Http\Request` örneği, gelen HTTP isteğini incelemek için çeşitli metodlar sağlar ve `Symfony\Component\HttpFoundation\Request` sınıfını genişletir. Aşağıda birkaç önemli metodu tartışacağız.

### İstek Yolunu Geri Döndürme

`path` metodu isteğin yol bilgisini döndürür. Örneğin, `http://example.com/foo/bar` adresine istekte bulunulursa, `path` metodu `foo/bar` değerini döndürecektir.

```php
$uri = $request->path();
```

#### Yolun / Rotanın İncelenmesi

`is` metodu, gelen istek yolunun belirli bir pattern'la eşleşip eşleşmediğini doğrulamanıza olanak sağlar. Bu metodu kullanırken `*` karakterini joker karakter olarak kullanabilirsiniz.

```php
if ($request->is('admin/*')) {
    // ...
}
```

`routeIs` metodunu kullanarak, gelen isteğin isimlendirilmiş bir rota ile eşleşip eşleşmediğini belirleyebilirsiniz.

```php
if ($request->routeIs('admin.*')) {
    // ...
}
```

### İstek URL'sini Döndürme

Gelen istek için tam URL'yi almak için `url` veya `fullUrl` metodlarını kullanabilirsiniz. `url` metodu sorgu dizisi olmadan URL'yi döndürecektir, `fullUrl` metodu ise sorgu dizisini içerir.

```php
$url = $request->url();
 
$urlWithQueryString = $request->fullUrl();
```

Eğer mevcut URL'ye sorgu dizesi verileri eklemek isterseniz, `fullUrlWithQuery` metodunu çağırabilirsiniz. Bu metod, verilen sorgu dizesi değişkenlerinin dizisini mevcut sorgu dizesiyle birleştirir.

```php
$request->fullUrlWithQuery(['type' => 'phone']);
```

Eğer belirli bir sorgu dizesi parametresi olmadan mevcut URL'yi almak isterseniz, `fullUrlWithoutQuery` metodunu kullanabilirsiniz.

```php
$request->fullUrlWithoutQuery(['type']);
```

## İstek Ana Makinesini (Host) Döndürme

Gelen isteğin "host"unu `host`, `httpHost` ve `schemeAndHttpHost` metodları aracılığıyla alabilirsiniz.

```php
$request->host();
$request->httpHost();
$request->schemeAndHttpHost();
```

## İstek Başlıkları (Header)

`Illuminate\Http\Request` örneğinden isteğin header'ini alabilirsiniz. Header istekte bulunmuyorsa, `null` değeri döndürülür. Ancak, `header` metodu, istekte bulunmayan bir header için döndürülecek isteğe bağlı ikinci bir argümanı kabul eder.

```php
$value = $request->header('X-Header-Name');
 
$value = $request->header('X-Header-Name', 'default');
```

`hasHeader` metodu, isteğin belirli bir header içerip içermediğini belirlemek için kullanılabilir.

```php
if ($request->hasHeader('X-Header-Name')) {
    // ...
}
```

Kolaylık sağlamak için, `bearerToken` metodu `Authorization` header'inden bir taşıyıcı belirteci almak için kullanılabilir. Eğer böyle bir header yoksa, boş bir dize döndürülür.

```php
$token = $request->bearerToken();
```

## İsteğin IP Adresi

`ip` metodu, uygulamanıza istekte bulunan istemcinin IP adresini almak için kullanılabilir.

```php
$ipAddress = $request->ip();
```

Eğer proxy'ler tarafından iletilen tüm istemci IP adreslerini içeren bir IP adresleri dizisi almak isterseniz, `ips` metodunu kullanabilirsiniz. "Orijinal" istemci IP adresi dizi sonunda olacaktır.

```php
$ipAddresses = $request->ips();
```

Genel olarak, IP adresleri güvenilmez, kullanıcı tarafından kontrol edilen girişler olarak kabul edilmeli ve sadece bilgilendirme amaçlı kullanılmalıdır.

## Content Negotiation

Laravel, `Accept` başlığı aracılığıyla gelen isteğin istenen içerik türlerini incelemek için birkaç metot sağlar. İlk olarak, `getAcceptableContentTypes` metodu istek tarafından kabul edilen tüm içerik tiplerini içeren bir dizi döndürecektir:

```php
$contentTypes = $request->getAcceptableContentTypes();
```

`accepts` metodu bir dizi içerik türünü kabul eder ve içerik türlerinden herhangi biri istek tarafından kabul edilirse `true` değerini döndürür. Aksi takdirde `false` döndürülür:

```php
if ($request->accepts(['text/html', 'application/json'])) {
    // ...
}
```

Belirli bir içerik türü dizisinden hangi içerik türünün istek tarafından en çok tercih edildiğini belirlemek için `prefers` metodunu kullanabilirsiniz. Sağlanan içerik türlerinden hiçbiri istek tarafından kabul edilmiyorsa, `null` döndürülür:

```php
$preferred = $request->prefers(['text/html', 'application/json']);
```

Birçok uygulama yalnızca HTML veya JSON sunduğundan, gelen isteğin bir JSON yanıtı bekleyip beklemediğini hızlı bir şekilde belirlemek için `expectsJson` metodunu kullanabilirsiniz:

```php
if ($request->expectsJson()) {
    // ...
}
```

## PSR-7 Requests

PSR-7 standardı, istekler ve yanıtlar dahil olmak üzere HTTP mesajları için arayüzleri belirtir. Eğer bir Laravel isteği yerine PSR-7 isteğinin bir örneğini elde etmek isterseniz, öncelikle birkaç kütüphane yüklemeniz gerekecektir. Laravel, tipik Laravel isteklerini ve yanıtlarını PSR-7 uyumlu uygulamalara dönüştürmek için Symfony HTTP Mesaj Köprüsü bileşenini kullanır:

```shell
composer require symfony/psr-http-message-bridge
composer require nyholm/psr7
```

Bu kütüphaneleri yükledikten sonra, rota kapatma veya controller metodunuzdaki istek arayüzünü yazarak bir PSR-7 isteği elde edebilirsiniz:

```php
use Psr\Http\Message\ServerRequestInterface;
 
Route::get('/', function (ServerRequestInterface $request) {
    // ...
});
```

> Bir rota veya controllerdan bir PSR-7 yanıt örneği döndürürseniz, otomatik olarak bir Laravel yanıt örneğine dönüştürülecek ve framework tarafından görüntülenecektir.

# `#` Input (Girdiler)
---
## Girdileri Yakalama

### Bütün Girdi Verilerini Yakalama

Gelen isteğin tüm girdi verilerini, `all` metodunu kullanarak bir dizi olarak alabilirsiniz. Bu metod, gelen isteğin bir HTML formundan mı yoksa bir XHR isteğinden mi geldiğine bakılmaksızın kullanılabilir.

```php
$input = $request->all();
```

`collect` metodunu kullanarak, gelen isteğin tüm girdi verilerini bir koleksiyon (collection) olarak alabilirsiniz.

```php
$input = $request->collect();
```

`collect` metodu, gelen isteğin girdisinin bir koleksiyon (collection) olarak bir alt kümesini almanıza olanak sağlar.

```php
$request->collect('users')->each(function (string $user) {
    // ...
});
```

### Girdi Verisini Yakalama

`Illuminate\Http\Request` örneğinizden, istekte hangi HTTP fiili kullanıldığı fark etmeksizin tüm `user` girdisine erişebilirsiniz. HTTP fiilinden bağımsız olarak, `user` girdisini almak için `input` metodu kullanılabilir:

```php
$name = $request->input('name');
```

İkinci argüman olarak `input` metoduna bir varsayılan değer geçirebilirsiniz. Bu değer, istenen girdi değeri istekte bulunulan yerde bulunmuyorsa döndürülür:

```php
$name = $request->input('name', 'Sally');
```

Dizi girdilerini içeren formlarla çalışırken, dizilere erişmek için "nokta" notasyonunu kullanın:

```php
$name = $request->input('products.0.name');
 
$names = $request->input('products.*.name');
```

Tüm girdi değerlerini birleşik bir dizi olarak almak için `input` metodunu herhangi bir parametre olmadan çağırabilirsiniz.

```php
$input = $request->input();
```

### Sorgu Dizesinden Girdi Verisi Yakalama

`input` metodu, sorgu dizesi de dahil olmak üzere tüm istek türünden değerleri alırken, `query` metodu yalnızca sorgu dizesinden değerleri alır:

```php
$name = $request->query('name');
```

Eğer istenen sorgu dizesi değeri veri mevcut değilse, bu metodun ikinci argümanı döndürülür:

```php
$name = $request->query('name', 'Helen');
```

Tüm sorgu dizesi değerlerini bir ilişkisel dizi olarak almak için herhangi bir argüman olmadan `query` metodunu çağırabilirsiniz.

```php
$query = $request->query();
```

### JSON Formatındaki Girdi Verilerini Yakalama

Uygulamanıza JSON istekleri gönderirken, isteğin `Content-Type` başlığı doğru şekilde `application/json` olarak ayarlandığı sürece JSON verilerine `input` metoduyla erişebilirsiniz. JSON dizileri / nesneleri içinde yer alan değerleri almak için "nokta" sözdizimini bile kullanabilirsiniz.

```php
$name = $request->input('user.name');
```

### Dizesel Girdi Verilerini Yakalama

İstek verilerini ilkel bir dize (string) olarak almak yerine, `Illuminate\Support\Stringable` sınıfının bir örneği olarak istek verilerini almak için `string` metodunu kullanabilirsiniz.

```php
$name = $request->string('name')->trim();
```

### Boolean Girdi Verilerini Yakalama

HTML'de onay kutuları gibi elementlerle uğraşırken, uygulamanız gerçekte dizeler olan "true" değerler alabilir. Örneğin, "true" veya "on". Kolaylık sağlamak için, bu değerleri boolean olarak almak için `boolean` metodunu kullanabilirsiniz. `boolean` metoddu, `1`, `"1"`, `true`, `"true"`, `"on"` ve `"yes"` için `true` değerini döndürür. Diğer tüm değerler `false` olarak dönecektir:

```php
$archived = $request->boolean('archived');
```

### Tarih Girdi Verilerini Yakalama

Kolaylık sağlamak için, tarih / saat içeren giriş değerleri, `date` metodunu kullanarak Carbon örnekleri olarak alınabilir. İstek, belirtilen isimle bir giriş değeri içermiyorsa, null döndürülür.

```php
$birthday = $request->date('birthday');
```

`date` metodu tarafından kabul edilen ikinci ve üçüncü argümanlar, sırasıyla tarihin formatını ve zaman dilimini belirtmek için kullanılabilir.

```php
$elapsed = $request->date('elapsed', '!H:i', 'Europe/Madrid');
```

Eğer giriş değeri mevcut olsa da geçersiz bir formatta ise, bir `InvalidArgumentException` fırlatılacaktır; bu nedenle, `date` metodunu çağırmadan önce girişi doğrulamanız önerilir.

### Enum Girdi Verilerini Yakalama

PHP enum'lere karşılık gelen girdi değerleri de istekten alınabilir. Eğer istek, verilen isme sahip bir girdi değeri içermiyorsa veya enumun bir eşleşen değeri yoksa, `null` döndürülür. `enum` metodu, ilk ve ikinci argümanları olarak girdi değerinin adını ve enum sınıfını kabul eder.

```php
use App\Enums\Status;
 
$status = $request->enum('status', Status::class);
```

### Dinamik Özelliklerin Girdilerini Yakalama

`Illuminate\Http\Request` örneği üzerinde dinamik özellikler kullanarak kullanıcı girişine erişebilirsiniz. Örneğin, uygulamanızın formlarından biri bir `name` alanı içeriyorsa, alanın değerine şu şekilde erişebilirsiniz:

```php
$name = $request->name;
```

Dinamik özellikler kullanılırken, Laravel önce istek yükünde parametrenin değerini arayacaktır. Eğer bulunamazsa, Laravel eşleşen rota parametrelerindeki alana bakacaktır.

### Girdi Verilerinin Bir Kısmını Yaklama

Girdi verilerinin bir alt kümesini almanız gerekiyorsa, `only` ve `except` metodlarını kullanabilirsiniz. Bu metodların her ikisi de tek bir dizi veya dinamik bir argüman listesini kabul eder.

```php
$input = $request->only(['username', 'password']);
 
$input = $request->only('username', 'password');
 
$input = $request->except(['credit_card']);
 
$input = $request->except('credit_card');
```

`only` metodu, istediğiniz tüm anahtar / değer çiftlerini döndürür; ancak istekte bulunmayan anahtar / değer çiftlerini döndürmez.

## Girdi Durumları

Bir değerin istekte bulunup bulunmadığını belirlemek için `has` metodunu kullanabilirsiniz. `has` metodu, değerin istekte bulunması durumunda `true` değerini döndürür.

```php
if ($request->has('name')) {
    // ...
}
```

Argümana bir dizi geçirilirse, `has` metodu belirtilen değerlerin hepsinin var olup olmadığını belirleyecektir:

```php
if ($request->has(['name', 'email'])) {
    // ...
}
```

`hasAny` metodu, belirtilen değerlerin herhangi birinin mevcut olması durumunda `true` değerini döndürür:

```php
if ($request->hasAny(['name', 'email'])) {
    // ...
}
```

`whenHas` metodu, istekte bir değer varsa verilen closure'u çalıştıracaktır.

```php
$request->whenHas('name', function (string $input) {
    // ...
});
```

Eğer belirtilen değer istekte bulunmuyorsa, `whenHas` metoduna ikinci bir closure geçilebilir ve bu closure çalıştırılacaktır:

```php
$request->whenHas('name', function (string $input) {
    // name değer girilirse...
}, function () {
    // name değer girilmezse...
});
```

Eğer bir değerin istekte bulunup bulunmadığını ve boş bir dize olmadığını belirlemek isterseniz, `filled` metodunu kullanabilirsiniz.

```php
if ($request->filled('name')) {
    // ...
}
```

`anyFilled` metodu, belirtilen değerlerin herhangi birinin boş bir dize olmadığı durumda `true` değerini döndürür.

```php
if ($request->anyFilled(['name', 'email'])) {
    // ...
}
```

`whenFilled` meotdu, istekte bir değer varsa ve boş bir dize değilse verilen closure'u çalıştıracaktır.

```php
$request->whenFilled('name', function (string $input) {
    // ...
});
```

Eğer belirtilen değer "doldurulmamış" ise, `whenFilled` metoduna ikinci bir closure geçirilebilir ve bu closure çalıştırılacaktır:

```php
$request->whenFilled('name', function (string $input) {
    // name değeri doldurulmuşsa...
}, function () {
    // name değeri doldurulmamışsa...
});
```

Verilen bir anahtarın istekte bulunmadığı durumu belirlemek için, `missing` ve `whenMissing` metodlarını kullanabilirsiniz.

```php
if ($request->missing('name')) {
    // ...
}
 
$request->whenMissing('name', function (array $input) {
    // name bulunuamazsa...
}, function () {
    // name bulunursa...
});
```

## Girdi Birleştirme (Overwrite)

Bazen isteğin mevcut giriş verilerine ek olarak ek girişi manuel olarak birleştirmeniz gerekebilir. Bunun için `merge` metodunu kullanabilirsiniz. Eğer belirli bir giriş anahtarı istekte zaten mevcutsa, `merge` metoduna sağlanan veri tarafından üzerine yazılacaktır.

```php
$request->merge(['votes' => 0]);
```

`mergeIfMissing` metodu, isteğin giriş verileri içinde ilgili anahtarlar henüz mevcut değilse, girişi isteğe birleştirmek için kullanılabilir.

```php
$request->mergeIfMissing(['votes' => 0]);
```

## Eski Girdi

Laravel, bir isteğin girişini bir sonraki istekte saklamanıza olanak tanır. Bu özellik, doğrulama hataları tespit edildikten sonra formları tekrar doldurmak için özellikle kullanışlıdır. Bununla birlikte, Laravel'in dahili doğrulama özelliklerini kullanıyorsanız, bu oturum girişi flaşlama metodlarını manuel olarak kullanmanıza gerek olmayabilir, çünkü Laravel'in bazı dahili doğrulama araçları bunları otomatik olarak çağırır.

### Girdiyi Oturuma Kaydetme

`Illuminate\Http\Request` sınıfındaki flash metodu, mevcut girdiui oturuma kaydederek kullanıcının uygulamaya yaptığı sonraki istekte kullanılabilir hale getirir.

```php
$request->flash();
```

`flashOnly` ve `flashExcept` metodlarını kullanarak istek verilerinin bir alt kümesini oturuma flaşlayabilirsiniz. Bu metodlar, şifre gibi hassas bilgileri oturumdan uzak tutmak için kullanışlıdır.

```php
$request->flashOnly(['username', 'email']);
 
$request->flashExcept('password');
```

### Eski Girdileri Gönderme

Çoğu zaman oturuma girişi flaşlamak ve ardından önceki sayfaya yönlendirmek isteyeceğiniz için, giriş flaşlamayı `withInput` metodunu kullanarak kolayca bir yönlendirmeye zincirleyebilirsiniz.

```php
return redirect('form')->withInput();
 
return redirect()->route('user.create')->withInput();
 
return redirect('form')->withInput(
    $request->except('password')
);
```

### Eski Girdiyi Alma

Önceki isteğin flaşlanmış girdisini almak için `Illuminate\Http\Request`'in bir örneğinde `old` metodunu çağırın. `old` metodu, önceden flaşlanmış girdi verilerini oturumdan çekecektir.

```php
$username = $request->old('username');
```

Laravel ayrıca bir global `old` yardımcısı sağlar. Bir Blade şablonunda `old` girdi görüntülüyorsanız, formu tekrar doldurmak için `old` yardımcısını kullanmak daha uygun olacaktır. Verilen alan için eski girdi bulunmuyorsa, null döndürülür.

```php
<input type="text" name="username" value="{{ old('username') }}">
```

## Cookies (Çerezler)

### Çerezleri Yakalama

Laravel frameworkü tarafından oluşturulan tüm çerezler şifrelenir ve bir kimlik doğrulama koduyla imzalanır, bu da istemci tarafından değiştirildiyse geçersiz kabul edilecekleri anlamına gelir. Bir istekten çerez değerini almak için `Illuminate\Http\Request` örneğindeki `cookie` metodunu kullanın.

```php
$value = $request->cookie('name');
```

## Girdi Düzenleme ve Normalleştirme

Varsayılan olarak, Laravel uygulamanızın global middlleware yığınına `Illuminate\Foundation\Http\Middleware\TrimStrings` ve `Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull` middleware'lerini dahil eder. Bu middleware istekteki tüm gelen dize girdilerini otomatik olarak kırpar ve boş dize alanlarını `null`'a dönüştürür. Bu sayede rotalarınızda ve denetleyicilerinizde bu normalleştirme endişeleriyle uğraşmanız gerekmez.

### Girdi Normalleştirmesini Devre Dışı Bırakma

Bu davranışı tüm istekler için devre dışı bırakmak isterseniz, uygulamanızın `bootstrap/app.php` dosyasında `$middleware->remove` metodunu çağırarak iki ara yazılımı uygulamanızın ara yazılım yığınından kaldırabilirsiniz:

```php
use Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull;
use Illuminate\Foundation\Http\Middleware\TrimStrings;
 
->withMiddleware(function (Middleware $middleware) {
    $middleware->remove([
        ConvertEmptyStringsToNull::class,
        TrimStrings::class,
    ]);
})
```

Uygulamanıza gelen isteklerin bir alt kümesi için dize trim'lemeyi ve boş dize dönüştürmeyi devre dışı bırakmak isterseniz, uygulamanızın `bootstrap/app.php` dosyasında `trimStrings` ve `convertEmptyStringsToNull` ara katman metodlarını kullanabilirsiniz. Her iki metot da, giriş normalleştirmesinin atlanıp atlanmayacağını belirtmek için `true` veya `false` döndürmesi gereken bir dizi closure kabul eder:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->convertEmptyStringsToNull(except: [
        fn (Request $request) => $request->is('admin/*'),
    ]);
 
    $middleware->trimStrings(except: [
        fn (Request $request) => $request->is('admin/*'),
    ]);
})
```

# `#` Files (Dosyalar)
---

## Dosya Yüklemesini Yakalama

`Illuminate\Http\Request` örneğinden yüklenmiş dosyaları `file` metodu veya dinamik özellikler kullanarak alabilirsiniz. `file` metodu, PHP `SplFileInfo` sınıfını genişleten `Illuminate\Http\UploadedFile` sınıfının bir örneğini döndürür ve dosya ile etkileşim için çeşitli metodlar sağlar.

```php
$file = $request->file('photo');
 
$file = $request->photo;
```

Bir dosyanın istekte bulunup bulunmadığını `hasFile` metodunu kullanarak belirleyebilirsiniz.

```php
if ($request->hasFile('photo')) {
    // ...
}
```

### Başarılı Bir Yüklemeyi Kontrol Etme

Dosyanın mevcut olup olmadığını kontrol etmenin yanı sıra, dosyanın yükleme sırasında herhangi bir sorun olup olmadığını `isValid` metoduyla doğrulayabilirsiniz.

```php
if ($request->file('photo')->isValid()) {
    // ...
}
```

### Dosya Yolu ve Uzantısı

`UploadedFile` sınıfı ayrıca dosyanın tam yolunu ve uzantısını elde etmek için metodlar içerir. `extension` metodu, dosyanın içeriğine dayanarak dosyanın uzantısını tahmin etmeye çalışacaktır. Bu uzantı, istemci tarafından sağlanan uzantıdan farklı olabilir.

```php
$path = $request->photo->path();
 
$extension = $request->photo->extension();
```

## Yüklenen Dosyaları Saklama

Bir yüklenen dosyayı depolamak için genellikle yapılandırdığınız dosya sistemlerinden birini kullanırsınız. `UploadedFile` sınıfının bir store metodu vardır ve bu metod yüklenen bir dosyayı yerel dosya sisteminizdeki bir konuma veya Amazon S3 gibi bir bulut depolama konumuna taşır.

`store` metodu, dosyanın dosya sisteminin yapılandırılmış kök dizinine göre nereye depolanması gerektiğini kabul eder. Bu yol, bir dosya adı içermemelidir, çünkü dosya adı olarak hizmet edecek benzersiz bir kimlik otomatik olarak oluşturulacaktır.

`store` metodu, dosyanın depolanması için kullanılması gereken diskin adı için isteğe bağlı ikinci bir argümanı da kabul eder. Metod, dosyanın diskin köküne göre göreceli yolunu döndürecektir.

```php
$path = $request->photo->store('images');
 
$path = $request->photo->store('images', 's3');
```

Eğer otomatik olarak bir dosya adı oluşturmak istemiyorsanız, argüman olarak yol, dosya adı ve disk adını kabul eden `storeAs` metodunu kullanabilirsiniz.

```php
$path = $request->photo->storeAs('images', 'filename.jpg');
 
$path = $request->photo->storeAs('images', 'filename.jpg', 's3');
```

# `#` Güvenilir Proxy'leri Yapılandırma
---
Uygulamalarınızı TLS / SSL sertifikalarını sonlandıran bir load balancerların arkasında çalıştırırken, `url` yardımcısını kullanırken uygulamanızın bazen HTTPS bağlantıları oluşturmadığını fark edebilirsiniz. Genellikle bunun nedeni, uygulamanızın yük dengeleyicinizden 80 numaralı bağlantı noktası üzerinden trafik iletmesi ve güvenli bağlantılar oluşturması gerektiğini bilmemesidir.

Bunu çözmek için, Laravel uygulamanızda bulunan `Illuminate\Http\Middleware\TrustProxies` middleware'ini etkinleştirebilirsiniz, bu da uygulamanız tarafından güvenilmesi gereken load balancerları veya proxy'leri hızlı bir şekilde özelleştirmenize olanak tanır. Güvenilir proxy'leriniz, uygulamanızın `bootstrap/app.php` dosyasındaki `trustProxies` middleware metodu kullanılarak belirtilmelidir:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->trustProxies(at: [
        '192.168.1.1',
        '192.168.1.2',
    ]);
})
```

Güvenilen proxy'leri yapılandırmanın yanı sıra, güvenilmesi gereken proxy başlıklarını da yapılandırabilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->trustProxies(headers: Request::HEADER_X_FORWARDED_FOR |
        Request::HEADER_X_FORWARDED_HOST |
        Request::HEADER_X_FORWARDED_PORT |
        Request::HEADER_X_FORWARDED_PROTO |
        Request::HEADER_X_FORWARDED_AWS_ELB
    );
})
```

## Bütün Proxy'lere Güvenme

Amazon AWS veya başka bir "bulut" yük dengeleyici sağlayıcısı kullanıyorsanız, gerçek dengeleyicilerinizin IP adreslerini bilmiyor olabilirsiniz. Bu durumda, tüm proxy'lere güvenmek için `*` kullanabilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->trustProxies(at: '*');
})
```

# `#` Güvenilir Hostları Yapılandırma
---
Varsayılan olarak Laravel, HTTP isteğinin `Host` başlığının içeriğine bakmaksızın aldığı tüm isteklere yanıt verecektir. Buna ek olarak, `Host` başlığının değeri bir web isteği sırasında uygulamanıza mutlak URL'ler oluşturulurken kullanılacaktır.

Tipik olarak, Nginx veya Apache gibi web sunucunuzu, uygulamanıza yalnızca belirli bir ana bilgisayar adıyla eşleşen istekleri gönderecek şekilde yapılandırmalısınız. Ancak, web sunucunuzu doğrudan özelleştirme yeteneğiniz yoksa ve Laravel'e yalnızca belirli ana bilgisayar adlarına yanıt vermesi talimatını vermeniz gerekiyorsa, bunu uygulamanız için `Illuminate\Http\Middleware\TrustHosts` middleware'ini etkinleştirerek yapabilirsiniz.

`TrustHosts` middleware'ini etkinleştirmek için, uygulamanızın `bootstrap/app.php` dosyasında `trustHosts` middleware metodunu çağırmalısınız. Bu metodun `at` bağımsız değişkenini kullanarak, uygulamanızın yanıt vermesi gereken ana bilgisayar adlarını belirtebilirsiniz. Diğer `Host` başlıklarına sahip gelen istekler reddedilecektir:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->trustHosts(at: ['laravel.test']);
})
```

Varsayılan olarak, uygulamanın URL'sinin alt alan adlarından gelen isteklere de otomatik olarak güvenilir. Bu davranışı devre dışı bırakmak isterseniz, `subdomains` bağımsız değişkenini kullanabilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->trustHosts(at: ['laravel.test'], subdomains: false);
})
```


