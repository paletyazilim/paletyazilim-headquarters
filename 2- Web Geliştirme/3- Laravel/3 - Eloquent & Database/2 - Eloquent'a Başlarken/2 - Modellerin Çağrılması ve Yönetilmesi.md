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

Eloquent, kayıtları veritabanınızdan gerçekten kaldırmanın yanı sıra modelleri de "yumuşak silebilir". Modeller soft deleted olarak silindiğinde, aslında veritabanınızdan kaldırılmazlar. Bunun yerine, model üzerinde modelin "silindiği" tarih ve saati belirten bir `deleted_at` niteliği ayarlanır. Bir modelin soft deleted silinmesini etkinleştirmek için modele `Illuminate\Database\Eloquent\SoftDeletes` özelliğini ekleyin:

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

Şimdi, model üzerinde `delete` metodunu çağırdığınızda, `deleted_at` sütunu geçerli tarih ve saate ayarlanacaktır. Ancak modelin veritabanı kaydı tabloda bırakılacaktır. Soft deleted silme kullanan bir modeli sorgularken, soft deleted silinen modeller otomatik olarak tüm sorgu sonuçlarından hariç tutulacaktır.

Belirli bir model örneğinin soft deleted silinip silinmediğini belirlemek için `trashed` metodunu kullanabilirsiniz:

```php
if ($flight->trashed()) {
    // ...
}
```

### Soft Deleted Modelleri Geri Yükleme

Bazen soft deleted silinmiş bir modeli "silmekten vazgeçmek" isteyebilirsiniz. Soft deleted silinmiş bir modeli geri yüklemek için model örneğinde `restore` metodunu çağırabilirsiniz. `restore` metodu, modelin `deleted_at` sütununu `null` olarak ayarlayacaktır:

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

Bazen bir modeli veritabanınızdan gerçekten kaldırmanız gerekebilir. Soft deleted olarak silinmiş bir modeli veritabanı tablosundan kalıcı olarak kaldırmak için `forceDelete` metodunu kullanabilirsiniz:

```php
$flight->forceDelete();
```

Eloquent ilişki sorguları oluştururken `forceDelete` metodunu da kullanabilirsiniz:

```php
$flight->history()->forceDelete();
```

## Soft Deleted Modelleri Sorgulama

### Soft Deleted Modeller Dahil Alma

Yukarıda belirtildiği gibi, soft deleted silinen modeller otomatik olarak sorgu sonuçlarından hariç tutulacaktır. Ancak, sorgu üzerinde `withTrashed` metodunu çağırarak soft deleted silinmiş modelleri sorgu sonuçlarına dahil edilmeye zorlayabilirsiniz:

```php
use App\Models\Flight;
 
$flights = Flight::withTrashed()
                ->where('account_id', 1)
                ->get();
```

`withTrashed` metodu bir ilişki sorgusu oluşturulurken de çağrılabilir:

```php
$flight->history()->withTrashed()->get();
```

### Yalnızca Soft Deleted Modelleri Alma

`onlyTrashed` metodu yalnızca soft deleted silinen modelleri geri getirir:

```php
$flights = Flight::onlyTrashed()
                ->where('airline_id', 1)
                ->get();
```
