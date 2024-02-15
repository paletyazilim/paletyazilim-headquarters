## Dosya Yükleme
---
### "php.ini" Dosyasını Yapılandırma
---
İlk olarak, PHP'nin dosya yüklemelerine izin verecek şekilde yapılandırıldığından emin olun.

"php.ini" dosyanızda `file_uploads` yönergesini bulun ve `On` olarak ayarlayın:

```TXT title:'php.ini'
file_uploads = On
```

### HTML Formunu Oluşturma
---
Ardından, kullanıcıların yüklemek istedikleri resim dosyasını seçmelerine olanak tanıyan bir HTML formu oluşturun:

```HTML title:'Dosya yükleme formu'
<!DOCTYPE html>
<html>
<body>

<form action="yukleme.php" method="POST" enctype="multipart/form-data">
  Yüklemek için seçtiğiniz görsel:
  <input type="file" name="yuklenecekDosya" id="yuklenecekDosya">
  <input type="submit" value="Dosya Yükle" name="submit">
</form>

</body>
</html>
```

Yukarıdaki HTML formu için uyulması gereken bazı kurallar:

- Formun `method="post"` kullandığından emin olun
- Formun ayrıca şu niteliğe de ihtiyacı vardır: `enctype="multipart/form-data"`. Formu gönderirken hangi içerik türünün kullanılacağını belirtir
- Yukarıdaki gereklilikler olmadan dosya yükleme işlemi çalışmayacaktır.

Dikkat edilmesi gereken diğer şeyler:

- `<input>` etiketinin `type="file"` niteliği, giriş alanını bir dosya seçme kontrolü olarak gösterir ve giriş kontrolünün yanında bir "Gözat" düğmesi bulunur

Yukarıdaki form, verileri daha sonra oluşturacağımız "yukleme.php" adlı bir dosyaya gönderir.

### Dosya Yükleme PHP Kodları
---
"yukleme.php" dosyası bir dosyanın yüklenmesine ilişkin kodu içerir:

```PHP title:'yukleme.php'
<?php
$hedef_konum = "uploads/";
$hedef_dosya = $hedef_konum . basename($_FILES["yuklenecekDosya"]["name"]);
$yuklemeOnaylandi = 1;
$dosyaTuru = strtolower(pathinfo($hedef_dosya,PATHINFO_EXTENSION));
// Görüntü dosyasının gerçek bir görsel mi yoksa sahte bir görsel mi olduğunu kontrol edin
if(isset($_POST["submit"])) {
  $kontrol = getimagesize($_FILES["fileToUpload"]["tmp_name"]);
  if($kontrol !== false) {
    echo "Dosya bir görsel - " . $kontrol["mime"] . ".";
    $yuklemeOnaylandi = 1;
  } else {
    echo "Yüklenen dosya bir görsel değil.";
    $yuklemeOnaylandi = 0;
  }
}
?>
```

Kodun Açıklaması:

- `$hedef_konum = "uploads/"`: Dosyanın yükleneceği klasörü belirtir
- `$hedef_dosya`: Yüklenecek dosyanın yolunu belirtir
- `$yuklemeOnaylandi = 1`: Henüz kullanılmıyor (daha sonra kullanılacak)
- `$dosyaTuru`: Dosyanın dosya uzantısını tutar (küçük harflerle)
- Ardından, görüntü dosyasının gerçek bir görüntü mü yoksa sahte bir görüntü mü olduğunu kontrol edin

**Not:** "yukleme.php" dosyasının bulunduğu dizinde "uploads" adında yeni bir klasör oluşturmanız gerekecektir. Yüklenen dosyalar oraya kaydedilecektir.

### Dosya Varlığını Kontrol Etme
---
Şimdi bazı kısıtlamalar ekleyebiliriz.

İlk olarak, dosyanın "uploads" klasöründe zaten var olup olmadığını kontrol edeceğiz. Eğer varsa, bir hata mesajı görüntülenir ve `$yuklemeOnaylandi` değeri 0 olarak ayarlanır:

```PHP title:'Dosya Varlığını Kontrol Et'
if (file_exists($hedef_dosya)) {
  echo "Üzgünüz, dosya zaten mevcut.";
  $yuklemeOnaylandi = 0;
}
```

### Dosya Limiti Sınırlama
---
Yukarıdaki HTML formumuzdaki dosya giriş alanı "yuklenecekDosya" olarak adlandırılmıştır.

Şimdi, dosyanın boyutunu kontrol etmek istiyoruz. Dosya 500KB'den büyükse, bir hata mesajı görüntülenir ve `$yuklemeOnaylandi` değeri 0 olarak ayarlanır:

```PHP title:'Dosya Boyutunu Kontrol Et'
if ($_FILES["yuklenecekDosya"]["size"] > 500000) {
  echo "Üzgünüz, dosyanız çok büyük.";
  $yuklemeOnaylandi = 0;
}
```

### Dosya Türü Sınırlama
---
Aşağıdaki kod, kullanıcıların yalnızca JPG, JPEG, PNG ve GIF dosyalarını yüklemesine izin verir. Diğer tüm dosya türleri `$yuklemeOnaylandi` değerini 0 olarak ayarlamadan önce bir hata mesajı verir:

```PHP title:'Dosya Türünü Kontrol Et'
if($dosyaTuru != "jpg" && $dosyaTuru != "png" && $dosyaTuru != "jpeg"
&& $dosyaTuru != "gif" ) {
  echo "Üzgünüz, yalnızca JPG, JPEG, PNG ve GIF dosyalarına izin verilmektedir.";
  $yuklemeOnaylandi = 0;
}
```

### Kodun Tamamlanmış Hali
---
"yukleme.php" dosyasının tamamı artık şu şekilde görünüyor:

```PHP title:'yukleme.php'
<?php
$hedef_konum = "uploads/";
$hedef_dosya = $hedef_konum . basename($_FILES["yuklenecekDosya"]["name"]);
$yuklemeOnaylandi = 1;
$dosyaTuru = strtolower(pathinfo($hedef_dosya,PATHINFO_EXTENSION));
// Görüntü dosyasının gerçek bir görsel mi yoksa sahte bir görsel mi olduğunu kontrol edin
if(isset($_POST["submit"])) {
  $kontrol = getimagesize($_FILES["fileToUpload"]["tmp_name"]);
  if($kontrol !== false) {
    echo "Dosya bir görsel - " . $kontrol["mime"] . ".";
    $yuklemeOnaylandi = 1;
  } else {
    echo "Yüklenen dosya bir görsel değil.";
    $yuklemeOnaylandi = 0;
  }
}

if (file_exists($hedef_dosya)) {
  echo "Üzgünüz, dosya zaten mevcut.";
  $yuklemeOnaylandi = 0;
}

if ($_FILES["yuklenecekDosya"]["size"] > 500000) {
  echo "Üzgünüz, dosyanız çok büyük.";
  $yuklemeOnaylandi = 0;
}

if($dosyaTuru != "jpg" && $dosyaTuru != "png" && $dosyaTuru != "jpeg"
&& $dosyaTuru != "gif" ) {
  echo "Üzgünüz, yalnızca JPG, JPEG, PNG ve GIF dosyalarına izin verilmektedir.";
  $yuklemeOnaylandi = 0;
}

if ($yuklemeOnaylandi == 0) {
  echo "Üzgünüz, dosyanız yüklenmedi.";
} else {
  if (move_uploaded_file($_FILES["yuklenecekDosya"]["tmp_name"], $hedef_dosya)) {
    echo htmlspecialchars( basename( $_FILES["yuklenecekDosya"]["name"])). " yüklenmiştir.";
  } else {
    echo "Üzgünüz, dosyanız yüklenirken bir hata meydana geldi.";
  }
}
?>
```