# `#` Giriş
---

Varsayılan Laravel uygulama yapısı hem büyük hem de küçük uygulamalar için harika bir başlangıç noktası sağlamak üzere tasarlanmıştır. Ancak uygulamanızı istediğiniz gibi düzenlemekte özgürsünüz. Laravel, Composer sınıfı otomatik olarak yükleyebildiği sürece, herhangi bir sınıfın nerede bulunduğu konusunda neredeyse hiçbir kısıtlama getirmez.

# `#` Kök (Root) Dizini
---

## App Dizini

`app` dizini uygulamanızın çekirdek kodunu içerir. Bu dizini yakında daha ayrıntılı olarak inceleyeceğiz; ancak, uygulamanızdaki sınıfların neredeyse tamamı bu dizinde olacaktır.

## Bootstrap Dizini

`bootstrap` dizini, frameworkü önyükleyen `app.php` dosyasını içerir. Bu dizin aynı zamanda rota ve hizmetler önbellek dosyaları gibi performans optimizasyonu için framework tarafından oluşturulan dosyaları içeren bir `cache` dizini barındırır.

## Config Dizini

`config` dizini, adından da anlaşılacağı gibi, uygulamanızın tüm yapılandırma dosyalarını içerir. Bu dosyaların tümünü okumak ve kullanabileceğiniz tüm seçeneklere aşina olmak harika bir fikirdir.

## Database Dizini

`database` dizini veritabanı migrationslarınızı, model factorieslerinizi ve seedlerinizi içerir. Dilerseniz bu dizini bir SQLite veritabanı tutmak için de kullanabilirsiniz.

## Public Dizini

`public` dizini, uygulamanıza giren tüm isteklerin giriş noktası olan ve otomatik yüklemeyi yapılandıran `index.php` dosyasını içerir. Bu dizin ayrıca resimler, JavaScript ve CSS gibi varlıklarınızı da barındırır.

## Resources Dizini

`resources` dizini, views (görünümlerinizin) yanı sıra CSS veya JavaScript gibi ham, derlenmemiş varlıklarınızı da içerir.

## Routes Dizini

`routes` dizini uygulamanız için tüm route (rota) tanımlarını içerir. Varsayılan olarak, Laravel'e iki rota dosyası dahildir: `web.php` ve `console.php`.

`web.php` dosyası Laravel'in session (oturum) durumu, CSRF koruması ve cookie encryption (çerez şifrelemesi) sağlayan `web` middleware grubuna yerleştirdiği rotaları içerir. Eğer uygulamanız durumsuz, RESTful bir API sunmuyorsa, o zaman tüm rotalarınız büyük olasılıkla `web.php` dosyasında tanımlanacaktır.

`console.php` dosyası, tüm closure based konsol komutlarınızı tanımlayabileceğiniz yerdir. Her closure bir komut örneğine bağlıdır ve her komutun IO yöntemleriyle etkileşim kurmak için basit bir yaklaşım sağlar. Bu dosya HTTP rotalarını tanımlamasa da, uygulamanıza konsol tabanlı giriş noktalarını (rotaları) tanımlar.

İsteğe bağlı olarak, `install:api` ve `install:broadcasting` Artisan komutları aracılığıyla API rotaları (`api.php`) ve yayın kanalları (`channels.php`) için ek rota dosyaları yükleyebilirsiniz.

`api.php` dosyası, durumsuz olması amaçlanan rotalar içerir, bu nedenle bu rotalar aracılığıyla uygulamaya giren isteklerin belirteçler aracılığıyla doğrulanması amaçlanır ve oturum durumuna erişemez.

`channels.php` dosyası, uygulamanızın desteklediği tüm event broadcasting kanallarını kaydedebileceğiniz yerdir.

## Storage Dizini

Depolama dizini logs (günlüklerinizi), derlenmiş Blade şablonlarını, dosya tabanlı sessions (oturumları), dosya cache (önbelleklerini) ve framework tarafından oluşturulan diğer dosyaları içerir. Bu dizin, `app`, `framework` ve `logs` dizinleri olarak ayrılmıştır. `app` dizini, uygulamanız tarafından oluşturulan tüm dosyaları depolamak için kullanılabilir. Framework dizini, `framework` tarafından oluşturulan dosyaları ve cache’leri (önbellekleri) depolamak için kullanılır. Son olarak, `logs` dizini uygulamanızın günlük dosyalarını içerir.

`storage/app/public` dizini, profil avatarları gibi kullanıcı tarafından oluşturulan ve herkesin erişimine açık olması gereken dosyaları depolamak için kullanılabilir. `public/storage` adresinde bu dizini işaret eden bir sembolik bağlantı oluşturmalısınız. Bağlantıyı `php artisan storage:link` Artisan komutunu kullanarak oluşturabilirsiniz.

## Tests Dizini

`test` dizini otomatik testlerinizi içerir. Örnek Pest veya PHPUnit birim testleri ve özellik testleri kutudan çıkar çıkmaz sağlanır. Her test sınıfının sonuna `Test` kelimesi eklenmelidir. Testlerinizi `/vendor/bin/pest` veya `/vendor/bin/phpunit` komutlarını kullanarak çalıştırabilirsiniz. Ya da test sonuçlarınızın daha detaylı ve güzel bir gösterimini istiyorsanız `php artisan test` Artisan komutunu kullanarak testlerinizi çalıştırabilirsiniz.

## Vendor Dizini

`vendor` dizini Composer bağımlılıklarınızı içerir.

# `#` Uygulama (App) Dizini
---

Uygulamanızın büyük bir kısmı `app` dizininde yer alır ve PSR-4 otomatik yükleme standardı kullanılarak Composer tarafından otomatik yüklenir.

Varsayılan olarak, `app` dizini `Http`, `Models` ve `Providers` dizinlerini içerir. Ancak zaman içinde, sınıfları oluşturmak için `make` Artisan komutlarını kullandıkça `app` dizini içinde çeşitli başka dizinler de oluşturulacaktır. Örneğin, bir komut sınıfı oluşturmak için `make:command` Artisan komutunu çalıştırana kadar `app/Console` dizini mevcut olmayacaktır.

Hem `Console` hem de `Http` dizinleri aşağıdaki ilgili bölümlerde daha ayrıntılı olarak açıklanmıştır, ancak `Console` ve `Http` dizinlerini uygulamanızın çekirdeğine bir API sağlamak olarak düşünün. HTTP protokolü ve CLI, uygulamanızla etkileşime geçmek için kullanılan mekanizmalardır, ancak aslında uygulama mantığı içermezler. Başka bir deyişle, uygulamanıza komut vermenin iki yoludur. `Console` dizini tüm Artisan komutlarınızı içerirken, `Http` dizini controller’ları, middleware’leri (ara yazılımları) ve isteklerinizi içerir.

Uygulama dizinindeki sınıfların çoğu Artisan tarafından komutlar aracılığıyla oluşturulabilir. Mevcut komutları incelemek için terminalinizde `php artisan list make` komutunu çalıştırın.

## Broadcasting Dizini

`Broadcasting` dizini, uygulamanız için tüm yayın kanalı sınıflarını içerir. Bu sınıflar `make:channel` komutu kullanılarak oluşturulur. Bu dizin varsayılan olarak mevcut değildir, ancak ilk kanalınızı oluşturduğunuzda sizin için oluşturulacaktır.

## Console Dizini

`Console` dizini, uygulamanız için tüm özel Artisan komutlarını içerir. Bu komutlar `make:command` komutu kullanılarak oluşturulabilir.

## Events Dizini

Bu dizin varsayılan olarak mevcut değildir, ancak `event:generate` ve `make:event` Artisan komutları tarafından sizin için oluşturulacaktır. `Events` dizini olay sınıflarını barındırır. Olaylar, uygulamanızın diğer bölümlerini belirli bir eylemin gerçekleştiği konusunda uyarmak için kullanılabilir ve büyük ölçüde esneklik ve ayrıştırma sağlar.

## Exceptions Dizini

`Exceptions` dizini, uygulamanız için tüm özel istisnaları içerir. Bu istisnalar `make:exception` komutu kullanılarak oluşturulabilir.

## Http Dizini

`Http` dizini denetleyicilerinizi, middleware’leri ve form isteklerinizi içerir. Uygulamanıza giren istekleri işleme mantığının neredeyse tamamı bu dizine yerleştirilecektir.

## Jobs Dizini

Bu dizin varsayılan olarak mevcut değildir, ancak `make:job` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Jobs` dizini, uygulamanız için kuyruğa alınabilir işleri barındırır. İşler uygulamanız tarafından kuyruğa alınabilir veya geçerli istek yaşam döngüsü içinde eşzamanlı olarak çalıştırılabilir. Geçerli istek sırasında eşzamanlı olarak çalışan işler, komut modelinin bir uygulaması olduklarından bazen "komutlar" olarak adlandırılırlar.

## Listeners Dizini

Bu dizin varsayılan olarak mevcut değildir, ancak `event:generate` veya `make:listener` Artisan komutlarını çalıştırırsanız sizin için oluşturulacaktır. `Listeners` dizini, event’lerinizi (olaylarınızı) işleyen sınıfları içerir. Event Listeners (Olay Dinleyicileri) bir olay örneği alır ve ateşlenen olaya yanıt olarak mantık yürütür. Örneğin, bir `KullaniciKayitOldu` event’i (olayı) bir `HosgeldinEpostasiGonder` dinleyicisi tarafından işlenebilir.

## Mail Dizini

Bu dizin varsayılan olarak mevcut değildir, ancak `make:mail` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Mail` dizini, uygulamanız tarafından gönderilen e-postaları temsil eden tüm sınıflarınızı içerir. `Mail` nesneleri, `Mail::send` `metotu` kullanılarak gönderilebilecek tek ve basit bir sınıfta bir e-posta oluşturmanın tüm mantığını kapsüllemenize olanak tanır.

## Models Dizini

`Models` dizini tüm Eloquent model sınıflarınızı içerir. Laravel ile birlikte gelen Eloquent ORM, veritabanınızla çalışmak için güzel, basit bir ActiveRecord uygulaması sağlar. Her veritabanı tablosunun, o tablo ile etkileşim için kullanılan karşılık gelen bir "Model" vardır. Model’ler tablolarınızdaki verileri sorgulamanıza ve tabloya yeni kayıtlar eklemenize izin verir.

## Notifications Dizini

Bu dizin varsayılan olarak mevcut değildir, ancak `make:notification` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Notifications` dizini, uygulamanızda gerçekleşen event’ler (olaylar) hakkında basit bildirimler gibi uygulamanız tarafından gönderilen tüm "işlemsel" bildirimleri içerir. Laravel'in bildirim özelliği, e-posta, Slack, SMS gibi çeşitli sürücüler üzerinden bildirim göndermeyi veya bir veritabanında saklamayı soyutlar.

## Policies Dizini

Bu dizin varsayılan olarak mevcut değildir, ancak `make:policy` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Policies` dizini, uygulamanız için yetkilendirme ilkesi sınıflarını içerir. İlkeler, bir kullanıcının bir kaynağa karşı belirli bir eylemi gerçekleştirip gerçekleştiremeyeceğini belirlemek için kullanılır.

## Providers Dizini

`Providers` dizini, uygulamanız için tüm service providers’ları içerir. Service providers, providers’ları service container’ına bağlayarak, event’leri (olayları) kaydederek veya uygulamanızı gelen isteklere hazırlamak için diğer görevleri yerine getirerek uygulamanızı önyükler.

Yeni bir Laravel uygulamasında, bu dizin zaten `AppServiceProvider` içerecektir. Gerektiğinde bu dizine kendi providers’larınızı eklemekte özgürsünüz.

## Rules Dizini

Bu dizin varsayılan olarak mevcut değildir, ancak `make:rule` Artisan komutunu çalıştırırsanız sizin için oluşturulacaktır. `Rules` dizini, uygulamanız için özel doğrulama kuralı nesnelerini içerir. Kurallar, karmaşık doğrulama mantığını basit bir nesnede kapsüllemek için kullanılır.