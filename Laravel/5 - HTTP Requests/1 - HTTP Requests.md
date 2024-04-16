# # Giriş
---
Laravel'ın Illuminate\Http\Request sınıfı, uygulamanız tarafından işlenen mevcut HTTP isteğiyle etkileşimde bulunmanın ve istekle birlikte gönderilen girişleri, çerezleri ve dosyaları almanın nesne tabanlı bir yolunu sağlar.

# # İstekle (Request) Etkileşime Girmek
---

## İsteğe Erişme

Laravel service container tarafından otomatik olarak enjekte edilecek gelen istek örneği için bağımlılık enjeksiyonu yoluyla mevcut HTTP isteğinin bir örneğini elde etmek için rotanızın closure'na veya controller metoduna `Illuminate\Http\Request` sınıfını tip belirtimi olarak kullanmalısınız.

```php
<?php
 
namespace App\Http\Controllers;
 
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
 
class UserController extends Controller
{
    /**
     * Store a new user.
     */
    public function store(Request $request): RedirectResponse
    {
        $name = $request->input('name');
 
        // Store the user...
 
        return redirect('/users');
    }
}
```

Bahsedildiği gibi, bir rota kapanışında `Illuminate\Http\Request` sınıfını da yazabilirsiniz. Service container, closure çalıştırıldığında gelen isteği otomatik olarak kapanışa enjekte edecektir.

```php
Route::get('/', function (Request $request) {
    // ...
});
```

## Bağımlılık Enjeksiyonu ve Rota Parametreleri

