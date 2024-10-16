# `#` Pruning Model (Budama)
---
Bazen artık ihtiyaç duyulmayan modelleri periyodik olarak silmek isteyebilirsiniz. Bunu gerçekleştirmek için, periyodik olarak silmek istediğiniz modellere `Illuminate\Database\Eloquent\Prunable` veya `Illuminate\Database\Eloquent\MassPrunable` özelliğini ekleyebilirsiniz. Özelliklerden birini modele ekledikten sonra, artık ihtiyaç duyulmayan modelleri çözümleyen bir Eloquent sorgu oluşturucusu döndüren bir `prunable` metodu uygulayın:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Prunable;
 
class Flight extends Model
{
    use Prunable;
 
    /**
     * Get the prunable model query.
     */
    public function prunable(): Builder
    {
        return static::where('created_at', '<=', now()->subMonth());
    }
}
```

Modelleri `Prunable` olarak işaretlerken, model üzerinde bir `pruning` metodu da tanımlayabilirsiniz. Bu metot, model silinmeden önce çağrılacaktır. Bu metot, model veritabanından kalıcı olarak kaldırılmadan önce depolanan dosyalar gibi modelle ilişkili tüm ek kaynakları silmek için yararlı olabilir:

```php
/**
 * Prepare the model for pruning.
 */
protected function pruning(): void
{
    // ...
}
```

`Prunable` modelinizi yapılandırdıktan sonra, uygulamanızın `routes/console.php` dosyasında `model:prune` Artisan komutunu zamanlamalısınız. Bu komutun çalıştırılacağı uygun aralığı seçmekte özgürsünüz:

```php
use Illuminate\Support\Facades\Schedule;
 
Schedule::command('model:prune')->daily();
```

Sahne arkasında, `model:prune` komutu uygulamanızın `app/Models` dizini içindeki "Prunable" modelleri otomatik olarak algılar. Modelleriniz farklı bir konumdaysa, model sınıfı adlarını belirtmek için `--model` seçeneğini kullanabilirsiniz:

```php
Schedule::command('model:prune', [
    '--model' => [Address::class, Flight::class],
])->daily();
```

Algılanan diğer tüm modeller silinirken belirli modelleri silme işlemi dışında bırakmak isterseniz `--except` seçeneğini kullanabilirsiniz:

```php
Schedule::command('model:prune', [
    '--except' => [Address::class, Flight::class],
])->daily();
```

`prunable` sorgunuzu `model:prune` komutunu `--pretend` seçeneği ile çalıştırarak test edebilirsiniz. Çalıştırıldığında, `model:prune` komutu, komutun gerçekten çalışması durumunda kaç kaydın silineceğini bildirecektir:

```php
php artisan model:prune --pretend
```

> Soft deleted modeller, pruneable bir sorguyla eşleşirse kalıcı olarak silinir (`forceDelete`).

## Mass Pruning (Toplu Budama)

Modeller `Illuminate\Database\Eloquent\MassPrunable` özelliği ile işaretlendiğinde, modeller toplu silme sorguları kullanılarak veritabanından silinir. Bu nedenle, `prunable` metodu çağrılmayacak, `deleting` ve `deleted` model eventleri gönderilmeyecektir. Bunun nedeni, modellerin silinmeden önce hiçbir zaman gerçekten alınmaması ve böylece prune işleminin çok daha verimli hale gelmesidir:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\MassPrunable;
 
class Flight extends Model
{
    use MassPrunable;
 
    /**
     * Get the prunable model query.
     */
    public function prunable(): Builder
    {
        return static::where('created_at', '<=', now()->subMonth());
    }
}
```

# `#` Replicating Models (Kopyalama)
---
`replicate` metodunu kullanarak mevcut bir model örneğinin kaydedilmemiş bir kopyasını oluşturabilirsiniz. Bu metot özellikle aynı niteliklerin çoğunu paylaşan model örnekleriniz olduğunda kullanışlıdır:

```php
use App\Models\Address;
 
$shipping = Address::create([
    'type' => 'shipping',
    'line_1' => '123 Example Street',
    'city' => 'Victorville',
    'state' => 'CA',
    'postcode' => '90001',
]);
 
$billing = $shipping->replicate()->fill([
    'type' => 'billing'
]);
 
$billing->save();
```

Bir veya daha fazla niteliğin yeni modele çoğaltılmasını engellemek için, `replicate` metotuna bir dizi aktarabilirsiniz:

```php
$flight = Flight::create([
    'destination' => 'LAX',
    'origin' => 'LHR',
    'last_flown' => '2020-03-04 11:00:00',
    'last_pilot_id' => 747,
]);
 
$flight = $flight->replicate([
    'last_flown',
    'last_pilot_id'
]);
```
