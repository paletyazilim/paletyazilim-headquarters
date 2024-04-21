# `#` Giriş
---

Route (Rota) nedir? Rota uygulamalarımız içerisinde URI’ye gelen istekleri işleyerek kullanıcılara bir View döndürmeye yarayan yapılandırma dosyalarıdır.

Laravel’de ise bir route tanımlamak ve route aracılığı ile çeşitli işlemleri yerine getirmek oldukça basittir, Laravel uygulamanızı oluşturduğunuz anda `routes/web.php` dosyanızın içerisinde böyle bir kod bulunmaktadır:

```php
Route::get('/', function () {
    return view('welcome');
});
```

# `#` Basit Rotalandırma
---
## Varsayılan Rota Dosyaları

Tüm Laravel rotaları, `routes` dizininde bulunan rota dosyalarınızda tanımlanır. Bu dosyalar uygulamanızın `App\Providers\RouteServiceProvider`'ı tarafından otomatik olarak yüklenir. `routes/web.php` dosyası web arayüzünüz için olan rotaları tanımlar. Bu rotalara, oturum durumu ve CSRF koruması gibi özellikler sağlayan `web` middleware grubu atanır. `routes/api.php` dosyasındaki rotalar durumsuzdur ve `api` middleware grubuna atanmıştır.

Çoğu uygulama için, `routes/web.php` dosyanızda rotalar tanımlayarak başlayacaksınız. `routes/web.php` dosyasında tanımlanan rotalara, tanımlanan rotanın URL'sini tarayıcınıza girerek erişebilirsiniz. Örneğin, tarayıcınızda `http://example.com/user` adresine giderek aşağıdaki rotaya erişebilirsiniz:

```php
Route::get('/user', function () {
    return 'Merhaba kullanıcı!';
});
```

`routes/api.php` dosyasında tanımlanan rotalar `RouteServiceProvider` tarafından bir rota grubu içinde iç içe yerleştirilir. Bu grup içinde `/api` URI öneki otomatik olarak uygulanır, böylece dosyadaki her rotaya manuel olarak uygulamanız gerekmez. `RouteServiceProvider` sınıfınızı değiştirerek öneki ve diğer rota grubu seçeneklerini değiştirebilirsiniz.

### API Rotalar

Uygulamanız aynı zamanda durum bilgisi olmayan bir API sunacaksa, `install:api` Artisan komutunu kullanarak API yönlendirmesini etkinleştirebilirsiniz:

```shell
php artisan install:api
```

`install:api` komutu, üçüncü taraf API tüketicilerini, SPA'ları veya mobil uygulamaları doğrulamak için kullanılabilecek sağlam ancak basit bir API token kimlik doğrulama koruması sağlayan Laravel Sanctum'u yükler. Ek olarak, `install:api` komutu `routes/api.php` dosyasını oluşturur:

```php
Route::get('/user', function (Request $request) {
    return $request->user();
})->middleware('auth:sanctum');
```

`routes/api.php` dosyasındaki rotalar durum bilgisi içermez ve `api` middleware grubuna atanır. Ayrıca, `/api` URI öneki bu rotalara otomatik olarak uygulanır, bu nedenle dosyadaki her rotaya manuel olarak uygulamanız gerekmez. Uygulamanızın `bootstrap/app.php` dosyasını düzenleyerek öneki değiştirebilirsiniz:

```php
->withRouting(
    api: __DIR__.'/../routes/api.php',
    apiPrefix: 'api/admin', // Ön ek değiştirme işlemi
    // ...
)
```

## Mevcut Yönlendirme Yöntemleri

Router, herhangi bir HTTP fiiline yanıt veren rotaları kaydetmenize olanak tanır:

```php
Route::get('/istek', function () { });
Route::post('/istek', function () { });
Route::put('/istek', function () { });
Route::patch('/istek', function () { });
Route::delete('/istek', function () { });
Route::options('/istek', function () { });
```

Bazen birden fazla HTTP fiiline yanıt veren bir rota kaydetmeniz gerekebilir. Bunu `match` metodunu kullanarak yapabilirsiniz. Ya da `any` metodunu kullanarak tüm HTTP fiillerine yanıt veren bir rota kaydedebilirsiniz:

```php
Route::match(['get', 'post'], '/istek', function () {
    return 'Yalnızca GET ve POST isteği yakalandı!';
});

// YA DA

Route::any('/istek', function () {
    return 'Bütün istekler yakalandı!';
});
```

Aynı URI'yi paylaşan birden fazla rota tanımlarken `get`, `post`, `put`, `patch`, `delete` ve `options` yöntemlerini kullanan rotalar `any`, `match` ve `redirect` yöntemlerini kullanan rotalardan önce tanımlanmalıdır. Bu, gelen isteğin doğru rota ile eşleştirilmesini sağlar:

```php
Route::get('/istek', function () {return 'GET isteği yakalandı!';});
Route::post('/istek', function () {return 'POST isteği yakalandı!'; });
Route::put('/istek', function () {return 'PUT isteği yakalandı!'; });
Route::patch('/istek', function () {return 'PATCH isteği yakalandı!'; });
Route::delete('/istek', function () {return 'DELETE isteği yakalandı!'; });
Route::options('/istek', function () {return 'OPTIONS isteği yakalandı!'; });

Route::match(['get', 'post'], '/istek', function () {
    return 'Yalnızca GET ve POST isteği yakalandı!';
});
```

## Dependency Injection (Bağımlılık Enjeksiyonu)

Rotanızın geri çağırma imzasında rotanızın gerektirdiği bağımlılıkları yazabilirsiniz. Bildirilen bağımlılıklar otomatik olarak çözümlenecek ve Laravel service container’i tarafından geri aramaya enjekte edilecektir. Örneğin, mevcut HTTP isteğinin rota geri çağırmanıza otomatik olarak enjekte edilmesi için `Illuminate\Http\Request` sınıfını işaretleyebilirsiniz:

```php
Route::get('/user', function (Request $request) {
    return 'Merhaba kullanıcı! İsteğiniz </br>' . $request;
});
```

## CSRF Koruması

`web` rotaları dosyasında tanımlanan `POST`, `PUT`, `PATCH` veya `DELETE` rotalarına işaret eden tüm HTML formlarının bir CSRF belirteci alanı içermesi gerektiğini unutmayın. Aksi takdirde istek reddedilecektir. CSRF koruması hakkında daha fazla bilgiyi ilerleyen konularda işleyeceğiz:

```php
<form method="POST" action="/profile">
    @csrf // CSRF Koruması
    ...
</form>
```

## Redirect (Yönlendirme) Rotaları

Başka bir URI'ye yönlendiren bir rota tanımlıyorsanız, `Route::redirect` metodunu kullanabilirsiniz. Bu metot, basit bir yönlendirme gerçekleştirmek için tam bir rota veya controller enetleyici tanımlamak zorunda kalmamanız için kullanışlı bir kısayol sağlar:

```php
Route::get('/new-page', function () {
    return 'Yenilenmiş sayfamıza hoşgeldiniz';
});

Route::redirect('/old-page', '/new-page');
```

Varsayılan olarak, `Route::redirect` `302` durum kodu döndürür. İsteğe bağlı üçüncü parametreyi kullanarak durum kodunu özelleştirebilirsiniz:

```php
Route::redirect('/old-page', '/new-page', 301);
```

Ya da `301` durum kodu döndürmek için `Route::permanentRedirect` metodunu kullanabilirsiniz:

```php
Route::permanentRedirect('/old-page','/new-page');
```

Yönlendirme rotalarında rota parametrelerini kullanırken, aşağıdaki parametreler Laravel tarafından rezervedir ve kullanılamaz: `destination` ve `status`.
