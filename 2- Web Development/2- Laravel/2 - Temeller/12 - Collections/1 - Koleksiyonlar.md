# # Giriş
---
`Illuminate\Support\Collection` sınıfı, veri dizileriyle çalışmak için akıcı ve kullanışlı bir sarmalayıcı sağlar. Örneğin, aşağıdaki koda göz atın. Diziden yeni bir koleksiyon örneği oluşturmak için `collect` yardımcısını kullanacağız, her öğe üzerinde `strtoupper` işlevini çalıştıracağız ve ardından tüm boş öğeleri kaldıracağız:

```php
$collection = collect(['taylor', 'abigail', null])->map(function (?string $name) {
    return strtoupper($name);
})->reject(function (string $name) {
    return empty($name);
});
```

Gördüğünüz gibi, `Collection` sınıfı, altta yatan dizinin akıcı bir şekilde eşlenmesini ve azaltılmasını gerçekleştirmek metodları zincirlemenize olanak tanır. Genel olarak, koleksiyonlar değişmezdir, yani her `Collection` metodu tamamen yeni bir `Collection` örneği döndürür.

# `#` Koleksiyon Oluşturma
---
Yukarıda belirtildiği gibi, `collect` yardımcısı verilen dizi için yeni bir `Illuminate\Support\Collection` örneği döndürür. Yani, bir koleksiyon oluşturmak şu kadar basittir:

```php
$collection = collect([1, 2, 3]);
```

> Eloquent sorgularının sonuçları her zaman `Collection` örnekleri olarak döndürülür.

# `#` Koleksiyonları Genişletme
---
Koleksiyonlar "makrolanabilir", bu da çalışma zamanında `Collection` sınıfına ek metotlar eklemenize olanak tanır. `Illuminate\Support\Collection` sınıfının `macro` metodu, makronuz çağrıldığında çalıştırılacak bir kapanış kabul eder. Makro kapanışı, koleksiyon sınıfının gerçek metoduymuş gibi `$this` aracılığıyla koleksiyonun diğer metotlarına erişebilir. Örneğin, aşağıdaki kod `Collection` sınıfına bir `toUpper` metotu ekler:

```php
use Illuminate\Support\Collection;
use Illuminate\Support\Str;
 
Collection::macro('toUpper', function () {
    return $this->map(function (string $value) {
        return Str::upper($value);
    });
});
 
$collection = collect(['first', 'second']);
 
$upper = $collection->toUpper();
 
// ['FIRST', 'SECOND']
```

Tipik olarak, koleksiyon makrolarını bir service provider'ın `boot` metoduna bildirmeniz gerekir.

## Makro Parametreleri

Gerekirse, ek bağımsız değişkenler kabul eden makrolar tanımlayabilirsiniz:

```php
use Illuminate\Support\Collection;
use Illuminate\Support\Facades\Lang;
 
Collection::macro('toLocale', function (string $locale) {
    return $this->map(function (string $value) use ($locale) {
        return Lang::get($value, [], $locale);
    });
});
 
$collection = collect(['first', 'second']);
 
$translated = $collection->toLocale('es');
```

# `#` Mevcut Metotlar
---
Kalan koleksiyon dokümantasyonunun çoğunluğu için, `Collection` sınıfında mevcut olan her metotu tartışacağız. Unutmayın, bu metodların tümü, temel diziyi akıcı bir şekilde işlemek için zincirleme olarak kullanılabilir. Ayrıca, neredeyse her metot yeni bir `Collection` örneği döndürerek gerektiğinde koleksiyonun orijinal kopyasını korumanıza olanak tanır:

| METOTLAR #1          | METOTLAR #2      | METOTLAR #3      |
| -------------------- | -------------------- | -------------------- |
| `after`            | `isEmpty`          | `slice`            |
| `all`              | `isNotEmpty`       | `sliding`          |
| `average`          | `join`             | `sole`             |
| `avg`              | `keyBy`            | `some`             |
| `before`           | `keys`             | `sort`             |
| `chunk`            | `last`             | `sortBy`           |
| `chunkWhile`       | `lazy`             | `sortByDesc`       |
| `collapse`         | `macro`            | `sortDesc`         |
| `collect`          | `make`             | `sortKeys`         |
| `combine`          | `map`              | `sortKeysDesc`     |
| `concat`           | `mapInto`          | `sortKeysUsing`    |
| `contains`         | `mapSpread`        | `splice`           |
| `containsOneItem`  | `mapToGroups`      | `split`            |
| `containsStrict`   | `mapWithKeys`      | `splitIn`          |
| `count`            | `max`              | `sum`              |
| `countBy`          | `median`           | `take`             |
| `crossJoin`        | `merge`            | `takeUntil`        |
| `dd`               | `mergeRecursive`   | `takeWhile`        |
| `diff`             | `min`              | `tap`              |
| `diffAssoc`        | `mode`             | `times`            |
| `diffAssocUsing`   | `nth`              | `toArray`          |
| `diffKeys`         | `only`             | `toJson`           |
| `doesntContain`    | `pad`              | `transform`        |
| `dot`              | `partition`        | `undot`            |
| `dump`             | `percentage`       | `union`            |
| `duplicates`       | `pipe`             | `unique`           |
| `duplicatesStrict` | `pipeInto`         | `uniqueStrict`     |
| `each`             | `pipeThrough`      | `unless`           |
| `eachSpread`       | `pluck`            | `unlessEmpty`      |
| `ensure`           | `pop`              | `unlessNotEmpty`   |
| `every`            | `prepend`          | `unwrap`           |
| `except`           | `pull`             | `value`            |
| `filter`           | `push`             | `values`           |
| `first`            | `put`              | `when`             |
| `firstOrFail`      | `random`           | `whenEmpty`        |
| `firstWhere`       | `range`            | `whenNotEmpty`     |
| `flatMap`          | `reduce`           | `where`            |
| `flatten`          | `reduceSpread`     | `whereStrict`      |
| `flip`             | `reject`           | `whereBetween`     |
| `forget`           | `replace`          | `whereIn`          |
| `forPage`          | `replaceRecursive` | `whereInStrict`    |
| `get`              | `reverse`          | `whereInstanceOf`  |
| `groupBy`          | `search`           | `whereNotBetween`  |
| `has`              | `select`           | `whereNotIn`       |
| `hasAny`           | `shift`            | `whereNotInStrict` |
| `implode`          | `shuffle`          | `whereNotNull`     |
| `intersect`        | `skip`             | `whereNull`        |
| `intersectAssoc`   | `skipUntil`        | `wrap`             |
| `intersectByKeys`  | `skipWhile`        | `zip`              |
