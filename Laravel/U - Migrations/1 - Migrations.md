# `#` Migrations
---
Migration, veritabanınız için sürüm kontrolü gibidir ve ekibinizin uygulamanın veritabanı şema tanımını belirlemesine ve paylaşmasına olanak tanır. Kaynak kontrolünden değişikliklerinizi çektiğinizde bir takım arkadaşınıza yerel veritabanı şemasına manuel olarak bir sütun eklemesini söylemek zorunda kaldıysanız, migration bu sorunu kolayca çözecektir.

Laravel `Schema` facade'i, Laravel'in desteklediği tüm veritabanı sistemleri üzerinde tablo oluşturma ve düzenleme için veritabanı bağımsız desteği sağlar. Genellikle, migration bu facade'i kullanarak veritabanı tablolarını ve sütunlarını oluşturur ve değiştirir.

# `#` Migration Oluşturma
---
Veritabanı migration'u oluşturmak için `make:migration` Artisan komutunu kullanabilirsiniz. Yeni migration, `database/migrations` dizinine yerleştirilecektir. Her migration dosya adı, migration sırasını belirlemek için bir zaman damgası içerir.

```shell
php artisan make:migration create_flights_table
```

Laravel, migration dosyasının adından tablonun adını ve migration'un yeni bir tablo oluşturup oluşturmayacağını tahmin etmek için kullanacak. Laravel, migration adından tablo adını belirleyebilirse, oluşturulan migration dosyasını belirtilen tablo ile önceden dolduracaktır. Aksi takdirde, tabloyu migration dosyasında manuel olarak belirtebilirsiniz.

Eğer oluşturulan migration için özel bir yol belirtmek isterseniz, `make:migration` komutunu çalıştırırken `--path` seçeneğini kullanabilirsiniz. Verilen yol, uygulamanızın temel yoluna göre göreceli olmalıdır.

# `#` Migration'ları Sıkıştırma
---
Uygulamanızı oluştururken, zamanla daha fazla migration biriktirebilirsiniz. Bu, `database/migrations` dizininizin potansiyel olarak yüzlerce migration ile şişmesine neden olabilir. İsterseniz, migrationları tek bir SQL dosyasına "sıkıştırabilirsiniz". Başlamak için `schema:dump` komutunu çalıştırın:

```shell
php artisan schema:dump

# Mevcut bütün migration dosyalarını sıkıştır ve ardından sil!
php artisan schema:dump --prune
```

Bu komutu çalıştırdığınızda, Laravel uygulamanızın `database/schema` dizinine bir "şema" dosyası yazacaktır. Şema dosyasının adı, veritabanı bağlantısına karşılık gelecektir. 

Şimdi, veritabanınızı migration etmeye çalıştığınızda ve başka migration işlemi gerçekleştirilmemişse, Laravel önce kullandığınız veritabanı bağlantısının şema dosyasındaki SQL ifadelerini çalıştıracaktır. Şema dosyasının SQL ifadelerini çalıştırdıktan sonra, Laravel şema dökümünün parçası olmayan kalan migrationları gerçekleştirecektir.

Uygulamanızın testleri, yerel geliştirme sürecinde genellikle kullandığınız veritabanı bağlantısından farklı bir veritabanı bağlantısı kullanıyorsa, testlerinizin veritabanını oluşturabilmesi için o veritabanı bağlantısını kullanarak bir şema dosyası dökmeniz gerekmektedir. Genellikle yerel geliştirme sürecinde kullandığınız veritabanı bağlantısını döktükten sonra bunu yapmak isteyebilirsiniz.

```shell
php artisan schema:dump
php artisan schema:dump --database=testing --prune
```

Veritabanı şema dosyanızı kaynak kontrolüne eklemelisiniz, böylece takımınızdaki diğer yeni geliştiriciler uygulamanın başlangıç veritabanı yapısını hızlı bir şekilde oluşturabilir.

> Migration sıkıştırma yalnızca MySQL, PostgreSQL ve SQLite veritabanları için kullanılabilir ve veritabanının komut satırı istemcisini kullanır.

# `#` Migration Mimarisi 
---
Bir migration sınıfı, iki metod içerir: `up` ve `down`. `up` metodu, veritabanınıza yeni tablolar, sütunlar veya dizinler eklemek için kullanılırken, `down` metodu `up` metodu tarafından gerçekleştirilen işlemleri geri almalıdır.

Bu metodların her ikisinde de, Laravel şema oluşturucusunu kullanarak tabloları açık bir şekilde oluşturabilir ve değiştirebilirsiniz. `Schema` oluşturucusunda mevcut olan tüm metodları öğrenmek için devam edin. Örneğin, aşağıdaki göç, bir uçuşlar tablosu oluşturur:

```php
<?php
 
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('flights', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('airline');
            $table->timestamps();
        });
    }
 
    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::drop('flights');
    }
};
```

## Migration Bağlantısını Ayarlama

Eğer migration işleminiz, uygulamanızın varsayılan veritabanı bağlantısı dışında bir veritabanı bağlantısıyla etkileşimde bulunacaksa, migrationunuz `$connection` özelliğini ayarlamanız gerekmektedir.

```php
/**
 * The database connection that should be used by the migration.
 *
 * @var string
 */
protected $connection = 'pgsql';
 
/**
 * Run the migrations.
 */
public function up(): void
{
    // ...
}
```

# `#` Migrationları Çalıştırma
---
Tüm bekleyen migrate'leri çalıştırmak için `migrate Artisan` komutunu çalıştırın:

```shell
php artisan migrate
```

Eğer şu ana kadar hangi migrate'lerin çalıştığını görmek isterseniz, `migrate:status` Artisan komutunu kullanabilirsiniz.

```shell
php artisan migrate:status
```

Eğer migrate'leri gerçekleştirmeden önce çalıştırılacak SQL ifadelerini görmek isterseniz, migrate komutuna `--pretend` seçeneğini ekleyebilirsiniz:

```shell
php artisan migrate --pretend
```

## Migrateleri İzole Etme

Uygulamanızı birden fazla sunucuya dağıtıyorsanız ve dağıtım sürecinin bir parçası olarak migrate çalıştırıyorsanız, muhtemelen iki sunucunun aynı anda veritabanını migrate etmeye çalışmasını istemezsiniz. Bunu önlemek için, migrate komutunu çağırırken `--isolated` seçeneğini kullanabilirsiniz.

`isolated` seçeneği sağlandığında, Laravel, migrationları çalıştırmadan önce uygulamanızın önbellek sürücüsünü kullanarak atomik bir kilitleme alır. Bu kilidin tutulduğu süre boyunca `migrate` komutunu çalıştırmak için yapılan diğer tüm girişimler gerçekleştirilmez; ancak, komut hala başarılı bir çıkış durum koduyla sonlanır.

```shell
php artisan migrate --isolated
```

Bu özelliği kullanmak için uygulamanızın varsayılan önbellek sürücüsü olarak `memcached`, `redis`, `dynamodb`, `database`, `file` veya `array` önbellek sürücüsünü kullanması gerekmektedir. Ayrıca, tüm sunucuların aynı merkezi önbellek sunucusuyla iletişim halinde olması gerekmektedir.

## Prodüksyon Aşamasında Migrationların Çalıştırılması

Bazı migrate işlemleri `destructive` olabilir, bu da veri kaybına neden olabilir. Prodüksyon veritabanınıza karşı bu komutları çalıştırmaktan sizi korumak için komutlar uygulanmadan önce onay almanız istenecektir. Komutları onay almadan çalıştırmak için `--force` seçeneğini kullanın:

```shell
php artisan migrate --force
```

## Migrate'i Geri Alma

En son migrate işlemini geri almak için `rollback` Artisan komutunu kullanabilirsiniz. Bu komut, birden fazla migrate dosyasını içerebilen son "batch" migrate'i geri alır.

```shell
php artisan migrate:rollback
```

`rollback` komutuna `--step` seçeneğini sağlayarak sınırlı sayıda migrate'i geri alabilirsiniz. Örneğin, aşağıdaki komut son beş migrate'i geri alacaktır:

```shell
php artisan migrate:rollback --step=5
```

Belirli bir "grup" migrate'ini geri almak için `rollback` komutuna `--batch` seçeneğini sağlayarak geri alabilirsiniz, `batch` seçeneği uygulamanızın migrate veritabanı tablosundaki bir grup değerine karşılık gelir. Örneğin, aşağıdaki komut, üçüncü gruptaki tüm migrate'leri geri alacaktır:

```shell
php artisan migrate:rollback --batch=3
```

Migrate tarafından çalıştırılacak SQL ifadelerini çalıştırmadan görmek isterseniz `migrate:rollback` komutuna `--pretend` seçeneğini ekleyebilirsiniz:

```shell
php artisan migrate:rollback --pretend
```

`migrate:reset` komutu, uygulamanın tüm migratelerini geri alacaktır:

```shell
php artisan migrate:reset
```

### Tek Bir Komutla Geri Alma ve Migrate

`migrate:refresh` komutu tüm migrate'leri geri alır ve ardından `migrate` komutunu çalıştırır. Bu komut tüm veritabanınızı etkili bir şekilde yeniden oluşturur:

```shell
php artisan migrate:refresh
 
# Veritabanını yenileyin ve tüm veritabanı seedlerini çalıştırın...
php artisan migrate:refresh --seed
```

`refresh` komutuna `--step` seçeneğini ekleyerek sınırlı sayıda migrate'i geri alabilir ve yeniden `migrate` yapabilirsiniz. Örneğin, aşağıdaki komut son beş migrate'i geri alır ve yeniden `migrate` eder:

```shell
php artisan migrate:refresh --step=5
```

### Bütün Tabloları Silme ve Migrate Etme

`migrate:fresh` komutu tüm tabloları veritabanından silecek ve ardından `migrate` komutunu çalıştıracaktır:

```shell
php artisan migrate:fresh
 
php artisan migrate:fresh --seed
```

Varsayılan olarak, `migrate:fresh` komutu yalnızca varsayılan veritabanı bağlantısındaki tabloları siler. Ancak, taşınması gereken veritabanı bağlantısını belirtmek için `--database` seçeneğini kullanabilirsiniz. Veritabanı bağlantı adı, uygulamanızın veritabanı yapılandırma dosyasında tanımlanan bir bağlantıya karşılık gelmelidir:

```shell
php artisan migrate:fresh --database=admin
```

>`migrate:fresh` komutu, öneklerine bakılmaksızın tüm veritabanı tablolarını silecektir. Bu komut, diğer uygulamalarla paylaşılan bir veritabanı üzerinde geliştirme yaparken dikkatli kullanılmalıdır.

# `#` Tablolar
---

## Tablo Oluşturma

Yeni bir veritabanı tablosu oluşturmak için, Schema facade'inin `create` metodunu kullanın. `create` metodu iki argüman kabul eder: birincisi tablonun adı, ikincisi ise yeni tabloyu tanımlamak için kullanılabilecek bir `Blueprint` nesnesi alan bir closure'dur:

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->string('email');
    $table->timestamps();
});
```

## Tablo ve Sütun Varlığı Koşulları

`hasTable`, `hasColumn` ve `hasIndex` metodlarını kullanarak bir tablo, sütun veya dizinin varlığını belirleyebilirsiniz:

```php
if (Schema::hasTable('users')) {
    // The "users" table exists...
}
 
if (Schema::hasColumn('users', 'email')) {
    // The "users" table exists and has an "email" column...
}
 
if (Schema::hasIndex('users', ['email'], 'unique')) {
    // The "users" table exists and has a unique index on the "email" column...
}
```

### Veritabanı Bağlantısı ve Tablo Ayarları

Uygulamanızın varsayılan bağlantısı olmayan bir veritabanı bağlantısı üzerinde bir şema işlemi gerçekleştirmek istiyorsanız, `connection` metodunu kullanın:

```php
Schema::connection('sqlite')->create('users', function (Blueprint $table) {
    $table->id();
});
```

Buna ek olarak, tablonun oluşturulmasının diğer yönlerini tanımlamak için birkaç özellik ve metod daha kullanılabilir. `engine` özelliği, MySQL kullanılırken tablonun depolama motorunu belirtmek için kullanılabilir:

```php
Schema::create('users', function (Blueprint $table) {
    $table->engine('InnoDB');
 
    // ...
});
```

`charset` ve `collation` özellikleri, MySQL kullanılırken oluşturulan tablo için karakter kümesini ve harmanlamayı belirtmek için kullanılabilir:

```php
Schema::create('users', function (Blueprint $table) {
    $table->charset('utf8mb4');
    $table->collation('utf8mb4_unicode_ci');
 
    // ...
});
```

Tablonun “geçici” olması gerektiğini belirtmek için `temporary` metodu kullanılabilir. Geçici tablolar yalnızca geçerli bağlantının veritabanı oturumu tarafından görülebilir ve bağlantı kapatıldığında otomatik olarak silinir:

```php
Schema::create('calculations', function (Blueprint $table) {
    $table->temporary();
 
    // ...
});
```

Bir veritabanı tablosuna “yorum” eklemek isterseniz, tablo örneği üzerinde `comment` metodunu çağırabilirsiniz. Tablo yorumları şu anda yalnızca MySQL ve PostgreSQL tarafından desteklenmektedir:

```php
Schema::create('calculations', function (Blueprint $table) {
    $table->comment('Business calculations');
 
    // ...
});
```

## Tablo Güncelleme

`Schema` facade'inin `table` metodu mevcut tabloları güncellemek için kullanılabilir. `Create` metodu gibi, `table` metodu da iki bağımsız değişken kabul eder: tablonun adı ve tabloya sütun veya dizin eklemek için kullanabileceğiniz bir `Blueprint` örneği alan bir closure:

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
Schema::table('users', function (Blueprint $table) {
    $table->integer('votes');
});
```

## Tabloları Yeniden İsimlendirme ve Silme

Mevcut bir veritabanı tablosunu yeniden adlandırmak için `rename` metodunu kullanın:

```php
use Illuminate\Support\Facades\Schema;
 
Schema::rename($from, $to);
```

Mevcut bir tabloyu silmek için `drop` veya `dropIfExists` metodlarını kullanabilirsiniz:

```php
Schema::drop('users');
 
Schema::dropIfExists('users');
```

### Foreign Key'li Tabloları Yeniden Adlandırma

Bir tabloyu yeniden adlandırmadan önce, Laravel'in kurallara dayalı bir isim atamasına izin vermek yerine, tablodaki herhangi bir yabancı anahtar kısıtlamasının geçiş dosyalarınızda açık bir isme sahip olduğunu doğrulamalısınız. Aksi takdirde, yabancı anahtar kısıtlama adı eski tablo adına atıfta bulunacaktır.

# `#` Sütunlar
---

## Sütun Oluşturma

`Schema` facade'inin `table` metodu, mevcut tabloları güncellemek için kullanılabilir. `create` metodu gibi `table` metodu da iki argüman kabul eder: tablonun adı ve tabloya sütun eklemek için kullanabileceğiniz bir `Illuminate\Database\Schema\Blueprint` örneği alan bir closure:

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
Schema::table('users', function (Blueprint $table) {
    $table->integer('votes');
});
```

### Mevcut Sütun Türleri

Schema oluşturucu planı, veritabanı tablolarınıza ekleyebileceğiniz farklı sütun türlerine karşılık gelen çeşitli metodlar sunar. Mevcut yöntemlerin her biri aşağıdaki tabloda listelenmiştir:

|                 |                      |                         |
| --------------- | -------------------- | ----------------------- |
| `bigIncrements` | `ipAddress`          | `timeTz`                |
| `bigInteger`    | `json`               | `time`                  |
| `binary`        | `jsonb`              | `timestampTz`           |
| `boolean`       | `longText`           | `timestamp`             |
| `char`          | `macAddress`         | `timestampsTz`          |
| `dateTimeTz`    | `mediumIncrements`   | `timestamps`            |
| `dateTime`      | `mediumInteger`      | `tinyIncrements`        |
| `date`          | `mediumText`         | `tinyInteger`           |
| `decimal`       | `morphs`             | `tinyText`              |
| `double`        | `nullableMorphs`     | `unsignedBigInteger`    |
| `enum`          | `nullableTimestamps` | `unsignedInteger`       |
| `float`         | `nullableUlidMorphs` | `unsignedMediumInteger` |
| `foreignId`     | `nullableUuidMorphs` | `unsignedSmallInteger`  |
| `foreignIdFor`  | `rememberToken`      | `unsignedTinyInteger`   |
| `foreignUlid`   | `set`                | `ulidMorphs`            |
| `foreignUuid`   | `smallIncrements`    | `uuidMorphs`            |
| `geography`     | `smallInteger`       | `ulid`                  |
| `geometry`      | `softDeletesTz`      | `uuid`                  |
| `id`            | `softDeletes`        | `year`                  |
| `increments`    | `string`             |                         |
| `integer`       | `text`               |                         |

## Sütun Düzenleyicileri

Yukarıda listelenen sütun türlerine ek olarak, bir veritabanı tablosuna sütun eklerken kullanabileceğiniz birkaç sütun “düzenleyici” vardır. Örneğin, sütunu “nullable” yapmak için `nullable` metodunu kullanabilirsiniz:

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
Schema::table('users', function (Blueprint $table) {
    $table->string('email')->nullable();
});
```

Aşağıdaki tabloda mevcut tüm sütun düzenleyicileri yer almaktadır. Bu liste index düzenleyicileri içermez:

| Düzenleyici                         | Açıklama                                                                                                 |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------- |
| `->after('column')`                 | Sütunu başka bir sütundan “sonra” yerleştirin (MySQL).                                                   |
| `->autoIncrement()`                 | INTEGER sütunlarını otomatik artan (birincil anahtar) olarak ayarlayın.<br>                              |
| `->charset('utf8mb4')`              | Sütun için bir karakter kümesi belirtin (MySQL).<br>                                                     |
| `->collation('utf8mb4_unicode_ci')` | Sütun için bir collation belirtin.                                                                       |
| `->comment('my comment')`           | Bir sütuna yorum ekleyin (MySQL / PostgreSQL).<br>                                                       |
| `->default($value)`                 | Sütun için bir “varsayılan” değer belirtin.<br>                                                          |
| `->first()`                         | Tabloya “first” sütununu yerleştirin (MySQL).<br>                                                        |
| `->from($integer)`                  | Otomatik artan bir alanın başlangıç değerini ayarlayın (MySQL / PostgreSQL).                             |
| `->invisible()`                     | Sütunu SELECT * sorguları için “görünmez” yapın (MySQL).                                                 |
| `->nullable($value = true)`         | NULL değerlerin sütuna eklenmesine izin verin.<br>                                                       |
| `->storedAs($expression)`           | Depolanmış oluşturulmuş bir sütun oluşturun (MySQL / PostgreSQL / SQLite).                               |
| `->unsigned()`                      | INTEGER sütunlarını UNSIGNED olarak ayarlayın (MySQL).                                                   |
| `->useCurrent()`                    | TIMESTAMP sütunlarını varsayılan değer olarak CURRENT_TIMESTAMP kullanacak şekilde ayarlayın.            |
| `->useCurrentOnUpdate()`            | Bir kayıt güncellendiğinde TIMESTAMP sütunlarını CURRENT_TIMESTAMP kullanacak şekilde ayarlayın (MySQL). |
| `->virtualAs($expression)`          | Sanal olarak oluşturulmuş bir sütun oluşturun (MySQL / SQLite).                                          |
| `->generatedAs($expression)`        | Belirtilen sıra seçenekleriyle bir kimlik sütunu oluşturun (PostgreSQL).                                 |
| `->always()`                        | Bir kimlik sütunu için girdi yerine sıra değerlerinin önceliğini tanımlar (PostgreSQL).                  |

`default` düzenleyici bir değeri veya bir `Illuminate\Database\Query\Expression` örneğini kabul eder. Bir `Expression` örneği kullanmak Laravel'in değeri tırnak içine almasını engeller ve veritabanına özel fonksiyonları kullanmanıza izin verir. Bunun özellikle yararlı olduğu bir durum, JSON sütunlarına varsayılan değerler atamanız gerektiğinde ortaya çıkar:

```php
<?php
 
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Query\Expression;
use Illuminate\Database\Migrations\Migration;
 
return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('flights', function (Blueprint $table) {
            $table->id();
            $table->json('movies')->default(new Expression('(JSON_ARRAY())'));
            $table->timestamps();
        });
    }
};
```

>Varsayılan ifadeler için destek, veritabanı sürücünüze, veritabanı sürümünüze ve alan türüne bağlıdır.

### Sütun Sıralama

MySQL veritabanı kullanılırken, şemadaki mevcut bir sütundan sonra sütun eklemek için `after` metodu kullanılabilir:

```php
$table->after('password', function (Blueprint $table) {
    $table->string('address_line1');
    $table->string('address_line2');
    $table->string('city');
});
```

## Sütun Güncelleme

`change` metodu, mevcut sütunların türünü ve niteliklerini güncellemenize olanak tanır. Örneğin, bir dize sütununun boyutunu artırmak isteyebilirsiniz. `change` metodunu çalışırken görmek için, `name` sütununun boyutunu 25'ten 50'ye çıkaralım. Bunu gerçekleştirmek için, sütunun yeni durumunu tanımlamamız ve ardından `change` metodunu çağırmamız yeterlidir:

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('name', 50)->change();
});
```

Bir sütunu güncellerken, sütun tanımında tutmak istediğiniz tüm düzenleyicileri açıkça eklemelisiniz - eksik olan herhangi bir nitelik silinecektir. Örneğin, `unsigned`, `default` ve `comment` niteliklerini korumak için, sütunu değiştirirken her bir düzenleyiciyi açıkça çağırmanız gerekir:

```php
Schema::table('users', function (Blueprint $table) {
    $table->integer('votes')->unsigned()->default(1)->comment('my comment')->change();
});
```

`change` metodu sütunun indexlerini değiştirmez. Bu nedenle, sütunu değiştirirken bir dizini açıkça eklemek veya kaldırmak için index düzenleyicileri kullanabilirsiniz:

```php
// Add an index...
$table->bigIncrements('id')->primary()->change();
 
// Drop an index...
$table->char('postal_code', 10)->unique(false)->change();
```

## Sütunları Yeniden İsimlendirme

Bir sütunu yeniden adlandırmak için Schema oluşturucu tarafından sağlanan `renameColumn` metodunu kullanabilirsiniz:

```php
Schema::table('users', function (Blueprint $table) {
    $table->renameColumn('from', 'to');
});
```

## Sütunları Silme

Bir sütunu silmek için şema oluşturucudaki `dropColumn` metodunu kullanabilirsiniz:

```php
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn('votes');
});
```

`dropColumn` metoduna bir dizi geçirerek sütun isimleri ileterek bir tablodan birden fazla sütun silebilirsiniz:

```php
Schema::table('users', function (Blueprint $table) {
    $table->dropColumn(['votes', 'avatar', 'location']);
});
```

## Mevcut Komutların Kısa Adları

Laravel, yaygın sütun türlerini silmek için birkaç kullanışlı metod sağlar. Bu metodların her biri aşağıdaki tabloda açıklanmıştır:

| Metod                            | Açıklama                                              |
| -------------------------------- | ----------------------------------------------------- |
| `$table->dropMorphs('morphable');` | `morphable_id` ve `morphable_type` sütunlarını silin. |
| `$table->dropRememberToken();`     | `remember_token` sütununu silin.                      |
| `$table->dropSoftDeletes();`       | `deleted_at` sütununu silin.                          |
| `$table->dropSoftDeletesTz();`     | `dropSoftDeletes()` metodunun kısa adı.               |
| `$table->dropTimestamps();`        | `created_at` ve `updated_at` sütunlarını silin.       |
| `$table->dropTimestampsTz();`      | `dropTimestamps()` metodunun kısa adı.                |

# `#` Indexler
---

## Index Oluşturma

Laravel Schema oluşturucu çeşitli index türlerini destekler. Aşağıdaki örnek yeni bir `email` sütunu oluşturmakta ve değerlerinin benzersiz olması gerektiğini belirtmektedir. Index'i oluşturmak için, `unique` metodunu sütun tanımına zincirleyebiliriz:

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
Schema::table('users', function (Blueprint $table) {
    $table->string('email')->unique();
});
```

Alternatif olarak, sütunu tanımladıktan sonra dizini oluşturabilirsiniz. Bunu yapmak için, Schema oluşturucu planında `unique` metodunu çağırmalısınız. Bu metod, benzersiz bir dizin alması gereken sütunun adını kabul eder:

```php
$table->unique('email');
```

Bileşik (veya kompozit) bir index oluşturmak için bir `index` metoduna bir dizi sütun bile aktarabilirsiniz:

```php
$table->index(['account_id', 'created_at']);
```

Bir index oluştururken, Laravel tabloya, sütun adlarına ve indeks türüne göre otomatik olarak bir indeks adı oluşturacaktır, ancak indeks adını kendiniz belirtmek için metoda ikinci bir argüman iletebilirsiniz:

```php
$table->unique('email', 'unique_email');
```

## Mevcut Index Türleri

Laravel'in `Schema` oluşturucu `Blueprint` sınıfı, Laravel tarafından desteklenen her indeks tipini oluşturmak için metotlar sağlar. Her indeks metodu, indeksin adını belirtmek için isteğe bağlı ikinci bir argüman kabul eder. Eğer atlanırsa, isim indeks için kullanılan tablo ve sütun(lar)ın isimlerinden ve indeks tipinden türetilecektir. Mevcut indeks metodlarının her biri aşağıdaki tabloda açıklanmaktadır:

| Metod                                          | Açıklama                                                           |
| ---------------------------------------------- | ------------------------------------------------------------------ |
| `$table->primary('id');`                         | Bir primary key (birincil anahtar) ekler                           |
| `$table->primary(['id', 'parent_id']);`          | Bileşik (veya Kompozit) composite keys (kompozit anahtarlar) ekler |
| `$table->unique('email');`                       | Bir unique (benzersiz) index ekler                                 |
| `$table->index('state');`                        | Bir index ekler                                                    |
| `$table->fullText('body');`                      | Tam metin index ekler (MySQL / PostgreSQL).                        |
| `$table->fullText('body')->language('english');` | Spesifik bir dilde tam metin bir index ekler (Postgre SQL)         |
| `$table->spatialIndex('location');`              | Bir uzamsal dizin ekler (SQLite hariç)                             |

## Indexlerin Yeniden İsimlendirilmesi

Bir dizini yeniden adlandırmak için, `Schema` oluşturucu `Blueprint`'i tarafından sağlanan `renameIndex` metodunu kullanabilirsiniz. Bu metod, ilk bağımsız değişkeni olarak geçerli dizin adını ve ikinci bağımsız değişkeni olarak istenen adı kabul eder:

```php
$table->renameIndex('from', 'to')
```

## Indexleri Silme

Bir indeksi silmek için, indeksin adını belirtmelisiniz. Varsayılan olarak, Laravel tablo adını, indekslenen sütunun adını ve indeks türünü temel alarak otomatik olarak bir indeks adı siler. İşte bazı örnekler:

| Metod                                                    | Açıklama                                     |
| -------------------------------------------------------- | -------------------------------------------- |
| `$table->dropPrimary('users_id_primary');`               | "users" tablosunun primary key'i silinir     |
| `$table->dropUnique('users_email_unique');`              | "users" tablosunun unique key'i silinir      |
| `$table->dropIndex('geo_state_index');`                  | "geo" tablosunun index'i silinir             |
| `$table->dropFullText('posts_body_fulltext');`           | "posts" tablosunun Tam metin index'i silinir |
| `$table->dropSpatialIndex('geo_location_spatialindex');` | "goe" tablosunun spatial indexi'i silinir    |

Indexleri silen bir metoda bir dizi sütun geçirirseniz, tablo adı, sütunlar ve dizin türüne göre geleneksel dizin adı oluşturulur:

```php
Schema::table('geo', function (Blueprint $table) {
    $table->dropIndex(['state']); // Drops index 'geo_state_index'
});
```

## Foreign Key Tanımlama

Laravel ayrıca veritabanı seviyesinde referans bütünlüğü için kullanılan yabancı anahtar (foreign key) sınırlamaları oluşturmak için destek sağlar. Örneğin, `posts` tablosunda `users` tablosundaki `id` sütununa referans veren bir `user_id` sütunu tanımlayalım:

```php
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
 
Schema::table('posts', function (Blueprint $table) {
    $table->unsignedBigInteger('user_id');
 
    $table->foreign('user_id')->references('id')->on('users');
});
```

Bu sözdizimi oldukça ayrıntılı olduğundan, Laravel daha iyi bir geliştirici deneyimi sağlamak için konvansiyonları kullanan ek, daha ters metodlar sağlar. Sütununuzu oluşturmak için `foreignId` metodunu kullanırken, yukarıdaki örnek şu şekilde yeniden yazılabilir:

```php
Schema::table('posts', function (Blueprint $table) {
    $table->foreignId('user_id')->constrained();
});
```

`foreignId` metodu `UNSIGNED BIGINT` bir sütun oluştururken, `constrained` metodu başvurulan tablo ve sütunu belirlemek için kuralları kullanacaktır. Eğer tablo adınız Laravel'in kurallarına uymuyorsa, bunu `constrained` metoduna manuel olarak sağlayabilirsiniz. Ek olarak, oluşturulan indekse atanması gereken isim de belirtilebilir:

```php
Schema::table('posts', function (Blueprint $table) {
    $table->foreignId('user_id')->constrained(
        table: 'users', indexName: 'posts_user_id'
    );
});
```

Kısıtlamanın “silindiğinde” ve “güncellendiğinde” özellikleri için istenen eylemi de belirtebilirsiniz:

```php
$table->foreignId('user_id')
      ->constrained()
      ->onUpdate('cascade')
      ->onDelete('cascade');
```

Bu eylemler için alternatif, anlamlı bir sözdizimi de sağlanmıştır:

| Method                      | Açıklama                                                                |
| --------------------------- | ----------------------------------------------------------------------- |
| `$table->cascadeOnUpdate();`  | Güncellemeler kademeli olarak yapılmalıdır.                             |
| `$table->restrictOnUpdate();` | Güncellemeler kısıtlanmalıdır.<br>                                      |
| `$table->noActionOnUpdate();` | Güncellemelerde bir eylem yapılmamalı.                                  |
| `$table->cascadeOnDelete();`  | Silmeler kademeli olmalıdır.                                            |
| `$table->restrictOnDelete();` | Silme işlemleri kısıtlanmalıdır.                                        |
| `$table->nullOnDelete();`     | Silme işlemleri yabancı anahtar değerini null olarak ayarlamalıdır.<br> |

Tüm ek sütun düzenleyicileri `constrained` metodundan önce çağrılmalıdır:

```php
$table->foreignId('user_id')
      ->nullable()
      ->constrained();
```

### Froeign Key Silme

Bir yabancı anahtarı bırakmak için `dropForeign` metodu kullanabilir ve silinecek yabancı anahtar kısıtlamasının adını bağımsız değişken olarak aktarabilirsiniz. Yabancı anahtar kısıtlamaları, dizinlerle aynı adlandırma kuralını kullanır. Başka bir deyişle, yabancı anahtar kısıtlamasının adı tablonun ve kısıtlamadaki sütunların adını temel alır ve ardından bir “`_foreign`” soneki gelir:


```php
$table->dropForeign('posts_user_id_foreign');
```

Alternatif olarak, `dropForeign` metoduna yabancı anahtarı tutan sütun adını içeren bir dizi iletebilirsiniz. Dizi, Laravel'in kısıtlama adlandırma kurallarını kullanarak bir yabancı anahtar kısıtlama adına dönüştürülecektir:

```php
$table->dropForeign(['user_id']);
```