


# `#` Controller Middleware
---
Controller, rotalarınza middleware atanabilir:

```php
Route::get('/user', [UserController::class, 'show'])->middleware('auth');
```

Ya da controller sınıfınız içinde middleware belirtebilirsiniz. Bunu yapmak için, Controller’ınızın `HasMiddleware` arayüzünü uygulaması gerekir; bu da controller’ın statik bir `middleware` metoduna sahip olması gerektiğini belirtir. Bu metotla, controller’ın eylemlerine uygulanması gereken bir dizi middleware döndürebilirsiniz:

```php
<?php

namespace App\Http\Controllers;

use App\Http\Middleware\EnsureTokenIsValid;
use Illuminate\Http\Request;
use Illuminate\Routing\Controllers\HasMiddleware;
use Illuminate\Routing\Controllers\Middleware;

class MiddlewareController extends Controller implements HasMiddleware
{
    public function show()
    {
        return 'Merhaba dünya!';
    }

    public static function middleware() : array
    {
        return [
            EnsureTokenIsValid::class,
        ];
    }
}
```

Controller’a middleware’i closure olarak da tanımlayabilirsiniz, bu da middleware sınıfı yazmadan satır içi bir middleware tanımlamak için uygun bir yol sağlar:

```php
<?php

namespace App\Http\Controllers;

use App\Http\Middleware\EnsureTokenIsValid;
use Closure;
use Illuminate\Http\Request;
use Illuminate\Routing\Controllers\HasMiddleware;
use Illuminate\Routing\Controllers\Middleware;

class MiddlewareController extends Controller implements HasMiddleware
{
    public function show()
    {
        return 'Merhaba dünya!';
    }

    public static function middleware() : array
    {
        return [
            function (Request $request, Closure $next) {
                if ($request->input('token') !== 'my-secret-token') {
                    return redirect('/');
                }

                return $next($request);
            }
        ];
    }
}
```
