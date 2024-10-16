# `#` Giriş
---

Laravel Service Container sınıf bağımlılıklarını yönetmek ve bağımlılık enjeksiyonu yapmak için güçlü bir araçtır. Bağımlılık enjeksiyonu, esasen şu anlama gelen süslü bir ifadedir: sınıf bağımlılıkları, consturactor veya bazı durumlarda "setter" yöntemleri aracılığıyla sınıfa "enjekte edilir".

Basit bir örneğe bakalım:

```php
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Repositories\UserRepository;
use App\Models\User;
use Illuminate\View\View;
 
class UserController extends Controller
{
    /**
     * Yeni bir controller örneği oluştur.
     */
    public function __construct(
        protected UserRepository $users,
    ) {}
 
    /**
     * Verilen ID'ye göre kullanıcının profili gösteriliyor.
     */
    public function show(string $id): View
    {
        $user = $this->users->find($id);
 
        return view('user.profile', ['user' => $user]);
    }
}
```

Bu örnekte, `UserController`'ın kullanıcıları bir veri kaynağından alması gerekiyor. Bu yüzden, kullanıcıları alabilen bir servis **enjekte** edeceğiz. Bu bağlamda, `UserRepository`'miz büyük olasılıkla kullanıcı bilgilerini veritabanından almak için Eloquent'i kullanır. Ancak, repository (depo) enjekte edildiğinden, onu başka bir uygulama ile kolayca değiştirebiliriz. Ayrıca uygulamamızı test ederken `UserRepository` için kolayca "mock" yapabilir ya da sahte bir uygulama oluşturabiliriz.

Laravel service container’i derinlemesine anlamak, güçlü ve büyük bir uygulama oluşturmanın yanı sıra Laravel çekirdeğinin kendisine katkıda bulunmak için de gereklidir.

## Zero Configuration Resolution

Bir sınıfın bağımlılığı yoksa veya yalnızca diğer somut sınıflara (arayüzlere değil) bağlıysa, container’lerin bu sınıfı nasıl çözümleyeceği konusunda bilgilendirilmesine gerek yoktur. Örneğin, `routes/web.php` dosyanıza aşağıdaki kodu yerleştirebilirsiniz:

```php
<?php
 
class Service
{
    // ...
}
 
Route::get('/', function (Service $service) {
    die($service::class);
});
```

Bu örnekte, uygulamanızın `/` rotasına tıkladığınızda `Service` sınıfı otomatik olarak çözümlenecek ve rotanızın işleyicisine enjekte edilecektir. Bu oyunun kurallarını değiştirir. Bu, uygulamanızı geliştirebileceğiniz ve şişirilmiş yapılandırma dosyaları hakkında endişelenmeden bağımlılık enjeksiyonundan yararlanabileceğiniz anlamına gelir.

Neyse ki, bir Laravel uygulaması oluştururken yazacağınız sınıfların çoğu, controller, event listeners (olay dinleyicileri), middleware (ara yazılımlar) ve daha fazlası dahil olmak üzere container aracılığıyla bağımlılıklarını otomatik olarak alır. Ek olarak, queued jobs (kuyruğa alınmış işlerin) `handle` metodunda bağımlılıkları yazabilirsiniz. Otomatik konfigürasyonlu bağımlılık enjeksiyonunun gücünü bir kez tattığınızda, onsuz geliştirme yapmak imkansız hale gelir.

## Container’lar Ne Zaman Kullanılmalı

Zero Configuration Resolution sayesinde, containerlere manuel olarak etkileşime girmeden route’ler (rotalar), controller’lar, event listeners (olay dinleyicileri) ve başka yerlerdeki bağımlılıkları sık sık yazarak ima edersiniz. Örneğin, mevcut isteğe kolayca erişebilmek için rota tanımınızda `Illuminate\Http\Request` nesnesini işaretleyebilirsiniz. Bu kodu yazmak için hiçbir zaman konteynerle etkileşime girmemiz gerekmese de, konteyner bu bağımlılıkların enjeksiyonunu perde arkasında yönetmektedir:

Birçok durumda, otomatik bağımlılık enjeksiyonu ve facades sayesinde, containerden herhangi bir şeyi manuel olarak bağlamadan veya çözümlemeden Laravel uygulamaları oluşturabilirsiniz. Peki, ne zaman container ile manuel olarak etkileşime girersiniz? İki durumu inceleyelim.

- İlk olarak, bir arayüzü uygulayan bir sınıf yazıyorsanız ve bu arayüzü bir route’da (rotada) veya sınıf consturactor yazmak istiyorsanız, containere bu arayüzü nasıl çözeceğini söylemelisiniz.
- İkinci olarak, eğer diğer Laravel geliştiricileri ile paylaşmayı planladığınız bir Laravel paketi yazıyorsanız, paketinizin providers’larını container’ınıza bağlamanız gerekebilir.