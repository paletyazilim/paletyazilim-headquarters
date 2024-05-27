# # Yanıtların Oluşturulması
---

### Dizeler ve Diziler

Tüm rotalar ve controller'lar, kullanıcının tarayıcısına gönderilmek üzere bir `response` döndürmelidir. Laravel, response'leri döndürmek için birkaç farklı yöntem sağlar. En temel response, bir rotadan veya denetleyiciden bir dize döndürmektir. Framework, dizesini otomatik olarak tam bir HTTP response'sına dönüştürecektir.

```php
Route::get('/', function () {
    return 'Hello World';
});
```

Rotalarınızdan ve controller'larınızdan dizileri döndürebileceğiniz gibi, dizi de döndürebilirsiniz. Framework, diziyi otomatik olarak bir JSON response'sine dönüştürecektir.

```php
Route::get('/', function () {
    return [1, 2, 3];
});
```

Rotalarınızdan veya controller'larınızdan Eloquent koleksiyonlarını da otomatik olarak JSON'a dönüştürüleceklerdir.

### Yanıt Nesneleri

Genellikle, rota eylemlerinizden sadece basit dizeler veya diziler döndürmeyeceksiniz. Bunun yerine, tam `Illuminate\Http\Response` örnekleri veya view'lar döndüreceksinizdir.

Tam bir `Response` örneği döndürmek, response'nin HTTP durum kodunu ve başlıklarını özelleştirmenizi sağlar. Bir `Response` örneği, çeşitli HTTP response'leri oluşturmak için metodlar sağlayan `Symfony\Component\HttpFoundation\Response` sınıfından miras alır.

```php
Route::get('/home', function () {
    return response('Hello World', 200)
                  ->header('Content-Type', 'text/plain');
});
```

### Eloquent Model'lleri ve Kolleksiyonlar

Rotalarınız ve controller'larınızdan Eloquent ORM modellerini ve koleksiyonlarını doğrudan döndürebilirsiniz. Bunu yaptığınızda, Laravel otomatik olarak modelleri ve koleksiyonları JSON response'lerine dönüştürecektir ve modelin gizli özelliklerini koruyacaktır.

```php
use App\Models\User;
 
Route::get('/user/{user}', function (User $user) {
    return $user;
});
```

## Yanıtlara Header Ekleme

Çoğu `response` metodunun zincirlenebilir olduğunu unutmayın, bu da response örneklerinin akıcı bir şekilde oluşturulmasına olanak tanır. Örneğin, kullanıcıya geri gönderilmeden önce response'a bir dizi header eklemek için `header` metodunu kullanabilirsiniz.

```php
return response($content)
            ->header('Content-Type', $type)
            ->header('X-Header-One', 'Header Value')
            ->header('X-Header-Two', 'Header Value');
```

Veya, response'da eklenmesi için header dizisi belirtmek için `withHeaders` metodunu kullanabilirsiniz:

```php
return response($content)
            ->withHeaders([
                'Content-Type' => $type,
                'X-Header-One' => 'Header Value',
                'X-Header-Two' => 'Header Value',
            ]);
```

### Cache Control Middleware

Laravel, bir grup rotaya hızlı bir şekilde `Cache-Control` headerini ayarlamak için kullanılabilecek bir `cache.headers` middleware'ini içerir. Direktifler, ilgili `cache-control` direktifinin "snake case" eşdeğerini kullanarak ve noktalı virgülle ayrılarak sağlanmalıdır. Eğer direktifler listesinde `etag` belirtilmişse, response içeriğinin MD5 özeti otomatik olarak ETag tanımlayıcısı olarak ayarlanır.

```php
Route::middleware('cache.headers:public;max_age=2628000;etag')->group(function () {
    Route::get('/privacy', function () {
        // ...
    });
 
    Route::get('/terms', function () {
        // ...
    });
});
```

## Yanıtlara Cookie Ekleme

Bir cookie'ye, `cookie` metodunu kullanarak bir çıkış yapan `Illuminate\Http\Response` örneğine ekleyebilirsiniz. Bu metoda cookie'nin adını, değerini ve cookie'nin geçerli kabul edilmesi gereken dakika sayısını geçirmelisiniz:

```php
return response('Hello World')->cookie(
    'name', 'value', $minutes
);
```

`cookie` metodu ayrıca daha az sıklıkla kullanılan birkaç argümanı da kabul eder. Genellikle, bu argümanlar PHP'nin yerel `setcookie` metoduna verilecek argümanlarla aynı amaç ve anlama sahiptir.

```php
return response('Hello World')->cookie(
    'name', 'value', $minutes, $path, $domain, $secure, $httpOnly
);
```

Gönderilen response'la birlikte bir cookie'nin gönderilmesini sağlamak isterseniz ancak henüz bir response örneğiniz yoksa, `Cookie` facade'ını kullanarak cookie'leri response'ta "sıraya" alabilirsiniz. `queue` metodu, bir cookie örneği oluşturmak için gereken argümanları kabul eder. Bu cookieler tarayıcıya gönderilmeden önce gönderilen response eklenir.

```php
use Illuminate\Support\Facades\Cookie;
 
Cookie::queue('name', 'value', $minutes);
```

### Cookie Örnekleri Oluşturma

Daha sonra bir response örneğine eklenmek üzere `Symfony\Component\HttpFoundation\Cookie` örneği oluşturmak isterseniz, global `cookie` yardımcısını kullanabilirsiniz. Bu cookie, bir response örneğine eklenmediği sürece istemciye geri gönderilmeyecektir.

```php
$cookie = cookie('name', 'value', $minutes);
 
return response('Hello World')->cookie($cookie);
```

### Cookie'yi Erkenden Sonlandırma

Bir cookie'yi, çıkış yapan bir response'un `withoutCookie` metodu aracılığıyla sonlandırarak kaldırabilirsiniz.

```php
return response('Hello World')->withoutCookie('name');
```

Eğer henüz çıkış yapmış bir response örneğiniz yoksa, bir cookie'nin süresini sonlandırmak için `Cookie` facade'in `expire` metodunu kullanabilirsiniz.

```php
Cookie::expire('name');
```

## Cookie'ler ve Şifreleme

Varsayılan olarak, Laravel tarafından oluşturulan tüm cookie'ler `Illuminate\Cookie\Middleware\EncryptCookies` aracılığıyla şifrelenir ve imzalanır, böylece istemci tarafından değiştirilemez veya okunamazlar. Uygulamanız tarafından oluşturulan çerezlerin bir alt kümesi için şifrelemeyi devre dışı bırakmak isterseniz, uygulamanızın `bootstrap/app.php` dosyasında `encryptCookies` metodunu kullanabilirsiniz.

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->encryptCookies(except: [
        'cookie_name',
    ]);
})
```

# `#` Redirects (Yönlendirmeler)
---
Yönlendirme yanıtları `Illuminate\Http\RedirectResponse` sınıfının örnekleridir ve kullanıcıyı başka bir URL'ye yönlendirmek için gereken uygun başlıkları içerir. Bir `RedirectResponse` örneği oluşturmanın birkaç yolu vardır. En basit yöntem, global `redirect` yardımcısını kullanmaktır:

```php
Route::get('/dashboard', function () {
    return redirect('home/dashboard');
});
```

Bazen, örneğin gönderilen bir form geçersiz olduğunda, kullanıcıyı önceki konumuna yönlendirmek isteyebilirsiniz. Bunu global `back` yardımcı fonksiyonunu kullanarak yapabilirsiniz. Bu özellik `session`'u kullandığından, `back` metodunu çağıran rotanın `web` middleware grubunu kullandığından emin olun:

```php
Route::post('/user/profile', function () {
    // Validate the request...
 
    return back()->withInput();
});
```

## İsimlendirilmiş Rotalara Yönlendirme

`redirect` yardımcısını parametre olmadan çağırdığınızda, bir `Illuminate\Routing\Redirector` örneği döndürülür ve `Redirector` örneği üzerinde herhangi bir metodu çağırmanıza olanak tanır. Örneğin, adlandırılmış bir rotaya bir `RedirectResponse` oluşturmak için `route` metodunu kullanabilirsiniz:

```php
return redirect()->route('login');
```

Rotanızın parametreleri varsa, bunları `route` metoduna ikinci bağımsız değişken olarak aktarabilirsiniz:

```php
return redirect()->route('profile', ['id' => 1]);
```

### Parametreleri Eloquent Nesneleri ile Doldurma

Bir Eloquent modelinden doldurulan "ID" parametresine sahip bir rotaya yönlendirme yapıyorsanız, modelin kendisini iletebilirsiniz. Kimlik otomatik olarak çıkarılacaktır:

```php
// For a route with the following URI: /profile/{id}
 
return redirect()->route('profile', [$user]);
```

Rota parametresine yerleştirilen değeri özelleştirmek isterseniz, sütunu rota parametresi tanımında (`/profile/{id:slug}`) belirtebilir veya Eloquent modelinizdeki `getRouteKey` metodunu geçersiz kılabilirsiniz:

```php
/**
 * Get the value of the model's route key.
 */
public function getRouteKey(): mixed
{
    return $this->slug;
}
```

## Controller Eylemlerine Yönlendirme

Controller eylemlerine yönlendirmeler de oluşturabilirsiniz. Bunu yapmak için, controller'i ve eylem adını `action` metodunu geçirin:

```php
use App\Http\Controllers\UserController;
 
return redirect()->action([UserController::class, 'index']);
```

Controller rotanız parametre gerektiriyorsa, bunları `action` metoduna ikinci bağımsız değişken olarak aktarabilirsiniz:

```php
return redirect()->action(
    [UserController::class, 'profile'], ['id' => 1]
);
```

## External Domain'e Yönlendirme

Bazen uygulamanızın dışındaki bir etki alanına yeniden yönlendirme yapmanız gerekebilir. Bunu, herhangi bir ek URL kodlaması, doğrulama veya onaylama olmadan bir `RedirectResponse` oluşturan `away` metodunu çağırarak yapabilirsiniz:

```php
return redirect()->away('https://www.google.com');
```

## Flashed Session Verileriyle Yönlendirme

Yeni bir URL'ye yönlendirme ve flashed session genellikle aynı anda yapılır. Genellikle bu işlem, bir eylemi başarıyla gerçekleştirdikten sonra oturuma bir başarı mesajı gönderdiğinizde yapılır. Kolaylık sağlamak için, bir `RedirectResponse` örneği oluşturabilir ve verileri tek bir akıcı metod zincirinde oturuma gönderebilirsiniz:

```php
Route::post('/user/profile', function () {
    // ...
 
    return redirect('dashboard')->with('status', 'Profile updated!');
});
```

Kullanıcı yeniden yönlendirildikten sonra, oturumdaki flashed mesajı görüntüleyebilirsiniz. Örneğin, Blade sözdizimini kullanarak:

```php
@if (session('status'))
    <div class="alert alert-success">
        {{ session('status') }}
    </div>
@endif
```

### Girdiyle Beraber Yönlendirme

`RedirectResponse` örneği tarafından sağlanan `withInput` metodunu, kullanıcıyı yeni bir konuma yönlendirmeden önce geçerli isteğin girdi verilerini oturuma flash etmek için kullanabilirsiniz. Bu genellikle kullanıcı bir doğrulama hatasıyla karşılaştığında yapılır. Girdi oturuma aktarıldıktan sonra, formu yeniden doldurmak için bir sonraki istek sırasında kolayca geri alabilirsiniz:

```php
return back()->withInput();
```

# `#` Diğer Yanıt Türleri
---
`response` yardımcısı, diğer yanıt örneklerini oluşturmak için kullanılabilir. `response` yardımcısı bağımsız değişkenler olmadan çağrıldığında, `Illuminate\Contracts\Routing\ResponseFactory` sözleşmesinin bir uygulaması döndürülür. Bu sözleşme, yanıt oluşturmak için çeşitli yararlı metodlar sağlar.

## View Responses

Yanıtın durumu ve üstbilgileri üzerinde kontrole ihtiyacınız varsa ancak aynı zamanda yanıtın içeriği olarak bir görünüm döndürmeniz gerekiyorsa `view` metodunu kullanmalısınız:


```php
return response()
            ->view('hello', $data, 200)
            ->header('Content-Type', $type);
```

Elbette, özel bir HTTP durum kodu veya özel başlıklar geçirmeniz gerekmiyorsa, global `view` yardımcı metodunu kullanabilirsiniz.

## JSON Responses

`json` metodu `Content-Type` başlığını otomatik olarak `application/json` olarak ayarlar ve verilen diziyi `json_encode` PHP işlevini kullanarak JSON'a dönüştürür:

```php
return response()->json([
    'name' => 'Abigail',
    'state' => 'CA',
]);
```

Bir JSONP yanıtı oluşturmak isterseniz, `json` metodunu `withCallback` metoduyla birlikte kullanabilirsiniz:

```php
return response()
            ->json(['name' => 'Abigail', 'state' => 'CA'])
            ->withCallback($request->input('callback'));
```

## Dosya İndirmeleri

`download` metodu, kullanıcının tarayıcısını verilen yoldaki dosyayı indirmeye zorlayan bir yanıt oluşturmak için kullanılabilir. `download` metodu, kullanıcının dosyayı indirirken göreceği dosya adını belirleyecek olan bir dosya adını yöntemin ikinci bağımsız değişkeni olarak kabul eder. Son olarak, yönteme üçüncü bağımsız değişken olarak bir dizi HTTP başlığı iletebilirsiniz:

```php
return response()->download($pathToFile);
 
return response()->download($pathToFile, $name, $headers);
```

> Dosya indirmelerini yöneten Symfony HttpFoundation, indirilen dosyanın bir ASCII dosya adına sahip olmasını gerektirir.

### Akışlı İndirmeler

Bazen, belirli bir işlemin dize yanıtını, işlemin içeriğini diske yazmak zorunda kalmadan indirilebilir bir yanıta dönüştürmek isteyebilirsiniz. Bu senaryoda `streamDownload` metodunu kullanabilirsiniz. Bu metot, argüman olarak bir callback, dosya adı ve isteğe bağlı bir başlık dizisi kabul eder:

```php
use App\Services\GitHub;
 
return response()->streamDownload(function () {
    echo GitHub::api('repo')
                ->contents()
                ->readme('laravel', 'laravel')['contents'];
}, 'laravel-readme.md');
```

## File Responses

`file` metodu, resim veya PDF gibi bir dosyayı indirme işlemi başlatmak yerine doğrudan kullanıcının tarayıcısında görüntülemek için kullanılabilir. Bu metod, ilk bağımsız değişkeni olarak dosyanın mutlak yolunu ve ikinci bağımsız değişkeni olarak bir başlık dizisini kabul eder:

```php
return response()->file($pathToFile);
 
return response()->file($pathToFile, $headers);
```











