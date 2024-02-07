Bu sayfalar PHP formlarının güvenlik göz önünde bulundurularak nasıl işleneceğini gösterecektir. Form verilerinin doğru şekilde doğrulanması, formunuzu bilgisayar korsanlarından ve spam gönderenlerden korumak için önemlidir!

Bu bölümlerde üzerinde çalışacağımız HTML formu çeşitli girdi alanları içerir: gerekli ve isteğe bağlı metin alanları, radyo düğmeleri ve bir gönder düğmesi:

<body>  
<h2>PHP Form Doğrulama Örneği</h2>
<p><span>* zorunlu</span></p>
<form>  
  İsim: <input type="text" name="name" value="">
  <span>*</span>
  <br><br>
  E-posta: <input type="text" name="email" value="">
  <span>*</span>
  <br><br>
  Websayfanız: <input type="text" name="website" value="">
  <span></span>
  <br><br>
  Yorumunuz: <textarea name="comment" rows="5" cols="40"></textarea>
  <br><br>
  Cinsiyet:
  <input type="radio" name="gender" value="female">Kadın
  <input type="radio" name="gender" value="male">Erkek
  <input type="radio" name="gender" value="other">Diğer  
  <span>*</span>
  <br><br>
  <input type="submit" name="submit" value="Gönder">  
</form>
</body>

Yukarıdaki form için doğrulama kuralları aşağıdaki gibi olsun:

| Alan | Doğrulama Kuralı |
| ---- | ---- |
| İsim | Zorunlu + Yalnızca harf ve boşluk içermelidir |
| E-posta | Zorunlu + Must contain a valid email address (with @ and ) |
| Websayfanız | İsteğe bağlı. Mevcutsa, geçerli bir URL içermelidir |
| Yorumunuz | İsteğe bağlı. Çok satırlı giriş alanı (textarea) |
| Cinsiyet | Zorunlu. Bir tane seçmelisiniz |
