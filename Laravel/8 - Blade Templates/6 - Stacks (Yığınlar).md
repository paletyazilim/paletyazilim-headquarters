# `#` Stacks (Yığınlar)
---
Blade, başka bir görünümde veya şablonda başka bir yerde oluşturulabilecek adlandırılmış yığınları itmenize olanak tanır. Bu, özellikle alt görünümleriniz için gereken JavaScript kütüphanelerini belirtmek için yararlı olabilir:

```php
@push('scripts')
    <script src="/example.js"></script>
@endpush
```

Belirli bir boolean ifadenin `true` olarak değerlendirilmesi durumunda içeriği `@push` etmek isterseniz, `@pushIf` yönergesini kullanabilirsiniz:

```php
@pushIf($shouldPush, 'scripts')
    <script src="/example.js"></script>
@endPushIf
```

Bir yığına gerektiği kadar itme yapabilirsiniz. Yığın içeriğinin tamamını işlemek için yığının adını `@stack` yönergesine aktarın:

```php
<head>
    <!-- Head Contents -->
 
    @stack('scripts')
</head>
```

İçeriği bir yığının başına eklemek isterseniz, `@prepend` yönergesini kullanmalısınız:

```php
@push('scripts')
    This will be second...
@endpush
 
// Later...
 
@prepend('scripts')
    This will be first...
@endprepend
```
