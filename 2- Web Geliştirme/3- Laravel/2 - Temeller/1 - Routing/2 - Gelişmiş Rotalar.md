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
    return 'slug';
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
