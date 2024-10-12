# `#` Giriş
---
Laravel, uygulamanız için URL'ler oluşturmanıza yardımcı olmak için çeşitli helpers'lar sağlar. Bu helpers'lar öncelikle şablonlarınızda ve API yanıtlarınızda bağlantılar oluştururken veya uygulamanızın başka bir bölümüne yönlendirme yanıtları oluştururken faydalıdır.

# `#` Temeller
---

## URL Oluşturma

`url` helper'ı, uygulamanız için rastgele URL'ler oluşturmak için kullanılabilir. Oluşturulan URL, uygulama tarafından işlenen geçerli isteğin şemasını (HTTP veya HTTPS) ve ana bilgisayarını otomatik olarak kullanacaktır:

```php
$post = App\Models\Post::find(1);
 
echo url("/posts/{$post->id}");
 
// http://example.com/posts/1
```

Sorgu dizesi parametreleri içeren bir URL oluşturmak için `query` metodunu kullanabilirsiniz:

```php
echo url()->query('/posts', ['search' => 'Laravel']);
 
// https://example.com/posts?search=Laravel
 
echo url()->query('/posts?sort=latest', ['search' => 'Laravel']);
 
// http://example.com/posts?sort=latest&search=Laravel
```

Zaten var olan sorgu dizesi parametrelerinin sağlanması, mevcut değerlerinin üzerine yazacaktır:

```php
echo url()->query('/posts?sort=latest', ['sort' => 'oldest']);
 
// http://example.com/posts?sort=oldest
```

Değer dizileri de sorgu parametreleri olarak geçirilebilir. Bu değerler, oluşturulan URL'de uygun şekilde anahtarlanacak ve kodlanacaktır:

```php
echo $url = url()->query('/posts', ['columns' => ['title', 'body']]);
 
// http://example.com/posts?columns%5B0%5D=title&columns%5B1%5D=body
 
echo urldecode($url);
 
// http://example.com/posts?columns[0]=title&columns[1]=body
```

## Mevcut URL'ye Erişme

`url` helper'ına herhangi bir yol sağlanmazsa, geçerli URL hakkındaki bilgilere erişmenize olanak tanıyan bir `Illuminate\Routing\UrlGenerator` örneği döndürülür:

```php
// Get the current URL without the query string...
echo url()->current();
 
// Get the current URL including the query string...
echo url()->full();
 
// Get the full URL for the previous request...
echo url()->previous();
```

Bu metodların her birine URL facade'i aracılığıyla da erişilebilir:

```php
use Illuminate\Support\Facades\URL;
 
echo URL::current();
```

# `#` İsimlendirilmiş Rotalara URL'ler
---
`route` yardımcısı, isimlendirilmiş rotalara URL'ler oluşturmak için kullanılabilir. İsimlendirilmiş rotalar, rotada tanımlanan gerçek URL'ye bağlı olmadan URL'ler oluşturmanıza olanak tanır. Bu nedenle, rotanın URL'si değişirse, rota işlevine yaptığınız çağrılarda herhangi bir değişiklik yapmanız gerekmez. Örneğin, uygulamanızın aşağıdaki gibi tanımlanmış bir rota içerdiğini düşünün:

```php
Route::get('/post/{post}', function (Post $post) {
    // ...
})->name('post.show');
```

Bu rotaya bir URL oluşturmak için, `route` helper'ını aşağıdaki gibi kullanabilirsiniz:

```php
echo route('post.show', ['post' => 1]);
 
// http://example.com/post/1
```

Elbette, `route` yardımcısı birden fazla parametreye sahip rotalar için URL'ler oluşturmak için de kullanılabilir:

```php
Route::get('/post/{post}/comment/{comment}', function (Post $post, Comment $comment) {
    // ...
})->name('comment.show');
 
echo route('comment.show', ['post' => 1, 'comment' => 3]);
 
// http://example.com/post/1/comment/3
```

Rotanın tanım parametrelerine karşılık gelmeyen tüm ek dizi öğeleri URL'nin sorgu dizesine eklenecektir:

```php
echo route('post.show', ['post' => 1, 'search' => 'rocket']);
 
// http://example.com/post/1?search=rocket
```

## Eloquent Modeli

Genellikle Eloquent modellerinin rota anahtarını (genellikle birincil anahtar) kullanarak URL'ler oluşturacaksınız. Bu nedenle, Eloquent modellerini parametre değerleri olarak aktarabilirsiniz. Rota yardımcısı modelin rota anahtarını otomatik olarak çıkaracaktır:

```php
echo route('post.show', ['post' => $post]);
```

# `#` İmzalanmış URL'ler
---
Laravel, isimlendirilmiş rotalara kolayca "imzalı" URL'ler oluşturmanıza izin verir. Bu URL'lerin sorgu dizesine eklenen bir "imza" hash'i vardır, bu da Laravel'in URL'nin oluşturulduktan sonra değiştirilmediğini doğrulamasını sağlar. İmzalı URL'ler özellikle herkese açık olan ancak URL manipülasyonuna karşı bir koruma katmanına ihtiyaç duyan rotalar için kullanışlıdır.

Örneğin, müşterilerinize e-posta ile gönderilen genel bir "abonelikten çıkma" bağlantısı uygulamak için imzalı URL'leri kullanabilirsiniz. İsimlendirilmiş bir rotaya imzalı bir URL oluşturmak için `URL` facade'inin `signedRoute` metodunu kullanın:

```php
use Illuminate\Support\Facades\URL;
 
return URL::signedRoute('unsubscribe', ['user' => 1]);
```

`signedRoute` metoduna `absolute` değişken sağlayarak etki alanını imzalı URL karmasından hariç tutabilirsiniz:

```php
return URL::signedRoute('unsubscribe', ['user' => 1], absolute: false);
```

Eğer belirli bir süre sonra sona erecek geçici bir imzalı rota URL'si oluşturmak isterseniz, `temporarySignedRoute` metodunu kullanabilirsiniz. Laravel geçici bir imzalı rota URL'sini doğruladığında, imzalı URL'ye kodlanmış olan sona erme zaman damgasının geçmediğinden emin olacaktır:

```php
use Illuminate\Support\Facades\URL;
 
return URL::temporarySignedRoute(
    'unsubscribe', now()->addMinutes(30), ['user' => 1]
);
```

## İmzalanmış Rotayı Doğrulama

Gelen bir isteğin geçerli bir imzaya sahip olduğunu doğrulamak için, gelen `Illuminate\Http\Request` örneğinde `hasValidSignature` metodunnu çağırmalısınız:

```php
use Illuminate\Http\Request;
 
Route::get('/unsubscribe/{user}', function (Request $request) {
    if (! $request->hasValidSignature()) {
        abort(401);
    }
 
    // ...
})->name('unsubscribe');
```

Bazen, istemci tarafı sayfalandırma gerçekleştirirken olduğu gibi, uygulamanızın ön ucunun imzalı bir URL'ye veri eklemesine izin vermeniz gerekebilir. Bu nedenle, `hasValidSignatureWhileIgnoring` metodunu kullanarak imzalı bir URL doğrulanırken göz ardı edilmesi gereken istek sorgu parametrelerini belirtebilirsiniz. Parametreleri yok saymanın, herhangi birinin istek üzerinde bu parametreleri değiştirmesine izin verdiğini unutmayın:

```php
if (! $request->hasValidSignatureWhileIgnoring(['page', 'order'])) {
    abort(401);
}
```

Gelen istek örneğini kullanarak imzalı URL'leri doğrulamak yerine, imzalı (`Illuminate\Routing\Middleware\ValidateSignature`) middleware'ini rotaya atayabilirsiniz. Gelen istek geçerli bir imzaya sahip değilse, ara yazılım otomatik olarak 403 HTTP yanıtı döndürür:

```php
Route::post('/unsubscribe/{user}', function (Request $request) {
    // ...
})->name('unsubscribe')->middleware('signed');
```

İmzalı URL'leriniz URL karmasına etki alanını dahil etmiyorsa, middleware `relative` argümanı sağlamalısınız:

```php
Route::post('/unsubscribe/{user}', function (Request $request) {
    // ...
})->name('unsubscribe')->middleware('signed:relative');
```

## Geçersiz İmazalanmış Rotalar

Birisi süresi dolmuş imzalı bir URL'yi ziyaret ettiğinde, 403 HTTP durum kodu için genel bir hata sayfası alacaktır. Ancak, uygulamanızın `bootstrap/app.php` dosyasında `InvalidSignatureException` istisnası için özel bir "render" closure tanımlayarak bu davranışı özelleştirebilirsiniz:

```php
use Illuminate\Routing\Exceptions\InvalidSignatureException;
 
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->render(function (InvalidSignatureException $e) {
        return response()->view('error.link-expired', [], 403);
    });
})
```

# `#` Controller Eylemleri için URL'ler
---
`action` fonksiyonu, verilen controlleri eylemi için bir URL oluşturur:

```php
use App\Http\Controllers\HomeController;
 
$url = action([HomeController::class, 'index']);
```

Controller metodu rota parametrelerini kabul ediyorsa, fonksiyona ikinci bağımsız değişken olarak rota parametrelerinden oluşan bir ilişkisel dizi iletebilirsiniz:

```php
$url = action([UserController::class, 'profile'], ['id' => 1]);
```

# `#` Varsayılan Değerler

---
Bazı uygulamalar için, belirli URL parametreleri için istek genelinde varsayılan değerler belirtmek isteyebilirsiniz. Örneğin, rotalarınızın çoğunun bir `{locale}` parametresi tanımladığını düşünün:

```php
Route::get('/{locale}/posts', function () {
    // ...
})->name('post.index');
```

`route` helperini her çağırdığınızda `locale` paramatresini her zaman aktarmak zahmetlidir. Bu nedenle, bu parametre için geçerli istek sırasında her zaman uygulanacak varsayılan bir değer tanımlamak için `URL::defaults` metodunu kullanabilirsiniz. Geçerli isteğe erişiminiz olması için bu metodu bir rota ara katmanından çağırmak isteyebilirsiniz:

```php
<?php
 
namespace App\Http\Middleware;
 
use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\URL;
use Symfony\Component\HttpFoundation\Response;
 
class SetDefaultLocaleForUrls
{
    /**
     * Handle an incoming request.
     *
     * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
     */
    public function handle(Request $request, Closure $next): Response
    {
        URL::defaults(['locale' => $request->user()->locale]);
 
        return $next($request);
    }
}
```

`locale` parametresi için varsayılan değer ayarlandıktan sonra, rota yardımcısı aracılığıyla URL'ler oluştururken artık değerini geçmeniz gerekmez.

## URL Varsayılanları ve Middleware Önceliği

URL varsayılan değerlerini ayarlamak Laravel'in örtük model bağlarını işlemesini engelleyebilir. Bu nedenle, Laravel'in kendi `SubstituteBindings` middleware'inden önce çalıştırılmak üzere URL varsayılanlarını ayarlayan middleware'inize öncelik vermelisiniz. Bunu uygulamanızın `bootstrap/app.php` dosyasındaki `priority` middleware metodunu kullanarak gerçekleştirebilirsiniz:

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->priority([
        \Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests::class,
        \Illuminate\Cookie\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Illuminate\Foundation\Http\Middleware\ValidateCsrfToken::class,
        \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        \App\Http\Middleware\SetDefaultLocaleForUrls::class, 
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ]);
})
```


















