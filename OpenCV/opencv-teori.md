# 📘 OpenCV — Teorik Notlar

> **Modül:** Teori | **Seviye:** Başlangıç → Orta
> **İlgili dosyalar:** `opencv-uygulamalar.md` · `opencv-alistirmalar.md`

---

## BLOK 0 — Giriş Kartı

```
Kütüphane  : OpenCV (Open Source Computer Vision Library)
Sürüm      : 4.x (güncel)
Lisans     : Apache 2.0
Kurulum    : pip install opencv-python
```

**Ne yapar?**
OpenCV, görüntü ve video işleme için geliştirilmiş açık kaynaklı bir kütüphanedir. 1999 yılında Intel tarafından başlatılmış, bugün robotik, güvenlik, tıp, üretim ve yapay zeka projelerinde fiilen standart haline gelmiştir. Görüntü okuma, renk dönüşümü, filtreleme, kenar tespiti, nesne tespiti, kamera kalibrasyonu, video analizi ve derin öğrenme entegrasyonu gibi 2500'den fazla optimize edilmiş algoritma içerir. Python, C++, Java ve MATLAB ile kullanılabilir; bu dokümanda Python arayüzü anlatılmaktadır.

**Kullanım alanları:**

| Alan | Örnek Uygulama |
|------|----------------|
| Güvenlik ve Gözetleme | Hareket tespiti, yüz tanıma, anormallik tespiti |
| Üretim / Kalite Kontrol | Kusur tespiti, boyut ölçümü, OCR |
| Tıbbi Görüntüleme | Hücre sayımı, MRI/CT analizi |
| Otonom Araçlar | Şerit takibi, trafik işareti okuma |
| Robotik | Nesne yakalama, ortam haritalama |
| Artırılmış Gerçeklik | Marker tespiti, kamera kalibrasyonu |

**Güçlü olduğu alan:**
- Gerçek zamanlı görüntü ve video işleme (hız odaklı C++ çekirdeği)
- Klasik bilgisayarlı görü algoritmaları (filtreleme, morfoloji, kontur, optik akış vb.)
- Kamera kalibrasyonu ve 3D yeniden yapılandırma
- Hazır DNN modelleriyle hızlı inference (ONNX, Caffe, TensorFlow, Darknet)
- Düşük seviyeli piksel erişimi ve NumPy entegrasyonu

**Zayıf olduğu / yapmaması gereken:**
- Model eğitimi — OpenCV bu iş için tasarlanmamıştır; eğitim için TensorFlow veya PyTorch kullanılır
- Karmaşık poz tahmini, el/yüz landmark gibi hazır pipeline'lar — bunlar için MediaPipe daha uygundur
- GPU hızlandırmalı derin öğrenme — bunun için TensorRT veya özel framework'ler tercih edilir

**Alternatifleri:**

| Alternatif | Ne zaman tercih edilir? | Hız |
|------------|------------------------|-----|
| Pillow (PIL) | Sadece basit görüntü açma/kaydetme/kırpma | Orta |
| scikit-image | Akademik/bilimsel görüntü işleme; daha Pythonic API | Yavaş |
| MediaPipe | Hazır yüz, el, poz pipeline'ları; OpenCV'nin üstüne kurulu | Orta |
| ImageAI / Detectron2 | Doğrudan nesne tespiti ve segmentasyon | Değişken |

**Önkoşullar:**
- Python >= 3.7
- NumPy (opencv-python kurulumunda otomatik gelir)
- Görüntü dosyaları için herhangi bir hazırlık gerekmez

---

## BLOK 1 — Kurulum

**Paket türleri — hangisini seçmeli?**

| Paket | Ne zaman seçilir |
|-------|-----------------|
| `opencv-python` | Çoğu proje için yeterli (standart) |
| `opencv-contrib-python` | SIFT, SURF, ArUco gibi ek algoritmalar gerektiğinde |
| `opencv-python-headless` | Sunucu, Docker, CI ortamları (GUI yok) |
| `opencv-contrib-python-headless` | Sunucuda gelişmiş algoritma gerektiğinde |

```bash
# Standart kurulum (GUI penceresi açmak için yeterli)
pip install opencv-python

# Tam kurulum (ek modüller: SIFT, video codec'ler vb.)
pip install opencv-contrib-python

# Conda ile
conda install -c conda-forge opencv

# Headless (sunucu ortamı, GUI yok)
pip install opencv-python-headless
```

> ⚠️ `opencv-python` ve `opencv-contrib-python` aynı ortama birlikte kurulmamalıdır. İkisi çakışır, birini seçin.

**Sanal ortam (şiddetle önerilir):**

```bash
# venv ile
python -m venv cv_env
source cv_env/bin/activate     # Linux/macOS
cv_env\Scripts\activate        # Windows
pip install opencv-python numpy matplotlib

# conda ile
conda create -n cv_env python=3.10
conda activate cv_env
conda install -c conda-forge opencv numpy matplotlib
```

**Kurulum doğrulama:**

```python
import cv2
print(cv2.__version__)   # Beklenen çıktı: 4.x.x
```

**⚠️ Sürüm uyarıları:**
- SIFT ve SURF gibi patentli algoritmalar `opencv-contrib-python` içinde gelir, standart pakette yoktur. OpenCV 4.4+ ile SIFT ana pakete alınmıştır, SURF hâlâ contrib'dedir.
- `findContours()` dönüş değeri OpenCV 4'te değişti: eski sürümlerde `(image, contours, hierarchy)` dönerken, 4.x'te `(contours, hierarchy)` döner.
- Eski `cv2.cv` API'si Python 3'te kaldırılmıştır, sadece `cv2.*` kullanın.
- Sanal ortam (venv/conda) kullanımı şiddetle önerilir; sistem Python'una kurmaktan kaçının.
- `cv2.imshow()` Jupyter Notebook'ta kernel çökertebilir. Jupyter'da Matplotlib kullanın: `plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))`

---

## BLOK 2 — Temel Kavramlar

### OpenCV'nin düşünce biçimi

OpenCV'de her görüntü aslında bir **NumPy dizisidir (ndarray)**. Bu, görüntüler üzerinde doğrudan NumPy işlemleri yapabileceğiniz anlamına gelir. Kütüphanenin tüm fonksiyonları bu dizi üzerinde çalışır ve genellikle yeni bir dizi döndürür; orijinal görüntüyü değiştirmez.

```
Disk / Kamera
     ↓
cv2.imread() / cv2.VideoCapture()
     ↓
NumPy ndarray (H × W × C)
     ↓
İşlem Fonksiyonları (blur, threshold, contour vb.)
     ↓
Yeni ndarray
     ↓
cv2.imshow() / cv2.imwrite()
```

---

### Kavram 1 — Görüntü Veri Yapısı

OpenCV'de bir renkli görüntü **3 boyutlu bir NumPy dizisidir:**
- **Boyut 0 (satır):** Yükseklik (Height) — piksel sayısı
- **Boyut 1 (sütun):** Genişlik (Width) — piksel sayısı
- **Boyut 2 (kanal):** Renk kanalları — renkli için 3 (B, G, R), gri için 1

```python
import cv2
img = cv2.imread("foto.jpg")

print(img.shape)    # (480, 640, 3) → Yükseklik × Genişlik × Kanal
print(img.dtype)    # uint8 → her piksel 0-255 arası tam sayı
print(img.size)     # 921600 → toplam eleman sayısı (480×640×3)
print(img.nbytes)   # 921600 → bellekte kapladığı byte

# Piksel erişimi
piksel     = img[100, 200]        # y=100, x=200 → [B, G, R]
mavi_kanal = img[100, 200, 0]     # Sadece mavi kanal değeri
satir      = img[100, :]          # Tüm 100. satır
roi        = img[50:150, 80:200]  # Bölgesel erişim (ROI)
```

Gri tonlamalı görüntüde `shape` sadece `(480, 640)` olur, 3. boyut yoktur.

**Veri tipleri:**

| dtype | Aralık | Kullanım Yeri |
|-------|--------|---------------|
| `uint8` | 0 – 255 | Standart görüntü, maske |
| `int16` | -32768 – 32767 | Sobel çıktısı (negatif gradyan) |
| `float32` | ±3.4×10³⁸ | Hesaplama aracı, ara işlem |
| `float64` | ±1.7×10³⁰⁸ | Yüksek hassasiyetli hesaplama |

---

### Kavram 2 — BGR Renk Sırası

OpenCV görüntüleri **BGR (Blue-Green-Red)** sırasıyla saklar. Bu, diğer kütüphanelerin (Matplotlib, PIL, TensorFlow, PyTorch) kullandığı **RGB (Red-Green-Blue)** sırası ile ters olduğu anlamına gelir. Bu farkı görmezden gelirseniz renkler yanlış görünür.

```
OpenCV     : kanal[0] = Mavi,    kanal[1] = Yeşil, kanal[2] = Kırmızı
Matplotlib : kanal[0] = Kırmızı, kanal[1] = Yeşil, kanal[2] = Mavi
TensorFlow : RGB  (dönüşüm gerekir)
PyTorch    : RGB  (dönüşüm gerekir)
PIL/Pillow : RGB  (dönüşüm gerekir)
```

Matplotlib ile göstermek istediğinizde mutlaka dönüştürün:

```python
import cv2
import matplotlib.pyplot as plt

img_bgr = cv2.imread("foto.jpg")
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)  # dönüştür
plt.imshow(img_rgb)   # ✅ doğru renkler
plt.imshow(img_bgr)   # ❌ kırmızı-mavi ters görünür
plt.show()

# Doğrudan piksel atarken sırayı unutma
img[50, 50] = [255, 0, 0]   # Mavi piksel (BGR: B=255, G=0, R=0)
img[50, 51] = [0, 0, 255]   # Kırmızı piksel (BGR: B=0, G=0, R=255)
```

---

### Kavram 3 — Koordinat Sistemi

OpenCV'de koordinatlar **piksel (x, y)** formatındadır ama NumPy dizileri **[satır, sütun]** yani **[y, x]** formatında erişilir. Bu terslik sık kafa karıştırır:

```python
# NumPy erişimi: [satır, sütun] yani [y, x]
piksel = img[100, 200]      # y=100, x=200 noktasındaki piksel

# OpenCV fonksiyonları: (x, y) formatında
cv2.circle(img, (200, 100), 5, (0, 255, 0), -1)  # merkez (x=200, y=100)

# Kontur kütle merkezi hesabında dikkat:
M  = cv2.moments(cnt)
cx = int(M["m10"] / M["m00"])   # x koordinatı
cy = int(M["m01"] / M["m00"])   # y koordinatı
cv2.circle(img, (cx, cy), 5, (0, 255, 0), -1)   # çizim: (cx, cy) ✅
piksel = img[cy, cx]                              # NumPy erişimi: [cy, cx] ✅
```

---

### Kavram 4 — Değişmezlik ve Kopyalama

OpenCV fonksiyonlarının büyük çoğunluğu orijinal görüntüyü değiştirmez, **yeni bir görüntü döndürür.** Ancak `rectangle`, `circle`, `drawContours` gibi çizim fonksiyonları görüntüyü **yerinde (in-place)** değiştirir.

```python
# Yeni dizi döndüren fonksiyonlar — orijinal değişmez
blurred = cv2.GaussianBlur(img, (5, 5), 0)
gray    = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
resized = cv2.resize(img, (320, 240))

# Yerinde değiştiren fonksiyonlar — orijinal değişir!
gorsel = img.copy()   # önce kopyala
cv2.rectangle(gorsel, (10, 10), (100, 100), (0, 255, 0), 2)
cv2.drawContours(gorsel, contours, -1, (0, 255, 0), 2)
```

**⚠️ NumPy referans tuzağı:** `a = img` gerçek bir kopya değildir; aynı bellek bloğunu paylaşır. Gerçek bağımsız kopya için `img.copy()` kullanın.

```python
a = img             # REFERANS → a[0,0] değiştirilirse img de değişir!
b = img.copy()      # KOPYA  → bağımsız bellek bloğu
```

**ROI (Region of Interest):** NumPy dilimleme ile seçilen bölge de referanstır. ROI üzerinde yapılan değişiklik orijinal görüntüyü etkiler.

```python
roi = img[100:300, 150:400]          # REFERANS
roi_kopya = img[100:300, 150:400].copy()  # KOPYA
```

> **Performans notu:** Tüm görüntü yerine yalnızca ROI üzerinde işlem yapmak, işlem süresini önemli ölçüde düşürür.

---

### Kavram 5 — uint8 ve Değer Taşması

Görüntüler genellikle `uint8` tipindedir; değerler 0-255 arasındadır. Bu aralığın dışına çıkılırsa **değer taşması (overflow)** olur:

```
255 + 1  →  0    (taşma yukarı)
  0 - 1  →  255  (taşma aşağı)
250 + 10 →  4    (260 mod 256 = 4)
```

```python
import numpy as np

a = np.uint8(250)
print(a + 10)   # 4 çıkar! — tehlikeli

# Güvenli yöntem 1: float32 + clip
img_f = img.astype(np.float32)
img_f = img_f + 80
img_f = np.clip(img_f, 0, 255)
img_result = img_f.astype(np.uint8)

# Güvenli yöntem 2: cv2.add — doyma aritmetiği (255'i geçmez, 0'ın altına düşmez)
img_parlak = cv2.add(img, np.full_like(img, 80))
```

`cv2.add()` ve `cv2.subtract()` **doyma (saturation)** aritmetiği kullanır: 255'i aşan değerler 255'e, 0'ın altına düşen değerler 0'a sabitlenir.

---

## BLOK 3 — Fonksiyon Rehberi

---

### 🔹 Grup 1 — Görüntü Okuma, Gösterme ve Kaydetme

#### `cv2.imread(filename, flags)`

**Ne yapar:**
Diskten bir görüntü dosyasını okur ve NumPy dizisi olarak döndürür. Dosya bulunamazsa hata fırlatmaz, `None` döndürür. JPEG, PNG, BMP, TIFF ve daha birçok formatı destekler.

**Ne zaman kullanılır:**
Statik bir görüntü dosyasını programa yüklemek istediğinizde. Video veya kamera için `VideoCapture` kullanılır.

**Parametreler:**
- `filename` — okunacak dosyanın yolu (string)
- `flags` — okuma modu:
  - `cv2.IMREAD_COLOR` (1) — renkli, BGR, varsayılan
  - `cv2.IMREAD_GRAYSCALE` (0) — gri tonlamalı
  - `cv2.IMREAD_UNCHANGED` (-1) — alpha kanalı dahil, olduğu gibi oku

**Döndürür:** Başarılıysa `(H, W, 3)` uint8 ndarray; başarısızsa `None`.

```python
import cv2

img      = cv2.imread("foto.jpg")        # renkli (BGR)
img_gray = cv2.imread("foto.jpg", 0)     # gri
img_rgba = cv2.imread("logo.png", -1)    # alpha dahil

if img is None:
    raise FileNotFoundError("Görüntü yüklenemedi, dosya yolu kontrol edin.")

print(img.shape)   # (yükseklik, genişlik, 3)
```

**🔴 Dikkat:** Dosya yolu yanlışsa `None` döner, hata vermez. Her `imread` çağrısından sonra `if img is None` kontrolü yapma alışkanlığı edinin. Türkçe karakter içeren dosya yolları Windows'ta sorun çıkarabilir; `np.fromfile` + `imdecode` kullanın.

---

#### `cv2.imwrite(filename, img, params=None)`

**Ne yapar:**
Bellekteki NumPy görüntüsünü diske yazar. Dosya uzantısına göre formatı otomatik belirler.

**Döndürür:** Başarılıysa `True`, başarısızsa `False`.

**Parametreler:**
- `filename` — yazılacak dosya yolu ve adı
- `img` — kaydedilecek NumPy dizisi
- `params` — format parametreleri (opsiyonel):
  - JPEG kalitesi: `[cv2.IMWRITE_JPEG_QUALITY, 90]` (0-100)
  - PNG sıkıştırma: `[cv2.IMWRITE_PNG_COMPRESSION, 6]` (0-9)

```python
success = cv2.imwrite("cikti.png", img)
if not success:
    print("Kaydetme başarısız.")

# JPEG kalitesini ayarla
cv2.imwrite("cikti.jpg", img, [cv2.IMWRITE_JPEG_QUALITY, 90])
```

**🔴 Dikkat:** Hedef dizin mevcut değilse sessizce başarısız olur. `os.makedirs` ile önce dizini oluşturun. `float32` görüntüler kaydedilemez; önce `uint8`'e dönüştürün.

---

#### `cv2.imshow(winname, mat)` ve `cv2.waitKey(delay)`

**Ne yapar:**
`imshow`, görüntüyü ekran penceresinde gösterir. `waitKey`, pencereyi belirtilen milisaniye (ms) boyunca açık tutar; `0` değeri sonsuz bekleme anlamına gelir. Bu iki fonksiyon her zaman birlikte kullanılır.

**Döndürür:** `waitKey`, basılan tuşun ASCII kodunu döndürür; süre dolup tuşa basılmazsa `-1` döner.

```python
cv2.imshow("Pencere Adı", img)
cv2.waitKey(0)              # herhangi bir tuşa basılana kadar bekle
cv2.destroyAllWindows()

# Video döngüsü için
key = cv2.waitKey(30) & 0xFF   # ~30 FPS
if key == ord('q'):
    break
```

**🔴 Dikkat:** `imshow` tek başına çalışmaz, mutlaka `waitKey` ile takip edilmelidir. Jupyter Notebook'ta kernel çökertebilir — orada Matplotlib kullanın.

---

### 🔹 Grup 2 — Renk Dönüşümleri

#### `cv2.cvtColor(src, code)`

**Ne yapar:**
Görüntüyü bir renk uzayından diğerine dönüştürür.

**Ne zaman kullanılır:**
Matplotlib ile gösterme, HSV tabanlı renk maskeleme, gri görüntü üzerinde işlem ve derin öğrenme modeline aktarma gibi durumlarda renk uzayı dönüşümü gerekir.

**Döndürür:** Dönüştürülmüş görüntü ndarray.

**Sık kullanılan `code` değerleri:**

| Kod | Dönüşüm | Ne zaman |
|-----|---------|---------|
| `COLOR_BGR2GRAY` | Renkli → Gri | Kenar tespiti, eşikleme |
| `COLOR_BGR2RGB` | BGR → RGB | Matplotlib görüntüleme |
| `COLOR_BGR2HSV` | BGR → HSV | Renk tabanlı maskeleme |
| `COLOR_BGR2LAB` | BGR → LAB | Renk karşılaştırma |
| `COLOR_BGR2YCrCb` | BGR → YCrCb | Cilt tonu tespiti |
| `COLOR_GRAY2BGR` | Gri → Renkli | Gri üzerine renkli çizim yapılacaksa |
| `COLOR_HSV2BGR` | HSV → BGR | Maske uyguladıktan sonra geri dönüş |

**Algoritma mantığı (Grayscale):**
Gri dönüşümde üç kanal basit ortalama yerine `Gray = 0.299×R + 0.587×G + 0.114×B` formülüyle birleştirilir. İnsan gözü yeşile daha duyarlı olduğundan yeşil kanal daha fazla ağırlık alır; bu sayede sonuç doğal parlaklık algısına uygun çıkar.

**Algoritma mantığı (HSV):**
HSV'de renk (H), doygunluk (S) ve parlaklık (V) birbirinden ayrılır. Aynı renk farklı aydınlatma koşullarında BGR'de çok farklı değerler alırken HSV'de yalnızca V kanalı değişir; H kanalı sabit kalır. Bu nedenle renk tabanlı nesne tespiti için HSV çok daha güvenilirdir.

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
hsv  = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
rgb  = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

# HSV ile renk maskeleme (turuncu nesne)
lower = np.array([10,  100, 100])
upper = np.array([25,  255, 255])
mask  = cv2.inRange(hsv, lower, upper)
```

**🔴 Dikkat:** Gri görüntüyü `COLOR_BGR2HSV` ile dönüştürmeye çalışmak hata verir. Önce `COLOR_GRAY2BGR` ile üç kanala çevirin.

---

### 🔹 Grup 3 — Geometrik Dönüşümler

#### `cv2.resize(src, dsize, fx=0, fy=0, interpolation=cv2.INTER_LINEAR)`

**Ne yapar:**
Görüntüyü yeniden boyutlandırır. Hedef boyut `dsize` ile piksel cinsinden, ya da `fx`/`fy` ölçek faktörleriyle belirtilebilir.

**Ne zaman kullanılır:**
Modele giriş boyutunu standartlaştırma, görüntüyü küçülterek işlem hızlandırma, yan yana görüntüleri aynı boyuta getirme.

**Döndürür:** Yeniden boyutlandırılmış görüntü ndarray.

**Enterpolasyon yöntemleri:**

| Yöntem | Kullanım | Not |
|--------|---------|-----|
| `INTER_NEAREST` | Maske, binary görüntü | En hızlı, düşük kalite |
| `INTER_LINEAR` | Genel amaçlı (varsayılan) | İyi denge |
| `INTER_CUBIC` | Büyütme | Daha pürüzsüz |
| `INTER_LANCZOS4` | Yüksek kaliteli büyütme | En yavaş, en iyi kalite |
| `INTER_AREA` | **Küçültme için önerilir** | Moire artefaktını önler |

```python
# Belirli boyuta
kucuk = cv2.resize(img, (320, 240))

# Oranı koruyarak yarıya indir
yarim = cv2.resize(img, (0, 0), fx=0.5, fy=0.5,
                   interpolation=cv2.INTER_AREA)

# Büyütme (yüksek kalite)
buyuk = cv2.resize(img, (0, 0), fx=2.0, fy=2.0,
                   interpolation=cv2.INTER_CUBIC)
```

**🔴 Dikkat:** `dsize` parametresi `(genişlik, yükseklik)` sırasında; `img.shape` ise `(yükseklik, genişlik)` sırasında. Bu sırayı karıştırmak yamuk görüntüye yol açar.

```python
h, w = img.shape[:2]
cv2.resize(img, (w, h))     # ✅ doğru
cv2.resize(img, img.shape[:2])  # ❌ yanlış! (h, w) sırası
```

---

#### `cv2.rotate(src, rotateCode)`

**Ne yapar:**
Görüntüyü 90°'nin katları kadar döndürür. Serbest açı döndürme için `getRotationMatrix2D` + `warpAffine` kullanılır.

```python
# 90°, 180°, 270° döndürme
cv2.rotate(img, cv2.ROTATE_90_CLOCKWISE)
cv2.rotate(img, cv2.ROTATE_180)
cv2.rotate(img, cv2.ROTATE_90_COUNTERCLOCKWISE)

# Serbest açıda döndürme (merkez etrafında 45°)
merkez = (img.shape[1] // 2, img.shape[0] // 2)
M      = cv2.getRotationMatrix2D(center=merkez, angle=45, scale=1.0)
donmus = cv2.warpAffine(img, M, (img.shape[1], img.shape[0]))
```

---

#### `cv2.warpAffine(src, M, dsize)` ve `cv2.warpPerspective(src, M, dsize)`

**Ne yapar:**
`warpAffine`, 2×3 afin dönüşüm matrisi (döndürme, öteleme, ölçekleme) uygular. `warpPerspective`, 3×3 perspektif dönüşüm matrisi uygular; belge tarama ve plaka düzeltme için kullanılır.

**Algoritma mantığı:**
Afin dönüşüm paralel çizgileri paralel olarak korur; üç nokta çiftiyle belirlenir. Perspektif dönüşüm ise bir düzlemi farklı bir açıdan görüyormuş gibi dönüştürür ve dört nokta çiftiyle belirlenir. Bu, yamuk duran bir belgeyi dikdörtgene dönüştürmek için idealdir.

```python
# Perspektif düzeltme (belge tarama)
pts_src = np.float32([[73, 45], [420, 30], [450, 590], [50, 600]])
w, h    = 400, 560
pts_dst = np.float32([[0, 0], [w, 0], [w, h], [0, h]])

M           = cv2.getPerspectiveTransform(pts_src, pts_dst)
duzeltilmis = cv2.warpPerspective(img, M, (w, h))

# Afin dönüşüm (3 nokta çiftinden)
pts_src = np.float32([[50, 50], [200, 50], [50, 200]])
pts_dst = np.float32([[10, 80], [200, 50], [100, 250]])
M_afin  = cv2.getAffineTransform(pts_src, pts_dst)
afin    = cv2.warpAffine(img, M_afin, (img.shape[1], img.shape[0]))
```

---

### 🔹 Grup 4 — Bulanıklaştırma / Gürültü Azaltma

#### `cv2.GaussianBlur(src, ksize, sigmaX)`

**Ne yapar:**
Her pikseli, ağırlıkları Gaussian dağılımına göre belirlenmiş komşularının ortalamasıyla değiştirir.

**Ne zaman kullanılır:**
Kenar tespiti veya eşikleme öncesinde gürültüyü azaltmak için neredeyse her pipeline'ın ilk adımıdır.

**Döndürür:** Bulanıklaştırılmış görüntü ndarray.

**Parametreler:**
- `ksize` — kernel boyutu, **tek sayı** olmalı; ör. `(5, 5)`, `(11, 11)`
- `sigmaX` — bulanıklık miktarı; `0` verilirse ksize'dan otomatik hesaplanır

**Algoritma mantığı:**
Merkeze yakın pikseller daha fazla ağırlık alır, uzaktaki pikseller daha az etki eder. Böylece gürültü azalırken görüntü doğal bir şekilde yumuşar. Büyük kernel ve yüksek sigma, daha fazla bulanıklık demektir.

```python
blur_hafif = cv2.GaussianBlur(img, (5, 5),   sigmaX=0)
blur_guclu = cv2.GaussianBlur(img, (15, 15), sigmaX=3.0)
```

**🔴 Dikkat:** `ksize` çift sayı verilemez; `(4, 4)` hata verir.

---

#### `cv2.medianBlur(src, ksize)`

**Ne yapar:**
Her pikseli, komşularının ortanca (medyan) değeriyle değiştirir.

**Ne zaman kullanılır:**
Tuz-biber gürültüsü (rastgele siyah/beyaz piksel) varsa Gaussian yerine medyan tercih edilir.

**Döndürür:** Gürültüsü azaltılmış görüntü ndarray.

**Algoritma mantığı:**
Sıralama işlemi sayesinde aşırı değerler (0 veya 255) her zaman listenin ucuna itilir ve ortancaya etki etmez. Bu nedenle tek bir aykırı piksel, Gaussian filtrenin aksine sonucu hiç bozmaz.

```python
denoised = cv2.medianBlur(img, ksize=5)  # ksize tek sayı olmalı
```

---

#### `cv2.bilateralFilter(src, d, sigmaColor, sigmaSpace)`

**Ne yapar:**
Hem mekansal yakınlığa hem de renk/yoğunluk benzerliğine dayalı akıllı bir bulanıklaştırma uygular.

**Ne zaman kullanılır:**
Kenarları koruyarak gürültü azaltmak istediğinizde; Gaussian filtre kenarları körleştirirken bilateral filtre korur.

**Döndürür:** Kenar korunmuş, bulanıklaştırılmış görüntü ndarray.

**Algoritma mantığı:**
Komşu piksellerden yalnızca renk değeri merkez piksele benzer olanlar ağırlıklı ortalamaya dahil edilir. Kenar ötesindeki pikseller çok farklı renk taşıdığından hesaba katılmaz; bu yüzden kenar keskin kalır.

**Parametreler:**
- `d` — komşuluk çapı (büyüdükçe yavaşlar)
- `sigmaColor` — renk farkı hassasiyeti; büyük değer daha geniş renk aralığını yumuşatır
- `sigmaSpace` — mekansal hassasiyet; büyük değer daha uzak pikselleri etkiler

```python
# Hafif kenar koruma
cv2.bilateralFilter(img, d=5, sigmaColor=35, sigmaSpace=35)

# Güçlü kenar koruma
cv2.bilateralFilter(img, d=9, sigmaColor=75, sigmaSpace=75)
```

**🔴 Dikkat:** Gaussian'dan 5-10× yavaştır; gerçek zamanlı işlemde `d ≤ 9` tutun.

---

### 🔹 Grup 5 — Kenar Tespiti

#### `cv2.Canny(image, threshold1, threshold2)`

**Ne yapar:**
Görüntüdeki kenarları ince (1 piksel kalınlığında), temiz ve bağlantılı çizgiler olarak tespit eder.

**Ne zaman kullanılır:**
Nesne sınırlarını bulmak, kontur tespitine hazırlık, şekil analizi. Klasik görüntü işlemede kenar tespitinin fiili standardıdır.

**Döndürür:** Kenarların beyaz, arka planın siyah olduğu binary görüntü (uint8).

**Parametreler:**
- `threshold1` — düşük eşik; bu değerin altındaki gradyanlar kenar değil
- `threshold2` — yüksek eşik; bu değerin üstündeki gradyanlar kesin kenar
- Kural: `threshold2 : threshold1 = 2:1` veya `3:1` oranı iyi başlangıç noktasıdır

**Algoritma mantığı (6 adım):**
1. Gaussian ile gürültü temizlenir
2. Sobel ile her pikselin gradyan büyüklüğü ve yönü hesaplanır
3. Non-maximum suppression: kenar boyunca yalnızca en güçlü piksel korunur, çevresi sıfırlanır — bu sayede kenarlar 1 piksel kalınlığına iner
4. Yüksek eşiği geçenler "kesin kenar" olarak işaretlenir
5. İki eşik arasındaki "zayıf kenarlar" yalnızca bir kesin kenara bağlıysa korunur, aksi halde gürültü sayılır ve silinir

```python
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
blur = cv2.GaussianBlur(gray, (5, 5), 0)

# Sabit eşikler
edges = cv2.Canny(blur, 50, 150)

# Otsu'dan otomatik türetilmiş eşikler (önerilen)
otsu_val, _ = cv2.threshold(blur, 0, 255, cv2.THRESH_OTSU)
edges = cv2.Canny(blur, otsu_val * 0.5, otsu_val)
```

**🔴 Dikkat:** Canny gri görüntü ister; renkli görüntü vermek hata verir.

---

#### `cv2.Sobel(src, ddepth, dx, dy, ksize=3)`

**Ne yapar:**
Görüntüdeki yatay veya dikey piksel değişimlerini (gradyanı) ölçer. X gradyanı dikey kenarları, Y gradyanı yatay kenarları yakalar.

**Ne zaman kullanılır:**
Kenar tespiti, gradyan haritası oluşturma, yön bilgisi gerektiren analizler. Canny'nin içinde de Sobel kullanılır.

**Döndürür:** Gradyan haritası ndarray (`ddepth` tipinde).

**Algoritma mantığı:**
Bir pikselin değişimini hem türev hem de komşu yumuşatma işlemini birleştiren 3×3 kernel ile ölçer. Bu yapı Sobel'i saf fark hesaplamalarına kıyasla gürültüye karşı daha dayanıklı kılar.

**Parametreler:**
- `ddepth` — çıktı derinliği; negatif değerleri kaybetmemek için `cv2.CV_64F` kullanın
- `dx`, `dy` — türev yönü; `(1,0)` = yatay değişim, `(0,1)` = dikey değişim

```python
sobel_x = cv2.Sobel(gray, cv2.CV_64F, dx=1, dy=0, ksize=3)  # dikey kenarlar
sobel_y = cv2.Sobel(gray, cv2.CV_64F, dx=0, dy=1, ksize=3)  # yatay kenarlar

# Toplam büyüklük haritası
magnitude = cv2.magnitude(sobel_x, sobel_y)
magnitude = cv2.convertScaleAbs(magnitude)   # uint8'e döndür
```

**🔴 Dikkat:** `uint8` ile Sobel kullanmak negatif gradyanları sıfıra kırpar; görüntünün bir tarafındaki kenarlar kaybolur. Her zaman `CV_64F` veya `CV_32F` kullanın.

---

#### `cv2.Laplacian(src, ddepth)`

**Ne yapar:**
İkinci türevi hesaplayarak yoğunluk değişiminin hızlandığı bölgeleri vurgular.

**Ne zaman kullanılır:**
Görüntünün bulanık olup olmadığını test etmek için pratik bir araçtır.

**Algoritma mantığı:**
Laplacian bir pikselin değeri ile komşu piksellerinin ortalaması arasındaki farkı ölçer. Net görüntülerde kenarlar belirgin gradyanlar oluşturduğundan varyans yüksek çıkar; bulanık görüntülerde ise kenarlar yumuşadığından varyans düşük kalır.

```python
gray      = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
laplacian = cv2.Laplacian(gray, cv2.CV_64F)
varyans   = laplacian.var()
if varyans < 100:
    print("Görüntü bulanık!")
```

---

### 🔹 Grup 6 — Eşikleme

#### `cv2.threshold(src, thresh, maxval, type)`

**Ne yapar:**
Her pikseli eşik değeriyle karşılaştırarak ikili (binary) görüntüye dönüştürür.

**Ne zaman kullanılır:**
Nesneyi arka plandan ayırmak, kontur tespitine hazırlık, renk veya parlaklık bazlı maskeleme.

**Döndürür:** `(retval, binary_image)` çifti. Otsu kullanıldığında `retval` bulunan eşik değeridir.

**Eşik türleri:**

| Tür | Sonuç |
|-----|-------|
| `THRESH_BINARY` | I > T → maxval, diğer → 0 |
| `THRESH_BINARY_INV` | I > T → 0, diğer → maxval |
| `THRESH_TRUNC` | I > T → T, diğer → I |
| `THRESH_TOZERO` | I > T → I, diğer → 0 |
| `THRESH_TOZERO_INV` | I > T → 0, diğer → I |

**Algoritma mantığı (Otsu):**
Otsu yöntemi en iyi eşik değerini histogramdan otomatik olarak belirler. Nesne ve arka plan arasındaki renk/parlaklık farkını maksimize eden eşiği seçer. Histogramda iki belirgin tepe (bimodal dağılım) varsa mükemmel çalışır.

```python
# Sabit eşik
_, binary = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY)

# Otsu — eşiği histogramdan otomatik hesapla
otsu_val, binary = cv2.threshold(gray, 0, 255,
                                  cv2.THRESH_BINARY + cv2.THRESH_OTSU)
print(f"Otsu eşiği: {otsu_val}")

# Ters binary
_, binary_inv = cv2.threshold(gray, 127, 255, cv2.THRESH_BINARY_INV)
```

---

#### `cv2.adaptiveThreshold(src, maxValue, adaptiveMethod, thresholdType, blockSize, C)`

**Ne yapar:**
Görüntüyü küçük bloklara bölerek her blok için bağımsız bir eşik hesaplar.

**Ne zaman kullanılır:**
Aydınlatmanın düzensiz olduğu durumlarda (el yazısı, taranmış belge, gölgeli sahne) global eşik başarısız olur; bu durumlarda adaptif eşikleme kullanılır.

**Algoritma mantığı:**
Her piksel için çevresindeki komşu piksel ortalaması hesaplanır ve bu ortalamadan C sabit değeri çıkarılarak o noktanın eşiği belirlenir. Her bölge kendi aydınlatma koşuluna göre ayrı işlenir.

**Parametreler:**
- `adaptiveMethod` — `ADAPTIVE_THRESH_MEAN_C` veya `ADAPTIVE_THRESH_GAUSSIAN_C`
- `blockSize` — her bölgenin boyutu (tek sayı, en az 3)
- `C` — ortalamadan çıkarılan ince ayar sabiti

```python
adaptive = cv2.adaptiveThreshold(
    gray, 255,
    cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
    cv2.THRESH_BINARY,
    blockSize=21, C=4
)
```

**Seçim rehberi:**

```
Düzgün aydınlatma, belirgin nesne/arka plan  →  Global + Otsu
Düzensiz aydınlatma, gölgeli metin           →  Adaptif Gaussian C
El yazısı, taranmış belge                    →  Adaptif Mean C
```

---

### 🔹 Grup 7 — Morfolojik İşlemler

Morfolojik işlemler, binary veya gri görüntüler üzerinde şekil bazlı dönüşümler yapar. Her işlem küçük bir şablon şekli olan **yapısal element** (kernel) kullanır.

```python
# Yapısal element türleri
kernel_kare  = cv2.getStructuringElement(cv2.MORPH_RECT,    (5, 5))
kernel_elips = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
kernel_capraz = cv2.getStructuringElement(cv2.MORPH_CROSS,  (5, 5))
```

---

#### `cv2.erode(src, kernel, iterations=1)`

**Ne yapar:**
Beyaz nesneleri kenarlardan aşındırır, küçültür.

**Algoritma mantığı:**
Bir pikselin beyaz kalabilmesi için kernel altındaki tüm piksellerin beyaz olması gerekir. Tek bir siyah piksel varsa merkez piksel siyaha döner. Bu, küçük beyaz gürültü noktalarını ve ince bağlantıları siler.

```python
eroded = cv2.erode(binary, kernel, iterations=1)
```

---

#### `cv2.dilate(src, kernel, iterations=1)`

**Ne yapar:**
Beyaz nesneleri kenarlardan genişletir, büyütür.

**Algoritma mantığı:**
Kernel altındaki herhangi bir piksel beyazsa merkez piksel beyaz yapılır. Bu, kopuk bölgeleri birleştirir, delikleri kapatır ve ince çizgileri kalınlaştırır.

```python
dilated = cv2.dilate(binary, kernel, iterations=2)
```

---

#### `cv2.morphologyEx(src, op, kernel, iterations=1)`

**Ne yapar:**
Erosion ve dilation kombinasyonlarından oluşan gelişmiş morfolojik işlemleri tek adımda uygular.

**Ne zaman kullanılır:**
Gürültü temizleme ve boşluk kapatma işlemlerini ayrı ayrı `erode`/`dilate` yerine tek adımda yapmak için.

**İşlem türleri:**

| `op` | Formül | Ne Zaman |
|------|--------|---------|
| `MORPH_OPEN` | Erozyon → Dilasyon | Küçük beyaz gürültüyü siler, büyük nesneleri korur |
| `MORPH_CLOSE` | Dilasyon → Erozyon | Nesne içi küçük delikleri kapatır |
| `MORPH_GRADIENT` | Dilasyon - Erozyon | Nesne kenar konturunu verir |
| `MORPH_TOPHAT` | Orijinal - Opening | Parlak küçük detayları vurgular |
| `MORPH_BLACKHAT` | Closing - Orijinal | Koyu küçük detayları vurgular |

```python
# Küçük gürültüyü temizle (opening)
temiz = cv2.morphologyEx(binary, cv2.MORPH_OPEN, kernel, iterations=2)

# Küçük delikleri kapat (closing)
kapali = cv2.morphologyEx(binary, cv2.MORPH_CLOSE, kernel, iterations=2)

# Kenar bulma
kenar = cv2.morphologyEx(binary, cv2.MORPH_GRADIENT, kernel)
```

---

### 🔹 Grup 8 — Kontur Analizi

#### `cv2.findContours(image, mode, method)`

**Ne yapar:**
Binary görüntüdeki nesne sınırlarını (konturları) bulur ve her konturun koordinatlarını döndürür.

**Ne zaman kullanılır:**
Nesne sayma, alan/çevre hesaplama, şekil sınıflandırma ve nesne izleme için temel adımdır.

**Döndürür:** `(contours, hierarchy)` çifti. `contours` her biri NumPy dizisi olan konturlar listesi; `hierarchy` iç içe şekil ilişkileri.

**Parametreler:**
- `mode`:
  - `cv2.RETR_EXTERNAL` — sadece en dış konturlar (çoğu durumda yeterli)
  - `cv2.RETR_TREE` — tüm konturlar, iç içe hiyerarşiyle
  - `cv2.RETR_LIST` — tüm konturlar, hiyerarşi olmadan
- `method`:
  - `cv2.CHAIN_APPROX_SIMPLE` — gereksiz noktaları atar, bellek verimli (önerilen)
  - `cv2.CHAIN_APPROX_NONE` — tüm sınır piksellerini saklar

```python
import cv2

contours, hierarchy = cv2.findContours(
    binary.copy(), cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE
)
print(f"Bulunan nesne sayısı: {len(contours)}")
```

**🔴 Dikkat:** Fonksiyon girdi görüntüyü değiştirebilir; `.copy()` ile kopyasını verin.

---

#### `cv2.drawContours(image, contours, contourIdx, color, thickness)`

**Ne yapar:**
Bulunan konturları bir görüntünün üzerine çizer.

**Parametreler:**
- `contourIdx` — `-1` girilirse tüm konturlar çizilir; belirli bir indeks de verilebilir
- `color` — BGR renk tuple'ı
- `thickness` — çizgi kalınlığı; `-1` içi doldurur

```python
gorsel = img.copy()
cv2.drawContours(gorsel, contours, -1, (0, 255, 0), 2)         # tümü
cv2.drawContours(gorsel, contours, 0,  (0, 0, 255), -1)        # ilki, dolu
```

---

#### `cv2.contourArea(contour)` ve `cv2.arcLength(contour, closed)`

**Ne yapar:**
`contourArea` pikseli cinsinden alanı, `arcLength` çevreyi hesaplar. Bu iki değerden dairesellik metriği türetilebilir.

```python
for cnt in contours:
    alan  = cv2.contourArea(cnt)
    cevre = cv2.arcLength(cnt, closed=True)

    if alan < 500:    # küçük gürültü konturlarını filtrele
        continue

    # Dairesellik (1.0 = mükemmel daire)
    dairesellik = 4 * 3.14159 * alan / (cevre ** 2)
```

---

#### `cv2.boundingRect(contour)` ve `cv2.minAreaRect(contour)`

**Ne yapar:**
`boundingRect` dik açılı minimum sınırlayıcı kutu döndürür. `minAreaRect` nesnenin gerçek açısına göre döndürülmüş minimum sınırlayıcı kutuyu döndürür — nesne eğik duruyor olsa da en küçük kutuyu verir.

```python
# Dik kutu
x, y, w, h = cv2.boundingRect(cnt)
cv2.rectangle(img, (x, y), (x+w, y+h), (255, 0, 0), 2)
en_boy_orani = w / h

# Döndürülmüş kutu
rect  = cv2.minAreaRect(cnt)    # ((cx, cy), (w, h), açı)
box   = cv2.boxPoints(rect)
box   = box.astype(int)
aci   = rect[2]                  # döndürme açısı
cv2.drawContours(img, [box], 0, (0, 0, 255), 2)
```

---

#### `cv2.approxPolyDP(contour, epsilon, closed)` ve `cv2.convexHull(contour)`

**Ne yapar:**
`approxPolyDP`, kontur üzerindeki gereksiz noktaları kaldırarak poligon köşelerini bulur. `convexHull`, konturun dış bükey kabuğunu hesaplar.

**Ne zaman kullanılır:**
Şekil sınıflandırma (üçgen/dikdörtgen/daire), parmak sayma, konveksite analizi.

**Algoritma mantığı (`approxPolyDP`):**
Kontur nokta listesi, gerçek konturdan en fazla `epsilon` piksel uzaklaşacak şekilde sadeleştirilir. `epsilon` büyüdükçe daha az köşe kalır; küçüldükçe orijinal şekle daha yakın kalır.

```python
cevre   = cv2.arcLength(cnt, True)
epsilon = 0.03 * cevre        # çevre uzunluğunun %3'ü tipik değer
approx  = cv2.approxPolyDP(cnt, epsilon, closed=True)
kose    = len(approx)

# Şekil adı
if   kose == 3: sekil = "Ucgen"
elif kose == 4: sekil = "Kare" if 0.9 <= w/h <= 1.1 else "Dikdortgen"
elif kose >= 8 and dairesellik > 0.85: sekil = "Daire"
else:           sekil = f"{kose}-kose"

# Dış bükey kabuk
hull = cv2.convexHull(cnt)
cv2.drawContours(img, [hull], -1, (0, 0, 255), 2)
```

---

#### `cv2.moments(contour)` ve kütle merkezi

**Ne yapar:**
Konturun istatistiksel özelliklerini (alan, kütle merkezi vb.) hesaplar.

**Ne zaman kullanılır:**
Nesnenin merkez koordinatını bulmak, şekil tanıma ve eşleştirme için Hu momentleri.

```python
M  = cv2.moments(cnt)
alan = M["m00"]
if alan > 0:
    cx = int(M["m10"] / M["m00"])   # kütle merkezi x
    cy = int(M["m01"] / M["m00"])   # kütle merkezi y
    cv2.circle(img, (cx, cy), 5, (0, 255, 0), -1)

# Hu momentleri — şekil eşleştirme
hu = cv2.HuMoments(M).flatten()

# İki şeklin benzerliği (0 = aynı)
benzerlik = cv2.matchShapes(cnt1, cnt2, cv2.CONTOURS_MATCH_I1, 0)
```

---

### 🔹 Grup 9 — Görüntü Üzerine Çizim

#### `cv2.rectangle`, `cv2.circle`, `cv2.line`, `cv2.ellipse`, `cv2.putText`

**Ne yapar:**
Görüntü üzerine dikdörtgen, daire, çizgi, elips veya metin ekler. Nesne tespiti sonuçlarını görselleştirmek için kullanılır. Tüm bu fonksiyonlar görüntüyü **yerinde (in-place)** değiştirir.

```python
import cv2

gorsel = img.copy()

# Dikdörtgen: sol üst köşe, sağ alt köşe, renk, kalınlık
cv2.rectangle(gorsel, (50, 50), (200, 150), (0, 255, 0), 2)

# Daire: merkez, yarıçap, renk, kalınlık (-1 doldurur)
cv2.circle(gorsel, (300, 200), 40, (0, 0, 255), -1)

# Çizgi: başlangıç, bitiş, renk, kalınlık
cv2.line(gorsel, (0, 0), (400, 300), (255, 0, 0), 3, lineType=cv2.LINE_AA)

# Elips
cv2.ellipse(gorsel, (400, 200), (80, 40), angle=30, startAngle=0,
            endAngle=360, color=(255, 255, 0), thickness=2)

# Metin
cv2.putText(
    gorsel,
    text="Merhaba",
    org=(50, 400),
    fontFace=cv2.FONT_HERSHEY_SIMPLEX,
    fontScale=1.0,
    color=(255, 255, 255),
    thickness=2,
    lineType=cv2.LINE_AA   # anti-aliased — pürüzsüz
)
```

**`lineType` seçenekleri:** `LINE_4` (hızlı, pürüzlü), `LINE_8` (varsayılan), `LINE_AA` (anti-aliased, önerilen).

**🔴 Dikkat:** Bu fonksiyonlar orijinal görüntüyü değiştirir. Orijinali korumak istiyorsanız `img.copy()` kullanın.

---

### 🔹 Grup 10 — Video ve Kamera

#### `cv2.VideoCapture(source)`

**Ne yapar:**
Kameradan veya video dosyasından görüntü okumak için bir yakalama nesnesi oluşturur. `read()` çağrıldıkça sıradaki kareyi döndürür.

**Ne zaman kullanılır:**
Gerçek zamanlı kamera görüntüsü işlemek veya kayıtlı video dosyalarını kare kare analiz etmek için kullanılır.

**Döndürür:** `cap.read()` → `(ret, frame)` çifti; `ret` başarı durumu, `frame` BGR ndarray.

```python
import cv2

cap = cv2.VideoCapture(0)       # 0 = varsayılan kamera
# cap = cv2.VideoCapture("video.mp4")  # dosya
# cap = cv2.VideoCapture("rtsp://...")  # IP kamera

if not cap.isOpened():
    raise RuntimeError("Kamera açılamadı.")

# Kamera özelliklerini ayarla
cap.set(cv2.CAP_PROP_FRAME_WIDTH,  1280)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 720)

try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break

        cv2.imshow("Kamera", frame)

        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
finally:
    cap.release()
    cv2.destroyAllWindows()
```

**Sık kullanılan `CAP_PROP_*` değerleri:**

| Özellik | Açıklama |
|---------|----------|
| `CAP_PROP_FPS` | Saniyedeki kare sayısı |
| `CAP_PROP_FRAME_WIDTH` | Genişlik |
| `CAP_PROP_FRAME_HEIGHT` | Yükseklik |
| `CAP_PROP_FRAME_COUNT` | Video dosyasındaki toplam kare |
| `CAP_PROP_POS_MSEC` | Mevcut pozisyon (milisaniye) |

**🔴 Dikkat:** `cap.release()` mutlaka çağrılmalıdır. `try-finally` bloğu kullanmak güvenli bir alışkanlıktır.

---

#### `cv2.VideoWriter(filename, fourcc, fps, frameSize)`

**Ne yapar:**
İşlenmiş kareleri video dosyasına yazar.

**Döndürür:** `VideoWriter` nesnesi; `write(frame)` ile kare eklenir.

**Parametreler:**
- `fourcc` — video codec kodu: `cv2.VideoWriter_fourcc(*'mp4v')` veya `*'XVID'`
- `fps` — saniyedeki kare sayısı
- `frameSize` — `(genişlik, yükseklik)` tuple'ı

```python
fourcc = cv2.VideoWriter_fourcc(*'mp4v')
out    = cv2.VideoWriter('cikti.mp4', fourcc, 30.0, (640, 480))

# Döngü içinde
out.write(frame)

out.release()
```

---

#### Arka Plan Çıkarma (Background Subtraction)

**Ne yapar:**
Sabit kamera videosunda hareketli nesneleri tespit eder. Zamanla arka planı öğrenir; mevcut kare arka plan modelinden farklıysa o piksel ön plan (hareket) sayılır.

**Algoritma mantığı (MOG2):**
Her piksel için birden fazla Gaussian dağılımı modelini takip eder. Rüzgarda sallanan yaprak veya dalgalanan su gibi tekrar eden dinamik arka planlar zamanla "arka plan" olarak öğrenilir; gerçek hareketli nesneler ön plan olarak kalır.

```python
mog2 = cv2.createBackgroundSubtractorMOG2(
    history=500,         # kaç kare geçmiş tutulur
    varThreshold=16,
    detectShadows=True   # gölgeler gri (127) olarak işaretlenir
)

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break

    fg_mask = mog2.apply(frame)

    # Gölgeleri temizle (127 = gölge, 255 = ön plan)
    _, fg_binary = cv2.threshold(fg_mask, 200, 255, cv2.THRESH_BINARY)

    # Morfoloji ile gürültü temizle
    kernel   = cv2.getStructuringElement(cv2.MORPH_ELLIPSE, (5, 5))
    fg_clean = cv2.morphologyEx(fg_binary, cv2.MORPH_OPEN, kernel)

    cv2.imshow("On Plan", fg_clean)
    if cv2.waitKey(30) & 0xFF == ord('q'):
        break
```

---

#### Optik Akış — Lucas-Kanade

**Ne yapar:**
Seçilen anahtar noktalarda kareden kareye hareketi takip eder; her noktanın hareket vektörünü hesaplar.

**Algoritma mantığı:**
Bir noktanın parlaklığının kareler arası sabit kaldığı varsayılır. Bu kısıt küçük bir penceredeki piksel bilgisiyle birleştirilince her noktanın yatay ve dikey hareket miktarı hesaplanabilir.

```python
# İlk karede iyi köşeleri bul
p0 = cv2.goodFeaturesToTrack(gray_prev, maxCorners=200,
                              qualityLevel=0.3, minDistance=7)

# Sonraki karede noktaları takip et
p1, status, _ = cv2.calcOpticalFlowPyrLK(
    gray_prev, gray_curr, p0, None,
    winSize=(15, 15), maxLevel=2,
    criteria=(cv2.TERM_CRITERIA_EPS | cv2.TERM_CRITERIA_COUNT, 10, 0.03)
)

# Başarıyla takip edilen noktalar
iyi_yeni = p1[status == 1]
iyi_eski = p0[status == 1]

# Hareket çizgilerini çiz
for (yeni, eski) in zip(iyi_yeni, iyi_eski):
    a, b = yeni.ravel().astype(int)
    c, d = eski.ravel().astype(int)
    cv2.line(frame, (a, b), (c, d), (0, 255, 0), 2)
    cv2.circle(frame, (a, b), 3, (0, 0, 255), -1)
```

---

### 🔹 Grup 11 — Öznitelik Tespiti

Bu grup `opencv-contrib-python` gerektirir.

#### FAST, ORB ve SIFT

**Ne yapar:**
Görüntüde farklı açı, ölçek ve aydınlatmada tekrar bulunabilen ayırt edici noktaları (anahtar noktaları) ve onların tanımlayıcılarını hesaplar. İki görüntüyü eşleştirme, nesne tanıma ve görüntü hizalamada kullanılır.

**Algoritma mantığı (kısaca):**
- **FAST:** Bir noktanın çevresindeki 16-piksellik çembere bakarak yeterince parlak veya koyu ardışık piksel varsa köşe sayar; gerçek zamanlı için son derece hızlıdır.
- **ORB:** FAST ile noktaları bulur, her noktanın açısını hesaplar ve bu açıya göre döndürme değişmez binary bir tanımlayıcı üretir. Ücretsiz ve hızlı.
- **SIFT:** Görüntüyü farklı bulanıklık seviyelerinde işleyerek ölçekten bağımsız noktalar bulur; her nokta için 128 boyutlu bir vektör tanımlayıcı hesaplar. Daha yavaş ama daha gürbüz.

```python
# FAST — yalnızca tespit
fast = cv2.FastFeatureDetector_create(threshold=10)
kp   = fast.detect(gray, None)
img_kp = cv2.drawKeypoints(img, kp, None, color=(0, 255, 0))

# ORB — tespit + tanımlayıcı
orb      = cv2.ORB_create(nfeatures=500)
kp, des  = orb.detectAndCompute(gray, None)
# des: (N, 32) uint8 — Hamming mesafesiyle eşleştirme

# SIFT — tespit + tanımlayıcı (OpenCV 4.4+)
sift     = cv2.SIFT_create()
kp, des  = sift.detectAndCompute(gray, None)
# des: (N, 128) float32 — L2 mesafesiyle eşleştirme

# İki görüntüyü ORB ile eşleştir
bf       = cv2.BFMatcher(cv2.NORM_HAMMING, crossCheck=True)
matches  = bf.match(des1, des2)
matches  = sorted(matches, key=lambda x: x.distance)
img_eslesme = cv2.drawMatches(img1, kp1, img2, kp2, matches[:50], None)
```

---

### 🔹 Grup 12 — Histogram

#### `cv2.calcHist(images, channels, mask, histSize, ranges)`

**Ne yapar:**
Görüntünün piksel yoğunluk dağılımını hesaplar; her yoğunluk değerinde kaç piksel olduğunu gösteren bir dizi döndürür.

**Ne zaman kullanılır:**
Görüntü analizi, parlaklık/kontrast değerlendirmesi, renk dağılımı inceleme, eşik değeri seçiminde rehber olarak.

**Döndürür:** `(256, 1)` şeklinde float32 dizi.

```python
import cv2
import matplotlib.pyplot as plt

gray = cv2.cvtColor(cv2.imread("foto.jpg"), cv2.COLOR_BGR2GRAY)
hist = cv2.calcHist([gray], [0], None, [256], [0, 256])

plt.plot(hist)
plt.title("Gri Histogram")
plt.show()

# BGR kanalları için ayrı ayrı
for i, renk in enumerate(['b', 'g', 'r']):
    hist = cv2.calcHist([img], [i], None, [256], [0, 256])
    plt.plot(hist, color=renk)
```

---

#### `cv2.equalizeHist(src)` ve `cv2.createCLAHE()`

**Ne yapar:**
`equalizeHist`, histogram eşitleme uygulayarak görüntünün kontrastını artırır; piksel dağılımını 0-255 aralığına dengeli yayar. `createCLAHE` görüntüyü küçük kutucuklara bölerek her kutucukta bağımsız histogram eşitleme uygular ve aşırı açılmayı sınırlar.

**Algoritma mantığı:**
`equalizeHist` tüm görüntüyü tek seferde normalize eder; bazı bölgeler aşırı açılabilir. CLAHE ise yerel kontrast iyileştirmesi yaptığından düzensiz aydınlatmalı görüntülerde çok daha doğal sonuç üretir.

```python
# Basit histogram eşitleme
esitlenmis = cv2.equalizeHist(gray)

# CLAHE (önerilen — daha doğal)
clahe          = cv2.createCLAHE(clipLimit=2.0, tileGridSize=(8, 8))
clahe_sonuc    = clahe.apply(gray)

# Renkli görüntüde CLAHE (yalnızca parlaklık kanalına uygula)
lab     = cv2.cvtColor(img, cv2.COLOR_BGR2LAB)
l, a, b = cv2.split(lab)
l_clahe = clahe.apply(l)
sonuc   = cv2.cvtColor(cv2.merge([l_clahe, a, b]), cv2.COLOR_LAB2BGR)
```

---

## BLOK 4 — Sık Karşılaşılan Hatalar

---

### Hata 1 — imread `None` Döndürüyor

**Belirti:** Kod hata vermeden çalışıyor ama işlemler yanlış sonuç üretiyor.
**Neden:** `imread`, dosyayı bulamazsa exception fırlatmak yerine `None` döndürür.

```python
# ❌ Yanlış
img  = cv2.imread("foto.jpg")
gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)   # None üzerinde → CRASH

# ✅ Doğru
img = cv2.imread("foto.jpg")
if img is None:
    raise FileNotFoundError("Dosya bulunamadı: foto.jpg")
```

---

### Hata 2 — BGR / RGB Karışıklığı

**Belirti:** Matplotlib'de görüntü kırmızı-mavi ters görünüyor; modele yanlış renk verisi gidiyor.

```python
# ❌ Yanlış
plt.imshow(img)   # kırmızı-mavi ters!

# ✅ Doğru
plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))

# PyTorch / TensorFlow'a geçişte
img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
```

---

### Hata 3 — `waitKey()` Unutmak

**Belirti:** `imshow` penceresi açılıp hemen kapanıyor.

```python
# ❌ Yanlış
cv2.imshow("Goruntu", img)

# ✅ Doğru
cv2.imshow("Goruntu", img)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

### Hata 4 — Canny'ye Renkli Görüntü Vermek

**Belirti:** `cv2.error: ... (image.channels() == 1)`

```python
# ❌ Yanlış
edges = cv2.Canny(img, 50, 150)

# ✅ Doğru
gray  = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
edges = cv2.Canny(gray, 50, 150)
```

---

### Hata 5 — Çift Kernel Boyutu

**Belirti:** `cv2.error: Odd kernel size expected.`

```python
# ❌ Çift sayı hata verir
cv2.GaussianBlur(img, (4, 4), 0)
cv2.medianBlur(img, 4)

# ✅ Doğru: tek sayı
cv2.GaussianBlur(img, (5, 5), 0)
cv2.medianBlur(img, 5)
```

---

### Hata 6 — resize'da Boyut Sırası Karışıklığı

**Belirti:** Döndürülmüş veya yamuk görüntü.

```python
# ❌ Yanlış: shape (h, w) ama resize (w, h) ister
cv2.resize(img, img.shape[:2])   # (h, w) → yanlış!

# ✅ Doğru
h, w = img.shape[:2]
cv2.resize(img, (w, h))
```

---

### Hata 7 — uint8 Taşması

**Belirti:** Parlaklık artırınca bazı alanlar siyaha dönüyor.

```python
# ❌ uint8 taşması
img_parlak = img + 100   # 200 + 100 = 300 → 44 (taşma!)

# ✅ cv2.add — doyma aritmetiği
img_parlak = cv2.add(img, np.full_like(img, 100))

# ✅ float32 + clip
img_parlak = np.clip(img.astype(np.float32) + 100, 0, 255).astype(np.uint8)
```

---

### Hata 8 — VideoCapture Serbest Bırakmamak

**Belirti:** Sonraki çalıştırmada "kamera açılamadı" hatası.

```python
# ✅ Her zaman try-finally kullanın
cap = cv2.VideoCapture(0)
try:
    while cap.isOpened():
        ret, frame = cap.read()
        if not ret:
            break
        cv2.imshow("Kamera", frame)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break
finally:
    cap.release()
    cv2.destroyAllWindows()
```

---

### Hata 9 — findContours Orijinali Değiştiriyor

**Belirti:** `findContours` sonrası binary görüntü bozulmuş.

```python
# ❌ Dikkat: orijinal değişebilir
contours, _ = cv2.findContours(binary, cv2.RETR_EXTERNAL,
                                cv2.CHAIN_APPROX_SIMPLE)

# ✅ Kopyası üzerinde çalış
contours, _ = cv2.findContours(binary.copy(), cv2.RETR_EXTERNAL,
                                cv2.CHAIN_APPROX_SIMPLE)
```

---

### Hata 10 — Sobel için `CV_64F` Kullanmamak

**Belirti:** Görüntünün bir tarafındaki kenarlar görünmüyor.

```python
# ❌ uint8 ile negatif gradyanlar sıfıra kırpılır
sobel_x = cv2.Sobel(gray, cv2.CV_8U, 1, 0)

# ✅ Doğru
sobel_x = cv2.Sobel(gray, cv2.CV_64F, 1, 0)
sobel_x_abs = cv2.convertScaleAbs(sobel_x)   # görselleştirme için uint8'e dön
```

---

### Hata 11 — Renk Maskelemede HSV Aralık Hatası

**Belirti:** Yanlış pikseller maskeleniyor ya da renk hiç yakalanmıyor.

```python
# ❌ BGR ile renk maskeleme — güvenilmez
mask = cv2.inRange(img, np.array([0, 0, 100]), np.array([50, 50, 255]))

# ✅ HSV ile renk maskeleme — aydınlatmadan bağımsız
hsv    = cv2.cvtColor(img, cv2.COLOR_BGR2HSV)
lower  = np.array([0,   100, 100])
upper  = np.array([10,  255, 255])
mask   = cv2.inRange(hsv, lower, upper)

# Kırmızı H değeri 0 ve 170-180 civarında ikiye bölündüğünden iki maske gerekir
lower2 = np.array([160, 100, 100])
upper2 = np.array([180, 255, 255])
mask   = cv2.bitwise_or(mask, cv2.inRange(hsv, lower2, upper2))
```

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `opencv-uygulamalar.md` · `opencv-alistirmalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
