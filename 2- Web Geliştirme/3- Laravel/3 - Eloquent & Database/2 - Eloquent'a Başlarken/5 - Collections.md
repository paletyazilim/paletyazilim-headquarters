# `#` Giriş
---
Birden fazla model sonucu döndüren tüm Eloquent metotları, `get` metotu ile alınan veya bir ilişki yoluyla erişilen sonuçlar da dahil olmak üzere `Illuminate\Database\Eloquent\Collection` sınıfının örneklerini döndürecektir. Eloquent koleksiyon nesnesi Laravel'in temel koleksiyonunu genişletir, bu nedenle doğal olarak altta yatan Eloquent modelleri dizisiyle akıcı bir şekilde çalışmak için kullanılan düzinelerce metotu miras alır. Bu yararlı metotlar hakkında her şeyi öğrenmek için Laravel koleksiyon dokümantasyonunu gözden geçirdiğinizden emin olun! 

Tüm koleksiyonlar aynı zamanda yineleyici olarak da işlev görür ve basit PHP dizileriymiş gibi üzerlerinde döngü yapmanıza olanak tanır:

```php
use App\Models\User;
 
$users = User::where('active', 1)->get();
 
foreach ($users as $user) {
    echo $user->name;
}
```

Ancak, daha önce de belirtildiği gibi, koleksiyonlar dizilerden çok daha güçlüdür ve sezgisel bir arayüz kullanılarak zincirlenebilen çeşitli map / reduce işlemlerini ortaya çıkarır. Örneğin, tüm etkin olmayan modelleri kaldırabilir ve ardından kalan her kullanıcı için ilk adı toplayabiliriz:

```php
$names = User::all()->reject(function (User $user) {
    return $user->active === false;
})->map(function (User $user) {
    return $user->name;
});
```

### Koleksiyon Dönüşümü

Çoğu Eloquent koleksiyon metodu bir Eloquent koleksiyonunun yeni bir örneğini döndürürken, `collapse`, `flatten`, `flip`, `keys`, `pluck` ve `zip` metotları bir temel koleksiyon örneği döndürür. Benzer şekilde, bir eşleme işlemi herhangi bir Eloquent modeli içermeyen bir koleksiyon döndürürse, bu bir temel koleksiyon örneğine dönüştürülür.

# `#` Mevcut Metotlar
---
Tüm Eloquent koleksiyonları temel Laravel koleksiyon nesnesini genişletir; bu nedenle, temel koleksiyon sınıfı tarafından sağlanan tüm güçlü yöntemleri miras alırlar. 

Ek olarak, `Illuminate\Database\Eloquent\Collection` sınıfı, model koleksiyonlarınızı yönetmeye yardımcı olacak bir üst metot kümesi sağlar. Çoğu metot `Illuminate\Database\Eloquent\Collection` örneklerini döndürür; ancak `modelKeys` gibi bazı metotlar bir `Illuminate\Support\Collection` örneği döndürür.

| Metotlar #1 | Metotlar #2 |
| ----------- | ----------- |
| `append`      | `modelKeys`   |
| `contains`    | `makeVisible` |
| `diff`        | `makeHidden`  |
| `except`      | `only`        |
| `find`        | `setVisible`  |
| `fresh`       | `setHidden`   |
| `intersect`   | `toQuery`     |
| `load`        | `unique`      |
| `loadMissing` |             |

### `append($attributes)`

`append` metodu, koleksiyondaki her model için bir niteliğin eklenmesi gerektiğini belirtmek için kullanılabilir. Bu metot, bir nitelik dizisini veya tek bir niteliği kabul eder:

```php
$users->append('team');
 
$users->append(['team', 'is_admin']);
```

### `contains($key, $operator = null, $value = null)`

`contains` metotu, belirli bir model örneğinin koleksiyon tarafından içerilip içerilmediğini belirlemek için kullanılabilir. Bu metot bir birincil anahtar veya bir model örneği kabul eder:

```php
$users->contains(1);
 
$users->contains(User::find(1));
```

### `diff($items)`
`diff` metotu, verilen koleksiyonda bulunmayan tüm modelleri döndürür:

```php
use App\Models\User;
 
$users = $users->diff(User::whereIn('id', [1, 2, 3])->get());
```

### `except($keys)`
`except` metotu, verilen birincil anahtarlara sahip olmayan tüm modelleri döndürür:

```php
$users = $users->except([1, 2, 3]);
```

### `find($key)`
`find` metodu, verilen anahtarla eşleşen birincil anahtara sahip modeli döndürür. Eğer `$key` bir model örneğiyse, `find` metotu birincil anahtarla eşleşen bir model döndürmeye çalışır. Eğer `$key` bir anahtar dizisi ise, `find` verilen dizide birincil anahtarı olan tüm modelleri döndürür:

```php
$users = User::all();
 
$user = $users->find(1);
```

### `fresh($with = [])`
`fresh` metotu, koleksiyondaki her modelin yeni bir örneğini veritabanından alır. Buna ek olarak, belirtilen tüm ilişkiler eager olarak yüklenir:

```php
$users = $users->fresh();
 
$users = $users->fresh('comments');
```

### `intersect($items)`
`intersect` metotu, verilen koleksiyonda da bulunan tüm modelleri döndürür:

```php
use App\Models\User;
 
$users = $users->intersect(User::whereIn('id', [1, 2, 3])->get());
```

### `load($relations)`
`load` metotu, koleksiyondaki tüm modeller için verilen ilişkileri eager olarak yükler:

```php
$users->load(['comments', 'posts']);
 
$users->load('comments.author');
 
$users->load(['comments', 'posts' => fn ($query) => $query->where('active', 1)]);
```

### `loadMissing($relations)`
`loadMissing` metotu, ilişkiler zaten yüklenmemişse koleksiyondaki tüm modeller için verilen ilişkileri eager bir şekilde yükler:

```php
$users->loadMissing(['comments', 'posts']);
 
$users->loadMissing('comments.author');
 
$users->loadMissing(['comments', 'posts' => fn ($query) => $query->where('active', 1)]);
```

### `modelKeys()`
`modelKeys` metotu, koleksiyondaki tüm modeller için birincil anahtarları döndürür:

```php
$users->modelKeys();
 
// [1, 2, 3, 4, 5]
```

### `makeVisible($attributes)`
`makeVisible` metotu, koleksiyondaki her modelde tipik olarak "gizli" olan nitelikleri görünür hale getirir:

```php
$users = $users->makeVisible(['address', 'phone_number']);
```

### `makeHidden($attributes)`
`makeHidden` metotu, koleksiyondaki her modelde tipik olarak "görünür" olan nitelikleri gizler:

```php
$users = $users->makeHidden(['address', 'phone_number']);
```

### `only($keys)`
`only` metotu, verilen birincil anahtarlara sahip tüm modelleri döndürür:

```php
$users = $users->only([1, 2, 3]);
```

### `setVisible($attributes)`
`setVisible` metotu, koleksiyondaki her model üzerindeki tüm görünür nitelikleri geçici olarak geçersiz kılar:

```php
$users = $users->setVisible(['id', 'name']);
```

### `setHidden($attributes)`
`setHidden` metotu, koleksiyondaki her model üzerindeki tüm gizli nitelikleri geçici olarak geçersiz kılar:

```php
$users = $users->setHidden(['email', 'password', 'remember_token']);
```

### `toQuery()`
`toQuery` metotu, koleksiyon modelinin birincil anahtarları üzerinde bir `whereIn` kısıtlaması içeren bir Eloquent sorgu oluşturucu örneği döndürür:

```php
use App\Models\User;
 
$users = User::where('status', 'VIP')->get();
 
$users->toQuery()->update([
    'status' => 'Administrator',
]);
```

### `unique($key = null, $strict = false)`
`unique` metotu koleksiyondaki tüm benzersiz modelleri döndürür. Koleksiyondaki başka bir modelle aynı birincil anahtara sahip tüm modeller kaldırılır:

```php
$users = $users->unique();
```

# `#` Custom Collections
---
#çevrilecek 