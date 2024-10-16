Geri çağırma fonksiyonları (genellikle sadece "geri çağırma" olarak adlandırılır), başka bir fonksiyona argüman olarak aktarılan bir fonksiyondur.

Mevcut herhangi bir fonksiyon geri çağırma fonksiyonu olarak kullanılabilir. Bir fonksiyonu geri çağırma fonksiyonu olarak kullanmak için, fonksiyonun adını içeren bir dizeyi başka bir fonksiyonun argümanı olarak geçirin.

Bir dizideki her dizenin uzunluğunu hesaplamak için PHP'nin `array_map()` fonksiyonuna bir geriçağırım iletin:

```PHP title:'Geri Çağırma Fonksiyonları'
<?php
function geri_cagirma($oge) {
  return strlen($oge);
}

$dizeler = ["elma", "portakal", "muz", "hindistan cevizi"];
$uzunluk = array_map("geri_cagirma", $dizeler);
print_r($uzunluk);
?>
```

PHP, 7. sürümden itibaren anonim fonksiyonlar geriçağırım olarak aktarabilir:

```PHP title:'Anonim fonksiyonla geriçağırım'
<?php
$dizeler = ["elma", "portakal", "muz", "hindistan cevizi"];
$uzunluk = array_map( function($oge) { return strlen($oge); } , $dizeler);
print_r($uzunluk);
?>
```

### Kullanıcı Tanımlı Fonksiyonlarda Geri Çağırım
---
Kullanıcı tanımlı fonksiyonlar ve metotlar argüman olarak geri çağırma fonksiyonlarını da alabilir. Geri çağırma fonksiyonlarını kullanıcı tanımlı bir fonksiyon veya metot içinde kullanmak için, değişkene parantez ekleyerek çağırın ve normal fonksiyonlarda olduğu gibi argümanları iletin:

```PHP title:'Geri Çağırma Fonksiyonları'
<?php
function unlem($gelenMetin) {
  return $gelenMetin . "!";
}

function soruIsareti($gelenMetin) {
  return $gelenMetin . "?";
}

function anaFonksiyon($metin, $cagirilanFonksiyon) {
  // $cagirilanFonksiyon değişkeni ile bir fonksiyon çağırılır.
  echo $cagirilanFonksiyon($metin);
}

// Pass "exclaim" and "ask" as callback functions to printFormatted()
anaFonksiyon("Merhaba Dünya", "unlem");
anaFonksiyon("Merhaba Dünya", "soruIsareti");
?>
```