
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

# `#` Modellerin Çağrılması
---
Bir model ve ilişkili veritabanı tablosunu oluşturduktan sonra, veritabanınızdan veri almaya başlamaya hazırsınız demektir. Her Eloquent modelini, modelle ilişkili veritabanı tablosunu akıcı bir şekilde sorgulamanızı sağlayan güçlü bir sorgu oluşturucu olarak düşünebilirsiniz. Modelin `all` metodu, modelin ilişkili veritabanı tablosundaki tüm kayıtları alacaktır:

```php
use App\Models\Flight;
 
foreach (Flight::all() as $flight) {
    echo $flight->name;
}
```

## Sorgu Oluşturma

Eloquent `all` metodu, modelin tablosundaki tüm sonuçları döndürür. Ancak, her Eloquent modeli bir sorgu oluşturucu olarak hizmet verdiğinden, sorgulara ek kısıtlamalar ekleyebilir ve ardından sonuçları almak için `get` metodunu çağırabilirsiniz:

```php
$flights = Flight::where('active', 1)
               ->orderBy('name')
               ->take(10)
               ->get();
```

Eloquent modelleri sorgu oluşturucu olduğundan, Laravel'in sorgu oluşturucusu tarafından sağlanan tüm metodları gözden geçirmelisiniz. Eloquent sorgularınızı yazarken bu metodlardan herhangi birini kullanabilirsiniz.

## Modelleri Yenileme

Veritabanından alınmış bir Eloquent modeli örneğiniz zaten varsa, `fresh` ve `refresh` metodlarını kullanarak modeli "yenileyebilirsiniz". `fresh` metodu modeli veritabanından yeniden alır. Mevcut model örneği etkilenmeyecektir:

```php
$flight = Flight::where('number', 'FR 900')->first();
 
$freshFlight = $flight->fresh();
```

`refresh` metodu, veritabanındaki yeni verileri kullanarak mevcut modeli yeniden alacaktır. Buna ek olarak, yüklenen tüm ilişkiler de yenilenecektir:

```php
$flight = Flight::where('number', 'FR 900')->first();
 
$flight->number = 'FR 456';
 
$flight->refresh();
 
$flight->number; // "FR 900"
```

## Collections

Gördüğümüz gibi, `all` ve `get` gibi Eloquent metodları veritabanından birden fazla kayıt alır. Ancak, bu metodlar düz bir PHP dizisi döndürmez. Bunun yerine, bir `Illuminate\Database\Eloquent\Collection` örneği döndürülür.

Eloquent `Collection` sınıfı, Laravel'in veri koleksiyonlarıyla etkileşim için çeşitli yararlı metodlar sağlayan temel `Illuminate\Support\Collection` sınıfını genişletir. Örneğin, `reject` metodu, çağrılan bir closure'un sonuçlarına göre bir koleksiyondan modelleri kaldırmak için kullanılabilir:

```php
$flights = Flight::where('destination', 'Paris')->get();
 
$flights = $flights->reject(function (Flight $flight) {
    return $flight->cancelled;
});
```

Laravel'in temel koleksiyon sınıfı tarafından sağlanan metodlara ek olarak, Eloquent `Collection` sınıfı özellikle Eloquent model koleksiyonları ile etkileşim için tasarlanmış birkaç ekstra metod sağlar.

Laravel'in tüm koleksiyonları PHP'nin yinelenebilir arayüzlerini uyguladığından, koleksiyonlar üzerinde bir diziymiş gibi döngü yapabilirsiniz:

```php
foreach ($flights as $flight) {
    echo $flight->name;
}
```

## Yığınlama Sonuçları (Chunking)

`all` veya `get` metodlarını kullanarak on binlerce Eloquent kaydını yüklemeye çalışırsanız uygulamanızın belleği tükenebilir. Bu metodları kullanmak yerine, çok sayıda modeli daha verimli bir şekilde işlemek için `chunk` metodu kullanılabilir.

`chunk` metodu, Eloquent modellerinin bir alt kümesini alır ve bunları işlenmek üzere bir closure'a aktarır. Bir seferde yalnızca geçerli Eloquent modelleri yığını alındığından, `chunk` metodu çok sayıda modelle çalışırken önemli ölçüde daha az bellek kullanımı sağlayacaktır:

```php
use App\Models\Flight;
use Illuminate\Database\Eloquent\Collection;
 
Flight::chunk(200, function (Collection $flights) {
    foreach ($flights as $flight) {
        // ...
    }
});
```

`chunk` yöntemine aktarılan ilk bağımsız değişken, "chunk" başına almak istediğiniz kayıt sayısıdır. İkinci bağımsız değişken olarak aktarılan closure, veritabanından alınan her yığın için çağrılacaktır. Closure aktarılan her bir kayıt yığınını almak için bir veritabanı sorgusu yürütülecektir.

`chunk` metodunun sonuçlarını, sonuçlar üzerinde yineleme yaparken güncelleyeceğiniz bir sütuna göre filtreliyorsanız `chunkById` metodunu kullanmalısınız. Bu senaryolarda `chunk` metodunun kullanılması beklenmedik ve tutarsız sonuçlara yol açabilir. Dahili olarak, `chunkById` metodu her zaman önceki yığındaki son modelden daha büyük bir `id` sütununa sahip modelleri alacaktır:

```php
Flight::where('departed', true)
    ->chunkById(200, function (Collection $flights) {
        $flights->each->update(['departed' => false]);
    }, $column = 'id');
```

## Lazy Collection ile Yığınlama Yapma (Chunking)

`lazy` metodu, perde arkasında sorguyu parçalar halinde yürütmesi açısından yığın yöntemine benzer şekilde çalışır. Ancak, her bir yığını olduğu gibi doğrudan bir geri aramaya aktarmak yerine, `lazy` metodu Eloquent modellerinin düzleştirilmiş bir `LazyCollection`'ını döndürür ve bu da sonuçlarla tek bir akış olarak etkileşim kurmanıza olanak tanır:

```php
use App\Models\Flight;
 
foreach (Flight::lazy() as $flight) {
    // ...
}
```

`lazy` metodun sonuçlarını, sonuçlar üzerinde yineleme yaparken güncelleyeceğiniz bir sütuna göre filtreliyorsanız, `lazyById` metodunu kullanmalısınız. Dahili olarak, `lazyById` metodu her zaman önceki yığındaki son modelden daha büyük bir `id` sütununa sahip modelleri alacaktır:

```php
Flight::where('departed', true)
    ->lazyById(200, $column = 'id')
    ->each->update(['departed' => false]);
```

Sonuçları `lazyByIdDesc` yöntemini kullanarak `id`'nin azalan sırasına göre filtreleyebilirsiniz.

## Cursors

`lazy` metoduna benzer şekilde, on binlerce Eloquent model kaydı arasında yineleme yaparken uygulamanızın bellek tüketimini önemli ölçüde azaltmak için `cursor` metodu kullanılabilir.

`cursor` metodu yalnızca tek bir veritabanı sorgusu yürütür; ancak, bireysel Eloquent modelleri gerçekten yinelenene kadar belleğe alınmayacaktır. Bu nedenle, `cursor` üzerinde yineleme yapılırken herhangi bir zamanda bellekte yalnızca bir Eloquent modeli tutulur.

> `cursor` metodu bir seferde bellekte yalnızca tek bir Eloquent modeli tuttuğundan, ilişkileri eager ile yükleyemez. İlişkileri eager olarak yüklemeniz gerekiyorsa, bunun yerine `lazy` metodunu kullanmayı düşünün.

Dahili olarak, `cursor` meotdu bu işlevi uygulamak için PHP üreteçlerini kullanır:

```php
use App\Models\Flight;
 
foreach (Flight::where('destination', 'Zurich')->cursor() as $flight) {
    // ...
}
```

`cursor` bir `Illuminate\Support\LazyCollection` örneği döndürür. Lazy koleksiyonlar, bir seferde belleğe sadece tek bir model yüklerken tipik Laravel koleksiyonlarında bulunan koleksiyon yöntemlerinin çoğunu kullanmanıza izin verir:

```php
use App\Models\User;
 
$users = User::cursor()->filter(function (User $user) {
    return $user->id > 500;
});
 
foreach ($users as $user) {
    echo $user->id;
}
```

`cursor` metodu normal bir sorgudan çok daha az bellek kullansa da (bir seferde bellekte yalnızca tek bir Eloquent modeli tutarak), yine de sonunda bellek tükenecektir. Bunun nedeni PHP'nin PDO sürücüsünün tüm ham sorgu sonuçlarını arabelleğinde dahili olarak önbelleğe almasıdır. Eğer çok fazla sayıda Eloquent kaydı ile uğraşıyorsanız, bunun yerine `lazy` metodunu kullanmayı düşünün.

## Gelişmiş Alt Sorgular

### Alt Sorgu Seçicileri

Eloquent ayrıca, tek bir sorguda ilgili tablolardan bilgi çekmenize olanak tanıyan gelişmiş alt sorgu desteği sunar. Örneğin, bir uçuş `destinations` tablomuz ve bir de varış noktalarına `flights` tablomuz olduğunu düşünelim. `flights` tablosu, uçuşun varış noktasına ne zaman ulaştığını gösteren bir `arrived_at` sütunu içerir.

Sorgu oluşturucunun `select` ve `addSelect` metodlarında bulunan alt sorgu işlevini kullanarak, tek bir sorgu kullanarak tüm varış noktalarını ve o varış noktasına en son varan uçuşun adını seçebiliriz:

```php
use App\Models\Destination;
use App\Models\Flight;
 
return Destination::addSelect(['last_flight' => Flight::select('name')
    ->whereColumn('destination_id', 'destinations.id')
    ->orderByDesc('arrived_at')
    ->limit(1)
])->get();
```

## Alt Sorgu Sırlama

Ayrıca, sorgu oluşturucunun `orderBy` işlevi alt sorguları da destekler. Flight örneğimizi kullanmaya devam edersek, bu işlevi tüm varış noktalarını son uçuşun o varış noktasına ulaştığı zamana göre sıralamak için kullanabiliriz. Yine, bu işlem tek bir veritabanı sorgusu yürütülürken yapılabilir:

```php
return Destination::orderByDesc(
    Flight::select('arrived_at')
        ->whereColumn('destination_id', 'destinations.id')
        ->orderByDesc('arrived_at')
        ->limit(1)
)->get();
```

# `#` Tek Modelleri Alma
---
Belirli bir sorguyla eşleşen tüm kayıtları almanın yanı sıra, `find`, `first` veya `firstWhere` metodlarını kullanarak tek kayıtları da alabilirsiniz. Bu metodlar, bir model koleksiyonu döndürmek yerine tek bir model örneği döndürür:

```php
use App\Models\Flight;
 
// Retrieve a model by its primary key...
$flight = Flight::find(1);
 
// Retrieve the first model matching the query constraints...
$flight = Flight::where('active', 1)->first();
 
// Alternative to retrieving the first model matching the query constraints...
$flight = Flight::firstWhere('active', 1);
```

Bazen hiçbir sonuç bulunamazsa başka bir eylem gerçekleştirmek isteyebilirsiniz. `findOr` ve `firstOr` metodları tek bir model örneği döndürür veya hiçbir sonuç bulunamazsa verilen closure'ı çalıştırır. Closure tarafından döndürülen değer, metodun sonucu olarak kabul edilir:

```php
$flight = Flight::findOr(1, function () {
    // ...
});
 
$flight = Flight::where('legs', '>', 3)->firstOr(function () {
    // ...
});
```

## "Bulunamadı" İstisnaları

Bazen bir model bulunamazsa bir istisna fırlatmak isteyebilirsiniz. Bu özellikle rotalarda veya denetleyicilerde kullanışlıdır. `findOrFail` ve `firstOrFail` metodları sorgunun ilk sonucunu alır; ancak sonuç bulunamazsa bir `Illuminate\Database\Eloquent\ModelNotFoundException` atılır:

```php
$flight = Flight::findOrFail(1);
 
$flight = Flight::where('legs', '>', 3)->firstOrFail();
```

`ModelNotFoundException` yakalanmazsa, istemciye otomatik olarak bir 404 HTTP yanıtı gönderilir:

```php
use App\Models\Flight;
 
Route::get('/api/flights/{id}', function (string $id) {
    return Flight::findOrFail($id);
});
```

# `#` Modelleri Alma veya Oluşturma
---
`firstOrCreate` metodu, verilen sütun/değer çiftlerini kullanarak bir veritabanı kaydı bulmaya çalışacaktır. Model veritabanında bulunamazsa, ilk dizi bağımsız değişkeninin isteğe bağlı ikinci dizi bağımsız değişkeniyle birleştirilmesinden elde edilen özniteliklerle bir kayıt eklenir.

`firstOrNew` metodu, `firstOrCreate` gibi, veritabanında verilen niteliklerle eşleşen bir kayıt bulmaya çalışır. Ancak, bir model bulunamazsa, yeni bir model örneği döndürülür. `firstOrNew` tarafından döndürülen modelin henüz veritabanına persiste edilmediğini unutmayın. Kalıcı hale getirmek için `save` metodunu manuel olarak çağırmanız gerekecektir:

```php
use App\Models\Flight;
 
// Retrieve flight by name or create it if it doesn't exist...
$flight = Flight::firstOrCreate([
    'name' => 'London to Paris'
]);
 
// Retrieve flight by name or create it with the name, delayed, and arrival_time attributes...
$flight = Flight::firstOrCreate(
    ['name' => 'London to Paris'],
    ['delayed' => 1, 'arrival_time' => '11:30']
);
 
// Retrieve flight by name or instantiate a new Flight instance...
$flight = Flight::firstOrNew([
    'name' => 'London to Paris'
]);
 
// Retrieve flight by name or instantiate with the name, delayed, and arrival_time attributes...
$flight = Flight::firstOrNew(
    ['name' => 'Tokyo to Sydney'],
    ['delayed' => 1, 'arrival_time' => '11:30']
);
```

## Aggregates

Eloquent modelleri ile etkileşim kurarken, Laravel sorgu oluşturucusu tarafından sağlanan `count`, `sum`, `max` ve diğer hesap metotlarını da kullanabilirsiniz. Tahmin edebileceğiniz gibi, bu metodlar bir Eloquent model örneği yerine bir skaler değer döndürür:

```php
$count = Flight::where('active', 1)->count();
 
$max = Flight::where('active', 1)->max('price');
```

# `#` Modellere Veri Ekleme ve Güncelleme
---

## Ekleme

Elbette, Eloquent kullanırken, yalnızca modelleri veritabanından almamız gerekmez. Yeni kayıtlar da eklememiz gerekir. Neyse ki, Eloquent bunu basitleştiriyor. Veritabanına yeni bir kayıt eklemek için, yeni bir model örneği oluşturmalı ve model üzerinde öznitelikleri ayarlamalısınız. Ardından, model örneği üzerinde `save` metodunu çağırın:

```php
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Models\Flight;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
 
class FlightController extends Controller
{
    /**
     * Store a new flight in the database.
     */
    public function store(Request $request): RedirectResponse
    {
        // Validate the request...
 
        $flight = new Flight;
 
        $flight->name = $request->name;
 
        $flight->save();
 
        return redirect('/flights');
    }
}
```

Bu örnekte, gelen HTTP isteğindeki `name` alanını `App\Models\Flight` model örneğinin `name` niteliğine atıyoruz. `save` metodunu çağırdığımızda, veritabanına bir kayıt eklenecektir. Modelin `created_at` ve `updated_at` zaman damgaları, `save` yöntemi çağrıldığında otomatik olarak ayarlanacaktır, bu nedenle bunları manuel olarak ayarlamaya gerek yoktur.

Alternatif olarak, tek bir PHP deyimi kullanarak yeni bir modeli "kaydetmek" için `create` metodunu kullanabilirsiniz. Eklenen model örneği `create` metodu tarafından size döndürülecektir:

```php
use App\Models\Flight;
 
$flight = Flight::create([
    'name' => 'London to Paris',
]);
```

Ancak, `create` metodunu kullanmadan önce, model sınıfınızda fillable (doldurulabilir) veya protected (korumalı) bir özellik belirtmeniz gerekecektir. Bu özellikler gereklidir çünkü tüm Eloquent modelleri varsayılan olarak toplu atama güvenlik açıklarına karşı korunmaktadır.

## Güncelleme

`save` metodu, veritabanında zaten var olan modelleri güncellemek için de kullanılabilir. Bir modeli güncellemek için modeli almalı ve güncellemek istediğiniz nitelikleri ayarlamalısınız. Ardından, modelin `save` metodunu çağırmalısınız. Yine, `updated_at` zaman damgası otomatik olarak güncellenecektir, bu nedenle değerini manuel olarak ayarlamanıza gerek yoktur:

```php
use App\Models\Flight;
 
$flight = Flight::find(1);
 
$flight->name = 'Paris to London';
 
$flight->save();
```

Bazen, mevcut bir modeli güncellemeniz veya eşleşen bir model yoksa yeni bir model oluşturmanız gerekebilir. `FirstOrCreate` metodu gibi, `updateOrCreate` metodu da modeli kalıcı hale getirir, böylece `save` metodunu manuel olarak çağırmanıza gerek kalmaz.

Aşağıdaki örnekte, `departure` (kalkış yeri) `Oakland` ve `destination` (varış yeri) `San Diego` olan bir uçuş mevcutsa, `price` (fiyatı) ve `discounted` (indirimli) sütunları güncellenecektir. Böyle bir uçuş yoksa, ilk bağımsız değişken dizisi ile ikinci bağımsız değişken dizisinin birleştirilmesinden elde edilen özniteliklere sahip yeni bir uçuş oluşturulur:

```php
$flight = Flight::updateOrCreate(
    ['departure' => 'Oakland', 'destination' => 'San Diego'],
    ['price' => 99, 'discounted' => 1]
);
```

### Toplu Güncellemeler

Güncellemeler, belirli bir sorguyla eşleşen modellere karşı da gerçekleştirilebilir. Bu örnekte, `active` (aktif) olan ve `destination` (varış noktası) `San Diego` olan tüm uçuşlar `delayed` (gecikmeli) olarak işaretlenecektir:

```php
Flight::where('active', 1)
      ->where('destination', 'San Diego')
      ->update(['delayed' => 1]);
```

`update` metodu, güncellenmesi gereken sütunları temsil eden bir sütun ve değer çiftleri dizisi bekler. `update` metodu, etkilenen satırların sayısını döndürür.

> Eloquent aracılığıyla bir toplu güncelleme yayınlarken, güncellenen modeller için `saving`, `saved`, `updating` ve `updated` model olayları ateşlenmeyecektir. Bunun nedeni, toplu güncelleme yayınlanırken modellerin hiçbir zaman gerçekten alınmamasıdır.

### Sütunlarda Değişikliklerin Belirlenmesi

Eloquent, modelinizin dahili durumunu incelemek ve özniteliklerinin modelin ilk alındığı zamana göre nasıl değiştiğini belirlemek için `isDirty`, `isClean` ve `wasChanged` metodlarını sağlar.

`isDirty` metodu, modelin alınmasından bu yana modelin özniteliklerinden herhangi birinin değiştirilip değiştirilmediğini belirler. Özniteliklerden herhangi birinin "kirli" olup olmadığını belirlemek için `isDirty` metoduna belirli bir öznitelik adı veya bir öznitelik dizisi aktarabilirsiniz. `isClean` metodu, model alındığından beri bir özniteliğin değişmeden kalıp kalmadığını belirler. Bu metod ayrıca isteğe bağlı bir öznitelik bağımsız değişkenini de kabul eder:

```php
use App\Models\User;
 
$user = User::create([
    'first_name' => 'Taylor',
    'last_name' => 'Otwell',
    'title' => 'Developer',
]);
 
$user->title = 'Painter';
 
$user->isDirty(); // true
$user->isDirty('title'); // true
$user->isDirty('first_name'); // false
$user->isDirty(['first_name', 'title']); // true
 
$user->isClean(); // false
$user->isClean('title'); // false
$user->isClean('first_name'); // true
$user->isClean(['first_name', 'title']); // false
 
$user->save();
 
$user->isDirty(); // false
$user->isClean(); // true
```

`wasChanged` metodu, model geçerli istek döngüsü içinde en son kaydedildiğinde herhangi bir özniteliğin değiştirilip değiştirilmediğini belirler. Gerekirse, belirli bir özniteliğin değiştirilip değiştirilmediğini görmek için bir öznitelik adı iletebilirsiniz:

```php
$user = User::create([
    'first_name' => 'Taylor',
    'last_name' => 'Otwell',
    'title' => 'Developer',
]);
 
$user->title = 'Painter';
 
$user->save();
 
$user->wasChanged(); // true
$user->wasChanged('title'); // true
$user->wasChanged(['title', 'slug']); // true
$user->wasChanged('first_name'); // false
$user->wasChanged(['first_name', 'title']); // true
```

`getOriginal` metodu, alındığından bu yana modelde yapılan değişikliklerden bağımsız olarak modelin orijinal özniteliklerini içeren bir dizi döndürür. Gerekirse, belirli bir özniteliğin orijinal değerini almak için belirli bir öznitelik adı geçebilirsiniz:

```php
$user = User::find(1);
 
$user->name; // John
$user->email; // john@example.com
 
$user->name = "Jack";
$user->name; // Jack
 
$user->getOriginal('name'); // John
$user->getOriginal(); // Array of original attributes...
```

## Toplu Atama

Tek bir PHP deyimi kullanarak yeni bir modeli "kaydetmek" için `create` metodunu kullanabilirsiniz. Eklenen model örneği metod tarafından size döndürülecektir:

```php
use App\Models\Flight;
 
$flight = Flight::create([
    'name' => 'London to Paris',
]);
```

Ancak, `create` metodunu kullanmadan önce, model sınıfınızda `fillable` veya `protected` bir özellik belirtmeniz gerekecektir. Bu özellikler gereklidir çünkü tüm Eloquent modelleri varsayılan olarak toplu atama güvenlik açıklarına karşı korunmaktadır.

Bir kullanıcı beklenmedik bir HTTP istek alanı ilettiğinde ve bu alan veritabanınızda beklemediğiniz bir sütunu değiştirdiğinde toplu atama güvenlik açığı oluşur. Örneğin, kötü niyetli bir kullanıcı HTTP isteği aracılığıyla bir `is_admin` parametresi gönderebilir ve bu parametre daha sonra modelinizin oluşturma metoduna aktarılarak kullanıcının kendisini bir yöneticiye yükseltmesine olanak tanır.

Bu nedenle, başlamak için hangi model niteliklerini toplu olarak atanabilir yapmak istediğinizi tanımlamanız gerekir. Bunu model üzerindeki `$fillable` özelliğini kullanarak yapabilirsiniz. Örneğin, `Flight` modelimizin `name` niteliğini toplu atanabilir yapalım:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = ['name'];
}
```

Hangi niteliklerin toplu olarak atanabilir olduğunu belirledikten sonra, veritabanına yeni bir kayıt eklemek için `create` metodunu kullanabilirsiniz. `create` metodu yeni oluşturulan model örneğini döndürür:

```php
$flight = Flight::create(['name' => 'London to Paris']);
```

Zaten bir model örneğiniz varsa, bunu bir dizi öznitelikle doldurmak için `fill` metodunu kullanabilirsiniz:

```php
$flight->fill(['name' => 'Amsterdam to Frankfurt']);
```

### Toplu Atama ve JSON Sütunlar

JSON sütunlarını atarken, her sütunun toplu atanabilir anahtarı modelinizin `$fillable` dizisinde belirtilmelidir. Güvenlik için, Laravel `guarded` özelliği kullanılırken iç içe geçmiş JSON niteliklerinin güncellenmesini desteklemez:

```php
/**
 * The attributes that are mass assignable.
 *
 * @var array
 */
protected $fillable = [
    'options->enabled',
];
```

### Toplu Atamaya İzin Verme

Tüm niteliklerinizi toplu olarak atanabilir yapmak isterseniz, modelinizin `$guarded` özelliğini boş bir dizi olarak tanımlayabilirsiniz. Modelinizin korumasını kaldırmayı seçerseniz, Eloquent'in `fill`, `create` ve `update` metodlarına aktarılan dizileri her zaman elle oluşturmaya özellikle dikkat etmelisiniz:

```php
/**
 * The attributes that aren't mass assignable.
 *
 * @var array
 */
protected $guarded = [];
```

### Toplu Atama İstisnaları

Varsayılan olarak, `$fillable` dizisine dahil edilmeyen öznitelikler toplu atama işlemleri gerçekleştirilirken sessizce atılır. Üretimde bu beklenen bir davranıştır; ancak yerel geliştirme sırasında model değişikliklerinin neden etkili olmadığı konusunda kafa karışıklığına yol açabilir.

Eğer isterseniz, `preventSilentlyDiscardingAttributes` metodunu çağırarak Laravel'e doldurulamayan bir özniteliği doldurmaya çalışırken bir istisna fırlatması talimatını verebilirsiniz. Tipik olarak, bu metot uygulamanızın `AppServiceProvider` sınıfının `boot` metodunda çağrılmalıdır:

```php
use Illuminate\Database\Eloquent\Model;
 
/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    Model::preventSilentlyDiscardingAttributes($this->app->isLocal());
}
```

## Upserts

Eloquent'in `upsert` metodu, tek bir atomik işlemde kayıtları güncellemek veya oluşturmak için kullanılabilir. Metodun ilk bağımsız değişkeni eklenecek veya güncellenecek değerlerden oluşurken, ikinci bağımsız değişken ilişkili tablodaki kayıtları benzersiz bir şekilde tanımlayan sütun(lar)ı listeler. Yöntemin üçüncü ve son bağımsız değişkeni, veritabanında eşleşen bir kayıt zaten mevcutsa güncellenmesi gereken sütunların bir dizisidir. Modelde zaman damgaları etkinleştirilmişse `upsert` metodu otomatik olarak `created_at` ve `updated_at` zaman damgalarını ayarlar:

```php
Flight::upsert([
    ['departure' => 'Oakland', 'destination' => 'San Diego', 'price' => 99],
    ['departure' => 'Chicago', 'destination' => 'New York', 'price' => 150]
], uniqueBy: ['departure', 'destination'], update: ['price']);
```

> SQL Server dışındaki tüm veritabanları, `upsert` metodunun ikinci bağımsız değişkenindeki sütunların "birincil" veya "benzersiz" bir dizine sahip olmasını gerektirir. Buna ek olarak, MySQL veritabanı sürücüsü `upsert` metodunun ikinci bağımsız değişkenini yok sayar ve mevcut kayıtları algılamak için her zaman tablonun "birincil" ve "benzersiz" dizinlerini kullanır.

# `#` Modellerin Silinmesi

Bir modeli silmek için, model örneği üzerinde `delete` metodunu çağırabilirsiniz:

```php
use App\Models\Flight;
 
$flight = Flight::find(1);
 
$flight->delete();
```

Modelin ilişkili veritabanı kayıtlarının tümünü silmek için `truncate` metodunnu çağırabilirsiniz. `truncate` işlemi, modelin ilişkili tablosundaki otomatik artan kimlikleri de sıfırlayacaktır:

```php
Flight::truncate();
```

## Mevcut Modeli Birincil Anahtarına Göre Silme

Yukarıdaki örnekte, `delete` metodunu çağırmadan önce modeli veritabanından alıyoruz. Ancak, modelin birincil anahtarını biliyorsanız, `destroy` metodunu çağırarak modeli açıkça almadan silebilirsiniz. Tek bir birincil anahtarı kabul etmenin yanı sıra, `destroy` metodu birden fazla birincil anahtarı, bir birincil anahtar dizisini veya bir birincil anahtar koleksiyonunu da kabul eder:

```php
Flight::destroy(1);
 
Flight::destroy(1, 2, 3);
 
Flight::destroy([1, 2, 3]);
 
Flight::destroy(collect([1, 2, 3]));
```

> `destroy` metodu her modeli ayrı ayrı yükler ve `delete` metodunu çağırır, böylece `deleting` ve `deleted` olayları her model için uygun şekilde gönderilir.

### Modelleri Şartlı Silme

Elbette, sorgunuzun kriterleriyle eşleşen tüm modelleri silmek için bir Eloquent sorgusu oluşturabilirsiniz. Bu örnekte, inaktif olarak işaretlenmiş tüm uçuşları sileceğiz. Toplu güncellemeler gibi, toplu silmeler de silinen modeller için model olayları göndermez:

```php
$deleted = Flight::where('active', 0)->delete();
```

>Eloquent aracılığıyla bir toplu silme ifadesi yürütülürken, silinen modeller için `deleting` ve `deleted` model olayları gönderilmeyecektir. Bunun nedeni, `delete` deyimi yürütülürken modellerin hiçbir zaman gerçekten alınmamasıdır.

## Soft Deleting (Yumuşak Silme)

Eloquent, kayıtları veritabanınızdan gerçekten kaldırmanın yanı sıra modelleri de "yazılımsal olarak silebilir". Modeller yazılımsal olarak silindiğinde, aslında veritabanınızdan kaldırılmazlar. Bunun yerine, model üzerinde modelin "silindiği" tarih ve saati belirten bir `deleted_at` özniteliği ayarlanır. Bir modelin yazılımla silinmesini etkinleştirmek için modele `Illuminate\Database\Eloquent\SoftDeletes` özelliğini ekleyin:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;
 
class Flight extends Model
{
    use SoftDeletes;
}
```

> `SoftDeletes` özelliği, `deleted_at` niteliğini sizin için otomatik olarak bir `DateTime` / `Carbon` örneğine dönüştürecektir.

Ayrıca veritabanı tablonuza `deleted_at` sütununu da eklemelisiniz. Laravel şema oluşturucu bu sütunu oluşturmak için bir yardımcı metod içerir:

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
Schema::table('flights', function (Blueprint $table) {
    $table->softDeletes();
});
 
Schema::table('flights', function (Blueprint $table) {
    $table->dropSoftDeletes();
});
```

Şimdi, model üzerinde `delete` metodunu çağırdığınızda, `deleted_at` sütunu geçerli tarih ve saate ayarlanacaktır. Ancak modelin veritabanı kaydı tabloda bırakılacaktır. Yazılımsal silme kullanan bir modeli sorgularken, yazılımsal silinen modeller otomatik olarak tüm sorgu sonuçlarından hariç tutulacaktır.

Belirli bir model örneğinin yazılımla silinip silinmediğini belirlemek için `trashed` metodunu kullanabilirsiniz:

```php
if ($flight->trashed()) {
    // ...
}
```

### Soft Deleted Modelleri Geri Yükleme

Bazen yazılımla silinmiş bir modeli "silmekten vazgeçmek" isteyebilirsiniz. Yazılımla silinmiş bir modeli geri yüklemek için model örneğinde `restore` metodunu çağırabilirsiniz. `restore` metodu, modelin `deleted_at` sütununu `null` olarak ayarlayacaktır:

```php
$flight->restore();
```

Birden fazla modeli geri yüklemek için bir sorguda `restore` metodunu da kullanabilirsiniz. Yine, diğer "toplu" işlemler gibi, bu da geri yüklenen modeller için herhangi bir model olayı göndermeyecektir:

```php
Flight::withTrashed()
        ->where('airline_id', 1)
        ->restore();
```

`restore` metodu ilişki sorguları oluşturulurken de kullanılabilir:

```php
$flight->history()->restore();
```

### Soft Deleted Modelleri Kalıcı Olarak Silme

Bazen bir modeli veritabanınızdan gerçekten kaldırmanız gerekebilir. Yazılımsal olarak silinmiş bir modeli veritabanı tablosundan kalıcı olarak kaldırmak için `forceDelete` metodunu kullanabilirsiniz:

```php
$flight->forceDelete();
```

Eloquent ilişki sorguları oluştururken `forceDelete` metodunu da kullanabilirsiniz:

```php
$flight->history()->forceDelete();
```










