# `#` Laravel Kurulumu Öncesi

---

İlk Laravel projenizi oluşturmadan önce, yerel makinenizde PHP sunucunuz ve [Composer](https://getcomposer.org/)'ın kurulu olduğundan emin olun. Eğer macOS veya Windows üzerinde geliştirme yapıyorsanız, PHP ve Composer [Laravel Herd](https://herd.laravel.com/) aracılığıyla dakikalar içinde kurulabilir ancak ücretsiz sürümünün kısıtlı özelliklere sahip olması ve lisans fiyatının ülkemize göre yüksek olması sebebiyle [Laragon](https://laragon.org/index.html) web sunucu yazılımını tercih edebilirsiniz. Ek olarak, [Node ve NPM](https://nodejs.org/) yüklemenizi öneririz.

# `#` İlk Laravel Projesi

---

PHP ve Composer'ı kurduktan sonra, Composer'ın `create-project` komutu ile yeni bir Laravel projesi oluşturabilirsiniz:

```shell
composer create-project laravel/laravel:^11.0 ornek-uygulama
```

Ya da, Composer aracılığıyla [Laravel Installer](https://github.com/laravel/installer) aracını global olarak yükleyerek yeni Laravel projeleri oluşturabilirsiniz:

```shell
composer global require laravel/installer
 
laravel new ornek-uygulama
```

Proje oluşturulduktan sonra, Laravel Artisan'ın `serve` komutunu kullanarak Laravel'in yerel geliştirme sunucusunu başlatın:

```shell
cd ornek-uygulama
 
php artisan serve
```

Artisan geliştirme sunucusunu başlattıktan sonra, uygulamanız web tarayıcınızda [http://localhost:8000](http://localhost:8000/) adresinden erişilebilir olacaktır.