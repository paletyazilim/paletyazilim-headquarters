# `#` View (Görüntü) Rotaları
---
Rotanızın yalnızca bir view döndürmesi gerekiyorsa, `Route::view` metodunu kullanabilirsiniz. `redirect` metodu gibi, bu metotta de tam bir rota veya controller tanımlamak zorunda kalmamanız için basit bir kısayol sağlar. `view` metodu ilk argüman olarak bir URI ve ikinci argüman olarak bir view adı kabul eder. Buna ek olarak, isteğe bağlı üçüncü bir bağımsız değişken olarak view’e aktarılacak bir veri dizisi sağlayabilirsiniz:

```php
Route::view('/gorunum', 'welcome');

Route::view('/gorunum', 'welcome', ['isim' => 'Berat']);
```

View rotalarında rota parametrelerini kullanırken, aşağıdaki parametreler Laravel tarafından rezervedir ve kullanılamaz: `view`, `data`, `status` ve `header`.