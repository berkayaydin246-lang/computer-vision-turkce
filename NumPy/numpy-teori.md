# 📘 NumPy — Teorik Notlar

> **Modül:** Teori | **Seviye:** Başlangıç → Orta
> **İlgili dosyalar:** `numpy-uygulamalar.md` · `numpy-alistirmalar.md`

---

## BLOK 0 — Giriş Kartı

```
Kütüphane  : NumPy (Numerical Python)
Sürüm      : 1.24+ (güncel)
Lisans     : BSD
Kurulum    : pip install numpy
İçe aktarma: import numpy as np
```

**Ne yapar?**
NumPy, Python'da sayısal hesaplama için geliştirilmiş temel bir kütüphanedir. Çok boyutlu diziler üzerinde hızlı matematiksel işlemler yapmanızı sağlar. Veri bilimi, makine öğrenmesi, bilimsel hesaplama ve bilgisayarlı görü gibi alanlardaki Python ekosisteminin büyük bölümü NumPy üzerine inşa edilmiştir.

**Neden NumPy?**
Python'un yerleşik listelerini kullanarak da sayısal hesaplamalar yapabilirsiniz; ancak bu yol hem yavaş hem de zahmetlidir. NumPy, aynı işlemi onlarca kat daha hızlı yapar çünkü hesaplamalar C dilinde derlenmiş, optimize edilmiş kod bloklarıyla çalışır.

```python
import numpy as np

# Python listesi — yavaş, tek tek işlem
liste = [1, 2, 3, 4, 5]
ikiye_katlanmis = [x * 2 for x in liste]   # döngü gerekli

# NumPy dizisi — hızlı, vektörel işlem
dizi = np.array([1, 2, 3, 4, 5])
ikiye_katlanmis = dizi * 2                  # döngü gereksiz, tek satır
```

**Kullanıldığı alanlar:**

| Alan | Kullanım Amacı |
|------|---------------|
| Veri Bilimi | Veri temizleme, istatistik, tablo işlemleri (pandas'ın temeli) |
| Makine Öğrenmesi | Matris çarpımı, ağırlık güncellemeleri, vektör işlemleri |
| Bilgisayarlı Görü | Görüntüleri dizi olarak saklama ve işleme (OpenCV'nin temeli) |
| Bilimsel Hesaplama | Simülasyon, diferansiyel denklem çözümü, istatistik |
| Sinyal İşleme | Fourier dönüşümü, filtre uygulama |

**Bağımlı kütüphaneler:**
NumPy'yi doğrudan kullanmasanız bile, kullandığınız pek çok kütüphane arka planda NumPy'a dayanır: pandas, OpenCV, scikit-learn, TensorFlow, PyTorch, Matplotlib.

---

## BLOK 1 — Kurulum

```bash
# pip ile
pip install numpy

# conda ile
conda install numpy

# Bilimsel paket grubuyla birlikte (önerilir)
pip install numpy scipy matplotlib pandas
```

**Kurulum doğrulama:**
```python
import numpy as np
print(np.__version__)   # örn: 1.26.4
```

**⚠️ Uyarılar:**
- NumPy, OpenCV veya pandas kurulduğunda genellikle otomatik olarak yüklenir; tekrar kurmak gerekmez.
- `import numpy as np` kısaltması evrensel standarttır; başka isim kullanmaktan kaçının.
- Sanal ortam (venv/conda) kullanımı şiddetle önerilir.

---

## BLOK 2 — Temel Kavramlar

### NumPy'ın Düşünce Biçimi

NumPy'da her şey **dizi (array)** etrafında döner. Bir veri kümesini tek tek elemanlarla işlemek yerine tüm diziyi tek bir komutla işlersiniz. Bu yaklaşıma **vektörleştirme (vectorization)** denir.

```
Normal Python düşüncesi:
  "Her eleman için ayrı bir işlem yap"
  → for döngüsü

NumPy düşüncesi:
  "Tüm diziyi tek seferde işle"
  → dizi * 2  (tek komut, tüm elemanlar)
```

**Neden bu kadar hızlı?**
Python'un her döngü adımı için ayrı ayrı tip kontrolü, bellek yönetimi ve yorumlama maliyeti vardır. NumPy dizileri ise bellekte bitişik bloklar halinde saklanan aynı tipten elemanlardan oluşur; hesaplamalar derleme hızında çalışan C kodu tarafından yapılır. Bu fark, büyük veri setlerinde 10x–100x hız avantajına dönüşür.

---

### Kavram 1 — ndarray: NumPy'ın Temel Veri Yapısı

NumPy'ın çekirdeğinde **`ndarray`** (N-dimensional array — N boyutlu dizi) adlı veri yapısı bulunur. Tüm sayısal işlemler bu yapı üzerinde çalışır.

**ndarray'i Python listesinden ayıran özellikler:**

| Özellik | Python Listesi | NumPy ndarray |
|---------|---------------|---------------|
| Eleman tipi | Karışık olabilir | Tüm elemanlar aynı tip |
| Boyut | Yalnızca 1D | N boyutlu (1D, 2D, 3D...) |
| Hız | Yavaş | Hızlı (C ile derlendi) |
| Bellek | Dağınık | Bitişik blok |
| Matematiksel işlem | Manuel döngü | Vektörel, tek komut |

```python
import numpy as np

# Tek boyutlu dizi (1D)
a = np.array([1, 2, 3, 4, 5])

# İki boyutlu dizi (2D) — matris
b = np.array([[1, 2, 3],
              [4, 5, 6]])

# Üç boyutlu dizi (3D) — renkli görüntü gibi
c = np.array([[[1, 2], [3, 4]],
              [[5, 6], [7, 8]]])

print(a.shape)    # (5,)        → 5 elemanlı 1D dizi
print(b.shape)    # (2, 3)      → 2 satır, 3 sütun
print(c.shape)    # (2, 2, 2)   → 2×2×2 küp
```

---

### Kavram 2 — shape, dtype ve ndim

Her ndarray'in üç temel niteliği vardır:

```python
import numpy as np

img = np.zeros((480, 640, 3), dtype=np.uint8)

print(img.shape)    # (480, 640, 3)  → yükseklik × genişlik × kanal
print(img.dtype)    # uint8          → veri tipi
print(img.ndim)     # 3              → boyut sayısı
print(img.size)     # 921600         → toplam eleman sayısı (480×640×3)
print(img.nbytes)   # 921600         → bellekte kapladığı byte
```

**Yaygın `dtype` değerleri:**

| dtype | Aralık / Açıklama | Kullanım Yeri |
|-------|-------------------|---------------|
| `int32` | -2³¹ – 2³¹-1 | Sayma, indeks |
| `int64` | Çok büyük tam sayı | Büyük veri |
| `float32` | ±3.4×10³⁸ | Makine öğrenmesi modelleri |
| `float64` | ±1.7×10³⁰⁸ | Bilimsel hesaplama (varsayılan) |
| `uint8` | 0 – 255 | Görüntü pikselleri |
| `bool` | True / False | Maske, filtre |
| `complex64` | Karmaşık sayı | Sinyal işleme |

**Dtype neden önemlidir?**
Yanlış dtype seçimi hem bellek israfına hem de hesaplama hatalarına yol açar. Görüntüler için `uint8`, ağırlıklar için `float32`, sayaçlar için `int32` tipik seçimlerdir.

---

### Kavram 3 — Eksenler (Axes)

NumPy'da boyutlar **eksen (axis)** olarak adlandırılır. İşlemlerin hangi eksen boyunca yapılacağını belirtmek mümkündür.

```
2D dizi için:
axis=0  →  satırlar boyunca (aşağıya doğru)
axis=1  →  sütunlar boyunca (sağa doğru)

[[1, 2, 3],
 [4, 5, 6],
 [7, 8, 9]]

np.sum(A, axis=0) → [12, 15, 18]   (her sütunun toplamı)
np.sum(A, axis=1) → [6, 15, 24]    (her satırın toplamı)
np.sum(A)         → 45              (tüm elemanların toplamı)
```

```python
import numpy as np

A = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

print(np.sum(A, axis=0))   # [12 15 18] — sütun toplamları
print(np.sum(A, axis=1))   # [ 6 15 24] — satır toplamları
print(np.mean(A, axis=0))  # [4. 5. 6.] — sütun ortalamaları
```

---

### Kavram 4 — Kopyalama ve Görünüm (View vs Copy)

NumPy'da dizi dilimleme bir **görünüm (view)** döndürür, kopya değil. Bu, bellek verimliliği sağlar ama beklenmedik değişikliklere yol açabilir.

```python
import numpy as np

a = np.array([1, 2, 3, 4, 5])

# View — aynı bellek bloğu
b = a[1:4]
b[0] = 99
print(a)   # [1 99 3 4 5] → a da değişti!

# Kopya — bağımsız bellek
c = a[1:4].copy()
c[0] = 0
print(a)   # [1 99 3 4 5] → a değişmedi
```

**Ne zaman `.copy()` kullanmalı?**
Orijinal diziyi bozmadan alt dizi üzerinde işlem yapmak istediğinizde. Özellikle görüntü işleme pipeline'larında orijinali korumak için `img.copy()` yaygın kullanılır.

---

## BLOK 3 — Dizi Oluşturma

### Sıfırdan Dizi Oluşturma

```python
import numpy as np

# Sıfırlardan oluşan dizi
sifirlar = np.zeros((3, 4))           # 3×4 float64 sıfır matrisi
sifirlar_int = np.zeros((3, 4), dtype=np.int32)

# Birlerden oluşan dizi
birler = np.ones((2, 5))              # 2×5 float64 birler matrisi

# Sabit değerle dolu dizi
dolu = np.full((3, 3), 7)             # 3×3, tüm elemanlar 7
dolu_f = np.full((2, 3), 3.14)        # 3.14 ile dolu

# Birim matris (kimlik matrisi)
kimlik = np.eye(4)                     # 4×4 birim matris (köşegen=1, diğer=0)

# Boş dizi (başlatılmamış, çöp değer içerebilir — dikkatli kullanın)
bos = np.empty((2, 3))
```

---

### Aralık ve Düzenli Diziler

#### `np.arange(start, stop, step)`

**Ne yapar:** Python'un `range()` fonksiyonuna benzer, ancak NumPy dizisi döndürür ve float değerleri destekler.

**Döndürür:** 1D ndarray.

```python
import numpy as np

a = np.arange(10)            # [0 1 2 3 4 5 6 7 8 9]
b = np.arange(2, 10, 2)      # [2 4 6 8]
c = np.arange(0, 1, 0.25)    # [0.   0.25 0.5  0.75]
```

**⚠️ Dikkat:** `arange` ile float step kullanmak yuvarlama hatası nedeniyle beklenenden farklı eleman sayısı üretebilir. Float aralıklar için `linspace` tercih edilir.

---

#### `np.linspace(start, stop, num)`

**Ne yapar:** İki değer arasında eşit aralıklı `num` sayıda nokta üretir. `arange`'den farkı: adım boyutunu değil, eleman sayısını belirtirsiniz ve bitiş değeri dahildir.

**Döndürür:** 1D ndarray.

```python
import numpy as np

x = np.linspace(0, 1, 5)      # [0.   0.25 0.5  0.75 1.  ]  (5 nokta)
y = np.linspace(0, 2*np.pi, 100)   # 0 ile 2π arası 100 nokta (grafik çizimi için)
```

**Ne zaman kullanılır:** Grafik eksenleri oluşturmak, matematiksel fonksiyon değerlendirmek, eşit aralıklı test noktaları üretmek.

---

### Var Olan Veriden Dizi Oluşturma

#### `np.array(data, dtype=None)`

**Ne yapar:** Python listesi, tuple veya başka dizi benzeri yapıları ndarray'e dönüştürür.

```python
import numpy as np

# Listeden
a = np.array([1, 2, 3])
b = np.array([[1.0, 2.0], [3.0, 4.0]])

# dtype belirterek
c = np.array([1, 2, 3], dtype=np.float32)
d = np.array([0, 1, 0, 1], dtype=bool)

# Python listesi vs NumPy dizisi
liste = [1, 2, 3]
dizi  = np.array(liste)
print(type(liste))   # <class 'list'>
print(type(dizi))    # <class 'numpy.ndarray'>
```

---

### Rastgele Dizi Oluşturma

```python
import numpy as np

# 0-1 arası düzgün dağılım
rng = np.random.default_rng(seed=42)   # tekrarlanabilir sonuç için seed
a   = rng.random((3, 4))               # 3×4, float, [0, 1)

# Normal (Gaussian) dağılım — ortalama=0, std=1
b = rng.standard_normal((100,))

# Normal dağılım — ortalama ve std belirterek
c = rng.normal(loc=50, scale=10, size=(3, 3))

# Tam sayı rastgele (0-9 arası)
d = rng.integers(0, 10, size=(4, 4))

# Eski API (hâlâ yaygın kullanılır)
np.random.seed(42)
e = np.random.rand(3, 4)       # [0, 1) arası uniform
f = np.random.randint(0, 256, size=(480, 640, 3), dtype=np.uint8)  # örn. sahte görüntü
```

**`default_rng` neden tercih edilir?**
Yeni NumPy sürümlerinde `np.random.default_rng()` ile oluşturulan Generator nesnesi daha tutarlı ve thread-safe'dir. Yeni kod yazarken bu API tercih edilmelidir.

---

## BLOK 4 — Dizi İşlemleri

### İndeksleme ve Dilimleme

#### Temel İndeksleme

```python
import numpy as np

a = np.array([10, 20, 30, 40, 50])

print(a[0])     # 10   — ilk eleman
print(a[-1])    # 50   — son eleman
print(a[1:4])   # [20 30 40] — indeks 1'den 3'e kadar (4 dahil değil)
print(a[::2])   # [10 30 50] — her iki eleman

b = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

print(b[0, 0])     # 1    — ilk satır, ilk sütun
print(b[1, 2])     # 6    — ikinci satır, üçüncü sütun
print(b[0, :])     # [1 2 3] — tüm birinci satır
print(b[:, 1])     # [2 5 8] — tüm ikinci sütun
print(b[1:, 1:])   # [[5 6] [8 9]] — sağ alt köşe
```

#### Boolean İndeksleme (Maskeleme)

Koşul sağlayan elemanları seçmenin en verimli yoludur.

```python
import numpy as np

a = np.array([10, 25, 3, 47, 18, 60])

maske   = a > 20           # [False  True False  True False  True]
secilen = a[maske]         # [25 47 60]

# Tek satırda
buyukler = a[a > 20]       # [25 47 60]

# Birden fazla koşul
orta = a[(a > 15) & (a < 50)]   # [25 47 18]

# Değer değiştirme
a[a > 50] = 50   # 50'den büyük her değeri 50 yap (clipping)
print(a)          # [10 25  3 47 18 50]
```

#### Fancy (Gelişmiş) İndeksleme

```python
import numpy as np

a = np.array([10, 20, 30, 40, 50])

indeksler = [0, 2, 4]
print(a[indeksler])   # [10 30 50] — belirli indeksler

b = np.array([[1, 2, 3],
              [4, 5, 6],
              [7, 8, 9]])

satirlar  = [0, 1, 2]
sutunlar  = [2, 0, 1]
print(b[satirlar, sutunlar])   # [3 4 8] — (0,2), (1,0), (2,1) elemanları
```

---

### Yayım (Broadcasting)

Broadcasting, farklı şekillerdeki dizilerin birlikte işleme alınmasını sağlar. NumPy eksik boyutları otomatik olarak genişletir — ancak ek bellek kopyası oluşturmaz.

**Temel kural:** İki dizi soldan hizalanır; boyut sayıları eşleşmiyorsa küçük olan dizinin soluna 1 değerli boyutlar eklenir. Her boyutta boyutlar ya eşit olmalı ya da biri 1 olmalıdır.

```python
import numpy as np

# Skalar ile dizi
a = np.array([1, 2, 3])
print(a + 10)        # [11 12 13] — 10 tüm elemanlara eklendi

# 1D ile 2D
satir  = np.array([1, 2, 3])          # shape: (3,)
matris = np.array([[10], [20], [30]]) # shape: (3, 1)
print(matris + satir)
# [[11 12 13]
#  [21 22 23]
#  [31 32 33]]

# Gerçek kullanım: normalizasyon
veri = np.array([[1.0, 2.0, 3.0],
                 [4.0, 5.0, 6.0]])
ortalama = veri.mean(axis=0)   # [2.5 3.5 4.5] — sütun ortalamaları
normalize = veri - ortalama    # her satırdan ortalama çıkarıldı
```

**Broadcasting ne zaman başarısız olur?**
```python
a = np.array([1, 2, 3])      # shape (3,)
b = np.array([1, 2, 3, 4])   # shape (4,)
a + b    # ValueError — 3 ≠ 4, hiçbiri 1 değil
```

---

### Eleman Bazlı Matematiksel İşlemler

```python
import numpy as np

a = np.array([1.0, 4.0, 9.0, 16.0])
b = np.array([2.0, 2.0, 3.0, 4.0])

# Aritmetik
print(a + b)          # [ 3.  6. 12. 20.]
print(a - b)          # [-1.  2.  6. 12.]
print(a * b)          # [ 2.  8. 27. 64.]
print(a / b)          # [0.5 2.  3.  4. ]
print(a ** 2)         # [  1.  16.  81. 256.] — her elemanın karesi

# Matematiksel fonksiyonlar (eleman bazlı)
print(np.sqrt(a))     # [1. 2. 3. 4.]
print(np.log(a))      # doğal logaritma
print(np.exp(a))      # e üzeri her eleman
print(np.sin(a))      # sinüs (radyan cinsinden)
print(np.abs(np.array([-1, -2, 3])))  # [1 2 3] — mutlak değer

# Karşılaştırma (bool dizi döner)
print(a > 5)          # [False False  True  True]
print(a == b)         # [False  True False  True]
```

---

### İstatistiksel Fonksiyonlar

| Fonksiyon | Ne Yapar | Axis Kullanımı |
|-----------|----------|----------------|
| `np.sum(a)` | Toplam | `axis=0/1` |
| `np.mean(a)` | Ortalama | `axis=0/1` |
| `np.std(a)` | Standart sapma | `axis=0/1` |
| `np.var(a)` | Varyans | `axis=0/1` |
| `np.min(a)` | Minimum değer | `axis=0/1` |
| `np.max(a)` | Maksimum değer | `axis=0/1` |
| `np.argmin(a)` | Min değerin indeksi | `axis=0/1` |
| `np.argmax(a)` | Max değerin indeksi | `axis=0/1` |
| `np.median(a)` | Medyan | `axis=0/1` |
| `np.percentile(a, q)` | q. yüzdelik dilim | `axis=0/1` |
| `np.cumsum(a)` | Kümülatif toplam | — |

```python
import numpy as np

veri = np.array([[2, 8, 3],
                 [5, 1, 9],
                 [4, 6, 7]])

print(np.mean(veri))            # 5.0       — tüm dizi
print(np.mean(veri, axis=0))    # [3.67 5.0 6.33] — sütun ortalamaları
print(np.mean(veri, axis=1))    # [4.33 5.0 5.67] — satır ortalamaları
print(np.argmax(veri))          # 5         — en büyük değerin düz indeksi
print(np.argmax(veri, axis=1))  # [1 2 2]   — her satırın max sütunu
```

---

### Matris İşlemleri

```python
import numpy as np

A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])

# Eleman bazlı çarpma (her eleman kendi karşılığıyla çarpılır)
print(A * B)
# [[ 5 12]
#  [21 32]]

# Matris çarpımı (dot product)
print(A @ B)           # @ operatörü (Python 3.5+)
print(np.dot(A, B))    # aynı sonuç

# Transpoz
print(A.T)
# [[1 3]
#  [2 4]]

# Determinant ve ters matris
print(np.linalg.det(A))      # -2.0
print(np.linalg.inv(A))      # ters matris

# Özdeğer ve özvektör
ozdeger, ozvektor = np.linalg.eig(A)
```

---

## BLOK 5 — Dizi Biçimlendirme

### reshape ve flatten

#### `ndarray.reshape(new_shape)`

**Ne yapar:** Dizinin eleman sayısını değiştirmeden şeklini (shape) değiştirir. Verinin kendisi kopyalanmaz; yalnızca dizi görünümü değişir.

**Döndürür:** Yeni şekle sahip view veya copy.

```python
import numpy as np

a = np.arange(12)    # [0 1 2 3 4 5 6 7 8 9 10 11]

b = a.reshape(3, 4)
# [[ 0  1  2  3]
#  [ 4  5  6  7]
#  [ 8  9 10 11]]

c = a.reshape(2, 2, 3)   # 3D: 2×2×3

# -1 ile otomatik boyut hesaplama
d = a.reshape(-1, 4)     # -1 → NumPy hesaplar → (3, 4)
e = a.reshape(4, -1)     # → (4, 3)
f = a.reshape(1, -1)     # → (1, 12) — satır vektörü
g = a.reshape(-1, 1)     # → (12, 1) — sütun vektörü
```

**⚠️ Dikkat:** `reshape` yeni eleman sayısı ile eski eleman sayısı eşit olmak zorundadır; aksi hâlde `ValueError` alınır.

#### `ndarray.flatten()` ve `ndarray.ravel()`

```python
import numpy as np

b = np.array([[1, 2, 3],
              [4, 5, 6]])

duz_kopya = b.flatten()    # [1 2 3 4 5 6] — her zaman kopya döndürür
duz_view  = b.ravel()      # [1 2 3 4 5 6] — mümkünse view döndürür (daha hızlı)
```

---

### Birleştirme ve Ayırma

#### `np.concatenate`, `np.hstack`, `np.vstack`

```python
import numpy as np

a = np.array([[1, 2], [3, 4]])   # shape (2, 2)
b = np.array([[5, 6], [7, 8]])   # shape (2, 2)

# Yatay birleştirme (sütun sayısı artar)
yatay = np.hstack([a, b])
# [[1 2 5 6]
#  [3 4 7 8]]

# Dikey birleştirme (satır sayısı artar)
dikey = np.vstack([a, b])
# [[1 2]
#  [3 4]
#  [5 6]
#  [7 8]]

# Genel birleştirme (axis belirtilerek)
np.concatenate([a, b], axis=0)  # dikey (=vstack)
np.concatenate([a, b], axis=1)  # yatay (=hstack)
```

#### `np.stack` — Yeni boyut ekleyerek birleştirme

```python
import numpy as np

a = np.array([1, 2, 3])   # shape (3,)
b = np.array([4, 5, 6])   # shape (3,)

s0 = np.stack([a, b], axis=0)   # shape (2, 3) — satırlar olarak
s1 = np.stack([a, b], axis=1)   # shape (3, 2) — sütunlar olarak
```

---

### Transpoz ve Eksen Değiştirme

```python
import numpy as np

a = np.array([[1, 2, 3],
              [4, 5, 6]])   # shape (2, 3)

print(a.T)         # shape (3, 2) — transpoz
# [[1 4]
#  [2 5]
#  [3 6]]

# 3D dizide eksen değiştirme
img = np.zeros((480, 640, 3))         # H × W × C (OpenCV formatı)
img_t = np.transpose(img, (2, 0, 1))  # C × H × W (PyTorch formatı)
print(img_t.shape)   # (3, 480, 640)
```

---

### Tekrarlama ve Döşeme

```python
import numpy as np

a = np.array([1, 2, 3])

print(np.tile(a, 3))       # [1 2 3 1 2 3 1 2 3] — diziyi 3 kez tekrarla
print(np.repeat(a, 3))     # [1 1 1 2 2 2 3 3 3] — her elemanı 3 kez tekrarla

b = np.array([[1, 2], [3, 4]])
print(np.tile(b, (2, 3)))  # 2×3 tekrarlama (2 satır yönünde, 3 sütun yönünde)
```

---

## BLOK 6 — Yaygın Kullanım Senaryoları

### Senaryo 1 — Veri Normalizasyon

Makine öğrenmesinde verileri belirli bir aralığa normalize etmek çok yaygındır.

```python
import numpy as np

veri = np.array([15.0, 42.0, 8.0, 67.0, 23.0, 55.0])

# Min-Max normalizasyonu (0-1 aralığına)
min_val = veri.min()
max_val = veri.max()
normalize = (veri - min_val) / (max_val - min_val)
print(normalize.round(3))   # [0.119 0.576 0.    1.    0.254 0.796]

# Z-score standardizasyonu (ortalama=0, std=1)
ortalama  = veri.mean()
std       = veri.std()
standart  = (veri - ortalama) / std
print(standart.round(3))

# Görüntü için 0-255 normalizasyonu
img_float = np.random.rand(100, 100).astype(np.float32)
img_uint8 = (img_float * 255).astype(np.uint8)
```

---

### Senaryo 2 — Görüntü İşleme Pipeline'ında NumPy

OpenCV görüntüleri NumPy dizisi olarak saklar. NumPy operasyonları doğrudan görüntülere uygulanabilir.

```python
import numpy as np
import cv2

img = cv2.imread("foto.jpg")   # NumPy ndarray (H, W, 3), uint8

# Parlaklık artırma — float32 + clip (güvenli)
parlak = np.clip(img.astype(np.float32) + 50, 0, 255).astype(np.uint8)

# Kontrast ayarı — ölçekleme
kontrast = np.clip(img.astype(np.float32) * 1.5, 0, 255).astype(np.uint8)

# Belirli renk kanalına erişim
mavi   = img[:, :, 0]    # B kanalı
yesil  = img[:, :, 1]    # G kanalı
kirmizi = img[:, :, 2]   # R kanalı

# ROI (Region of Interest) — kopyasız dilimleme
roi = img[100:300, 150:400]       # görünüm (view)
roi_kopya = img[100:300, 150:400].copy()  # bağımsız kopya

# Boolean maskeleme — belirli piksel değerlerini değiştir
maske = img[:, :, 2] > 200       # kırmızı kanalda 200'den yüksek pikseller
img[maske] = [0, 0, 255]          # o pikselleri kırmızıya boya

# Görüntüyü düzleştir (ML modeline verirken)
duz = img.flatten()               # (H*W*C,) şeklinde 1D vektör
duz_satir = img.reshape(1, -1)    # (1, H*W*C) — tek örnek olarak
```

---

### Senaryo 3 — Veri Analizi

```python
import numpy as np

# Öğrenci notları (30 öğrenci, 4 sınav)
notlar = np.random.randint(40, 100, size=(30, 4))

# Temel istatistikler
print(f"Sınıf ortalaması   : {notlar.mean():.1f}")
print(f"En yüksek not      : {notlar.max()}")
print(f"En düşük not       : {notlar.min()}")
print(f"Standart sapma     : {notlar.std():.1f}")

# Sınav bazlı analiz
sinav_ortalamalari = notlar.mean(axis=0)
print(f"Sınav ortalamaları : {sinav_ortalamalari.round(1)}")

# Öğrenci bazlı ortalama
ogrenci_ortalamalari = notlar.mean(axis=1)

# Geçenler ve kalanlar
gec_esigi  = 60
gecenler   = (ogrenci_ortalamalari >= gec_esigi).sum()
print(f"Geçen öğrenci: {gecenler}/{len(notlar)}")

# En başarılı 5 öğrenci
en_basarili = np.argsort(ogrenci_ortalamalari)[::-1][:5]
print(f"En başarılı 5 öğrenci (indeks): {en_basarili}")
```

---

### Senaryo 4 — Sinyal Üretme ve Analiz

```python
import numpy as np

# Sinyal oluştur
t          = np.linspace(0, 2 * np.pi, 1000)
sinyal     = np.sin(t) + 0.3 * np.sin(5 * t)   # temel + harmonik

# Gürültü ekle
gurultu    = np.random.normal(0, 0.1, size=t.shape)
gurultulu  = sinyal + gurultu

# Basit hareketli ortalama filtresi
pencere = 10
filtreli = np.convolve(gurultulu, np.ones(pencere) / pencere, mode='same')

# Fourier dönüşümü
fft_sonuc  = np.fft.fft(sinyal)
frekanslar = np.fft.fftfreq(len(t))
genlikler  = np.abs(fft_sonuc)
```

---

## BLOK 7 — Dikkat Edilmesi Gerekenler

---

### Dikkat 1 — View ve Copy Karışıklığı

```python
# ❌ View: orijinal değişir
import numpy as np
a = np.array([1, 2, 3, 4, 5])
b = a[1:4]
b[0] = 99         # a[1] de 99 oldu!

# ✅ Kopya: orijinal korunur
b = a[1:4].copy()
b[0] = 99         # a değişmedi
```

---

### Dikkat 2 — Boyut Karışıklığı: `(n,)` ile `(n, 1)` farkı

```python
import numpy as np

a = np.array([1, 2, 3])           # shape: (3,)   — 1D vektör
b = np.array([[1], [2], [3]])     # shape: (3, 1) — sütun vektörü
c = a.reshape(-1, 1)              # (3,) → (3, 1)

# Matris çarpımı için boyut kritik!
M = np.ones((2, 3))
print((M @ a).shape)     # (2,)   — a 1D vektör olarak işlenir
print((M @ c).shape)     # (2, 1) — c sütun vektörü
```

---

### Dikkat 3 — uint8 Taşması

```python
import numpy as np

a = np.array([250], dtype=np.uint8)
print(a + 10)      # [4]  — taşma! (260 mod 256 = 4)

# ✅ Güvenli yöntem
guvenlı = np.clip(a.astype(np.float32) + 10, 0, 255).astype(np.uint8)
print(guvenlı)     # [255] — değer 255'te kaldı
```

---

### Dikkat 4 — Float Hassasiyet Sorunu

```python
import numpy as np

print(0.1 + 0.2 == 0.3)          # False  — float yuvarlama
print(np.isclose(0.1 + 0.2, 0.3)) # True  — doğru yaklaşım

a = np.array([0.1, 0.2, 0.3])
b = np.array([0.3, 0.3, 0.3])
print(np.allclose(a, b))          # False (0.1+0.2 ≠ 0.3 tam olarak)
```

---

### Dikkat 5 — Dtype Dönüşümlerinde Veri Kaybı

```python
import numpy as np

a = np.array([1.7, 2.9, 3.1], dtype=np.float64)

# float → int: kesir atılır, yuvarlanmaz!
b = a.astype(np.int32)
print(b)     # [1 2 3] — 1.7 → 1, 2.9 → 2

# Yuvarlamak için
c = np.round(a).astype(np.int32)
print(c)     # [2 3 3]
```

---

### Dikkat 6 — Bellek Tüketimi

```python
import numpy as np

# float64 vs float32 — 2 kat fark
a64 = np.zeros((1000, 1000), dtype=np.float64)
a32 = np.zeros((1000, 1000), dtype=np.float32)
print(f"float64: {a64.nbytes / 1024 / 1024:.1f} MB")   # 7.6 MB
print(f"float32: {a32.nbytes / 1024 / 1024:.1f} MB")   # 3.8 MB

# Gereksiz kopya oluşturmaktan kaçının
b = a64 + 1         # yeni dizi — bellek harcıyor
a64 += 1            # yerinde — daha verimli (in-place)
```

---

### Dikkat 7 — Eksen Sırası

```python
import numpy as np

img = np.zeros((480, 640, 3))    # OpenCV: (H, W, C)

# shape'e dikkat!
print(img.shape[0])   # 480 → yükseklik
print(img.shape[1])   # 640 → genişlik
print(img.shape[2])   # 3   → kanal sayısı

# resize için (genişlik, yükseklik) sırası kullanılır — karıştırmayın!
import cv2
kucuk = cv2.resize(img.astype(np.uint8), (img.shape[1]//2, img.shape[0]//2))
#                                          genişlik            yükseklik
```

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `numpy-uygulamalar.md` · `numpy-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
