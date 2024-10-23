
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

# `#` Blade Yönergeleri
---
Şablon kalıtımına ve veri görüntülemeye ek olarak Blade, koşullu ifadeler ve döngüler gibi yaygın PHP kontrol yapıları için kullanışlı kısayollar da sağlar. Bu kısayollar, PHP kontrol yapıları ile çalışmak için çok temiz ve kısa bir yol sağlarken, aynı zamanda PHP benzerlerine aşina kalırlar.

## İf-Else İfadeleri
---
`if-else` ifadelerini `@if`, `@elseif`, `@else` ve `@endif` yönergelerini kullanarak oluşturabilirsiniz. Bu yönergeler PHP'deki karşılıklarıyla aynı işlevi görür:

```php
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I dont have any records!
@endif
```

Kolaylık sağlamak için Blade ayrıca bir `@unless` yönergesi de sağlar:

```php
@unless (Auth::check())
    You are not signed in.
@endunless
```

Daha önce yorumlanan koşullu yönergelere ek olarak, `@isset` ve `@empty` yönergeleri ilgili PHP işlevleri için kullanışlı kısayollar olarak kullanılabilir:

```php
@isset($records)
    // $records is defined and is not null...
@endisset
 
@empty($records)
    // $records is "empty"...
@endempty
```

### Oturum (Authentication) Yönergeleri

`@auth` ve `@guest` yönergeleri mevcut kullanıcının kimliğinin doğrulanıp doğrulanmadığını veya misafir olup olmadığını hızlıca belirlemek için kullanılabilir:

```php
@auth
    // The user is authenticated...
@endauth
 
@guest
    // The user is not authenticated...
@endguest
```

Gerekirse, `@auth` ve `@guest` yönergelerini kullanırken denetlenmesi gereken kimlik doğrulama korumasını belirtebilirsiniz:

```php
@auth('admin')
    // The user is authenticated...
@endauth
 
@guest('admin')
    // The user is not authenticated...
@endguest
```

### Çevre (Env) Yönergeleri

Uygulamanın üretim ortamında çalışıp çalışmadığını `@production` yönergesini kullanarak kontrol edebilirsiniz:

```php
@production
    // Production specific content...
@endproduction
```

Ya da `@env` yönergesini kullanarak uygulamanın belirli bir ortamda çalışıp çalışmadığını belirleyebilirsiniz:

```php
@env('staging')
    // The application is running in "staging"...
@endenv
 
@env(['staging', 'production'])
    // The application is running in "staging" or "production"...
@endenv
```

### Section (Alan/Bölüm) Yönergeleri

Bir şablon kalıtım bölümünün içeriğe sahip olup olmadığını `@hasSection` yönergesini kullanarak belirleyebilirsiniz:

```php
@hasSection('navigation')
    <div class="pull-right">
        @yield('navigation')
    </div>
 
    <div class="clearfix"></div>
@endif
```

Bir bölümün içeriğinin olup olmadığını belirlemek için `@sectionMissing` yönergesini kullanabilirsiniz:

```php
@sectionMissing('navigation')
    <div class="pull-right">
        @include('default-navigation')
    </div>
@endif
```

### Session (Oturum) Yönergeleri

Bir oturum değerinin var olup olmadığını belirlemek için `@session` yönergesi kullanılabilir. Eğer oturum değeri mevcutsa, `@session` ve `@endsession` yönergeleri içindeki şablon içerikleri değerlendirilecektir. Oturum değerini görüntülemek için `@session` yönergesinin içeriği içinde `$value` değişkenini yazdırabilirsiniz:

```php
@session('status')
    <div class="p-4 bg-green-100">
        {{ $value }}
    </div>
@endsession
```

## Switch İfadeleri

Switch ifadeleri `@switch`, `@case`, `@break`, `@default` ve `@endswitch` yönergeleri kullanılarak oluşturulabilir:

```php
@switch($i)
    @case(1)
        First case...
        @break
 
    @case(2)
        Second case...
        @break
 
    @default
        Default case...
@endswitch
```

## Döngüler 

Koşullu ifadelere ek olarak Blade, PHP'nin döngü yapılarıyla çalışmak için basit yönergeler sağlar. Yine, bu yönergelerin her biri PHP'deki karşılıklarıyla aynı şekilde çalışır:

```php
@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor
 
@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach
 
@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse
 
@while (true)
    <p>Im looping forever.</p>
@endwhile
```

>Bir `foreach` döngüsünde yineleme yaparken, `$loop değişkenini (loop variable)` döngü hakkında değerli bilgiler elde etmek için kullanabilirsiniz (örneğin, döngüde ilk veya son yinelemede olup olmadığınız gibi).

Döngüleri kullanırken, `@continue` ve `@break` yönergelerini kullanarak mevcut yinelemeyi atlayabilir veya döngüyü sonlandırabilirsiniz:

```php
@foreach ($users as $user)
    @if ($user->type == 1)
        @continue
    @endif
 
    <li>{{ $user->name }}</li>
 
    @if ($user->number == 5)
        @break
    @endif
@endforeach
```

`continue` veya `break` koşulunu yönerge bildirimine de dahil edebilirsiniz:

```php
@foreach ($users as $user)
    @continue($user->type == 1)
 
    <li>{{ $user->name }}</li>
 
    @break($user->number == 5)
@endforeach
```

## Döngü Değişkeni

Bir `foreach` döngüsü içinde yineleme yaparken, döngünüzün içinde bir `$loop` değişkeni bulunacaktır. Bu değişken, geçerli döngü indeksi ve bunun döngüdeki ilk veya son yineleme olup olmadığı gibi bazı yararlı bilgilere erişim sağlar:

```php
@foreach ($users as $user)
    @if ($loop->first)
        This is the first iteration.
    @endif
 
    @if ($loop->last)
        This is the last iteration.
    @endif
 
    <p>This is user {{ $user->id }}</p>
@endforeach
```

İç içe geçmiş bir döngü içindeyseniz, `parent` özelliği aracılığıyla üst döngünün `$loop` değişkenine erişebilirsiniz:

```php
@foreach ($users as $user)
    @foreach ($user->posts as $post)
        @if ($loop->parent->first)
            This is the first iteration of the parent loop.
        @endif
    @endforeach
@endforeach
```

`$loop` değişkeni ayrıca çeşitli başka yararlı özellikler de içerir:

| Özellik            | Açıklama                                                  |
| ------------------ | --------------------------------------------------------- |
| `$loop->index`     | Geçerli döngü yinelemesinin indeksi (0'dan başlar).       |
| `$loop->iteration` | Geçerli döngü yinelemesi (1'den başlar).                  |
| `$loop->remaining` | Döngüde kalan yinelemeler.                                |
| `$loop->count`     | Yinelenen dizideki toplam öğe sayısı.                     |
| `$loop->first`     | Bunun döngü boyunca ilk yineleme olup olmadığı.           |
| `$loop->last`      | Bunun döngü boyunca son yineleme olup olmadığı.           |
| `$loop->even`      | Bunun döngü boyunca çift bir yineleme olup olmadığı.      |
| `$loop->odd`       | Bunun döngü boyunca tek bir yineleme olup olmadığı.       |
| `$loop->depth`     | Geçerli döngünün seviyesi.                                |
| `$loop->parent`    | İç içe geçmiş bir döngüdeyken, ebeveynin döngü değişkeni. |

## Koşullu Sınıflar ve Stiller

`@class` yönergesi bir CSS sınıf dizgesini koşullu olarak derler. Yönerge, dizi anahtarının eklemek istediğiniz sınıf veya sınıfları içerdiği, değerin ise `boolean` bir ifade olduğu bir dizi sınıf kabul eder. Dizi elemanı sayısal bir anahtara sahipse, her zaman işlenen sınıf listesine dahil edilecektir:

```php
@php
    $isActive = false;
    $hasError = true;
@endphp
 
<span @class([
    'p-4',
    'font-bold' => $isActive,
    'text-gray-500' => ! $isActive,
    'bg-red' => $hasError,
])></span>

// Output
<span class="p-4 text-gray-500 bg-red"></span>
```

Benzer şekilde, `@style` yönergesi bir HTML öğesine koşullu olarak satır içi CSS stilleri eklemek için kullanılabilir:

```php
@php
    $isActive = true;
@endphp
 
<span @style([
    'background-color: red',
    'font-weight: bold' => $isActive,
])></span>

//Output
<span style="background-color: red; font-weight: bold;"></span>
```

## Ek Özellikler

Kolaylık sağlamak amacıyla, belirli bir HTML onay kutusu girdisinin "checked" olup olmadığını kolayca belirtmek için `@checked` yönergesini kullanabilirsiniz. Bu yönerge, sağlanan koşul `true` olarak değerlendirilirse işaretli olarak gösterilecektir:

```php
<input type="checkbox"
        name="active"
        value="active"
        @checked(old('active', $user->active)) />
```

Benzer şekilde, `@selected` yönergesi, belirli bir seçme seçeneğinin "selected" olması gerekip gerekmediğini belirtmek için kullanılabilir:

```php
<select name="version">
    @foreach ($product->versions as $version)
        <option value="{{ $version }}" @selected(old('version') == $version)>
            {{ $version }}
        </option>
    @endforeach
</select>
```

Ek olarak, `@disabled` yönergesi belirli bir öğenin "disabled" bırakılıp bırakılmayacağını belirtmek için kullanılabilir:

```php
<button type="submit" @disabled($errors->isNotEmpty())>Submit</button>
```

Ayrıca, `@readonly` yönergesi, belirli bir öğenin "readonly" olması gerekip gerekmediğini belirtmek için kullanılabilir:

```php
<input type="email"
        name="email"
        value="email@laravel.com"
        @readonly($user->isNotAdmin()) />
```

Buna ek olarak, `@required` yönergesi belirli bir öğenin "required" olup olmadığını belirtmek için kullanılabilir:

```php
<input type="text"
        name="title"
        value="title"
        @required($user->isAdmin()) />
```

## Görünüm Dahil Etme

>Her ne kadar `@include` yönergesini kullanmakta özgür olsanız da Blade componentleri benzer işlevsellik sağlar ve `@include` yönergesine göre veri ve nitelik bağlama gibi çeşitli avantajlar sunar.

Blade'in `@include` yönergesi, bir Blade görünümünü başka bir görünümün içine dahil etmenizi sağlar. Ana görünüm için kullanılabilir olan tüm değişkenler, dahil edilen görünüm için de kullanılabilir hale getirilecektir:

```php
<div>
    @include('shared.errors')
 
    <form>
        <!-- Form Contents -->
    </form>
</div>
```

Dahil edilen görünüm üst görünümde bulunan tüm verileri devralacak olsa da, dahil edilen görünümün kullanımına sunulması gereken bir dizi ek veri de iletebilirsiniz:

```php
@include('view.name', ['status' => 'complete'])
```

Eğer var olmayan bir görünümü `@include` etmeye çalışırsanız, Laravel bir hata verecektir. Eğer mevcut olan veya olmayan bir görünümü dahil etmek istiyorsanız, `@includeIf` yönergesini kullanmalısınız:

```php
@includeIf('view.name', ['status' => 'complete'])
```

Belirli bir boolean ifadenin `true` veya `false` olarak değerlendirilmesi durumunda bir görünümü `@include` etmek isterseniz, `@includeWhen` ve `@includeUnless` yönergelerini kullanabilirsiniz:

```php
@includeWhen($boolean, 'view.name', ['status' => 'complete'])
 
@includeUnless($boolean, 'view.name', ['status' => 'complete'])
```

Belirli bir görünüm dizisinden var olan ilk görünümü dahil etmek için `includeFirst` yönergesini kullanabilirsiniz:

```php
@includeFirst(['custom.admin', 'admin'], ['status' => 'complete'])
```

>Blade görünümlerinizde `__DIR__` ve `__FILE__` sabitlerini kullanmaktan kaçınmalısınız, çünkü bunlar önbelleğe alınmış, derlenmiş görünümün konumuna atıfta bulunacaktır.

## Koleksiyonları Render Etmek

Blade'in `@each` yönergesi ile döngüleri ve include'leri tek bir satırda birleştirebilirsiniz:

```php
@each('view.name', $jobs, 'job')
```

`@each` yönergesinin ilk argümanı dizi veya koleksiyondaki her eleman için oluşturulacak görünümdür. İkinci bağımsız değişken, üzerinde yineleme yapmak istediğiniz dizi ya da koleksiyondur; üçüncü bağımsız değişken ise görünüm içinde geçerli yinelemeye atanacak değişken adıdır. Örneğin, bir `jobs` dizisi üzerinde yineleme yapıyorsanız, genellikle her işe görünüm içinde bir `job` değişkeni olarak erişmek istersiniz. Geçerli yineleme için dizi anahtarı, görünüm içinde anahtar değişkeni olarak kullanılabilir olacaktır.

Ayrıca `@each` yönergesine dördüncü bir argüman da aktarabilirsiniz. Bu argüman, verilen dizinin boş olması durumunda oluşturulacak görünümü belirler.

```php
@each('view.name', $jobs, 'job', 'view.empty')
```

>`@each` aracılığıyla renderlenen görünümler üst görünümden değişkenleri miras almaz. Eğer alt görünüm bu değişkenlere ihtiyaç duyuyorsa, bunun yerine `@foreach` ve `@include` yönergelerini kullanmalısınız.

## `@once` Yönergisi

`@once` yönergesi, şablonun her işleme döngüsünde yalnızca bir kez değerlendirilecek bir bölümünü tanımlamanıza olanak tanır. Bu, belirli bir JavaScript parçasını yığınları kullanarak sayfanın başlığına itmek için yararlı olabilir. Örneğin, belirli bir bileşeni bir döngü içinde oluşturuyorsanız, JavaScript'i yalnızca bileşen ilk kez oluşturulduğunda başlığa itmek isteyebilirsiniz:

```php
@once
    @push('scripts')
        <script>
            // Your custom JavaScript...
        </script>
    @endpush
@endonce
```

`@once` yönergesi genellikle `@push` veya `@prepend` yönergeleri ile birlikte kullanıldığından, `@pushOnce` ve `@prependOnce` yönergeleri size kolaylık sağlamak için mevcuttur:

```php
@pushOnce('scripts')
    <script>
        // Your custom JavaScript...
    </script>
@endPushOnce
```

## Ham PHP

Bazı durumlarda, PHP kodunu görünümlerinize gömmek yararlıdır. Şablonunuz içinde düz PHP bloğu çalıştırmak için Blade `@php` yönergesini kullanabilirsiniz:

```php
@php
    $counter = 1;
@endphp
```

Ya da, PHP'yi sadece bir sınıfı içe aktarmak için kullanmanız gerekiyorsa, `@use` yönergesini kullanabilirsiniz:

```php
@use('App\Models\Flight')
```

İçe aktarılan sınıfa takma ad vermek için `@use` yönergesine ikinci bir argüman verilebilir:

```php
@use('App\Models\Flight', 'FlightModel')
```

## Yorumlar

Blade, görünümlerinizde yorum tanımlamanıza da izin verir. Ancak HTML yorumlarından farklı olarak Blade yorumları, uygulamanız tarafından döndürülen HTML'ye dahil edilmez:

```php
{{-- This comment will not be present in the rendered HTML --}}
```

# `#` Components (Bileşenler)
--- 
Bileşenler (components) ve yuvalar (slots); bölümler (sections), şablonlar (layouts) ve içerikler (contents) ile benzer faydalar sağlar; ancak bazıları bileşenlerin ve yuvaların zihinsel modelini daha kolay anlaşılır bulabilir. Bileşen yazmak için iki yaklaşım vardır: sınıf tabanlı bileşenler ve anonim bileşenler.

Sınıf tabanlı bir bileşen oluşturmak için `make:component` Artisan komutunu kullanabilirsiniz. Bileşenlerin nasıl kullanılacağını göstermek için basit bir `Alert` bileşeni oluşturacağız. `make:component` komutu bileşeni `app/View/Components` dizinine yerleştirecektir:

```shell
php artisan make:component Alert
```

`make:component` komutu ayrıca bileşen için bir görünüm şablonu oluşturacaktır. Görünüm `resources/views/components` dizinine yerleştirilecektir. Kendi uygulamanız için bileşen yazarken, bileşenler otomatik olarak `app/View/Components` dizini ve `resources/views/components` dizini içine oluşturulur, bu nedenle genellikle başka bir bileşen kaydı gerekmez.

Alt dizinler içinde de bileşenler oluşturabilirsiniz:

```shell
php artisan make:component Forms/Input
```

Yukarıdaki komut `app/View/Components/Forms` dizininde bir `Input` bileşeni oluşturacak ve görünüm `resources/views/components/forms` dizinine yerleştirilecektir.

Anonim bir bileşen (sadece Blade şablonu olan ve sınıfı olmayan bir bileşen) oluşturmak istiyorsanız, `make:component` komutunu çağırırken `--view` özelliğini kullanabilirsiniz:

```shell
php artisan make:component forms.input --view
```

Yukarıdaki komut `resources/views/components/forms/input.blade.php` adresinde bir Blade dosyası oluşturacak ve bu dosya `<x-forms.input />` aracılığıyla bir bileşen olarak işlenebilecektir.

## Paket Bileşenlerinin Manuel Olarak Kaydedilmesi

Kendi uygulamanız için bileşenler yazarken, bileşenler otomatik olarak `app/View/Components` dizini ve `resources/views/components` dizini içinde oluşturulur.

Ancak, Blade bileşenlerini kullanan bir paket oluşturuyorsanız, bileşen sınıfınızı ve HTML etiketi takma adını manuel olarak kaydetmeniz gerekir. Bileşenlerinizi genellikle paketinizin hizmet sağlayıcısının `boot` metoduna kaydetmelisiniz:

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

Bileşeniniz kaydedildikten sonra, etiket takma adı kullanılarak oluşturulabilir:

```php
<x-package-alert/>
```

Alternatif olarak, bileşen sınıflarını kurallara göre otomatik yüklemek için `componentNamespace` metodunu kullanabilirsiniz. Örneğin, bir `Nightshade` paketi `Package\Views\Components` ad alanında bulunan `Calendar` ve `ColorPicker` bileşenlerine sahip olabilir:

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

Bu, paket bileşenlerinin `paket-adı::` sözdizimi kullanılarak kullanılmasına izin verecektir:

```php
<x-nightshade::calendar />
<x-nightshade::color-picker />
```

Blade, bileşen adını pascal-casing yaparak bu bileşene bağlı olan sınıfı otomatik olarak algılar. Alt dizinler de "nokta" gösterimi kullanılarak desteklenir.

## Bileşenlerin Render Edilmesi

Bir bileşeni görüntülemek için, Blade şablonlarınızdan birinde bir Blade bileşen etiketi kullanabilirsiniz. Blade bileşen etiketleri `x-` dizesi ile başlar ve ardından bileşen sınıfının kebab case adı gelir:

```php
<x-alert/>
 
<x-user-profile/>
```

Bileşen sınıfı `app/View/Components` dizini içinde daha derinde yuvalanmışsa, dizin yuvalamasını belirtmek için `.` karakterini kullanabilirsiniz. Örneğin, bir bileşenin `app/View/Components/Inputs/Button.php` adresinde bulunduğunu varsayarsak, bileşeni şu şekilde oluşturabiliriz:

```php
<x-inputs.button/>
```

Bileşeninizi koşullu olarak render etmek isterseniz, bileşen sınıfınızda bir `shouldRender` metodu tanımlayabilirsiniz. Eğer `shouldRender` metodu `false` değerini döndürürse bileşen render edilmeyecektir:

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

## Bileşenlere Veri Aktarma

HTML niteliklerini kullanarak Blade bileşenlerine veri aktarabilirsiniz. Sabit kodlanmış, ilkel değerler, basit HTML öznitelik dizeleri kullanılarak bileşene aktarılabilir. PHP ifadeleri ve değişkenleri, önek olarak `:` karakterini kullanan öznitelikler aracılığıyla bileşene aktarılmalıdır:

```php
<x-alert type="error" :message="$message"/>
```

Bileşenin tüm veri niteliklerini sınıf constructor'unda tanımlamalısınız. Bir bileşen üzerindeki tüm genel özellikler otomatik olarak bileşenin görünümü tarafından kullanılabilir hale getirilecektir. Verilerin bileşenin render metodundan görünüme aktarılması gerekli değildir:

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

Bileşeniniz işlendiğinde, değişkenleri isimlerine göre echo'layarak bileşeninizin genel değişkenlerinin içeriğini görüntüleyebilirsiniz:

```php
<div class="alert alert-{{ $type }}">
    {{ $message }}
</div>
```

### Casing

Bileşen constructor argümanları `camelCase` kullanılarak belirtilmeli, HTML niteliklerinizde argüman adlarına atıfta bulunurken kebab-case kullanılmalıdır. Örneğin, aşağıdaki bileşen constructor'ını ele alalım:

```php
/**
 * Create the component instance.
 */
public function __construct(
    public string $alertType,
) {}
```

Bileşene `$alertType` bağımsız değişkeni şu şekilde sağlanabilir:

```php
<x-alert alert-type="danger" />
```

### Kısa Nitelik Sözdizimi

Öznitelikleri bileşenlere aktarırken "kısa öznitelik" sözdizimini de kullanabilirsiniz. Öznitelik adları sıklıkla karşılık geldikleri değişken adlarıyla eşleştiğinden bu genellikle kullanışlıdır:

```php
{{-- Short attribute syntax... --}}
<x-profile :$userId :$name />
 
{{-- Is equivalent to... --}}
<x-profile :user-id="$userId" :name="$name" />
```

### Nitelik Oluşturmadan Kaçınma

Alpine.js gibi bazı JavaScript çerçeveleri de iki nokta üst üste önekli öznitelikler kullandığından, Blade'e özniteliğin bir PHP ifadesi olmadığını bildirmek için çift iki nokta üst üste (`::`) önekini kullanabilirsiniz. Örneğin, aşağıdaki bileşen göz önüne alındığında:

```php
<x-button ::class="{ danger: isDeleting }">
    Submit
</x-button>
```

Aşağıdaki HTML Blade tarafından işlenecektir:

```php
<button :class="{ danger: isDeleting }">
    Submit
</button>
```

### Bileşen Metotları

Genel değişkenlerin bileşen şablonunuz tarafından kullanılabilmesinin yanı sıra, bileşen üzerindeki tüm genel metodlar da çağrılabilir. Örneğin, `isSelected` metoduna sahip bir bileşen düşünün:

Genel değişkenlerin bileşen şablonunuz tarafından kullanılabilmesinin yanı sıra, bileşen üzerindeki tüm genel yöntemler de çağrılabilir. Örneğin, isSelected yöntemine sahip bir bileşen düşünün:

```php
/**
 * Determine if the given option is the currently selected option.
 */
public function isSelected(string $option): bool
{
    return $option === $this->selected;
}
```

Bu metodu, metodun adıyla eşleşen değişkeni çağırarak bileşen şablonunuzdan çalıştırabilirsiniz:

```php
<option {{ $isSelected($value) ? 'selected' : '' }} value="{{ $value }}">
    {{ $label }}
</option>
```

### Bileşen Sınıfları İçinde Niteliklere ve Yuvalara Erişim

Blade bileşenleri ayrıca sınıfın `render` metodu içindeki bileşen adına, niteliklerine ve yuvasına erişmenize olanak tanır. Ancak, bu verilere erişmek için bileşeninizin `render` metodundan bir closure döndürmeniz gerekir. Bu closure, tek argüman olarak bir `$data` dizisi alacaktır. Bu dizi, bileşen hakkında bilgi sağlayan birkaç öğe içerecektir:

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

`componentName`, `x-` önekinden sonra HTML etiketinde kullanılan ada eşittir. Yani `<x-alert />`'in `componentName`'i `alert` olacaktır. `attributes` öğesi, HTML etiketinde mevcut olan tüm nitelikleri içerecektir. Slot öğesi, bileşenin slotunun içeriğini içeren bir `Illuminate\Support\HtmlString` örneğidir.

Closure bir dize döndürmelidir. Döndürülen dize mevcut bir görünüme karşılık geliyorsa, bu görünüm işlenir; aksi takdirde, döndürülen dize satır içi Blade görünümü olarak değerlendirilir.

### Ek Bağımlılıklar

Eğer bileşeniniz Laravel'in service container'inden bağımlılıklar gerektiriyorsa, bunları bileşenin veri niteliklerinden önce listeleyebilirsiniz ve konteyner tarafından otomatik olarak enjekte edilecektir:

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

### Nitelikleri ve Metodları Gizleme

Bazı genel metodların veya özelliklerin bileşen şablonunuzda değişken olarak gösterilmesini önlemek isterseniz, bunları bileşeninizdeki bir `$except` dizi özelliğine ekleyebilirsiniz:

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

## Bileşen Nitelikleri

Veri niteliklerinin bir bileşene nasıl aktarılacağını daha önce incelemiştik; ancak bazen bir bileşenin çalışması için gereken verilerin parçası olmayan sınıf gibi ek HTML nitelikleri belirtmeniz gerekebilir. Tipik olarak, bu ek nitelikleri bileşen şablonunun kök öğesine aktarmak istersiniz. Örneğin, bir uyarı bileşenini şu şekilde oluşturmak istediğimizi düşünün:

```php
<x-alert type="error" :message="$message" class="mt-4"/>
```

Bileşenin construcator'unun bir parçası olmayan tüm nitelikler otomatik olarak bileşenin "nitelik çantasına" eklenecektir. Bu nitelik çantası, `$attributes` değişkeni aracılığıyla bileşen tarafından otomatik olarak kullanılabilir hale getirilir. Tüm nitelikler, bu değişken yankılanarak bileşen içinde oluşturulabilir:

```php
<div {{ $attributes }}>
    <!-- Component content -->
</div>
```

>Bileşen etiketleri içinde @env gibi direktiflerin kullanılması şu anda desteklenmemektedir. Örneğin, `<x-alert :live="@env('production')"/>` derlenmeyecektir.

### Varsayılan / Birleştirilmiş Nitelikler

Bazen nitelikler için varsayılan değerler belirtmeniz veya bileşenin bazı niteliklerine ek değerler eklemeniz gerekebilir. Bunu gerçekleştirmek için nitelik çantasının `merge` metodunu kullanabilirsiniz. Bu metot özellikle bir bileşene her zaman uygulanması gereken bir dizi varsayılan CSS sınıfı tanımlamak için kullanışlıdır:

```php
<div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
    {{ $message }}
</div>
```

Bu bileşenin bu şekilde kullanıldığını varsayarsak:

```php
<x-alert type="error" :message="$message" class="mb-4"/>
```

Bileşenin son, renderlenmiş HTML'si aşağıdaki gibi görünecektir:

```php
<div class="alert alert-error mb-4">
    <!-- Contents of the $message variable -->
</div>
```

### Sınıfları Koşullu Olarak Birleştirme

Bazen belirli bir koşul `true` ise sınıfları birleştirmek isteyebilirsiniz. Bunu, dizi anahtarının eklemek istediğiniz sınıf veya sınıfları içerdiği, değerin ise boolean bir ifade olduğu bir dizi sınıfı kabul eden `class` metoduyla gerçekleştirebilirsiniz. Dizi öğesi sayısal bir anahtara sahipse, her zaman işlenen sınıf listesine dahil edilecektir:

```php
<div {{ $attributes->class(['p-4', 'bg-red' => $hasError]) }}>
    {{ $message }}
</div>
```

Bileşeninizde başka nitelikleri birleştirmeniz gerekiyorsa, `merge` metodunu `class` metoduna zincirleyebilirsiniz:

```php
<button {{ $attributes->class(['p-4'])->merge(['type' => 'button']) }}>
    {{ $slot }}
</button>
```

>Birleştirilmiş nitelikleri almaması gereken diğer HTML öğeleri üzerinde sınıfları koşullu olarak derlemeniz gerekiyorsa, `@class` yönergesini kullanabilirsiniz.

### Sınıf Dışı Nitelik Birleştirme

`class` nitelikleri olmayan nitelikler birleştirilirken, `merge` metoduna sağlanan değerler niteliğin "varsayılan" değerleri olarak kabul edilecektir. Ancak, `class` niteliğinden farklı olarak, bu nitelikler enjekte edilen nitelik değerleriyle birleştirilmeyecektir. Bunun yerine, üzerlerine yazılacaktır. Örneğin, bir `button` bileşeninin uygulaması aşağıdaki gibi görünebilir:

```php
<button {{ $attributes->merge(['type' => 'button']) }}>
    {{ $slot }}
</button>
```

`button` bileşenini özel bir `type` ile oluşturmak için, bileşen oluşturulurken bu `type` belirtilebilir. Herhangi bir `type` belirtilmezse, `type` `button` değerini kullanır kullanılır:

```php
<x-button type="submit">
    Submit
</x-button>
```

Bu örnekteki `button` bileşeninin işlenmiş HTML'si şöyle olacaktır:

```php
<button type="submit">
    Submit
</button>
```

`class` dışındaki bir niteliğin varsayılan değerinin ve enjekte edilen değerlerin bir araya getirilmesini istiyorsanız `prepends` metodunu kullanabilirsiniz. Bu örnekte, `data-controller` niteliği her zaman `profile-controller` ile başlayacak ve enjekte edilen tüm ek `data-controller` değerleri bu varsayılan değerden sonra yerleştirilecektir:

```php
<div {{ $attributes->merge(['data-controller' => $attributes->prepends('profile-controller')]) }}>
    {{ $slot }}
</div>
```

### Nitelikleri Alma ve Filtreleme

`filter` metodunu kullanarak nitelikleri filtreleyebilirsiniz. Bu metod, niteliği nitelik torbasında tutmak istiyorsanız `true` değerini döndürmesi gereken bir closure kabul eder:

```php
{{ $attributes->filter(fn (string $value, string $key) => $key == 'foo') }}
```

Kolaylık sağlamak amacıyla, anahtarları belirli bir dizeyle başlayan tüm nitelikleri almak için `whereStartsWith` metodunu kullanabilirsiniz:

```php
{{ $attributes->whereStartsWith('wire:model') }}
```

Tersine, `whereDoesntStartWith` metodu, anahtarları belirli bir dizeyle başlayan tüm nitelikleri hariç tutmak için kullanılabilir:

```php
{{ $attributes->whereDoesntStartWith('wire:model') }}
```

`first` metodu kullanarak, belirli bir nitelik torbasındaki ilk niteliği işleyebilirsiniz:

```php
{{ $attributes->whereStartsWith('wire:model')->first() }}
```

Bileşende bir niteliğin mevcut olup olmadığını kontrol etmek isterseniz `has` metodunu kullanabilirsiniz. Bu metot, nitelik adını tek argüman olarak kabul eder ve niteliğin mevcut olup olmadığını gösteren bir `boolean` döndürür:

```php
@if ($attributes->has('class'))
    <div>Class attribute is present</div>
@endif
```

`has` metoduna bir dizi aktarılırsa, metot verilen tüm niteliklerin bileşen üzerinde mevcut olup olmadığını belirler:

```php
@if ($attributes->has(['name', 'class']))
    <div>All of the attributes are present</div>
@endif
```

`hasAny` metodu, verilen niteliklerden herhangi birinin bileşen üzerinde mevcut olup olmadığını belirlemek için kullanılabilir:

```php
@if ($attributes->hasAny(['href', ':href', 'v-bind:href']))
    <div>One of the attributes is present</div>
@endif
```

`get` metodunu kullanarak belirli bir niteliğin değerini alabilirsiniz:

```php
{{ $attributes->get('class') }}
```

## Rezerve Edilmiş Anahtar Kelimeler

Varsayılan olarak, bazı anahtar kelimeler bileşenleri oluşturmak için Blade'in dahili kullanımına ayrılmıştır. Aşağıdaki anahtar sözcükler, bileşenleriniz içinde genel özellikler veya metot adları olarak tanımlanamaz:

- `data`
- `render`
- `resloveView`
- `shouldRender`
- `view`
- `withAttributes`
- `withName`

## Slots (Yuvalar)

Sıklıkla bileşeninize "yuvalar" aracılığıyla ek içerik aktarmanız gerekecektir. Bileşen slot'ları `$slot` değişkeninin echo'lanması ile oluşturulur. Bu kavramı keşfetmek için, bir `alert` bileşeninin aşağıdaki işaretlemeye sahip olduğunu düşünelim:

```php
<div class="alert alert-danger">
    {{ $slot }}
</div>
```

Bileşene içerik enjekte ederek slot'a içerik aktarabiliriz:

```php
<x-alert>
    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

Bazen bir bileşenin, bileşen içinde farklı konumlarda birden fazla farklı slot oluşturması gerekebilir. Uyarı bileşenimizi bir "title" slot'unun eklenmesine izin verecek şekilde değiştirelim:

```php
<!-- /resources/views/components/alert.blade.php -->
 
<span class="alert-title">{{ $title }}</span>
 
<div class="alert alert-danger">
    {{ $slot }}
</div>
```

İsimlendirilmiş slto'un içeriğini `x-slot` etiketini kullanarak tanımlayabilirsiniz. Açık bir `x-slot` etiketi içinde olmayan herhangi bir içerik `$slot` değişkeni içinde bileşene aktarılacaktır:

```php
<x-alert>
    <x-slot:title>
        Server Error
    </x-slot>
 
    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

Slotun içerik içerip içermediğini belirlemek için bir slotun `isEmpty` metodunu çağırabilirsiniz:

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

Ayrıca, `hasActualContent` metodu, slot'un HTML yorumu olmayan herhangi bir "gerçek" içerik içerip içermediğini belirlemek için kullanılabilir:

```php
@if ($slot->hasActualContent())
    The scope has non-comment content.
@endif
```

### Kapsamlı Slot'lar

Vue gibi bir JavaScript framework kullandıysanız, slotlarınızda ki bileşenden verilere veya metodlara erişmenizi sağlayan "kapsamlandırılmış slot"lara aşina olabilirsiniz. Benzer davranışı Laravel'de bileşeniniz üzerinde `public` metotlar veya özellikler tanımlayarak ve slotunuz içindeki bileşene `$component` değişkeni üzerinden erişerek elde edebilirsiniz. Bu örnekte, `x-alert` bileşeninin bileşen sınıfı üzerinde tanımlanmış `public` bir `formatAlert` metoduna sahip olduğunu varsayacağız:

```php
<x-alert>
    <x-slot:title>
        {{ $component->formatAlert('Server Error') }}
    </x-slot>
 
    <strong>Whoops!</strong> Something went wrong!
</x-alert>
```

### Slot Nitelikleri

Blade bileşenlerinde olduğu gibi, slot'lara CSS sınıf adları gibi ek nitelikler atayabilirsiniz:

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

Slot nitelikleriyle etkileşim kurmak için, slot değişkeninin `attributes` özelliğine erişebilirsiniz. 

```php
@props([
    'heading',
    'footer',
])
 
<div {{ $attributes->class(['border']) }}>
    <h1 {{ $heading->attributes->class(['text-lg']) }}>
        {{ $heading }}
    </h1>
 
    {{ $slot }}
 
    <footer {{ $footer->attributes->class(['text-gray-700']) }}>
        {{ $footer }}
    </footer>
</div>
```

## Inline Bileşen Görünümler

Çok küçük bileşenler için hem bileşen sınıfını hem de bileşenin görünüm şablonunu yönetmek zahmetli olabilir. Bu nedenle, bileşenin biçimlendirmesini doğrudan render metodundan döndürebilirsiniz:

```php
public function render(): string
{
    return <<<'blade'
        <div class="alert alert-danger">
            {{ $slot }}
        </div>
    blade;
}
```

### Inline Bileşen Oluşturma

Inline görünüm oluşturan bir bileşen oluşturmak için `make:component` komutunu çalıştırırken `--inline` seçeneğini kullanabilirsiniz:

```shell
php artisan make:component Alert --inline
```

## Dinamik Bileşenler

Bazen bir bileşeni render etmeniz gerekebilir ancak çalışma zamanına kadar hangi bileşenin render edileceğini bilemeyebilirsiniz. Bu durumda, bileşeni bir çalışma zamanı değerine veya değişkenine göre render etmek için Laravel'in yerleşik `dynamic-component` bileşenini kullanabilirsiniz:

```php
// $componentName = "secondary-button";
 
<x-dynamic-component :component="$componentName" class="mt-4" />
```

## Manually Registering Components

#çevrilecek 

# `#` Anonim Bileşenler
---
Inline bileşenlere benzer şekilde, anonim bileşenler de bir bileşeni tek bir dosya üzerinden yönetmek için bir mekanizma sağlar. Bununla birlikte, anonim bileşenler tek bir görünüm dosyası kullanır ve ilişkili bir sınıfı yoktur. Anonim bir bileşen tanımlamak için `sources/views/components` dizininize bir Blade şablonu yerleştirmeniz yeterlidir. Örneğin, `resources/views/components/alert.blade.php` adresinde bir bileşen tanımladığınızı varsayarsak, bu bileşeni şu şekilde oluşturabilirsiniz:

```php
<x-alert/>
```

Bir bileşenin `components` dizini içinde daha derinde yuvalanmış olup olmadığını belirtmek için `.` karakterini kullanabilirsiniz. Örneğin, bileşenin `resources/views/components/inputs/button.blade.php` adresinde tanımlandığını varsayarsak, bileşeni şu şekilde oluşturabilirsiniz:

```php
<x-inputs.button/>
```

## Anonim Index Bileşenler

Bazen, bir bileşen birçok Blade şablonundan oluştuğunda, söz konusu bileşenin şablonlarını tek bir dizinde gruplamak isteyebilirsiniz. Örneğin, aşağıdaki dizin yapısına sahip bir "accoridon" bileşeni hayal edin:

```plaintext
/resources/views/components/accordion.blade.php
/resources/views/components/accordion/item.blade.php
```

Bu dizin yapısı, akordeon bileşenini ve öğesini aşağıdaki gibi oluşturmanıza olanak tanır:

```php
<x-accordion>
    <x-accordion.item>
        ...
    </x-accordion.item>
</x-accordion>
```

Ancak, accordion bileşenini `x-accordion` aracılığıyla oluşturmak için, "index" accordion bileşeni şablonunu accprdion dizini içinde accordion ile ilgili diğer şablonlarla birlikte yerleştirmek yerine `resources/views/components` dizinine yerleştirmek zorunda kaldık.

Neyse ki Blade, bir bileşenin şablon dizinine bir `index.blade.php` dosyası yerleştirmenize izin verir. Bileşen için bir `index.blade.php` şablonu mevcut olduğunda, bileşenin "kök" düğümü olarak işlenecektir. Dolayısıyla, yukarıdaki örnekte verilen aynı Blade sözdizimini kullanmaya devam edebiliriz; ancak dizin yapımızı şu şekilde ayarlayacağız:

```plaintext
/resources/views/components/accordion/index.blade.php
/resources/views/components/accordion/item.blade.php
```

## Veri Özellikleri / Nitelikleri

Anonim bileşenlerin ilişkili bir sınıfı olmadığından, hangi verilerin değişken olarak bileşene aktarılacağını ve hangi niteliklerin bileşenin nitelik çantasına yerleştirileceğini nasıl ayırt edebileceğinizi merak edebilirsiniz.

Bileşeninizin Blade şablonunun üst kısmındaki `@props` yönergesini kullanarak hangi niteliklerin veri değişkenleri olarak kabul edileceğini belirtebilirsiniz. Bileşen üzerindeki diğer tüm nitelikler, bileşenin nitelik çantası aracılığıyla kullanılabilir olacaktır. Bir veri değişkenine varsayılan bir değer vermek isterseniz, dizi anahtarı olarak değişkenin adını ve dizi değeri olarak varsayılan değeri belirtebilirsiniz:

```php
<!-- /resources/views/components/alert.blade.php -->
 
@props(['type' => 'info', 'message'])
 
<div {{ $attributes->merge(['class' => 'alert alert-'.$type]) }}>
    {{ $message }}
</div>
```

Yukarıdaki bileşen tanımı göz önüne alındığında, bileşeni şu şekilde oluşturabiliriz:

```php
<x-alert type="error" :message="$message" class="mb-4"/>
```

## Ebevyn Veriye Erişme

Bazen bir alt bileşen içindeki bir üst bileşenden verilere erişmek isteyebilirsiniz. Bu durumlarda `@aware` yönergesini kullanabilirsiniz. Örneğin, bir üst `<x-menu>` ve alt `<x-menu.item>` bileşeninden oluşan karmaşık bir menü bileşeni oluşturduğumuzu düşünün:

```php
<x-menu color="purple">
    <x-menu.item>...</x-menu.item>
    <x-menu.item>...</x-menu.item>
</x-menu>
```

`<x-menu>` bileşeni aşağıdaki gibi bir uygulamaya sahip olabilir:

```php
<!-- /resources/views/components/menu/index.blade.php -->
 
@props(['color' => 'gray'])
 
<ul {{ $attributes->merge(['class' => 'bg-'.$color.'-200']) }}>
    {{ $slot }}
</ul>
```

Renk prop'u yalnızca üst öğeye (`<x-menu>`) aktarıldığı için, `<x-menu.item>` içinde kullanılamayacaktır. Ancak, `@aware` yönergesini kullanırsak, bunu `<x-menu.item>` içinde de kullanılabilir hale getirebiliriz:

```php
<!-- /resources/views/components/menu/item.blade.php -->
 
@aware(['color' => 'gray'])
 
<li {{ $attributes->merge(['class' => 'text-'.$color.'-800']) }}>
    {{ $slot }}
</li>
```

>`@aware` yönergesi, HTML nitelikleri aracılığıyla üst bileşene açıkça aktarılmayan üst verilere erişemez. Üst bileşene açıkça aktarılmayan varsayılan `@props` değerlerine `@aware` yönergesi tarafından erişilemez.

## Anonim Bileşen Yolları

Daha önce tartışıldığı gibi, anonim bileşenler genellikle `resources/views/components` dizininize bir Blade şablonu yerleştirerek tanımlanır. Ancak, zaman zaman varsayılan yola ek olarak Laravel'e başka anonim bileşen yolları kaydetmek isteyebilirsiniz.

`anonymousComponentPath` metodu, ilk bağımsız değişkeni olarak anonim bileşen konumuna giden "yolu" ve ikinci bağımsız değişkeni olarak bileşenlerin altına yerleştirilmesi gereken isteğe bağlı bir "ad alanını" kabul eder. Tipik olarak, bu metod uygulamanızın service providerslarınızın birinin `boot` metodundan çağrılmalıdır:

```php
/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    Blade::anonymousComponentPath(__DIR__.'/../components');
}
```

Bileşen yolları, yukarıdaki örnekte olduğu gibi belirtilen bir önek olmadan kaydedildiğinde, Blade bileşenlerinizde de karşılık gelen bir önek olmadan oluşturulabilir. Örneğin, yukarıda kaydedilen yolda bir `panel.blade.php` bileşeni varsa, bu şekilde oluşturulabilir:

```php
<x-panel />
```

"namespaces" öneki `anonymousComponentPath` metoduna ikinci argüman olarak sağlanabilir:

```php
Blade::anonymousComponentPath(__DIR__.'/../components', 'dashboard');
```

Bir önek sağlandığında, bu "namespaces" içindeki bileşenler, bileşen oluşturulduğunda bileşen adına bileşenin ad alanının öneki eklenerek oluşturulabilir:

```php
<x-dashboard::panel />
```

# `#` Şablon Oluşturma
---
## Componentleri Kullanarak Şablon Oluşturma

Çoğu web uygulaması çeşitli sayfalarda aynı genel düzeni korur. Oluşturduğumuz her görünümde tüm HTML düzenini tekrarlamak zorunda kalsaydık, uygulamamızı sürdürmek inanılmaz derecede külfetli ve zor olurdu. Neyse ki, bu düzeni tek bir Blade bileşeni olarak tanımlamak ve daha sonra uygulamamız boyunca kullanmak uygundur.

### Şablon Bileşenininin Tanımlanması

Örneğin, bir "yapılacaklar" listesi uygulaması oluşturduğumuzu düşünün. Aşağıdaki gibi görünen bir `layout` bileşeni tanımlayabiliriz:

```php
<!-- resources/views/components/layout.blade.php -->
 
<html>
    <head>
        <title>{{ $title ?? 'Todo Manager' }}</title>
    </head>
    <body>
        <h1>Todos</h1>
        <hr/>
        {{ $slot }}
    </body>
</html>
```

### Şablon Bileşeninin Uygulanması

`layout` bileşeni tanımlandıktan sonra, bileşeni kullanan bir Blade görünümü oluşturabiliriz. Bu örnekte, görev listemizi görüntüleyen basit bir görünüm tanımlayacağız:

```php
<!-- resources/views/tasks.blade.php -->
 
<x-layout>
    @foreach ($tasks as $task)
        {{ $task }}
    @endforeach
</x-layout>
```

Unutmayın, bir bileşene enjekte edilen içerik, düzen bileşenimizdeki varsayılan `$slot` değişkenine sağlanacaktır. Fark etmiş olabileceğiniz gibi, düzenimiz ayrıca sağlanmışsa bir `$title` slot'una da saygı duyar; aksi takdirde varsayılan bir başlık gösterilir. Bileşen belgelerinde tartışılan standart slot sözdizimini kullanarak görev listesi görünümümüzden özel bir başlık enjekte edebiliriz:

```php
<!-- resources/views/tasks.blade.php -->
 
<x-layout>
    <x-slot:title>
        Custom Title
    </x-slot>
 
    @foreach ($tasks as $task)
        {{ $task }}
    @endforeach
</x-layout>
```

Artık şablonumuzu ve görev listesi görünümlerimizi tanımladığımıza göre, `task` görünümünü bir rotadan döndürmemiz gerekiyor:

```php
use App\Models\Task;
 
Route::get('/tasks', function () {
    return view('tasks', ['tasks' => Task::all()]);
});
```

## Şablon Kalıtımı Kullanarak Şablon Oluşturma

### Şablon Düzenini Oluşturma

Düzenler "şablon kalıtımı" yoluyla da oluşturulabilir. Bu, componentlerin kullanılmaya başlanmasından önce uygulama oluşturmanın birincil yoluydu.

Başlamak için basit bir örneğe göz atalım. İlk olarak, bir sayfa düzenini inceleyeceğiz. Çoğu web uygulaması çeşitli sayfalarda aynı genel düzeni koruduğundan, bu düzeni tek bir Blade görünümü olarak tanımlamak uygundur:

```php
<!-- resources/views/layouts/app.blade.php -->
 
<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show
 
        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

Gördüğünüz gibi, bu dosya tipik HTML işaretlemesi içermektedir. Ancak, `@section` ve `@yield` yönergelerine dikkat edin. Adından da anlaşılacağı gibi `@section` yönergesi bir içerik bölümü tanımlarken, `@yield` yönergesi belirli bir bölümün içeriğini görüntülemek için kullanılır.

Artık uygulamamız için bir düzen tanımladığımıza göre, düzeni devralan bir alt sayfa tanımlayalım.

### Şablonu Genişletme

Bir alt görünümü tanımlarken, alt görünümün hangi şablonu "miras alacağını" belirtmek için `@extends` Blade yönergesini kullanın. Bir Blade şablonunu genişleten görünümler, `@section` yönergelerini kullanarak şablonun bölümlerine içerik ekleyebilir. Yukarıdaki örnekte görüldüğü gibi, bu bölümlerin içeriğinin `@yield` kullanılarak şablonda görüntüleneceğini unutmayın:

```php
<!-- resources/views/child.blade.php -->
 
@extends('layouts.app')
 
@section('title', 'Page Title')
 
@section('sidebar')
    @parent
 
    <p>This is appended to the master sidebar.</p>
@endsection
 
@section('content')
    <p>This is my body content.</p>
@endsection
```

Bu örnekte, kenar çubuğu bölümü, yerleşimin kenar çubuğuna içerik eklemek (üzerine yazmak yerine) için `@parent` yönergesini kullanmaktadır. Görünüm işlendiğinde `@parent` yönergesinin yerini şablonun içeriği alacaktır.

>Önceki örneğin aksine, bu kenar çubuğu bölümü `@show` yerine `@endsection` ile biter. `@endsection` yönergesi sadece bir bölüm tanımlarken `@show` yönergesi bölümü tanımlar ve hemen verir.

Ayrıca `@yield` yönergesi ikinci değiştirgesi olarak bir öntanımlı değer kabul eder. Eğer `@yield` edilen bölüm tanımsız ise bu değer işlenir:

```php
@yield('content', 'Default content')
```

# `#` Formlar
---
## CSRF Alanı

Uygulamanızda bir HTML formu tanımladığınızda, CSRF koruma ara yazılımının isteği doğrulayabilmesi için forma gizli bir CSRF belirteci alanı eklemelisiniz. Belirteç alanını oluşturmak için `@csrf` Blade yönergesini kullanabilirsiniz:

```php
<form method="POST" action="/profile">
    @csrf
 
    ...
</form>
```

## Method Alanı

HTML formları `PUT`, `PATCH` veya `DELETE` istekleri yapamadığından, bu HTTP fiillerini taklit etmek için gizli bir `_method` alanı eklemeniz gerekecektir. Bu alanı `@method` Blade yönergesi sizin için oluşturabilir:

```php
<form action="/foo/bar" method="POST">
    @method('PUT')
 
    ...
</form>
```

## Doğrulama Hataları

`@error` yönergesi, belirli bir nitelik için doğrulama hata iletilerinin var olup olmadığını hızlıca kontrol etmek için kullanılabilir. Bir `@error` yönergesi içinde, hata mesajını görüntülemek için `$message` değişkenini yankılayabilirsiniz:

```php
<!-- /resources/views/post/create.blade.php -->
 
<label for="title">Post Title</label>
 
<input id="title"
    type="text"
    class="@error('title') is-invalid @enderror">
 
@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

`@error` yönergesi bir "if" deyimine derlendiğinden, bir öznitelik için hata olmadığında içerik oluşturmak için `@else` yönergesini kullanabilirsiniz:

```php
<!-- /resources/views/auth.blade.php -->
 
<label for="email">Email address</label>
 
<input id="email"
    type="email"
    class="@error('email') is-invalid @else is-valid @enderror">
```

Birden fazla form içeren sayfalarda doğrulama hata mesajlarını almak için `@error` yönergesine ikinci parametre olarak belirli bir hata torbasının adını aktarabilirsiniz:

```php
<!-- /resources/views/auth.blade.php -->
 
<label for="email">Email address</label>
 
<input id="email"
    type="email"
    class="@error('email', 'login') is-invalid @enderror">
 
@error('email', 'login')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

# `#` Stacks (Yığınlar)
---
Blade, başka bir görünümde veya şablonda başka bir yerde oluşturulabilecek adlandırılmış yığınları itmenize olanak tanır. Bu, özellikle alt görünümleriniz için gereken JavaScript kütüphanelerini belirtmek için yararlı olabilir:

```php
@push('scripts')
    <script src="/example.js"></script>
@endpush
```

Belirli bir boolean ifadenin `true` olarak değerlendirilmesi durumunda içeriği `@push` etmek isterseniz, `@pushIf` yönergesini kullanabilirsiniz:

```php
@pushIf($shouldPush, 'scripts')
    <script src="/example.js"></script>
@endPushIf
```

Bir yığına gerektiği kadar itme yapabilirsiniz. Yığın içeriğinin tamamını işlemek için yığının adını `@stack` yönergesine aktarın:

```php
<head>
    <!-- Head Contents -->
 
    @stack('scripts')
</head>
```

İçeriği bir yığının başına eklemek isterseniz, `@prepend` yönergesini kullanmalısınız:

```php
@push('scripts')
    This will be second...
@endpush
 
// Later...
 
@prepend('scripts')
    This will be first...
@endprepend
```




