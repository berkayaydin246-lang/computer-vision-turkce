# 📝 Matplotlib — Alıştırmalar

> **Modül:** Alıştırmalar | **Seviye:** Kolay → Orta → Zor
> **İlgili dosyalar:** `matplotlib-teori.md` · `matplotlib-uygulamalar.md` · `matplotlib-alistirmalar-cozum.md`

---

## Nasıl Çalışılır?

- Soruyu okuyun; hangi grafik türünün, hangi biçimlendirmenin ve hangi veri yapısının gerekli olduğunu önce kafanızda planlayın.
- İpucuna yalnızca gerçekten takılırsanız bakın.
- Çözümünüzü tamamladıktan sonra `matplotlib-alistirmalar-cozum.md` ile karşılaştırın.
- Grafikleri `plt.savefig` ile kaydedin ve farklı `figsize` ya da `dpi` değerleriyle oynayın.

```
Zorluk göstergesi:
⭐       — Kolay
⭐⭐     — Orta
⭐⭐⭐   — Zor
```

---

## BLOK 1 — Kolay Alıştırmalar ⭐

---

### Soru 1.1 — İlk Çizgi Grafiği

**Problem:**
`y = x²` ve `y = x³` fonksiyonlarını `-3` ile `3` aralığında aynı grafikte çizin. Şu özellikleri ekleyin:
- Her fonksiyon için farklı renk ve çizgi stili
- Başlık, x ekseni etiketi, y ekseni etiketi
- Legend
- Yatay ve dikey referans çizgileri (`axhline`, `axvline` ile y=0 ve x=0)

**Amaç:**
`plt.plot` temel kullanımını, grafik biçimlendirmesini ve referans çizgilerini öğrenmek.

**Kullanılabilecek teknikler:**
`np.linspace`, `plt.plot`, `ax.set_title`, `ax.legend`, `ax.axhline`, `ax.axvline`

**İpucu:**
Çizgi stili `linestyle='--'` veya kısaca `ls='--'` ile, kalınlık `linewidth` veya `lw` ile belirlenir. x=0 ve y=0 çizgileri grafiğin eksenleri gibi görünür ve okunabilirliği artırır.

---

### Soru 1.2 — Saçılım Diyagramı ve Trend

**Problem:**
50 veri noktası için rastgele `x` ve `y` değerleri oluşturun; `y = 2x + gürültü` ilişkisini simüle edin. Şunları yapın:
1. Veri noktalarını saçılım diyagramı olarak çizin.
2. `np.polyfit` ile doğrusal regresyon çizgisini hesaplayıp üzerine çizin.
3. Korelasyon katsayısını grafiğin köşesine metin olarak ekleyin.

**Amaç:**
`scatter`, doğrusal trend çizgisi ve metin eklemeyi pratikte uygulamak.

**Kullanılabilecek teknikler:**
`plt.scatter`, `np.polyfit`, `np.polyval`, `ax.text`, `np.corrcoef`

**İpucu:**
`ax.text(x, y, metin, transform=ax.transAxes)` ile koordinatları 0-1 aralığında belirterek metin konumunu eksen boyutundan bağımsız ayarlayabilirsiniz.

---

### Soru 1.3 — Basit Çubuk Grafik

**Problem:**
Beş şehrin nüfusunu (kendiniz uydurun veya gerçek veri kullanın) gösteren yatay çubuk grafik çizin. Şunları ekleyin:
- Her çubuğun sonuna nüfus değerini yazdırın.
- Nüfusu en yüksek şehri farklı renkle vurgulayın.
- x eksenini milyon cinsinden (örn: 1.5M) etiketleyin.

**Amaç:**
`barh`, veri etiketleme ve koşullu renk belirlemeyi öğrenmek.

**Kullanılabilecek teknikler:**
`plt.barh`, `ax.bar_label`, liste anlayışı ile renk seçimi

**İpucu:**
Renk listesi oluştururken maksimum değeri bulup ona farklı renk verin. `bar_label` ile otomatik etiket yerleştirme çok daha hızlıdır.

---

### Soru 1.4 — Histogram ve Dağılım

**Problem:**
Normal dağılımdan 1000 veri noktası üretin (ortalama=100, std=15). Bu verinin:
1. Histogramını 25 bin ile çizin.
2. Ortalamasını kırmızı kesik çizgiyle işaretleyin.
3. ±1σ bandını `axvspan` ile gölgeli bölge olarak gösterin.
4. Grafik başlığında ortalama ve standart sapmayı yazın.

**Amaç:**
`plt.hist`, `axvline`, `axvspan` kullanımını ve histogram yorumlamayı anlamak.

**Kullanılabilecek teknikler:**
`plt.hist`, `ax.axvline`, `ax.axvspan`, `np.mean`, `np.std`

**İpucu:**
`axvspan(xmin, xmax, alpha=0.15)` dikey bant gölgelendirmesi yapar. Alpha değeri saydamlığı belirler — çok düşük tutun ki histogramın önüne geçmesin.

---

### Soru 1.5 — Pasta Grafik

**Problem:**
Bir şirketin gelir kaynaklarını pasta grafikle gösterin:
- Online Satış: %40
- Mağaza: %28
- Kurumsal: %20
- Diğer: %12

En büyük dilimi (`explode` ile) biraz öne çıkarın. Yüzdeleri dilimlerin üzerine yazın. `shadow=True` ekleyin.

**Amaç:**
`plt.pie`, `explode` ve `autopct` kullanımını öğrenmek.

**Kullanılabilecek teknikler:**
`plt.pie`, `explode`, `autopct`, `startangle`

**İpucu:**
`explode` her dilim için ne kadar öne çıkarılacağını belirten bir liste alır; öne çıkarılmayanlar için 0, öne çıkarılanlar için küçük bir değer (örn: 0.05) verin.

---

### Soru 1.6 — Zaman Serisi Grafiği

**Problem:**
12 aylık iki farklı ürünün satış verisini oluşturun (rastgele). Bunları aynı grafikte çizin ve şunları ekleyin:
- Her veri noktasına küçük daire işaret (`marker='o'`)
- İki ürünün ortalama satışlarını karşılaştıran yatay kesik çizgi
- Aylık isimler x ekseninde görünsün

**Amaç:**
İşaretli çizgi grafik, eksen etiket özelleştirmesi ve referans çizgisi kombinasyonunu uygulamak.

**Kullanılabilecek teknikler:**
`plt.plot`, `ax.set_xticks`, `ax.set_xticklabels`, `ax.axhline`

**İpucu:**
Ay isimlerini bir listeye alıp `set_xticklabels` ile etiket olarak verin. `rotation=45` ile çakışmayı önleyebilirsiniz.

---

### Soru 1.7 — Çoklu Çizgi Grafik — Sınav Notları

**Problem:**
4 öğrencinin 5 sınavdaki notlarını aynı grafikte çizin. Her öğrenci için farklı renk ve marker kullanın. Grafik, her öğrencinin performans trendini açıkça göstermeli. Sınav ortalamasını arka planda gri kesik çizgi olarak gösterin.

**Amaç:**
Çok serili grafik, legend yönetimi ve arka plan referans çizgisini bir arada kullanmak.

**Kullanılabilecek teknikler:**
`plt.plot`, döngüde grafik çizimi, `ax.legend`, `ax.axhline`

**İpucu:**
Her öğrenci için ayrı `plot` çağrısı yapabilirsiniz ya da bir döngü kullanabilirsiniz. Renk ve marker listelerini önceden tanımlayıp döngü içinde indeksleyerek seçin.

---

### Soru 1.8 — Grafik Kaydetme ve Boyut Ayarı

**Problem:**
Herhangi bir grafik oluşturun (tercihinize bırakılmış) ve şu üç farklı konfigürasyonda kaydedin:
1. 800×600 piksel, DPI=72 → "web_kalitesi.png"
2. 1600×900 piksel, DPI=150 → "iyi_kalite.png"
3. 3200×1800 piksel, DPI=300 → "baski_kalitesi.png"

Her dosyanın byte cinsinden boyutunu Python ile okuyup konsola yazdırın.

**Amaç:**
`figsize`, `dpi` ve `savefig` arasındaki ilişkiyi pratikte görmek.

**Kullanılabilecek teknikler:**
`plt.figure`, `plt.savefig`, `os.path.getsize`

**İpucu:**
`figsize=(genislik_inç, yukseklik_inç)` inç cinsinden, DPI piksel başına nokta sayısıdır. Toplam piksel = figsize × dpi. Dosya boyutu ise içerik ve sıkıştırmaya bağlı değişir.

---

## BLOK 2 — Orta Alıştırmalar ⭐⭐

---

### Soru 2.1 — 2×2 Subplot Paneli

**Problem:**
Aynı figure içinde 2×2 panel oluşturun; her panelde farklı bir veri türü gösterin:
- Sol üst: Sinüs fonksiyonu (0-4π)
- Sağ üst: 200 rastgele noktanın saçılım diyagramı (rengi üçüncü bir değişkeni temsil etsin)
- Sol alt: 5 kategorinin çubuk grafiği
- Sağ alt: Normal dağılım histogramı

Her panelin ayrı başlığı olsun. `plt.tight_layout()` kullanın.

**Amaç:**
`plt.subplots` ile çoklu panel yönetimini ve farklı grafik türlerini bir arada kullanmayı öğrenmek.

**Kullanılabilecek teknikler:**
`plt.subplots`, `axes[0,0].plot`, `axes[0,1].scatter`, `cmap`, `tight_layout`

**İpucu:**
`fig, axes = plt.subplots(2, 2)` ile 2×2 axes nesnesi döner. Her panel `axes[satir, sutun]` ile erişilir. Renk kodlaması için `scatter`'a `c=veri, cmap='viridis'` verin.

---

### Soru 2.2 — Isı Haritası (Korelasyon Matrisi)

**Problem:**
Beş farklı değişkenin 100 gözlemlik veri setini simüle edin. `np.corrcoef` ile korelasyon matrisini hesaplayın ve `plt.imshow` ile ısı haritası olarak gösterin. Şunları ekleyin:
- Her hücreye korelasyon değerini metin olarak yazın.
- `coolwarm` renk haritası kullanın (pozitif korelasyon kırmızı, negatif mavi).
- Eksen etiketlerine değişken adlarını yazın.
- Colorbar ekleyin.

**Amaç:**
`imshow`, colorbar ve üzerine metin yazma tekniklerini öğrenmek.

**Kullanılabilecek teknikler:**
`np.corrcoef`, `plt.imshow`, `plt.colorbar`, `ax.text`, `ax.set_xticks`

**İpucu:**
`ax.text(j, i, f'{deger:.2f}', ha='center', va='center')` ile her hücrenin üzerine değer yazabilirsiniz. Renk kontrastı için arka plan değerine göre metin rengini değiştirin.

---

### Soru 2.3 — Çift Y Eksenli Grafik

**Problem:**
Bir şehrin 12 aylık verisi:
- Aylık yağış miktarı (mm, çubuk grafik)
- Ortalama sıcaklık (°C, çizgi grafik)

Bu iki veriyi aynı panelde `twinx` ile gösterin. Her veri türü için ayrı y ekseni kullanın; sol eksen yağış için mm, sağ eksen sıcaklık için °C göstersin.

**Amaç:**
`ax.twinx()` ile çift eksen kullanımını ve farklı ölçekteki verileri aynı grafikte göstermeyi öğrenmek.

**Kullanılabilecek teknikler:**
`ax.twinx`, `ax.bar`, `ax.plot`, farklı renklerde eksen etiketi

**İpucu:**
Sol ve sağ eksenin y etiketleri karışmasın diye her eksenin rengini grafiğindeki veri rengiyle eşleştirin. `ax2.tick_params(axis='y', labelcolor=...)` ile sağ eksenin rengi ayarlanabilir.

---

### Soru 2.4 — Karşılaştırmalı Histogram

**Problem:**
İki farklı gruptan (A ve B) 500'er veri noktası oluşturun:
- Grup A: ortalama 60, std 12
- Grup B: ortalama 70, std 8

Şunları gösterin:
1. Her iki grubu aynı panelde, farklı renk ve yarı saydam histogramlarla.
2. Her grubun ortalamasını dikey çizgiyle işaretleyin.
3. İkinci panelde her grubun KDE (Kernel Density Estimate) eğrisini çizin (`density=True` ile histogram + polinom fit veya basit görsel).

**Amaç:**
Çakışan histogramlar, `density=True` ve dağılım karşılaştırmasını anlamak.

**Kullanılabilecek teknikler:**
`plt.subplots(1,2)`, `plt.hist` `density=True`, `alpha`, `axvline`

**İpucu:**
`density=True` histogramı alanı 1 olan olasılık yoğunluk grafiğine çevirir. Bu sayede farklı büyüklükteki grupları adil karşılaştırabilirsiniz.

---

### Soru 2.5 — Yığılmış Alan Grafiği

**Problem:**
Bir şirketin 6 yıllık dört bölge satışını yığılmış alan grafiği (`stackplot`) olarak gösterin. Her bölge farklı renkle temsil edilsin. Y ekseninde toplam satışın yıllık değişimi görülsün.

Ardından aynı veriyi `subplot` ile normal çizgi grafiği olarak da gösterin; iki görselleştirmeyi yan yana karşılaştırın.

**Amaç:**
`plt.stackplot` kullanımını ve oran değişimini görselleştirme stratejilerini öğrenmek.

**Kullanılabilecek teknikler:**
`plt.stackplot`, `plt.subplots(1,2)`, `labels`, `legend`

**İpucu:**
`plt.stackplot(x, y1, y2, y3, y4, labels=[...])` ile tüm veri setini tek seferde çizebilirsiniz. Legend için `ax.legend(loc='upper left')` kullanmak sıkça yeterlidir.

---

### Soru 2.6 — Grafik Özelleştirme Ustası

**Problem:**
Herhangi bir veri setiyle çizgi grafik oluşturun ve şu biçimlendirmelerin **tümünü** uygulayın:
1. Özel font boyutu (başlık 14, eksen etiketleri 11, tick etiketleri 9)
2. Arka planı açık gri yapın (`ax.set_facecolor`)
3. Çerçeve (spine) üst ve sağ kenarlarını gizleyin
4. Y ekseninde her sayı değerini gösterin (tüm tick'ler)
5. Grafik alanı dışına legend ekleyin (`bbox_to_anchor`)
6. `plt.savefig` ile `bbox_inches='tight'` kullanarak kaydedin

**Amaç:**
Matplotlib'in detaylı biçimlendirme seçeneklerini tek uygulamada bir araya getirmek.

**Kullanılabilecek teknikler:**
`fontsize`, `set_facecolor`, `spines`, `set_yticks`, `bbox_to_anchor`, `tight_layout`

**İpucu:**
`ax.spines['top'].set_visible(False)` çerçeve kenarını gizler. Legend'ı grafik dışına taşımak için `ax.legend(loc='upper left', bbox_to_anchor=(1, 1))` ve `plt.savefig(..., bbox_inches='tight')` birlikte kullanılır.

---

### Soru 2.7 — Dağılım ve Kutu Grafiği

**Problem:**
Dört gruptan birer grup rastgele veri oluşturun. Şu iki görselleştirmeyi yan yana yapın:
1. Sol: 4 grubun `boxplot`'u (medyan, çeyrekler, aykırılar görünür)
2. Sağ: Aynı 4 grubun violin plot'u

Her iki panelde de gruplar aynı renklerle gösterilsin. Alt kısma yorumlar için bir metin notu ekleyin (örn: "Grup B daha yüksek varyans göstermektedir").

**Amaç:**
`boxplot` ve `violinplot`'ı karşılaştırarak ne zaman hangisinin tercih edileceğini anlamak.

**Kullanılabilecek teknikler:**
`ax.boxplot`, `ax.violinplot`, `patch_artist=True`, renkli yamalar

**İpucu:**
`violin`'de `vp['bodies']` ile her gövdenin rengini değiştirebilirsiniz. `boxplot`'ta `patch_artist=True` ile kutular doldurulabilir; ardından `bp['boxes']` listesi üzerinden renkleri atayın.

---

### Soru 2.8 — Animasyonlu Veri Keşfi (Statik Sürüm)

**Problem:**
Gerçek zamanlı animasyon yerine, aynı grafiği birden fazla zaman noktasını gösterecek şekilde üst üste çizin. Bir sinüs dalgasının 5 farklı faz değerini (`phase = 0, π/4, π/2, 3π/4, π`) tek figure'da, her faz için ayrı renk ve etiket olacak şekilde çizin. Sonra bu grafiği gerçekten animasyona dönüştürmek isteseydiniz hangi Matplotlib modülünü kullanmanız gerekirdi? Cevabı yorumda yazın.

**Amaç:**
Çok serili faz grafiği çizmeyi ve Matplotlib animasyon altyapısını keşfetmeyi teşvik etmek.

**Kullanılabilecek teknikler:**
`plt.plot` (döngüde), `np.linspace`, `np.pi`, `cmap`'ten renk üretme

**İpucu:**
`plt.get_cmap('plasma')(i / n)` renk haritasından eşit aralıklı renkler üretir — her faz için farklı renk elde etmek için döngü içinde `i / (n-1)` değeri kullanılabilir.

---

### Soru 2.9 — Görüntü Kanalı Görselleştirme

**Problem:**
NumPy ile bir renkli görüntü oluşturun (veya `cv2.imread` ile yükleyin). Şu 6 paneli tek figure'da gösterin:
1. Orijinal RGB
2. Gri tonlama
3. R kanalı (kırmızı renk haritasıyla)
4. G kanalı (yeşil renk haritasıyla)
5. B kanalı (mavi renk haritasıyla)
6. Sadece kırmızı kanalla oluşturulmuş görüntü (G ve B sıfır)

Tüm panellerde eksenleri gizleyin (`axis('off')`).

**Amaç:**
`imshow` ile renk haritası kullanımını ve görüntü kanallarını görselleştirmeyi pekiştirmek.

**Kullanılabilecek teknikler:**
`plt.imshow`, `cmap='Reds'/'Greens'/'Blues'`, `axis('off')`, `np.zeros_like`

**İpucu:**
"Sadece kırmızı kanaldan oluşan görüntü" için G ve B kanallarını sıfırlamak üzere `np.zeros_like` ile oluşturulmuş bir dizi kullanıp orijinal R kanalını 0. konuma koyun.

---

### Soru 2.10 — Mini Veri Analizi Paneli

**Problem:**
100 gözlemlük iki değişken oluşturun: `gelir` (4000-15000 arası rastgele) ve `harcama` (gelirin %50-%80'i + gürültü). Tek figure'da 3 panel oluşturun:
1. `gelir` histogramı
2. `harcama` histogramı
3. `gelir` vs `harcama` scatter (regresyon çizgisi dahil)

Üç paneli `plt.subplots(1, 3)` ile yan yana düzenleyin ve `fig.suptitle` ile genel başlık ekleyin.

**Amaç:**
`subplots(1,3)` düzenini, `suptitle` ve birbiriyle ilişkili grafikleri bir arada sunmayı öğrenmek.

**Kullanılabilecek teknikler:**
`plt.subplots(1,3)`, `fig.suptitle`, `plt.hist`, `plt.scatter`, `np.polyfit`

**İpucu:**
`tight_layout(rect=[0, 0, 1, 0.95])` ile `suptitle` ve alt paneller arasında boşluk bırakabilirsiniz.

---

## BLOK 3 — Zor Alıştırmalar ⭐⭐⭐

---

### Soru 3.1 — Tam Veri Analizi Panosu

**Problem:**
`gridspec.GridSpec` kullanarak şu yerleşimi oluşturun:
- Üst satır (tam genişlik): Zaman serisi + hareketli ortalama
- Alt sol: Dağılım histogramı
- Alt orta: Kategorik kutu grafik (3-4 grup)
- Alt sağ: Metin tabanlı özet metrikler (eksen yok, yalnızca `ax.text`)

Veriyi kendiniz oluşturun. Renk paleti tutarlı olsun (aynı veri hep aynı renkte).

**Amaç:**
`GridSpec` ile özel panel yerleşimi ve birden fazla görselleştirmeyi bir analiz panosunda birleştirmek.

**Kullanılabilecek teknikler:**
`gridspec.GridSpec`, `fig.add_subplot(gs[...])`, `ax.twinx`, metin tabanlı panel

**İpucu:**
`gs[0, :]` ilk satırın tüm sütunlarını kapsar. `gs[1, 0]` alt sol köşedir. Metin paneli için `ax.axis('off')` ile eksenleri gizleyin ve metinleri `ax.text(x, y, ...)` ile konumlandırın.

---

### Soru 3.2 — Görüntü Karşılaştırma Aracı

**Problem:**
Bir orijinal görüntü ve onun Gaussian bulanıklaştırılmış versiyonunu yan yana gösteren bir araç oluşturun. Şunları içersin:
1. Üst satır: Orijinal ve bulanık görüntü (her ikisinin başlığında ortalama ve std değerleri olsun)
2. Alt sol: İki görüntünün mutlak fark haritası (`hot` cmap)
3. Alt orta: Her iki görüntünün gri histogram karşılaştırması (aynı eksende)
4. Alt sağ: MSE ve PSNR değerleri metin tablosunda

**Amaç:**
Görüntü işleme sonuçlarını Matplotlib ile kapsamlı biçimde raporlamayı öğrenmek.

**Kullanılabilecek teknikler:**
`plt.imshow`, `cmap='hot'`, `plt.hist`, `np.abs`, `ax.axis('off')` (metin için), `gridspec`

**İpucu:**
`np.abs(img1.astype(int) - img2.astype(int)).astype(np.uint8)` ile uint8 taşması olmadan fark hesaplanır. PSNR = `10 * np.log10(255² / MSE)` formülüyle hesaplanır.

---

### Soru 3.3 — Çok Boyutlu Veri Görselleştirmesi

**Problem:**
100 örnek, 4 özellik içeren bir veri seti oluşturun. Şu dört görselleştirmeyi tek figure'da yapın:
1. **Scatter matrix (pairs plot)**: 4×4 ızgara; köşegen panellerde histogram, köşegen dışı panellerde scatter. (GridSpec veya `fig.add_subplot` döngüsüyle)
2. Sağ üst köşeye `np.corrcoef` matrisinin ısı haritasını ekleyin (ayrı panel).

**Amaç:**
Çok değişkenli veri keşfini görselleştirme tekniklerini uygulamak.

**Kullanılabilecek teknikler:**
`gridspec`, iç içe döngüler, `plt.scatter`, `plt.hist`, `plt.imshow` (korelasyon için)

**İpucu:**
Scatter matrix için `for i in range(4): for j in range(4):` döngüsü her paneli doldurur. `i == j` ise histogram, değilse scatter çizin. `GridSpec(4, 5)` veya benzeri bir yerleşimle sağ tarafa korelasyon paneli sığdırılabilir.

---

### Soru 3.4 — Etkileşimli Grafik Temel Taşı

**Problem:**
`plt.ginput` veya `mpl_connect` kullanarak şu interaktif gösterimi oluşturun: Kullanıcı fareyle tıkladığı noktalara daire çizer ve her tıklama sonrasında eklenilen noktaların koordinatları konsola yazdırılır. İptal etmek için 'q' tuşuna basılır.

Alternatif (eğer interaktif ortam yoksa): Önceden tanımlanmış 10 noktayı animasyon yerine her birini farklı renk ve büyüklükte göstererek "tıklama sırası" simülasyonu yapın — noktanın numarasını üzerine yazın.

**Amaç:**
Matplotlib olay modeli ve interaktif özelliklerini tanımak; dinamik veri ekleme mantığını kavramak.

**Kullanılabilecek teknikler:**
`plt.ginput`, `ax.scatter`, `ax.annotate`, döngüsel renk kullanımı

**İpucu:**
`plt.ginput(n=-1, timeout=0)` sonraki tıklamayı bekler; tüm tıklamaları liste olarak döndürür. Alternatif çözümde `fig.canvas.mpl_connect('button_press_event', callback)` ile olay dinleyicisi eklenebilir.

---

### Soru 3.5 — Veri Hikâyesi Görseli

**Problem:**
Bir şirketin ürün lansmanı sonrası performansını anlatan, **hikâye formatında** bir görsel oluşturun:
- 18 aylık satış verisi (lansman öncesi: düşük+sabit, lansman sonrası: hızlı büyüme simüle edin)
- Lansman noktasına dikey çizgi + açıklayıcı ok ve metin
- Büyüme dönemini `fill_between` ile vurgulayın
- Hedef satış çizgisi (yatay kesik çizgi)
- Sol üstte şirket logosu yerine renkli bir metin kutusu
- Grafik başlığı, kaynak notu ve tarih

**Amaç:**
Matplotlib'in sunumsal ve anlatımsal görsel oluşturma yeteneklerini bir arada kullanmak.

**Kullanılabilecek teknikler:**
`ax.annotate`, `ax.fill_between`, `ax.axvline`, `ax.axhline`, `ax.text`, `bbox` parametresi

**İpucu:**
`ax.annotate(text, xy=okun_ucu, xytext=metnin_konumu, arrowprops=dict(...))` hem ok hem metin ekler. `ax.text(x, y, metin, bbox=dict(facecolor='yellow', alpha=0.8))` arka planlı metin kutusu oluşturur.

---

### Soru 3.6 — Monte Carlo Görselleştirmesi

**Problem:**
NumPy alıştırmasındaki Monte Carlo pi tahmini sonuçlarını görsel olarak anlatın:

1. **Panel 1**: 1000 noktalık simülasyonu gösterin — çember içindeki noktaları mavi, dışındakileri kırmızı renkte scatter ile çizin. Birim çemberi üzerine çizin.
2. **Panel 2**: N noktası arttıkça pi tahmininin gerçek π değerine nasıl yaklaştığını gösterin (N = [10, 50, 100, 500, 1000, 5000, 10000]).
3. **Panel 3**: Tahmin hatası (|tahmin - π|) ile N arasındaki ilişkiyi **log-log ölçekte** çizin; teorik `O(1/√N)` eğrisini de üzerine koyun.

**Amaç:**
Sayısal simülasyon sonuçlarını üç farklı boyutuyla görselleştirmek ve log-log ölçek kullanımını öğrenmek.

**Kullanılabilecek teknikler:**
`plt.scatter`, `plt.Circle`, `ax.set_aspect('equal')`, `ax.set_xscale('log')`, `ax.set_yscale('log')`

**İpucu:**
Bir daire çizmek için `plt.Circle((0,0), 1, fill=False)` oluşturup `ax.add_patch(cember)` ile ekleyebilirsiniz. Log-log grafik için `ax.set_xscale('log')` ve `ax.set_yscale('log')` kullanılır; `ax.loglog` kısaltması da mevcuttur.

---

### Soru 3.7 — Gelişmiş Isı Haritası Analizi

**Problem:**
365 günlük (52 hafta × 7 gün) rastgele sıcaklık verisi oluşturun (mevsimsel desen + gürültü). Bu veriyi GitHub katkı grafiğine benzer şekilde görselleştirin:

1. 52 sütun × 7 satır ısı haritası (`imshow`) — x ekseni haftalar, y ekseni gün isimleri
2. Renk haritasını düzgün seçin (soğuk-sıcak geçişi için uygun)
3. Her aya denk gelen hafta başlarında x ekseni etiketlerini ay isimleri olarak gösterin
4. Colorbar ekleyin ve etiketleyin
5. En sıcak ve en soğuk haftayı dikdörtgen ile işaretleyin (`add_patch`)

**Amaç:**
`imshow` ile takvim tarzı ısı haritası, özel tick etiketleri ve patch eklemeyi öğrenmek.

**Kullanılabilecek teknikler:**
`plt.imshow`, `ax.set_yticks`, `ax.set_xticks`, `matplotlib.patches.Rectangle`, `ax.add_patch`

**İpucu:**
`imshow`'da `aspect='auto'` ile hücreleri kare yerine dikdörtgen yapabilirsiniz. `Rectangle((x-0.5, y-0.5), genislik, yukseklik, fill=False, edgecolor='red', lw=2)` dikdörtgen çizer; konum ±0.5 ofsetle hücre merkezine hizalanır.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `matplotlib-teori.md` · `matplotlib-uygulamalar.md` · `matplotlib-alistirmalar-cozum.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
