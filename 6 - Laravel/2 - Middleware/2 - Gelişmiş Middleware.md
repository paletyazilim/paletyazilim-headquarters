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

