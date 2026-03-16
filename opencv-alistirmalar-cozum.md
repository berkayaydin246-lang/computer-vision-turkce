# ✅ OpenCV — Alıştırma Çözümleri

> **Modül:** Çözümler | **Seviye:** Kolay → Orta → Zor
> **İlgili dosya:** `opencv-alistirmalar.md`

---

> ⚠️ Bu dosyayı kendi çözümünüzü tamamlamadan açmayın. Çözümler yalnızca kendi denemenizden sonra karşılaştırma ve öğrenme amacıyla kullanılmalıdır.

---

## BLOK 1 — Kolay Çözümler

---

### Çözüm 1.1 — Görüntü Kimlik Kartı

**Çözüm yaklaşımı:**
Görüntü nitelikleri NumPy'ın dizi özellikleriyle doğrudan erişilebilir. Parlaklık sınıflandırması için gri ortalamasına eşik uygulanır. Yan yana gösterim için gri görüntünün 3 kanala çevrilmesi gerekir.

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    # Yoksa yapay görüntü oluştur
    img = np.random.randint(50, 200, (480, 640, 3), dtype=np.uint8)

h, w, c   = img.shape
gray      = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
ortalama  = gray.mean()
boyut_kb  = img.nbytes / 1024

if ortalama < 85:
    parlaklik = "Karanlik"
elif ortalama < 170:
    parlaklik = "Normal"
else:
    parlaklik = "Parlak"

print("=" * 40)
print(f"  Genislik      : {w} piksel")
print(f"  Yukseklik     : {h} piksel")
print(f"  Kanal sayisi  : {c}")
print(f"  Veri tipi     : {img.dtype}")
print(f"  Toplam piksel : {img.size}")
print(f"  Bellek        : {boyut_kb:.1f} KB")
print(f"  Min piksel    : {img.min()}")
print(f"  Max piksel    : {img.max()}")
print(f"  Ort. parlaklk : {ortalama:.1f}")
print(f"  Durum         : {parlaklik}")
print("=" * 40)

# Yan yana gösterim için gri → 3 kanal
gray_bgr  = cv2.cvtColor(gray, cv2.COLOR_GRAY2BGR)

# Başlıklar
cv2.putText(img.copy(),      "Orijinal (BGR)", (10, 30),
            cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 255), 2)
cv2.putText(gray_bgr.copy(), f"Gri | {parlaklik}", (10, 30),
            cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 255), 2)

# Aynı boyuta getirerek birleştir
panel = np.hstack([img, gray_bgr])
cv2.imshow("Goruntu Kimlik Karti", panel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`img.shape` her zaman `(yükseklik, genişlik, kanal)` sırasında döner. Gri görüntüyü `cv2.cvtColor(gray, cv2.COLOR_GRAY2BGR)` ile 3 kanala çevirmeden `np.hstack` ile birleştirmek kanal uyuşmazlığı hatası verir. Parlaklık eşikleri (85 ve 170) değiştirilebilir; önemli olan karar mekanizmasının sayısal bir ölçüme dayanmasıdır.

---

### Çözüm 1.2 — Üç Filtre, Bir Karar

**Çözüm yaklaşımı:**
Üç filtre aynı görüntüye uygulanır. Kenar korumayı değerlendirmek için Canny ile kenar yoğunluğu sayılabilir ya da kenar bölgeleri görsel olarak karşılaştırılabilir.

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    # Kenar korumanın belirgin olduğu yapay görüntü
    img = np.zeros((300, 400, 3), dtype=np.uint8)
    cv2.rectangle(img, (50, 50),  (180, 250), (200, 180, 120), -1)
    cv2.circle(img,    (300, 150), 80,         (100, 200, 80),  -1)
    img = img + np.random.randint(0, 20, img.shape, dtype=np.uint8)

gaussian  = cv2.GaussianBlur(img, (9, 9), 0)
median    = cv2.medianBlur(img, 9)
bilateral = cv2.bilateralFilter(img, d=9, sigmaColor=75, sigmaSpace=75)

BOYUT = (300, 225)

def etiket_ekle(g, metin):
    g = cv2.resize(g.copy(), BOYUT)
    cv2.putText(g, metin, (8, 26), cv2.FONT_HERSHEY_SIMPLEX,
                0.7, (0, 255, 255), 2)
    return g

panel = np.hstack([
    etiket_ekle(img,       "Orijinal"),
    etiket_ekle(gaussian,  "Gaussian"),
    etiket_ekle(median,    "Median"),
    etiket_ekle(bilateral, "Bilateral"),
])

cv2.imshow("Filtre Karsilastirma", panel)
cv2.waitKey(0)
cv2.destroyAllWindows()

# Kenar koruma değerlendirmesi
gray_o = cv2.cvtColor(img,       cv2.COLOR_BGR2GRAY)
gray_g = cv2.cvtColor(gaussian,  cv2.COLOR_BGR2GRAY)
gray_b = cv2.cvtColor(bilateral, cv2.COLOR_BGR2GRAY)

fark_gaussian  = cv2.absdiff(gray_o, gray_g)
fark_bilateral = cv2.absdiff(gray_o, gray_b)

print(f"Gaussian kenar kaybı (ortalama fark)  : {fark_gaussian.mean():.2f}")
print(f"Bilateral kenar kaybı (ortalama fark) : {fark_bilateral.mean():.2f}")
print("Karar: Bilateral filtre kenarları daha iyi korur çünkü")
print("renk benzerliği kriteri sayesinde kenar ötesi pikselleri")
print("hesaba katmaz. Gaussian tüm komşuları ağırlığına göre karıştırır.")
```

**Açıklama:**
Gaussian filtre tüm komşuları uzaklık ağırlığına göre karıştırır — kenarları körleştirir. Median aykırı değerleri temizler ama kenarları Gaussian'a benzer şekilde yumuşatır. Bilateral ise yalnızca renk değeri yakın olan komşuları hesaba katar; bu nedenle kenar piksellerinin ötesine "sızmaz" ve en iyi kenar korumayı sağlar.

---

### Çözüm 1.3 — Parlaklık Ayarlayıcı

**Çözüm yaklaşımı:**
`float32`'ye dönüştür, çarp, `np.clip` ile 0-255'e sıkıştır, `uint8`'e dön. Bu sıra `uint8` taşmasını önler.

**Kod:**
```python
import cv2
import numpy as np

def parlaklik_ayarla(img, alpha):
    """alpha < 1 → karart, alpha > 1 → aydınlat, alpha = 1 → değişiklik yok."""
    img_f  = img.astype(np.float32)
    ayarli = np.clip(img_f * alpha, 0, 255)
    return ayarli.astype(np.uint8)

img = cv2.imread("foto.jpg")
if img is None:
    img = np.random.randint(80, 200, (300, 400, 3), dtype=np.uint8)

alpha_degerleri = [0.5, 0.75, 1.0, 1.5, 2.0]
BOYUT           = (280, 210)
paneller        = []

for alpha in alpha_degerleri:
    sonuc = parlaklik_ayarla(img, alpha)
    gray  = cv2.cvtColor(sonuc, cv2.COLOR_BGR2GRAY)
    ort   = gray.mean()

    panel = cv2.resize(sonuc.copy(), BOYUT)
    cv2.putText(panel, f"a={alpha}  ort={ort:.0f}", (6, 24),
                cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 255, 0), 2)
    paneller.append(panel)

gosterim = np.hstack(paneller)
cv2.imshow("Parlaklik Ayarlayici", gosterim)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`uint8` tipinde 200 × 2 = 144 döner (taşma). `float32` tipinde 200 × 2 = 400.0, ardından `np.clip(400, 0, 255)` = 255 → güvenli sonuç. `alpha=1.0` durumunda `img_f * 1.0 = img_f` ve dönüşüm sonrası görüntü değişmez. `cv2.add` ile `np.full_like` kullanımı da güvenli alternatiftir.

---

### Çözüm 1.4 — Kenar Bölge Analizi

**Çözüm yaklaşımı:**
İki farklı Gaussian blur uygulanır, her biri Canny'ye gönderilir. Beyaz piksel sayıları karşılaştırılır.

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    img = np.zeros((400, 600, 3), dtype=np.uint8)
    for _ in range(8):
        pt1 = (np.random.randint(50, 550), np.random.randint(50, 350))
        pt2 = (np.random.randint(50, 550), np.random.randint(50, 350))
        cv2.line(img, pt1, pt2, (200, 200, 200), 2)

gray   = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur_k = cv2.GaussianBlur(gray, (3, 3),   0)
blur_b = cv2.GaussianBlur(gray, (15, 15), 0)

canny_k = cv2.Canny(blur_k, 50, 150)
canny_b = cv2.Canny(blur_b, 50, 150)

sayi_k  = cv2.countNonZero(canny_k)
sayi_b  = cv2.countNonZero(canny_b)

print(f"Kucuk kernel (3x3)  + Canny beyaz piksel: {sayi_k}")
print(f"Buyuk kernel (15x15)+ Canny beyaz piksel: {sayi_b}")
print()
if sayi_k > sayi_b:
    print("Kucuk kernel daha fazla kenar buldu.")
    print("Neden: Az yumusatma → gurultu piksel de kenar sayildi,")
    print("ince detaylar da korundu. Buyuk kernel gurultuyu de")
    print("gercek kenarlari da siler; yalnizca belirgin konturlar kalir.")
else:
    print("Buyuk kernel daha fazla kenar buldu (beklenmedik; goruntu kontrol edin).")

BOYUT = (280, 200)

def haz(g, baslik):
    g = cv2.resize(g, BOYUT)
    if len(g.shape) == 2:
        g = cv2.cvtColor(g, cv2.COLOR_GRAY2BGR)
    cv2.putText(g, baslik, (6, 22), cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0,255,255), 2)
    return g

satir1 = np.hstack([haz(blur_k, "Blur (3x3)"),   haz(canny_k, "Canny kucuk")])
satir2 = np.hstack([haz(blur_b, "Blur (15x15)"),  haz(canny_b, "Canny buyuk")])
cv2.imshow("Kernel Karsilastirma", np.vstack([satir1, satir2]))
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Büyük kernel gürültüyü bastırırken ince kenarları da siler; Canny daha az ama daha güvenilir kenar bulur. Küçük kernel az yumuşattığından gürültü ve gerçek kenarlar birlikte Canny'e girer; beyaz piksel sayısı yüksek ama bazıları yanlış pozitif olabilir.

---

### Çözüm 1.5 — Şekil Çizim Katmanı

**Çözüm yaklaşımı:**
Siyah canvas üzerinde şekiller çizilir, sonra `addWeighted` ile fotoğrafa bindirme yapılır.

**Kod:**
```python
import cv2
import numpy as np

canvas = np.zeros((600, 800, 3), dtype=np.uint8)

# 1. Dolu kırmızı daire
cv2.circle(canvas,    (150, 150), 80, (0, 0, 255), -1)
cv2.putText(canvas,   "Daire",   (60, 260),  cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255,255,255), 2)

# 2. İçi boş mavi dikdörtgen
cv2.rectangle(canvas, (280, 80), (520, 300), (255, 0, 0), 3)
cv2.putText(canvas,   "Dikdortgen", (290, 330), cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255,255,255), 2)

# 3. Çapraz yeşil çizgi (sol alttan sağ üste)
cv2.line(canvas, (0, 600), (800, 0), (0, 255, 0), 3)
cv2.putText(canvas, "Cizgi", (380, 420), cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255,255,255), 2)

# 4. Sarı elips (sağ alt)
cv2.ellipse(canvas, (660, 470), (100, 60), angle=30,
            startAngle=0, endAngle=360, color=(0, 255, 255), thickness=3)
cv2.putText(canvas, "Elips", (590, 560), cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255,255,255), 2)

cv2.imshow("Canvas", canvas)

# Fotoğrafla birleştir
foto = cv2.imread("foto.jpg")
if foto is not None:
    canvas_yeniden = cv2.resize(canvas, (foto.shape[1], foto.shape[0]))
    birlesik = cv2.addWeighted(foto, 0.7, canvas_yeniden, 0.5, 0)
    cv2.imshow("Bindirme Sonucu", birlesik)
    cv2.imwrite("birlesik.jpg", birlesik)

cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`addWeighted(src1, alpha, src2, beta, gamma)` → çıktı = src1 × alpha + src2 × beta + gamma. `alpha=0.7, beta=0.5` değerleri orijinal fotoğrafı biraz ön plana çıkarırken şekilleri belirgin bırakır. Her iki görselin aynı boyut ve tipte olması zorunludur.

---

## BLOK 2 — Orta Çözümler

---

### Çözüm 2.1 — HSV Dedektifi

**Çözüm yaklaşımı:**
Kırmızı renk HSV'de H=0-10 ve H=160-180 arasında iki bölgeye bölündüğünden iki ayrı maske oluşturulup birleştirilir. Sonra morfoloji ile maske temizlenir.

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("kirmizi_nesne.jpg")
if img is None:
    img = np.zeros((400, 600, 3), dtype=np.uint8)
    cv2.circle(img, (200, 200), 120, (0, 0, 220), -1)
    cv2.rectangle(img, (350, 100), (560, 350), (30, 30, 200), -1)

hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

# Kırmızı iki bölge: düşük H ve yüksek H
lower1, upper1 = np.array([0, 100, 80]),  np.array([10,  255, 255])
lower2, upper2 = np.array([160, 100, 80]), np.array([180, 255, 255])

mask1 = cv2.inRange(hsv, lower1, upper1)
mask2 = cv2.inRange(hsv, lower2, upper2)
mask  = cv2.bitwise_or(mask1, mask2)

# Morfoloji ile temizle
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (7, 7))
mask   = cv2.morphologyEx(mask, cv2.MORPH_OPEN,  kernel, iterations=2)
mask   = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, kernel, iterations=2)

# Konturlar
konturlar, _ = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)

kirmizi_piksel = cv2.countNonZero(mask)
print(f"Toplam kirmizi piksel: {kirmizi_piksel}")

gorsel = img.copy()
for cnt in konturlar:
    alan = cv2.contourArea(cnt)
    if alan < 300:
        continue
    cevre       = cv2.arcLength(cnt, True)
    dairesellik = (4 * np.pi * alan / cevre ** 2) if cevre > 0 else 0
    x, y, w, h  = cv2.boundingRect(cnt)

    cv2.drawContours(gorsel, [cnt], -1, (0, 255, 0), 2)
    cv2.rectangle(gorsel, (x, y), (x+w, y+h), (255, 0, 0), 2)
    print(f"Alan:{alan:.0f}  Cevre:{cevre:.1f}  Dairesellik:{dairesellik:.3f}")

sonuc = cv2.bitwise_and(img, img, mask=mask)
cv2.imshow("Orijinal",    img)
cv2.imshow("Maske",       mask)
cv2.imshow("Maskelenmis", sonuc)
cv2.imshow("Tespit",      gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Kırmızı renk HSV renk çarkında 360° döngüsünün sınırında bulunduğundan iki ayrı aralık gerekir. Tek bir `inRange` yalnızca bölgenin yarısını yakalar. `bitwise_or` iki maskeyi birleştirir. Morfoloji küçük yanlış tespitleri temizler.

---

### Çözüm 2.2 — Eşikleme Yarışması

**Çözüm yaklaşımı:**
Dört yöntem aynı gri görüntüye uygulanır; beyaz piksel sayıları ve görsel sonuçlar karşılaştırılır.

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("metin.jpg")
if img is None:
    img = (np.ones((300, 400, 3), dtype=np.uint8) * 180)
    for i in range(0, 400, 50):
        koyuluk = np.random.randint(100, 160)
        img[:, i:i+50] = img[:, i:i+50] * (koyuluk / 180)
    img = img.astype(np.uint8)
    cv2.putText(img, "METIN ORNEK", (40, 160), cv2.FONT_HERSHEY_SIMPLEX, 1.5, (30,30,30), 3)
    img = img.astype(np.uint8)

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# 4 yöntem
_, global_esik = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)
_, otsu_esik   = cv2.threshold(gray, 0,   255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)
adaptif_mean   = cv2.adaptiveThreshold(gray, 255, cv2.ADAPTIVE_THRESH_MEAN_C,
                                        cv2.THRESH_BINARY, 21, 4)
adaptif_gauss  = cv2.adaptiveThreshold(gray, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
                                        cv2.THRESH_BINARY, 21, 4)

for isim, sonuc in [("Global(127)", global_esik), ("Otsu", otsu_esik),
                     ("AdaptifMean", adaptif_mean), ("AdaptifGauss", adaptif_gauss)]:
    print(f"{isim:18s}: {cv2.countNonZero(sonuc):6d} beyaz piksel")

BOYUT = (300, 200)

def haz(g, baslik):
    g = cv2.resize(g, BOYUT)
    g = cv2.cvtColor(g, cv2.COLOR_GRAY2BGR)
    cv2.putText(g, baslik, (6, 22), cv2.FONT_HERSHEY_SIMPLEX, 0.55, (0, 200, 255), 2)
    return g

orijinal = cv2.resize(cv2.cvtColor(gray, cv2.COLOR_GRAY2BGR), BOYUT)
cv2.putText(orijinal, "Orijinal", (6, 22), cv2.FONT_HERSHEY_SIMPLEX, 0.55, (0, 200, 255), 2)

panel = np.hstack([orijinal,
                   haz(global_esik,  "Global(127)"),
                   haz(otsu_esik,    "Otsu"),
                   haz(adaptif_mean, "Adapt.Mean"),
                   haz(adaptif_gauss,"Adapt.Gauss")])

cv2.imshow("Esikleme Karsilastirma", panel)
cv2.waitKey(0)
cv2.destroyAllWindows()

print("\nSonuc: Duzensiz aydinlatmali belgede adaptif yöntemler üstündür.")
print("Global ve Otsu tum goruntuy tek esikle isler; golge alanlarda")
print("metin karanlasir ya da tamamen kaybolur. Adaptif yontemler")
print("her kucuk blokta kendi esigini hesaplar, bu yuzden yerel")
print("kontrast farkliliklarina karsi dayaniklidir.")
```

**Açıklama:**
Global ve Otsu yöntemleri tek bir eşik değeri kullandığından görüntünün bir bölümü aydınlık diğeri karanlıksa sınırda olan pikseller hatalı sınıflandırılır. Adaptif yöntemler her pikselin komşuluğunu ayrı değerlendirir; bu "yerel" karar sayesinde aydınlatma farklılıklarına dayanıklıdır.

---

### Çözüm 2.3 — Morfoloji Tasarımcısı

**Çözüm yaklaşımı:**
Her problem farklı bir morfolojik işlemle çözülür. Problem A: Opening (gürültü sil). Problem B: Closing (delik kapat). Problem C: Erozyon (bağlantı kes).

**Kod:**
```python
import cv2
import numpy as np

# Test görüntüsü: gürültü, delikli nesne ve bitişik nesneler
canvas = np.zeros((400, 600), dtype=np.uint8)
# Ana nesneler
cv2.circle(canvas,    (150, 200), 80, 255, -1)
cv2.rectangle(canvas, (280, 100),(500, 320), 255, -1)
# Gürültü noktaları
for _ in range(80):
    x, y = np.random.randint(0, 600), np.random.randint(0, 400)
    cv2.circle(canvas, (x, y), np.random.randint(1, 4), 255, -1)
# Nesne içinde delikler
cv2.circle(canvas,    (150, 200), 25, 0, -1)
cv2.rectangle(canvas, (340, 150),(430, 250), 0, -1)
# İnce bağlantı (iki nesneyi bağlıyor)
cv2.line(canvas, (230, 200), (280, 200), 255, 4)

img = canvas.copy()

# --- Problem A: Küçük gürültü noktalarını sil (Opening) ---
# Küçük kernel → yalnızca küçük noktalar silinir, büyük nesneler korunur
kernel_a = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (7, 7))
cozum_a  = cv2.morphologyEx(img, cv2.MORPH_OPEN, kernel_a, iterations=1)
# NEDEN: Opening = önce erozyon (küçük noktaları siler) → sonra dilasyon (büyük nesneleri geri getirir)

# --- Problem B: Nesne içindeki delikleri kapat (Closing) ---
kernel_b = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (9, 9))
cozum_b  = cv2.morphologyEx(img, cv2.MORPH_CLOSE, kernel_b, iterations=2)
# NEDEN: Closing = önce dilasyon (delikleri doldurur) → sonra erozyon (boyutu geri getirir)

# --- Problem C: İnce bağlantıyı kes (Erozyon) ---
kernel_c = cv2.getStructuringElement(cv2.MORPH_RECT, (5, 5))
cozum_c  = cv2.erode(img, kernel_c, iterations=2)
# NEDEN: Erozyon tüm beyaz bölgeleri kenardan aşındırır; ince bağlantı tamamen silinir

# Görselleştirme
BOYUT = (400, 300)

def goster(g, baslik):
    g = cv2.resize(g, BOYUT)
    g = cv2.cvtColor(g, cv2.COLOR_GRAY2BGR)
    cv2.putText(g, baslik, (6, 26), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0,255,255), 2)
    return g

orijinal_p = goster(img.copy(), "Orijinal")

cv2.imshow("Problem A - Gurultu Sil", np.hstack([orijinal_p, goster(cozum_a, "Opening (gurultu sil)")]))
cv2.imshow("Problem B - Delik Kapat", np.hstack([orijinal_p, goster(cozum_b, "Closing (delik kapat)")]))
cv2.imshow("Problem C - Bag. Kes",    np.hstack([orijinal_p, goster(cozum_c, "Erozyon (baglantiyi kes)")]))

cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Opening (erozyon→dilasyon) küçük nesneleri siler çünkü erozyon onları yok eder ve dilasyon geri getiremez. Closing (dilasyon→erozyon) delikleri kapatır çünkü dilasyon içeriye girip doldurur, erozyon boyutu geri alır ama delik artık kapanmıştır. Erozyon tek başına nesneleri küçülterek ince bağlantıları keser.

---

### Çözüm 2.4 — Akıllı Nesne Sayacı

**Çözüm yaklaşımı:**
Ön işleme → eşikleme → morfoloji → kontur tespiti → alan filtresi → özellik tablosu.

**Kod:**
```python
import cv2
import numpy as np

# Test görüntüsü
canvas = np.zeros((500, 800, 3), dtype=np.uint8)
cv2.circle(canvas,    (100, 150), 75, (255,255,255), -1)
cv2.circle(canvas,    (280, 150), 45, (255,255,255), -1)
cv2.rectangle(canvas, (380, 90), (560, 220), (255,255,255), -1)
cv2.rectangle(canvas, (610, 110),(770, 200), (255,255,255), -1)
pts = np.array([[100,380],[200,480],[0,480]], np.int32)
cv2.fillPoly(canvas, [pts], (255,255,255))
cv2.circle(canvas,    (350, 380), 90, (255,255,255), -1)
img = canvas

gray   = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur   = cv2.GaussianBlur(gray, (7, 7), 0)
_, bin = cv2.threshold(blur, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
bin    = cv2.morphologyEx(bin, cv2.MORPH_OPEN,  kernel, iterations=2)
bin    = cv2.morphologyEx(bin, cv2.MORPH_CLOSE, kernel, iterations=2)

konturlar, _ = cv2.findContours(bin.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)

MIN_ALAN = 400
gorsel   = img.copy()
nesneler = []

for cnt in sorted(konturlar, key=cv2.contourArea, reverse=True):
    alan = cv2.contourArea(cnt)
    if alan < MIN_ALAN:
        continue

    cevre       = cv2.arcLength(cnt, True)
    dairesellik = (4 * np.pi * alan / cevre**2) if cevre > 0 else 0
    approx      = cv2.approxPolyDP(cnt, 0.03 * cevre, True)
    kose        = len(approx)
    x, y, w, h  = cv2.boundingRect(cnt)
    oran        = w / h if h > 0 else 0

    if kose == 3:
        sekil = "Ucgen"
    elif kose == 4:
        sekil = "Kare" if 0.9 <= oran <= 1.1 else "Dikdortgen"
    elif dairesellik > 0.80:
        sekil = "Daire"
    else:
        sekil = f"{kose}-kose"

    nesneler.append((alan, cevre, dairesellik, sekil))

    M  = cv2.moments(cnt)
    numarasi = len(nesneler)
    cv2.drawContours(gorsel, [cnt], -1, (0, 255, 0), 2)
    if M["m00"] > 0:
        cx = int(M["m10"] / M["m00"])
        cy = int(M["m01"] / M["m00"])
        cv2.putText(gorsel, f"#{numarasi} {sekil}", (cx - 30, cy),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.55, (0, 0, 255), 2)

# Tablo yazdır
print(f"\n{'Nesne':>6} | {'Alan':>8} | {'Cevre':>7} | {'Dairesellik':>12} | Sekil")
print("-" * 55)
for i, (alan, cevre, dairesellik, sekil) in enumerate(nesneler, 1):
    print(f"{i:>6} | {alan:>8.0f} | {cevre:>7.1f} | {dairesellik:>12.3f} | {sekil}")
print(f"\nToplam: {len(nesneler)} nesne")

cv2.putText(gorsel, f"Toplam: {len(nesneler)}", (10, 35),
            cv2.FONT_HERSHEY_SIMPLEX, 1.0, (0, 0, 255), 2)
cv2.imshow("Nesne Sayaci", gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Konturlar alana göre sıralanır; bu, büyük nesneleri önce işler. `moments` ile bulunan kütle merkezi, etiket yerleştirmek için nesnenin tam ortasını verir. Dairesellik + köşe sayısı kombinasyonu şekil adını belirlemenin güvenilir yoludur.

---

### Çözüm 2.5 — Histogram Dedektifi

**Çözüm yaklaşımı:**
Histogram hesaplanır, standart sapma ile kontrast ölçülür. Üç yöntem karşılaştırılır.

**Kod:**
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

# İki farklı görüntü
iyi = cv2.imread("iyi_kontrastli.jpg")
dusuk = cv2.imread("dusuk_kontrastli.jpg")

if iyi is None:
    iyi   = np.random.randint(30, 225, (300, 400, 3), dtype=np.uint8)
if dusuk is None:
    dusuk = np.random.randint(100, 160, (300, 400, 3), dtype=np.uint8)

def analiz_et(img, baslik):
    gray  = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    std   = gray.std()
    ort   = gray.mean()
    print(f"\n{baslik}")
    print(f"  Ortalama: {ort:.1f}  Std sapma: {std:.1f}  "
          f"({'Iyi' if std > 50 else 'Dusuk'} kontrast)")

    esit  = cv2.equalizeHist(gray)

    clahe_listesi = {}
    for clip in [1.0, 2.0, 4.0]:
        clahe_obj    = cv2.createCLAHE(clipLimit=clip, tileGridSize=(8,8))
        clahe_listesi[clip] = clahe_obj.apply(gray)

    return gray, esit, clahe_listesi

gray_i, esit_i, clahe_i = analiz_et(iyi,   "Iyi Kontrastli Goruntu")
gray_d, esit_d, clahe_d = analiz_et(dusuk, "Dusuk Kontrastli Goruntu")

fig, axes = plt.subplots(4, 4, figsize=(16, 10))
fig.suptitle("Histogram Analizi — İki Görüntü Karşılaştırması")

gorseller = [
    (gray_i, "Orijinal (iyi)"), (esit_i, "EqualizeHist"),
    (clahe_i[2.0], "CLAHE clip=2"), (clahe_i[4.0], "CLAHE clip=4"),
    (gray_d, "Orijinal (düşük)"), (esit_d, "EqualizeHist"),
    (clahe_d[2.0], "CLAHE clip=2"), (clahe_d[4.0], "CLAHE clip=4"),
]

for i, (g, baslik) in enumerate(gorseller):
    satir = i // 4
    sutun = i % 4
    hist  = cv2.calcHist([g], [0], None, [256], [0, 256])

    axes[satir * 2][sutun].imshow(g, cmap='gray')
    axes[satir * 2][sutun].set_title(baslik, fontsize=8)
    axes[satir * 2][sutun].axis('off')

    renk = 'blue' if satir == 0 else 'green'
    axes[satir * 2 + 1][sutun].plot(hist, color=renk)
    axes[satir * 2 + 1][sutun].set_xlim([0, 256])

plt.tight_layout()
plt.show()

print("\nClipLimit etkisi: Dusuk kontrastli goruntude")
print("clip=1 → nazik iyilestirme, clip=4 → agresif iyilestirme")
print("Yuksek clip deger bazi bolgelerde asilama/hata olusturabilir.")
print("Iyi kontrastli goruntude equalizeHist bazi bolgeleri asabilir;")
print("CLAHE daha dogal kalir.")
```

**Açıklama:**
Standart sapma görüntünün "dinamik aralığını" sayısal olarak ölçer; düşük std → sıkışık histogram → düşük kontrast. `equalizeHist` histogramı düzleştirirken bazı bölgeler aşırı parlayabilir. CLAHE'nin `clipLimit` parametresi bu "aşırı amplifikasyonu" frenler; yüksek değer daha agresif iyileştirme ama gürültü artışı riski demektir.

---

### Çözüm 2.6 — Kenar Yoğunluğu Haritası

**Çözüm yaklaşımı:**
Kenar haritası ızgaraya bölünür, her bloktaki beyaz piksel sayısı bir matrise yazılır, normalize edilip ısı haritasına dönüştürülür.

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    img = np.zeros((480, 640, 3), dtype=np.uint8)
    for _ in range(20):
        pt1 = (np.random.randint(0,640), np.random.randint(0,480))
        pt2 = (np.random.randint(0,640), np.random.randint(0,480))
        cv2.line(img, pt1, pt2, (180,180,180), np.random.randint(1,6))

gray  = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur  = cv2.GaussianBlur(gray, (5, 5), 0)
kenar = cv2.Canny(blur, 50, 150)

IZGARAH, IZGARAK = 16, 16  # blok sayısı
H, W = kenar.shape
bH   = H // IZGARAH
bK   = W // IZGARAK

# Blok başına kenar sayısını hesapla
yogunluk_mat = np.zeros((IZGARAH, IZGARAK), dtype=np.float32)
for r in range(IZGARAH):
    for c in range(IZGARAK):
        blok = kenar[r*bH:(r+1)*bH, c*bK:(c+1)*bK]
        yogunluk_mat[r, c] = cv2.countNonZero(blok)

# Normalize et → 0-255 arası uint8
yogunluk_norm = cv2.normalize(yogunluk_mat, None, 0, 255, cv2.NORM_MINMAX)
yogunluk_u8   = yogunluk_norm.astype(np.uint8)

# Orijinal boyuta büyüt
yogunluk_buyuk = cv2.resize(yogunluk_u8, (W, H), interpolation=cv2.INTER_LINEAR)

# Isı haritası renklendirme (COLORMAP_JET: mavi→yeşil→kırmızı)
isi_haritasi = cv2.applyColorMap(yogunluk_buyuk, cv2.COLORMAP_JET)

# Orijinal görüntüyle bindirme
birlesik = cv2.addWeighted(img, 0.5, isi_haritasi, 0.6, 0)

cv2.imshow("Kenar Haritasi",    kenar)
cv2.imshow("Isi Haritasi",      isi_haritasi)
cv2.imshow("Orijinal + Isi",    birlesik)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Her bloktaki `countNonZero` o bölgede kaç kenar pikseli olduğunu verir. `normalize` değerleri 0-255 aralığına getirir. `COLORMAP_JET` düşük değerleri maviye, yüksek değerleri kırmızıya boyar; böylece en yoğun kenar bölgeleri görsel olarak öne çıkar. `addWeighted` ile bindirme yapıldığında hangi bölgelerin "dolu" olduğu sezgisel biçimde görülür.

---

## BLOK 3 — Zor Çözümler

---

### Çözüm 3.1 — Bozuk Metin Onarımı

**Çözüm yaklaşımı:**
Adaptif eşikleme → closing (harf boşluklarını kapat) → opening (gürültü sil) → kontur tespiti ile kelime grupları.

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("bozuk_metin.jpg")
if img is None:
    img = np.ones((300, 600, 3), dtype=np.uint8) * 200
    for i in range(0, 600, 60):
        img[:, i:i+60] = img[:, i:i+60] * (np.random.randint(80, 120) / 100)
    img = img.astype(np.uint8)
    cv2.putText(img, "Deneme Metin", (30, 100),
                cv2.FONT_HERSHEY_SIMPLEX, 1.8, (30, 30, 30), 3)
    cv2.putText(img, "Ornek Kelime", (30, 200),
                cv2.FONT_HERSHEY_SIMPLEX, 1.8, (30, 30, 30), 3)
    for _ in range(40):
        x, y = np.random.randint(0,600), np.random.randint(0,300)
        cv2.circle(img, (x, y), np.random.randint(1,4), (50,50,50), -1)

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# 1. Adaptif eşikleme — düzensiz arka plana karşı güçlü
esik = cv2.adaptiveThreshold(gray, 255,
                              cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
                              cv2.THRESH_BINARY_INV, 21, 5)

# 2. Harf kopmaları ve boşlukları kapat (closing — önce dilasyon)
kernel_close = cv2.getStructuringElement(cv2.MORPH_RECT, (3, 3))
onarmis      = cv2.morphologyEx(esik, cv2.MORPH_CLOSE, kernel_close, iterations=2)

# 3. Gürültü noktalarını temizle (opening — önce erozyon)
kernel_open = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (3, 3))
temiz        = cv2.morphologyEx(onarmis, cv2.MORPH_OPEN, kernel_open, iterations=1)

# 4. Kelime grupları için geniş yatay dilation
kernel_word = cv2.getStructuringElement(cv2.MORPH_RECT, (25, 3))
kelimeler   = cv2.dilate(temiz, kernel_word, iterations=1)

konturlar, _ = cv2.findContours(kelimeler.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)

gorsel = img.copy()
print(f"\n{'ID':>4} | {'x':>5} {'y':>5} {'Genislik':>10} {'Yukseklik':>10}")
print("-" * 40)
for i, cnt in enumerate(sorted(konturlar, key=lambda c: cv2.boundingRect(c)[0]), 1):
    alan = cv2.contourArea(cnt)
    if alan < 200:
        continue
    x, y, w, h = cv2.boundingRect(cnt)
    cv2.rectangle(gorsel, (x, y), (x+w, y+h), (0, 200, 255), 2)
    cv2.putText(gorsel, str(i), (x, y-5),
                cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 0, 255), 2)
    print(f"{i:>4} | {x:>5} {y:>5} {w:>10} {h:>10}")

BOYUT = (600, 200)
cv2.imshow("Adim 1 Esik",   cv2.resize(esik,    BOYUT))
cv2.imshow("Adim 2 Onarim", cv2.resize(onarmis, BOYUT))
cv2.imshow("Adim 3 Temiz",  cv2.resize(temiz,   BOYUT))
cv2.imshow("Adim 4 Tespit", cv2.resize(gorsel,  BOYUT))
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Metin görüntülerinde `THRESH_BINARY_INV` metni beyaz, arka planı siyah yapar. Closing önce harfleri biraz şişirip kopmaları kapatır, erozyon adımı boyutu geri getirir. Geniş yatay kernel ile yapılan dilation harfleri birleştirip kelimeleri tek kontur haline getirir; bu teknik metin segmentasyonunda yaygın kullanılır.

---

### Çözüm 3.2 — Otomatik Pano Algılayıcı

**Çözüm yaklaşımı:**
Canny + dilation → konturları bul → 4 köşeli en büyük kontur → köşeleri sırala → perspektif düzelt → CLAHE.

**Kod:**
```python
import cv2
import numpy as np

def koseler_sirala(pts):
    pts    = pts.reshape(4, 2).astype(np.float32)
    sirali = np.zeros((4, 2), dtype=np.float32)
    toplam = pts.sum(axis=1)
    fark   = np.diff(pts, axis=1).flatten()
    sirali[0] = pts[np.argmin(toplam)]   # TL
    sirali[2] = pts[np.argmax(toplam)]   # BR
    sirali[1] = pts[np.argmin(fark)]     # TR
    sirali[3] = pts[np.argmax(fark)]     # BL
    return sirali

img = cv2.imread("pano.jpg")
if img is None:
    img = np.ones((500, 700, 3), dtype=np.uint8) * 160
    pts_pano = np.array([[80, 60], [600, 40], [640, 460], [60, 480]])
    cv2.fillPoly(img, [pts_pano], (230, 225, 215))
    cv2.putText(img, "PANO", (250, 270), cv2.FONT_HERSHEY_SIMPLEX, 3, (50,50,50), 5)

orijinal = img.copy()
gray     = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur     = cv2.GaussianBlur(gray, (5, 5), 0)
edges    = cv2.Canny(blur, 50, 200)
kernel   = np.ones((3, 3), np.uint8)
edges    = cv2.dilate(edges, kernel, iterations=1)

konturlar, _ = cv2.findContours(edges.copy(), cv2.RETR_LIST,
                                 cv2.CHAIN_APPROX_SIMPLE)
konturlar     = sorted(konturlar, key=cv2.contourArea, reverse=True)[:10]

pano_cnt = None
for cnt in konturlar:
    cevre    = cv2.arcLength(cnt, True)
    yaklasik = cv2.approxPolyDP(cnt, 0.02 * cevre, True)
    if len(yaklasik) == 4 and cv2.contourArea(cnt) > 3000:
        pano_cnt = yaklasik
        break

koseli = orijinal.copy()

if pano_cnt is None:
    print("Pano bulunamadı.")
    cv2.imshow("Sonuc", orijinal)
else:
    pts = koseler_sirala(pano_cnt)
    tl, tr, br, bl = pts

    for nokta in pts.astype(int):
        cv2.circle(koseli, tuple(nokta), 10, (0, 0, 255), -1)
    cv2.drawContours(koseli, [pano_cnt], -1, (0, 255, 0), 3)

    w = int(max(np.linalg.norm(tr-tl), np.linalg.norm(br-bl)))
    h = int(max(np.linalg.norm(tl-bl), np.linalg.norm(tr-br)))

    dst = np.float32([[0,0],[w,0],[w,h],[0,h]])
    M   = cv2.getPerspectiveTransform(pts, dst)
    duz = cv2.warpPerspective(img, M, (w, h))

    gray_d = cv2.cvtColor(duz, cv2.COLOR_BGR2GRAY)
    clahe  = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8,8))
    iyilestirilmis = clahe.apply(gray_d)

    BOYUT = (500, 380)
    panel = np.hstack([
        cv2.resize(orijinal, BOYUT),
        cv2.resize(koseli,   BOYUT),
        cv2.cvtColor(cv2.resize(iyilestirilmis, BOYUT), cv2.COLOR_GRAY2BGR)
    ])

    cv2.imshow("Orijinal | Koseler | Duzeltilmis", panel)
    cv2.imwrite("duzeltilmis_pano.jpg", iyilestirilmis)

cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`approxPolyDP` konturun köşelerini basitleştirir; epsilon ne kadar büyükse o kadar az köşe kalır. Köşeleri TL-TR-BR-BL sırasına koymak için koordinat toplamı ve farkı kullanılır: sol-üst noktanın x+y toplamı en küçük, sağ-alt noktanın en büyük olur. Bu sıralama perspektif matrisinin doğru hesaplanması için zorunludur.

---

### Çözüm 3.3 — İki Görüntü Fark Dedektifi

**Çözüm yaklaşımı:**
`absdiff` ile fark alınır, gri + eşikleme ile binary hale getirilir, morfoloji ile temizlenir, konturlarla farklılıklar işaretlenir.

**Kod:**
```python
import cv2
import numpy as np

# İki benzer görüntü oluştur / yükle
img1 = cv2.imread("img1.jpg")
img2 = cv2.imread("img2.jpg")

if img1 is None or img2 is None:
    base = np.ones((400, 600, 3), dtype=np.uint8) * 200
    cv2.circle(base,    (150, 150), 60, (100, 200, 100), -1)
    cv2.rectangle(base, (300, 100),(500, 300), (200, 100, 100), -1)
    cv2.circle(base,    (500, 320), 50, (100, 100, 200), -1)
    img1 = base.copy()
    img2 = base.copy()
    # Görüntü 2'de 3 farklılık yap
    cv2.circle(img2,    (150, 150), 60, (200, 50, 50),  -1)  # renk değişti
    cv2.rectangle(img2, (320, 140),(400, 260), (0, 0, 0), -1)   # kutu içi siyah
    cv2.circle(img2,    (80, 320),  40, (255, 255, 0),  -1)     # yeni nesne

# Aynı boyuta getir
if img1.shape != img2.shape:
    img2 = cv2.resize(img2, (img1.shape[1], img1.shape[0]))

# Fark hesapla
fark     = cv2.absdiff(img1, img2)
fark_gri = cv2.cvtColor(fark, cv2.COLOR_BGR2GRAY)

# Eşikle → binary
_, binary = cv2.threshold(fark_gri, 30, 255, cv2.THRESH_BINARY)

# Morfoloji ile temizle ve genişlet
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (7, 7))
binary = cv2.morphologyEx(binary, cv2.MORPH_OPEN,  kernel)
binary = cv2.morphologyEx(binary, cv2.MORPH_CLOSE, kernel, iterations=2)

# Konturlar
konturlar, _ = cv2.findContours(binary.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)

sonuc1 = img1.copy()
sonuc2 = img2.copy()

print(f"\nFark eşiği: 30  |  Min alan: 200 px")
print(f"{'ID':>4} | {'Alan':>8} | {'Konum (x,y,w,h)'}")
print("-" * 45)

sayi = 0
for cnt in konturlar:
    alan = cv2.contourArea(cnt)
    if alan < 200:
        continue
    sayi += 1
    x, y, w, h = cv2.boundingRect(cnt)
    cv2.rectangle(sonuc1, (x,y), (x+w,y+h), (0,0,255), 2)
    cv2.rectangle(sonuc2, (x,y), (x+w,y+h), (0,0,255), 2)
    cv2.putText(sonuc1, f"#{sayi}", (x,y-5), cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0,0,255), 2)
    cv2.putText(sonuc2, f"#{sayi}", (x,y-5), cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0,0,255), 2)
    print(f"{sayi:>4} | {alan:>8.0f} | ({x}, {y}, {w}, {h})")

print(f"\nToplam {sayi} farklılık tespit edildi.")

BOYUT = (500, 350)
panel = np.hstack([cv2.resize(sonuc1, BOYUT), cv2.resize(sonuc2, BOYUT)])
cv2.imshow("Goruntu 1 | Goruntu 2", panel)
cv2.imshow("Fark Haritasi", cv2.resize(binary, BOYUT))
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`absdiff` mutlak fark değerini hesaplar; her kanalda 0 ile 255 arasında bir değer döner. Gri dönüşümü ve eşikleme bu farkı binary maskeye çevirir. Eşik değeri (30) gürültü ile gerçek farkı ayırt eder; çok düşük tutulursa gürültü de farklılık sayılır. Morfoloji ile küçük gürültü noktaları silinir ve bitişik farklılıklar birleştirilir.

---

### Çözüm 3.4 — Tek Renkli Nesne Takip Edici

**Çözüm yaklaşımı:**
HSV maskeleme → en büyük kontur → minEnclosingCircle → deque ile iz → iz çizimi.

**Kod:**
```python
import cv2
import numpy as np
from collections import deque

# Takip rengi yapılandırması (kolayca değiştirilebilir)
RENK_AYARLARI = {
    "isim"     : "Sari",
    "lower_hsv": np.array([20,  100, 100]),
    "upper_hsv": np.array([35,  255, 255]),
    "renk_bgr" : (0, 255, 255),
}

IZ_UZUNLUGU   = 40
MIN_YARI_CAP  = 15   # piksel

cap = cv2.VideoCapture(0)
if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

iz     = deque(maxlen=IZ_UZUNLUGU)
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (9, 9))

lower = RENK_AYARLARI["lower_hsv"]
upper = RENK_AYARLARI["upper_hsv"]

try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break
        frame = cv2.flip(frame, 1)

        hsv  = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
        mask = cv2.inRange(hsv, lower, upper)
        mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN,  kernel)
        mask = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, kernel)

        konturlar, _ = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
                                         cv2.CHAIN_APPROX_SIMPLE)
        merkez = None

        if konturlar:
            en_buyuk = max(konturlar, key=cv2.contourArea)
            ((cx, cy), yari_cap) = cv2.minEnclosingCircle(en_buyuk)

            if yari_cap > MIN_YARI_CAP:
                M = cv2.moments(en_buyuk)
                if M["m00"] > 0:
                    merkez = (int(M["m10"] / M["m00"]),
                              int(M["m01"] / M["m00"]))

                cv2.circle(frame, (int(cx), int(cy)), int(yari_cap),
                           RENK_AYARLARI["renk_bgr"], 2)
                cv2.circle(frame, merkez, 5, (0, 0, 255), -1)

        iz.appendleft(merkez)

        # İz çiz — eskiye gidildikçe ince ve soluk
        for i in range(1, len(iz)):
            if iz[i-1] is None or iz[i] is None:
                continue
            kalinlik = max(1, int(np.sqrt(IZ_UZUNLUGU / float(i + 1)) * 3))
            alfa     = max(0.2, 1.0 - i / IZ_UZUNLUGU)
            renk     = tuple(int(c * alfa) for c in RENK_AYARLARI["renk_bgr"])
            cv2.line(frame, iz[i-1], iz[i], renk, kalinlik)

        durum = "Nesne Kayip" if merkez is None else f"Konum: {merkez}"
        durum_renk = (0, 0, 200) if merkez is None else (0, 200, 0)
        cv2.putText(frame, f"{RENK_AYARLARI['isim']} Takip | {durum}",
                    (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 0.7, durum_renk, 2)

        cv2.imshow("Renk Takip", frame)

        key = cv2.waitKey(30) & 0xFF
        if key == ord('q'):
            break
        elif key == ord('r'):
            iz.clear()
            print("İz sıfırlandı.")
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Açıklama:**
`deque(maxlen=N)` otomatik olarak en eski konumu siler, bellek verimlidir. `iz[i-1]` en yeni konum, `iz[-1]` en eskidir; `i` büyüdükçe kalınlık `sqrt` ile azaltılır. `alfa` değeri ize "soluklaşma" efekti verir. `minEnclosingCircle` nesnenin yaklaşık yarı çapını verir; bu değer nesnenin görüntüdeki boyutunu izlemeye de yarar.

---

### Çözüm 3.5 — Tam Görüntü Analiz Pipeline'ı

**Çözüm yaklaşımı:**
Her adım için önce gerekçe, sonra uygulama. Pipeline görüntüye özgü değil, genel çalışacak şekilde tasarlanır.

**Kod:**
```python
import cv2
import numpy as np

# ----------------------------------------------------------------
# ADIM 0 — Görüntüyü yükle ve hazırla
# ----------------------------------------------------------------
img = cv2.imread("nesneler.jpg")
if img is None:
    canvas = np.zeros((500, 800, 3), dtype=np.uint8)
    cv2.circle(canvas,    (100, 120), 70,  (255,255,255), -1)
    cv2.circle(canvas,    (280, 120), 40,  (255,255,255), -1)
    cv2.rectangle(canvas, (380, 70), (560, 200), (255,255,255), -1)
    cv2.rectangle(canvas, (610, 80), (780, 185), (255,255,255), -1)
    pts = np.array([[100,350],[220,460],[0,460]], np.int32)
    cv2.fillPoly(canvas, [pts], (255,255,255))
    cv2.circle(canvas,    (420, 380), 100, (255,255,255), -1)
    img = canvas

orijinal = img.copy()

# ----------------------------------------------------------------
# ADIM 1 — Ön işleme
# NEDEN: Görüntüyü eşiklemeye hazırlamak için gri + blur gerekli.
#        Otsu yöntemi bimodal histogram için uygundur.
# ----------------------------------------------------------------
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur = cv2.GaussianBlur(gray, (7, 7), 0)
_, binary = cv2.threshold(blur, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

# ----------------------------------------------------------------
# ADIM 2 — Nesne tespiti
# NEDEN: Opening küçük gürültü noktalarını siler,
#        Closing nesne içindeki boşlukları kapatır.
# ----------------------------------------------------------------
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
binary = cv2.morphologyEx(binary, cv2.MORPH_OPEN,  kernel, iterations=2)
binary = cv2.morphologyEx(binary, cv2.MORPH_CLOSE, kernel, iterations=2)

konturlar, _ = cv2.findContours(binary.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)

# ----------------------------------------------------------------
# ADIM 3 — Özellik çıkarımı
# NEDEN: Her nesne için alan, dairesellik, köşe sayısı hesaplanır.
# ----------------------------------------------------------------
MIN_ALAN = 300
nesneler = []

RENKLER = [(0,255,0),(255,165,0),(0,0,255),(255,0,255),(0,255,255),(255,255,0)]

for cnt in sorted(konturlar, key=cv2.contourArea, reverse=True):
    alan = cv2.contourArea(cnt)
    if alan < MIN_ALAN:
        continue

    cevre       = cv2.arcLength(cnt, True)
    dairesellik = (4 * np.pi * alan / cevre**2) if cevre > 0 else 0
    approx      = cv2.approxPolyDP(cnt, 0.03 * cevre, True)
    kose        = len(approx)
    x, y, w, h  = cv2.boundingRect(cnt)
    oran        = w / h if h > 0 else 0

    if kose == 3:
        sekil = "Ucgen"
    elif kose == 4:
        sekil = "Kare" if 0.9 <= oran <= 1.1 else "Dikdortgen"
    elif dairesellik > 0.82:
        sekil = "Daire"
    else:
        sekil = f"{kose}-kose"

    M = cv2.moments(cnt)
    cx = int(M["m10"]/M["m00"]) if M["m00"] > 0 else x + w//2
    cy = int(M["m01"]/M["m00"]) if M["m00"] > 0 else y + h//2

    nesneler.append({
        "alan": alan, "cevre": cevre, "dairesellik": dairesellik,
        "sekil": sekil, "cnt": cnt, "cx": cx, "cy": cy,
        "x": x, "y": y, "w": w, "h": h
    })

# ----------------------------------------------------------------
# ADIM 4 — Görselleştirme
# ----------------------------------------------------------------
gorsel = img.copy()
for i, n in enumerate(nesneler):
    renk = RENKLER[i % len(RENKLER)]
    cv2.drawContours(gorsel, [n["cnt"]], -1, renk, 2)
    cv2.rectangle(gorsel, (n["x"], n["y"]), (n["x"]+n["w"], n["y"]+n["h"]), renk, 1)
    cv2.putText(gorsel, n["sekil"], (n["cx"]-30, n["cy"]-8),
                cv2.FONT_HERSHEY_SIMPLEX, 0.55, renk, 2)
    cv2.putText(gorsel, f"A:{n['alan']:.0f}", (n["cx"]-25, n["cy"]+12),
                cv2.FONT_HERSHEY_SIMPLEX, 0.45, renk, 1)

cv2.putText(gorsel, f"Toplam: {len(nesneler)}", (10, 30),
            cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 0, 255), 2)

# ----------------------------------------------------------------
# ADIM 5 — Rapor
# ----------------------------------------------------------------
print(f"\n{'Sira':>5} | {'Sekil':>12} | {'Alan':>8} | {'Dairesellik':>12}")
print("-" * 48)
for i, n in enumerate(nesneler, 1):
    print(f"{i:>5} | {n['sekil']:>12} | {n['alan']:>8.0f} | {n['dairesellik']:>12.3f}")

alanlar = [n["alan"] for n in nesneler]
daires  = [n["dairesellik"] for n in nesneler]
print(f"\nToplam nesne     : {len(nesneler)}")
print(f"En büyük alan    : {max(alanlar):.0f} px")
print(f"En küçük alan    : {min(alanlar):.0f} px")
print(f"Ort. dairesellik : {np.mean(daires):.3f}")

# ----------------------------------------------------------------
# ADIM 6 — Kalite değerlendirmesi
# ----------------------------------------------------------------
print("\n--- Kalite Değerlendirmesi ---")
print("Başarılar:")
print("  + Beyaz nesneler düz arka plan üzerinde iyi ayrıştı.")
print("  + Otsu eşiği kontrast yüksek olduğundan iyi çalıştı.")
print("Sınırlılıklar:")
print("  - Gürültülü veya düşük kontrastlı görüntülerde başarısız olabilir.")
print("  - Birbiriyle temas eden nesneleri ayırt edemez.")
print("  - Gerçek dünya görüntüleri için HSV + renk bilgisi eklenmelidir.")

cv2.imshow("Analiz Pipeline Sonucu", gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Bu pipeline'ı gerçek bir görüntüde çalıştırmak için en kritik adım eşikleme seçimidir. Düz beyaz/siyah görüntülerde Otsu mükemmel çalışır. Düzensiz aydınlatmada adaptif eşikleme daha güvenilirdir. "Kalite değerlendirmesi" adımı; bir çözümün sınırlarını fark etmeyi, farklı koşullar için nasıl geliştirileceğini düşünmeyi öğretir — bu, gerçek mühendislik pratiğinin ayrılmaz parçasıdır.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `opencv-teori.md` · `opencv-uygulamalar.md` · `opencv-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
