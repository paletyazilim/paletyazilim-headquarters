# `#` Giriş
---

Tüm istek işleme mantığınızı rota dosyalarınızda closure olarak tanımlamak yerine, bu davranışı "controller" sınıflarını kullanarak düzenlemek isteyebilirsiniz. Controller, ilgili istek işleme mantığını tek bir sınıfta gruplandırabilir. Örneğin, bir `UserController` sınıfı, kullanıcıları gösterme, oluşturma, güncelleme ve silme dahil olmak üzere kullanıcılarla ilgili tüm gelen istekleri işleyebilir. Varsayılan olarak, denetleyiciler `app/Http/Controllers` dizininde saklanır.

# `#` Controller Oluşturma
---

## Basit Controller’lar

Hızlı bir şekilde yeni bir controller oluşturmak için `make:controller` Artisan komutunu çalıştırabilirsiniz. Varsayılan olarak, uygulamanız için tüm controller’lar `app/Http/Controllers` dizininde saklanır:

```shell
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

```shell
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
---
Uygulamanızdaki her Eloquent modelini bir "kaynak" olarak düşünürseniz, uygulamanızdaki her kaynağa karşı aynı eylem kümelerini gerçekleştirmek tipiktir. Örneğin, uygulamanızın bir `Photo` modeli ve bir `Movie` modeli içerdiğini düşünün. Kullanıcılar muhtemelen bu kaynakları oluşturabilir, okuyabilir, güncelleyebilir veya silebilir.

Bu yaygın kullanım durumu nedeniyle, Laravel kaynak yönlendirmesi tipik oluşturma, okuma, güncelleme ve silme ("CRUD") rotalarını tek bir kod satırı ile bir controller'a atar. Başlamak için, `make:controller` Artisan komutunun `--resource` seçeneğini kullanarak bu eylemleri gerçekleştirecek bir controller'ı hızlıca oluşturabiliriz:

```shell
php artisan make:controller PhotoController --resource
```

Bu komut `app/Http/Controllers/PhotoController.php` adresinde bir controller oluşturacaktır. Controller, mevcut kaynak işlemlerinin her biri için bir metod içerecektir. Daha sonra, controller'a işaret eden bir kaynak rotası kaydedebilirsiniz:

```php
Route::resource('photos', PhotoController::class);
```

Bu tek rota bildirimi, kaynak üzerindeki çeşitli eylemleri işlemek için birden fazla rota oluşturur. Oluşturulan controller, bu eylemlerin her biri için zaten metodlara sahip olacaktır. Unutmayın, `route:list` Artisan komutunu çalıştırarak uygulamanızın rotalarına her zaman hızlı bir genel bakış elde edebilirsiniz.

Hatta `resources` metoduna bir dizi `[]` oluşturarak birçok resource controller'ını aynı anda kaydedebilirsiniz:

## Resource Controller Eylemleri

| **HTTP Fiili** | **URI**              | **Action (Eylem)** | **Rota İsmi**  |
| -------------- | -------------------- | ------------------ | -------------- |
| GET            | `/photos`              | index              | photos.index   |
| GET            | `/photos/create`       | create             | photos.create  |
| POST           | `/photos`              | store              | photos.store   |
| GET            | `/photos/{photo}`      | show               | photos.show    |
| GET            | `/photos/{photo}/edit` | edit               | photos.edit    |
| PUT/PATCH      | `/photos/{photo}`      | update             | photos.update  |
| DELETE         | `/photos/{photo}`      | destroy            | photos.destroy |
## Resource'a Model Belirtme

Rota model bağlama kullanıyorsanız ve resource controller metodlarının bir model örneğini yazmasını istiyorsanız, denetleyiciyi oluştururken --model seçeneğini kullanabilirsiniz:
## Eksik Model Davranışını Özelleştirme

Genellikle, örtük olarak bağlanmış bir kaynak modeli bulunamazsa 404 HTTP yanıtı oluşturulur. Ancak, kaynak rotanızı tanımlarken `missing` metodunu çağırarak bu davranışı özelleştirebilirsiniz. `missing` metodu, kaynağın rotalarından herhangi biri için örtük olarak bağlanmış bir model bulunamazsa çağrılacak bir closure kabul eder:

```php
use Illuminate\Support\Facades\Redirect;

Route::resource('photos', PhotoController::class)
    ->missing(function (Request $request) {
        return Redirect::route('photos.index');
    });
```

## Soft Deleted Models

Genellikle, kapsayıcı model bağlama işlemi silinmiş olan modelleri almayacak ve bunun yerine bir 404 HTTP yanıtı döndürecektir. Ancak, çerçeveye silinmiş modelleri izin vermesi için kaynak rotanızı tanımlarken `withTrashed` metodunu çağırarak çerçeveye silinmiş modelleri alması için talimat verebilirsiniz:

```php
Route::resource('photos', PhotoController::class)->withTrashed();
```

`withTrashed` metodunun bağımsız değişken olmadan çağrılması `show`, `edit` ve `update` kaynak rotaları için yazılımla silinen modellere izin verir. `withTrashed` metoduna bir dizi ileterek bu rotaların bir alt kümesini belirtebilirsiniz:

```php
Route::resource('photos', PhotoController::class)->withTrashed(['show']);
```


