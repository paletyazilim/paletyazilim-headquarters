Laravel service container (servis kapsayıcı) sınıf dependencylerini (bağımlılıklarını) yönetmek ve dependicy (bağımlılık) enjeksiyonu yapmak için güçlü bir araçtır. 

Dependency injection (Bağımlılık enjeksiyonu), esasen şu anlama gelen süslü bir ifadedir: sınıf dependencyleri (bağımlılıkları), constructor (kurucu) veya bazı durumlarda "setter" metotları aracılığıyla sınıfa "enjekte edilir".

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
     * Yeni bir controller örneği oluşturduk
     */
    public function __construct(
        protected UserRepository $users,
    ) {}
 
    /**
     * Verilen kullanıcıyı veritabanından çekiyoruz.
     */
    public function show(string $id): View
    {
        $user = $this->users->find($id);
 
        return view('user.profile', ['user' => $user]);
    }
}
```

Bu örnekte, `UserController`'ın kullanıcıları bir veri kaynağından alması gerekiyor. Bu yüzden, kullanıcıları alabilen bir servis enjekte edeceğiz. Bu bağlamda, `UserRepository`'miz büyük olasılıkla kullanıcı bilgilerini veritabanından almak için Eloquent'i kullanır. Ancak, depo enjekte edildiğinden, onu başka bir uygulama ile kolayca değiştirebiliriz. Ayrıca uygulamamızı test ederken `UserRepository` için kolayca "mock" yapabilir ya da sahte bir uygulama oluşturabiliriz.

Laravel service container (hizmet kapsamını) derinlemesine anlamak, güçlü ve büyük bir uygulama oluşturmanın yanı sıra Laravel çekirdeğinin kendisine katkıda bulunmak için de gereklidir.

#### Zero Configuration Resolution
---
Bir sınıfın bağımlılığı yoksa veya yalnızca diğer somut sınıflara (arayüzlere değil) bağlıysa, kapsamların bu sınıfı nasıl çözümleyeceği konusunda bilgilendirilmesine gerek yoktur. Örneğin, `routes/web.php` dosyanıza aşağıdaki kodu yerleştirebilirsiniz:

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

Neyse ki, bir Laravel uygulaması oluştururken yazacağınız sınıfların çoğu, controllerlar, event listeners, middleware yazılımları ve daha fazlası dahil olmak üzere kapsam aracılığıyla dependecyleri otomatik olarak alır. Ek olarak, queued jobs (kuyruğa alınmış işlerin) `handle` metodunda dependencyleri yazabilirsiniz. Otomatik dependency enjeksiyonunun gücünü bir kez tattığınızda, onsuz geliştirme yapmak imkansız hale gelir.

#### Kapsayıcılar Ne Zaman Kullanılır
---
Zero configuration resolution sayesinde, kapsamların manuel olarak etkileşime girmeden routes (rotalar), controllerlar (denetleyiciler), event listeners (olay dinleyicileri) ve başka yerlerdeki dependencyleri sık sık yazarak ima edersiniz. Örneğin, mevcut requeste (isteğe) kolayca erişebilmek için rota tanımınızda (Illuminate\Http\Request) nesnesini işaretleyebilirsiniz. Bu kodu yazmak için hiçbir zaman kapsayıcılarla etkileşime girmemize gerek olmasa da, kapsayıcı bu dependencyleri perde arkasında yönetmektedir:

```PHP
use Illuminate\Http\Request;
 
Route::get('/', function (Request $request) {
    // ...
});
```

Birçok durumda, otomatik dependency enjeksiyonu ve facades sayesinde, kapsayıcıdan herhangi bir şeyi manuel olarak bağlamadan veya çözümlemeden Laravel uygulamaları oluşturabilirsiniz. Peki, ne zaman kapsayıcı ile manuel olarak etkileşime girersiniz? İki durumu inceleyelim.

- İlk olarak, bir interface (arayüzü) uygulayan bir sınıf yazıyorsanız ve bu interface (arayüzü) bir rotada veya sınıf constructor (kurucusunda) yazmak istiyorsanız, kapsayıcıya bu interfacei nasıl çözeceğini söylemelisiniz. 
- İkinci olarak, eğer diğer Laravel geliştiricileri ile paylaşmayı planladığınız bir Laravel paketi yazıyorsanız, paketinizin servislerini kapsayıcıya bağlamanız gerekebilir.

## Binding (Bağlayıcı)
---
#### Binding Temelleri
---
###### Basit Binding
---
Service containers bağlarınızın neredeyse tamamı service providerslara (hizmet sağlayıcılara) kaydedilecektir, bu nedenle bu örneklerin çoğu kapsayıcıyı bu bağlamda kullanmayı gösterecektir.

Bir service providers içinde, `$this->app` özelliği aracılığıyla her zaman konteynere erişiminiz vardır. `bind` metodunu kullanarak bir bağlama kaydedebilir, kaydetmek istediğimiz sınıf veya arayüz adını sınıfın bir örneğini döndüren bir closure ile birlikte iletebiliriz:

