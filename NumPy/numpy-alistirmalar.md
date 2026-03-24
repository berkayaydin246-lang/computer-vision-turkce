# 📝 NumPy — Alıştırmalar

> **Modül:** Alıştırmalar | **Seviye:** Kolay → Orta → Zor
> **İlgili dosyalar:** `numpy-teori.md` · `numpy-uygulamalar.md` · `numpy-alistirmalar-cozum.md`

---

## Nasıl Çalışılır?

- Soruyu okuyun, koda atlamadan önce yaklaşımınızı kafanızda kurun.
- İpucuna yalnızca gerçekten takılırsanız bakın.
- Çözümünüzü tamamladıktan sonra `numpy-alistirmalar-cozum.md` ile karşılaştırın.
- Farklı giriş değerleriyle oynayarak sonucun nasıl değiştiğini gözlemleyin.

```
Zorluk göstergesi:
⭐       — Kolay
⭐⭐     — Orta
⭐⭐⭐   — Zor
```

---

## BLOK 1 — Kolay Alıştırmalar ⭐

---

### Soru 1.1 — Kimlik Matrisi Olmadan Birim Matris

**Problem:**
`np.eye` veya `np.identity` fonksiyonlarını **kullanmadan** 5×5 birim matris (köşegeninde 1, geri kalan her yerde 0) oluşturun. Ardından bu matrisin herhangi bir 3×3 kısmını dilimle alın ve konsola yazdırın.

**Amaç:**
`np.zeros`, `np.arange`, dizi indeksleme ve dilimleme becerilerini pekiştirmek.

**Kullanılabilecek teknikler:**
`np.zeros`, `np.arange`, dizi indeksleme

**İpucu:**
Sıfır matris oluşturduktan sonra köşegen konumundaki elemanları seçmek için iki diziye ihtiyacınız var. `np.arange` ile sıra numaralarını hem satır hem de sütun indeksi olarak kullanmayı düşünün.

---

### Soru 1.2 — Aralık ve Şekil Oyunu

**Problem:**
`np.arange` ile 1'den 49'a kadar (49 dahil) tek adımlı bir dizi oluşturun. Bu diziyi:
1. 7×7 matrise dönüştürün.
2. Matrisin yalnızca **çift sütunlarını** (0, 2, 4, 6. sütunlar) alın.
3. Elde edilen alt matrisin satır toplamlarını hesaplayın.

**Amaç:**
`reshape`, sütun dilimleme ve eksen bazlı toplam hesaplamayı anlamak.

**Kullanılabilecek teknikler:**
`np.arange`, `reshape`, dilimleme, `np.sum`

**İpucu:**
Sütun dilimlemede adım parametresi kullanabilirsiniz. `sum` fonksiyonunda hangi `axis` değerinin "her satırın toplamını" vereceğini düşünün.

---

### Soru 1.3 — Geri Sayım Matrisi

**Problem:**
100'den 1'e kadar (1 dahil) geriye sayan bir dizi oluşturun. Bu diziyi 10×10 matrise dönüştürün. Son olarak bu matrisin:
- Ana köşegenindeki değerleri konsola yazdırın.
- Matrisin tersini (`flip`) alın ve her iki versiyonu alt alta gösterin.

**Amaç:**
`arange` ile ters sıra, `reshape` ve köşegen erişimini kavramak.

**Kullanılabilecek teknikler:**
`np.arange`, `reshape`, `np.diag`, `np.flip`

**İpucu:**
`np.arange(başlangıç, bitiş, adım)` — negatif adım değeri diziyi tersine çevirir. Ana köşegenine erişmek için `np.diag` fonksiyonunu araştırın.

---

### Soru 1.4 — Boolean Süzgeci

**Problem:**
100 elemanlı, 0-200 arasında **rastgele tam sayı** dizisi oluşturun. Bu diziden:
1. 50'den küçük olan değerleri ayırın.
2. 100 ile 150 arasındaki değerleri ayırın.
3. 3'e ve 5'e birden bölünebilen değerleri ayırın.

Her grup için eleman sayısını ve ortalamasını yazdırın.

**Amaç:**
Boolean maskeleme ve koşullu filtrelemeyi pratikte uygulamak.

**Kullanılabilecek teknikler:**
`np.random.randint` veya `rng.integers`, Boolean indeksleme, `&` ve `|` operatörleri

**İpucu:**
Birden fazla koşulu birleştirirken her koşuyu ayrı parantez içine almanız gerekir. 3'e ve 5'e bölünebilirlik için modulo operatörü `%` kullanın.

---

### Soru 1.5 — İstatistik Hesap Makinesi

**Problem:**
Aşağıdaki veriyi NumPy dizisine çevirin:

```python
sinav_notlari = [72, 85, 91, 63, 78, 88, 55, 94, 67, 82, 77, 89, 71, 60, 95]
```

Şu metrikleri hesaplayın ve bir rapor olarak yazdırın:
- Ortalama, medyan, standart sapma
- En yüksek ve en düşük not ile onların indeksleri
- 75 ve üzeri alanların sayısı ve oranı (%)
- Notları büyükten küçüğe sıralayın

**Amaç:**
Temel istatistik fonksiyonlarını (`mean`, `median`, `std`, `argmax`, `sort`) bir arada kullanmak.

**Kullanılabilecek teknikler:**
`np.array`, `np.mean`, `np.median`, `np.std`, `np.max`, `np.argmax`, `np.sort`

**İpucu:**
`argmax` ve `argmin` değerin indeksini döndürür — değerin kendisini almak için bu indeksi kullanabilirsiniz.

---

### Soru 1.6 — Görüntü Kanalı Simülasyonu

**Problem:**
Rastgele piksel değerleri içeren 64×64×3 boyutunda `uint8` türünde bir "yapay görüntü" dizisi oluşturun. Ardından:
1. Her kanalın (R, G, B) ortalama değerini hesaplayın.
2. Kırmızı kanalın tüm değerlerini 0 yapın ve görüntünün yeni ortalamasını yazdırın.
3. Orijinal görüntünün parlaklığını 1.3 ile çarpın ancak 255 sınırını aşmayın.

**Amaç:**
`uint8` veri tipi sınırlamalarını ve güvenli piksel manipülasyonunu kavramak.

**Kullanılabilecek teknikler:**
`np.random.randint`, `uint8`, `np.clip`, `astype`, dizi dilimleme

**İpucu:**
`uint8` tipiyle doğrudan çarpma işlemi değer taşmasına neden olabilir. Hesaplamayı hangi veri tipinde yapmanız gerektiğini düşünün; sonra geri dönüştürün.

---

### Soru 1.7 — Tekrarlayan Desen

**Problem:**
`[1, 0, -1]` dizisini şu iki farklı yöntemle genişletin:
1. Bu deseni **10 kez** tekrarlayan 30 elemanlı bir dizi oluşturun.
2. Her elemanı **5 kez** tekrarlayan 9 elemanlı bir dizi oluşturun.

İki diziyi de yazdırın ve her birinin toplamını kontrol edin (her ikisi için de toplam 0 olmalı — neden?).

**Amaç:**
`np.tile` ve `np.repeat` arasındaki farkı pratikte görmek.

**Kullanılabilecek teknikler:**
`np.tile`, `np.repeat`, `np.sum`

**İpucu:**
`tile` deseni tekrarlar, `repeat` her elemanı tekrarlar. Her iki dizi için de toplam neden aynı olacak? Matematiksel olarak düşünün.

---

### Soru 1.8 — Matrisin Alt ve Üst Üçgeni

**Problem:**
Tüm elemanları 1 olan 6×6 matris oluşturun. Ardından bu matrisin:
1. Yalnızca **alt üçgenini** (köşegen dahil) koruyun, geri kalanı 0 yapın.
2. Yalnızca **üst üçgenini** (köşegen dahil) koruyun, geri kalanı 0 yapın.
3. İki matrisin elemanları toplamının orijinal matrisi geri verip vermediğini doğrulayın.

**Amaç:**
`np.tril`, `np.triu` fonksiyonlarını ve matris aritmetiğini anlamak.

**Kullanılabilecek teknikler:**
`np.ones`, `np.tril`, `np.triu`, `np.all`

**İpucu:**
Üçgen matrisler birbirini tamamlar; ancak köşegen her iki fonksiyonda da dahil edildiğinde toplamda ne olur? Köşegen değerleri iki kez sayılmış olur — bunu düzeltmek için ne yaparsınız?

---

## BLOK 2 — Orta Alıştırmalar ⭐⭐

---

### Soru 2.1 — Broadcasting ile Mesafe Tablosu

**Problem:**
`[0, 1, 2, 3, 4, 5, 6, 7]` sayılarından oluşan bir dizi alın. Bu dizinin her elemanı ile diğer tüm elemanları arasındaki **mutlak farkı** içeren 8×8 mesafe matrisi oluşturun. Döngü kullanmayın.

Ardından:
- En büyük mesafeyi bulun.
- Köşegenin sıfır olduğunu doğrulayın.
- Matrisin simetrik olduğunu doğrulayın (`M[i,j] == M[j,i]`).

**Amaç:**
Broadcasting ile iki boyutlu işlem yapabilmeyi ve matris özelliklerini doğrulamayı anlamak.

**Kullanılabilecek teknikler:**
Broadcasting, `np.abs`, `np.all`, `reshape`

**İpucu:**
Bir diziyi `(n, 1)` şeklinde, diğerini `(1, n)` şeklinde yeniden biçimlendirirseniz çıkarma işlemi broadcasting ile `(n, n)` sonuç üretir. Bunun nasıl çalıştığını teorik olarak düşünün.

---

### Soru 2.2 — Veri Temizleme Pipeline'ı

**Problem:**
Aşağıdaki sensör verisini alın:

```python
olcumler = np.array([23.1, 24.5, 999.0, 22.8, 23.9, -50.0, 25.1,
                      24.2, 23.7, 999.0, 24.8, 23.5, 22.9, 25.3, 24.1])
```

`999.0` ve `-50.0` değerleri sensör hatasıdır. Bu pipeline'ı uygulayın:
1. 0-50 aralığı dışındaki değerleri `np.nan` ile değiştirin.
2. NaN değerleri dizinin geçerli değerlerinin **medyanıyla** doldurun.
3. Temizlenmiş dizinin min, max ve ortalamasını yazdırın.
4. Orijinal ile temizlenmiş diziyi yan yana (iki sütun) bir matris olarak gösterin.

**Amaç:**
Koşullu atama, `np.nan`, `np.nanmedian` ve veri temizleme mantığını kavramak.

**Kullanılabilecek teknikler:**
`np.nan`, `np.isnan`, `np.nanmedian`, Boolean maskeleme, `np.column_stack`

**İpucu:**
Önce hatalı değerleri nan yapın, sonra nan değerlerin konumlarını bulun. `nanmedian` hesabı NaN'ları yoksayar; bu yüzden NaN doldurma öncesinde medyanı hesaplamanız gerekir.

---

### Soru 2.3 — Satranç Tahtası Deseni

**Problem:**
NumPy ile 8×8 satranç tahtası deseni oluşturun. Siyah kareler 0, beyaz kareler 1 olsun. Ardından:
1. Siyah kare sayısını ve beyaz kare sayısını doğrulayın (her ikisi de 32 olmalı).
2. Tahtanın herhangi bir 4×4 köşesini alın ve yeni bir dizi oluşturun.
3. Deseni `flip` ile dikey ve yatay olarak yansıtın; yansıtılmış versiyonun orijinalle aynı olup olmadığını kontrol edin.

**Amaç:**
Array oluşturma mantığı, dilimleme ve dizi özelliklerini doğrulamayı pratikleştirmek.

**Kullanılabilecek teknikler:**
`np.zeros`, `np.ones`, `np.tile`, indeksleme, `np.flip`, `np.array_equal`

**İpucu:**
2×2 temel desen (`[[0,1],[1,0]]`) oluşturup bunu `np.tile` ile 4×4 kez tekrarlayabilirsiniz. Veya `np.zeros` üzerinde indeks toplamı çift/tek kontrolü yapabilirsiniz.

---

### Soru 2.4 — Çalışan İstatistikleri

**Problem:**
50 çalışan için rastgele simüle edilmiş veri oluşturun:
- `maas`: 4000-12000 arası rastgele tam sayı
- `yas`: 22-60 arası rastgele tam sayı
- `departman`: 0, 1, 2, 3 (dört departman)

Şunları hesaplayın:
1. Her departmanın ortalama maaşı.
2. 35 yaş üzeri çalışanların maaş ortalaması.
3. En yüksek maaşı alan çalışanın yaşı.
4. Maaşı genel ortalamanın üzerinde olan çalışan oranı (%).

**Amaç:**
Koşullu filtreleme, fancy indeksleme ve gruplu istatistik hesaplamayı uygulamak.

**Kullanılabilecek teknikler:**
`np.random`, Boolean maskeleme, `np.mean`, `np.argmax`, döngü ve filtreleme kombinasyonu

**İpucu:**
Her departman için ayrı maske oluşturup o maskeye uyan maaşların ortalamasını alabilirsiniz. Tüm bunu bir döngü içinde yaparak kod tekrarından kaçının.

---

### Soru 2.5 — Hareketli Ortalama

**Problem:**
200 elemanlı rastgele bir veri dizisi oluşturun (normal dağılım, ortalama=50, std=10). Bu dizi için:
1. 7-elemanlı hareketli ortalama hesaplayın (`np.convolve` ile).
2. 15-elemanlı hareketli ortalama hesaplayın.
3. İki hareketli ortalamanın farkını hesaplayın ve bu farkın en büyük olduğu indeksi bulun.

**Amaç:**
`np.convolve` ile sinyal yumuşatma ve zaman serisi analizini öğrenmek.

**Kullanılabilecek teknikler:**
`np.random.normal`, `np.convolve`, `np.ones`, `np.abs`, `np.argmax`

**İpucu:**
`np.convolve` ile hareketli ortalama için `np.ones(pencere) / pencere` şeklinde bir kernel kullanılır. `mode='valid'` ile çıktı boyutu orijinalden farklı olur — boyutları nasıl hizalayacağınızı düşünün.

---

### Soru 2.6 — Normalize ve Ölçekleme

**Problem:**
Bir görüntüyü temsil eden 100×100 float dizi oluşturun (0-500 arası rastgele değerler). Bu diziyi üç farklı yöntemle 0-255 aralığına ölçeklendirin:
1. **Min-max normalizasyon** → 0-255 arası
2. **Yüzdelik dilimleme** → %2 ve %98 yüzdelik dilimler sınır olarak
3. **Kırpma** → 100'den küçük değerleri 100, 400'den büyük değerleri 400 yap, sonra 0-255'e ölçekle

Her yöntemin çıktısındaki min, max ve ortalama değeri yazdırın. Tüm çıktıların `uint8` tipinde olduğunu doğrulayın.

**Amaç:**
Farklı normalizasyon stratejilerini karşılaştırmak ve `uint8`'e dönüşümdeki incelikleri öğrenmek.

**Kullanılabilecek teknikler:**
`np.clip`, `np.percentile`, `astype`, `np.min`, `np.max`

**İpucu:**
Her yöntemde `(veri - min) / (max - min) * 255` formülü kullanılır; fark minimum ve maksimum değerlerin nasıl seçildiğindedir.

---

### Soru 2.7 — Kümülatif Analiz

**Problem:**
12 aylık satış verisini temsil eden dizi: `[120, 85, 143, 167, 201, 178, 233, 256, 189, 212, 245, 310]`

Şunları hesaplayın:
1. Kümülatif toplam (ay ay birikimli satış).
2. Her aya önceki aya göre büyüme oranı (%).
3. Yıllık toplamın ilk altı ayı oluşturan oranı (%).
4. En yüksek büyüme oranı hangi aydaydı?

**Amaç:**
`np.cumsum`, yüzde hesaplama ve diziler arası matematiksel işlemleri kavramak.

**Kullanılabilecek teknikler:**
`np.array`, `np.cumsum`, `np.diff`, `np.argmax`

**İpucu:**
Büyüme oranı için `((bu_ay - önceki_ay) / önceki_ay) * 100` formülü kullanılır. `np.diff` ardışık elemanlar arasındaki farkı verir — bu farkı önceki ayın değerine bölmek büyüme oranını verir.

---

### Soru 2.8 — Matris Manipülasyonu

**Problem:**
3×4 boyutunda, değerleri 1'den 12'ye kadar olan bir matris oluşturun. Ardından:
1. Satırları büyüyen toplamlarına göre sıralayın (toplamı küçük olan satır üstte).
2. Her satırı kendi ortalamasına bölerek satır bazlı normalleştirin.
3. Normalleştirilmiş matrisin her satırının ortalamasının 1.0 olduğunu doğrulayın.

**Amaç:**
Satır toplamına göre sıralama, eksen bazlı normalleştirme ve doğrulama mantığını uygulamak.

**Kullanılabilecek teknikler:**
`np.arange`, `reshape`, `np.sum`, `np.argsort`, `np.all`, `np.isclose`, broadcasting

**İpucu:**
`np.argsort` ile sıralama indekslerini elde edip bu indekslerle satırları seçebilirsiniz. Normalleştirmede `keepdims=True` parametresi broadcasting'i kolaylaştırır.

---

### Soru 2.9 — İki Boyutlu Histogram

**Problem:**
Normal dağılımdan gelen 500×2 boyutunda rastgele veri oluşturun:
- X sütunu: ortalama 0, std 1
- Y sütunu: X'in 0.7 katı + gürültü (ortalama 0, std 0.5)

Ardından:
1. `np.histogram2d` ile 20×20 ızgarada 2D histogram hesaplayın.
2. Veri noktalarının en yoğun olduğu ızgara hücresinin koordinatlarını bulun.
3. X ve Y değerleri arasındaki korelasyon katsayısını hesaplayın.

**Amaç:**
`np.histogram2d`, `np.corrcoef` ve 2D veri analizini kavramak.

**Kullanılabilecek teknikler:**
`np.random`, `np.histogram2d`, `np.corrcoef`, `np.unravel_index`, `np.argmax`

**İpucu:**
2D histogramın en büyük değerinin indeksi `np.argmax` ile bulunur; ancak bu düzleştirilmiş indeks döndürür. `np.unravel_index` ile bunu 2D indekse çevirebilirsiniz.

---

### Soru 2.10 — Özyinelemesiz Fibonacci

**Problem:**
NumPy kullanarak ilk 20 Fibonacci sayısını **döngüsüz** hesaplayın. Ardından:
1. Ardışık Fibonacci sayılarının oranını hesaplayın (F[n+1]/F[n]).
2. Bu oranın altın orana (φ ≈ 1.618) yaklaştığını doğrulayın.
3. İlk 20 Fibonacci sayısından **çift sayıları** filtreleyin.

**Amaç:**
Döngü yerine NumPy'ın vektörel yaklaşımını kullanarak sayısal dizileri hesaplamak.

**Kullanılabilecek teknikler:**
`np.zeros`, döngüsüz yaklaşım için matris üssü (`np.linalg.matrix_power`), `np.mod`

**İpucu:**
Fibonacci sayıları klasik döngüyle hesaplanabilir ancak NumPy yaklaşımı için matris kuvvetini kullanabilirsiniz: `[[1,1],[1,0]]^n` matrisi n. Fibonacci sayısını verir. Alternatif olarak küçük bir döngü ile NumPy dizisi doldurmak da kabul edilir.

---

## BLOK 3 — Zor Alıştırmalar ⭐⭐⭐

---

### Soru 3.1 — Görüntü Mozaik Oluşturucu

**Problem:**
128×128 boyutunda, 0-255 arası rastgele `uint8` görüntü oluşturun. Bu görüntüyü:
1. 16×16 piksellik bloklara bölün (8×8 blok ızgarası oluşur).
2. Her bloğun ortalama piksel değerini hesaplayın.
3. Her bloku kendi ortalamasıyla doldurun (pikselleştirme / mosaic efekti).
4. Orijinal ile mozaik versiyonun PSNR değerini hesaplayın: `PSNR = 10 × log10(255² / MSE)`

**Amaç:**
Görüntü blok analizi, reshape ile 4D dönüşüm ve kalite metriği hesaplamayı öğrenmek.

**Kullanılabilecek teknikler:**
`np.random`, `reshape`, `np.mean`, `np.log10`, broadcasting, 4D yeniden biçimlendirme

**İpucu:**
128×128 görüntüyü `(8, 16, 8, 16)` şeklinde yeniden biçimlendirip her bloğun ortalamasını `axis=(1,3)` ile alabilirsiniz. Sonra bu ortalama değerlerini geri yayarak mozaik görüntüyü elde edebilirsiniz.

---

### Soru 3.2 — Monte Carlo ile Alan Hesabı

**Problem:**
Monte Carlo yöntemiyle şu soruların her birini çözün:
1. Birim çemberin alanını tahmin edin (π'yi bulun) — 1 milyon nokta kullanın.
2. `y = x²` eğrisinin altındaki alanı 0-1 arasında hesaplayın (gerçek değer: 1/3).
3. Her deney için tahminin gerçek değere olan hatası ile kullanılan nokta sayısı arasındaki ilişkiyi 10, 100, 1000, 10000, 100000 noktası için tablolayın.

**Amaç:**
Büyük ölçekli vektörel simülasyon ve istatistiksel tahmin hatası analizini kavramak.

**Kullanılabilecek teknikler:**
`np.random`, vektörel koşul değerlendirmesi, `np.abs`, `np.log10`

**İpucu:**
Her simülasyon adımı döngüsüz çalışmalı. N nokta için dizi oluşturup koşulu tüm dizi üzerinde aynı anda değerlendirin. Hata ile nokta sayısı arasında genellikle `O(1/√N)` ilişkisi vardır.

---

### Soru 3.3 — K-Means Benzeri Kümeleme

**Problem:**
2D uzayda 3 farklı kümeye ait toplam 150 nokta oluşturun:
- Küme A: merkez (2, 2), std 0.8
- Küme B: merkez (7, 3), std 1.0
- Küme C: merkez (4, 8), std 0.7

Ardından **yalnızca NumPy ile** basit bir k-means benzeri algoritma uygulayın:
1. Başlangıçta 3 rastgele merkez seçin.
2. Her noktayı en yakın merkeze atayın.
3. Her kümenin yeni merkezini hesaplayın.
4. Adım 2-3'ü yakınsayana kadar (veya 50 iterasyona kadar) tekrarlayın.
5. Son atamaları ve nihai merkezleri yazdırın.

**Amaç:**
Mesafe hesabı, yinelemeli algoritma tasarımı ve NumPy'ın vektörel gücünü bir araya getirmek.

**Kullanılabilecek teknikler:**
`np.random`, broadcasting ile Öklid mesafesi, `np.argmin`, `np.mean`, döngü

**İpucu:**
Bir noktanın üç merkeze olan mesafeleri broadcasting ile tek seferde hesaplanabilir. Her noktanın ataması `np.argmin(mesafeler, axis=1)` ile yapılır.

---

### Soru 3.4 — Zaman Serisi Anomali Tespiti

**Problem:**
365 günlük sıcaklık verisi simüle edin:
- Temel: mevsimsel sinüs dalgası (ortalama 15°C, genlik ±10°C)
- Gürültü: normal dağılım (std=2)
- Anomali: rastgele 8 gün sensör hatası ekleyin (+30°C ile +50°C arası)

Şu adımlarla anomalileri tespit edin:
1. 15 günlük hareketli ortalama ve hareketli standart sapma hesaplayın.
2. Mevcut değerin `ortalama ± 3×std` sınırını aşıp aşmadığına bakın.
3. Tespit edilen anomali günlerini listeleyin.
4. Gerçek anomali günleriyle karşılaştırın: doğruluk, yanlış pozitif ve kaçırılan gün sayısını raporlayın.

**Amaç:**
İstatistiksel anomali tespiti algoritmasını NumPy ile sıfırdan uygulamak.

**Kullanılabilecek teknikler:**
`np.convolve`, `np.sqrt`, Boolean maskeleme, `np.where`, `np.setdiff1d`, `np.intersect1d`

**İpucu:**
Hareketli standart sapma hesaplamak için şu formülü kullanabilirsiniz: `E[X²] - E[X]²`. `np.convolve` hem ortalama hem de E[X²] için uygulanabilir.

---

### Soru 3.5 — Görüntü Histogramı Eşitleme

**Problem:**
Düşük kontrastlı bir görüntü simüle edin: 100×100, değerleri yalnızca 80-180 aralığında olan gri görüntü.

Şu adımlarla histogram eşitleme (histogram equalization) uygulayın:
1. Orijinal histogramı hesaplayın (256 bin).
2. Kümülatif dağılım fonksiyonunu (CDF) hesaplayın.
3. CDF'yi kullanarak her piksel değerini yeni bir değere eşleyin (0-255 aralığına yay).
4. Orijinal ve eşitlenmiş görüntünün standart sapmasını karşılaştırın.
5. Histogram eşitlemeden önce ve sonra değer aralıklarını yazdırın.

**Amaç:**
Histogram hesabı, CDF ve görüntü iyileştirme algoritmasını sıfırdan NumPy ile uygulamak.

**Kullanılabilecek teknikler:**
`np.histogram`, `np.cumsum`, `np.interp`, `np.clip`, `np.round`

**İpucu:**
CDF'nin normalize versiyonu [0,1] aralığındadır. Piksel değerlerini bu CDF üzerinden yeni değerlere `np.interp` ile eşleyebilirsiniz.

---

### Soru 3.6 — Matris Faktörizasyonu ile Boyut İndirgeme

**Problem:**
10×5 boyutunda, iki gizli faktörün kombinasyonu olarak üretilmiş veri matrisi oluşturun:

```python
np.random.seed(42)
W = np.random.rand(10, 2)   # 10 örnek, 2 faktör
H = np.random.rand(2, 5)    # 2 faktör, 5 özellik
X = W @ H + 0.01 * np.random.rand(10, 5)   # gürültülü çarpım
```

Şunları hesaplayın:
1. SVD ayrışımı uygulayın (`np.linalg.svd`) ve tekil değerleri yazdırın.
2. Yalnızca ilk 2 tekil değeri kullanarak **düşük ranklı yaklaşım** hesaplayın.
3. Orijinal ve yaklaşım arasındaki Frobenius normunu (ortalama karesel hata) hesaplayın.
4. İlk 2 temel bileşenin toplam varyansın ne kadarını açıkladığını hesaplayın (%).

**Amaç:**
`np.linalg.svd` ile lineer cebir uygulamasını ve boyut indirgeme mantığını öğrenmek.

**Kullanılabilecek teknikler:**
`np.linalg.svd`, `np.linalg.norm`, matris çarpımı `@`, `np.sum`

**İpucu:**
SVD: `U, S, Vt = np.linalg.svd(X, full_matrices=False)` verir. İlk k bileşeni kullanmak için `U[:, :k] @ np.diag(S[:k]) @ Vt[:k, :]` formülü kullanılır.

---

### Soru 3.7 — Kelime Frekans Matrisi

**Problem:**
Aşağıdaki 5 cümleyi temsil eden sayısal vektörler oluşturun (Bag-of-Words benzeri):

```python
cumle1 = [1, 0, 2, 1, 0, 3, 0, 1]   # kelime frekansları
cumle2 = [0, 2, 1, 0, 3, 0, 1, 2]
cumle3 = [1, 1, 0, 2, 0, 1, 3, 0]
cumle4 = [2, 0, 1, 0, 1, 2, 0, 1]
cumle5 = [0, 3, 0, 1, 2, 0, 1, 1]
```

Bu vektörlerden oluşan matrisle:
1. TF-IDF benzeri ağırlık hesaplayın: her kelimenin ağırlığı = `log(toplam_cümle_sayısı / o_kelimeyi_içeren_cümle_sayısı + 1)`.
2. Her cümleyi L2 normuna bölerek normalleştirin.
3. Tüm cümle çiftleri arasındaki kosinüs benzerliğini hesaplayın (5×5 matris).
4. Birinci cümleye en benzer iki cümleyi bulun.

**Amaç:**
Doğal dil işlemenin temel sayısal kavramlarını NumPy ile uygulamak.

**Kullanılabilecek teknikler:**
`np.log`, `np.linalg.norm`, matris çarpımı `@`, `np.argsort`, broadcasting

**İpucu:**
L2 normalleştirme: her satır kendi normuna bölünür. Kosinüs benzerliği normalleştirilmiş vektörlerin iç çarpımıdır: `normalized @ normalized.T`.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `numpy-teori.md` · `numpy-uygulamalar.md` · `numpy-alistirmalar-cozum.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
