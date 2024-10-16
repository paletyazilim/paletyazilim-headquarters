# `#` Rotalarınızı Listeleme
---

`route:list` Artisan komutu, uygulamanız tarafından tanımlanan tüm rotaların genel bir listesini kolayca sağlayabilir:

```shell
php artisan route:list
```

Varsayılan olarak, her rotaya atanan rota middleware `route:list` çıktısında görüntülenmeyecektir; ancak, komuta `-v` seçeneğini ekleyerek Laravel'e rota middleware ve middleware grup adlarını görüntülemesi talimatını verebilirsiniz:

```shell
php artisan route:list -v
 
# Middleware Gruplarını Genişletir
php artisan route:list -vv
```

Laravel'e sadece belirli bir URI ile başlayan rotaları göstermesi talimatını da verebilirsiniz:

```shell
php artisan route:list --path=api
```

Buna ek olarak, `route:list` komutunu çalıştırırken `--except-vendor` seçeneğini sağlayarak Laravel'e üçüncü taraf paketleri tarafından tanımlanan rotaları gizlemesi talimatını verebilirsiniz:

```shell
php artisan route:list --except-vendor
```

Aynı şekilde, `route:list` komutunu çalıştırırken `--only-vendor` seçeneğini sağlayarak Laravel'e sadece üçüncü taraf paketleri tarafından tanımlanan rotaları göstermesi talimatını da verebilirsiniz:

```shell
php artisan route:list --only-vendor
```

# `#` Rotaları Özelleştirme
---
