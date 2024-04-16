# `#` Components (Bileşenler)
---
Component'ler ve slot'lar; section, layout ve include ile benzer faydalar sağlar, ancak bazıları componentlerin ve slotların zihinsel modelini daha kolay anlayabilir. Componentlerin yazılması için iki yaklaşım vardır: sınıf tabanlı componentler ve anonim componentler.

Bir sınıf tabanlı component oluşturmak için `make:component` Artisan komutunu kullanabilirsiniz. Component'leri nasıl kullanacağımızı göstermek için basit bir `Alert` bileşeni oluşturacağız. `make:component` komutu bileşeni `app/View/Components` dizinine yerleştirecektir.

```shell
php artisan make:component Alert
```

`make:component` komutu ayrıca component için bir view şablonu oluşturacaktır. View, `resources/views/components` dizinine yerleştirilecektir. Kendi uygulamanız için componentler yazarken, componentler genellikle `app/View/Components` dizini ve `resources/views/components` dizini içinde otomatik olarak bulunur, bu nedenle genellikle başka bir component kaydı gerektirmez.

Alt dizinler içinde de component oluşturabilirsiniz.

```shell
php artisan make:component Forms/Input
```

Yukarıdaki komut, `app/View/Components/Forms` dizininde bir `Input` componenti oluşturacak ve view `resources/views/components/forms` dizinine yerleştirilecektir.

Eğer anonim bir component oluşturmak isterseniz (sadece bir Blade şablonu ve hiçbir sınıf içermeyen bir bileşen), `make:component` komutunu çağırırken `--view` özelliğini kullanabilirsiniz.

```shell
php artisan make:component forms.input --view
```

Yukarıdaki komut, `resources/views/components/forms/input.blade.php` adında bir Blade dosyası oluşturacak ve `<x-forms.input />` şeklinde bir component olarak render edilebilir.

## Paket Bileşenlerini Manuel Olarak Kaydetme

Kendi uygulamanız için componentler yazarken, componentler otomatik olarak `app/View/Components` dizini ve `resources/views/components` dizini içinde bulunur.

Ancak, Blade componentlerini kullanan bir paket oluşturuyorsanız, bileşen sınıfınızı ve HTML etiket takma adını manuel olarak kaydetmeniz gerekecektir. Genellikle componentlerinizi paketinizin service provider'ınızın `boot` metoduna kaydetmelisiniz.

```php
use Illuminate\Support\Facades\Blade;
 
/**
 * Bootstrap your package's services.
 */
public function boot(): void
{
    Blade::component('package-alert', Alert::class);
}
```

Componentiniz kaydedildikten sonra, etiket takma adı kullanılarak render edilebilir.

```php
<x-package-alert />
```

Alternatif olarak, component sınıflarını otomatik olarak yüklemek için `componentNamespace` metodunu kullanabilirsiniz. Örneğin, bir `Nightshade` paketi, `Package\Views\Components` ad alanında bulunan `Calendar` ve `ColorPicker` bileşenlerine sahip olabilir.

```php
use Illuminate\Support\Facades\Blade;
 
/**
 * Bootstrap your package's services.
 */
public function boot(): void
{
    Blade::componentNamespace('Nightshade\\Views\\Components', 'nightshade');
}
```

Bu, paket componentlerin vendor ad alanı kullanılarak `paket-adı::` sözdizimiyle kullanılmasına izin verecektir.

```php
<x-nightshade::calendar />
```

Blade, componente bağlı olan sınıfı bileşen adını Pascal case kullanarak otomatik olarak algılar. "Nokta" notasyonu kullanılarak alt dizinler de desteklenir.

## Component'leri Renderleme

Bir componenti görüntülemek için, Blade şablonlarınızdan birinde Blade component etiketi kullanabilirsiniz. Blade component etiketleri, component sınıfının kebab case adından sonra `x-` dizesiyle başlar:

```php
<x-alert />

<x-user-profile />
```

Eğer component sınıfı `app/View/Components` dizini içinde daha derin bir şekilde yer alıyorsa, dizin içindeki yerleşimi göstermek için `.` karakterini kullanabilirsiniz. Örneğin, bir component `app/View/Components/Inputs/Button.php` konumunda olduğunu varsayarsak, onu şu şekilde oluşturabiliriz:

```php
<x-inputs.button />
```

Eğer compoteniniz koşullu olarak render etmek isterseniz, component sınıfınızda `shouldRender` adında bir metod tanımlayabilirsiniz. `shouldRender` metodu `false` değerini döndürürse, component render edilmeyecektir.

```php
use Illuminate\Support\Str;
 
/**
 * Whether the component should be rendered
 */
public function shouldRender(): bool
{
    return Str::length($this->message) > 0;
}
```

## Component'lere Veri Aktarımı

Blade componentlerine veri iletebilirsiniz HTML öznitelikleri kullanarak. Sabit, ilkel değerler basit HTML öznitelik dizeleri kullanılarak bileşene iletilir. PHP ifadeleri ve değişkenler bileşene `:` karakterini önek olarak kullanan öznitelikler aracılığıyla iletilmelidir:

```php
<x-alert type="error" :message"$message" />
```

Componentin tüm veri özniteliklerini sınıfınızın kurucu metodunda tanımlamanız gerekmektedir. Componentin view'ına otomatik olarak erişilebilir hale getirilen tüm genel özellikler bileşenin render metodundan veriyi view'a iletmek gerekli değildir.

```php
<?php
 
namespace App\View\Components;
 
use Illuminate\View\Component;
use Illuminate\View\View;
 
class Alert extends Component
{
    /**
     * Create the component instance.
     */
    public function __construct(
        public string $type,
        public string $message,
    ) {}
 
    /**
     * Get the view / contents that represent the component.
     */
    public function render(): View
    {
        return view('components.alert');
    }
}
```

Componentiniz render edildiğinde, componentin genel değişkenlerinin içeriğini isimleriyle ekrana yazdırarak gösterebilirsiniz:

```php
<div class="alert alert-{{ $type }}">
    {{ $message }}
</div>
```

### Casing

Componentinizin construct metodunun parametreleri `camelCase` kullanılarak belirtilmelidir, HTML özniteliklerinde ise parametre isimlerini `kebab-case` kullanmalısınız. Örneğin, aşağıdaki component yapıcısı verildiğinde:

```php
/**
 * Create the component instance.
 */
public function __construct(
    public string $alertType,
) {}
```

`$alertType` parametresine componentte şu şekilde sağlanabilir:

```php
<x-alert alert-type="danger" />
```

