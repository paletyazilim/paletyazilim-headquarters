# `#` Giriş
---
Blade, Laravel ile birlikte gelen basit ama güçlü bir şablonlama motorudur. Bazı PHP şablonlama motorlarının aksine, Blade şablonlarınızda düz PHP kodu kullanmanızı kısıtlamaz. Aslında, tüm Blade şablonları düz PHP koduna derlenir ve değiştirilene kadar önbelleğe alınır, yani Blade uygulamanıza esasen sıfır ek yük ekler. Blade şablon dosyaları `.blade.php` dosya uzantısını kullanır ve genellikle `resources/views` dizininde saklanır.

Blade görünümleri, global `view` yardımcısı kullanılarak rotalardan veya controllerlardan döndürülebilir. Elbette, görünümlerle ilgili dokümantasyonda belirtildiği gibi, `view` yardımcısının ikinci argümanı kullanılarak Blade görünümüne veri aktarılabilir:

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'Finn']);
});
```

## Blade Şablonlarını Livewire İle Güçlendirme

Blade şablonlarınızı bir sonraki seviyeye taşımak ve kolaylıkla dinamik arayüzler oluşturmak mı istiyorsunuz? Laravel Livewire'a göz atın. Livewire, tipik olarak yalnızca React veya Vue gibi ön uç çerçeveleri ile mümkün olabilecek dinamik işlevsellik ile güçlendirilmiş Blade bileşenleri yazmanıza olanak tanır ve birçok JavaScript çerçevesinin karmaşıklıkları, istemci tarafı oluşturma veya oluşturma adımları olmadan modern, reaktif ön uçlar oluşturmak için harika bir yaklaşım sağlar.

# `#` Verileri Görüntüleme
---
Blade görünümlerinize aktarılan verileri, veriyi süslü parantezleri içine alarak görüntüleyebilirsiniz. Örneğin, aşağıdaki rota verildiğinde:

```php
Route::get('/', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

`name` değişkeninin içeriğini aşağıdaki gibi görüntüleyebilirsiniz:

```php
<p>Merhaba {{ $name }}</p>
```

>Blade'in `{{ }}` echo deyimleri, XSS saldırılarını önlemek için otomatik olarak PHP'nin `htmlspecialchars` fonksiyonu aracılığıyla gönderilir.

Görünüme aktarılan değişkenlerin içeriğini görüntülemekle sınırlı değilsiniz. Herhangi bir PHP fonksiyonunun sonuçlarını da echolayabilirsiniz. Aslında, bir Blade `{{ }}` ifadesinin içine istediğiniz PHP kodunu koyabilirsiniz:

```php
<p>Mevcut UNIX zamanı: {{ time() }}.</p>
```

## HTML Nesnelerini Görüntüleme

Varsayılan olarak, Blade (ve Laravel'in `e` fonksiyonu) HTML varlıklarını çift kodlayacaktır. Eğer çift kodlamayı devre dışı bırakmak isterseniz, `AppServiceProvider`'ınızın `boot` metodundan `Blade::withoutDoubleEncoding` metodunu çağırın:

```php
<?php
 
namespace App\Providers;
 
use Illuminate\Support\Facades\Blade;
use Illuminate\Support\ServiceProvider;
 
class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     */
    public function boot(): void
    {
        Blade::withoutDoubleEncoding();
    }
}
```

### Biçimlendirmemiş Verileri Görüntüleme

Varsayılan olarak, Blade `{{ }}` ifadeleri XSS saldırılarını önlemek için otomatik olarak PHP'nin `htmlspecialchars` fonksiyonu aracılığıyla gönderilir. Verilerinizin kaçmasını istemiyorsanız, aşağıdaki sözdizimini kullanabilirsiniz:

```php
<p>Merhaba {!! $name !!}</p>
```

>Uygulamanızın kullanıcıları tarafından sağlanan içeriği gösterirken çok dikkatli olun. Kullanıcı tarafından sağlanan verileri görüntülerken XSS saldırılarını önlemek için genellikle escaped, double curly brace sözdizimini kullanmalısınız.
