Service provider (hizmet sağlayıcılar) tüm Laravel uygulama önyüklemesinin merkezi yeridir. Kendi uygulamanızın yanı sıra Laravel'in tüm çekirdek hizmetleri service providers (hizmet sağlayıcılar) aracılığıyla önyüklenir.

Peki, "bootstrapped" derken neyi kastediyoruz? Genel olarak, service container (hizmet kapsayıcı) bağlarının, event listenerslerinin (olay dinleyicilerinin), middlewarelerin ve hatta routlerin kaydedilmesi de dahil olmak üzere bir şeylerin kaydedilmesini kastediyoruz. Service providers (Hizmet sağlayıcılar) uygulamanızı yapılandırmak için merkezi bir yerdir.

Laravel ile birlikte gelen `config/app.php` dosyasını açarsanız, bir `providers` dizisi göreceksiniz. Bunlar uygulamanız için yüklenecek olan tüm service providers (hizmet sağlayıcı) sınıflarıdır. Varsayılan olarak, bir dizi Laravel çekirdek service providers (hizmet sağlayıcısı) bu dizide listelenir. Bu sağlayıcılar mailer, queue, cache ve diğerleri gibi çekirdek Laravel bileşenlerini önyükler. Bu sağlayıcıların çoğu "deferred/ertelenmiş" sağlayıcılardır, yani her istekte yüklenmezler, sadece sağladıkları hizmetlere gerçekten ihtiyaç duyulduğunda yüklenirler.

