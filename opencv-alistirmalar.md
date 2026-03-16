# 📝 OpenCV — Alıştırmalar

> **Modül:** Alıştırmalar | **Seviye:** Kolay → Orta → Zor
> **İlgili dosyalar:** `opencv-teori.md` · `opencv-uygulamalar.md` · `opencv-alistirmalar-cozum.md`

---

## Nasıl Çalışılır?

Bu alıştırmalar bilgisayarlı görü düşünce biçimini geliştirmek için tasarlanmıştır. Her soru bir problemi tanımlar; sizden hem **ne yapacağınıza** hem de **nasıl yapacağınıza** karar vermeniz beklenir.

- Soruyu okuyun, koda atlamadan önce pipeline'ı kafanızda kurun.
- İpucuna yalnızca gerçekten takılırsanız bakın.
- Çözümünüzü tamamladıktan sonra `opencv-alistirmalar-cozum.md` ile karşılaştırın.
- Farklı parametrelerle oynayarak sonucun nasıl değiştiğini gözlemleyin.

```
Zorluk göstergesi:
⭐         — Kolay
⭐⭐       — Orta
⭐⭐⭐     — Zor
```

---

## BLOK 1 — Kolay Alıştırmalar ⭐

---

### Soru 1.1 — Görüntü Kimlik Kartı

**Problem:**
Verilen bir görüntü dosyası için aşağıdaki bilgileri hesaplayan ve ekrana yazdıran bir program yazın:

- Genişlik ve yükseklik (piksel cinsinden)
- Kanal sayısı
- Veri tipi (`dtype`)
- Toplam piksel sayısı
- Bellekte kapladığı alan (KB cinsinden)
- En küçük ve en büyük piksel değeri
- Ortalama parlaklık (gri tonlamalı versiyon üzerinden)
- Görüntünün genel parlaklık durumu: "Karanlık", "Normal" veya "Parlak"

Son olarak orijinal görüntü ile gri tonlamalı versiyonunu yan yana gösterin.

**Amaç:** Görüntü veri yapısını ve temel NumPy operasyonlarını kavramak.

**Gereken teknikler:**
- `cv2.imread`, `cv2.cvtColor`, `cv2.imshow`
- `img.shape`, `img.dtype`, `img.nbytes`, `img.min()`, `img.max()`
- `np.hstack`, `cv2.putText`

**İpucu:**
Parlaklık değerlendirmesi için gri görüntünün ortalama piksel değerini belirli eşiklerle karşılaştırmayı düşünün. Yan yana gösterim için iki görüntünün aynı yükseklikte ve aynı kanal sayısında olması gerektiğini göz önünde bulundurun.

---

### Soru 1.2 — Üç Filtre, Bir Karar

**Problem:**
Aynı görüntüye üç farklı bulanıklaştırma filtresi uygulayın: `GaussianBlur`, `medianBlur` ve `bilateralFilter`. Sonuçları orijinal görüntüyle birlikte dört panel halinde yan yana gösterin; her panelon üstüne filtre adını yazın.

Ardından şu soruyu yanıtlayın ve programınızın son satırlarında `print` ile cevabı yazdırın:

> "Kenarları koruma açısından en iyi filtre hangisi? Neden?"

**Amaç:** Filtreleme yöntemlerinin görsel farkını gözlemlemek ve seçim gerekçesi geliştirmek.

**Gereken teknikler:**
- `cv2.GaussianBlur`, `cv2.medianBlur`, `cv2.bilateralFilter`
- `np.hstack`, `cv2.putText`

**İpucu:**
Filtreleri aynı "güç" seviyesinde karşılaştırmak için her birinde benzer alan büyüklükleri kullanmayı deneyin. Kenar korumayı değerlendirmek için keskin kenarlı bir görüntü seçin ya da yapay olarak oluşturun. Kenar bölgelerinde iki filtre çıktısını piksel piksel gözlemleyin.

---

### Soru 1.3 — Parlaklık Ayarlayıcı

**Problem:**
Kullanıcıdan bir görüntü ve bir parlaklık katsayısı (`alpha`, 0.5 ile 2.0 arası) alarak görüntünün parlaklığını ayarlayan bir fonksiyon yazın: `parlaklik_ayarla(img, alpha)`.

Bu fonksiyon:
- `alpha < 1.0` ise görüntüyü karartır
- `alpha > 1.0` ise görüntüyü aydınlatır
- `alpha = 1.0` ise değişiklik yapmaz

Ayrıca beş farklı alpha değeri için (0.5, 0.75, 1.0, 1.5, 2.0) sonuçları yan yana gösterin ve her panel için gri tonlamalı versiyonun ortalama piksel değerini üstüne yazdırın.

**Amaç:** `uint8` veri tipinin sınırlarını anlamak ve güvenli piksel manipülasyonu yapmak.

**Gereken teknikler:**
- `np.clip`, `.astype(np.float32)`, `.astype(np.uint8)`
- `cv2.putText`, `np.hstack`

**İpucu:**
`uint8` tipiyle doğrudan çarpa veya böle işlemi yapmak değer taşmasına (overflow) neden olur. Hesaplamanın hangi veri tipinde yapılması gerektiğini ve sonucu `uint8`'e geri döndürmeden önce hangi güvenlik adımını uygulamanız gerektiğini düşünün.

---

### Soru 1.4 — Kenar Bölge Analizi

**Problem:**
Bir görüntüde iki farklı kernel boyutuyla (örn. `(3,3)` ve `(15,15)`) Gaussian bulanıklaştırma uygulayın. Ardından her iki sonucu Canny kenar tespitinden geçirin. Dört sonucu (2 bulanık + 2 kenar) ızgara düzeninde gösterin.

Konsolda şunu yazdırın:
- Küçük kernel + Canny sonucundaki beyaz piksel sayısı
- Büyük kernel + Canny sonucundaki beyaz piksel sayısı
- "Hangi kernel daha fazla kenar buldu ve neden?"

**Amaç:** Ön işleme parametrelerinin kenar tespiti sonucuna etkisini anlamak.

**Gereken teknikler:**
- `cv2.GaussianBlur`, `cv2.Canny`
- `np.sum` veya `cv2.countNonZero`
- `np.hstack`, `np.vstack`

**İpucu:**
Kernel büyüdükçe görüntü daha fazla yumuşar. Canny'nin görevi gerçek kenarları gürültüden ayırmaktır. Gürültü fazla yumuşatılırsa ne tür kenarlar kaybolur? Hangilerini hâlâ bulabilir?

---

### Soru 1.5 — Şekil Çizim Katmanı

**Problem:**
Siyah arka plan üzerine aşağıdaki şekilleri çizen bir görüntü (600×800, 3 kanal) oluşturun:

1. Sol üstte dolu kırmızı daire (merkez: 150,150, yarıçap: 80)
2. Ortada içi boş mavi dikdörtgen (kalınlık: 3)
3. Çapraz yeşil çizgi (sol alttan sağ üste)
4. Sağ altta sarı elips (döndürme açısı: 30°)
5. Her şeklin yanına beyaz renkte adını yazan metin

Ardından bu görüntüyü orijinal bir fotoğrafın üzerine saydamlık efektiyle (`addWeighted`) bindirin ve sonucu kaydedin.

**Amaç:** Çizim fonksiyonlarını ve görüntü birleştirme işlemini uygulamak.

**Gereken teknikler:**
- `cv2.circle`, `cv2.rectangle`, `cv2.line`, `cv2.ellipse`, `cv2.putText`
- `cv2.addWeighted`

**İpucu:**
`addWeighted(src1, alpha, src2, beta, gamma)` fonksiyonu iki görüntüyü ağırlıklı olarak birleştirir. `alpha + beta = 1.0` olacak şekilde değer seçin. İki görüntünün aynı boyutta ve aynı veri tipinde olması gerekir.

---

## BLOK 2 — Orta Alıştırmalar ⭐⭐

---

### Soru 2.1 — HSV Dedektifi

**Problem:**
Aşağıdaki pipeline'ı uygulayan bir program yazın:

1. Bir görüntüyü HSV uzayına çevirin.
2. Kırmızı nesneleri maskeleme oluşturun. *(İpucu: Kırmızı HSV'de iki ayrı bölgede bulunur.)*
3. Maskeye morfolojik temizleme uygulayın.
4. Maskeye uyan en büyük konturun etrafına yeşil bir çerçeve çizin.
5. Konsolda şunları yazdırın:
   - Tespit edilen toplam kırmızı piksel sayısı
   - En büyük kırmızı nesnenin alanı ve çevre uzunluğu
   - Nesnenin daireselliği

Kendi HSV aralıklarınızı deneyerek kırmızıyı doğru yakaladığınızdan emin olun.

**Amaç:** HSV renk uzayını, maskelemeyi ve kontur analizini birleştirmek.

**Gereken teknikler:**
- `cv2.cvtColor` (BGR→HSV), `cv2.inRange`, `cv2.bitwise_or`
- `cv2.morphologyEx`, `cv2.findContours`
- `cv2.contourArea`, `cv2.arcLength`, `cv2.boundingRect`

**İpucu:**
Kırmızı renk HSV'de H=0 civarı **ve** H=170-180 civarında iki ayrı bölge kaplar; dolayısıyla iki ayrı `inRange` maskesi oluşturup bunları `bitwise_or` ile birleştirmeniz gerekebilir. Maskenizin doğruluğunu ayrı bir pencerede göstererek kontrol edin.

---

### Soru 2.2 — Eşikleme Yarışması

**Problem:**
Aynı gri görüntüde dört farklı eşikleme yöntemi uygulayın:

1. Global eşik (manuel, threshold=127)
2. Otsu yöntemi
3. Adaptif Mean C
4. Adaptif Gaussian C

Dört sonucu orijinal görüntüyle birlikte 1×5 panelde gösterin. Konsolda her yöntemin bulduğu beyaz piksel sayısını yazdırın.

Ardından şu soruyu yanıtlayın:

> "Düzensiz aydınlatma altında çekilmiş bir belgede hangi yöntem daha iyi çalışır ve neden?"

Cevabınızı `print` ile yazdırın.

**Amaç:** Eşikleme yöntemlerinin hangi koşullarda üstün olduğunu kavramak.

**Gereken teknikler:**
- `cv2.threshold` (THRESH_BINARY, THRESH_OTSU)
- `cv2.adaptiveThreshold` (ADAPTIVE_THRESH_MEAN_C, ADAPTIVE_THRESH_GAUSSIAN_C)
- `cv2.countNonZero`

**İpucu:**
Dört yöntemi adil karşılaştırmak için aynı görüntüyü kullanın ve adaptif yöntemler için `blockSize` ve `C` parametrelerini deneyerek makul sonuç alın. Özellikle aydınlatmanın bozuk olduğu bir görüntüde test edin; gerekirse `np.random` ile sahte gölge ekleyebilirsiniz.

---

### Soru 2.3 — Morfoloji Tasarımcısı

**Problem:**
Beyaz arka plan üzerinde siyah metin veya şekiller içeren bir görüntü alın. Bu görüntü üzerinde şu üç problemi sırasıyla çözün:

**Problem A:** Binary görüntüde küçük gürültü noktaları var. Bunları büyük nesnelere dokunmadan silin.

**Problem B:** Binary görüntüde nesnelerin içinde küçük delikler var. Bu delikleri doldurun.

**Problem C:** Bitişik nesneleri birbirinden ayırmak için aralarındaki ince bağlantıları kesin (her nesne ayrı kalmalı, fazla küçülmesin).

Her problem için kullandığınız morfolojik işlemi, kernel boyutunu ve iterations sayısını bir yorum satırında belirtin. Her adımın binary görüntüsünü ayrı pencerede gösterin.

**Amaç:** Morfolojik işlemleri belirli bir amaca yönelik seçmeyi öğrenmek.

**Gereken teknikler:**
- `cv2.erode`, `cv2.dilate`, `cv2.morphologyEx`
- `cv2.getStructuringElement` (MORPH_RECT, MORPH_ELLIPSE, MORPH_CROSS)
- `cv2.threshold` (ön işleme için)

**İpucu:**
Her problem için farklı bir morfolojik işlem uygundur. "Gürültü silme" ve "delik kapatma" farklı operasyonlarla yapılır; hangisinin önce erozyon hangisinin önce dilatasyon yaptığını teori notlarından hatırlayın. Üçüncü problem için ise daha küçük kernel veya iterasyon sayısı belirleyici olabilir.

---

### Soru 2.4 — Akıllı Nesne Sayacı

**Problem:**
Birden fazla nesne içeren bir görüntüde:

1. Uygun ön işleme pipeline'ı tasarlayın (gri → blur → eşikleme → morfoloji).
2. `findContours` ile nesneleri bulun ve alan filtresi uygulayın.
3. Her nesnenin üzerine sıra numarası yazın.
4. Konsolda bir özet tablo yazdırın:

```
Nesne | Alan (px) | Çevre | Dairesellik | Tahmini Şekil
------|-----------| ------|-------------|---------------
  1   |  15234    |  452  |    0.94     |    Daire
  2   |   8901    |  380  |    0.78     |    Dikdortgen
  ...
Toplam: 5 nesne
```

5. Toplam nesne sayısını sol üst köşeye yazın.

**Amaç:** Kontur analizi, özellik çıkarımı ve sonuç raporlamayı bir pipeline içinde birleştirmek.

**Gereken teknikler:**
- `cv2.threshold`, `cv2.morphologyEx`
- `cv2.findContours`, `cv2.contourArea`, `cv2.arcLength`
- `cv2.approxPolyDP`, `cv2.moments`, `cv2.putText`

**İpucu:**
Hangi ön işleme adımlarının gerekli olduğuna karar vermek için görüntüye bakın: aydınlatma düzgün mü, gürültü var mı, nesneler birbirine değiyor mu? Dairesellik formülünü teori notlarından kontrol edin. Tablo oluştururken `print(f"...")` ile biçimlendirilmiş çıktı yeterlidir; `pandas` zorunlu değildir.

---

### Soru 2.5 — Histogram Dedektifi

**Problem:**
İki farklı görüntü alın (biri iyi aydınlatılmış, biri düşük kontrastlı). Her görüntü için:

1. Gri tonlamalı histogramı hesaplayın ve Matplotlib ile çizin.
2. Standart sapmayı hesaplayarak kontrast kalitesini sayısal olarak ölçün.
3. Her görüntüye hem `equalizeHist` hem de CLAHE uygulayın.
4. İyileştirilmiş görüntülerin histogramlarını yan yana çizin.

Son olarak:
- Hangi yöntem hangi görüntüde daha iyi sonuç verdi?
- CLAHE'nin `clipLimit` parametresini 1.0, 2.0 ve 4.0 olarak deneyin ve farkı yorumlayın.

**Amaç:** Histogram analizini görüntü kalitesi değerlendirmesi için kullanmak ve iyileştirme yöntemlerini karşılaştırmak.

**Gereken teknikler:**
- `cv2.calcHist`, `cv2.equalizeHist`, `cv2.createCLAHE`
- `matplotlib.pyplot`
- `numpy.std`

**İpucu:**
Histogram dağılımına bakarak bir görüntünün aydınlatma durumunu yorumlayabilirsiniz: tüm değerler düşük yoğunlukta yığılmışsa görüntü karanlık, yüksekte yığılmışsa parlak, ortada yığılmışsa düşük kontrastlıdır. `equalizeHist` ve CLAHE'nin başarısını hem görsel hem sayısal olarak ölçmeyi deneyin.

---

### Soru 2.6 — Kenar Yoğunluğu Haritası

**Problem:**
Bir görüntüye Canny uygulayıp kenar haritasını elde edin. Ardından bu kenar haritasını düzenli bir ızgaraya (örneğin 8×8 veya 16×16 blok) bölün. Her bloktaki beyaz piksel (kenar) sayısını hesaplayın ve bunu bir "kenar yoğunluğu" haritasına dönüştürün.

Bu haritayı ısı haritası olarak görselleştirin: yoğun kenar bölgeleri kırmızıya, az kenarlı bölgeler maviye yakın görünsün. Sonucu orijinal görüntünün üzerine yarı saydam olarak bindirin.

**Amaç:** Kenar tespiti sonuçlarını bölgesel analiz için kullanmak ve sonuç görselleştirmek.

**Gereken teknikler:**
- `cv2.Canny`, `cv2.GaussianBlur`
- `cv2.resize`, `cv2.applyColorMap`
- `cv2.addWeighted`

**İpucu:**
Her bloktaki beyaz piksel sayısını küçük bir matrise (örneğin 8×8) yazın. Bu matrisi normalize edip `cv2.resize` ile orijinal boyuta büyütün, ardından `cv2.applyColorMap(mat, cv2.COLORMAP_JET)` ile renkli ısı haritasına dönüştürün.

---

## BLOK 3 — Zor Alıştırmalar ⭐⭐⭐

---

### Soru 3.1 — Bozuk Metin Onarımı

**Problem:**
Bir görüntünüz var: üzerinde yazı olan beyaz kâğıt. Sorunlar:
- Yazı gri tonlamalı, arka plan düzensiz aydınlatmalı
- Bazı harfler arasında kopmalar var (ince çizgiler)
- Küçük gürültü noktaları mevcut

Şu adımları gerçekleştirin:

1. Uygun eşikleme yöntemiyle metni beyaz, arka planı siyah yapın.
2. Harf içindeki boşlukları ve kopmaları onarın.
3. Gürültü noktalarını temizleyin.
4. Bağlı harf gruplarını (kelimeler veya karakter kümeleri) birer kontur olarak bulun.
5. Her grubu ayrı bounding box ile işaretleyin ve grupların koordinatlarını konsola yazdırın.

Ardından: Orijinal görüntü, ara adımlar (eşikleme sonrası, onarım sonrası) ve son tespit görüntüsünü bir grid'de yan yana gösterin.

**Amaç:** Gerçek bir görüntü işleme probleminde birden fazla tekniği sıralı ve gerekçeli biçimde uygulamak.

**Gereken teknikler:**
- `cv2.adaptiveThreshold` veya `cv2.threshold` + OTSU
- `cv2.morphologyEx` (CLOSE, OPEN, kombinasyon)
- `cv2.findContours`, `cv2.boundingRect`
- Çoklu pencere veya grid gösterimi

**İpucu:**
Ön işleme sırasında yaptığınız her adımın binary görüntüyü nasıl değiştirdiğini ara pencerelerle gözlemleyin. Kopmalar ve boşluklar için hangi morfolojik işlemin uygun olduğunu düşünün. Gürültü temizleme ve boşluk kapatma işlemlerinin sırası önemlidir; hangisinin önce yapılması daha mantıklı?

---

### Soru 3.2 — Otomatik Pano Algılayıcı

**Problem:**
Bir fotoğrafta dörtgen şekilli bir pano, tabela veya kâğıt var. Programınız:

1. Görüntüdeki en büyük dörtgen nesneyi otomatik olarak bulmalı.
2. Nesnenin dört köşesini kırmızı nokta ile işaretlemeli.
3. `warpPerspective` ile nesneyi düzleştirip tam dikdörtgen hale getirmeli.
4. Düzleştirilmiş görüntüye CLAHE uygulayarak kontrastı iyileştirmeli.
5. Orijinal görüntü, köşe tespiti ve düzleştirilmiş sonuç olmak üzere üç paneli yan yana göstermeli.

**Kısıt:** `eps` değerini sabit kodlamayın; kontur çevresinin belirli bir yüzdesi olarak hesaplayın.

**Amaç:** Kenar tespiti, kontur analizi ve perspektif dönüşümünü bir araya getirmek.

**Gereken teknikler:**
- `cv2.Canny`, `cv2.dilate`, `cv2.findContours`
- `cv2.approxPolyDP`, `cv2.arcLength`
- `cv2.getPerspectiveTransform`, `cv2.warpPerspective`
- `cv2.createCLAHE`

**İpucu:**
Dört köşe bulmak için `approxPolyDP` kullanırken `epsilon = 0.02 * cevre` iyi bir başlangıçtır ama görüntüye göre ayarlama gerekebilir. Köşeleri TL, TR, BR, BL olarak doğru sıraya koymak perspektif dönüşümünün kalitesini doğrudan etkiler. Köşeleri sıralamak için koordinat toplamı ve fark özelliklerini kullanabilirsiniz.

---

### Soru 3.3 — İki Görüntü Fark Dedektifi

**Problem:**
"Farkı Bul" türü bir problem: Neredeyse aynı olan iki görüntü verilmiş. Aralarındaki görsel farklılıkları otomatik olarak tespit eden bir program yazın.

Programınız:
1. İki görüntüyü aynı boyuta getirmeli.
2. Fark görüntüsünü hesaplamali ve eşikleme uygulamalı.
3. Farklı bölgeleri morfoloji ile temizlemeli.
4. Her farklılık bölgesini bounding box ile işaretlemeli.
5. Kaç farklılık bulunduğunu ve her birinin piksel alanını yazdırmalı.
6. Farklılıkları iki orijinal görüntü üzerinde aynı anda göstermeli (aynı kutular iki görüntüye de çizilmeli).

**Amaç:** Görüntü aritmetiğini, maskeleme ve kontur analizini birleştirmek.

**Gereken teknikler:**
- `cv2.absdiff` veya NumPy çıkarma
- `cv2.cvtColor`, `cv2.threshold`
- `cv2.morphologyEx`, `cv2.findContours`
- `cv2.boundingRect`, `cv2.rectangle`

**İpucu:**
İki görüntü arasındaki fark genellikle gürültülüdür. Fark hesabından sonra eşikleme ve morfoloji ile bu gürültüyü ayıklamanız gerekir. Küçük farklılıkları gözden kaçırmamak için uygun minimum alan eşiği seçin.

---

### Soru 3.4 — Tek Renkli Nesne Takip Edici (Video)

**Problem:**
Kamera görüntüsü üzerinde gerçek zamanlı çalışan bir renk takip sistemi tasarlayın. Sistem:

1. Belirli bir renkteki (tercihinize göre; sarı, mavi, yeşil vb.) nesneyi HSV ile tespit etmeli.
2. Nesnenin merkez koordinatlarını ve yaklaşık yarıçapını gerçek zamanlı hesaplamalı.
3. Son 30 konumu bir deque'de saklayıp iz olarak ekrana çizmeli (iz zamanla soluklaşmalı).
4. Nesne bulunamadığında "Nesne Kayıp" uyarısı göstermeli.
5. `r` tuşuna basıldığında izi sıfırlamalı, `q` ile çıkmalı.

**Kısıt:** HSV aralığını koda sabit yazmak yerine, bir Python sözlüğü veya değişkenler grubu üzerinden tanımlayın, böylece renk değiştirmek kolayca mümkün olsun.

**Amaç:** Video döngüsü, renk maskeleme ve hareket izi görselleştirmeyi bir arada kullanmak.

**Gereken teknikler:**
- `cv2.VideoCapture`, `cv2.cvtColor` (BGR→HSV)
- `cv2.inRange`, `cv2.morphologyEx`
- `cv2.findContours`, `cv2.minEnclosingCircle`, `cv2.moments`
- `collections.deque`, `cv2.line`, `cv2.circle`

**İpucu:**
İz çizerken "zamanla soluklaşma" efekti için her çizginin kalınlığını veya rengini iz uzunluğundaki konumuna göre değiştirmeyi deneyin. Nesne bulunamadığında deque'ye `None` ekleyip çizim döngüsünde bu değerleri atlayabilirsiniz.

---

### Soru 3.5 — Tam Görüntü Analiz Pipeline'ı

**Problem:**
Bu soru tek bir pipeline kurmayı gerektiriyor. Birden fazla nesne içeren herhangi bir görüntü alın ve aşağıdaki **her adımı** kendiniz tasarlayın — ne kullanacağınıza, hangi sırada uygulayacağınıza ve parametreleri nasıl seçeceğinize bağımsız olarak karar verin:

**Adım 1 — Ön İşleme:**
Görüntüyü analiz için hazırlayın. Hangi renk uzayı, hangi filtre, hangi eşikleme yöntemi uygun?

**Adım 2 — Nesne Tespiti:**
Nesneleri bulun ve gürültüden ayıklayın. Hangi morfolojik adımlar gerekli?

**Adım 3 — Özellik Çıkarımı:**
Her nesne için şu özellikleri hesaplayın: alan, çevre, dairesellik, en-boy oranı, tahmini şekil adı, kütle merkezi koordinatları.

**Adım 4 — Görselleştirme:**
Her nesnenin üzerine şekil adını ve alanını yazın. Her nesneyi farklı renkle çerçeveleyin.

**Adım 5 — Rapor:**
Konsola sıralı özet tablo yazdırın (büyük alandan küçüğe). Toplam nesne sayısı, en büyük ve en küçük nesnenin alanı, ortalama dairesellik değerini yazdırın.

**Adım 6 — Kalite Değerlendirmesi:**
Tespit sonucunuzu değerlendirin: Hangi nesneler doğru tespit edildi? Hangi durumlarda pipeline başarısız olabilir?

**Amaç:** Bir bilgisayarlı görü çözümünü baştan sona bağımsız olarak tasarlamak ve gerekçelendirmek.

**Gereken teknikler:** Teori ve uygulamalar belgelerinde gördüğünüz tüm teknikler.

**İpucu:**
Her adım için önce hangi problemi çözdüğünüzü bir yorum satırında yazın, sonra kodu yazın. "Bu adımı neden yapıyorum?" sorusu, "nasıl yapıyorum?" sorusundan önce gelmelidir. Farklı görüntülerde test edin; iyi bir pipeline tek bir görüntüye özel olmamalıdır.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `opencv-teori.md` · `opencv-uygulamalar.md` · `opencv-alistirmalar-cozum.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
