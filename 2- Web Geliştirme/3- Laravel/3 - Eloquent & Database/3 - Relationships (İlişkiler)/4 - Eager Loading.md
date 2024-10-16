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
