# `#` Formlar
---
## CSRF Alanı

Uygulamanızda bir HTML formu tanımladığınızda, CSRF koruma ara yazılımının isteği doğrulayabilmesi için forma gizli bir CSRF belirteci alanı eklemelisiniz. Belirteç alanını oluşturmak için `@csrf` Blade yönergesini kullanabilirsiniz:

```php
<form method="POST" action="/profile">
    @csrf
 
    ...
</form>
```

## Method Alanı

HTML formları `PUT`, `PATCH` veya `DELETE` istekleri yapamadığından, bu HTTP fiillerini taklit etmek için gizli bir `_method` alanı eklemeniz gerekecektir. Bu alanı `@method` Blade yönergesi sizin için oluşturabilir:

```php
<form action="/foo/bar" method="POST">
    @method('PUT')
 
    ...
</form>
```

## Doğrulama Hataları

`@error` yönergesi, belirli bir nitelik için doğrulama hata iletilerinin var olup olmadığını hızlıca kontrol etmek için kullanılabilir. Bir `@error` yönergesi içinde, hata mesajını görüntülemek için `$message` değişkenini yankılayabilirsiniz:

```php
<!-- /resources/views/post/create.blade.php -->
 
<label for="title">Post Title</label>
 
<input id="title"
    type="text"
    class="@error('title') is-invalid @enderror">
 
@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

`@error` yönergesi bir "if" deyimine derlendiğinden, bir öznitelik için hata olmadığında içerik oluşturmak için `@else` yönergesini kullanabilirsiniz:

```php
<!-- /resources/views/auth.blade.php -->
 
<label for="email">Email address</label>
 
<input id="email"
    type="email"
    class="@error('email') is-invalid @else is-valid @enderror">
```

Birden fazla form içeren sayfalarda doğrulama hata mesajlarını almak için `@error` yönergesine ikinci parametre olarak belirli bir hata torbasının adını aktarabilirsiniz:

```php
<!-- /resources/views/auth.blade.php -->
 
<label for="email">Email address</label>
 
<input id="email"
    type="email"
    class="@error('email', 'login') is-invalid @enderror">
 
@error('email', 'login')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```
