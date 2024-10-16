# `#` Giriş
---
Veritabanı tabloları genellikle birbirleriyle ilişkilidir. Örneğin, bir blog gönderisinin birçok yorumu olabilir veya bir sipariş, siparişi veren kullanıcıyla ilişkili olabilir. Eloquent bu ilişkileri yönetmeyi ve bunlarla çalışmayı kolaylaştırır ve çeşitli yaygın ilişkileri destekler:

- One To One (Bire Bir)
- One To Many (Birden Çoka)
- Many To Many (Çoktan Çoka)
- Has One Through (Bir Yolu Var)
- Has Many Through (Birçok Yolu Var)
- One To One (Polymorphic) - (Bire Bir)
- One To Many (Polymorphic) - (Birden Çoka)
- Many To Many (Polymorphic) - (Çoktan Çoka)

# `#` İlişki Tanımlama
---
Eloquent ilişkileri, Eloquent model sınıflarınızda metotlar olarak tanımlanır. İlişkiler aynı zamanda güçlü sorgu oluşturucular olarak da hizmet verdiğinden, ilişkileri metotlar olarak tanımlamak güçlü metot zincirleme ve sorgulama yetenekleri sağlar. Örneğin, bu `posts` ilişkisi üzerinde ek sorgu kısıtlamalarını zincirleyebiliriz:

```php
$user->posts()->where('active', 1)->get();
```

Ancak, ilişkileri kullanmaya çok derinlemesine dalmadan önce, Eloquent tarafından desteklenen her bir ilişki türünü nasıl tanımlayacağımızı öğrenelim.

## One to One - Bire Bir
---
Bire bir ilişki, çok temel bir veritabanı ilişkisi türüdür. Örneğin, bir `User` modeli bir `Phone` modeliyle ilişkilendirilebilir. Bu ilişkiyi tanımlamak için `User` modeline bir `phone` metodu yerleştireceğiz. `phone` metodu `hasOne` metodunu çağırmalı ve sonucunu döndürmelidir. `hasOne` metodu, modelinizin `Illuminate\Database\Eloquent\Model` temel sınıfı aracılığıyla kullanılabilir:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasOne;
 
class User extends Model
{
    /**
     * Get the phone associated with the user.
     */
    public function phone(): HasOne
    {
        return $this->hasOne(Phone::class);
    }
}
```

`hasOne` metoduna aktarılan ilk argüman, ilgili model sınıfının adıdır. İlişki tanımlandıktan sonra, Eloquent'in dinamik özelliklerini kullanarak ilgili kaydı alabiliriz. Dinamik özellikler, ilişki metodlarına sanki model üzerinde tanımlanmış özelliklermiş gibi erişmenizi sağlar:

```php
$phone = User::find(1)->phone;
```

Eloquent, ilişkinin yabancı antahtarını üst model adına göre belirler. Bu durumda, Phone modelinin otomatik olarak `user_id` yabancı anahtarına sahip olduğu varsayılır. Bu kuralı geçersiz kılmak isterseniz `hasOne` metoduna ikinci bir bağımsız değişken iletebilirsiniz:

```php
return $this->hasOne(Phone::class, 'foreign_key');
```

Ayrıca, Eloquent yabancı anahtarın üst öğenin birincil anahtar sütunuyla eşleşen bir değere sahip olması gerektiğini varsayar. Başka bir deyişle, Eloquent, `Phone` kaydının `user_id` sütununda kullanıcının `id` sütununun değerini arayacaktır. İlişkinin `id` veya modelinizin `$primaryKey` özelliği dışında bir birincil anahtar değeri kullanmasını istiyorsanız `hasOne` metoduna üçüncü bir bağımsız değişken iletebilirsiniz:

```php
return $this->hasOne(Phone::class, 'foreign_key', 'local_key');
```

### İlişkinin Tersinin Tanımlanması

Böylece, `Phone` modeline `User` modelimizden erişebiliriz. Ardından, `Phone` modeli üzerinde telefonun sahibi olan kullanıcıya erişmemizi sağlayacak bir ilişki tanımlayalım. `belongsTo` metodunu kullanarak `hasOne` ilişkisinin tersini tanımlayabiliriz:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
 
class Phone extends Model
{
    /**
     * Get the user that owns the phone.
     */
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }
}
```

`user` metodu çağrıldığında, Eloquent, `Phone` modelindeki `user_id` sütunuyla eşleşen bir kimliğe sahip bir `User` modeli bulmaya çalışacaktır.

Eloquent, ilişki yönteminin adını inceleyerek ve yöntem adının sonuna `_id` ekleyerek yabancı anahtar adını belirler. Dolayısıyla, bu durumda Eloquent, `Phone` modelinin bir `user_id` sütununa sahip olduğunu varsayar. Ancak, `Phone` modelindeki yabancı anahtar `user_id` değilse, `belongsTo` metoduna ikinci bağımsız değişken olarak özel bir anahtar adı iletebilirsiniz:

```php
/**
 * Get the user that owns the phone.
 */
public function user(): BelongsTo
{
    return $this->belongsTo(User::class, 'foreign_key');
}
```

Üst model birincil anahtar olarak `id` kullanmıyorsa veya ilişkili modeli farklı bir sütun kullanarak bulmak istiyorsanız, `belongsTo` metoduna üst tablonun özel anahtarını belirten üçüncü bir bağımsız değişken iletebilirsiniz:

```php
/**
 * Get the user that owns the phone.
 */
public function user(): BelongsTo
{
    return $this->belongsTo(User::class, 'foreign_key', 'owner_key');
}
```

## One to Many - Birden Çoka

Birden çoğa ilişki, tek bir modelin bir veya daha fazla alt modelin ebeveyni olduğu ilişkileri tanımlamak için kullanılır. Örneğin, bir blog gönderisi sonsuz sayıda yoruma sahip olabilir. Diğer tüm Eloquent ilişkileri gibi, bire-çok ilişkileri de Eloquent modelinizde bir metot tanımlanarak tanımlanır:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasMany;
 
class Post extends Model
{
    /**
     * Get the comments for the blog post.
     */
    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }
}
```

Eloquent'in `Comment` modeli için uygun yabancı anahtar sütununu otomatik olarak belirleyeceğini unutmayın. Geleneksel olarak, Eloquent üst modelin "snake case" adını alacak ve `_id` ile sonlandıracaktır. Dolayısıyla, bu örnekte Eloquent, `Comment` modelindeki yabancı anahtar sütununun `post_id` olduğunu varsayacaktır.

İlişki metodu tanımlandıktan sonra, `comments` özelliğine erişerek ilgili yorumlar koleksiyonuna erişebiliriz. Unutmayın, Eloquent "dinamik ilişki özellikleri" sağladığından, ilişki metodlarına model üzerinde özellik olarak tanımlanmış gibi erişebiliriz:

```php
use App\Models\Post;
 
$comments = Post::find(1)->comments;
 
foreach ($comments as $comment) {
    // ...
}
```

Tüm ilişkiler aynı zamanda sorgu oluşturucu görevi gördüğünden, `comments` metodunu çağırarak ve sorguya koşulları zincirlemeye devam ederek ilişki sorgusuna başka kısıtlamalar ekleyebilirsiniz:

```php
$comment = Post::find(1)->comments()
                    ->where('title', 'foo')
                    ->first();
```

`hasOne` metodunda olduğu gibi, `hasMany` metoduna ek bağımsız değişkenler geçerek yabancı ve yerel anahtarları da geçersiz kılabilirsiniz:

```php
return $this->hasMany(Comment::class, 'foreign_key');
 
return $this->hasMany(Comment::class, 'foreign_key', 'local_key');
```

### İlişkinin Tersinin Tanımlanması

Artık bir gönderinin tüm yorumlarına erişebildiğimize göre, bir yorumun ana gönderisine erişmesine izin vermek için bir ilişki tanımlayalım. Bir `hasMany` ilişkisinin tersini tanımlamak için, alt modelde `belongsTo` metodunu çağıran bir ilişki metodu tanımlayın:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
 
class Comment extends Model
{
    /**
     * Get the post that owns the comment.
     */
    public function post(): BelongsTo
    {
        return $this->belongsTo(Post::class);
    }
}
```

İlişki tanımlandıktan sonra, "dinamik ilişki özelliği" gönderisine erişerek bir yorumun üst gönderisini alabiliriz:

```php
use App\Models\Comment;
 
$comment = Comment::find(1);
 
return $comment->post->title;
```

Yukarıdaki örnekte Eloquent, `Comment` modelindeki `post_id` sütunuyla eşleşen bir `id`'ye sahip bir `Post` modeli bulmaya çalışacaktır.

Eloquent varsayılan yabancı anahtar adını, ilişki metodunun adını inceleyerek ve metod adını `_` ile sonlandırıp ardından üst modelin birincil anahtar sütununun adını ekleyerek belirler. Dolayısıyla, bu örnekte Eloquent, `Post` modelinin `comments` tablosundaki yabancı anahtarının `post_id` olduğunu varsayacaktır.

Ancak, ilişkiniz için yabancı anahtar bu kurallara uymuyorsa, `belongsTo` metoduna ikinci bağımsız değişken olarak özel bir yabancı anahtar adı iletebilirsiniz:

```php
/**
 * Get the post that owns the comment.
 */
public function post(): BelongsTo
{
    return $this->belongsTo(Post::class, 'foreign_key');
}
```

Üst modeliniz birincil anahtar olarak `id` kullanmıyorsa veya ilişkili modeli farklı bir sütun kullanarak bulmak istiyorsanız, `belongsTo` metoduna üst tablonuzun özel anahtarını belirten üçüncü bir bağımsız değişken iletebilirsiniz:

```php
/**
 * Get the post that owns the comment.
 */
public function post(): BelongsTo
{
    return $this->belongsTo(Post::class, 'foreign_key', 'owner_key');
}
```

### Varsayılan Modeller

`belongsTo`, `hasOne`, `hasOneThrough` ve `morphOne` ilişkileri, verilen ilişkinin `null` olması durumunda döndürülecek varsayılan bir model tanımlamanıza olanak tanır. Bu model genellikle Null Object modeli olarak adlandırılır ve kodunuzdaki koşullu kontrolleri kaldırmanıza yardımcı olabilir. Aşağıdaki örnekte, `user` ilişkisi, `Post` modeline herhangi bir kullanıcı eklenmemişse boş bir `App\Models\User` modeli döndürecektir:

```php
/**
 * Get the author of the post.
 */
public function user(): BelongsTo
{
    return $this->belongsTo(User::class)->withDefault();
}
```

Varsayılan modeli niteliklerle doldurmak için, `withDefault` metoduna bir dizi veya closure aktarabilirsiniz:

```php
/**
 * Get the author of the post.
 */
public function user(): BelongsTo
{
    return $this->belongsTo(User::class)->withDefault([
        'name' => 'Guest Author',
    ]);
}
 
/**
 * Get the author of the post.
 */
public function user(): BelongsTo
{
    return $this->belongsTo(User::class)->withDefault(function (User $user, Post $post) {
        $user->name = 'Guest Author';
    });
}
```

### İlişkilere Ait Sorgulama

Bir "belongs to" ilişkisinin alt öğelerini sorgularken, ilgili Eloquent modellerini almak için `where` cümlesini manuel olarak oluşturabilirsiniz:

```php
use App\Models\Post;
 
$posts = Post::where('user_id', $user->id)->get();
```

Ancak, verilen model için uygun ilişkiyi ve yabancı anahtarı otomatik olarak belirleyecek olan `whereBelongsTo` metodunu kullanmayı daha uygun bulabilirsiniz:

```php
$posts = Post::whereBelongsTo($user)->get();
```

`whereBelongsTo` metoduna bir koleksiyon örneği de sağlayabilirsiniz. Bunu yaparken, Laravel koleksiyon içindeki herhangi bir üst modele ait olan modelleri alacaktır:

```php
$users = User::where('vip', true)->get();
 
$posts = Post::whereBelongsTo($users)->get();
```

Varsayılan olarak, Laravel verilen modelle ilişkili ilişkiyi modelin sınıf adına göre belirleyecektir; ancak, ilişki adını `whereBelongsTo` metoduna ikinci argüman olarak sağlayarak manuel olarak belirtebilirsiniz:

```php
$posts = Post::whereBelongsTo($user, 'author')->get();
```

## Has One of Many - Çoğundan Biri

Bazen bir modelin birçok ilgili modeli olabilir, ancak ilişkinin "en son" veya "en eski" ilgili modelini kolayca almak istersiniz. Örneğin, bir `User` modeli birçok `Order` modeliyle ilişkili olabilir, ancak kullanıcının verdiği en son siparişle etkileşim kurmak için uygun bir yol tanımlamak istersiniz. Bunu, `hasOne` ilişki türünü `ofMany` metodlarıyla birlikte kullanarak gerçekleştirebilirsiniz:

```php
/**
 * Get the user's most recent order.
 */
public function latestOrder(): HasOne
{
    return $this->hasOne(Order::class)->latestOfMany();
}
```

Benzer şekilde, bir ilişkinin "en eski" veya ilk ilgili modelini almak için bir metot tanımlayabilirsiniz:

```php
/**
 * Get the user's oldest order.
 */
public function oldestOrder(): HasOne
{
    return $this->hasOne(Order::class)->oldestOfMany();
}
```

Varsayılan olarak, `latestOfMany` ve `oldestOfMany` metodları, sıralanabilir olması gereken modelin birincil anahtarına dayalı olarak en son veya en eski ilgili modeli alır. Ancak, bazen farklı bir sıralama ölçütü kullanarak daha büyük bir ilişkiden tek bir model almak isteyebilirsiniz.

Örneğin, `ofMany` metodunu kullanarak kullanıcının en pahalı siparişini alabilirsiniz. `ofMany` metodu, ilk bağımsız değişkeni olarak sıralanabilir sütunu ve ilgili model için sorgulama yaparken hangi aggreagate işlevinin (`min` veya `max`) uygulanacağını kabul eder:

```php
/**
 * Get the user's largest order.
 */
public function largestOrder(): HasOne
{
    return $this->hasOne(Order::class)->ofMany('price', 'max');
}
```

### "Çok" İlişkiye Tek İlişkiye Dönüştürme

Genellikle, `latestOfMany`, `oldestOfMany` veya `ofMany` metodlarını kullanarak tek bir model alırken, aynı model için tanımlanmış bir "has many" ilişkiniz zaten vardır. Kolaylık sağlamak için Laravel, ilişki üzerinde `one` metodunu çağırarak bu ilişkiyi kolayca "has one" ilişkisine dönüştürmenize izin verir:

```php
/**
 * Get the user's orders.
 */
public function orders(): HasMany
{
    return $this->hasMany(Order::class);
}
 
/**
 * Get the user's largest order.
 */
public function largestOrder(): HasOne
{
    return $this->orders()->one()->ofMany('price', 'max');
}
```

### Gelişmiş Has One of Many (Çoğundan Biri) İlişkileri

Daha gelişmiş "çoğundan biri" ilişkileri oluşturmak mümkündür. Örneğin, bir `Product` modeli, yeni fiyatlandırma yayınlandıktan sonra bile sistemde tutulan birçok ilişkili `Price` modeline sahip olabilir. Buna ek olarak, ürün için yeni fiyatlandırma verileri, bir `published_at` sütunu aracılığıyla gelecekteki bir tarihte yürürlüğe girmek üzere önceden yayınlanabilir.

Özetle, yayınlanma tarihi gelecekte olmayan en son yayınlanan fiyatı almamız gerekiyor. Ayrıca, iki fiyatın yayınlanma tarihi aynıysa, en büyük ID'ye sahip fiyatı tercih edeceğiz. Bunu başarmak için, `ofMany` metoduna en son fiyatı belirleyen sıralanabilir sütunları içeren bir dizi aktarmalıyız. Buna ek olarak, `ofMany` metoduna ikinci argüman olarak bir closure sağlanacaktır. Bu closure, ilişki sorgusuna ek yayın tarihi kısıtlamaları eklemekten sorumlu olacaktır:

```php
/**
 * Get the current pricing for the product.
 */
public function currentPricing(): HasOne
{
    return $this->hasOne(Price::class)->ofMany([
        'published_at' => 'max',
        'id' => 'max',
    ], function (Builder $query) {
        $query->where('published_at', '<', now());
    });
}
```

## Has One Through - Bir Yolu Var

"has-one-through" ilişkisi başka bir modelle bire bir ilişkiyi tanımlar. Ancak bu ilişki, bildiren modelin üçüncü bir model üzerinden ilerleyerek başka bir modelin bir örneği ile eşleştirilebileceğini gösterir.

Örneğin, bir araç tamir atölyesi uygulamasında, her `Mechanic` modeli bir `Car` modeliyle ve her `Car` modeli bir `Owner` modeliyle ilişkilendirilebilir. Tamirci ve araç sahibinin veritabanı içinde doğrudan bir ilişkisi olmasa da, tamirci araç sahibine `Car` modeli üzerinden erişebilir. Bu ilişkiyi tanımlamak için gerekli tablolara bakalım:

```plain
mechanics
    id - integer
    name - string
 
cars
    id - integer
    model - string
    mechanic_id - integer
 
owners
    id - integer
    name - string
    car_id - integer
```

İlişki için tablo yapısını incelediğimize göre, şimdi ilişkiyi `Mechanic` modeli üzerinde tanımlayalım:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasOneThrough;
 
class Mechanic extends Model
{
    /**
     * Get the car's owner.
     */
    public function carOwner(): HasOneThrough
    {
        return $this->hasOneThrough(Owner::class, Car::class);
    }
}
```

`hasOneThrough` metoduna aktarılan ilk bağımsız değişken, erişmek istediğimiz nihai modelin adıdır; ikinci bağımsız değişken ise ara modelin adıdır.

Veya ilgili ilişkiler ilişkiye dahil olan tüm modellerde zaten tanımlanmışsa, `through` metodunu çağırarak ve bu ilişkilerin adlarını sağlayarak akıcı bir şekilde bir "has-one-through" ilişkisi tanımlayabilirsiniz. Örneğin, `Mechanic` modelinin bir `cars` ilişkisi ve `Car` modelinin bir `owner` ilişkisi varsa, `mechanic` ve `owner`'ı birbirine bağlayan bir "has-one-through" ilişkisini şu şekilde tanımlayabilirsiniz:

```php
// String based syntax...
return $this->through('cars')->has('owner');
 
// Dynamic syntax...
return $this->throughCars()->hasOwner();
```

### Anahtar Düzenleri

İlişkinin sorguları gerçekleştirilirken tipik Eloquent yabancı anahtar kuralları kullanılacaktır. İlişkinin anahtarlarını özelleştirmek isterseniz, bunları `hasOneThrough` metoduna üçüncü ve dördüncü bağımsız değişkenler olarak aktarabilirsiniz. Üçüncü bağımsız değişken, ara modeldeki yabancı anahtarın adıdır. Dördüncü bağımsız değişken, son modeldeki yabancı anahtarın adıdır. Beşinci bağımsız değişken yerel anahtardır, altıncı bağımsız değişken ise ara modelin yerel anahtarıdır:

```php
class Mechanic extends Model
{
    /**
     * Get the car's owner.
     */
    public function carOwner(): HasOneThrough
    {
        return $this->hasOneThrough(
            Owner::class,
            Car::class,
            'mechanic_id', // Foreign key on the cars table...
            'car_id', // Foreign key on the owners table...
            'id', // Local key on the mechanics table...
            'id' // Local key on the cars table...
        );
    }
}
```

Ya da daha önce tartışıldığı gibi, ilgili ilişkiler ilişkiye dahil olan tüm modellerde zaten tanımlanmışsa, `through` metodunu çağırarak ve bu ilişkilerin adlarını sağlayarak akıcı bir şekilde bir "has-one-through" ilişkisi tanımlayabilirsiniz. Bu yaklaşım, mevcut ilişkilerde zaten tanımlanmış olan anahtar kuralların yeniden kullanılması avantajını sunar:

```php
// String based syntax...
return $this->through('cars')->has('owner');
 
// Dynamic syntax...
return $this->throughCars()->hasOwner();
```

## Has Many Through - Birçok Yolu Var

"has-many-through" ilişkisi, bir ara ilişki aracılığıyla uzak ilişkilere erişmek için uygun bir yol sağlar. Örneğin, Laravel Vapor gibi bir dağıtım platformu oluşturduğumuzu varsayalım. Bir `Project` modeli, bir ara `Environmet` modeli aracılığıyla birçok `Deployment` modeline erişebilir. Bu örneği kullanarak, belirli bir proje için tüm dağıtımları kolayca toplayabilirsiniz. Bu ilişkiyi tanımlamak için gereken tablolara bakalım:

```plain
projects
    id - integer
    name - string
 
environments
    id - integer
    project_id - integer
    name - string
 
deployments
    id - integer
    environment_id - integer
    commit_hash - string
```

İlişki için tablo yapısını incelediğimize göre, şimdi ilişkiyi `Project` modeli üzerinde tanımlayalım:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\HasManyThrough;
 
class Project extends Model
{
    /**
     * Get all of the deployments for the project.
     */
    public function deployments(): HasManyThrough
    {
        return $this->hasManyThrough(Deployment::class, Environment::class);
    }
}
```

`hasManyThrough` metoduna aktarılan ilk bağımsız değişken, erişmek istediğimiz son modelin adıdır; ikinci bağımsız değişken ise ara modelin adıdır.

Veya ilgili ilişkiler ilişkiye dahil olan tüm modellerde zaten tanımlanmışsa, `through` metodunu çağırarak ve bu ilişkilerin adlarını sağlayarak akıcı bir şekilde bir "has-many-through" ilişkisi tanımlayabilirsiniz. Örneğin, `Project` modeli bir `environments` ilişkisine ve `Environment` modeli bir `deployments` ilişkisine sahipse, projeyi ve deployments'ı birbirine bağlayan bir "has-many-through" ilişkisini şu şekilde tanımlayabilirsiniz:

```php
// String based syntax...
return $this->through('environments')->has('deployments');
 
// Dynamic syntax...
return $this->throughEnvironments()->hasDeployments();
```

`Deployment` modelinin tablosu bir `project_id` sütunu içermese de `hasManyThrough` ilişkisi `$project->deployments` aracılığıyla bir projenin dağıtımlarına erişim sağlar. Bu modelleri almak için Eloquent, ara `Environment` modelinin tablosundaki `project_id` sütununu inceler. İlgili ortam kimliklerini bulduktan sonra, bunlar `Deployment` modelinin tablosunu sorgulamak için kullanılır.

### Anahtar Düzenleri

İlişkinin sorguları gerçekleştirilirken tipik Eloquent yabancı anahtar kuralları kullanılacaktır. İlişkinin anahtarlarını özelleştirmek isterseniz, bunları `hasManyThrough` metoduna üçüncü ve dördüncü bağımsız değişkenler olarak aktarabilirsiniz. Üçüncü bağımsız değişken, ara modeldeki yabancı anahtarın adıdır. Dördüncü bağımsız değişken, son modeldeki yabancı anahtarın adıdır. Beşinci bağımsız değişken yerel anahtardır, altıncı bağımsız değişken ise ara modelin yerel anahtarıdır:

```php
class Project extends Model
{
    public function deployments(): HasManyThrough
    {
        return $this->hasManyThrough(
            Deployment::class,
            Environment::class,
            'project_id', // Foreign key on the environments table...
            'environment_id', // Foreign key on the deployments table...
            'id', // Local key on the projects table...
            'id' // Local key on the environments table...
        );
    }
}
```

Ya da daha önce tartışıldığı gibi, ilgili ilişkiler ilişkiye dahil olan tüm modellerde zaten tanımlanmışsa, `through` metodunu çağırarak ve bu ilişkilerin adlarını sağlayarak bir "has-many-through" ilişkisini akıcı bir şekilde tanımlayabilirsiniz. Bu yaklaşım, mevcut ilişkilerde zaten tanımlanmış olan anahtar kuralların yeniden kullanılması avantajını sunar:

```php
// String based syntax...
return $this->through('environments')->has('deployments');
 
// Dynamic syntax...
return $this->throughEnvironments()->hasDeployments();
```

# `#` Many to Many - Çoktan Çoka İlişkiler
---
Çoktan çoka ilişkiler `hasOne` ve `hasMany` ilişkilerinden biraz daha karmaşıktır. Çoktan çoka ilişkinin bir örneği, birçok role sahip olan ve bu rolleri uygulamadaki diğer kullanıcılar tarafından da paylaşılan bir kullanıcıdır. Örneğin, bir kullanıcıya "Yazar" ve "Editör" rolleri atanmış olabilir; ancak bu roller başka kullanıcılara da atanmış olabilir. Dolayısıyla, bir kullanıcının birçok rolü ve bir rolün de birçok kullanıcısı vardır.

### Tablo Yapısı
---
Bu ilişkiyi tanımlamak için üç veritabanı tablosuna ihtiyaç vardır: `users`, `roles` ve `role_user`. `role_user` tablosu, ilgili model adlarının alfabetik sırasından türetilir ve `user_id` ve `role_id` sütunlarını içerir. Bu tablo, kullanıcıları ve rolleri birbirine bağlayan bir ara tablo olarak kullanılır.

Unutmayın, bir rol birçok kullanıcıya ait olabileceğinden, `roles` tablosuna basitçe bir `user_id` sütunu yerleştiremeyiz. Bu, bir rolün yalnızca tek bir kullanıcıya ait olabileceği anlamına gelir. Rollerin birden fazla kullanıcıya atanmasını desteklemek için `role_user` tablosuna ihtiyaç vardır. İlişkinin tablo yapısını şu şekilde özetleyebiliriz:

```plain
users
    id - integer
    name - string
 
roles
    id - integer
    name - string
 
role_user
    user_id - integer
    role_id - integer
```

### Model Yapısı

Çoktan çoka ilişkiler, `belongsToMany` metodunun sonucunu döndüren bir metod yazılarak tanımlanır. `belongsToMany` metodu, uygulamanızın tüm Eloquent modelleri tarafından kullanılan `Illuminate\Database\Eloquent\Model` temel sınıfı tarafından sağlanır. Örneğin, `User` modelimiz üzerinde bir `roles` metodu tanımlayalım. Bu metoda aktarılan ilk bağımsız değişken, ilgili model sınıfının adıdır:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
 
class User extends Model
{
    /**
     * The roles that belong to the user.
     */
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }
}
```

İlişki tanımlandıktan sonra, `roles` dinamik ilişki özelliğini kullanarak kullanıcının rollerine erişebilirsiniz:

```php
use App\Models\User;
 
$user = User::find(1);
 
foreach ($user->roles as $role) {
    // ...
}
```

Tüm ilişkiler aynı zamanda sorgu oluşturucu görevi gördüğünden, `roles` metodunu çağırarak ve sorguya koşulları zincirlemeye devam ederek ilişki sorgusuna başka kısıtlamalar ekleyebilirsiniz:

```php
$roles = User::find(1)->roles()->orderBy('name')->get();
```

İlişkinin ara tablosunun tablo adını belirlemek için, Eloquent ilgili iki model adını alfabetik sırayla birleştirir. Ancak, bu kuralı geçersiz kılmakta özgürsünüz. Bunu `belongsToMany` metoduna ikinci bir bağımsız değişken ileterek yapabilirsiniz:

```php
return $this->belongsToMany(Role::class, 'role_user');
```

Ara tablonun adını özelleştirmenin yanı sıra, `belongsToMany` yöntemine ek bağımsız değişkenler ileterek tablodaki anahtarların sütun adlarını da özelleştirebilirsiniz. Üçüncü bağımsız değişken, ilişkiyi tanımladığınız modelin yabancı anahtar adıdır; dördüncü bağımsız değişken ise katıldığınız modelin yabancı anahtar adıdır:

```php
return $this->belongsToMany(Role::class, 'role_user', 'user_id', 'role_id');
```

#### İlişkinin Tersinin Tanımlanması

Bir çoktan çoka ilişkinin "tersini" tanımlamak için, ilgili model üzerinde `belongsToMany` metodunun sonucunu da döndüren bir metot tanımlamalısınız. Kullanıcı / rol örneğimizi tamamlamak için, `Role` modeli üzerinde `users` metodunu tanımlayalım:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
 
class Role extends Model
{
    /**
     * The users that belong to the role.
     */
    public function users(): BelongsToMany
    {
        return $this->belongsToMany(User::class);
    }
}
```

Gördüğünüz gibi, ilişki `App\Models\User` modeline başvurulması dışında `User` modelindeki muadiliyle tamamen aynı şekilde tanımlanmıştır. `belongsToMany` metodunu yeniden kullandığımız için, çoktan çoka ilişkilerin "tersini" tanımlarken tüm olağan tablo ve anahtar özelleştirme seçenekleri kullanılabilir.

## Ara Tablo Sütunlarını Alma

Daha önce öğrendiğiniz gibi, çoktan çoka ilişkilerle çalışmak bir ara tablonun varlığını gerektirir. Eloquent, bu tabloyla etkileşim kurmak için çok yararlı bazı yollar sağlar. Örneğin, `User` modelimizin ilişkili olduğu birçok `Role` modeli olduğunu varsayalım. Bu ilişkiye eriştikten sonra, modeller üzerindeki `pivot` özelliğini kullanarak ara tabloya erişebiliriz:

```php
use App\Models\User;
 
$user = User::find(1);
 
foreach ($user->roles as $role) {
    echo $role->pivot->created_at;
}
```

Aldığımız her `Role` modeline otomatik olarak bir `pivot` niteliği atandığına dikkat edin. Bu nitelik, ara tabloyu temsil eden bir model içerir.

Varsayılan olarak, `pivot` modelde yalnızca model anahtarları bulunacaktır. Ara tablonuz ekstra nitelikler içeriyorsa, ilişkiyi tanımlarken bunları belirtmeniz gerekir:

```php
return $this->belongsToMany(Role::class)->withPivot('active', 'created_by');
```

Ara tablonuzun Eloquent tarafından otomatik olarak tutulan `created_at` ve `updated_at` zaman damgalarına sahip olmasını istiyorsanız, ilişkiyi tanımlarken `withTimestamps` metodunu çağırın:

```php
return $this->belongsToMany(Role::class)->withTimestamps();
```

> Eloquent'in otomatik olarak tutulan zaman damgalarını kullanan ara tabloların hem `created_at` hem de `updated_at` zaman damgası sütunlarına sahip olması gerekir.

### `pivot` Niteliğinin İsmini Özelleştirme

Daha önce belirtildiği gibi, ara tablodaki niteliklere modellerde `pivot` niteliği aracılığıyla erişilebilir. Ancak, bu niteliğin adını uygulamanızdaki amacını daha iyi yansıtacak şekilde özelleştirmekte özgürsünüz.

Örneğin, uygulamanız podcast'lere abone olabilecek kullanıcılar içeriyorsa, muhtemelen kullanıcılar ve podcast'ler arasında çoktan çoğa bir ilişkiniz vardır. Bu durumda, ara tablo niteliğinizi `pivot` yerine `subscription` olarak yeniden adlandırmak isteyebilirsiniz. Bu, ilişki tanımlanırken `as` metodu kullanılarak yapılabilir:

```php
return $this->belongsToMany(Podcast::class)
                ->as('subscription')
                ->withTimestamps();
```

Özel ara tablo niteliği belirlendikten sonra, özelleştirilmiş adı kullanarak ara tablo verilerine erişebilirsiniz:

```php
$users = User::with('podcasts')->get();
 
foreach ($users->flatMap->podcasts as $podcast) {
    echo $podcast->subscription->created_at;
}
```

## Ara Tablo Sütunları Aracılığıyla Sorguları Filtreleme

Ayrıca, ilişkiyi tanımlarken `wherePivot`, `wherePivotIn`, `wherePivotNotIn`, `wherePivotBetween`, `wherePivotNotBetween`, `wherePivotNull` ve `wherePivotNotNull` metodlarını kullanarak `belongsToMany` ilişki sorguları tarafından döndürülen sonuçları filtreleyebilirsiniz:

```php
return $this->belongsToMany(Role::class)
                ->wherePivot('approved', 1);
 
return $this->belongsToMany(Role::class)
                ->wherePivotIn('priority', [1, 2]);
 
return $this->belongsToMany(Role::class)
                ->wherePivotNotIn('priority', [1, 2]);
 
return $this->belongsToMany(Podcast::class)
                ->as('subscriptions')
                ->wherePivotBetween('created_at', ['2020-01-01 00:00:00', '2020-12-31 00:00:00']);
 
return $this->belongsToMany(Podcast::class)
                ->as('subscriptions')
                ->wherePivotNotBetween('created_at', ['2020-01-01 00:00:00', '2020-12-31 00:00:00']);
 
return $this->belongsToMany(Podcast::class)
                ->as('subscriptions')
                ->wherePivotNull('expired_at');
 
return $this->belongsToMany(Podcast::class)
                ->as('subscriptions')
                ->wherePivotNotNull('expired_at');
```

## Ara Tablo Sütunları Aracılığıyla Sorguları Sıralama

`belongsToMany` ilişki sorguları tarafından döndürülen sonuçları `orderByPivot` metodunu kullanarak sıralayabilirsiniz. Aşağıdaki örnekte, kullanıcı için en son rozetlerin tümünü alacağız:

```php
return $this->belongsToMany(Badge::class)
                ->where('rank', 'gold')
                ->orderByPivot('created_at', 'desc');
```

## Özel Ara Tablo Modellerini Tanımlama

Çoktan çoka ilişkinizin ara tablosunu temsil etmek için özel bir model tanımlamak isterseniz, ilişkiyi tanımlarken `using` metodunu çağırabilirsiniz. Özel pivot modeller size pivot model üzerinde yöntemler ve dökümler gibi ek davranışlar tanımlama fırsatı verir.

Özel çoktan çoka pivot modelleri `Illuminate\Database\Eloquent\Relations\Pivot` sınıfını genişletirken, özel polimorfik çoktan çoka pivot modelleri `Illuminate\Database\Eloquent\Relations\MorphPivot` sınıfını genişletmelidir. Örneğin, özel bir `RoleUser` pivot modeli kullanan bir `Role` modeli tanımlayabiliriz:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
 
class Role extends Model
{
    /**
     * The users that belong to the role.
     */
    public function users(): BelongsToMany
    {
        return $this->belongsToMany(User::class)->using(RoleUser::class);
    }
}
```

`RoleUser` modelini tanımlarken, `Illuminate\Database\Eloquent\Relations\Pivot` sınıfını genişletmelisiniz:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Relations\Pivot;
 
class RoleUser extends Pivot
{
    // ...
}
```

> Pivot modeller `SoftDeletes` özelliğini kullanmayabilir. Pivot kayıtlarını soft delete silmeniz gerekiyorsa, pivot modelinizi gerçek bir Eloquent modeline dönüştürmeyi düşünün.

### Özel Pivot Modelleri ve ID

Özel bir pivot model kullanan çoktan çoğa bir ilişki tanımladıysanız ve bu pivot model otomatik artan bir birincil anahtara sahipse, özel pivot model sınıfınızın `true` olarak ayarlanmış bir `$incrementing` özelliği tanımladığından emin olmalısınız.

```php
/**
 * Indicates if the IDs are auto-incrementing.
 *
 * @var bool
 */
public $incrementing = true;
```
