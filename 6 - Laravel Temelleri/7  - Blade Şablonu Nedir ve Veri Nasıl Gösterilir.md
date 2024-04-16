# `#` Giriş
---
Blade, Laravel ile birlikte gelen basit ancak güçlü bir şablon motorudur. Bazı PHP şablon motorlarının aksine, Blade şablonlarında düz PHP kodu kullanmanızı kısıtlamaz. Aslında, tüm Blade şablonları düz PHP koduna derlenir ve değiştirilene kadar önbelleğe alınır, bu da Blade'in uygulamanıza neredeyse hiçbir ek yük getirmediği anlamına gelir. Blade şablon dosyaları `.blade.php` dosya uzantısını kullanır ve genellikle `resources/views` dizininde saklanır.

Blade görünümleri, rotalardan veya controller'lardan global `view` yardımcı metodu kullanılarak döndürülebilir. Tabii ki, görünümlerle ilgili belgelerde belirtildiği gibi, veriler Blade görünümüne geçirilebilir, `view` metodunun ikinci argümanı kullanılarak:

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'Finn']);
});
```

# `#` Gönderilen Veriyi Gösterme
---
Blade görünümlerine aktarılan verileri süslü parantez `{{ }}` içine alarak görüntüleyebilirsiniz. Örneğin, aşağıdaki rota verildiğinde:

```php
Route::get('/', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

`name` değişkeninin içeriğini şu şekilde görüntüleyebilirsiniz:

```php
Hello, {{ $name }}.
```

Blade'nin `{{ }}` echo ifadeleri, XSS saldırılarını önlemek için otomatik olarak PHP'nin `htmlspecialchars` fonksiyonundan geçirilir

Görünüme iletilen değişkenlerin içeriğini görüntülemekle sınırlı değilsiniz. Ayrıca herhangi bir PHP fonksiyonunun sonuçlarını da yazdırabilirsiniz. Aslında, Blade echo ifadesi içine istediğiniz herhangi bir PHP kodunu yerleştirebilirsiniz..

```php
Mevcut UNIX zaman damgası {{ time() }}.
```


