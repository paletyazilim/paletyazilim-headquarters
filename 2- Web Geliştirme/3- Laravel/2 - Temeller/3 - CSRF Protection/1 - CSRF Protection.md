# `#` Giriş
---
Cross-Site Request Forgeries (Siteler Arası İstek Sahteciliği), kimliği doğrulanmış bir kullanıcı adına yetkisiz komutların gerçekleştirildiği kötü niyetli bir istismar türüdür. Neyse ki Laravel, uygulamanızı siteler arası istek sahteciliği (CSRF) saldırılarından korumanızı kolaylaştırır.

## Güvenlik Açığına Genel Bir Açıklama

Siteler arası istek sahteciliğine aşina değilseniz, bu güvenlik açığından nasıl yararlanılabileceğine dair bir örneği ele alalım. Uygulamanızın, kimliği doğrulanmış kullanıcının e-posta adresini değiştirmek için bir `POST` isteğini kabul eden bir `/user/email` rotasına sahip olduğunu düşünün. Büyük olasılıkla bu rota, kullanıcının kullanmaya başlamak istediği e-posta adresini içeren bir `email` giriş alanı beklemektedir.

CSRF koruması olmadan, kötü niyetli bir web sitesi uygulamanızın `/user/email` rotasına işaret eden ve kötü niyetli kullanıcının kendi e-posta adresini gönderen bir HTML formu oluşturabilir:

```php
<form action="https://your-application.com/user/email" method="POST">
    <input type="email" value="malicious-email@example.com">
</form>
 
<script>
    document.forms[0].submit();
</script>
```

Kötü niyetli web sitesi, sayfa yüklendiğinde formu otomatik olarak gönderirse, kötü niyetli kullanıcının yalnızca uygulamanızın şüphelenmeyen bir kullanıcısını web sitesini ziyaret etmesi için kandırması gerekir ve e-posta adresi uygulamanızda değiştirilir.

Bu güvenlik açığını önlemek için, gelen her `POST`, `PUT`, `PATCH` veya `DELETE` isteğini kötü niyetli uygulamanın erişemeyeceği gizli bir oturum değeri açısından incelememiz gerekir.

# `#` CSRF İsteklerini Önleme
---

Laravel, uygulama tarafından yönetilen her aktif kullanıcı oturumu için otomatik olarak bir CSRF "token" oluşturur. Bu token, kimliği doğrulanmış kullanıcının gerçekten uygulamaya istekte bulunan kişi olduğunu doğrulamak için kullanılır. Bu token kullanıcının oturumunda saklandığından ve oturum her yeniden oluşturulduğunda değiştiğinden, kötü niyetli bir uygulama buna erişemez.

Geçerli oturumun CSRF belirtecine isteğin oturumu veya `csrf_token` yardımcı metodu aracılığıyla erişilebilir:

```php
use Illuminate\Http\Request;
 
Route::get('/token', function (Request $request) {
    $token = $request->session()->token();
 
    $token = csrf_token();
 
    // ...
});
```

Uygulamanızda bir "POST", "PUT", "PATCH" veya "DELETE" HTML formu tanımladığınızda, CSRF koruma ara yazılımının isteği doğrulayabilmesi için forma gizli bir CSRF `_token` alanı eklemelisiniz. Kolaylık sağlamak amacıyla, gizli belirteç giriş alanını oluşturmak için `@csrf` Blade yönergesini kullanabilirsiniz:

```php
<form method="POST" action="/profile">
    @csrf
 
    <!-- YADA -->
    
    <input type="hidden" name="_token" value="{{ csrf_token() }}" />
</form>
```

Varsayılan olarak web ara yazılım grubuna dahil edilen `Illuminate\Foundation\Http\Middleware\ValidateCsrfToken` middleware'i, istek girdisindeki belirtecin oturumda depolanan belirteçle eşleştiğini otomatik olarak doğrulayacaktır. Bu iki belirteç eşleştiğinde, kimliği doğrulanmış kullanıcının isteği başlatan kişi olduğunu biliriz.