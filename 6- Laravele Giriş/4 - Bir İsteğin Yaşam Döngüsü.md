# Giriş
---

"Gerçek dünyada" herhangi bir aracı kullanırken, o aracın nasıl çalıştığını anlarsanız kendinizi daha güvende hissedersiniz. Uygulama geliştirme de farklı değildir. Geliştirme araçlarınızın nasıl çalıştığını anladığınızda, onları kullanırken kendinizi daha rahat ve güvende hissedersiniz.

Bu belgenin amacı size Laravel framework’ünün nasıl çalıştığına dair iyi ve üst düzey bir genel bakış sunmaktır. Genel framework’ü daha iyi tanıyarak, her şey daha az "sihirli" hissettirecek ve uygulamalarınızı oluştururken kendinize daha çok güveneceksiniz. Eğer tüm terimleri hemen anlamazsanız, cesaretinizi kaybetmeyin! Sadece neler olup bittiğine dair temel bir kavrayış edinmeye çalışın ve dokümantasyonun diğer bölümlerini keşfettikçe bilginiz artacaktır.

# Yaşam Döngüsüne Genel Bakış
---

## İlk Adım

Bir Laravel uygulamasına gelen tüm isteklerin giriş noktası `public/index.php` dosyasıdır. Tüm istekler web sunucunuzun (Apache / Nginx) yapılandırması tarafından bu dosyaya yönlendirilir. `index.php` dosyası çok fazla kod içermez. Daha ziyade, framework’ün geri kalanını yüklemek için bir başlangıç noktasıdır.

`index.php` dosyası Composer tarafından oluşturulan otomatik yükleyici tanımını yükler ve ardından `bootstrap/app.php`'den Laravel uygulamasının bir örneğini alır. Laravel'in kendisi tarafından gerçekleştirilen ilk eylem, application / service containers’ın bir örneğini oluşturmaktır.

## HTTP / Console Çekirdeği

Ardından, gelen istek, uygulamaya giren isteğin türüne bağlı olarak uygulama örneğinin `handleRequest` veya `handleCommand` metotları kullanılarak HTTP çekirdeğine veya konsol çekirdeğine gönderilir. Bu iki çekirdek, tüm isteklerin aktığı merkezi konum olarak hizmet eder. Şimdilik sadece `Illuminate\Foundation\Http\Kernel`'in bir örneği olan HTTP çekirdeğine odaklanalım.

HTTP çekirdeği, istek yürütülmeden önce çalıştırılacak bir dizi önyükleyici tanımlar. Bu `bootstrappers` hata işlemeyi yapılandırır, günlüğü yapılandırır, uygulama ortamını tespit eder ve istek gerçekten işlenmeden önce yapılması gereken diğer görevleri yerine getirir. Tipik olarak, bu sınıflar endişelenmeniz gerekmeyen dahili Laravel yapılandırmasını ele alır.

HTTP çekirdeği ayrıca isteği uygulamanın middleware (ara yazılım) yığınından geçirmekten de sorumludur. Bu middleware’ler HTTP session (oturumunu) okuma ve yazma, uygulamanın bakım modunda olup olmadığını belirleme, CSRF tokenini doğrulama ve daha fazlasını gerçekleştirir. Bunlar hakkında yakında daha fazla konuşacağız.

HTTP çekirdeğinin `handle` metotu için yöntem oldukça basittir: bir `Request` (İstek) alır ve bir `Response` (Yanıt) döndürür. Çekirdeği tüm uygulamanızı temsil eden büyük bir kara kutu olarak düşünün. Onu HTTP istekleriyle besleyin ve o da HTTP yanıtları döndürsün.

## Service Providers

En önemli çekirdek bootstrapping (önyükleme) eylemlerinden biri, uygulamanız için service providers yüklemektir. Serivce providers, framework’ün database (veritabanı), queue (kuyruk), validation (doğrulama) ve routing (yönlendirme) bileşenleri gibi çeşitli bileşenlerinin önyüklemesinden sorumludur.

Laravel bu provider listesini yineleyecek ve her birini örnekleyecektir. Provider’lar örnekledikten sonra, `register` metodu tüm sağlayıcılar üzerinde çağrılacaktır. Daha sonra, tüm providerlar kaydedildikten sonra, `boot` metodu her bir provider üzerinde çağrılacaktır. Bunun nedeni, hizmet sağlayıcıların, bootstrap (önyükleme) metotları çalıştırıldığında her konteyner bağımlılığının kayıtlı ve kullanılabilir olmasına bağlı olabilmesidir.

Esasen Laravel tarafından sunulan her önemli özellik bir service provider tarafından bootstrapped (önyüklenir) ve yapılandırılır. Framework tarafından sunulan pek çok özelliği bootstrap (önyükledikleri) ve yapılandırdıkları için, service providers tüm Laravel bootstrap (önyükleme) sürecinin en önemli yönüdür.

Framework dahili olarak düzinelerce service providers kullanırken, siz de kendi service provider’ınızı oluşturma seçeneğine sahipsiniz. Uygulamanızın kullandığı kullanıcı tanımlı veya üçüncü taraf service providers’ların listesini `bootstrap/providers.php` dosyasında bulabilirsiniz.

## Routing (Yönlendirme)

Uygulama bootstraped (önyüklendikten) ve tüm service provider’lar kaydedildikten sonra, `Request` (İstek) gönderilmek üzere router (yönlendiriciye) teslim edilecektir. Router (Yönlendirici), isteği bir route’a (rotaya) veya controller’a (denetleyiciye) gönderecek ve rotaya özgü middleware’ları (ara yazılımları) çalıştıracaktır.

Middleware’lar (Ara yazılımlar), uygulamanıza giren HTTP isteklerini filtrelemek veya incelemek için uygun bir mekanizma sağlar. Örneğin, Laravel, uygulamanızın kullanıcısının kimliğinin doğrulanmış olup olmadığını doğrulayan bir middleware (ara yazılımı) içerir. Eğer kullanıcının kimliği doğrulanmamışsa, middleware (ara yazılım) kullanıcıyı giriş ekranına yönlendirecektir. Ancak, kullanıcının kimliği doğrulanmışsa, middleware (ara yazılım) isteğin uygulamaya devam etmesine izin verecektir. `PreventRequestsDuringMaintenance` gibi bazı middleware’lar (ara yazılımlar) uygulama içindeki tüm rotalara atanırken, bazıları yalnızca belirli rotalara veya rota gruplarına atanır.

## Bitiş

Route (Rota) veya controller (denetleyici) metotu bir yanıt döndürdüğünde, yanıt rotanın middleware’i (ara yazılımı) aracılığıyla dışarıya doğru geri gider ve uygulamaya giden yanıtı değiştirme veya inceleme şansı verir.

Son olarak, yanıt middleware (ara katman) üzerinden geri döndüğünde, HTTP çekirdeğinin `handle` metotu yanıt nesnesini uygulama örneğinin `handleRequest`'ine döndürür ve bu metot döndürülen yanıt üzerinde `send` metotunu çağırır. `send` metotu yanıt içeriğini kullanıcının web tarayıcısına gönderir. Şimdi tüm Laravel istek yaşam döngüsü boyunca yolculuğumuzu tamamladık!

# `#` Service Providers’lara Odaklanın

---

Service providers bir Laravel uygulamasını bootstrap yapmanın (önyüklemenin) gerçek anahtarıdır. Uygulama örneği oluşturulur, service providers kaydedilir ve istek bootstrapped uygulamaya iletilir. Gerçekten bu kadar basit!

Bir Laravel uygulamasının service providers aracılığıyla nasıl inşa edildiğini ve önyüklendiğini kavramak çok değerlidir. Uygulamanızın kullanıcı tanımlı service providers’ları `app/Providers` dizininde saklanır.

Varsayılan olarak `AppServiceProvider` oldukça boştur. Bu provider, uygulamanızın kendi bootstrap (önyükleme) ve service container bağlarını eklemek için harika bir yerdir. Büyük uygulamalar için, her biri uygulamanız tarafından kullanılan belirli hizmetler için daha ayrıntılı bootstrap (önyükleme) içeren birkaç service providers oluşturmak isteyebilirsiniz.