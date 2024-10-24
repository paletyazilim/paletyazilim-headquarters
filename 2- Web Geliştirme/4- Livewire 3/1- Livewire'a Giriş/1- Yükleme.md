Livewire bir Laravel paketidir, bu nedenle Livewire'ı yükleyip kullanabilmeniz için önce bir Laravel uygulamasına sahip olmanız gerekir. Yeni bir Laravel uygulaması kurmak için yardıma ihtiyacınız varsa, lütfen Laravel belgelerine bakın.

Livewire'ı yüklemek için terminalinizi açın ve Laravel uygulama dizininize gidin, ardından aşağıdaki komutu çalıştırın:

```shell
composer require livewire/livewire
```

Hepsi bu kadar - gerçekten. Daha fazla özelleştirme seçeneği istiyorsanız okumaya devam edin.

> **`/livewire/livewire.js` 404 durum kodu döndürüyor**
> 
> Varsayılan olarak, Livewire JavaScript varlıklarını sunmak için uygulamanızda bir rota sunar: `/livewire/livewire.js`. Bu çoğu uygulama için uygundur, ancak Nginx'i özel bir yapılandırmayla kullanıyorsanız bu uç noktadan 404 alabilirsiniz. Bu sorunu çözmek için Livewire'ın JavaScript varlıklarını kendiniz derleyebilir ya da Nginx'i buna izin verecek şekilde yapılandırabilirsiniz.

## `#` Yapılandırma Dosyasını Oluşturma
---
Livewire "zero-config "dir, yani herhangi bir ek yapılandırma yapmadan, kurallara uyarak kullanabilirsiniz. Ancak, gerekirse, aşağıdaki Artisan komutunu çalıştırarak Livewire'ın yapılandırma dosyasını yayınlayabilir ve özelleştirebilirsiniz:

```shell
php artisan livewire:publish --config
```

Bu, Laravel uygulamanızın `config` dizininde yeni bir `livewire.php` dosyası oluşturacaktır.

## `#` Livewire'in Frontend Kaynaklarını Manuel Dahil Etme
---
Varsayılan olarak Livewire, ihtiyaç duyduğu JavaScript ve CSS kaynaklarını bir Livewire bileşeni içeren her sayfaya enjekte eder. 

Bu davranış üzerinde daha fazla kontrol sahibi olmak istiyorsanız aşağıdaki Blade direktiflerini kullanarak varlıkları sayfaya manuel olarak ekleyebilirsiniz:

```html
<html>
<head>
    ...
    @livewireStyles
</head>
<body>
    ...
    @livewireScripts
</body>
</html>
```

Bu kaynakları bir sayfaya manuel olarak eklediğinizde, Livewire kaynakları otomatik olarak enjekte etmeyeceğini bilir.

> **AlpineJS Livewire ile birlikte gelir**
> 
> Alpine, Livewire'ın JavaScript kaynaklarıyla birlikte geldiğinden, Alpine'i kullanmak istediğiniz her sayfaya `@livewireScripts` eklemeniz gerekir. O sayfada Livewire kullanmıyor olsanız bile.

Nadiren gerekli olsa da, uygulamanızın `config/livewire.php` dosyasındaki `inject_assets` yapılandırma seçeneğini güncelleyerek Livewire'ın otomatik kaynak enjekte etme davranışını devre dışı bırakabilirsiniz:

```web
'inject_assets' => false,
```

Livewire'ı kaynaklarını tek bir sayfaya veya birden fazla sayfaya enjekte etmeye zorlamak isterseniz, geçerli rotadan veya bir service provider'dan aşağıdaki global metotu çağırabilirsiniz.

```php
\Livewire\Livewire::forceAssetInjection();
```

## `#` Livewire Güncelleme Uç Noktasını Değiştirme
---
Bir Livewire bileşenindeki her güncelleme aşağıdaki uç noktadan (endpoint) sunucuya bir ağ isteği gönderir: `{http} https://example.com/livewire/update`

Bu, yerelleştirme veya multi-tenancy çoklu kiracılık kullanan bazı uygulamalar için bir sorun olabilir.

Bu durumlarda, kendi uç noktanızı istediğiniz gibi kaydedebilirsiniz ve bunu `Livewire::setUpdateRoute()` içinde yaptığınız sürece, Livewire tüm bileşen güncellemeleri için bu uç noktayı kullanacaktır:

```php
Livewire::setUpdateRoute(function ($handle) {
    return Route::post('/custom/livewire/update', $handle);
});
```

Artık Livewire, `{http icon} /livewire/update` kullanmak yerine bileşen güncellemelerini `{http icon} /custom/livewire/update` adresine gönderecektir.

Livewire kendi güncelleme rotanızı kaydetmenize izin verdiğinden, Livewire'ın kullanmasını istediğiniz herhangi bir ek middleware'i doğrudan `setUpdateRoute()` metodu içinde bildirebilirsiniz:

```php
Livewire::setUpdateRoute(function ($handle) {
    return Route::post('/custom/livewire/update', $handle)
        ->middleware([...]); 
});
```

## `#` Kaynak URL'sini Değiştirme
---
Varsayılan olarak, Livewire JavaScript varlıklarını aşağıdaki URL'den sunacaktır: `{http icon} https://example.com/livewire/livewire.js`. Ek olarak, Livewire bu varlığa bir script etiketinden şu şekilde referans verecektir:

```html
<script src="/livewire/livewire.js" ...
```

Uygulamanızda yerelleştirme veya çoklu kiracılık (multi-tenancy) nedeniyle global rota önekleri varsa, Livewire'ın JavaScript'ini getirirken dahili olarak kullanması gereken kendi uç noktanızı kaydedebilirsiniz.

Özel bir JavaScript kaynak uç noktası kullanmak için `Livewire::setScriptRoute()` metotu içinde kendi rotanızı kaydedebilirsiniz:

```php
Livewire::setScriptRoute(function ($handle) {
    return Route::get('/custom/livewire/livewire.js', $handle);
});
```

```html
<script src="/custom/livewire/livewire.js" ...
```

## `#` Livewire ve Alpine'nin Manuel Paketlenmesi
---
#çevrilecek 