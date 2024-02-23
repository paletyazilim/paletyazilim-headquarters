## Temel Routing
---
En temel Laravel rotaları bir URI ve bir closure kabul eder, karmaşık rota yapılandırma dosyaları olmadan rotaları ve davranışları tanımlamak için çok basit ve etkileyici bir yöntem sağlar:

```PHP title:routes/web.php'
use Illuminate\Support\Facades\Route;
 
Route::get('/greeting', function () {
    return 'Hello World';
});
```

#### Varsayılan Route Dosyaları
---
Tüm Laravel rotaları, `routes` dizininde bulunan rota dosyalarınızda tanımlanır. Bu dosyalar uygulamanızın `App\Providers\RouteServiceProvider`'ı tarafından otomatik olarak yüklenir. `routes/web.php` dosyası web arayüzünüz için olan rotaları tanımlar. Bu rotalara, oturum durumu ve CSRF koruması gibi özellikler sağlayan web middleware grubu atanır. `routes/api.php` dosyasındaki rotalar durumsuzdur ve `api` middleware grubuna atanmıştır.

Çoğu uygulama için, `routes/web.php` dosyanızda rotalar tanımlayarak başlayacaksınız. `routes/web.php` dosyasında tanımlanan rotalara, tanımlanan rotanın URL'sini tarayıcınıza girerek erişebilirsiniz. Örneğin, tarayıcınızda http://example.com/user adresine giderek aşağıdaki rotaya erişebilirsiniz:

```PHP
use App\Http\Controllers\UserController;
 
Route::get('/user', [UserController::class, 'index']);
```

`routes/api.php` dosyasında tanımlanan rotalar `RouteServiceProvider` tarafından bir rota grubu içinde iç içe yerleştirilir. Bu grup içinde `/api` URI öneki otomatik olarak uygulanır, böylece dosyadaki her rotaya manuel olarak uygulamanız gerekmez. `RouteServiceProvider` sınıfınızı değiştirerek öneki ve diğer rota grubu seçeneklerini değiştirebilirsiniz.

#### Router Methods
---
Router, herhangi bir HTTP fiiline yanıt veren rotaları kaydetmenize olanak tanır:

```PHP
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

Bazen birden fazla HTTP fiiline yanıt veren bir rota kaydetmeniz gerekebilir. Bunu `match` yöntemini kullanarak yapabilirsiniz. Ya da `any` yöntemini kullanarak tüm HTTP fiillerine yanıt veren bir rota kaydedebilirsiniz:

```PHP
Route::match(['get', 'post'], '/', function () {
    // ...
});
 
Route::any('/', function () {
    // ...
});
```

>Aynı URI'yi paylaşan birden fazla rota tanımlarken `get`, `post`, `put`, `patch`, `delete` ve `options` yöntemlerini kullanan rotalar `any`, `match` ve `redirect` yöntemlerini kullanan rotalardan önce tanımlanmalıdır. Bu, gelen isteğin doğru rota ile eşleştirilmesini sağlar.

#### Dependency Enjeksiyonu
---
Rotanızın geri çağırma imzasında rotanızın gerektirdiği dependencyleri yazabilirsiniz. Bildirilen dependencyler otomatik olarak çözümlenecek ve Laravel service containers (servis kapsamları) tarafından geri aramaya enjekte edilecektir. Örneğin, mevcut HTTP isteğinin rota geri çağırmanıza otomatik olarak enjekte edilmesi için `Illuminate\Http\Request` sınıfını işaretleyebilirsiniz:

###### CSRF Koruması
---
Web rotaları dosyasında tanımlanan `POST`, `PUT`, `PATCH` veya `DELETE` rotalarına işaret eden tüm HTML formlarının bir CSRF belirteci alanı içermesi gerektiğini unutmayın. Aksi takdirde istek reddedilecektir:

```PHP
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```

#### Redirect Routes (Yönlendirme Rotaları)
---
Başka bir URI'ye yönlendiren bir rota tanımlıyorsanız, `Route::redirect` metotunu kullanabilirsiniz. Bu metot, basit bir yönlendirme gerçekleştirmek için tam bir rota veya denetleyici tanımlamak zorunda kalmamanız için kullanışlı bir kısayol sağlar:

```PHP
Route::redirect('/here', '/there');
```

Varsayılan olarak, `Route::redirect` `302` durum kodunu döndürür. İsteğe bağlı üçüncü parametreyi kullanarak durum kodunu özelleştirebilirsiniz:

```PHP
Route::redirect('/here', '/there', 301);
```

Ya da `301` durum kodu döndürmek için `Route::permanentRedirect` metotunu kullanabilirsiniz:

```PHP
Route::permanentRedirect('/here', '/there');
```

>Yönlendirme rotalarında rota parametrelerini kullanırken, aşağıdaki parametreler Laravel tarafından ayrılmıştır ve kullanılamaz: `destination` ve `status`.

#### View Routes (View Rotaları)
---
Rotanızın yalnızca bir view döndürmesi gerekiyorsa, `Route::view` metotunu kullanabilirsiniz. `redirect` metotu gibi, bu yöntem de tam bir rota veya denetleyici tanımlamak zorunda kalmamanız için basit bir kısayol sağlar. `view` yöntemi ilk argüman olarak bir URI ve ikinci argüman olarak bir görünüm adı kabul eder. Buna ek olarak, isteğe bağlı üçüncü bir bağımsız değişken olarak görünüme aktarılacak bir veri dizisi sağlayabilirsiniz:

```PHP
Route::view('/welcome', 'welcome');
 
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
```

>Görünüm rotalarında rota parametrelerini kullanırken, aşağıdaki parametreler Laravel tarafından ayrılmıştır ve kullanılamaz: `view`, `data`, `status` ve `headers`.

#### Route List (Rota Listesi)
---
`route:list` Artisan komutu, uygulamanız tarafından tanımlanan tüm rotaların genel bir görünümünü kolayca sağlayabilir:

```
php artisan route:list
```

Varsayılan olarak, her rotaya atanan rota middlewareleri `route:list` çıktısında görüntülenmeyecektir; ancak, komuta `-v` seçeneğini ekleyerek Laravel'e rota middleware ve middleware grup adlarını görüntülemesi talimatını verebilirsiniz:

```
php artisan route:list -v
 
php artisan route:list -vv
```

Laravel'e sadece belirli bir URI ile başlayan rotaları göstermesi talimatını da verebilirsiniz:

```
php artisan route:list --path=api
```

Buna ek olarak, `route:list` komutunu çalıştırırken `--except-vendor` seçeneğini sağlayarak Laravel'e üçüncü taraf paketleri tarafından tanımlanan rotaları gizlemesi talimatını verebilirsiniz:

```
php artisan route:list --except-vendor
```

Aynı şekilde, `route:list` komutunu çalıştırırken `--only-vendor` seçeneğini sağlayarak Laravel'e sadece üçüncü taraf paketleri tarafından tanımlanan rotaları göstermesi talimatını da verebilirsiniz:

```
php artisan route:list --only-vendor
```

## Route Parameters (Rota Parametreleri)
---
#### Zorunlu Parametreler
---
Bazen rotanızdaki URI segmentlerini yakalamanız gerekebilir. Örneğin, URL'den bir kullanıcının kimliğini yakalamanız gerekebilir. Bunu rota parametreleri tanımlayarak yapabilirsiniz:

```PHP
Route::get('/user/{id}', function (string $id) {
    return 'Kullanıcı '.$id;
});
```

Rotanızın gerektirdiği sayıda rota parametresi tanımlayabilirsiniz:

```PHP 
Route::get('/posts/{post}/comments/{comment}', function (string $postId, string $commentId) {
    // ...
});
```

Rota parametreleri her zaman `{}` parantezleri içinde yer alır ve alfabetik karakterlerden oluşmalıdır. Rota parametre adları içinde alt çizgi (`_`) de kabul edilebilir. Rota parametreleri, sıralarına göre rota geri çağrılarına / controllerlara enjekte edilir - rota geri çağrısının / controllerlar argümanlarının adları önemli değildir.

###### Parametreler ve Dependency Enjeksiyonu
---
Eğer rotanızın Laravel service container (hizmet kapsamı) rotanızın geri çağrısına otomatik olarak enjekte etmesini istediğiniz bağımlılıkları varsa, rota parametrelerinizi dependencylerden sonra listelemelisiniz:

```PHP
use Illuminate\Http\Request;
 
Route::get('/user/{id}', function (Request $request, string $id) {
    return 'User '.$id;
});
```

#### Opsiyonel Parametreler
---
Bazen URI'de her zaman bulunmayan bir rota parametresi belirtmeniz gerekebilir. Bunu parametre adından sonra bir `?` işareti koyarak yapabilirsiniz. Rotanın ilgili değişkenine varsayılan bir değer verdiğinizden emin olun:

```PHP
Route::get('/user/{name?}', function (?string $name = null) {
    return $name;
});
 
Route::get('/user/{name?}', function (?string $name = 'John') {
    return $name;
});
```

#### RegEx Kısıtlama
---
Bir rota örneğindeki `where` metotunu kullanarak rota parametrelerinizin biçimini kısıtlayabilirsiniz. `where` metotu, parametrenin adını ve parametrenin nasıl kısıtlanması gerektiğini tanımlayan düzenli bir ifadeyi kabul eder:

```PHP
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

Kolaylık sağlamak için, yaygın olarak kullanılan bazı RegEx kalıpları, rotalarınıza kalıp kısıtlamalarını hızlı bir şekilde eklemenize olanak tanıyan yardımcı metotlara sahiptir:

```PHP
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

Gelen istek rota kalıbı kısıtlamalarıyla eşleşmiyorsa 404 HTTP yanıtı döndürülür.

###### Global Kısıtlama
---
Bir rota parametresinin her zaman belirli bir RegExp ile kısıtlanmasını istiyorsanız, kalıp yöntemini kullanabilirsiniz. Bu kalıpları `App\Providers\RouteServiceProvider` sınıfınızın `boot` metotunda tanımlamalısınız:

```PHP
public function boot(): void
{
    Route::pattern('id', '[0-9]+');
}
```

Kalıp tanımlandıktan sonra, bu parametre adını kullanan tüm rotalara otomatik olarak uygulanır:

```PHP
Route::get('/user/{id}', function (string $id) {
    // ...
});
```

Laravel yönlendirme bileşeni `/` hariç tüm karakterlerin rota parametre değerleri içinde bulunmasına izin verir. Bir `where` koşulu RegEx kullanarak `/` karakterinin yer tutucunuzun bir parçası olmasına açıkça izin vermelisiniz:

```PHP
Route::get('/search/{search}', function (string $search) {
    return $search;
})->where('search', '.*');
```

>Kodlanmış ileri eğik çizgiler yalnızca son rota segmenti içinde desteklenir.