# `#` Rota Grupları Oluşturma
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
