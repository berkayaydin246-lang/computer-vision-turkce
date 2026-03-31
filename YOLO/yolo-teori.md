# 📘 YOLO — Teorik Notlar

> **Modül:** Teori | **Seviye:** Başlangıç → Orta → İleri
> **İlgili dosyalar:** `yolo-uygulamalar.md`
> **Ekosistem:** Ultralytics YOLOv11

---

## BLOK 0 — Giriş Kartı

```
Çerçeve    : Ultralytics YOLO (YOLOv11)
Görev türü : Nesne Tespiti, Segmentasyon, Sınıflandırma, Poz Tahmini, Takip
Kurulum    : pip install ultralytics
Lisans     : AGPL-3.0 (açık kaynak) / Ticari lisans mevcut
Geliştiren : Ultralytics (Glenn Jocher ve ekibi)
```

**YOLO Nedir?**
YOLO (**Y**ou **O**nly **L**ook **O**nce) bir görüntüye yalnızca tek bir geçişle bakarak içindeki nesneleri tespit eden derin öğrenme tabanlı bir nesne tespiti mimarisidir. Geleneksel yaklaşımların aksine görüntüyü defalarca taramak yerine tümünü bir seferde analiz eder; bu da onu gerçek zamanlı uygulamalar için ideal kılar.

**Hangi Problemleri Çözer?**
- "Bu görüntüde ne var ve nerede?" sorusunu cevaplar.
- Birden fazla nesneyi aynı anda, farklı konumlarda ve farklı boyutlarda bulur.
- Gerçek zamanlı (canlı kamera, video akışı) tespit yapabilir.

**Hangi Alanlarda Kullanılır?**

| Alan | Uygulama Örneği |
|------|----------------|
| Güvenlik | Kamera sistemleri, davranış analizi, kalabalık tespiti |
| Trafik | Araç sayma, hız ihlali tespiti, trafik yoğunluğu analizi |
| Sanayi | Kalite kontrol, hata tespiti, üretim hattı izleme |
| Perakende | Raf doluluk tespiti, müşteri davranışı analizi |
| Sağlık | Tıbbi görüntülerde anomali tespiti |
| Tarım | Bitki hastalığı tespiti, hasat analizi |
| Spor | Oyuncu takibi, top analizi, hareket metrikleri |
| Robotik | Nesne kavrama, engel tanıma, navigasyon |

**Avantajları:**
- Gerçek zamanlı hız (GPU ile saniyede 30–100+ kare)
- Tek adımda tespit (hız ve basitlik)
- Hazır eğitilmiş modeller (hemen kullanıma hazır)
- Kolay fine-tuning (az veriyle özel model eğitimi)
- Ultralytics ekosistemi ile güçlü araç desteği

**Sınırlamaları:**

| Sınır | Açıklama |
|-------|----------|
| Küçük nesneler | Çok küçük veya yoğun paketlenmiş nesnelerde başarım düşer |
| Örtüşen nesneler | Aynı konumdaki birden fazla nesne zorlaştırır |
| Veri gereksinimi | İyi kaliteli etiketli veri olmadan iyi model elde edilemez |
| Açıklanabilirlik | "Neden bu nesneyi tespit etti?" sorusu yanıtlaması güç |
| Ticari lisans | Açık kaynak sürüm AGPL-3.0 — ticari kullanım için lisans gerekebilir |

**Alternatif Modeller:**

| Model | Güçlü Yanı | Ne Zaman Tercih Edilir |
|-------|-----------|----------------------|
| Faster R-CNN | Yüksek doğruluk | Hızın kritik olmadığı akademik çalışmalar |
| SSD | Düşük kaynak tüketimi | Gömülü sistemler, eski donanım |
| Detectron2 | Araştırma esnekliği | Yeni mimari denemeleri, akademik çalışmalar |
| RT-DETR | Transformer tabanlı, yüksek doğruluk | Gerçek zamanlı + yüksek hassasiyet gereken durumlar |
| **YOLOv11** | **Hız + doğruluk dengesi** | **Çoğu gerçek dünya projesi için** |

---

## BLOK 1 — Kurulum ve Ortam Hazırlığı

### Python Ortamı

```bash
# Sanal ortam oluştur (kesinlikle önerilir)
python -m venv yolo_env
source yolo_env/bin/activate        # Linux / Mac
yolo_env\Scripts\activate           # Windows

# Conda ile
conda create -n yolo_env python=3.10
conda activate yolo_env
```

### Ultralytics Kurulumu

```bash
# Temel kurulum
pip install ultralytics

# Belirli sürüm (tekrarlanabilirlik için)
pip install ultralytics==8.3.0

# Tam geliştirme ortamı
pip install ultralytics matplotlib pandas seaborn opencv-python
```

### Kurulum Doğrulama

```python
import ultralytics
ultralytics.checks()    # GPU, CUDA, Python, NumPy sistem kontrolü

# Hızlı çalışma testi
from ultralytics import YOLO
model = YOLO("yolo11n.pt")   # Model otomatik indirilir
print("Model hazır:", model.info())
```

### GPU ve CPU Kullanımı

```python
import torch
print(torch.cuda.is_available())       # True → GPU kullanılabilir
print(torch.cuda.get_device_name(0))   # GPU model adı

# YOLO cihazı otomatik seçer; elle belirtmek için:
results = model.predict("goruntu.jpg", device="cuda:0")   # GPU
results = model.predict("goruntu.jpg", device="cpu")      # CPU
```

**GPU neden önemlidir?**
CPU ile küçük modelde bile eğitim saatler sürebilir; GPU ile aynı iş dakikalara iner. Inference (tahmin) için CPU yeterli olabilir, ancak gerçek zamanlı video uygulamalarında GPU gereklidir.

### Temel Klasör Yapısı

```
proje/
├── data/
│   ├── images/
│   │   ├── train/      ← Eğitim görüntüleri
│   │   └── val/        ← Doğrulama görüntüleri
│   └── labels/
│       ├── train/      ← Eğitim etiketleri
│       └── val/        ← Doğrulama etiketleri
├── dataset.yaml        ← Dataset tanım dosyası
├── runs/               ← YOLO çıktı klasörü (otomatik oluşur)
└── main.py
```

---

## BLOK 2 — Object Detection Nedir?

### Bilgisayarlı Görü Görev Hiyerarşisi

```
Soru                                   Görev
────────────────────────────────────────────────────────────────────
"Bu görüntüde ne var?"             → Sınıflandırma (Classification)
"Nesne görüntünün neresinde?"      → Lokalizasyon (Localization)
"Ne var, nerede, kaç tane?"        → Nesne Tespiti (Object Detection)  ← YOLO
"Nesnenin piksel sınırı nerede?"   → Segmentasyon (Segmentation)
"İnsan iskelet noktaları nerede?"  → Poz Tahmini (Pose Estimation)
```

---

### Sınıflandırma (Classification)

Görüntüye bakıp "Bu bir kedi mi, köpek mi?" sorusunu yanıtlar. Tek etiket döndürür. **Nesnenin konumunu söylemez.**

```
Girdi : Görüntü
Çıktı : "kedi" (sınıf adı + olasılık)
Sınır : Yalnızca genel etiket, konum bilgisi yok
```

---

### Nesne Tespiti (Object Detection)

Görüntüde **birden fazla nesneyi** bulur, sınıflandırır ve her birinin konumunu dikdörtgenle (bounding box) belirtir. YOLO bu görev için tasarlanmıştır.

```
Girdi : Görüntü
Çıktı : [
  ("kedi",  0.92, [120, 80,  300, 260]),
  ("köpek", 0.87, [400, 90,  620, 350]),
  ("kedi",  0.78, [ 50, 20,  180, 200])
]
Format: (sınıf, güven_skoru, [x1, y1, x2, y2])
```

---

### Segmentasyon (Segmentation)

Nesne tespitinin ötesine gider: nesnenin piksel düzeyinde kesin sınırını çıkarır.

```
Girdi : Görüntü
Çıktı : Her nesne için bounding box + piksel maskesi
Maliyet: Daha hesaplama yoğun, daha kesin sınır
```

---

### Poz Tahmini (Pose Estimation)

İnsan vücudunun anahtar noktalarını (omuz, dirsek, diz vb.) tespit eder.

```
Girdi : Görüntü (insan içeren)
Çıktı : 17 keypoint koordinatı (COCO formatında)
```

---

## BLOK 3 — YOLO Nedir?

### Tarihsel Perspektif

2015'te Joseph Redmon tarafından sunulan YOLO, nesne tespitine devrim getirdi. O zamana kadar var olan modeller görüntüyü birden fazla kez işliyordu; bu doğruluk sağlıyordu ama gerçek zamanlı kullanım için çok yavaştı. YOLO görüntüyü **tek geçişte** analiz ederek hem hızı hem de tatmin edici doğruluğu bir arada sundu.

Günümüzde Ultralytics tarafından geliştirilen YOLOv8 ve YOLOv11 bu mirası sürdürüyor; her sürümde hem doğruluk hem hız artırılıyor.

### YOLO'nun Temel Fikri

```
Geleneksel yaklaşım:
  1. Görüntüde olabilecek yüzlerce-binlerce adayı belirle
  2. Her adayı ayrı ayrı sınıflandır
  3. En iyi sonuçları birleştir
  Sonuç: YAVAŞ, çok adımlı

YOLO yaklaşımı:
  1. Görüntüye bir kez bak
  2. Tüm nesne konumları ve sınıflarını aynı anda tahmin et
  Sonuç: HIZLI, tek adım
```

### Modern Kullanım Kolaylığı

Ultralytics YOLOv11 ile kullanıcıların mimari detayları bilmesine gerek kalmaz:

```python
from ultralytics import YOLO

model   = YOLO("yolo11n.pt")
results = model("goruntu.jpg")   # Bu tek satır arkasında:
# 1. Görüntü yüklenir ve yeniden boyutlandırılır
# 2. Model görüntüyü analiz eder
# 3. Confidence ve NMS filtresi uygulanır
# 4. Sonuçlar nesne olarak döner
```

---

## BLOK 4 — YOLO Mantığı

### Izgara Tabanlı Tahmin (Kavramsal)

YOLO görüntüyü kavramsal olarak farklı ölçeklerde ızgaralara böler. Her ızgara bölgesi, o bölgedeki nesneleri tahmin etmekten sorumludur. Modern YOLO (v8, v11) **çok ölçekli tahmin** yaparak hem büyük hem de küçük nesneleri aynı anda tespit edebilir.

```
Görüntü (640×640)
    ↓
Az ayrıntılı harita → Büyük nesneler için tahmin
Orta ayrıntılı harita → Orta nesneler için tahmin
Çok ayrıntılı harita → Küçük nesneler için tahmin
```

### Aynı Anda Kutu + Sınıf Tahmini

Model her bölge için şunları aynı anda tahmin eder:

```
Tek tahmin paketi = [
  x_merkez, y_merkez,    ← Nesnenin konumu
  genislik, yukseklik,   ← Nesnenin boyutu
  confidence,            ← "Bu bölgede nesne var" güven skoru
  sınıf_1_olasılığı,    ← "kedi mi?" olasılığı
  sınıf_2_olasılığı,    ← "köpek mi?" olasılığı
  ...
]
```

### Confidence Skoru

```
confidence = 0.95 → Modelin kesin gördüğü nesne
confidence = 0.52 → Belirsiz, eşik değerine yakın
confidence = 0.12 → Muhtemelen gerçek nesne değil (filtrelenir)

Eşik (threshold = 0.25 varsayılan):
  confidence < threshold olan tespitler atılır
```

### Non-Maximum Suppression (NMS)

Model aynı nesneyi birden fazla kutuda tespit ettiğinde NMS devreye girer:

```
NMS öncesi:
  Araba: conf=0.91, konum=[100, 50, 300, 200]
  Araba: conf=0.85, konum=[105, 55, 295, 205]  ← Neredeyse aynı
  Araba: conf=0.23, konum=[108, 52, 302, 203]  ← Neredeyse aynı

NMS sonrası:
  Araba: conf=0.91, konum=[100, 50, 300, 200]  ← Sadece en güçlü kaldı
```

NMS şunu yapar: İki kutu büyük ölçüde üst üste geliyorsa (IoU yüksekse) zayıf olanı siler. Böylece her nesne için tek ve en iyi kutu kalır.

---

## BLOK 5 — Bounding Box ve Detection Kavramları

### Bounding Box (Sınırlayıcı Kutu)

Nesnenin görüntüde kapladığı alanı çevreleyen eksene paralel dikdörtgendir.

**İki yaygın format:**
```
Köşe formatı (XYXY) — çıktıda yaygın:
  [x_min, y_min, x_max, y_max]
  [120,   80,    300,   260]
  Sol üst=(120,80), Sağ alt=(300,260)

Merkez + boyut formatı (XYWH) — YOLO etiket formatı (normalize):
  [x_merkez, y_merkez, genişlik, yükseklik]
  [0.42,     0.33,     0.28,    0.35]   ← Hepsi 0.0-1.0 arası
```

**Piksel koordinatından YOLO formatına dönüşüm:**
```python
def piksel_to_yolo(x1, y1, x2, y2, goruntu_w, goruntu_h):
    x_merkez  = ((x1 + x2) / 2) / goruntu_w
    y_merkez  = ((y1 + y2) / 2) / goruntu_h
    genislik  = (x2 - x1) / goruntu_w
    yukseklik = (y2 - y1) / goruntu_h
    return x_merkez, y_merkez, genislik, yukseklik
```

---

### Class Label (Sınıf Etiketi)

Tespit edilen nesnenin hangi kategoriye ait olduğunu gösteren **tam sayı indeksidir**. `dataset.yaml` dosyasındaki sırayla eşleşir.

```yaml
# dataset.yaml
names:
  0: kedi     # class_id = 0
  1: köpek    # class_id = 1
  2: araba    # class_id = 2
```

Tespit sonucunda `cls=0` → "kedi" anlamına gelir.

---

### Confidence Score (Güven Skoru)

Modelin bu tespite ne kadar güvendiğini 0.0–1.0 arasında ifade eden değer.

```
> 0.8 : Yüksek güven — model çok emin
0.5–0.8 : Orta güven — kabul edilebilir
< 0.5 : Düşük güven — genellikle filtrelenir (threshold ile)
```

**Threshold (Eşik) seçimi uygulamaya göre değişir:**
- Yüksek eşik → Az tespit, düşük yanlış alarm
- Düşük eşik → Çok tespit, yanlış alarmlar artar
- Varsayılan: `conf=0.25` (Ultralytics)

---

### IoU — Intersection over Union

İki kutunun ne kadar örtüştüğünü ölçen metriktir. Hem NMS için hem de model değerlendirmesinde kullanılır.

```
IoU = Kesişim Alanı / Birleşim Alanı

IoU = 1.0 → Kutular tam üst üste (mükemmel)
IoU = 0.5 → "Doğru tespit" için yaygın eşik
IoU = 0.0 → Kutular hiç örtüşmüyor

Gerçek kutu : [100, 50, 300, 250]
Tahmin kutu : [120, 60, 310, 260]
IoU ≈ 0.75  → İyi eşleşme
```

---

### NMS — Non-Maximum Suppression

Aynı nesne için üretilmiş fazla tahminleri temizleyen algoritma.

```
Algoritma:
1. Tüm tahminleri confidence'a göre büyükten küçüğe sırala
2. En yüksek confidence'lı tahmini seç (kesinlikle kalır)
3. Bu tahminle IoU > eşik olan diğer tahminleri sil
4. Kalan listede tekrar 2. adıma git
5. Liste boşalana kadar devam et
```

---

## BLOK 6 — YOLOv11 Görev Türleri

YOLOv11 birden fazla bilgisayarlı görü görevini tek çerçevede destekler.

---

### 🔹 Detect — Nesne Tespiti

**Ne yapar:** Görüntüdeki nesneleri bulur; bounding box + sınıf + güven skoru döndürür.

**Ne zaman:** "Bu görüntüde ne var, nerede?" sorusuna cevap aranırken.

**Model:** `yolo11n.pt`, `yolo11s.pt`, `yolo11m.pt`, `yolo11l.pt`, `yolo11x.pt`

```python
from ultralytics import YOLO

model   = YOLO("yolo11n.pt")
results = model("goruntu.jpg")

for r in results:
    print(r.boxes.xyxy)   # [x1, y1, x2, y2]
    print(r.boxes.cls)    # Sınıf indeksleri
    print(r.boxes.conf)   # Güven skorları
    print(r.names)        # {0: 'kedi', 1: 'köpek', ...}
```

---

### 🔹 Segment — Nesne Segmentasyonu

**Ne yapar:** Bounding box'a ek olarak piksel düzeyinde maske çıkarır.

**Ne zaman:** "Hangi pikseller bu nesneye ait?" bilgisi gerektiğinde.

**Model:** `yolo11n-seg.pt`, `yolo11s-seg.pt`, ...

```python
model   = YOLO("yolo11n-seg.pt")
results = model("goruntu.jpg")

for r in results:
    print(r.masks.data)   # Segmentasyon maskeleri
```

---

### 🔹 Classify — Görüntü Sınıflandırma

**Ne yapar:** Tüm görüntüyü tek bir sınıfa atar. Bounding box üretmez.

**Ne zaman:** Görüntünün genel içeriğini bilmek yeterliyken.

**Model:** `yolo11n-cls.pt`, ...

```python
model   = YOLO("yolo11n-cls.pt")
results = model("goruntu.jpg")

for r in results:
    print(r.probs.top1)    # En olası sınıf indeksi
    print(r.probs.top5)    # İlk 5 sınıf indeksi
```

---

### 🔹 Pose — Poz Tahmini

**Ne yapar:** İnsan vücudunun anahtar iskelet noktalarını tespit eder.

**Ne zaman:** Spor analizi, fitness uygulamaları, hareket analizi.

**Model:** `yolo11n-pose.pt`, ...

```python
model   = YOLO("yolo11n-pose.pt")
results = model("goruntu.jpg")

for r in results:
    print(r.keypoints.xy)    # [x, y] koordinatları
    print(r.keypoints.conf)  # Her noktanın güven skoru
```

---

### 🔹 Track — Nesne Takibi

**Ne yapar:** Video kareleri boyunca nesnelere benzersiz ID atar; aynı nesne farklı karelerde aynı ID ile izlenir.

**Ne zaman:** Güvenlik kameraları, trafik analizi, spor takibi.

```python
model = YOLO("yolo11n.pt")

for kare in video_kareleri:
    results = model.track(kare, persist=True)   # persist=True kritik
    for r in results:
        if r.boxes.id is not None:
            print(r.boxes.id)   # Takip ID'leri
```

---

## BLOK 7 — Dataset Yapısı ve Etiketleme

Bu bölüm **başarılı eğitimin temel taşıdır.** Yanlış yapılandırılmış dataset, iyi bir modeli imkânsız kılar.

---

### Klasör Yapısı

```
dataset/
├── images/
│   ├── train/           ← Eğitim görüntüleri (örn: 800 adet)
│   │   ├── img001.jpg
│   │   └── ...
│   └── val/             ← Doğrulama görüntüleri (örn: 200 adet)
│       ├── img901.jpg
│       └── ...
└── labels/
    ├── train/           ← Eğitim etiketleri (görüntüyle birebir eşleşmeli)
    │   ├── img001.txt   ← img001.jpg için etiket
    │   └── ...
    └── val/
        ├── img901.txt
        └── ...
```

**Kritik kural:** `img001.jpg` için mutlaka `img001.txt` olmalıdır. Dosya adları tam eşleşmelidir (uzantı hariç). Etiketsiz görüntü varsa boş `.txt` dosyası oluşturun — hiç nesne içermediğini böyle bildirirsiniz.

---

### YOLO Etiket Formatı

Her `.txt` dosyasında her satır bir nesneyi temsil eder:

```
<sınıf_id> <x_merkez> <y_merkez> <genişlik> <yükseklik>
```

Tüm değerler **normalize** (0.0–1.0 arası):

```
0 0.4200 0.3300 0.2800 0.3500
1 0.7500 0.6000 0.1500 0.2200
0 0.1800 0.4500 0.2000 0.3000
```

Satır 1: "kedi" (id=0), görüntü ortasına yakın, belirli genişlik ve yükseklikte.

```python
# Piksel → YOLO dönüşümü
def piksel_to_yolo(x1, y1, x2, y2, img_w, img_h):
    xc = ((x1 + x2) / 2) / img_w
    yc = ((y1 + y2) / 2) / img_h
    w  = (x2 - x1) / img_w
    h  = (y2 - y1) / img_h
    return xc, yc, w, h

# YOLO → Piksel dönüşümü
def yolo_to_piksel(xc, yc, w, h, img_w, img_h):
    x1 = int((xc - w/2) * img_w)
    y1 = int((yc - h/2) * img_h)
    x2 = int((xc + w/2) * img_w)
    y2 = int((yc + h/2) * img_h)
    return x1, y1, x2, y2
```

---

### dataset.yaml

```yaml
# dataset.yaml — YOLO'nun dataset hakkında her şeyi öğrendiği dosya

path: /proje/dataset    # Dataset kök dizini (mutlak yol önerilir)
train: images/train     # Eğitim görüntüleri (path'e göre göreceli)
val:   images/val       # Doğrulama görüntüleri
test:  images/test      # Test görüntüleri (isteğe bağlı)

nc: 3                   # Sınıf sayısı

names:                  # Sınıf isimleri (ID sırası kritik!)
  0: kedi
  1: köpek
  2: araba
```

**Dikkat:** `names` listesindeki sıra, etiket dosyalarındaki sınıf ID'leriyle **tam uyuşmalıdır.** Sıralamayı değiştirirseniz tüm etiketler yanlış anlam taşır.

---

### Train / Val / Test Ayrımı

```
Toplam: 1000 görüntü

Önerilen dağılım:
  train: %70–80  → 700–800 görüntü  (model bu veriyle öğrenir)
  val:   %15–20  → 150–200 görüntü  (eğitim sırasında izlenir)
  test:  %5–10   → 50–100 görüntü   (son değerlendirme, model hiç görmedi)
```

**Önemli kurallar:**
- **Train ve val'ı kesinlikle karıştırmayın.** Val seti model tarafından "öğrenilmemelidir."
- **Test seti kutsaldır.** Sadece projenin en sonunda, tek bir kez kullanılır.
- Aynı videonun kareleri tek bir split'te olmalı — farklı split'lere dağıtmak veri sızıntısına yol açar.

---

### Sınıf Dengesizliği (Class Imbalance)

```
Kötü örnek:
  kedi:  800 görüntü  ← Aşırı temsil
  köpek: 150 görüntü
  araba:  50 görüntü  ← Yetersiz temsil

Sonuç: Model "kedi" için iyi, "araba" için kötü çalışır.
       mAP yüksek görünür ama "araba" tespiti berbat olabilir.

Çözüm:
  1. Az örnekli sınıf için daha fazla veri topla (tercih edilen)
  2. Az örnekli sınıfa augmentasyon uygula
  3. Sınıf ağırlıkları kullan (gelişmiş)
```

---

### Veri Sızıntısı (Data Leakage)

Val verisinden görüntüler eğitim setine karışırsa model bu görüntüleri "ezberlemiş" olur. Metrikler yanıltıcı biçimde yüksek görünür ama gerçek performans düşüktür.

```python
# Basit kontrol
train_dosyalar = set(os.listdir("data/images/train"))
val_dosyalar   = set(os.listdir("data/images/val"))

ortak = train_dosyalar & val_dosyalar
if ortak:
    print(f"⚠️ {len(ortak)} dosya her iki sette de var!")
else:
    print("✓ Veri sızıntısı yok.")
```

---

### Etiketleme Araçları

| Araç | Platform | Özellik |
|------|---------|---------|
| Label Studio | Web | Açık kaynak, güçlü, çok format |
| Roboflow | Web | Kolay kullanım, bulut depolama, YOLO export |
| CVAT | Web | Microsoft, gelişmiş işbirliği |
| LabelImg | Masaüstü | Hafif, hızlı başlangıç |
| MakeSense.ai | Web | Ücretsiz, tarayıcıda çalışır |

---

## BLOK 8 — Eğitim (Training) Mantığı

### Temel Eğitim Çağrısı

```python
from ultralytics import YOLO

model = YOLO("yolo11n.pt")   # Hazır ağırlıklarla başla (önerilir)

sonuclar = model.train(
    data    = "dataset.yaml",   # Dataset tanım dosyası
    epochs  = 100,              # Kaç epoch
    imgsz   = 640,              # Görüntü boyutu
    batch   = 16,               # Batch büyüklüğü
    lr0     = 0.01,             # Başlangıç öğrenme hızı
    device  = "cuda:0",         # GPU (yoksa "cpu")
    project = "runs/train",     # Çıktı ana klasörü
    name    = "deney1",         # Deney adı
    patience= 30,               # 30 epoch iyileşme yoksa dur (early stopping)
)
```

---

### Temel Parametreler

#### Epoch

Her eğitim görüntüsünün modelden bir kez geçirilmesi = 1 epoch.

```
Az epoch (10):
  → Yeterince öğrenemedi (underfitting)

Çok epoch (500+):
  → Eğitim verisini ezberledi (overfitting)

Önerilen başlangıç: 50–200 epoch
  Val loss duraksadığında early stopping devreye girer.
```

#### Batch Size

Her ağırlık güncellemesinde aynı anda işlenen görüntü sayısı.

```
Küçük batch (4–8):
  + Az GPU belleği
  - Yavaş, gürültülü güncelleme

Büyük batch (32–64):
  + Hızlı, kararlı güncelleme
  - Çok GPU belleği

GPU'nuz taşmıyorsa mümkün olan en büyük batch'i kullanın.
Pratik: batch artarsa lr de biraz artırılabilir.
```

#### Image Size (imgsz)

Görüntüler modele verilmeden önce bu karesel boyuta yeniden ölçeklenir.

```
imgsz=640  → Varsayılan, çoğu durum için ideal
imgsz=1280 → Küçük nesneler için daha iyi, daha yavaş
imgsz=320  → Hız kritikse, doğruluk düşer
```

#### Learning Rate

Her güncelleme adımında ağırlıkların ne kadar değiştirileceği.

```
Çok yüksek: Eğitim ıraksayabilir (loss artar veya salınır)
Çok düşük:  Eğitim çok yavaş ilerler, takılıp kalabilir

Varsayılan lr0=0.01 çoğu durum için uygundur.
Fine-tuning için: lr0=0.001 (daha küçük adımlar)
```

#### Pretrained Weights (Transfer Learning)

```python
# İki başlangıç yolu:

# 1. Sıfırdan eğitim — nadir tercih edilmeli
model = YOLO("yolo11n.yaml")   # Sadece mimari, ağırlık yok

# 2. Transfer learning — NEREDEYSE HER ZAMAN önerilir
model = YOLO("yolo11n.pt")     # COCO'da eğitilmiş 80 sınıf ağırlığı
```

**Neden transfer learning?** COCO'da eğitilmiş model, kenar tespiti, doku ayrımı gibi temel görsel özellikleri zaten öğrenmiştir. Sizin verinizle yalnızca kendi sınıflarınıza odaklanmasını öğretirsiniz — bu çok daha verimlidir.

---

### Augmentasyon

Augmentasyon eğitim verisini çeşitlendirerek modelin gerçek dünya koşullarına daha iyi adapte olmasını sağlar.

```python
model.train(
    data    = "dataset.yaml",
    epochs  = 100,
    # Augmentasyon parametreleri:
    fliplr   = 0.5,    # %50 yatay çevirme
    flipud   = 0.0,    # Dikey çevirme (genelde kapalı)
    degrees  = 10.0,   # ±10° döndürme
    scale    = 0.5,    # Ölçek değişimi
    hsv_h    = 0.015,  # Ton değişimi
    hsv_s    = 0.7,    # Doygunluk değişimi
    mosaic   = 1.0,    # Mozaik augmentasyonu (4 görüntüyü birleştir)
    mixup    = 0.1,    # MixUp (iki görüntüyü karıştır)
)
```

Ultralytics varsayılan augmentasyonları zaten çoğu durum için iyi çalışır; manuel ayara genellikle gerek kalmaz.

---

### Overfitting ve Underfitting

```
Overfitting (aşırı öğrenme):
  Belirti: train_loss düşük, val_loss yüksek ve artıyor
  Sebep: Az veri, çok epoch, çok büyük model, az augmentasyon
  Çözüm: Daha fazla veri topla, augmentasyonu artır,
          patience ile erken durdur, daha küçük model seç

Underfitting (yetersiz öğrenme):
  Belirti: Her iki loss da yüksek, plateau'da takılı
  Sebep: Az epoch, çok küçük model, yanlış etiketler, çok yüksek lr
  Çözüm: Epoch artır, lr azalt, etiketleri kontrol et, daha büyük model dene

İdeal:
  train_loss ve val_loss birbirine yakın ve ikisi de düşüyor
```

---

### Eğitim Çıktıları

```
runs/train/deney1/
├── weights/
│   ├── best.pt      ← Val metriğine göre en iyi ağırlıklar
│   └── last.pt      ← Son epoch ağırlıkları
├── results.csv      ← Epoch bazlı tüm metrikler
├── results.png      ← Metrik grafikleri (loss, mAP eğrileri)
├── confusion_matrix.png
├── labels.jpg       ← Etiket dağılımı görselleştirmesi
├── val_batch*.jpg   ← Val tahminleri örnek görüntüleri
└── args.yaml        ← Kullanılan tüm parametreler
```

**Her zaman `best.pt` kullanın** — `last.pt` son epoch ağırlıklarıdır, en iyi değil.

---

## BLOK 9 — Prediction / Inference Mantığı

### Model Çıktısını Okuma

```python
from ultralytics import YOLO
import cv2

model   = YOLO("runs/train/deney1/weights/best.pt")
results = model.predict("goruntu.jpg", conf=0.5)

for r in results:
    boxes = r.boxes

    # Koordinatlar
    print(boxes.xyxy)     # [x1, y1, x2, y2] piksel (tensor)
    print(boxes.xywhn)    # [xc, yc, w, h] normalize

    # Sınıf ve güven
    print(boxes.cls)      # Sınıf indeksleri
    print(boxes.conf)     # Güven skorları

    # İnsan okunabilir çıktı
    for box in boxes:
        sinif_id  = int(box.cls)
        sinif_adi = r.names[sinif_id]
        guven     = float(box.conf)
        konum     = box.xyxy[0].tolist()
        print(f"{sinif_adi}: {guven:.2f} @ {konum}")
```

---

### Threshold Seçim Mantığı

```
conf=0.25 (Ultralytics varsayılanı):
  → Geliştirme aşamasında modeli keşfetmek için iyi
  → Düşük güvenli tespitler de görünür

conf=0.5:
  → Çoğu üretim senaryosu için başlangıç noktası

conf=0.7+:
  → Yüksek güven isteniyor, yanlış alarm toleransı düşük

Yüksek hassasiyet (precision) gerekirse: conf artır
Yüksek recall gerekirse: conf düşür
İkisi arasındaki dengeyi F1 skoru gösterir.
```

---

### Gerçek Zamanlı Kamera İnference

```python
import cv2
from ultralytics import YOLO

model = YOLO("yolo11n.pt")
cap   = cv2.VideoCapture(0)

while cap.isOpened():
    ret, frame = cap.read()
    if not ret:
        break

    results  = model.predict(frame, conf=0.5, verbose=False)
    annotated = results[0].plot()   # Tespitler çizilmiş görüntü

    cv2.imshow("YOLOv11 Gerçek Zamanlı", annotated)
    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
```

---

### Tahmin Parametreleri

```python
results = model.predict(
    source   = "goruntu.jpg",   # Görüntü, klasör, URL, video, 0 (kamera)
    conf     = 0.5,             # Confidence threshold
    iou      = 0.7,             # NMS IoU threshold
    imgsz    = 640,             # Inference görüntü boyutu
    device   = "cuda:0",        # Cihaz
    save     = True,            # Sonucu kaydet
    save_txt = True,            # Etiket txt kaydet
    save_conf= True,            # Confidence değerini kaydet
    classes  = [0, 2],          # Yalnızca belirli sınıfları tespit et
    max_det  = 50,              # Maksimum tespit sayısı
    verbose  = False,           # Konsol çıktısını kapat
)
```

---

## BLOK 10 — Değerlendirme Metrikleri

### Temel Kavramlar

```
TP (True Positive):  Gerçek nesne var, model buldu ve doğru sınıfladı ✅
FP (False Positive): Nesne yok, ama model buldu (yanlış alarm) ❌
FN (False Negative): Gerçek nesne var, ama model bulamadı (kaçırma) ❌
TN (True Negative):  Nesne yok, model de bulmadı ✅ (detectionda az kullanılır)
```

---

### Precision

**Ne ölçer:** Model "nesne var" dediğinde ne kadar haklı?

```
Precision = TP / (TP + FP)

Precision = 1.0 → Bulduğu her şey gerçek nesne
Precision = 0.5 → Bulduklarının yarısı yanlış alarm

Yüksek precision isteniyorsa: conf threshold artır
```

---

### Recall

**Ne ölçer:** Gerçek nesnelerin kaçını buluyor?

```
Recall = TP / (TP + FN)

Recall = 1.0 → Hiçbir gerçek nesneyi kaçırmıyor
Recall = 0.5 → Gerçek nesnelerin yarısını buluyor

Yüksek recall isteniyorsa: conf threshold düşür
```

---

### Precision-Recall Dengesi

```
Yüksek Precision, Düşük Recall:
  "Seçici ama eksik" — bulduklarına güvenilir, ama bazı nesneleri kaçırıyor
  Uygun: Yanlış alarmın pahalı olduğu sistemler (tıbbi teşhis)

Düşük Precision, Yüksek Recall:
  "Kapsayıcı ama gürültülü" — neredeyse her şeyi buluyor, yanlış alarmları çok
  Uygun: Kaçırmanın tehlikeli olduğu sistemler (güvenlik, sürüş)

F1 skoru: İkisinin harmonik ortalaması
F1 = 2 × (Precision × Recall) / (Precision + Recall)
```

---

### mAP — Mean Average Precision

YOLO modellerini karşılaştırmak için standart metriktir.

```
mAP@50:
  Tahmin kutusu gerçek kutuyla en az %50 örtüşüyorsa (IoU≥0.5) doğru sayılır.
  Daha gevşek değerlendirme.

mAP@50-95:
  IoU eşiklerini 0.50, 0.55, ..., 0.95 şeklinde alıp ortalamasını alır.
  Daha sıkı ve gerçeği yansıtır — tercih edilen metrik.

mAP@50-95 yorumlama:
  > 0.70 : Çok iyi
  0.50–0.70 : İyi
  0.30–0.50 : Orta
  < 0.30 : Geliştirme gerekiyor
```

---

### Model Değerlendirme

```python
from ultralytics import YOLO

model   = YOLO("runs/train/deney1/weights/best.pt")
metrics = model.val(data="dataset.yaml")

print(f"mAP@50     : {metrics.box.map50:.3f}")
print(f"mAP@50-95  : {metrics.box.map:.3f}")
print(f"Precision  : {metrics.box.p:.3f}")
print(f"Recall     : {metrics.box.r:.3f}")

# Sınıf bazlı metrikler
for i, sinif_adi in model.names.items():
    print(f"  {sinif_adi}: AP50={metrics.box.ap50[i]:.3f}")
```

---

## BLOK 11 — Model Boyutları ve Seçim Stratejisi

### YOLOv11 Model Ailesi

| Model | Parametre | COCO mAP@50-95 | Hız (T4 GPU ms) | Kullanım |
|-------|-----------|---------------|-----------------|---------|
| yolo11n | ~2.6M | ~39.5 | ~1.5 | Edge, gömülü, Jetson, mobil |
| yolo11s | ~9.4M | ~47.0 | ~2.5 | Hız-doğruluk dengesi |
| yolo11m | ~20M  | ~51.5 | ~5.0 | Genel amaçlı üretim |
| yolo11l | ~25M  | ~53.4 | ~7.0 | Yüksek doğruluk gerektiğinde |
| yolo11x | ~57M  | ~54.7 | ~12.0 | Maksimum doğruluk |

*Değerler yaklaşık; donanım ve dataset'e göre değişir.*

---

### Model Seçim Karar Rehberi

```
DONANIM KISITI:
  Raspberry Pi / gömülü → nano (n)
  Orta GPU (RTX 3060)   → small (s) veya medium (m)
  Yüksek GPU (A100)     → large (l) veya xlarge (x)

HIZ GEREKSİNİMİ:
  ≥30 FPS gerçek zamanlı → nano veya small
  Batch işleme (hız kritik değil) → medium veya büyük

DOĞRULUK GEREKSİNİMİ:
  Kritik (tıp, endüstri kalite kontrol) → large veya xlarge
  Genel amaç → medium

VERİ MİKTARI:
  Az veri (<500) → nano (az parametre = az overfitting riski)
  Çok veri (>5000) → büyük model avantaj sağlayabilir

PRATİK YAKLAŞIM:
  1. yolo11n ile başlayın — hızlı iterasyon
  2. Sonuç yetersizse yolo11s veya yolo11m deneyin
  3. Doğruluk kritikse yolo11l veya yolo11x'e geçin
```

---

### Görev Türüne Göre Model Adları

```python
# Nesne tespiti (varsayılan)
model = YOLO("yolo11n.pt")       # n, s, m, l, x

# Segmentasyon
model = YOLO("yolo11n-seg.pt")

# Sınıflandırma
model = YOLO("yolo11n-cls.pt")

# Poz tahmini
model = YOLO("yolo11n-pose.pt")

# Oriented Bounding Box (döndürülmüş kutu)
model = YOLO("yolo11n-obb.pt")
```

---

## BLOK 12 — Export ve Deployment

### Neden Export Gerekir?

Eğitim sırasında PyTorch `.pt` formatı kullanılır. Üretim ortamında ise:
- PyTorch kurulu olmayabilir
- Donanıma özgü optimizasyon gerekebilir (TensorRT, OpenVINO)
- Mobil veya gömülü cihaz söz konusu olabilir
- Daha hızlı inference gerekebilir

---

### Export Kullanımı

```python
from ultralytics import YOLO

model = YOLO("runs/train/deney1/weights/best.pt")

# ONNX — taşınabilir, neredeyse her platformda çalışır
model.export(format="onnx", imgsz=640)

# TensorRT — NVIDIA GPU'larda maksimum hız
model.export(format="engine", imgsz=640)

# OpenVINO — Intel CPU/GPU optimizasyonu
model.export(format="openvino", imgsz=640)

# TFLite — Android ve gömülü sistemler
model.export(format="tflite", imgsz=640)

# CoreML — Apple ekosistemi (iOS, macOS)
model.export(format="coreml", imgsz=640)
```

---

### Format Karşılaştırması

| Format | Hedef Platform | Hız Avantajı | Kurulum Zorluğu |
|--------|---------------|-------------|----------------|
| `.pt` PyTorch | Her yer | Referans | Kolay |
| ONNX | Her yer | %10–30 | Orta |
| TensorRT | NVIDIA GPU | %50–200 | Zor |
| OpenVINO | Intel | Orta | Orta |
| TFLite | Mobil, Raspberry Pi | Düşük bellek | Orta |
| CoreML | Apple (iOS/macOS) | Apple chip için optimize | Apple ekosistem |

---

### Export Sonrası Kullanım

```python
# ONNX modeli — aynı YOLO API'siyle kullanılır
model   = YOLO("best.onnx")
results = model.predict("goruntu.jpg")
```

---

### Deployment Düşünce Çerçevesi

```
Sorulması gereken sorular:
  1. Model nerede çalışacak? (bulut sunucu / edge cihazı / mobil)
  2. Kaç FPS gerekli?
  3. Kabul edilebilir gecikme (latency) nedir?
  4. Model boyutu ne kadar olabilir?
  5. PyTorch / ONNX / TensorRT kurulabilir mi?
  6. İnternet bağlantısı sürekli var mı?

Bu sorulara göre: format seçimi + model boyutu seçimi yapılır.
```

---

## BLOK 13 — Yaygın Hatalar ve Gerçek Dünya Problemleri

---

### Hata 1 — Kötü Etiketleme

**Belirti:** Model eğitildi, metrikler makul görünüyor ama tespitler tutarsız.

```
Sık yapılan etiketleme hataları:
  ✗ Nesnenin yalnızca küçük bir kısmı etiketlendi
  ✗ Farklı kişiler aynı sınıfı farklı şekilde etiketledi
  ✗ Bazı görüntülerde nesneler hiç etiketlenmedi
  ✗ Bounding box nesnenin sınırını aşıyor
  ✗ Yanlış sınıf ID'si atandı

Çözüm:
  - Her sınıf için yazılı tanım hazırlayın
  - Etiketleyicilere örnek görseller verin
  - labels.jpg çıktısını düzenli inceleyin
  - Etiket sayımı tutarlı mı diye kontrol edin
```

---

### Hata 2 — Yetersiz Veri

**Belirti:** Eğitim iyi gidiyor ama yeni görüntülerde başarım düşük (overfitting).

```
Kural: Her sınıf için minimum 100–200 görüntü
       Zor senaryolar için 500+ görüntü

Az veriyle yapılabilecekler:
  - Augmentasyonu agresif kullan (mosaic, mixup, rotation)
  - Daha küçük model seç (nano veya small)
  - Epoch sayısını düşür (overfitting önlemi)
  - patience değerini kullan (erken durdurma)
  - Transfer learning ağırlıklarını kullan
```

---

### Hata 3 — Yanlış Sınıf Tanımı

**Belirti:** Model belirsiz görüntülerde tutarsız sınıflandırma yapıyor.

```
Kötü örnekler:
  ✗ "araç" sınıfı: bazen kamyon dahil, bazen değil
  ✗ "hata" sınıfı: çizik mi, kırık mı, renk anomalisi mi?

İyi sınıf tanımı:
  ✓ Her sınıfın net, yazılı kriteri olsun
  ✓ Sınır durumlar belgelensin (dahil mi / hariç mi?)
  ✓ Farklı etiketleyiciler arasında anlaşma protokolü olsun
```

---

### Hata 4 — Yanlış Threshold Kullanımı

```
conf çok yüksek (örn: 0.95):
  → "Model hiç nesne bulamıyor" gibi görünüyor
  → Aslında model çalışıyor ama aşırı seçici

conf çok düşük (örn: 0.05):
  → Her yere bounding box geliyor
  → Yanlış pozitifler uygulamayı kullanılmaz kılıyor

Çözüm:
  - Geliştirme aşamasında conf=0.25 ile başlayın
  - Precision-Recall dengesini belirleyin
  - Uygulamanın toleransına göre ayarlayın
  - Birkaç farklı threshold değerini test edin
```

---

### Hata 5 — Train/Val Veri Sızıntısı

```
✗ Yanlış:
  Val görüntüleri yanlışlıkla train klasörüne de kopyalandı.
  Model bu görüntüleri eğitim sırasında gördü.
  Val metrikleri çok yüksek çıkıyor — ama bu yanıltıcı.

✓ Kontrol:
  len(set(train_dosyalar) & set(val_dosyalar)) == 0 olmalı
```

---

### Hata 6 — Gerçek Dünya Koşulları Gözardı Edildi

```
Eğitim koşulu        Üretim koşulu         Sonuç
────────────────────────────────────────────────────────────
İyi aydınlatma   →   Gece / karanlık   →   Başarım düşer
Düz arka plan    →   Karmaşık sahne    →   Yanlış tespit artar
Standart açı     →   Üst/yan bakış     →   Kaçırma artar
Net görüntü      →   Motion blur       →   Tespit bozulur
Büyük nesneler   →   Uzakta küçük      →   Kaçırma artar

Çözüm: Eğitim verisi, üretim koşullarını yansıtmalı.
        Gerçek sahne fotoğrafları kullanın.
```

---

### Hata 7 — Küçük Nesne Sorunları

YOLO varsayılan olarak küçük nesnelerde zorlanır.

```
İyileştirme yöntemleri:
  1. imgsz artır: imgsz=1280 (daha fazla piksel detayı)
  2. Küçük nesneler için daha fazla örnek topla
  3. SAHI (Slicing Aided Hyper Inference) kullan:
     Görüntüyü örtüşen tile'lara böl, her tile üzerinde inference yap
  4. Büyük model kullan (l veya x)
  5. Küçük nesneleri yakın çekimle etiketle
```

---

### Hata 8 — Metrik Yanlış Yorumlama

```
Yanıltıcı durum 1:
  "mAP@50 = 0.92 → Harika model!"
  Gerçek: mAP@50-95 = 0.45 → Orta kalite

Yanıltıcı durum 2:
  "Precision = 0.95 → Mükemmel!"
  Gerçek: Recall = 0.30 → Nesnelerin %70'ini kaçırıyor

Yanıltıcı durum 3:
  "Eğitim loss düşük → İyi model"
  Gerçek: Val loss yüksek → Overfitting var

Doğru yaklaşım:
  → Her zaman hem Precision hem Recall'a bakın
  → mAP@50-95 birincil metriğiniz olsun
  → Val metriklerine odaklanın, train metriklerine değil
  → Confusion matrix'i inceleyin
```

---

### Hata 9 — Overfitting Fark Edilmemesi

```python
# results.csv'yi takip edin:

# İyi eğitim:
# epoch  train/box_loss  val/box_loss
#   10      1.20              1.30
#   50      0.60              0.65
#  100      0.40              0.42   ← Her ikisi de düşüyor

# Overfitting:
# epoch  train/box_loss  val/box_loss
#   10      1.20              1.30
#   50      0.50              0.70
#  100      0.30              0.95   ← Train düşüyor, val artıyor!

# Çözüm: Early stopping
model.train(
    data    = "dataset.yaml",
    epochs  = 300,
    patience= 30    # 30 epoch iyileşme yoksa dur
)
```

---

### Hata 10 — Sadece COCO Modeliyle Test Etmek

```
Yanılgı: "yolo11n.pt ile test ettim, iyi çalıştı → Deployment'a hazır!"

Gerçek:
  yolo11n.pt COCO'da eğitildi (80 genel sınıf).
  Sizin özel sınıflarınız (örn: "üretim hatası", "kendi ürününüz") için
  yeniden eğitim (fine-tuning) yapılmadan iyi sonuç beklenmemelidir.

Çözüm:
  Özel veri setinizle fine-tuning yapın.
  Hazır modeli yalnızca hızlı prototip için kullanın.
```

---

*Son güncelleme: Mart 2026*
*İlgili dosyalar: `yolo-uygulamalar.md`*
*Katkıda bulunmak için: CONTRIBUTING.md*
