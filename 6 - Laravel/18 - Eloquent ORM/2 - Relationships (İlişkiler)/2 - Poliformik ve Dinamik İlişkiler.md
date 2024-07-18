# `#` Poliformik (Polymorphic) İlişkiler
---
Polimorfik ilişki, alt modelin tek bir ilişki kullanarak birden fazla model türüne ait olmasını sağlar. Örneğin, kullanıcıların blog gönderilerini ve videolarını paylaşmasına olanak tanıyan bir uygulama oluşturduğunuzu düşünün. Böyle bir uygulamada, bir `Comment` modeli hem `Post` hem de `Video` modellerine ait olabilir.

## One to One (Polymorphic) - Bire Bir

### Tablo Yapısı

Bire bir polimorfik ilişki tipik bire bir ilişkiye benzer; ancak alt model tek bir ilişki kullanarak birden fazla model türüne ait olabilir. Örneğin, bir `Post` ve bir `User`, bir `Image` modeliyle polimorfik bir ilişkiyi paylaşabilir. Bire bir polimorfik ilişki kullanmak, yazılar ve kullanıcılarla ilişkilendirilebilecek benzersiz resimlerden oluşan tek bir tabloya sahip olmanızı sağlar. İlk olarak tablo yapısını inceleyelim:

```TXT
posts
    id - integer
    name - string
 
users
    id - integer
    name - string
 
images
    id - integer
    url - string
    imageable_id - integer
    imageable_type - string
```

`images` tablosundaki `imageable_id` ve `imageable_type` sütunlarına dikkat edin. `imageable_id` sütunu gönderinin veya kullanıcının ID değerini içerirken, `imageable_type` sütunu ana modelin sınıf adını içerecektir. `imageable_type` sütunu, Eloquent tarafından `imageable` ilişkisine erişirken hangi ana model "türünün" döndürüleceğini belirlemek için kullanılır. Bu durumda, sütun `App\Models\Post` ya da `App\Models\User` içerecektir.

### Model Yapısı

Daha sonra, bu ilişkiyi kurmak için gereken model tanımlarını inceleyelim:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
class Image extends Model
{
    /**
     * Get the parent imageable model (user or post).
     */
    public function imageable(): MorphTo
    {
        return $this->morphTo();
    }
}
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphOne;
 
class Post extends Model
{
    /**
     * Get the post's image.
     */
    public function image(): MorphOne
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphOne;
 
class User extends Model
{
    /**
     * Get the user's image.
     */
    public function image(): MorphOne
    {
        return $this->morphOne(Image::class, 'imageable');
    }
}
```

### İlişkinin Kullanımı

Veritabanı tablonuz ve modelleriniz tanımlandıktan sonra, modelleriniz aracılığıyla ilişkilere erişebilirsiniz. Örneğin, bir yazıya ait resmi almak için `image` dinamik ilişki özelliğine erişebiliriz:

```php
use App\Models\Post;
 
$post = Post::find(1);
 
$image = $post->image;
```

`morphTo` çağrısını gerçekleştiren metodun adına erişerek polimorfik modelin üst öğesini alabilirsiniz. Bu durumda, bu Image modelindeki `imageable` metodudur. Dolayısıyla, bu metoda dinamik bir ilişki özelliği olarak erişeceğiz:

```php
use App\Models\Image;
 
$image = Image::find(1);
 
$imageable = $image->imageable;
```

`Image` modelindeki `imageable` ilişkisi, resmin hangi model türüne ait olduğuna bağlı olarak bir `Post` veya `User` örneği döndürür.

### Anahtar Düzenleri

Gerekirse, polimorfik alt modeliniz tarafından kullanılan "id" ve "type" sütunlarının adını belirtebilirsiniz. Bunu yaparsanız, `morphTo` metoduna ilk argüman olarak her zaman ilişkinin adını ilettiğinizden emin olun. Tipik olarak, bu değer metod adıyla eşleşmelidir, bu nedenle PHP'nin `__FUNCTION__` sabitini kullanabilirsiniz:

```php
/**
 * Get the model that the image belongs to.
 */
public function imageable(): MorphTo
{
    return $this->morphTo(__FUNCTION__, 'imageable_type', 'imageable_id');
}
```

## One to Many (Polymorphic) - Birden Çoka

### Tablo Yapısı

Birden çoğa polimorfik ilişki tipik bire çok ilişkiye benzer; ancak alt model tek bir ilişki kullanarak birden fazla model türüne ait olabilir. Örneğin, uygulamanızın kullanıcılarının gönderiler ve videolar hakkında "yorum" yapabildiğini düşünün. Çok biçimli ilişkileri kullanarak, hem gönderiler hem de videolar için yorumları içeren tek bir `comments` tablosu kullanabilirsiniz. Öncelikle, bu ilişkiyi oluşturmak için gereken tablo yapısını inceleyelim:

```txt
posts
    id - integer
    title - string
    body - text
 
videos
    id - integer
    title - string
    url - string
 
comments
    id - integer
    body - text
    commentable_id - integer
    commentable_type - string
```

### Model Yapısı

Daha sonra, bu ilişkiyi kurmak için gereken model tanımlarını inceleyelim:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
class Comment extends Model
{
    /**
     * Get the parent commentable model (post or video).
     */
    public function commentable(): MorphTo
    {
        return $this->morphTo();
    }
}
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphMany;
 
class Post extends Model
{
    /**
     * Get all of the post's comments.
     */
    public function comments(): MorphMany
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphMany;
 
class Video extends Model
{
    /**
     * Get all of the video's comments.
     */
    public function comments(): MorphMany
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

### İlişkinin Kullanımı

Veritabanı tablonuz ve modelleriniz tanımlandıktan sonra, modelinizin dinamik ilişki özellikleri aracılığıyla ilişkilere erişebilirsiniz. Örneğin, bir gönderinin tüm yorumlarına erişmek için `comments` dinamik özelliğini kullanabiliriz:

```php
use App\Models\Post;
 
$post = Post::find(1);
 
foreach ($post->comments as $comment) {
    // ...
}
```

Ayrıca, `morphTo` çağrısını gerçekleştiren metodun adına erişerek çok biçimli bir alt modelin üst modelini de alabilirsiniz. Bu durumda, bu `Comment` modelindeki `commentable` metodudur. Dolayısıyla, yorumun üst modeline erişmek için bu metoda dinamik bir ilişki özelliği olarak erişeceğiz:

```php
use App\Models\Comment;
 
$comment = Comment::find(1);
 
$commentable = $comment->commentable;
```

`Comment` modelindeki `commentable` ilişkisi, yorumun üst modelinin hangi tür model olduğuna bağlı olarak bir `Post` veya `Video` örneği döndürür.

## One of Many (Polymorphic) - Çoğundan Biri

Bazen bir modelin birçok ilgili modeli olabilir, ancak ilişkinin "en son" veya "en eski" ilgili modelini kolayca almak istersiniz. Örneğin, bir `User` modeli birçok `Image` modeliyle ilişkili olabilir, ancak kullanıcının yüklediği en son görüntüyle etkileşim kurmak için uygun bir yol tanımlamak istersiniz. Bunu, `ofMany` yöntemleriyle birlikte `morphOne` ilişki türünü kullanarak gerçekleştirebilirsiniz:

```php
/**
 * Get the user's most recent image.
 */
public function latestImage(): MorphOne
{
    return $this->morphOne(Image::class, 'imageable')->latestOfMany();
}
```

Benzer şekilde, bir ilişkinin "en eski" veya ilk ilgili modelini almak için bir metot tanımlayabilirsiniz:

```php
/**
 * Get the user's oldest image.
 */
public function oldestImage(): MorphOne
{
    return $this->morphOne(Image::class, 'imageable')->oldestOfMany();
}
```

Varsayılan olarak, `latestOfMany` ve `oldestOfMany` metodları, sıralanabilir olması gereken modelin birincil anahtarına dayalı olarak en son veya en eski ilgili modeli alır. Ancak, bazen farklı bir sıralama ölçütü kullanarak daha büyük bir ilişkiden tek bir model almak isteyebilirsiniz.

Örneğin, `ofMany` metodunu kullanarak kullanıcının en çok "beğenilen" resmini alabilirsiniz. `ofMany` metodu, ilk bağımsız değişkeni olarak sıralanabilir sütunu ve ilgili model için sorgulama yaparken hangi toplama işlevinin (`min` veya `max`) uygulanacağını kabul eder:

```php
/**
 * Get the user's most popular image.
 */
public function bestImage(): MorphOne
{
    return $this->morphOne(Image::class, 'imageable')->ofMany('likes', 'max');
}
```

## Many to Many (Polymorphic) - Çoktan Çoka

### Tablo Yapısı

Çoktan çoğa polimorfik ilişkiler "morph one" ve "morph many" ilişkilerinden biraz daha karmaşıktır. Örneğin, bir `Post` modeli ve `Video` modeli bir `Tag` modeliyle çok biçimli bir ilişkiyi paylaşabilir. Bu durumda çoktan çoğa çok biçimli bir ilişki kullanmak, uygulamanızın gönderiler veya videolarla ilişkilendirilebilecek benzersiz etiketlerden oluşan tek bir tabloya sahip olmasını sağlayacaktır. Öncelikle, bu ilişkiyi oluşturmak için gereken tablo yapısını inceleyelim:

```txt
posts
    id - integer
    name - string
 
videos
    id - integer
    name - string
 
tags
    id - integer
    name - string
 
taggables
    tag_id - integer
    taggable_id - integer
    taggable_type - string
```

### Model Yapısı

Ardından, modeller üzerindeki ilişkileri tanımlamaya hazırız. `Post` ve `Video` modellerinin her ikisi de temel Eloquent model sınıfı tarafından sağlanan `morphToMany` metodunu çağıran bir `tags` metodu içerecektir.

`morphToMany` metodu, ilgili modelin adının yanı sıra "ilişki adını" da kabul eder. Ara tablo ismimize ve içerdiği anahtarlara atadığımız isme dayanarak, ilişkiyi "`taggable`" olarak adlandıracağız:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphToMany;
 
class Post extends Model
{
    /**
     * Get all of the tags for the post.
     */
    public function tags(): MorphToMany
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}
```

### İlişkinin Tersinin Tanımlanması

Ardından, `Tag` modelinde, olası üst modellerinin her biri için bir metot tanımlamalısınız. Bu örnekte, bir `posts` metodu ve bir `videos` metodu tanımlayacağız. Bu metodların her ikisi de `morphedByMany` metodunun sonucunu döndürmelidir.

`morphedByMany` metodu, ilgili modelin adının yanı sıra "ilişki adını" da kabul eder. Ara tablo ismimize ve içerdiği anahtarlara atadığımız isme dayanarak, ilişkiyi "`taggable`" olarak adlandıracağız:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphToMany;
 
class Tag extends Model
{
    /**
     * Get all of the posts that are assigned this tag.
     */
    public function posts(): MorphToMany
    {
        return $this->morphedByMany(Post::class, 'taggable');
    }
 
    /**
     * Get all of the videos that are assigned this tag.
     */
    public function videos(): MorphToMany
    {
        return $this->morphedByMany(Video::class, 'taggable');
    }
}
```

### İlişkinin Kullanımı

Veritabanı tablonuz ve modelleriniz tanımlandıktan sonra, modelleriniz aracılığıyla ilişkilere erişebilirsiniz. Örneğin, bir gönderinin tüm etiketlerine erişmek için `tags` dinamik ilişki özelliğini kullanabilirsiniz:

```php
use App\Models\Post;
 
$post = Post::find(1);
 
foreach ($post->tags as $tag) {
    // ...
}
```

`morphedByMany` çağrısını gerçekleştiren metodun adına erişerek çok biçimli bir ilişkinin üst öğesini çok biçimli alt modelden alabilirsiniz. Bu durumda bu, `Tag` modelindeki `posts` veya `videos` metodlarıdır:

```php
use App\Models\Tag;
 
$tag = Tag::find(1);
 
foreach ($tag->posts as $post) {
    // ...
}
 
foreach ($tag->videos as $video) {
    // ...
}
```

## Özel Poliformik Tipler

Varsayılan olarak, Laravel ilgili modelin "tipini" saklamak için tam nitelikli sınıf adını kullanacaktır. Örneğin, bir `Comment` modelinin bir `Post` veya `Video` modeline ait olabileceği yukarıdaki bire-çok ilişki örneği göz önüne alındığında, varsayılan `commentable_type` sırasıyla `App\Models\Post` veya `App\Models\Video` olacaktır. Ancak, bu değerleri uygulamanızın iç yapısından ayırmak isteyebilirsiniz.

Örneğin, model adlarını "tip" olarak kullanmak yerine, `post` ve `video` gibi basit dizeler kullanabiliriz. Bu şekilde, modeller yeniden adlandırılsa bile veritabanımızdaki polimorfik "tip" sütun değerleri geçerli kalacaktır:

```php
use Illuminate\Database\Eloquent\Relations\Relation;
 
Relation::enforceMorphMap([
    'post' => 'App\Models\Post',
    'video' => 'App\Models\Video',
]);
```

`EnforceMorphMap` metodunu `App\Providers\AppServiceProvider` sınıfınızın `boot` metodunda çağırabilir veya isterseniz ayrı bir service provider oluşturabilirsiniz.

Belirli bir modelin morph takma adını çalışma zamanında modelin `getMorphClass` metodunu kullanarak belirleyebilirsiniz. Tersine, `Relation::getMorphedModel` metodunu kullanarak bir morph takma adıyla ilişkili tam nitelikli sınıf adını belirleyebilirsiniz:

```php
use Illuminate\Database\Eloquent\Relations\Relation;
 
$alias = $post->getMorphClass();
 
$class = Relation::getMorphedModel($alias);
```

> Mevcut uygulamanıza bir "morph map" eklerken, veritabanınızda hala tam nitelikli bir sınıf içeren her morphable `*_type` sütun değerinin "map" adına dönüştürülmesi gerekecektir.

# `#` Dinamik İlişkiler
---
Çalışma zamanında Eloquent modelleri arasındaki ilişkileri tanımlamak için `resolveRelationUsing` metodunu kullanabilirsiniz. Normal uygulama geliştirme için tipik olarak tavsiye edilmese de, Laravel paketleri geliştirirken bu bazen yararlı olabilir.

`resolveRelationUsing` metodu, ilk bağımsız değişkeni olarak istenen ilişki adını kabul eder. Metoda aktarılan ikinci bağımsız değişken, model örneğini kabul eden ve geçerli bir Eloquent ilişki tanımı döndüren bir closure olmalıdır. Tipik olarak, dinamik ilişkileri bir service provider'in `boot` metodu içinde yapılandırmanız gerekir:

```php
use App\Models\Order;
use App\Models\Customer;
 
Order::resolveRelationUsing('customer', function (Order $orderModel) {
    return $orderModel->belongsTo(Customer::class, 'customer_id');
});
```

Dinamik ilişkileri tanımlarken, Eloquent ilişki metodlarına her zaman açık anahtar adı bağımsız değişkenleri sağlayın.
