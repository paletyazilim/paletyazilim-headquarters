"Gerçek dünyada" herhangi bir aracı kullanırken, o aracın nasıl çalıştığını anlarsanız kendinizi daha güvende hissedersiniz. Uygulama geliştirme de farklı değildir. Geliştirme araçlarınızın nasıl çalıştığını anladığınızda, onları kullanırken kendinizi daha rahat ve güvende hissedersiniz.

Bu belgenin amacı size Laravel çerçevesinin nasıl çalıştığına dair iyi ve üst düzey bir genel bakış sunmaktır. Genel çerçeveyi daha iyi tanıyarak, her şey daha az "sihirli" hissettirecek ve uygulamalarınızı oluştururken kendinize daha çok güveneceksiniz. Eğer tüm terimleri hemen anlamazsanız, cesaretinizi kaybetmeyin! Sadece neler olup bittiğine dair temel bir kavrayış edinmeye çalışın ve dokümantasyonun diğer bölümlerini keşfettikçe bilginiz artacaktır.

## Yaşam Döngüsüne Genel Bir Bakış
---

#### İlk Adım
---
Bir Laravel uygulamasına gelen tüm isteklerin giriş noktası `public/index.php` dosyasıdır. Tüm istekler web sunucunuzun (Apache / Nginx) yapılandırması tarafından bu dosyaya yönlendirilir. `index.php` dosyası çok fazla kod içermez. Daha ziyade, çerçevenin geri kalanını yüklemek için bir başlangıç noktasıdır.

`index.php` dosyası Composer tarafından oluşturulan otomatik yükleyici tanımını yükler ve ardından `bootstrap/app.php`'den Laravel uygulamasının bir örneğini alır. Laravel'in kendisi tarafından gerçekleştirilen ilk eylem, uygulama / hizmet konteynerinin bir örneğini oluşturmaktır.

#### HTTP / Console Kernels
---
Ardından, gelen istek, uygulamaya giren isteğin türüne bağlı olarak HTTP çekirdeğine veya konsol çekirdeğine gönderilir. Bu iki çekirdek, tüm isteklerin aktığı merkezi konum olarak hizmet eder. Şimdilik sadece `app/Http/Kernel.php` içinde bulunan HTTP çekirdeğine odaklanalım.

HTTP çekirdeği, istek yürütülmeden önce çalıştırılacak bir dizi önyükleyici tanımlayan `Illuminate\Foundation\Http\Kernel` sınıfını genişletir. Bu `bootstrappers` hata işlemeyi yapılandırır, günlüğü yapılandırır, uygulama ortamını tespit eder ve istek gerçekten işlenmeden önce yapılması gereken diğer görevleri yerine getirir. Tipik olarak, bu sınıflar endişelenmeniz gerekmeyen dahili Laravel yapılandırmasını ele alır.

HTTP çekirdeği ayrıca tüm isteklerin uygulama tarafından işlenmeden önce geçmesi gereken bir HTTP ara yazılım listesi tanımlar. Bu ara yazılımlar HTTP oturumunu okuma ve yazma, uygulamanın bakım modunda olup olmadığını belirleme, CSRF belirtecini doğrulama ve daha fazlasını gerçekleştirir. Bunlar hakkında yakında daha fazla konuşacağız.

HTTP çekirdeğinin `handle` metotu için yöntem imzası oldukça basittir: bir İstek alır ve bir Yanıt döndürür. Çekirdeği tüm uygulamanızı temsil eden büyük bir kara kutu olarak düşünün. Onu HTTP istekleriyle besleyin ve o da HTTP yanıtları döndürsün.

#### Service Providers (Hizmet Sağlayıcılar)
---
En önemli çekirdek önyükleme eylemlerinden biri, uygulamanız için hizmet sağlayıcıları yüklemektir. Hizmet sağlayıcılar, veritabanı, kuyruk, doğrulama ve yönlendirme bileşenleri gibi çerçevenin çeşitli bileşenlerinin önyüklenmesinden sorumludur. Uygulama için tüm hizmet sağlayıcılar `config/app.php` yapılandırma dosyasının `providers` dizisinde yapılandırılır.

Laravel bu sağlayıcı listesini yineleyecek ve her birini örnekleyecektir. Sağlayıcıları örnekledikten sonra, `register` metodu tüm sağlayıcılar üzerinde çağrılacaktır. Daha sonra, tüm sağlayıcılar kaydedildikten sonra, `boot` metodu her bir sağlayıcı üzerinde çağrılacaktır. Bunun nedeni, hizmet sağlayıcıların, önyükleme yöntemleri çalıştırıldığında her konteyner bağının kayıtlı ve kullanılabilir olmasına bağlı olabilmesidir.

Esasen Laravel tarafından sunulan her önemli özellik bir servis sağlayıcı tarafından önyüklenir ve yapılandırılır. Framework tarafından sunulan pek çok özelliği önyükledikleri ve yapılandırdıkları için, servis sağlayıcılar tüm Laravel önyükleme sürecinin en önemli yönüdür.

#### Routing (Yönlendirme)
---
Uygulamanızdaki en önemli hizmet sağlayıcılardan biri `App\Providers\RouteServiceProvider`'dır. Bu hizmet sağlayıcı, uygulamanızın `routes` dizininde bulunan rota dosyalarını yükler. `RouteServiceProvider` kodunu açın ve nasıl çalıştığına bir göz atın!

Uygulama önyüklendikten ve tüm hizmet sağlayıcılar kaydedildikten sonra, `Request` gönderilmek üzere yönlendiriciye teslim edilecektir. Yönlendirici, isteği bir rotaya veya denetleyiciye gönderecek ve rotaya özgü ara yazılımları çalıştıracaktır.

Ara yazılımlar, uygulamanıza giren HTTP isteklerini filtrelemek veya incelemek için uygun bir mekanizma sağlar. Örneğin, Laravel, uygulamanızın kullanıcısının kimliğinin doğrulanmış olup olmadığını doğrulayan bir ara katman yazılımı içerir. Eğer kullanıcının kimliği doğrulanmamışsa, ara yazılım kullanıcıyı giriş ekranına yönlendirecektir. Ancak, kullanıcının kimliği doğrulanmışsa, ara yazılım isteğin uygulamaya devam etmesine izin verecektir. Bazı ara yazılımlar, HTTP çekirdeğinizin `$middleware` özelliğinde tanımlananlar gibi uygulama içindeki tüm rotalara atanırken, bazıları yalnızca belirli rotalara veya rota gruplarına atanır.

İstek, eşleşen rotanın atanmış tüm ara katmanlarından geçerse, rota veya denetleyici yöntemi yürütülür ve rota veya denetleyici yöntemi tarafından döndürülen yanıt, rotanın ara katman zinciri aracılığıyla geri gönderilir.

#### Bitiş
---
Rota veya denetleyici yöntemi bir yanıt döndürdüğünde, yanıt rotanın ara yazılımı aracılığıyla dışarıya doğru geri gider ve uygulamaya giden yanıtı değiştirme veya inceleme şansı verir.

Son olarak, yanıt ara katman üzerinden geri döndüğünde, HTTP çekirdeğinin `handle` metodu yanıt nesnesini döndürür ve `index.php` dosyası döndürülen yanıt üzerinde `send` metodunu çağırır. `send` metodu yanıt içeriğini kullanıcının web tarayıcısına gönderir.

## Service Providers (Hizmet Sağlayıcılarına) Odaklanın
---
Servis sağlayıcılar bir Laravel uygulamasını önyüklemenin gerçek anahtarıdır. Uygulama örneği oluşturulur, servis sağlayıcılar kaydedilir ve istek `bootstrapped` uygulamaya iletilir. Gerçekten bu kadar basit!

Bir Laravel uygulamasının servis sağlayıcılar aracılığıyla nasıl oluşturulduğunu ve önyüklendiğini kavramak çok değerlidir. Uygulamanızın varsayılan servis sağlayıcıları `app/Providers` dizininde saklanır.

Varsayılan olarak `AppServiceProvider` oldukça boştur. Bu sağlayıcı, uygulamanızın kendi önyükleme ve hizmet kapsayıcı bağlarını eklemek için harika bir yerdir. Büyük uygulamalar için, her biri uygulamanız tarafından kullanılan belirli hizmetler için daha ayrıntılı önyükleme içeren birkaç hizmet sağlayıcı oluşturmak isteyebilirsiniz.