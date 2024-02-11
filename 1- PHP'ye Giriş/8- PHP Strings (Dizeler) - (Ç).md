## Dizelerin Temel Özellikleri
---
Dizi, harflerin sıralanmış halidir, tıpkı "Merhaba dünya!" gibi.

```PHP
echo "Merhaba";
echo 'Merhaba';
```

**Not:** PHP'de çift tırnak ile tek tırnak arasında büyük bir fark vardır. Çift tırnak özel karakterleri işler, tek tırnak işlemez.

### Çift Tırnak ile Tek Tırnak Farkı
---
Çift veya tek tırnak kullanabilirsiniz, ancak ikisi arasındaki farkları bilmelisiniz. 

Çift tırnaklı dizeler özel karakterler üzerinde işlem yapabilir. Örneğin, dizenizin içinde bir değişken olduğunda, değişkenin değerini döndürür:

```PHP
$x = "Berat";
echo "Merhaba $x"; // Çıktı: Merhaba Berat
```

Ancak tek tırnaklı dizeler bu tür eylemleri gerçekleştirmez, dizeyi değişken adıyla birlikte yazıldığı gibi döndürür:

```PHP
$x = "Berat";
echo 'Merhaba $x';
```

Tek tırnak ve çift tırnak kullanımlarındaki bu fark, PHP programlama dilinde metin ve değişkenleri birbirinden ayırmak için kullanılır. Bu sayede, programcı, metni **olduğu** gibi göstermek istediğinde veya değişkenin değerini göstermek istediğinde, bunu kolaylıkla yapabilir.

### Dize Uzunluğu
---
PHP'de `strlen()` fonksiyonu bir dizenin karakter uzunluğunu döndürür.

```PHP
echo strlen("Merhaba Dünya!"); // Çıktı: 14
```

### Kelime Miktarı
---
PHP'de `str_word_count()` fonksiyonu bir dizedeki kelime miktarını sayar.

```PHP
echo str_word_count("Merhaba Dünya!"); // Çıktı: 2
```

### Dizelerde Arama
---
PHP `strpos()` fonksiyonu bir dize içinde belirli bir metni arar.

Bir eşleşme bulunursa, fonksiyon ilk eşleşmenin karakter konumunu döndürür. Eşleşme bulunamazsa, `false` değerini döndürür. İlk karakteri **HER ZAMAN** 0 olarak ele alıp aramaya başlar.

```PHP
echo strpos("Merhaba Dünya!", "Dünya"); // Çıktı: 8 
```

## Dizeleri Değiştirme
---
Çevrilecek

## Kaçış Karakterleri
---
Bir dizeye eklemek istediğiniz özel karakterleri eklemek için bir kaçış (yok sayma) karakteridir.

Kaçış karakteri, ters eğik çizgi \ ve ardından eklemek istediğiniz karakterdir.

| Kaçış Karakteri | Açıklama | Örnek |
| ---- | ---- | ---- |
| `\'` | Tek tırnak karakterinden kaçmak için kullanılır. | `'Bu bir \'alıntı''` |
| `\"` | Çift tırnak karakterinden kaçmak için kullanılır. | `"Bu bir \"alıntı""` |
| `\$` | PHP değişkenlerinden kaçmak için kullanılır. | `\$isim` |
| `\n` | Yeni satır eklemek için kullanılır. | `Bu birinci satır.\nBu ikinci satır.` |
| `\r` | Satır başı eklemek için kullanılır. | `\rBir paragrafa satır başı uygulandı.` |
| `\t` | Tab boşluğu eklemek için kullanılır. | `Bir \tTab boşluğu kadar.` |

```PHP error:1
$x = "Çift tırnak kullanımı olan bir dizede "alıntı" yapmak için kullandığınız başka bir çift tırnak hataya sebep olur.";
```

Bu yüzden kaçış karakterini bu metin içerisinde ekstra uğraşa girmeden kullanabiliriz:

```PHP
$x = "Çift tırnak kullanımı olan bir dizede \"alıntı\" yapmak için kullandığınız başka bir çift tırnak hataya sebep olur.";
```
