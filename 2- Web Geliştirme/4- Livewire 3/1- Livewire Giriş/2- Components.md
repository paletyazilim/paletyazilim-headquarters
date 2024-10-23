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

### Inline Components

Bileşeniniz oldukça küçükse, satır içi bir bileşen oluşturmak isteyebilirsiniz. Satır içi bileşenler, görünüm şablonu ayrı bir dosya yerine doğrudan `render()` metotunda bulunan tek dosyalı Livewire bileşenleridir:

```php
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
#çevrilecek 