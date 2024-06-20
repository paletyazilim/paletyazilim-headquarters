# `#` İsimlendirilmiş Rota Oluşturma
---
İsimlendirilmiş rotalar, belirli rotalar için URL'lerin veya yönlendirmelerin kolayca oluşturulmasını sağlar. name metodunu rota tanımına zincirleyerek bir rota için bir ad belirtebilirsiniz:

```php
Route::get('/dashboard/profile/settings', function() {
    return 'Kullanıcı ayar sekmesine hoşgeldiniz!';
})->name('user-settings');
```

Controller eylemleri için rota adları da belirtebilirsiniz:

```php
Route::get('/dashboard/profile/settings', [UserProfileController::class, 'show'])->name('user-settings');
```

Rota adları her zaman benzersiz olmalıdır. Farklı rotalar aynı isime sahip olamaz.

## İsimlendirilmiş Rotaya Yönlendirme İşlemi

Belirli bir rotaya bir isim atadıktan sonra, Laravel'in `route` ve `redirect` yardımcı fonksiyonları aracılığıyla URL'ler veya yönlendirmeler oluştururken rotanın adını kullanabilirsiniz:

```php
Route::get('/redirect-user', function () {

    // İsimlendirilmiş Rotanın URL'sini Alma
    $usersettings = route('user-settings');
    //https://www.example.com/dashboard/profile/settings

    // İsimlendirilmiş Rotaya Yönlendirme
    return redirect()->route('user-settings');

    // YA DA

    return to_route('user-settings');
});
```

İsimlendirilmiş rota parametrelere sahipse, parametreleri `route` metoduna ikinci bağımsız değişken olarak aktarabilirsiniz. Verilen parametreler otomatik olarak oluşturulan URL'ye doğru konumlarında eklenecektir:

```php
Route::get('/dashboard/profile/settings/{id}', function ($id) {
    return "Kullanıcı ayar sekmesine hoşgeldiniz! </br> ID: $id";
})->name('user-settings');

Route::get('/redirect-user', function () {
    return redirect()->route('user-settings', ['id' => 500]);
});
```

Diziye ek parametreler geçerseniz, bu anahtar / değer çiftleri otomatik olarak oluşturulan URL'nin sorgu dizesine eklenecektir:

```php
Route::get('/dashboard/profile/settings/{id}', function ($id) {
    return "Kullanıcı ayar sekmesine hoşgeldiniz! </br> ID: $id";
})->name('user-settings');

Route::get('/redirect-user', function () {
    return redirect()->route('user-settings', ['id' => 500,'isim' => 'berat']);
});
//https://www.example.com/dashboard/profile/settings/500?isim=berat
```

Bazen, geçerli yerel ayar gibi URL parametreleri için istek genelinde varsayılan değerler belirtmek isteyebilirsiniz. Bunu gerçekleştirmek için `URL::defaults` metodunu kullanabilirsiniz.

## Mevcut Rotanın İncelenmesi

Geçerli isteğin belirli bir adlandırılmış rotaya yönlendirilip yönlendirilmediğini belirlemek isterseniz, bir Route örneğinde `named` metodunu kullanabilirsiniz. Örneğin, bir rota middleware’in geçerli rota adını kontrol edebilirsiniz:

```php
use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;
 
/**
 * Handle an incoming request.
 *
 * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
 */
public function handle(Request $request, Closure $next): Response
{
    if ($request->route()->named('profile')) {
        // ...
    }
 
    return $next($request);
}
```
