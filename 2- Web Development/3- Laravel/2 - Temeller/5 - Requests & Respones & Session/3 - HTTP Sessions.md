# `#` Giriş
---
HTTP güdümlü uygulamalar durum bilgisi içermediğinden, oturumlar kullanıcı hakkındaki bilgileri birden fazla istek boyunca saklamak için bir yol sağlar. Bu kullanıcı bilgileri genellikle sonraki isteklerden erişilebilen kalıcı bir depoya / arka uca yerleştirilir.

Laravel, etkileyici, birleşik bir API aracılığıyla erişilen çeşitli oturum arka uçları ile birlikte gelir. Memcached, Redis ve veritabanları gibi popüler arka uçlar için destek dahildir.

## Yapılandırma

Uygulamanızın oturum yapılandırma dosyası `config/session.php` adresinde saklanır. Bu dosyada kullanabileceğiniz seçenekleri gözden geçirdiğinizden emin olun. Varsayılan olarak, Laravel `database` oturum driverini kullanacak şekilde yapılandırılmıştır.

Oturum `driver` yapılandırma seçeneği, oturum verilerinin her istek için nerede saklanacağını tanımlar. Laravel çeşitli sürücüler içerir:

- `file` - oturumlar `storage/framework/sessions` içinde saklanır.
- `cookie` - oturumlar güvenli, şifrelenmiş çerezlerde saklanır.
- `database` - oturumlar ilişkisel bir veritabanında saklanır.
- `memcached` / `redis` - oturumlar bu hızlı, önbellek tabanlı depolardan birinde saklanır.
- `dynamodb` - oturumlar AWS DynamoDB'de saklanır.
- `array` - oturumlar bir PHP dizisinde saklanır ve kalıcı hale getirilmez.

>`array` driveri öncelikle test sırasında kullanılır ve oturumda depolanan verilerin kalıcı olmasını engeller.

# `#` Driver Koşulları
---
## Database (Veritabanı)

Veritabanı oturum driverini kullanırken, oturum verilerini içerecek bir veritabanı tablonuz olduğundan emin olmanız gerekecektir. Tipik olarak, bu Laravel'in varsayılan `0001_01_000000_create_users_table.php` veritabanı migration'una dahil edilir; ancak, herhangi bir nedenle bir oturum tablonuz yoksa, oluşturmak için `make:session-table` Artisan komutunu kullanabilirsiniz:

## Redis

Laravel ile Redis oturumlarını kullanmadan önce, PECL üzerinden PhpRedis PHP eklentisini yüklemeniz veya Composer üzerinden `predis/predis` paketini (~1.0) yüklemeniz gerekecektir. Redis'i yapılandırma hakkında daha fazla bilgi için Laravel'in Redis dokümantasyonuna bakın.

>`SESSION_CONNECTION` ortam değişkeni veya `session.php` yapılandırma dosyasındaki `connection` seçeneği, oturum depolama için hangi Redis bağlantısının kullanılacağını belirtmek için kullanılabilir.

# `#` Oturumla Etkileşime Girme
---

## Veri Alma

Laravel'de oturum verisi ile çalışmanın iki temel yolu vardır: global `session` helper'ı ve bir `Request` örneği üzerinden. İlk olarak, bir rota closure'u veya controller metodu üzerinde tip ipucu verilebilen bir `Request` örneği üzerinden oturuma erişmeye bakalım. Unutmayın, controller metot bağımlılıkları Laravel service container üzerinden otomatik olarak enjekte edilir:

```php
<?php
 
namespace App\Http\Controllers;
 
use Illuminate\Http\Request;
use Illuminate\View\View;
 
class UserController extends Controller
{
    /**
     * Show the profile for the given user.
     */
    public function show(Request $request, string $id): View
    {
        $value = $request->session()->get('key');
 
        // ...
 
        $user = $this->users->find($id);
 
        return view('user.profile', ['user' => $user]);
    }
}
```

Oturumdan bir öğe aldığınızda, `get` metoduna ikinci bağımsız değişken olarak varsayılan bir değer de aktarabilirsiniz. Belirtilen anahtar oturumda mevcut değilse bu varsayılan değer döndürülür. `get` metoduna varsayılan değer olarak bir closure iletirseniz ve istenen key mevcut değilse, closure çalıştırılır ve sonucu döndürülür:

```php
$value = $request->session()->get('key', 'default');
 
$value = $request->session()->get('key', function () {
    return 'default';
});
```

### Global Session Helper (Yardımcısı)

Oturumdaki verileri almak ve saklamak için global `session` PHP işlevini de kullanabilirsiniz. `session` yardımcısı tek bir dize argümanıyla çağrıldığında, o oturum anahtarının değerini döndürür. Yardımcı, anahtar/değer çiftlerinden oluşan bir dizi ile çağrıldığında, bu değerler oturumda saklanacaktır:

```php
Route::get('/home', function () {
    // Retrieve a piece of data from the session...
    $value = session('key');
 
    // Specifying a default value...
    $value = session('key', 'default');
 
    // Store a piece of data in the session...
    session(['key' => 'value']);
});
```

Oturumu bir HTTP istek örneği aracılığıyla kullanmak ile global `session` yardımcısını kullanmak arasında pratikte çok az fark vardır. Her iki metotta tüm test senaryolarınızda bulunan `assertSessionHas` metodu aracılığıyla test edilebilir.

### Bütün Oturum Verilerini Alma

Oturumdaki tüm verileri almak isterseniz, `all` metodunu kullanabilirsiniz:

```php
$data = $request->session()->all();
```

### Belirli Oturum Verilerini Alma

Oturum verilerinin bir kısmını almak için `only` ve `except` metodları kullanılabilir:

```php
$data = $request->session()->only(['username', 'email']);
 
$data = $request->session()->except(['username', 'email']);
```

### Oturumda Öğenin Varlığını Kontrol Etmek

Bir öğenin oturumda mevcut olup olmadığını belirlemek için `has` metodunu kullanabilirsiniz. Öğe mevcutsa ve `null` değilse `has` metodu `true` sonucunu döndürür:

```php
if ($request->session()->has('users')) {
    // ...
}
```

Değeri `null` olsa bile bir öğenin oturumda mevcut olup olmadığını belirlemek için `exists` metodunnu kullanabilirsiniz:

```php
if ($request->session()->exists('users')) {
    // ...
}
```

Bir öğenin oturumda bulunup bulunmadığını belirlemek için `missing` metodunu kullanabilirsiniz. Öğe mevcut değilse `missing` metodu `true` değerini döndürür:

```php
if ($request->session()->missing('users')) {
    // ...
}
```

## Veri Depolama

Verileri oturumda saklamak için genellikle request örneğinin `put` metodunu veya global `session` yardımcısını kullanırsınız:

```php
$request->session()->put('key', 'value');
 
// Via the global "session" helper...
session(['key' => 'value']);
```

### Oturumlarda Dizi Değerlere Yeni Değer İtme

`push` metodu, bir dizi olan bir oturum değerine yeni bir değer itmek için kullanılabilir. Örneğin, `user.teams` anahtarı takım adlarından oluşan bir dizi içeriyorsa, diziye aşağıdaki gibi yeni bir değer atayabilirsiniz:

```php
$request->session()->push('user.teams', 'developers');
```

### Öğeyi Geri Alma ve Silme

`pull` metodu, tek bir deyimde oturumdan bir öğeyi alır ve siler:

```php
$value = $request->session()->pull('key', 'default');
```

### Oturum Değerlerini Arttırma ve Azaltma

Oturum verileriniz artırmak veya azaltmak istediğiniz bir tamsayı içeriyorsa, `increment` ve `decrement` metodlarını kullanabilirsiniz:

```php
$request->session()->increment('count');
 
$request->session()->increment('count', $incrementBy = 2);
 
$request->session()->decrement('count');
 
$request->session()->decrement('count', $decrementBy = 2);
```

## Flash Veri

Bazen öğeleri bir sonraki istek için oturumda saklamak isteyebilirsiniz. Bunu `flash` metodunu kullanarak yapabilirsiniz. Bu metot kullanılarak oturumda saklanan veriler hemen ve sonraki HTTP isteği sırasında kullanılabilir olacaktır. Sonraki HTTP isteğinden sonra, flaşlanan veriler silinecektir. Flash verileri öncelikle kısa ömürlü durum mesajları için kullanışlıdır:

```php
$request->session()->flash('status', 'Task was successful!');
```

Flash verilerinizi birkaç istek için saklamanız gerekiyorsa, tüm flash verilerini ek bir istek için saklayacak olan `reflash` metodunu kullanabilirsiniz. Yalnızca belirli flash verilerini tutmanız gerekiyorsa `keep` metodunu kullanabilirsiniz:

```php
$request->session()->reflash();
 
$request->session()->keep(['username', 'email']);
```

Flash verilerinizi yalnızca geçerli istek için kalıcı hale getirmek için `now` metodunu kullanabilirsiniz:

```php
$request->session()->now('status', 'Task was successful!');
```

## Veri Silme

`forget` metodu oturumdan bir veri parçasını kaldıracaktır. Oturumdaki tüm verileri kaldırmak isterseniz `flush` metodunu kullanabilirsiniz:

```php
// Forget a single key...
$request->session()->forget('name');
 
// Forget multiple keys...
$request->session()->forget(['name', 'status']);
 
$request->session()->flush();
```

## Oturum Kimliğini Yeniden Oluşturma

Oturum kimliğinin yeniden oluşturulması genellikle kötü niyetli kullanıcıların uygulamanıza yönelik bir oturum sabitleme saldırısından yararlanmasını önlemek için yapılır.

Laravel uygulama başlangıç kitlerinden birini veya Laravel Fortify'ı kullanıyorsanız, Laravel kimlik doğrulama sırasında oturum kimliğini otomatik olarak yeniden oluşturur; ancak, oturum kimliğini manuel olarak yeniden oluşturmanız gerekiyorsa, `regenerate` metodunu kullanabilirsiniz:

```php
$request->session()->regenerate();
```

Oturum kimliğini yeniden oluşturmanız ve oturumdaki tüm verileri tek bir deyimde kaldırmanız gerekiyorsa `invalidate` metodunu kullanabilirsiniz:

```php
$request->session()->invalidate();
```

# `#` Oturum Engelleme
---

> Oturum engellemeyi kullanmak için uygulamanızın atomik kilitleri destekleyen bir önbellek sürücüsü kullanıyor olması gerekir. Şu anda, bu önbellek sürücüleri `memcached`, `dynamodb`, `redis`, `database`, `file` ve `array` sürücülerini içermektedir. Ayrıca, `cookie` oturum sürücüsünü kullanamazsınız.

Varsayılan olarak, Laravel aynı oturumu kullanan isteklerin eşzamanlı olarak yürütülmesine izin verir. Yani, örneğin, uygulamanıza iki HTTP isteği yapmak için bir JavaScript HTTP kütüphanesi kullanırsanız, her ikisi de aynı anda yürütülecektir. Birçok uygulama için bu bir sorun değildir; ancak, her ikisi de oturuma veri yazan iki farklı uygulama uç noktasına eşzamanlı istek yapan uygulamaların küçük bir alt kümesinde oturum veri kaybı meydana gelebilir.

#çevrilecek 









