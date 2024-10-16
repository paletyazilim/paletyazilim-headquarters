Kullanıcı gönder düğmesine bastıktan sonra giriş alanlarındaki değerleri göstermek için, aşağıdaki giriş alanlarının değer niteliğinin içine küçük bir PHP betiği ekliyoruz: ad, e-posta ve web sitesi. Yorum textarea alanında, betiği `<textarea>` ve `</textarea>` etiketleri arasına yerleştiriyoruz. Küçük betik `$name`, `$email`, `$websit`e ve `$comment` değişkenlerinin değerini çıktı olarak verir.

Ardından, hangi radyo düğmesinin işaretlendiğini de göstermemiz gerekir. Bunun için, checked niteliğini (radyo düğmeleri için `value` niteliğini değil) manipüle etmeliyiz:

```PHP title:'Son dokunuşlar'
İsim: <input type="text" name="isim" value="<?php echo $name;?>">
<span>* <?php echo $nameErr;?></span>
<br><br>

E-posta:
<input type="text" name="email" value="<?php echo $email;?>">
<span>* <?php echo $emailErr;?></span>
<br><br>

Websayfa:
<input type="text" name="website" value="<?php echo $website;?>">
<span><?php echo $websiteErr;?></span>
<br><br>

Yorum: <textarea name="yorum" rows="5" cols="40"><?php echo $comment;?></textarea>
<br><br>

Cinsiyet:
<input type="radio" name="cinsiyet" value="kadin" <?php if (isset($gender) && $gender=="kadin") echo "checked";?>>Female
<input type="radio" name="cinsiyet" value="erkek" <?php if (isset($gender) && $gender=="erkek") echo "checked";?>>Male
<input type="radio" name="cinsiyet" value="diger" <?php if (isset($gender) && $gender=="diger") echo "checked";?>>Other
<span class="error">* <?php echo $genderErr;?></span>
<br><br>
```

### Kodun Tamamlanmış Hali
---

```PHP title:'Bütün kod'
<body>
<?php
// Karışıklık olmaması için bu değişkenleri ingilizce tanımladık.
$nameErr = $emailErr = $genderErr = $websiteErr = "";
$name = $email = $gender = $comment = $website = "";

if ($_SERVER["REQUEST_METHOD"] == "POST") {
  if (empty($_POST["isim"])) {
    $nameErr = "Bu alan zorunludur";
  } else {
    $name = test_input($_POST["isim"]);

    if (!preg_match("/^[a-zA-Z-' ]*$/",$name)) {
      $nameErr = "Yalnızca karakter ve boşluk";
    }
  }

  if (empty($_POST["eposta"])) {
    $emailErr = "Bu alan zorunludur";
  } else {
    $email = test_input($_POST["eposta"]);

    if (!filter_var($email, FILTER_VALIDATE_EMAIL)) {
      $emailErr = "Yanlış e-posta formatı";
    }
  }

  if (empty($_POST["website"])) {
    $website = "";
  } else {
    $website = test_input($_POST["website"]);

    if (!preg_match("/\b(?:(?:https?|ftp):\/\/|www\.)[-a-z0-9+&@#\/%?=~_|!:,.;]*[-a-z0-9+&@#\/%=~_|]/i",$website)) {
      $websiteErr = "Geçersiz URL";
    }
  }

  if (empty($_POST["yorum"])) {
    $comment = "";
  } else {
    $comment = test_input($_POST["yorum"]);
  }

  if (empty($_POST["cinsiyet"])) {
    $genderErr = "Bu alan zorunludur";
  } else {
    $gender = test_input($_POST["cinsiyet"]);
  }
}
?>

<h2>PHP Form Doğrulama Örneği</h2>
<p><span>* zorunlu</span></p>
<form method="POST" action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]);?>">

İsim: <input type="text" name="isim" value="<?php echo $name;?>">
<span>* <?php echo $nameErr;?></span>
<br><br>

E-posta:
<input type="text" name="email" value="<?php echo $email;?>">
<span>* <?php echo $emailErr;?></span>
<br><br>

Websayfa:
<input type="text" name="website" value="<?php echo $website;?>">
<span><?php echo $websiteErr;?></span>
<br><br>

Yorum: <textarea name="yorum" rows="5" cols="40"><?php echo $comment;?></textarea>
<br><br>

Cinsiyet:
<input type="radio" name="cinsiyet" value="kadin" <?php if (isset($gender) && $gender=="kadin") echo "checked";?>>Female
<input type="radio" name="cinsiyet" value="erkek" <?php if (isset($gender) && $gender=="erkek") echo "checked";?>>Male
<input type="radio" name="cinsiyet" value="diger" <?php if (isset($gender) && $gender=="diger") echo "checked";?>>Other
<span class="error">* <?php echo $genderErr;?></span>
<br><br>

<input type="submit" name="submit" value="Gönder">
</form>

<?php
echo "<h2>Girilen Bilgiler:</h2>";
echo $name;
echo "<br>";
echo $email;
echo "<br>";
echo $website;
echo "<br>";
echo $comment;
echo "<br>";
echo $gender;
?>

</body>
```