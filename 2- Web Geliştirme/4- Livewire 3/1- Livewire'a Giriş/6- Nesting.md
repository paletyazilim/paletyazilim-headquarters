Diğer birçok bileşen tabanlı çerçeve gibi Livewire bileşenleri de iç içe yerleştirilebilir - yani bir bileşen kendi içinde birden fazla bileşen oluşturabilir. 

Ancak Livewire'ın iç içe yerleştirme sistemi diğer çerçevelerden farklı bir şekilde oluşturulduğundan, farkında olunması gereken bazı çıkarımlar ve kısıtlamalar vardır.

## # Her Bileşen Bir Adadır
---
Livewire'da bir sayfadaki her bileşen kendi durumunu izler ve güncellemeleri diğer bileşenlerden bağımsız olarak yapar.

Örneğin, aşağıdaki `Posts` ve iç içe geçmiş `ShowPost` bileşenlerini düşünün:

```php title:'app/Livewire/Posts.php'
<?php
 
namespace App\Livewire;
 
use Illuminate\Support\Facades\Auth;
use Livewire\Component;
 
class Posts extends Component
{
    public $postLimit = 2;
 
    public function render()
    {
        return view('livewire.posts', [
            'posts' => Auth::user()->posts()
                ->limit($this->postLimit)->get(),
        ]);
    }
}
```

```HTML title:'resources/views/livewire/posts.blade.php'
<div>
    Post Limit: <input type="number" wire:model.live="postLimit">
 
    @foreach ($posts as $post)
        <livewire:show-post :$post :key="$post->id"/>
    @endforeach
</div>
```

```php title:'app/Livewire/ShowPosts.php'
<?php
 
namespace App\Livewire;
 
use Illuminate\Support\Facades\Auth;
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

```HTML title:'resources/views/livewire/show-posts.blade.php'
<div>
    <h1>{{ $post->title }}</h1>
 
    <p>{{ $post->content }}</p>
 
    <button wire:click="$refresh">Refresh post</button>
</div>
```

İlk sayfa yüklemesinde tüm bileşen ağacının HTML'si şu şekilde görünebilir:

```HTML
<div wire:id="123" wire:snapshot="...">
    Post Limit: <input type="number" wire:model.live="postLimit">
 
    <div wire:id="456" wire:snapshot="...">
        <h1>The first post</h1>
 
        <p>Post content</p>
 
        <button wire:click="$refresh">Refresh post</button>
    </div>
 
    <div wire:id="789" wire:snapshot="...">
        <h1>The second post</h1>
 
        <p>Post content</p>
 
        <button wire:click="$refresh">Refresh post</button>
    </div>
</div>
```

Üst bileşenin hem kendi işlenmiş şablonunu hem de içinde yer alan tüm bileşenlerin işlenmiş şablonlarını içerdiğine dikkat edin.

Her bileşen bağımsız olduğundan, Livewire'ın JavaScript çekirdeğinin ayıklaması ve izlemesi için her birinin HTML'lerine gömülü kendi kimlikleri ve anlık görüntüleri (`wire:id` ve `wire:snapshot`) vardır.

Livewire'ın farklı iç içe geçme seviyelerini nasıl ele aldığına dair farklılıkları görmek için birkaç farklı güncelleme senaryosunu ele alalım.

### Çocuk Bileşeni Güncelleme

Alt `show-post` bileşenlerinden birinde "Refresh" düğmesine tıklarsanız, sunucuya gönderilecek olan şey şudur:

```JSON
{
    memo: { name: 'show-post', id: '456' },
 
    state: { ... },
}
```

İşte geri gönderilecek HTML:

```HTML
<div wire:id="456">
    <h1>The first post</h1>
 
    <p>Post content</p>
 
    <button wire:click="$refresh">Refresh post</button>
</div>
```

Burada dikkat edilmesi gereken önemli nokta, bir alt bileşende güncelleme tetiklendiğinde, sunucuya yalnızca o bileşenin verilerinin gönderilmesi ve yalnızca o bileşenin yeniden işlenmesidir.

Şimdi daha az sezgisel bir senaryoya bakalım: bir üst bileşeni güncellemek.

### Ebeveyn Bileşenin Güncellenmesi

Bir hatırlatma olarak, burada `Posts` bileşeninin Blade şablonu yer almaktadır:

```HTML title:'resources/views/livewire/posts.blade.php'
<div>
    Post Limit: <input type="number" wire:model.live="postLimit">
 
    @foreach ($posts as $post)
        <livewire:show-post :$post :key="$post->id"/>
    @endforeach
</div>
```

Bir kullanıcı "Post Limit" değerini `2`'den `1`'e değiştirirse, yalnızca üst öğede bir güncelleme tetiklenir.

İşte istek yükünün nasıl görünebileceğine dair bir örnek:

```JSON
{
    updates: { postLimit: 1 },
 
    snapshot: {
        memo: { name: 'posts', id: '123' },
 
        state: { postLimit: 2, ... },
    },
}
```

Gördüğünüz gibi, sunucuya yalnızca üst `Posts` bileşeninin anlık görüntüsü gönderilir.

Kendinize soruyor olabileceğiniz önemli bir soru şudur: Ana bileşen yeniden işlendiğinde ve alt `show-post` bileşenleriyle karşılaştığında ne olur? Anlık görüntüleri istek yüküne dahil edilmemişse alt bileşenleri nasıl yeniden oluşturacak?

Cevap şu: yeniden işlenmeyecekler.

Livewire `Posts` bileşenini oluştururken, karşılaştığı tüm alt bileşenler için yer tutucular oluşturacaktır.

İşte yukarıdaki güncellemeden sonra `Posts` bileşeni için işlenen HTML'nin nasıl olabileceğine dair bir örnek:

```HTML
<div wire:id="123">
    Post Limit: <input type="number" wire:model.live="postLimit">
 
    <div wire:id="456"></div>
</div>
```

Gördüğünüz gibi, postLimit `1` olarak güncellendiği için yalnızca bir alt bileşen oluşturuldu. Ancak, tam alt bileşen yerine yalnızca eşleşen `wire:id` niteliğine sahip boş bir `<div></div>` olduğunu da fark edeceksiniz.

Bu HTML frontend alındığında, Livewire bu bileşen için eski HTML'yi bu yeni HTML'ye dönüştürür, ancak herhangi bir alt bileşen yer tutucusunu akıllıca atlar.

Bunun etkisi, morphingten sonra ana `Posts` bileşeninin nihai DOM içeriğinin aşağıdaki gibi olmasıdır:

```HTML
<div wire:id="123">
    Post Limit: <input type="number" wire:model.live="postLimit">
 
    <div wire:id="456">
        <h1>The first post</h1>
 
        <p>Post content</p>
 
        <button wire:click="$refresh">Refresh post</button>
    </div>
</div>
```

## `#` Performans Etkileri
---
Livewire'ın "ada" mimarisinin uygulamanız için hem olumlu hem de olumsuz etkileri olabilir.

Bu mimarinin bir avantajı da uygulamanızın yorucu kısımlarını izole etmenize olanak sağlamasıdır. Örneğin, yavaş bir veritabanı sorgusunu kendi bağımsız bileşenine karantinaya alabilirsiniz ve performans ek yükü sayfanın geri kalanını etkilemez.

Ancak bu yaklaşımın en büyük dezavantajı, bileşenlerin tamamen ayrı olması nedeniyle bileşenler arası iletişimin/bağımlılıkların daha zor hale gelmesidir.

Örneğin, yukarıdaki ana Posts bileşeninden iç içe geçmiş `ShowPost` bileşenine aktarılan bir özelliğiniz olsaydı, bu "reaktif" olmazdı. Her bileşen bir ada olduğundan, üst bileşene yapılan bir istek `ShowPost`'a aktarılan özelliğin değerini değiştirirse, `ShowPost` içinde güncellenmez.

Livewire bu engellerin birçoğunun üstesinden gelmiş ve bu senaryolar için özel API'ler sağlamıştır: Reaktif özellikler, Modellenebilir bileşenler ve `$parent` nesnesi.

İç içe geçmiş Livewire bileşenlerinin nasıl çalıştığına dair bu bilgiyle donanmış olarak, uygulamanızda bileşenleri ne zaman ve nasıl iç içe geçireceğiniz konusunda daha bilinçli kararlar verebileceksiniz.