# 🛠️ NumPy — Uygulamalar

> **Modül:** Uygulamalar | **Seviye:** Başlangıç → Orta
> **İlgili dosyalar:** `numpy-teori.md` · `numpy-alistirmalar.md`

---

## BLOK 0 — Uygulama Belgesi Hakkında

Bu belge, `numpy-teori.md` dosyasında öğrendiğiniz kavramları **gerçek senaryolarda** nasıl kullanacağınızı gösterir. Her uygulama belirli bir problemi çözer; kod sade, yorumlu ve çalıştırılabilir biçimde sunulmuştur.

**Belgede neler var:**

| Blok | Konu |
|------|------|
| Blok 1 | Temel dizi işlemleri |
| Blok 2 | Veri manipülasyonu |
| Blok 3 | Matematiksel ve istatistiksel hesaplamalar |
| Blok 4 | Gerçek dünya senaryoları |
| Blok 5 | Mini projeler |

**Kodları çalıştırmak için:**
```bash
pip install numpy matplotlib
python uygulama.py
```

---

## BLOK 1 — Temel Dizi İşlemleri

---

### Uygulama 1.1 — Dizi Oluşturma ve Temel Nitelikler

**Problem:**
Farklı yöntemlerle NumPy dizisi oluşturmak ve her dizinin temel niteliklerini (shape, dtype, boyut) raporlamak.

**Amaç:**
`ndarray` veri yapısını tanımak; `shape`, `dtype`, `ndim`, `size` gibi nitelikleri kullanmayı öğrenmek.

**Kullanılan teknikler:**
`np.array`, `np.zeros`, `np.ones`, `np.arange`, `np.linspace`, `np.eye`

**Kod:**
```python
import numpy as np

def dizi_bilgisi(dizi, isim):
    """Bir dizinin temel niteliklerini yazdırır."""
    print(f"\n{'─'*45}")
    print(f"  {isim}")
    print(f"{'─'*45}")
    print(f"  Değer    : {dizi}")
    print(f"  Shape    : {dizi.shape}")
    print(f"  Dtype    : {dizi.dtype}")
    print(f"  Ndim     : {dizi.ndim}  (boyut sayısı)")
    print(f"  Size     : {dizi.size}  (toplam eleman)")
    print(f"  Nbytes   : {dizi.nbytes} byte")

# 1. Python listesinden dizi
a = np.array([3, 1, 4, 1, 5, 9, 2, 6])
dizi_bilgisi(a, "1D Dizi (listeden)")

# 2. İç içe listeden 2D dizi
b = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]], dtype=np.float32)
dizi_bilgisi(b, "2D Dizi (matris) — float32")

# 3. Sıfır ve bir dizileri
s = np.zeros((4, 5))
dizi_bilgisi(s, "Sıfır Matrisi (4×5)")

# 4. Belirli değerle dolu dizi
d = np.full((3, 3), fill_value=7, dtype=np.int32)
dizi_bilgisi(d, "Sabit Değerli Matris (7 ile dolu)")

# 5. Aralık dizisi
r = np.arange(0, 20, 3)    # 0'dan 20'ye kadar, adım 3
dizi_bilgisi(r, "Arange (0→20, adım=3)")

# 6. Eşit aralıklı nokta dizisi
l = np.linspace(0, 1, 6)   # 0 ile 1 arasında 6 nokta
dizi_bilgisi(l, "Linspace (0→1, 6 nokta)")

# 7. Birim matris
eye = np.eye(4, dtype=np.int32)
dizi_bilgisi(eye, "Birim Matris (4×4)")
```

**Açıklama:**
`dizi_bilgisi` fonksiyonu her dizin için 5 temel niteliği bir arada gösterir. `shape` her boyuttaki eleman sayısını tuple olarak verir; `dtype` bellekte nasıl saklandığını belirtir. `float32` ile `float64` arasındaki fark bellek tüketiminde 2 kat farka karşılık gelir.

**Beklenen çıktı:**
Her dizi için ayrı bir bilgi bloğu. `birim matris` köşegeninde 1, diğer konumlarda 0 içerdiği görülür.

**İyileştirme fikirleri:**
- `np.random.randint` ile rastgele dizi oluşturup aynı raporlama fonksiyonuyla inceleyin.
- `dtype=np.complex64` deneyerek karmaşık sayı dizisi oluşturun.

---

### Uygulama 1.2 — İndeksleme, Dilimleme ve Boolean Maskeleme

**Problem:**
Bir veri dizisinden belirli elemanları, satırları ve koşula uyan değerleri çıkarmak.

**Amaç:**
NumPy'ın güçlü indeksleme mekanizmalarını (dilim, boolean maske, fancy index) pratik senaryolarda kullanmayı öğrenmek.

**Kullanılan teknikler:**
Temel dilimleme, boolean indeksleme, fancy indeksleme, `np.where`

**Pipeline:**
```
Ham dizi → koşul maskesi → filtrelenmiş veri → işlem → sonuç
```

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(seed=0)

# Örnek: 20 öğrencinin 5 sınavdaki notları (0-100 arası)
notlar = rng.integers(40, 101, size=(20, 5))

print("Not Matrisi (20 öğrenci × 5 sınav):")
print(notlar)

# --- Temel Dilimleme ---
print("\n[Dilimleme]")
print(f"  İlk 3 öğrenci, ilk 2 sınav:\n{notlar[:3, :2]}")
print(f"  Son öğrencinin tüm notları: {notlar[-1, :]}")
print(f"  3. sınavın tüm sonuçları  : {notlar[:, 2]}")

# --- Her öğrencinin ortalaması ---
ogrenci_ort = notlar.mean(axis=1)  # her satırın ortalaması
print(f"\n[Öğrenci Ortalamaları]\n{ogrenci_ort.round(1)}")

# --- Boolean Maskeleme ---
gec_esigi = 70
gecenler_mask = ogrenci_ort >= gec_esigi

print(f"\n[Boolean Maskeleme] Geçenler (ort ≥ {gec_esigi}):")
print(f"  Geçen öğrenci sayısı: {gecenler_mask.sum()}")
print(f"  Geçenlerin ortalamaları: {ogrenci_ort[gecenler_mask].round(1)}")

# --- np.where: koşullu değer atama ---
harf_notu = np.where(ogrenci_ort >= 85, "A",
            np.where(ogrenci_ort >= 70, "B",
            np.where(ogrenci_ort >= 55, "C", "F")))
print(f"\n[Harf Notları]: {harf_notu}")

# --- Fancy İndeksleme ---
secilen_ogrenciler = [0, 4, 9, 14, 19]   # belirli öğrenciler
print(f"\n[Seçilen 5 öğrencinin ortalamaları]:")
for idx in secilen_ogrenciler:
    print(f"  Öğrenci {idx+1:2d}: {ogrenci_ort[idx]:.1f}  [{harf_notu[idx]}]")

# --- 100 alan öğrenciler ---
mukemmel = np.where(notlar == 100)
print(f"\n[100 alan sınav sayısı]: {len(mukemmel[0])}")
```

**Açıklama:**
`axis=1` ile `mean` her satırın (öğrencinin) ortalamasını hesaplar. Boolean maske `ogrenci_ort >= gec_esigi` ifadesi True/False dizisi üretir; bu maske dizi indeksleyicisi olarak kullanılarak yalnızca koşul sağlayanlar seçilir. `np.where` üç argümanla koşullu değer ataması yapar.

**Beklenen çıktı:**
Not matrisinin alt dilimleri, geçen öğrenci sayısı ve harf notu dağılımı. Rastgele üretildiği için her çalıştırmada farklı sonuç çıkar; `seed=0` ile tekrarlanabilirlik sağlanır.

**İyileştirme fikirleri:**
- En yüksek ortalamaya sahip 3 öğrenciyi `np.argsort` ile bulun.
- Her sınavın standart sapmasını hesaplayarak en tutarsız sınavı belirleyin.

---

### Uygulama 1.3 — Yeniden Biçimlendirme ve Birleştirme

**Problem:**
Farklı şekillerdeki dizileri reshape ile dönüştürmek ve birden fazla diziyi birleştirmek.

**Amaç:**
`reshape`, `flatten`, `hstack`, `vstack`, `concatenate` fonksiyonlarını gerçek senaryolarda kullanmak.

**Kullanılan teknikler:**
`reshape`, `flatten`, `ravel`, `hstack`, `vstack`, `np.stack`

**Kod:**
```python
import numpy as np

# --- reshape ---
print("=== RESHAPE ===")
a = np.arange(24)
print(f"Orijinal (1D, 24 eleman): {a}")

b = a.reshape(4, 6)     # 4×6 matris
c = a.reshape(2, 3, 4)  # 2×3×4 küp (3D)
d = a.reshape(-1, 8)    # -1 → NumPy hesaplar (3×8)

print(f"\n4×6 matris:\n{b}")
print(f"\n2×3×4 küp (shape): {c.shape}")
print(f"\nOtomatik boyut (-1,8): shape={d.shape}")

# --- flatten ve ravel ---
print("\n=== FLATTEN vs RAVEL ===")
kopya = b.flatten()    # her zaman kopya
gorunum = b.ravel()    # mümkünse view

kopya[0] = 999
gorunum[0] = 999
print(f"flatten kopya mu? {not np.shares_memory(b, kopya)}")   # True
print(f"ravel view mu?    {np.shares_memory(b, gorunum)}")     # True (çoğunlukla)

# --- Birleştirme ---
print("\n=== BİRLEŞTİRME ===")
X = np.array([[1, 2, 3],
              [4, 5, 6]])
Y = np.array([[7,  8,  9],
              [10, 11, 12]])

yatay = np.hstack([X, Y])     # (2,3) + (2,3) → (2,6)
dikey = np.vstack([X, Y])     # (2,3) + (2,3) → (4,3)

print(f"hstack (yatay): shape={yatay.shape}\n{yatay}")
print(f"\nvstack (dikey): shape={dikey.shape}\n{dikey}")

# Gerçek senaryo: veri kanallarını birleştirme
kirmizi = np.random.randint(0, 256, (5, 5), dtype=np.uint8)
yesil   = np.random.randint(0, 256, (5, 5), dtype=np.uint8)
mavi    = np.random.randint(0, 256, (5, 5), dtype=np.uint8)

# Her kanalı 3D'ye genişlet, sonra birleştir
rgb = np.stack([kirmizi, yesil, mavi], axis=2)   # (5,5,3) — RGB görüntü gibi
print(f"\nRGB benzeri dizi: {rgb.shape}  (H × W × C)")
print(f"Kırmızı kanalı geri al: {rgb[:, :, 0].shape}")
```

**Açıklama:**
`reshape(-1, 8)` sözdizimi NumPy'a "sütun sayısını 8 yap, satır sayısını kendin hesapla" der. `flatten` her zaman bağımsız kopya döndürürken `ravel` mümkün olduğunda view döndürür; bellek kullanımı açısından `ravel` daha verimlidir. `np.stack` yeni bir eksen ekleyerek dizileri üst üste katlar — renk kanallarını birleştirmek için ideal yapıdır.

**Beklenen çıktı:**
Farklı shape değerleriyle yeniden biçimlendirilmiş matrisler ve birleştirilmiş diziler.

**İyileştirme fikirleri:**
- `np.split` ile bir diziyi parçalara ayırın.
- `np.transpose(rgb, (2, 0, 1))` ile PyTorch formatına `(C, H, W)` dönüştürün.

---

## BLOK 2 — Veri Manipülasyonu

---

### Uygulama 2.1 — Sıralama, Filtreleme ve Arama

**Problem:**
Büyük bir veri kümesini sıralamak, belirli koşullara uyan değerleri filtrelemek ve arama yapmak.

**Amaç:**
`np.sort`, `np.argsort`, `np.searchsorted`, `np.clip`, `np.unique` fonksiyonlarını kullanmak.

**Kullanılan teknikler:**
`np.sort`, `np.argsort`, `np.clip`, `np.unique`, `np.searchsorted`

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(42)

# Örnek: 1000 ürünün satış verisi
satis = rng.integers(10, 500, size=1000)
urun_id = np.arange(1, 1001)

print("=== SIRALAMA ===")
# Küçükten büyüğe sıralı kopya
sirali = np.sort(satis)
print(f"En düşük 5 satış : {sirali[:5]}")
print(f"En yüksek 5 satış: {sirali[-5:]}")

# Sıralama indekslerini al (orijinal diziyi bozmaz)
indeksler = np.argsort(satis)
print(f"\nEn çok satan 3 ürün:")
for yer, idx in enumerate(indeksler[-3:][::-1], start=1):
    print(f"  {yer}. sıra → Ürün {urun_id[idx]:4d}, Satış: {satis[idx]}")

print("\n=== FİLTRELEME ===")
# Belirli aralıkta satış yapan ürünler
alt, ust = 200, 300
aralik_mask = (satis >= alt) & (satis <= ust)
print(f"{alt}–{ust} arası satışlı ürün sayısı: {aralik_mask.sum()}")
print(f"Bu ürünlerin ortalama satışı: {satis[aralik_mask].mean():.1f}")

# Değer kırpma — aykırı değerleri sınırla
kirmizi_esik = 450
kirpilmis = np.clip(satis, 0, kirmizi_esik)
print(f"\nKırpma öncesi max: {satis.max()}")
print(f"Kırpma sonrası max: {kirpilmis.max()}")

print("\n=== TEKSİZ DEĞERLER ===")
kucuk = rng.integers(1, 8, size=20)
tekil, sayim = np.unique(kucuk, return_counts=True)
print(f"Dizi: {kucuk}")
for val, cnt in zip(tekil, sayim):
    print(f"  Değer {val}: {cnt} kez {'█' * cnt}")

print("\n=== İKİLİ ARAMA ===")
# Sıralı dizide eleman arama (O(log n))
sirali_kucuk = np.sort(kucuk)
aranan = 4
konum = np.searchsorted(sirali_kucuk, aranan)
print(f"Sıralı dizi: {sirali_kucuk}")
print(f"{aranan} değeri {konum}. konumdan itibaren eklenebilir")
```

**Açıklama:**
`np.argsort` değerler yerine sıralama indekslerini döndürür; bu sayede orijinal veriyle ilişkilendirme yapılabilir (ürün ID'si ile satış sayısı gibi). `np.clip` veriyi belirli bir aralıkta tutar; aykırı değerlerin model eğitimini bozmasını önlemek için sıkça kullanılır. `np.unique` ile `return_counts=True` kombinasyonu veri içindeki frekans analizini tek satırda sağlar.

**Beklenen çıktı:**
En çok satan ürünler, belirli satış aralığındaki ürün sayısı, kırpma öncesi/sonrası maksimum değer ve değer frekans analizi.

**İyileştirme fikirleri:**
- İki farklı ay verisini karşılaştırıp her ürünün satış değişimini hesaplayın.
- `np.percentile` ile 25., 50., 75. yüzdelikleri hesaplayın.

---

### Uygulama 2.2 — Broadcasting ile Vektörel Hesaplama

**Problem:**
Farklı şekillerdeki dizilerle döngüsüz hesaplama yapmak; vektörleştirmenin hız avantajını ölçmek.

**Amaç:**
Broadcasting kurallarını anlamak ve Python döngüsü yerine NumPy vektörel işlemlerini kullanmayı öğrenmek.

**Kullanılan teknikler:**
Broadcasting, `%timeit` eşdeğeri (`time.perf_counter`), vektörel operasyonlar

**Kod:**
```python
import numpy as np
import time

# --- Broadcasting: Çarpım Tablosu ---
print("=== BROADCASTING — Çarpım Tablosu ===")
satir = np.arange(1, 6).reshape(5, 1)  # shape (5,1) — sütun vektörü
sutun = np.arange(1, 6).reshape(1, 5)  # shape (1,5) — satır vektörü

tablo = satir * sutun    # broadcasting: (5,1) × (1,5) → (5,5)
print("1-5 arası çarpım tablosu:")
print(tablo)

# --- Broadcasting: Her Satırı Normalize Etme ---
print("\n=== SATIR BAZLI NORMALİZASYON ===")
veri = np.array([[10.0, 20.0, 30.0],
                 [ 5.0, 50.0, 25.0],
                 [ 3.0,  3.0, 99.0]])

# Her satırın min ve max değeri
satir_min = veri.min(axis=1, keepdims=True)  # shape (3,1)
satir_max = veri.max(axis=1, keepdims=True)  # shape (3,1)

# Broadcasting: her satır kendi min/max'ına göre normalize olur
normalize = (veri - satir_min) / (satir_max - satir_min)
print(f"Orijinal:\n{veri}")
print(f"\nNormalize (0-1):\n{normalize.round(3)}")

# --- Hız Karşılaştırması ---
print("\n=== HIZ KARŞILAŞTIRMASI ===")
N = 1_000_000
a = np.random.rand(N)
b = np.random.rand(N)

# Python döngüsü
t0 = time.perf_counter()
sonuc_dongu = [a[i] * 2 + b[i] for i in range(N)]
t1 = time.perf_counter()
sure_dongu = t1 - t0

# NumPy vektörel
t0 = time.perf_counter()
sonuc_numpy = a * 2 + b
t1 = time.perf_counter()
sure_numpy = t1 - t0

print(f"Python döngüsü : {sure_dongu:.4f} saniye")
print(f"NumPy vektörel : {sure_numpy:.6f} saniye")
print(f"Hız farkı      : {sure_dongu / sure_numpy:.0f}× daha hızlı")

# --- Mesafe Matrisi ---
print("\n=== MESAFE MATRİSİ ===")
# 5 şehrin koordinatları (enlem, boylam)
sehirler = np.array([[41.0, 29.0],   # İstanbul
                     [39.9, 32.9],   # Ankara
                     [38.4, 27.1],   # İzmir
                     [37.0, 35.3],   # Adana
                     [36.9, 30.7]])  # Antalya

isimler = ["İstanbul", "Ankara", "İzmir", "Adana", "Antalya"]

# Broadcasting ile tüm çiftler arası fark
fark = sehirler[:, np.newaxis, :] - sehirler[np.newaxis, :, :]  # (5,5,2)
mesafe = np.sqrt((fark**2).sum(axis=2))   # (5,5) mesafe matrisi (yaklaşık)

print("Şehirler arası tahmini mesafe matrisi (derece cinsinden):")
print(f"{'':>12}", end="")
for s in isimler:
    print(f"  {s:>10}", end="")
print()
for i, s1 in enumerate(isimler):
    print(f"{s1:>12}", end="")
    for j in range(len(isimler)):
        print(f"  {mesafe[i,j]:>10.2f}", end="")
    print()
```

**Açıklama:**
Broadcasting satır ve sütun vektörlerini otomatik olarak eşleştirerek 25 elemanlı çarpım tablosunu **tek bir çarpma işlemi** ile üretir. `keepdims=True` boyutun korunmasını sağlar; bu, `(3,1)` shape ile `(3,3)` dizinin broadcasting ile etkileşmesine olanak tanır. Mesafe matrisi hesabında `[:, np.newaxis, :]` yeni eksen ekleyerek tüm çift kombinasyonlarını döngüsüz hesaplar.

**Beklenen çıktı:**
Çarpım tablosu, satır bazlı normalize edilmiş veri ve döngü vs. NumPy hız karşılaştırması. NumPy genellikle 50-200× daha hızlı çıkar.

**İyileştirme fikirleri:**
- Mesafe hesabını Haversine formülü ile gerçek küresel mesafeye dönüştürün.
- `N=10_000_000` ile hız testini yeniden deneyin.

---

### Uygulama 2.3 — Eksik Veri ve Aykırı Değer İşleme

**Problem:**
Gerçek dünya verilerinde sıkça karşılaşılan eksik (NaN) ve aykırı değerleri NumPy ile tespit etmek ve düzeltmek.

**Amaç:**
`np.nan`, `np.isnan`, `np.nanmean` gibi NaN-güvenli fonksiyonları kullanmayı ve aykırı değer tespiti yapmayı öğrenmek.

**Kullanılan teknikler:**
`np.nan`, `np.isnan`, `np.nanmean`, `np.nanstd`, `np.percentile`

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(7)

# Sensörden gelen sıcaklık verisi — bazı ölçümler eksik
sicaklik = rng.normal(loc=22.0, scale=3.0, size=200).astype(np.float64)

# Rastgele %8 veriyi NaN yap (eksik ölçüm)
eksik_idx = rng.choice(200, size=16, replace=False)
sicaklik[eksik_idx] = np.nan

# Bazı aykırı değerler ekle (sensör hatası)
sicaklik[rng.choice(200, size=4, replace=False)] = rng.uniform(50, 70, 4)

print("=== EKSİK VERİ ANALİZİ ===")
eksik_sayi = np.isnan(sicaklik).sum()
print(f"Toplam kayıt    : {len(sicaklik)}")
print(f"Eksik kayıt     : {eksik_sayi}")
print(f"Eksik oran      : %{eksik_sayi/len(sicaklik)*100:.1f}")

print("\n=== NAN-GÜVENLİ İSTATİSTİKLER ===")
print(f"Ortalama (NaN yoksayarak): {np.nanmean(sicaklik):.2f}°C")
print(f"Medyan   (NaN yoksayarak): {np.nanmedian(sicaklik):.2f}°C")
print(f"Std sap. (NaN yoksayarak): {np.nanstd(sicaklik):.2f}")
print(f"Min/Max (NaN yoksayarak) : {np.nanmin(sicaklik):.1f} / {np.nanmax(sicaklik):.1f}")

print("\n=== AYKIRI DEĞER TESPİTİ (IQR Yöntemi) ===")
temiz = sicaklik[~np.isnan(sicaklik)]   # NaN'ları çıkar

q1 = np.percentile(temiz, 25)
q3 = np.percentile(temiz, 75)
iqr = q3 - q1
alt_sinir = q1 - 1.5 * iqr
ust_sinir = q3 + 1.5 * iqr

aykiri_mask = (temiz < alt_sinir) | (temiz > ust_sinir)
print(f"Q1={q1:.2f}, Q3={q3:.2f}, IQR={iqr:.2f}")
print(f"Alt sınır: {alt_sinir:.2f}, Üst sınır: {ust_sinir:.2f}")
print(f"Aykırı değer sayısı: {aykiri_mask.sum()}")
print(f"Aykırı değerler: {temiz[aykiri_mask].round(1)}")

print("\n=== VERİ TEMİZLEME ===")
# Yöntem 1: Aykırıları medyanla doldur
medyan = np.nanmedian(sicaklik)
temizlenmis = sicaklik.copy()
temizlenmis[np.isnan(temizlenmis)] = medyan   # NaN → medyan

# Aykırıları sınırla
temizlenmis = np.clip(temizlenmis, alt_sinir, ust_sinir)
print(f"Temizleme sonrası: ort={temizlenmis.mean():.2f}, "
      f"min={temizlenmis.min():.1f}, max={temizlenmis.max():.1f}")
print(f"NaN kaldı mı? {np.isnan(temizlenmis).any()}")
```

**Açıklama:**
`np.nanmean` gibi fonksiyonlar NaN değerleri hesaplamadan dışlar; standart `np.mean` NaN içeren dizide `nan` döndürür. IQR (çeyrekler arası aralık) yöntemi aykırı değer tespitinde istatistiksel olarak sağlam bir yaklaşımdır: Q1 ve Q3'ün 1.5×IQR dışına çıkan değerler aykırı sayılır. `np.clip` değerleri belirlenen sınırlar içine sıkıştırır.

**Beklenen çıktı:**
Eksik kayıt istatistikleri, NaN-güvenli özet istatistikler ve IQR yöntemi ile tespit edilen yüksek sıcaklık değerleri.

**İyileştirme fikirleri:**
- NaN'ları medyan yerine doğrusal interpolasyon (`np.interp`) ile doldurun.
- Z-score yöntemi `(x - μ) / σ > 3` ile aykırı değerleri karşılaştırın.

---

## BLOK 3 — Matematiksel ve İstatistiksel Hesaplamalar

---

### Uygulama 3.1 — İstatistiksel Analiz

**Problem:**
Birden fazla gruba ait sayısal veriyi istatistiksel olarak analiz etmek ve gruplar arası karşılaştırma yapmak.

**Amaç:**
Temel istatistik fonksiyonlarını (`mean`, `std`, `percentile`, `corrcoef`) gerçek bir analiz senaryosunda kullanmak.

**Kullanılan teknikler:**
`np.mean`, `np.std`, `np.percentile`, `np.corrcoef`, `np.histogram`

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(21)

# Simülasyon: 3 farklı fabrika hattından üretim verisi
hat_a = rng.normal(loc=98.5, scale=1.2, size=500)   # stabil hat
hat_b = rng.normal(loc=97.8, scale=3.5, size=500)   # değişken hat
hat_c = rng.normal(loc=99.1, scale=0.8, size=500)   # yüksek kalite

veriler = {"Hat A": hat_a, "Hat B": hat_b, "Hat C": hat_c}

print("=" * 55)
print(f"{'Metrik':>15} {'Hat A':>12} {'Hat B':>12} {'Hat C':>12}")
print("=" * 55)

metrikler = {
    "Ortalama":     lambda x: np.mean(x),
    "Medyan":       lambda x: np.median(x),
    "Std Sapma":    lambda x: np.std(x),
    "Min":          lambda x: np.min(x),
    "Max":          lambda x: np.max(x),
    "Q1 (25%)":     lambda x: np.percentile(x, 25),
    "Q3 (75%)":     lambda x: np.percentile(x, 75),
    "Kap. %90+":    lambda x: (x >= 90).mean() * 100,
}

for isim, func in metrikler.items():
    degerler = [func(v) for v in veriler.values()]
    print(f"{isim:>15} {degerler[0]:>12.2f} {degerler[1]:>12.2f} {degerler[2]:>12.2f}")

print("=" * 55)

# --- Korelasyon Analizi ---
print("\n=== KORELASYON ANALİZİ ===")
# A ve B hattı arasında korelasyon var mı?
korelasyon = np.corrcoef(hat_a, hat_b)
print(f"Hat A — Hat B korelasyonu: {korelasyon[0,1]:.4f}")
print("(≈0 → ilişki yok, ±1 → güçlü ilişki)")

# --- Histogram ile Dağılım ---
print("\n=== DAĞILIM ANALİZİ (Histogram) ===")
for isim, hat in veriler.items():
    sayimlar, sinirlar = np.histogram(hat, bins=10, range=(90, 105))
    print(f"\n{isim}:")
    for i, (s, say) in enumerate(zip(sinirlar, sayimlar)):
        print(f"  [{s:.1f}–{sinirlar[i+1]:.1f}]: {'█'*(say//10)} ({say})")
```

**Açıklama:**
Lambda fonksiyonları ile metrik hesaplamaları bir sözlükte toplanır; bu yapı yeni metrik eklemeyi kolaylaştırır. `np.corrcoef` normalleştirilmiş kovaryans matrisi döndürür; `[0,1]` konumu iki değişken arasındaki Pearson korelasyon katsayısıdır. `np.histogram` ile dağılımı sayısal olarak inceleyebilir, Matplotlib olmadan da anlayabilirsiniz.

**Beklenen çıktı:**
Karşılaştırmalı istatistik tablosu. Hat B'nin standart sapması diğerlerinden belirgin yüksek çıkar; Hat C en yüksek ortalama kaliteyi gösterir.

**İyileştirme fikirleri:**
- Üç hat arasındaki farkın istatistiksel anlamlılığını basit bir t-testi ile kontrol edin.
- Kapasite kullanım oranını `(x >= 95).mean()` ile hesaplayın.

---

### Uygulama 3.2 — Matris İşlemleri ve Lineer Cebir

**Problem:**
Matris çarpımı, transpoz ve temel lineer cebir işlemlerini NumPy ile gerçekleştirmek.

**Amaç:**
`np.dot`, `@` operatörü, `np.linalg` modülünü kullanmayı öğrenmek.

**Kullanılan teknikler:**
`np.dot`, `@`, `np.linalg.inv`, `np.linalg.det`, `np.linalg.solve`

**Kod:**
```python
import numpy as np

# --- Matris Çarpımı ---
print("=== MATRİS ÇARPIMI ===")
A = np.array([[1, 2, 3],
              [4, 5, 6]])   # 2×3

B = np.array([[7,  8],
              [9, 10],
              [11, 12]])    # 3×2

C = A @ B    # 2×3 @ 3×2 → 2×2
print(f"A (2×3):\n{A}")
print(f"B (3×2):\n{B}")
print(f"A @ B (2×2):\n{C}")

# Eleman bazlı vs matris çarpımı farkı
X = np.array([[1, 2], [3, 4]])
Y = np.array([[5, 6], [7, 8]])
print(f"\nEleman bazlı (X * Y):\n{X * Y}")
print(f"\nMatris çarpımı (X @ Y):\n{X @ Y}")

# --- Ters Matris ---
print("\n=== TERS MATRİS ve DETERMINANT ===")
M = np.array([[2.0, 1.0],
              [5.0, 3.0]])

det = np.linalg.det(M)
inv = np.linalg.inv(M)

print(f"M:\n{M}")
print(f"det(M): {det:.2f}")
print(f"M⁻¹:\n{inv}")
print(f"\nDoğrulama M @ M⁻¹ ≈ I:\n{(M @ inv).round(10)}")

# --- Lineer Denklem Sistemi ---
print("\n=== LİNEER DENKLEM SİSTEMİ ===")
# Sistem:  2x + y = 8
#          5x + 3y = 21
A_sys = np.array([[2.0, 1.0],
                   [5.0, 3.0]])
b_sys = np.array([8.0, 21.0])

cozum = np.linalg.solve(A_sys, b_sys)
print(f"2x + y  = 8")
print(f"5x + 3y = 21")
print(f"Çözüm: x = {cozum[0]:.1f}, y = {cozum[1]:.1f}")
print(f"Doğrulama: A @ cozum = {(A_sys @ cozum).round(6)}")

# --- Vektör Normları ---
print("\n=== VEKTÖR NORMLARI ===")
v = np.array([3.0, 4.0, 0.0])
print(f"Vektör: {v}")
print(f"L2 normu (Öklid uzaklığı): {np.linalg.norm(v):.2f}")
print(f"L1 normu (Manhattan uzaklık): {np.linalg.norm(v, ord=1):.2f}")
print(f"Birim vektör: {v / np.linalg.norm(v)}")
```

**Açıklama:**
`@` operatörü Python 3.5+ ile matris çarpımı için özel olarak eklendi; `np.dot(A, B)` ile eşdeğerdir ama okunması daha kolaydır. `np.linalg.solve` denklem sistemini Gaussian eliminasyon ile çözer; `inv(A) @ b`'den daha kararlı (numerically stable) bir yaklaşımdır. L2 normu bir vektörün Öklid uzunluğunu verir; normalleştirmede bölünür.

**Beklenen çıktı:**
Matris çarpımı sonuçları, ters matris ile doğrulama (I'ya yakın) ve denklem sistemi çözümü.

**İyileştirme fikirleri:**
- `np.linalg.eig` ile özdeğer/özvektör hesaplayın.
- `np.linalg.lstsq` ile en küçük kareler regresyon uygulayın.

---

### Uygulama 3.3 — Rastgele Simülasyon — Monte Carlo

**Problem:**
Monte Carlo yöntemiyle π sayısını tahmin etmek ve bir finans simülasyonu yapmak.

**Amaç:**
Büyük ölçekli rastgele simülasyonlar için NumPy'ın vektörel operasyonlarını kullanmayı öğrenmek.

**Kullanılan teknikler:**
`np.random`, `np.sum`, vektörel koşul değerlendirmesi

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(99)

# --- Monte Carlo ile Pi Tahmini ---
print("=== MONTE CARLO — Pi Tahmini ===")
for N in [1_000, 10_000, 100_000, 1_000_000]:
    # Birim kare içine rastgele nokta at
    x = rng.uniform(-1, 1, N)
    y = rng.uniform(-1, 1, N)

    # Birim çember içinde kalanları say (x²+y² ≤ 1)
    icerde = (x**2 + y**2) <= 1.0
    pi_tahmin = 4 * icerde.sum() / N
    hata = abs(pi_tahmin - np.pi)
    print(f"  N={N:>9,}  pi≈{pi_tahmin:.6f}  hata={hata:.6f}")

# --- Finansal Simülasyon — Hisse Senedi Fiyatı ---
print("\n=== FİNANSAL SİMÜLASYON — Geometrik Brownian Motion ===")
S0        = 100.0    # başlangıç fiyatı
mu        = 0.10     # yıllık beklenen getiri
sigma     = 0.25     # yıllık volatilite
T         = 252      # gün (1 yıl)
N_yol     = 10_000   # simülasyon yolu sayısı

# Günlük getiri = N(mu/T, sigma/sqrt(T)) dağılımından
dt      = 1
gunluk  = rng.normal((mu - 0.5 * sigma**2) * dt / 252,
                      sigma * np.sqrt(dt / 252),
                      size=(T, N_yol))

# Kümülatif çarpım ile fiyat yolu
fiyat_yollari = S0 * np.exp(np.cumsum(gunluk, axis=0))

son_fiyatlar = fiyat_yollari[-1, :]   # 1 yıl sonraki fiyatlar

print(f"Başlangıç fiyatı  : {S0:.2f}")
print(f"Yol sayısı        : {N_yol:,}")
print(f"\n1 yıl sonraki fiyat dağılımı:")
print(f"  Ortalama   : {son_fiyatlar.mean():.2f}")
print(f"  Medyan     : {np.median(son_fiyatlar):.2f}")
print(f"  5. yüzdelik: {np.percentile(son_fiyatlar, 5):.2f}")
print(f"  95. yüzdelik: {np.percentile(son_fiyatlar, 95):.2f}")
print(f"  Kayıp riski (<%80): %{(son_fiyatlar < 80).mean()*100:.1f}")
print(f"  Kâr olasılığı (>%100): %{(son_fiyatlar > 100).mean()*100:.1f}")
```

**Açıklama:**
Pi tahminin arkasında basit bir olasılık ilkesi yatar: birim karedeki noktalarda çember içine düşme oranı π/4'e eşittir. Nokta sayısı arttıkça tahmin gerçek değere yaklaşır. Finans simülasyonunda `T × N_yol` boyutlu matris oluşturulur; `cumsum` ile her sütun birbirinden bağımsız bir fiyat yolu oluşturur. Tüm yollar vektörel olarak hesaplanır — döngü yoktur.

**Beklenen çıktı:**
N arttıkça pi tahmininin gerçek değere yaklaştığı tablo ve 10.000 simülasyon yoluna dayalı olasılık dağılımı.

**İyileştirme fikirleri:**
- Farklı `sigma` (volatilite) değerlerini deneyerek risk-getiri ilişkisini gözlemleyin.
- Zararlı fiyat yollarını `fiyat_yollari.min(axis=0)` ile bulun.

---

## BLOK 4 — Gerçek Dünya Senaryoları

---

### Uygulama 4.1 — NumPy ile Görüntü Manipülasyonu

**Problem:**
Bir görüntüyü NumPy dizisi olarak işleyerek parlaklık ayarı, kontrast iyileştirme, kanal maskeleme ve temel filtre uygulama.

**Amaç:**
NumPy array operasyonlarının görüntü işleme için nasıl kullanıldığını öğrenmek; OpenCV'ye ek olarak saf NumPy ile neler yapılabileceğini görmek.

**Kullanılan teknikler:**
`np.clip`, broadcasting, boolean maskeleme, konvolüsyon (manuel)

**Pipeline:**
```
Görüntü (uint8 ndarray)
    → float32'ye çevir
    → parlaklık/kontrast ayarı
    → uint8'e geri dön
    → kanal analizi
    → basit filtre
```

**Kod:**
```python
import numpy as np
import cv2
import matplotlib.pyplot as plt

# Görüntü yükle veya yapay oluştur
img = cv2.imread("foto.jpg")
if img is None:
    # Yapay renkli görüntü: gradient + daireler
    img = np.zeros((300, 400, 3), dtype=np.uint8)
    for r in range(300):
        img[r, :, 0] = int(r / 300 * 200)    # R kanalı: dikey degrade
    for c in range(400):
        img[:, c, 1] = int(c / 400 * 200)    # G kanalı: yatay degrade
    img[:, :, 2] = 80                          # B kanalı: sabit
    cv2.circle(img, (200, 150), 80, (255, 255, 255), -1)

img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
print(f"Görüntü shape: {img.shape}, dtype: {img.dtype}")

# --- Parlaklık Ayarı ---
def parlaklik_ayarla(goruntu, beta):
    """Beta: pozitif → aydınlat, negatif → karart."""
    float_img = goruntu.astype(np.float32)
    ayarli    = np.clip(float_img + beta, 0, 255)
    return ayarli.astype(np.uint8)

# --- Kontrast Ayarı ---
def kontrast_ayarla(goruntu, alpha):
    """Alpha > 1 → kontrast artır, < 1 → azalt."""
    float_img = goruntu.astype(np.float32)
    ayarli    = np.clip(float_img * alpha, 0, 255)
    return ayarli.astype(np.uint8)

# --- Min-Max Normalizasyon (0-255) ---
def normalize_et(goruntu):
    float_img = goruntu.astype(np.float32)
    min_v, max_v = float_img.min(), float_img.max()
    normalize = (float_img - min_v) / (max_v - min_v) * 255
    return normalize.astype(np.uint8)

# --- Kanal Maskeleme ---
def kirmizi_vurgula(goruntu):
    """Kırmızı kanalı korur, diğerlerini bastırır."""
    maskeli = goruntu.copy()
    maskeli[:, :, 0] = 0    # B → 0 (OpenCV BGR sırası)
    maskeli[:, :, 1] = 0    # G → 0
    return maskeli

# --- Basit 3×3 Box Filtre ---
def kutu_filtrele(goruntu):
    """Her pikseli komşularının ortalamasıyla değiştirir."""
    float_img = goruntu.astype(np.float32)
    sonuc = np.zeros_like(float_img)
    # Manuel sliding window (kenarlar hariç)
    sonuc[1:-1, 1:-1] = (
        float_img[:-2,  :-2] + float_img[:-2, 1:-1] + float_img[:-2, 2:] +
        float_img[1:-1, :-2] + float_img[1:-1,1:-1] + float_img[1:-1,2:] +
        float_img[2:,   :-2] + float_img[2:,  1:-1] + float_img[2:,  2:]
    ) / 9.0
    return sonuc.astype(np.uint8)

# Işlemler
aydinlik  = parlaklik_ayarla(img_rgb, +60)
karanlik  = parlaklik_ayarla(img_rgb, -60)
yuksek_k  = kontrast_ayarla(img_rgb, 1.5)
normalize = normalize_et(img_rgb)
kirmizi   = kirmizi_vurgula(img_rgb)
filtreli  = kutu_filtrele(img_rgb)

# Görselleştir
fig, axes = plt.subplots(2, 4, figsize=(16, 8))
basliklar  = ["Orijinal", "Aydınlık (+60)", "Karanlık (-60)", "Yüksek Kontrast",
              "Normalize", "Kırmızı Kanal", "Kutu Filtre", "Fark Haritası"]
gorseller  = [img_rgb, aydinlik, karanlik, yuksek_k,
              normalize, kirmizi, filtreli,
              np.abs(img_rgb.astype(int) - filtreli.astype(int)).astype(np.uint8)]

for ax, gorsel, baslik in zip(axes.flat, gorseller, basliklar):
    ax.imshow(gorsel)
    ax.set_title(baslik, fontsize=9)
    ax.axis('off')

plt.suptitle("NumPy ile Görüntü İşleme", fontsize=13)
plt.tight_layout()
plt.savefig("goruntu_isleme.png", dpi=120)
plt.show()

print(f"\nÖzet istatistikler (gri kanal):")
gri = img_rgb.mean(axis=2)
print(f"  Ortalama parlaklık: {gri.mean():.1f}")
print(f"  Std sapma (kontrast göstergesi): {gri.std():.1f}")
```

**Açıklama:**
`float32`'ye dönüşüm `uint8` taşmasını önler; `clip` ile 0-255 arasına sıkıştırıp geri `uint8`'e döndürülür. Manuel kutu filtre uygulaması NumPy slice aligns kullanır: `[:-2, :-2]` sol-üst, `[1:-1, 1:-1]` merkez ve `[2:, 2:]` sağ-alt komşuyu temsil eder; dokuz dilim toplamı 9'a bölünür. Bu işlem OpenCV'nin `blur` fonksiyonunun saf NumPy karşılığıdır.

**Beklenen çıktı:**
8 panelli görüntü karşılaştırma ızgarası. Fark haritası orijinal ile filtreli görüntü arasındaki piksel farklarını gösterir.

**İyileştirme fikirleri:**
- Sharpen kerneli `[[0,-1,0],[-1,5,-1],[0,-1,0]]` uygulayın.
- Histogramı `np.histogram` ile hesaplayıp grafik çizin.

---

### Uygulama 4.2 — Zaman Serisi Analizi

**Problem:**
Bir yılın her günü için simüle edilmiş satış verisini analiz etmek: trend, mevsimsellik ve hareketli ortalama.

**Amaç:**
Kümülatif fonksiyonları, konvolüsyonu (hareketli ortalama için) ve zaman serisi analizini öğrenmek.

**Kullanılan teknikler:**
`np.cumsum`, `np.convolve`, `np.polyfit`, `np.correlate`

**Kod:**
```python
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(55)

# 365 günlük satış verisi simülasyonu
gunler = np.arange(365)

# Mevsimsel bileşen (yıllık sinüs dalgası)
mevsimsel = 50 * np.sin(2 * np.pi * gunler / 365 - np.pi / 2) + 50

# Trend bileşeni (yavaş artış)
trend = 0.15 * gunler

# Gürültü
gurultu = rng.normal(0, 15, 365)

# Toplam satış
satis = mevsimsel + trend + gurultu + 100   # +100 baz düzey

print("=== GENEL İSTATİSTİKLER ===")
print(f"Yıllık toplam satış: {satis.sum():.0f}")
print(f"Günlük ortalama    : {satis.mean():.1f}")
print(f"En iyi gün         : {gunler[satis.argmax()]+1}. gün ({satis.max():.1f})")
print(f"En kötü gün        : {gunler[satis.argmin()]+1}. gün ({satis.min():.1f})")

# --- Hareketli Ortalama ---
pencere_7  = np.ones(7)  / 7
pencere_30 = np.ones(30) / 30

ort_7  = np.convolve(satis, pencere_7,  mode='same')
ort_30 = np.convolve(satis, pencere_30, mode='same')

# --- Trend Tahmini (Doğrusal Regresyon) ---
katsayilar = np.polyfit(gunler, satis, deg=1)   # y = ax + b
trend_cizgi = np.polyval(katsayilar, gunler)
print(f"\nTrend analizi: Günde {katsayilar[0]:.3f} birim artış")

# --- Aylık Özet ---
print("\n=== AYLIK ÖZET ===")
print(f"{'Ay':>4} {'Toplam':>8} {'Ort':>8} {'Max':>8}")
print("-" * 35)
gun_basi = 0
for ay in range(1, 13):
    gun_sayisi = 31 if ay in [1,3,5,7,8,10,12] else (28 if ay == 2 else 30)
    gun_sonu   = min(gun_basi + gun_sayisi, 365)
    ay_satis   = satis[gun_basi:gun_sonu]
    print(f"{ay:>4} {ay_satis.sum():>8.0f} {ay_satis.mean():>8.1f} {ay_satis.max():>8.1f}")
    gun_basi = gun_sonu

# --- Grafik ---
fig, axes = plt.subplots(2, 1, figsize=(12, 8))

axes[0].plot(gunler, satis, alpha=0.4, color='steelblue', label='Günlük Satış', lw=0.8)
axes[0].plot(gunler, ort_7,  color='orange', lw=2,   label='7 Günlük Ort.')
axes[0].plot(gunler, ort_30, color='red',    lw=2.5, label='30 Günlük Ort.')
axes[0].plot(gunler, trend_cizgi, color='black', lw=1.5, ls='--', label='Trend')
axes[0].set_title("Satış Zaman Serisi")
axes[0].set_xlabel("Gün")
axes[0].set_ylabel("Satış")
axes[0].legend()
axes[0].grid(True, alpha=0.3)

kumulatif = np.cumsum(satis)
axes[1].fill_between(gunler, kumulatif, alpha=0.4, color='green')
axes[1].plot(gunler, kumulatif, color='darkgreen', lw=2)
axes[1].set_title("Kümülatif Satış")
axes[1].set_xlabel("Gün")
axes[1].set_ylabel("Toplam Satış")
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("zaman_serisi.png", dpi=120)
plt.show()
```

**Açıklama:**
`np.convolve` hareketli ortalamayı verimli şekilde hesaplar: `mode='same'` çıktıyı girişle aynı uzunlukta tutar. `np.polyfit` ile doğrusal regresyon yapılır; döndürülen katsayılar `polyval` ile kolayca grafiğe aktarılır. `np.cumsum` birikimli toplamı tek geçişte hesaplar.

**Beklenen çıktı:**
Üst panelde ham veri, 7 ve 30 günlük hareketli ortalamalar ve trend çizgisi. Alt panelde yıl boyunca büyüyen kümülatif satış eğrisi.

**İyileştirme fikirleri:**
- `np.polyfit(gunler, satis, deg=2)` ile kuadratik trend deneyin.
- Haftalık periyodikliği `np.correlate` ile kontrol edin.

---

## BLOK 5 — Mini Projeler

---

### Uygulama 5.1 — Veri Temizleme ve Analiz Boru Hattı

**Problem:**
Ham, kirli bir CSV benzeri veri setini baştan sona temizleyip analiz eden yeniden kullanılabilir bir pipeline kurmak.

**Amaç:**
NumPy fonksiyonlarını bir fonksiyon zinciri içinde birleştirerek gerçek bir veri işleme akışı oluşturmak.

**Pipeline:**
```
Ham veri (kirli)
    → NaN tespiti ve doldurma
    → aykırı değer kırpma
    → normalizasyon
    → istatistiksel özet raporu
    → Matplotlib ile görselleştirme
```

**Kod:**
```python
import numpy as np
import matplotlib.pyplot as plt

rng = np.random.default_rng(13)

# ── Veri Üretimi (kirli veri simülasyonu) ──────────────────────────────
def kirli_veri_uret(n=300):
    veri = rng.normal(50, 10, n).astype(np.float64)
    # %7 NaN ekle
    nan_idx = rng.choice(n, size=int(n*0.07), replace=False)
    veri[nan_idx] = np.nan
    # %3 aykırı değer
    aykiri_idx = rng.choice(n, size=int(n*0.03), replace=False)
    veri[aykiri_idx] = rng.uniform(100, 150, len(aykiri_idx))
    return veri

# ── Pipeline Fonksiyonları ─────────────────────────────────────────────
def nan_doldur(dizi, yontem='medyan'):
    """NaN değerleri medyan veya ortalama ile doldurur."""
    temiz = dizi.copy()
    if yontem == 'medyan':
        deger = np.nanmedian(dizi)
    else:
        deger = np.nanmean(dizi)
    temiz[np.isnan(temiz)] = deger
    return temiz, deger

def aykiri_kirp(dizi, katsayi=1.5):
    """IQR yöntemiyle aykırı değerleri sınırlar."""
    q1 = np.percentile(dizi, 25)
    q3 = np.percentile(dizi, 75)
    iqr = q3 - q1
    alt = q1 - katsayi * iqr
    ust = q3 + katsayi * iqr
    kirpilmis = np.clip(dizi, alt, ust)
    return kirpilmis, alt, ust

def normalize(dizi, yontem='minmax'):
    """Min-max veya z-score normalizasyonu."""
    if yontem == 'minmax':
        return (dizi - dizi.min()) / (dizi.max() - dizi.min())
    elif yontem == 'zscore':
        return (dizi - dizi.mean()) / dizi.std()

def ozet_raporu(etiket, dizi):
    """İstatistiksel özet yazdırır."""
    print(f"\n{'─'*40}")
    print(f"  {etiket}")
    print(f"{'─'*40}")
    print(f"  Boyut     : {len(dizi)}")
    print(f"  NaN sayısı: {np.isnan(dizi).sum()}")
    print(f"  Ort ± Std : {np.nanmean(dizi):.2f} ± {np.nanstd(dizi):.2f}")
    print(f"  [Min, Max]: [{np.nanmin(dizi):.2f}, {np.nanmax(dizi):.2f}]")
    print(f"  Medyan    : {np.nanmedian(dizi):.2f}")

# ── Pipeline ──────────────────────────────────────────────────────────
ham_veri = kirli_veri_uret(300)
ozet_raporu("1. Ham Veri", ham_veri)

temiz_veri, doldurma_degeri = nan_doldur(ham_veri, 'medyan')
ozet_raporu(f"2. NaN Doldurma (medyan={doldurma_degeri:.1f})", temiz_veri)

kirpilmis_veri, alt, ust = aykiri_kirp(temiz_veri, katsayi=1.5)
ozet_raporu(f"3. Aykırı Kırpma (sınırlar: {alt:.1f}–{ust:.1f})", kirpilmis_veri)

normalize_veri = normalize(kirpilmis_veri, 'minmax')
ozet_raporu("4. Min-Max Normalizasyon", normalize_veri)

# ── Görselleştirme ────────────────────────────────────────────────────
fig, axes = plt.subplots(2, 2, figsize=(12, 9))
fig.suptitle("Veri Temizleme Pipeline'ı", fontsize=14, fontweight='bold')

veriler   = [ham_veri, temiz_veri, kirpilmis_veri, normalize_veri]
basliklar = ["Ham Veri (NaN + Aykırı)", "NaN Doldurulmuş",
             "Aykırı Değerler Kırpıldı", "Normalize Edilmiş (0-1)"]
renkler   = ['tomato', 'steelblue', 'orange', 'seagreen']

for ax, veri, baslik, renk in zip(axes.flat, veriler, basliklar, renkler):
    gecerli = veri[~np.isnan(veri)] if np.isnan(veri).any() else veri
    ax.hist(gecerli, bins=30, color=renk, alpha=0.8, edgecolor='white')
    ax.axvline(np.nanmean(veri), color='black', lw=2, ls='--',
               label=f'Ort: {np.nanmean(veri):.2f}')
    ax.set_title(baslik)
    ax.legend(fontsize=9)
    ax.grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("pipeline_sonuc.png", dpi=120)
plt.show()
```

**Açıklama:**
Her pipeline adımı bağımsız bir fonksiyondur; bu yapı test edilebilirliği ve yeniden kullanımı kolaylaştırır. Fonksiyonlar hem işlenmiş veriyi hem de bilgi değerlerini (doldurma değeri, sınırlar) döndürür — raporlama için kullanılır. Pipeline akışı ham veriden normalize veriye adım adım ilerler ve her adımda dağılım histogramla görselleştirilir.

**Beklenen çıktı:**
4 adımlık konsol raporu ve 2×2 histogram ızgarası. Ham verinin sağ kuyruğundaki aykırı değerler kırpma adımında kaybolur; son histogramın 0-1 aralığına sıkıştığı görülür.

**İyileştirme fikirleri:**
- Pipeline'a yeni bir adım ekleyin: `tekilstir` (veri noktalarını çeyreklik kategorilere sınıflandırma).
- Fonksiyonları `class Pipeline` içinde toplayarak nesne yönelimli bir yapıya dönüştürün.

---

### Uygulama 5.2 — Görüntü Karşılaştırma Aracı

**Problem:**
İki görüntü arasındaki piksel farklarını hesaplamak ve görsel olarak raporlamak.

**Amaç:**
NumPy'ın görüntü aritmetiği, istatistik ve dizi operasyonlarını birleştirerek küçük ama eksiksiz bir analiz aracı oluşturmak.

**Kod:**
```python
import numpy as np
import matplotlib.pyplot as plt

def goruntu_uret(sekil=(200, 300, 3), seed=None):
    """Test için gerçekçi görüntü üretir."""
    rng = np.random.default_rng(seed)
    g = np.zeros(sekil, dtype=np.uint8)
    g[:, :, 0] = np.tile(np.linspace(0, 200, sekil[1]), (sekil[0], 1))
    g[:, :, 1] = np.tile(np.linspace(200, 0, sekil[0]).reshape(-1, 1), (1, sekil[1]))
    g[:, :, 2] = 100
    g += rng.integers(0, 30, sekil, dtype=np.uint8)
    return g

def goruntu_karsilastir(img1, img2):
    """İki görüntüyü karşılaştırır ve metriklerle rapor üretir."""
    assert img1.shape == img2.shape, "Görüntüler aynı boyutta olmalı"

    # Mutlak fark
    fark = np.abs(img1.astype(np.int16) - img2.astype(np.int16)).astype(np.uint8)

    # Metrikler
    mse  = ((img1.astype(np.float32) - img2.astype(np.float32))**2).mean()
    rmse = np.sqrt(mse)
    mae  = np.abs(img1.astype(np.float32) - img2.astype(np.float32)).mean()

    # PSNR (Peak Signal-to-Noise Ratio)
    if mse == 0:
        psnr = float('inf')
    else:
        psnr = 10 * np.log10(255**2 / mse)

    # Piksel benzerlik oranı (fark < 10 ise benzer say)
    esik = 10
    benzer_oran = (fark.mean(axis=2) < esik).mean() * 100

    # Görselleştirme
    fig, axes = plt.subplots(2, 3, figsize=(14, 8))
    fig.suptitle("Görüntü Karşılaştırma Raporu", fontsize=13, fontweight='bold')

    axes[0,0].imshow(img1)
    axes[0,0].set_title("Görüntü 1 (Orijinal)")
    axes[0,0].axis('off')

    axes[0,1].imshow(img2)
    axes[0,1].set_title("Görüntü 2 (Değiştirilmiş)")
    axes[0,1].axis('off')

    im = axes[0,2].imshow(fark, cmap='hot')
    axes[0,2].set_title("Fark Haritası")
    axes[0,2].axis('off')
    plt.colorbar(im, ax=axes[0,2], fraction=0.046)

    # Kanal histogramları
    renkler = ['red', 'green', 'blue']
    kanal_isimleri = ['R', 'G', 'B']
    for k in range(3):
        axes[1,0].hist(img1[:,:,k].ravel(), bins=64, alpha=0.5,
                       color=renkler[k], label=f'Img1-{kanal_isimleri[k]}')
        axes[1,0].hist(img2[:,:,k].ravel(), bins=64, alpha=0.3,
                       color=renkler[k], linestyle='--', histtype='step',
                       linewidth=2, label=f'Img2-{kanal_isimleri[k]}')
    axes[1,0].set_title("Kanal Histogramları")
    axes[1,0].legend(fontsize=7)
    axes[1,0].grid(True, alpha=0.3)

    # Fark dağılımı
    axes[1,1].hist(fark.ravel(), bins=50, color='coral', edgecolor='white')
    axes[1,1].set_title("Fark Değerleri Dağılımı")
    axes[1,1].axvline(fark.mean(), color='black', lw=2, ls='--',
                      label=f'Ort: {fark.mean():.1f}')
    axes[1,1].legend()
    axes[1,1].grid(True, alpha=0.3)

    # Metrik kutusu
    axes[1,2].axis('off')
    metrik_metni = (
        f"KARŞILAŞTIRMA METRİKLERİ\n"
        f"{'─'*30}\n"
        f"MSE  : {mse:.2f}\n"
        f"RMSE : {rmse:.2f}\n"
        f"MAE  : {mae:.2f}\n"
        f"PSNR : {psnr:.1f} dB\n"
        f"{'─'*30}\n"
        f"Benzerlik (eşik<{esik}): %{benzer_oran:.1f}\n\n"
        f"(PSNR > 40 dB → çok yakın\n"
        f" PSNR < 20 dB → belirgin fark)"
    )
    axes[1,2].text(0.1, 0.5, metrik_metni, transform=axes[1,2].transAxes,
                   fontsize=11, verticalalignment='center',
                   fontfamily='monospace',
                   bbox=dict(boxstyle='round', facecolor='lightyellow', alpha=0.8))

    plt.tight_layout()
    plt.savefig("karsilastirma_raporu.png", dpi=120, bbox_inches='tight')
    plt.show()

    return {"mse": mse, "rmse": rmse, "mae": mae,
            "psnr": psnr, "benzerlik": benzer_oran}

# Test
img_orijinal = goruntu_uret(seed=0)
img_degisik  = img_orijinal.copy()

# Değişiklik uygula: sağ bölümü aydınlat, bir kutu ekle
img_degisik[:, 150:] = np.clip(
    img_degisik[:, 150:].astype(np.int16) + 40, 0, 255
).astype(np.uint8)
img_degisik[80:120, 100:200, :] = [255, 200, 0]  # sarı dikdörtgen

sonuclar = goruntu_karsilastir(img_orijinal, img_degisik)
print("\nMetrikler:", sonuclar)
```

**Açıklama:**
MSE (Mean Squared Error) büyük farklara aşırı duyarlıdır; MAE tüm farklara eşit davranır. PSNR sinyalin gürültüye oranını dB cinsinden verir; 40 dB üzeri görüntüler gözle neredeyse ayırt edilemez. Fark haritasında `hot` colormap sıfır farkı siyah, büyük farkları kırmızı-beyaz gösterir; değişiklik yapılan bölgeler hemen göze çarpar.

**Beklenen çıktı:**
2×3 görsel panel: orijinal ve değiştirilmiş görüntü, ısı haritası fark, kanal histogramları, fark dağılımı ve metrik tablosu.

**İyileştirme fikirleri:**
- OpenCV ile okuduktan sonra `cv2.cvtColor(img, cv2.COLOR_BGR2RGB)` uygulayarak gerçek fotoğraflarla deneyin.
- SSIM (Structural Similarity) metriği eklemek için `scipy.signal.correlate2d` kullanın.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `numpy-teori.md` · `numpy-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
