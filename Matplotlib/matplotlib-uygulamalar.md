# 🛠️ Matplotlib — Uygulamalar

> **Modül:** Uygulamalar | **Seviye:** Başlangıç → Orta
> **İlgili dosyalar:** `matplotlib-teori.md` · `matplotlib-alistirmalar.md`

---

## BLOK 0 — Uygulama Belgesi Hakkında

Bu belge, `matplotlib-teori.md` dosyasında öğrenilen görselleştirme kavramlarını **gerçek senaryolarda** nasıl kullanacağınızı gösterir. Her uygulama belirli bir veri türünü ya da analiz amacını ele alır ve çalışır Python kodu sunar.

**Belgede neler var:**

| Blok | Konu |
|------|------|
| Blok 1 | Temel grafik türleri |
| Blok 2 | İstatistiksel görselleştirme |
| Blok 3 | Çoklu panel ve karşılaştırma |
| Blok 4 | Görüntü görselleştirme |
| Blok 5 | Mini projeler |

**Kodları çalıştırmak için:**
```bash
pip install matplotlib numpy
python uygulama.py
```

> **Jupyter Notebook kullanıcıları:** Hücrenin başına `%matplotlib inline` ekleyin; `plt.show()` çağrısına genellikle gerek kalmaz.

---

## BLOK 1 — Temel Grafik Türleri

---

### Uygulama 1.1 — Çizgi Grafik: Fonksiyon Karşılaştırması

**Problem:**
Birden fazla matematiksel fonksiyonu aynı grafikte göstermek ve grafik biçimlendirmenin temel öğelerini uygulamak.

**Amaç:**
`plt.plot`, eksen etiketleri, legend, ızgara ve çizgi stillerini kullanmayı öğrenmek.

**Kullanılan teknikler:**
`plt.plot`, `ax.set_title`, `ax.legend`, `ax.grid`, `ax.axhline`

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(-2 * np.pi, 2 * np.pi, 500)

fonksiyonlar = [
    (np.sin(x),          "sin(x)",        "royalblue",  "-",  2.0),
    (np.cos(x),          "cos(x)",        "tomato",     "--", 2.0),
    (np.sin(2 * x) / 2,  "sin(2x)/2",    "seagreen",   "-.", 1.5),
    (np.tanh(x),         "tanh(x)",       "darkorchid", ":",  2.0),
]

fig, ax = plt.subplots(figsize=(11, 5))

for y, etiket, renk, stil, kalinlik in fonksiyonlar:
    ax.plot(x, y, label=etiket, color=renk,
            linestyle=stil, linewidth=kalinlik)

# Referans çizgileri
ax.axhline(0,  color='black', lw=0.7, alpha=0.5)   # y = 0
ax.axvline(0,  color='black', lw=0.7, alpha=0.5)   # x = 0

# Pi değerlerinde dikey kesik çizgi
for carp, etiket in [(-np.pi, '-π'), (np.pi, 'π'), (-2*np.pi, '-2π'), (2*np.pi, '2π')]:
    ax.axvline(carp, color='gray', lw=0.6, ls='--', alpha=0.4)
    ax.text(carp, -1.2, etiket, ha='center', fontsize=9, color='gray')

# Biçimlendirme
ax.set_title("Trigonometrik ve Hiperbolik Fonksiyonlar", fontsize=14, pad=12)
ax.set_xlabel("x (radyan)", fontsize=11)
ax.set_ylabel("f(x)", fontsize=11)
ax.set_xlim(-2*np.pi - 0.3, 2*np.pi + 0.3)
ax.set_ylim(-1.4, 1.4)
ax.legend(loc='upper right', fontsize=10, framealpha=0.9)
ax.grid(True, linestyle='--', alpha=0.4)

plt.tight_layout()
plt.savefig("fonksiyon_karsilastirma.png", dpi=140)
plt.show()
```

**Açıklama:**
Fonksiyon parametreleri tuple listesinde saklanır; döngü ile her fonksiyon aynı stilde işlenir — yeni fonksiyon eklemek tek satır değiştirir. `axhline` ve `axvline` referans çizgileri grafik anlaşılırlığını artırır. `ax.text` ile metin doğrudan koordinat üzerine yerleştirilir.

**Beklenen çıktı:**
Dört fonksiyon farklı renk ve çizgi stillerinde, referans π noktaları işaretlenmiş tek bir grafik.

**İyileştirme fikirleri:**
- `ax.fill_between(x, np.sin(x), 0, alpha=0.1)` ile eğrinin altındaki alanı doldurun.
- `ax.set_xticks([-2*np.pi, -np.pi, 0, np.pi, 2*np.pi])` ile eksen etiketlerini π cinsinden gösterin.

---

### Uygulama 1.2 — Saçılım Diyagramı: Korelasyon Analizi

**Problem:**
İki değişken arasındaki ilişkiyi saçılım diyagramı ile görselleştirmek ve regresyon doğrusunu üzerine çizmek.

**Amaç:**
`plt.scatter`, renk/boyut kodlaması ve basit doğrusal regresyon çizgisi eklemeyi öğrenmek.

**Kullanılan teknikler:**
`plt.scatter`, `np.polyfit`, `np.polyval`, `plt.colorbar`

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(42)

# Simülasyon: Reklam harcaması vs satış
reklam = rng.uniform(10, 100, 80)              # bin TL reklam harcaması
satis  = 3.2 * reklam + rng.normal(0, 40, 80) # yaklaşık doğrusal ilişki + gürültü
bolge  = rng.integers(0, 4, 80)               # 4 bölge: 0,1,2,3

renkler_harita = ['royalblue', 'tomato', 'seagreen', 'darkorchid']
bolge_isimleri = ['Kuzey', 'Güney', 'Doğu', 'Batı']

fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# --- Sol: Bölgelere göre renklendirilmiş scatter ---
for b in range(4):
    maske = bolge == b
    axes[0].scatter(reklam[maske], satis[maske],
                    color=renkler_harita[b], label=bolge_isimleri[b],
                    alpha=0.8, s=60, edgecolors='white', linewidths=0.5)

# Doğrusal regresyon çizgisi
katsayilar = np.polyfit(reklam, satis, 1)
x_line = np.linspace(reklam.min(), reklam.max(), 200)
y_line = np.polyval(katsayilar, x_line)

axes[0].plot(x_line, y_line, color='black', lw=2, ls='--',
             label=f'Regresyon (y={katsayilar[0]:.1f}x+{katsayilar[1]:.0f})')

# Korelasyon katsayısı
r = np.corrcoef(reklam, satis)[0, 1]
axes[0].text(0.05, 0.92, f'r = {r:.3f}', transform=axes[0].transAxes,
             fontsize=11, bbox=dict(facecolor='lightyellow', alpha=0.8))

axes[0].set_title("Reklam Harcaması — Satış İlişkisi")
axes[0].set_xlabel("Reklam Harcaması (bin TL)")
axes[0].set_ylabel("Satış (adet)")
axes[0].legend(fontsize=9)
axes[0].grid(True, alpha=0.3)

# --- Sağ: Boyut ve renk ile üç değişken ---
buyume = rng.uniform(5, 40, 80)   # üçüncü değişken: büyüme oranı
sc = axes[1].scatter(reklam, satis,
                     s=buyume * 5,         # nokta boyutu büyümeyle orantılı
                     c=buyume,             # renk de büyümeyle değişiyor
                     cmap='plasma',
                     alpha=0.75,
                     edgecolors='white', linewidths=0.4)

plt.colorbar(sc, ax=axes[1], label='Büyüme Oranı (%)')
axes[1].set_title("Üç Değişkenli Görselleştirme\n(boyut + renk = büyüme oranı)")
axes[1].set_xlabel("Reklam Harcaması (bin TL)")
axes[1].set_ylabel("Satış (adet)")
axes[1].grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("korelasyon_analizi.png", dpi=140)
plt.show()
```

**Açıklama:**
`np.polyfit(x, y, 1)` ile tek geçişte doğrusal regresyon yapılır; katsayılar `polyval` ile yeni x değerlerine uygulanır. `ax.text(0.05, 0.92, ..., transform=ax.transAxes)` metni eksen koordinatlarına (0-1 arası) göre yerleştirir — eksen boyutu değişse bile metin konumu sabit kalır.

**Beklenen çıktı:**
Sol panelde bölgesel renkler ve regresyon çizgisi, sağ panelde üç değişkenin aynı anda görselleştirildiği renkli-boyutlu scatter.

**İyileştirme fikirleri:**
- Scatter'a mouse hover ile nokta bilgisi göstermek için `mplcursors` kütüphanesini deneyin.
- Residual (artık) diyagramı çizerek regresyonun uyumunu değerlendirin.

---

### Uygulama 1.3 — Çubuk Grafik: Kategori Karşılaştırması

**Problem:**
Zaman bazlı kategori verisini gruplu çubuk grafikle karşılaştırmak ve değerleri çubukların üstüne yazmak.

**Amaç:**
Gruplu çubuk grafik oluşturmayı, renk yönetimini ve veri etiketleme tekniklerini öğrenmek.

**Kullanılan teknikler:**
`ax.bar`, `ax.bar_label`, `np.arange`, yatay referans çizgisi

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

# Üç ürün kategorisi, dört çeyrek satış
kategoriler   = ['Elektronik', 'Giyim', 'Gıda', 'Kitap', 'Spor']
q1_satis = np.array([245, 180, 320, 95,  150])
q2_satis = np.array([310, 210, 295, 110, 185])
q3_satis = np.array([280, 260, 340, 130, 200])
q4_satis = np.array([420, 320, 380, 155, 220])

ceyrekler = [q1_satis, q2_satis, q3_satis, q4_satis]
isimler   = ['Q1', 'Q2', 'Q3', 'Q4']
renkler   = ['#4C72B0', '#DD8452', '#55A868', '#C44E52']

x = np.arange(len(kategoriler))
genislik = 0.18

fig, axes = plt.subplots(1, 2, figsize=(15, 6))
fig.suptitle("Kategori Bazlı Satış Analizi", fontsize=14, fontweight='bold')

# --- Sol: Gruplu çubuk grafik ---
for i, (veri, isim, renk) in enumerate(zip(ceyrekler, isimler, renkler)):
    konum   = x + (i - 1.5) * genislik
    cubuklar = axes[0].bar(konum, veri, genislik,
                            label=isim, color=renk, alpha=0.9,
                            edgecolor='white', linewidth=0.5)
    # Değerleri çubukların üstüne yaz
    axes[0].bar_label(cubuklar, fmt='%d', fontsize=7, padding=2)

axes[0].set_xticks(x)
axes[0].set_xticklabels(kategoriler, fontsize=10)
axes[0].set_title("Çeyreklik Satışlar (Gruplu)")
axes[0].set_ylabel("Satış Adedi")
axes[0].legend(title='Çeyrek', loc='upper left', fontsize=9)
axes[0].grid(True, axis='y', alpha=0.4)

# Yıllık ortalama referans çizgisi
yillik_ort = np.concatenate(ceyrekler).mean()
axes[0].axhline(yillik_ort, color='black', lw=1.5, ls='--',
                label=f'Yıllık ort: {yillik_ort:.0f}')
axes[0].legend(fontsize=9)

# --- Sağ: Yığılmış çubuk grafik (yüzde) ---
toplam     = sum(q.astype(float) for q in ceyrekler)
yuzde_veri = [(q / toplam) * 100 for q in ceyrekler]

altta = np.zeros(len(kategoriler))
for veri, isim, renk in zip(yuzde_veri, isimler, renkler):
    axes[1].bar(kategoriler, veri, bottom=altta,
                label=isim, color=renk, alpha=0.9, edgecolor='white')
    # Her bölüme yüzde yaz
    for i, (v, a) in enumerate(zip(veri, altta)):
        if v > 5:   # küçük dilimlerde yazma
            axes[1].text(i, a + v/2, f'{v:.0f}%', ha='center',
                         va='center', fontsize=8, color='white', fontweight='bold')
    altta += veri

axes[1].set_title("Çeyreklik Dağılım (Yığılmış, %)")
axes[1].set_ylabel("Yüzde")
axes[1].legend(title='Çeyrek', loc='upper right', fontsize=9)
axes[1].set_ylim(0, 105)

plt.tight_layout()
plt.savefig("cubuk_grafik.png", dpi=140)
plt.show()
```

**Açıklama:**
Gruplu çubuk grafik için her grubun konumu `x + (i - 1.5) * genislik` ile hesaplanır; bu formül grupları merkez etrafında eşit dağıtır. `ax.bar_label(cubuklar)` çubukların üstüne değerleri otomatik olarak yerleştirir. Yığılmış grafik için `bottom` parametresi her katmanın başlangıç yüksekliğini belirler; kümülatif olarak artırılır.

**Beklenen çıktı:**
Sol panelde kategoriler yan yana dört çeyreğin çubuklarıyla, sağ panelde her kategorinin çeyreklik yüzde dağılımı.

**İyileştirme fikirleri:**
- `ax.barh` ile yatay çubuk grafik versiyonu yapın; çok kategorili veride daha okunabilir olabilir.
- En hızlı büyüyen kategorinin üstüne ok ve metin ekleyin.

---

## BLOK 2 — İstatistiksel Görselleştirme

---

### Uygulama 2.1 — Histogram ve Dağılım Analizi

**Problem:**
Birden fazla grubun dağılımını histogramla karşılaştırmak ve teorik dağılım eğrisini üzerine çizmek.

**Amaç:**
`plt.hist`, `density=True`, Gaussian yoğunluk eğrisi ve dağılım karşılaştırmasını öğrenmek.

**Kullanılan teknikler:**
`plt.hist`, `plt.hist2d`, `np.linspace`, Gaussian PDF

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np
from scipy import stats  # pip install scipy

rng = np.random.default_rng(8)

# Dört farklı dağılım
normal   = rng.normal(loc=0, scale=1, size=2000)
ceyrek_n = rng.normal(loc=2, scale=0.5, size=2000)
uniform  = rng.uniform(-3, 3, size=2000)
skewed   = rng.exponential(scale=1, size=2000) - 1   # kaydırılmış üstel

fig, axes = plt.subplots(2, 2, figsize=(13, 9))
fig.suptitle("Dağılım Analizi", fontsize=14, fontweight='bold')

# --- Normal dağılım + teorik eğri ---
ax = axes[0, 0]
ax.hist(normal, bins=40, density=True, color='steelblue',
        alpha=0.7, edgecolor='white', label='Gözlem')
x_t = np.linspace(normal.min(), normal.max(), 200)
ax.plot(x_t, stats.norm.pdf(x_t, 0, 1), 'r-', lw=2.5, label='Teorik N(0,1)')
ax.axvline(normal.mean(), color='black', lw=1.5, ls='--',
           label=f'Ort: {normal.mean():.3f}')
ax.set_title(f"Normal Dağılım  (skewness={stats.skew(normal):.2f})")
ax.legend(fontsize=9)
ax.grid(True, alpha=0.3)

# --- Çakışan iki dağılım ---
ax = axes[0, 1]
ax.hist(normal,   bins=40, density=True, alpha=0.55, color='royalblue', label='N(0,1)')
ax.hist(ceyrek_n, bins=40, density=True, alpha=0.55, color='tomato',    label='N(2,0.5)')
ax.set_title("İki Normal Dağılım Karşılaştırması")
ax.legend(fontsize=10)
ax.grid(True, alpha=0.3)

# --- Çarpık dağılım ---
ax = axes[1, 0]
ax.hist(skewed, bins=40, density=True, color='seagreen', alpha=0.7, edgecolor='white')
x_sk = np.linspace(skewed.min(), skewed.max(), 200)
ax.plot(x_sk, stats.expon.pdf(x_sk + 1), 'r-', lw=2, label='Teorik üstel')
ax.axvline(np.median(skewed), color='orange', lw=2, ls='--',
           label=f'Medyan: {np.median(skewed):.2f}')
ax.axvline(skewed.mean(), color='black', lw=2, ls='-.',
           label=f'Ort: {skewed.mean():.2f}')
ax.set_title(f"Çarpık Dağılım  (skewness={stats.skew(skewed):.2f})")
ax.legend(fontsize=9)
ax.grid(True, alpha=0.3)

# --- 2D Histogram (yoğunluk haritası) ---
ax = axes[1, 1]
x2d = rng.normal(0, 1, 5000)
y2d = 0.7 * x2d + rng.normal(0, 0.8, 5000)
h, xedges, yedges, im = ax.hist2d(x2d, y2d, bins=50, cmap='YlOrRd')
plt.colorbar(im, ax=ax, label='Frekans')
ax.set_title("2D Histogram — Korelasyonlu Veri")
ax.set_xlabel("x")
ax.set_ylabel("y")

plt.tight_layout()
plt.savefig("dagilim_analizi.png", dpi=140)
plt.show()

# İstatistiksel özet
print("=== DAĞILIM ÖZETİ ===")
for veri, isim in [(normal,'Normal'), (ceyrek_n,'N(2,0.5)'),
                    (uniform,'Uniform'), (skewed,'Çarpık')]:
    print(f"{isim:>12}: ort={veri.mean():+.3f} "
          f"std={veri.std():.3f} skew={stats.skew(veri):+.3f}")
```

**Açıklama:**
`density=True` histogramı alanı 1 olan olasılık yoğunluk grafiğine çevirir; bu sayede teorik PDF eğrisi direkt üzerine çizilebilir. `stats.norm.pdf` teorik yoğunluk değerlerini verir. Çarpıklıkta (skewness) ortalama ile medyan arasındaki fark net görülür: sağa çarpık dağılımda ortalama her zaman medyandan büyüktür.

**Beklenen çıktı:**
Dört panelli ızgara: normal dağılım ve teorik eğri, iki dağılım çakışması, çarpık dağılımda ort-medyan farkı, 2D histogram ısı haritası.

**İyileştirme fikirleri:**
- `scipy.stats.probplot` ile Q-Q grafiği çizerek normallik testi yapın.
- KDE (Kernel Density Estimate) için `scipy.stats.gaussian_kde` ekleyin.

---

### Uygulama 2.2 — Kutu Grafik ve Violin Plot Karşılaştırması

**Problem:**
Gruplar arası dağılım farklarını kutu grafik ve violin plot ile karşılaştırmak.

**Amaç:**
`boxplot` ve `violinplot`'ın ne zaman tercih edildiğini görsel olarak anlamak.

**Kullanılan teknikler:**
`ax.boxplot`, `ax.violinplot`, `ax.swarmplot` (manuel)

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np
from scipy import stats

rng = np.random.default_rng(30)

# Beş grubun performans puanları
gruplar = {
    'A — Dengeli':    rng.normal(70, 8, 120),
    'B — Değişken':   rng.normal(70, 20, 120),
    'C — Yüksek':     rng.normal(85, 6, 120),
    'D — Bimodal':    np.concatenate([rng.normal(55, 5, 60), rng.normal(85, 5, 60)]),
    'E — Çarpık':     rng.exponential(10, 120) + 50,
}

isimler = list(gruplar.keys())
veriler = list(gruplar.values())

fig, axes = plt.subplots(1, 2, figsize=(15, 7))
fig.suptitle("Dağılım Karşılaştırma: Boxplot vs Violin Plot", fontsize=13)

# --- Kutu Grafik ---
bp = axes[0].boxplot(veriler, labels=isimler, patch_artist=True,
                      medianprops=dict(color='white', lw=2.5),
                      whiskerprops=dict(lw=1.5),
                      capprops=dict(lw=1.5))

renkler = ['#4C72B0', '#DD8452', '#55A868', '#C44E52', '#8172B2']
for patch, renk in zip(bp['boxes'], renkler):
    patch.set_facecolor(renk)
    patch.set_alpha(0.8)

# Her grubun medyanını text olarak ekle
for i, veri in enumerate(veriler):
    medyan = np.median(veri)
    axes[0].text(i+1, medyan + 1, f'{medyan:.0f}', ha='center',
                 fontsize=9, fontweight='bold', color='white')

axes[0].set_title("Boxplot — 5 Özet İstatistik")
axes[0].set_ylabel("Puan")
axes[0].grid(True, axis='y', alpha=0.3)
axes[0].tick_params(axis='x', rotation=20)

# --- Violin Plot ---
vp = axes[1].violinplot(veriler, positions=range(1, 6),
                         showmeans=True, showmedians=True,
                         showextrema=True)

for pc, renk in zip(vp['bodies'], renkler):
    pc.set_facecolor(renk)
    pc.set_alpha(0.7)

vp['cmedians'].set_color('white')
vp['cmedians'].set_linewidth(2)
vp['cmeans'].set_color('black')
vp['cmeans'].set_linestyle('--')

axes[1].set_xticks(range(1, 6))
axes[1].set_xticklabels(isimler, rotation=20, ha='right')
axes[1].set_title("Violin Plot — Tam Dağılım Şekli")
axes[1].set_ylabel("Puan")
axes[1].grid(True, axis='y', alpha=0.3)

# Açıklama notu ekle
axes[1].text(0.02, 0.98,
             "Kesik çizgi = Ortalama\nBeyaz çizgi = Medyan",
             transform=axes[1].transAxes, fontsize=9,
             va='top', bbox=dict(facecolor='lightyellow', alpha=0.8))

plt.tight_layout()
plt.savefig("boxplot_violin.png", dpi=140)
plt.show()

print("=== NEDEN VİOLIN? ===")
print("D grubunun bimodal (çift tepeli) dağılımı")
print("boxplot'ta 'geniş kutu' olarak görünür.")
print("Violin plot ise iki ayrı tepeyi açıkça gösterir.")
```

**Açıklama:**
Boxplot 5 özet istatistiği (min, Q1, medyan, Q3, max) sıkıştırılmış biçimde gösterir — 120 sayı 5 sayıya sığar. Violin plot ise gerçek dağılım şeklini yansıtır; bu, bimodal (D grubu) veya çarpık (E grubu) dağılımlarda çok daha bilgi vericidir. Gruplar karmaşık yapı içeriyorsa violin tercih edilmeli, hızlı istatistik karşılaştırması için boxplot yeterlidir.

**Beklenen çıktı:**
Sol panelde klasik kutu grafik, sağ panelde violin şekli. D grubunun bimodal yapısı violin'de iki ayrı dolgu bölgesiyle görünür, boxplot'ta bu detay gizlenir.

**İyileştirme fikirleri:**
- Violin üzerine veri noktalarını eklemek için `np.random.uniform` ile yatay jitter uygulayın.
- Her gruba ait medyanlar arası istatistiksel anlamlılık testini yazdırın.

---

## BLOK 3 — Çoklu Panel ve Karşılaştırma

---

### Uygulama 3.1 — Çok Panelli Veri Analizi Panosu

**Problem:**
Bir satış veri setini farklı açılardan analiz eden birden fazla grafiği tek figure içinde organize etmek.

**Amaç:**
`gridspec` ile özel yerleşim oluşturmayı ve birden fazla grafik tipini uyumlu biçimde bir araya getirmeyi öğrenmek.

**Kullanılan teknikler:**
`gridspec.GridSpec`, `fig.add_subplot`, `ax.twinx` (ikinci y ekseni)

**Pipeline:**
```
Satış verisi
    → zaman serisi çizgisi (trend)
    → aylık histogram
    → kategori çubuk
    → trend analizi
```

**Kod:**
```python
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np

rng = np.random.default_rng(42)

# Veri üret: 365 günlük satış
gunler     = np.arange(365)
trend_veri = 100 + 0.2 * gunler
mevsim     = 30 * np.sin(2 * np.pi * gunler / 365)
gurultu    = rng.normal(0, 12, 365)
satis      = trend_veri + mevsim + gurultu

# Aylık ort. ve toplam
aylar = np.array_split(satis, 12)
aylik_ort   = np.array([a.mean() for a in aylar])
aylik_top   = np.array([a.sum()  for a in aylar])
ay_isimleri = ['Oca','Şub','Mar','Nis','May','Haz',
                'Tem','Ağu','Eyl','Eki','Kas','Ara']

# Figure + GridSpec
fig = plt.figure(figsize=(15, 10))
fig.suptitle("Yıllık Satış Analiz Panosu", fontsize=16, fontweight='bold', y=0.98)
gs = gridspec.GridSpec(2, 3, figure=fig, hspace=0.4, wspace=0.35)

# --- Üst: Tam genişlik zaman serisi ---
ax_zaman = fig.add_subplot(gs[0, :])
pencere  = np.ones(14) / 14
ort_14   = np.convolve(satis, pencere, mode='same')

ax_zaman.plot(gunler, satis, alpha=0.35, color='steelblue', lw=0.8, label='Günlük')
ax_zaman.plot(gunler, ort_14, color='royalblue', lw=2.5, label='14 Gün Ort.')
ax_zaman.fill_between(gunler, satis, ort_14, alpha=0.12, color='steelblue')
ax_zaman.set_title("Günlük Satış Eğrisi", fontsize=12)
ax_zaman.set_xlabel("Gün")
ax_zaman.set_ylabel("Satış")
ax_zaman.legend()
ax_zaman.grid(True, alpha=0.3)

# --- Alt sol: Aylık çubuk + kümülatif çizgi (çift eksen) ---
ax_ay  = fig.add_subplot(gs[1, 0])
ax_top = ax_ay.twinx()   # ikinci y ekseni

renkler_ay = ['tomato' if a == aylik_ort.max() else 'steelblue' for a in aylik_ort]
ax_ay.bar(ay_isimleri, aylik_ort, color=renkler_ay, alpha=0.8, edgecolor='white')
ax_ay.set_title("Aylık Ortalama + Kümülatif")
ax_ay.set_ylabel("Günlük Ort.", color='steelblue')
ax_ay.tick_params(axis='x', rotation=45)
ax_ay.grid(True, axis='y', alpha=0.3)

kumul = np.cumsum(aylik_top)
ax_top.plot(ay_isimleri, kumul / 1000, 'ko--', lw=1.5, ms=4, label='Kümülatif')
ax_top.set_ylabel("Kümülatif (×1000)", color='black')

# --- Alt orta: Dağılım histogram ---
ax_hist = fig.add_subplot(gs[1, 1])
ax_hist.hist(satis, bins=35, color='seagreen', edgecolor='white', alpha=0.8)
ax_hist.axvline(satis.mean(), color='red', lw=2, ls='--',
                label=f'Ort: {satis.mean():.0f}')
ax_hist.axvline(np.median(satis), color='orange', lw=2, ls=':',
                label=f'Med: {np.median(satis):.0f}')
ax_hist.set_title("Satış Değer Dağılımı")
ax_hist.set_xlabel("Satış")
ax_hist.set_ylabel("Gün Sayısı")
ax_hist.legend(fontsize=9)
ax_hist.grid(True, alpha=0.3)

# --- Alt sağ: Mevsimsel heatmap ---
ax_isi = fig.add_subplot(gs[1, 2])
haftalik = satis[:364].reshape(52, 7)   # 52 hafta × 7 gün
im = ax_isi.imshow(haftalik.T, aspect='auto', cmap='RdYlGn',
                    interpolation='nearest')
ax_isi.set_title("Haftalık Isı Haritası")
ax_isi.set_xlabel("Hafta")
ax_isi.set_yticks(range(7))
ax_isi.set_yticklabels(['Pzt','Sal','Çar','Per','Cum','Cmt','Paz'])
plt.colorbar(im, ax=ax_isi, fraction=0.046, pad=0.04)

plt.savefig("analiz_panosu.png", dpi=140, bbox_inches='tight')
plt.show()
```

**Açıklama:**
`gridspec.GridSpec(2, 3)` 2 satır 3 sütunluk bir ızgara tanımlar. `gs[0, :]` ilk satırın tüm sütunlarını kapsayan tek bir alan verir. `ax.twinx()` aynı x eksenini paylaşan ikinci y ekseni oluşturur; çubuk ve çizgi gibi farklı ölçekteki veriler aynı panelde gösterilebilir. Isı haritasında `reshape(52, 7)` 364 günü hafta×gün matrisine dönüştürür; `imshow` ile görselleştirilir.

**Beklenen çıktı:**
Üstte geniş zaman serisi, altta üç farklı analiz: aylık çubuk+kümülatif, dağılım histogramı ve haftalık ısı haritası.

**İyileştirme fikirleri:**
- `fig.text(0.5, 0.01, 'Kaynak: Simülasyon verisi', ha='center')` ile kaynak dipnotu ekleyin.
- Isı haritasında en yüksek günleri `ax.annotate` ile işaretleyin.

---

### Uygulama 3.2 — Çizgi Grafik Animasyonu (Statik Seriler)

**Problem:**
Birden fazla yılın aylık verisini aynı grafikte birleştirerek yıllar arası karşılaştırma yapmak.

**Amaç:**
Çok serili görselleştirme, renk haritasından otomatik renk seçimi ve grafik açıklamalarını öğrenmek.

**Kullanılan teknikler:**
`plt.get_cmap`, birden fazla çizgi, şerit (`fill_between`) ve açıklama

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(77)
aylar = ['Oca','Şub','Mar','Nis','May','Haz',
          'Tem','Ağu','Eyl','Eki','Kas','Ara']
x = np.arange(12)

# 2019–2024 aylık ziyaretçi verisi simülasyonu
yillar = range(2019, 2025)
veri = {}
baz = np.array([120, 115, 140, 165, 200, 240, 260, 255, 210, 175, 140, 125])
for i, yil in enumerate(yillar):
    buyume = 1 + 0.07 * i
    gurultu = rng.normal(0, 12, 12)
    veri[yil] = (baz * buyume + gurultu).clip(50, None)

# Renk haritasından otomatik renkler
renk_haritasi = plt.get_cmap('tab10')
fig, axes = plt.subplots(1, 2, figsize=(15, 6))

# --- Sol: Tüm yıllar üst üste ---
for i, yil in enumerate(yillar):
    renk = renk_haritasi(i)
    kalinlik = 3.0 if yil == 2024 else 1.5
    stil = '-' if yil == 2024 else '--'
    alpha = 1.0 if yil == 2024 else 0.6
    axes[0].plot(x, veri[yil], color=renk, lw=kalinlik,
                 ls=stil, alpha=alpha, marker='o', ms=4 if yil==2024 else 2,
                 label=str(yil))

# 2024 için alanı gölgele
axes[0].fill_between(x, veri[2024], alpha=0.12, color=renk_haritasi(5))

axes[0].set_xticks(x)
axes[0].set_xticklabels(aylar)
axes[0].set_title("Yıllık Ziyaretçi Karşılaştırması (2019–2024)")
axes[0].set_ylabel("Ziyaretçi (bin)")
axes[0].legend(loc='upper left', fontsize=9)
axes[0].grid(True, alpha=0.3)
axes[0].tick_params(axis='x', rotation=30)

# Yıl etiketlerini çizgi sonuna ekle
for i, yil in enumerate(yillar):
    axes[0].annotate(str(yil), xy=(11, veri[yil][-1]),
                     fontsize=8, color=renk_haritasi(i), va='center')

# --- Sağ: Yıllık büyüme oranı ---
buyume_oranlari = {}
for i in range(1, len(list(yillar))):
    y_once = list(yillar)[i-1]
    y_simd = list(yillar)[i]
    buyume_oranlari[y_simd] = ((veri[y_simd] - veri[y_once]) / veri[y_once]) * 100

for i, (yil, oran) in enumerate(buyume_oranlari.items()):
    renk = renk_haritasi(i + 1)
    axes[1].plot(x, oran, color=renk, lw=1.8, marker='s', ms=5, label=str(yil))
    axes[1].fill_between(x, oran, 0, alpha=0.08, color=renk)

axes[1].axhline(0, color='black', lw=1.2)
axes[1].set_xticks(x)
axes[1].set_xticklabels(aylar)
axes[1].set_title("Aylık Büyüme Oranı (YoY, %)")
axes[1].set_ylabel("Büyüme (%)")
axes[1].legend(fontsize=9)
axes[1].grid(True, alpha=0.3)
axes[1].tick_params(axis='x', rotation=30)

plt.tight_layout()
plt.savefig("yillik_karsilastirma.png", dpi=140)
plt.show()
```

**Açıklama:**
`plt.get_cmap('tab10')` her yıl için farklı bir renk üretir; Manuel renk listesi tutmak yerine renk haritasından indeks ile seçim yapılır. 2024 çizgisi kalın ve kesintisiz; diğer yıllar ince ve kesik — bu "vurgu" tekniği okuyucunun gözünü en önemli seriye çeker. YoY (Year-over-Year) büyüme oranı her ayda bir önceki yıla kıyasla değişimi gösterir.

**Beklenen çıktı:**
Sol panelde 6 yılın aylık trendi, sağ panelde yıllık büyüme oranları. Sıfır çizgisi büyüme/küçülme sınırını gösterir.

**İyileştirme fikirleri:**
- Her serinin son noktasının yanına ek metin annotasyon ekleyin.
- `plt.style.use('dark_background')` ile koyu tema deneyin.

---

## BLOK 4 — Görüntü Görselleştirme

---

### Uygulama 4.1 — OpenCV Görüntüsünü Matplotlib ile Gösterme

**Problem:**
OpenCV ile yüklenen görüntüleri Jupyter/Matplotlib'de doğru renklerle göstermek ve kanal analizini görselleştirmek.

**Amaç:**
BGR-RGB dönüşümünü, `imshow` ile kanal görselleştirmeyi ve görüntü histogramını birleştirmeyi öğrenmek.

**Kullanılan teknikler:**
`cv2.cvtColor`, `plt.imshow`, `cmap='gray'`, `plt.colorbar`

**Pipeline:**
```
OpenCV görüntü (BGR)
    → BGR→RGB dönüşümü
    → kanal ayrımı
    → her kanal için imshow (gri harita)
    → histogram görselleştirme
```

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

try:
    import cv2
    img_bgr = cv2.imread("foto.jpg")
    if img_bgr is not None:
        img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)
    else:
        raise FileNotFoundError
except:
    # Yapay görüntü (OpenCV yoksa veya dosya bulunamazsa)
    img_rgb = np.zeros((300, 400, 3), dtype=np.uint8)
    for r in range(300):
        img_rgb[r, :, 0] = int(r / 300 * 255)
    for c in range(400):
        img_rgb[:, c, 1] = int(c / 400 * 255)
    img_rgb[:, :, 2] = 80
    rng = np.random.default_rng(5)
    img_rgb = np.clip(img_rgb + rng.integers(-15, 15, img_rgb.shape), 0, 255).astype(np.uint8)

# Kanalları ayır
r_kanali = img_rgb[:, :, 0]
g_kanali = img_rgb[:, :, 1]
b_kanali = img_rgb[:, :, 2]
gri      = img_rgb.mean(axis=2).astype(np.uint8)

# Figure
fig, axes = plt.subplots(3, 4, figsize=(16, 12))
fig.suptitle("Görüntü Kanal Analizi", fontsize=14, fontweight='bold')

# Satır 1: Orijinal ve kanallar
axes[0,0].imshow(img_rgb)
axes[0,0].set_title("Orijinal (RGB)")
axes[0,0].axis('off')

kanal_bilgi = [
    (r_kanali, 'Reds',   "R Kanalı"),
    (g_kanali, 'Greens', "G Kanalı"),
    (b_kanali, 'Blues',  "B Kanalı"),
]
for ax, (kanal, cmap, baslik) in zip(axes[0,1:], kanal_bilgi):
    im = ax.imshow(kanal, cmap=cmap, vmin=0, vmax=255)
    ax.set_title(baslik)
    ax.axis('off')
    plt.colorbar(im, ax=ax, fraction=0.046, pad=0.04)

# Satır 2: Gri ve renk haritası varyasyonları
cmaplar = [('gray', 'Gri Tonlama'), ('plasma', 'Plasma'), ('viridis', 'Viridis')]
for ax, (cmap, baslik) in zip(axes[1,:3], cmaplar):
    ax.imshow(gri, cmap=cmap, vmin=0, vmax=255)
    ax.set_title(f"Gri → {baslik}")
    ax.axis('off')

# HSV görünümü
hsv = np.zeros_like(img_rgb)
hsv[:,:,0] = (r_kanali.astype(int) - b_kanali.astype(int)).clip(0,255)
axes[1,3].imshow(hsv)
axes[1,3].set_title("(R-B) Fark Görünümü")
axes[1,3].axis('off')

# Satır 3: Histogramlar
kanal_renk = [('R', r_kanali, 'red'), ('G', g_kanali, 'green'),
               ('B', b_kanali, 'blue'), ('Gri', gri, 'gray')]
for ax, (isim, kanal, renk) in zip(axes[2,:], kanal_renk):
    ax.hist(kanal.ravel(), bins=64, range=(0, 256),
            color=renk, alpha=0.8, edgecolor='none')
    ax.set_title(f"{isim} Kanal Histogramı")
    ax.set_xlabel("Piksel Değeri")
    ax.set_ylabel("Piksel Sayısı")
    ax.axvline(kanal.mean(), color='black', lw=2, ls='--',
               label=f'Ort:{kanal.mean():.0f}')
    ax.legend(fontsize=8)
    ax.grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("kanal_analizi.png", dpi=130)
plt.show()

print("Kanal Ortalamaları:")
for isim, kanal in [('R', r_kanali), ('G', g_kanali), ('B', b_kanali), ('Gri', gri)]:
    print(f"  {isim}: {kanal.mean():.1f} ± {kanal.std():.1f}")
```

**Açıklama:**
`cmap='Reds'` kanalı kırmızı tonlarında gösterir; sıfır değer koyu, 255 değer açık kırmızı olur. `vmin=0, vmax=255` colorbar ölçeğini sabit tutar; farklı kanalların aynı ölçekte karşılaştırılmasını sağlar. Histogram satırı her kanalın dağılımını ayrı ayrı gösterir; yüksek ortalama o rengin görüntüde baskın olduğunu işaret eder.

**Beklenen çıktı:**
3 satır × 4 sütun ızgara: orijinal + kanal görüntüleri, gri dönüşüm varyasyonları ve kanal histogramları.

**İyileştirme fikirleri:**
- `plt.imshow(img_rgb[100:200, 150:300])` ile bir ROI yakın çekimini gösterin.
- Her kanalın min-max normalizasyonunu uygulayıp dağılım değişimini gözlemleyin.

---

### Uygulama 4.2 — Görüntü İşleme Sonuçlarını Karşılaştırma

**Problem:**
Farklı filtre veya eşikleme yöntemlerinin görüntü üzerindeki etkisini yan yana görselleştirmek.

**Amaç:**
Görüntü işleme pipeline'ının her adımını Matplotlib ile izlemeyi öğrenmek.

**Kullanılan teknikler:**
`plt.imshow`, `ax.axis('off')`, metrik tablosu, `cmap='hot'`

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

# Görüntü yükle veya yapay oluştur
try:
    import cv2
    img = cv2.imread("foto.jpg")
    if img is None:
        raise FileNotFoundError
    img_gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    img_rgb  = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
except:
    from scipy import ndimage
    rng = np.random.default_rng(3)
    img_gray = np.zeros((200, 300), dtype=np.uint8)
    img_gray[60:140, 80:220] = 180
    for _ in range(5):
        cx, cy = rng.integers(20, 280), rng.integers(20, 180)
        r = rng.integers(10, 40)
        Y, X = np.ogrid[:200, :300]
        masque = (X-cx)**2 + (Y-cy)**2 <= r**2
        img_gray[masque] = rng.integers(100, 255)
    img_gray = ndimage.gaussian_filter(img_gray.astype(float), sigma=1).astype(np.uint8)
    img_gray += rng.integers(0, 20, img_gray.shape, dtype=np.uint8)
    img_rgb  = np.stack([img_gray]*3, axis=2)

# Numpy ile işlemler (OpenCV yoksa)
def gaussian_blur_np(g, sigma=2):
    from scipy import ndimage
    return ndimage.gaussian_filter(g.astype(float), sigma=sigma).astype(np.uint8)

def canny_np(g, low=50, high=150):
    try:
        import cv2
        return cv2.Canny(g, low, high)
    except:
        from scipy import ndimage
        gx = ndimage.sobel(g.astype(float), axis=1)
        gy = ndimage.sobel(g.astype(float), axis=0)
        mag = np.sqrt(gx**2 + gy**2)
        return (mag > low).astype(np.uint8) * 255

bulanik   = gaussian_blur_np(img_gray, sigma=3)
esik_127  = (img_gray > 127).astype(np.uint8) * 255
esik_otsu_val = img_gray.mean()
esik_otsu = (img_gray > esik_otsu_val).astype(np.uint8) * 255
kenarlar  = canny_np(bulanik)
fark      = np.abs(img_gray.astype(int) - bulanik.astype(int)).astype(np.uint8)

veriler = [
    (img_gray,   'gray',  "Orijinal Gri"),
    (bulanik,    'gray',  "Gaussian Blur (σ=3)"),
    (esik_127,   'gray',  "Eşik (T=127)"),
    (esik_otsu,  'gray',  f"Otsu (T≈{esik_otsu_val:.0f})"),
    (kenarlar,   'gray',  "Kenar Tespiti"),
    (fark,       'hot',   "Fark Haritası"),
]

fig, axes = plt.subplots(2, 3, figsize=(14, 9))
fig.suptitle("Görüntü İşleme Adımları", fontsize=13, fontweight='bold')

for ax, (gorsel, cmap, baslik) in zip(axes.flat, veriler):
    im = ax.imshow(gorsel, cmap=cmap, vmin=0, vmax=255)
    ax.set_title(baslik, fontsize=10)
    ax.axis('off')
    ort = gorsel.mean()
    std = gorsel.std()
    ax.set_xlabel(f"ort={ort:.0f} std={std:.0f}", fontsize=8)

    # Fark haritasında colorbar ekle
    if cmap == 'hot':
        plt.colorbar(im, ax=ax, fraction=0.046, pad=0.04)

# Görünür etiketleri aç
for ax in axes.flat:
    ax.xaxis.set_visible(True)
    ax.tick_params(left=False, bottom=False, labelleft=False, labelbottom=True)

plt.tight_layout()
plt.savefig("isleme_adim.png", dpi=130)
plt.show()
```

**Açıklama:**
Her panel `(görüntü, renk haritası, başlık)` tuple'ıyla tanımlanır — yeni adım eklemek `veriler` listesine satır eklemektir. Fark haritası `'hot'` colormap kullanır çünkü sıfır fark siyah, büyük fark parlak beyaz görünür; bu farklılıkların hızlıca tespit edilmesini sağlar. `ax.set_xlabel` ile başlık yerine metrikleri alt etikete yerleştirmek alan tasarrufu sağlar.

**Beklenen çıktı:**
6 panel: orijinal gri, bulanık, iki eşikleme yöntemi, kenar haritası ve fark ısı haritası.

**İyileştirme fikirleri:**
- Her adımın piksel sayım histogramını küçük inset grafik olarak ekleyin.
- `cv2.createCLAHE` uygulamasının öncesi ve sonrasını karşılaştırın.

---

## BLOK 5 — Mini Projeler

---

### Uygulama 5.1 — İnteraktif Veri Analiz Panosu

**Problem:**
Çok boyutlu bir veri setini farklı açılardan analiz eden, temiz ve yayına hazır bir görsel sunum oluşturmak.

**Amaç:**
Farklı grafik tiplerini, özel yerleşim düzenini ve görsel biçimlendirmeyi birleştirerek profesyonel görünümlü bir analiz panosu üretmek.

**Kod:**
```python
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np

plt.rcParams.update({
    'font.family': 'sans-serif',
    'axes.spines.top': False,
    'axes.spines.right': False,
    'axes.labelsize': 10,
    'axes.titlesize': 11,
    'xtick.labelsize': 9,
    'ytick.labelsize': 9,
})

rng = np.random.default_rng(17)

# ── Veri ──────────────────────────────────────────────────────────────
n = 200
gelir     = rng.normal(8500, 2000, n).clip(3000, 20000)
yas       = rng.integers(22, 65, n)
egitim    = rng.integers(1, 5, n)             # 1=lise, 2=ön lisans, 3=lisans, 4=y.lisans
departman = rng.integers(0, 4, n)
dep_isim  = ['Mühendislik', 'Pazarlama', 'Finans', 'İnsan Kaynakları']
dep_renk  = ['#2196F3', '#FF9800', '#4CAF50', '#9C27B0']

# ── Figure ────────────────────────────────────────────────────────────
fig = plt.figure(figsize=(16, 12))
fig.patch.set_facecolor('#F8F9FA')

gs = gridspec.GridSpec(3, 4, figure=fig,
                        hspace=0.5, wspace=0.4,
                        top=0.88, bottom=0.06)

# Başlık bandı
fig.text(0.5, 0.93, "Çalışan Gelir Analizi Panosu",
         ha='center', fontsize=18, fontweight='bold', color='#212121')
fig.text(0.5, 0.895, f"n = {n} çalışan  |  Simülasyon verisi",
         ha='center', fontsize=10, color='gray')

# ── Panel 1: Gelir Dağılımı (geniş) ──────────────────────────────────
ax1 = fig.add_subplot(gs[0, :2])
ax1.set_facecolor('white')
ax1.hist(gelir, bins=30, color='#2196F3', alpha=0.8, edgecolor='white')
ax1.axvline(gelir.mean(), color='#F44336', lw=2, ls='--',
             label=f'Ort: {gelir.mean():.0f}₺')
ax1.axvline(np.median(gelir), color='#FF9800', lw=2, ls=':',
             label=f'Med: {np.median(gelir):.0f}₺')
ax1.set_title("Gelir Dağılımı")
ax1.set_xlabel("Aylık Gelir (₺)")
ax1.set_ylabel("Çalışan Sayısı")
ax1.legend(fontsize=9)
ax1.grid(True, axis='y', alpha=0.3)

# ── Panel 2: Departman Pasta ──────────────────────────────────────────
ax2 = fig.add_subplot(gs[0, 2])
ax2.set_facecolor('white')
sayimlar = [(departman == d).sum() for d in range(4)]
ax2.pie(sayimlar, labels=[f'{i}\n{c}' for i,c in zip(dep_isim, sayimlar)],
        colors=dep_renk, autopct='%1.0f%%',
        startangle=90, textprops={'fontsize': 8})
ax2.set_title("Departman Dağılımı")

# ── Panel 3: Eğitim Çubuk ────────────────────────────────────────────
ax3 = fig.add_subplot(gs[0, 3])
ax3.set_facecolor('white')
egitim_isim = ['Lise', 'Ön Lisans', 'Lisans', 'Y.Lisans']
egitim_ort  = [gelir[egitim==e].mean() for e in range(1,5)]
renk_egitim = ['#BBDEFB', '#64B5F6', '#1976D2', '#0D47A1']
cubuklar = ax3.bar(egitim_isim, egitim_ort, color=renk_egitim, edgecolor='white')
ax3.bar_label(cubuklar, fmt='%.0f₺', fontsize=8, padding=2)
ax3.set_title("Eğitim → Gelir")
ax3.set_ylabel("Ort. Gelir (₺)")
ax3.tick_params(axis='x', rotation=30)
ax3.grid(True, axis='y', alpha=0.3)

# ── Panel 4: Yaş — Gelir Scatter ─────────────────────────────────────
ax4 = fig.add_subplot(gs[1, :2])
ax4.set_facecolor('white')
for d in range(4):
    maske = departman == d
    ax4.scatter(yas[maske], gelir[maske],
                color=dep_renk[d], alpha=0.6, s=30,
                label=dep_isim[d], edgecolors='white', lw=0.3)
kats = np.polyfit(yas, gelir, 1)
x_r  = np.linspace(yas.min(), yas.max(), 100)
ax4.plot(x_r, np.polyval(kats, x_r), 'k--', lw=2, alpha=0.6,
          label=f'Trend (r={np.corrcoef(yas,gelir)[0,1]:.2f})')
ax4.set_title("Yaş — Gelir İlişkisi")
ax4.set_xlabel("Yaş")
ax4.set_ylabel("Aylık Gelir (₺)")
ax4.legend(fontsize=8, loc='upper left', ncol=2)
ax4.grid(True, alpha=0.3)

# ── Panel 5: Departman Kutu ───────────────────────────────────────────
ax5 = fig.add_subplot(gs[1, 2:])
ax5.set_facecolor('white')
veri_dep  = [gelir[departman==d] for d in range(4)]
bp = ax5.boxplot(veri_dep, labels=dep_isim, patch_artist=True,
                  medianprops=dict(color='white', lw=2))
for patch, renk in zip(bp['boxes'], dep_renk):
    patch.set_facecolor(renk)
    patch.set_alpha(0.8)
ax5.set_title("Departmanlara Göre Gelir")
ax5.set_ylabel("Aylık Gelir (₺)")
ax5.tick_params(axis='x', rotation=20)
ax5.grid(True, axis='y', alpha=0.3)

# ── Panel 6: Metrik Özet ─────────────────────────────────────────────
ax6 = fig.add_subplot(gs[2, :])
ax6.axis('off')
metrikler = [
    ("Toplam Çalışan", f"{n}"),
    ("Ort. Gelir", f"{gelir.mean():.0f}₺"),
    ("Medyan Gelir", f"{np.median(gelir):.0f}₺"),
    ("Std Sapma", f"{gelir.std():.0f}₺"),
    ("Min Gelir", f"{gelir.min():.0f}₺"),
    ("Max Gelir", f"{gelir.max():.0f}₺"),
    ("10K+ Oran", f"%{(gelir>10000).mean()*100:.0f}"),
    ("Ort. Yaş", f"{yas.mean():.0f}"),
]
x_pos = np.linspace(0.05, 0.95, len(metrikler))
for i, (baslik, deger) in enumerate(metrikler):
    ax6.text(x_pos[i], 0.7,  deger,  ha='center', fontsize=16,
             fontweight='bold', color='#1976D2')
    ax6.text(x_pos[i], 0.25, baslik, ha='center', fontsize=8, color='gray')
ax6.set_title("Özet Metrikler", fontsize=11, pad=5)
ax6.patch.set_facecolor('white')

plt.savefig("analiz_panosu_profesyonel.png", dpi=140, bbox_inches='tight')
plt.show()
```

**Açıklama:**
`plt.rcParams.update` tüm figure için global stil atar; ayrı ayrı her ekseni ayarlamak gerekmez. `fig.text` konumu figure koordinat sisteminde (0-1) verilir — axes'e bağlı değildir. Özet metrik satırı `ax6.axis('off')` ile eksen gizlenmiş, yalnızca metin içeren dekoratif bir paneldir; Matplotlib'de "tablo" yerine bu yöntem daha esnektir.

**Beklenen çıktı:**
Açık gri arka planlı, altı panelli profesyonel analiz panosu. Gelir dağılımı, departman dağılımı, eğitim-gelir ilişkisi, yaş-gelir scatter ve kutu grafik bir arada. Alt banda özet metrikler büyük ve okunaklı biçimde yazılı.

**İyileştirme fikirleri:**
- `plt.style.use('seaborn-v0_8-whitegrid')` ile alternatif stil deneyin.
- `ax.annotate` ile en yüksek maaşlı departmanı ok ile işaretleyin.

---

### Uygulama 5.2 — Görüntü Karşılaştırma Galerisi

**Problem:**
Birden fazla görüntüyü (veya işlenmiş versiyonlarını) etiketlenmiş galeriye dönüştürmek.

**Amaç:**
`imshow`, `axis('off')`, `colorbar` ve otomatik yerleşim mantığını pratikte kullanmak.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

def galeri_goster(goruntular, basliklar, sutun_sayisi=4,
                  figsize_carpan=3.5, colorbar=False,
                  cmap=None, baslangic_cmap='viridis',
                  ana_baslik="Görüntü Galerisi",
                  kaydet_yolu=None):
    """
    Görüntü listesini galeri düzeninde gösterir.

    Parametreler:
        goruntular   : 2D veya 3D NumPy dizi listesi
        basliklar    : Her görüntü için başlık listesi
        sutun_sayisi : Yatay sütun sayısı
        figsize_carpan: Her panel için genişlik çarpanı
        colorbar     : Her panel altına renk çubuğu ekle
        cmap         : Sabit renk haritası (None ise otomatik)
        ana_baslik   : Figure başlığı
    """
    n = len(goruntular)
    satir_sayisi = (n + sutun_sayisi - 1) // sutun_sayisi
    fig_genislik = sutun_sayisi * figsize_carpan
    fig_yukseklik = satir_sayisi * (figsize_carpan + 0.5)

    fig, axes = plt.subplots(satir_sayisi, sutun_sayisi,
                              figsize=(fig_genislik, fig_yukseklik))
    if satir_sayisi == 1:
        axes = axes.reshape(1, -1)
    elif sutun_sayisi == 1:
        axes = axes.reshape(-1, 1)

    fig.suptitle(ana_baslik, fontsize=13, fontweight='bold', y=1.01)

    for idx in range(satir_sayisi * sutun_sayisi):
        satir = idx // sutun_sayisi
        sutun = idx % sutun_sayisi
        ax = axes[satir, sutun]

        if idx < n:
            g = goruntular[idx]
            # Otomatik cmap seçimi
            kullan_cmap = cmap
            if kullan_cmap is None:
                kullan_cmap = 'gray' if g.ndim == 2 else None

            im = ax.imshow(g, cmap=kullan_cmap, vmin=0, vmax=255,
                           interpolation='bilinear')
            ax.set_title(basliklar[idx], fontsize=9, pad=4)
            ax.axis('off')

            if colorbar:
                plt.colorbar(im, ax=ax, fraction=0.046, pad=0.04)
        else:
            ax.axis('off')   # boş paneli gizle

    plt.tight_layout()
    if kaydet_yolu:
        plt.savefig(kaydet_yolu, dpi=130, bbox_inches='tight')
        print(f"Kaydedildi: {kaydet_yolu}")
    plt.show()

# ── Demo: Renk haritası galerisi ──────────────────────────────────────
rng = np.random.default_rng(99)
test_gri = np.zeros((150, 200), dtype=np.uint8)
for r in range(150):
    test_gri[r, :] = int(r / 150 * 200)
test_gri[50:100, 50:150] = 220
test_gri += rng.integers(0, 15, test_gri.shape, dtype=np.uint8)

cmaplar   = ['gray', 'viridis', 'plasma', 'inferno', 'hot', 'cool',
              'RdYlGn', 'coolwarm', 'jet', 'hsv', 'twilight', 'rainbow']
goruntular_cmap = [test_gri] * len(cmaplar)
basliklar_cmap  = cmaplar

# Her cmap farklı
fig, axes = plt.subplots(3, 4, figsize=(16, 11))
fig.suptitle("Renk Haritası Galerisi", fontsize=14, fontweight='bold')
for ax, cmap_adi in zip(axes.flat, cmaplar):
    im = ax.imshow(test_gri, cmap=cmap_adi, vmin=0, vmax=255)
    ax.set_title(cmap_adi, fontsize=10)
    ax.axis('off')
    plt.colorbar(im, ax=ax, fraction=0.046, pad=0.04)

plt.tight_layout()
plt.savefig("cmap_galerisi.png", dpi=130, bbox_inches='tight')
plt.show()

# ── Demo: Görüntü işleme galerisi (galeri_goster fonksiyonu ile) ───
renkli = np.zeros((150, 200, 3), dtype=np.uint8)
renkli[:, :, 0] = test_gri
renkli[:, :, 1] = 255 - test_gri
renkli[:, :, 2] = 100

islenmisler = [
    renkli,
    test_gri,
    np.clip(renkli.astype(int) + 60, 0, 255).astype(np.uint8),
    np.clip(renkli.astype(int) - 60, 0, 255).astype(np.uint8),
    np.clip(renkli.astype(np.float32) * 1.5, 0, 255).astype(np.uint8),
    np.flip(renkli, axis=1),
    np.rot90(renkli),
]
islem_isimleri = [
    "Orijinal (RGB)",
    "Gri Tonlama",
    "Parlak (+60)",
    "Karanlık (-60)",
    "Yüksek Kontrast (×1.5)",
    "Yatay Çevirme",
    "90° Döndürme",
]

galeri_goster(islenmisler, islem_isimleri,
               sutun_sayisi=4,
               ana_baslik="Görüntü İşleme Galerisi",
               kaydet_yolu="isleme_galerisi.png")
```

**Açıklama:**
`galeri_goster` fonksiyonu yeniden kullanılabilir bir galeri bileşenidir; görüntü sayısı değişse de yerleşim otomatik hesaplanır. Satır ve sütun sayısı tavan bölmesiyle belirlenir; `n`'den fazla panel olursa sonuncuları `axis('off')` ile gizlenir. `fig.suptitle`'a `y=1.01` vermek başlığın grafik alanının üstüne çıkmasını sağlar.

**Beklenen çıktı:**
İki ayrı pencere: 12 farklı renk haritasıyla aynı görüntü ve galeri fonksiyonu ile oluşturulan 7 işlem adımı galerisi.

**İyileştirme fikirleri:**
- `plt.savefig` ile birden fazla görüntüyü otomatik olarak tek PDF'e kaydetmek için `PdfPages` kullanın.
- `matplotlib.animation.FuncAnimation` ile görüntüler arası geçiş animasyonu oluşturun.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `matplotlib-teori.md` · `matplotlib-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
