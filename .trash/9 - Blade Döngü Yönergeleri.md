# `#` Döngü Yönergeleri
---
Blade, koşullu ifadelerin yanı sıra, PHP'nin döngü yapılarıyla çalışmak için basit yönergeler sağlar. Yine, bu yönergelerden her biri, PHP karşılıklarıyla aynı şekilde çalışır:

```php
@for ($i = 0; $i < 10; $i++)
    Şu an ki değer: {{ $i }}
@endfor
 
@foreach ($users as $user)
    <p>Şu an ki kullanıcının ID: {{ $user->id }}</p>
@endforeach
 
@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>Kullanıcı yok</p>
@endforelse
 
@while (true)
    <p>Sonsuza kadar tekrar ederim</p>
@endwhile
```

`foreach` döngüsü üzerinden geçerken, döngü değişkenini kullanarak döngü hakkında değerli bilgiler elde edebilirsiniz, örneğin döngünün ilk mi yoksa son mu iterasyonunda olduğunuzu öğrenebilirsiniz.

Döngüler kullanılırken, mevcut iterasyonu atlayabilir veya döngüyü sonlandırabilirsiniz. Bunun için `@continue` ve `@break` yönergelerini kullanabilirsiniz.

```php
@foreach ($users as $user)
    @if ($user->type == 1)
        @continue
    @endif
 
    <li>{{ $user->name }}</li>
 
    @if ($user->number == 5)
        @break
    @endif
@endforeach
```

Yönerge bildirimine continue veya break koşulunu da ekleyebilirsiniz.

```php
@foreach ($users as $user)
    @continue($user->type == 1)
 
    <li>{{ $user->name }}</li>
 
    @break($user->number == 5)
@endforeach
```

# `#` Döngü Değişkeni
---
Foreach döngüsü üzerinden geçerken, döngü içinde bir `$loop` değişkeni kullanılabilir olacak. Bu değişken, mevcut döngü indeksi gibi bazı kullanışlı bilgilere erişim sağlar ve döngünün ilk veya son iterasyonu olup olmadığını belirtir:

```php
@foreach ($users as $user)
    @if ($loop->first)
        //Döngünün ilk iterasyonu
    @endif
 
    @if ($loop->last)
        //Döngünün son iterasyonu
    @endif
 
    <p>Kullanıcı: {{ $user->id }}</p>
@endforeach
```

Eğer iç içe döngüdeyseniz, üst döngünün `$loop` değişkenine `parent` özelliği aracılığıyla erişebilirsiniz.

```php
@foreach ($users as $user)
    @foreach ($user->posts as $post)
        @if ($loop->parent->first)
            Bu, ana döngünün ilk iterasyonudur.
        @endif
    @endforeach
@endforeach
```

`$loop` değişkeni ayrıca çeşitli diğer kullanışlı özellikleri de içerir:


| Özellik          | Açıklama                                                 |
| ---------------- | -------------------------------------------------------- |
| `$loop->index`     | Geçerli döngünün indeksi (0'dan başlar).                 |
| `$loop->iteration` | Geçerli döngünün iterasyonu (1'den başlar).              |
| `$loop->remaining` | Döngüde kalan tekrarlamalar                              |
| `$loop->count`     | Yinelemeye tabi tutulan dizideki öğelerin toplam sayısı. |
| `$loop->first`     | Bu döngünün ilk iterasyonu olup olmadığı.                |
| `$loop->last`      | Bu döngünün son iterasyonu olup olmadığı.                |
| `$loop->even`      | Bu döngüde çift bir iterasyon olup olmadığı.             |
| `$loop->odd`       | Bu döngüdeki tek bir iterasyon olup olmadığı.            |
| `$loop->depth`     | Mevcut döngünün iç içe geçme seviyesi.                   |
| `$loop->parent`    | Bir iç içe döngüde, ebeveynin döngü değişkeni.           |

