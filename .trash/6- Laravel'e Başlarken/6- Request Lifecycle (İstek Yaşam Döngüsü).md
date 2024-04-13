"Gerçek dünyada" herhangi bir aracı kullanırken, o aracın nasıl çalıştığını anlarsanız kendinizi daha güvende hissedersiniz. Uygulama geliştirme de farklı değildir. Geliştirme araçlarınızın nasıl çalıştığını anladığınızda, onları kullanırken kendinizi daha rahat ve güvende hissedersiniz.

Bu belgenin amacı size Laravel frameworkünün nasıl çalıştığına dair iyi ve üst düzey bir genel bakış sunmaktır. Genel frameworkü daha iyi tanıyarak, her şey daha az "sihirli" hissettirecek ve uygulamalarınızı oluştururken kendinize daha çok güveneceksiniz. Eğer tüm terimleri hemen anlamazsanız, cesaretinizi kaybetmeyin! Sadece neler olup bittiğine dair temel bir kavrayış edinmeye çalışın ve dokümantasyonun diğer bölümlerini keşfettikçe bilginiz artacaktır.

## Yaşam Döngüsüne Genel Bakış
---

#### İlk Adım
---
Bir Laravel uygulamasına gelen tüm isteklerin giriş noktası `public/index.php` dosyasıdır. Tüm istekler web sunucunuzun (Apache / Nginx) yapılandırması tarafından bu dosyaya yönlendirilir. `index.php` dosyası çok fazla kod içermez. Daha ziyade, frameworkün geri kalanını yüklemek için bir başlangıç noktasıdır.

`index.php` dosyası Composer tarafından oluşturulan otomatik yükleyici tanımını yükler ve ardından `bootstrap/app.php`'den Laravel uygulamasının bir örneğini alır. Laravel'in kendisi tarafından gerçekleştirilen ilk eylem, uygulama / service containerına (hizmet kapsayıcısı) bir örneğini oluşturmaktır.

#### HTTP / Console Çekirdeği
---
Ardından, gelen istek, uygulamaya giren isteğin türüne bağlı olarak HTTP çekirdeğine veya konsol çekirdeğine gönderilir. Bu iki çekirdek, tüm isteklerin aktığı merkezi konum olarak hizmet eder. Şimdilik sadece `app/Http/Kernel.php` içinde bulunan HTTP çekirdeğine odaklanalım.

HTTP çekirdeği, istek yürütülmeden önce çalıştırılacak bir dizi önyükleyici tanımlayan `Illuminate\Foundation\Http\Kernel` sınıfını genişletir. Bu bootstrappers hata işlemeyi yapılandırır, günlüğü yapılandırır, uygulama ortamını tespit eder ve istek gerçekten işlenmeden önce yapılması gereken diğer görevleri yerine getirir. Tipik olarak, bu sınıflar endişelenmeniz gerekmeyen dahili Laravel yapılandırmasını ele alır.

HTTP çekirdeği ayrıca tüm isteklerin uygulama tarafından işlenmeden önce geçmesi gereken bir HTTP middleware listesi tanımlar. Bu middlewarelar HTTP oturumunu okuma ve yazma, uygulamanın bakım modunda olup olmadığını belirleme, CSRF belirtecini doğrulama ve daha fazlasını gerçekleştirir. Bunlar hakkında yakında daha fazla konuşacağız.

HTTP çekirdeğinin `handle` metotu için yöntem imzası oldukça basittir: bir İstek alır ve bir Yanıt döndürür. Çekirdeği, tüm uygulamanızı temsil eden büyük bir kara kutu olarak düşünün. Onu HTTP istekleriyle besleyin ve o da HTTP yanıtları döndürsün.

#### Service Providers (Hizmet Sağlayıclar)
---
En önemli çekirdek önyükleme eylemlerinden biri, uygulamanız için service providersı (hizmet sağlayıcıları) yüklemektir. Service Providers, database (veritabanı), queue (kuyruk), validation (doğrulama) ve routing (yönlendirme) bileşenleri gibi frameworkün çeşitli bileşenlerinin önyüklenmesinden sorumludur. Uygulama için tüm service providerslar (hizmet sağlayıcılar) `config/app.php` yapılandırma dosyasının `providers` dizisinde yapılandırılır.

Laravel bu sağlayıcı listesini yineleyecek ve her birini örnekleyecektir. Sağlayıcıları örnekledikten sonra, `register` metodu tüm providers (sağlayıcılar) üzerinde çağrılacaktır. Daha sonra, tüm providerslar (sağlayıcılar) kaydedildikten sonra, `boot` metodu her bir provider (sağlayıcı) üzerinde çağrılacaktır. Bunun nedeni, service providersların (hizmet sağlayıcıların), `boot` metotları çalıştırıldığında her kapsamın bağının kayıtlı ve kullanılabilir olmasına bağlı olabilmesidir.

Esasen Laravel tarafından sunulan her önemli özellik bir service providers (hizmet sağlayıcı) tarafından önyüklenir ve yapılandırılır. Framework tarafından sunulan pek çok özelliği önyükledikleri ve yapılandırdıkları için, service provider (hizmet sağlayıcılar) tüm Laravel önyükleme sürecinin en önemli yönüdür.

#### Routing (Yönlendirme)
---
Uygulamanızdaki en önemli service providerslardan (hizmet sağlayıcılardan) biri `App\Providers\RouteServiceProvider`'dır. Bu service providers (hizmet sağlayıcı), uygulamanızın `routes` dizininde bulunan rota dosyalarını yükler. `RouteServiceProvider` kodunu açın ve nasıl çalıştığına bir göz atın!

Uygulama önyüklendikten ve tüm service providers (hizmet sağlayıcılar) kaydedildikten sonra, `Request` (İstek) gönderilmek üzere routere (yönlendiriciye) teslim edilecektir. Router, isteği bir rotaya veya denetleyiciye gönderecek ve rotaya özgü middlewareleri çalıştıracaktır.

Middleware, uygulamanıza giren HTTP isteklerini filtrelemek veya incelemek için uygun bir mekanizma sağlar. Örneğin, Laravel, uygulamanızın kullanıcısının kimliğinin doğrulanmış olup olmadığını doğrulayan bir middleware yazılımı içerir. Eğer kullanıcının kimliği doğrulanmamışsa, middleware kullanıcıyı giriş ekranına yönlendirecektir. Ancak, kullanıcının kimliği doğrulanmışsa, middleware isteğin uygulamada daha fazla ilerlemesine izin verecektir. 

Bazı middlewareler, HTTP çekirdeğinizin `$middleware` özelliğinde tanımlananlar gibi uygulama içindeki tüm rotalara atanırken, bazıları yalnızca belirli rotalara veya rota gruplarına atanır.

İstek, eşleşen rotanın atanmış tüm middlewarelerden geçerse, route veya controller metotu yürütülür ve route veya controller metotu tarafından döndürülen yanıt, rotanın middleware zinciri aracılığıyla geri gönderilir.

#### Bitiş
---
Route veya controller metotu bir yanıt döndürdüğünde, yanıt rotanın middlewarei aracılığıyla dışarıya doğru geri gider ve uygulamaya giden yanıtı değiştirme veya inceleme şansı verir.

Son olarak, yanıt middleware üzerinden geri döndüğünde, HTTP çekirdeğinin `handle` metotu yanıt nesnesini döndürür ve `index.php` dosyası döndürülen yanıt üzerinde `send` metotunu çağırır. `send` metodu yanıt içeriğini kullanıcının web tarayıcısına gönderir. Tüm Laravel istek yaşam döngüsü boyunca yolculuğumuzu tamamladık!

## Service Providerslara (Hizmet Sağlayıcılarına) Odaklanın
---
Service providers bir Laravel uygulamasını önyüklemenin gerçek anahtarıdır. Uygulama örneği oluşturulur, service providers kaydedilir ve istek bootstrapped uygulamaya iletilir. Gerçekten bu kadar basit!

Bir Laravel uygulamasının service providers aracılığıyla nasıl oluşturulduğunu ve önyüklendiğini kavramak çok değerlidir. Uygulamanızın varsayılan hizmet sağlayıcıları `app/Providers` dizininde saklanır.

Varsayılan olarak `AppServiceProvider` oldukça boştur. Bu provider, uygulamanızın kendi önyükleme ve service container (hizmet kapsayıcı) bağlarını eklemek için harika bir yerdir. Büyük uygulamalar için, her biri uygulamanız tarafından kullanılan belirli hizmetler için daha ayrıntılı önyükleme içeren birkaç service providers (hizmet sağlayıcı) oluşturmak isteyebilirsiniz.