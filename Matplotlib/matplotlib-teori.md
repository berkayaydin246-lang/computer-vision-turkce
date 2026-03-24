# 📘 Matplotlib — Teorik Notlar

> **Modül:** Teori | **Seviye:** Başlangıç → Orta
> **İlgili dosyalar:** `matplotlib-uygulamalar.md` · `matplotlib-alistirmalar.md`

---

## BLOK 0 — Giriş Kartı

```
Kütüphane  : Matplotlib
Sürüm      : 3.7+ (güncel)
Lisans     : PSF (BSD uyumlu)
Kurulum    : pip install matplotlib
İçe aktarma: import matplotlib.pyplot as plt
```

**Ne yapar?**
Matplotlib, Python'da veri görselleştirme için geliştirilmiş temel kütüphanedir. Çizgi grafikleri, saçılım diyagramları, çubuk grafikler, histogramlar, ısı haritaları ve görüntü gösterimi gibi pek çok görsel türü oluşturmanızı sağlar. 2003 yılında John Hunter tarafından MATLAB'ın grafik motoruna benzer bir Python alternatifi olarak başlatılmıştır.

**Neden Görselleştirme Önemlidir?**
Sayılar tek başına anlam taşımaz. 10.000 satırlık bir veri setinin ortalaması, dağılımı ve trendleri bir grafik ile saniyeler içinde kavranabilirken, aynı bilgiyi tabloda bulmak dakikalar alabilir. Görselleştirme; veriyi anlamayı, hataları fark etmeyi ve bulguları başkalarına aktarmayı kolaylaştırır.

**Kullanıldığı alanlar:**

| Alan | Kullanım Amacı |
|------|---------------|
| Veri Bilimi | Keşif analizi (EDA), dağılım ve korelasyon görselleştirme |
| Makine Öğrenmesi | Eğitim kaybı grafikleri, confusion matrix, ROC eğrisi |
| Bilgisayarlı Görü | OpenCV görüntülerini Jupyter'da gösterme, histogram analizi |
| Bilimsel Hesaplama | Simülasyon çıktıları, fonksiyon grafikleri |
| Rapor ve Sunum | Yayın kalitesinde grafik üretme |

**Bağımlı ve birlikte kullanılan kütüphaneler:**
Matplotlib tek başına kullanılabildiği gibi NumPy, pandas, scikit-learn ve OpenCV ile entegre çalışır. Seaborn ve Plotly gibi kütüphaneler Matplotlib üzerine inşa edilmiştir.

---

## BLOK 1 — Kurulum

```bash
# pip ile
pip install matplotlib

# conda ile
conda install matplotlib

# Bilimsel paket grubuyla
pip install numpy scipy matplotlib pandas
```

**Kurulum doğrulama:**
```python
import matplotlib
import matplotlib.pyplot as plt
print(matplotlib.__version__)   # örn: 3.8.2
```

**Jupyter Notebook kurulumu:**
```python
# Grafiği notebook içinde göstermek için (genellikle varsayılan)
%matplotlib inline

# İnteraktif grafik için
%matplotlib widget
```

**⚠️ Uyarılar:**
- `import matplotlib.pyplot as plt` kısaltması evrensel standarttır.
- Sunucu ortamlarında (Docker, CI) `plt.show()` çalışmayabilir; `plt.savefig()` kullanın.
- Jupyter'da `plt.show()` genellikle gerekmez; `%matplotlib inline` sonrası hücre otomatik gösterir.

---

## BLOK 2 — Temel Kavramlar

### Matplotlib'in Düşünce Biçimi

Matplotlib'de her grafik iki ana nesneden oluşur:

```
Figure  →  Tüm grafik alanı (kağıt)
  └── Axes  →  Gerçek grafik koordinat sistemi (çizim alanı)
        ├── x ekseni, y ekseni
        ├── başlık, etiketler
        ├── çizgiler, noktalar, çubuklar
        └── legend
```

**Önemli ayrım:** `Figure` ve `Axes` farklı nesnelerdir. Bir `Figure` birden fazla `Axes` içerebilir (alt grafik). Çoğu işlem `Axes` nesnesi üzerinde yapılır.

```
┌─────────────────── Figure ──────────────────────┐
│  ┌────────── Axes ──────────┐                   │
│  │   ^                      │                   │
│  │   │    grafik içeriği    │                   │
│  │   │                      │                   │
│  │   └──────────────────>   │                   │
│  └──────────────────────────┘                   │
│           Figure başlığı, kenar boşlukları      │
└─────────────────────────────────────────────────┘
```

---

### İki Kullanım Stili

Matplotlib'de grafik oluşturmanın iki yolu vardır:

**1. pyplot (plt) stili — kolay, hızlı:**
```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 2 * np.pi, 100)
y = np.sin(x)

plt.plot(x, y)
plt.title("Sinüs Dalgası")
plt.xlabel("x")
plt.ylabel("sin(x)")
plt.grid(True)
plt.show()
```

**2. Nesne yönelimli (OOP) stili — esnek, çoklu grafik için:**
```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 2 * np.pi, 100)

fig, ax = plt.subplots()     # Figure ve Axes nesnesini aç
ax.plot(x, np.sin(x), label="sin(x)")
ax.plot(x, np.cos(x), label="cos(x)")
ax.set_title("Trigonometrik Fonksiyonlar")
ax.set_xlabel("x")
ax.set_ylabel("y")
ax.legend()
ax.grid(True)
plt.show()
```

**Hangisini kullanmalıyım?**
Tekil ve basit grafikler için `plt.*` stili daha hızlıdır. Çoklu alt grafik, özel yerleşim veya grafikleri fonksiyon içinde oluşturma söz konusuysa nesne yönelimli stil tercih edilmelidir. Bu dokümanda her iki stil de kullanılacaktır.

---

### Temel Çalışma Akışı

```
1. Veriyi hazırla (NumPy, pandas, liste vb.)
2. Figure ve Axes oluştur (plt.subplots veya plt.figure)
3. Grafik türünü seç ve çiz (plot, scatter, bar vb.)
4. Başlık, etiket, legend gibi biçimlendirmeleri ekle
5. Göster (plt.show()) veya kaydet (plt.savefig())
6. Belleği temizle (plt.close())
```

---

## BLOK 3 — Temel Grafik Türleri

---

### 🔹 Çizgi Grafik — `plt.plot`

**Ne yapar:** Veri noktalarını çizgiyle bağlar. Zaman serisi, matematiksel fonksiyon ve trend gösterimi için kullanılır.

**Ne zaman kullanılır:** Sürekli veya sıralı verinin değişimini göstermek istediğinizde.

**Parametreler:**
- `x`, `y` — çizilecek veriler
- `color` veya `c` — renk (`'red'`, `'#FF0000'`, `(1,0,0)`)
- `linestyle` veya `ls` — çizgi stili (`'-'`, `'--'`, `':'`, `'-.'`)
- `linewidth` veya `lw` — çizgi kalınlığı
- `marker` — nokta işareti (`'o'`, `'s'`, `'^'`, `'.'`)
- `markersize` veya `ms` — işaret boyutu
- `label` — legend için etiket
- `alpha` — saydamlık (0=tam saydam, 1=tam opak)

**Döndürür:** `Line2D` nesnesi (genellikle kullanılmaz).

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 4 * np.pi, 200)

fig, ax = plt.subplots(figsize=(10, 4))

ax.plot(x, np.sin(x),     color='blue',   lw=2,   label='sin(x)')
ax.plot(x, np.cos(x),     color='red',    lw=2,   ls='--',  label='cos(x)')
ax.plot(x, 0.5*np.sin(2*x), color='green', lw=1.5, ls=':',  label='0.5·sin(2x)')

ax.set_title("Trigonometrik Fonksiyonlar", fontsize=14)
ax.set_xlabel("x (radyan)")
ax.set_ylabel("Genlik")
ax.legend()
ax.grid(True, alpha=0.4)
ax.axhline(0, color='black', lw=0.8)   # y=0 çizgisi

plt.tight_layout()
plt.savefig("cizgi_grafik.png", dpi=150)
plt.show()
```

**🔴 Dikkat:** `plot(x, y)` için `x` ve `y` aynı uzunlukta olmalıdır. `ax.set_ylim(-1.5, 1.5)` ile eksen sınırlarını manuel belirleyebilirsiniz.

---

### 🔹 Saçılım Diyagramı — `plt.scatter`

**Ne yapar:** Her veri noktasını bağımsız bir işaret olarak çizer. İki değişken arasındaki ilişkiyi, kümeleri ve aykırı değerleri göstermek için kullanılır.

**Ne zaman kullanılır:** İki sürekli değişken arasındaki korelasyonu veya veri dağılımını incelemek istediğinizde.

**Parametreler:**
- `x`, `y` — koordinatlar
- `s` — nokta boyutu (skalar veya dizi)
- `c` — renk (skalar, dizi veya renk adı)
- `cmap` — renk haritası (dizi ile kullanıldığında)
- `alpha` — saydamlık
- `marker` — işaret tipi
- `edgecolors` — nokta kenar rengi

```python
import matplotlib.pyplot as plt
import numpy as np

rng  = np.random.default_rng(42)
n    = 150

# İki grup oluştur
grup1_x = rng.normal(2, 0.8, n)
grup1_y = rng.normal(3, 0.8, n)
grup2_x = rng.normal(5, 0.8, n)
grup2_y = rng.normal(1, 0.8, n)

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Sol: Basit scatter
axes[0].scatter(grup1_x, grup1_y, color='blue',   alpha=0.6, label='Grup A')
axes[0].scatter(grup2_x, grup2_y, color='orange', alpha=0.6, label='Grup B')
axes[0].set_title("Küme Saçılımı")
axes[0].legend()
axes[0].grid(True, alpha=0.3)

# Sağ: Boyut ve renk ile kodlanmış
x    = rng.uniform(0, 10, 80)
y    = rng.uniform(0, 10, 80)
boyut = rng.uniform(20, 300, 80)   # nokta boyutu veriye bağlı
renk  = rng.uniform(0, 1, 80)      # renk veriye bağlı

sc = axes[1].scatter(x, y, s=boyut, c=renk, cmap='viridis', alpha=0.7)
plt.colorbar(sc, ax=axes[1])
axes[1].set_title("Boyut ve Renk Kodlamalı")

plt.tight_layout()
plt.show()
```

---

### 🔹 Çubuk Grafik — `plt.bar` ve `plt.barh`

**Ne yapar:** Kategorik verileri dikey (`bar`) veya yatay (`barh`) çubuklarla karşılaştırır.

**Ne zaman kullanılır:** Kategoriler arasındaki büyüklük farklarını karşılaştırmak istediğinizde.

```python
import matplotlib.pyplot as plt
import numpy as np

kategoriler = ['Ocak', 'Şubat', 'Mart', 'Nisan', 'Mayıs', 'Haziran']
satis_a     = [120, 95, 140, 110, 160, 130]
satis_b     = [ 80, 115, 100, 140, 90, 155]

x = np.arange(len(kategoriler))
genislik = 0.35   # çubuk genişliği

fig, axes = plt.subplots(1, 2, figsize=(13, 4))

# Gruplu çubuk grafik
axes[0].bar(x - genislik/2, satis_a, genislik, label='Ürün A', color='steelblue')
axes[0].bar(x + genislik/2, satis_b, genislik, label='Ürün B', color='tomato')
axes[0].set_xticks(x)
axes[0].set_xticklabels(kategoriler)
axes[0].set_title("Aylık Satış Karşılaştırması")
axes[0].set_ylabel("Satış Adedi")
axes[0].legend()
axes[0].grid(True, axis='y', alpha=0.4)

# Yatay çubuk grafik (sıralı)
diller    = ['Python', 'JavaScript', 'Java', 'C++', 'Rust']
populerite = [32, 28, 18, 12, 10]
renkler    = ['#3776AB', '#F7DF1E', '#B07219', '#555555', '#DEA584']

axes[1].barh(diller, populerite, color=renkler)
axes[1].set_title("Programlama Dili Popülaritesi (%)")
axes[1].set_xlabel("Kullanım Oranı")
for i, v in enumerate(populerite):
    axes[1].text(v + 0.3, i, f'{v}%', va='center')

plt.tight_layout()
plt.show()
```

---

### 🔹 Histogram — `plt.hist`

**Ne yapar:** Bir sayısal değişkenin dağılımını görselleştirir. Değer aralığını eşit bölümlere (bin) ayırır ve her bölümdeki eleman sayısını çubukla gösterir.

**Ne zaman kullanılır:** Verinin dağılımını, çarpıklığını, uç değerleri ve yoğunluk bölgelerini anlamak istediğinizde.

**Parametreler:**
- `x` — veri
- `bins` — bin sayısı veya sınır noktaları
- `density` — `True` ise olasılık yoğunluğu gösterir
- `color`, `alpha`, `edgecolor`
- `cumulative` — `True` ise kümülatif histogram

```python
import matplotlib.pyplot as plt
import numpy as np

rng    = np.random.default_rng(42)
normal = rng.normal(100, 15, 1000)    # ortalama=100, std=15
carp   = rng.exponential(10, 1000)    # çarpık dağılım

fig, axes = plt.subplots(1, 3, figsize=(14, 4))

# Standart histogram
axes[0].hist(normal, bins=30, color='steelblue', edgecolor='white', alpha=0.8)
axes[0].axvline(normal.mean(), color='red',    lw=2, ls='--', label=f'Ort: {normal.mean():.1f}')
axes[0].axvline(np.median(normal), color='green', lw=2, ls=':',  label=f'Med: {np.median(normal):.1f}')
axes[0].set_title("Normal Dağılım")
axes[0].legend()
axes[0].grid(True, alpha=0.4)

# Çarpık dağılım
axes[1].hist(carp, bins=40, color='tomato', edgecolor='white', alpha=0.8)
axes[1].set_title("Çarpık (Üstel) Dağılım")
axes[1].grid(True, alpha=0.4)

# Karşılaştırmalı (normalize edilmiş)
axes[2].hist(rng.normal(0, 1, 500), bins=25, alpha=0.6,
             density=True, label='Grup A', color='blue')
axes[2].hist(rng.normal(1, 1.5, 500), bins=25, alpha=0.6,
             density=True, label='Grup B', color='orange')
axes[2].set_title("Karşılaştırmalı (Yoğunluk)")
axes[2].legend()
axes[2].grid(True, alpha=0.4)

plt.tight_layout()
plt.show()
```

**🔴 Dikkat:** `bins` değeri çok küçük olursa detay kaybolur; çok büyük olursa grafik gürültülü görünür. İyi bir başlangıç noktası `bins = int(np.sqrt(len(veri)))` formülüdür.

---

### 🔹 Kutu Grafik — `plt.boxplot`

**Ne yapar:** Verinin beş özet istatistiğini (min, Q1, medyan, Q3, max) tek grafikte gösterir. Aykırı değerleri nokta olarak vurgular.

**Ne zaman kullanılır:** Gruplar arasındaki dağılım ve medyan farklarını hızlıca karşılaştırmak için.

```python
import matplotlib.pyplot as plt
import numpy as np

rng    = np.random.default_rng(42)
gruplar = {
    'A': rng.normal(70, 10, 100),
    'B': rng.normal(75, 15, 100),
    'C': rng.normal(65, 8,  100),
    'D': rng.normal(80, 12, 100),
}

fig, ax = plt.subplots(figsize=(8, 5))
ax.boxplot(gruplar.values(), labels=gruplar.keys(),
           patch_artist=True,
           boxprops=dict(facecolor='lightblue'),
           medianprops=dict(color='red', lw=2))
ax.set_title("Grup Karşılaştırması — Kutu Grafik")
ax.set_ylabel("Puan")
ax.grid(True, axis='y', alpha=0.4)
plt.show()
```

---

### 🔹 Pasta Grafik — `plt.pie`

**Ne yapar:** Bütünün parçalarını yüzde olarak gösterir.

**Ne zaman kullanılır:** Parçaların bütüne oranını göstermek istediğinizde. Çok fazla dilim varsa (>6) çubuk grafik genellikle daha okunaklıdır.

```python
import matplotlib.pyplot as plt

kategoriler  = ['Python', 'JavaScript', 'Java', 'Diğer']
yuzde        = [35, 28, 20, 17]
vurgulama    = [0.05, 0, 0, 0]   # Python dilimini öne çıkar

fig, ax = plt.subplots(figsize=(7, 7))
ax.pie(yuzde, labels=kategoriler, explode=vurgulama,
       autopct='%1.1f%%', startangle=90,
       colors=['#3776AB', '#F7DF1E', '#B07219', '#AAAAAA'],
       shadow=True)
ax.set_title("Programlama Dili Dağılımı")
plt.tight_layout()
plt.show()
```

---

## BLOK 4 — Grafik Biçimlendirme

### Başlık ve Etiketler

```python
import matplotlib.pyplot as plt
import numpy as np

fig, ax = plt.subplots(figsize=(8, 4))
x = np.linspace(-3, 3, 100)
ax.plot(x, x**2, color='navy')

# Başlık ve eksen etiketleri
ax.set_title("İkinci Dereceden Fonksiyon", fontsize=14, fontweight='bold', pad=15)
ax.set_xlabel("x değeri",  fontsize=12, labelpad=8)
ax.set_ylabel("y = x²",    fontsize=12, labelpad=8)

# Eksen sınırları
ax.set_xlim(-3.5, 3.5)
ax.set_ylim(-0.5, 10)

# Eksen kıvrımları (ticks)
ax.set_xticks([-3, -2, -1, 0, 1, 2, 3])
ax.set_yticks([0, 2, 4, 6, 8])
ax.tick_params(axis='both', labelsize=10)

# Izgara
ax.grid(True, linestyle='--', alpha=0.5, color='gray')

# Referans çizgileri
ax.axhline(0, color='black', lw=0.8)
ax.axvline(0, color='black', lw=0.8)

plt.tight_layout()
plt.show()
```

---

### Legend (Açıklama Kutusu)

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 10, 100)

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Sol: Otomatik legend
for n in [1, 2, 3]:
    axes[0].plot(x, np.sin(n * x), label=f'sin({n}x)')
axes[0].legend()   # varsayılan konum (best)

# Sağ: Konumlandırılmış legend
axes[1].plot(x, np.exp(-x),   'b-',  lw=2, label='$e^{-x}$')
axes[1].plot(x, np.exp(-x/2), 'r--', lw=2, label='$e^{-x/2}$')
axes[1].legend(
    loc='upper right',   # konum: upper/lower/center + left/right/center
    fontsize=11,
    framealpha=0.8,      # arka plan saydamlığı
    edgecolor='gray',
    title='Fonksiyonlar'
)
axes[1].set_title("Legend Örnekleri")

plt.tight_layout()
plt.show()
```

**Yaygın `loc` değerleri:**
`'best'`, `'upper right'`, `'upper left'`, `'lower right'`, `'lower left'`, `'center'`, `(0.8, 0.5)` (x,y koordinatı)

---

### Stil ve Renk

```python
import matplotlib.pyplot as plt

# Mevcut stilleri listele
print(plt.style.available)

# Stil uygula
plt.style.use('seaborn-v0_8-whitegrid')
# Diğer popüler stiller: 'ggplot', 'dark_background', 'bmh', 'fivethirtyeight'

# Renk belirtme yolları
ax.plot(x, y, color='red')          # isim
ax.plot(x, y, color='#FF5733')      # hex kodu
ax.plot(x, y, color=(0.2, 0.4, 0.8))   # RGB tuple (0-1)
ax.plot(x, y, color='C0')           # varsayılan renk döngüsü (C0, C1, C2...)
ax.plot(x, y, 'b-o')               # kısaltma: mavi, çizgi, daire
```

---

### Metin ve Açıklama (Annotation)

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 2 * np.pi, 200)
y = np.sin(x)

fig, ax = plt.subplots(figsize=(9, 4))
ax.plot(x, y, 'b-', lw=2)

# Basit metin
ax.text(1.0, 0.8, "Maksimum bölge", fontsize=10, color='green')

# Ok ile açıklama
ax.annotate(
    text="Tepe nokta (π/2, 1)",
    xy=(np.pi/2, 1.0),             # ok ucunun konumu
    xytext=(2.5, 0.7),             # metnin konumu
    fontsize=10,
    arrowprops=dict(arrowstyle='->', color='red', lw=1.5),
    color='red'
)

ax.set_title("Sinüs Fonksiyonu — Açıklamalar")
ax.grid(True, alpha=0.3)
plt.tight_layout()
plt.show()
```

---

## BLOK 5 — Çoklu Grafikler

### `plt.subplots` ile Alt Grafikler

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 2 * np.pi, 100)

# Basit 2×2 ızgara
fig, axes = plt.subplots(2, 2, figsize=(10, 8))
fig.suptitle("Farklı Grafik Türleri", fontsize=14, fontweight='bold')

axes[0, 0].plot(x, np.sin(x), 'b-')
axes[0, 0].set_title("Çizgi Grafik")

axes[0, 1].scatter(np.random.rand(50), np.random.rand(50), alpha=0.6)
axes[0, 1].set_title("Saçılım Diyagramı")

axes[1, 0].bar(['A', 'B', 'C', 'D'], [3, 7, 5, 9])
axes[1, 0].set_title("Çubuk Grafik")

axes[1, 1].hist(np.random.randn(300), bins=20, edgecolor='white')
axes[1, 1].set_title("Histogram")

for ax in axes.flat:
    ax.grid(True, alpha=0.3)

plt.tight_layout()
plt.show()
```

---

### `gridspec` ile Esnek Yerleşim

```python
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np

fig = plt.figure(figsize=(11, 6))
gs  = gridspec.GridSpec(2, 3, figure=fig, hspace=0.4, wspace=0.3)

ax1 = fig.add_subplot(gs[0, :])      # üst satır — tüm sütunlar
ax2 = fig.add_subplot(gs[1, 0])      # alt sol
ax3 = fig.add_subplot(gs[1, 1])      # alt orta
ax4 = fig.add_subplot(gs[1, 2])      # alt sağ

x = np.linspace(0, 10, 200)
ax1.plot(x, np.sin(x) * np.exp(-x/5), color='navy', lw=2)
ax1.set_title("Sönümlü Sinüs Dalgası (Geniş Panel)")

veri = np.random.randn(200)
ax2.hist(veri, bins=20, color='steelblue', edgecolor='white')
ax2.set_title("Histogram")

ax3.scatter(np.random.rand(50), np.random.rand(50), alpha=0.5)
ax3.set_title("Scatter")

ax4.bar(['X', 'Y', 'Z'], [4, 7, 5], color=['tomato', 'gold', 'mediumseagreen'])
ax4.set_title("Çubuk")

plt.suptitle("Karma Yerleşim Örneği", fontsize=13)
plt.show()
```

---

## BLOK 6 — Görüntü Görselleştirme

### `plt.imshow` ile Görüntü Gösterimi

**Ne yapar:** 2D veya 3D NumPy dizisini görüntü olarak gösterir. Gri tonlamalı diziler, renkli görüntüler ve özel renk haritaları kullanılabilir.

**Ne zaman kullanılır:** Jupyter Notebook'ta OpenCV görüntülerini, ısı haritalarını, matrisleri veya herhangi bir 2D veriyi görsel olarak incelemek istediğinizde.

**Parametreler:**
- `X` — gösterilecek dizi (H×W veya H×W×3)
- `cmap` — renk haritası (`'gray'`, `'viridis'`, `'hot'`, `'jet'` vb.)
- `vmin`, `vmax` — renk ölçeği sınırları
- `interpolation` — piksel büyütme yöntemi (`'nearest'`, `'bilinear'`)
- `aspect` — en-boy oranı (`'equal'`, `'auto'`)

**Döndürür:** `AxesImage` nesnesi (genellikle `plt.colorbar()` için kullanılır).

```python
import matplotlib.pyplot as plt
import numpy as np

# Yapay görüntüler
gri_img    = np.random.randint(0, 256, (200, 300), dtype=np.uint8)
renkli_img = np.random.randint(0, 256, (200, 300, 3), dtype=np.uint8)
isi_mat    = np.outer(np.linspace(0, 1, 50), np.linspace(0, 1, 50))  # ısı haritası

fig, axes = plt.subplots(1, 3, figsize=(14, 4))

# Gri görüntü
axes[0].imshow(gri_img, cmap='gray', vmin=0, vmax=255)
axes[0].set_title("Gri Görüntü")
axes[0].axis('off')   # eksen çizgilerini gizle

# Renkli görüntü (RGB)
axes[1].imshow(renkli_img)
axes[1].set_title("Renkli Görüntü (RGB)")
axes[1].axis('off')

# Isı haritası + colorbar
im = axes[2].imshow(isi_mat, cmap='viridis', interpolation='bilinear')
axes[2].set_title("Isı Haritası")
plt.colorbar(im, ax=axes[2], fraction=0.046, pad=0.04)

plt.tight_layout()
plt.show()
```

---

### OpenCV Görüntüsünü Matplotlib ile Gösterme

OpenCV görüntüleri BGR formatında saklanır. Matplotlib RGB bekler; göstermeden önce dönüşüm yapılmalıdır.

```python
import cv2
import matplotlib.pyplot as plt
import numpy as np

img_bgr = cv2.imread("foto.jpg")
if img_bgr is None:
    img_bgr = (np.random.rand(300, 400, 3) * 255).astype(np.uint8)

# BGR → RGB dönüşümü (zorunlu!)
img_rgb  = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
img_gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)
img_hsv  = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2HSV)

fig, axes = plt.subplots(1, 4, figsize=(16, 4))

axes[0].imshow(img_rgb);                    axes[0].set_title("Renkli (RGB)")
axes[1].imshow(img_gray,  cmap='gray');     axes[1].set_title("Gri")
axes[2].imshow(img_bgr[:, :, 2], cmap='Reds');  axes[2].set_title("Kırmızı Kanal")
axes[3].imshow(img_hsv[:, :, 0],  cmap='hsv');  axes[3].set_title("H Kanalı")

for ax in axes:
    ax.axis('off')

plt.suptitle("OpenCV Görüntüsü — Kanal Karşılaştırması")
plt.tight_layout()
plt.show()
```

**⚠️ Dikkat:** `plt.imshow(img_bgr)` çağrısı kırmızı-mavi renkleri ters gösterir. Her zaman `cv2.cvtColor(img, cv2.COLOR_BGR2RGB)` ile dönüştürün.

---

### Renk Haritaları (Colormaps)

Renk haritası, sayısal değerleri renge dönüştüren bir tablo gibi düşünülebilir. Doğru seçim verinin anlaşılırlığını önemli ölçüde artırır.

```python
import matplotlib.pyplot as plt
import numpy as np

# Isı haritası örneği
veri = np.random.rand(10, 10)

fig, axes = plt.subplots(2, 4, figsize=(16, 6))
haritalar = ['gray', 'viridis', 'plasma', 'hot',
             'coolwarm', 'jet', 'RdYlGn', 'hsv']

for ax, cmap_adi in zip(axes.flat, haritalar):
    im = ax.imshow(veri, cmap=cmap_adi, interpolation='nearest')
    ax.set_title(cmap_adi)
    ax.axis('off')
    plt.colorbar(im, ax=ax, fraction=0.046)

plt.suptitle("Renk Haritası Karşılaştırması", fontsize=13)
plt.tight_layout()
plt.show()
```

**Renk haritası seçim rehberi:**

| Amaç | Önerilen Colormap |
|------|------------------|
| Gri tonlamalı görüntü | `'gray'` |
| Genel amaçlı sayısal veri | `'viridis'`, `'plasma'` |
| Sıcak-soğuk karşılaştırma | `'coolwarm'`, `'RdBu'` |
| Isı / yoğunluk haritası | `'hot'`, `'inferno'` |
| Negatif-pozitif ayrımı | `'RdYlGn'`, `'bwr'` |
| Renk açısı (H kanalı) | `'hsv'` |

**`jet` neden önerilmez?**
`jet` renk haritası görsel olarak çekici görünse de renk algısı düzgün değildir; eşit değer farklılıkları eşit renk farklılığı oluşturmaz ve renk körü olan kişiler için erişilebilir değildir. `viridis` hem algısal olarak düzgündür hem de renk körlüğüne uyumludur.

---

### Görüntü Histogramı

```python
import cv2
import matplotlib.pyplot as plt
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    img = (np.random.rand(200, 300, 3) * 255).astype(np.uint8)

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Gri histogram
axes[0].hist(gray.ravel(), bins=256, range=(0, 256),
             color='gray', alpha=0.8, edgecolor='none')
axes[0].set_title("Gri Tonlama Histogramı")
axes[0].set_xlabel("Piksel Değeri (0-255)")
axes[0].set_ylabel("Piksel Sayısı")
axes[0].axvline(gray.mean(), color='red', lw=2, ls='--',
                label=f'Ort: {gray.mean():.0f}')
axes[0].legend()
axes[0].grid(True, alpha=0.3)

# BGR kanal histogramları
for kanal, renk, isim in zip([0, 1, 2], ['blue', 'green', 'red'], ['B', 'G', 'R']):
    hist = cv2.calcHist([img], [kanal], None, [256], [0, 256])
    axes[1].plot(hist, color=renk, alpha=0.7, label=isim)
axes[1].set_title("BGR Kanal Histogramları")
axes[1].set_xlabel("Piksel Değeri")
axes[1].set_ylabel("Piksel Sayısı")
axes[1].legend()
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.show()
```

---

## BLOK 7 — Grafik Kaydetme ve Çıktı

### `plt.savefig`

```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(0, 2*np.pi, 100)
fig, ax = plt.subplots(figsize=(8, 4))
ax.plot(x, np.sin(x))
ax.set_title("Örnek Grafik")

# PNG kaydet (yüksek çözünürlük)
plt.savefig("grafik.png",
            dpi=150,             # DPI: 72=ekran, 150=iyi, 300=baskı kalitesi
            bbox_inches='tight', # kenarlık beyaz boşluğunu kaldır
            facecolor='white',   # arka plan rengi
            transparent=False)

# PDF kaydet (vektörel, yayın kalitesi)
plt.savefig("grafik.pdf", bbox_inches='tight')

# SVG kaydet (web için)
plt.savefig("grafik.svg", bbox_inches='tight')

plt.show()
```

**Format seçimi:**
- **PNG** — web, raporlar, Jupyter notebook çıktısı
- **PDF** — akademik yayın, LaTeX entegrasyonu
- **SVG** — web uygulamaları, ölçeklenebilir vektör
- **EPS** — bazı bilimsel dergiler

**⚠️ Dikkat:** `plt.savefig()` her zaman `plt.show()` dan **önce** çağrılmalıdır; `show()` sonrası figür temizlenir ve boş PNG kaydedilir.

---

### Belleği Temizleme

```python
import matplotlib.pyplot as plt

# Belirli bir figure'ı kapat
fig, ax = plt.subplots()
# ... grafik çizimi ...
plt.close(fig)

# Tüm figürleri kapat
plt.close('all')

# Mevcut figürü temizle (yeni grafik için)
plt.clf()   # figure'ı temizler
plt.cla()   # yalnızca mevcut axes'i temizler
```

**Ne zaman önemlidir?**
Döngü içinde çok sayıda grafik oluşturulduğunda her iterasyonda `plt.close()` çağrılmazsa bellek kullanımı birikir ve yavaşlamaya neden olur.

---

## BLOK 8 — Dikkat Edilmesi Gerekenler

---

### Dikkat 1 — imshow BGR/RGB Karışıklığı

```python
import cv2
import matplotlib.pyplot as plt

img = cv2.imread("foto.jpg")

# ❌ Yanlış — renkler ters görünür
plt.imshow(img)

# ✅ Doğru
plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))

# Alternatif: NumPy ile dönüştür
plt.imshow(img[:, :, ::-1])   # kanalları ters sırayla oku
```

---

### Dikkat 2 — savefig show'dan Önce Gelmeli

```python
# ❌ Yanlış — boş görüntü kaydedilir
plt.plot([1, 2, 3])
plt.show()
plt.savefig("grafik.png")  # figure zaten kapandı!

# ✅ Doğru
plt.plot([1, 2, 3])
plt.savefig("grafik.png")  # önce kaydet
plt.show()                  # sonra göster
```

---

### Dikkat 3 — Bellek Sızıntısı

```python
# ❌ Döngüde figure kapatmayı unutmak → bellek birikmesi
for i in range(100):
    plt.figure()
    plt.plot([1, 2, 3])
    plt.savefig(f"grafik_{i}.png")
    # plt.close() unutuldu!

# ✅ Doğru
for i in range(100):
    fig, ax = plt.subplots()
    ax.plot([1, 2, 3])
    fig.savefig(f"grafik_{i}.png")
    plt.close(fig)   # her iterasyonda kapat
```

---

### Dikkat 4 — İki Farklı API'yi Karıştırma

```python
# ❌ plt ve ax karışık kullanım — öngörülemeyen sonuçlar
fig, ax = plt.subplots(1, 2)
plt.title("Başlık")     # hangi axes'e eklendi?
plt.xlabel("x")         # belirsiz

# ✅ Doğru — nesne yönelimli stil ile tutarlı
fig, axes = plt.subplots(1, 2)
axes[0].set_title("Sol Panel")
axes[1].set_title("Sağ Panel")
axes[0].set_xlabel("x değeri")
```

---

### Dikkat 5 — tight_layout Eksikliği

```python
# ❌ Etiketler ve başlıklar birbirine girer
fig, axes = plt.subplots(2, 2, figsize=(8, 6))
for ax in axes.flat:
    ax.set_title("Uzun Başlık Metni")
    ax.set_xlabel("X Ekseni Etiketi")
    ax.set_ylabel("Y Ekseni Etiketi")
plt.show()

# ✅ tight_layout ile düzeltilmiş
# ...aynı kod...
plt.tight_layout()
plt.show()
```

---

### Dikkat 6 — imshow Eksen Sırası

```python
import matplotlib.pyplot as plt
import numpy as np

img = np.zeros((100, 200))   # (satır=100, sütun=200)
plt.imshow(img)
# Otomatik eksenler:
# x ekseni → 0-200 (sütunlar = genişlik)
# y ekseni → 0-100 (satırlar = yükseklik), yukarıdan aşağıya

# Matematiksel koordinatlar için (y ekseni yukarı doğru)
plt.imshow(img, origin='lower')
```

---

### Dikkat 7 — DPI ve Boyut

```python
import matplotlib.pyplot as plt

# Ekran için uygun (küçük dosya boyutu)
fig = plt.figure(figsize=(8, 4), dpi=72)

# Web/rapor için (orta kalite)
fig = plt.figure(figsize=(8, 4), dpi=150)

# Baskı/yayın için (yüksek kalite, büyük dosya)
fig = plt.figure(figsize=(8, 4), dpi=300)

# Baskı boyutu hesabı:
# Genişlik (inç) = figsize[0], Yükseklik (inç) = figsize[1]
# Piksel genişliği = figsize[0] * dpi = 8 * 150 = 1200 piksel
```

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `matplotlib-uygulamalar.md` · `matplotlib-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
