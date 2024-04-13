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

Bazen birden fazla HTTP fiiline yanıt veren bir rota kaydetmeniz gerekebilir. Bunu `match` yöntemini kullanarak yapabilirsiniz. Ya da `any` yöntemini kullanarak tüm HTTP fiillerine yanıt veren bir rota kaydedebilirsiniz:

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

## View (Görüntü) Rotaları

Rotanızın yalnızca bir view döndürmesi gerekiyorsa, `Route::view` metodunu kullanabilirsiniz. `redirect` metodu gibi, bu metotta de tam bir rota veya controller tanımlamak zorunda kalmamanız için basit bir kısayol sağlar. `view` metodu ilk argüman olarak bir URI ve ikinci argüman olarak bir view adı kabul eder. Buna ek olarak, isteğe bağlı üçüncü bir bağımsız değişken olarak view’e aktarılacak bir veri dizisi sağlayabilirsiniz:

```php
Route::view('/gorunum', 'welcome');

Route::view('/gorunum', 'welcome', ['isim' => 'Berat']);
```

View rotalarında rota parametrelerini kullanırken, aşağıdaki parametreler Laravel tarafından rezervedir ve kullanılamaz: `view`, `data`, `status` ve `header`.

# `#` Parametreli Rota Tanımlama
---

## Gerekli (Zorunlu) Parametreler

Bazen rotanızdaki URI parametreleri yakalamanız gerekebilir. Örneğin, URL'den bir kullanıcının kimliğini yakalamanız gerekebilir. Bunu rotanıza parametre tanımlayarak yapabilirsiniz:

```php
Route::get('/user/{user_id}', function ($id) {
    return "Gelen kullanıcı ID'si $id";
});
```

Rotanızın gerektirdiği sayıda rota parametresi tanımlayabilirsiniz:

```php
Route::get('/user/{user_id}/{user_name}', function ($id, $name) {
    return "Gelen kullanıcı ID'si $id </br> İsim: $name";
});
```

Rota parametreleri her zaman `{}` parantezleri içinde yer alır ve alfabetik karakterlerden oluşmalıdır. Rota parametre adları içinde alt çizgi (`_`) de kabul edilebilir. Rota parametreleri, sıralarına göre rota geri çağrılarına / controller’lara enjekte edilir - rota geri çağrısının / controller’ın argümanlarının adları önemli değildir.

### Parametreler ve Dependecy Injection

Eğer rotanızın Laravel service container’lerinin rotanızın geri çağrısına otomatik olarak enjekte etmesini istediğiniz bağımlılıkları varsa, rota parametrelerinizi bağımlılıklarınızdan sonra listelemelisiniz:

```php
Route::get('/user/{user_id}/{user_name}', function (Request $request, $id, $name) {
    return "Gelen kullanıcı ID'si $id </br> İsim: $name </br> Gelen İstek: $request";
});
```

## Opsiyonel (İsteğe Bağlı) Parametreler

Bazen URI'de her zaman bulunmayan bir rota parametresi belirtmeniz gerekebilir. Bunu parametre adından sonra bir `?` işareti koyarak yapabilirsiniz. Rotanın ilgili değişkenine varsayılan bir değer verdiğinizden emin olun:

```php
Route::get('/user/{user_id}/{user_name?}', function ($id, $name = null) {
    return "Gelen kullanıcı ID'si $id </br> İsim: $name";
});
```

# `#` İsimlendirilmiş Rotalar
---

İsimlendirilmiş rotalar, belirli rotalar için URL'lerin veya yönlendirmelerin kolayca oluşturulmasını sağlar. name metodunu rota tanımına zincirleyerek bir rota için bir ad belirtebilirsiniz:

```php
Route::get('/dashboard/profile/settings', function() {
    return 'Kullanıcı ayar sekmesine hoşgeldiniz!';
})->name('user-settings');
```

Controller eylemleri için rota adları da belirtebilirsiniz:

```php
Route::get('/dashboard/profile/settings', [UserProfileController::class, 'show'])->name('user-settings');
```

Rota adları her zaman benzersiz olmalıdır. Farklı rotalar aynı isime sahip olamaz.

## İsimlendirilmiş Rotaya Yönlendirme İşlemi

Belirli bir rotaya bir isim atadıktan sonra, Laravel'in `route` ve `redirect` yardımcı fonksiyonları aracılığıyla URL'ler veya yönlendirmeler oluştururken rotanın adını kullanabilirsiniz:

```php
Route::get('/redirect-user', function () {

    // İsimlendirilmiş Rotanın URL'sini Alma
    $usersettings = route('user-settings');
    //https://www.example.com/dashboard/profile/settings

    // İsimlendirilmiş Rotaya Yönlendirme
    return redirect()->route('user-settings');

    // YA DA

    return to_route('user-settings');
});
```

İsimlendirilmiş rota parametrelere sahipse, parametreleri `route` metoduna ikinci bağımsız değişken olarak aktarabilirsiniz. Verilen parametreler otomatik olarak oluşturulan URL'ye doğru konumlarında eklenecektir:

```php
Route::get('/dashboard/profile/settings/{id}', function ($id) {
    return "Kullanıcı ayar sekmesine hoşgeldiniz! </br> ID: $id";
})->name('user-settings');

Route::get('/redirect-user', function () {
    return redirect()->route('user-settings', ['id' => 500]);
});
```

Diziye ek parametreler geçerseniz, bu anahtar / değer çiftleri otomatik olarak oluşturulan URL'nin sorgu dizesine eklenecektir:

```php
Route::get('/dashboard/profile/settings/{id}', function ($id) {
    return "Kullanıcı ayar sekmesine hoşgeldiniz! </br> ID: $id";
})->name('user-settings');

Route::get('/redirect-user', function () {
    return redirect()->route('user-settings', ['id' => 500,'isim' => 'berat']);
});
//https://www.example.com/dashboard/profile/settings/500?isim=berat
```

Bazen, geçerli yerel ayar gibi URL parametreleri için istek genelinde varsayılan değerler belirtmek isteyebilirsiniz. Bunu gerçekleştirmek için `URL::defaults` metodunu kullanabilirsiniz.

## Mevcut Rotanın İncelenmesi

Geçerli isteğin belirli bir adlandırılmış rotaya yönlendirilip yönlendirilmediğini belirlemek isterseniz, bir Route örneğinde `named` metodunu kullanabilirsiniz. Örneğin, bir rota middleware’in geçerli rota adını kontrol edebilirsiniz:

```php
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
/**
 * Handle an incoming request.
 *
 * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
 */
public function handle(Request $request, Closure $next): Response
{
    if ($request->route()->named('profile')) {
        // ...
    }
 
    return $next($request);
}
```

# `#` Rota Grupları
---
Rota grupları, middleware gibi rota niteliklerini her bir rotada tanımlamanıza gerek kalmadan çok sayıda rota arasında paylaşmanıza olanak tanır.

İç içe gruplar, niteliklerini üst gruplarıyla akıllıca "birleştirmeye" çalışır. Middleware ve `where` koşulları birleştirilirken adlar ve önekler eklenir. Ad alanı sınırlayıcıları ve URI öneklerindeki eğik çizgiler uygun olan yerlerde otomatik olarak eklenir.

## Middleware Grupları

Bir grup içindeki tüm rotalara middleware atamak için, grubu tanımlamadan önce `middleware` metodunu kullanabilirsiniz. Ara yazılımlar dizide listelendikleri sırayla çalıştırılır:

```php
Route::middleware(['birinci','ikinci'])->group(function () {

    Route::get('/', function() {
        // İlk önce birinci middleware'i ardından ikinci middleware'i kullanır...
    });

    Route::get('/user', function() {
        // İlk önce birinci middleware'i ardından ikinci middleware'i kullanır...
    });

});
```

## Controller Grupları

Bir grup rota aynı controller’i kullanıyorsa, gruptaki tüm rotalar için ortak controlleri tanımlamak üzere `controller` metodunu kullanabilirsiniz. Ardından, rotaları tanımlarken yalnızca çağırdıkları controller metodunu sağlamanız gerekir:

```php
Route::controller(OrderController::class)->group(function () {
    Route::get('/order', 'show');
    Route::get('/orders', 'store');
});
```

## Subdomain Grupları

Rota grupları subdomain yönlendirmesini işlemek için de kullanılabilir. Subdomain’lere tıpkı rota URI'ları gibi rota parametreleri atanabilir ve rotanızda veya controller’ınızda kullanmak üzere subdomain’in bir bölümünü yakalamanıza olanak tanır. Subdomain, grup tanımlanmadan önce `subdomain` metodu çağrılarak belirtilebilir:

```php
Route::domain('{users}.example.com')->group(function() {
    Route::get('/', function () {
        // ...
    });
});
```

Subdomain rotalarınızın erişilebilir olmasını sağlamak için, ana domain rotalarını kaydetmeden önce subdomain rotalarını kaydetmelisiniz. Bu, ana domain rotalarının aynı URI yoluna sahip subdomain rotalarının üzerine yazılmasını önleyecektir.

## Rota Önekleri

`prefix` metodu, gruptaki her bir rotayı belirli bir URI ile öneklemek için kullanılabilir. Örneğin, grup içindeki tüm rota URI'lerinin önüne `admin` eklemek isteyebilirsiniz:

```php
Route::prefix('admin')->group(function() {
    Route::get('/', function() {
    
        // "/admin/users" URL'si ile erişilir

        return 'Yönetici kullanıcı paneline hoşgeldiniz';
    });
});
```

### İsimlendirilmiş Rotalara Önek

`name` metodu, gruptaki her rota adının önüne belirli bir dize eklemek için kullanılabilir. Örneğin, gruptaki tüm rotaların adlarının önüne `admin` eklemek isteyebilirsiniz. Verilen dize, tam olarak belirtildiği gibi rota adının önüne eklenir, bu nedenle ön ekte sondaki `.` karakterini sağladığımızdan emin olacağız:

```php
Route::name('admin.')->group(function () {
    Route::get('/home', function () {
        return 'Yönetici kullanıcı paneline hoşgeldiniz';
    })->name('home');
});

Route::get('/redirect', function () {
    return redirect()->route('admin.home');
});
```

# `#` Rotalarınızı Listeleme
---

`route:list` Artisan komutu, uygulamanız tarafından tanımlanan tüm rotaların genel bir listesini kolayca sağlayabilir:

```shell
php artisan route:list
```

Varsayılan olarak, her rotaya atanan rota middleware `route:list` çıktısında görüntülenmeyecektir; ancak, komuta `-v` seçeneğini ekleyerek Laravel'e rota middleware ve middleware grup adlarını görüntülemesi talimatını verebilirsiniz:

```shell
php artisan route:list -v
 
# Middleware Gruplarını Genişletir
php artisan route:list -vv
```

Laravel'e sadece belirli bir URI ile başlayan rotaları göstermesi talimatını da verebilirsiniz:

```shell
php artisan route:list --path=api
```

Buna ek olarak, `route:list` komutunu çalıştırırken `--except-vendor` seçeneğini sağlayarak Laravel'e üçüncü taraf paketleri tarafından tanımlanan rotaları gizlemesi talimatını verebilirsiniz:

```shell
php artisan route:list --except-vendor
```

Aynı şekilde, `route:list` komutunu çalıştırırken `--only-vendor` seçeneğini sağlayarak Laravel'e sadece üçüncü taraf paketleri tarafından tanımlanan rotaları göstermesi talimatını da verebilirsiniz:

```shell
php artisan route:list --only-vendor
```

# `#` API Rotalar
---

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

# `#` Rotaları Özelleştirme
---