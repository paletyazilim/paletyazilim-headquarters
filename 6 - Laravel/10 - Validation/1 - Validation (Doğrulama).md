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

Laravel'in yerleşik doğrulama kuralı hata mesajlarının çoğu bir `:attribute` placeholder'ı içerir. Eğer doğrulama mesajınızın `:attribute` placeholder'ı özel bir attribute ismi ile değiştirilmesini istiyorsanız, `attributes` metodunu geçersiz kılarak özel isimleri belirtebilirsiniz. Bu yöntem, öznitelik / ad çiftlerinden oluşan bir dizi döndürmelidir:

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
İstek üzerinde `validate` metodunu kullanmak istemiyorsanız, `Validator` facade'ini kullanarak manuel olarak bir validator örneği oluşturabilirsiniz. Facade üzerindeki `make` metodu yeni bir validator örneği oluşturur:

```php
<?php
 
namespace App\Http\Controllers;
 
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Validator;
 
class PostController extends Controller
{
    /**
     * Store a new blog post.
     */
    public function store(Request $request): RedirectResponse
    {
        $validator = Validator::make($request->all(), [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);
 
        if ($validator->fails()) {
            return redirect('post/create')
                        ->withErrors($validator)
                        ->withInput();
        }
 
        // Retrieve the validated input...
        $validated = $validator->validated();
 
        // Retrieve a portion of the validated input...
        $validated = $validator->safe()->only(['name', 'email']);
        $validated = $validator->safe()->except(['name', 'email']);
 
        // Store the blog post...
 
        return redirect('/posts');
    }
}
```

`make` metoduna aktarılan ilk bağımsız değişken, doğrulama altındaki verilerdir. İkinci bağımsız değişken, verilere uygulanması gereken doğrulama kurallarının bir dizisidir.

İstek doğrulamasının başarısız olup olmadığını belirledikten sonra, hata mesajlarını oturuma göstermek için `withErrors` metodunu kullanabilirsiniz. Bu metodu kullanırken, `$errors` değişkeni yeniden yönlendirmeden sonra otomatik olarak görünümlerinizle paylaşılacak ve bunları kullanıcıya kolayca geri görüntülemenize olanak tanıyacaktır. `withErrors` metodu bir doğrulayıcı, bir `MessageBag` veya bir PHP `array` kabul eder.

### İlk Doğrulama Hatasında Durdurma

`stopOnFirstFailure` metodu, doğrulayıcıya tek bir doğrulama hatası oluştuğunda tüm nitelikleri doğrulamayı durdurması gerektiğini bildirir:

```php
if ($validator->stopOnFirstFailure()->fails()) {
    // ...
}
```

## Otomatik Yeniden Yönlendirme

Manuel olarak bir doğrulayıcı örneği oluşturmak, ancak yine de HTTP isteğinin `validate` metodunun sunduğu otomatik yönlendirmeden yararlanmak istiyorsanız, mevcut bir doğrulayıcı örneği üzerinde `validate` metodunu çağırabilirsiniz. Doğrulama başarısız olursa, kullanıcı otomatik olarak yeniden yönlendirilir veya XHR isteği durumunda bir JSON yanıtı döndürülür:

```php
Validator::make($request->all(), [
    'title' => 'required|unique:posts|max:255',
    'body' => 'required',
])->validate();
```

Doğrulama başarısız olursa hata mesajlarını adlandırılmış bir hata torbasında saklamak için `validateWithBag` metodunu kullanabilirsiniz:

```php
Validator::make($request->all(), [
    'title' => 'required|unique:posts|max:255',
    'body' => 'required',
])->validateWithBag('post');
```

## İsimlendirilmiş Error Bags (Hata Torbaları)

Tek bir sayfada birden fazla formunuz varsa, doğrulama hatalarını içeren `MessageBag`'i adlandırarak belirli bir form için hata mesajlarını almanıza izin vermek isteyebilirsiniz. Bunu başarmak için, `withErrors` öğesine ikinci bağımsız değişken olarak bir ad iletin:

```php
return redirect('register')->withErrors($validator, 'login');
```

Daha sonra isimlendirilmiş `MessageBag` örneğine `$errors` değişkeninden erişebilirsiniz:

```php
{{ $errors->login->first('email') }}
```

## Hata Mesajlarını Özelleştirme

Gerekirse, Laravel tarafından sağlanan varsayılan hata mesajları yerine bir doğrulayıcı örneğinin kullanması gereken özel hata mesajları sağlayabilirsiniz. Özel mesajları belirtmenin birkaç yolu vardır. İlk olarak, özel mesajları `Validator::make` metoduna üçüncü argüman olarak aktarabilirsiniz:

```php
$validator = Validator::make($input, $rules, $messages = [
    'required' => 'The :attribute field is required.',
]);
```

Bu örnekte, `:attribute` placeholder'la, doğrulama altındaki alanın gerçek adıyla değiştirilecektir. Doğrulama mesajlarında başka placeholderlar da kullanabilirsiniz. Örneğin:

```php
$messages = [
    'same' => 'The :attribute and :other must match.',
    'size' => 'The :attribute must be exactly :size.',
    'between' => 'The :attribute value :input is not between :min - :max.',
    'in' => 'The :attribute must be one of the following types: :values',
];
```

### Belirli Bir Nitelik İçin Özel Mesaj Belirleme

Bazen yalnızca belirli bir nitelik için özel bir hata mesajı belirtmek isteyebilirsiniz. Bunu "nokta" notasyonu kullanarak yapabilirsiniz. Önce niteliğin adını, ardından kuralı belirtin:

```php
$messages = [
    'email.required' => 'We need to know your email address!',
];
```

### Özel Nitelik Değerlerini Belirtme

Laravel'in yerleşik hata mesajlarının çoğu, doğrulama altındaki alanın veya niteliğin adıyla değiştirilen bir `:attribute` placeholder'ı içerir. Belirli alanlar için bu yer tutucuların yerine kullanılan değerleri özelleştirmek için, `Validator::make` metoduna dördüncü argüman olarak bir dizi özel nitelik iletebilirsiniz:

```php
$validator = Validator::make($input, $rules, $messages, [
    'email' => 'email address',
]);
```

## Ek Doğrulama Gerçekleştirme

Bazen ilk doğrulama tamamlandıktan sonra ek doğrulama gerçekleştirmeniz gerekir. Bunu doğrulayıcının `after` metodunu kullanarak gerçekleştirebilirsiniz. `after` metodu, doğrulama tamamlandıktan sonra çağrılacak bir closure veya callable dizisi kabul eder. Verilen çağrılabilirler bir `Illuminate\Validation\Validator` örneği alır ve gerekirse ek hata mesajları oluşturmanıza olanak tanır:

```php
use Illuminate\Support\Facades\Validator;
 
$validator = Validator::make(/* ... */);
 
$validator->after(function ($validator) {
    if ($this->somethingElseIsInvalid()) {
        $validator->errors()->add(
            'field', 'Something is wrong with this field!'
        );
    }
});
 
if ($validator->fails()) {
    // ...
}
```

Belirtildiği gibi, `after` metodu aynı zamanda bir callable dizisini de kabul eder; bu, özellikle "doğrulama sonrası" mantığınız, `__invoke` metodu aracılığıyla bir `Illuminate\Validation\Validator` örneği alacak olan çağrılabilir sınıflarda kapsüllenmişse kullanışlıdır:

```php
use App\Validation\ValidateShippingTime;
use App\Validation\ValidateUserStatus;
 
$validator->after([
    new ValidateUserStatus,
    new ValidateShippingTime,
    function ($validator) {
        // ...
    },
]);
```

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

Laravel'in yerleşik hata mesajlarının çoğu, doğrulama altındaki alanın veya niteliğin adı ile değiştirilen bir `:attribute` placeholder'ı içerir. Eğer doğrulama mesajınızın `:attribute` kısmının özel bir değer ile değiştirilmesini istiyorsanız, `lang/xx/validation.php` dil dosyanızın `attributes` dizisinde özel nitelik adını belirtebilirsiniz:

```php
'attributes' => [
    'email' => 'email address',
],
```

### Dil Dosyalarında Değerlerin Belirtilmesi

Laravel'in yerleşik doğrulama kuralı hata mesajlarından bazıları, istek niteliğinin geçerli değeri ile değiştirilen bir `:value` placeholder'ı içerir. Ancak, zaman zaman doğrulama mesajınızın `:value` kısmının değerin özel bir gösterimi ile değiştirilmesine ihtiyaç duyabilirsiniz. Örneğin, `payment_type` öğesinin `cc` değerine sahip olması durumunda bir kredi kartı numarasının gerekli olduğunu belirten aşağıdaki kuralı ele alalım:

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
## Koşullar Sağlandığında Doğrulamayı Atlama

Bazen, başka bir alan belirli bir değere sahipse belirli bir alanı doğrulamamak isteyebilirsiniz. Bunu `exclude_if` doğrulama kuralını kullanarak gerçekleştirebilirsiniz. Bu örnekte, `has_appointment` alanı `false` değerine sahipse `appointment_date` ve `doctor_name` alanları doğrulanmayacaktır:

```php
use Illuminate\Support\Facades\Validator;
 
$validator = Validator::make($data, [
    'has_appointment' => 'required|boolean',
    'appointment_date' => 'exclude_if:has_appointment,false|required|date',
    'doctor_name' => 'exclude_if:has_appointment,false|required|string',
]);
```

Alternatif olarak, başka bir alan belirli bir değere sahip olmadıkça belirli bir alanı doğrulamamak için `exclude_unless` kuralını kullanabilirsiniz:

```php
$validator = Validator::make($data, [
    'has_appointment' => 'required|boolean',
    'appointment_date' => 'exclude_unless:has_appointment,true|required|date',
    'doctor_name' => 'exclude_unless:has_appointment,true|required|string',
]);
```

## Mevcut Olduğunda Doğrulama

Bazı durumlarda, bir alana yönelik doğrulama kontrollerini yalnızca o alan doğrulanan verilerde mevcutsa çalıştırmak isteyebilirsiniz. Bunu hızlı bir şekilde gerçekleştirmek için, kural listenize `sometimes` kuralı ekleyin:

```php
$v = Validator::make($data, [
    'email' => 'sometimes|required|email',
]);
```

Yukarıdaki örnekte, e-posta alanı yalnızca `$data` dizisinde mevcutsa doğrulanacaktır.

## Karmaşık Koşullu Doğrulama

Bazen daha karmaşık koşullu mantığa dayalı doğrulama kuralları eklemek isteyebilirsiniz. Örneğin, belirli bir alanın yalnızca başka bir alanın 100'den büyük bir değere sahip olması durumunda gerekli olmasını isteyebilirsiniz. Veya iki alanın yalnızca başka bir alan mevcut olduğunda belirli bir değere sahip olmasını isteyebilirsiniz. Bu doğrulama kurallarını eklemek zahmetli olmak zorunda değildir. İlk olarak, hiç değişmeyen statik kurallarınızla bir `Validator` örneği oluşturun:

```php
use Illuminate\Support\Facades\Validator;
 
$validator = Validator::make($request->all(), [
    'email' => 'required|email',
    'games' => 'required|numeric',
]);
```

Web uygulamamızın oyun koleksiyoncuları için olduğunu varsayalım. Bir oyun koleksiyoncusu uygulamamıza kaydolursa ve 100'den fazla oyuna sahipse, neden bu kadar çok oyuna sahip olduklarını açıklamalarını istiyoruz. Örneğin, belki bir oyun satış dükkanı işletiyorlardır ya da sadece oyun toplamaktan hoşlanıyorlardır. Bu gereksinimi koşullu olarak eklemek için `Validator` örneğinde `sometimes` metodunu kullanabiliriz.

```php
use Illuminate\Support\Fluent;
 
$validator->sometimes('reason', 'required|max:500', function (Fluent $input) {
    return $input->games >= 100;
});
```

`sometimes` metoduna aktarılan ilk bağımsız değişken, koşullu olarak doğruladığımız alanın adıdır. İkinci bağımsız değişken, eklemek istediğimiz kuralların bir listesidir. Üçüncü bağımsız değişken olarak geçirilen kapanış `true` değerini döndürürse kurallar eklenir. Bu metot, karmaşık koşullu doğrulamalar oluşturmayı kolaylaştırır. Hatta aynı anda birden fazla alan için koşullu doğrulama ekleyebilirsiniz:

```php
$validator->sometimes(['reason', 'cost'], 'required', function (Fluent $input) {
    return $input->games >= 100;
});
```

> Kapanışınıza aktarılan `$input` parametresi bir `Illuminate\Support\Fluent` örneği olacaktır ve girdinize ve doğrulama altındaki dosyalara erişmek için kullanılabilir.

### Karmaşık Koşullu Dizi Doğrulaması

Bazen bir alanı, aynı iç içe dizideki indeksini bilmediğiniz başka bir alana göre doğrulamak isteyebilirsiniz. Bu gibi durumlarda, closure'unuzun ikinci bir argüman almasına izin verebilirsiniz; bu argüman, doğrulanan dizideki geçerli tekil öğe olacaktır:

```php
$input = [
    'channels' => [
        [
            'type' => 'email',
            'address' => 'abigail@example.com',
        ],
        [
            'type' => 'url',
            'address' => 'https://example.com',
        ],
    ],
];
 
$validator->sometimes('channels.*.address', 'email', function (Fluent $input, Fluent $item) {
    return $item->type === 'email';
});
 
$validator->sometimes('channels.*.address', 'url', function (Fluent $input, Fluent $item) {
    return $item->type !== 'email';
});
```

Closure aktarılan `$input` parametresi gibi, `$item` parametresi de öznitelik verileri bir dizi olduğunda bir `Illuminate\Support\Fluent` örneğidir; aksi takdirde bir dizedir.

# `#` Dizileri Doğrulama
---
`array` doğrulama kuralı belgelerinde açıklandığı gibi, `array` kuralı izin verilen dizi anahtarlarının bir listesini kabul eder. Dizi içinde herhangi bir ek anahtar varsa, doğrulama başarısız olur:

```php
use Illuminate\Support\Facades\Validator;
 
$input = [
    'user' => [
        'name' => 'Taylor Otwell',
        'username' => 'taylorotwell',
        'admin' => true,
    ],
];
 
Validator::make($input, [
    'user' => 'array:name,username',
]);
```

Genel olarak, dizinizde bulunmasına izin verilen dizi anahtarlarını her zaman belirtmelisiniz. Aksi takdirde, validator'ın `validate` ve `validated` metodları, dizi ve tüm anahtarları dahil olmak üzere, bu anahtarlar diğer iç içe dizi doğrulama kuralları tarafından doğrulanmamış olsa bile, doğrulanan tüm verileri döndürür.

## İç İçe Dizileri Doğrulama

İç içe geçmiş dizi tabanlı form giriş alanlarını doğrulamak zahmetli olmak zorunda değildir. Bir dizi içindeki nitelikleri doğrulamak için "nokta notasyonunu" kullanabilirsiniz. Örneğin, gelen HTTP isteği bir `photos[profile]` alanı içeriyorsa, bunu şu şekilde doğrulayabilirsiniz:

```php
use Illuminate\Support\Facades\Validator;
 
$validator = Validator::make($request->all(), [
    'photos.profile' => 'required|image',
]);
```

Bir dizinin her bir öğesini de doğrulayabilirsiniz. Örneğin, belirli bir dizi giriş alanındaki her e-postanın benzersiz olduğunu doğrulamak için aşağıdakileri yapabilirsiniz:

```php
$validator = Validator::make($request->all(), [
    'person.*.email' => 'email|unique:users',
    'person.*.first_name' => 'required_with:person.*.last_name',
]);
```

Benzer şekilde, dil dosyalarınızda özel doğrulama mesajları belirlerken `*` karakterini kullanabilirsiniz, böylece dizi tabanlı alanlar için tek bir doğrulama mesajı kullanmak çok kolay hale gelir:

```php
'custom' => [
    'person.*.email' => [
        'unique' => 'Each person must have a unique email address',
    ]
],
```

### İç İçe Dizi Verilerine Erişme

Bazen, niteliğe doğrulama kuralları atarken belirli bir iç içe dizi öğesinin değerine erişmeniz gerekebilir. Bunu `Rule::forEach` metodunu kullanarak gerçekleştirebilirsiniz. `forEach` metodu, doğrulama altındaki dizi niteliğinin her yinelemesi için çağrılacak ve niteliğin değerini ve açık, tam genişletilmiş nitelik adını alacak bir closure kabul eder. Closure, dizi öğesine atanacak bir kural dizisi döndürmelidir:

```php
use App\Rules\HasPermission;
use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\Rule;
 
$validator = Validator::make($request->all(), [
    'companies.*.id' => Rule::forEach(function (string|null $value, string $attribute) {
        return [
            Rule::exists(Company::class, 'id'),
            new HasPermission('manage-company', $value),
        ];
    }),
]);
```

## Hata Mesajı Indexleri ve Konumları

Dizileri doğrularken, uygulamanız tarafından görüntülenen hata mesajında doğrulamada başarısız olan belirli bir öğenin dizinine veya konumuna başvurmak isteyebilirsiniz. Bunu gerçekleştirmek için, özel doğrulama mesajınıza `:index` (0'dan başlar) ve `:position` (1'den başlar) placeholderlarını dahil edebilirsiniz:

```php
use Illuminate\Support\Facades\Validator;
 
$input = [
    'photos' => [
        [
            'name' => 'BeachVacation.jpg',
            'description' => 'A photo of my beach vacation!',
        ],
        [
            'name' => 'GrandCanyon.jpg',
            'description' => '',
        ],
    ],
];
 
Validator::validate($input, [
    'photos.*.description' => 'required',
], [
    'photos.*.description.required' => 'Please describe photo #:position.',
]);
```

Yukarıdaki örnek göz önüne alındığında, doğrulama başarısız olacak ve kullanıcıya "Please describe photo #2" hatası sunulacaktır.

Gerekirse, `second-index`, `second-position`, `third-index`, `third-position` vb. aracılığıyla daha derin iç içe geçmiş indekslere ve konumlara başvurabilirsiniz.

# `#` Dosyaları Doğrulama
---
Laravel yüklenen dosyaları doğrulamak için kullanılabilecek `mimes`, `image`, `min` ve `max` gibi çeşitli doğrulama kuralları sağlar. Dosyaları doğrularken bu kuralları tek tek belirtmekte özgür olsanız da, Laravel ayrıca kullanışlı bulabileceğiniz akıcı bir dosya doğrulama kuralı oluşturucusu sunar:

```php
use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\Rules\File;
 
Validator::validate($input, [
    'attachment' => [
        'required',
        File::types(['mp3', 'wav'])
            ->min(1024)
            ->max(12 * 1024),
    ],
]);
```

Uygulamanız kullanıcılarınız tarafından yüklenen görüntüleri kabul ediyorsa, yüklenen dosyanın bir görüntü olması gerektiğini belirtmek için `File` kuralının `image` oluşturucu metodunu kullanabilirsiniz. Buna ek olarak, görüntünün boyutlarını sınırlamak için `dimensions` kuralı kullanılabilir:

```php
use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\Rule;
use Illuminate\Validation\Rules\File;
 
Validator::validate($input, [
    'photo' => [
        'required',
        File::image()
            ->min(1024)
            ->max(12 * 1024)
            ->dimensions(Rule::dimensions()->maxWidth(1000)->maxHeight(500)),
    ],
]);
```

## Dosya Boyutu

Kolaylık sağlamak için, minimum ve maksimum dosya boyutları, dosya boyutu birimlerini belirten bir son ek ile birlikte bir dize olarak belirtilebilir. `kb`, `mb`, `gb` ve `tb` son ekleri desteklenir:

```php
File::image()
    ->min('1kb')
    ->max('10mb')
```

## Dosya Türleri

`types` metodunu çağırırken yalnızca uzantıları belirtmeniz gerekse de, bu metot aslında dosyanın içeriğini okuyarak ve MIME türünü tahmin ederek dosyanın MIME türünü doğrular. MIME türlerinin ve bunlara karşılık gelen uzantıların tam bir listesi aşağıdaki konumda bulunabilir:

https://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types

# `#` Parolaları Doğrulama
---
Parolaların yeterli düzeyde karmaşıklığa sahip olmasını sağlamak için Laravel'in `Password` kuralı nesnesini kullanabilirsiniz:

```php
use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\Rules\Password;
 
$validator = Validator::make($request->all(), [
    'password' => ['required', 'confirmed', Password::min(8)],
]);
```

`Password` kuralı nesnesi, uygulamanız için parola karmaşıklığı gereksinimlerini kolayca özelleştirmenize olanak tanır; örneğin, parolaların en az bir harf, sayı, sembol veya karışık harfli karakterler gerektirdiğini belirtebilirsiniz:

```php
// Require at least 8 characters...
Password::min(8)
 
// Require at least one letter...
Password::min(8)->letters()
 
// Require at least one uppercase and one lowercase letter...
Password::min(8)->mixedCase()
 
// Require at least one number...
Password::min(8)->numbers()
 
// Require at least one symbol...
Password::min(8)->symbols()
```

Ayrıca, herkese açık bir parola veri ihlali sızıntısında bir parolanın ele geçirilmediğinden, `uncompromised` metodu kullanarak emin olabilirsiniz:

```php
Password::min(8)->uncompromised()
```

Dahili olarak, `Password` kuralı nesnesi, kullanıcının gizliliğinden veya güvenliğinden ödün vermeden haveibeenpwned.com hizmeti aracılığıyla bir parolanın sızdırılıp sızdırılmadığını belirlemek için k-Anonymity modelini kullanır.

Varsayılan olarak, bir parola bir veri sızıntısında en az bir kez görünürse, tehlikeye atılmış olarak kabul edilir. `unompromised` metodunun ilk bağımsız değişkenini kullanarak bu eşiği özelleştirebilirsiniz:

```php
Password::min(8)->uncompromised(3);
```

Elbette, yukarıdaki örneklerdeki tüm metotları zincirleyebilirsiniz:

```php
Password::min(8)
    ->letters()
    ->mixedCase()
    ->numbers()
    ->symbols()
    ->uncompromised()
```






