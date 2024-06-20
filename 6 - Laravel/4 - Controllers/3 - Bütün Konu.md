# `#` Giriş
---

Tüm istek işleme mantığınızı rota dosyalarınızda closure olarak tanımlamak yerine, bu davranışı "controller" sınıflarını kullanarak düzenlemek isteyebilirsiniz. Controller, ilgili istek işleme mantığını tek bir sınıfta gruplandırabilir. Örneğin, bir `UserController` sınıfı, kullanıcıları gösterme, oluşturma, güncelleme ve silme dahil olmak üzere kullanıcılarla ilgili tüm gelen istekleri işleyebilir. Varsayılan olarak, controllerlar `app/Http/Controllers` dizininde saklanır.

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
## Resource Controller Oluşturma

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

```php
Route::resources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);
```

### Resource Controller Eylemleri

| **HTTP Fiili** | **URI**                | **Action (Eylem)** | **Rota İsmi**  |
| -------------- | ---------------------- | ------------------ | -------------- |
| GET            | `/photos`              | index              | photos.index   |
| GET            | `/photos/create`       | create             | photos.create  |
| POST           | `/photos`              | store              | photos.store   |
| GET            | `/photos/{photo}`      | show               | photos.show    |
| GET            | `/photos/{photo}/edit` | edit               | photos.edit    |
| PUT/PATCH      | `/photos/{photo}`      | update             | photos.update  |
| DELETE         | `/photos/{photo}`      | destroy            | photos.destroy |

### Eksik Model Davranışını Özelleştirme

Genellikle, örtük olarak bağlanmış bir kaynak modeli bulunamazsa 404 HTTP yanıtı oluşturulur. Ancak, kaynak rotanızı tanımlarken `missing` metodunu çağırarak bu davranışı özelleştirebilirsiniz. `missing` metodu, kaynağın rotalarından herhangi biri için örtük olarak bağlanmış bir model bulunamazsa çağrılacak bir closure kabul eder:

```php
use Illuminate\Support\Facades\Redirect;

Route::resource('photos', PhotoController::class)
    ->missing(function (Request $request) {
        return Redirect::route('photos.index');
    });
```

### Soft Deleted Models

Genellikle, kapsayıcı model bağlama işlemi silinmiş olan modelleri almayacak ve bunun yerine bir 404 HTTP yanıtı döndürecektir. Ancak, çerçeveye silinmiş modelleri izin vermesi için kaynak rotanızı tanımlarken `withTrashed` metodunu çağırarak çerçeveye silinmiş modelleri alması için talimat verebilirsiniz:

```php
Route::resource('photos', PhotoController::class)->withTrashed();
```

`withTrashed` metodunun bağımsız değişken olmadan çağrılması `show`, `edit` ve `update` kaynak rotaları için yazılımla silinen modellere izin verir. `withTrashed` metoduna bir dizi ileterek bu rotaların bir alt kümesini belirtebilirsiniz:

```php
Route::resource('photos', PhotoController::class)->withTrashed(['show']);
```

### Resource'a Model Belirtme

Rota model bağlama kullanıyorsanız ve resource controller metodlarının bir model örneğini yazmasını istiyorsanız, denetleyiciyi oluştururken `--model` seçeneğini kullanabilirsiniz:

```shell
php artisan make:controller PhotoController --model=Photo --resource
```

### Form İstekleri Oluşturma

Bir resource controller oluştururken `--requests` seçeneğini sağlayabilirsiniz, bu Artisan'a controllerın depolama ve güncelleme metodları için form istek sınıfları oluşturmasını talimat verir.

```shell
php artisan make:controller PhotoController --model=Photo --resource --requests
```

## Kısmi Resource Controller

Bir resource rotası bildirirken, tam set yerine controller'ın ele alması gereken eylemlerin bir alt kümesini belirtebilirsiniz.

```php
Route::resource('photos', PhotoController::class)->only([
    'index', 'show'
]);

// YA DA

Route::resource('photos', PhotoController::class)->except([
    'create', 'store', 'update', 'destroy'
]);
```

### API Resource Routes

API'ler tarafından kullanılacak resource rotalarını bildirirken, genellikle `create` ve `edit` gibi HTML şablonları sunan rotaları hariç tutmak istersiniz. Kolaylık sağlamak için, bu iki rotayı otomatik olarak hariç tutmak için `apiResource` metodunu kullanabilirsiniz.

```php
Route::apiResource('photos', PhotoController::class);
```

`apiResources` metoduna bir dizi geçirerek birçok API resource controller'ini aynı anda kaydedebilirsiniz.

```php
use App\Http\Controllers\PhotoController;
use App\Http\Controllers\PostController;
 
Route::apiResources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);
```

`make:controller` aritsan komutunu çalıştırırken `--api` seçeneğini kullanarak, `create` veya `edit` metodlarını içermeyen bir API kaynak denetleyicisi hızlı bir şekilde oluşturabilirsiniz.

```shell
php artisan make:controller PhotoController --api
```

## Nested Resources (İç İçe Kaynaklar)

Bazen iç içe resource'lere yönlendirme tanımlamanız gerekebilir. Örneğin, bir photos resource'ına bağlı olabilecek birden fazla comment olabilir. Resource controllerini iç içe yerleştirmek için rota bildiriminizde "nokta" notasyonunu kullanabilirsiniz.

```php
Route::resource('photos.comments', PhotoCommentController::class);
```

Bu rota, aşağıdaki gibi URI'lerle erişilebilen iç içe resource'leri kaydedecektir:

```
/photos/{photo}/comments/{comment}
```

### Scope Nested Resources (İç İçe Kaynakları Kapsama) 

Laravel'in model tanımlama özelliği, çözülen alt modelin ebeveyn modele ait olduğunu doğrulamak için otomatik olarak iç içe bağlamaları kapsayabilir. Nested resource tanımlarken `scoped` metodunu kullanarak otomatik kapsamayı etkinleştirebilir ve Laravel'e hangi alanın çocuk resource'nın alınması gerektiğini bildirebilirsiniz.

### Shallow Nesting

Çoğu zaman, çocuk kimlik numarasının zaten benzersiz bir tanımlayıcı olduğu için URI içinde hem ebeveyn hem de çocuk kimlik numaralarına sahip olmak tamamen gerekli değildir. URI parametrelerinde modellerinizi tanımlamak için otomatik artan birincil anahtarlar gibi benzersiz tanımlayıcılar kullandığınızda, "yüzeysel" nesting kullanmayı tercih edebilirsiniz.

```php
Route::resource('photos.comments', CommentController::class)->shallow();
```

Bu rota tanımı aşağıdaki rotaları tanımlayacaktır:

| HTTP Fiili | URI                               | Action (Eylem) | Rota İsmi              |
| ---------- | --------------------------------- | -------------- | ---------------------- |
| GET        | `/photos/{photo}/comments`        | index          | photos.comments.index  |
| GET        | `/photos/{photo}/comments/create` | create         | photos.comments.create |
| POST       | `/photos/{photo}/comments`        | store          | photos.comments.store  |
| GET        | `/comments/{comment}`             | show           | comments.show          |
| GET        | `/comments/{comment}/edit`        | edit           | comments.edit          |
| PUT/PATCH  | `/comments/{comment}`             | update         | comments.update        |
| DELETE     | `/comments/{comment}`             | destroy        | comments.destroy       |

## İsimlendirilmiş Resource Rotaları

Varsayılan olarak, tüm resource controller eylemleri bir rota adına sahiptir; ancak istediğiniz rota adlarıyla bir `names` dizisi geçirerek bu adları geçersiz kılabilirsiniz.

```php
Route::resource('photos', PhotoController::class)->names([
    'create' => 'photos.build'
]);
```

## İsimlendirilmiş Resource Rota Parametreleri

Varsayılan olarak, `Route::resource`, resource rotalarınız için rota parametrelerini resource adının "tekil hali"ne dayanarak oluşturur. Ancak, `parameters` metodunu kullanarak kaynak bazında kolayca geçersiz kılabilirsiniz. `parameters` metoduna geçirilen dizi, resource adları ve parametre adlarından oluşan bir ilişkisel dizi olmalıdır:

```php
Route::resource('users', AdminUserController::class)->parameters([
    'users' => 'admin_user'
]);
```

Yukarıdaki örnek, resource'nuın `show` rotası için aşağıdaki URI'yi oluşturur:

```http
/users/{admin_user}
```

## Resource Rotaların Kapsamı

Laravel'in kapsamlı model bağlama özelliği, çözülen alt modelin ebeveyn modele ait olduğunu doğrulayan şekilde otomatik olarak iç içe bağlantıları kapsayabilir. İç içe resource'ınızı tanımlarken `scoped` metodunu kullanarak otomatik kapsamayı etkinleştirebilir ve Laravel'e hangi alanın çocuk resource'ının alınması gerektiğini bildirebilirsiniz.

```php
Route::resource('photos.comments', PhotoCommentController::class)->scoped([
    'comment' => 'slug',
]);
```

Bu rota, aşağıdaki gibi URI'lerle erişilebilen kapsamlı bir iç içe kaynağı kaydeder:

```http
/photos/{photo}/comments/{comment:slug}
```

Laravel, özel anahtarlı model bağlama kullanırken, iç içe geçmiş bir yol parametresi olarak kullanıldığında, otomatik olarak sorguyu, ilişki adını tahmin etmek için kuralları kullanarak, üst modeline göre iç içe geçmiş modeli almak için kapsamlandırır. Bu durumda, `Photos` modelinin, `comments` (yol parametresi adının çoğulu) adında bir ilişkiye sahip olduğu varsayılacaktır ve `Comments` modelini almak için kullanılabilir.

## Resource URI'lerini Yerelleştirme

Varsayılan olarak, `Route::resource`, İngilizce fiilleri ve çoğul kurallarını kullanarak kaynak URI'ları oluşturur. Oluşturma ve düzenleme eylem fiillerini yerelleştirmeniz gerekiyorsa, `Route::resourceVerbs` meotdunu kullanabilirsiniz. Bu, uygulamanızın `App\Providers\AppServiceProvider` içindeki `boot` metodunun başında yapılabilir.

```php
public function boot(): void
{
    Route::resourceVerbs([
        'create' => 'crear',
        'edit' => 'editar',
    ]);
}
```

Laravel'in çoğul hali destekleyicisi (pluralizer), ihtiyaçlarınıza göre yapılandırabileceğiniz birkaç farklı dil destekler. Fiiller ve çoğul hali dil özelleştirildikten sonra, `Route::resource('publicacion', PublicacionController::class)` gibi bir controller rotası kaydı aşağıdaki URI'leri üretecektir:

```http
/publicacion/crear

/publicacion/{publicaciones}/editar
```

## Resource Controller'leri Tamamlama

Varsayılan resource rotaları setine ek olarak bir resource controller'ına ek rotalar eklemeniz gerekiyorsa, bu rotaları `Route::resource` metoduna çağrı yapmadan önce tanımlamanız gerekmektedir; aksi takdirde, `resource` metodu tarafından tanımlanan rotalar yanlışlıkla ek rotalarınızın önüne geçebilir.

```php
Route::get('/photos/popular', [PhotoController::class, 'popular']);
Route::resource('photos', PhotoController::class);
```

>Controllerlarınızı odaklı tutmayı unutmayın. Eğer kendinizi tipik resource işlemleri dışında metodlara ihtiyaç duyarken bulursanız, controller'ınızı iki daha küçük controller'a bölmeyi düşünün.

## Singleton Controllers

#çevrilecek 

# `#` Dependecy Injection
---
#çevrilecek 