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


