Özellikler Livewire bileşenlerinizin içindeki verileri depolar ve yönetir. Bileşen sınıflarında genel özellikler olarak tanımlanırlar ve hem sunucu hem de istemci tarafında erişilebilir ve değiştirilebilirler.

## # Özellikleri Tanımlama
---
Bileşeninizin `mount()` metotu içinde özellikler için başlangıç değerleri ayarlayabilirsiniz. 

Aşağıdaki örneği ele alalım:

```php title:'app/Livewire/TodoList.php'
<?php
 
namespace App\Livewire;
 
use Illuminate\Support\Facades\Auth;
use Livewire\Component;
 
class TodoList extends Component
{
    public $todos = [];
 
    public $todo = '';
 
    public function mount()
    {
        $this->todos = Auth::user()->todos; 
    }
 
    // ...
}
```

Bu örnekte, boş bir `todos` dizisi tanımladık ve kimliği doğrulanmış kullanıcıdan gelen mevcut todos'larla başlattık. Şimdi, bileşen ilk kez oluşturulduğunda, veritabanındaki mevcut tüm todos'lar kullanıcıya gösterilir.

## # Toplu Atama
---
Bazen `mount()` metotunda çok sayıda özelliği başlatmak ayrıntılı olabilir. Bu konuda yardımcı olmak için Livewire, `fill()` metotu aracılığıyla aynı anda birden fazla özellik atamak için uygun bir yol sağlar. Özellik adları ve ilgili değerlerinden oluşan bir ilişkisel dizi ileterek, aynı anda birden fazla özelliği ayarlayabilir ve `mount()` metodundaki tekrarlayan kod satırlarını azaltabilirsiniz.

Örneğin:

```php title:'app/Livewire/UpdatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
use App\Models\Post;
 
class UpdatePost extends Component
{
    public $post;
 
    public $title;
 
    public $description;
 
    public function mount(Post $post)
    {
        $this->post = $post;
 
        $this->fill( 
            $post->only('title', 'description'), 
        ); 
    }
 
    // ...
}
```

`post->only(...)`, içine aktardığınız adlara dayalı olarak model niteliklerinin ve değerlerinin ilişkisel bir dizisini döndürdüğünden, `$title` ve `$description` özellikleri başlangıçta her birini ayrı ayrı ayarlamak zorunda kalmadan veritabanından `$post` modelinin başlığına ve açıklamasına ayarlanacaktır.

## # Veri Bağlama
---
Livewire, `wire:model` HTML niteliği aracılığıyla iki yönlü veri bağlamayı destekler. Bu, bileşen özellikleri ve HTML girdileri arasındaki verileri kolayca senkronize etmenize olanak tanıyarak kullanıcı arayüzünüzü ve bileşen durumunuzu senkronize tutar. 

`wire:model` niteliği kullanarak bir `TodoList` bileşenindeki `$todo` özelliğini temel bir girdi öğesine bağlayalım:

```php title:'app/Livewire/TodoList.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class TodoList extends Component
{
    public $todos = [];
 
    public $todo = '';
 
    public function add()
    {
        $this->todos[] = $this->todo;
 
        $this->todo = '';
    }
 
    // ...
}
```

```HTML title:'resources/views/livewire/todo-list.blade.php'
<div>
    <input type="text" wire:model="todo" placeholder="Todo..."> 
 
    <button wire:click="add">Add Todo</button>
 
    <ul>
        @foreach ($todos as $todo)
            <li>{{ $todo }}</li>
        @endforeach
    </ul>
</div>
```

Yukarıdaki örnekte, "Add Todo" düğmesine tıklandığında metin girdisinin değeri sunucudaki `$todo` özelliği ile senkronize olacaktır. 

## # Özellikleri Sıfırlama
---
Bazen, kullanıcı tarafından bir eylem gerçekleştirildikten sonra özelliklerinizi ilk durumlarına geri döndürmeniz gerekebilir. Bu durumlarda Livewire, bir veya daha fazla özellik adını kabul eden ve değerlerini ilk durumlarına sıfırlayan bir `reset()` metotu sağlar. 

Aşağıdaki örnekte, "Add Todo" düğmesine tıklandıktan sonra todo alanını sıfırlamak için `$this->reset()` kullanarak kod tekrarını önleyebiliriz:

```php title:'app/Livewire/ManageTodos.php' hl:17
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class ManageTodos extends Component
{
    public $todos = [];
 
    public $todo = '';
 
    public function addTodo()
    {
        $this->todos[] = $this->todo;
 
        $this->reset('todo'); 
    }
 
    // ...
}
```

Yukarıdaki örnekte, bir kullanıcı "Add Todo"ya tıkladıktan sonra, yeni eklenen yapılacak işi tutan giriş alanı temizlenecek ve kullanıcının yeni bir yapılacak iş yazmasına izin verilecektir.

> **`reset()` `mount()` metodunda ayarlanan değerler üzerinde çalışmaz.** 
> 
> `reset()` metodu bir özelliği `mount()` metodu çağrılmadan önceki durumuna sıfırlar. `mount()` metodunda özelliği farklı bir değerle başlattıysanız, özelliği manuel olarak sıfırlamanız gerekir.

## # Özellikleri Çekme
---
Alternatif olarak, değeri tek bir işlemde hem sıfırlamak hem de almak için `pull()` metotunu kullanabilirsiniz. 

İşte yukarıdaki aynı örnek, ancak `pull()` ile basitleştirilmiştir:

```php title:'app/Livewire/ManageTodos.php' hl:15
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
 
class ManageTodos extends Component
{
    public $todos = [];
 
    public $todo = '';
 
    public function addTodo()
    {
        $this->todos[] = $this->pull('todo'); 
    }
 
    // ...
}
```

Yukarıdaki örnekte tek bir değer çekilmektedir, ancak `pull()` işlevi tüm veya bazı özellikleri sıfırlamak ve almak (anahtar-değer çifti olarak) için de kullanılabilir:

```php 
// The same as $this->all() and $this->reset();
$this->pull();
 
// The same as $this->only(...) and $this->reset(...);
$this->pull(['title', 'content']);
```

## # Desteklenen Özellik Türleri
### Primitif Türler

Livewire dizeler, tam sayılar vb. gibi ilkel türleri destekler. Bu türler kolayca JSON'a ve JSON'dan dönüştürülebilir, bu da onları Livewire bileşenlerinde özellik olarak kullanmak için ideal hale getirir. 

Livewire aşağıdaki ilkel özellik türlerini destekler: `Array`, `String`, `Integer`, `Float`, `Boolean` ve `Null`.

```php title:'app/Livewire/TodoList.php'
class TodoList extends Component
{
    public $todos = []; // Array
 
    public $todo = ''; // String
 
    public $maxTodos = 10; // Integer
 
    public $showTodos = false; // Boolean
 
    public $todoFilter; // Null
}
```

### Yaygın PHP Türleri

İlkel tiplere ek olarak, Livewire Laravel uygulamalarında kullanılan yaygın PHP nesne tiplerini de destekler. Ancak, bu türlerin JSON'a dehydrate edileceğini ve her istekte PHP'ye geri hydrate edileceğini unutmamak önemlidir. Bu, özelliğin closure gibi çalışma zamanı değerlerini koruyamayabileceği anlamına gelir. Ayrıca, nesne hakkında sınıf adları gibi bilgiler JavaScript'e açık olabilir.

Desteklenen PHP Türleri

| Tür                 | Tam Sınıf Adı                           |
| ------------------- | --------------------------------------- |
| BackedEnum          | `BackedEnum`                              |
| Collection          | `Illuminate\Support\Collection`           |
| Eloquent Collection | `Illuminate\Database\Eloquent\Collection` |
| Model               | `Illuminate\Database\Model`               |
| DateTime            | `DateTime`                                |
| Carbon              | `Carbon\Carbon`                           |
| Stringable          | `Illuminate\Support\Stringable`           |

> **Eloquent Koleksiyonları ve Modelleri**
> 
> Eloquent Koleksiyonlarını ve Modellerini Livewire özelliklerinde depolarken `select(...)` gibi ek sorgu kısıtlamaları sonraki isteklerde yeniden uygulanmayacaktır.

İşte özellikleri bu çeşitli türler olarak ayarlamanın hızlı bir örneği:

```php 
public function mount()
{
    $this->todos = collect([]); // Collection
 
    $this->todos = Todos::all(); // Eloquent Collection
 
    $this->todo = Todos::first(); // Model
 
    $this->date = new DateTime('now'); // DateTime
 
    $this->date = new Carbon('now'); // Carbon
 
    $this->todo = str(''); // Stringable
}
```

### Özel Türleri Destekleme

#çevrilecek 

## # Javascript'en Özelliklere Erişme
---
Livewire özellikleri JavaScript aracılığıyla tarayıcıda da kullanılabilir olduğundan, bunların JavaScript temsillerine AlpineJS'den erişebilir ve bunları değiştirebilirsiniz. 

Alpine, Livewire ile birlikte gelen hafif bir JavaScript kütüphanesidir. Alpine, Livewire bileşenlerinize tam sunucu gezintileri yapmadan hafif etkileşimler oluşturmanın bir yolunu sağlar. 

Dahili olarak, Livewire'ın frontendi Alpine'in üzerine inşa edilmiştir. Aslında, her Livewire bileşeni aslında kaputun altında bir Alpine bileşenidir. Bu, Livewire bileşenlerinizin içinde Alpine'i özgürce kullanabileceğiniz anlamına gelir. 

Bu sayfanın geri kalanı Alpine ile temel bir aşinalık olduğunu varsayar.

### Özelliklere Erişme

Livewire, Alpine'e sihirli bir `$wire` nesnesi sunar. Livewire bileşeninizin içindeki herhangi bir Alpine ifadesinden `$wire` nesnesine erişebilirsiniz. 

`$wire` nesnesi, Livewire bileşeninizin JavaScript sürümü gibi ele alınabilir. Bileşeninizin PHP sürümüyle aynı özelliklere ve yöntemlere sahiptir, ancak şablonunuzda belirli işlevleri gerçekleştirmek için birkaç özel yöntem de içerir.

Örneğin, `todo` giriş alanının canlı karakter sayısını göstermek için `$wire` kullanabiliriz:

```HTML
<div>
    <input type="text" wire:model="todo">
 
    Todo character length: <h2 x-text="$wire.todo.length"></h2>
</div>
```

Kullanıcı alana yazdıkça, yazılmakta olan geçerli yapılacak işin karakter uzunluğu, sunucuya bir ağ isteği göndermeden sayfada gösterilecek ve canlı olarak güncellenecektir. 

Tercih ederseniz, aynı şeyi gerçekleştirmek için daha açık olan `.get()` metotunu kullanabilirsiniz:

```HTML
<div>
    <input type="text" wire:model="todo">
 
    Todo character length: <h2 x-text="$wire.get('todo').length"></h2>
</div>
```

### Özellikleri Manipüle Etme

Benzer şekilde, Livewire bileşeninizin özelliklerini JavaScript'te `$wire` kullanarak değiştirebilirsiniz. 

Örneğin, kullanıcının yalnızca JavaScript kullanarak giriş alanını sıfırlamasına izin vermek için `TodoList` bileşenine bir "Clear" düğmesi ekleyelim:

```HTML
<div>
    <input type="text" wire:model="todo">
 
    <button x-on:click="$wire.todo = ''">Clear</button>
</div>
```

Kullanıcı "Clear"a tıkladıktan sonra, girdi sunucuya bir ağ isteği göndermeden boş bir dizeye sıfırlanacaktır. 

Sonraki istekte, `$todo`'nun sunucu tarafındaki değeri güncellenecek ve senkronize edilecektir.

İsterseniz, özellikleri istemci tarafında ayarlamak için daha açık olan `.set()` metotunu da kullanabilirsiniz. Ancak, varsayılan olarak `.set()` metotunu kullanmanın hemen bir ağ isteğini tetiklediğini ve durumu sunucuyla senkronize ettiğini unutmamalısınız. Eğer bu isteniyorsa, bu mükemmel bir API'dir:

```HTML
<button x-on:click="$wire.set('todo', '')">Clear</button>
```

Sunucuya bir ağ isteği göndermeden özelliği güncellemek için üçüncü bir bool parametresi geçebilirsiniz. Bu, ağ isteğini erteleyecek ve sonraki bir istekte durum sunucu tarafında senkronize edilecektir:

```HTML
<button x-on:click="$wire.set('todo', '', false)">Clear</button>
```

## # Güvenlik Kaygıları
---
Livewire özellikleri güçlü bir özellik olsa da, bunları kullanmadan önce bilmeniz gereken birkaç güvenlik hususu vardır. 

Kısacası, public özellikleri her zaman kullanıcı girdisi olarak ele alın - geleneksel bir uç noktadan gelen istek girdisi gibi. Bunun ışığında, özellikleri bir veritabanına kalıcı hale getirmeden önce doğrulamak ve yetkilendirmek çok önemlidir - tıpkı bir controller'da istek girdisiyle çalışırken yaptığınız gibi.

### Özellik Değerlerine Güvenmeyin

Özellikleri yetkilendirmeyi ve doğrulamayı ihmal etmenin uygulamanızda nasıl güvenlik açıklarına yol açabileceğini göstermek için aşağıdaki `UpdatePost` bileşeni saldırıya açıktır:

```php title:'app/Livewire/UpdatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
use App\Models\Post;
 
class UpdatePost extends Component
{
    public $id;
    public $title;
    public $content;
 
    public function mount(Post $post)
    {
        $this->id = $post->id;
        $this->title = $post->title;
        $this->content = $post->content;
    }
 
    public function update()
    {
        $post = Post::findOrFail($this->id);
 
        $post->update([
            'title' => $this->title,
            'content' => $this->content,
        ]);
 
        session()->flash('message', 'Post updated successfully!');
    }
 
    public function render()
    {
        return view('livewire.update-post');
    }
}
```

```HTML title:'resources/views/livewire/update-post.blade.php'
<form wire:submit="update">
    <input type="text" wire:model="title">
    <input type="text" wire:model="content">
 
    <button type="submit">Update</button>
</form>
```

İlk bakışta, bu bileşen tamamen iyi görünebilir. Ancak, bir saldırganın uygulamanızda yetkisiz şeyler yapmak için bileşeni nasıl kullanabileceğini inceleyelim. 

Gönderinin id'sini bileşende genel bir özellik olarak sakladığımız için, istemcide başlık ve içerik özellikleriyle aynı şekilde manipüle edilebilir.

`wire:model="id"` ile bir girdi yazmamış olmamız önemli değildir. Kötü niyetli bir kullanıcı, tarayıcı DevTools'unu kullanarak görünümü kolayca aşağıdaki gibi değiştirebilir:

```HTML title:'resources/views/livewire/update-post.blade.php'
<form wire:submit="update">
    <input type="text" wire:model="id"> 
    <input type="text" wire:model="title">
    <input type="text" wire:model="content">
 
    <button type="submit">Update</button>
</form>
```

Artık kötü niyetli kullanıcı id girişini farklı bir gönderi modelinin ID'si olarak güncelleyebilir. Form gönderildiğinde ve `update()` çağrıldığında, `Post::findOrFail()` dönecek ve kullanıcının sahibi olmadığı bir gönderiyi güncelleyecektir. 

Bu tür bir saldırıyı önlemek için aşağıdaki stratejilerden birini veya her ikisini de kullanabiliriz: 

- Girdiyi yetkilendirin 
- Özelliği güncellemelerden kilitleyin

#### Girdiyi Yetkilendirme

Çünkü `$id`, tıpkı bir controller'da olduğu gibi `wire:model` gibi bir şeyle istemci tarafında manipüle edilebilir, mevcut kullanıcının gönderiyi güncelleyebildiğinden emin olmak için Laravel'in yetkilendirmesini kullanabiliriz:

```php hl:5
public function update()
{
    $post = Post::findOrFail($this->id);
 
    $this->authorize('update', $post); 
 
    $post->update(...);
}
```

Kötü niyetli bir kullanıcı `$id` özelliğini değiştirirse, eklenen yetkilendirme bunu yakalayacak ve bir hata verecektir.

#### Özelliği Kilitleme

Livewire ayrıca özelliklerin istemci tarafında değiştirilmesini önlemek için özellikleri "kilitlemenize" de olanak tanır. Bir özelliği `#[Locked]` niteliğini kullanarak istemci tarafı manipülasyonuna karşı "kilitleyebilirsiniz":

```php hl:6
use Livewire\Attributes\Locked;
use Livewire\Component;
 
class UpdatePost extends Component
{
    #[Locked] 
    public $id;
 
    // ...
}
```

Şimdi, bir kullanıcı ön uçta `$id` özelliğini değiştirmeye çalışırsa bir hata oluşacaktır. 

`#[Locked]` kullanarak, bu özelliğin bileşeninizin sınıfı dışında herhangi bir yerde değiştirilmediğini varsayabilirsiniz.

#### Eloquent Modelleri ve Kilitleme

Bir Eloquent modeli bir Livewire bileşen özelliğine atandığında, Livewire özelliği otomatik olarak kilitleyecek ve kimliğin değiştirilmemesini sağlayacaktır, böylece bu tür saldırılara karşı güvende olursunuz:

```php title:'app/Livewire/UpdatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
use App\Models\Post;
 
class UpdatePost extends Component
{
    public Post $post; 
    public $title;
    public $content;
 
    public function mount(Post $post)
    {
        $this->post = $post;
        $this->title = $post->title;
        $this->content = $post->content;
    }
 
    public function update()
    {
        $this->post->update([
            'title' => $this->title,
            'content' => $this->content,
        ]);
 
        session()->flash('message', 'Post updated successfully!');
    }
 
    public function render()
    {
        return view('livewire.update-post');
    }
}
```

### Özellikler Sistem Bilgilerini Tarayıcıya Gönderir

Hatırlanması gereken bir diğer önemli nokta da Livewire özelliklerinin tarayıcıya gönderilmeden önce serileştirildiği ya da "dehydrated" edildiğidir. Bu, değerlerinin wire üzerinden gönderilebilecek ve JavaScript tarafından anlaşılabilecek bir biçime dönüştürüldüğü anlamına gelir. Bu format, özelliklerinizin adları ve sınıf adları da dahil olmak üzere uygulamanız hakkındaki bilgileri tarayıcıya gösterebilir. 

Örneğin, `$post` adlı bir genel özellik tanımlayan bir Livewire bileşeniniz olduğunu varsayalım. Bu özellik, veritabanınızdan bir Post modelinin örneğini içerir. Bu durumda, bu özelliğin wire üzerinden gönderilen dehydrate değeri aşağıdaki gibi görünebilir:

```JSON
{
    "type": "model",
    "class": "App\Models\Post",
    "key": 1,
    "relationships": []
}
```

Gördüğünüz gibi, `$post` özelliğinin dehydrate değeri modelin sınıf adının (`App\Models\Post`) yanı sıra ID'yi ve eager-load edilmiş tüm ilişkileri içerir. 

Eğer modelin sınıf adını göstermek istemiyorsanız, Laravel'in "morphMap" metotunu bir model sınıf adına bir takma ad atamak için bir service providerde kullanabilirsiniz:

```php title:'app/Providers/AppServiceProvider.php'
<?php
 
namespace App\Providers;
 
use Illuminate\Support\ServiceProvider;
use Illuminate\Database\Eloquent\Relations\Relation;
 
class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Relation::morphMap([
            'post' => 'App\Models\Post',
        ]);
    }
}
```

Şimdi, Eloquent modeli "dehydrate" edildiğinde (serileştirildiğinde), orijinal sınıf adı açığa çıkmayacak, yalnızca "post" takma adı açığa çıkacaktır:

```JSON error:3 hl:4
{
    "type": "model",
    "class": "App\Models\Post", 
    "class": "post", 
    "key": 1,
    "relationships": []
}
```

### Eloquent Kısıtlamaları İstekler Arasında Korunamaz

Genellikle Livewire, istekler arasında sunucu tarafı özelliklerini koruyabilir ve yeniden oluşturabilir; ancak istekler arasında değerlerin korunmasının imkansız olduğu bazı senaryolar vardır.

Örneğin, Eloquent koleksiyonlarını Livewire özellikleri olarak depolarken `select(...)` gibi ek sorgu kısıtlamaları sonraki isteklerde yeniden uygulanmayacaktır.

Göstermek için, `Todos` Eloquent koleksiyonuna uygulanan bir `select()` kısıtlamasıyla aşağıdaki `ShowTodos` bileşenini düşünün:

```php title:'app/Livewire/ShowTodos.php' hl:16
<?php
 
namespace App\Livewire;
 
use Illuminate\Support\Facades\Auth;
use Livewire\Component;
 
class ShowTodos extends Component
{
    public $todos;
 
    public function mount()
    {
        $this->todos = Auth::user()
            ->todos()
            ->select(['title', 'content']) 
            ->get();
    }
 
    public function render()
    {
        return view('livewire.show-todos');
    }
}
```

Bu bileşen ilk yüklendiğinde, `$todos` özelliği kullanıcının todos'larının bir Eloquent koleksiyonuna ayarlanacaktır; ancak, veritabanındaki her satırın yalnızca başlık ve içerik alanları sorgulanmış ve her bir modele yüklenmiş olacaktır.

Livewire sonraki bir istekte bu özelliğin JSON'unu PHP'ye geri aktardığında, `select()` kısıtlaması kaybolacaktır.

Eloquent sorgularının bütünlüğünü sağlamak için, özellikler yerine computed özellikleri kullanmanızı öneririz.

Computed özellikler, bileşeninizde `#[Computed]` niteliğiyle işaretlenmiş metotlardır. Bileşenin durumunun bir parçası olarak saklanmayan, bunun yerine anında değerlendirilen dinamik bir özellik olarak erişilebilirler.

İşte yukarıdaki örnek, hesaplanmış bir özellik kullanılarak yeniden yazılmıştır:

```php title:'app/Livewire/ShowTodos.php' hl:11
<?php
 
namespace App\Livewire;
 
use Illuminate\Support\Facades\Auth;
use Livewire\Attributes\Computed;
use Livewire\Component;
 
class ShowTodos extends Component
{
    #[Computed] 
    public function todos()
    {
        return Auth::user()
            ->todos()
            ->select(['title', 'content'])
            ->get();
    }
 
    public function render()
    {
        return view('livewire.show-todos');
    }
}
```

Blade view'ında `$todos`'a şu şekilde erişebilirsiniz:

```HTML title:'resources/views/livewire/show-todos.blade.php'
<ul>
    @foreach ($this->todos as $todo)
        <li>{{ $todo }}</li>
    @endforeach
</ul>
```

View içinde, yalnızca `$this` nesnesi üzerindeki computed özelliklere şu şekilde erişebilirsiniz: `$this->todos`. `$todos` öğesine sınıfınızın içinden de erişebilirsiniz. 

Örneğin, bir `markAllAsComplete()` eyleminiz varsa:

```php title:'app/Livewire/ShowTodos.php' hl:20-23
<?php
 
namespace App\Livewire;
 
use Illuminate\Support\Facades\Auth;
use Livewire\Attributes\Computed;
use Livewire\Component;
 
class ShowTodos extends Component
{
    #[Computed]
    public function todos()
    {
        return Auth::user()
            ->todos()
            ->select(['title', 'content'])
            ->get();
    }
 
    public function markAllComplete() 
    {
        $this->todos->each->complete();
    }
 
    public function render()
    {
        return view('livewire.show-todos');
    }
}
```

İhtiyaç duyduğunuz yerde neden `$this->todos()`'u doğrudan bir metot olarak çağırmadığınızı merak edebilirsiniz. Neden ilk etapta `#[Computed]` kullanıyorsunuz?

Bunun nedeni, tek bir istek sırasında ilk kullanımlarından sonra otomatik olarak önbelleğe alındıkları için computed özelliklerin performans avantajına sahip olmasıdır. Bu, bileşeniniz içinde `$this->todos`'a serbestçe erişebileceğiniz ve gerçek metotun yalnızca bir kez çağrılacağından emin olabileceğiniz anlamına gelir, böylece aynı istekte birden çok kez yorucu bir sorgu çalıştırmazsınız.