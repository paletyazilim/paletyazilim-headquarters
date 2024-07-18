# `#` Giriş
---
Accessor'lar, mutator'lar ve attribute casting, Eloquent attribute değerlerini model örnekleri üzerinde aldığınızda veya ayarladığınızda dönüştürmenize izin verir. Örneğin, veritabanında saklanırken bir değeri şifrelemek için Laravel şifreleyicisini kullanmak ve daha sonra bir Eloquent modelinde eriştiğinizde özniteliğin şifresini otomatik olarak çözmek isteyebilirsiniz. Veya, veritabanınızda saklanan bir JSON dizesini Eloquent modeliniz üzerinden erişildiğinde bir diziye dönüştürmek isteyebilirsiniz.

# `#` Accessors and Mutators
---
## Accessor Tanımlama
Bir accessor, bir Eloquent nitelik değerine erişildiğinde onu dönüştürür. Bir accessor tanımlamak için, erişilebilir niteliği temsil etmek üzere modelinizde korumalı bir metot oluşturun. Bu metot adı, uygun olduğunda, gerçek temel model niteliğinin / veritabanı sütununun "camel case" temsiline karşılık gelmelidir.

Bu örnekte, `first_name` niteliği için bir accessor tanımlayacağız. Bu accessor, `first_name` niteliğinin değeri alınmaya çalışıldığında Eloquent tarafından otomatik olarak çağrılacaktır. Tüm nitelik erişimci/mutator metotları `Illuminate\Database\Eloquent\Casts\Attribute` dönüş türü ipucu bildirmelidir:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Casts\Attribute;
use Illuminate\Database\Eloquent\Model;
 
class User extends Model
{
    /**
     * Get the user's first name.
     */
    protected function firstName(): Attribute
    {
        return Attribute::make(
            get: fn (string $value) => ucfirst($value),
        );
    }
}
```

Tüm accessor metotları, niteliğe nasıl erişileceğini ve isteğe bağlı olarak nasıl değiştirileceğini tanımlayan bir `Attribute` örneği döndürür. Bu örnekte, yalnızca niteliğe nasıl erişileceğini tanımlıyoruz. Bunu yapmak için, `Attribute` sınıfı kurucusuna `get` argümanını sağlıyoruz.

Gördüğünüz gibi, sütunun orijinal değeri erişiciye aktarılır ve değeri değiştirmenize ve döndürmenize olanak tanır. Erişimcinin değerine erişmek için, bir model örneğindeki `first_name` niteliğine erişebilirsiniz:

```php
use App\Models\User;
 
$user = User::find(1);
 
$firstName = $user->first_name;
```