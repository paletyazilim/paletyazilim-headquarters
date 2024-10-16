# `#` Ders Öncesi

---

Bu kurs belirli seviyede PHP bilgisi olan kişilere hitap etmektedir eğer PHP bilginiz yetersiz kalıyor ise lütfen daha sonra bu dokümantasyona tekrar bakın.

Bu doküman Laravel’in en güncel sürümü olan `11.x` sürümünün Laravel’in resmi web sayfasındaki dokümantasyonunun yerelleştirme çalışmasıdır. 

Yazılan örnekler veya anlatılan örnekler birebir şekilde aynısı olmamakla beraber dokümantasyondaki bütün kavramların Türkçeleştirilememesi olasıdır. Daha kompleks bir anlatımdan daha basit bir anlatım tekniği benimsenmiş olup her uzun anlatımdan sonra konular basitçe özetlenmiş olacaktır. Daha ayrıntılı bir kaynak için [resmi dokümantasyonu](https://laravel.com/docs/11.x) lütfen ziyaret etmeyi unutmayın

# `#` Laravel Nedir?

---

Laravel, etkileyici ve zarif sözdizimine sahip bir web framework’üdür. Bir framework, uygulamanızı oluşturmak için başlangıç noktası sağlayarak, daha kısa sürede daha fazla üretkenlik ve zaman tasarrufu sağlamayı hedefler.

Laravel, kapsamlı bağımlılık enjeksiyonu (dependecy injections), etkileyici bir veritabanı soyutlama katmanı (database abstraction), kuyruklar (queue) ve zamanlanmış işler (scheduled jobs), birim (unit) ve entegrasyon testi ve daha fazlası gibi güçlü özellikler sunarken harika bir geliştirici deneyimi sağlamaya çalışır.

İster PHP web framework’lerin de yeni olun, ister yılların deneyimine sahip olun, Laravel sizinle birlikte büyüyebilecek bir framework’tur. Bir web geliştiricisi olarak ilk adımlarınızı atmanıza yardımcı olacaktır veya uzmanlığınızı bir sonraki seviyeye taşırken size katkısı olacaktır.

# `#` Neden Laravel?

---

Bir web uygulaması oluştururken kullanabileceğiniz çeşitli araçlar ve framework’ler vardır. Ancak, Laravel'in modern, full stack web uygulamaları oluşturmak için en iyi seçeneklerden biri olduğunu söyleyebiliriz.

## Genişleyen Bir Framework

Laravel'i "genişleyen" bir framework olarak adlandırmayı seviyoruz. Bununla, Laravel'in sizinle birlikte büyüdüğünü kastediyoruz. Web geliştirmeye ilk adımlarınızı atıyorsanız, Laravel'in geniş dokümantasyon kütüphanesi, kılavuzları ve [video eğitimleri](https://laracasts.com/), bunalmadan öğrenmenize yardımcı olacaktır.

Üst düzey bir geliştiriciyseniz, Laravel size bağımlılık enjeksiyonu (dependecy injections), birim testi (unit testing), kuyruklar (queues), gerçek zamanlı olaylar (real-time events) ve daha fazlası için sağlam araçlar sunar. Laravel, profesyonel web uygulamaları oluşturmak için ince ayarlanmıştır ve kurumsal iş yüklerini kaldırmaya hazırdır.

## Ölçeklenebilir Bir Framework

Laravel inanılmaz derecede ölçeklenebilir. PHP'nin ölçeklendirme dostu doğası ve Laravel'in Redis gibi hızlı, dağıtılmış önbellek sistemleri için yerleşik desteği sayesinde, Laravel ile yatay ölçeklendirme çocuk oyuncağıdır. Aslında, Laravel uygulamaları ayda yüz milyonlarca isteği karşılayacak şekilde kolayca ölçeklendirilmiştir.

## Full Stack Bir Framework

Laravel bir full stack framework olarak hizmet verebilir. "Full stack" framework ile Laravel'i uygulamanıza istekleri yönlendirmek ve Blade şablonları veya [Inertia](https://inertiajs.com/) gibi SPA bir uygulama hibrit teknolojisi aracılığıyla frontend oluşturmak için kullanacağınızı kastediyoruz. Bu, Laravel framework'ünü kullanmanın en yaygın yoludur ve bize göre Laravel'i kullanmanın en verimli yoludur.

Laravel'i bu şekilde kullanmayı planlıyorsanız, frontend geliştirme, routing, views veya Eloquent ORM hakkındaki belgelerimize göz atmak isteyebilirsiniz. Ek olarak, [Livewire](https://livewire.laravel.com/) ve [Inertia](https://inertiajs.com/) gibi topluluk paketleri hakkında bilgi edinmek ilginizi çekebilir. Bu paketler, SPA JavaScript uygulamalarının sağladığı birçok kullanıcı arayüzü avantajından yararlanırken Laravel'i full stack bir framework olarak kullanmanıza olanak tanır.

## Topluluk Framework’ü

Laravel, PHP ekosistemindeki en iyi paketlerini bir araya getirerek mevcut en sağlam ve geliştirici dostu framework’ü sunmaktadır. Buna ek olarak, dünyanın dört bir yanından binlerce yetenekli geliştirici bu framework'e katkıda bulunmuştur.

# `#` Laravel API Backend

---

Laravel ayrıca bir JavaScript SPA uygulaması veya mobil uygulama için bir API backend olarak da hizmet verebilir. Örneğin, Laravel'i örneğin [Next.js](https://nextjs.org/) uygulamanız için bir API backend olarak kullanabilirsiniz. Bu bağlamda, Laravel'i uygulamanız için kimlik doğrulama ve veri depolama / alma sağlamak için kullanabilir ve aynı zamanda Laravel'in kuyruklar (queues), e-postalar, bildirimler (notifications) ve daha fazlası gibi güçlü hizmetlerinden yararlanabilirsiniz.