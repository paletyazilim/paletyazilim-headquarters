# `#` Giriş
---
Laravel, veritabanınızla etkileşimi keyifli hale getiren bir object-relational mapper (nesne-ilişkisel eşleyici) (ORM) olan Eloquent'i içerir. Eloquent kullanırken, her veritabanı tablosunun, o tablo ile etkileşim kurmak için kullanılan karşılık gelen bir "Modeli" vardır. Veritabanı tablosundan kayıt almanın yanı sıra, Eloquent modelleri tablodan kayıt eklemenize, güncellemenize ve silmenize de olanak tanır.

> Başlamadan önce, uygulamanızın `config/database.php` yapılandırma dosyasında bir veritabanı bağlantısı yapılandırdığınızdan emin olun.

# `#` Model Sınıfları Oluşturma
---
Başlamak için bir Eloquent modeli oluşturalım. Modeller genellikle `app\Models` dizininde bulunur ve `Illuminate\Database\Eloquent\Model` sınıfını genişletir. Yeni bir model oluşturmak için `make:model` Artisan komutunu kullanabilirsiniz:

```shell
php artisan make:model Flight
```

Modeli oluştururken bir veritabanı migration'u oluşturmak isterseniz `--migration` veya `-m` seçeneğini kullanabilirsiniz:

```shell
php artisan make:model Flight --migration
```

Bir model oluştururken factories, seeders, policies, controller ve form request gibi çeşitli başka sınıf türleri de oluşturabilirsiniz. Ayrıca, bu seçenekler bir kerede birden fazla sınıf oluşturmak için birleştirilebilir:

```shell
# Generate a model and a FlightFactory class...
php artisan make:model Flight --factory
php artisan make:model Flight -f
 
# Generate a model and a FlightSeeder class...
php artisan make:model Flight --seed
php artisan make:model Flight -s
 
# Generate a model and a FlightController class...
php artisan make:model Flight --controller
php artisan make:model Flight -c
 
# Generate a model, FlightController resource class, and form request classes...
php artisan make:model Flight --controller --resource --requests
php artisan make:model Flight -crR
 
# Generate a model and a FlightPolicy class...
php artisan make:model Flight --policy
 
# Generate a model and a migration, factory, seeder, and controller...
php artisan make:model Flight -mfsc
 
# Shortcut to generate a model, migration, factory, seeder, policy, controller, and form requests...
php artisan make:model Flight --all
php artisan make:model Flight -a
 
# Generate a pivot model...
php artisan make:model Member --pivot
php artisan make:model Member -p
```

## Modellerin İncelenmesi

Bazen bir modelin mevcut tüm niteliklerini ve ilişkilerini sadece koduna göz atarak belirlemek zor olabilir. Bunun yerine, modelin tüm özniteliklerine ve ilişkilerine uygun bir genel bakış sağlayan `model:show` Artisan komutunu deneyin:

```shell
php artisan model:show Flight
```


# `#` Eloquent Model Ayarlamaları
---

`make:model` komutuyla oluşturulan modeller `app/Models` dizinine yerleştirilecektir. Temel bir model sınıfını inceleyelim ve Eloquent'in bazı temel kurallarını tartışalım:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    // ...
}
```

## Tablo İsimleri

Yukarıdaki örneğe göz attıktan sonra, Eloquent'e `Flight` modelimize hangi veritabanı tablosunun karşılık geldiğini söylemediğimizi fark etmiş olabilirsiniz. Kural olarak, başka bir ad açıkça belirtilmediği sürece sınıfın "snake case", çoğul adı tablo adı olarak kullanılacaktır. Dolayısıyla, bu durumda Eloquent, Flight modelinin kayıtları `flights` tablosunda sakladığını varsayarken, bir `AirTrafficController` modeli kayıtları `air_traffic_controllers` tablosunda saklayacaktır.

Modelinizin karşılık gelen veritabanı tablosu bu kurala uymuyorsa, model üzerinde bir `table` özelliği tanımlayarak modelin tablo adını manuel olarak belirtebilirsiniz:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'my_flights'; // Bu tabloyu kullanacaktır
}
```

## Birincil Anahtarlar

Eloquent ayrıca her modelin ilgili veritabanı tablosunun `id` adında bir birincil anahtar sütunu olduğunu varsayacaktır. Gerekirse, modelinizin birincil anahtarı olarak hizmet eden farklı bir sütun belirtmek için modelinizde korumalı bir `$primaryKey` özelliği tanımlayabilirsiniz:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The primary key associated with the table.
     *
     * @var string
     */
    protected $primaryKey = 'flight_id';
}
```

Buna ek olarak, Eloquent birincil anahtarın artan bir tamsayı değeri olduğunu varsayar; bu da Eloquent'in birincil anahtarı otomatik olarak bir tamsayıya dönüştüreceği anlamına gelir. Artmayan veya sayısal olmayan bir birincil anahtar kullanmak istiyorsanız, modelinizde `false` olarak ayarlanmış bir public `$incrementing` özelliği tanımlamanız gerekir:

```php
<?php
 
class Flight extends Model
{
    /**
     * Indicates if the model's ID is auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false;
}
```

Modelinizin birincil anahtarı bir tamsayı değilse, modelinizde korumalı bir `$keyType` özelliği tanımlamalısınız. Bu özellik `string` değerine sahip olmalıdır:

```php
<?php
 
class Flight extends Model
{
    /**
     * The data type of the primary key ID.
     *
     * @var string
     */
    protected $keyType = 'string';
}
```

### Composite Birincil Anahtarlar

Eloquent, her modelin birincil anahtar olarak kullanılabilecek en az bir benzersiz tanımlayıcı "ID "ye sahip olmasını gerektirir. "Bileşik" birincil anahtarlar Eloquent modelleri tarafından desteklenmez. Ancak, veritabanı tablolarınıza tablonun benzersiz tanımlayıcı birincil anahtarına ek olarak ek çok sütunlu, benzersiz dizinler eklemekte özgürsünüz.

## UUID ve ULID Anahtarlar

Eloquent modelinizin birincil anahtarları olarak otomatik artan tamsayılar kullanmak yerine UUID'leri kullanmayı tercih edebilirsiniz. UUID'ler, 36 karakter uzunluğunda evrensel olarak benzersiz alfa sayısal tanımlayıcılardır.

Bir modelin otomatik artan tamsayı anahtarı yerine UUID anahtarı kullanmasını istiyorsanız, modelde `Illuminate\Database\Eloquent\Concerns\HasUuids` özelliğini kullanabilirsiniz. Elbette, modelin UUID eşdeğeri birincil anahtar sütununa sahip olduğundan emin olmalısınız:

```php
use Illuminate\Database\Eloquent\Concerns\HasUuids;
use Illuminate\Database\Eloquent\Model;
 
class Article extends Model
{
    use HasUuids;
 
    // ...
}
 
$article = Article::create(['title' => 'Traveling to Europe']);
 
$article->id; // "8f8e8478-9035-4d23-b9a7-62f4d2612ce5"
```

Varsayılan olarak, `HasUuids` özelliği modelleriniz için "sıralı" UUID'ler üretecektir. Bu UUID'ler, leksikografik olarak sıralanabildikleri için indeksli veritabanı depolaması için daha verimlidir.

Model üzerinde bir `newUniqueId` metodu tanımlayarak belirli bir model için UUID oluşturma işlemini geçersiz kılabilirsiniz. Ayrıca, model üzerinde bir `uniqueIds` metodu tanımlayarak hangi sütunların UUID alması gerektiğini belirtebilirsiniz:

```php
use Ramsey\Uuid\Uuid;
 
/**
 * Generate a new UUID for the model.
 */
public function newUniqueId(): string
{
    return (string) Uuid::uuid4();
}
 
/**
 * Get the columns that should receive a unique identifier.
 *
 * @return array<int, string>
 */
public function uniqueIds(): array
{
    return ['id', 'discount_code'];
}
```

İsterseniz, UUID'ler yerine "ULID'leri" kullanmayı tercih edebilirsiniz. ULID'ler UUID'lere benzer; ancak sadece 26 karakter uzunluğundadırlar. Sıralı UUID'ler gibi ULID'ler de verimli veritabanı indekslemesi için leksikografik olarak sıralanabilir. ULID'leri kullanmak için modelinizde `Illuminate\Database\Eloquent\Concerns\HasUlids` özelliğini kullanmanız gerekir. Ayrıca modelin ULID eşdeğeri birincil anahtar sütununa sahip olduğundan emin olmalısınız:

```php
use Illuminate\Database\Eloquent\Concerns\HasUlids;
use Illuminate\Database\Eloquent\Model;
 
class Article extends Model
{
    use HasUlids;
 
    // ...
}
 
$article = Article::create(['title' => 'Traveling to Asia']);
 
$article->id; // "01gd4d3tgrrfqeda94gdbtdk5c"
```

## Timestamps

Varsayılan olarak, Eloquent `created_at` ve `updated_at` sütunlarının modelinizin ilgili veritabanı tablosunda bulunmasını bekler. Eloquent, modeller oluşturulduğunda veya güncellendiğinde bu sütunların değerlerini otomatik olarak ayarlayacaktır. Bu sütunların Eloquent tarafından otomatik olarak yönetilmesini istemiyorsanız, modelinizde `false` değerine sahip bir `$timestamps` özelliği tanımlamanız gerekir:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * Indicates if the model should be timestamped.
     *
     * @var bool
     */
    public $timestamps = false;
}
```

Modelinizin zaman damgalarının biçimini özelleştirmeniz gerekiyorsa, modelinizde `$dateFormat` özelliğini ayarlayın. Bu özellik, tarih özniteliklerinin veritabanında nasıl saklanacağını ve model bir diziye veya JSON'a serileştirildiğinde biçimlerini belirler:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The storage format of the model's date columns.
     *
     * @var string
     */
    protected $dateFormat = 'U';
}
```

Zaman damgalarını saklamak için kullanılan sütunların adlarını özelleştirmeniz gerekiyorsa, modelinizde `CREATED_AT` ve `UPDATED_AT` sabitlerini tanımlayabilirsiniz:

```php
<?php
 
class Flight extends Model
{
    const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'updated_date';
}
```

Modelin `updated_at` zaman damgası değiştirilmeden model işlemleri gerçekleştirmek isterseniz, `withoutTimestamps` metoduna verilen bir kapanış içinde model üzerinde işlem yapabilirsiniz:

```php
Model::withoutTimestamps(fn () => $post->increment(['reads']));
```

## Veritabanı Bağlantısı

Varsayılan olarak, tüm Eloquent modelleri uygulamanız için yapılandırılmış olan varsayılan veritabanı bağlantısını kullanacaktır. Belirli bir modelle etkileşime girerken kullanılması gereken farklı bir bağlantı belirtmek isterseniz, model üzerinde bir `$connection` özelliği tanımlamanız gerekir:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The database connection that should be used by the model.
     *
     * @var string
     */
    protected $connection = 'mysql';
}
```

## Varsayılan Özellik Değerleri

Varsayılan olarak, yeni örneklenen bir model örneği herhangi bir öznitelik değeri içermez. Modelinizin bazı öznitelikleri için varsayılan değerleri tanımlamak isterseniz, modelinizde bir `$attributes` özelliği tanımlayabilirsiniz. attribute dizisine yerleştirilen öznitelik değerleri, sanki veritabanından okunmuş gibi ham, "depolanabilir" formatta olmalıdır:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The model's default values for attributes.
     *
     * @var array
     */
    protected $attributes = [
        'options' => '[]',
        'delayed' => false,
    ];
}
```

## Eloquent Yapılandırması

Laravel, çeşitli durumlarda Eloquent'in davranışını ve "katılığını" yapılandırmanıza olanak tanıyan çeşitli yöntemler sunar.

İlk olarak, `preventLazyLoading` metodu, tembel yüklemenin engellenip engellenmeyeceğini belirten isteğe bağlı bir boolean bağımsız değişken kabul eder. Örneğin, üretim kodunda yanlışlıkla tembel yüklenmiş bir ilişki bulunsa bile üretim ortamınızın normal şekilde çalışmaya devam etmesi için tembel yüklemeyi yalnızca üretim dışı ortamlarda devre dışı bırakmak isteyebilirsiniz. Tipik olarak, bu yöntem uygulamanızın `AppServiceProvider`'ının `boot` metodunda çağrılmalıdır:

```php
use Illuminate\Database\Eloquent\Model;
 
/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    Model::preventLazyLoading(! $this->app->isProduction());
}
```

Ayrıca, `preventSilentlyDiscardingAttributes` metodunu çağırarak Laravel'e doldurulamayan bir niteliği doldurmaya çalışırken bir istisna atması talimatını verebilirsiniz. Bu, yerel geliştirme sırasında modelin doldurulabilir dizisine eklenmemiş bir niteliği ayarlamaya çalışırken beklenmedik hataları önlemeye yardımcı olabilir:

```php
Model::preventSilentlyDiscardingAttributes(! $this->app->isProduction());
```
