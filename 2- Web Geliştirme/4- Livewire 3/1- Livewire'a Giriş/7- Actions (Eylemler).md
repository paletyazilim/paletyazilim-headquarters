Livewire eylemleri, bileşeniniz üzerinde bir düğmeye tıklamak veya bir form göndermek gibi frontend etkileşimleri tarafından tetiklenebilen metotlardır. Bir PHP metotu doğrudan tarayıcıdan çağırabilmenin geliştirici deneyimini sunarak, uygulamanızın frontend'i ile backend'ini birbirine bağlayan şablon kodu yazmaya boğulmadan uygulamanızın mantığına odaklanmanıza olanak tanır.

`CreatePost` bileşeni üzerinde bir `save` eylemi çağırmanın temel bir örneğini inceleyelim:

```php title:'app/Livewire/CreatePost.php'
<?php
 
namespace App\Livewire;
 
use Livewire\Component;
use App\Models\Post;
 
class CreatePost extends Component
{
    public $title = '';
 
    public $content = '';
 
    public function save()
    {
        Post::create([
            'title' => $this->title,
            'content' => $this->content,
        ]);
 
        return redirect()->to('/posts');
    }
 
    public function render()
    {
        return view('livewire.create-post');
    }
}
```

```HTML title:'app/views/livewire/create-post.blade.php' hl:1
<form wire:submit="save"> 
    <input type="text" wire:model="title">
 
    <textarea wire:model="content"></textarea>
 
    <button type="submit">Save</button>
</form>
```

Yukarıdaki örnekte, kullanıcı "Save"e tıklayarak formu gönderdiğinde, `wire:submit` `submit` olayını yakalar ve sunucuda `save()` metotunu çağırır.

Özünde eylemler, AJAX isteklerini manuel olarak gönderme ve işleme zahmetine girmeden kullanıcı etkileşimlerini sunucu tarafı işlevselliğiyle kolayca eşleştirmenin bir yoludur.

## `#` Component Yenileme
---
Bazen bileşeninizin basit bir "yenilenmesini" tetiklemek isteyebilirsiniz. Örneğin, veritabanındaki bir şeyin durumunu kontrol eden bir bileşeniniz varsa, kullanıcılarınıza görüntülenen sonuçları yenilemelerine izin veren bir düğme göstermek isteyebilirsiniz.

Bunu Livewire'ın basit `$refresh` eylemini kullanarak normalde kendi bileşen metotuna referans verdiğiniz her yerde yapabilirsiniz:

```HTML
<button type="button" wire:click="$refresh">...</button>
```

`$refresh` eylemi tetiklendiğinde, Livewire sunucuda bir tur atacak ve herhangi bir metot çağırmadan bileşeninizi yeniden oluşturacaktır.

Bileşeninizde bekleyen tüm veri güncellemelerinin (örneğin `wire:model` bağlamaları) bileşen yenilendiğinde sunucuda uygulanacağını unutmamak önemlidir.

Livewire dahili olarak, bir Livewire bileşeninin sunucuda güncellendiği her an için "commit" adını kullanır. Bu terminolojiyi tercih ediyorsanız, `$refresh` yerine `$commit` yardımcısını kullanabilirsiniz. Bu ikisi birbirinin aynısıdır.

```HTML
<button type="button" wire:click="$commit">...</button>
```

Livewire bileşeninizde AlpineJS kullanarak bir bileşen yenilemesini de tetikleyebilirsiniz:

```HTML
<button type="button" x-on:click="$wire.$refresh()">...</button>
```

## `#` Eylemi Onaylamak
---
Kullanıcıların tehlikeli eylemler gerçekleştirmesine izin verirken (örneğin bir gönderiyi veritabanından silmek gibi), bu eylemi gerçekleştirmek istediklerini doğrulamak için onlara bir onay uyarısı göstermek isteyebilirsiniz.

Livewire, `wire:confirm` adlı basit bir yönerge sağlayarak bunu kolaylaştırır:

```HTML
<button
    type="button"
    wire:click="delete"
    wire:confirm="Are you sure you want to delete this post?"
>
    Delete post 
</button>
```

Bir Livewire eylemi içeren bir öğeye `wire:confirm` eklendiğinde, bir kullanıcı bu eylemi tetiklemeye çalıştığında, kendisine sağlanan mesajı içeren bir onay iletişim kutusu sunulur. Kullanıcılar eylemi onaylamak için "OK" tuşuna basabilir ya da "Cancel" tuşuna basabilir veya escape tuşuna basabilirler.

## `#` Olay Dinleyicileri
---
Livewire, çeşitli kullanıcı etkileşimlerine yanıt vermenize olanak tanıyan çeşitli olay dinleyicilerini destekler

| Dinleyici       | Açıklama                                                            |
| --------------- | ------------------------------------------------------------------- |
| `wire:click`      | Bir öğeye tıklandığında tetiklenir                                  |
| `wire:submit`     | Bir form gönderildiğinde tetiklenir                                 |
| `wire:keydown`    | Bir tuşa basıldığında tetiklenir                                    |
| `wire:keyup`      | Bir tuş bırakıldığında tetiklenir                                   |
| `wire:mouseenter` | Fare bir öğeye girdiğinde tetiklenir                                |
| `wire:*`          | wire:'den sonra gelen metin dinleyicinin olay adı olarak kullanılır |

`wire:'`den sonraki olay adı herhangi bir şey olabileceğinden, Livewire dinlemeniz gerekebilecek tüm tarayıcı olaylarını destekler. Örneğin, `transitionend` dinlemek için `wire:transitionend` kullanabilirsiniz.

###  Belirli Tuşları Dinleme

Tuş basma olayı dinleyicilerini belirli bir tuşa veya tuş kombinasyonuna daraltmak için Livewire'ın kullanışlı takma adlarından birini kullanabilirsiniz.

Örneğin, kullanıcı bir arama kutusuna yazdıktan sonra `Enter` tuşuna bastığında bir arama gerçekleştirmek için `wire:keydown.enter` kullanabilirsiniz:

```HTML
<input wire:model="query" wire:keydown.enter="searchPosts">
```

Tuş kombinasyonlarını dinlemek için ilkinden sonra daha fazla tuş takma adı zincirleyebilirsiniz. Örneğin, `Shift` tuşu basılıyken yalnızca `Enter` tuşunu dinlemek isterseniz, aşağıdakileri yazabilirsiniz:

```HTML
<input wire:keydown.shift.enter="...">
```

Aşağıda mevcut tüm tuş değiştiricilerin bir listesi bulunmaktadır:

| Niteleyici | Tuş                                 |
| ---------- | ----------------------------------- |
| `.shift`     | Shift                               |
| `.enter`     | Enter                               |
| `.space`     | Boşluk                              |
| `.ctrl`      | Ctrl                                |
| `.cmd`       | Cmd                                 |
| `.meta`      | Mac'te Cmd, Windows'ta Windows tuşu |
| `.alt`       | Alt                                 |
| `.up`        | Yukarı ok                           |
| `.down`      | Aşağı ok                            |
| `.left`      | Sol ok                              |
| `.right`     | Sağ ok                              |
| `.escape`    | Esc                                 |
| `.tab`       | Tab                                 |
| `.caps-lock` | Caps Lock                           |
| `.equal`     | Eşittir, `=`                        |
| `.period`    | Nokta, `.`                          |
| `.slash`     | Eğik çizgi, `/`                     |

### Olay İşleyicileri Değiştiricileri

Livewire, yaygın olay işleme görevlerini önemsiz hale getirmek için yararlı değiştiriciler de içerir.

Örneğin, bir olay dinleyicisinin içinden `event.preventDefault()` işlevini çağırmanız gerekiyorsa, olay adını `.prevent` ile sonlandırabilirsiniz:

```HTML
<input wire:keydown.prevent="...">
```

İşte mevcut tüm olay dinleyicisi değiştiricilerinin ve işlevlerinin tam listesi:

#çevrilecek 

`wire:` temelde Alpine'in `x-on` yönergesini kullandığından, bu değiştiriciler Alpine tarafından size sunulur.

### Üçüncül Taraf Olayları Yakalama

Livewire ayrıca üçüncü taraf kütüphaneler tarafından ateşlenen özel olayları dinlemeyi de destekler. 

Örneğin, projenizde Trix zengin metin düzenleyicisini kullandığınızı ve düzenleyicinin içeriğini yakalamak için `trix-change` olayını dinlemek istediğinizi düşünelim. Bunu `wire:trix-change` niteliğini kullanarak gerçekleştirebilirsiniz:

```HTML
<form wire:submit="save">
    <!-- ... -->
 
    <trix-editor
        wire:trix-change="setPostContent($event.target.value)"
    ></trix-editor>
 
    <!-- ... -->
</form>
```

Bu örnekte, `trix-change` olayı tetiklendiğinde `setPostContent` eylemi çağrılır ve Livewire bileşenindeki `content` özelliği Trix düzenleyicisinin geçerli değeriyle güncellenir.

> **Olay nesnesine `$event` kullanarak erişebilirsiniz** 
> 
> Livewire olay işleyicileri içinde, olay nesnesine `$event` aracılığıyla erişebilirsiniz. Bu, olayla ilgili bilgilere başvurmak için kullanışlıdır. Örneğin, `$event.target` aracılığıyla olayı tetikleyen öğeye erişebilirsiniz.

Yukarıdaki Trix demo kodu eksiktir ve yalnızca olay dinleyicilerinin bir gösterimi olarak kullanışlıdır. Harfi harfine kullanılırsa, her bir tuş vuruşunda bir ağ isteği tetiklenir. Daha performanslı bir uygulama şöyle olurdu:

```HTML
<trix-editor
   x-on:trix-change="$wire.content = $event.target.value"
></trix-editor>
```

### Gönderilen Olayları Dinleme

Uygulamanız Alpine'den özel olaylar gönderiyorsa, bunları Livewire kullanarak da dinleyebilirsiniz:

```HTML
<div wire:custom-event="...">
 
    <!-- Bu bileşen içinde derinlemesine yuvalanmış: -->
    <button x-on:click="$dispatch('custom-event')">...</button>
 
</div>
```

Yukarıdaki örnekte düğmeye tıklandığında, `custom-event` olayı gönderilir ve `wire:custom-event` olayının yakalandığı ve belirli bir eylemin çağrıldığı Livewire bileşeninin kök elementine kadar kabarcıklar oluşturur.

Uygulamanızda başka bir yere gönderilen bir olayı dinlemek istiyorsanız, bunun yerine olayın pencere nesnesine gelmesini beklemeniz ve olayı orada dinlemeniz gerekir. Neyse ki Livewire, herhangi bir olay dinleyicisine basit bir `.window` değiştiricisi eklemenize izin vererek bunu kolaylaştırır:

```HTML
<div wire:custom-event.window="...">
    <!-- ... -->
</div>

<!-- Sayfada bileşenin dışında bir yere gönderilir: -->
<button x-on:click="$dispatch('custom-event')">...</button>
```

### Form Gönderildikten Sonra Girdileri Devre Dışı Bırakma

Daha önce ele aldığımız `CreatePost` örneğini düşünün:

```php title:'resources/views/livewire/create-pors.blade.php'
<form wire:submit="save">
    <input wire:model="title">
 
    <textarea wire:model="content"></textarea>
 
    <button type="submit">Save</button>
</form>
```

