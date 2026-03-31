# 🛠️ YOLO — Uygulamalar

> **Modül:** Uygulamalar | **Seviye:** Başlangıç → Orta → İleri
> **İlgili dosyalar:** `yolo-teori.md`
> **Ekosistem:** Ultralytics YOLOv11

---

## BLOK 0 — Uygulama Belgesi Hakkında

Bu belge, `yolo-teori.md` dosyasında öğrenilen kavramları **gerçek, çalışır projelerde** nasıl kullanacağınızı gösterir. Her uygulama belirli bir problemi çözer; kod sade, yorumlu ve çalıştırılabilir biçimde sunulmuştur.

**Belgede neler var:**

| Blok | Konu |
|------|------|
| Blok 1 | Hazır model ile tek görüntüde detection |
| Blok 2 | Klasördeki görseller üzerinde toplu detection |
| Blok 3 | Video üzerinde detection |
| Blok 4 | Webcam ile gerçek zamanlı detection |
| Blok 5 | Kendi datasetin ile eğitim |
| Blok 6 | Sonuçları analiz etme ve görselleştirme |
| Blok 7 | Model karşılaştırma |
| Blok 8 | Mini projeler |

**Kurulum:**
```bash
pip install ultralytics opencv-python matplotlib pandas
```

**Not:** Örneklerin çoğu hazır COCO modeli (`yolo11n.pt`) kullanır. Kendi sınıflarınız için Blok 5'teki eğitim adımını izleyin.

---

## BLOK 1 — Hazır Model ile Görüntü Üzerinde Detection

---

### Uygulama 1.1 — İlk Tespit: Temel Kullanım

**Problem:** Bir fotoğraftaki nesneleri tespit edin ve terminale konsol çıktısı olarak yazdırın.

**Amaç:** YOLO API'sinin temel yapısını ve model çıktısını anlamak.

**Pipeline:**
```
Görüntü dosyası → Model yükle → Predict → Sonuçları oku
```

**Kod:**
```python
from ultralytics import YOLO

# Model yükle (ilk çalıştırmada otomatik indirilir)
model = YOLO("yolo11n.pt")

# Tahmin yap
results = model.predict("goruntu.jpg", conf=0.5)

# Sonuçları oku
for r in results:
    print(f"Görüntü: {r.path}")
    print(f"Tespit sayısı: {len(r.boxes)}")
    print("-" * 40)

    for box in r.boxes:
        sinif_id  = int(box.cls)
        sinif_adi = r.names[sinif_id]
        guven     = float(box.conf)
        x1, y1, x2, y2 = box.xyxy[0].tolist()

        print(f"  {sinif_adi:15s} | güven: {guven:.2f} | "
              f"konum: [{x1:.0f}, {y1:.0f}, {x2:.0f}, {y2:.0f}]")
```

**Açıklama:**
`model.predict()` bir veya daha fazla `Results` nesnesi döndürür. Her `Results` nesnesinde `.boxes` içinde tüm tespitler bulunur. `r.names` sınıf ID'lerini isimlere eşleyen sözlüktür.

**Beklenen çıktı:**
```
Görüntü: goruntu.jpg
Tespit sayısı: 3
────────────────────────────────────────
  person          | güven: 0.92 | konum: [120, 80, 300, 260]
  car             | güven: 0.87 | konum: [400, 90, 620, 350]
  person          | güven: 0.74 | konum: [50, 20, 180, 200]
```

**İyileştirme fikirleri:**
- `conf=0.3` ile daha düşük güvenli tespitleri de görün.
- `classes=[0]` ile yalnızca `person` sınıfını filtreleyin.

---

### Uygulama 1.2 — Görselleştirme: Tespitleri Çizme

**Problem:** Tespit sonuçlarını görüntü üzerine çizerek kaydedin ve ekranda gösterin.

**Amaç:** `results.plot()` ve OpenCV entegrasyonunu öğrenmek.

**Kod:**
```python
import cv2
from ultralytics import YOLO

model   = YOLO("yolo11n.pt")
results = model.predict("goruntu.jpg", conf=0.5)

# Yöntem 1: plot() ile otomatik görselleştirme
annotated = results[0].plot()   # numpy array (BGR)
cv2.imwrite("tespit_sonucu.jpg", annotated)
cv2.imshow("Tespitler", annotated)
cv2.waitKey(0)
cv2.destroyAllWindows()

# Yöntem 2: Manuel çizim (tam kontrol)
import cv2

goruntu = cv2.imread("goruntu.jpg")
r       = results[0]

for box in r.boxes:
    x1, y1, x2, y2 = map(int, box.xyxy[0].tolist())
    sinif_id  = int(box.cls)
    sinif_adi = r.names[sinif_id]
    guven     = float(box.conf)
    etiket    = f"{sinif_adi} {guven:.2f}"

    # Kutu çiz
    cv2.rectangle(goruntu, (x1, y1), (x2, y2), (0, 255, 0), 2)

    # Etiket arka planı
    (metin_w, metin_h), _ = cv2.getTextSize(
        etiket, cv2.FONT_HERSHEY_SIMPLEX, 0.6, 1)
    cv2.rectangle(goruntu, (x1, y1 - metin_h - 8), (x1 + metin_w, y1), (0, 255, 0), -1)

    # Etiket metni
    cv2.putText(goruntu, etiket, (x1, y1 - 4),
                cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 0, 0), 1)

cv2.imwrite("manuel_tespit.jpg", goruntu)
```

**Açıklama:**
`results[0].plot()` en hızlı görselleştirme yöntemidir. Manuel çizim yöntemi kutu rengi, metin stili ve ek bilgiler ekleme konusunda tam esneklik sağlar.

**Beklenen çıktı:**
Görüntü üzerinde yeşil bounding box'lar, sınıf adı ve güven skoru etiketleri.

**İyileştirme fikirleri:**
- Her sınıf için farklı renk atayın.
- Tespit sayısını sol üst köşeye yazın.

---

### Uygulama 1.3 — Confidence Threshold Deneyi

**Problem:** Farklı confidence threshold değerlerinin tespit sonuçlarını nasıl etkilediğini karşılaştırın.

**Amaç:** Threshold seçiminin etkisini gözlemleyerek uygulama için doğru değeri seçme yeteneği kazanmak.

**Kod:**
```python
from ultralytics import YOLO
import cv2
import numpy as np

model    = YOLO("yolo11n.pt")
goruntu  = cv2.imread("goruntu.jpg")
BOYUT    = (400, 300)
esikler  = [0.1, 0.3, 0.5, 0.7, 0.9]
paneller = []

for esik in esikler:
    results   = model.predict(goruntu, conf=esik, verbose=False)
    annotated = results[0].plot()
    annotated = cv2.resize(annotated, BOYUT)

    n_tespit = len(results[0].boxes)
    cv2.putText(annotated, f"conf={esik}  |  {n_tespit} tespit",
                (8, 26), cv2.FONT_HERSHEY_SIMPLEX, 0.65, (0, 255, 255), 2)
    paneller.append(annotated)

# 1×5 panel
grid = np.hstack(paneller)
cv2.imshow("Threshold Karsilastirma", grid)
cv2.imwrite("threshold_karsilastirma.jpg", grid)
cv2.waitKey(0)
cv2.destroyAllWindows()

# Sayısal karşılaştırma
print(f"\n{'Threshold':>12} {'Tespit Sayisi':>15} {'Ort Güven':>12}")
print("-" * 42)
for esik in esikler:
    r = model.predict(goruntu, conf=esik, verbose=False)[0]
    if len(r.boxes) > 0:
        ort_guven = float(r.boxes.conf.mean())
    else:
        ort_guven = 0.0
    print(f"{esik:>12.2f} {len(r.boxes):>15} {ort_guven:>12.3f}")
```

**Açıklama:**
Düşük threshold → Daha fazla tespit, ortalama güven düşer, yanlış pozitifler artar. Yüksek threshold → Az tespit, yüksek güven, bazı gerçek nesneler kaçabilir. Bu deneyi kendi verilerinizde çalıştırarak uygulamanız için doğru eşiği belirleyin.

**Beklenen çıktı:**
5 panelli karşılaştırma görüntüsü ve sayısal tablo.

---

## BLOK 2 — Klasördeki Görseller Üzerinde Toplu Detection

---

### Uygulama 2.1 — Batch Görüntü İşleme

**Problem:** Bir klasördeki tüm görüntüleri YOLO ile işleyin; sonuçları kaydedin ve özet rapor oluşturun.

**Amaç:** Toplu inference ve sonuç kaydetme pipeline'ını öğrenmek.

**Pipeline:**
```
Klasör → Görüntü listesi → Batch predict → Sonuçları kaydet → Özet rapor
```

**Kod:**
```python
from ultralytics import YOLO
from pathlib import Path
import pandas as pd
import cv2

model      = YOLO("yolo11n.pt")
KAYNAK_DIR = Path("goruntular")
CIKTI_DIR  = Path("tespitler")
CIKTI_DIR.mkdir(exist_ok=True)

# Tüm görüntü dosyaları
goruntu_listesi = list(KAYNAK_DIR.glob("*.jpg")) + \
                  list(KAYNAK_DIR.glob("*.png"))
print(f"{len(goruntu_listesi)} görüntü işlenecek...")

# Tüm sonuçları topla
tum_sonuclar = []

for goruntu_yolu in goruntu_listesi:
    results = model.predict(str(goruntu_yolu), conf=0.5, verbose=False)
    r       = results[0]

    for box in r.boxes:
        tum_sonuclar.append({
            "dosya"      : goruntu_yolu.name,
            "sinif"      : r.names[int(box.cls)],
            "guven"      : round(float(box.conf), 3),
            "x1"         : int(box.xyxy[0][0]),
            "y1"         : int(box.xyxy[0][1]),
            "x2"         : int(box.xyxy[0][2]),
            "y2"         : int(box.xyxy[0][3]),
        })

    # Annotasyonlu görüntü kaydet
    if len(r.boxes) > 0:
        cikti_yolu = CIKTI_DIR / goruntu_yolu.name
        cv2.imwrite(str(cikti_yolu), r.plot())

# Sonuçları DataFrame'e çevir
df = pd.DataFrame(tum_sonuclar)
df.to_csv("tespit_sonuclari.csv", index=False)

# Özet rapor
print("\n=== ÖZET RAPOR ===")
print(f"Toplam işlenen görüntü : {len(goruntu_listesi)}")
print(f"Toplam tespit          : {len(df)}")
if not df.empty:
    print(f"\nSınıf bazlı dağılım:")
    print(df["sinif"].value_counts().to_string())
    print(f"\nOrtalama güven: {df['guven'].mean():.3f}")
```

**Açıklama:**
Batch işlemede her görüntü ayrı `predict` çağrısıyla işlenir; Ultralytics tüm listeyi de tek seferde alabiliyor (`model.predict(list)`) ancak büyük listeler için bellek yönetimi açısından tek tek işlemek daha güvenlidir. Sonuçlar Pandas ile kolayca analiz edilebilir.

**Beklenen çıktı:**
`tespitler/` klasöründe annotasyonlu görüntüler, `tespit_sonuclari.csv` ve konsol özet.

**İyileştirme fikirleri:**
- Sınıf bazlı filtreleme yapın (`classes=[0]`).
- Güven dağılımını Matplotlib ile görselleştirin.

---

### Uygulama 2.2 — Sonuç Filtreleme ve Seçici Tespit

**Problem:** Yalnızca belirli sınıfları, belirli minimum boyuttaki nesneleri veya belirli bir bölgedeki nesneleri filtreleyin.

**Amaç:** YOLO çıktısını özel kriterlere göre post-process etmeyi öğrenmek.

**Kod:**
```python
from ultralytics import YOLO
import numpy as np
import cv2

model   = YOLO("yolo11n.pt")
goruntu = cv2.imread("goruntu.jpg")
h, w    = goruntu.shape[:2]

results = model.predict(goruntu, conf=0.3, verbose=False)
r       = results[0]

# ─── Filtre 1: Yalnızca kişi (class 0) ─────────────────────────────────
kisi_kutu = r.boxes[r.boxes.cls == 0]
print(f"Toplam tespit: {len(r.boxes)} | Yalnızca kişi: {len(kisi_kutu)}")

# ─── Filtre 2: Minimum alan filtresi ────────────────────────────────────
MIN_ALAN = 0.01 * (w * h)   # Görüntünün en az %1'ini kaplasın

buyuk_kutular = []
for box in r.boxes:
    x1, y1, x2, y2 = map(int, box.xyxy[0].tolist())
    alan = (x2 - x1) * (y2 - y1)
    if alan >= MIN_ALAN:
        buyuk_kutular.append(box)

print(f"Minimum alan filtresi sonrası: {len(buyuk_kutular)}")

# ─── Filtre 3: Belirli bölgedeki nesneler (ROI) ──────────────────────────
# Görüntünün sağ yarısındaki nesneleri bul
ROI_X_BASLANGIC = w // 2

roi_kutular = []
for box in r.boxes:
    x1 = int(box.xyxy[0][0])
    if x1 >= ROI_X_BASLANGIC:
        roi_kutular.append(box)

print(f"Sağ yarıdaki nesneler: {len(roi_kutular)}")

# Filtrelenmiş sonucu görselleştir
gorsel = goruntu.copy()
for box in roi_kutular:
    x1, y1, x2, y2 = map(int, box.xyxy[0].tolist())
    cv2.rectangle(gorsel, (x1, y1), (x2, y2), (0, 255, 0), 2)

# ROI sınırını çiz
cv2.line(gorsel, (ROI_X_BASLANGIC, 0), (ROI_X_BASLANGIC, h), (255, 0, 0), 2)
cv2.imshow("ROI Filtreleme", gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
YOLO çıktısı zengin bir veri kaynağıdır; yalnızca modeli çalıştırmak değil, çıktıyı akıllıca filtrelemek uygulamayı kullanışlı kılar. Sınıf filtresi, alan filtresi ve ROI filtresi en yaygın post-process adımlarıdır.

---

## BLOK 3 — Video Üzerinde Detection

---

### Uygulama 3.1 — Video Dosyasında Detection

**Problem:** Bir video dosyasındaki her kareyi YOLO ile işleyin; annotasyonlu videoyu kaydedin.

**Amaç:** Video döngüsü ve VideoWriter kullanımını öğrenmek.

**Pipeline:**
```
Video dosyası → OpenCV ile kare kare oku → Her kareye predict → Yaz → Kaydet
```

**Kod:**
```python
import cv2
from ultralytics import YOLO

model    = YOLO("yolo11n.pt")
VIDEO_YL = "ornek_video.mp4"
CIKTI_YL = "tespit_video.mp4"

cap   = cv2.VideoCapture(VIDEO_YL)
fps   = int(cap.get(cv2.CAP_PROP_FPS))
genislik = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
yukseklik = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
toplam_kare = int(cap.get(cv2.CAP_PROP_FRAME_COUNT))

# Video yazıcı
fourcc = cv2.VideoWriter_fourcc(*"mp4v")
out    = cv2.VideoWriter(CIKTI_YL, fourcc, fps, (genislik, yukseklik))

print(f"Video: {genislik}×{yukseklik}, {fps} FPS, {toplam_kare} kare")

kare_no = 0
while cap.isOpened():
    ret, kare = cap.read()
    if not ret:
        break

    kare_no += 1

    # Her kareyi işle
    results   = model.predict(kare, conf=0.5, verbose=False)
    annotated = results[0].plot()

    # İlerleme bilgisi ekle
    n_tespit = len(results[0].boxes)
    cv2.putText(annotated,
                f"Kare: {kare_no}/{toplam_kare} | Tespit: {n_tespit}",
                (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 255), 2)

    out.write(annotated)

    if kare_no % 30 == 0:
        print(f"  İlerleme: {kare_no}/{toplam_kare} ({kare_no/toplam_kare*100:.0f}%)")

cap.release()
out.release()
print(f"\nKaydedildi: {CIKTI_YL}")
```

**Açıklama:**
Video işlemede her kare için ayrı `predict` çağrısı yapılır. `VideoWriter` aynı codec ve boyutu koruyarak çıktıyı kaydeder. Büyük videolarda `verbose=False` konsol çıktısını kapatarak hızı artırır.

**Beklenen çıktı:**
Her karede nesnelerin işaretlendiği annotasyonlu video dosyası.

**İyileştirme fikirleri:**
- Her `N` karede bir işleyin (`kare_no % 3 == 0`) — daha hızlı.
- FPS sayacı ekleyin.
- Tespit sayısını zaman bazlı grafikle kaydedin.

---

### Uygulama 3.2 — Video Üzerinde Nesne Sayma

**Problem:** Bir trafik videosunda araç sayısını sayın ve sınıf bazlı istatistik üretin.

**Amaç:** Detection çıktısını istatistiksel analize dönüştürmeyi öğrenmek.

**Kod:**
```python
import cv2
from ultralytics import YOLO
from collections import defaultdict
import matplotlib.pyplot as plt

model    = YOLO("yolo11n.pt")
cap      = cv2.VideoCapture("trafik.mp4")

# Sınıf bazlı tespit sayacı
tespit_sayaci   = defaultdict(int)    # toplam tespit
kare_basi_sayi  = []                  # her karede kaç nesne

kare_no = 0
while cap.isOpened():
    ret, kare = cap.read()
    if not ret:
        break

    kare_no += 1
    results = model.predict(kare, conf=0.5, verbose=False,
                             classes=[2, 3, 5, 7])  # araç sınıfları (COCO)
    # COCO araç sınıfları: 2=car, 3=motorcycle, 5=bus, 7=truck

    kare_sayisi = 0
    for box in results[0].boxes:
        sinif = results[0].names[int(box.cls)]
        tespit_sayaci[sinif] += 1
        kare_sayisi += 1

    kare_basi_sayi.append(kare_sayisi)

cap.release()

# Özet rapor
print("=== TRAFİK ANALİZİ ===")
print(f"İşlenen kare: {kare_no}")
print(f"\nSınıf bazlı toplam tespit:")
for sinif, sayi in sorted(tespit_sayaci.items(), key=lambda x: -x[1]):
    print(f"  {sinif:15s}: {sayi:6d}")

print(f"\nOrtalama anlık araç: {sum(kare_basi_sayi)/len(kare_basi_sayi):.1f}")
print(f"Max anlık araç     : {max(kare_basi_sayi)}")

# Zaman grafik
plt.figure(figsize=(12, 4))
plt.plot(kare_basi_sayi, color='steelblue', lw=1.5)
plt.axhline(sum(kare_basi_sayi)/len(kare_basi_sayi),
            color='red', ls='--', label='Ortalama')
plt.title("Kare Bazlı Araç Sayısı")
plt.xlabel("Kare Numarası")
plt.ylabel("Araç Sayısı")
plt.legend()
plt.tight_layout()
plt.savefig("trafik_grafik.png", dpi=120)
plt.show()
```

**Açıklama:**
`classes=[2, 3, 5, 7]` COCO sınıf indekslerini filtreler; yalnızca araç türlerini tespit eder. `defaultdict(int)` her sınıf için otomatik sıfır başlangıç sayacı oluşturur.

---

## BLOK 4 — Webcam ile Gerçek Zamanlı Detection

---

### Uygulama 4.1 — Temel Webcam Detection

**Problem:** Bilgisayar kamerasından canlı görüntü alarak gerçek zamanlı nesne tespiti yapın.

**Amaç:** Gerçek zamanlı inference döngüsünü ve FPS hesaplamayı öğrenmek.

**Kod:**
```python
import cv2
import time
from ultralytics import YOLO

model = YOLO("yolo11n.pt")
cap   = cv2.VideoCapture(0)   # 0: varsayılan kamera

# Kamera çözünürlüğü ayarla
cap.set(cv2.CAP_PROP_FRAME_WIDTH,  640)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 480)

fps_sayac = 0
fps_sure  = time.time()
fps_goster = 0.0

print("Başlatıldı — çıkmak için 'q' tuşuna basın")

while cap.isOpened():
    ret, kare = cap.read()
    if not ret:
        print("Kamera okunamadı.")
        break

    # Tahmin
    results   = model.predict(kare, conf=0.5, verbose=False)
    annotated = results[0].plot()

    # FPS hesapla (her saniye güncelle)
    fps_sayac += 1
    if time.time() - fps_sure >= 1.0:
        fps_goster = fps_sayac / (time.time() - fps_sure)
        fps_sayac  = 0
        fps_sure   = time.time()

    # FPS ve tespit sayısı ekle
    n_tespit = len(results[0].boxes)
    cv2.putText(annotated, f"FPS: {fps_goster:.1f}", (10, 30),
                cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 0), 2)
    cv2.putText(annotated, f"Nesne: {n_tespit}", (10, 60),
                cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 255), 2)

    cv2.imshow("YOLOv11 Gercek Zamanli", annotated)

    tus = cv2.waitKey(1) & 0xFF
    if tus == ord("q"):
        break
    elif tus == ord("s"):   # 's' ile anlık kayıt
        cv2.imwrite(f"ekran_goruntusu_{fps_sayac}.jpg", annotated)
        print("Kaydedildi.")

cap.release()
cv2.destroyAllWindows()
```

**Açıklama:**
`cv2.VideoCapture(0)` varsayılan kamerayı açar. `verbose=False` her karede konsol çıktısını engeller. FPS hesabı için sistem saati kullanılır. `'s'` tuşuyla anlık görüntü almak pratikte sıklıkla kullanılır.

**Beklenen çıktı:**
Kamera görüntüsü üzerinde anlık tespitler, FPS sayacı.

---

### Uygulama 4.2 — Nesne Takipli Gerçek Zamanlı Sistem

**Problem:** Kamerada görünen nesneleri tespit edip takip edin; her nesneye ID atayın ve hareketi izleyin.

**Amaç:** YOLO Track API'sini öğrenmek ve ID-bazlı nesne takibini anlamak.

**Pipeline:**
```
Kamera → Her kare → track(persist=True) → ID ata → Hareket izi çiz
```

**Kod:**
```python
import cv2
from ultralytics import YOLO
from collections import defaultdict
import numpy as np

model = YOLO("yolo11n.pt")
cap   = cv2.VideoCapture(0)

# Her ID için iz koordinatları
izler = defaultdict(list)
IZ_UZUNLUGU = 30   # Kaç kare geriye iz çizilsin

while cap.isOpened():
    ret, kare = cap.read()
    if not ret:
        break

    # Takip başlat (persist=True kritik — ID sürekliliği sağlar)
    results = model.track(kare, persist=True, conf=0.5, verbose=False)
    r       = results[0]

    annotated = kare.copy()

    if r.boxes.id is not None:
        kutular  = r.boxes.xyxy.cpu().numpy()
        id_listesi = r.boxes.id.cpu().numpy().astype(int)
        sinif_listesi = r.boxes.cls.cpu().numpy().astype(int)

        for kutu, obj_id, sinif_id in zip(kutular, id_listesi, sinif_listesi):
            x1, y1, x2, y2 = map(int, kutu)
            merkez_x = (x1 + x2) // 2
            merkez_y = (y1 + y2) // 2

            # İzi güncelle
            izler[obj_id].append((merkez_x, merkez_y))
            if len(izler[obj_id]) > IZ_UZUNLUGU:
                izler[obj_id].pop(0)

            # Kutu ve ID çiz
            renk = tuple(int(c) for c in np.random.RandomState(obj_id).randint(50, 255, 3))
            cv2.rectangle(annotated, (x1, y1), (x2, y2), renk, 2)
            cv2.putText(annotated, f"ID:{obj_id} {r.names[sinif_id]}",
                        (x1, y1 - 8), cv2.FONT_HERSHEY_SIMPLEX, 0.6, renk, 2)
            cv2.circle(annotated, (merkez_x, merkez_y), 4, renk, -1)

            # Hareket izi çiz
            for i in range(1, len(izler[obj_id])):
                kalinlik = int(i / len(izler[obj_id]) * 3) + 1
                cv2.line(annotated, izler[obj_id][i-1], izler[obj_id][i], renk, kalinlik)

    cv2.imshow("Nesne Takibi", annotated)
    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
```

**Açıklama:**
`persist=True` ID'lerin kareler arasında korunmasını sağlar. Her `obj_id` için benzersiz renk `np.random.RandomState(obj_id)` ile tutarlı biçimde üretilir. İz kalınlığı zamanla ince → kalın şeklinde değişerek hareketin yönünü görsel olarak anlatır.

**Beklenen çıktı:**
Her nesne farklı renkte izlenmiş, hareket izleri çizilmiş görüntü.

---

## BLOK 5 — Kendi Datasetin ile Eğitim

---

### Uygulama 5.1 — Dataset Hazırlama Scripti

**Problem:** Etiketleme sonrası ham veriyi YOLO formatına dönüştürün ve dataset.yaml oluşturun.

**Amaç:** Dataset hazırlama pipeline'ını sıfırdan kurmayı öğrenmek.

**Kod:**
```python
import os
import random
import shutil
from pathlib import Path

def dataset_hazirla(kaynak_goruntu_klas, kaynak_etiket_klas,
                     hedef_klas, val_oran=0.2, tohum=42):
    """
    Görüntü ve etiket klasörlerini alıp YOLO formatında
    train/val ayrımı yaparak organize eder.
    """
    random.seed(tohum)

    hedef = Path(hedef_klas)
    for bolum in ["train", "val"]:
        (hedef / "images" / bolum).mkdir(parents=True, exist_ok=True)
        (hedef / "labels"  / bolum).mkdir(parents=True, exist_ok=True)

    # Tüm görüntü dosyaları
    goruntu_yollari = list(Path(kaynak_goruntu_klas).glob("*.jpg")) + \
                      list(Path(kaynak_goruntu_klas).glob("*.png"))
    random.shuffle(goruntu_yollari)

    # Train/val ayrımı
    kesim    = int(len(goruntu_yollari) * (1 - val_oran))
    train_gs = goruntu_yollari[:kesim]
    val_gs   = goruntu_yollari[kesim:]

    kopyalanan = {"train": 0, "val": 0, "etiketsiz": 0}

    for bolum, goruntu_listesi in [("train", train_gs), ("val", val_gs)]:
        for goruntu_yolu in goruntu_listesi:
            # Etiket dosyası var mı?
            etiket_yolu = Path(kaynak_etiket_klas) / (goruntu_yolu.stem + ".txt")

            if not etiket_yolu.exists():
                kopyalanan["etiketsiz"] += 1
                # Boş etiket dosyası oluştur (nesne yok anlamına gelir)
                (hedef / "labels" / bolum / (goruntu_yolu.stem + ".txt")).touch()

            else:
                shutil.copy(etiket_yolu, hedef / "labels" / bolum / etiket_yolu.name)

            shutil.copy(goruntu_yolu, hedef / "images" / bolum / goruntu_yolu.name)
            kopyalanan[bolum] += 1

    print(f"Dataset hazırlandı: {hedef_klas}")
    print(f"  Train: {kopyalanan['train']}")
    print(f"  Val  : {kopyalanan['val']}")
    print(f"  Etiketsiz: {kopyalanan['etiketsiz']} (boş txt oluşturuldu)")
    return kopyalanan


def yaml_olustur(hedef_klas, sinif_isimleri):
    """dataset.yaml dosyası oluşturur."""
    hedef = Path(hedef_klas).resolve()
    icerik = f"""# YOLO Dataset Yapılandırması
# Otomatik oluşturuldu

path: {hedef}
train: images/train
val:   images/val

nc: {len(sinif_isimleri)}

names:
"""
    for i, isim in enumerate(sinif_isimleri):
        icerik += f"  {i}: {isim}\n"

    yaml_yolu = hedef / "dataset.yaml"
    with open(yaml_yolu, "w", encoding="utf-8") as f:
        f.write(icerik)
    print(f"dataset.yaml oluşturuldu: {yaml_yolu}")
    return yaml_yolu


# Kullanım
if __name__ == "__main__":
    dataset_hazirla(
        kaynak_goruntu_klas = "ham_veri/goruntular",
        kaynak_etiket_klas  = "ham_veri/etiketler",
        hedef_klas          = "dataset",
        val_oran            = 0.2
    )

    yaml_olustur(
        hedef_klas   = "dataset",
        sinif_isimleri = ["kedi", "köpek", "araba"]
    )
```

---

### Uygulama 5.2 — YOLOv11 ile Özel Model Eğitimi

**Problem:** Hazırladığınız özel dataset ile YOLOv11 modeli eğitin.

**Pipeline:**
```
dataset.yaml → YOLO("yolo11n.pt") → train() → best.pt → val() → predict()
```

**Kod:**
```python
from ultralytics import YOLO
import yaml

# ─── 1. Model başlat ────────────────────────────────────────────────────
model = YOLO("yolo11n.pt")   # COCO pretrained — transfer learning

# ─── 2. Eğitim ─────────────────────────────────────────────────────────
print("Eğitim başlıyor...")
sonuclar = model.train(
    data     = "dataset/dataset.yaml",
    epochs   = 100,
    imgsz    = 640,
    batch    = 16,             # GPU belleğine göre ayarlayın
    lr0      = 0.01,
    patience = 20,             # 20 epoch iyileşme yoksa dur
    device   = "0",            # GPU ("cpu" eğer GPU yoksa)
    project  = "runs/egitim",
    name     = "ozel_model_v1",
    save     = True,
    # Augmentasyon (isteğe bağlı özelleştirme)
    fliplr   = 0.5,
    mosaic   = 1.0,
)

print("Eğitim tamamlandı!")
print(f"En iyi model: {sonuclar.save_dir}/weights/best.pt")

# ─── 3. Doğrulama ───────────────────────────────────────────────────────
print("\nModel doğrulanıyor...")
en_iyi = YOLO(f"{sonuclar.save_dir}/weights/best.pt")
metrikler = en_iyi.val(data="dataset/dataset.yaml")

print(f"\n{'='*40}")
print(f"mAP@50     : {metrikler.box.map50:.3f}")
print(f"mAP@50-95  : {metrikler.box.map:.3f}")
print(f"Precision  : {metrikler.box.p:.3f}")
print(f"Recall     : {metrikler.box.r:.3f}")
print(f"{'='*40}")

# ─── 4. Test tahmin ─────────────────────────────────────────────────────
test_results = en_iyi.predict(
    source   = "dataset/images/val",   # Val görselleri üzerinde test
    conf     = 0.5,
    save     = True,
    save_txt = True,
)
print(f"\nTest tespitleri kaydedildi.")
```

**Açıklama:**
`patience=20` erken durdurma için kritik parametredir — 20 epoch boyunca val metriği iyileşmezse eğitim durur, zaman kazanılır. Eğitim çıktıları `runs/egitim/ozel_model_v1/` klasöründe bulunur. `best.pt` her zaman val metriğine göre en iyi checkpoint'tir.

**Eğitim sırasında takip:**
```bash
# Ayrı terminalde sonuçları izle
tensorboard --logdir runs/egitim/
```

---

### Uygulama 5.3 — Eğitim Sonrası Model Kalitesi Kontrolü

**Problem:** Eğitilen modeli değerlendirin; hata analizi yapın.

**Kod:**
```python
from ultralytics import YOLO
import matplotlib.pyplot as plt
import pandas as pd
from pathlib import Path

MODEL_YL = "runs/egitim/ozel_model_v1/weights/best.pt"
model    = YOLO(MODEL_YL)

# ─── 1. Metrik değerlendirme ─────────────────────────────────────────────
metrikler = model.val(data="dataset/dataset.yaml")

# ─── 2. results.csv'den eğitim grafiği ───────────────────────────────────
csv_yolu = Path("runs/egitim/ozel_model_v1/results.csv")
if csv_yolu.exists():
    df = pd.read_csv(csv_yolu)
    df.columns = df.columns.str.strip()

    fig, axes = plt.subplots(2, 2, figsize=(12, 8))
    fig.suptitle("Eğitim Süreci", fontsize=14, fontweight="bold")

    axes[0,0].plot(df["epoch"], df["train/box_loss"], label="Train")
    axes[0,0].plot(df["epoch"], df["val/box_loss"],   label="Val")
    axes[0,0].set_title("Box Loss"); axes[0,0].legend()

    axes[0,1].plot(df["epoch"], df["train/cls_loss"], label="Train")
    axes[0,1].plot(df["epoch"], df["val/cls_loss"],   label="Val")
    axes[0,1].set_title("Class Loss"); axes[0,1].legend()

    axes[1,0].plot(df["epoch"], df["metrics/mAP50(B)"],    label="mAP@50")
    axes[1,0].plot(df["epoch"], df["metrics/mAP50-95(B)"], label="mAP@50-95")
    axes[1,0].set_title("mAP Metrikleri"); axes[1,0].legend()

    axes[1,1].plot(df["epoch"], df["metrics/precision(B)"], label="Precision")
    axes[1,1].plot(df["epoch"], df["metrics/recall(B)"],    label="Recall")
    axes[1,1].set_title("Precision & Recall"); axes[1,1].legend()

    for ax in axes.flat:
        ax.grid(True, alpha=0.3)
        ax.set_xlabel("Epoch")

    plt.tight_layout()
    plt.savefig("egitim_grafik.png", dpi=120)
    plt.show()
    print("Eğitim grafiği kaydedildi: egitim_grafik.png")
```

---

## BLOK 6 — Sonuçları Analiz Etme ve Görselleştirme

---

### Uygulama 6.1 — Detection İstatistikleri Panosu

**Problem:** Bir model çalıştırmasının sonuçlarını kapsamlı şekilde görselleştirin.

**Kod:**
```python
from ultralytics import YOLO
from pathlib import Path
import matplotlib.pyplot as plt
import matplotlib.gridspec as gridspec
import numpy as np
from collections import Counter

model     = YOLO("yolo11n.pt")
GORUNTU_KL = "goruntular"

goruntu_listesi = list(Path(GORUNTU_KL).glob("*.jpg"))
results = model.predict(goruntu_listesi, conf=0.5, verbose=False)

# Veri toplama
siniflar, guvenler, alanlar, goruntu_basi_sayilar = [], [], [], []

for r in results:
    goruntu_basi_sayilar.append(len(r.boxes))
    h, w = r.orig_shape
    for box in r.boxes:
        siniflar.append(r.names[int(box.cls)])
        guvenler.append(float(box.conf))
        x1, y1, x2, y2 = box.xyxy[0].tolist()
        alan_oran = ((x2-x1) * (y2-y1)) / (w * h)
        alanlar.append(alan_oran)

# Görselleştirme
fig = plt.figure(figsize=(16, 10))
fig.suptitle("Detection Analiz Panosu", fontsize=15, fontweight="bold")
gs  = gridspec.GridSpec(2, 3, figure=fig, hspace=0.4, wspace=0.35)

# Panel 1: Sınıf dağılımı
ax1 = fig.add_subplot(gs[0, :2])
sinif_sayisi = Counter(siniflar)
ax1.bar(sinif_sayisi.keys(), sinif_sayisi.values(), color="steelblue", edgecolor="white")
ax1.set_title("Sınıf Dağılımı")
ax1.set_ylabel("Tespit Sayısı")
ax1.tick_params(axis="x", rotation=30)

# Panel 2: Güven dağılımı
ax2 = fig.add_subplot(gs[0, 2])
ax2.hist(guvenler, bins=20, color="seagreen", edgecolor="white")
ax2.axvline(np.mean(guvenler), color="red", ls="--", lw=2,
            label=f"Ort: {np.mean(guvenler):.2f}")
ax2.set_title("Güven Skoru Dağılımı")
ax2.legend()

# Panel 3: Görüntü başına tespit sayısı
ax3 = fig.add_subplot(gs[1, :2])
ax3.bar(range(len(goruntu_basi_sayilar)), goruntu_basi_sayilar, color="tomato", edgecolor="white")
ax3.axhline(np.mean(goruntu_basi_sayilar), color="black", ls="--",
            label=f"Ort: {np.mean(goruntu_basi_sayilar):.1f}")
ax3.set_title("Görüntü Başına Tespit Sayısı")
ax3.set_xlabel("Görüntü"); ax3.set_ylabel("Tespit Sayısı"); ax3.legend()

# Panel 4: Nesne boyutu dağılımı
ax4 = fig.add_subplot(gs[1, 2])
ax4.hist(alanlar, bins=20, color="darkorchid", edgecolor="white")
ax4.set_title("Nesne Boyutu Dağılımı\n(Görüntü Alanının Oranı)")
ax4.set_xlabel("Alan Oranı")

for ax in [ax1, ax2, ax3, ax4]:
    ax.grid(True, alpha=0.3)

plt.savefig("analiz_panosu.png", dpi=130, bbox_inches="tight")
plt.show()

print(f"\nÖzet:")
print(f"  Toplam görüntü      : {len(goruntu_listesi)}")
print(f"  Toplam tespit       : {len(siniflar)}")
print(f"  Ortalama güven      : {np.mean(guvenler):.3f}")
print(f"  Görüntü başına ort  : {np.mean(goruntu_basi_sayilar):.1f}")
```

---

## BLOK 7 — Model Karşılaştırma

---

### Uygulama 7.1 — Hız ve Doğruluk Karşılaştırması

**Problem:** Farklı YOLOv11 model boyutlarını hız ve tespit sayısı açısından karşılaştırın.

**Amaç:** Uygulama için doğru model boyutunu seçmeyi öğrenmek.

**Kod:**
```python
from ultralytics import YOLO
import time
import cv2
import pandas as pd
import matplotlib.pyplot as plt

MODEL_BOYUTLARI = ["yolo11n", "yolo11s", "yolo11m"]   # l ve x GPU gerektirebilir
TEST_GORUNTU    = "test_goruntu.jpg"
TEKRAR          = 10   # Her model için kaç kez çalıştırılsın

goruntu = cv2.imread(TEST_GORUNTU)
sonuclar_listesi = []

for model_adi in MODEL_BOYUTLARI:
    print(f"\n{model_adi} test ediliyor...")
    model = YOLO(f"{model_adi}.pt")

    # Isınma turu (ilk çalışma daha yavaş olabilir)
    model.predict(goruntu, conf=0.5, verbose=False)

    # Zamanlama
    sureler = []
    n_tespitler = []
    for _ in range(TEKRAR):
        t0      = time.perf_counter()
        results = model.predict(goruntu, conf=0.5, verbose=False)
        t1      = time.perf_counter()
        sureler.append((t1 - t0) * 1000)   # ms
        n_tespitler.append(len(results[0].boxes))

    ortalama_sure = sum(sureler) / len(sureler)
    ort_tespit    = sum(n_tespitler) / len(n_tespitler)

    sonuclar_listesi.append({
        "model"           : model_adi,
        "ortalama_sure_ms": round(ortalama_sure, 1),
        "tahmini_fps"     : round(1000 / ortalama_sure, 1),
        "ort_tespit"      : round(ort_tespit, 1),
    })
    print(f"  Süre: {ortalama_sure:.1f} ms | FPS: {1000/ortalama_sure:.1f} | Tespit: {ort_tespit:.1f}")

# Tablo
df = pd.DataFrame(sonuclar_listesi)
print("\n" + "=" * 60)
print(df.to_string(index=False))

# Grafik
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

axes[0].bar(df["model"], df["tahmini_fps"], color="steelblue", edgecolor="white")
axes[0].set_title("Model Hız Karşılaştırması")
axes[0].set_ylabel("FPS (Saniyede Kare)")
axes[0].axhline(30, color="red", ls="--", label="30 FPS hedef")
axes[0].legend()

axes[1].bar(df["model"], df["ort_tespit"], color="seagreen", edgecolor="white")
axes[1].set_title("Ortalama Tespit Sayısı")
axes[1].set_ylabel("Tespit Sayısı")

for ax in axes:
    ax.grid(True, axis="y", alpha=0.3)

plt.tight_layout()
plt.savefig("model_karsilastirma.png", dpi=120)
plt.show()
```

**Açıklama:**
Isınma turu olmadan ilk inference daha yavaş görünür (model yükleme süresi dahil olur). 10 tekrar ortalaması daha güvenilir ölçüm sağlar. Karşılaştırma her zaman **aynı donanımda** yapılmalıdır.

---

## BLOK 8 — Mini Projeler

---

### Uygulama 8.1 — Akıllı Güvenlik Kamerası Simülasyonu

**Problem:** Kameradan gelen görüntüde yalnızca belirli bir bölgeye (ROI) giren kişileri algılayın ve alarm üretin.

**Amaç:** Tespit + bölge analizi + olay tetikleme mantığını birleştirmek.

**Pipeline:**
```
Kamera → Tespit → ROI kontrolü → Kişi girdi mi? → Alarm + Log
```

**Kod:**
```python
import cv2
import time
from ultralytics import YOLO
from datetime import datetime

model = YOLO("yolo11n.pt")
cap   = cv2.VideoCapture(0)

# İzleme bölgesi (ROI) — piksel koordinatı
ROI = {"x1": 150, "y1": 80, "x2": 490, "y2": 380}

alarm_aktif   = False
alarm_baslama = None
olay_log      = []

def roi_icinde_mi(box_xyxy, roi):
    """Tespit kutusunun merkezi ROI içinde mi?"""
    x1, y1, x2, y2 = box_xyxy
    merkez_x = (x1 + x2) / 2
    merkez_y = (y1 + y2) / 2
    return (roi["x1"] <= merkez_x <= roi["x2"] and
            roi["y1"] <= merkez_y <= roi["y2"])

while cap.isOpened():
    ret, kare = cap.read()
    if not ret:
        break

    results  = model.predict(kare, conf=0.55, classes=[0], verbose=False)  # 0=person
    r        = results[0]
    gorsel   = kare.copy()

    # ROI dikdörtgeni çiz
    renk_roi = (0, 0, 255) if alarm_aktif else (0, 255, 0)
    cv2.rectangle(gorsel,
                  (ROI["x1"], ROI["y1"]), (ROI["x2"], ROI["y2"]),
                  renk_roi, 2)
    cv2.putText(gorsel, "IZLEME BOLGESI", (ROI["x1"], ROI["y1"] - 8),
                cv2.FONT_HERSHEY_SIMPLEX, 0.5, renk_roi, 1)

    # Her tespit için ROI kontrolü
    kisi_roi_icinde = False
    for box in r.boxes:
        x1, y1, x2, y2 = map(int, box.xyxy[0].tolist())
        cv2.rectangle(gorsel, (x1, y1), (x2, y2), (255, 165, 0), 2)

        if roi_icinde_mi([x1, y1, x2, y2], ROI):
            kisi_roi_icinde = True
            cv2.rectangle(gorsel, (x1, y1), (x2, y2), (0, 0, 255), 3)
            cv2.putText(gorsel, "⚠ ROI'DA KİŞİ", (x1, y1 - 12),
                        cv2.FONT_HERSHEY_SIMPLEX, 0.6, (0, 0, 255), 2)

    # Alarm yönetimi
    if kisi_roi_icinde and not alarm_aktif:
        alarm_aktif   = True
        alarm_baslama = time.time()
        zaman_str     = datetime.now().strftime("%H:%M:%S")
        olay_log.append(f"{zaman_str} — KİŞİ GİRDİ")
        print(f"⚠️  {zaman_str}: Bölgeye kişi girdi!")

    elif not kisi_roi_icinde and alarm_aktif:
        sure = time.time() - alarm_baslama
        alarm_aktif = False
        zaman_str   = datetime.now().strftime("%H:%M:%S")
        olay_log.append(f"{zaman_str} — ÇIKTI ({sure:.1f}s)")
        print(f"✓  {zaman_str}: Kişi çıktı. Bölgede {sure:.1f}s kaldı.")

    # Alarm göstergesi
    if alarm_aktif:
        cv2.putText(gorsel, "⚠ ALARM", (10, 40),
                    cv2.FONT_HERSHEY_SIMPLEX, 1.2, (0, 0, 255), 3)

    # Log göster
    for i, olay in enumerate(olay_log[-4:]):
        cv2.putText(gorsel, olay, (10, gorsel.shape[0] - 20 - i*22),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 1)

    cv2.imshow("Guvenlik Kamerasi", gorsel)
    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()

print("\n=== OLAY GÜNLÜĞÜ ===")
for olay in olay_log:
    print(f"  {olay}")
```

**Açıklama:**
`classes=[0]` yalnızca "person" sınıfını tespit eder — diğer nesneler işlenmez, FPS artar. ROI kontrolü merkez noktasına göre yapılır; parmak gibi yalnızca küçük bir kısım girerse alarm verilmez. Olay günlüğü hem ekranda hem konsola yazdırılır.

**İyileştirme fikirleri:**
- Alarm sesini `playsound` ile çalın.
- Alarm anında `cv2.imwrite` ile anlık görüntü kaydedin.
- Birden fazla ROI tanımlayın.

---

### Uygulama 8.2 — Raf Doluluk Tespiti

**Problem:** Bir market rafının görüntüsünde ürünleri tespit edin ve doluluk oranını hesaplayın.

**Amaç:** Detection sonuçlarından iş değeri üreten analiz yapmayı öğrenmek.

**Kod:**
```python
from ultralytics import YOLO
import cv2
import numpy as np
from pathlib import Path

# Ürün tespiti için fine-tuned model (özel eğitim gerektirir)
# Burada COCO modeli demo amaçlı kullanılıyor
model = YOLO("yolo11n.pt")

def doluluk_hesapla(results, goruntu_genislik, goruntu_yukseklik, n_bolge=5):
    """
    Görüntüyü yatay bölgelere bölerek her bölgenin doluluk yüzdesini hesaplar.
    """
    bolge_genislik = goruntu_genislik // n_bolge
    bolge_alanlari = [0.0] * n_bolge

    for box in results[0].boxes:
        x1, y1, x2, y2 = box.xyxy[0].tolist()

        # Bu kutunun hangi bölgelerde yer aldığını bul
        for i in range(n_bolge):
            bolge_x1 = i * bolge_genislik
            bolge_x2 = (i + 1) * bolge_genislik

            # Kesişim alanı
            kesisim_x1 = max(x1, bolge_x1)
            kesisim_x2 = min(x2, bolge_x2)
            if kesisim_x2 > kesisim_x1:
                kesisim_alan = (kesisim_x2 - kesisim_x1) * (y2 - y1)
                bolge_alanlari[i] += kesisim_alan

    # Her bölgenin doluluk yüzdesi
    max_alan = bolge_genislik * goruntu_yukseklik
    return [min(alan / max_alan * 100, 100.0) for alan in bolge_alanlari]


def gorsellestir(goruntu, results, doluluk_listesi, n_bolge=5):
    gorsel = goruntu.copy()
    h, w   = goruntu.shape[:2]

    # Tespitleri çiz
    annotated = results[0].plot()

    # Bölge doluluk barları
    bolge_genislik = w // n_bolge
    for i, doluluk in enumerate(doluluk_listesi):
        x       = i * bolge_genislik
        renk    = (0, int(255 * doluluk/100), int(255 * (1 - doluluk/100)))
        # Doluluk barı (alttan yukarıya)
        bar_h   = int(doluluk / 100 * 80)
        cv2.rectangle(annotated,
                       (x + 2, h - bar_h - 5),
                       (x + bolge_genislik - 2, h - 5),
                       renk, -1)
        cv2.putText(annotated, f"{doluluk:.0f}%",
                    (x + 5, h - bar_h - 10),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.5, (255, 255, 255), 1)

        # Bölge sınırı
        cv2.line(annotated, (x, 0), (x, h), (128, 128, 128), 1)

    # Genel doluluk
    genel = sum(doluluk_listesi) / len(doluluk_listesi)
    cv2.putText(annotated, f"Genel Doluluk: {genel:.0f}%",
                (10, 35), cv2.FONT_HERSHEY_SIMPLEX, 0.9, (0, 255, 255), 2)

    return annotated


# Ana işlem
goruntu_yolu = "raf_goruntu.jpg"
if not Path(goruntu_yolu).exists():
    # Demo görüntü oluştur
    goruntu = np.ones((400, 800, 3), dtype=np.uint8) * 230
    for i in range(0, 800, 80):
        if np.random.rand() > 0.3:  # %70 dolu
            cv2.rectangle(goruntu, (i+5, 100), (i+75, 300), (80, 120, 200), -1)
    cv2.imwrite(goruntu_yolu, goruntu)

goruntu = cv2.imread(goruntu_yolu)
h, w    = goruntu.shape[:2]

results        = model.predict(goruntu, conf=0.3, verbose=False)
doluluk_listesi = doluluk_hesapla(results, w, h)

print("=== RAF DOLULUK ANALİZİ ===")
for i, doluluk in enumerate(doluluk_listesi):
    durum = "✓ Dolu" if doluluk > 50 else "⚠ Az"
    print(f"  Bölge {i+1}: {doluluk:.0f}%  {durum}")
print(f"\nGenel doluluk: {sum(doluluk_listesi)/len(doluluk_listesi):.0f}%")
print(f"Toplam tespit: {len(results[0].boxes)}")

gorsel = gorsellestir(goruntu, results, doluluk_listesi)
cv2.imshow("Raf Doluluk Analizi", gorsel)
cv2.imwrite("raf_analiz_sonucu.jpg", gorsel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Bu uygulama YOLO detection çıktısını doğrudan iş değerine (doluluk oranı) dönüştürür. Gerçek bir raf analizi için özel eğitilmiş model kullanılması gerekir; COCO modeli genel nesneleri tespit eder.

---

### Uygulama 8.3 — Endüstriyel Hata Tespiti Simülasyonu

**Problem:** Üretim bandından geçen ürünlerde hata tespiti yapan bir sistem kurun; hatalı ürünleri tespit edip kayıt altına alın.

**Amaç:** Endüstriyel tespit pipeline'ının mantığını anlamak.

**Kod:**
```python
from ultralytics import YOLO
import cv2
import numpy as np
from datetime import datetime
from pathlib import Path

# Gerçek projede: hata tepsiti için fine-tuned model kullanılır
# model = YOLO("uretim_hata_modeli.pt")
# Demo: COCO modeli ile simülasyon
model = YOLO("yolo11n.pt")

LOG_DIR = Path("hata_loglari")
LOG_DIR.mkdir(exist_ok=True)

class UretimBantSimulatoru:
    """Üretim bandını simüle eder."""
    def __init__(self, goruntu_boyutu=(480, 640)):
        self.h, self.w = goruntu_boyutu
        self.sayac = 0
        self.hata_sayisi = 0
        self.toplam_urun = 0

    def yeni_kare_uret(self):
        """Ürün geçişini simüle eden kare üretir."""
        kare = np.ones((self.h, self.w, 3), dtype=np.uint8) * 200

        # Bant çizgisi
        cv2.line(kare, (0, self.h//2), (self.w, self.h//2), (150, 150, 150), 3)

        # Ürün (rastgele konumlu kutu)
        self.sayac += 1
        urun_x = (self.sayac * 15) % (self.w - 100)
        cv2.rectangle(kare, (urun_x, self.h//2 - 60), (urun_x+80, self.h//2+60),
                       (100, 150, 220), -1)

        # %30 olasılıkla "hata" (farklı renk)
        hata_var = np.random.rand() < 0.3
        if hata_var:
            cv2.rectangle(kare, (urun_x+15, self.h//2 - 20), (urun_x+40, self.h//2+20),
                           (50, 50, 180), -1)

        return kare, hata_var


def hata_kaydet(kare, hata_tipi, guven):
    zaman = datetime.now().strftime("%Y%m%d_%H%M%S_%f")
    dosya = LOG_DIR / f"hata_{zaman}.jpg"
    cv2.imwrite(str(dosya), kare)

    log_dosya = LOG_DIR / "hata_log.txt"
    with open(log_dosya, "a") as f:
        f.write(f"{datetime.now()} | {hata_tipi} | guven={guven:.2f} | {dosya.name}\n")
    return dosya


# Ana döngü
bant = UretimBantSimulatoru()
hata_sayaci = 0
toplam_kare = 0
HATA_ESIGI  = 0.35   # Tespit confidence eşiği

print("Üretim bandı başlatıldı... (çıkmak için 'q')")

while True:
    kare, gercek_hata = bant.yeni_kare_uret()
    toplam_kare += 1

    results  = model.predict(kare, conf=HATA_ESIGI, verbose=False)
    r        = results[0]
    gorsel   = r.plot()

    # Basit hata tespiti mantığı: belirli sınıf tespiti = hata
    hata_tespit_edildi = len(r.boxes) > 0
    if hata_tespit_edildi:
        hata_sayaci += 1
        guven = float(r.boxes[0].conf) if len(r.boxes) > 0 else 0
        hata_kaydet(kare, "TESPIT", guven)
        renk_durum = (0, 0, 255)
        durum_mesaj = "⚠ HATA TESPİT EDİLDİ"
    else:
        renk_durum  = (0, 255, 0)
        durum_mesaj = "✓ Ürün OK"

    # Gösterge paneli
    panel_y = gorsel.shape[0] - 80
    cv2.rectangle(gorsel, (0, panel_y), (gorsel.shape[1], gorsel.shape[0]), (30,30,30), -1)
    cv2.putText(gorsel, durum_mesaj, (10, panel_y + 25),
                cv2.FONT_HERSHEY_SIMPLEX, 0.8, renk_durum, 2)
    cv2.putText(gorsel,
                f"Toplam: {toplam_kare} | Hata: {hata_sayaci} | Oran: %{hata_sayaci/toplam_kare*100:.1f}",
                (10, panel_y + 55), cv2.FONT_HERSHEY_SIMPLEX, 0.6, (200, 200, 200), 1)

    cv2.imshow("Uretim Bandi Kontrol", gorsel)

    tus = cv2.waitKey(200) & 0xFF   # 200ms bekle (bant hızı simülasyonu)
    if tus == ord("q"):
        break

cv2.destroyAllWindows()

print(f"\n=== ÜRETİM RAPORU ===")
print(f"Toplam kare      : {toplam_kare}")
print(f"Tespit edilen hata: {hata_sayaci}")
print(f"Hata oranı       : %{hata_sayaci/toplam_kare*100:.1f}")
print(f"Log dosyaları    : {LOG_DIR}/")
```

**Açıklama:**
Bu simülasyon endüstriyel bir hata tespit sisteminin temel bileşenlerini içerir: sürekli kare işleme, eşik tabanlı karar, otomatik log ve görüntü kaydetme. Gerçek bir sistemde COCO modeli yerine özel eğitilmiş model kullanılır.

---

### Uygulama 8.4 — Segment vs Detect Karşılaştırması

**Problem:** Aynı görüntüde detection ve segmentation modellerini yan yana karşılaştırın.

**Amaç:** İki görev türünün çıktı farkını görsel olarak anlamak.

**Kod:**
```python
import cv2
import numpy as np
from ultralytics import YOLO

goruntu = cv2.imread("goruntu.jpg")
if goruntu is None:
    goruntu = np.random.randint(50, 200, (480, 640, 3), dtype=np.uint8)

# İki farklı model
model_det = YOLO("yolo11n.pt")       # Detection
model_seg = YOLO("yolo11n-seg.pt")   # Segmentation

# Tahmin
results_det = model_det.predict(goruntu, conf=0.5, verbose=False)
results_seg = model_seg.predict(goruntu, conf=0.5, verbose=False)

# Görselleştir
det_gorsel = results_det[0].plot()
seg_gorsel = results_seg[0].plot()

# Yan yana
BOYUT = (600, 450)
det_yeniden = cv2.resize(det_gorsel, BOYUT)
seg_yeniden = cv2.resize(seg_gorsel, BOYUT)

cv2.putText(det_yeniden, "DETECTION (Bounding Box)",
            (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 255), 2)
cv2.putText(seg_yeniden, "SEGMENTATION (Piksel Maskesi)",
            (10, 30), cv2.FONT_HERSHEY_SIMPLEX, 0.8, (0, 255, 255), 2)

panel = np.hstack([det_yeniden, seg_yeniden])

# İstatistik
n_det = len(results_det[0].boxes)
n_seg = len(results_seg[0].boxes)
print(f"Detection: {n_det} nesne")
print(f"Segmentation: {n_seg} nesne (maske ile)")

if results_seg[0].masks is not None:
    print(f"Maske sayısı: {len(results_seg[0].masks.data)}")

cv2.imshow("Det vs Seg Karsilastirma", panel)
cv2.imwrite("det_vs_seg.jpg", panel)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

**Açıklama:**
Detection yalnızca bounding box üretir — hızlıdır. Segmentation ek olarak piksel maskesi üretir — daha yavaş ama nesnenin tam sınırını verir. Konveyör bant, ameliyat görüntüleri gibi kesin sınır gereken durumlarda segmentation tercih edilir.

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `yolo-teori.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
