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

### Kısa Nitelik Sözdizimi

Componentlere nitelikler geçirirken, "kısa nitelik" sözdizimini de kullanabilirsiniz. Bu genellikle uygun bir seçenektir çünkü nitelik isimleri genellikle karşılık geldikleri değişken isimleriyle eşleşir.

```php
//{{-- Short attribute syntax... --}}
<x-profile :$userId :$name />
 
//{{-- Is equivalent to... --}}
<x-profile :user-id="$userId" :name="$name" />
```

### Components Methods

Component şablonuna ek olarak, component'te bulunan herhangi bir genel değişken component şablonunda kullanılabilir. Örneğin, `isSelected` adında bir metoda sahip bir component düşünün:

```php
/**
 * Determine if the given option is the currently selected option.
 */
public function isSelected(string $option): bool
{
    return $option === $this->selected;
```

Bu metodu component şablonunuzdan, metodun adıyla eşleşen değişkeni çağırarak çalıştırabilirsiniz.

```php
<option {{ $isSelected($value) ? 'selected' : '' }} value="{{ $value }}">
    {{ $label }}
</option>
```

### Component Sınıfları İçinde Özelliklere ve Slot'lara Erişim

Blade componentleri ayrıca component adına, özelliklere ve slot'a componentin `render` metodu içinden erişmenizi sağlar. Ancak, bu verilere erişmek için componentinizin `render` metodundan bir closure döndürmeniz gerekmektedir. Closure, tek bir `$data` dizisi parametre olarak alacaktır. Bu dizi, component hakkında bilgi sağlayan birkaç öğe içerecektir:

```php
use Closure;
 
/**
 * Get the view / contents that represent the component.
 */
public function render(): Closure
{
    return function (array $data) {
        // $data['componentName'];
        // $data['attributes'];
        // $data['slot'];
 
        return '<div>Components content</div>';
    };
}
```

`componentName`, `x-` öneki sonrasında HTML etiketinde kullanılan isme eşittir. Dolayısıyla `<x-alert />`'ın `componentName`'i `alert` olacaktır. `attributes` öğesi, HTML etiketinde bulunan tüm öznitelikleri içerecektir. `slot` öğesi, bileşenin slot içeriğiyle birlikte `Illuminate\Support\HtmlString` örneğidir.

Closure bir dize döndürmelidir. Döndürülen dize mevcut bir view'a karşılık geliyorsa, o view oluşturulur; aksi takdirde, döndürülen dize iç içe Blade görünümü olarak değerlendirilir.

### Ek Bağımlılıklar

Eğer component'iniz Laravel'in service container'inden bağımlılıkları gerekiyorsa, bunları componentin veri özelliklerinden önce listeleyebilirsiniz ve container tarafından otomatik olarak enjekte edilecektir:

```php
use App\Services\AlertCreator;
 
/**
 * Create the component instance.
 */
public function __construct(
    public AlertCreator $creator,
    public string $type,
    public string $message,
) {}
```

### Nitelikleri / Methodları Gizleme

Eğer component şablonuna bazı global metodları veya özellikleri değişken olarak aktarmaktan kaçınmak isterseniz, bunları componentinizdeki bir `$except` dizisi özelliğine ekleyebilirsiniz.

```php
<?php
 
namespace App\View\Components;
 
use Illuminate\View\Component;
 
class Alert extends Component
{
    /**
     * The properties / methods that should not be exposed to the component template.
     *
     * @var array
     */
    protected $except = ['type'];
 
    /**
     * Create the component instance.
     */
    public function __construct(
        public string $type,
    ) {}
}
```

## Component Nitelikleri

Veri niteliklerini bir componente nasıl ileteceğimizi zaten inceledik; ancak bazen componentin işlevi için gerekli olmayan sınıf gibi ek HTML özniteliklerini belirtmeniz gerekebilir. Genellikle, bu ek öznitelikleri component şablonunun kök öğesine iletmek istersiniz. Örneğin, bir uyarı bileşenini şu şekilde oluşturmak istediğimizi düşünelim:

```php
<x-alert type="error" :message="$message" class="mt-4"/>
```

Componentin constructor metodunun bir parçası olmayan tüm nitelikler otomatik olarak componentin "nitelik çantasına" eklenir. Bu nitelik çantası, bileşene `$attributes` değişkeni aracılığıyla otomatik olarak erişilebilir hale getirilir. Tüm nitelikler, bu değişkeni ekrana yazdırarak bileşen içinde görüntülenebilir:

```php
<div {{ $attributes }}>
    <!-- Component content -->
</div>
```

### Birleştirilmiş Nitelikler

Bazen nitelikler için varsayılan değerler belirtmeniz veya bazı component nitelikleriyle ek değerler birleştirmeniz gerekebilir. Bunun için nitelik çantasının `merge` metodunu kullanabilirsiniz. Bu metod, her zaman bir componente uygulanması gereken bir dizi varsayılan CSS sınıfını tanımlamak için özellikle kullanışlıdır.

```php
<div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
    {{ $message }}
</div>
```

Bu componentin şu şekilde kullanıldığını varsayarsak:

```php
<x-alert type="error" :message="$message" class="mb-4"/>
```

Componentin son, oluşturulmuş HTML'i aşağıdaki gibi görünecektir:

```php
<div class="alert alert-error mb-4">
    <!-- Component İçeriği -->
</div>
```

#### Sınıfları Koşullu Olarak Birleştirme

Bazen belirli bir koşul `true` ise sınıfları birleştirmek isteyebilirsiniz. Bu, `class` metodu aracılığıyla gerçekleştirilebilir. Bu metod, sınıf veya sınıfları eklemek istediğiniz bir sınıf dizisi kabul ederken, değer ise bir `boolean` ifadedir. Eğer dizi elemanının sayısal bir anahtarı varsa, her zaman oluşturulan sınıf listesine dahil edilecektir.

```php
<div {{ $attributes->class(['p-4', 'bg-red' => $hasError]) }}>
    {{ $message }}
</div>
```

Eğer componentiniz de diğer özellikleri birleştirmeniz gerekiyorsa, `merge` metodunu `class` metoduna zincirleme şekilde ekleyebilirsiniz.

```php
<button {{ $attributes->class(['p-4'])->merge(['type' => 'button']) }}>
    {{ $slot }}
</button>
```

Eğer birleştirilmiş nitelik almayan diğer HTML öğelerine bağlı olarak sınıfları koşullu derlemek isterseniz, `@class` yönergesini kullanabilirsiniz.

### Sınıf Dışı Nitelik Birleştirme

Sınıf nitelikleri olmayan nitelikleri birleştirirken, `merge` metoduna sağlanan değerler niteliğin "varsayılan" değerleri olarak kabul edilecektir. Ancak, sınıf niteliği gibi, bu nitelikler enjekte edilen nitelik değerleriyle birleştirilmeyecektir. Bunun yerine üzerine yazılacaktır. Örneğin, bir `button` componentinin uygulaması aşağıdaki gibi olabilir:

```php
<button {{ $attributes->merge(['type' => 'button']) }}>
    {{ $slot }}
</button>
```

Özel bir `type` değeri ile düğme componentini oluşturmak için, component tanımlanırken belirtilebilir. Eğer bir `type` belirtilmezse, type `button` olarak değerlendirilecektir:

```php
<x-button type="submit">
    Submit
</x-button>
```

Bu örnekteki `button` componentinin oluşturulan HTML'i şu şekilde olurdu:

```php
<button type="submit">
    Submit
</button>
```

Eğer sınıf dışında bir niteliğin varsayılan değeri ile enjekte edilen değerlerin birleştirilmesini isterseniz, `prepends` metodunu kullanabilirsiniz. Bu örnekte, `data-controller` özelliği her zaman `profile-controller` ile başlayacak ve ek enjekte edilen `data-controller` değerleri bu varsayılan değerin ardından yerleştirilecektir:

```php
<div {{ $attributes->merge(['data-controller' => $attributes->prepends('profile-controller')]) }}>
    {{ $slot }}
</div>
```

### Nitelikleri Alma ve Filitreleme

#çevrilecek 

### Rezerve Edilmiş Anahtar Kelimeler

Varsayılan olarak, bazı anahtar kelimeler Blade'in componentleri render etmek için dahili olarak kullanılır. Aşağıdaki anahtar kelimeler, componentlerinizde genel özellikler veya method adları olarak tanımlanamaz:

- `data`
- `render`
- `resolveView`
- `shouldRender`
- `view`
- `withAttributes`
- `withName`

## Slots

Componentinize ek içerik geçirmek için genellikle "slotlar" kullanmanız gerekecektir. Component slotları, `$slot` değişkenini yakalayarak render edilir. Bu kavramı keşfetmek için, bir `alert` componentinin aşağıdaki işaretlemeye sahip olduğunu hayal edelim:

```php
<div class="alert alert-danger">
    {{ $slot }}
</div>
```

İçeriğimizi componente enjekte ederek içeriği slot'a iletebiliriz.

```php
<x-alert>
    <strong>Upss!</strong> bir şeyler yanlış gitti!
</x-alert>
```

Bazen bir component, componentin farklı konumlarında farklı slotları render etmesi gerekebilir. Alert componentimizin "title" slotunun enjekte edilmesine izin vermek için değiştirelim:

```php
<span class="alert-title">{{ $title }}</span>
 
<div class="alert alert-danger">
    {{ $slot }}
</div>
```

İsimlendirilmiş slot içeriğini `x-slot` etiketi kullanarak tanımlayabilirsiniz. Bir `x-slot` etiketi içinde olmayan içerik, bileşene `$slot` değişkeniyle iletilir.

```php
<x-alert>
    <x-slot:title>
        Server Error
    </x-slot>
 
    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

Bir slot'un içeriği var mı yok mu belirlemek için bir slot'ta `isEmpty` metodunu çağırabilirsiniz:

```php
<span class="alert-title">{{ $title }}</span>
 
<div class="alert alert-danger">
    @if ($slot->isEmpty())
        This is default content if the slot is empty.
    @else
        {{ $slot }}
    @endif
</div>
```

Ayrıca, `hasActualContent` metodu, slot içerisinde HTML yorumu olmayan herhangi bir "gerçek" içeriğin olup olmadığını belirlemek için kullanılabilir:

```php
@if ($slot->hasActualContent())
    The scope has non-comment content.
@endif
```

### Slot Nitelikleri

Blade componentleri gibi, slotlara ek CSS sınıf adları gibi ek nitelikler atayabilirsiniz.

```php
<x-card class="shadow-sm">
    <x-slot:heading class="font-bold">
        Heading
    </x-slot>
 
    Content
 
    <x-slot:footer class="text-sm">
        Footer
    </x-slot>
</x-card>
```

#çevrilecek 