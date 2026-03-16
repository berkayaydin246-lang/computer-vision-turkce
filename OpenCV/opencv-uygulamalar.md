# 🛠️ OpenCV — Uygulamalar

> **Modül:** Uygulamalar | **Seviye:** Başlangıç → Orta
> **İlgili dosyalar:** `opencv-teori.md` · `opencv-alistirmalar.md`

---

## BLOK 0 — Uygulama Belgesi Hakkında

Bu belge, teori belgesinde öğrendiğiniz OpenCV kavramlarını **gerçek görevlerde** nasıl kullanacağınızı gösterir. Her uygulama bir problemi tanımlar, çözüm pipeline'ını açıklar ve çalışır Python kodu sunar.

**Teori öğrendim, şimdi ne yapmalıyım?** sorusunun cevabıdır.

**Belgede neler var:**

| Blok | Konu |
|------|------|
| Blok 1 | Temel görüntü işleme uygulamaları |
| Blok 2 | Renk tabanlı nesne tespiti |
| Blok 3 | Kenar tespiti ve kontur analizi |
| Blok 4 | Belge / kağıt tespiti (Document Scanner) |
| Blok 5 | Nesne sayma |
| Blok 6 | Histogram ve kontrast iyileştirme |
| Blok 7 | Video ve kamera uygulamaları |
| Blok 8 | Mini projeler |

**Kod nasıl çalıştırılır:**
```bash
pip install opencv-python numpy matplotlib
python uygulama.py
```

> **Not:** `cv2.imshow()` kullanan örnekler terminal / script ortamı için yazılmıştır. Jupyter kullanıyorsanız `imshow` yerine `plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))` kullanın.

---

## BLOK 1 — Temel Görüntü İşleme Uygulamaları

---

### Uygulama 1.1 — Görüntüyü Yükleme, İnceleme ve Kaydetme

**Problem:**
Bir görüntü dosyasını okumak, temel bilgilerini konsola yazdırmak ve farklı formatta kaydetmek.

**Kullanılan teknikler:** `cv2.imread`, `cv2.imwrite`, `cv2.imshow`

**Pipeline:**
```
Dosya → imread → bilgi yazdır → imwrite → farklı formatta kaydet
```

**Kod:**
```python
import cv2
import numpy as np

# Görüntüyü yükle
img = cv2.imread("foto.jpg")
if img is None:
    raise FileNotFoundError("foto.jpg bulunamadı.")

# Temel bilgileri yazdır
yukseklik, genislik, kanal = img.shape
print(f"Boyut       : {genislik} × {yukseklik} piksel")
print(f"Kanal sayısı: {kanal}")
print(f"Veri tipi   : {img.dtype}")
print(f"Bellek      : {img.nbytes / 1024:.1f} KB")

# Tek piksel değerini oku (sol üst köşe)
bgr = img[0, 0]
print(f"Sol üst köşe (BGR): {bgr}")

# PNG olarak kaydet (kayıpsız)
cv2.imwrite("cikti.png", img)

# JPEG olarak kaydet (90 kalite)
cv2.imwrite("cikti_90.jpg", img, [cv2.IMWRITE_JPEG_QUALITY, 90])

# Göster
cv2.imshow("Orijinal", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`img.shape` üçlü demeti yükseklik-genişlik-kanal sırasıyla döndürür. Genişlik-yükseklik karıştırılmaması için her zaman bu isimleri açıkça atamak iyi alışkanlıktır. `imwrite`'a parametre listesi vererek format kalitesini kontrol edebilirsiniz.

**Beklenen çıktı:**
```
Boyut       : 640 × 480 piksel
Kanal sayısı: 3
Veri tipi   : uint8
Bellek      : 900.0 KB
Sol üst köşe (BGR): [120  95 110]
```

**İyileştirme fikirleri:**
- Birden fazla görüntüyü döngüyle toplu işleme
- Dosya uzantısını otomatik tespit etme
- Görüntü boyutunu standart bir çözünürlüğe normalize etme

---

### Uygulama 1.2 — Görüntü Kanallarını Ayırma ve Karşılaştırma

**Problem:**
Bir renkli görüntünün B, G, R kanallarını ayırarak tek tek incelemek ve her kanalın katkısını görselleştirmek.

**Kullanılan teknikler:** `cv2.split`, `cv2.merge`, `cv2.cvtColor`, `np.hstack`

**Pipeline:**
```
BGR görüntü → split → 3 ayrı kanal → etiketle → yan yana göster
```

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    raise FileNotFoundError("foto.jpg bulunamadı.")

# Kanalları ayır
b, g, r = cv2.split(img)

# Her kanalın ortalamasını yazdır
print(f"B kanalı ortalaması: {b.mean():.1f}")
print(f"G kanalı ortalaması: {g.mean():.1f}")
print(f"R kanalı ortalaması: {r.mean():.1f}")

# Hangi renk hâkim?
ortalamalar = {"Mavi": b.mean(), "Yeşil": g.mean(), "Kırmızı": r.mean()}
hakim = max(ortalamalar, key=ortalamalar.get)
print(f"Hâkim renk: {hakim}")

BOYUT = (300, 250)

def etiketle(gray_img, baslik):
    """Gri görüntüyü 3 kanala çevirip başlık yazar."""
    renkli = cv2.cvtColor(cv2.resize(gray_img, BOYUT), cv2.COLOR_GRAY2BGR)
    cv2.putText(renkli, baslik, (8, 28),
                cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 255), 2)
    return renkli

# Gri tonlamalı da hesapla
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# Orijinali de aynı boyuta getir
orijinal = cv2.resize(img.copy(), BOYUT)
cv2.putText(orijinal, "Orijinal (BGR)", (8, 28),
            cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 255), 2)

# Kanalları ve gri versiyonu yan yana diz
panel = np.hstack([
    orijinal,
    etiketle(b, "Mavi Kanal"),
    etiketle(g, "Yesil Kanal"),
    etiketle(r, "Kirmizi Kanal"),
    etiketle(gray, "Gri")
])

cv2.imshow("Kanal Karsilastirma", panel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`cv2.split` görüntüyü üç ayrı tek kanallı matrise böler. Her kanal o rengin görüntü üzerindeki yoğunluk haritasıdır; parlak alanlar o rengin yoğun olduğu yerleri gösterir. `np.hstack` aynı yükseklikteki görüntüleri yatayda birleştirir.

**Beklenen çıktı:**
Yan yana beş panel: orijinal görüntü ve dört gri tonlamalı kanal haritası. Kırmızı nesne içeren bir görüntüde kırmızı kanal paneli diğerlerinden daha parlak görünür.

**İyileştirme fikirleri:**
- Kanalları renkli göstermek için `np.zeros` ile yapay BGR görüntü oluşturma
- HSV kanallarını da aynı şekilde karşılaştırma

---

### Uygulama 1.3 — Yeniden Boyutlandırma ve Enterpolasyon Karşılaştırması

**Problem:**
Farklı enterpolasyon yöntemlerinin görüntü kalitesine etkisini yan yana karşılaştırmak.

**Kullanılan teknikler:** `cv2.resize`, `INTER_NEAREST`, `INTER_LINEAR`, `INTER_CUBIC`, `INTER_AREA`

**Pipeline:**
```
Orijinal → 4 farklı yöntemle küçült → 4 farklı yöntemle büyüt → karşılaştır
```

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    raise FileNotFoundError("foto.jpg bulunamadı.")

h, w = img.shape[:2]
kucuk_boyut = (w // 4, h // 4)     # ¼ boyutuna küçült
buyuk_boyut = (w * 2,  h * 2)      # 2× büyüt

yontemler = [
    ("NEAREST",  cv2.INTER_NEAREST),
    ("LINEAR",   cv2.INTER_LINEAR),
    ("CUBIC",    cv2.INTER_CUBIC),
    ("AREA",     cv2.INTER_AREA),
]

GOSTER_BOYUT = (300, 225)

def isle_ve_etiketle(img_src, hedef, interpolasyon, baslik):
    sonuc = cv2.resize(img_src, hedef, interpolation=interpolasyon)
    sonuc = cv2.resize(sonuc, GOSTER_BOYUT)   # ekranda aynı boyutta görünmesi için
    cv2.putText(sonuc, baslik, (6, 25),
                cv2.FONT_HERSHEY_SIMPLEX, 0.65, (0, 255, 0), 2)
    return sonuc

# Önce küçültme karşılaştırması
kucultme_panelleri = []
for isim, yontem in yontemler:
    panel = isle_ve_etiketle(img, kucuk_boyut, yontem, f"Kucult: {isim}")
    kucultme_panelleri.append(panel)

# Sonra büyütme karşılaştırması (küçültülmüş görüntüden büyüt → kayıp belirgin olur)
kucuk = cv2.resize(img, kucuk_boyut, interpolation=cv2.INTER_AREA)
buyutme_panelleri = []
for isim, yontem in yontemler:
    panel = isle_ve_etiketle(kucuk, GOSTER_BOYUT, yontem, f"Buyut: {isim}")
    buyutme_panelleri.append(panel)

satir1 = np.hstack(kucultme_panelleri)
satir2 = np.hstack(buyutme_panelleri)
grid   = np.vstack([satir1, satir2])

cv2.imshow("Enterpolasyon Karsilastirmasi", grid)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Küçültme satırında INTER_AREA ile INTER_NEAREST arasındaki fark düz kenarlarda belirgindir: NEAREST blok görünüm üretirken AREA daha yumuşak geçişler sağlar. Büyütme satırında CUBIC en pürüzsüz sonucu verirken NEAREST'in piksel bloklarını açıkça görürsünüz.

**Beklenen çıktı:**
2×4 grid. Küçültme satırında yöntemler arasındaki fark küçüktür; büyütme satırında NEAREST belirgin bloklu görünürken CUBIC çok daha pürüzsüz gözükür.

**İyileştirme fikirleri:**
- Kenar bölgesini ROI ile crop edip 10× büyüterek farkları daha belirgin hale getirme
- Yeniden boyutlandırma sürelerini ölçüp hız/kalite grafiği çıkarma

---

## BLOK 2 — Renk Tabanlı Nesne Tespiti

---

### Uygulama 2.1 — HSV ile Tek Renkli Nesne Maskeleme

**Problem:**
Görüntüdeki belirli bir renkteki nesneyi arka plandan ayırmak ve konumunu bulmak.

**Kullanılan teknikler:** `cv2.cvtColor`, `cv2.inRange`, `cv2.bitwise_and`, `cv2.findContours`, `cv2.boundingRect`

**Pipeline:**
```
BGR → HSV → inRange maskesi → morfoloji temizleme
     → findContours → en büyük kontur → boundingRect → çerçeve çiz
```

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("nesne.jpg")
if img is None:
    raise FileNotFoundError("nesne.jpg bulunamadı.")

# BGR → HSV
hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

# ---- HSV aralıklarını buradan ayarlayın ----
# Turuncu nesne için örnek değerler
# HSV Hue tablosu: Kırmızı≈0/170, Sarı≈25, Yeşil≈60, Mavi≈110, Mor≈140
lower = np.array([10,  100, 80])
upper = np.array([25,  255, 255])
# --------------------------------------------

# Renk maskesi oluştur
mask = cv2.inRange(hsv, lower, upper)

# Küçük gürültüleri temizle
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (7, 7))
mask   = cv2.morphologyEx(mask, cv2.MORPH_OPEN,  kernel, iterations=2)
mask   = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, kernel, iterations=2)

# Maskeyi orijinal görüntüye uygula
sonuc = cv2.bitwise_and(img, img, mask=mask)

# Konturları bul ve en büyüğünü seç
konturlar, _ = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)

gorsel = img.copy()
if konturlar:
    en_buyuk = max(konturlar, key=cv2.contourArea)
    alan     = cv2.contourArea(en_buyuk)

    if alan > 500:   # çok küçük tespitleri filtrele
        x, y, w, h = cv2.boundingRect(en_buyuk)
        cv2.drawContours(gorsel, [en_buyuk], -1, (0, 255, 0), 2)
        cv2.rectangle(gorsel,   (x, y),    (x+w, y+h), (255, 0, 0), 2)
        cv2.putText(gorsel, f"Alan: {alan:.0f}px", (x, y - 10),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.7, (255, 0, 0), 2)
        print(f"Nesne konumu — x:{x} y:{y}  boyut:{w}×{h}  alan:{alan:.0f}px")
    else:
        print("Yeterince büyük nesne bulunamadı.")
else:
    print("Nesne bulunamadı.")

# Sonuçları göster
cv2.imshow("Orijinal",  img)
cv2.imshow("Maske",     mask)
cv2.imshow("Maskelenmis", sonuc)
cv2.imshow("Tespit",    gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
HSV renk uzayında yalnızca Hue (H) kanalı rengi temsil eder; aydınlatma V kanalındadır. Bu sayede farklı ışık koşullarında da aynı H aralığı aynı rengi yakalar. Morfoloji adımı maskeden küçük gürültü noktalarını siler ve varsa delikleri kapatır.

**Beklenen çıktı:**
Dört pencere: orijinal görüntü, ikili maske (nesne beyaz), maskelenmiş görüntü (yalnızca renkli nesne görünür) ve üzerine yeşil kontur ile mavi bounding box çizilmiş tespit sonucu.

**İyileştirme fikirleri:**
- HSV değerlerini trackbar ile gerçek zamanlı ayarlamak (bkz. Uygulama 2.2)
- Birden fazla renk aralığını `cv2.bitwise_or` ile birleştirme
- Her konturun alanını küçükten büyüğe sıralayıp hepsini çerçeveleme

---

### Uygulama 2.2 — Trackbar ile İnteraktif HSV Ayarı

**Problem:**
Farklı renk aralıklarını denemek için HSV değerlerini kaydıraçlarla (trackbar) gerçek zamanlı ayarlamak.

**Kullanılan teknikler:** `cv2.createTrackbar`, `cv2.getTrackbarPos`, `cv2.inRange`

**Pipeline:**
```
Kamera / görüntü → HSV dönüşümü → trackbar değerlerini oku
                → inRange maskesi → göster (döngü)
```

**Kod:**
```python
import cv2
import numpy as np

def bos_callback(x):
    pass

img = cv2.imread("nesne.jpg")
if img is None:
    raise FileNotFoundError("nesne.jpg bulunamadı.")

hsv = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)

# Kontrol paneli penceresi
cv2.namedWindow("HSV Ayar Paneli")
cv2.createTrackbar("H Alt",  "HSV Ayar Paneli",   0, 179, bos_callback)
cv2.createTrackbar("H Ust",  "HSV Ayar Paneli", 179, 179, bos_callback)
cv2.createTrackbar("S Alt",  "HSV Ayar Paneli",  50, 255, bos_callback)
cv2.createTrackbar("S Ust",  "HSV Ayar Paneli", 255, 255, bos_callback)
cv2.createTrackbar("V Alt",  "HSV Ayar Paneli",  50, 255, bos_callback)
cv2.createTrackbar("V Ust",  "HSV Ayar Paneli", 255, 255, bos_callback)

while True:
    # Trackbar değerlerini oku
    h_alt = cv2.getTrackbarPos("H Alt", "HSV Ayar Paneli")
    h_ust = cv2.getTrackbarPos("H Ust", "HSV Ayar Paneli")
    s_alt = cv2.getTrackbarPos("S Alt", "HSV Ayar Paneli")
    s_ust = cv2.getTrackbarPos("S Ust", "HSV Ayar Paneli")
    v_alt = cv2.getTrackbarPos("V Alt", "HSV Ayar Paneli")
    v_ust = cv2.getTrackbarPos("V Ust", "HSV Ayar Paneli")

    lower = np.array([h_alt, s_alt, v_alt])
    upper = np.array([h_ust, s_ust, v_ust])

    mask  = cv2.inRange(hsv, lower, upper)
    sonuc = cv2.bitwise_and(img, img, mask=mask)

    cv2.imshow("Maske",    mask)
    cv2.imshow("Sonuc",    sonuc)
    cv2.imshow("Orijinal", img)

    # 'p' tuşuna basınca değerleri yazdır
    key = cv2.waitKey(30) & 0xFF
    if key == ord('p'):
        print(f"lower = np.array([{h_alt}, {s_alt}, {v_alt}])")
        print(f"upper = np.array([{h_ust}, {s_ust}, {v_ust}])")
    elif key == ord('q'):
        break

cv2.destroyAllWindows()
```

**Açıklama:**
Bu araç, bir renk için doğru HSV aralığını bulmayı kolaylaştırır. Kaydıraçları ayarlayarak aranan rengi beyaz olarak göreceksiniz; 'p' tuşuna basınca konsola kopyalayabileceğiniz aralık değerleri yazdırılır. Gerçek projelerde bu araçla bulunan değerler koda sabit olarak yazılır.

**Beklenen çıktı:**
Üç pencere: orijinal, anlık maske, maskelenmiş sonuç. Trackbar kaydırıldıkça maskeler gerçek zamanlı güncellenir.

**İyileştirme fikirleri:**
- `cap = cv2.VideoCapture(0)` ekleyerek canlı kamera görüntüsünde çalıştırma
- Bulunan değerleri JSON dosyasına kaydetme

---

## BLOK 3 — Kenar Tespiti ve Kontur Analizi

---

### Uygulama 3.1 — Canny Kenar Tespiti Pipeline'ı

**Problem:**
Bir görüntüde Gaussian bulanıklaştırmadan Canny'ye uzanan tam pipeline'ı adım adım uygulamak ve her adımın etkisini karşılaştırmak.

**Kullanılan teknikler:** `cv2.GaussianBlur`, `cv2.Sobel`, `cv2.Canny`, `cv2.threshold`

**Pipeline:**
```
BGR → gri → Gaussian blur → Sobel büyüklük → Canny (sabit) → Canny (Otsu)
```

**Kod:**
```python
import cv2
import numpy as np

img = cv2.imread("foto.jpg")
if img is None:
    raise FileNotFoundError("foto.jpg bulunamadı.")

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur = cv2.GaussianBlur(gray, (5, 5), 0)

# Sobel büyüklük haritası
sx = cv2.Sobel(blur, cv2.CV_64F, 1, 0, ksize=3)
sy = cv2.Sobel(blur, cv2.CV_64F, 0, 1, ksize=3)
sobel_mag = cv2.convertScaleAbs(cv2.magnitude(sx, sy))

# Canny — sabit eşikler
canny_sabit = cv2.Canny(blur, 50, 150)

# Canny — Otsu eşiğinden otomatik türet
otsu_val, _ = cv2.threshold(blur, 0, 255, cv2.THRESH_OTSU)
canny_otsu  = cv2.Canny(blur, otsu_val * 0.5, otsu_val)
print(f"Otsu eşiği: {otsu_val:.0f}  →  Canny: [{otsu_val*0.5:.0f}, {otsu_val:.0f}]")

BOYUT = (320, 240)

def hazirla(g, baslik):
    g = cv2.resize(g, BOYUT)
    if len(g.shape) == 2:
        g = cv2.cvtColor(g, cv2.COLOR_GRAY2BGR)
    cv2.putText(g, baslik, (6, 24), cv2.FONT_HERSHEY_SIMPLEX,
                0.65, (0, 255, 255), 2)
    return g

orijinal_panel = cv2.resize(img.copy(), BOYUT)
cv2.putText(orijinal_panel, "Orijinal", (6, 24),
            cv2.FONT_HERSHEY_SIMPLEX, 0.65, (0, 255, 255), 2)

panel = np.hstack([
    orijinal_panel,
    hazirla(blur,        "Gaussian Blur"),
    hazirla(sobel_mag,   "Sobel Buyukluk"),
    hazirla(canny_sabit, "Canny (50/150)"),
    hazirla(canny_otsu,  f"Canny (Otsu={otsu_val:.0f})"),
])

cv2.imshow("Kenar Tespiti Pipeline", panel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Sobel yalnızca gradyan büyüklüğünü hesaplar; kenarlar geniş ve kalındır. Canny, Sobel'in üzerine non-maximum suppression ve histerezis ekleyerek kenarları 1 piksel kalınlığına indirir ve yalnızca gerçek kenarları bağlı çizgiler olarak döndürür. Otsu tabanlı eşik seçimi, her görüntüye otomatik uyum sağlar.

**Beklenen çıktı:**
Beş panelden oluşan yatay şerit. Soldan sağa Sobel bulanık kenarlardan Canny'nin ince ve bağlantılı kenarlarına geçişi görürsünüz.

**İyileştirme fikirleri:**
- Kernel boyutunu (3/5/7) değiştirerek Sobel kalitesini karşılaştırma
- `apertureSize=5` ile Canny'nin daha ince kenarlar bulmasını gözlemleme

---

### Uygulama 3.2 — Şekil Tanıma

**Problem:**
Görüntüdeki geometrik şekilleri (daire, kare, dikdörtgen, üçgen) otomatik olarak tanımak ve etiketlemek.

**Kullanılan teknikler:** `cv2.threshold`, `cv2.findContours`, `cv2.approxPolyDP`, `cv2.contourArea`, `cv2.moments`

**Pipeline:**
```
BGR → gri → Gaussian blur → Otsu eşik → opening
   → findContours → approxPolyDP → köşe sayısı + dairesellik → etiket
```

**Kod:**
```python
import cv2
import numpy as np

# Test görüntüsü oluştur (elinizde uygun dosya varsa imread kullanın)
canvas = np.zeros((500, 800, 3), dtype=np.uint8)
cv2.circle(canvas,    (120, 250), 90,  (255, 255, 255), -1)
cv2.rectangle(canvas, (260, 160), (420, 320), (255, 255, 255), -1)
cv2.rectangle(canvas, (460, 195), (730, 295), (255, 255, 255), -1)
pts = np.array([[620, 350], [720, 490], [520, 490]], np.int32)
cv2.fillPoly(canvas, [pts], (255, 255, 255))
img = canvas

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur = cv2.GaussianBlur(gray, (7, 7), 0)
_, binary = cv2.threshold(blur, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
binary = cv2.morphologyEx(binary, cv2.MORPH_OPEN, kernel)

konturlar, _ = cv2.findContours(binary.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)
gorsel = img.copy()

for cnt in konturlar:
    alan = cv2.contourArea(cnt)
    if alan < 400:
        continue

    cevre       = cv2.arcLength(cnt, True)
    yaklasik    = cv2.approxPolyDP(cnt, 0.03 * cevre, True)
    kose        = len(yaklasik)
    dairesellik = (4 * np.pi * alan / cevre ** 2) if cevre > 0 else 0
    x, y, w, h  = cv2.boundingRect(cnt)
    oran        = w / h if h > 0 else 0

    # Şekil adı belirle
    if kose == 3:
        sekil = "Ucgen"
        renk  = (0, 255, 255)
    elif kose == 4:
        sekil = "Kare" if 0.9 <= oran <= 1.1 else "Dikdortgen"
        renk  = (255, 165, 0)
    elif dairesellik > 0.80:
        sekil = "Daire"
        renk  = (0, 255, 0)
    else:
        sekil = f"{kose}-kose"
        renk  = (200, 200, 200)

    cv2.drawContours(gorsel, [cnt], -1, renk, 2)
    cv2.putText(gorsel, sekil,           (x, y - 10),
                cv2.FONT_HERSHEY_SIMPLEX, 0.7, renk, 2)
    cv2.putText(gorsel, f"A:{alan:.0f}", (x, y + h + 20),
                cv2.FONT_HERSHEY_SIMPLEX, 0.5, renk, 1)

    M  = cv2.moments(cnt)
    if M["m00"] > 0:
        cx = int(M["m10"] / M["m00"])
        cy = int(M["m01"] / M["m00"])
        cv2.circle(gorsel, (cx, cy), 4, (0, 0, 255), -1)

    print(f"{sekil:15s} | köşe:{kose:2d} | dairesellik:{dairesellik:.3f} "
          f"| oran:{oran:.2f} | alan:{alan:.0f}")

cv2.imshow("Sekil Tanima", gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`approxPolyDP` bir konturu köşelere dönüştürür; `epsilon` değeri toleransı belirler. Dairesellik formülü `4π × alan / çevre²` ile daireden ne kadar uzaklaşıldığı ölçülür; 1.0 mükemmel daire demektir. Kare–dikdörtgen ayrımı için en-boy oranı kullanılır.

**Beklenen çıktı:**
```
Daire           | köşe: 8 | dairesellik:0.994 | oran:1.01 | alan:25413
Kare            | köşe: 4 | dairesellik:0.785 | oran:0.97 | alan:25600
Dikdortgen      | köşe: 4 | dairesellik:0.637 | oran:2.44 | alan:27000
Ucgen           | köşe: 3 | dairesellik:0.612 | oran:1.82 | alan:10200
```

**İyileştirme fikirleri:**
- `epsilon` değerini değiştirerek (0.01–0.05) köşe tespitinin hassasiyetini gözlemlemek
- Renk bilgisini de ekleyerek "Kırmızı Daire" gibi bileşik etiketler üretmek

---

## BLOK 4 — Belge / Kağıt Tespiti (Document Scanner)

---

### Uygulama 4.1 — Otomatik Belge Tarama

**Problem:**
Fotoğraflanmış eğik veya yamuk bir kağıdı veya belgeyi otomatik tespit edip perspektif düzeltmesi yaparak düz bir dikdörtgene dönüştürmek.

**Kullanılan teknikler:** `cv2.Canny`, `cv2.findContours`, `cv2.approxPolyDP`, `cv2.getPerspectiveTransform`, `cv2.warpPerspective`, `cv2.createCLAHE`

**Pipeline:**
```
BGR → gri → Gaussian → Canny → dilate → findContours (büyükten küçüğe)
   → 4 köşeli kontur bul → köşeleri sırala
   → getPerspectiveTransform → warpPerspective → CLAHE
```

**Kod:**
```python
import cv2
import numpy as np

def koseler_sirala(pts):
    """Dört noktayı sol-üst, sağ-üst, sağ-alt, sol-alt sırasına koy."""
    pts    = pts.reshape(4, 2).astype(np.float32)
    sirali = np.zeros((4, 2), dtype=np.float32)
    toplam = pts.sum(axis=1)
    fark   = np.diff(pts, axis=1).flatten()
    sirali[0] = pts[np.argmin(toplam)]   # sol üst  → x+y en küçük
    sirali[2] = pts[np.argmax(toplam)]   # sağ alt  → x+y en büyük
    sirali[1] = pts[np.argmin(fark)]     # sağ üst  → y-x en küçük
    sirali[3] = pts[np.argmax(fark)]     # sol alt  → y-x en büyük
    return sirali

def belge_genislik_yukseklik(pts):
    """Düzeltilmiş görüntünün boyutunu köşe uzaklıklarından hesapla."""
    (tl, tr, br, bl) = pts
    ust_genislik  = np.linalg.norm(tr - tl)
    alt_genislik  = np.linalg.norm(br - bl)
    sol_yukseklik = np.linalg.norm(bl - tl)
    sag_yukseklik = np.linalg.norm(br - tr)
    w = int(max(ust_genislik, alt_genislik))
    h = int(max(sol_yukseklik, sag_yukseklik))
    return w, h

img = cv2.imread("belge.jpg")
if img is None:
    # Test için yapay belge görüntüsü oluştur
    img = np.ones((600, 800, 3), dtype=np.uint8) * 200
    pts_belge = np.array([[80, 50], [700, 30], [740, 560], [60, 580]])
    cv2.fillPoly(img, [pts_belge], (240, 240, 230))
    cv2.putText(img, "BELGE ICERIGI", (200, 300),
                cv2.FONT_HERSHEY_SIMPLEX, 1.5, (50, 50, 50), 3)

orijinal = img.copy()
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur = cv2.GaussianBlur(gray, (5, 5), 0)

# Kenarları bul
edges = cv2.Canny(blur, 50, 200)
kernel = np.ones((3, 3), np.uint8)
edges  = cv2.dilate(edges, kernel, iterations=1)  # kopuk kenarları birleştir

# Konturları büyükten küçüğe sırala
konturlar, _ = cv2.findContours(edges.copy(), cv2.RETR_LIST,
                                 cv2.CHAIN_APPROX_SIMPLE)
konturlar     = sorted(konturlar, key=cv2.contourArea, reverse=True)[:10]

belge_cnt = None
for cnt in konturlar:
    cevre   = cv2.arcLength(cnt, True)
    yaklasik = cv2.approxPolyDP(cnt, 0.02 * cevre, True)
    if len(yaklasik) == 4 and cv2.contourArea(cnt) > 5000:
        belge_cnt = yaklasik
        break

if belge_cnt is None:
    print("Belge köşeleri bulunamadı.")
else:
    pts  = koseler_sirala(belge_cnt)
    w, h = belge_genislik_yukseklik(pts)

    dst_pts = np.float32([[0, 0], [w, 0], [w, h], [0, h]])
    M       = cv2.getPerspectiveTransform(pts, dst_pts)
    taranmis = cv2.warpPerspective(img, M, (w, h))

    # Kontrast iyileştir
    gray_t = cv2.cvtColor(taranmis, cv2.COLOR_BGR2GRAY)
    clahe  = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
    temiz  = clahe.apply(gray_t)

    # Konturları orijinal üzerine çiz
    cv2.drawContours(orijinal, [belge_cnt], -1, (0, 255, 0), 3)
    for nokta in belge_cnt.reshape(4, 2):
        cv2.circle(orijinal, tuple(nokta.astype(int)), 8, (0, 0, 255), -1)

    cv2.imshow("Orijinal + Koseler", orijinal)
    cv2.imshow("Perspektif Duzeltilmis", taranmis)
    cv2.imshow("Temizlenmis Belge", temiz)
    cv2.imwrite("taranmis_belge.jpg", temiz)
    print(f"Belge tarandı → {w}×{h} piksel")

cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Konturlar büyükten küçüğe sıralanır; 4 köşesi olan en büyük kontur büyük olasılıkla kâğıdın kenarıdır. `koseler_sirala` fonksiyonu dört köşeyi TL-TR-BR-BL düzenine sokar; bu sıralama `getPerspectiveTransform` için zorunludur. CLAHE sonunda metni arka plandan keskinleştirir.

**Beklenen çıktı:**
Üç pencere: köşeleri işaretlenmiş orijinal, perspektif düzeltilmiş renkli belge ve kontrast artırılmış gri temiz çıktı. `taranmis_belge.jpg` diske kaydedilir.

**İyileştirme fikirleri:**
- Adaptif eşikleme ekleyerek metni siyah-beyaza dönüştürme (OCR hazırlığı)
- Bulunan belgeyi otomatik A4 en-boy oranına kırpma
- `cv2.VideoCapture` ile gerçek zamanlı tarama uygulamasına dönüştürme

---

## BLOK 5 — Nesne Sayma

---

### Uygulama 5.1 — Morfoloji ile Nesne Sayma ve Özellik Tablosu

**Problem:**
Görüntüdeki ayrı nesnelerin sayısını bulmak ve her nesne için alan, çevre ve dairesellik değerlerini tablo olarak çıkarmak.

**Kullanılan teknikler:** `cv2.threshold`, `cv2.morphologyEx`, `cv2.findContours`, `cv2.contourArea`, `cv2.arcLength`, `cv2.boundingRect`

**Pipeline:**
```
BGR → gri → Otsu eşik → opening → closing
   → findContours → alan filtresi → her nesneyi analiz et → tablo yazdır
```

**Kod:**
```python
import cv2
import numpy as np

# Test görüntüsü (farklı boyut ve şekillerde nesneler)
canvas = np.zeros((500, 900, 3), dtype=np.uint8)
cv2.circle(canvas,    (100, 150), 70,  (255, 255, 255), -1)
cv2.circle(canvas,    (300, 150), 40,  (255, 255, 255), -1)
cv2.rectangle(canvas, (420, 90), (600, 210), (255, 255, 255), -1)
cv2.rectangle(canvas, (650, 100),(870, 195), (255, 255, 255), -1)
cv2.circle(canvas,    (180, 370), 100, (255, 255, 255), -1)
pts = np.array([[500, 280], [620, 450], [380, 450]], np.int32)
cv2.fillPoly(canvas, [pts], (255, 255, 255))
img = canvas

# Ön işleme
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur = cv2.GaussianBlur(gray, (7, 7), 0)
_, binary = cv2.threshold(blur, 0, 255, cv2.THRESH_BINARY + cv2.THRESH_OTSU)

kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
binary = cv2.morphologyEx(binary, cv2.MORPH_OPEN,  kernel, iterations=2)
binary = cv2.morphologyEx(binary, cv2.MORPH_CLOSE, kernel, iterations=2)

# Kontur tespiti
konturlar, _ = cv2.findContours(binary.copy(), cv2.RETR_EXTERNAL,
                                 cv2.CHAIN_APPROX_SIMPLE)

MIN_ALAN = 500
gorsel   = img.copy()
sayac    = 0
tablo    = []

print(f"\n{'ID':>4} {'Alan':>8} {'Cevre':>8} {'Dairesellik':>12} {'W×H':>10}")
print("-" * 50)

for cnt in sorted(konturlar, key=cv2.contourArea, reverse=True):
    alan = cv2.contourArea(cnt)
    if alan < MIN_ALAN:
        continue
    sayac += 1

    cevre       = cv2.arcLength(cnt, True)
    dairesellik = (4 * np.pi * alan / cevre ** 2) if cevre > 0 else 0
    x, y, w, h  = cv2.boundingRect(cnt)

    tablo.append({"id": sayac, "alan": alan, "cevre": cevre,
                  "dairesellik": dairesellik, "w": w, "h": h})

    # Görsele ID ve bounding box ekle
    cv2.drawContours(gorsel, [cnt], -1, (0, 255, 0), 2)
    cv2.rectangle(gorsel, (x, y), (x+w, y+h), (255, 165, 0), 1)
    cv2.putText(gorsel, f"#{sayac}", (x, y - 6),
                cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 0, 255), 2)

    print(f"{sayac:>4} {alan:>8.0f} {cevre:>8.1f} {dairesellik:>12.3f} "
          f"  {w}×{h}")

print("-" * 50)
print(f"Toplam nesne: {sayac}")

# Toplam nesne sayısını görüntüye yaz
cv2.putText(gorsel, f"Toplam: {sayac}", (10, 35),
            cv2.FONT_HERSHEY_SIMPLEX, 1.0, (0, 0, 255), 2)

cv2.imshow("Nesne Sayma", gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Opening işlemi küçük gürültü noktalarını silerken büyük nesneleri korur. Closing ise nesne içindeki boşlukları doldurar. Konturlar alana göre büyükten küçüğe sıralanarak en büyük nesneler önce işlenir. Her nesne için dairesellik değeri şekil hakkında ek bilgi sağlar.

**Beklenen çıktı:**
```
  ID     Alan    Cevre  Dairesellik        W×H
--------------------------------------------------
   1  31416.0    628.3        1.000    140×140
   2  25600.0    640.0        0.785    180×120
   ...
Toplam nesne: 6
```

**İyileştirme fikirleri:**
- Sonuçları CSV dosyasına yazmak için `pandas` kullanımı
- Her nesneyi ayrı dosya olarak kaydetme (crop)
- İki görüntüdeki nesne sayısını karşılaştırıp farkı raporlama

---

## BLOK 6 — Histogram ve Kontrast İyileştirme

---

### Uygulama 6.1 — Histogram Analizi ve Karşılaştırma

**Problem:**
Bir görüntünün piksel dağılımını histogram ile görselleştirmek ve düşük kontrastlı görüntüleri tespit etmek.

**Kullanılan teknikler:** `cv2.calcHist`, `cv2.equalizeHist`, `cv2.createCLAHE`

**Pipeline:**
```
Görüntü → gri → histogram hesapla → dağılımı analiz et
       → equalizeHist → CLAHE → histogramları karşılaştır
```

**Kod:**
```python
import cv2
import numpy as np
import matplotlib.pyplot as plt

img = cv2.imread("dusuk_kontrast.jpg")
if img is None:
    # Yapay düşük kontrastlı görüntü
    img = np.random.randint(80, 160, (300, 400, 3), dtype=np.uint8)

gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

# Kontrast kalitesini ölç
std  = gray.std()
ort  = gray.mean()
print(f"Ortalama parlaklık : {ort:.1f}")
print(f"Standart sapma     : {std:.1f}")
print(f"Değer aralığı      : {gray.min()} – {gray.max()}")
print(f"Kontrast değerlendirme: ", end="")
if std < 30:
    print("Düşük kontrast (std < 30)")
elif std < 60:
    print("Orta kontrast")
else:
    print("İyi kontrast")

# Kontrast iyileştirme
esit   = cv2.equalizeHist(gray)
clahe  = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
clahe_sonuc = clahe.apply(gray)

# Histogram hesapla
hist_orijinal = cv2.calcHist([gray],         [0], None, [256], [0, 256])
hist_esit     = cv2.calcHist([esit],         [0], None, [256], [0, 256])
hist_clahe    = cv2.calcHist([clahe_sonuc],  [0], None, [256], [0, 256])

# Görselleştir
fig, eksenler = plt.subplots(2, 3, figsize=(14, 7))
fig.suptitle("Histogram Karsilastirmasi", fontsize=14)

eksenler[0][0].imshow(gray,        cmap='gray'); eksenler[0][0].set_title("Orijinal")
eksenler[0][1].imshow(esit,        cmap='gray'); eksenler[0][1].set_title("equalizeHist")
eksenler[0][2].imshow(clahe_sonuc, cmap='gray'); eksenler[0][2].set_title("CLAHE")

eksenler[1][0].plot(hist_orijinal, color='gray');   eksenler[1][0].set_title("Histogram: Orijinal")
eksenler[1][1].plot(hist_esit,     color='blue');   eksenler[1][1].set_title("Histogram: equalizeHist")
eksenler[1][2].plot(hist_clahe,    color='green');  eksenler[1][2].set_title("Histogram: CLAHE")

for ax in eksenler.flat:
    ax.axis('off') if ax in eksenler[0] else None

plt.tight_layout()
plt.show()

# Renkli görüntüde CLAHE — yalnızca L kanalına uygula
lab    = cv2.cvtColor(img, cv2.COLOR_BGR2LAB)
l, a, b_ch = cv2.split(lab)
l_clahe     = clahe.apply(l)
img_clahe   = cv2.cvtColor(cv2.merge([l_clahe, a, b_ch]), cv2.COLOR_LAB2BGR)

cv2.imshow("Renkli CLAHE", img_clahe)
cv2.imshow("Renkli Orijinal", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
`equalizeHist` histogramı düzleştirir ama tüm görüntüyü tek eşik ile işlediğinden bazı alanlar aşırı parlayabilir. CLAHE görüntüyü küçük kutucuklara bölüp her bölgede ayrı histogram eşitlemesi yaptığından çok daha doğal sonuç verir. Renkli görüntülerde CLAHE yalnızca LAB renk uzayının L (parlaklık) kanalına uygulanır; böylece renkler bozulmaz.

**Beklenen çıktı:**
Matplotlib penceresinde 2×3 grid: üst satır görüntüler, alt satır histogramlar. equalizeHist histogramı geniş yayarken CLAHE'nin histogramı daha dengeli görünür.

**İyileştirme fikirleri:**
- `clipLimit` değerini (1.0–4.0) değiştirerek CLAHE yoğunluğunu ayarlama
- Gece çekilmiş görüntülerde parlaklık artırma pipeline'ı kurma

---

## BLOK 7 — Video ve Kamera Uygulamaları

---

### Uygulama 7.1 — Gerçek Zamanlı Kenar Tespiti

**Problem:**
Kamera görüntüsü üzerinde gerçek zamanlı Canny kenar tespiti uygulamak; kullanıcının tuş ile orijinal ve kenar görüntüleri arasında geçiş yapabilmesi.

**Kullanılan teknikler:** `cv2.VideoCapture`, `cv2.cvtColor`, `cv2.GaussianBlur`, `cv2.Canny`

**Pipeline:**
```
Kamera → her kare → gri → blur → Canny → imshow (döngü)
```

**Kod:**
```python
import cv2

cap = cv2.VideoCapture(0)   # 0 = varsayılan kamera
if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

mod = "kenar"   # başlangıç modu

print("Kontroller: [o] Orijinal  [k] Kenar  [g] Gri  [q] Çıkış")

try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        blur = cv2.GaussianBlur(gray, (5, 5), 0)

        # Otsu tabanlı Canny
        otsu, _ = cv2.threshold(blur, 0, 255, cv2.THRESH_OTSU)
        kenar   = cv2.Canny(blur, otsu * 0.4, otsu)

        # Mod seçimi
        if   mod == "orijinal": gosterim = frame
        elif mod == "gri":      gosterim = cv2.cvtColor(gray, cv2.COLOR_GRAY2BGR)
        else:                   gosterim = cv2.cvtColor(kenar, cv2.COLOR_GRAY2BGR)

        # Bilgi çubuğu
        cv2.putText(gosterim, f"Mod: {mod.upper()}  [o/k/g/q]",
                    (10, 28), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 0), 2)

        cv2.imshow("Gercek Zamanli Kenar Tespiti", gosterim)

        key = cv2.waitKey(1) & 0xFF
        if   key == ord('q'): break
        elif key == ord('o'): mod = "orijinal"
        elif key == ord('k'): mod = "kenar"
        elif key == ord('g'): mod = "gri"
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Açıklama:**
Otsu eşiği her karede yeniden hesaplanır; bu sayede aydınlatma değişimlerine otomatik uyum sağlar. `waitKey(1)` döngünün ~1000 FPS'e kadar çalışmasına izin verir; görüntü işleme süresi pratikte FPS'i sınırlar. `try-finally` bloğu `q` tuşuna basmadan program kapanırsa dahi kamerayı serbest bırakır.

**Beklenen çıktı:**
Gerçek zamanlı pencere. `k` tuşunda Canny kenar görüntüsü, `o` tuşunda orijinal görüntü, `g` tuşunda gri görüntü gösterilir.

**İyileştirme fikirleri:**
- `VideoWriter` ekleyerek işlenmiş videoyu kaydetme
- Trackbar ile eşik değerlerini gerçek zamanlı ayarlama
- FPS sayacı eklemek için `time.perf_counter()` kullanımı

---

### Uygulama 7.2 — MOG2 ile Hareket Tespiti

**Problem:**
Sabit kamera görüntüsünde hareketli nesneleri tespit etmek, konumlarını işaretlemek ve hareket olduğunda uyarı vermek.

**Kullanılan teknikler:** `cv2.createBackgroundSubtractorMOG2`, `cv2.morphologyEx`, `cv2.findContours`, `cv2.boundingRect`

**Pipeline:**
```
Kamera → MOG2 arka plan çıkarma → threshold (gölgeleri temizle)
       → opening (gürültü sil) → findContours → büyük konturları çerçevele
```

**Kod:**
```python
import cv2
import numpy as np

cap = cv2.VideoCapture(0)
if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

# MOG2 — 300 kare geçmiş, gölge tespiti açık
mog2   = cv2.createBackgroundSubtractorMOG2(
    history=300, varThreshold=25, detectShadows=True
)
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))

MIN_HAREKET_ALANI = 1500   # piksel² — küçük gürültüleri yoksay

print("Kameraya bir süre hareketsiz bakın — arka plan öğreniliyor...")

try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        # Arka plan çıkarma
        fg_mask = mog2.apply(frame)

        # Gölgeleri (127) temizle → yalnızca gerçek ön plan (255) kalsın
        _, fg_binary = cv2.threshold(fg_mask, 200, 255, cv2.THRESH_BINARY)

        # Gürültü temizleme ve delikleri kapatma
        fg_temiz = cv2.morphologyEx(fg_binary, cv2.MORPH_OPEN,  kernel, iterations=2)
        fg_temiz = cv2.morphologyEx(fg_temiz,  cv2.MORPH_CLOSE, kernel, iterations=2)

        # Hareketli nesneleri bul
        konturlar, _ = cv2.findContours(fg_temiz.copy(), cv2.RETR_EXTERNAL,
                                         cv2.CHAIN_APPROX_SIMPLE)
        gorsel   = frame.copy()
        hareket  = False

        for cnt in konturlar:
            if cv2.contourArea(cnt) < MIN_HAREKET_ALANI:
                continue
            hareket = True
            x, y, w, h = cv2.boundingRect(cnt)
            cv2.rectangle(gorsel, (x, y), (x+w, y+h), (0, 0, 255), 2)
            cv2.putText(gorsel, "HAREKET", (x, y - 8),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 0, 255), 2)

        # Durum çubuğu
        durum_metni = "HAREKET ALGILANDI!" if hareket else "Normal"
        durum_renk  = (0, 0, 255) if hareket else (0, 200, 0)
        cv2.putText(gorsel, durum_metni, (10, 30),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.9, durum_renk, 2)

        cv2.imshow("Kamera",       gorsel)
        cv2.imshow("On Plan Mask", fg_temiz)

        if cv2.waitKey(30) & 0xFF == ord('q'):
            break
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Açıklama:**
MOG2 ilk karelerde arka planı öğrenir; bu nedenle başlangıçta kamera karşısında hareketsiz durmak veya birkaç saniye beklemek daha temiz sonuç verir. `detectShadows=True` gölgeleri 127 değeriyle işaretler; threshold adımı bunları eleyerek yalnızca gerçek ön plan piksellerini (255) bırakır. Opening gürültü noktalarını temizler, Closing nesne içindeki boşlukları kapatır.

**Beklenen çıktı:**
İki pencere: kamera görüntüsü (hareketli nesneler kırmızı kutularla işaretli) ve ön plan maskesi (hareketli bölgeler beyaz). Hareket algılandığında durum çubuğunda "HAREKET ALGILANDI!" yazar.

**İyileştirme fikirleri:**
- Hareket tespit edildiğinde `cv2.imwrite` ile anlık fotoğraf kaydetme
- `history` değerini artırarak arka plan modelinin yavaş değişen ışık koşullarına uyumunu gözlemlemek
- Her nesnenin merkez koordinatını takip ederek basit yörünge çizimi

---

### Uygulama 7.3 — Lucas-Kanade ile Nokta Takibi

**Problem:**
Seçilen anahtar noktaları kareden kareye optik akış ile takip etmek ve yörüngelerini görselleştirmek.

**Kullanılan teknikler:** `cv2.goodFeaturesToTrack`, `cv2.calcOpticalFlowPyrLK`

**Pipeline:**
```
İlk kare → goodFeaturesToTrack → anahtar noktalar
Sonraki kare → calcOpticalFlowPyrLK → yeni konumlar → yörünge çiz (döngü)
```

**Kod:**
```python
import cv2
import numpy as np
import random

cap = cv2.VideoCapture(0)
if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

LK_PARAMS = dict(winSize=(15, 15), maxLevel=2,
                 criteria=(cv2.TERM_CRITERIA_EPS | cv2.TERM_CRITERIA_COUNT, 10, 0.03))

OZELLIK_PARAMS = dict(maxCorners=80, qualityLevel=0.3,
                      minDistance=10, blockSize=7)

ret, ilk_kare = cap.read()
if not ret:
    raise RuntimeError("İlk kare okunamadı.")

onceki_gray = cv2.cvtColor(ilk_kare, cv2.COLOR_BGR2GRAY)
noktalar    = cv2.goodFeaturesToTrack(onceki_gray, **OZELLIK_PARAMS)

# Her noktaya rastgele renk ata
renkler = [tuple(random.randint(0, 255) for _ in range(3)) for _ in range(200)]
iz_tuvali = np.zeros_like(ilk_kare)   # hareket izleri için ayrı katman

print(f"{len(noktalar)} anahtar nokta tespit edildi.")
print("'r' ile noktaları sıfırla, 'q' ile çıkış")

try:
    while cap.isOpened():
        ret, kare = cap.read()
        if not ret:
            break

        simdiki_gray = cv2.cvtColor(kare, cv2.COLOR_BGR2GRAY)

        if noktalar is not None and len(noktalar) > 0:
            # Noktaları takip et
            yeni_noktalar, durum, _ = cv2.calcOpticalFlowPyrLK(
                onceki_gray, simdiki_gray, noktalar, None, **LK_PARAMS
            )

            iyi_yeni = yeni_noktalar[durum == 1]
            iyi_eski = noktalar[durum == 1]

            # Hareket izleri çiz
            for i, (yeni, eski) in enumerate(zip(iyi_yeni, iyi_eski)):
                a, b = yeni.ravel().astype(int)
                c, d = eski.ravel().astype(int)
                renk = renkler[i % len(renkler)]
                cv2.line(iz_tuvali, (a, b), (c, d), renk, 2)
                cv2.circle(kare, (a, b), 3, renk, -1)

            gorsel = cv2.add(kare, iz_tuvali)
            cv2.putText(gorsel, f"Takip edilen: {len(iyi_yeni)}", (10, 30),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 0), 2)

            noktalar = iyi_yeni.reshape(-1, 1, 2)
        else:
            gorsel = kare

        cv2.imshow("Lucas-Kanade Optik Akis", gorsel)
        onceki_gray = simdiki_gray.copy()

        key = cv2.waitKey(30) & 0xFF
        if key == ord('q'):
            break
        elif key == ord('r'):
            # Noktaları yeniden tespit et, izleri sıfırla
            noktalar   = cv2.goodFeaturesToTrack(simdiki_gray, **OZELLIK_PARAMS)
            iz_tuvali  = np.zeros_like(kare)
            print(f"Sıfırlandı → {len(noktalar)} yeni nokta")
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Açıklama:**
`goodFeaturesToTrack` köşe benzeri, takip edilmesi kolay noktalar seçer. `calcOpticalFlowPyrLK` bu noktaları bir sonraki karede piramit yaklaşımıyla arar; `durum` dizisi başarıyla takip edilen noktaları (1) ve kayıpları (0) belirtir. İz tuvalinde her karenin hareketi birikerek yörünge çizgisi oluşturur.

**Beklenen çıktı:**
Kamera görüntüsü üzerinde renkli hareket yörüngeleri. 'r' tuşu yörüngeleri sıfırlar ve yeni noktalar tespit edilir.

**İyileştirme fikirleri:**
- Takip kaybedilen noktaları her N karede bir `goodFeaturesToTrack` ile otomatik yenileme
- Ortalama hareket vektörünü hesaplayıp kamera sallantısı tespiti

---

## BLOK 8 — Mini Projeler

---

### Uygulama 8.1 — Renk Takip Sistemi

**Problem:**
Kamera görüntüsünde seçilen renkli nesnenin konumunu gerçek zamanlı izlemek ve merkezini ekranda göstermek.

**Kullanılan teknikler:** HSV maskeleme, kontur tespiti, ağırlık merkezi hesabı, hareket izi

**Pipeline:**
```
Kamera → HSV → renk maskesi → morfoloji → en büyük kontur
       → kütle merkezi → ize ekle → çerçeve + hedef çiz (döngü)
```

**Kod:**
```python
import cv2
import numpy as np
from collections import deque

# ---- Takip edilecek renk (turuncu top için örnek) ----
LOWER_HSV = np.array([10,  100, 80])
UPPER_HSV = np.array([25,  255, 255])
IZ_UZUNLUGU = 40   # kaç kare geriye kadar iz gösterilsin
# ------------------------------------------------------

cap = cv2.VideoCapture(0)
if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

iz = deque(maxlen=IZ_UZUNLUGU)
kernel = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (9, 9))

try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        frame = cv2.flip(frame, 1)   # ayna görüntüsü
        hsv   = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
        mask  = cv2.inRange(hsv, LOWER_HSV, UPPER_HSV)
        mask  = cv2.morphologyEx(mask, cv2.MORPH_OPEN,  kernel)
        mask  = cv2.morphologyEx(mask, cv2.MORPH_CLOSE, kernel)

        konturlar, _ = cv2.findContours(mask.copy(), cv2.RETR_EXTERNAL,
                                         cv2.CHAIN_APPROX_SIMPLE)
        merkez = None

        if konturlar:
            en_buyuk = max(konturlar, key=cv2.contourArea)
            if cv2.contourArea(en_buyuk) > 300:
                ((cx, cy), yari_cap) = cv2.minEnclosingCircle(en_buyuk)
                M = cv2.moments(en_buyuk)
                if M["m00"] > 0:
                    merkez = (int(M["m10"] / M["m00"]),
                              int(M["m01"] / M["m00"]))

                # Nesneyi daire ve çarpı ile işaretle
                cv2.circle(frame, (int(cx), int(cy)), int(yari_cap),
                           (0, 255, 255), 2)
                cv2.circle(frame, merkez, 5, (0, 0, 255), -1)
                cv2.putText(frame, f"({merkez[0]}, {merkez[1]})",
                            (merkez[0]+10, merkez[1]-10),
                            cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 255, 255), 2)

        iz.appendleft(merkez)

        # Hareket izini çiz
        for i in range(1, len(iz)):
            if iz[i-1] is None or iz[i] is None:
                continue
            kalinlik = int(np.sqrt(IZ_UZUNLUGU / float(i + 1)) * 2.5)
            cv2.line(frame, iz[i-1], iz[i], (0, 0, 255), max(kalinlik, 1))

        cv2.imshow("Renk Takip",  frame)
        cv2.imshow("HSV Maske",   mask)

        if cv2.waitKey(30) & 0xFF == ord('q'):
            break
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Açıklama:**
`minEnclosingCircle` nesneyi kapsayan en küçük daireyi bulur ve hem merkez koordinatını hem de yarı çapını verir. `deque(maxlen=N)` son N konumu saklayan ve otomatik eski konumları silen bir yapıdır. İz kalınlığının `sqrt` ile azalması, en yeni hareketi daha belirgin gösterir.

**Beklenen çıktı:**
Nesne etrafında sarı daire, merkez noktası kırmızı, koordinatlar yazılı; hareket ettikçe kırmızı iz bırakır.

**İyileştirme fikirleri:**
- Uygulama 2.2'deki trackbar ile renk aralığını gerçek zamanlı ayarlama
- Nesnenin hızını (piksel/kare) hesaplayıp ekranda gösterme
- Birden fazla nesneyi farklı renk aralıklarıyla eş zamanlı takip etme

---

### Uygulama 8.2 — Bulanıklık Tespiti

**Problem:**
Bir görüntünün veya video karesinin odakta olup olmadığını otomatik olarak belirlemek.

**Kullanılan teknikler:** `cv2.Laplacian`, varyans hesabı

**Pipeline:**
```
Görüntü → gri → Laplacian → varyans → eşikle karşılaştır → karar
```

**Kod:**
```python
import cv2
import numpy as np
import os

ESIK = 100.0   # varyans bu değerin altındaysa görüntü bulanık sayılır

def bulaniklik_olc(img):
    """Görüntünün bulanıklık skorunu (Laplacian varyansı) döndürür."""
    gray      = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY) if len(img.shape) == 3 else img
    laplacian = cv2.Laplacian(gray, cv2.CV_64F)
    return laplacian.var()

def goruntu_degerlendir(dosya_yolu):
    img = cv2.imread(dosya_yolu)
    if img is None:
        return
    skor  = bulaniklik_olc(img)
    durum = "BULANIK" if skor < ESIK else "NET"
    print(f"{os.path.basename(dosya_yolu):30s} | Skor: {skor:8.1f} | {durum}")
    return img, skor, durum

# Kamera ile gerçek zamanlı test
cap = cv2.VideoCapture(0)
if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

print(f"Eşik: {ESIK}  —  Kaydedilen karelerin bulanıklık durumu gösterilir")

try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        skor  = bulaniklik_olc(frame)
        durum = "BULANIK" if skor < ESIK else "NET"
        renk  = (0, 0, 255) if skor < ESIK else (0, 200, 0)

        cv2.putText(frame, f"Skor: {skor:.1f}  →  {durum}",
                    (10, 35), cv2.FONT_HERSHEY_SIMPLEX, 0.9, renk, 2)

        # Bulanıklık çubuğu (görsel gösterge)
        bar_max = 300
        bar_w   = min(int(skor / bar_max * frame.shape[1]), frame.shape[1])
        cv2.rectangle(frame, (0, frame.shape[0]-15),
                      (bar_w, frame.shape[0]), renk, -1)

        cv2.imshow("Bulaniklik Tespiti", frame)

        # 's' tuşuyla anlık fotoğraf kaydet
        key = cv2.waitKey(30) & 0xFF
        if key == ord('s'):
            cv2.imwrite(f"cekilen_{durum.lower()}_{skor:.0f}.jpg", frame)
            print(f"Kaydedildi: skor={skor:.1f} durum={durum}")
        elif key == ord('q'):
            break
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Açıklama:**
Net görüntülerde kenarlar belirgin ve kontrastlıdır; Laplacian bu değişimleri yakalarken yüksek varyans üretir. Bulanık görüntülerde kenarlar yumuşatıldığından Laplacian çok küçük değişimler görür ve varyans düşük kalır. Eşik değeri kullanılan kameraya ve sahneye göre ayarlanmalıdır; birkaç net ve bulanık örnek fotoğraf çekerek doğru değeri kalibre edebilirsiniz.

**Beklened çıktı:**
Kamera görüntüsü üzerinde anlık bulanıklık skoru ve "NET/BULANIK" etiketi. Alt tarafta yeşil/kırmızı skor çubuğu görünür.

**İyileştirme fikirleri:**
- Eşik değerini `ESIK = 100` satırından kolayca ayarlama
- Toplu görüntü klasöründe en net 10 fotoğrafı otomatik seçme
- Video kaydında yalnızca net kareleri farklı klasöre kaydetme

---

### Uygulama 8.3 — Basit Yüz Bulanıklaştırıcı

**Problem:**
Bir görüntü veya video karesindeki yüzleri tespit edip gizlilik amacıyla bulanıklaştırmak.

**Kullanılan teknikler:** `cv2.CascadeClassifier`, `cv2.GaussianBlur`, ROI işleme

**Pipeline:**
```
Gri görüntü → Haar cascade → yüz ROI'leri
           → her ROI'ye güçlü GaussianBlur → orijinale geri yaz
```

**Kod:**
```python
import cv2
import numpy as np

# Haar cascade dosyasını yükle (OpenCV ile birlikte gelir)
cascades_yolu = cv2.data.haarcascades + "haarcascade_frontalface_default.xml"
yuz_cascade   = cv2.CascadeClassifier(cascades_yolu)

if yuz_cascade.empty():
    raise RuntimeError("Cascade dosyası yüklenemedi.")

def yuzleri_bulaniklas(frame, kuvvet=51):
    """Görüntüdeki yüzleri tespit edip bulanıklaştırır."""
    gray  = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    yuzler = yuz_cascade.detectMultiScale(
        gray,
        scaleFactor=1.1,
        minNeighbors=5,
        minSize=(50, 50)
    )

    sonuc = frame.copy()
    for (x, y, w, h) in yuzler:
        roi           = sonuc[y:y+h, x:x+w]
        bulanik_roi   = cv2.GaussianBlur(roi, (kuvvet, kuvvet), 0)
        sonuc[y:y+h, x:x+w] = bulanik_roi

    return sonuc, len(yuzler)

# Görüntüde test
img = cv2.imread("insanlar.jpg")
if img is not None:
    sonuc, sayi = yuzleri_bulaniklas(img)
    print(f"Bulanıklaştırılan yüz: {sayi}")
    cv2.imshow("Sonuc", sonuc)
    cv2.waitKey(0)
    cv2.destroyAllWindows()

# Gerçek zamanlı kamera ile
cap = cv2.VideoCapture(0)
if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

print("Tuş kontrolleri: [q] Çıkış")

try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        sonuc, sayi = yuzleri_bulaniklas(frame)

        cv2.putText(sonuc, f"Tespit: {sayi} yuz", (10, 30),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 0), 2)

        cv2.imshow("Yuz Bulaniklatirici", sonuc)

        if cv2.waitKey(30) & 0xFF == ord('q'):
            break
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Açıklama:**
Haar cascade sınıflandırıcısı, eğitim sırasında öğrendiği yüz özellik şablonlarını görüntü üzerinde farklı ölçeklerde arar. `scaleFactor=1.1` görüntüyü %10 küçülterek tarar; `minNeighbors=5` ise sahte tespitleri filtreler. Bulanıklaştırma için yüz ROI'si `GaussianBlur` ile işlenip aynı konuma geri yazılır.

**Beklenen çıktı:**
Tespit edilen yüzlerin üzeri Gaussian bulanıklaştırma ile gizlenmiş görüntü. Yüz sayısı ekranda görünür.

**İyileştirme fikirleri:**
- `kuvvet` parametresini trackbar ile ayarlama
- Bulanıklaştırma yerine pikselleştirme (mosaic) efekti uygulama
- `haarcascade_eye.xml` ekleyerek göz tespiti de yapma

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `opencv-teori.md` · `opencv-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
