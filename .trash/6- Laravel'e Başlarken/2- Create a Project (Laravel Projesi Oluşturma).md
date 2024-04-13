İlk Laravel projenizi oluşturmadan önce, yerel makinenizde PHP ve Composer'ın kurulu olduğundan emin olun.

PHP ve Composer'ı kurduktan sonra terminalde, Composer'ın create-project komutu ile yeni bir Laravel projesi oluşturabilirsiniz:

```
composer create-project laravel/laravel ornek-uygulama
```

Ya da, Composer aracılığıyla Laravel yükleyicisini global olarak yükleyerek yeni Laravel projeleri oluşturabilirsiniz:

```
composer global require laravel/installer
 
laravel new ornek-uygulama
```

Proje oluşturulduktan sonra, Laravel Artisan'ın `serve` komutunu kullanarak Laravel'in yerel geliştirme sunucusunu başlatın:

```
cd ornek-uygulama
 
php artisan serve
```

Artisan geliştirme sunucusunu başlattıktan sonra, uygulamanız web tarayıcınızda http://localhost:8000 adresinden erişilebilir olacaktır. Daha sonra, Laravel ekosisteminde sonraki adımlarınızı atmaya hazırsınız. Elbette, bir veritabanı yapılandırmak da isteyebilirsiniz.

