# Sürümler
---
Bu proje anlamsal sürümlendirme (semantic versioning) kullanmaktadır. (bkz: https://semver.org/)
## v0.1.0
---
Projeye ilk adım atıldı ve `x-data-list.table` blade bileşenine geçirilen veriler bir Livewire bileşenine aktarılıp render edilmekte.

- `headers` niteliği eklendi.
  - `model` niteliği eklendi.
- `zebra` niteliği eklendi.
- `prefix-style` niteliği eklendi.
- `prefix-size` niteliği eklendi.
- `trailing-style` niteliği eklendi.
- `trailing-size` niteliği eklendi.
- `sort-by` niteliği eklendi.

## v0.1.1
---
Proje tekrardan yazılmaya başlandı ve livewire'nin kapasitesi doğrultusunda tekrardan inşaa edilecek. 

Yeni güncelleme ile artık statik bir blade bileşeni tarafından sarmalamaya ihtiyaç duymayacak doğrudan bir livewire bileşeni olarak çağrılıp gerekli nitelikler ve veriler livewire bileşenine aktarılacak.

🐞 **BUG**: Livewire maalesef doğrudan `mount` metodu içinde `Illuminate\Pagination\LengthAwarePaginator` nesnelerini desteklememekte bu yüzden modeller doğrudan `paginate()` metoduyla çağrılamaz. Bu yüzden sayfalama eklense dahi bu bilginin bilincinde olunmasında fayda var, yapılan her işlemde component render edildiğinde veritabanından tekrardan sorgu çalıştırılır.

🐞 **BUG:** Livewire v3 için geliştirilen Sortable veya SortableJS pluginleri `lazy` niteliği taşıyamaz. İki pakette doğrudan `lazy` yapıldığında ve öğeler taşındığında component içindeki taşınabilir elementler silinmekte.

🚫 **LİMİTASYON:** Livewire bileşenleri tam anlamıyla normal bir Blade bileşeni gibi davranamaz yani bu şu demek: Blade direktifleri (layout), nitelik çantası vb. gibi bazı özellikler çalışmamakta ancak ilerleyen zamanlarda tekrardan bir Blade bileşeni ile livewire componenti sarmalanabilir ve çok daha fazla özelleştirme seçeneği sunabilir ancak şu an ki süreçte mümkün gözükmüyor. 

❓ **SONRAKİ ADIM NE**

Proje tekrardan yazılacak ve muhtemelen ilk etapta oldukça fazla bug'a sahip olacak öncelikli amacımız en azından en hızlı ve en verimli şekilde (pagination dışında) client-side çalışabilen, özelleştirilebilen bir tablo paketi yazmak.