# ✅ Matplotlib — Alıştırma Çözümleri

> **Modül:** Çözümler
> **İlgili dosya:** `matplotlib-alistirmalar.md`

> ⚠️ Kendi çözümünüzü tamamlamadan bu dosyayı açmayın.

---

## BLOK 1 — Kolay Çözümler

---

### Çözüm 1.1 — İlk Çizgi Grafiği

**Çözüm yaklaşımı:**
İki fonksiyonu ayrı `plot` çağrısıyla çizin; her biri için renk, stil ve etiket belirtin.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

x = np.linspace(-3, 3, 300)

fig, ax = plt.subplots(figsize=(9, 5))

ax.plot(x, x**2, color='royalblue', lw=2,   ls='-',  label='y = x²')
ax.plot(x, x**3, color='tomato',    lw=2,   ls='--', label='y = x³')

# Referans çizgileri
ax.axhline(0, color='black', lw=0.8, alpha=0.5)
ax.axvline(0, color='black', lw=0.8, alpha=0.5)

ax.set_title("İkinci ve Üçüncü Dereceden Fonksiyonlar", fontsize=14)
ax.set_xlabel("x", fontsize=12)
ax.set_ylabel("f(x)", fontsize=12)
ax.legend(fontsize=11)
ax.grid(True, linestyle='--', alpha=0.4)
ax.set_ylim(-30, 30)

plt.tight_layout()
plt.savefig("soru_1_1.png", dpi=120)
plt.show()
```

**Açıklama:**
`axhline(0)` ve `axvline(0)` tam koordinat eksenlerini çizer; bu, grafiğin orijin etrafındaki davranışını anlamayı kolaylaştırır. `ls='--'` ikinci fonksiyonu kesik çizgi yaparak iki eğriyi görsel olarak ayırır. `set_ylim` büyük x değerlerinde y³'ün çok hızlı büyümesini sınırlar.

---

### Çözüm 1.2 — Saçılım Diyagramı ve Trend

**Çözüm yaklaşımı:**
Rastgele veri oluştur, scatter çiz, `polyfit` ile trend hesapla, `corrcoef` ile r değeri bul.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(42)
x   = rng.uniform(0, 10, 50)
y   = 2 * x + rng.normal(0, 2, 50)

fig, ax = plt.subplots(figsize=(8, 5))

ax.scatter(x, y, color='steelblue', alpha=0.7, s=60,
           edgecolors='white', linewidths=0.5, label='Veri Noktaları')

# Trend çizgisi
katsayilar = np.polyfit(x, y, 1)
x_line = np.linspace(x.min(), x.max(), 200)
ax.plot(x_line, np.polyval(katsayilar, x_line), color='red', lw=2, ls='--', label='Trend')

# Korelasyon katsayısı
r = np.corrcoef(x, y)[0, 1]
ax.text(0.05, 0.92, f'r = {r:.3f}', transform=ax.transAxes,
        fontsize=12, bbox=dict(facecolor='lightyellow', alpha=0.8))

ax.set_title("Saçılım Diyagramı ve Doğrusal Trend")
ax.set_xlabel("x")
ax.set_ylabel("y")
ax.legend()
ax.grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("soru_1_2.png", dpi=120)
plt.show()
```

**Açıklama:**
`transform=ax.transAxes` metin konumunu eksen koordinatlarına (0-1 arası) göre belirler; eksen boyutu değişse de metin her zaman sol üst köşede kalır. `polyfit` katsayıları `[eğim, kesim_noktası]` sırasıyla döner; `polyval` bunları yeni x değerlerine uygular.

---

### Çözüm 1.3 — Basit Çubuk Grafik

**Çözüm yaklaşımı:**
`barh` ile yatay çubuk, `bar_label` ile değer etiketi, en büyük değer için farklı renk.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

sehirler  = ['İstanbul', 'Ankara', 'İzmir', 'Bursa', 'Antalya']
nufuslar  = [15.84, 5.78, 4.42, 3.21, 2.65]   # milyon

# En büyüğe farklı renk
max_idx = np.argmax(nufuslar)
renkler = ['#FF6B6B' if i == max_idx else '#4DABF7'
           for i in range(len(sehirler))]

fig, ax = plt.subplots(figsize=(9, 5))

cubuklar = ax.barh(sehirler, nufuslar, color=renkler,
                    edgecolor='white', height=0.6)
ax.bar_label(cubuklar, labels=[f'{n:.2f}M' for n in nufuslar],
             padding=5, fontsize=10)

ax.set_title("Türkiye'nin En Kalabalık 5 Şehri", fontsize=13)
ax.set_xlabel("Nüfus (Milyon)")
ax.set_xlim(0, max(nufuslar) * 1.2)
ax.grid(True, axis='x', alpha=0.3)
ax.invert_yaxis()   # en büyük üstte olsun

plt.tight_layout()
plt.savefig("soru_1_3.png", dpi=120)
plt.show()
```

**Açıklama:**
Koşullu renk listesi oluşturmak için liste anlayışı (list comprehension) kullanılır; `argmax` ile en büyük indeks bulunur. `invert_yaxis()` çubukları yukarıdan aşağıya sıralar — en büyük değer üstte görünür. `xlim` sağ tarafta etiketler için boşluk bırakır.

---

### Çözüm 1.4 — Histogram ve Dağılım

**Çözüm yaklaşımı:**
Histogram çiz, ortalama çizgisi ekle, ±1σ bandını `axvspan` ile gölgele.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng  = np.random.default_rng(7)
veri = rng.normal(100, 15, 1000)
ort  = veri.mean()
std  = veri.std()

fig, ax = plt.subplots(figsize=(9, 5))

ax.hist(veri, bins=25, color='steelblue', edgecolor='white', alpha=0.8)

# Ortalama çizgisi
ax.axvline(ort, color='red', lw=2, ls='--', label=f'Ort: {ort:.1f}')

# ±1σ bandı
ax.axvspan(ort - std, ort + std, alpha=0.12, color='orange',
           label=f'±1σ [{ort-std:.1f} – {ort+std:.1f}]')

ax.set_title(f"Normal Dağılım  (μ={ort:.1f}, σ={std:.1f})", fontsize=13)
ax.set_xlabel("Değer")
ax.set_ylabel("Frekans")
ax.legend(fontsize=10)
ax.grid(True, axis='y', alpha=0.3)

plt.tight_layout()
plt.savefig("soru_1_4.png", dpi=120)
plt.show()
```

**Açıklama:**
`axvspan` x aralığı boyunca tam yükseklikte dikey bant çizer; `alpha=0.12` çok saydam tutarak histogramın görünmesini sağlar. Başlıkta `ort` ve `std` değerlerini f-string ile göstermek grafiği kendi kendini açıklayan hale getirir.

---

### Çözüm 1.5 — Pasta Grafik

**Çözüm yaklaşımı:**
`explode` ile en büyük dilimi öne çıkar, `autopct` ile yüzdeleri yaz.

**Kod:**
```python
import matplotlib.pyplot as plt

kategoriler = ['Online Satış', 'Mağaza', 'Kurumsal', 'Diğer']
yuzde       = [40, 28, 20, 12]
renkler     = ['#4DABF7', '#74C0FC', '#A5D8FF', '#CDE9FF']
explode     = [0.07, 0, 0, 0]   # Online Satış'ı öne çıkar

fig, ax = plt.subplots(figsize=(7, 7))
wedges, texts, autotexts = ax.pie(
    yuzde,
    labels=kategoriler,
    explode=explode,
    autopct='%1.1f%%',
    colors=renkler,
    startangle=90,
    shadow=True,
    textprops={'fontsize': 11},
)

# Yüzde metinlerini kalın yap
for at in autotexts:
    at.set_fontweight('bold')

ax.set_title("Gelir Kaynaklarına Göre Dağılım", fontsize=14, pad=15)

plt.tight_layout()
plt.savefig("soru_1_5.png", dpi=120)
plt.show()
```

**Açıklama:**
`startangle=90` ilk dilimi 12 o'clock konumundan başlatır; görsel olarak daha dengeli görünür. `explode` listesinin uzunluğu dilim sayısıyla aynı olmalı. `shadow=True` hafif gölge ekleyerek dilimler arasındaki derinlik hissini artırır.

---

### Çözüm 1.6 — Zaman Serisi Grafiği

**Çözüm yaklaşımı:**
İşaretli çizgi grafik, ay isimleri, iki ortalama çizgisi.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng   = np.random.default_rng(33)
aylar = ['Oca','Şub','Mar','Nis','May','Haz','Tem','Ağu','Eyl','Eki','Kas','Ara']
x     = np.arange(12)

urun_a = rng.integers(80, 200, 12)
urun_b = rng.integers(60, 180, 12)

fig, ax = plt.subplots(figsize=(11, 5))

ax.plot(x, urun_a, 'o-', color='steelblue',  lw=2, ms=7, label='Ürün A')
ax.plot(x, urun_b, 's-', color='tomato',     lw=2, ms=7, label='Ürün B')

# Ortalama çizgileri
ax.axhline(urun_a.mean(), color='steelblue', lw=1.2, ls=':',
           alpha=0.7, label=f'A Ort: {urun_a.mean():.0f}')
ax.axhline(urun_b.mean(), color='tomato',    lw=1.2, ls=':',
           alpha=0.7, label=f'B Ort: {urun_b.mean():.0f}')

ax.set_xticks(x)
ax.set_xticklabels(aylar, rotation=30, ha='right')
ax.set_title("Aylık Ürün Satışları", fontsize=13)
ax.set_ylabel("Satış Adedi")
ax.legend(fontsize=10)
ax.grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("soru_1_6.png", dpi=120)
plt.show()
```

**Açıklama:**
`'o-'` kısaltması hem nokta işareti hem çizgi anlamına gelir. `rotation=30, ha='right'` ile etiketler sağa eğerek çakışması önlenir. Her ürünün ortalama çizgisi `:` (noktalı) stil ve düşük alpha ile arka planda kalır.

---

### Çözüm 1.7 — Çoklu Çizgi Grafik

**Çözüm yaklaşımı:**
Öğrenciler için döngü, her biri için renk ve marker listeden al.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng     = np.random.default_rng(12)
ogrenci = ['Ali', 'Ayşe', 'Can', 'Deniz']
renkler = ['royalblue', 'tomato', 'seagreen', 'darkorchid']
markerler = ['o', 's', '^', 'D']
notlar  = rng.integers(55, 100, size=(4, 5))

sinav_ortalamalari = notlar.mean(axis=0)

fig, ax = plt.subplots(figsize=(10, 5))

for i, (isim, renk, marker) in enumerate(zip(ogrenci, renkler, markerler)):
    ax.plot(range(1, 6), notlar[i], color=renk, marker=marker,
            lw=2, ms=8, label=isim)

# Sınav ortalaması arka planda
ax.plot(range(1, 6), sinav_ortalamalari, color='gray', lw=1.5, ls='--',
        alpha=0.6, label='Sınıf Ort.')

ax.set_title("Öğrenci Performans Trendi (5 Sınav)", fontsize=13)
ax.set_xlabel("Sınav Numarası")
ax.set_ylabel("Not")
ax.set_xticks(range(1, 6))
ax.set_ylim(40, 110)
ax.legend(fontsize=10)
ax.grid(True, alpha=0.3)

plt.tight_layout()
plt.savefig("soru_1_7.png", dpi=120)
plt.show()
```

**Açıklama:**
Renk ve marker listeleri `zip` ile birlikte döngüye sokulur; her öğrenci otomatik olarak benzersiz renk ve işaret alır. Sınıf ortalaması gri ve düşük alpha ile arka planda kalarak her öğrencinin bununla karşılaştırılmasını kolaylaştırır.

---

### Çözüm 1.8 — Grafik Kaydetme ve Boyut Ayarı

**Çözüm yaklaşımı:**
Farklı `figsize` ve `dpi` kombinasyonlarıyla kaydet, dosya boyutunu ölç.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np
import os

x = np.linspace(0, 2*np.pi, 200)

konfigurasyonlar = [
    ("web_kalitesi.png",   (800/72,  600/72),  72),
    ("iyi_kalite.png",     (1600/150, 900/150), 150),
    ("baski_kalitesi.png", (3200/300, 1800/300), 300),
]

for dosya, boyut, dpi in konfigurasyonlar:
    fig, ax = plt.subplots(figsize=boyut)
    ax.plot(x, np.sin(x), 'royalblue', lw=2)
    ax.set_title(f"{dosya} — DPI={dpi}")
    ax.grid(True, alpha=0.3)
    plt.savefig(dosya, dpi=dpi, bbox_inches='tight')
    plt.close(fig)

    boyut_kb = os.path.getsize(dosya) / 1024
    toplam_piksel = int(boyut[0] * dpi) * int(boyut[1] * dpi)
    print(f"{dosya:25s}  |  {boyut_kb:7.1f} KB  |  "
          f"~{int(boyut[0]*dpi)}×{int(boyut[1]*dpi)} px")

print("\nNot: Baskı kalitesinde dosya web kalitesinin ~10x büyüklüğünde olabilir.")
```

**Açıklama:**
`figsize` inç cinsinden, DPI piksel/inç'tir. Toplam piksel = `figsize[0]*dpi × figsize[1]*dpi`. PNG sıkıştırması kayıpsız olduğundan içerik karmaşıklığı dosya boyutunu etkiler. Her figure `plt.close(fig)` ile kapatılmazsa bellek birikir.

---

## BLOK 2 — Orta Çözümler

---

### Çözüm 2.1 — 2×2 Subplot Paneli

**Çözüm yaklaşımı:**
`plt.subplots(2,2)` ile ızgara oluştur, her panele farklı grafik türü uygula.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(42)
fig, axes = plt.subplots(2, 2, figsize=(12, 9))
fig.suptitle("Dört Grafik Türü", fontsize=14, fontweight='bold')

# Sol üst: Sinüs
x = np.linspace(0, 4*np.pi, 300)
axes[0,0].plot(x, np.sin(x), color='royalblue', lw=2)
axes[0,0].set_title("Sinüs Fonksiyonu (0–4π)")
axes[0,0].grid(True, alpha=0.3)

# Sağ üst: Scatter (renk üçüncü değişken)
sx = rng.standard_normal(200)
sy = rng.standard_normal(200)
sc_val = np.sqrt(sx**2 + sy**2)
sc = axes[0,1].scatter(sx, sy, c=sc_val, cmap='plasma', alpha=0.7, s=25)
plt.colorbar(sc, ax=axes[0,1], label='Orijinden Uzaklık')
axes[0,1].set_title("Scatter (3. değişken = renk)")

# Sol alt: Çubuk
kategoriler = ['A', 'B', 'C', 'D', 'E']
degerler    = rng.integers(20, 80, 5)
axes[1,0].bar(kategoriler, degerler, color='seagreen', edgecolor='white')
axes[1,0].set_title("Çubuk Grafik")
axes[1,0].grid(True, axis='y', alpha=0.3)

# Sağ alt: Histogram
veri = rng.normal(50, 12, 500)
axes[1,1].hist(veri, bins=25, color='tomato', edgecolor='white', alpha=0.8)
axes[1,1].axvline(veri.mean(), color='black', lw=2, ls='--',
                   label=f'Ort: {veri.mean():.1f}')
axes[1,1].legend()
axes[1,1].set_title("Normal Dağılım Histogramı")
axes[1,1].grid(True, axis='y', alpha=0.3)

plt.tight_layout()
plt.savefig("soru_2_1.png", dpi=120)
plt.show()
```

**Açıklama:**
`fig.suptitle` figure düzeyinde başlık ekler; her `ax.set_title` panel başlığıdır. `tight_layout()` panel aralıklarını otomatik düzenler. `colorbar(sc, ax=axes[0,1])` scatter'ın belirli bir paneline renk çubuğu ekler — `ax` belirtilmezse yanlış panele eklenebilir.

---

### Çözüm 2.2 — Isı Haritası (Korelasyon Matrisi)

**Çözüm yaklaşımı:**
`corrcoef` ile matris hesapla, `imshow` ile göster, döngüyle her hücreye değer yaz.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(5)
n   = 100
V1  = rng.standard_normal(n)
V2  = 0.8 * V1 + rng.normal(0, 0.5, n)
V3  = rng.standard_normal(n)
V4  = -0.6 * V1 + rng.normal(0, 0.7, n)
V5  = 0.4 * V2 + rng.normal(0, 0.8, n)

veri = np.column_stack([V1, V2, V3, V4, V5])
kor  = np.corrcoef(veri.T)   # (5,5) korelasyon matrisi
isimler = ['V1', 'V2', 'V3', 'V4', 'V5']

fig, ax = plt.subplots(figsize=(7, 6))
im = ax.imshow(kor, cmap='coolwarm', vmin=-1, vmax=1)
plt.colorbar(im, ax=ax, label='Korelasyon')

# Hücre değerleri
for i in range(5):
    for j in range(5):
        val = kor[i, j]
        renk = 'white' if abs(val) > 0.5 else 'black'
        ax.text(j, i, f'{val:.2f}', ha='center', va='center',
                fontsize=10, color=renk, fontweight='bold')

ax.set_xticks(range(5))
ax.set_yticks(range(5))
ax.set_xticklabels(isimler, fontsize=11)
ax.set_yticklabels(isimler, fontsize=11)
ax.set_title("Korelasyon Matrisi Isı Haritası", fontsize=13)

plt.tight_layout()
plt.savefig("soru_2_2.png", dpi=120)
plt.show()
```

**Açıklama:**
`vmin=-1, vmax=1` colormap'i ±1 aralığında sabitle; bu sayede korelasyon değerleri tutarlı renklere eşlenir. `abs(val) > 0.5` kontrolüyle koyu arka planda beyaz, açık arka planda siyah metin kullanılır — okunabilirlik için. `corrcoef(veri.T)` — dikkat: `veri` (n, 5) şeklindedir; `.T` ile değişkenler satıra alınır.

---

### Çözüm 2.3 — Çift Y Eksenli Grafik

**Çözüm yaklaşımı:**
`ax.twinx()` ile ikinci y ekseni oluştur; çubuk ve çizgi ayrı eksenlerde.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng   = np.random.default_rng(20)
aylar = ['Oca','Şub','Mar','Nis','May','Haz','Tem','Ağu','Eyl','Eki','Kas','Ara']
x     = np.arange(12)

yagis    = np.array([65, 55, 45, 30, 20, 10, 8, 12, 25, 55, 70, 72])
sicaklik = np.array([-2, 0, 5, 12, 18, 24, 27, 26, 20, 13, 6, 1])

fig, ax1 = plt.subplots(figsize=(11, 5))
ax2 = ax1.twinx()

# Yağış: çubuk (sol eksen)
bars = ax1.bar(x, yagis, color='steelblue', alpha=0.7, label='Yağış (mm)', width=0.6)
ax1.set_ylabel("Yağış (mm)", color='steelblue', fontsize=11)
ax1.tick_params(axis='y', labelcolor='steelblue')
ax1.set_ylim(0, max(yagis) * 1.4)

# Sıcaklık: çizgi (sağ eksen)
ax2.plot(x, sicaklik, 'o-', color='tomato', lw=2.5, ms=7, label='Sıcaklık (°C)')
ax2.set_ylabel("Sıcaklık (°C)", color='tomato', fontsize=11)
ax2.tick_params(axis='y', labelcolor='tomato')
ax2.axhline(0, color='gray', lw=0.8, ls=':')

ax1.set_xticks(x)
ax1.set_xticklabels(aylar)
ax1.set_title("Aylık Yağış ve Sıcaklık", fontsize=13)

# Birleşik legend
handles1, labels1 = ax1.get_legend_handles_labels()
handles2, labels2 = ax2.get_legend_handles_labels()
ax1.legend(handles1 + handles2, labels1 + labels2, loc='upper right', fontsize=10)

plt.tight_layout()
plt.savefig("soru_2_3.png", dpi=120)
plt.show()
```

**Açıklama:**
`twinx()` aynı x eksenini paylaşan ikinci y ekseni oluşturur. Her eksenin `tick_params(labelcolor=...)` ile rengi kendi grafiğiyle eşleştirilir — hangi eksenin ne ölçtüğü görsel olarak ayrışır. Legend iki eksenden ayrı alınıp birleştirilir.

---

### Çözüm 2.4 — Karşılaştırmalı Histogram

**Çözüm yaklaşımı:**
İki histogramı yarı saydam şekilde üst üste çiz; ikinci panelde `density=True` ile karşılaştır.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(88)
A   = rng.normal(60, 12, 500)
B   = rng.normal(70,  8, 500)

fig, axes = plt.subplots(1, 2, figsize=(13, 5))

# Sol: Standart histogram
axes[0].hist(A, bins=30, color='steelblue', alpha=0.6, edgecolor='white', label='Grup A')
axes[0].hist(B, bins=30, color='tomato',    alpha=0.6, edgecolor='white', label='Grup B')
axes[0].axvline(A.mean(), color='steelblue', lw=2, ls='--', label=f'A Ort: {A.mean():.1f}')
axes[0].axvline(B.mean(), color='tomato',    lw=2, ls='--', label=f'B Ort: {B.mean():.1f}')
axes[0].set_title("Grup Karşılaştırması (Frekans)")
axes[0].legend(fontsize=9)
axes[0].grid(True, alpha=0.3)

# Sağ: Yoğunluk histogramı
axes[1].hist(A, bins=30, density=True, color='steelblue', alpha=0.6,
             edgecolor='white', label='Grup A')
axes[1].hist(B, bins=30, density=True, color='tomato',    alpha=0.6,
             edgecolor='white', label='Grup B')
axes[1].set_title("Grup Karşılaştırması (Yoğunluk)")
axes[1].legend(fontsize=9)
axes[1].grid(True, alpha=0.3)
axes[1].set_xlabel("Değer")
axes[1].set_ylabel("Olasılık Yoğunluğu")

plt.suptitle("Normal Dağılım Karşılaştırması", fontsize=13)
plt.tight_layout()
plt.savefig("soru_2_4.png", dpi=120)
plt.show()
```

**Açıklama:**
`alpha=0.6` iki histogramın üst üste bindiği bölgelerin her ikisini de gösterir. `density=True` toplam alanı 1'e normalize eder; bu sayede farklı boyuttaki gruplar adil karşılaştırılabilir. Sağ panelde iki dağılımın örtüşme bölgesi (gürültü aralığı) net görünür.

---

### Çözüm 2.5 — Yığılmış Alan Grafiği

**Çözüm yaklaşımı:**
`stackplot` ile yığılmış alan, `plot` ile normal çizgi — `subplots(1,2)` ile yan yana.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng   = np.random.default_rng(42)
yillar = np.arange(2018, 2024)

kuzey  = np.array([120, 145, 160, 178, 195, 220])
guney  = np.array([ 90, 100, 115, 108, 130, 142])
dogu   = np.array([ 70,  88,  92, 105, 112, 128])
bati   = np.array([ 50,  62,  75,  80,  88,  99])

renkler = ['#4DABF7', '#74C0FC', '#A5D8FF', '#D0EBFF']
bolge_isimleri = ['Kuzey', 'Güney', 'Doğu', 'Batı']

fig, axes = plt.subplots(1, 2, figsize=(14, 5))
fig.suptitle("Bölgesel Satış Karşılaştırması", fontsize=13)

# Sol: Yığılmış alan
axes[0].stackplot(yillar, kuzey, guney, dogu, bati,
                   labels=bolge_isimleri, colors=renkler, alpha=0.9)
axes[0].set_title("Yığılmış Alan Grafiği")
axes[0].set_ylabel("Toplam Satış")
axes[0].legend(loc='upper left', fontsize=9)
axes[0].grid(True, axis='y', alpha=0.3)

# Sağ: Çizgi grafik
for veri, isim, renk in zip([kuzey, guney, dogu, bati], bolge_isimleri, renkler):
    axes[1].plot(yillar, veri, 'o-', color=renk, lw=2, ms=6,
                 label=isim, markeredgecolor='gray', markeredgewidth=0.5)
axes[1].set_title("Ayrık Çizgi Grafik")
axes[1].set_ylabel("Bölge Satışı")
axes[1].legend(fontsize=9)
axes[1].grid(True, alpha=0.3)

for ax in axes:
    ax.set_xticks(yillar)
    ax.set_xticklabels(yillar.astype(str))
    ax.set_xlabel("Yıl")

plt.tight_layout()
plt.savefig("soru_2_5.png", dpi=120)
plt.show()
```

**Açıklama:**
`stackplot` otomatik olarak katmanları üst üste yığar; her katmanın yüksekliği o grubun değerini, toplam yükseklik genel toplamı gösterir. Yan yana karşılaştırmada: yığılmış grafik oransal değişimi, çizgi grafik mutlak değişimi daha net gösterir.

---

### Çözüm 2.6 — Grafik Özelleştirme Ustası

**Çözüm yaklaşımı:**
`rcParams` veya manuel ayarlarla tüm biçimlendirme özelliklerini uygula.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

x   = np.linspace(0, 10, 100)
y1  = np.sin(x) * np.exp(-x/8)
y2  = np.cos(x) * np.exp(-x/8)

fig, ax = plt.subplots(figsize=(9, 5))

ax.plot(x, y1, lw=2.5, color='royalblue', label='sin(x)·e⁻ˣ/⁸')
ax.plot(x, y2, lw=2.5, color='tomato',    label='cos(x)·e⁻ˣ/⁸', ls='--')

# 1. Özel font boyutları
ax.set_title("Sönümlü Dalgalar", fontsize=14, fontweight='bold')
ax.set_xlabel("x",   fontsize=11)
ax.set_ylabel("f(x)", fontsize=11)
ax.tick_params(axis='both', labelsize=9)

# 2. Arka plan rengi
ax.set_facecolor('#F8F9FA')

# 3. Çerçeve kenarlıklarını gizle
ax.spines['top'].set_visible(False)
ax.spines['right'].set_visible(False)

# 4. Y ekseninde her 0.2 birimde tick
ax.set_yticks(np.arange(-1, 1.2, 0.2))

# 5. Legend dışarıda
ax.legend(loc='upper left', bbox_to_anchor=(1.01, 1.0), fontsize=10)

ax.grid(True, linestyle='--', alpha=0.4)

plt.tight_layout()
plt.savefig("soru_2_6.png", dpi=130, bbox_inches='tight')
plt.show()
```

**Açıklama:**
`bbox_to_anchor=(1.01, 1.0)` legend'ı axes'in sağ üst köşesinin dışına yerleştirir. `bbox_inches='tight'` bu dışa taşan legend'ın da kaydedilen alana dahil edilmesini sağlar — aksi takdirde kırpılır. `spines['top'].set_visible(False)` minimalist ve modern görünüm için yaygın kullanılır.

---

### Çözüm 2.7 — Dağılım ve Kutu Grafiği

**Çözüm yaklaşımı:**
`patch_artist=True` ile renkli kutu, `violinplot` ile dağılım şekli.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(30)

gruplar = {
    'A': rng.normal(70, 8, 100),
    'B': rng.normal(70, 20, 100),
    'C': np.concatenate([rng.normal(55, 5, 50), rng.normal(85, 5, 50)]),
    'D': rng.normal(80, 10, 100),
}
isimler = list(gruplar.keys())
veriler = list(gruplar.values())
renkler = ['#4DABF7', '#FF6B6B', '#69DB7C', '#FCC419']

fig, axes = plt.subplots(1, 2, figsize=(14, 6))

# Boxplot
bp = axes[0].boxplot(veriler, labels=isimler, patch_artist=True,
                      medianprops=dict(color='white', lw=2.5),
                      whiskerprops=dict(lw=1.5))
for patch, renk in zip(bp['boxes'], renkler):
    patch.set_facecolor(renk)
    patch.set_alpha(0.8)
axes[0].set_title("Boxplot")
axes[0].set_ylabel("Puan")
axes[0].grid(True, axis='y', alpha=0.3)

# Violin plot
vp = axes[1].violinplot(veriler, positions=range(1, 5),
                         showmeans=True, showmedians=True)
for body, renk in zip(vp['bodies'], renkler):
    body.set_facecolor(renk)
    body.set_alpha(0.75)
axes[1].set_xticks(range(1, 5))
axes[1].set_xticklabels(isimler)
axes[1].set_title("Violin Plot")
axes[1].grid(True, axis='y', alpha=0.3)

# Yorum notu
fig.text(0.5, 0.01, "Not: C grubunun bimodal dağılımı violin'de iki tepe olarak görünür, "
                     "boxplot'ta gizlenir.",
         ha='center', fontsize=10, color='gray', style='italic')

plt.suptitle("Boxplot vs Violin Plot", fontsize=13, fontweight='bold')
plt.tight_layout(rect=[0, 0.05, 1, 1])
plt.savefig("soru_2_7.png", dpi=120)
plt.show()
```

**Açıklama:**
`patch_artist=True` kutuların dolu renk almasını sağlar; `bp['boxes']` listesi üzerinden her kutunun rengi değiştirilebilir. Violin plot'ta bimodal dağılım (C grubu) iki şişkinlik olarak görünür — boxplot bunu gizler. `fig.text(..., rect=[0,0.05,1,1])` alt notu için alan bırakır.

---

### Çözüm 2.8 — Animasyonlu Veri Keşfi

**Çözüm yaklaşımı:**
Beş faz için döngüde `plot`, renk haritasından otomatik renk.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

x      = np.linspace(0, 2*np.pi, 300)
fazlar = [0, np.pi/4, np.pi/2, 3*np.pi/4, np.pi]
cmap   = plt.get_cmap('plasma')

fig, ax = plt.subplots(figsize=(10, 5))

for i, faz in enumerate(fazlar):
    renk = cmap(i / (len(fazlar) - 1))
    ax.plot(x, np.sin(x + faz), color=renk, lw=2.5,
            label=f'φ = {faz/np.pi:.2f}π', alpha=0.85)

ax.axhline(0, color='black', lw=0.7, alpha=0.4)
ax.set_title("Sinüs Fonksiyonunun Faz Kayması", fontsize=13)
ax.set_xlabel("x")
ax.set_ylabel("sin(x + φ)")
ax.legend(fontsize=9, loc='upper right')
ax.grid(True, alpha=0.3)

# Animasyon için gereken modül
# ax.text(0.02, 0.06, "Animasyon için: matplotlib.animation.FuncAnimation",
#         transform=ax.transAxes, fontsize=9, color='gray')

plt.tight_layout()
plt.savefig("soru_2_8.png", dpi=120)
plt.show()

# Cevap: Gerçek animasyon için 'matplotlib.animation' modülü,
# özellikle 'FuncAnimation' veya 'ArtistAnimation' sınıfları kullanılır.
print("Animasyon modülü: matplotlib.animation.FuncAnimation")
```

**Açıklama:**
`cmap(i / (len(fazlar) - 1))` renk haritasını 0-1 arasında eşit aralıklarla örnekler; her faz için benzersiz renk üretir. Gerçek animasyon için `FuncAnimation(fig, update_func, frames=N)` her karede `update_func` çağırır — statik grafik gibi çizer ama her karede veri güncellenir.

---

### Çözüm 2.9 — Görüntü Kanalı Görselleştirme

**Çözüm yaklaşımı:**
Görüntüyü kanallarına ayır, her panel için uygun cmap seç.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

# Yapay görüntü
rng = np.random.default_rng(5)
img = np.zeros((200, 300, 3), dtype=np.uint8)
for r in range(200):
    img[r, :, 0] = int(r / 200 * 220)
for c in range(300):
    img[:, c, 1] = int(c / 300 * 200)
img[:, :, 2] = 90
img = np.clip(img + rng.integers(0, 20, img.shape), 0, 255).astype(np.uint8)

R, G, B = img[:,:,0], img[:,:,1], img[:,:,2]
gri     = img.mean(axis=2).astype(np.uint8)

yalniz_r = np.zeros_like(img)
yalniz_r[:, :, 0] = R

basliklar = ['Orijinal RGB', 'Gri Tonlama', 'R Kanalı', 'G Kanalı', 'B Kanalı', 'Yalnız Kırmızı']
gorseller = [img, gri, R, G, B, yalniz_r]
cmaplar   = [None, 'gray', 'Reds', 'Greens', 'Blues', None]

fig, axes = plt.subplots(2, 3, figsize=(13, 8))
fig.suptitle("Görüntü Kanalları", fontsize=13, fontweight='bold')

for ax, g, baslik, cmap in zip(axes.flat, gorseller, basliklar, cmaplar):
    ax.imshow(g, cmap=cmap, vmin=0, vmax=255)
    ax.set_title(baslik, fontsize=10)
    ax.axis('off')

plt.tight_layout()
plt.savefig("soru_2_9.png", dpi=120)
plt.show()
```

**Açıklama:**
`cmap=None` kullanıldığında Matplotlib görüntüyü RGB olarak yorumlar; 2D dizi için `cmap='gray'` gerekir. `np.zeros_like(img)` orijinalle aynı şekil ve tipte sıfır dizi oluşturur; R kanalı kopyalanır, diğerleri sıfır kalır.

---

### Çözüm 2.10 — Mini Veri Analizi Paneli

**Çözüm yaklaşımı:**
`subplots(1,3)` ile üç panel, `suptitle` ve `tight_layout(rect=...)`.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng      = np.random.default_rng(77)
n        = 100
gelir    = rng.integers(4000, 15001, n)
harcama  = gelir * rng.uniform(0.5, 0.8, n) + rng.normal(0, 500, n)
harcama  = np.clip(harcama, 500, None).astype(int)

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
fig.suptitle("Gelir ve Harcama Analizi", fontsize=14, fontweight='bold')

# Sol: Gelir histogramı
axes[0].hist(gelir, bins=20, color='steelblue', edgecolor='white', alpha=0.8)
axes[0].axvline(gelir.mean(), color='red', lw=2, ls='--',
                label=f'Ort: {gelir.mean():.0f}')
axes[0].set_title("Gelir Dağılımı")
axes[0].set_xlabel("Gelir (TL)")
axes[0].set_ylabel("Frekans")
axes[0].legend(fontsize=9)
axes[0].grid(True, axis='y', alpha=0.3)

# Orta: Harcama histogramı
axes[1].hist(harcama, bins=20, color='seagreen', edgecolor='white', alpha=0.8)
axes[1].axvline(harcama.mean(), color='red', lw=2, ls='--',
                label=f'Ort: {harcama.mean():.0f}')
axes[1].set_title("Harcama Dağılımı")
axes[1].set_xlabel("Harcama (TL)")
axes[1].legend(fontsize=9)
axes[1].grid(True, axis='y', alpha=0.3)

# Sağ: Scatter + trend
katsayilar = np.polyfit(gelir, harcama, 1)
x_line = np.linspace(gelir.min(), gelir.max(), 200)
axes[2].scatter(gelir, harcama, alpha=0.5, s=30, color='darkorchid',
                edgecolors='white', linewidths=0.3)
axes[2].plot(x_line, np.polyval(katsayilar, x_line), 'r-', lw=2, label='Trend')
r = np.corrcoef(gelir, harcama)[0,1]
axes[2].text(0.05, 0.9, f'r = {r:.2f}', transform=axes[2].transAxes,
             fontsize=11, bbox=dict(facecolor='lightyellow', alpha=0.8))
axes[2].set_title("Gelir — Harcama İlişkisi")
axes[2].set_xlabel("Gelir (TL)")
axes[2].set_ylabel("Harcama (TL)")
axes[2].legend(fontsize=9)
axes[2].grid(True, alpha=0.3)

plt.tight_layout(rect=[0, 0, 1, 0.94])
plt.savefig("soru_2_10.png", dpi=120)
plt.show()
```

**Açıklama:**
`tight_layout(rect=[0, 0, 1, 0.94])` üstten %6 boşluk bırakarak `suptitle` için yer açar. Her panel bağımsız olarak ayarlanır; `axes[i]` indeksiyle erişilir.

---

## BLOK 3 — Zor Çözümler

---

### Çözüm 3.1 — Tam Veri Analizi Panosu

**Çözüm yaklaşımı:**
`GridSpec` ile özel yerleşim; geniş üst panel + üç alt panel.

**Kod:**
```python
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np

rng   = np.random.default_rng(42)
n     = 200
gunler = np.arange(n)
veri  = 50 + 15 * np.sin(2*np.pi*gunler/50) + rng.normal(0, 8, n)
kategoriler = rng.integers(0, 4, n)
cat_isim    = ['A', 'B', 'C', 'D']
CAT_RENK    = ['#4DABF7', '#FF6B6B', '#69DB7C', '#FCC419']

pencere = np.ones(15) / 15
ort_15  = np.convolve(veri, pencere, mode='same')

fig = plt.figure(figsize=(15, 10))
gs  = gridspec.GridSpec(2, 3, figure=fig, hspace=0.45, wspace=0.35)

# Üst: tam genişlik zaman serisi
ax_z = fig.add_subplot(gs[0, :])
ax_z.plot(gunler, veri, alpha=0.35, color='steelblue', lw=0.9, label='Günlük')
ax_z.plot(gunler, ort_15, color='steelblue', lw=2.5, label='15 Gün Ort.')
ax_z.fill_between(gunler, veri, ort_15, alpha=0.1, color='steelblue')
ax_z.set_title("Zaman Serisi", fontsize=12)
ax_z.legend(); ax_z.grid(True, alpha=0.3)

# Alt sol: histogram
ax_h = fig.add_subplot(gs[1, 0])
ax_h.hist(veri, bins=25, color='steelblue', edgecolor='white', alpha=0.8)
ax_h.axvline(veri.mean(), color='red', lw=2, ls='--',
             label=f'Ort: {veri.mean():.1f}')
ax_h.set_title("Dağılım"); ax_h.legend(fontsize=9); ax_h.grid(True, axis='y', alpha=0.3)

# Alt orta: kutu grafik
ax_k = fig.add_subplot(gs[1, 1])
cat_veri = [veri[kategoriler == c] for c in range(4)]
bp = ax_k.boxplot(cat_veri, labels=cat_isim, patch_artist=True,
                   medianprops=dict(color='white', lw=2))
for patch, renk in zip(bp['boxes'], CAT_RENK):
    patch.set_facecolor(renk); patch.set_alpha(0.8)
ax_k.set_title("Kategori Kutu Grafik"); ax_k.grid(True, axis='y', alpha=0.3)

# Alt sağ: metin özet
ax_m = fig.add_subplot(gs[1, 2])
ax_m.axis('off')
ax_m.set_facecolor('#FFFBE6')
metrikler_str = (
    "  ÖZET METRİKLER\n"
    "  " + "─"*22 + "\n"
    f"  N         : {n}\n"
    f"  Ort ± Std : {veri.mean():.1f} ± {veri.std():.1f}\n"
    f"  Min / Max : {veri.min():.1f} / {veri.max():.1f}\n"
    f"  Medyan    : {np.median(veri):.1f}\n"
    "  " + "─"*22 + "\n"
    f"  50+ oran  : %{(veri>50).mean()*100:.0f}\n"
)
ax_m.text(0.05, 0.5, metrikler_str, transform=ax_m.transAxes,
          fontsize=11, va='center', fontfamily='monospace',
          bbox=dict(facecolor='white', alpha=0.7))

fig.suptitle("Veri Analizi Panosu", fontsize=15, fontweight='bold')
plt.savefig("soru_3_1.png", dpi=130, bbox_inches='tight')
plt.show()
```

**Açıklama:**
`gs[0, :]` birinci satırın tüm 3 sütununu kapsayan tek panel verir. `gs[1, 0]`, `gs[1, 1]`, `gs[1, 2]` alt üç ayrı paneldir. Metin paneli `axis('off')` ile eksenleri gizler; `fontfamily='monospace'` metni hizalı gösterir.

---

### Çözüm 3.2 — Görüntü Karşılaştırma Aracı

**Çözüm yaklaşımı:**
Orijinal + bulanık görüntü, fark haritası, histogramlar ve metrik tablosu.

**Kod:**
```python
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np
from scipy import ndimage

rng = np.random.default_rng(3)
img = np.zeros((200, 300), dtype=np.uint8)
for r in range(200):
    img[r, :] = int(r / 200 * 180)
img = np.clip(img + rng.integers(0, 30, img.shape), 0, 255).astype(np.uint8)
blur = ndimage.gaussian_filter(img.astype(float), sigma=5).astype(np.uint8)

fark = np.abs(img.astype(int) - blur.astype(int)).astype(np.uint8)
mse  = ((img.astype(float) - blur.astype(float))**2).mean()
psnr = 10 * np.log10(255**2 / mse) if mse > 0 else float('inf')

fig = plt.figure(figsize=(15, 10))
gs  = gridspec.GridSpec(2, 3, figure=fig, hspace=0.45, wspace=0.35)

ax_o  = fig.add_subplot(gs[0, 0])
ax_b  = fig.add_subplot(gs[0, 1])
ax_f  = fig.add_subplot(gs[0, 2])
ax_h  = fig.add_subplot(gs[1, :2])
ax_mt = fig.add_subplot(gs[1, 2])

for ax, g, baslik in [(ax_o, img, f"Orijinal\nort={img.mean():.0f} std={img.std():.1f}"),
                       (ax_b, blur, f"Bulanık (σ=5)\nort={blur.mean():.0f} std={blur.std():.1f}")]:
    ax.imshow(g, cmap='gray', vmin=0, vmax=255)
    ax.set_title(baslik, fontsize=10); ax.axis('off')

im = ax_f.imshow(fark, cmap='hot', vmin=0, vmax=fark.max())
ax_f.set_title("Mutlak Fark Haritası"); ax_f.axis('off')
plt.colorbar(im, ax=ax_f, fraction=0.046)

ax_h.hist(img.ravel(),  bins=64, alpha=0.6, color='steelblue', label='Orijinal')
ax_h.hist(blur.ravel(), bins=64, alpha=0.6, color='tomato',    label='Bulanık')
ax_h.set_title("Histogram Karşılaştırması")
ax_h.set_xlabel("Piksel Değeri"); ax_h.legend(); ax_h.grid(True, alpha=0.3)

ax_mt.axis('off')
ax_mt.text(0.1, 0.5,
    f"  KALİTE METRİKLERİ\n"
    f"  {'─'*20}\n"
    f"  MSE  : {mse:.2f}\n"
    f"  PSNR : {psnr:.1f} dB\n"
    f"  {'─'*20}\n"
    f"  PSNR > 40 → çok yakın\n"
    f"  PSNR < 20 → belirgin fark",
    transform=ax_mt.transAxes, fontsize=11, va='center',
    fontfamily='monospace',
    bbox=dict(facecolor='lightyellow', alpha=0.85))

fig.suptitle("Görüntü Karşılaştırma Raporu", fontsize=14, fontweight='bold')
plt.savefig("soru_3_2.png", dpi=130, bbox_inches='tight')
plt.show()
```

**Açıklama:**
`np.abs(img.astype(int) - blur.astype(int))` tam sayıya çevirerek uint8 taşması olmadan fark hesaplar. PSNR 40 dB üzeri görüntüler gözle neredeyse ayırt edilemez. Fark haritasında `hot` cmap sıfır farkı siyah, büyük farkı parlak sarı-beyaz gösterir.

---

### Çözüm 3.3 — Çok Boyutlu Veri Görselleştirmesi

**Çözüm yaklaşımı:**
Scatter matrix için iç içe döngü; köşegen histogram, diğerleri scatter. Korelasyon paneli sağda.

**Kod:**
```python
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np

rng = np.random.default_rng(42)
n = 100
V1 = rng.standard_normal(n)
V2 = 0.8 * V1 + rng.normal(0, 0.5, n)
V3 = rng.standard_normal(n)
V4 = -0.6 * V1 + rng.normal(0, 0.7, n)
veri   = np.column_stack([V1, V2, V3, V4])
isimler = ['V1', 'V2', 'V3', 'V4']
kor    = np.corrcoef(veri.T)

fig = plt.figure(figsize=(16, 12))
gs  = gridspec.GridSpec(4, 5, figure=fig, hspace=0.4, wspace=0.4)

renkler_scatter = 'steelblue'

for i in range(4):
    for j in range(4):
        ax = fig.add_subplot(gs[i, j])
        if i == j:
            ax.hist(veri[:, i], bins=20, color='steelblue', alpha=0.7, edgecolor='white')
            ax.set_ylabel(isimler[i], fontsize=8)
        else:
            ax.scatter(veri[:, j], veri[:, i], s=12, alpha=0.5, color='steelblue')
        ax.tick_params(labelsize=7)
        if i == 3: ax.set_xlabel(isimler[j], fontsize=8)

# Sağ panel: korelasyon ısı haritası
ax_kor = fig.add_subplot(gs[:, 4])
im = ax_kor.imshow(kor, cmap='coolwarm', vmin=-1, vmax=1)
plt.colorbar(im, ax=ax_kor, fraction=0.046)
ax_kor.set_xticks(range(4)); ax_kor.set_yticks(range(4))
ax_kor.set_xticklabels(isimler, fontsize=9)
ax_kor.set_yticklabels(isimler, fontsize=9)
for i in range(4):
    for j in range(4):
        ax_kor.text(j, i, f'{kor[i,j]:.2f}', ha='center', va='center',
                    fontsize=9, color='white' if abs(kor[i,j]) > 0.5 else 'black')
ax_kor.set_title("Korelasyon\nMatrisi", fontsize=10)

fig.suptitle("Çok Boyutlu Veri Keşfi", fontsize=14, fontweight='bold')
plt.savefig("soru_3_3.png", dpi=120, bbox_inches='tight')
plt.show()
```

**Açıklama:**
Scatter matrix için `4×4` döngü her `(i,j)` kombinasyonu için uygun grafiği çizer. `GridSpec(4,5)` ile 4 sütun scatter + 1 sütun korelasyon yan yana yerleştirilir. Köşegen paneller dağılımı, diğerleri iki değişken ilişkisini gösterir.

---

### Çözüm 3.4 — Etkileşimli Grafik Temel Taşı

**Çözüm yaklaşımı:**
10 önceden tanımlanmış noktayı büyüklük ve renk değişimiyle göster, sıra numarasını yaz.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng     = np.random.default_rng(42)
noktalar = rng.uniform(0.1, 0.9, (10, 2))
cmap    = plt.get_cmap('tab10')

fig, ax = plt.subplots(figsize=(8, 7))
ax.set_xlim(0, 1); ax.set_ylim(0, 1)
ax.set_facecolor('#F0F4F8')
ax.grid(True, alpha=0.4)
ax.set_title("Tıklama Simülasyonu (10 Nokta)", fontsize=13)
ax.set_xlabel("x"); ax.set_ylabel("y")

for i, (x, y) in enumerate(noktalar):
    renk  = cmap(i / 9)
    boyut = 100 + i * 30   # sonraki noktalar daha büyük
    ax.scatter(x, y, s=boyut, color=renk, zorder=5,
               edgecolors='white', linewidths=1.5, alpha=0.85)
    ax.annotate(f'#{i+1}', (x, y), textcoords='offset points',
                xytext=(8, 8), fontsize=9, fontweight='bold', color=renk)

ax.text(0.02, 0.97, "Gerçek etkileşim için: plt.ginput veya\nmpl_connect('button_press_event')",
        transform=ax.transAxes, fontsize=9, va='top',
        bbox=dict(facecolor='lightyellow', alpha=0.8))

plt.tight_layout()
plt.savefig("soru_3_4.png", dpi=120)
plt.show()

# Gerçek ginput kullanımı (terminal/script ortamında):
# noktalar = plt.ginput(n=-1, timeout=30)  # -1: tüm tıklamalar
# print(f"Tıklanan noktalar: {noktalar}")
```

**Açıklama:**
`annotate` ile ok veya offset kullanarak noktaların üzerine numara yazılır. `zorder=5` scatter noktalarının ızgara çizgilerinin üzerinde kalmasını sağlar. Gerçek `ginput` terminal ortamında çalışır; Jupyter'da `ipympl` gerektirebilir.

---

### Çözüm 3.5 — Veri Hikâyesi Görseli

**Çözüm yaklaşımı:**
Lansman noktası, büyüme vurgulama, hedef çizgisi, annotation ile anlatım.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

aylar = np.arange(1, 19)

# Veri: lansman 7. ayda
oncesi = np.array([42, 45, 40, 48, 43, 47])
sonrasi = 47 * (1 + np.arange(12) * 0.12) + np.random.default_rng(5).normal(0, 5, 12)
satis = np.concatenate([oncesi, sonrasi])
lansman_ay = 7
hedef = 180

fig, ax = plt.subplots(figsize=(13, 6))
ax.set_facecolor('#FAFAFA')

# Ana veri çizgisi
ax.plot(aylar, satis, 'o-', color='steelblue', lw=2.5, ms=6,
        markeredgecolor='white', markeredgewidth=1.5, zorder=4)

# Büyüme dönemini vurgula
ax.fill_between(aylar[lansman_ay-1:], satis[lansman_ay-1:],
                color='steelblue', alpha=0.1, label='Büyüme dönemi')

# Lansman çizgisi
ax.axvline(lansman_ay, color='red', lw=2, ls='--', alpha=0.8, zorder=3)

# Lansman açıklaması
ax.annotate("Ürün Lansmanı",
            xy=(lansman_ay, satis[lansman_ay-1]),
            xytext=(lansman_ay + 1.2, satis[lansman_ay-1] + 30),
            fontsize=11, color='red', fontweight='bold',
            arrowprops=dict(arrowstyle='->', color='red', lw=1.8))

# Hedef çizgisi
ax.axhline(hedef, color='seagreen', lw=2, ls=':', alpha=0.8,
           label=f'Hedef: {hedef}')
ax.text(17.5, hedef + 3, f'Hedef\n({hedef})', color='seagreen',
        fontsize=9, ha='right', va='bottom')

# Şirket bilgi kutusu
ax.text(1.5, 155,
        "  TechCo A.Ş.\n  Ürün X Lansmanı", fontsize=11,
        bbox=dict(facecolor='steelblue', alpha=0.15, boxstyle='round,pad=0.5'),
        color='steelblue', fontweight='bold')

ax.set_title("Ürün Lansmanı Satış Performansı (18 Ay)", fontsize=14, fontweight='bold')
ax.set_xlabel("Ay")
ax.set_ylabel("Aylık Satış Adedi")
ax.legend(fontsize=10)
ax.grid(True, alpha=0.25)
ax.set_xlim(0.5, 18.5)

fig.text(0.99, 0.01, "Kaynak: İç Satış Verisi | Mart 2026",
         ha='right', fontsize=8, color='gray')

plt.tight_layout()
plt.savefig("soru_3_5.png", dpi=130, bbox_inches='tight')
plt.show()
```

**Açıklama:**
`annotate` ile ok ve metin birlikte eklenir; `xy` ok ucunun, `xytext` metnin konumunu belirtir. `fill_between` büyüme dönemini görsel olarak vurgular. `fig.text` figure koordinatında dipnot ekler — axes boyutundan bağımsız sabit konumda kalır.

---

### Çözüm 3.6 — Monte Carlo Görselleştirmesi

**Çözüm yaklaşımı:**
Üç panel: nokta simülasyonu, yakınsama çizgisi, log-log hata eğrisi.

**Kod:**
```python
import matplotlib.pyplot as plt
import numpy as np

rng = np.random.default_rng(99)
fig, axes = plt.subplots(1, 3, figsize=(16, 5))

# Panel 1: 1000 nokta simülasyonu
N_goster = 1000
x = rng.uniform(-1, 1, N_goster)
y = rng.uniform(-1, 1, N_goster)
icerde = (x**2 + y**2) <= 1
pi_tahmin = 4 * icerde.mean()

axes[0].scatter(x[icerde],  y[icerde],  s=4, color='steelblue', alpha=0.5)
axes[0].scatter(x[~icerde], y[~icerde], s=4, color='tomato',    alpha=0.5)
cember = plt.Circle((0,0), 1, fill=False, color='black', lw=2)
axes[0].add_patch(cember)
axes[0].set_aspect('equal')
axes[0].set_title(f"Monte Carlo Simülasyonu\nπ ≈ {pi_tahmin:.4f}")
axes[0].set_xlim(-1.05, 1.05); axes[0].set_ylim(-1.05, 1.05)

# Panel 2: Yakınsama grafiği
N_list  = np.logspace(1, 4, 50).astype(int)
pi_list = [4 * (rng.uniform(-1,1,n)**2 + rng.uniform(-1,1,n)**2 <= 1).mean() for n in N_list]
axes[1].semilogx(N_list, pi_list, 'o-', color='steelblue', ms=3, lw=1.5, label='Tahmin')
axes[1].axhline(np.pi, color='red', lw=2, ls='--', label=f'Gerçek π={np.pi:.4f}')
axes[1].set_title("Yakınsama (N → ∞)")
axes[1].set_xlabel("N (log ölçek)")
axes[1].set_ylabel("Pi Tahmini")
axes[1].legend(); axes[1].grid(True, alpha=0.3)

# Panel 3: Log-log hata
N_hata  = [10, 50, 100, 500, 1000, 5000, 10000]
hatalar = [abs(4*(rng.uniform(-1,1,n)**2+rng.uniform(-1,1,n)**2<=1).mean()-np.pi) for n in N_hata]

teorik = 1 / np.sqrt(N_hata)
teorik *= hatalar[0] / teorik[0]   # ölçekle

axes[2].loglog(N_hata, hatalar, 'o-', color='steelblue', ms=7, lw=2, label='Ölçülen hata')
axes[2].loglog(N_hata, teorik, 'r--', lw=2, label='O(1/√N) teorik')
axes[2].set_title("Hata — N İlişkisi (Log-Log)")
axes[2].set_xlabel("N")
axes[2].set_ylabel("|Tahmin - π|")
axes[2].legend(); axes[2].grid(True, alpha=0.3)

plt.suptitle("Monte Carlo: Pi Tahmini", fontsize=14, fontweight='bold')
plt.tight_layout()
plt.savefig("soru_3_6.png", dpi=130)
plt.show()
```

**Açıklama:**
`plt.Circle((0,0), 1, fill=False)` + `add_patch` daire ekler. `semilogx` yalnızca x ekseni logaritmik; `loglog` her ikisi de logaritmik. Log-log grafikte `O(1/√N)` doğrusal bir çizgi olarak görünür — bu, algoritmanın istatistiksel hata davranışını analiz etmenin standart yoludur.

---

### Çözüm 3.7 — Gelişmiş Isı Haritası Analizi

**Çözüm yaklaşımı:**
365 günü 52×7'ye reshape et, `imshow` ile haftalık ısı haritası, `Rectangle` ile işaret.

**Kod:**
```python
import matplotlib.pyplot as plt
import matplotlib.patches as patches
import numpy as np

rng    = np.random.default_rng(77)
gunler = np.arange(365)
sicaklik = 15 + 13 * np.sin(2*np.pi*gunler/365 - np.pi/2) + rng.normal(0, 3, 365)

# 52×7 matris
sicaklik_364 = sicaklik[:364].reshape(52, 7)

fig, ax = plt.subplots(figsize=(16, 5))

im = ax.imshow(sicaklik_364.T, aspect='auto', cmap='RdYlBu_r',
               interpolation='nearest')
plt.colorbar(im, ax=ax, label='Sıcaklık (°C)', fraction=0.03)

# Y ekseni: gün isimleri
gun_isimleri = ['Pzt', 'Sal', 'Çar', 'Per', 'Cum', 'Cmt', 'Paz']
ax.set_yticks(range(7))
ax.set_yticklabels(gun_isimleri, fontsize=9)

# X ekseni: ay isimleri (yaklaşık hafta başları)
ay_haftalar = [0, 4, 8, 13, 17, 21, 26, 30, 35, 39, 43, 48]
ay_isimleri = ['Oca','Şub','Mar','Nis','May','Haz','Tem','Ağu','Eyl','Eki','Kas','Ara']
ax.set_xticks(ay_haftalar)
ax.set_xticklabels(ay_isimleri, fontsize=9)

# En sıcak ve en soğuk hafta
hafta_ort = sicaklik_364.mean(axis=1)
en_sicak  = hafta_ort.argmax()
en_soguk  = hafta_ort.argmin()

for idx, renk, etiket in [(en_sicak, 'red', 'En Sıcak'), (en_soguk, 'blue', 'En Soğuk')]:
    rect = patches.Rectangle((idx - 0.5, -0.5), 1, 7,
                               fill=False, edgecolor=renk, lw=3)
    ax.add_patch(rect)
    ax.text(idx, -1.2, etiket, ha='center', fontsize=9,
            color=renk, fontweight='bold')

ax.set_title("Yıllık Sıcaklık Takvimi (Haftalık Isı Haritası)", fontsize=13)
ax.set_xlabel("Hafta")

plt.tight_layout()
plt.savefig("soru_3_7.png", dpi=130)
plt.show()

print(f"En sıcak hafta: {en_sicak+1}. hafta ({hafta_ort[en_sicak]:.1f}°C)")
print(f"En soğuk hafta: {en_soguk+1}. hafta ({hafta_ort[en_soguk]:.1f}°C)")
```

**Açıklama:**
`reshape(52, 7)` her satırı bir hafta, her sütunu bir gün olarak düzenler; `.T` ile hafta sütun, gün satır olur — bu `imshow`'da haftaları yatayda, günleri dikeyde gösterir. `patches.Rectangle((x-0.5, y-0.5), w, h)` koordinat sistemi hücre merkezine hizalanır. `aspect='auto'` hücreleri kare yerine dikdörtgen yaparak grafik alanını verimli kullanır.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `matplotlib-teori.md` · `matplotlib-uygulamalar.md` · `matplotlib-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
