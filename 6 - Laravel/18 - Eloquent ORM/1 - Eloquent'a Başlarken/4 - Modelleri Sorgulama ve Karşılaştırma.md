# `#` Query Scopes (Sorgu Kapsamları)
---
## Global Scopes

Global kapsamlar, belirli bir model için tüm sorgulara kısıtlamalar eklemenize izin verir. Laravel'in kendi soft delete işlevi, veritabanından yalnızca "silinmemiş" modelleri almak için global kapsamları kullanır. Kendi global kapsamlarınızı yazmak, belirli bir model için her sorgunun belirli kısıtlamaları aldığından emin olmak için uygun ve kolay bir yol sağlayabilir.

### Scope Oluşturma

Yeni bir global kapsam oluşturmak için, oluşturulan kapsamı uygulamanızın `app/Models/Scopes` dizinine yerleştirecek olan `make:scope` Artisan komutunu çağırabilirsiniz:

```shell
php artisan make:scope AncientScope
```

### Global Scope Yazma

Global bir kapsam yazmak basittir. İlk olarak, `Illuminate\Database\Eloquent\Scope` arayüzünü uygulayan bir sınıf oluşturmak için `make:scope` komutunu kullanın. `Scope` arayüzü tek bir metotu uygulamanızı gerektirir: `apply`. `apply` metodu, gerektiğinde sorguya `where` kısıtlamaları veya diğer cümle türlerini ekleyebilir:

```php
<?php
 
namespace App\Models\Scopes;
 
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Scope;
 
class AncientScope implements Scope
{
    /**
     * Apply the scope to a given Eloquent query builder.
     */
    public function apply(Builder $builder, Model $model): void
    {
        $builder->where('created_at', '<', now()->subYears(2000));
    }
}
```

> Genel kapsamınız sorgunun `select` cümlesine sütunlar eklemekse, `select` yerine `addSelect` metodunu kullanmalısınız. Bu, sorgunun mevcut `select` cümlesinin istemeden değiştirilmesini önleyecektir.

### Global Scope'un Uygulanması

Bir modele global bir kapsam atamak için, `ScopedBy` niteliğini modele yerleştirebilirsiniz:

```php
<?php
 
namespace App\Models;
 
use App\Models\Scopes\AncientScope;
use Illuminate\Database\Eloquent\Attributes\ScopedBy;
 
#[ScopedBy([AncientScope::class])]
class User extends Model
{
    //
}
```

Ya da modelin `booted` metodunu geçersiz kılarak ve modelin `addGlobalScope` metodunu çağırarak global kapsamı manuel olarak kaydedebilirsiniz. `addGlobalScope` metodu, kapsamınızın bir örneğini tek bağımsız değişkeni olarak kabul eder:

```php
<?php
 
namespace App\Models;
 
use App\Models\Scopes\AncientScope;
use Illuminate\Database\Eloquent\Model;
 
class User extends Model
{
    /**
     * The "booted" method of the model.
     */
    protected static function booted(): void
    {
        static::addGlobalScope(new AncientScope);
    }
}
```

Yukarıdaki örnekte yer alan kapsam `App\Models\User` modeline eklendikten sonra `User::all()` metoduna yapılan bir çağrı aşağıdaki SQL sorgusunu çalıştıracaktır:

```MySQL
select * from `users` where `created_at` < 0021-02-18 00:00:00
```

### Anonim Global Scopelar

Eloquent ayrıca, kendi başına ayrı bir sınıf gerektirmeyen basit kapsamlar için özellikle yararlı olan closure'ları kullanarak global kapsamlar tanımlamanıza izin verir. Bir closure kullanarak global bir kapsam tanımlarken, `addGlobalScope` metoduna ilk argüman olarak kendi seçtiğiniz bir kapsam adı sağlamalısınız:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
 
class User extends Model
{
    /**
     * The "booted" method of the model.
     */
    protected static function booted(): void
    {
        static::addGlobalScope('ancient', function (Builder $builder) {
            $builder->where('created_at', '<', now()->subYears(2000));
        });
    }
}
```

### Global Scope'ları Silme

Belirli bir sorgu için bir global kapsamı kaldırmak isterseniz, `withoutGlobalScope` metodunu kullanabilirsiniz. Bu metot, tek bağımsız değişkeni olarak global kapsamın sınıf adını kabul eder:

```php
User::withoutGlobalScope(AncientScope::class)->get();
```

Veya global kapsamı bir closure kullanarak tanımladıysanız, global kapsama atadığınız dize adını geçirmelisiniz:

```php
User::withoutGlobalScope('ancient')->get();
```

Sorgunun global kapsamlarından birkaçını veya tümünü kaldırmak isterseniz, `withoutGlobalScopes` metotunu kullanabilirsiniz:

```php
// Remove all of the global scopes...
User::withoutGlobalScopes()->get();
 
// Remove some of the global scopes...
User::withoutGlobalScopes([
    FirstScope::class, SecondScope::class
])->get();
```

## Local Scopes

Local kapsamlar, uygulamanızın tamamında kolayca yeniden kullanabileceğiniz ortak sorgu kısıtlamaları kümeleri tanımlamanıza olanak tanır. Örneğin, "popular" olarak kabul edilen tüm kullanıcıları sık sık almanız gerekebilir. Bir kapsam tanımlamak için, bir Eloquent model metotunun önüne `scope` ekleyin.

Kapsamlar her zaman aynı sorgu oluşturucu örneğini veya `void`'i döndürmelidir:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
 
class User extends Model
{
    /**
     * Scope a query to only include popular users.
     */
    public function scopePopular(Builder $query): void
    {
        $query->where('votes', '>', 100);
    }
 
    /**
     * Scope a query to only include active users.
     */
    public function scopeActive(Builder $query): void
    {
        $query->where('active', 1);
    }
}
```

### Yerel Scope Sorguları

Kapsam tanımlandıktan sonra, modeli sorgularken kapsam yöntemlerini çağırabilirsiniz. Ancak, metotu çağırırken `scope` önekini eklememeniz gerekir. Hatta çeşitli kapsamlara yapılan çağrıları zincirleyebilirsiniz:

```php
use App\Models\User;
 
$users = User::popular()->active()->orderBy('created_at')->get();
```

Birden fazla Eloquent model kapsamının bir `or` sorgu operatörü aracılığıyla birleştirilmesi, doğru mantıksal gruplamayı elde etmek için kapanışların kullanılmasını gerektirebilir:

```php
$users = User::popular()->orWhere(function (Builder $query) {
    $query->active();
})->get();
```

Ancak, bu zahmetli olabileceğinden, Laravel kapsamları closure kullanmadan akıcı bir şekilde birbirine zincirlemenize izin veren bir "üst düzey" `orWhere` metotu sağlar:

```php
$users = User::popular()->orWhere->active()->get();
```

### Dinamik Scope'lar

Bazen parametre kabul eden bir kapsam tanımlamak isteyebilirsiniz. Başlamak için, ek parametrelerinizi kapsam metotunuzun parametresine eklemeniz yeterlidir. Kapsam parametreleri `$query` parametresinden sonra tanımlanmalıdır:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;
 
class User extends Model
{
    /**
     * Scope a query to only include users of a given type.
     */
    public function scopeOfType(Builder $query, string $type): void
    {
        $query->where('type', $type);
    }
}
```

Kapsam metotunuzun parametresine beklenen argümanlar eklendikten sonra, kapsamı çağırırken parametreyi iletebilirsiniz:

```php
$users = User::ofType('admin')->get();
```

# `#` Modellerin Karşılaştırılması (Comparing)
---
Bazen iki modelin "aynı" olup olmadığını belirlemeniz gerekebilir. `is` ve `isNot` metotları, iki modelin aynı birincil anahtara, tabloya ve veritabanı bağlantısına sahip olup olmadığını hızlı bir şekilde doğrulamak için kullanılabilir:

```php
if ($post->is($anotherPost)) {
    // ...
}
 
if ($post->isNot($anotherPost)) {
    // ...
}
```

`is` ve `isNot` metotları ayrıca `belongsTo`, `hasOne`, `morphTo` ve `morphOne` ilişkileri kullanılırken de kullanılabilir. Bu metot özellikle ilgili bir modeli, o modeli almak için bir sorgu yayınlamadan karşılaştırmak istediğinizde faydalıdır:

```php
if ($post->author()->is($user)) {
    // ...
}
```
