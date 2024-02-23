## Basit Rotalama
---
En temel Laravel rotaları bir URI ve bir closure kabul eder, karmaşık rota yapılandırma dosyaları olmadan rotaları ve davranışları tanımlamak için çok basit ve etkileyici bir yöntem sağlar:

```PHP title:'routes/web.php'
use Illuminate\Support\Facades\Route;
 
Route::get('/example', function () {
    return 'Merhaba Dünya';
});
```

#### Varsayılan `Route` Dosyaları
---
Tüm Laravel rotaları, `routes` dizininde bulunan rota dosyalarınızda tanımlanır. Bu dosyalar uygulamanızın `App\Providers\RouteServiceProvider`'ı tarafından otomatik olarak yüklenir. `routes/web.php` dosyası web arayüzünüz için olan rotaları tanımlar. Bu rotalara, oturum durumu ve CSRF koruması gibi özellikler sağlayan web ara yazılım grubu atanır. `routes/api.php` dosyasındaki rotalar durumsuzdur ve `api` middleware grubuna atanmıştır.

Çoğu uygulama için, `routes/web.php` dosyanızda rotalar tanımlayarak başlayacaksınız. `routes/web.php` dosyasında tanımlanan rotalara, tanımlanan rotanın URL'sini tarayıcınıza girerek erişebilirsiniz. Örneğin, tarayıcınızda http://example.com/user adresine giderek aşağıdaki rotaya erişebilirsiniz:

```PHP title:'routes/web.php'
use App\Http\Controllers\UserController;
 
Route::get('/user', [UserController::class, 'index']);
```

`routes/api.php` dosyasında tanımlanan rotalar `RouteServiceProvider` tarafından bir rota grubu içinde iç içe yerleştirilir. Bu grup içinde `/api` URI öneki otomatik olarak uygulanır, böylece dosyadaki her rotaya manuel olarak uygulamanız gerekmez. `RouteServiceProvider` sınıfınızı değiştirerek prefix ve diğer rota grubu seçeneklerini değiştirebilirsiniz.

#### `Route` Metotları
---
Yönlendirici, herhangi bir HTTP fiiline yanıt veren rotaları kaydetmenize olanak tanır:

```PHP title:'Router Methods'
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```

Bazen birden fazla HTTP fiiline yanıt veren bir rota kaydetmeniz gerekebilir. Bunu `match` metotunu kullanarak yapabilirsiniz. Ya da `any` metotunu kullanarak tüm HTTP fiillerine yanıt veren bir rota kaydedebilirsiniz:

```PHP title:'match and any Methods'
Route::match(['get', 'post'], '/', function () {
    // ...
});
 
Route::any('/', function () {
    // ...
});
```

Aynı URI'yi paylaşan birden fazla rota tanımlarken `get`, `post`, `put`, `patch`, `delete` ve `options` metotlarını kullanan rotalar `any`, `match` ve `redirect` metotlarını kullanan rotalardan önce tanımlanmalıdır. Bu, gelen isteğin doğru rota ile eşleştirilmesini sağlar.

#### Bağımlılık Enjeksiyonu
---
Rotanızın geri çağırma imzasında rotanızın gerektirdiği bağımlılıkları yazabilirsiniz. Bildirilen bağımlılıklar otomatik olarak çözümlenecek ve Laravel servis konteyneri tarafından geri aramaya enjekte edilecektir. Örneğin, mevcut HTTP isteğinin rota geri çağırmanıza otomatik olarak enjekte edilmesi için `Illuminate\Http\Request` sınıfını işaretleyebilirsiniz:

```PHP
use Illuminate\Http\Request;
 
Route::get('/users', function (Request $request) {
    // ...
});
```

#### CSRF Koruması
---
`web` rotaları dosyasında tanımlanan `POST`, `PUT`, `PATCH` veya `DELETE` rotalarına işaret eden tüm HTML formlarının bir CSRF belirteci alanı içermesi gerektiğini unutmayın. Aksi takdirde istek reddedilecektir.

```PHP
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```

#### Yönlendirme Rotaları
---
Başka bir URI'ye yönlendiren bir rota tanımlıyorsanız, `Route::redirect` metotunu kullanabilirsiniz. Bu metot, basit bir yönlendirme gerçekleştirmek için tam bir rota veya denetleyici tanımlamak zorunda kalmamanız için kullanışlı bir kısayol sağlar:

```PHP
Route::redirect('/here', '/there');
```

Varsayılan olarak, `Route::redirect` `302` durum kodu döndürür. İsteğe bağlı üçüncü parametreyi kullanarak durum kodunu özelleştirebilirsiniz:

```PHP
Route::redirect('/here', '/there', 301);
```

Ya da `301` durum kodu döndürmek için `Route::permanentRedirect` metotunu kullanabilirsiniz:

```PHP
Route::permanentRedirect('/here', '/there');
```

>Yönlendirme rotalarında rota parametrelerini kullanırken, aşağıdaki parametreler Laravel tarafından ayrılmıştır ve kullanılamaz: `destination` ve `status`.

#### View Route
---
Rotanızın yalnızca bir view döndürmesi gerekiyorsa, `Route::view` metotunu kullanabilirsiniz. Redirect yöntemi gibi, bu metotta tam bir rota veya denetleyici tanımlamak zorunda kalmamanız için basit bir kısayol sağlar. `view` metotu ilk argüman olarak bir URI ve ikinci argüman olarak bir görünüm adı kabul eder. Buna ek olarak, isteğe bağlı üçüncü bir bağımsız değişken olarak görünüme aktarılacak bir veri dizisi sağlayabilirsiniz:

```PHP
Route::view('/welcome', 'welcome');
 
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
```

>View rotalarında rota parametrelerini kullanırken, aşağıdaki parametreler Laravel tarafından ayrılmıştır ve kullanılamaz: `view`, `data`, `status`, ve `headers`.

#### `Route` Listesi
---
`route:list` Artisan komutu, uygulamanız tarafından tanımlanan tüm rotaların genel bir görünümünü kolayca sağlayabilir:

```
php artisan route:list
```

Varsayılan olarak, her rotaya atanan middleware `route:list` çıktısında görüntülenmeyecektir; ancak, komuta `-v` seçeneğini ekleyerek Laravel'e rota middleware ve middleware grup adlarını görüntülemesi talimatını verebilirsiniz:

```
php artisan route:list -v
 
# middleware gruplarını genişletin...
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

## `Route` Parametreleri
---
#### Zorunlu Parametreler
---
Bazen rotanızdaki URI segmentlerini yakalamanız gerekebilir. Örneğin, URL'den bir kullanıcının kimliğini yakalamanız gerekebilir. Bunu rota parametreleri tanımlayarak yapabilirsiniz:

```PHP
Route::get('/user/{id}', function (string $id) {
    return 'User '.$id;
});
```

Rotanızın gerektirdiği sayıda rota parametresi tanımlayabilirsiniz:

```PHP
Route::get('/posts/{post}/comments/{comment}', function (string $postId, string $commentId) {
    // ...
});
```

Route parametreleri her zaman `{}` parantezleri içinde yer alır ve alfabetik karakterlerden oluşmalıdır. Route parametre adları içinde alt çizgi (`_`) de kabul edilebilir. Rota parametreleri, sıralarına göre rota callbacks / controllers enjekte edilir - rota callback / controller argümanlarının adları önemli değildir.