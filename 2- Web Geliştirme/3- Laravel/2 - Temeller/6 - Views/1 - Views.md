# `#` Giriş
---
Elbette, HTML belgelerinin tamamını doğrudan rota ve controllerlarınızdan döndürmek pratik değildir. Neyse ki görünümler, tüm HTML'lerimizi ayrı dosyalara yerleştirmek için uygun bir yol sağlar.

Görünümler controller'ınızı / uygulama mantığınızı sunum mantığınızdan ayırır ve `resources/views` dizininde saklanır. Laravel kullanırken, görünüm şablonları genellikle Blade şablonlama dili kullanılarak yazılır. Basit bir görünüm aşağıdaki gibi görünebilir:

```php
<!-- View stored in resources/views/greeting.blade.php -->
 
<html>
    <body>
        <h1>Hello, {{ $name }}</h1>
    </body>
</html>
```

Bu görünüm `resources/views/greeting.blade.php` adresinde saklandığından, global `view` yardımcısını kullanarak bu görünümü şu şekilde döndürebiliriz:

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'James']);
});
```

# `#` Görünümlerin Oluşturulması ve Render Edilmesi

Uygulamanızın `resources/views` dizinine `.blade.php` uzantılı bir dosya yerleştirerek veya `make:view` Artisan komutunu kullanarak bir görünüm oluşturabilirsiniz:

```shell
php artisan make:view greeting
```

`.blade.php` uzantısı, framework'e dosyanın bir Blade şablonu içerdiğini bildirir. Blade şablonları, HTML'nin yanı sıra değerleri kolayca yankılamanıza, "if" deyimleri oluşturmanıza, veriler üzerinde yineleme yapmanıza ve daha pek çok şeye olanak tanıyan Blade yönergeleri içerir.

Bir görünüm oluşturduktan sonra, global `view` yardımcısını kullanarak uygulamanızın rotalarından veya denetleyicilerinden birinden bu görünümü döndürebilirsiniz:

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'James']);
});
```

Görünümler `View` facade kullanılarak da döndürülebilir:

```php
use Illuminate\Support\Facades\View;
 
return View::make('greeting', ['name' => 'James']);
```

Gördüğünüz gibi, `view` yardımcısına aktarılan ilk argüman `resources/views` dizinindeki görünüm dosyasının adına karşılık gelir. İkinci argüman, görünüm için kullanılabilir hale getirilmesi gereken bir veri dizisidir. Bu durumda, Blade sözdizimi kullanılarak görünümde görüntülenen `name` değişkenini geçiyoruz.

## İç İçe Görünümler

Görünümler, `resources/views` dizininin alt dizinleri içinde de yuvalanabilir. İç içe görünümlere başvurmak için "`.`" gösterimi kullanılabilir. Örneğin, görünümünüz `resources/views/admin/profile.blade.php` dizininde saklanıyorsa, uygulamanızın rota/controller birinden bu görünümü şu şekilde döndürebilirsiniz:

```php
return view('admin.profile', $data);
```

> Görünüm dizini adları `.` karakteri içermemelidir.

## Kullanılabilir İlk Görünümü Oluşturma

`View` facade'in `first` metodunu kullanarak, belirli bir görünüm dizisinde var olan ilk görünümü oluşturabilirsiniz. Uygulamanız veya paketiniz görünümlerin özelleştirilmesine veya üzerine yazılmasına izin veriyorsa bu yararlı olabilir:

```php
use Illuminate\Support\Facades\View;
 
return View::first(['custom.admin', 'admin'], $data);
```

## View'in Varlığını Kontrol Etme

Bir görünümün var olup olmadığını belirlemeniz gerekiyorsa, `View` facade'ini kullanabilirsiniz. Görünüm mevcutsa `exists` metodu `true` değerini döndürür:

```php
use Illuminate\Support\Facades\View;
 
if (View::exists('admin.profile')) {
    // ...
}
```

# `#` Görünümlere Veri Aktarımı
---
Önceki örneklerde gördüğünüz gibi, görünümlere bir dizi veri aktararak bu verilerin görünüm tarafından kullanılabilmesini sağlayabilirsiniz:

```php
return view('greetings', ['name' => 'Victoria']);
```

Bu şekilde bilgi aktarırken, veriler anahtar / değer çiftleri içeren bir dizi olmalıdır. Bir görünüme veri sağladıktan sonra, `<?php echo $name; ?>` gibi verilerin anahtarlarını kullanarak görünümünüzdeki her bir değere erişebilirsiniz.

`view` yardımcı metoduna eksiksiz bir veri dizisi geçirmeye alternatif olarak, görünüme tek tek veri parçaları eklemek için `with` metodunu kullanabilirsiniz. `with` metodu, görünümü döndürmeden önce yöntemleri zincirlemeye devam edebilmeniz için görünüm nesnesinin bir örneğini döndürür:

```php
return view('greeting')
            ->with('name', 'Victoria')
            ->with('occupation', 'Astronaut');
```

## Veriyi Bütün Görünümlere Aktarma

Bazen, uygulamanız tarafından işlenen tüm görünümlerle veri paylaşmanız gerekebilir. Bunu `View` facade'in `share` metodunu kullanarak yapabilirsiniz. Genellikle, `share` metoduna yapılan çağrıları bir service provider'ının `boot` metoduna yerleştirmeniz gerekir. Bunları `App\Providers\AppServiceProvider` sınıfına eklemekte veya bunları barındırmak için ayrı bir service provider oluşturmakta serbestsiniz:

```php
<?php
 
namespace App\Providers;
 
use Illuminate\Support\Facades\View;
 
class AppServiceProvider extends ServiceProvider
{
    /**
     * Register any application services.
     */
    public function register(): void
    {
        // ...
    }
 
    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        View::share('key', 'value');
    }
}
```

# `#` View Composer
---
#çevrilecek 

# `#` Görünümleri Optimize Etmek
---
Varsayılan olarak, Blade şablon görünümleri talep üzerine derlenir. Bir görünümü işleyen bir istek yürütüldüğünde, Laravel görünümün derlenmiş bir sürümünün mevcut olup olmadığını belirleyecektir. Eğer dosya mevcutsa, Laravel daha sonra derlenmemiş görünümün derlenmiş görünümden daha yakın zamanda değiştirilip değiştirilmediğini belirleyecektir. Eğer derlenmiş görünüm mevcut değilse veya derlenmemiş görünüm değiştirilmişse, Laravel görünümü yeniden derleyecektir.

Görünümleri istek sırasında derlemek performans üzerinde küçük bir olumsuz etkiye sahip olabilir, bu nedenle Laravel, uygulamanız tarafından kullanılan tüm görünümleri önceden derlemek için `view:cache` Artisan komutunu sağlar. Daha yüksek performans için, bu komutu dağıtım sürecinizin bir parçası olarak çalıştırmak isteyebilirsiniz:

```shell
php artisan view:cache
```

Görünüm önbelleğini temizlemek için `view:clear` komutunu kullanabilirsiniz:

```shell
php artisan view:clear
```




