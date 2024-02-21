Varsayılan Laravel uygulama yapısı hem büyük hem de küçük uygulamalar için harika bir başlangıç noktası sağlamak üzere tasarlanmıştır. Ancak uygulamanızı istediğiniz gibi düzenlemekte özgürsünüz. Laravel, Composer sınıfı otomatik olarak yükleyebildiği sürece, herhangi bir sınıfın nerede bulunduğu konusunda neredeyse hiçbir kısıtlama getirmez.

Başlıca proje dizinimiz ise şu şekildedir:

- Kök Dizini
    - `app` Dizini
    - `bootstrap` Dizini
    - `config` Dizini
    - `database` Dizini
    - `public` Dizini
    - `resources` Dizini
    - `routes` Dizini
    - `storage` Dizini
    - `tests` Dizini
    - `vendor` Dizini
- Uygulama Dizini
    - `Broadcasting` Dizini
    - `Console` Dizini
    - `Events` Dizini
    - `Exceptions` Dizini
    - `Http` Dizini
    - `Jobs` Dizini
    - `Listeners` Dizini
    - `Mail` Dizini
    - `Models` Dizini
    - `Notifications` Dizini
    - `Policies` Dizini
    - `Rules` Dizini

## Kök Dizini
---

#### `app` Dizini
---
`app` dizini uygulamanızın çekirdek kodunu içerir. Bu dizini yakında daha ayrıntılı olarak inceleyeceğiz; ancak, uygulamanızdaki sınıfların neredeyse tamamı bu dizinde olacaktır.

#### `bootstrap` Dizini
---
`bootstrap` dizini, çerçeveyi önyükleyen `app.php` dosyasını içerir. Bu dizin aynı zamanda rota ve hizmetler önbellek dosyaları gibi performans optimizasyonu için çerçeve tarafından oluşturulan dosyaları içeren bir `cache` dizini barındırır. Genellikle bu dizin içindeki herhangi bir dosyayı değiştirmeniz gerekmez.

#### `config` Dizini
---
`config` dizini, adından da anlaşılacağı gibi, uygulamanızın tüm yapılandırma dosyalarını içerir. Bu dosyaların tümünü okumak ve kullanabileceğiniz tüm seçeneklere aşina olmak harika bir fikirdir.

#### `database` Dizini
---
`database` dizini veritabanı geçişlerinizi, model fabrikalarınızı ve tohumlarınızı içerir. Dilerseniz bu dizini bir SQLite veritabanı tutmak için de kullanabilirsiniz.

#### `public` Dizini
---
`public` dizini, uygulamanıza giren tüm isteklerin giriş noktası olan ve otomatik yüklemeyi yapılandıran `index.php` dosyasını içerir. Bu dizin ayrıca resimler, JavaScript ve CSS gibi varlıklarınızı da barındırır.

#### `resources` Dizini
---
Kaynaklar dizini, görünümlerinizin yanı sıra CSS veya JavaScript gibi ham, derlenmemiş varlıklarınızı da içerir.

#### `routes` Dizini
---
`routes` dizini uygulamanız için tüm rota tanımlarını içerir. Varsayılan olarak, Laravel'e birkaç rota dosyası dahildir:` web.php`, `api.php`, `console.php` ve `channels.php`.

`web.php` dosyası, `RouteServiceProvider`'ın `web` ara yazılım grubuna yerleştirdiği ve oturum durumu, CSRF koruması ve çerez şifrelemesi sağlayan rotaları içerir. Uygulamanız durumsuz, RESTful bir API sunmuyorsa, tüm rotalarınız büyük olasılıkla `web.php` dosyasında tanımlanacaktır.

`api.php` dosyası, `RouteServiceProvider`'ın `api` ara yazılım grubuna yerleştirdiği rotaları içerir. Bu rotaların durumsuz olması amaçlanmıştır, bu nedenle bu rotalar aracılığıyla uygulamaya giren isteklerin belirteçler aracılığıyla doğrulanması ve oturum durumuna erişmemesi amaçlanmıştır.

`console.php` dosyası, tüm kapatma tabanlı konsol komutlarınızı tanımlayabileceğiniz yerdir. Her kapanış bir komut örneğine bağlıdır ve her komutun IO yöntemleriyle etkileşim kurmak için basit bir yaklaşım sağlar. Bu dosya HTTP rotalarını tanımlamasa da, uygulamanıza konsol tabanlı giriş noktalarını (rotaları) tanımlar.

`channels.php` dosyası, uygulamanızın desteklediği tüm olay yayın kanallarını kaydedebileceğiniz yerdir.

#### `storage` Dizini
---
`storage` dizini günlüklerinizi, derlenmiş Blade şablonlarını, dosya tabanlı oturumları, dosya önbelleklerini ve çerçeve tarafından oluşturulan diğer dosyaları içerir. Bu dizin, `app`, `framework` ve `logs` dizinleri olarak ayrılmıştır. `app` dizini, uygulamanız tarafından oluşturulan tüm dosyaları depolamak için kullanılabilir. `framework` dizini, framework tarafından oluşturulan dosyaları ve önbellekleri saklamak için kullanılır. Son olarak, `logs` dizini uygulamanızın günlük dosyalarını içerir.

`storage/app/public` dizini, profil avatarları gibi kullanıcı tarafından oluşturulan ve herkesin erişimine açık olması gereken dosyaları depolamak için kullanılabilir. `public/storage` adresinde bu dizini işaret eden bir sembolik bağlantı oluşturmalısınız. Bağlantıyı `php artisan storage:link` Artisan komutunu kullanarak oluşturabilirsiniz.

#### `tests` Dizini
---
`tests` dizini otomatik testlerinizi içerir. Örnek PHPUnit birim testleri ve özellik testleri kutudan çıkar çıkmaz sağlanır. Her test sınıfının sonuna Test kelimesi eklenmelidir. Testlerinizi `phpunit` veya `php vendor/bin/phpunit` komutlarını kullanarak çalıştırabilirsiniz. Ya da test sonuçlarınızın daha ayrıntılı ve güzel bir gösterimini istiyorsanız, `php artisan test` Artisan komutunu kullanarak testlerinizi çalıştırabilirsiniz.

#### `vendor` Dizini
---
`vendor` dizini Composer bağımlılıklarınızı içerir.

## Uygulama Dizini
---
Uygulamanızın büyük bir kısmı `app` dizininde yer alır. Varsayılan olarak, bu dizin `App` altında isimlendirilir ve PSR-4 otomatik yükleme standardı kullanılarak Composer tarafından otomatik yüklenir.

`app` dizini `Console`, `Http` ve `Providers` gibi çeşitli ek dizinler içerir. `Console` ve `Http` dizinlerini uygulamanızın çekirdeğine bir API sağlamak olarak düşünün. HTTP protokolü ve CLI, uygulamanızla etkileşim kurmak için kullanılan mekanizmalardır, ancak aslında uygulama mantığı içermezler. Başka bir deyişle, uygulamanıza komut vermenin iki yoludur. `Console` dizini tüm Artisan komutlarınızı içerirken, `Http` dizini denetleyicilerinizi, ara katman yazılımlarınızı ve isteklerinizi içerir.

Sınıfları oluşturmak için `make` Artisan komutlarını kullandıkça `app` dizini içinde çeşitli başka dizinler de oluşturulacaktır. Örneğin, bir iş sınıfı oluşturmak için `make:job` Artisan komutunu çalıştırana kadar `app/Jobs` dizini mevcut olmayacaktır.

>`app` dizinindeki sınıfların çoğu Artisan tarafından komutlar aracılığıyla oluşturulabilir. Mevcut komutları incelemek için terminalinizde `php artisan list make` komutunu çalıştırın.

#### `Broadcasting` Dizini
---
`Broadcasting` dizini, uygulamanız için tüm yayın kanalı sınıflarını içerir. Bu sınıflar `make:channel` komutu kullanılarak oluşturulur. Bu dizin varsayılan olarak mevcut değildir, ancak ilk kanalınızı oluşturduğunuzda sizin için oluşturulacaktır.

#### `Console` Dizini
---
Konsol dizini, uygulamanız için tüm özel Artisan komutlarını içerir. Bu komutlar `make:command` komutu kullanılarak oluşturulabilir. Bu dizin aynı zamanda özel Artisan komutlarınızın kaydedildiği ve zamanlanmış görevlerinizin tanımlandığı konsol çekirdeğinizi de barındırır.

#### `Events` Dizini
---
Bu dizin varsayılan olarak mevcut değildir, ancak `event:generate` ve `make:event` Artisan komutları tarafından sizin için oluşturulacaktır. Olaylar dizini olay sınıflarını barındırır. Olaylar, uygulamanızın diğer bölümlerini belirli bir eylemin gerçekleştiği konusunda uyarmak için kullanılabilir ve büyük ölçüde esneklik ve ayrıştırma sağlar.

#### `Exceptions` Dizini
---
`Exceptions` dizini uygulamanızın istisna işleyicisini içerir ve ayrıca uygulamanız tarafından atılan istisnaları yerleştirmek için iyi bir yerdir. İstisnalarınızın nasıl günlüğe kaydedileceğini veya işleneceğini özelleştirmek isterseniz, bu dizindeki `Handler` sınıfını değiştirmelisiniz.

#### `Http` Dizini
---
`Http` dizini denetleyicilerinizi, ara katman yazılımlarınızı ve form isteklerinizi içerir. Uygulamanıza giren istekleri işleme mantığının neredeyse tamamı bu dizine yerleştirilecektir.

#### `Jobs` Dizini
---
Bu dizin varsayılan olarak mevcut değildir, ancak `make:job` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Jobs` dizini, uygulamanız için kuyruğa alınabilir işleri barındırır. İşler uygulamanız tarafından kuyruğa alınabilir veya geçerli istek yaşam döngüsü içinde eşzamanlı olarak çalıştırılabilir. Geçerli istek sırasında eşzamanlı olarak çalışan işler, komut modelinin bir uygulaması olduklarından bazen "komutlar" olarak adlandırılırlar.

#### `Listeners` Dizini
---
Bu dizin varsayılan olarak mevcut değildir, ancak `event:generate` veya `make:listener` Artisan komutlarını çalıştırırsanız sizin için oluşturulacaktır. `Listeners` dizini, olaylarınızı işleyen sınıfları içerir. Olay dinleyicileri bir olay örneği alır ve ateşlenen olaya yanıt olarak mantık yürütür. Örneğin, bir `UserRegistered` olayı bir `SendWelcomeEmail` dinleyicisi tarafından işlenebilir.

#### `Mail` Dizini
---
Bu dizin varsayılan olarak mevcut değildir, ancak `make:mail` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Mail` dizini, uygulamanız tarafından gönderilen e-postaları temsil eden tüm sınıflarınızı içerir. Mail nesneleri, `Mail::send` yöntemi kullanılarak gönderilebilecek tek ve basit bir sınıfta bir e-posta oluşturmanın tüm mantığını kapsüllemenize olanak tanır.

#### `Models` Dizini
---
`Models` dizini tüm Eloquent model sınıflarınızı içerir. Laravel ile birlikte gelen Eloquent ORM, veritabanınızla çalışmak için güzel, basit bir ActiveRecord uygulaması sağlar. Her veritabanı tablosunun, o tablo ile etkileşim için kullanılan karşılık gelen bir "Modeli" vardır. Modeller tablolarınızdaki verileri sorgulamanıza ve tabloya yeni kayıtlar eklemenize izin verir.

#### `Notifications` Dizini
---
Bu dizin varsayılan olarak mevcut değildir, ancak `make:notification` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Notifications` dizini, uygulamanızda gerçekleşen olaylar hakkında basit bildirimler gibi uygulamanız tarafından gönderilen tüm "işlemsel" bildirimleri içerir. Laravel'in bildirim özelliği, e-posta, Slack, SMS gibi çeşitli sürücüler üzerinden bildirim göndermeyi veya bir veritabanında saklamayı soyutlar.

#### `Policies` Dizini
---
Bu dizin varsayılan olarak mevcut değildir, ancak `make:policy` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Policies` dizini, uygulamanız için yetkilendirme ilkesi sınıflarını içerir. İlkeler, bir kullanıcının bir kaynağa karşı belirli bir eylemi gerçekleştirip gerçekleştiremeyeceğini belirlemek için kullanılır.

#### `Providers` Dizini
---
`Providers` dizini, uygulamanız için tüm hizmet sağlayıcıları içerir. Hizmet sağlayıcılar, hizmetleri hizmet kapsayıcısına bağlayarak, olayları kaydederek veya uygulamanızı gelen isteklere hazırlamak için diğer görevleri yerine getirerek uygulamanızı önyükler.

Yeni bir Laravel uygulamasında, bu dizin zaten birkaç sağlayıcı içerecektir. Gerektiğinde bu dizine kendi sağlayıcılarınızı eklemekte özgürsünüz.

#### `Rules` Dizini
---
Bu dizin varsayılan olarak mevcut değildir, ancak `make:rule` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Rules` dizini, uygulamanız için özel doğrulama kuralı nesnelerini içerir. Kurallar, karmaşık doğrulama mantığını basit bir nesnede kapsüllemek için kullanılır.