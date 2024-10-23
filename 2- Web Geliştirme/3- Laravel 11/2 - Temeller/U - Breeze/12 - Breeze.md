# `#` Giriş
---
Laravel Breeze, Laravel'ın tüm kimlik doğrulama özelliklerinin minimal ve basit bir uygulamasıdır. Bu özellikler arasında giriş, kayıt, şifre sıfırlama, e-posta doğrulama ve şifre onayı bulunur. Ayrıca, Breeze kullanıcının adını, e-posta adresini ve şifresini güncelleyebileceği basit bir "profil" sayfasını da içerir.

Laravel Breeze'nin varsayılan görünüm katmanı, Tailwind CSS ile stil verilmiş basit Blade şablonlarından oluşur. Ayrıca, Breeze, Livewire veya Inertia'ya dayalı iskeleme seçenekleri sunar ve Inertia tabanlı iskeleme için Vue veya React kullanma seçeneği sunar.

# `#` Kurulum
---
Öncelikle yeni bir Laravel uygulaması oluşturmanız gerekmektedir. Laravel kurulumunu Laravel yükleyicisini kullanarak yaparsanız, kurulum sürecinde Laravel Breeze'ı yüklemek isteyip istemediğiniz sorulacaktır. Aksi takdirde, aşağıdaki manuel kurulum talimatlarını takip etmeniz gerekecektir.

Eğer daha önceden bir başlangıç paketi olmadan yeni bir Laravel uygulaması oluşturduysanız, Laravel Breeze'ı Composer kullanarak manuel olarak kurmanız gerekmektedir.

```shell
composer require laravel/breeze --dev
```

Laravel Breeze paketi Composer tarafından yüklendikten sonra, `breeze:install` Artisan komutunu çalıştırmalısınız. Bu komut, kimlik doğrulama görünümlerini, rotalarını, denetleyicilerini ve diğer kaynakları uygulamanıza yayınlar. Laravel Breeze, tüm kodunu uygulamanıza yayınlar, böylece özelliklerine ve uygulamasına tam kontrol ve görünürlük sağlarsınız.

`breeze:install` komutu, tercih ettiğiniz ön uç yığını ve test çerçevesi için size soru soracaktır:

```shell
php artisan breeze:install
 
php artisan migrate
npm install
npm run dev
```

## Breeze ve Blade

Varsayılan Breeze "yığını" Blade yığınıdır ve uygulamanın ön uçunu oluşturmak için basit Blade şablonlarını kullanır. Blade yığını, `breeze:install` komutunu başka hiçbir ek argüman olmadan çağırarak ve Blade ön uç yığını seçerek yüklenebilir. Breeze'in iskeleti yüklendikten sonra uygulamanın ön uç varlıklarını da derlemeniz gerekmektedir.

Sonraki adımda, web tarayıcınızda uygulamanızın `/login` veya `/register` URL'lerine gidebilirsiniz. Breeze'in tüm rotaları `routes/auth.php` dosyası içinde tanımlanmıştır.