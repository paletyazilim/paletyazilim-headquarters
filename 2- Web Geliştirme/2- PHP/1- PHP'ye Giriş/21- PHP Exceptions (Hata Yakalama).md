Exception (İstisna), bir PHP betiğinin hatasını veya beklenmedik davranışını tanımlayan bir nesnedir.

Exception (İstisna) birçok PHP işlevi ve sınıfı tarafından fırlatılır.

Kullanıcı tanımlı fonksiyonlar ve sınıflar da istisnaları fırlatabilir.

Exception (İstisna), kullanamayacağı verilerle karşılaştığında bir fonksiyonu durdurmak için iyi bir yoldur.

### Hata Fırlatma
---
`throw` anahtar sözcüğü, kullanıcı tanımlı bir fonksiyon veya metodun bir hata fırlatmasını sağlar. Bir hata fırlatıldığında, onu izleyen kod yürütülmez.

Bir hata yakalanmazsa, "Yakalanmamış Hata" mesajıyla birlikte ölümcül bir hata meydana gelecektir.

Bir hatayı yakalamadan fırlatmayı deneyelim:

```PHP title:'Yakalamadan fırlatılan hatalar'
<?php
function bolme($bolunen, $bolen) {
  if($bolen == 0) {
    throw new Exception("Sıfır değeri girilmiş");
  }
  return $bolunen / $bolen;
}

echo bolme(5, 0);
?>
```

Sonuç şuna benzer bir şey olacaktır:

```TXT title:'Hata Mesajı'
Fatal error: Uncaught Exception: Sıfır değeri girilmiş in C:\webfolder\test.php:4
Stack trace: #0 C:\webfolder\test.php(9):
divide(5, 0) #1 {main} thrown in C:\webfolder\test.php on line 4
```

### `try`-`catch` İfadeleri
---
Yukarıdaki örnekteki hatadan kaçınmak için, hataları yakalamak ve işleme devam etmek için `try`-`catch` ifadelerini kullanabiliriz.

### Syntax
---

```PHP title:'try-catch Syntax'
try {
  //Hata verebilecek kod/kodlar.
} catch(Exception $e) {
  //Bir hata yakalandığında yürütülecek kodlar.
}
```

```PHP title:'try-catch ile Hata Yakalama'
<?php
function bolme($bolunen, $bolen) {
  if($bolen == 0) {
    throw new Exception("Sıfıra bölme!");
  }
  return $bolunen / $bolen;
}

try {
  echo bolme(5, 0);
} catch(Exception $e) {
  echo "Bölünemez!";
}
?>
```

`catch` bloğu, ne tür bir hatanın yakalanması gerektiğini ve hataya erişmek için kullanılabilecek değişkenin adını belirtir. Yukarıdaki örnekte, hata türü Exception ve değişken adı `$e`'dir.

### `try`-`catch`-`finally` İfadeleri
---
Hataları yakalamak için `try`-`catch`-`finally` ifadeleri kullanılabilir. `finally` bloğundaki kod, bir hatanın yakalanıp yakalanmadığına bakılmaksızın her zaman çalışır. Eğer `finally` mevcutsa, `catch` bloğu isteğe bağlıdır.

### Syntax
---

```PHP title:'try-catch-finally Syntax'
try {
  //Hata verebilecek kod/kodlar.
} catch(Exception $e) {
  //Bir hata yakalandığında yürütülecek kodlar.
} finally {
  //Bir istisnanın yakalanıp yakalanmadığına bakılmaksızın her zaman çalışan kod
}
```

```PHP title:'try-catch-finally ile Hata Yakalama'
<?php
function bolme($bolunen, $bolen) {
  if($bolen == 0) {
    throw new Exception("Sıfıra bölme!");
  }
  return $bolunen / $bolen;
}

try {
  echo bolme(5, 0);
} catch(Exception $e) {
  echo "Bölünemez!";
} finally {
  echo "İşlem tamamlandı.";
}
?>
```

```PHP title:'try-catch-finally ile Hata Yakalama'
<?php
function bolme($bolunen, $bolen) {
  if($bolen == 0) {
    throw new Exception("Sıfıra bölme!");
  }
  return $bolunen / $bolen;
}

try {
  echo bolme(5, 0);
} finally {
  echo "İşlem tamamlandı.";
}
?>
```

### Exception Nesnesi
---
Exception Nesnesi, fonksiyonun karşılaştığı hata veya beklenmedik davranış hakkında bilgi içerir.

### Syntax
---

```PHP title:'Exception Syntax'
new Exception(message, code, previous)
```

#### Parametre Değerleri
---

| Parametre | Açıklama |
| ---- | ---- |
| message | İsteğe bağlı. İstisnanın neden atıldığını açıklayan bir mesaj |
| code | İsteğe bağlı. Bu hatayı aynı türdeki diğer hatalardan kolayca ayırt etmek için kullanılabilecek bir hata kodu |
| previous | İsteğe bağlı. Bu hata başka bir hatanın yakalama bloğunda atılmışsa, o hatanın bu parametreye aktarılmasını sağlar |

### Metotlar
---
Bir hata yakalandığında, aşağıdaki tabloda hata hakkında bilgi almak için kullanılabilecek bazı yöntemler gösterilmektedir:

| Metot | Açıklama |
| ---- | ---- |
| getMessage() | Hatanın neden atıldığını açıklayan mesajı döndürür |
| getPrevious()	 | Bu hata başka bir hata tarafından tetiklenmişse, bu yöntem önceki hatayı döndürür. Değilse, null döndürür |
| getCode()	 | Hata kodunu döndürür |
| getFile()	 | Hatanın atıldığı dosyanın tam yolunu döndürür |
| getLine() | Hatayı atan kod satırının satır numarasını döndürür |

```PHP title:'Hatayı metotlar ile gösterme'
<?php
function bolme($bolunen, $bolen) {
  if($bolen == 0) {
    throw new Exception("Sıfıra bölme!",1);
  }
  return $bolunen / $bolen;
}

try {
  echo bolme(5, 0);
} catch(Exception $e) {
  $kod = $e->getCode();
  $mesaj = $e->getMessage();
  $dosya = $e->getFile();
  $satir = $e->getLine();
  echo "Hatanız $dosya isimli dosyadan $satir. satırdan: $mesaj [$kod]";
}
?>
```