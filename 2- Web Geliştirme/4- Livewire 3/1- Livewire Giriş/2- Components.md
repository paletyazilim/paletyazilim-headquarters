Bileşenler Livewire uygulamanızın yapı taşlarıdır. Durum ve davranışı birleştirerek frontend'iniz için yeniden kullanılabilir kullanıcı arayüzü parçaları oluştururlar. Burada, bileşen oluşturmanın ve render etmenin temellerini ele alacağız.

## # Component Oluşturma
---
Bir Livewire bileşeni basitçe `Livewire\Component`'i genişleten bir PHP sınıfıdır. Bileşen dosyalarını elle oluşturabilir veya aşağıdaki Artisan komutunu kullanabilirsiniz:

```shell
php artisan make:livewire CreatePost
```

Eğer kebab-cased isimlerini tercih ediyorsanız, onları da kullanabilirsiniz:

```shell
php artisan make:livewire create-post
```

Bu komutu çalıştırdıktan sonra, Livewire uygulamanızda iki yeni dosya oluşturacaktır. İlki bileşenin sınıfı olacaktır: `app/Livewire/CreatePost.php`

```php title:'app/Livewire/CreatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class CreatePost extends Component
{
    public function render()
    {
        return view('livewire.create-post');
    }
}
```

İkincisi bileşenin Blade görünümü olacaktır: `resources/views/livewire/create-post.blade.php`

```HTML title:'resources/views/livewire/create-post.blade.php'
<div>
    {{-- ... --}}
</div>
```

Bileşenlerinizi alt dizinlerde oluşturmak için namespace sözdizimini veya nokta notasyonunu kullanabilirsiniz. Örneğin, aşağıdaki komutlar `Posts` alt dizininde bir `CreatePost` bileşeni oluşturacaktır:

```shell
php artisan make:livewire Posts\\CreatePost
php artisan make:livewire posts.create-post
```

### Satır İçi (Inline) Bileşenler

Bileşeniniz oldukça küçükse, satır içi bir bileşen oluşturmak isteyebilirsiniz. Satır içi bileşenler, görünüm şablonu ayrı bir dosya yerine doğrudan `render()` metotunda bulunan tek dosyalı Livewire bileşenleridir:

```php title:'app/Livewire/CreatePost.php'
<?php
namespace App\Livewire;
 
use Livewire\Component;
 
class CreatePost extends Component
{
    public function render()
    {
        return <<<'HTML' 
        <div>
            {{-- Your Blade template goes here... --}}
        </div>
        HTML;
    }
}
```

`make:livewire` komutuna `--inline` özelliğini ekleyerek satır içi bileşenler oluşturabilirsiniz:

```shell
php artisan make:livewire CreatePost --inline
```

### `render()` Metodunu Atlama

Bileşenlerinizdeki ayrıntı düzeyini azaltmak için `render()` metotunu tamamen atlayabilirsiniz ve Livewire, bileşeninize karşılık gelen geleneksel ada sahip bir view döndüren kendi temel `render()` metotunu kullanacaktır:

```php
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class CreatePost extends Component
{
    //
}
```

Yukarıdaki bileşen bir sayfada işlenirse Livewire otomatik olarak `livewire.create-post` şablonunu kullanarak işlenmesi gerektiğini belirleyecektir.

### Component Dosyalarını Özelleştirme

Livewire'ın yeni bileşenler oluşturmak için kullandığı dosyaları (veya taslakları) aşağıdaki komutu çalıştırarak özelleştirebilirsiniz:

```shell
php artisan livewire:stubs
```

Bu, uygulamanızda dört yeni dosya oluşturacaktır:

- `stubs/livewire.stub` - yeni bileşenler oluşturmak için kullanılır
- `stubs/livewire.inline.stub` - satır içi bileşenleri oluşturmak için kullanılır
- `stubs/livewire.test.stub` - test dosyaları oluşturmak için kullanılır
- `stubs/livewire.view.stub` - bileşen görünümleri oluşturmak için kullanılır

Bu dosyalar uygulamanızda, `make:livewire` Artisan komutunu kullanabilirsiniz ve Livewire dosyaları oluştururken otomatik olarak özel yolları kullanacaktır.

## `#` Özelliklerin Oluşturulması
---
Livewire bileşenleri, verileri depolayan ve bileşenin sınıfı ve Blade görünümü içinden kolayca erişilebilen özelliklere sahiptir. Bu bölümde, bir bileşene özellik eklemenin ve bunu uygulamanızda kullanmanın temelleri anlatılmaktadır.

Bir Livewire bileşenine özellik eklemek için bileşen sınıfınızda bir genel özellik bildirin. Örneğin, `CreatePost` bileşeninde bir `$title` özelliği oluşturalım:

```php title:'app/Livewire/CreatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class CreatePost extends Component
{
    public $title = 'Post title...';
 
    public function render()
    {
        return view('livewire.create-post');
    }
}
```

### View'da Özelliğe Erişme

Bileşen özellikleri, bileşenin Blade görünümünde otomatik olarak kullanılabilir hale getirilir. Standart Blade sözdizimini kullanarak referans verebilirsiniz. Burada `$title` özelliğinin değerini görüntüleyeceğiz:

```HTML title:'resources/views/livewire/create-post.blade.php'
<div>
    <h1>Title: "{{ $title }}"</h1>
</div>
```

Bu bileşenin işlenmiş çıktısı şöyle olacaktır:

```HTML
<div>
    <h1>Title: "Post title..."</h1>
</div>
```

### Ek Verileri View'a Paylaşma

Görünümden özelliklere erişmenin yanı sıra, genellikle bir controllerdan yapabileceğiniz gibi, `render()` metotundan view'a açıkça veri aktarabilirsiniz. Bu, ek verileri önce bir özellik olarak depolamadan iletmek istediğinizde yararlı olabilir; çünkü özelliklerin belirli performans ve güvenlik etkileri vardır.

`render()` metotunda view'a veri aktarmak için view örneğinde `with()` metodunu kullanabilirsiniz. Örneğin, gönderinin yazarının adını görünüme aktarmak istediğinizi varsayalım. Bu durumda, gönderinin yazarı o anda kimliği doğrulanmış olan kullanıcıdır:

```php title:'app/Livewire/CreatePost.php'
<?php
 
namespace App\Livewire;
 
use Illuminate\Support\Facades\Auth;
use Livewire\Component;
 
class CreatePost extends Component
{
    public $title;
 
    public function render()
    {
        return view('livewire.create-post')->with([
            'author' => Auth::user()->name,
        ]);
    }
}
```

Artık `$author` özelliğine bileşenin Blade görünümünden erişebilirsiniz:

```HTML title:'resources/views/livewire/create-post.blade.php'
<div>
    <h1>Title: {{ $title }}</h1>
 
    <span>Author: {{ $author }}</span>
</div>
```

### `@foreach` Döngülerinde `wire:key` Eklemek

Bir Livewire şablonundaki veriler arasında `@foreach` kullanarak döngü oluştururken, döngü tarafından oluşturulan kök öğeye benzersiz bir `wire:key` niteliği eklemeniz gerekir.

Bir Blade döngüsü içinde `wire:key` özniteliği olmadan, döngü değiştiğinde Livewire eski öğeleri yeni konumlarıyla düzgün bir şekilde eşleştiremez. Bu, uygulamanızda teşhis edilmesi zor birçok soruna neden olabilir.

Örneğin, bir dizi yazı arasında döngü yapıyorsanız, `wire:key` niteliğini yazının kimliğine ayarlayabilirsiniz:

```HTML 
<div>
    @foreach ($posts as $post)
        <div wire:key="{{ $post->id }}"> 
            <!-- ... -->
        </div>
    @endforeach
</div>
```

Livewire bileşenlerini yineleyen bir dizide döngü yapıyorsanız, anahtarı bir bileşen niteliği `:key()` olarak ayarlayabilir veya `@livewire` direktifini kullanırken anahtarı üçüncü bir bağımsız değişken olarak iletebilirsiniz.

```HTML
<div>
    @foreach ($posts as $post)
        <livewire:post-item :$post :key="$post->id">
 
        @livewire(PostItem::class, ['post' => $post], key($post->id))
    @endforeach
</div>
```

### Girdileri Özelliklere Bağlama

Livewire'ın en güçlü özelliklerinden biri "veri bağlama"dır: özellikleri sayfadaki form girdileriyle otomatik olarak eşzamanlı tutma yeteneği.

`CreatePost` bileşenindeki `$title` özelliğini `wire:model` niteliği kullanarak bir metin girişine bağlayalım:

```HTML title:'resources/views/livewire/create-post.blade.php'
<form>
    <label for="title">Title:</label>
 
    <input type="text" id="title" wire:model="title"> 
</form>
```

Metin girişinde yapılan tüm değişiklikler otomatik olarak Livewire bileşeninizdeki `$title` özelliği ile senkronize edilecektir.

> **"Ben yazarken bileşenim neden canlı olarak güncellenmiyor?"** 
> 
> Bunu tarayıcınızda denediyseniz ve `$title`'ın neden otomatik olarak güncellenmediği konusunda kafanız karıştıysa, bunun nedeni Livewire'ın bir bileşeni yalnızca bir "eylem" gönderildiğinde (örneğin bir `submit` düğmesine basıldığında) güncellemesidir; kullanıcı bir alana yazdığında değil. Bu, ağ isteklerini azaltır ve performansı artırır. Kullanıcı yazdıkça "canlı" güncellemeyi etkinleştirmek için bunun yerine `wire:model.live` kullanabilirsiniz.

Livewire özellikleri son derece güçlüdür ve anlaşılması gereken önemli bir kavramdır.

## `#` Eylemleri Çağırma
---
Eylemler, Livewire bileşeniniz içinde kullanıcı etkileşimlerini işleyen veya belirli görevleri gerçekleştiren metotlardır. Genellikle bir sayfadaki düğme tıklamalarına veya form gönderimlerine yanıt vermek için kullanışlıdırlar.

Eylemler hakkında daha fazla bilgi edinmek için `CreatePost` bileşenine bir `save` eylemi ekleyelim:

```php title:'app/Livewire/CreatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
use App\Models\Post;
 
class CreatePost extends Component
{
    public $title;
 
    public function save() 
    {
        $post = Post::create([
            'title' => $this->title
        ]);
 
        return redirect()->to('/posts')
             ->with('status', 'Post created!');
    }
 
    public function render()
    {
        return view('livewire.create-post');
    }
}
```

Ardından, `<form>` öğesine `wire:submit` niteliğini ekleyerek bileşenin Blade görünümünden `save` eylemini çağıralım:

```HTML title:'resources/views/livewire/create-post.blade.php'
<form wire:submit="save"> 
    <label for="title">Title:</label>
 
    <input type="text" id="title" wire:model="title">
 
    <button type="submit">Save</button>
</form>
```

"Save" düğmesine tıklandığında, Livewire bileşeninizdeki `save()` metotu çalıştırılacak ve bileşeniniz yeniden oluşturulacaktır.

## `#` Bileşenlerin Render Edilmesi
---
Bir Livewire bileşenini sayfada render etmenin iki yolu vardır:

- Mevcut bir Blade görünümüne dahil etme 
- Tam sayfa bileşeni olarak doğrudan bir rotaya atama

İkincisinden daha basit olduğu için bileşeninizi oluşturmanın ilk yolunu ele alalım.

`<livewire:component-name />` sözdizimini kullanarak Blade şablonlarınıza bir Livewire bileşeni ekleyebilirsiniz:

```HTML
<livewire:create-post />
```

Bileşen sınıfı `app/Livewire/` dizini içinde daha derinlere yerleştirilmişse, dizin yerleştirmeyi belirtmek için `.` karakterini kullanabilirsiniz. Örneğin, bir bileşenin `app/Livewire/EditorPosts/CreatePost.php` adresinde bulunduğunu varsayarsak, bileşeni şu şekilde oluşturabiliriz:

```HTML
<livewire:editor-posts.create-post />
```

> Kebab-case kullanmalısınız 
> 
> Yukarıdaki örneklerde de görebileceğiniz gibi, bileşen adının kebab-case sürümünü kullanmalısınız. İsmin StudlyCase versiyonunu kullanmak (`<livewire:CreatePost />`) geçersizdir ve Livewire tarafından tanınmayacaktır.

### Bileşen İçine Veri Aktarımı

Bir Livewire bileşenine dışarıdan veri aktarmak için bileşen etiketindeki nitelikleri kullanabilirsiniz. Bu, bir bileşeni belirli verilerle başlatmak istediğinizde kullanışlıdır.

`CreatePost` bileşeninin `$title` özelliğine bir başlangıç değeri aktarmak için aşağıdaki sözdizimini kullanabilirsiniz:

```HTML
<livewire:create-post title="Initial Title" />
```

Bir bileşene dinamik değerler veya değişkenler aktarmanız gerekiyorsa, PHP ifadelerini bileşen özniteliklerine, özniteliğin önüne iki nokta üst üste işareti koyarak yazabilirsiniz:

```HTML
<livewire:create-post :title="$initialTitle" />
```

Bileşenlere aktarılan veriler `mount()` yaşam döngüsü hook'u aracılığıyla metot parametreleri olarak alınır. Bu durumda, `$title` parametresini bir özelliğe atamak için aşağıdaki gibi bir `mount()` metotu yazarsınız:

```php title:'app/Livewire/CreatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class CreatePost extends Component
{
    public $title;
 
    public function mount($title = null)
    {
        $this->title = $title;
    }
 
    // ...
}
```

Bu örnekte, `$title` özelliği "Initial Title" değeriyle başlatılacaktır.

`mount()` metotunu bir sınıf kurucusu olarak düşünebilirsiniz. Bileşenin ilk yüklemesinde çalışır, ancak bir sayfa içindeki sonraki isteklerde çalışmaz.

Bileşenlerinizdeki ayrıntılı kodu azaltmak için, alternatif olarak `mount()` metotunu atlayabilirsiniz ve Livewire, aktarılan değerlerle eşleşen adlarla bileşeninizdeki tüm özellikleri otomatik olarak ayarlayacaktır:

```php title:'app/Livewire/CreatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class CreatePost extends Component
{
    public $title; 
 
    // ...
}
```

Bu, bir `mount()` metotunun içine `$title` atamakla aynı şeydir.

Bu özellikler varsayılan olarak reaktif değildir

`$title` özelliği, ilk sayfa yüklemesinden sonra dış `:title="$initialValue"` değişirse otomatik olarak güncellenmeyecektir. Bu, özellikle Vue veya React gibi JavaScript çerçevelerini kullanan ve bu "parametrelerin" bu çerçevelerde "reaktif sahne" gibi davrandığını varsayan geliştiriciler için Livewire kullanırken yaygın bir kafa karışıklığı noktasıdır. Ancak endişelenmeyin, Livewire aksesuarlarınızı reaktif hale getirmeyi seçmenize izin verir.

## `#` Tam Sayfa Bileşenler
---
Livewire, bileşenleri doğrudan Laravel uygulamanızdaki bir rotaya atamanıza izin verir. Bunlara "tam sayfa bileşenler" denir. Bunları, tamamen bir Livewire bileşeni içinde kapsüllenmiş mantık ve görünümlere sahip bağımsız sayfalar oluşturmak için kullanabilirsiniz.

Tam sayfa bir bileşen oluşturmak için `routes/web.php` dosyanızda bir rota tanımlayın ve bileşeni doğrudan belirli bir URL ile eşlemek için `Route::get()` metotunu kullanın. Örneğin, `CreatePost` bileşenini özel rotada oluşturmak istediğinizi düşünelim: `/posts/create`.

Bunu `routes/web.php` dosyanıza aşağıdaki satırı ekleyerek gerçekleştirebilirsiniz:

```php title:'routes/web.php'
use App\Livewire\CreatePost;
 
Route::get('/posts/create', CreatePost::class);
```

Artık tarayıcınızda `/posts/create` yolunu ziyaret ettiğinizde `CreatePost` bileşeni bir tam sayfa bileşeni olarak işlenecektir.

### Şablon Dosyaları

Tam sayfa bileşenlerin, genellikle `resources/views/components/layouts/app.blade.php` dosyasında tanımlanan uygulamanızın şablonunu kullanacağını unutmayın.

Bu dosya henüz mevcut değilse aşağıdaki komutu çalıştırarak oluşturabilirsiniz:

```shell
php artisan livewire:layout
```

Bu komut `resources/views/components/layouts/app.blade.php` adında bir dosya oluşturacaktır.

Bu konumda bir Blade dosyası oluşturduğunuzdan ve `{{ $slot }}` yer tutucusunu eklediğinizden emin olun:

```HTML title:'resources/views/components/layouts/app.blade.php'
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
 
        <title>{{ $title ?? 'Page Title' }}</title>
    </head>
    <body>
        {{ $slot }}
    </body>
</html>
```

#### Global Şablon Yapılandırması

Tüm bileşenlerinizde özel bir şablon kullanmak için `config/livewire.php` dosyasındaki düzen anahtarını `resources/views` dosyasına göre özel şablonunuzun yoluna ayarlayabilirsiniz. Örneğin:

```php title:'config/livewire.php'
'layout' => 'layouts.app',
```

Yukarıdaki yapılandırmayla Livewire, şablon dosyasının içinde tam sayfa bileşenleri oluşturacaktır: `resources/views/layouts/app.blade.php`.

### Bileşen Şablonunu Değiştirme

Belirli bir bileşen için farklı bir şablonu kullanmak için, Livewire'ın `#[Layout]` niteliğini bileşenin `render()` metotunun üzerine yerleştirebilir ve özel şablonun göreli görünüm yolunu iletebilirsiniz:

```php title:'app/Livewire/CreatePost.php' hl:12
<?php
 
namespace App\Livewire;
 
use Livewire\Attributes\Layout;
use Livewire\Component;
 
class CreatePost extends Component
{
    // ...
 
    #[Layout('layouts.app')] 
    public function render()
    {
        return view('livewire.create-post');
    }
}
```

Ya da tercih ederseniz, bu niteliği sınıf bildiriminin üzerinde kullanabilirsiniz:

```php title:'app/Livewire/CreatePost.php' hl:8
<?php
 
namespace App\Livewire;
 
use Livewire\Attributes\Layout;
use Livewire\Component;
 
#[Layout('layouts.app')] 
class CreatePost extends Component
{
    // ...
}
```

PHP nitelikleri yalnızca değişmez değerleri destekler. Dinamik bir değer aktarmanız gerekiyorsa veya bu alternatif sözdizimini tercih ediyorsanız, bileşenin `render()` metodunda fluent `->layout()` metodunu kullanabilirsiniz:

```php title:'app/Livewire/CreatePost.php' hl:4
public function render()
{
    return view('livewire.create-post')
         ->layout('layouts.app'); 
}
```

Alternatif olarak Livewire, `@extends` ile geleneksel Blade şablon dosyalarını kullanmayı destekler.

Aşağıda şablon dosyası verilmiştir:

```HTML
<body>
    @yield('content')
</body>
```

Livewire'ı `->layout()` yerine `->extends()` kullanarak referans verecek şekilde yapılandırabilirsiniz:

```php title:'app/Livewire/ShowPosts.php' hl:4
public function render()
{
    return view('livewire.show-posts')
        ->extends('layouts.app'); 
}
```

Bileşenin kullanacağı `@section` yapılandırmanız gerekiyorsa, bunu da `->section()` metoduyla yapılandırabilirsiniz:

```php title:'app/Livewire/ShowPosts.php' hl:5
public function render()
{
    return view('livewire.show-posts')
        ->extends('layouts.app')
        ->section('body'); 
}
```

### Sayfa Başlığının Ayarlanması

Uygulamanızdaki her sayfaya benzersiz sayfa başlıkları atamak hem kullanıcılar hem de arama motorları için yararlıdır.

Tam sayfa bir bileşen için özel bir sayfa başlığı ayarlamak için öncelikle şablon dosyanızın dinamik bir başlık içerdiğinden emin olun:

```HTML
<head>
    <title>{{ $title ?? 'Page Title' }}</title>
</head>
```

Ardından, Livewire bileşeninizin `render()` metotunun üzerine `#[Title]` niteliğini ekleyin ve sayfa başlığınızı iletin:

```php title:'app/Livewire/CreatePost.php' hl:12
<?php
 
namespace App\Livewire;
 
use Livewire\Attributes\Title;
use Livewire\Component;
 
class CreatePost extends Component
{
    // ...
 
    #[Title('Create Post')] 
    public function render()
    {
        return view('livewire.create-post');
    }
}
```

Bu, `CreatePost` Livewire bileşeni için sayfa başlığını ayarlayacaktır. Bu örnekte, bileşen oluşturulduğunda sayfa başlığı "Create Post" olacaktır.

Tercih ederseniz, bu niteliği sınıf bildiriminin üzerinde kullanabilirsiniz:

```php title:'app/Livewire/CreatePost.php' hl:8
<?php
 
namespace App\Livewire;
 
use Livewire\Attributes\Title;
use Livewire\Component;
 
#[Title('Create Post')] 
class CreatePost extends Component
{
    // ...
}
```

Bileşen özelliği kullanan bir başlık gibi dinamik bir başlık geçirmeniz gerekiyorsa, bileşenin `render()` metoduna `->title()` fluent metotunu kullanabilirsiniz:

```php title:'app/Livewire/CreatePost.php'
public function render()
{
    return view('livewire.create-post')
         ->title('Create Post'); 
}
```

### Ek Slotların Ayarlanması

Düzen dosyanızda `$slot`'a ek olarak adlandırılmış slotlar varsa, kök öğenizin dışında `<x-slot>`'lar tanımlayarak Blade görünümünüzde bunların içeriğini ayarlayabilirsiniz. Örneğin, her bileşen için sayfa dilini ayrı ayrı ayarlayabilmek istiyorsanız, şablon dosyanızdaki açılış HTML etiketine dinamik bir `$lang` yuvası ekleyebilirsiniz:

```HTML title:'resources/views/components/layouts/app.blade.php'
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', $lang ?? app()->getLocale()) }}"> 
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
 
        <title>{{ $title ?? 'Page Title' }}</title>
    </head>
    <body>
        {{ $slot }}
    </body>
</html>
```

Ardından, bileşen görünümünüzde, kök öğenin dışında bir `<x-slot>` öğesi tanımlayın:

```HTML
<x-slot:lang>fr</x-slot> // This component is in French 
 
<div>
    // French content goes here...
</div>
```

### Rota Parametrelerine Erişim

Tam sayfa bileşenlerle çalışırken Livewire bileşeninizdeki rota parametrelerine erişmeniz gerekebilir. 

Bunu göstermek için önce `routes/web.php` dosyanızda parametre içeren bir rota tanımlayın:

```php title:'routes/web.php'
use App\Livewire\ShowPost;
 
Route::get('/posts/{id}', ShowPost::class);
```

Burada, bir gönderinin kimliğini temsil eden `id` parametresine sahip bir rota tanımladık.

Ardından, `mount()` metotundaki rota parametresini kabul etmek için Livewire bileşeninizi güncelleyin:

```php title:'app/Livewire/ShowPost.php'
<?php
 
namespace App\Livewire;
 
use App\Models\Post;
use Livewire\Component;
 
class ShowPost extends Component
{
    public Post $post;
 
    public function mount($id) 
    {
        $this->post = Post::findOrFail($id);
    }
 
    public function render()
    {
        return view('livewire.show-post');
    }
}
```

Bu örnekte, `$id` parametre adı `{id}` rota parametresiyle eşleştiği için, `/posts/1` URL'si ziyaret edilirse Livewire `$id` olarak "1" değerini geçirecektir.

### Rota Model Bağlama

Laravel'in rota model bağlaması, Eloquent modellerini rota parametrelerinden otomatik olarak çözümlemenizi sağlar. 

`routes/web.php` dosyanızda bir model parametresi ile bir rota tanımladıktan sonra:

```php title:'routes/web.php'
use App\Livewire\ShowPost;
 
Route::get('/posts/{post}', ShowPost::class);
```

Artık rota modeli parametresini bileşeninizin `mount()` metotu aracılığıyla kabul edebilirsiniz:

```php title:'app/Livewire/ShowPost.php'
<?php
 
namespace App\Livewire;
 
use App\Models\Post;
use Livewire\Component;
 
class ShowPost extends Component
{
    public Post $post;
 
    public function mount(Post $post) 
    {
        $this->post = $post;
    }
 
    public function render()
    {
        return view('livewire.show-post');
    }
}
```

Livewire "rota modeli bağlama" kullanmayı bilir çünkü `Post` türü ipucu olarak `mount()` içindeki `$post` parametresinin önüne eklenir.

Daha önce olduğu gibi, `mount()` metotunu kullanmayarak daha az ayrıntıya yer verebilirsiniz:

```php title:'app/Livewire/ShowPost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
use App\Models\Post;
 
class ShowPost extends Component
{
    public Post $post; 
 
    public function render()
    {
        return view('livewire.show-post');
    }
}
```

`$post` özelliği, rotanın `{post}` parametresi aracılığıyla bağlanan modele otomatik olarak atanacaktır.

### Yanıtın Değiştirilmesi

Bazı senaryolarda, yanıtı değiştirmek ve özel bir yanıt başlığı ayarlamak isteyebilirsiniz. Görünümdeki `response()` metotunu çağırarak yanıt nesnesine bağlanabilir ve yanıt nesnesini değiştirmek için bir closure kullanabilirsiniz:

```php title:'app/Livewire/ShowPost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
use Illuminate\Http\Response;
 
class ShowPost extends Component
{
    public function render()
    {
        return view('livewire.show-post')
            ->response(function(Response $response) {
                $response->header('X-Custom-Header', true);
            });
    }
}
```

## `#` Javascript Kullanma
---
#çevrilecek 