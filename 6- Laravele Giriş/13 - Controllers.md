# `#` Giriş
---

Tüm istek işleme mantığınızı rota dosyalarınızda closure olarak tanımlamak yerine, bu davranışı "controller" sınıflarını kullanarak düzenlemek isteyebilirsiniz. Controller, ilgili istek işleme mantığını tek bir sınıfta gruplandırabilir. Örneğin, bir `UserController` sınıfı, kullanıcıları gösterme, oluşturma, güncelleme ve silme dahil olmak üzere kullanıcılarla ilgili tüm gelen istekleri işleyebilir. Varsayılan olarak, denetleyiciler `app/Http/Controllers` dizininde saklanır.

# `#` Controller Oluşturma
---

## Basit Controller’lar

Hızlı bir şekilde yeni bir controller oluşturmak için `make:controller` Artisan komutunu çalıştırabilirsiniz. Varsayılan olarak, uygulamanız için tüm controller’lar `app/Http/Controllers` dizininde saklanır:

```bash
php artisan make:controller UserController
```

Şimdi temel bir controller örneğine göz atalım. Bir controller, gelen HTTP isteklerine yanıt verecek herhangi bir sayıda public metoda sahip olabilir:

```php
<?php
 
namespace App\Http\Controllers;
 
use App\Models\User;
use Illuminate\View\View;
 
class UserController extends Controller
{
    /**
     * Verilen kullanıcının ID'sini
     */
    public function show(string $id): View
    {
        
    }
}
```

Bir controller sınıfı ve metodu yazdıktan sonra, controller metoduna aşağıdaki gibi bir rota tanımlayabilirsiniz:

```php
Route::get('/user/{id}', [UserController::class, 'show']);
```

Gelen bir istek belirtilen rota URI'sıyla eşleştiğinde, `App\Http\Controllers\UserController` sınıfındaki `show` metodu çağrılacak ve rota parametreleri metoda aktarılacaktır.

Oluşturulan controller herhangi bir sınıfı `extends` yapması yani miras alması gerekmez ancak bazen tüm controller'ların paylaşılan ortak metodlara sahip olması gerektiği durumlar olmaktadır bu yüzden controller’larımız Controller sınıfından miras alır.

## Single Action (Tek İşlevli) Controller’lar

Bir controller eylemi özellikle karmaşıksa, tüm bir controller sınıfını tek bir eyleme ayırmayı uygun bulabilirsiniz. Bunu yapmak için, controller içinde tek bir `__invoke` metodu tanımlayabilirsiniz:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class InvokebleController extends Controller
{
    public function __invoke($firstname, $lastname)
    {
        $name = $this->getFirstName($firstname);
        $surname = $this->getLastName($lastname);

        return "Merhaba $name $surname";
    }

    private function getFirstName($firstname)
    {
        return "$firstname";
    }

    private function getLastName($lastname)
    {
        return "$lastname";
    }
}
```

Tek işlevli controller’ları rotaya kaydederken, bir controller metodu belirtmeniz gerekmez. Bunun yerine, controller’ın adını route’a aktarabilirsiniz:

```php
Route::get('/name/{firstname}/{lastname}', InvokebleController::class);
```

`make:controller` Artisan komutunun `--invokable` seçeneğini kullanarak invokable bir controller oluşturabilirsiniz:

```bash
php artisan make:controller ProvisionServer --invokable
```

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

# `#` Resource (Kaynak) Controller’lar