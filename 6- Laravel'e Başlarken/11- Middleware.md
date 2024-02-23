Middleware, uygulamanıza giren HTTP isteklerini incelemek ve filtrelemek için uygun bir mekanizma sağlar. Örneğin, Laravel, uygulamanızın kullanıcısının kimliğinin doğrulandığını doğrulayan bir middleware içerir. Eğer kullanıcının kimliği doğrulanmamışsa, middleware kullanıcıyı uygulamanızın giriş ekranına yönlendirecektir. Ancak, eğer kullanıcının kimliği doğrulanmışsa, middleware isteğin uygulama içinde ilerlemesine izin verecektir.

Kimlik doğrulamanın yanı sıra çeşitli görevleri yerine getirmek için ek middlewareler yazılabilir. Örneğin, bir loglama middlewarei uygulamanıza gelen tüm istekleri loglayabilir. Laravel çatısında kimlik doğrulama ve CSRF koruması için middlewareler de dahil olmak üzere çeşitli middlewareler bulunmaktadır. Tüm bu middlewareler `app/Http/Middleware` dizininde bulunur.

#### Middleware Tanımlama
---
Yeni bir middleware oluşturmak için `make:middleware` Artisan komutunu kullanın:

```
php artisan make:middleware EnsureTokenIsValid
```

Bu komut, `app/Http/Middleware` dizininize yeni bir `EnsureTokenIsValid` sınıfı yerleştirecektir. Bu middleware, yalnızca sağlanan token girişi belirtilen bir değerle eşleşirse rotaya erişime izin vereceğiz. Aksi takdirde, kullanıcıları ana URI'ye geri yönlendireceğiz:

```PHP
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
    public function handle(Request $request, Closure $next): Response
    {
        if ($request->input('id') !== '1234') {
            return redirect('home');
        }

        return $next($request);
    }
}
```

Gördüğünüz gibi, verilen id gizli id'ile eşleşmezse, middleware istemciye bir HTTP yönlendirmesi döndürecektir; aksi takdirde, istek uygulamaya daha fazla aktarılacaktır. İsteği uygulamanın daha derinlerine iletmek için (middlewarein "geçmesine" izin vererek), `$request` ile `$next` geri çağrısını çağırmalısınız.

Middlewarei, HTTP isteklerinin uygulamanıza ulaşmadan önce geçmesi gereken bir dizi "katman" olarak düşünmek en iyisidir. Her katman isteği inceleyebilir ve hatta tamamen reddedebilir.

Tüm middlewareler service container (hizmet kapsayıcısı) aracılığıyla çözümlenir, bu nedenle bir middlewarein constructor (kurucusu) içinde ihtiyacınız olan tüm dependenycleri yazabilirsiniz.

#### Middleware ve Yanıtlar
---
Elbette bir middleware, isteği uygulamanın derinliklerine aktarmadan önce veya sonra görevleri yerine getirebilir. 

Örneğin, aşağıdaki middleware, istek uygulama tarafından işlenmeden önce bazı görevleri yerine getirecektir:

```PHP
<?php
 
namespace App\Http\Middleware;
 
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
class BeforeMiddleware
{
    public function handle(Request $request, Closure $next): Response
    {
        // Eylemleri gerçekleştir
 
        return $next($request);
    }
}
```

Ancak, bu middleware, istek uygulama tarafından işlendikten sonra görevini yerine getirecektir:

```PHP
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
 
        // Eylemleri gerçekleştir
 
        return $response;
    }
}
```

#### Middleware Kaydetme
---
#### Global Middleware
---
Uygulamanıza yapılan her HTTP isteği sırasında bir ara yazılımın çalışmasını istiyorsanız, ara yazılım sınıfını `app/Http/Kernel.php` sınıfınızın `$middleware` özelliğinde listeleyin.

#### Rotalara Middleware Atama
---
Belirli rotalara ara katman yazılımı atamak isterseniz, rotayı tanımlarken `middleware` metotunu çağırabilirsiniz:

```PHP
use App\Http\Middleware\Authenticate;
 
Route::get('/profile', function () {
    // ...
})->middleware(Authenticate::class);
```

`middleware` metotuna bir dizi (`[]`) geçirerek rotaya birden fazla middleware atayabilirsiniz:

```PHP
Route::get('/', function () {
    // ...
})->middleware([First::class, Second::class]);
```

Kolaylık sağlamak için, uygulamanızın `app/Http/Kernel.php` dosyasında middlewarelere takma adlar atayabilirsiniz. Varsayılan olarak, bu sınıfın `$middlewareAliases` özelliği Laravel ile birlikte gelen ara katman yazılımları için girişler içerir. Bu listeye kendi ara katmanınızı ekleyebilir ve ona seçtiğiniz bir takma ad atayabilirsiniz:

```PHP title:app/Http/Kernel.php'
protected $middlewareAliases = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
    'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
    'can' => \Illuminate\Auth\Middleware\Authorize::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
    'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
];
```

HTTP çekirdeğinde middleware takma adı tanımlandıktan sonra, rotalara middleware atarken bu takma adı kullanabilirsiniz:

```PHP
Route::get('/profile', function () {
    // ...
})->middleware('auth');
```

###### Middleware Hariç
---
Bir grup rotaya middleware atarken, middleware grup içindeki tek bir rotaya uygulanmasını bazen engellemeniz gerekebilir. Bunu `withoutMiddleware` metotunu kullanarak gerçekleştirebilirsiniz:

```PHP title:'routes/web.php'
use App\Http\Middleware\EnsureTokenIsValid;
 
Route::middleware([EnsureTokenIsValid::class])->group(function () {
    Route::get('/', function () {
        // ...
    });
 
    Route::get('/profile', function () {
        // ...
    })->withoutMiddleware([EnsureTokenIsValid::class]);
});
```

Ayrıca, belirli bir middlewarei tüm rota tanımları grubundan hariç tutabilirsiniz:

```PHP
use App\Http\Middleware\EnsureTokenIsValid;
 
Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function () {
    Route::get('/profile', function () {
        // ...
    });
});
```

`withoutMiddleware` metotu yalnızca rota middlewarei kaldırabilir ve global middleware için geçerli değildir.

#### Middleware Grupları
---
