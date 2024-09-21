# `#` İlişkileri Sorgulama
---
Tüm Eloquent ilişkileri metotlar aracılığıyla tanımlandığından, ilgili modelleri yüklemek için bir sorgu yürütmeden ilişkinin bir örneğini elde etmek için bu metodları çağırabilirsiniz. Buna ek olarak, tüm Eloquent ilişki türleri aynı zamanda sorgu oluşturucu görevi görerek, veritabanınızda SQL sorgusunu çalıştırmadan önce ilişki sorgusuna kısıtlamalar zincirlemeye devam etmenize olanak tanır.

Örneğin, bir `User` modelinin birçok ilişkili `Post` modeline sahip olduğu bir blog uygulaması düşünün:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
 
class User extends Model
{
    /**
     * Get all of the posts for the user.
     */
    public function posts(): HasMany
    {
        return $this->hasMany(Post::class);
    }
}
```

`posts` ilişkisini sorgulayabilir ve ilişkiye aşağıdaki gibi ek kısıtlamalar ekleyebilirsiniz:

```php
use App\Models\User;
 
$user = User::find(1);
 
$user->posts()->where('active', 1)->get();
```

Laravel sorgu oluşturucunun herhangi bir metodunu ilişki üzerinde kullanabilirsiniz.

## İlişkilerden Sonra Zincirleme `orWhere` Cümleleri

Yukarıdaki örnekte gösterildiği gibi, ilişkileri sorgularken bunlara ek kısıtlamalar eklemekte özgürsünüz. Ancak, `orWhere` cümleleri mantıksal olarak ilişki kısıtlamasıyla aynı düzeyde gruplandırılacağından, `orWhere` cümlelerini bir ilişkiye zincirlerken dikkatli olun:

```php
$user->posts()
        ->where('active', 1)
        ->orWhere('votes', '>=', 100)
        ->get();
```

Yukarıdaki örnek aşağıdaki SQL'i oluşturacaktır. Gördüğünüz gibi, `or` cümlesi sorguya 100'den fazla oy alan tüm gönderileri döndürme talimatı veriyor. Sorgu artık belirli bir kullanıcıyla kısıtlı değildir:

```MySQL
select *
from posts
where user_id = ? and active = 1 or votes >= 100
```

Çoğu durumda, parantezler arasındaki koşullu kontrolleri gruplamak için mantıksal gruplar kullanmalısınız:

```php
use Illuminate\Database\Eloquent\Builder;
 
$user->posts()
        ->where(function (Builder $query) {
            return $query->where('active', 1)
                         ->orWhere('votes', '>=', 100);
        })
        ->get();
```

Yukarıdaki örnek aşağıdaki SQL'i üretecektir. Mantıksal gruplamanın kısıtlamaları düzgün bir şekilde gruplandırdığına ve sorgunun belirli bir kullanıcıyla kısıtlı kaldığına dikkat edin:

```MySQL
select *
from posts
where user_id = ? and (active = 1 or votes >= 100)
```

## İlişki Metotları ve Dinamik Özellikleri
---
Bir Eloquent ilişki sorgusuna ek kısıtlamalar eklemeniz gerekmiyorsa, ilişkiye bir özellikmiş gibi erişebilirsiniz. Örneğin, `User` ve `Post` örnek modellerimizi kullanmaya devam ederek, bir kullanıcının tüm gönderilerine şu şekilde erişebiliriz:

```php
use App\Models\User;
 
$user = User::find(1);
 
foreach ($user->posts as $post) {
    // ...
}
```

Dinamik ilişki özellikleri "tembel yükleme" gerçekleştirir, yani ilişki verilerini yalnızca siz onlara gerçekten eriştiğinizde yüklerler. Bu nedenle, geliştiriciler genellikle model yüklendikten sonra erişileceğini bildikleri ilişkileri önceden yüklemek için istekli yüklemeyi kullanırlar. İstekli yükleme, bir modelin ilişkilerini yüklemek için yürütülmesi gereken SQL sorgularında önemli bir azalma sağlar.

## İlişki Varlığını Sorgulama
---
Model kayıtlarını alırken, sonuçlarınızı bir ilişkinin varlığına göre sınırlamak isteyebilirsiniz. Örneğin, en az bir yorumu olan tüm blog gönderilerini almak istediğinizi düşünün. Bunu yapmak için ilişkinin adını `has` ve `orHas` metodlarına aktarabilirsiniz:

```php
use App\Models\Post;
 
// Retrieve all posts that have at least one comment...
$posts = Post::has('comments')->get();
```

Sorguyu daha da özelleştirmek için bir işleç ve sayım değeri de belirtebilirsiniz:

```php
// Retrieve all posts that have three or more comments...
$posts = Post::has('comments', '>=', 3)->get();
```

İç içe `has` ifadeleri "nokta" gösterimi kullanılarak oluşturulabilir. Örneğin, en az bir resim içeren en az bir yorumu olan tüm gönderileri alabilirsiniz:

```php
// Retrieve posts that have at least one comment with images...
$posts = Post::has('comments.images')->get();
```

Daha da fazla güce ihtiyacınız varsa, `whereHas` ve `orWhereHas` metodlarını kullanarak `has` sorgularınızda ek sorgu kısıtlamaları tanımlayabilirsiniz, örneğin bir yorumun içeriğini incelemek gibi:

```php
use Illuminate\Database\Eloquent\Builder;
 
// Retrieve posts with at least one comment containing words like code%...
$posts = Post::whereHas('comments', function (Builder $query) {
    $query->where('content', 'like', 'code%');
})->get();
 
// Retrieve posts with at least ten comments containing words like code%...
$posts = Post::whereHas('comments', function (Builder $query) {
    $query->where('content', 'like', 'code%');
}, '>=', 10)->get();
```

> Eloquent şu anda veritabanları arasında ilişki varlığı için sorgulamayı desteklememektedir. İlişkiler aynı veritabanı içinde var olmalıdır.

### Inline İlişki Varlık Sorguları

İlişki sorgusuna eklenmiş tek ve basit bir `where` koşuluyla bir ilişkinin varlığını sorgulamak isterseniz `whereRelation`, `orWhereRelation`, `whereMorphRelation` ve `orWhereMorphRelation` metodlarını kullanmayı daha uygun bulabilirsiniz. Örneğin, onaylanmamış yorumları olan tüm gönderiler için sorgulama yapabiliriz:

```php
use App\Models\Post;
 
$posts = Post::whereRelation('comments', 'is_approved', false)->get();
```

Elbette, sorgu oluşturucunun `where` metoduna yapılan çağrılarda olduğu gibi, bir işleç de belirtebilirsiniz:

```php
$posts = Post::whereRelation(
    'comments', 'created_at', '>=', now()->subHour()
)->get();
```

## İlişkinin Yokluğunu Sorgulama

Model kayıtlarını alırken, sonuçlarınızı bir ilişkinin yokluğuna göre sınırlamak isteyebilirsiniz. Örneğin, hiç yorum içermeyen tüm blog gönderilerini almak istediğinizi düşünün. Bunu yapmak için ilişkinin adını `doesntHave` ve `orDoesntHave` metodlarına aktarabilirsiniz:

```php
use App\Models\Post;
 
$posts = Post::doesntHave('comments')->get();
```

Daha da fazla güce ihtiyacınız varsa, bir yorumun içeriğini incelemek gibi `doesntHave` sorgularınıza ek sorgu kısıtlamaları eklemek için `whereDoesntHave` ve `orWhereDoesntHave` metodlarını kullanabilirsiniz:

```php
use Illuminate\Database\Eloquent\Builder;
 
$posts = Post::whereDoesntHave('comments', function (Builder $query) {
    $query->where('content', 'like', 'code%');
})->get();
```

İç içe geçmiş bir ilişkiye karşı bir sorgu yürütmek için "nokta" gösterimini kullanabilirsiniz. Örneğin, aşağıdaki sorgu yorum içermeyen tüm gönderileri getirecektir; ancak, yasaklı olmayan yazarların yorumlarını içeren gönderiler sonuçlara dahil edilecektir:

```php
use Illuminate\Database\Eloquent\Builder;
 
$posts = Post::whereDoesntHave('comments.author', function (Builder $query) {
    $query->where('banned', 0);
})->get();
```

## Polimorfik İlişkileri Sorgulama

"Morph to" ilişkilerinin varlığını sorgulamak için `whereHasMorph` ve `whereDoesntHaveMorph` metodlarını kullanabilirsiniz. Bu metotlar ilk bağımsız değişken olarak ilişkinin adını kabul eder. Ardından, metotlar sorguya dahil etmek istediğiniz ilgili modellerin adlarını kabul eder. Son olarak, ilişki sorgusunu özelleştiren bir kapanış sağlayabilirsiniz:

```php
use App\Models\Comment;
use App\Models\Post;
use App\Models\Video;
use Illuminate\Database\Eloquent\Builder;
 
// Retrieve comments associated to posts or videos with a title like code%...
$comments = Comment::whereHasMorph(
    'commentable',
    [Post::class, Video::class],
    function (Builder $query) {
        $query->where('title', 'like', 'code%');
    }
)->get();
 
// Retrieve comments associated to posts with a title not like code%...
$comments = Comment::whereDoesntHaveMorph(
    'commentable',
    Post::class,
    function (Builder $query) {
        $query->where('title', 'like', 'code%');
    }
)->get();
```

Bazen ilgili polimorfik modelin "türüne" dayalı sorgu kısıtlamaları eklemeniz gerekebilir. `whereHasMorph` metoduna aktarılan closure, ikinci argüman olarak bir `$type` değeri alabilir. Bu argüman, oluşturulmakta olan sorgunun "türünü" incelemenizi sağlar:

```php
use Illuminate\Database\Eloquent\Builder;
 
$comments = Comment::whereHasMorph(
    'commentable',
    [Post::class, Video::class],
    function (Builder $query, string $type) {
        $column = $type === Post::class ? 'content' : 'title';
 
        $query->where($column, 'like', 'code%');
    }
)->get();
```

### İlgili Tüm Modelleri Sorgulama

Olası polimorfik modellerin bir dizisini geçmek yerine, bir joker değer olarak `*` sağlayabilirsiniz. Bu Laravel'e tüm olası polimorfik tipleri veritabanından alması talimatını verecektir. Laravel bu işlemi gerçekleştirmek için ek bir sorgu yürütecektir:

```php
use Illuminate\Database\Eloquent\Builder;
 
$comments = Comment::whereHasMorph('commentable', '*', function (Builder $query) {
    $query->where('title', 'like', 'foo%');
})->get();
```

# `#` Aggregate Sorgulama
---
## İlgili Modelleri Sayma

Bazen modelleri gerçekten yüklemeden belirli bir ilişki için ilgili modellerin sayısını saymak isteyebilirsiniz. Bunu gerçekleştirmek için `withCount` metodunu kullanabilirsiniz. `withCount` metodu, ortaya çıkan modellere bir `[relation]_count` niteliği yerleştirecektir:

```php
use App\Models\Post;
 
$posts = Post::withCount('comments')->get();
 
foreach ($posts as $post) {
    echo $post->comments_count;
}
```

`withCount` metoduna bir dizi aktararak, birden fazla ilişki için "sayıları" ekleyebilir ve sorgulara ek kısıtlamalar ekleyebilirsiniz:

```php
use Illuminate\Database\Eloquent\Builder;
 
$posts = Post::withCount(['votes', 'comments' => function (Builder $query) {
    $query->where('content', 'like', 'code%');
}])->get();
 
echo $posts[0]->votes_count;
echo $posts[0]->comments_count;
```

Aynı ilişkide birden fazla sayıma izin vererek ilişki sayımı sonucunu takma adla da adlandırabilirsiniz:

```php
use Illuminate\Database\Eloquent\Builder;
 
$posts = Post::withCount([
    'comments',
    'comments as pending_comments_count' => function (Builder $query) {
        $query->where('approved', false);
    },
])->get();
 
echo $posts[0]->comments_count;
echo $posts[0]->pending_comments_count;
```

### Ertelenmiş Sayım Yükleme

`loadCount` metodunu kullanarak, ana model zaten alındıktan sonra bir ilişki sayısı yükleyebilirsiniz:

```php
$book = Book::first();
 
$book->loadCount('genres');
```

Sayım sorgusunda ek sorgu kısıtlamaları ayarlamanız gerekiyorsa, saymak istediğiniz ilişkiler tarafından anahtarlanan bir dizi iletebilirsiniz. Dizi değerleri, sorgu oluşturucu örneğini alan kapanışlar olmalıdır:

```php
$book->loadCount(['reviews' => function (Builder $query) {
    $query->where('rating', 5);
}])
```

### İlişki Sayma ve Özel Select Deyimleri

`withCount` metodunu bir `select` deyimiyle birleştiriyorsanız, `select` metodundan sonra `withCount` öğesini çağırdığınızdan emin olun:

```php
$posts = Post::select(['title', 'body'])
                ->withCount('comments')
                ->get();
```

## Diğer Aggregate Metotları

`withCount` metoduna ek olarak, Eloquent `withMin`, `withMax`, `withAvg`, `withSum` ve `withExists` metodlarını sağlar. Bu metotlar, sonuçta ortaya çıkan modellerinize bir `[relation]_[function]_[column]` niteliği yerleştirecektir:

```php
use App\Models\Post;
 
$posts = Post::withSum('comments', 'votes')->get();
 
foreach ($posts as $post) {
    echo $post->comments_sum_votes;
}
```

Aggregate işlevinin sonucuna başka bir ad kullanarak erişmek isterseniz, kendi takma adınızı belirtebilirsiniz:

```php
$posts = Post::withSum('comments as total_comments', 'votes')->get();
 
foreach ($posts as $post) {
    echo $post->total_comments;
}
```

`loadCount` metodunda olduğu gibi, bu metotların ertelenmiş sürümleri de mevcuttur. Bu ek aggregate işlemleri, zaten alınmış olan Eloquent modelleri üzerinde gerçekleştirilebilir:

```php
$post = Post::first();
 
$post->loadSum('comments', 'votes');
```

Bu aggregate metodlarını bir `select` deyimiyle birleştiriyorsanız, aggregate metodlarını `select` metodundan sonra çağırdığınızdan emin olun:

```php
$posts = Post::select(['title', 'body'])
                ->withExists('comments')
                ->get();
```

## Poliformik İlişkilerde İlgili Modeli Sayma

Bir "morph to" ilişkisini ve bu ilişki tarafından döndürülebilecek çeşitli varlıklar için ilgili model sayılarını istekli olarak yüklemek isterseniz, `morphTo` ilişkisinin `morphWithCount` yöntemiyle birlikte `with` metodunu kullanabilirsiniz.

Bu örnekte, `Photo` ve `Post` modellerinin `ActivityFeed` modelleri oluşturabileceğini varsayalım. `ActivityFeed` modelinin, belirli bir `ActivityFeed` örneği için üst `Photo` veya `Post` modelini almamızı sağlayan `parentable` adlı bir "morph to" ilişkisi tanımladığını varsayacağız. Ayrıca, `Photo` modellerinin "birçok" `Tag` modeline ve `Post` modellerinin de "birçok" `Comment` modeline sahip olduğunu varsayalım.

Şimdi, `ActivityFeed` örneklerini almak ve her `ActivityFeed` örneği için ebeveynlenebilir üst modelleri yüklemek istediğimizi düşünelim. Buna ek olarak, her bir üst fotoğrafla ilişkili etiket sayısını ve her bir üst gönderiyle ilişkili yorum sayısını almak istiyoruz:

```php
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
$activities = ActivityFeed::with([
    'parentable' => function (MorphTo $morphTo) {
        $morphTo->morphWithCount([
            Photo::class => ['tags'],
            Post::class => ['comments'],
        ]);
    }])->get();
```

### Ertelenmiş Sayım Yükleme

Bir dizi `ActivityFeed` modelini zaten aldığımızı ve şimdi aktivite etkinlikleriyle ilişkili çeşitli ebeveynlenebilir modeller için iç içe geçmiş ilişki sayılarını yüklemek istediğimizi varsayalım. Bunu gerçekleştirmek için `loadMorphCount` metodunu kullanabilirsiniz:

```php
$activities = ActivityFeed::with('parentable')->get();
 
$activities->loadMorphCount('parentable', [
    Photo::class => ['tags'],
    Post::class => ['comments'],
]);
```
