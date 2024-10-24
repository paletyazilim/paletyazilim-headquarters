Bir Livewire bileşeni tarayıcının DOM'unu güncellediğinde, bunu "morphing" adını verdiğimiz akıllı bir şekilde yapar. *Morph* terimi *replace* gibi bir kelimenin zıttıdır.

Bir bileşen her güncellendiğinde bileşenin HTML'sini yeni işlenmiş HTML ile değiştirmek yerine, Livewire mevcut HTML ile yeni HTML'yi dinamik olarak karşılaştırır, farklılıkları belirler ve HTML'de yalnızca değişikliklerin gerekli olduğu yerlerde değişiklikler yapar.

Bu, bir bileşendeki mevcut, değiştirilmemiş öğeleri koruma avantajına sahiptir. Örneğin, olay dinleyicileri, odak durumu ve form girişi değerlerinin tümü Livewire güncellemeleri arasında korunur. Elbette morphing, her güncellemede yeni DOM'u silip yeniden oluşturmaya kıyasla daha yüksek performans da sunar.

## `#` Morphing Nasıl Çalışır?
---
Livewire'ın Livewire istekleri arasında hangi öğelerin güncelleneceğini nasıl belirlediğini anlamak için bu basit `Todos` bileşenini düşünün:

```php title:'app/Livewire/Todos.php'
class Todos extends Component
{
    public $todo = '';
 
    public $todos = [
        'first',
        'second',
    ];
 
    public function add()
    {
        $this->todos[] = $this->todo;
    }
}
```

```php title:'resources/views/livewire/todos.blade.php'
<form wire:submit="add">
    <ul>
        @foreach ($todos as $item)
            <li>{{ $item }}</li>
        @endforeach
    </ul>
 
    <input wire:model="todo">
</form>
```

Bu bileşenin ilk işlenişi aşağıdaki HTML çıktısını verecektir:

```HTML
<form wire:submit="add">
    <ul>
        <li>first</li>
 
        <li>second</li>
    </ul>
 
    <input wire:model="todo">
</form>
```

Şimdi, girdi alanına "third" yazdığınızı ve `[Enter]` tuşuna bastığınızı düşünün. Yeni oluşturulan HTML şöyle olacaktır:

```HTML hl:7
<form wire:submit="add">
    <ul>
        <li>first</li>
 
        <li>second</li>
 
        <li>third</li> 
    </ul>
 
    <input wire:model="todo">
</form>
```

Livewire bileşen güncellemesini işlediğinde, orijinal DOM'u yeni işlenen HTML'ye dönüştürür. Aşağıdaki görselleştirme size sezgisel olarak nasıl çalıştığını anlamanızı sağlayacaktır:

![[Morphing Laravel Livewire-01.mp4]]

Gördüğünüz gibi, Livewire her iki HTML ağacında da aynı anda geziniyor. Her iki ağaçtaki her bir öğeyle karşılaştığında, bunları değişiklikler, eklemeler ve çıkarmalar için karşılaştırır. Birini tespit ederse, uygun değişikliği yapar.

## # Morphing Eksiklikleri
---
Aşağıda, biçimlendirme algoritmalarının HTML ağaçlarındaki değişikliği doğru bir şekilde tanımlayamadığı ve bu nedenle uygulamanızda sorunlara neden olduğu senaryolar yer almaktadır.

### Ara Elemanların Eklenmesi

Hayali bir `CreatePost` bileşeni için aşağıdaki Livewire Blade şablonunu düşünün:

```php title:'resources/views/create-post.blade.php'
<form wire:submit="save">
    <div>
        <input wire:model="title">
    </div>
 
    @if ($errors->has('title'))
        <div>{{ $errors->first('title') }}</div>
    @endif
 
    <div>
        <button>Save</button>
    </div>
</form>
```

Bir kullanıcı formu göndermeyi dener ancak bir doğrulama hatasıyla karşılaşırsa aşağıdaki sorun oluşur:

![[Morphing Laravel Livewire-02.mp4]]

Gördüğünüz gibi, Livewire hata mesajı için yeni `<div>` ile karşılaştığında, mevcut `<div>`'i yerinde mi değiştireceğini yoksa yeni `<div>`'i ortaya mı ekleyeceğini bilemiyor.

Olanları daha açık bir şekilde tekrarlamak için:

- Livewire her iki ağaçta da ilk `<div>` ile karşılaşır. İkisi de aynıdır, bu yüzden devam eder.
- Livewire her iki ağaçta da ikinci `<div>` ile karşılaşır ve bunların aynı `<div>` olduğunu, sadece birinin içeriğinin değiştiğini düşünür. Bu yüzden hata mesajını yeni bir öğe olarak eklemek yerine `<button>` öğesini bir hata mesajına dönüştürür.
- Livewire daha sonra, yanlışlıkla önceki öğeyi değiştirdikten sonra, karşılaştırmanın sonunda ek bir öğe fark eder. Daha sonra bu öğeyi oluşturur ve bir öncekinden sonra ekler.
- Bu nedenle, aksi takdirde basitçe taşınması gereken bir öğeyi yok eder, sonra yeniden oluşturur.

Bu senaryo, morph ile ilgili neredeyse tüm hataların temelini oluşturur.

İşte bu hataların belirli birkaç sorunlu etkisi:

- Olay dinleyicileri ve öğe durumu güncellemeler arasında kaybolur
- Olay dinleyicileri ve durum yanlış öğeler arasında yanlış yerleştirilmiş
- Livewire bileşenlerinin tamamı sıfırlanabilir veya çoğaltılabilir, çünkü Livewire bileşenleri de DOM ağacındaki basit öğelerdir
- Alpine bileşenleri ve durumu kaybolabilir veya yanlış yerleştirilebilir

Neyse ki Livewire aşağıdaki yaklaşımları kullanarak bu sorunları azaltmak için çok çalışmıştır:

### Dahili İleriye Bakış

Livewire'ın biçimlendirme algoritmasında, bir öğeyi değiştirmeden önce sonraki öğeleri ve içeriklerini kontrol eden ek bir adım vardır. 

Bu, yukarıdaki senaryonun birçok durumda gerçekleşmesini önler. 

İşte "look-ahead" algoritmasının iş başındaki bir görselleştirmesi:

![[Morphing Laravel Livewire.mp4]]

### Morph Belirteçleri

Backend'e, Livewire Blade şablonlarının içindeki koşullu ifadeleri otomatik olarak algılar ve bunları Livewire'ın JavaScript'inin biçimlendirme sırasında kılavuz olarak kullanabileceği HTML yorum işaretlerine sarar.

İşte Livewire'ın enjekte edilmiş işaretleyicileri ile önceki Blade şablonunun bir örneği:

```HTML hl:6,10
<form wire:submit="save">
    <div>
        <input wire:model="title">
    </div>
 
    <!--[if BLOCK]><![endif]-->
    @if ($errors->has('title'))
        <div>Error: {{ $errors->first('title') }}</div>
    @endif
    <!--[if ENDBLOCK]><![endif]-->
 
    <div>
        <button>Save</button>
    </div>
</form>
```

Şablona enjekte edilen bu işaretçiler sayesinde Livewire artık bir değişiklik ile ekleme arasındaki farkı daha kolay tespit edebiliyor.

Bu özellik Livewire uygulamaları için son derece faydalıdır, ancak şablonları regex aracılığıyla ayrıştırmayı gerektirdiğinden, bazen koşulluları düzgün bir şekilde algılayamayabilir. Bu özellik uygulamanıza yardımcı olmaktan çok engel oluyorsa, uygulamanızın `config/livewire.php` dosyasında aşağıdaki yapılandırma ile devre dışı bırakabilirsiniz:

```php
'inject_morph_markers' => false,
```

### Koşulları Sarma

Yukarıdaki iki çözüm durumunuzu kapsamıyorsa, morping sorunlarından kaçınmanın en güvenilir yolu, koşulları ve döngüleri her zaman mevcut olan kendi öğelerine sarmaktır.

Örneğin, aşağıdaki Blade şablonu, `<div>` öğelerini sararak yeniden yazılmıştır:

```php hl:6,10
<form wire:submit="save">
    <div>
        <input wire:model="title">
    </div>
 
    <div> 
        @if ($errors->has('title'))
            <div>{{ $errors->first('title') }}</div>
        @endif
    </div> 
 
    <div>
        <button>Save</button>
    </div>
</form>
```

Artık koşullu kalıcı bir öğeye sarıldığı için Livewire iki farklı HTML ağacını düzgün bir şekilde biçimlendirecektir.

### Morphing Atlama

Bir öğe için biçimlendirmeyi tamamen atlamanız gerekiyorsa, livewire'a mevcut öğeleri biçimlendirmeye çalışmak yerine bir öğenin tüm çocuklarını değiştirmesi talimatını vermek için `wire:replace` kullanabilirsiniz.