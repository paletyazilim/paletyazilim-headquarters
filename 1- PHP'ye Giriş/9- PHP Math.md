
PHP, sayılar üzerinde matematiksel işlemler yapmanıza olanak tanıyan bir dizi yerleşik matematik fonksiyonlarına sahiptir.

### `pi()` Fonksiyonu
---
`pi()` fonksiyonu PI değerini döndürür:

```PHP
echo(pi());
```

### `min()` ve `max()` Fonksiyonları
---
`min()` ve `max()` fonksiyonları, bir argüman listesindeki en düşük veya en yüksek değeri bulmak için kullanılabilir:

```PHP
echo(min(0, 150, 30, 20, -8, -200));
echo(max(0, 150, 30, 20, -8, -200));
```

### `abs()` Fonksiyonu
---
`abs()` fonksiyonu bir sayının mutlak (pozitif) değerini döndürür:

```PHP
echo(abs(-6.7));
```

### `sqrt()` Fonksiyonu
---
`sqrt()` fonksiyonu bir sayının karekökünü döndürür:

```PHP
echo(sqrt(64));
```

### `round()` Fonksiyonu
---
`round()` işlevi, ondalıklı bir sayıyı en yakın tamsayıya yuvarlar:

```PHP
echo(round(0.60));
echo(round(0.49));
```

### Rastgele Sayılar
---
`rand()` fonksiyonu rastgele bir sayı üretir:

```PHP
echo(rand());
```

Rastgele sayı üzerinde daha fazla kontrol elde etmek için, döndürülecek en düşük tamsayıyı ve en yüksek tamsayıyı belirtmek üzere isteğe bağlı min ve max parametrelerini ekleyebilirsiniz.

Örneğin, 10 ile 100 (dahil) arasında rastgele bir tamsayı istiyorsanız `rand(10, 100)` kullanın:

```PHP
echo(rand(10, 100));
```




