# `#` Şablon Kalıtımını Kullanan Layoutlar (Düzenler)
---
## Layout (Düzen) Oluşturma 

Layout "şablon kalıtımı" yoluyla da oluşturulabilir. Bu, components kullanılmaya başlanılmadan önce uygulama oluşturmanın birincil yoluydu.

Başlamak için, basit bir örnek inceleyelim. İlk olarak, bir sayfa düzenini inceleyeceğiz. Çoğu web uygulaması, farklı sayfalarda aynı genel düzeni koruduğu için, bu düzeni tek bir Blade görünümü olarak tanımlamak uygun olacaktır:

```php
<!-- resources/views/layouts/app.blade.php -->
 
<html>
    <head>
        <title>Uygulama İsmi - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            Sabit yan menü
        @show
 
        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

Gördüğünüz gibi, bu dosya tipik HTML işaretlemelerini içerir. Ancak, `@section` ve `@yield` yönergelerine dikkat edin. `@section` yönergesi, adından da anlaşılacağı gibi bir içerik bölümünü tanımlarken, `@yield` yönergesi belirli bir bölümün içeriğini görüntülemek için kullanılır.

Uygulamamız için bir düzen tanımladığımıza göre, düzeni miras alan bir alt sayfa tanımlayalım.

## Layout (Düzen) Kullanma

Bir alt görünüm tanımlarken, çocuk görünümün hangi layout'ı "miras alması" gerektiğini belirtmek için `@extends` Blade yönergesini kullanın. Blade düzenini genişleten görünümler, `@section` yönergelerini kullanarak içeriği düzenin bölümlerine enjekte edebilir. Yukarıdaki örnekte görüldüğü gibi, bu bölümlerin içeriği `@yield` kullanılarak düzende görüntülenecektir.

```php
<!-- resources/views/child.blade.php -->
 
@extends('layouts.app')
 
@section('title', 'Sayfa Başlığı')
 
@section('sidebar')
    @parent
 
    <p>Sabit yan menüye eklenecek ek özellik.</p>
@endsection
 
@section('content')
    <p>Ana içerğim</p>
@endsection
```

Bu örnekte, kenar çubuğu bölümü, içeriği (üzerine yazmak yerine) düzenin kenar çubuğuna eklemek için `@parent` yönergesini kullanıyor. `@parent` yönergesi, görünüm render edildiğinde düzenin içeriğiyle değiştirilecektir.

Önceki örneğin aksine, bu `sidebar` bölümü `@show` yerine `@endsection` ile sona erer. `@endsection` yönergesi sadece bir bölüm tanımlayacakken, `@show` bölümü tanımlayacak ve hemen sonuç verecektir.

`@yield` yönergesi, ikinci parametre olarak bir varsayılan değer de kabul eder. Bu değer, yield edilen bölüm tanımlanmamışsa render edilecektir:

```php
@yield('content', 'Default content')
```