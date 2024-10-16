# `#` İlk Yapılandırma

---

Laravel framework’ü için tüm yapılandırma dosyaları `config` dizininde saklanır. Her seçenek belgelenmiştir, bu nedenle dosyalara bakmaktan çekinmeyin ve kullanabileceğiniz seçeneklere aşina olun.

Laravel ilk kurulduktan neredeyse hiçbir ek yapılandırmaya ihtiyaç duymaz. Geliştirmeye başlamakta özgürsünüz! Ancak, `config/app.php` dosyasını ve belgelerini incelemek isteyebilirsiniz. Uygulamanıza göre değiştirmek isteyebileceğiniz `timezone` ve `locale` gibi çeşitli seçenekler içerir.

# `#` Ortam Yapılandırması

---

Laravel'in yapılandırma seçenek değerlerinin çoğu, uygulamanızın yerel makinenizde veya fiziki bir web sunucusunda çalışmasına bağlı olarak değişebileceğinden, birçok önemli yapılandırma değeri uygulamanızın kök dizininde bulunan `.env` dosyası kullanılarak tanımlanır.

Uygulamanızı kullanan her geliştirici / sunucu farklı bir ortam yapılandırması gerektirebileceğinden, `.env` dosyanız uygulamanızın kaynak kontrolüne işlenmemelidir. Ayrıca, davetsiz bir misafirin kaynak kontrol havuzunuza erişim sağlaması durumunda, hassas kimlik bilgileri açığa çıkacağından bu bir güvenlik riski oluşturacaktır.

# `#` Veritabanları ve Migration’lar

---

Artık Laravel uygulamanızı oluşturduğunuza göre, muhtemelen bazı verileri bir veritabanında saklamak istiyorsunuz. Varsayılan olarak, uygulamanızın `.env` yapılandırma dosyası Laravel'in bir SQLite veritabanı yapısını kullanır.

Projenin oluşturulması sırasında Laravel sizin için bir `database/database.sqlite` dosyası oluşturdu ve uygulamanın veritabanı tablolarını oluşturmak için gerekli migration’ları çalıştırdı.

MySQL veya PostgreSQL gibi başka bir veritabanı yapısı kullanmayı tercih ederseniz, `.env` yapılandırma dosyanızı uygun veritabanını kullanacak şekilde güncelleyebilirsiniz. Örneğin, MySQL kullanmak istiyorsanız, `.env` yapılandırma dosyanızın DB_* değişkenlerini aşağıdaki gibi güncelleyin:

```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=
```

SQLite dışında bir veritabanı kullanmayı seçerseniz, veritabanını oluşturmanız ve uygulamanızın veritabanı migration’larını çalıştırmanız gerekecektir:

```shell
php artisan migrate
```

# `#` Dosya Yapılandırması

Laravel uygulamanız her zaman web sunucunuz için yapılandırılmış "web dizini"nin kökünden sunulmalıdır. Bir Laravel uygulamasını "web dizini"nin bir alt dizininden sunmaya çalışmamalısınız. Bunu yapmaya çalışmak, uygulamanızda bulunan hassas dosyaları açığa çıkarabilir.

Daha basit bir şekilde anlatılacak olursak projeniz dosyaları web sunucunuzun `htdocs` , `www` , `public_html` kök dizinlerde olduğu gibi bulunmalıdır. Bu, web sunucusunun doğrudan uygulamanın kök dizinine erişim sağlamasını sağlar ve uygulamanın diğer dosyalarının doğrudan erişilemez hale gelmesini sağlar. Ancak proje herhangi bir alt klasörden çağrıldığı takdirde güvenlik sorunlarına yol açabilir.