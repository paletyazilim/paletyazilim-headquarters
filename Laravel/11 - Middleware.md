# `#` Giriş
---
Middleware, uygulamanıza giren HTTP isteklerini incelemek ve filtrelemek için uygun bir mekanizma sağlar. Örneğin, Laravel, uygulamanızın kullanıcısının kimliğinin doğrulandığını doğrulayan bir middleware içerir. Eğer kullanıcının kimliği **doğrulanmamışsa**, middleware kullanıcıyı uygulamanızın giriş ekranına yönlendirecektir. Ancak, eğer kullanıcının kimliği **doğrulanmışsa**, middleware isteğin uygulama içinde ilerlemesine izin verecektir.

Kimlik doğrulamanın yanı sıra çeşitli görevleri yerine getirmek için ek middleware’lar yazılabilir. Örneğin, bir loglama middleware’i uygulamanıza gelen tüm istekleri loglayabilir. Laravel'de kimlik doğrulama ve CSRF koruması için middleware’lar da dahil olmak üzere çeşitli middleware bulunur; ancak, kullanıcı tanımlı tüm middleware’ler genellikle uygulamanızın `app/Http/Middleware` dizininde bulunur.

# `#` Middleware Oluşturma
---
Yeni bir middleware oluşturmak için `make:middleware` Artisan komutunu kullanın:

```shell
php artisan make:middleware EnsureTokenIsValid
```

Bu komut, `app/Http/Middleware` dizininize yeni bir `EnsureTokenIsValid` sınıfı yerleştirecektir. Bu middleware, yalnızca sağlanan token girişi belirtilen bir değerle eşleşirse rotaya erişime izin vereceğiz. Aksi takdirde, kullanıcıları ana URI'ye geri yönlendireceğiz:

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsureTokenIsValid
{
    /**
     * Handle an incoming request.
     *
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     */
    public function handle(Request $request, Closure $next) : Response
    {
        if ($request->input('token') !== 'my-secret-token') {
            return redirect('/');
        }

        return $next($request);
    }
}
```

Gördüğünüz gibi, verilen `token` gizli token ile eşleşmezse, middleware istemciye bir HTTP yönlendirmesi döndürecektir; aksi takdirde, istek uygulamaya daha fazla aktarılacaktır. İsteği uygulamanın daha derinlerine iletmek için (middleware "geçmesine" izin vererek), `$request` ile `$next` geri çağrısını çağırmalısınız.

Ara yazılımı, HTTP isteklerinin uygulamanıza ulaşmadan önce geçmesi gereken bir dizi "katman" olarak düşünmek en iyisidir. Her katman isteği inceleyebilir ve hatta tamamen reddedebilir.

Tüm middleware’lar service providers aracılığıyla çözümlenir, bu nedenle bir middleware kurucusu içinde ihtiyacınız olan tüm bağımlılıkları yazabilirsiniz.

## Middleware ve Yanıtlar

Elbette bir middleware, isteği uygulamanın derinliklerine aktarmadan önce veya sonra görevleri yerine getirebilir. Örneğin, aşağıdaki middleware, istek uygulama tarafından işlenmeden **önce** bazı görevleri yerine getirecektir:

```php
<?php
 
namespace App\Http\Middleware;
 
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
class BeforeMiddleware
{
    public function handle(Request $request, Closure $next): Response
    {
        // Eylemleri Gerçekleştirin...
 
        return $next($request);
    }
}
```

Ancak, bu middleware, istek uygulama tarafından işlendikten **sonra** görevini yerine getirecektir:

```php
<?php
 
namespace App\Http\Middleware;
 
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
class AfterMiddleware
{
    public function handle(Request $request, Closure $next): Response
    {
        $response = $next($request);
 
        // Eylemleri Gerçekleştirin...
 
        return $response;
    }
}
```

# `#` Middleware’lerin Kaydedilmesi
---

## Global Middleware

Uygulamanıza yapılan her HTTP isteği sırasında bir middleware’in çalışmasını istiyorsanız, bunu uygulamanızın `bootstrap/app.php` dosyasındaki global middleware yığınına ekleyebilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {
        $middleware->append(EnsureTokenIsValid::class);
    })
```

`withMiddleware` closure’una sağlanan `$middleware` nesnesi, `Illuminate\Foundation\Configuration\Middleware`'in bir örneğidir ve uygulamanızın rotalarına atanan middleware’leri yönetmekten sorumludur. `append` metodu, middleware’inizi global middleware listesinin **sonuna** ekler. Listenin başına bir ara katman eklemek isterseniz `prepend` metodunu kullanmalısınız.

### Manuel Olarak Global Middleware Listesini Düzenleme

Laravel'in global middleware yığınını manuel olarak yönetmek isterseniz, `use` metoduna Laravel'in varsayılan global middleware yığınını sağlayabilirsiniz. Daha sonra, varsayılan middleware yığınını gerektiği gibi düzenleyebilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {
        $middleware->use([
            // \Illuminate\Http\Middleware\TrustHosts::class,
            \Illuminate\Http\Middleware\TrustProxies::class,
            \Illuminate\Http\Middleware\HandleCors::class,
            \Illuminate\Foundation\Http\Middleware\PreventRequestsDuringMaintenance::class,
            \Illuminate\Http\Middleware\ValidatePostSize::class,
            \Illuminate\Foundation\Http\Middleware\TrimStrings::class,
            \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
            EnsureTokenIsValid::class
        ]);
    })
```

## Rota Kayıtlı Middleware

Belirli rotalara middleware atamak isterseniz, rotayı tanımlarken `middleware` metodunu çağırabilirsiniz:

```php
Route::get('/user', function () {
    //..
})->middleware(EnsureTokenIsValid::class);
```

`middleware` metodunda bir dizi `[]` oluşturarak aynı rotaya birden fazla middleware atayabilirsiniz:

```php
Route::get('/user', function () {
    return 'Kullanıcı sayfasına hoşgeldiniz!';
})->middleware(
        [
            EnsureTokenIsValid::class,
            //..
        ]
    );
```

### Middleware Gruplarında Belirli Rotalarda Middleware Yok Sayma

Bir grup rotaya middleware atarken, middleware grubunun içindeki bir rotaya uygulanmasını bazen engellemeniz gerekebilir. Bunu `withoutMiddleware` metodunu kullanarak gerçekleştirebilirsiniz:

```php
Route::middleware([EnsureTokenIsValid::class])->group(function () {
    Route::get('/', function () {
        //..
    })->withoutMiddleware([EnsureTokenIsValid::class]);

    Route::get('/user', function () {
        //..
    });
});
```

Ayrıca, belirli bir middleware kümesini tüm rota tanımları grubundan hariç tutabilirsiniz:

```php
Route::middleware([EnsureTokenIsValid::class])->group(function () {

    Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function () {
        Route::get('/', function () {
            //..
        });
    });

    Route::get('/user', function () {
        //..
    });
});
```

`withoutMiddleware` yöntemi yalnızca rota middleware’lerini kaldırabilir yani global middleware’ler için geçerli değildir.

## Middleware Grupları

Bazen rotalara atanmalarını kolaylaştırmak için birkaç middleware’i tek bir anahtar altında gruplamak isteyebilirsiniz. Bunu uygulamanızın `bootstrap/app.php` dosyasındaki `appendToGroup` veya `prependToGroup` metodunu kullanarak gerçekleştirebilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {

        $middleware->appendToGroup('md-group', [
            EnsureTokenIsValid::class,
            FirstMiddleware::class,
            SecondMiddleware::class
        ]);

        $middleware->prependToGroup('md-group', [
            EnsureTokenIsValid::class,
            FirstMiddleware::class,
            SecondMiddleware::class
        ]);

    })
```

Middleware grupları, aynı sözdizimi kullanılarak rotalara ve controller eylemlerine atanabilir:

```php
Route::get('/user', function () {
    //..
})->middleware('md-group');

Route::middleware(['md-group'])->group(function () {
    //..
});
```

### Laravel’in Varsayılan Middleware Grupları

Laravel, web ve API rotalarınıza uygulamak isteyebileceğiniz ortak middleware’leri içeren önceden tanımlanmış `web` ve `api` middleware grupları içerir. Unutmayın, Laravel bu middleware gruplarını otomatik olarak ilgili `routes/web.php` ve `routes/api.php` dosyalarına uygular:

| web Middleware Grupları                                     |
| ----------------------------------------------------------- |
|  `Illuminate\Cookie\Middleware\EncryptCookies`              |
|  `Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse`  |
|  `Illuminate\Session\Middleware\StartSession`               |
|  `Illuminate\View\Middleware\ShareErrorsFromSession`        |
|  `Illuminate\Foundation\Http\Middleware\ValidateCsrfToken`  |
| `Illuminate\Routing\Middleware\SubstituteBindings`          |

| api Middleware Grupları                            |
| -------------------------------------------------- |
| `Illuminate\Routing\Middleware\SubstituteBindings` |

Middleware’leri bu gruplara eklemek veya önceden eklemek isterseniz, uygulamanızın `bootstrap/app.php` dosyasındaki `web` ve `api` metodlarını kullanabilirsiniz. `web` ve `api` metodlarınnı `appendToGroup` metoduna uygun alternatiflerdir:

```php
->withMiddleware(function (Middleware $middleware) {

        $middleware->web(append: [
            EnsureTokenIsSubscribed::class,
        ]);

        $middleware->web(prepend: [
            EnsureTokenIsValid::class,
        ])

    })
```

Hatta Laravel'in varsayılan middleware gruplarındaki herhangi birini kendi özel middleware’iniz ile değiştirebilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {

        $middleware->web(replace: [
            StartSession::class => EnsureTokenIsValid::class,
        ]);

    })
```

Ya da bir middleware’i tamamen kaldırabilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {

        $middleware->web(remove: [
            StartSession::class,
        ]);

    })
```

### Laravel’in Varsayılan Middleware Gruplarını Düzenleme

Laravel'in varsayılan `web` ve `api` middleware gruplarındaki tüm middleware’leri manuel olarak yönetmek isterseniz, grupları tamamen yeniden tanımlayabilirsiniz. Aşağıdaki örnek `web` ve `api` middleware gruplarını varsayılan middleware ile tanımlayacak ve bunları gerektiği gibi özelleştirmenize izin verecektir:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->group('web', [
        \Illuminate\Cookie\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Illuminate\Foundation\Http\Middleware\ValidateCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        // \Illuminate\Session\Middleware\AuthenticateSession::class,
    ]);
 
    $middleware->group('api', [
        // \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        // 'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ]);
})
```

Varsayılan olarak, `web` ve `api` middleware grupları, `bootstrap/app.php` dosyası tarafından uygulamanızın ilgili `routes/web.php` ve `routes/api.php` dosyalarına otomatik olarak uygulanır.

## Middleware Aliases (Takma Adları)

Uygulamanızın `bootstrap/app.php` dosyasında middleware’lara takma adlar atayabilirsiniz. Middleware takma adları, belirli bir middleware sınıfı için kısa bir takma ad tanımlamanıza olanak tanır; bu, özellikle uzun sınıf adlarına sahip middleware’lar için yararlı olabilir:

```php
->withMiddleware(function (Middleware $middleware) {

        $middleware->alias([

            'ensureTokenMD' => EnsureTokenIsValid::class,
            'firstMD' => FirstMiddleware::class,
            'secondMD' => SecondMiddleware::class
        ]);

    })
```

Middleware takma adı uygulamanızın `bootstrap/app.php` dosyasında tanımlandıktan sonra, middleware’i rotalara atarken bu takma adı kullanabilirsiniz:

```php
Route::get('/user', function () {
    //..
})->middleware('ensureTokenMD');
```

Kolaylık sağlamak için, Laravel'in bazı yerleşik middleware’leri varsayılan olarak takma adlıdır. Örneğin, `auth` middleware’i `Illuminate\Auth\Middleware\Authenticate` middleware’i için bir takma addır. Aşağıda varsayılan middleware takma adlarının bir listesi bulunmaktadır:

| Alias            | Middleware                                                                                                |
| ---------------- | --------------------------------------------------------------------------------------------------------- |
| `auth`             | `Illuminate\Auth\Middleware\Authenticate`                                                                   |
| `auth.basic`       | `Illuminate\Auth\Middleware\AuthenticateWithBasicAuth`                                                      |
| `auth.session`     | `Illuminate\Session\Middleware\AuthenticateSession`                                                         |
| `cache.headers`    | `Illuminate\Http\Middleware\SetCacheHeaders`                                                                |
| `can`              | `Illuminate\Auth\Middleware\Authorize`                                                                      |
| `guest`            | `Illuminate\Auth\Middleware\RedirectIfAuthenticated`                                                        |
| `password.confirm` | `Illuminate\Auth\Middleware\RequirePassword`                                                                |
| `precognitive`     | `Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests`                                          |
| `signed`           | `Illuminate\Routing\Middleware\ValidateSignature`                                                           |
| `subscribed`       | `\Spark\Http\Middleware\VerifyBillableIsSubscribed`                                                         |
| `throttle`         | `Illuminate\Routing\Middleware\ThrottleRequests or Illuminate\Routing\Middleware\ThrottleRequestsWithRedis` |
| `verified`         | `Illuminate\Auth\Middleware\EnsureEmailIsVerified`                                                          |

## Middleware’leri Sıralama

Nadiren, middleware’lerinizin belirli bir sırada çalışmasına ihtiyaç duyabilirsiniz, ancak rotaya atandıklarında sıraları üzerinde kontrol sahibi olmayabilirsiniz. Bu durumlarda, uygulamanızın `bootstrap/app.php` dosyasındaki öncelik yöntemini kullanarak middleware önceliğinizi belirtebilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->priority([
        \Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests::class,
        \Illuminate\Cookie\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Illuminate\Foundation\Http\Middleware\ValidateCsrfToken::class,
        \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ]);
})
```

# `#` Middleware Parametreleri
---

Middleware’ler ek parametreler de alabilir. Örneğin, uygulamanızın belirli bir eylemi gerçekleştirmeden önce kimliği doğrulanmış kullanıcının belirli bir "role" sahip olduğunu doğrulaması gerekiyorsa, ek bağımsız değişken olarak bir rol adı alan bir `EnsureUserHasRole` middleware’ini oluşturabilirsiniz.

Ek middleware parametreleri `$next` bağımsız değişkeninden sonra middleware aktarılacaktır:

```php
<?php
 
namespace App\Http\Middleware;
 
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
class EnsureUserHasRole
{
    /**
     * Handle an incoming request.
     *
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     */
    public function handle(Request $request, Closure $next, string $role): Response
    {
        if (! $request->user()->hasRole($role)) {
            // Redirect...
        }
 
        return $next($request);
    }
 
}
```

Middleware parametreleri, middleware adı ve parametreler `:` ile ayrılarak rota tanımlanırken belirtilebilir:

```php
Route::put('/post/{id}', function (string $id) {
    // ...
})->middleware('role:editor');
```

Birden fazla parametre virgülle ayrılabilir:

```php
Route::put('/post/{id}', function (string $id) {
    // ...
})->middleware('role:editor,publisher');
```

# `#` Terminable (Sonlandırılabilir) Middleware’ler


