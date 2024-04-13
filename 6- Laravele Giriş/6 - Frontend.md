# `#` Giriş

---

Laravel, routing (yönlendirme), validation (doğrulama), caching (önbelleğe alma), queue (kuyruklar), storage (dosya depolama) ve daha fazlası gibi modern web uygulamaları oluşturmak için ihtiyaç duyduğunuz tüm özellikleri sağlayan bir backend framework’üdür. Bununla birlikte, geliştiricilere uygulamanızın frontend’ini oluşturmak için güçlü yaklaşımlar da dahil olmak üzere güzel bir fullstack deneyimi sunar.

Laravel ile bir uygulama oluştururken frontend geliştirmeyi ele almanın iki temel yolu vardır ve hangi yaklaşımı seçeceğiniz, frontend’i PHP'den yararlanarak mı yoksa Vue ve React gibi JavaScript çerçevelerini kullanarak mı oluşturmak istediğinize göre belirlenir. Aşağıda bu seçeneklerin her ikisini de tartışacağız, böylece uygulamanız için ön uç geliştirmeye en iyi yaklaşım konusunda bilinçli bir karar verebilirsiniz.

# `#` PHP Kullanarak

---

## PHP ve Blade

Geçmişte, çoğu PHP uygulaması, istek sırasında bir veritabanından alınan verileri işleyen PHP `echo` deyimleri arasına serpiştirilmiş basit HTML şablonları kullanarak tarayıcıya HTML işliyordu:

```php
<div>
    <?php foreach ($users as $user): ?>
        Merhaba, <?php echo $user->name; ?> <br />
    <?php endforeach; ?>
</div>
```

Laravel'de, HTML oluşturmaya yönelik bu yaklaşım views ve Blade kullanılarak elde edilebilir. Blade, verileri görüntülemek, veriler üzerinde yineleme yapmak ve daha fazlası için kullanışlı, kısa sözdizimi sağlayan son derece hafif bir şablonlama dilidir:

```php
<div>
    @foreach ($users as $user)
        Merhaba, {{ $user->name }} <br />
    @endforeach
</div>
```

Uygulamaları bu şekilde oluştururken, form gönderimleri ve diğer sayfa etkileşimleri genellikle sunucudan tamamen yeni bir HTML belgesi alır ve tüm sayfa tarayıcı tarafından yeniden oluşturulur. Bugün bile birçok uygulama, basit Blade şablonları kullanılarak frontend’lerinin bu şekilde oluşturulmasına mükemmel şekilde uygun olabilir.

## Livewire

[Laravel Livewire](https://livewire.laravel.com/), tıpkı Vue ve React gibi modern JavaScript çerçeveleriyle oluşturulmuş frontend’ler gibi dinamik, modern ve canlı hissettiren Laravel destekli frontend oluşturmak için bir çerçevedir.

Livewire kullanırken, kullanıcı arayüzünüzün ayrı bir bölümünü oluşturan ve uygulamanızın frontend’inden çağrılabilen ve etkileşime girilebilen metotları ve verileri ortaya çıkaran Livewire "bileşenleri" oluşturacaksınız. Örneğin, basit bir "Sayaç" bileşeni aşağıdaki gibi görünebilir:

```php
<?php
 
namespace App\Http\Livewire;
 
use Livewire\Component;
 
class Counter extends Component
{
    public $count = 0;
 
    public function increment()
    {
        $this->count++;
    }
 
    public function render()
    {
        return view('livewire.counter');
    }
}
```

Ve sayaç için ilgili şablon şu şekilde yazılacaktır:

```php
<div>
    <button wire:click="increment">+</button>
    <h1>{{ $count }}</h1>
</div>
```

Gördüğünüz gibi Livewire, Laravel uygulamanızın frontend’i ile backend’i birbirine bağlayan `wire:click` gibi yeni HTML nitelikleri yazmanıza olanak tanır. Buna ek olarak, basit Blade ifadelerini kullanarak bileşeninizin mevcut durumunu oluşturabilirsiniz.

Birçokları için Livewire, Laravel ile ön uç geliştirmede devrim yaratarak modern, dinamik web uygulamaları oluştururken Laravel'in rahatlığı içinde kalmalarını sağladı. Tipik olarak, Livewire kullanan geliştiriciler, JavaScript'i yalnızca bir iletişim penceresi oluşturmak gibi ihtiyaç duyulan yerlerde ön uçlarına "serpmek" için Alpine.js'yi de kullanacaklardır.

Laravel'de yeniyseniz, görünümlerin ve Blade'in temel kullanımına aşina olmanızı öneririz. Ardından, etkileşimli Livewire bileşenleri ile uygulamanızı bir sonraki seviyeye nasıl taşıyacağınızı öğrenmek için resmi Laravel Livewire belgelerine başvurun.