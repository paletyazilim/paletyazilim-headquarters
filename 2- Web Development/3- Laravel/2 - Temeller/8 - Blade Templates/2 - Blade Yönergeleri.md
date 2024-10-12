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

>Bir `foreach` döngüsünde yineleme yaparken, `döngü değişkenini (loop variable)` döngü hakkında değerli bilgiler elde etmek için kullanabilirsiniz (örneğin, döngüde ilk veya son yinelemede olup olmadığınız gibi).

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

Kolaylık sağlamak amacıyla, belirli bir HTML onay kutusu girdisinin "checkedd" olup olmadığını kolayca belirtmek için `@checked` yönergesini kullanabilirsiniz. Bu yönerge, sağlanan koşul `true` olarak değerlendirilirse işaretli olarak gösterilecektir:

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
