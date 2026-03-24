# ✅ NumPy — Alıştırma Çözümleri

> **Modül:** Çözümler
> **İlgili dosya:** `numpy-alistirmalar.md`

> ⚠️ Kendi çözümünüzü tamamlamadan bu dosyayı açmayın.

---

## BLOK 1 — Kolay Çözümler

---

### Çözüm 1.1 — Kimlik Matrisi Olmadan Birim Matris

**Çözüm yaklaşımı:**
Sıfır matris oluşturup köşegen indekslerini `np.arange` ile belirleyerek o konumlara 1 atayın.

**Kod:**
```python
import numpy as np

# Sıfır matris oluştur
matris = np.zeros((5, 5), dtype=int)

# Köşegen indekslerini belirle
indeksler = np.arange(5)
matris[indeksler, indeksler] = 1

print("5×5 Birim Matris:")
print(matris)

# Herhangi bir 3×3 kısım (örn: orta köşe, satır 1-3, sütun 1-3)
alt_matris = matris[1:4, 1:4]
print("\nOrta 3×3 kısım:")
print(alt_matris)
```

**Açıklama:**
`matris[indeksler, indeksler]` fancy indeksleme ile `(0,0)`, `(1,1)`, `(2,2)`, `(3,3)`, `(4,4)` konumlarını aynı anda seçer. Bu, `np.eye` veya `np.identity`'nin elle uygulanmış halidir. `[1:4, 1:4]` dilimleme orta 3×3 bloku verir; hem satır hem sütun 1'den 3'e kadar seçilir.

---

### Çözüm 1.2 — Aralık ve Şekil Oyunu

**Çözüm yaklaşımı:**
`arange` → `reshape` → çift sütun dilimleme → `axis=1` ile satır toplamı.

**Kod:**
```python
import numpy as np

# 1-49 dizisi → 7×7 matris
matris = np.arange(1, 50).reshape(7, 7)
print("7×7 Matris:")
print(matris)

# Çift sütunlar: 0, 2, 4, 6 (::2 dilimi)
cift_sutunlar = matris[:, ::2]
print("\nÇift sütunlar (sütun 0, 2, 4, 6):")
print(cift_sutunlar)

# Her satırın toplamı (axis=1)
satir_toplamlari = cift_sutunlar.sum(axis=1)
print("\nHer satırın çift sütun toplamı:")
for i, t in enumerate(satir_toplamlari):
    print(f"  Satır {i+1}: {t}")
```

**Açıklama:**
`::2` dilimi başlangıçtan sona kadar 2 adımlı seçim yapar; tüm satırlar için çift sütunları alır. `sum(axis=1)` her satırı yatay yönde toplar; `axis=0` olsaydı sütunları dikey toplamak demekti.

---

### Çözüm 1.3 — Geri Sayım Matrisi

**Çözüm yaklaşımı:**
`np.arange` ile negatif adım veya `[::-1]` kullanarak ters sıra; `np.diag` ile köşegen.

**Kod:**
```python
import numpy as np

# 100'den 1'e geri say → 10×10 matris
dizi   = np.arange(100, 0, -1)   # negatif adım
matris = dizi.reshape(10, 10)

print("10×10 Matris (ilk 3 satır):")
print(matris[:3])

# Ana köşegen
kosegen = np.diag(matris)
print(f"\nAna köşegen: {kosegen}")

# flip ile tersine çevir
yatay = np.flip(matris, axis=1)
dikey = np.flip(matris, axis=0)

print("\nOrijinal (ilk satır)   :", matris[0])
print("Yatay flip (ilk satır) :", yatay[0])
print("Dikey flip (ilk satır) :", dikey[0])
```

**Açıklama:**
`arange(100, 0, -1)` 100'den başlar, 0'a kadar (0 dahil değil) gider, her adımda -1 ekler. `np.diag` 2D matrise uygulandığında ana köşegeni 1D dizi olarak döndürür. `flip(matris, axis=1)` her satırı kendi içinde tersine çevirir.

---

### Çözüm 1.4 — Boolean Süzgeci

**Çözüm yaklaşımı:**
Her koşul için ayrı boolean maske oluştur, filtrele, eleman sayısını ve ortalamasını hesapla.

**Kod:**
```python
import numpy as np

rng  = np.random.default_rng(42)
dizi = rng.integers(0, 201, size=100)

# Grup 1: 50'den küçük
grup1 = dizi[dizi < 50]
print(f"50'den küçük  → Sayı: {len(grup1)}, Ort: {grup1.mean():.1f}")

# Grup 2: 100-150 arası
mask2 = (dizi >= 100) & (dizi <= 150)
grup2 = dizi[mask2]
print(f"100–150 arası → Sayı: {len(grup2)}, Ort: {grup2.mean():.1f}")

# Grup 3: 3'e ve 5'e bölünebilen
mask3 = (dizi % 3 == 0) & (dizi % 5 == 0)   # yani 15'in katları
grup3 = dizi[mask3]
print(f"15'in katları → Sayı: {len(grup3)}, Ort: {grup3.mean():.1f}")
print(f"  Değerler: {sorted(np.unique(grup3))}")
```

**Açıklama:**
`&` operatörü boolean dizilerde eleman bazlı VE işlemi yapar. Her koşul ayrı parantez içinde olmalı çünkü `&`'nin önceliği `>=` ve `<=`'den yüksektir. 3'e ve 5'e birden bölünebilen sayılar aynı zamanda 15'in katlarıdır.

---

### Çözüm 1.5 — İstatistik Hesap Makinesi

**Çözüm yaklaşımı:**
`np.array`'e dönüştür, standart istatistik fonksiyonlarını uygula, formatlanmış rapor yazdır.

**Kod:**
```python
import numpy as np

sinav_notlari = np.array([72, 85, 91, 63, 78, 88, 55, 94, 67, 82, 77, 89, 71, 60, 95])

print("=" * 40)
print("      SINAV NOTU RAPORU")
print("=" * 40)
print(f"Öğrenci sayısı : {len(sinav_notlari)}")
print(f"Ortalama       : {sinav_notlari.mean():.2f}")
print(f"Medyan         : {np.median(sinav_notlari):.2f}")
print(f"Std sapma      : {sinav_notlari.std():.2f}")
print(f"En yüksek      : {sinav_notlari.max()}  (indeks: {sinav_notlari.argmax()})")
print(f"En düşük       : {sinav_notlari.min()}  (indeks: {sinav_notlari.argmin()})")

gecenler = (sinav_notlari >= 75).sum()
print(f"75+ alanlar    : {gecenler} kişi (%{gecenler/len(sinav_notlari)*100:.0f})")

sirali = np.sort(sinav_notlari)[::-1]   # büyükten küçüğe
print(f"\nSıralı notlar  : {sirali}")
```

**Açıklama:**
`argmax()` en büyük değerin ilk geçtiği indeksi döndürür. `np.sort(dizi)[::-1]` önce artan sıraya dizer, `[::-1]` ile tersine çevirir — bu, azalan sıraya dizmenin standart yoludur. `np.median` merkezi değeri verir; çift sayıda eleman varsa ortadaki ikisinin ortalaması alınır.

---

### Çözüm 1.6 — Görüntü Kanalı Simülasyonu

**Çözüm yaklaşımı:**
`uint8` dizisi oluştur, kanallar üzerinde istatistik hesapla, güvenli çarpma için `float32` kullan.

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(7)
goruntu = rng.integers(0, 256, size=(64, 64, 3), dtype=np.uint8)

# Her kanalın ortalaması (OpenCV BGR sırasını simüle ediyoruz ama RGB kabul edelim)
for i, kanal in enumerate(['R', 'G', 'B']):
    print(f"{kanal} kanalı ortalaması: {goruntu[:, :, i].mean():.1f}")

# Kırmızı kanalı (indeks 0) sıfırla
goruntu_kirmizisiz = goruntu.copy()
goruntu_kirmizisiz[:, :, 0] = 0
print(f"\nKırmızı kanal sıfırlandı → yeni ortalama: {goruntu_kirmizisiz.mean():.1f}")

# Güvenli parlaklık artırma (1.3×)
float_goruntu = goruntu.astype(np.float32)
parlak        = np.clip(float_goruntu * 1.3, 0, 255).astype(np.uint8)
print(f"\nOrijinal ortalama : {goruntu.mean():.1f}")
print(f"Parlak ortalama   : {parlak.mean():.1f}")
print(f"Parlak max        : {parlak.max()}")  # 255'i aşmamalı
```

**Açıklama:**
`uint8` ile doğrudan `* 1.3` yapılırsa 200 * 1.3 = 260 değeri `uint8`'e sığmadığından taşar ve 4 döner. `float32`'ye çevirdikten sonra `np.clip(result, 0, 255).astype(np.uint8)` güvenli dönüşüm sağlar.

---

### Çözüm 1.7 — Tekrarlayan Desen

**Çözüm yaklaşımı:**
`np.tile` deseni tekrarlar, `np.repeat` elemanları tekrarlar.

**Kod:**
```python
import numpy as np

desen = np.array([1, 0, -1])

# tile: [1,0,-1,1,0,-1,...] → 30 eleman
tile_dizisi = np.tile(desen, 10)
print(f"tile (desen 10 kez): {tile_dizisi}")
print(f"  Uzunluk: {len(tile_dizisi)}, Toplam: {tile_dizisi.sum()}")

# repeat: [1,1,1,1,1,0,0,0,0,0,-1,-1,-1,-1,-1] → 15 eleman
repeat_dizisi = np.repeat(desen, 5)
print(f"\nrepeat (her eleman 5 kez): {repeat_dizisi}")
print(f"  Uzunluk: {len(repeat_dizisi)}, Toplam: {repeat_dizisi.sum()}")

print("\nNeden her ikisinin toplamı da 0?")
print(f"  tile: 10 × (1+0-1) = 10 × 0 = {tile_dizisi.sum()}")
print(f"  repeat: 5 × (1+0-1) = 5 × 0 = {repeat_dizisi.sum()}")
```

**Açıklama:**
`tile(desen, 10)` çıktısı `[1, 0, -1, 1, 0, -1, ...]` şeklinde deseni tekrarlar. `repeat(desen, 5)` ise `[1, 1, 1, 1, 1, 0, 0, 0, 0, 0, -1, -1, -1, -1, -1]` üretir — her eleman kendi içinde tekrarlanır. Her iki durumda da `1 + 0 + (-1) = 0` toplamı korunur.

---

### Çözüm 1.8 — Matrisin Alt ve Üst Üçgeni

**Çözüm yaklaşımı:**
`np.tril` ve `np.triu` fonksiyonları ile üçgen matrisler oluştur; köşegen sorunu `k` parametresiyle çözülür.

**Kod:**
```python
import numpy as np

matris = np.ones((6, 6), dtype=int)

alt    = np.tril(matris)       # alt üçgen, köşegen dahil
ust    = np.triu(matris)       # üst üçgen, köşegen dahil

print("Alt üçgen:\n", alt)
print("\nÜst üçgen:\n", ust)

# Toplam: köşegen iki kez sayılmış → 2'dir, orijinal 1'dir
toplam_ham = alt + ust
print(f"\nalt + ust köşegen değeri: {toplam_ham[0,0]}")  # 2

# Düzeltme: bir üçgenden köşegeni çıkar
kosegen = np.diag(np.diag(matris))   # yalnızca köşegen elemanları içeren matris
toplam_duzeltilmis = alt + ust - kosegen

dogru_mu = np.all(toplam_duzeltilmis == matris)
print(f"\nalt + ust - köşegen == orijinal? {dogru_mu}")
```

**Açıklama:**
`tril` ve `triu` varsayılan olarak köşegeni dahil eder. Toplamda köşegen iki kez eklendiğinden sonuçta köşegendeki değerler 2, diğerleri 1 olur. Bunu düzeltmek için bir kopyayı çıkarmak gerekir. `np.tril(matris, k=-1)` ile köşegen dahil edilmeden de alınabilir.

---

## BLOK 2 — Orta Çözümler

---

### Çözüm 2.1 — Broadcasting ile Mesafe Tablosu

**Çözüm yaklaşımı:**
Diziyi `(n,1)` ve `(1,n)` şeklinde yeniden biçimlendirerek çıkarma işlemini broadcasting'e bırak.

**Kod:**
```python
import numpy as np

dizi = np.arange(8)

# Broadcasting için farklı boyutlar
satir_vektoru = dizi.reshape(8, 1)   # (8,1)
sutun_vektoru = dizi.reshape(1, 8)   # (1,8)

mesafe = np.abs(satir_vektoru - sutun_vektoru)   # (8,8)

print("Mesafe Matrisi:")
print(mesafe)

# Doğrulamalar
print(f"\nEn büyük mesafe: {mesafe.max()}")
print(f"Köşegen sıfır mı? {np.all(np.diag(mesafe) == 0)}")
print(f"Simetrik mi? {np.all(mesafe == mesafe.T)}")
```

**Açıklama:**
`(8,1) - (1,8)` işlemi broadcasting ile `(8,8)` matris üretir. `mesafe[i,j] = |dizi[i] - dizi[j]|` formülünü temsil eder. Köşegen sıfır çünkü bir sayının kendisiyle farkı sıfırdır; simetri çünkü `|a-b| == |b-a|` doğrudur.

---

### Çözüm 2.2 — Veri Temizleme Pipeline'ı

**Çözüm yaklaşımı:**
Aralık dışı değerleri NaN yap → medyanı hesapla → NaN'ları doldur → raporla.

**Kod:**
```python
import numpy as np

olcumler = np.array([23.1, 24.5, 999.0, 22.8, 23.9, -50.0, 25.1,
                      24.2, 23.7, 999.0, 24.8, 23.5, 22.9, 25.3, 24.1])

print("Orijinal:", olcumler)

# 1. Aralık dışını NaN yap
temiz = olcumler.copy().astype(float)
temiz[(temiz < 0) | (temiz > 50)] = np.nan
print("NaN işlenmiş:", temiz)

# 2. Geçerli değerlerin medyanı (NaN yoksay)
medyan = np.nanmedian(temiz)
print(f"Medyan (geçerli): {medyan:.2f}")

# 3. NaN'ları medyanla doldur
temiz[np.isnan(temiz)] = medyan
print("Doldurulmuş:", temiz.round(2))

# 4. İstatistikler
print(f"\nMin: {temiz.min():.2f}, Max: {temiz.max():.2f}, Ort: {temiz.mean():.2f}")

# 5. Yan yana matris
karsilastirma = np.column_stack([olcumler, temiz])
print("\nOrijinal | Temizlenmiş")
for orijinal, temizlenmis in karsilastirma:
    flag = " ← düzeltildi" if orijinal != temizlenmis else ""
    print(f"  {orijinal:7.1f}  | {temizlenmis:7.2f}{flag}")
```

**Açıklama:**
NaN doldurma öncesinde medyanı hesaplamak kritiktir; aksi takdirde hatalı değerler medyanı bozar. `np.nanmedian` NaN değerleri hesaplamadan dışlar. `np.column_stack` iki 1D diziyi yan yana 2D matrise çevirir.

---

### Çözüm 2.3 — Satranç Tahtası Deseni

**Çözüm yaklaşımı:**
2×2 temel desen `np.tile` ile genişletilir; veya indeks toplamının çift/tek olması kontrol edilir.

**Kod:**
```python
import numpy as np

# Yöntem 1: tile
temel = np.array([[0, 1], [1, 0]])
tahta = np.tile(temel, (4, 4))

# Yöntem 2: indeks toplamı
tahta2 = np.zeros((8, 8), dtype=int)
satirlar, sutunlar = np.indices((8, 8))
tahta2[(satirlar + sutunlar) % 2 == 1] = 1

print("Satranç Tahtası:")
print(tahta)
print(f"\nSiyah kare sayısı: {(tahta == 0).sum()}")
print(f"Beyaz kare sayısı: {(tahta == 1).sum()}")

# Herhangi 4×4 köşe (sol üst)
kose = tahta[:4, :4]
print(f"\nSol üst 4×4 köşe:\n{kose}")

# Flip doğrulaması
yatay_flip = np.flip(tahta, axis=1)
dikey_flip = np.flip(tahta, axis=0)
print(f"\nYatay flip == orijinal? {np.array_equal(yatay_flip, tahta)}")
print(f"Dikey flip == orijinal? {np.array_equal(dikey_flip, tahta)}")
# Satranç tahtası deseninde flip orijinale eşit DEĞİLDİR (renkler yer değiştirir)
```

**Açıklama:**
`np.tile(temel, (4,4))` 2×2 deseni hem yatay hem dikey 4 kez tekrarlar. Alternatif: `(satır + sütun) % 2 == 1` koşulu satır ve sütun toplamı tek olan hücreleri beyaz yapar. Satranç deseninde flip sonucu orijinale eşit değildir çünkü renkler yer değiştirir.

---

### Çözüm 2.4 — Çalışan İstatistikleri

**Çözüm yaklaşımı:**
Rastgele veri oluştur, departman bazlı döngü ve koşullu maskelemeler uygula.

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(21)
n   = 50

maas       = rng.integers(4000, 12001, n)
yas        = rng.integers(22, 61, n)
departman  = rng.integers(0, 4, n)
dep_isimleri = ['Mühendislik', 'Pazarlama', 'Finans', 'İK']

print("=== DEPARTMAN BAZLI ORTALAMA MAAŞ ===")
for d in range(4):
    mask = departman == d
    print(f"  {dep_isimleri[d]:15s}: {maas[mask].mean():.0f} TL  ({mask.sum()} çalışan)")

print(f"\n=== 35 YAŞ ÜZERİ ORTALAMA MAAŞ ===")
mask_35    = yas > 35
print(f"  Ortalama: {maas[mask_35].mean():.0f} TL  ({mask_35.sum()} kişi)")

print(f"\n=== EN YÜKSEK MAAŞLI ÇALIŞAN ===")
en_yuksek_idx = maas.argmax()
print(f"  Maaş: {maas[en_yuksek_idx]} TL, Yaş: {yas[en_yuksek_idx]}, "
      f"Departman: {dep_isimleri[departman[en_yuksek_idx]]}")

print(f"\n=== ORTALAMANIN ÜZERİNDE OLANLAR ===")
genel_ort   = maas.mean()
ust_oran    = (maas > genel_ort).mean() * 100
print(f"  Genel ortalama: {genel_ort:.0f} TL")
print(f"  Üstünde oran  : %{ust_oran:.0f}")
```

**Açıklama:**
`departman == d` her departman için boolean maske üretir. `mask.sum()` boolean dizideki True sayısını verir. `(maas > genel_ort).mean()` True değerlerin (1) ortalaması, yani oranıdır.

---

### Çözüm 2.5 — Hareketli Ortalama

**Çözüm yaklaşımı:**
`np.convolve` ile uniform kernel kullan; `mode='valid'` ile sınır etkisinden kaçın.

**Kod:**
```python
import numpy as np

rng  = np.random.default_rng(55)
veri = rng.normal(50, 10, 200)

def hareketli_ort(dizi, pencere, mode='valid'):
    kernel = np.ones(pencere) / pencere
    return np.convolve(dizi, kernel, mode=mode)

ort_7  = hareketli_ort(veri, 7)
ort_15 = hareketli_ort(veri, 15)

# mode='valid' ile boyutlar farklı; ortak uzunluk için hizala
min_boy = min(len(ort_7), len(ort_15))
fark    = np.abs(ort_7[:min_boy] - ort_15[:min_boy])

maks_fark_idx = np.argmax(fark)
print(f"7-elemanlı ort uzunluğu : {len(ort_7)}")
print(f"15-elemanlı ort uzunluğu: {len(ort_15)}")
print(f"\nEn büyük fark           : {fark.max():.3f}")
print(f"En büyük farkın indeksi : {maks_fark_idx}")
print(f"O noktadaki 7-ort değeri: {ort_7[maks_fark_idx]:.3f}")
print(f"O noktadaki 15-ort değeri:{ort_15[maks_fark_idx]:.3f}")
```

**Açıklama:**
`mode='valid'` çıktı yalnızca kernel'in tam oturduğu pozisyonları içerir; bu nedenle n=200 giriş ve pencere=7 için çıktı 194 (200-7+1) elemanlı olur. İki farklı uzunluktaki çıktıyı karşılaştırmak için `min_boy` ile kırpma yapılır.

---

### Çözüm 2.6 — Normalize ve Ölçekleme

**Çözüm yaklaşımı:**
Her normalizasyon yöntemi farklı min/max belirler; hepsi aynı `(x - min) / (max - min) * 255` formülü kullanır.

**Kod:**
```python
import numpy as np

rng  = np.random.default_rng(33)
data = rng.uniform(0, 500, (100, 100))

def olcekle(arr, v_min, v_max):
    """Veriyi [v_min, v_max] aralığından [0, 255]'e ölçekle."""
    arr_clip = np.clip(arr, v_min, v_max)
    scaled   = (arr_clip - v_min) / (v_max - v_min) * 255
    return scaled.astype(np.uint8)

# Yöntem 1: Min-max
y1 = olcekle(data, data.min(), data.max())

# Yöntem 2: Yüzdelik dilimleme
p2, p98 = np.percentile(data, [2, 98])
y2 = olcekle(data, p2, p98)

# Yöntem 3: Manuel kırpma aralığı
y3 = olcekle(data, 100, 400)

for isim, y in [("Min-Max", y1), ("P2-P98", y2), ("100-400 kırp", y3)]:
    print(f"{isim:15s}: dtype={y.dtype}  "
          f"min={y.min():3d}  max={y.max():3d}  ort={y.mean():.1f}")
```

**Açıklama:**
Min-max normalizasyon tüm veri aralığını 0-255'e dönüştürür — aykırı değerler diğerlerini sıkıştırır. Yüzdelik dilim yöntemi aşırı uç değerlerin etkisini bastırır. Manuel kırpma belirli bir değer aralığına odaklanır; bu aralık dışındaki değerler sınır değerlerine çekmeye zorlanır.

---

### Çözüm 2.7 — Kümülatif Analiz

**Çözüm yaklaşımı:**
`np.cumsum`, `np.diff` ve yüzde hesaplamayı birleştir.

**Kod:**
```python
import numpy as np

satis = np.array([120, 85, 143, 167, 201, 178, 233, 256, 189, 212, 245, 310])
aylar = ['Oca','Şub','Mar','Nis','May','Haz','Tem','Ağu','Eyl','Eki','Kas','Ara']

# 1. Kümülatif toplam
kumulatif = np.cumsum(satis)
print("Kümülatif Satış:")
for ay, s, k in zip(aylar, satis, kumulatif):
    print(f"  {ay}: {s:4d}  →  Birikimli: {k:5d}")

# 2. Aylık büyüme oranı
buyume = (np.diff(satis) / satis[:-1]) * 100
print("\nAylık Büyüme Oranı (%):")
for i, b in enumerate(buyume):
    print(f"  {aylar[i+1]}: {b:+.1f}%")

# 3. İlk 6 ay oranı
ilk_alti_oran = satis[:6].sum() / satis.sum() * 100
print(f"\nİlk 6 ay yıllık toplamın %{ilk_alti_oran:.1f}'ini oluşturuyor")

# 4. En yüksek büyüme
en_yuksek_idx = np.argmax(buyume)
print(f"\nEn yüksek büyüme: {aylar[en_yuksek_idx+1]} (%{buyume[en_yuksek_idx]:.1f})")
```

**Açıklama:**
`np.diff` her ardışık çift için `[n+1] - [n]` farkını hesaplar; bu nedenle 12 elemanlı diziden 11 elemanlı büyüme oranı elde edilir. İlk ay büyüme oranı hesaplanamaz (önceki ay yok), bu yüzden `buyume[0]` Şubat'ın Ocak'a göre değişimidir.

---

### Çözüm 2.8 — Matris Manipülasyonu

**Çözüm yaklaşımı:**
`argsort` ile sıralama indeksleri → `keepdims=True` ile broadcasting → `isclose` ile doğrulama.

**Kod:**
```python
import numpy as np

matris = np.arange(1, 13).reshape(3, 4)
print("Orijinal:\n", matris)

# 1. Satır toplamlarına göre sırala
satir_toplamlari = matris.sum(axis=1)
siralama_idxleri = np.argsort(satir_toplamlari)
sirali_matris    = matris[siralama_idxleri]
print(f"\nSatır toplamları: {satir_toplamlari}")
print(f"Sıralama indeksleri: {siralama_idxleri}")
print("Sıralı matris:\n", sirali_matris)

# 2. Satır bazlı normalleştir (her satır kendi ortalamasına bölünsün)
satirlar_ort = sirali_matris.mean(axis=1, keepdims=True)   # (3,1) — broadcasting için
normalize    = sirali_matris / satirlar_ort
print("\nNormalize matris:\n", normalize.round(3))

# 3. Doğrulama: her satır ortalaması ≈ 1.0
satir_ortalamalari = normalize.mean(axis=1)
print(f"\nSatır ortalamaları: {satir_ortalamalari}")
print(f"Her satır ort ≈ 1? {np.all(np.isclose(satir_ortalamalari, 1.0))}")
```

**Açıklama:**
`mean(axis=1, keepdims=True)` `(3,)` yerine `(3,1)` döndürür. Bu, `(3,4) / (3,1)` broadcasting'inin doğru çalışmasını sağlar. `keepdims=False` olsaydı `(3,4) / (3,)` hata verirdi. `np.isclose` float point hatası toleransıyla karşılaştırma yapar.

---

### Çözüm 2.9 — İki Boyutlu Histogram

**Çözüm yaklaşımı:**
`np.histogram2d` ile 2D yoğunluk, `unravel_index` ile en yoğun hücre, `corrcoef` ile korelasyon.

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(88)
X   = rng.normal(0, 1, 500)
Y   = 0.7 * X + rng.normal(0, 0.5, 500)

# 2D histogram
hist, x_kenar, y_kenar = np.histogram2d(X, Y, bins=20)

# En yoğun hücre
maks_idx_duz = np.argmax(hist)
maks_idx_2d  = np.unravel_index(maks_idx_duz, hist.shape)
x_merkez = (x_kenar[maks_idx_2d[0]] + x_kenar[maks_idx_2d[0]+1]) / 2
y_merkez = (y_kenar[maks_idx_2d[1]] + y_kenar[maks_idx_2d[1]+1]) / 2

print(f"En yoğun ızgara hücresi indeksi: {maks_idx_2d}")
print(f"Yaklaşık koordinat: x ≈ {x_merkez:.2f}, y ≈ {y_merkez:.2f}")
print(f"Bu hücredeki nokta sayısı: {hist[maks_idx_2d]:.0f}")

# Korelasyon
r = np.corrcoef(X, Y)[0, 1]
print(f"\nKorelasyon katsayısı (X, Y): {r:.4f}")
print(f"(0.7 katsayısından beklenen yaklaşık korelasyon: ~0.81)")
```

**Açıklama:**
`np.argmax(hist)` düzleştirilmiş dizinin maksimum indeksini verir. `np.unravel_index(idx, shape)` bunu 2D indekse çevirir. Hücrenin merkez koordinatı kenar noktaları arasının ortalamasıdır. `corrcoef` X ve Y'yi birlikte değerlendirip 2×2 korelasyon matrisi döndürür; `[0,1]` öğesi X-Y korelasyonudur.

---

### Çözüm 2.10 — Özyinelemesiz Fibonacci

**Çözüm yaklaşımı:**
NumPy dizisiyle döngü veya matris kuvveti yöntemi.

**Kod:**
```python
import numpy as np

# Yöntem 1: NumPy dizisi + küçük döngü
fib = np.zeros(20, dtype=np.int64)
fib[0], fib[1] = 0, 1
for i in range(2, 20):
    fib[i] = fib[i-1] + fib[i-2]

print("İlk 20 Fibonacci sayısı:")
print(fib)

# Ardışık oranlar
oranlar = fib[1:] / fib[:-1]
altin_oran = (1 + np.sqrt(5)) / 2
print(f"\nArdışık oranların son değeri: {oranlar[-1]:.10f}")
print(f"Altın oran (φ)              : {altin_oran:.10f}")
print(f"Yakınsıyor mu?              : {np.isclose(oranlar[-1], altin_oran)}")

# Çift Fibonacci sayıları
cift_fib = fib[fib % 2 == 0]
print(f"\nÇift Fibonacci sayıları: {cift_fib}")
```

**Açıklama:**
Fibonacci dizisi her terimi önceki iki terimin toplamı olduğundan vektörel hesaplama doğrudan mümkün değildir; küçük bir döngü gerekir. Ancak sonuçlar NumPy dizisinde saklanır, istatistik ve filtreleme işlemleri vektörel yapılır. Ardışık oranlar φ'ya üstel hızda yaklaşır; n büyüdükçe hata `φ^(-2n)` ile azalır.

---

## BLOK 3 — Zor Çözümler

---

### Çözüm 3.1 — Görüntü Mozaik Oluşturucu

**Çözüm yaklaşımı:**
128×128 görüntüyü `(8, 16, 8, 16)` şeklinde yeniden biçimlendirip blok ortalamalarını `axis=(1,3)` ile hesapla, broadcasting ile geri yay.

**Kod:**
```python
import numpy as np

rng    = np.random.default_rng(5)
goruntu = rng.integers(0, 256, (128, 128), dtype=np.uint8).astype(np.float32)

BLOK = 16   # blok boyutu
IZGARAH = IZGARAW = 128 // BLOK  # 8×8 ızgara

# 4D yeniden biçimlendirme: (8, 16, 8, 16)
yeniden = goruntu.reshape(IZGARAH, BLOK, IZGARAW, BLOK)

# Her bloğun ortalaması: axis=(1,3) → (8,8)
blok_ort = yeniden.mean(axis=(1, 3))

# Geri yaymak için tekrarla
mozaik = np.repeat(np.repeat(blok_ort, BLOK, axis=0), BLOK, axis=1)

# PSNR hesabı
mse   = ((goruntu - mozaik) ** 2).mean()
psnr  = 10 * np.log10(255**2 / mse)

print(f"Blok boyutu   : {BLOK}×{BLOK}")
print(f"Orijinal ort  : {goruntu.mean():.1f}")
print(f"Mozaik ort    : {mozaik.mean():.1f}")
print(f"MSE           : {mse:.2f}")
print(f"PSNR          : {psnr:.2f} dB")
print(f"\nEn yüksek blok ortalaması: {blok_ort.max():.1f}")
print(f"En düşük blok ortalaması : {blok_ort.min():.1f}")
```

**Açıklama:**
`reshape(8, 16, 8, 16)` görüntüyü `[blok_satır, piksel_satır, blok_sütun, piksel_sütun]` düzenine sokar. `mean(axis=(1,3))` her bloğun tüm piksel değerlerini ortalar; `(8,8)` matris kalır. `np.repeat` her ortalama değeri BLOK kez tekrarlayarak orijinal boyutu geri üretir.

---

### Çözüm 3.2 — Monte Carlo ile Alan Hesabı

**Çözüm yaklaşımı:**
Her simülasyon vektörel; N noktayı tek seferde oluştur ve koşul maskesiyle işle.

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(99)

print("=== Pi Tahmini ===")
N = 1_000_000
x = rng.uniform(-1, 1, N)
y = rng.uniform(-1, 1, N)
pi_tahmin = 4 * ((x**2 + y**2) <= 1).mean()
print(f"Pi tahmini (N={N:,}): {pi_tahmin:.6f}  (gerçek: {np.pi:.6f})")

print("\n=== y=x² Altı Alan (0-1) ===")
x2 = rng.uniform(0, 1, N)
y2 = rng.uniform(0, 1, N)
alan_tahmin = (y2 <= x2**2).mean()
print(f"Alan tahmini (N={N:,}): {alan_tahmin:.6f}  (gerçek: {1/3:.6f})")

print("\n=== Nokta Sayısı — Hata İlişkisi ===")
print(f"{'N':>8} | {'Pi Tahmin':>10} | {'Hata':>10}")
print("-" * 35)
for n_log in [1, 2, 3, 4, 5]:
    n = 10**n_log
    x_n = rng.uniform(-1, 1, n)
    y_n = rng.uniform(-1, 1, n)
    pi_n = 4 * ((x_n**2 + y_n**2) <= 1).mean()
    hata = abs(pi_n - np.pi)
    print(f"{n:>8,} | {pi_n:>10.6f} | {hata:>10.6f}")
```

**Açıklama:**
`(x**2 + y**2) <= 1` koşulu tüm N noktayı aynı anda değerlendirir. `.mean()` True değerlerin oranını verir; daire içindeki oran π/4'e eşittir. N büyüdükçe hata yaklaşık `1/√N` hızında azalır — 100× daha fazla nokta hatayı ~10× düşürür.

---

### Çözüm 3.3 — K-Means Benzeri Kümeleme

**Çözüm yaklaşımı:**
Broadcasting ile mesafe hesabı, `argmin` ile atama, `mean` ile merkez güncelleme.

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(42)

# Küme verisi oluştur
A = rng.normal([2, 2], 0.8, (50, 2))
B = rng.normal([7, 3], 1.0, (50, 2))
C = rng.normal([4, 8], 0.7, (50, 2))
noktalar       = np.vstack([A, B, C])     # (150, 2)
gercek_etikler = np.array([0]*50 + [1]*50 + [2]*50)

# Rastgele başlangıç merkezleri (gerçek noktalardan seç)
merkez_idx = rng.choice(150, size=3, replace=False)
merkezler  = noktalar[merkez_idx].copy()

print("K-Means Benzeri Algoritma")
print(f"Başlangıç merkezleri:\n{merkezler.round(2)}\n")

for iterasyon in range(50):
    # Mesafe hesabı: (150,1,2) - (1,3,2) → (150,3,2) → (150,3)
    fark      = noktalar[:, np.newaxis, :] - merkezler[np.newaxis, :, :]
    mesafeler = np.sqrt((fark**2).sum(axis=2))   # (150, 3)

    # Her noktayı en yakın merkeze ata
    atamalar = np.argmin(mesafeler, axis=1)   # (150,)

    # Yeni merkez hesapla
    yeni_merkezler = np.array([
        noktalar[atamalar == k].mean(axis=0) for k in range(3)
    ])

    # Yakınsama kontrolü
    if np.allclose(merkezler, yeni_merkezler, atol=1e-4):
        print(f"Yakınsama: {iterasyon+1}. iterasyonda")
        break
    merkezler = yeni_merkezler

print(f"\nNihai merkezler:\n{merkezler.round(3)}")
print(f"Gerçek merkezler: [2,2], [7,3], [4,8]")

# Doğruluk (etiket eşleştirmesi basit; en yakın gerçek merkeze bak)
print(f"\nHer kümedeki nokta sayısı: {[(atamalar==k).sum() for k in range(3)]}")
```

**Açıklama:**
`noktalar[:, np.newaxis, :] - merkezler[np.newaxis, :, :]` broadcasting ile her noktanın her merkeze uzaklığı `(150,3,2)` tensörünü üretir. `.sum(axis=2)` XY farkının karesini toplar; `sqrt` ile Öklid mesafesi elde edilir. `argmin(axis=1)` her noktanın en yakın merkez indeksini verir.

---

### Çözüm 3.4 — Zaman Serisi Anomali Tespiti

**Çözüm yaklaşımı:**
Hareketli ortalama ve standart sapma ile ±3σ bandı oluştur; bant dışını anomali say.

**Kod:**
```python
import numpy as np

rng = np.random.default_rng(77)

gunler     = np.arange(365)
mevsimsel  = 15 + 10 * np.sin(2*np.pi*gunler/365 - np.pi/2)
gurultu    = rng.normal(0, 2, 365)
sicaklik   = mevsimsel + gurultu

# Gerçek anomaliler ekle
anomali_gunler = rng.choice(365, size=8, replace=False)
sicaklik[anomali_gunler] += rng.uniform(30, 50, 8)
anomali_set = set(anomali_gunler)

PENCERE = 15

# Hareketli ortalama
kernel = np.ones(PENCERE) / PENCERE
hm_ort = np.convolve(sicaklik, kernel, mode='same')

# Hareketli standart sapma: sqrt(E[X²] - E[X]²)
hm_x2   = np.convolve(sicaklik**2, kernel, mode='same')
hm_std  = np.sqrt(np.maximum(hm_x2 - hm_ort**2, 0))   # sayısal hata önlemi

# Anomali tespiti
ust_sinir    = hm_ort + 3 * hm_std
alt_sinir    = hm_ort - 3 * hm_std
tespit_mask  = (sicaklik > ust_sinir) | (sicaklik < alt_sinir)
tespit_set   = set(np.where(tespit_mask)[0])

# Performans raporu
dogru_tespit    = tespit_set & anomali_set
yanlis_pozitif  = tespit_set - anomali_set
kacirilan       = anomali_set - tespit_set

print("=== ANOMALİ TESPİT RAPORU ===")
print(f"Gerçek anomali sayısı  : {len(anomali_set)}")
print(f"Tespit edilen          : {len(tespit_set)}")
print(f"Doğru tespit (TP)      : {len(dogru_tespit)}")
print(f"Yanlış pozitif (FP)    : {len(yanlis_pozitif)}")
print(f"Kaçırılan (FN)         : {len(kacirilan)}")
precision = len(dogru_tespit) / max(len(tespit_set), 1)
recall    = len(dogru_tespit) / max(len(anomali_set), 1)
print(f"\nPrecision: {precision:.2f}")
print(f"Recall   : {recall:.2f}")
```

**Açıklama:**
Hareketli standart sapma `E[X²] - (E[X])²` formülüyle hesaplanır; her ikisi de `convolve` ile elde edilir. `np.maximum(..., 0)` sayısal yuvarlama hatası nedeniyle negatif değer çıkmasını önler. `set` operasyonları küme teorisiyle doğruluk metriklerini hesaplar.

---

### Çözüm 3.5 — Görüntü Histogramı Eşitleme

**Çözüm yaklaşımı:**
Histogram → CDF → normalize CDF → `np.interp` ile piksel yeniden eşleme.

**Kod:**
```python
import numpy as np

rng    = np.random.default_rng(11)
goruntu = rng.integers(80, 181, (100, 100), dtype=np.uint8)

print(f"Orijinal: min={goruntu.min()}, max={goruntu.max()}, std={goruntu.std():.2f}")

# Histogram
hist, sinirlar = np.histogram(goruntu.flatten(), bins=256, range=(0, 256))

# CDF
cdf         = np.cumsum(hist)
cdf_normalize = cdf / cdf[-1]   # [0,1] aralığına normalize

# Piksel yeniden eşleme
# np.interp: her piksel değerini CDF aracılığıyla yeni değere eşle
piksel_degerleri  = np.arange(256)
esitlenmis_gorutu = np.interp(goruntu.flatten(),
                               piksel_degerleri,
                               cdf_normalize * 255).astype(np.uint8).reshape(100, 100)

print(f"Eşitlenmiş: min={esitlenmis_gorutu.min()}, max={esitlenmis_gorutu.max()}, "
      f"std={esitlenmis_gorutu.std():.2f}")

# Histogramları karşılaştır
hist_orig, _ = np.histogram(goruntu, bins=10)
hist_esit, _ = np.histogram(esitlenmis_gorutu, bins=10)
print("\nOrijinal histogram (10 bin):", hist_orig)
print("Eşitlenmiş histogram       :", hist_esit)
print("\nAçıklama: Eşitlenmişin dağılımı daha homojendir.")
```

**Açıklama:**
Histogram eşitleme CDF'yi doğrusal bir fonksiyona dönüştürür; bu, piksel dağılımını 0-255'e dengeli yayar. `np.interp(x, xp, fp)` her piksel değerini `xp`-`fp` eşleme tablosuna göre enterpolasyon yapar. Sonuç: standart sapma artar (kontrast iyileşir), min-max aralığı genişler.

---

### Çözüm 3.6 — Matris Faktörizasyonu ile Boyut İndirgeme

**Çözüm yaklaşımı:**
`np.linalg.svd` ile ayrıştır; ilk k tekil değerle düşük ranklı yaklaşım üret.

**Kod:**
```python
import numpy as np

np.random.seed(42)
W = np.random.rand(10, 2)
H = np.random.rand(2, 5)
X = W @ H + 0.01 * np.random.rand(10, 5)

print("Orijinal matris X:")
print(X.round(3))

# SVD
U, S, Vt = np.linalg.svd(X, full_matrices=False)
print(f"\nTekil değerler: {S.round(4)}")
print(f"U: {U.shape}, S: {S.shape}, Vt: {Vt.shape}")

# k=2 ile düşük ranklı yaklaşım
k = 2
X_approx = U[:, :k] @ np.diag(S[:k]) @ Vt[:k, :]
print(f"\nk={k} ile yaklaşım (ilk satır):")
print(f"Orijinal : {X[0].round(4)}")
print(f"Yaklaşım : {X_approx[0].round(4)}")

# Frobenius normu
hata = np.linalg.norm(X - X_approx, 'fro')
print(f"\nFrobenius norm hatası: {hata:.6f}")

# Açıklanan varyans
aciklanan_varyans = (S[:k]**2).sum() / (S**2).sum() * 100
print(f"İlk {k} bileşen açıklanan varyans: %{aciklanan_varyans:.2f}")
```

**Açıklama:**
SVD X'i U × diag(S) × Vt olarak ayrıştırır. İlk k tekil değer bilginin büyük kısmını taşır; kalan tekil değerler gürültüyü temsil eder. Frobenius normu matris elemanlarının kare toplamının köküdür — yaklaşımın ne kadar iyi olduğunu ölçer. Açıklanan varyans oranı PCA'daki "explained variance ratio"ya karşılık gelir.

---

### Çözüm 3.7 — Kelime Frekans Matrisi

**Çözüm yaklaşımı:**
TF-IDF ağırlığı → L2 normalize → kosinüs benzerlik matrisi.

**Kod:**
```python
import numpy as np

cumle_matrisi = np.array([
    [1, 0, 2, 1, 0, 3, 0, 1],
    [0, 2, 1, 0, 3, 0, 1, 2],
    [1, 1, 0, 2, 0, 1, 3, 0],
    [2, 0, 1, 0, 1, 2, 0, 1],
    [0, 3, 0, 1, 2, 0, 1, 1],
], dtype=np.float32)

n_cumle = cumle_matrisi.shape[0]

# 1. TF-IDF benzeri ağırlık
# Her kelimenin kaç cümlede geçtiği
cümle_sayisi_per_kelime = (cumle_matrisi > 0).sum(axis=0)   # (8,)
idf = np.log(n_cumle / (cümle_sayisi_per_kelime + 1))        # smoothing

tfidf = cumle_matrisi * idf   # broadcasting: (5,8) * (8,) → (5,8)
print("TF-IDF matrisi:")
print(tfidf.round(3))

# 2. L2 normalizasyon
normlar     = np.linalg.norm(tfidf, axis=1, keepdims=True)   # (5,1)
normalize   = tfidf / normlar

# 3. Kosinüs benzerlik matrisi
kosinus = normalize @ normalize.T   # (5,5)
print("\nKosinüs Benzerlik Matrisi:")
print(kosinus.round(3))

# 4. Birinci cümleye en benzer iki cümle
benzerlikler    = kosinus[0]       # 1. cümlenin diğerleriyle benzerliği
# İndeks 0 (kendisi) hariç en yüksek iki
en_benzer_idxs  = np.argsort(benzerlikler)[::-1][1:3]
print(f"\n1. cümleye en benzer cümleler:")
for idx in en_benzer_idxs:
    print(f"  Cümle {idx+1}: benzerlik = {benzerlikler[idx]:.4f}")
```

**Açıklama:**
IDF puanı az cümlede geçen kelimelere yüksek ağırlık verir (ayırt edicilik). L2 normalizasyon uzunluk farklarını ortadan kaldırır; uzun cümleler kısa cümlelere göre otomatik avantaj kazanmaz. Kosinüs benzerliği `normalize @ normalize.T` ile tek matris çarpımında tüm çiftler için hesaplanır; köşegen her zaman 1.0'dır (cümle kendisiyle tam benzer).

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `numpy-teori.md` · `numpy-uygulamalar.md` · `numpy-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
