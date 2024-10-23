# `#` Giriş
---
Laravel, seed sınıflarını kullanarak veritabanınızı veri ile doldurma yeteneğini içerir. Tüm seed sınıfları `database/seeders` dizininde saklanır. Varsayılan olarak, sizin için bir `DatabaseSeeder` sınıfı tanımlanmıştır. Bu sınıftan, seeding sırasını kontrol etmenize izin veren diğer seed sınıflarını çalıştırmak için `call` metodunu kullanabilirsiniz.

# `#` Seeder Oluşturma
---
Bir seeder oluşturmak için `make:seeder` Artisan komutunu çalıştırın. Framework tarafından üretilen tüm seeder `database/seeders` dizinine yerleştirilecektir:

```shell
php artisan make:seeder UserSeeder
```

Bir seeder sınıfı varsayılan olarak yalnızca bir metot içerir: `run`. Bu metot, `db:seed` Artisan komutu çalıştırıldığında çağrılır. `run` metodu içinde, veritabanınıza istediğiniz şekilde veri ekleyebilirsiniz. Verileri manuel olarak eklemek için sorgu oluşturucuyu kullanabilir veya Eloquent model faktöryellerini kullanabilirsiniz.

Örnek olarak, varsayılan `DatabaseSeeder` sınıfını değiştirelim ve `run` metoduna bir veritabanı ekleme deyimi ekleyelim:

```php
<?php
 
namespace Database\Seeders;
 
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Str;
 
class DatabaseSeeder extends Seeder
{
    /**
     * Run the database seeders.
     */
    public function run(): void
    {
        DB::table('users')->insert([
            'name' => Str::random(10),
            'email' => Str::random(10).'@example.com',
            'password' => Hash::make('password'),
        ]);
    }
```

> İhtiyacınız olan bağımlılıkları `run` metodunun parametresi içinde yazabilirsiniz. Bunlar otomatik olarak Laravel service container aracılığıyla çözülecektir.

## Model Factories'leri Kullanma

Elbette, her model çekirdeği için nitelikleri manuel olarak belirtmek zahmetlidir. Bunun yerine, büyük miktarda veritabanı kaydını uygun bir şekilde oluşturmak için model factorieslerini kullanabilirsiniz. Öncelikle, factorieslerinizi nasıl tanımlayacağınızı öğrenmek için factories belgelerini inceleyin.

Örneğin, her biri bir ilgili gönderiye sahip 50 kullanıcı oluşturalım:

```php
use App\Models\User;
 
/**
 * Run the database seeders.
 */
public function run(): void
{
    User::factory()
            ->count(50)
            ->hasPosts(1)
            ->create();
}
```

## Ek Seederleri Çağırma

`DatabaseSeeder` sınıfı içinde, ek seed sınıflarını çalıştırmak için `call` metodunu kullanabilirsiniz. `call` metodunu kullanmak, veritabanı seederlarınızı birden fazla dosyaya bölmenize olanak tanır, böylece tek bir seeder sınıfı çok büyük olmaz. `call` metodu, yürütülmesi gereken bir dizi seeder sınıfı kabul eder:

```php
/**
 * Run the database seeders.
 */
public function run(): void
{
    $this->call([
        UserSeeder::class,
        PostSeeder::class,
        CommentSeeder::class,
    ]);
}
```

## Model Eventlerini Susturma

Seederleri çalıştırırken, modellerin event göndermesini engellemek isteyebilirsiniz. `WithoutModelEvents` özelliğini kullanarak bunu başarabilirsiniz. `WithoutModelEvents` özelliği kullanıldığında, `call` metodu aracılığıyla ek seed sınıfları çalıştırılsa bile hiçbir model eventinin gönderilmemesini sağlar:

```php
<?php
 
namespace Database\Seeders;
 
use Illuminate\Database\Seeder;
use Illuminate\Database\Console\Seeds\WithoutModelEvents;
 
class DatabaseSeeder extends Seeder
{
    use WithoutModelEvents;
 
    /**
     * Run the database seeders.
     */
    public function run(): void
    {
        $this->call([
            UserSeeder::class,
        ]);
    }
}
```

# # Seederleri Yürütme
---
Veritabanınızı doldurmak için `db:seed` Artisan komutunu çalıştırabilirsiniz. Varsayılan olarak, `db:seed` komutu `Database\Seeders\DatabaseSeeder` sınıfını çalıştırır ve bu sınıf da diğer seed sınıflarını çağırabilir. Ancak, tek tek çalıştırılacak belirli bir seeder sınıfını belirtmek için `--class` seçeneğini kullanabilirsiniz:

```shell
php artisan db:seed 

php artisan db:seed --class=UserSeeder
```

Veritabanınızı `--seed` seçeneğiyle birlikte `migrate:fresh` komutunu kullanarak da doldurabilirsiniz; bu komut tüm tabloları silecek ve tüm migrationlarınızı yeniden çalıştıracaktır. Bu komut, veritabanınızı tamamen yeniden oluşturmak için kullanışlıdır. Çalıştırılacak belirli bir seeder belirtmek için `--seeder` seçeneği kullanılabilir:

```shell
php artisan migrate:fresh --seed

php artisan migrate:fresh --seed --seeder=UserSeeder
```

## Üretim Aşamasında Seederleri Zorlama

Bazı ekleme işlemleri verileri değiştirmenize veya kaybetmenize neden olabilir. Üretim aşamasında ki veritabanınıza karşı seed komutlarını çalıştırmaktan korunmak için, seeder üretim ortamında çalıştırılmadan önce sizden onay istenecektir. Seederler'i istem olmadan çalışmaya zorlamak için `--force` özelliğini kullanın:

```shell
php artisan db:seed --force
```














