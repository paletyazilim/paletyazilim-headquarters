Laravel dokümantasyonu boyunca, Laravel'in özellikleri ile "facades" aracılığıyla etkileşime giren kod örnekleri göreceksiniz. Facade'ler, uygulamanın hizmet kapsayıcılar mevcut olan sınıflara "statik" bir arayüz sağlar. Laravel, Laravel'in neredeyse tüm özelliklerine erişim sağlayan birçok facade ile birlikte gelir.

Laravel facades, hizmet konteynerindeki temel sınıflara "statik vekiller" olarak hizmet eder, geleneksel statik metotlardan daha fazla test edilebilirlik ve esneklik sağlarken, kısa ve etkileyici bir sözdiziminin avantajını sağlar. Facade'lerin nasıl çalıştığını tam olarak anlamadıysanız sorun değil - sadece akışına bırakın ve Laravel hakkında öğrenmeye devam edin.

Laravel'in tüm facades'leri `Illuminate\Support\Facades` isim alanında tanımlanmıştır. Böylece, bir facade'e aşağıdaki gibi kolayca erişebiliriz:

```PHP
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Route;
 
Route::get('/cache', function () { //Route Facades
    return Cache::get('key'); //Cache Facades
});
```

Laravel dokümantasyonu boyunca, örneklerin çoğu frameworkün çeşitli özelliklerini göstermek için facades kullanacaktır.

