MySQL, döndürülecek kayıt sayısını belirtmek için kullanılan bir LIMIT sözcüğü sağlar.

LIMIT sözcüğü, SQL ile çok sayfalı sonuçlar veya sayfalandırma kodlamayı kolaylaştırır ve büyük tablolarda çok kullanışlıdır. Çok sayıda kayıt döndürmek performansı etkileyebilir.

"Siparisler" adlı bir tablodan 1 - 30 (dahil) arasındaki tüm kayıtları seçmek istediğimizi varsayalım. SQL sorgusu şu şekilde olacaktır:

```MySQL
SELECT * FROM Siparisler LIMIT 30
```

Yukarıdaki SQL sorgusu çalıştırıldığında, ilk 30 kaydı döndürecektir.

Peki ya 16 - 25 (dahil) arasındaki kayıtları seçmek istersek?

MySQL ayrıca bunu halletmek için bir yol sunar: OFFSET sözcüğü.

Aşağıdaki SQL sorgusu "sadece 10 kayıt döndür, 16. kayıttan başla (OFFSET 15)" diyor:

```MySQL
SELECT * FROM Siparisler LIMIT 10 OFFSET 15
```

Aynı sonucu elde etmek için daha kısa bir sözdizimi de kullanabilirsiniz:

```MySQL
SELECT * FROM Siparisler LIMIT 15, 10
```

Virgül kullandığınızda sayıların ters çevrildiğine dikkat edin.

