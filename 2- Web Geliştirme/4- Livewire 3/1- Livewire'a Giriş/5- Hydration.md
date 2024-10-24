Livewire'ı kullanmak, sunucu taraflı bir PHP sınıfını doğrudan bir web tarayıcısına eklemek gibi hissettirir. Sunucu tarafı işlevlerini doğrudan düğmeye basarak çağırmak gibi şeyler bu yanılsamayı destekler. Ancak gerçekte bu sadece bir yanılsamadır.

Livewire arka planda aslında daha çok standart bir web uygulaması gibi davranır. Tarayıcıya statik HTML işler, tarayıcı olaylarını dinler ve ardından sunucu tarafı kodunu çağırmak için AJAX istekleri yapar.

Livewire'ın sunucuya yaptığı her AJAX isteği "durumsuz" olduğundan (yani bir bileşenin durumunu canlı tutan uzun süre çalışan bir arka uç işlemi olmadığından), Livewire herhangi bir güncelleme yapmadan önce bir bileşenin en son bilinen durumunu yeniden oluşturmalıdır.

Bunu, her sunucu tarafı güncellemesinden sonra PHP bileşeninin "anlık görüntülerini" alarak yapar, böylece bileşen bir sonraki istekte yeniden oluşturulabilir veya devam ettirilebilir.

Bu dokümantasyon boyunca, anlık görüntüyü alma sürecine "dehydration" ve bir bileşeni anlık görüntüden yeniden oluşturma sürecine "hydration" olarak atıfta bulunacağız.

## `#` Dehydrating
---
Livewire sunucu tarafındaki bir bileşeni dehydrate yaptığında iki şey yapar: 

- Bileşenin şablonunu HTML'ye dönüştürür 
- Bileşenin JSON snapshot (anlık görüntü) oluşturur

### HTML'in Oluşturulması

Bir bileşen mount edildikten veya bir güncelleme yapıldıktan sonra Livewire, Blade şablonunu ham HTML'ye dönüştürmek için bileşenin `render()` metotunu çağırır.

Örneğin aşağıdaki Sayaç bileşenini ele alalım:

```php title:'app/Livewire/Counter.php'
class Counter extends Component
{
    public $count = 1;
 
    public function increment()
    {
        $this->count++;
    }
 
    public function render()
    {
        return <<<'HTML'
        <div>
            Count: {{ $count }}
 
            <button wire:click="increment">+</button>
        </div>
        HTML;
    }
}
```

Her mount veya güncellemeden sonra Livewire yukarıdaki Counter bileşenini aşağıdaki HTML'ye dönüştürür:

```HTML
<div>
    Count: 1
 
    <button wire:click="increment">+</button>
</div>
```

### Snapshot (Anlık Görüntü)

Bir sonraki istek sırasında `Counter` bileşenini sunucuda yeniden oluşturmak için, bileşenin durumunun mümkün olduğunca çoğunu yakalamaya çalışan bir JSON anlık görüntüsü oluşturulur:

```JSON
{
    state: {
        count: 1,
    },
 
    memo: {
        name: 'counter',
 
        id: '1526456',
    },
}
```

Anlık görüntünün iki farklı bölümüne dikkat edin: `memo` ve `state`.

`memo` kısmı bileşeni tanımlamak ve yeniden oluşturmak için gereken bilgileri depolamak için kullanılırken, `state` kısmı bileşenin tüm genel özelliklerinin değerlerini depolar.

Yukarıdaki snapshot, Livewire'daki gerçek bir snapshot'un basitleştirilmiş bir versiyonudur. Gerçek uygulamalarda, snapshot doğrulama hataları, alt bileşenlerin listesi, yerel ayarlar ve çok daha fazlası gibi bilgileri içerir.

### Snapshotu HTML'ye Gömme

Bir bileşen ilk kez oluşturulduğunda, Livewire snapshotu `wire:snapshot` adlı bir HTML niteliğinin içinde JSON olarak saklar. Bu şekilde, Livewire'ın JavaScript çekirdeği JSON'u çıkarabilir ve bir run-time nesnesine dönüştürebilir:

```HTML
<div wire:id="..." wire:snapshot="{ state: {...}, memo: {...} }">
    Count: 1
 
    <button wire:click="increment">+</button>
</div>
```

## `#` Hydrating
---
Bir bileşen güncellemesi tetiklendiğinde, örneğin Counter bileşeninde "+" düğmesine basıldığında, sunucuya aşağıdaki gibi bir yük gönderilir:

```JSON
{
    calls: [
        { method: 'increment', params: [] },
    ],
 
    snapshot: {
        state: {
            count: 1,
        },
 
        memo: {
            name: 'counter',
 
            id: '1526456',
        },
    }
}
```

Livewire'ın `increment` metotunu çağırabilmesi için önce yeni bir `Counter` örneği oluşturması ve bunu snapshot'ın durumuyla tohumlaması gerekir.

İşte bu sonucu elde eden bazı PHP psuedo kodları:

```php
$state = request('snapshot.state');
$memo = request('snapshot.memo');
 
$instance = Livewire::new($memo['name'], $memo['id']);
 
foreach ($state as $property => $value) {
    $instance[$property] = $value;
}
```

Yukarıdaki kodu takip ederseniz, bir `Counter` nesnesi oluşturulduktan sonra, genel özelliklerinin snapshot sağlanan duruma göre ayarlandığını göreceksiniz.

## `#` Gelişmiş Hydration
---
Yukarıdaki `Counter` örneği hydration kavramını göstermek için iyi çalışır; ancak Livewire'ın tamsayılar (`1`) gibi basit değerleri hydration'u nasıl ele aldığını gösterir.

Bildiğiniz gibi Livewire, tamsayıların ötesinde çok daha karmaşık özellik türlerini destekler.

Şimdi biraz daha karmaşık bir örneğe bakalım - bir `Todos` bileşeni:

```php title:'app/Livewire/Todos.php'
class Todos extends Component
{
    public $todos;
 
    public function mount() {
        $this->todos = collect([
            'first',
            'second',
            'third',
        ]);
    }
}
```

Gördüğünüz gibi, `$todos` özelliğini içeriği üç dizeden oluşan bir Laravel koleksiyonuna ayarlıyoruz.

JSON'un tek başına Laravel koleksiyonlarını temsil etmesinin bir yolu yoktur, bu nedenle Livewire bunun yerine meta verileri bir anlık görüntü içindeki saf verilerle ilişkilendirmek için kendi modelini oluşturmuştur.

İşte bu `Todos` bileşeni için snapshot'ın state nesnesi:

```JSON
state: {
    todos: [
        [ 'first', 'second', 'third' ],
        { s: 'clctn', class: 'Illuminate\\Support\\Collection' },
    ],
},
```

Daha basit bir şey bekliyorsanız bu sizin için kafa karıştırıcı olabilir:

```JSON
state: {
    todos: [ 'first', 'second', 'third' ],
},
```

Ancak Livewire bu verilere dayalı olarak bir bileşeni hydration ediyor olsaydı, bunun düz bir dizi değil de bir koleksiyon olduğunu bilmesinin hiçbir yolu olmazdı.

Bu nedenle Livewire, tuple (iki öğeden oluşan bir dizi) şeklinde alternatif bir state sözdizimini destekler:

```JSON
todos: [
    [ 'first', 'second', 'third' ],
    { s: 'clctn', class: 'Illuminate\\Support\\Collection' },
],
```

Livewire bir bileşenin durumunu hydrate yaparken bir tuple ile karşılaştığında, tuple'ın ikinci öğesinde saklanan bilgileri kullanarak ilk öğede saklanan durumu daha akıllıca hydrate yapar.

Daha açık bir şekilde göstermek için, Livewire'ın yukarıdaki snapshot'a dayalı olarak bir koleksiyon özelliğini nasıl yeniden oluşturabileceğini gösteren basitleştirilmiş kod aşağıda verilmiştir:

```php
[ $state, $metadata ] = request('snapshot.state.todos');
 
$collection = new $metadata['class']($state);
```

Gördüğünüz gibi Livewire, tam koleksiyon sınıfını türetmek için durumla ilişkili meta verileri kullanır.

### İç İçe Tuple'lar

Bu yaklaşımın belirgin bir avantajı, derin iç içe geçmiş özellikleri dehydrate ve hydrate yeteneğidir. 

Örneğin, yukarıdaki `Todos` örneğini düşünün, ancak şimdi koleksiyondaki üçüncü öğe olarak düz bir dize yerine bir Laravel Stringable ile:

```php title:'app/Livewire/Todos.php'
class Todos extends Component
{
    public $todos;
 
    public function mount() {
        $this->todos = collect([
            'first',
            'second',
            str('third'),
        ]);
    }
}
```

Bu bileşenin state'i için dehydrated edilmiş snapshot'u şimdi şöyle görünecektir:

```JSON
todos: [
    [
        'first',
        'second',
        [ 'third', { s: 'str' } ],
    ],
    { s: 'clctn', class: 'Illuminate\\Support\\Collection' },
],
```

Gördüğünüz gibi, koleksiyondaki üçüncü öğe bir meta veri tuple'ına dönüştürülmüştür. Tuple'daki ilk eleman düz string değeridir ve ikincisi Livewire'a bu stringin stringable olduğunu belirten bir flag'tır.

### Özel Özellik Türlerini Destekleme

Dahili olarak, Livewire en yaygın PHP ve Laravel türleri için hydrate desteğine sahiptir. Ancak, desteklenmeyen türleri desteklemek isterseniz, bunu Livewire'ın ilkel olmayan özellik türlerini hydrate/dehydrate etmek için dahili mekanizması olan Synthesizers'ı kullanarak yapabilirsiniz.

