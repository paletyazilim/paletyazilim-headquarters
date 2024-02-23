Laravel servis konteyneri sınıf bağımlılıklarını yönetmek ve bağımlılık enjeksiyonu yapmak için güçlü bir araçtır. Bağımlılık enjeksiyonu, esasen şu anlama gelen süslü bir ifadedir: sınıf bağımlılıkları, kurucu veya bazı durumlarda "setter" yöntemleri aracılığıyla sınıfa "enjekte edilir".

Basit bir örneğe bakalım:

```PHP
<?php
 
namespace App\Http\Controllers;
 
use App\Http\Controllers\Controller;
use App\Repositories\UserRepository;
use App\Models\User;
use Illuminate\View\View;
 
class UserController extends Controller
{
    /**
     * Yeni bir denetleyici örneği oluşturun.
     */
    public function __construct(
        protected UserRepository $users,
    ) {}
 
    /**
     * Verilen kullanıcı için profili gösterin.
     */
    public function show(string $id): View
    {
        $user = $this->users->find($id);
 
        return view('user.profile', ['user' => $user]);
    }
}
```

Bu örnekte, `UserController`'ın kullanıcıları bir veri kaynağından alması gerekiyor. Bu yüzden, kullanıcıları alabilen bir servis enjekte edeceğiz. Bu bağlamda, `UserRepository`'miz büyük olasılıkla kullanıcı bilgilerini veritabanından almak için Eloquent'i kullanır. Ancak, depo enjekte edildiğinden, onu başka bir uygulama ile kolayca değiştirebiliriz. Ayrıca uygulamamızı test ederken `UserRepository` için kolayca "mock" yapabilir ya da sahte bir uygulama oluşturabiliriz.

Laravel hizmet konteynerini derinlemesine anlamak, güçlü ve büyük bir uygulama oluşturmanın yanı sıra Laravel çekirdeğinin kendisine katkıda bulunmak için de gereklidir.

## Zero Configuration Resolution
---
Bir sınıfın bağımlılığı yoksa veya yalnızca diğer somut sınıflara (arayüzlere değil) bağlıysa, konteynerin bu sınıfı nasıl çözümleyeceği konusunda bilgilendirilmesine gerek yoktur. Örneğin, `routes/web.php` dosyanıza aşağıdaki kodu yerleştirebilirsiniz:

```PHP
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

Neyse ki, bir Laravel uygulaması oluştururken yazacağınız sınıfların çoğu, kontrolörler, olay dinleyicileri, ara katman yazılımları ve daha fazlası dahil olmak üzere konteyner aracılığıyla bağımlılıklarını otomatik olarak alır. Ek olarak, kuyruğa alınmış işlerin `handle` metodunda bağımlılıkları yazabilirsiniz. Otomatik ve sıfır konfigürasyonlu bağımlılık enjeksiyonunun gücünü bir kez tattığınızda, onsuz geliştirme yapmak imkansız hale gelir.

## Konteyner Ne Zaman Kullanılmalı
---
Zero Configuration Resolution sayesinde, konteynerle manuel olarak etkileşime girmeden rotalar, denetleyiciler, olay dinleyicileri ve başka yerlerdeki bağımlılıkları sık sık yazarak ima edersiniz. Örneğin, mevcut isteğe kolayca erişebilmek için rota tanımınızda `Illuminate\Http\Request` nesnesini işaretleyebilirsiniz. Bu kodu yazmak için hiçbir zaman konteynerle etkileşime girmemiz gerekmese de, konteyner bu bağımlılıkların enjeksiyonunu perde arkasında yönetmektedir:

```PHP
use Illuminate\Http\Request;
 
Route::get('/', function (Request $request) {
    // ...
});
```

Birçok durumda, otomatik bağımlılık enjeksiyonu ve facades sayesinde, konteynerden herhangi bir şeyi manuel olarak bağlamadan veya çözümlemeden Laravel uygulamaları oluşturabilirsiniz. Peki, ne zaman konteyner ile manuel olarak etkileşime girersiniz? İki durumu inceleyelim.

İlk olarak, bir arayüzü uygulayan bir sınıf yazıyorsanız ve bu arayüzü bir rotada veya sınıf kurucusunda yazmak istiyorsanız, konteynere bu arayüzü nasıl çözeceğini söylemelisiniz. İkinci olarak, eğer diğer Laravel geliştiricileri ile paylaşmayı planladığınız bir Laravel paketi yazıyorsanız, paketinizin servislerini konteynere bağlamanız gerekebilir.