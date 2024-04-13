# `#` Giriş

---

Laravel dokümantasyonu boyunca, Laravel'in özellikleri ile facades aracılığıyla etkileşime giren kod örnekleri göreceksiniz. Facade'ler, uygulamanın service providers’larında mevcut olan sınıflara "statik" bir arayüz sağlar. Laravel, Laravel'in neredeyse tüm özelliklerine erişim sağlayan birçok facade ile birlikte gelir.

Laravel facades, hizmet konteynerindeki temel sınıflara "static proxies" olarak hizmet eder, geleneksel statik yöntemlerden daha fazla test edilebilirlik ve esneklik sağlarken, kısa ve etkileyici bir sözdiziminin avantajını sağlar. Facade'lerin nasıl çalıştığını tam olarak anlamadıysanız sorun değil - sadece akışına bırakın ve Laravel hakkında öğrenmeye devam edin.

Laravel'in tüm facades’ları `Illuminate\Support\Facades` isim alanında tanımlanmıştır. Böylece, bir facade'e aşağıdaki gibi kolayca erişebiliriz:

```php
use Illuminate\Support\Facades\Cache;
use Illuminate\Support\Facades\Route;
 
Route::get('/cache', function () {
    return Cache::get('key');
});
```

## Yardımcı Fonksiyonlar

Facade'leri tamamlamak için Laravel, ortak Laravel özellikleri ile etkileşimi daha da kolaylaştıran çeşitli global "yardımcı fonksiyonlar" sunar. Etkileşim kurabileceğiniz yaygın yardımcı fonksiyonlardan bazıları view, response, url, config ve daha fazlasıdır. Laravel tarafından sunulan her bir yardımcı fonksiyon, ilgili özellikleriyle birlikte belgelenmiştir; ancak tam bir liste özel yardımcı dokümantasyonunda mevcuttur.

Örneğin, bir JSON yanıtı oluşturmak için `Illuminate\Support\Facades\Response` facades’ini kullanmak yerine, sadece `response` fonksiyonunu kullanabiliriz. Yardımcı fonksiyonlar global olarak kullanılabilir olduğundan, bunları kullanmak için herhangi bir sınıfı içe aktarmanız gerekmez:

```php
use Illuminate\Support\Facades\Response;
 
Route::get('/users', function () {
    return Response::json([
        // ...
    ]);
});
```

```php
Route::get('/users', function () {
    return response()->json([
        // ...
    ]);
});
```

# `#` Facades’ler Ne Zaman Kullanılmalı

---

Facade'lerin birçok faydası vardır. Enjekte edilmesi veya manuel olarak yapılandırılması gereken uzun sınıf adlarını hatırlamadan Laravel'in özelliklerini kullanmanıza izin veren kısa ve akılda kalıcı bir sözdizimi sağlarlar. Ayrıca, PHP'nin dinamik yöntemlerini benzersiz bir şekilde kullandıkları için test edilmeleri kolaydır.

Ancak, facade’leri kullanırken biraz dikkatli olunmalıdır. Facade'lerin birincil tehlikesi sınıf "kapsamının genişlemesi "dir. Facade'lerin kullanımı çok kolay olduğundan ve enjeksiyon gerektirmediğinden, sınıflarınızın büyümeye devam etmesine ve tek bir sınıfta birçok facade kullanmasına izin vermek kolay olabilir. Bağımlılık enjeksiyonu kullanıldığında, bu potansiyel, büyük bir yapıcının sınıfınızın çok büyüdüğüne dair size verdiği görsel geri bildirimle azaltılır. Bu nedenle, facade’leri kullanırken sınıfınızın boyutuna özellikle dikkat edin, böylece sorumluluk kapsamı dar kalır. Sınıfınız çok büyüyorsa, onu birden fazla küçük sınıfa bölmeyi düşünün.

## Facades vs. Dependency Injection (Bağımlılık Enjektesi)

Bağımlılık enjeksiyonunun birincil faydalarından biri, enjekte edilen sınıfın uygulamalarını değiştirme yeteneğidir. Bu, test sırasında kullanışlıdır çünkü bir mock veya stub enjekte edebilir ve çeşitli yöntemlerin stub üzerinde çağrıldığını iddia edebilirsiniz.

Tipik olarak, gerçekten statik bir sınıf yöntemini taklit etmek veya saplamak mümkün olmaz. Ancak, facade’lar service container’dan çözümlenen nesnelere yöntem çağrılarını proxy etmek için dinamik yöntemler kullandığından, aslında facade’lerin enjekte edilen bir sınıf örneğini test ettiğimiz gibi test edebiliriz. Örneğin, aşağıdaki rota göz önüne alındığında:

```php
use Illuminate\Support\Facades\Cache;
 
Route::get('/cache', function () {
    return Cache::get('key');
});
```

Laravel'in facade test yöntemlerini kullanarak, `Cache::get` metodunun beklediğimiz argüman ile çağrıldığını doğrulamak için aşağıdaki testi yazabiliriz:

```php
use Illuminate\Support\Facades\Cache;
 
/**
 * Temel bir fonksiyonel test örneği.
 */
public function test_basic_example(): void
{
    Cache::shouldReceive('get')
         ->with('key')
         ->andReturn('value');
 
    $response = $this->get('/cache');
 
    $response->assertSee('value');
}
```