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

# `#` Eager Loading
---
Eloquent ilişkilerine özellik olarak erişirken, ilgili modeller "tembel yüklenir". Bu, siz özelliğe ilk kez erişene kadar ilişki verilerinin gerçekte yüklenmediği anlamına gelir. Ancak Eloquent, üst modeli sorguladığınız sırada ilişkileri "istekli yükleyebilir". İstekli yükleme "N + 1" sorgu sorununu hafifletir. N + 1 sorgu sorununu açıklamak için, bir `Author` modeline "ait" olan bir `Book` modeli düşünün:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
 
class Book extends Model
{
    /**
     * Get the author that wrote the book.
     */
    public function author(): BelongsTo
    {
        return $this->belongsTo(Author::class);
    }
}
```

Şimdi, tüm kitapları ve yazarlarını alalım:

```php
use App\Models\Book;
 
$books = Book::all();
 
foreach ($books as $book) {
    echo $book->author->name;
}
```

Bu döngü, veritabanı tablosundaki tüm kitapları almak için bir sorgu çalıştıracak, ardından kitabın yazarını almak için her kitap için başka bir sorgu çalıştıracaktır. Yani, 25 kitabımız varsa, yukarıdaki kod 26 sorgu çalıştıracaktır: orijinal kitap için bir tane ve her kitabın yazarını almak için 25 ek sorgu.

Neyse ki, bu işlemi yalnızca iki sorguya indirgemek için eager yüklemeyi kullanabiliriz. Bir sorgu oluştururken, `with` metodunu kullanarak hangi ilişkilerin eager ile yükleneceğini belirtebilirsiniz:

```php
$books = Book::with('author')->get();
 
foreach ($books as $book) {
    echo $book->author->name;
}
```

Bu işlem için yalnızca iki sorgu yürütülecektir - tüm kitapları almak için bir sorgu ve tüm kitaplar için tüm yazarları almak için bir sorgu:

```MySQL
select * from books
 
select * from authors where id in (1, 2, 3, 4, 5, ...)
```

### Çoklu İlişkilerde Eager Loading

Bazen birkaç farklı ilişkiyi eager ile yüklemeniz gerekebilir. Bunu yapmak için, `with` metoduna bir dizi ilişki aktarmanız yeterlidir:

```php
$books = Book::with(['author', 'publisher'])->get();
```

### İç İçe İlişkilerde Eager Loading

Bir ilişkinin ilişkilerini eager ile yüklemek için "nokta" sözdizimini kullanabilirsiniz. Örneğin, kitabın tüm yazarlarını ve yazarın tüm kişisel bağlantılarını eager ile yükleyelim:

```php
$books = Book::with('author.contacts')->get();
```

Alternatif olarak, `with` metoduna iç içe geçmiş bir dizi sağlayarak iç içe geçmiş eager yüklenmiş ilişkileri belirtebilirsiniz; bu, birden fazla iç içe geçmiş ilişkiyi eager yüklerken kullanışlı olabilir:

```php
$books = Book::with([
    'author' => [
        'contacts',
        'publisher',
    ],
])->get();
```

### İç İçe Poliformik İlişkilerde Eager Loading

Bir `morphTo` ilişkisinin yanı sıra bu ilişki tarafından döndürülebilecek çeşitli varlıklar üzerindeki iç içe ilişkileri de yüklemek isterseniz, `morphTo` ilişkisinin `morphWith` metoduyla birlikte `with` metodunu kullanabilirsiniz. Bu metodu göstermeye yardımcı olması için aşağıdaki modeli ele alalım:

```php
<?php
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
class ActivityFeed extends Model
{
    /**
     * Get the parent of the activity feed record.
     */
    public function parentable(): MorphTo
    {
        return $this->morphTo();
    }
}
```

Bu örnekte, `Event`, `Photo` ve `Post` modellerinin `ActivityFeed` modelleri oluşturabileceğini varsayalım. Ayrıca, `Event` modellerinin bir `Calendar` modeline ait olduğunu, `Photo` modellerinin `Tag` modelleriyle ilişkili olduğunu ve `Post` modellerinin bir `Author` modeline ait olduğunu varsayalım.

Bu model tanımlarını ve ilişkilerini kullanarak, `ActivityFeed` model örneklerini alabilir ve tüm ebeveynlenebilir modelleri ve bunların ilgili iç içe geçmiş ilişkilerini yükleyebiliriz:

```php
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
$activities = ActivityFeed::query()
    ->with(['parentable' => function (MorphTo $morphTo) {
        $morphTo->morphWith([
            Event::class => ['calendar'],
            Photo::class => ['tags'],
            Post::class => ['author'],
        ]);
    }])->get();
```

### Eaged Loading'te Belirli Sütunları Seçme

Aldığınız ilişkilerdeki her sütuna her zaman ihtiyacınız olmayabilir. Bu nedenle Eloquent, ilişkinin hangi sütunlarını almak istediğinizi belirtmenize olanak tanır:

```php
$books = Book::with('author:id,name,book_id')->get();
```

> Bu özelliği kullanırken, almak istediğiniz sütunlar listesine her zaman `id` sütununu ve ilgili yabancı anahtar sütunlarını dahil etmelisiniz.

### Varsayılan Olarak Eager Loading Yapma

Bazen bir modeli alırken bazı ilişkileri her zaman yüklemek isteyebilirsiniz. Bunu gerçekleştirmek için, model üzerinde bir `$with` özelliği tanımlayabilirsiniz:

```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
 
class Book extends Model
{
    /**
     * The relationships that should always be loaded.
     *
     * @var array
     */
    protected $with = ['author'];
 
    /**
     * Get the author that wrote the book.
     */
    public function author(): BelongsTo
    {
        return $this->belongsTo(Author::class);
    }
 
    /**
     * Get the genre of the book.
     */
    public function genre(): BelongsTo
    {
        return $this->belongsTo(Genre::class);
    }
}
```

Tek bir sorgu için `$with` özelliğinden bir öğeyi kaldırmak isterseniz, `without` metodunu kullanabilirsiniz:

```php
$books = Book::without('author')->get();
```

Tek bir sorgu için `$with` özelliğindeki tüm öğeleri geçersiz kılmak isterseniz, `withOnly` metodunu kullanabilirsiniz:

```php
$books = Book::withOnly('genre')->get();
```

## Koşullu Eager Loading

Bazen bir ilişkiyi eager loading ile sorgusuyla kullanmak ama aynı zamanda eager loading sorgusu için ek sorgu koşulları belirtmek isteyebilirsiniz. Bunu, dizi anahtarının bir ilişki adı ve dizi değerinin de eager loading sorgusuna ek kısıtlamalar ekleyen bir closure olduğu bir dizi ilişkiyi `with` metoduna aktararak gerçekleştirebilirsiniz:

```php
use App\Models\User;
use Illuminate\Contracts\Database\Eloquent\Builder;
 
$users = User::with(['posts' => function (Builder $query) {
    $query->where('title', 'like', '%code%');
}])->get();
```

Bu örnekte, Eloquent yalnızca gönderinin `title` sütununda `code` kelimesinin bulunduğu gönderileri yükleyecektir. Eager yükleme işlemini daha da özelleştirmek için diğer sorgu oluşturucu yöntemlerini çağırabilirsiniz:

```php
$users = User::with(['posts' => function (Builder $query) {
    $query->orderBy('created_at', 'desc');
}])->get();
```

### Poliformik İlişkilerde Koşullu Eager Loading

Bir `morphTo` ilişkisini yüklemeye istekliyseniz, Eloquent ilgili modelin her türünü almak için birden fazla sorgu çalıştıracaktır. `MorphTo` ilişkisinin `constrain` metodunu kullanarak bu sorguların her birine ek kısıtlamalar ekleyebilirsiniz:

```php
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
$comments = Comment::with(['commentable' => function (MorphTo $morphTo) {
    $morphTo->constrain([
        Post::class => function ($query) {
            $query->whereNull('hidden_at');
        },
        Video::class => function ($query) {
            $query->where('type', 'educational');
        },
    ]);
}])->get();
```

Bu örnekte, Eloquent yalnızca gizlenmemiş gönderileri ve `type` değeri "educational" olan videoları yüklemek isteyecektir.

### İlişki Varlığı ile Eager Loading Kısıtlaması

Bazen bir ilişkinin varlığını kontrol ederken aynı zamanda ilişkiyi aynı koşullara göre yüklemeniz gerekebilir. Örneğin, yalnızca belirli bir sorgu koşuluyla eşleşen alt `Post` modellerine sahip `User` modellerini almak ve aynı zamanda eşleşen gönderileri yüklemek isteyebilirsiniz. Bunu `withWhereHas` metodunu kullanarak gerçekleştirebilirsiniz:

```php
use App\Models\User;
 
$users = User::withWhereHas('posts', function ($query) {
    $query->where('featured', true);
})->get();
```

## Lazy Eager Loading

Bazen üst model zaten alındıktan sonra bir ilişkiyi eager olarak yüklemeniz gerekebilir. Örneğin, ilgili modellerin yüklenip yüklenmeyeceğine dinamik olarak karar vermeniz gerekiyorsa bu yararlı olabilir:

```php
use App\Models\Book;
 
$books = Book::all();
 
if ($someCondition) {
    $books->load('author', 'publisher');
}
```

Eager loading sorgusunda ek sorgu kısıtlamaları ayarlamanız gerekiyorsa, yüklemek istediğiniz ilişkiler tarafından anahtarlanan bir dizi iletebilirsiniz. Dizi değerleri, sorgu örneğini alan kapanış örnekleri olmalıdır:

```php
$author->load(['books' => function (Builder $query) {
    $query->orderBy('published_date', 'asc');
}]);
```

Bir ilişkiyi yalnızca daha önce yüklenmemişse yüklemek için `loadMissing` metodunu kullanın:

```php
$book->loadMissing('author');
```

### İç İçe Poliformik İlişki ve Lazy Eager Loading

Bir `morphTo` ilişkisinin yanı sıra bu ilişki tarafından döndürülebilecek çeşitli varlıklar üzerindeki iç içe ilişkileri de yüklemek isterseniz `loadMorph` metodunu kullanabilirsiniz.

Bu metot, ilk bağımsız değişkeni olarak `morphTo` ilişkisinin adını ve ikinci bağımsız değişkeni olarak bir dizi model/ilişki çiftini kabul eder. Bu metotu göstermeye yardımcı olması için aşağıdaki modeli ele alalım:


```php
<?php
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
class ActivityFeed extends Model
{
    /**
     * Get the parent of the activity feed record.
     */
    public function parentable(): MorphTo
    {
        return $this->morphTo();
    }
}<?php
 
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\MorphTo;
 
class ActivityFeed extends Model
{
    /**
     * Get the parent of the activity feed record.
     */
    public function parentable(): MorphTo
    {
        return $this->morphTo();
    }
}
```

Bu örnekte, `Event`, `Photo` ve `Post` modellerinin `ActivityFeed` modelleri oluşturabileceğini varsayalım. Ayrıca, `Event` modellerinin bir `Calendar` modeline ait olduğunu, Photo modellerinin `Tag` modelleriyle ilişkili olduğunu ve `Post` modellerinin bir `Author` modeline ait olduğunu varsayalım.

Bu model tanımlarını ve ilişkilerini kullanarak, `ActivityFeed` model örneklerini alabilir ve tüm ebeveynlenebilir modelleri ve bunların ilgili iç içe geçmiş ilişkilerini yükleyebiliriz:

```php
$activities = ActivityFeed::with('parentable')
    ->get()
    ->loadMorph('parentable', [
        Event::class => ['calendar'],
        Photo::class => ['tags'],
        Post::class => ['author'],
    ]);
```

## Lazy Loading Engelleme

Daha önce tartışıldığı gibi, eager yükleme ilişkileri genellikle uygulamanıza önemli performans avantajları sağlayabilir. Bu nedenle, eğer isterseniz, Laravel'e ilişkilerin tembel yüklenmesini her zaman engellemesi talimatını verebilirsiniz. Bunu başarmak için, temel Eloquent model sınıfı tarafından sunulan `preventLazyLoading` metodunu çağırabilirsiniz. Tipik olarak, bu yöntemi uygulamanızın `AppServiceProvider` sınıfının `boot` metodu içinde çağırmanız gerekir.

`preventLazyLoading` metotu, tembel yüklemenin engellenip engellenmeyeceğini belirten isteğe bağlı bir `boolean` bağımsız değişken kabul eder. Örneğin, üretim kodunda yanlışlıkla tembel yüklenmiş bir ilişki bulunsa bile üretim ortamınızın normal şekilde çalışmaya devam etmesi için tembel yüklemeyi yalnızca üretim dışı ortamlarda devre dışı bırakmak isteyebilirsiniz:

```php
use Illuminate\Database\Eloquent\Model;
 
/**
 * Bootstrap any application services.
 */
public function boot(): void
{
    Model::preventLazyLoading(! $this->app->isProduction());
}
```

Tembel yüklemeyi engelledikten sonra, uygulamanız herhangi bir Eloquent ilişkisini tembel yüklemeye çalıştığında Eloquent bir `Illuminate\Database\LazyLoadingViolationException` istisnası atacaktır.

`handleLazyLoadingViolationsUsing` metodunu kullanarak tembel yükleme ihlallerinin davranışını özelleştirebilirsiniz. Örneğin, bu metotu kullanarak, tembel yükleme ihlallerinin uygulamanın yürütülmesini istisnalarla kesintiye uğratmak yerine yalnızca günlüğe kaydedilmesi talimatını verebilirsiniz:

```php
Model::handleLazyLoadingViolationUsing(function (Model $model, string $relation) {
    $class = $model::class;
 
    info("Attempted to lazy load [{$relation}] on model [{$class}].");
});
```

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


