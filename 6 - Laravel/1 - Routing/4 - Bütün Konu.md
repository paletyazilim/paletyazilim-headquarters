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

## API Rotalar

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

## Rotalarınızı Listeleme

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

## Rotaları Özelleştirme

Varsayılan olarak, uygulamanızın rotaları `bootstrap/app.php` dosyası tarafından yapılandırılır ve yüklenir.

```php
<?php
 
use Illuminate\Foundation\Application;
 
return Application::configure(basePath: dirname(__DIR__))
    ->withRouting(
        web: __DIR__.'/../routes/web.php',
        commands: __DIR__.'/../routes/console.php',
        health: '/up',
    )->create();
```

Ancak, bazen uygulamanızın rotalarının bir alt kümesini içeren tamamen yeni bir dosya tanımlamak isteyebilirsiniz. Bunun için `withRouting` metoduna bir `then` closure'u sağlayabilirsiniz. Bu kapanış içinde, uygulamanız için gerekli olan ek rotaları kaydedebilirsiniz.

```php
use Illuminate\Support\Facades\Route;
 
->withRouting(
    web: __DIR__.'/../routes/web.php',
    commands: __DIR__.'/../routes/console.php',
    health: '/up',
    then: function () {
        Route::middleware('api')
            ->prefix('webhooks')
            ->name('webhooks.')
            ->group(base_path('routes/webhooks.php'));
    },
)
```

Veya, `withRouting` metoduna bir kullanma kapanı sağlayarak rota kaydının tam kontrolünü bile alabilirsiniz. Bu argüman geçirildiğinde, çerçeve tarafından hiçbir HTTP rota kaydedilmez ve tüm rotaları manuel olarak kaydetmek sizin sorumluluğunuzdadır.

```php
use Illuminate\Support\Facades\Route;
 
->withRouting(
    commands: __DIR__.'/../routes/console.php',
    using: function () {
        Route::middleware('api')
            ->prefix('api')
            ->group(base_path('routes/api.php'));
 
        Route::middleware('web')
            ->group(base_path('routes/web.php'));
    },
)
```

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

## RegEx Kısıtlamaları

Rota parametrelerinin formatını, bir rota örneği üzerinde `where` metodunu kullanarak sınırlayabilirsiniz. `where` metodu, parametrenin adını ve parametrenin nasıl sınırlanması gerektiğini tanımlayan bir RegEx kabul eder:

```php
Route::get('/user/{name}', function (string $name) {
    // ...
})->where('name', '[A-Za-z]+');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->where('id', '[0-9]+');
 
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```

Kolaylık sağlamak için, yaygın olarak kullanılan bazı düzenli ifade pattern'larına, rotalarınıza hızlı bir şekilde desen pattern'ı eklemenizi sağlayan yardımcı metodlar bulunmaktadır.

```php
Route::get('/user/{id}/{name}', function (string $id, string $name) {
    // ...
})->whereNumber('id')->whereAlpha('name');
 
Route::get('/user/{name}', function (string $name) {
    // ...
})->whereAlphaNumeric('name');
 
Route::get('/user/{id}', function (string $id) {
    // ...
})->whereUuid('id');
 
Route::get('/user/{id}', function (string $id) {
    //
})->whereUlid('id');
 
Route::get('/category/{category}', function (string $category) {
    // ...
})->whereIn('category', ['movie', 'song', 'painting']);
```

Gelen istek, rota pattern kısıtlamalarıyla eşleşmezse, 404 HTTP yanıtı döndürülür.

### Global Kısıtlama

Eğer bir rota parametresinin her zaman belirli bir RegEx ile sınırlanmasını isterseniz, `pattern` metodunu kullanabilirsiniz. Bu desenleri uygulamanızın `App\Providers\AppServiceProvider` sınıfının `boot` metoduna tanımlamanız gerekmektedir.

```php
use Illuminate\Support\Facades\Route;
 
/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    Route::pattern('id', '[0-9]+');
}
```

Pattern bir kez tanımlandıktan sonra, bu parametre adını kullanan tüm rotalara otomatik olarak uygulanır.

```php
Route::get('/user/{id}', function (string $id) {
    // Sadece {id} sayısal ise çalıştırılır...
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

# `#` Rotalarınıza Model Bağlama
---
Bir rota veya controller eylemine bir model kimliği enjekte ederken, genellikle bu kimliğe karşılık gelen modeli almak için veritabanını sorgulayacaksınız. Laravel rota model binding, model örneklerini doğrudan rotalarınıza otomatik olarak enjekte etmek için uygun bir yol sağlar. Örneğin, bir kullanıcının ID'sini enjekte etmek yerine, verilen ID ile eşleşen tüm `User` model örneğini enjekte edebilirsiniz.

## Implicit Binding (Kapsayıcı Bağlama)

Laravel, yönlendirme veya controller işlemlerinde tanımlanmış olan Eloquent modellerini otomatik olarak çözer. Bu, tür belirten değişken adları, bir yol segment adıyla eşleştiğinde gerçekleşir. Örneğin:

```php
Route::get('/users/{user}', function (User $user) {
    return $user->email;
});
```

`user` değişkeni `App\Models\User` Eloquent modeline sahip olduğundan ve değişken adı `{user}` ile eşleştiğinden URI segmenti, Laravel otomatik olarak istek URI'sındaki karşılık gelen değerle eşleşen bir ID'ye sahip model örneğini enjekte edecektir. Eğer veritabanında eşleşen bir model örneği bulunamazsa, otomatik olarak 404 HTTP yanıtı üretilecektir.

Elbette, controller metodlarını kullanılırken kapsayıcı bağlama da mümkündür. Yine, `{user}` URI segmenti, controller'da ki bir `App\Models\User` $user değişkeniyle eşleşir:

```php

// Rota Tanımı | web.php 

Route::get('/users/{user}', [UserController::class, 'show']);

// Controller Tanımı | UserController.php

public function show(User $user)
{
    return view('user.profile', ['user' => $user]);
}
```

## Soft Deleted Models

Genellikle, kapsayıcı model bağlama işlemi silinmiş olan modelleri almayacaktır. Ancak, kapsayıcı bağlama işlemini, rotanızın tanımına `withTrashed` metodunu zincirleyerek bu modelleri alması için talimat verebilirsiniz:

```php
Route::get('/users/{user}', function (User $user) {
    return $user->email;
})->withTrashed();
```

## (Custom Keys) Anahtar Özelleştirme

Bazen Eloquent modellerini `id` dışında bir sütun kullanarak çözümlemek isteyebilirsiniz. Bunu yapmak için, sütunu rota parametresi tanımında belirtebilirsiniz:

```php
Route::get('/user/{user:name}', function (User $user) {
    return "$user->email";
});
```

Model bağlamanın belirli bir model sınıfını alırken her zaman `id` dışında bir veritabanı sütunu kullanmasını istiyorsanız, Eloquent modelindeki `getRouteKeyName` metodunu geçersiz kılabilirsiniz:

```php
public function getRouteKeyName(): string
{
    return 'name';
}
```

## (Custom Keys) Özelleştirilmiş Anahtarlar ve (Scoping) Kapsamlar

Tek bir rota tanımında birden fazla Eloquent modelini kapsamlı olarak bağlarken, ikinci Eloquent modelini önceki Eloquent modelinin bir alt öğesi olacak şekilde kapsamlandırmak isteyebilirsiniz. Örneğin, belirli bir kullanıcı için `slug`'a göre bir blog gönderisini alan bu rota tanımını düşünün:

```php
Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    return $post;
});
```

İç içe rota parametresi olarak özel bir anahtarlı kapsamlı bağlama kullanıldığında, Laravel, üstteki ilişki adını tahmin etmek için kuralları kullanarak iç içe modeli üst tarafından almak için sorguyu otomatik olarak kapsamlandıracaktır. Bu durumda, `User` modelinin `Post` modelini almak için kullanılabilecek `posts` (rota parametresi adının çoğul hali) adında bir ilişkiye sahip olduğu varsayılacaktır.

İsterseniz, Laravel'e özel bir anahtar sağlanmadığında bile "child" bağları kapsaması talimatını verebilirsiniz. Bunu yapmak için, rotanızı tanımlarken `scopeBindings` metodunu çağırabilirsiniz:

```php
Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
    return $post;
})->scopeBindings();
```

Ya da tüm bir rota tanımları grubuna kapsamlandırılmış bağları kullanmaları talimatını verebilirsiniz:

```php
Route::scopeBindings()->group(function () {
    Route::get('/users/{user}/posts/{post}', function (User $user, Post $post) {
        return $post;
    });
});
```

Benzer şekilde, `withoutScopedBindings` metodunu çağırarak Laravel'e bağları kapsamaması için açıkça talimat verebilirsiniz:

```php
Route::get('/users/{user}/posts/{post:slug}', function (User $user, Post $post) {
    return $post;
})->withoutScopedBindings();
```

## Implict Enum Binding (Kapsayıcı Enum Tanımlaması)

PHP 8.1, Enum'lar için destek ekledi. Bu özelliği tamamlamak için, Laravel, rotanızın tanımında bir dize destekli Enum'u tip belirtimi olarak kullanmanıza izin verir ve Laravel, yalnızca o rota segmenti geçerli bir Enum değerine karşılık geliyorsa rotayı çağırır. Aksi takdirde, otomatik olarak bir 404 HTTP yanıtı döndürülür. Örneğin, aşağıdaki Enum verildiğinde:

```php
<?php
 
namespace App\Enums;
 
enum Category: string
{
    case Fruits = 'fruits';
    case People = 'people';
}
```

Eğer `{category}` rota segmenti `fruits` veya `people` ise yalnızca çağrılacak bir rota tanımlayabilirsiniz. Aksi takdirde, Laravel bir 404 HTTP yanıtı döndürecektir.

```php
Route::get('/categories/{category}', function (Category $category) {
    return $category->value;
});
```

## Explicit Binding (Belirgin Bağlama)

Model bağlamayı kullanmak için Laravel'in, geleneksel model bağlamasını kullanmanız gerekmez. Rota parametrelerinin modellere nasıl karşılık geldiğini açıkça da tanımlayabilirsiniz. Belirgin bir bağlama kaydetmek için, belirli bir parametrenin sınıfını belirtmek üzere router'ın `model` metodunu kullanın. Belirgin model bağlarınızı `AppServiceProvider` sınıfınızın `boot` metodunun başında tanımlamalısınız:

```php
public function boot() : void
    {
        Route::model('user', User::class);
    }
```

Ardından, `{user}` parametresi içeren bir rota tanımlayın:

```php
Route::get('/user/{user}', function (User $user) {
    //..
});
```

Tüm `{user}` parametrelerini `App\Models\User` modeline bağladığımız için, bu sınıfın bir örneği rotaya enjekte edilecektir. Örneğin, `users/1`'e yapılan bir istek, veritabanından 1 ID'ye sahip `User` örneğini enjekte edecektir.

Veritabanında eşleşen bir model örneği bulunamazsa, otomatik olarak 404 HTTP yanıtı oluşturulur.

## Customizing the Resolution Logic

Kendi model bağlama çözümleme mantığınızı tanımlamak isterseniz, `Route::bind` metodunu kullanabilirsiniz. `bind` metoduna geçtiğiniz closure, URI segmentinin değerini alacak ve rotaya enjekte edilecek sınıf örneğini döndürmelidir. Yine, bu özelleştirme işlemi uygulamanızın `AppServiceProvider`'ının `boot` metodunda gerçekleştirilmelidir

```php
public function boot(): void
{
    Route::bind('user', function (string $value) {
        return User::where('name', $value)->firstOrFail();
    });
}
```

Alternatif olarak, Eloquent modelinizde `resolveRouteBinding` metodunu geçersiz kılabilirsiniz. Bu yöntem, URI segmentinin değerini alacak ve rotaya enjekte edilecek sınıf örneğini döndürmelidir.

```php
/**
 * Retrieve the model for a bound value.
 *
 * @param  mixed  $value
 * @param  string|null  $field
 * @return \Illuminate\Database\Eloquent\Model|null
 */
public function resolveRouteBinding($value, $field = null)
{
    return $this->where('name', $value)->firstOrFail();
}
```

Bir rota, kapsayıcı bağlama kapsamını kullanıyorsa, `resolveChildRouteBinding` metodu, ebeveyn modelin çocuk bağlamasını çözmek için kullanılır.

```php
/**
 * Retrieve the child model for a bound value.
 *
 * @param  string  $childType
 * @param  mixed  $value
 * @param  string|null  $field
 * @return \Illuminate\Database\Eloquent\Model|null
 */
public function resolveChildRouteBinding($childType, $value, $field)
{
    return parent::resolveChildRouteBinding($childType, $value, $field);
}
```

# `#` Fallback Rotalar
---
`Route::fallback` metodu kullanarak, gelen istekle eşleşen başka bir rota olmadığında çalıştırılacak bir rota tanımlayabilirsiniz. Tipik olarak, işlenmemiş istekler uygulamanızın istisna işleyicisi aracılığıyla otomatik olarak bir "404" sayfası oluşturacaktır. Ancak, geri dönüş rotasını genellikle `routes/web.php` dosyanız içinde tanımlayacağınız için, `web` middleware grubundaki tüm middleware yazılımları rota için geçerli olacaktır. Gerektiğinde bu rotaya ek middleware'lar eklemekte özgürsünüz:

```php
Route::fallback(function () {
    // ...
});
```

Fallback rota her zaman uygulamanız tarafından kaydedilen son rota olmalıdır.

# `#` Mevcut Rotaya Erişme
---
Gelen isteği işleyen rotayla ilgili bilgilere erişmek için `Route` facadesinin `current`, `currentRouteName` ve `currentRouteAction` metodunu kullanabilirsiniz.

```php
use Illuminate\Support\Facades\Route;
 
$route = Route::current(); // Illuminate\Routing\Route
$name = Route::currentRouteName(); // string
$action = Route::currentRouteAction(); // string
```

Router ve route sınıflarında mevcut olan tüm metodları incelemek için, Route facade'inin temel sınıfı ve Route örneğine ait API belgelerine başvurabilirsiniz.

# `#` Cross-Origin Resource Sharing (CORS)
---
Laravel, CORS `OPTIONS` HTTP isteklerine, yapılandırdığınız değerlerle otomatik olarak yanıt verebilir. `OPTIONS` istekleri, uygulamanızın genel middleware yığınına otomatik olarak dahil edilen `HandleCors` middleware tarafından otomatik olarak işlenecektir.

Bazen uygulamanız için CORS yapılandırma değerlerini özelleştirmeniz gerekebilir. Bunun için `cors` yapılandırma dosyasını `config:publish` Artisan komutunu kullanarak yayınlayabilirsiniz.

```shell
php artisan config:publish cors
```

Bu komut, uygulamanızın `config` dizinine bir `cors.php` yapılandırma dosyası yerleştirir.

# `#` Route Caching
---
Uygulamanızı üretime dağıtırken, Laravel'in rota önbelleğinden faydalanmalısınız. Rota önbelleğini kullanmak, uygulamanızın tüm rotalarını kaydetme süresini önemli ölçüde azaltacaktır. Bir rota önbelleği oluşturmak için `route:cache` Artisan komutunu çalıştırın:

```shell
php artisan route:cache
```

Bu komutu çalıştırdıktan sonra, önbelleğe alınmış rotalar dosyanız her istekte yüklenecektir. Unutmayın, yeni rotalar eklerseniz taze bir rota önbelleği oluşturmanız gerekecektir. Bu nedenle, `route:cache` komutunu yalnızca projenizin dağıtımı sırasında çalıştırmalısınız.

Rota önbelleğini temizlemek için `route:clear` komutunu kullanabilirsiniz.

```shell
php artisan route:clear
```

