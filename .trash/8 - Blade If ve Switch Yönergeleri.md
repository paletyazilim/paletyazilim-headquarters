# `#` Koşul Yönergeleri
---
Blade ayrıca koşullu ifadeler gibi yaygın PHP kontrol yapıları için kullanışlı kısayollar sağlar. Bu kısayollar, PHP kontrol yapılarıyla aşina olmanızı sağlarken aynı zamanda PHP karşılıklarına da aşina bir şekilde çok temiz ve özlü bir şekilde çalışmanızı sağlar.

# `#` If İfadeleri
---
`@if`, `@elseif`, `@else` ve `@endif` yönergelerini kullanarak if ifadeleri oluşturabilirsiniz. Bu yönergeler, PHP'deki karşılıklarıyla aynı şekilde çalışır.

```php
@if (count($records) === 1)
    Yalnızca bir kayıt bulundu.
@elseif (count($records) > 1)
    Birden fazla kayıt bulundu.
@else
    Hiç kayıt bulunamadı.
@endif
```

Kolaylık sağlamak için, Blade ayrıca bir `@unless` yönergesi de sağlar:

```php
@unless (Auth::check())
    Giriş yapılmamış.
@endunless
```

Zaten tartışılan koşullu yönergelerin yanı sıra, `@isset` ve `@empty` yönergeleri, ilgili PHP fonksiyonların kullanımı için pratik kısayollar olarak kullanılabilir.

```php
@isset($records)
    // $records tanımlı ve boş değil...
@endisset
 
@empty($records)
    // $records "boş"...
@endempty
```

## Authentication (Kimlik) Yönergeleri

`@auth` ve `@guest` yönergeleri, mevcut kullanıcının kimliği doğrulandı mı yoksa misafir mi olduğunu hızlı bir şekilde belirlemek için kullanılabilir.

```php
@auth
    // Kullanıcı kimliği doğrulandı...
@endauth
 
@guest
    // Kullanıcı kimliği doğrulanamadı...
@endguest
```

Gerekirse, `@auth` ve `@guest` yönergelerini kullanırken kontrol edilmesi gereken kimlik doğrulama koruyucusunu belirtebilirsiniz:

```php
@auth('admin')
    // Kullanıcı kimliği doğrulandı...
@endauth
 
@guest('admin')
    // Kullanıcı kimliği doğrulanamadı...
@endguest
```

## Environment (Ortam) Yönergeleri

Uygulamanın üretim ortamında çalışıp çalışmadığını kontrol etmek için `@production` yönergesini kullanabilirsiniz.

```php
@production
    // Üretim aşamasına ait özel içerik
@endproduction
```

Veya, uygulamanın belirli bir ortamda çalışıp çalışmadığını `@env` yönergesi kullanarak belirleyebilirsiniz:

```php
@env('staging')
    // Uygulama "staging" ortamında çalışıyor...
@endenv
 
@env(['staging', 'production'])
    // uygulama staging" yada "production" ortamında çalışıyor...
@endenv
```

## Section (Bölüm) Yönergeleri

`@hasSection` yönergesini kullanarak bir şablon miras bölümünün içeriğinin olup olmadığını belirleyebilirsiniz.

```php
@hasSection('navigation')
    <div class="pull-right">
        @yield('navigation')
    </div>
 
    <div class="clearfix"></div>
@endif
```

Bir bölümün içeriği olup olmadığını belirlemek için `@sectionMissing` yönergesini kullanabilirsiniz.

```php
@sectionMissing('navigation')
    <div class="pull-right">
        @include('default-navigation')
    </div>
@endif
```

## Session (Oturum) Yönergeleri

`@session` yönergesi, bir oturum değerinin var olup olmadığını belirlemek için kullanılabilir. Eğer oturum değeri varsa, `@session` ve `@endsession` yönergeleri arasındaki şablon içeriği değerlendirilecektir. `@session` yönergesinin içeriğinde, oturum değerini görüntülemek için `$value` değişkenini kullanabilirsiniz.

```php
@session('status')
    <div class="p-4 bg-green-100">
        {{ $value }}
    </div>
@endsession
```

# `#` Switch İfadeleri
---
`@switch`, `@case`, `@break`, `@default` ve `@endswitch` yönergeleri kullanılarak switch ifadeleri oluşturulabilir.

```php
@switch($i)
    @case(1)
        // İlk Durum...
        @break
 
    @case(2)
        // İkinci Durum...
        @break
 
    @default
        // Varsayılan Durum...
@endswitch
```