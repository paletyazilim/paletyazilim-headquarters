PHP kodundaki bir yorum, programın bir parçası olarak **çalıştırılmayan** bir satırdır. Tek amacı koda bakan biri tarafından okunmaktır.

Yorumlar şu amaçlarla kullanılabilir:

- Başkalarının kodunuzu anlamasına izin verin.
- Kendinize ne yaptığınızı hatırlatın - Çoğu programcı, bir veya iki yıl sonra kendi çalışmalarına geri dönüp ne yaptıklarını yeniden bulmak zorunda kalmayı deneyimlemiştir. Yorumlar size kodu yazarken ne düşündüğünüzü hatırlatabilir.
- Kodunuzun bazı bölümlerini dışarıda bırakın.

PHP yorum yapmanın çeşitli yollarını destekler:

```PHP title:"PHP'de yorumlar için sözdizimleri:"
// Tek satırlık bir yorumdur

# Tek satırlık bir yorumdur

/* Çok
satırlı yorum */
```

### Tek Satırlı Yorumlar
---
Tek satırlık yorumlar `//` ile başlar.

`//` ile satır sonu arasındaki herhangi bir metin yok sayılacaktır (yürütülmeyecektir).

Tek satırlık yorumlar için `#` kullanabilirsiniz, ancak bu eğitim boyunca `//` kullanacağız.

Aşağıdaki örneklerde açıklama olarak tek satırlık bir yorum kullanılmaktadır:

```PHP title:"Kodun üstünde hatırlatan bir yorum:" hl:1 
// Bir karşılama mesajı çıktılar:
echo "Anasayfaya Hoşgeldiniz!";
```

```PHP title:"Kodun yanında hatırlatan bir yorum:" hl:1 
echo "Anasayfaya Hoşgeldiniz!"; // Bir karşılama mesajı çıktılar
```

#### Kodu Yoksaymak için Yorumlar
---
Kod satırlarının yürütülmesini engellemek için yorumları kullanabiliriz:

```PHP title:"Hatalı kodu engelleyen yorum satırı:" hl:1
// ech 'Merhaba Dünya!';
```

### Çok Satırlı Yorumlar
---
Çok satırlı yorumlar `/*` ile başlar ve `*/` ile biter.

`/*` ve `*/` arasındaki herhangi bir metin yok sayılacaktır.

Aşağıdaki örnekte açıklama olarak çok satırlı bir yorum kullanılmıştır:

```PHP title:"Açıklama olarak çok satırlı yorum:" hl:1-3
/*
Bir sonraki kod bir karşılama mesajı yazdırır
*/
echo "Anasayfaya Hoşgeldiniz!";
```

#### Kodu Yoksaymak için Çok Satırlı Yorumlar
---
Kod bloklarının yürütülmesini önlemek için çok satırlı yorumlar kullanabiliriz:

```PHP title:"Kodu yok saymak için çok satırlı yorum:" hl:1-4 
/*
echo "Anasayfaya Hoşgeldiniz!;
echo "Mi casa su casa!;
*/
echo "Merhaba!";
```

#### Kodun Ortasındaki Yorumlar
---
Çok satırlı yorum sözdizimi, bir kod satırı içindeki parçaların yürütülmesini önlemek için de kullanılabilir:

```PHP title:"+15 kısmı hesaplamada göz ardı edilecektir:" hl:1
$x = 5 /* + 15 */ + 5;
echo $x;
```