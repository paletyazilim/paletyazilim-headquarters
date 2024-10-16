# # Giriş
---
Yeni bir Laravel projesine başladığınızda, hata ve istisna işleme sizin için zaten yapılandırılmıştır; ancak, herhangi bir noktada, istisnaların uygulamanız tarafından nasıl raporlanacağını ve işleneceğini yönetmek için uygulamanızın `bootstrap/app.php` dosyasındaki `withExceptions` metodunnu kullanabilirsiniz.

`withExceptions` kapanışına sağlanan `$exceptions` nesnesi, `Illuminate\Foundation\Configuration\Exceptions`'ın bir örneğidir ve uygulamanızdaki istisna işlemlerini yönetmekten sorumludur. Bu dokümantasyon boyunca bu nesneyi daha derinlemesine inceleyeceğiz.

# # Yapılandırma
---
`config/app.php` yapılandırma dosyanızdaki hata ayıklama seçeneği, bir hata hakkında kullanıcıya gerçekte ne kadar bilgi görüntüleneceğini belirler. Varsayılan olarak, bu seçenek `.env` dosyanızda depolanan `APP_DEBUG` ortam değişkeninin değerine uyacak şekilde ayarlanmıştır.

Yerel geliştirme sırasında `APP_DEBUG` ortam değişkenini `true` olarak ayarlamalısınız. Üretim ortamınızda bu değer her zaman `false` olmalıdır. Değer üretimde `true` olarak ayarlanırsa, hassas yapılandırma değerlerini uygulamanızın son kullanıcılarına ifşa etme riskini alırsınız.

# # İstisnaları Yakalama
---
## Raporlama İstisnaları

Laravel'de istisna raporlama, istisnaları günlüğe kaydetmek veya harici bir hizmet olan Sentry veya Flare'e göndermek için kullanılır. Varsayılan olarak, istisnalar günlükleme yapılandırmanıza göre günlüğe kaydedilecektir. Ancak, istisnaları istediğiniz gibi günlüğe kaydetmekte özgürsünüz.

Farklı istisna türlerini farklı şekillerde raporlamanız gerekiyorsa, uygulamanızın `bootstrap/app.php` dosyasında `report` exception metodunu kullanarak belirli türde bir istisnanın raporlanması gerektiğinde çalıştırılacak bir closure kaydedebilirsiniz. Laravel, closure'un type-hint'ini inceleyerek closure'un ne tür bir istisna raporladığını belirleyecektir:

```php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->report(function (InvalidOrderException $e) {
        // ...
    });
})
```

`report` metodunu kullanarak özel bir istisna raporlama geri çağrısı kaydettiğinizde, Laravel uygulama için varsayılan loglama yapılandırmasını kullanarak istisnayı loglamaya devam edecektir. Eğer istisnanın varsayılan loglama yığınına yayılmasını durdurmak isterseniz, raporlama geri çağrınızı tanımlarken `stop` metodunu kullanabilir veya geri çağrıdan `false` değerini döndürebilirsiniz:

```php
->withExceptions(function (Exceptions $exceptions) {
    $exceptions->report(function (InvalidOrderException $e) {
        // ...
    })->stop();
 
    $exceptions->report(function (InvalidOrderException $e) {
        return false;
    });
})
```

#çevrilecek 

