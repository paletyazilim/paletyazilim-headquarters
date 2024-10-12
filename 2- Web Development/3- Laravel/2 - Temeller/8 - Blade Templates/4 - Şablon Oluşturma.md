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
