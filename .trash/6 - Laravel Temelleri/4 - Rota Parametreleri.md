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
