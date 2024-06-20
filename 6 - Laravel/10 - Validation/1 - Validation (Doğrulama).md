# `#` Giriş
---
Laravel, uygulamanızın gelen verilerini doğrulamak için birkaç farklı yaklaşım sağlar. Gelen tüm HTTP isteklerinde mevcut olan `validate` metodunu kullanmak en yaygın olanıdır. Ancak, doğrulama için diğer yaklaşımları da tartışacağız.

Laravel, verilere uygulayabileceğiniz çok çeşitli uygun doğrulama kuralları içerir, hatta değerlerin belirli bir veritabanı tablosunda benzersiz olup olmadığını doğrulama yeteneği sağlar. Laravel'in tüm doğrulama özelliklerine aşina olmanız için bu doğrulama kurallarının her birini ayrıntılı olarak ele alacağız.

# `#` Doğrulamaya Genel Bir Bakış
---
Laravel'in güçlü doğrulama özelliklerini öğrenmek için, bir formun doğrulanması ve hata mesajlarının kullanıcıya geri gösterilmesi ile ilgili tam bir örneğe bakalım. Bu üst düzey genel bakışı okuyarak, Laravel kullanarak gelen istek verilerinin nasıl doğrulanacağı hakkında iyi bir genel anlayış kazanabileceksiniz:

## Rotaların Tanımlanması

İlk olarak, `routes/web.php` dosyamızda aşağıdaki rotaların tanımlı olduğunu varsayalım:

```php
use App\Http\Controllers\PostController;
 
Route::get('/post/create', [PostController::class, 'create']);
Route::post('/post', [PostController::class, 'store']);
```

`GET` rotası, kullanıcının yeni bir blog yazısı oluşturması için bir form görüntüler, `POST` rotası ise yeni blog yazısını veritabanında depolar.

## Controller'ın Oluşturulması

Daha sonra, bu rotalara gelen istekleri işleyen basit bir controller'a göz atalım. Şimdilik `store` metodunu boş bırakacağız:

```php
<?php
 
namespace App\Http\Controllers;
 
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\View\View;
 
class PostController extends Controller
{
    /**
     * Show the form to create a new blog post.
     */
    public function create(): View
    {
        return view('post.create');
    }
 
    /**
     * Store a new blog post.
     */
    public function store(Request $request): RedirectResponse
    {
        // Validate and store the blog post...
 
        $post = /** ... */
 
        return to_route('post.show', ['post' => $post->id]);
    }
}
```

## Doğrulama Mantığının Oluşturulması

Şimdi yeni blog gönderisini doğrulamak için `store` metodumuzu mantıkla doldurmaya hazırız. Bunu yapmak için `Illuminate\Http\Request` nesnesi tarafından sağlanan `validate` metodunu kullanacağız. Doğrulama kuralları geçerse kodunuz normal şekilde çalışmaya devam eder; ancak doğrulama başarısız olursa bir `Illuminate\Validation\ValidationException` istisnası atılır ve uygun hata yanıtı otomatik olarak kullanıcıya geri gönderilir.

Geleneksel bir HTTP isteği sırasında doğrulama başarısız olursa, önceki URL'ye bir yönlendirme yanıtı oluşturulur. Gelen istek bir XHR isteği ise, doğrulama hata mesajlarını içeren bir JSON yanıtı döndürülür.

`validate` metodunu daha iyi anlamak için `store` metoduna geri dönelim:

```php
/**
 * Store a new blog post.
 */
public function store(Request $request): RedirectResponse
{
    $validated = $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ]);
 
    // The blog post is valid...
 
    return redirect('/posts');
}
```

Gördüğünüz gibi, doğrulama kuralları `validate` metoduna aktarılır. Endişelenmeyin - mevcut tüm doğrulama kuralları belgelenmiştir. Yine, doğrulama başarısız olursa, uygun yanıt otomatik olarak oluşturulacaktır. Doğrulama geçerse, controllerimiz normal şekilde çalışmaya devam edecektir.

Alternatif olarak, doğrulama kuralları tek bir `|` sınırlandırılmış dize yerine kural dizileri olarak belirtilebilir:


```php
$validatedData = $request->validate([
    'title' => ['required', 'unique:posts', 'max:255'],
    'body' => ['required'],
]);
```

Ayrıca, bir isteği doğrulamak ve tüm hata mesajlarını isimlendirilmiş bir hata torbasında saklamak için `validateWithBag` metodunu kullanabilirsiniz:

```php
$validatedData = $request->validateWithBag('post', [
    'title' => ['required', 'unique:posts', 'max:255'],
    'body' => ['required'],
]);
```

### İlk Doğrulama Hatasında Durdurma

Bazen ilk doğrulama hatasından sonra bir nitelik üzerinde doğrulama kurallarını çalıştırmayı durdurmak isteyebilirsiniz. Bunu yapmak için, niteliğe `bail` kuralı atayın:

```php
$request->validate([
    'title' => 'bail|required|unique:posts|max:255',
    'body' => 'required',
]);
```

Bu örnekte, `title` niteliğindeki `unique` kuralı başarısız olursa, `maks` kuralı kontrol edilmeyecektir. Kurallar atandıkları sırayla doğrulanacaktır.

### İç İçe Nitelikler Hakkında Ufak Bir Not

Gelen HTTP isteği "iç içe" alan verileri içeriyorsa, bu alanları doğrulama kurallarınızda "nokta" sözdizimini kullanarak belirtebilirsiniz:

```php
$request->validate([
    'title' => 'required|unique:posts|max:255',
    'author.name' => 'required',
    'author.description' => 'required',
]);
```

Öte yandan, alan adınız gerçek bir nokta içeriyorsa, noktayı ters eğik çizgi ile önleyerek bunun "nokta" sözdizimi olarak yorumlanmasını açıkça önleyebilirsiniz:

```php
$request->validate([
    'title' => 'required|unique:posts|max:255',
    'v1\.0' => 'required',
]);
```

## Doğrulama Hatalarının Gösterimi

Peki, gelen istek alanları verilen doğrulama kurallarını geçemezse ne olur? Daha önce de belirtildiği gibi, Laravel kullanıcıyı otomatik olarak önceki konumuna geri yönlendirecektir. Buna ek olarak, tüm doğrulama hataları ve istek girdisi otomatik olarak oturuma yansıtılacaktır.

Bir `$errors` değişkeni, `web` middleware grubu tarafından sağlanan `Illuminate\View\Middleware\ShareErrorsFromSession` middleware'i tarafından uygulamanızın tüm görünümleriyle paylaşılır. Bu middleware uygulandığında, görünümlerinizde her zaman bir `$errors` değişkeni bulunur ve bu sayede `$errors` değişkeninin her zaman tanımlı olduğunu ve güvenle kullanılabileceğini varsayabilirsiniz. `$errors` değişkeni `Illuminate\Support\MessageBag`'in bir örneği olacaktır.

Bu nedenle, örneğimizde, doğrulama başarısız olduğunda kullanıcı controllerimiz `create` metoduna yönlendirilecek ve hata mesajlarını görünümde görüntülememize izin verecektir:

```php
<!-- /resources/views/post/create.blade.php -->
 
<h1>Create Post</h1>
 
@if ($errors->any())
    <div class="alert alert-danger">
        <ul>
            @foreach ($errors->all() as $error)
                <li>{{ $error }}</li>
            @endforeach
        </ul>
    </div>
@endif
 
<!-- Create Post Form -->
```

### Hata Mesajlarını Özelleştirme

Laravel'in yerleşik doğrulama kurallarının her biri uygulamanızın `lang/en/validation.php` dosyasında bulunan bir hata mesajına sahiptir. Eğer uygulamanızın bir `lang` dizini yoksa, `lang:publish` Artisan komutunu kullanarak Laravel'e bunu oluşturmasını söyleyebilirsiniz.

`lang/en/validation.php` dosyası içinde, her doğrulama kuralı için bir çeviri girişi bulacaksınız. Uygulamanızın ihtiyaçlarına göre bu mesajları değiştirmekte veya düzenlemekte özgürsünüz.

Ek olarak, mesajlarınızı uygulamanızın diline çevirmek için bu dosyayı başka bir dil dizinine kopyalayabilirsiniz. Laravel yerelleştirmesi hakkında daha fazla bilgi edinmek için yerelleştirme belgelerinin tamamına göz atın.

>Varsayılan olarak, Laravel uygulama iskeleti `lang` dizinini içermez. Laravel'in dil dosyalarını özelleştirmek isterseniz, bunları `lang:publish` Artisan komutu ile yayınlayabilirsiniz.

### XHR İstekler ve Doğrulama

Bu örnekte, uygulamaya veri göndermek için geleneksel bir form kullandık. Ancak, birçok uygulama JavaScript destekli bir ön uçtan XHR istekleri alır. Bir XHR isteği sırasında `validate` metodu kullanıldığında, Laravel bir yönlendirme yanıtı oluşturmayacaktır. Bunun yerine, Laravel tüm doğrulama hatalarını içeren bir JSON yanıtı oluşturur. Bu JSON yanıtı `422 HTTP` durum kodu ile gönderilecektir.

### `@error` Yönergesi

Belirli bir nitelik için doğrulama hata mesajlarının mevcut olup olmadığını hızlıca belirlemek için `@error` Blade yönergesini kullanabilirsiniz. Bir `@error` yönergesi içinde, hata mesajını görüntülemek için `$message` değişkenini yazdırabilirsiniz:

```php
<!-- /resources/views/post/create.blade.php -->
 
<label for="title">Post Title</label>
 
<input id="title"
    type="text"
    name="title"
    class="@error('title') is-invalid @enderror">
 
@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

İsimlendirilmiş hata torbaları kullanıyorsanız, `@error` yönergesine ikinci argüman olarak hata torbasının adını aktarabilirsiniz:

```php
<input ... class="@error('title', 'post') is-invalid @enderror">
```

## Formları Yeniden Doldurma

Laravel bir doğrulama hatası nedeniyle bir yönlendirme yanıtı oluşturduğunda, framework otomatik olarak isteğin tüm girdisini oturuma aktaracaktır. Bu, bir sonraki istek sırasında girdiye rahatça erişebilmeniz ve kullanıcının göndermeye çalıştığı formu yeniden doldurabilmeniz için yapılır.

Önceki istekten flashed girdiyi almak için, `Illuminate\Http\Request` örneği üzerinde `old` metodunu çağırın. `old` metodu, daha önce flashed giriş verilerini oturumdan çekecektir:

```php
$title = $request->old('title');
```

Laravel ayrıca global bir `old` yardımcısı sağlar. Bir Blade şablonu içinde eski girdiyi görüntülüyorsanız, formu yeniden doldurmak için `old` yardımcısını kullanmak daha uygundur. Eğer verilen alan için eski bir girdi yoksa, null döndürülecektir:

```php
<input type="text" name="title" value="{{ old('title') }}">
```

## Opsiyonel Alanlar Hakkında Ufak Bir Not

Laravel varsayılan olarak `TrimStrings` ve `ConvertEmptyStringsToNull` middlewarelerini uygulamanızın global middleware yığınına dahil eder. Bu nedenle, doğrulayıcının `null` değerleri geçersiz olarak değerlendirmesini istemiyorsanız, genellikle "isteğe bağlı" istek alanlarınızı `nullable` olarak işaretlemeniz gerekecektir. Örneğin:

```php
$request->validate([
    'title' => 'required|unique:posts|max:255',
    'body' => 'required',
    'publish_at' => 'nullable|date',
]);
```

Bu örnekte, `publish_at` alanının `null` veya geçerli bir tarih temsili olabileceğini belirtiyoruz. Kural tanımına `nullable` değiştiricisi eklenmezse, doğrulayıcı `null`'u geçersiz bir tarih olarak değerlendirecektir.

## Doğrulama Hatalarının Yanıt Biçimleri

Uygulamanız bir `Illuminate\Validation\ValidationException` istisnası attığında ve gelen HTTP isteği bir JSON yanıtı beklediğinde, Laravel hata mesajlarını sizin için otomatik olarak biçimlendirecek ve bir 422 İşlenemez Varlık HTTP yanıtı döndürecektir.

Aşağıda, doğrulama hataları için JSON yanıt biçiminin bir örneğini inceleyebilirsiniz. İç içe geçmiş hata anahtarlarının "nokta" gösterim biçiminde düzleştirildiğine dikkat edin:

```php
{
    "message": "The team name must be a string. (and 4 more errors)",
    "errors": {
        "team_name": [
            "The team name must be a string.",
            "The team name must be at least 1 characters."
        ],
        "authorization.role": [
            "The selected authorization.role is invalid."
        ],
        "users.0.email": [
            "The users.0.email field is required."
        ],
        "users.2.email": [
            "The users.2.email must be a valid email address."
        ]
    }
}
```

# `#` Form İsteklerini Doğrulama
---
## Form İstekleri Oluşturma

Daha karmaşık doğrulama senaryoları için bir "form isteği" oluşturmak isteyebilirsiniz. Form istekleri, kendi doğrulama ve yetkilendirme mantığını kapsülleyen özel istek sınıflarıdır. Bir form istek sınıfı oluşturmak için `make:request` Artisan CLI komutunu kullanabilirsiniz:

```shell
php artisan make:request StorePostRequest
```

Oluşturulan form isteği sınıfı `app/Http/Requests` dizinine yerleştirilecektir. Eğer bu dizin mevcut değilse, `make:request` komutunu çalıştırdığınızda oluşturulacaktır. Laravel tarafından oluşturulan her form isteğinin iki metodu vardır: `authorize` ve `rules`.

Tahmin edebileceğiniz gibi, `authorize` metodu o anda kimliği doğrulanmış kullanıcının istek tarafından temsil edilen eylemi gerçekleştirip gerçekleştiremeyeceğini belirlemekten sorumluyken, `rules` metodu isteğin verilerine uygulanması gereken doğrulama kurallarını döndürür:

```php
/**
 * Get the validation rules that apply to the request.
 *
 * @return array<string, \Illuminate\Contracts\Validation\Rule|array|string>
 */
public function rules(): array
{
    return [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ];
}
```

> `rules` metodunun imzası içinde ihtiyaç duyduğunuz bağımlılıkları yazabilirsiniz. Bunlar otomatik olarak Laravel servis konteyneri aracılığıyla çözülecektir.

Peki, doğrulama kuralları nasıl değerlendirilir? Yapmanız gereken tek şey, controller metodunuzda isteği yazmaktır. Gelen form isteği, controller metodu çağrılmadan önce doğrulanır, yani controllerınızı herhangi bir doğrulama mantığı ile karıştırmanıza gerek kalmaz:

```php
/**
 * Store a new blog post.
 */
public function store(StorePostRequest $request): RedirectResponse
{
    // The incoming request is valid...
 
    // Retrieve the validated input data...
    $validated = $request->validated();
 
    // Retrieve a portion of the validated input data...
    $validated = $request->safe()->only(['name', 'email']);
    $validated = $request->safe()->except(['name', 'email']);
 
    // Store the blog post...
 
    return redirect('/posts');
}
```

Doğrulama başarısız olursa, kullanıcıyı önceki konumuna geri göndermek için bir yönlendirme yanıtı oluşturulur. Hatalar da görüntülenebilmeleri için oturuma yansıtılır. İstek bir XHR isteğiyse, kullanıcıya doğrulama hatalarının JSON gösterimini içeren 422 durum kodlu bir HTTP yanıtı döndürülür.

> Inertia destekli Laravel ön ucunuza gerçek zamanlı form isteği doğrulaması eklemeniz mi gerekiyor? Laravel Precognition'a göz atın.

### Ek Doğrulama Gerçekleştirme

Bazen ilk doğrulama tamamlandıktan sonra ek doğrulama gerçekleştirmeniz gerekir. Bunu form isteğinin `after` metodunu kullanarak gerçekleştirebilirsiniz.

`after` metodu, doğrulama tamamlandıktan sonra çağrılacak bir dizi callable veya closure döndürmelidir. Verilen çağrılabilirler bir `Illuminate\Validation\Validator` örneği alır ve gerekirse ek hata mesajları oluşturmanıza olanak tanır:

```php
use Illuminate\Validation\Validator;
 
/**
 * Get the "after" validation callables for the request.
 */
public function after(): array
{
    return [
        function (Validator $validator) {
            if ($this->somethingElseIsInvalid()) {
                $validator->errors()->add(
                    'field',
                    'Something is wrong with this field!'
                );
            }
        }
    ];
}
```

Belirtildiği gibi, `after` metodu tarafından döndürülen dizi çağrılabilir sınıflar da içerebilir. Bu sınıfların `__invoke` metodu bir `Illuminate\Validation\Validator` örneği alacaktır:

```php
use App\Validation\ValidateShippingTime;
use App\Validation\ValidateUserStatus;
use Illuminate\Validation\Validator;
 
/**
 * Get the "after" validation callables for the request.
 */
public function after(): array
{
    return [
        new ValidateUserStatus,
        new ValidateShippingTime,
        function (Validator $validator) {
            //
        }
    ];
}
```

### İlk Doğrulama Hatasında Durdurma

İstek sınıfınıza bir `stopOnFirstFailure` özelliği ekleyerek, doğrulayıcıya tek bir doğrulama hatası oluştuğunda tüm nitelikleri doğrulamayı durdurması gerektiğini bildirebilirsiniz:

```php
/**
 * Indicates if the validator should stop on the first rule failure.
 *
 * @var bool
 */
protected $stopOnFirstFailure = true;
```

### Yönlendirme Konumunu Özelleştirme

Daha önce tartışıldığı gibi, form isteği doğrulaması başarısız olduğunda kullanıcıyı önceki konumuna geri göndermek için bir yönlendirme yanıtı oluşturulacaktır. Ancak, bu davranışı özelleştirmekte özgürsünüz. Bunu yapmak için, form isteğinizde bir `$redirect` özelliği tanımlayın:

```php
/**
 * The URI that users should be redirected to if validation fails.
 *
 * @var string
 */
protected $redirect = '/dashboard';
```

Veya kullanıcıları isimlendirilmiş bir rotaya yönlendirmek isterseniz, bunun yerine bir `$redirectRoute` özelliği tanımlayabilirsiniz:

```php
/**
 * The route that users should be redirected to if validation fails.
 *
 * @var string
 */
protected $redirectRoute = 'dashboard';
```

## Form İsteklerinin Yetkilendirilmesi

Form istek sınıfı ayrıca bir `authorize` metodu içerir. Bu metot, kimliği doğrulanan kullanıcının belirli bir kaynağı güncelleme yetkisine sahip olup olmadığını belirleyebilirsiniz. Örneğin, bir kullanıcının güncellemeye çalıştığı bir blog yorumunun gerçekten sahibi olup olmadığını belirleyebilirsiniz. Büyük olasılıkla, bu metot içinde yetkilendirme kapılarınız (gates) ve politikalarınızla (policy) etkileşime gireceksiniz:

```php
use App\Models\Comment;
 
/**
 * Determine if the user is authorized to make this request.
 */
public function authorize(): bool
{
    $comment = Comment::find($this->route('comment'));
 
    return $comment && $this->user()->can('update', $comment);
}
```

Tüm form istekleri temel Laravel istek sınıfını genişlettiğinden, o anda kimliği doğrulanmış kullanıcıya erişmek için `user` metodunu kullanabiliriz. Ayrıca, yukarıdaki örnekte `route` metoduna yapılan çağrıya dikkat edin. Bu metot, aşağıdaki örnekteki `{comment}` parametresi gibi, çağrılan rotada tanımlanan URI parametrelerine erişim sağlar:

```php
Route::post('/comment/{comment}');
```

Bu nedenle, uygulamanız rota modeli bağlamadan yararlanıyorsa, çözümlenmiş modele isteğin bir özelliği olarak erişerek kodunuzu daha da kısa hale getirebilirsiniz:

```php
return $this->user()->can('update', $this->comment);
```

`authorize` metodu `false` değerini döndürürse, otomatik olarak 403 durum kodlu bir HTTP yanıtı döndürülür ve controller metodunuz yürütülmez.

İstek için yetkilendirme mantığını uygulamanızın başka bir bölümünde ele almayı planlıyorsanız, `authorize` metodunu tamamen kaldırabilir veya sadece `true` değerini döndürebilirsiniz:

```php
/**
 * Determine if the user is authorized to make this request.
 */
public function authorize(): bool
{
    return true;
}
```

> `authorize` metodunun imzası içinde ihtiyacınız olan bağımlılıkları yazabilirsiniz. Bunlar otomatik olarak Laravel servis konteyneri üzerinden çözümlenecektir.

## Hata Mesajlarını Özelleştirme

`messages` metodunu geçersiz kılarak form isteği tarafından kullanılan hata mesajlarını özelleştirebilirsiniz. Bu yöntem, bir dizi öznitelik / kural çifti ve bunlara karşılık gelen hata mesajlarını döndürmelidir:

```php
/**
 * Get the error messages for the defined validation rules.
 *
 * @return array<string, string>
 */
public function messages(): array
{
    return [
        'title.required' => 'A title is required',
        'body.required' => 'A message is required',
    ];
}
```

#### Doğrulama Niteliklerini Özelleştirme

Laravel'in yerleşik doğrulama kuralı hata mesajlarının çoğu bir `:attribute` yer tutucusu içerir. Eğer doğrulama mesajınızın `:attribute` yer tutucusunun özel bir attribute ismi ile değiştirilmesini istiyorsanız, `attributes` metodunu geçersiz kılarak özel isimleri belirtebilirsiniz. Bu yöntem, öznitelik / ad çiftlerinden oluşan bir dizi döndürmelidir:

```php
/**
 * Get custom attributes for validator errors.
 *
 * @return array<string, string>
 */
public function attributes(): array
{
    return [
        'email' => 'email address',
    ];
}
```

## Doğrulama İçin Girdi Hazırlama

Doğrulama kurallarınızı uygulamadan önce istekteki herhangi bir veriyi hazırlamanız veya sterilize etmeniz gerekiyorsa, `prepareForValidation` metodunu kullanabilirsiniz:

```php
use Illuminate\Support\Str;
 
/**
 * Prepare the data for validation.
 */
protected function prepareForValidation(): void
{
    $this->merge([
        'slug' => Str::slug($this->slug),
    ]);
}
```

Aynı şekilde, doğrulama tamamlandıktan sonra herhangi bir istek verisini normalleştirmeniz gerekiyorsa, `passedValidation` metodunu kullanabilirsiniz:

```php
/**
 * Handle a passed validation attempt.
 */
protected function passedValidation(): void
{
    $this->replace(['name' => 'Taylor']);
}
```

# `#` Manuel Doğrulama Oluşturma
---
#çevrilecek 

# `#` Doğrulanmış Girdilerle Çalışma
---
Gelen istek verilerini bir form isteği veya elle oluşturulmuş bir doğrulayıcı örneği kullanarak doğruladıktan sonra, gerçekten doğrulamaya tabi tutulan gelen istek verilerini almak isteyebilirsiniz. Bu birkaç şekilde gerçekleştirilebilir. İlk olarak, bir form isteği veya doğrulayıcı örneği üzerinde `validated` metodunu çağırabilirsiniz. Bu metod, doğrulanan verilerin bir dizisini döndürür:

```php
$validated = $request->validated();
 
$validated = $validator->validated();
```

Alternatif olarak, bir form isteği veya doğrulayıcı örneği üzerinde `safe` metodunu çağırabilirsiniz. Bu metod bir `Illuminate\Support\ValidatedInput` örneği döndürür. Bu nesne, doğrulanmış verilerin bir alt kümesini veya doğrulanmış veri dizisinin tamamını almak için `only`, `except` ve `all` metodlarını ortaya çıkarır:

```php
$validated = $request->safe()->only(['name', 'email']);
 
$validated = $request->safe()->except(['name', 'email']);
 
$validated = $request->safe()->all();
```

Ayrıca, `Illuminate\Support\ValidatedInput` örneği üzerinde yineleme yapılabilir ve bu örneğe bir dizi gibi erişilebilir:

```php
// Validated data may be iterated...
foreach ($request->safe() as $key => $value) {
    // ...
}
 
// Validated data may be accessed as an array...
$validated = $request->safe();
 
$email = $validated['email'];
```

Doğrulanan verilere ek alanlar eklemek isterseniz, `merge` metodunu çağırabilirsiniz:

```php
$validated = $request->safe()->merge(['name' => 'Taylor Otwell']);
```

Doğrulanan verileri bir koleksiyon örneği olarak almak isterseniz, `collect` metodunu çağırabilirsiniz:

```php
$collection = $request->safe()->collect();
```

# `#` Hata Mesajlarıyla Çalışma
---
Bir `Validator` örneğinde `errors` metodunu çağırdıktan sonra, hata mesajlarıyla çalışmak için çeşitli kullanışlı metodlara sahip bir `Illuminate\Support\MessageBag` örneği alırsınız. Tüm görünümler için otomatik olarak kullanıma sunulan `$errors` değişkeni de `MessageBag` sınıfının bir örneğidir.

### Bir Girdinin İlk Hata Mesajını Alma

Belirli bir alan için ilk hata mesajını almak için `first` metodunu kullanın:

```php
$errors = $validator->errors();
 
echo $errors->first('email');
```

### Bir Girdinin Bütün Hata Mesajlarını Alma

Belirli bir alan için tüm mesajların bir dizisini almanız gerekiyorsa `get` metodunu kullanın:

```
foreach ($errors->get('email') as $message) {
    // ...
}
```

Bir dizi form alanını doğruluyorsanız, `*` karakterini kullanarak dizi öğelerinin her biri için tüm mesajları alabilirsiniz:

```php
foreach ($errors->get('attachments.*') as $message) {
    // ...
}
```

### Bütün Alanların Hata Mesajlarını Alma

Tüm alanlar için tüm mesajların bir dizisini almak için `all` metodunu kullanın:

```php
foreach ($errors->all() as $message) {
    // ...
}
```

### Bir Alan için İletilerin Var Olup Olmadığını Belirleme

`has` metodu, belirli bir alan için herhangi bir hata mesajının mevcut olup olmadığını belirlemek için kullanılabilir:

```php
if ($errors->has('email')) {
    // ...
}
```

## Dil Dosyaları İle Hata Mesajlarını Özelleştirme

Laravel'in yerleşik doğrulama kurallarının her biri uygulamanızın `lang/en/validation.php` dosyasında bulunan bir hata mesajına sahiptir. Eğer uygulamanızın bir `lang` dizini yoksa, `lang:publish` Artisan komutunu kullanarak Laravel'e bunu oluşturmasını söyleyebilirsiniz.

`lang/en/validation.php` dosyası içinde, her doğrulama kuralı için bir çeviri girişi bulacaksınız. Uygulamanızın ihtiyaçlarına göre bu mesajları değiştirmekte veya düzenlemekte özgürsünüz.

Ek olarak, mesajlarınızı uygulamanızın diline çevirmek için bu dosyayı başka bir dil dizinine kopyalayabilirsiniz. Laravel yerelleştirmesi hakkında daha fazla bilgi edinmek için yerelleştirme belgelerinin tamamına göz atın.

### Belirli Nitelikler İçin Özel Mesajlar

Uygulamanızın doğrulama dili dosyalarında belirtilen nitelik ve kural kombinasyonları için kullanılan hata mesajlarını özelleştirebilirsiniz. Bunu yapmak için, mesaj özelleştirmelerinizi uygulamanızın `lang/xx/validation.php` dil dosyasının özel dizisine ekleyin:

```php
'custom' => [
    'email' => [
        'required' => 'We need to know your email address!',
        'max' => 'Your email address is too long!'
    ],
],
```

### Dil Dosyalarında Nitelik Belirtme

Laravel'in yerleşik hata mesajlarının çoğu, doğrulama altındaki alanın veya niteliğin adı ile değiştirilen bir `:attribute` yer tutucusu içerir. Eğer doğrulama mesajınızın `:attribute` kısmının özel bir değer ile değiştirilmesini istiyorsanız, `lang/xx/validation.php` dil dosyanızın `attributes` dizisinde özel nitelik adını belirtebilirsiniz:

```php
'attributes' => [
    'email' => 'email address',
],
```

### Dil Dosyalarında Değerlerin Belirtilmesi

Laravel'in yerleşik doğrulama kuralı hata mesajlarından bazıları, istek niteliğinin geçerli değeri ile değiştirilen bir `:value` yer tutucusu içerir. Ancak, zaman zaman doğrulama mesajınızın `:value` kısmının değerin özel bir gösterimi ile değiştirilmesine ihtiyaç duyabilirsiniz. Örneğin, `payment_type` öğesinin `cc` değerine sahip olması durumunda bir kredi kartı numarasının gerekli olduğunu belirten aşağıdaki kuralı ele alalım:

```php
Validator::make($request->all(), [
    'credit_card_number' => 'required_if:payment_type,cc'
]);
```

Bu doğrulama kuralı başarısız olursa, aşağıdaki hata mesajını üretecektir:

```plain
The credit card number field is required when payment type is cc.
```

Ödeme türü değeri olarak `cc`'yi görüntülemek yerine, `lang/xx/validation.php` dil dosyanızda bir `values` dizisi tanımlayarak daha kullanıcı dostu bir değer gösterimi belirleyebilirsiniz:

```php
'values' => [
    'payment_type' => [
        'cc' => 'credit card'
    ],
],
```

Bu değer tanımlandıktan sonra, doğrulama kuralı aşağıdaki hata mesajını üretecektir:

```plain
The credit card number field is required when payment type is credit card.
```

# `#` Mevcut Doğrulama Kuralları
---
Aşağıda mevcut tüm doğrulama kurallarının bir listesi bulunmaktadır:

| KURALLAR #1                | KURALLAR #2                   | KURALLAR #3            |
| -------------------------- | ----------------------------- | ---------------------- |
| `Accepted`                 | `Exclude If`                  | `Not Regex`            |
| `Accepted If`              | `Exclude Unless`              | `Nullable`             |
| `Active URL`               | `Exclude With`                | `Numeric`              |
| `After (Date)`             | `Exclude Without`             | `Present`              |
| `After Or Equal (Date)`    | `Exists (Database)`           | `Present If`           |
| `Alpha`                    | `Extensions`                  | `Present Unless`       |
| `Alpha Dash`               | `File`                        | `Present With`         |
| `Alpha Numeric`            | `Filled`                      | `Present With All`     |
| `Array`                    | `Greater Than`                | `Prohibited`           |
| `Ascii`                    | `Greater Than Or Equal`       | `Prohibited If`        |
| `Bail`                     | `Hex Color`                   | `Prohibited Unless`    |
| `Before (Date)`            | `Image (File)`                | `Prohibits`            |
| `Before Or Equal (Date)`   | `In`                          | `Regular Expression`   |
| `Between`                  | `In Array`                    | `Required`             |
| `Boolean`                  | `Integer`                     | `Required If`          |
| `Confirmed`                | `IP Address`                  | `Required If Accepted` |
| `Contains`                 | `JSON`                        | `Required If Declined` |
| `Current Password`         | `Less Than`                   | `Required Unless`      |
| `Date`                     | `Less Than Or Equal`          | `Required With`        |
| `Date Equals`              | `List`                        | `Required With All`    |
| `Date Format`              | `Lowercase`                   | `Required Without`     |
| `Decimal`                  | `MAC Address`                 | `Required Without All` |
| `Declined`                 | `Max`                         | `Required Array Keys`  |
| `Declined If`              | `Max Digits`                  | `Same`                 |
| `Different`                | `MIME Types`                  | `Size`                 |
| `Digits`                   | `MIME Type By File Extension` | `Sometimes`            |
| `Digits Between`           | `Min`                         | `Starts With`          |
| `Dimensions (Image Files)` | `Min Digits`                  | `String`               |
| `Distinct`                 | `Missing`                     | `Timezone`             |
| `Doesnt Start With`        | `Missing If`                  | `Unique (Database)`    |
| `Doesnt End With`          | `Missing Unless`              | `Uppercase`            |
| `Email`                    | `Missing With`                | `URL`                  |
| `Ends With`                | `Missing With All`            | `ULID`                 |
| `Enum`                     | `Multiple Of`                 | `UUID`                 |
| `Exclude`                  | `Not In`                      |                        |

# `#` Koşullu Kurallar
---
