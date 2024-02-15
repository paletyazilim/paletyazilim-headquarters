## Dosyaları Yönetme
---
PHP dosya oluşturmak, okumak, yüklemek ve düzenlemek için çeşitli işlevlere sahiptir.

Dosyaları değiştirirken dikkatli olun!

Dosyaları değiştirirken çok dikkatli olmalısınız.
Yanlış bir şey yaparsanız çok fazla zarar verebilirsiniz. Yaygın hatalar şunlardır: yanlış dosyayı düzenlemek, bir sabit sürücüyü çöp verilerle doldurmak ve bir dosyanın içeriğini yanlışlıkla silmek.

### `readfile()`
---
`readfile()` fonksiyonu bir dosyayı okur ve çıktı arabelleğine yazar.

Sunucuda depolanan "acilimlar.txt" adlı bir metin dosyamız olduğunu ve bu dosyanın aşağıdaki gibi göründüğünü varsayalım:

```TXT title:'acilimlar.txt'
AJAX = Asynchronous JavaScript and XML
CSS = Cascading Style Sheets
HTML = Hyper Text Markup Language
PHP = PHP Hypertext Preprocessor
SQL = Structured Query Language
SVG = Scalable Vector Graphics
XML = EXtensible Markup Language
```

Dosyayı okumak ve çıktı arabelleğine yazmak için PHP kodu aşağıdaki gibidir (`readfile()` fonksiyonu başarı durumunda okunan bayt sayısını döndürür):

```PHP title:'Dosyayı okumak'
<?php
echo readfile("acilimlar.txt");
?>
```

Tek yapmak istediğiniz bir dosyayı açmak ve içeriğini okumaksa `readfile()` fonksiyonu kullanışlıdır.

## Dosyayı Açmak/Okumak/Kapatmak
---
### Dosyayı Açmak - `fopen()`
---
Dosyaları açmak için daha iyi bir yöntem `fopen()` fonksiyonudur. Bu fonksiyon size `readfile()` fonksiyonundan daha fazla seçenek sunar.

```TXT title:'acilimlar.txt'
AJAX = Asynchronous JavaScript and XML
CSS = Cascading Style Sheets
HTML = Hyper Text Markup Language
PHP = PHP Hypertext Preprocessor
SQL = Structured Query Language
SVG = Scalable Vector Graphics
XML = EXtensible Markup Language
```

`fopen()` fonksiyonunun ilk parametresi açılacak dosyanın adını içerir ve ikinci parametre dosyanın hangi modda açılması gerektiğini belirtir. Aşağıdaki örnek, `fopen()` fonksiyonu belirtilen dosyayı açamazsa bir mesaj da üretir:

```PHP title:'fopen() ile Dosya Açma'
<?php
$dosya = fopen("acilimlar.txt", "r") or die("Dosya bulunamadı yada bozuk!");
echo fread($dosya,filesize("acilimlar.txt"));
fclose($dosya);
?>
```

Dosya aşağıdaki modlardan birinde açılabilir:

| Mod | Açıklama |
| ---- | ---- |
| r | Bir dosyayı yalnızca okunmak üzere açar. Dosya işaretçisi dosyanın başından başlar |
| w | Bir dosyayı yalnızca yazmak için açar. Dosyanın içeriğini siler veya mevcut değilse yeni bir dosya oluşturur. Dosya işaretçisi dosyanın başından başlar |
| a | Bir dosyayı yalnızca yazmak için açın. Dosyadaki mevcut veriler korunur. Dosya işaretçisi dosyanın sonunda başlar. Dosya mevcut değilse yeni bir dosya oluşturur |
| x | Yalnızca yazmak için yeni bir dosya oluşturur. Dosya zaten mevcutsa FALSE döndürür ve hata verir |
| r+ | Bir dosyayı okuma/yazma için açar. Dosya işaretçisi dosyanın başından başlar |
| w+ | Bir dosyayı okuma/yazma için açar. Dosyanın içeriğini siler veya mevcut değilse yeni bir dosya oluşturur. Dosya işaretçisi dosyanın başından başlar |
| a+ | Okuma/yazma için bir dosya açın. Dosyadaki mevcut veriler korunur. Dosya işaretçisi dosyanın sonundan başlar. Dosya mevcut değilse yeni bir dosya oluşturur |
| x+ | Okuma/yazma için yeni bir dosya oluşturur. Dosya zaten mevcutsa FALSE döndürür ve hata verir |

### Dosyayı Okumak - `fread()`
---
`fread()` fonksiyonu açık bir dosyadan okuma yapar.

`fread()` fonksiyonunun ilk parametresi okunacak dosyanın adını içerir ve ikinci parametre okunacak maksimum bayt sayısını belirtir.

Aşağıdaki PHP kodu "acilimlar.txt" dosyasını sonuna kadar okur:

```PHP title:'Bütün dosyayı okumak'
fread($dosya,filesize("acilimlar.txt"));
```

### Dosyayı Kapatmak - `fclose()`
---
`fclose()` fonksiyonu açık bir dosyayı kapatmak için kullanılır.

İşiniz bittikten sonra tüm dosyaları kapatmak iyi bir programlama uygulamasıdır. Açık bir dosyanın sunucunuzda dolaşıp kaynakları tüketmesini istemezsiniz!

`fclose()` fonksiyonu, kapatmak istediğimiz dosyanın adını (veya dosya adını tutan bir değişkeni) gerektirir:

```PHP title:'Dosyayı kapatmak'
<?php
$dosya = fopen("acilimlar.txt", "r");
// Yapılacak işlemlerin kod blokları....
fclose($dosya);
?>
```

### Tek Bir Satırı Okumak - `fgets()`
---
`fgets()` fonksiyonu bir dosyadan tek bir satır okumak için kullanılır.

Aşağıdaki örnek "acilimlar.txt" dosyasının ilk satırının çıktısını verir:

```PHP title:'Bir dosyadan sadece tek bir satırı okumak'
<?php
$dosya = fopen("acilimlar.txt", "r") or die("Dosya bulunamadı yada bozuk!");
echo fgets($dosya);
fclose($dosya);
?>
```

**Not:** `fgets()` fonksiyonuna yapılan bir çağrıdan sonra, dosya işaretçisi bir sonraki satıra taşınır.

### Dosya Sonu Denetimi - `feof()`
---
`feof()` fonksiyonnu "dosya sonuna" (EOF) ulaşılıp ulaşılmadığını kontrol eder.

`feof()` fonksiyonu, uzunluğu bilinmeyen veriler arasında döngü oluşturmak için kullanışlıdır.

Aşağıdaki örnek "acilimlar.txt" dosyasını dosya sonuna ulaşılana kadar satır satır okur:

```PHP title:'Satır sonuna ulaşma'
<?php
$dosya = fopen("acilimlar.txt", "r") or die("Dosya bulunamadı yada bozuk!");
// Satır sonuna boşluklar ekleme
while(!feof($dosya)) {
  echo fgets($dosya) . "<br>";
}
fclose($dosya);
?>
```

### Her Karakteri Okuma - `fgetc()`
---
`fgetc()` fonksiyonu, bir dosyadan her bir karakteri okumak için kullanılır.

Aşağıdaki örnek "acilimlar.txt" dosyasını dosya sonuna ulaşılana kadar karakter karakter okur:

```PHP title:'
<?php
$dosya = fopen("acilimlar.txt", "r") or die("Dosya bulunamadı yada bozuk!");
// Dosya sonuna kadar bir karakter çıktı
while(!feof($dosya)) {
  echo fgetc($dosya);
}
fclose($dosya);
?>
```

**Not:** `fgetc()` fonksiyonuna yapılan bir çağrıdan sonra, dosya işaretçisi bir sonraki karaktere geçer.

## Dosya Oluşturma/Yazma
---
`fopen()` fonksiyonu aynı zamanda bir dosya oluşturmak için de kullanılır. Belki biraz kafa karıştırıcı olabilir, ancak PHP'de bir dosya, dosyaları açmak için kullanılan aynı işlev kullanılarak oluşturulur.

Mevcut olmayan bir dosya üzerinde `fopen()` fonksiyonunu kullanırsanız, dosyanın yazma (w) veya ekleme (a) için açılmış olması koşuluyla dosya oluşturulur.

Aşağıdaki örnek "ornek_dosya.txt" adında yeni bir dosya oluşturur. Dosya, PHP kodunun bulunduğu aynı dizinde oluşturulacaktır:

```PHP title:'Dosyayı kod aracılığıyla oluşturma'
$dosya = fopen("ornek_dosya.txt", "w")
```

### Dosya İzinleri
---
Bu kodu çalıştırmaya çalışırken hata alıyorsanız, PHP dosyanıza sabit sürücüye bilgi yazmak için erişim izni verip vermediğinizi kontrol edin.

### Dosyaya Yazma - `fwrite()`
---
`fwrite()` fonksiyonu bir dosyaya yazmak için kullanılır.

`fwrite()` fonksiyonun ilk parametresi yazılacak dosyanın adını, ikinci parametresi ise yazılacak dizeyi içerir.

Aşağıdaki örnek, "isimler.txt" adlı yeni bir dosyaya birkaç isim yazmaktadır:

```PHP title:'Dosyaya isim yazma'
<?php
$dosya = fopen("isimler.txt", "w") or die("Dosya bulunamadı veya bozuk!");
$isim1 = "Berat Masat\n";
fwrite($doysa, $isim1);
$isim2 = "Furkan Masat\n";
fwrite($dosya, $isim2);
fclose($dosya);
?>
```

"isimler.txt" dosyasına iki kez yazdığımıza dikkat edin. Dosyaya her yazdığımızda, ilkinde "Berat Masat" ve ikincisinde "Furkan Masat" içeren `$isim1` - `$isim2` dizesini gönderdik. Yazma işlemini bitirdikten sonra, `fclose()` fonksiyonunu kullanarak dosyayı kapattık.

Eğer "isimler.txt" dosyasını açarsak şöyle görünecektir:

```TXT title:'isimler.txt'
Berat Masat
Furkan Masat
```

### Dosya Üzerine Yazma
---
Şimdi "isimler.txt" bazı veriler içerdiğine göre, mevcut bir dosyayı yazmak için açtığımızda ne olacağını gösterebiliriz. Mevcut tüm veriler SİLİNECEK ve boş bir dosya ile başlayacağız.

Aşağıdaki örnekte mevcut "newfile.txt" dosyamızı açıyoruz ve içine bazı yeni veriler yazıyoruz:

```PHP title:'Mevcut verilerin üzerine yazma'
$dosya = fopen("isimler.txt", "w") or die("Dosya bulunamadı veyaz bozuk!");
$isim1 = "Mickey Mouse\n";
fwrite($dosya, $isim1);
$isim2 = "Minnie Mouse\n";
fwrite($dosya, $isim2);
fclose($dosya);
?>
```

Şimdi "newfile.txt" dosyasını açarsak, hem Berat hem de Furkan ortadan kaybolmuştur ve sadece az önce yazdığımız veriler mevcuttur:

```TXT title:'isimler.txt'
Mickey Mouse
Minnie Mouse
```

### Dosya Üzerine Ekleme (Düzenleme)
---
"a" modunu kullanarak bir dosyaya veri ekleyebilirsiniz. "a" modu dosyanın sonuna metin eklerken, "w" modu dosyanın eski içeriğini geçersiz kılar (ve siler).

Aşağıdaki örnekte mevcut "isimler.txt" dosyamızı açıyoruz ve ona bazı metinler ekliyoruz:

```PHP title:'Mevcut verilerin üzerine ekleme'
<?php
$dosya = fopen("isimler.txt", "a") or die("Dosya bulunamadı veya bozuk!");
$isim1 = "Berat Masat\n";
fwrite($dosya, $isim1);
$isim2 = "Furkan Masat\n";
fwrite($dosya, $isim2);
fclose($dosya);
?>
```

Şimdi "isimler.txt" dosyasını açarsak, Berat Masat ve Furkan Masat'ın dosyanın sonuna eklendiğini göreceğiz:

```TXT title:'isimler.txt'
Mickey Mouse
Minnie Mouse
Berat Masat
Furkan Masat
```

