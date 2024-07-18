# `#` İlgili Modellerin Eklenmesi veya Güncellenmesi
---
## `save` Metotu

Eloquent, ilişkilere yeni modeller eklemek için uygun metotlar sağlar. Örneğin, bir gönderiye yeni bir yorum eklemeniz gerekebilir. `Comment` modelinde `post_id` niteliğini manuel olarak ayarlamak yerine, ilişkinin `save` metodunu kullanarak yorumu ekleyebilirsiniz:

```php
use App\Models\Comment;
use App\Models\Post;
 
$comment = new Comment(['message' => 'A new comment.']);
 
$post = Post::find(1);
 
$post->comments()->save($comment);
```

`comments` ilişkisine dinamik bir özellik olarak erişmediğimize dikkat edin. Bunun yerine, ilişkinin bir örneğini elde etmek için `comments` metodunu çağırdık. `save` metodu, yeni `Comment` modeline uygun `post_id` değerini otomatik olarak ekleyecektir.

Birden fazla ilgili modeli kaydetmeniz gerekiyorsa `saveMany` metodunu kullanabilirsiniz:

```php
$post = Post::find(1);
 
$post->comments()->saveMany([
    new Comment(['message' => 'A new comment.']),
    new Comment(['message' => 'Another new comment.']),
]);
```

`save` ve `saveMany` metotları verilen model örneklerini kalıcı hale getirir, ancak yeni kalıcı hale getirilen modelleri ana modele zaten yüklenmiş olan herhangi bir bellek içi ilişkiye eklemez. `save` veya `saveMany` metotlarını kullandıktan sonra ilişkiye erişmeyi planlıyorsanız, modeli ve ilişkilerini yeniden yüklemek için `refresh` metodunu kullanmak isteyebilirsiniz:

```php
$post->comments()->save($comment);
 
$post->refresh();
 
// All comments, including the newly saved comment...
$post->comments;
```

### Özyinelemeli Kaydetme

Modelinizi ve ilişkili tüm ilişkilerini kaydetmek isterseniz, `push` metodunu kullanabilirsiniz. Bu örnekte, `Post` modelinin yanı sıra yorumları ve yorum yazarları da kaydedilecektir:

```php
$post = Post::find(1);
 
$post->comments[0]->message = 'Message';
$post->comments[0]->author->name = 'Author Name';
 
$post->push();
```

`PushQuietly` metodu, bir modeli ve ilişkili ilişkilerini herhangi bir event oluşturmadan kaydetmek için kullanılabilir:

```php
$post->pushQuietly();
```

## `create` Metotu

`save` ve `saveMany` metotlarına ek olarak, bir dizi nitelik kabul eden, bir model oluşturan ve bunu veritabanına ekleyen `create` metodunu da kullanabilirsiniz. `save` ve `create` arasındaki fark, `save` tam bir Eloquent model örneği kabul ederken, `create`'in düz bir PHP dizisi kabul etmesidir. Yeni oluşturulan model `create` metotu tarafından döndürülür:

```php
use App\Models\Post;
 
$post = Post::find(1);
 
$comment = $post->comments()->create([
    'message' => 'A new comment.',
]);
```

Birden fazla ilişkili model oluşturmak için `createMany` metotunu kullanabilirsiniz:

```php
$post = Post::find(1);
 
$post->comments()->createMany([
    ['message' => 'A new comment.'],
    ['message' => 'Another new comment.'],
]);
```

`createQuietly` ve `createManyQuietly` metotları, herhangi bir olay göndermeden bir model(ler) oluşturmak için kullanılabilir:

```php
$user = User::find(1);
 
$user->posts()->createQuietly([
    'title' => 'Post title.',
]);
 
$user->posts()->createManyQuietly([
    ['title' => 'First post.'],
    ['title' => 'Second post.'],
]);
```

İlişkiler üzerinde model oluşturmak ve güncellemek için `findOrNew`, `firstOrNew`, `firstOrCreate` ve `updateOrCreate` metotlarını da kullanabilirsiniz.

> `create` metodunu kullanmadan önce, toplu atama belgelerini gözden geçirdiğinizden emin olun.

## Belongs To İlişkiler

Bir alt modeli yeni bir üst modele atamak isterseniz `associate` metodunu kullanabilirsiniz. Bu örnekte, `User` modeli `Account` modeline bir `belongsTo` ilişkisi tanımlar. Bu ilişkilendirme yöntemi, alt modeldeki yabancı anahtarı ayarlayacaktır:

```php
use App\Models\Account;
 
$account = Account::find(10);
 
$user->account()->associate($account);
 
$user->save();
```

Bir üst modeli bir alt modelden kaldırmak için `dissociate` metotunu kullanabilirsiniz. Bu metot, ilişkinin yabancı anahtarını null olarak ayarlayacaktır:

```php
$user->account()->dissociate();
 
$user->save();
```

## Many to Many İlişkiler

### Atama / Çıkarma

Eloquent ayrıca çoktan çoka ilişkilerle çalışmayı daha kolay hale getirmek için metotlar sağlar. Örneğin, bir kullanıcının birçok rolü olabileceğini ve bir rolün de birçok kullanıcısı olabileceğini düşünelim. İlişkinin ara tablosuna bir kayıt ekleyerek bir rolü bir kullanıcıya eklemek için `attach` metotunu kullanabilirsiniz:

```php
use App\Models\User;
 
$user = User::find(1);
 
$user->roles()->attach($roleId);
```

Bir modele ilişki eklerken, ara tabloya eklenecek ek verilerden oluşan bir dizi de iletebilirsiniz:

```php
$user->roles()->attach($roleId, ['expires' => $expires]);
```

Bazen bir kullanıcıdan bir rolü kaldırmak gerekebilir. Bir çoktan çoka ilişki kaydını kaldırmak için `detach` metodunu kullanın. `detach` metodu uygun kaydı ara tablodan siler; ancak her iki model de veritabanında kalır:

```php
// Detach a single role from the user...
$user->roles()->detach($roleId);
 
// Detach all roles from the user...
$user->roles()->detach();
```

Kolaylık sağlamak için, `attach` ve `detach` ID dizilerini de girdi olarak kabul eder:

```php
$user = User::find(1);
 
$user->roles()->detach([1, 2, 3]);
 
$user->roles()->attach([
    1 => ['expires' => $expires],
    2 => ['expires' => $expires],
]);
```

### İlişkileri Senkronize Etme

Çoktan çoğa ilişkilendirmeler oluşturmak için `sync` metodunu da kullanabilirsiniz. `sync` metotu, ara tabloya yerleştirilecek bir ID dizisini kabul eder. Verilen dizide olmayan tüm kimlikler ara tablodan kaldırılacaktır. Dolayısıyla, bu işlem tamamlandıktan sonra, ara tabloda yalnızca verilen dizideki kimlikler bulunacaktır:

```php
$user->roles()->sync([1, 2, 3]);
```

ID'lerle birlikte ek ara tablo değerleri de iletebilirsiniz:

```php
$user->roles()->sync([1 => ['expires' => true], 2, 3]);
```

Senkronize edilen model kimliklerinin her biriyle aynı ara tablo değerlerini eklemek isterseniz `syncWithPivotValues` metodunu da kullanabilirsiniz:

```php
$user->roles()->syncWithPivotValues([1, 2, 3], ['active' => true]);
```

Verilen dizide eksik olan mevcut ID'leri ayırmak istemiyorsanız, `syncWithoutDetaching` metodunu kullanabilirsiniz:

```php
$user->roles()->syncWithoutDetaching([1, 2, 3]);
```

### İlişki Değiştirme

Çoktan çoğa ilişki aynı zamanda verilen ilgili model kimliklerinin bağlanma durumunu "değiştiren" bir `toggle` metodu da sağlar. Verilen kimlik şu anda bağlıysa, ayrılmış olacaktır. Aynı şekilde, şu anda ayrılmışsa, eklenecektir:

```php
$user->roles()->toggle([1, 2, 3]);
```

ID'lerle birlikte ek ara tablo değerleri de iletebilirsiniz:

```php
$user->roles()->toggle([
    1 => ['expires' => true],
    2 => ['expires' => true],
]);
```

### Ara Tablodaki Bir Kaydın Güncellenmesi

İlişkinizin ara tablosundaki mevcut bir satırı güncellemeniz gerekiyorsa `updateExistingPivot` metodunu kullanabilirsiniz. Bu metot, ara kayıt yabancı anahtarını ve güncellenecek nitelikler dizisini kabul eder:

```php
$user = User::find(1);
 
$user->roles()->updateExistingPivot($roleId, [
    'active' => false,
]);
```

# `#` Ebeveynin Zaman Damgasına Erişme
---
Bir model, bir `Post`'a ait olan bir `Comment` gibi başka bir modelle `belongsTo` veya `belongsToMany` ilişkisi tanımladığında, alt model güncellendiğinde üst modelin zaman damgasını güncellemek bazen yararlı olabilir.

Örneğin, bir `Comment` modeli güncellendiğinde, sahibi olan `Post`'un `updated_at` zaman damgasına otomatik olarak "dokunmak" isteyebilirsiniz, böylece geçerli tarih ve saate ayarlanır. Bunu gerçekleştirmek için, alt modelinize, alt model güncellendiğinde `updated_at` zaman damgalarının güncellenmesi gereken ilişkilerin adlarını içeren bir `touches` özelliği ekleyebilirsiniz:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
 
class Comment extends Model
{
    /**
     * All of the relationships to be touched.
     *
     * @var array
     */
    protected $touches = ['post'];
 
    /**
     * Get the post that the comment belongs to.
     */
    public function post(): BelongsTo
    {
        return $this->belongsTo(Post::class);
    }
}
```

> Üst model zaman damgaları yalnızca alt model Eloquent'in `save` metodu kullanılarak güncellendiğinde güncellenecektir.


