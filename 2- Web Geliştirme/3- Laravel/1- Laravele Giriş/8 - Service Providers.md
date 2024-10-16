# `#` Giriş
---

Service providers tüm Laravel uygulama bootstrap’inin (önyüklemesinin) merkezi yeridir. Kendi uygulamanızın yanı sıra Laravel'in tüm çekirdek hizmetleri service providers aracılığıyla bootstrap’lanir (önyüklenir).

Peki, "bootstraped" derken neyi kastediyoruz? Genel olarak, service providers bağlarının,event listeners (olay dinleyicilerinin), middleware’lerin (ara yazılımların) ve hatta route’ların (rotaların) kaydedilmesi de dahil olmak üzere bir şeylerin kaydedilmesini kastediyoruz. Service providers uygulamanızı yapılandırmak için merkezi bir yerdir.

Laravel, mailer, queue, cache ve diğerleri gibi temel hizmetlerini bootstrap (önyüklemek) yapabilmek için dahili olarak düzinelerce service providers kullanır. Bu provider’ların çoğu **deffered** (ertelenmiş) sağlayıcılardır, yani her istekte yüklenmezler, ancak sağladıkları hizmetlere gerçekten ihtiyaç duyulduğunda yüklenirler.

Tüm kullanıcı tanımlı service provider’lar `bootstrap/providers.php` dosyasına kaydedilir. Aşağıdaki dokümantasyonda, kendi servis sağlayıcılarınızı nasıl yazacağınızı ve Laravel uygulamanıza nasıl kaydedeceğinizi öğreneceksiniz.

# `#` Service Providers Oluşturma
---
#çevrilecek 