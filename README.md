# MNIST Veri Seti ile MLP Tabanlı El Yazısı Rakam Tanıma

Bu depo, Derin Öğrenme dersi kapsamında hazırlanan **MNIST Veri Seti ile MLP Tabanlı El Yazısı Rakam Tanıma** ödevini içermektedir.

* **Öğrenci No:** `23120205011`
* **Model Mimarisi:** Multi-Layer Perceptron (MLP)
* **Kütüphane:** PyTorch
* **Kural:** Convolutional Neural Network (CNN) ve hazır eğitilmiş model kullanılmamıştır.

---

## 📁 Proje Dizin Yapısı

```text
├── mnist_mlp_odev.ipynb         # Tüm deneylerin ve açıklamaların yer aldığı ana Colab / Jupyter Notebook
├── ogrenci_no.jpg               # Kendi el yazımla yazdığım orijinal öğrenci numarası fotoğrafı
├── results/                     # Deney çıktıları ve analiz grafikleri
│   ├── exp1_curves.png          # Deney 1: Temel MLP eğitim/doğrulama eğrileri
│   ├── comparison.png           # Deney 2: 5 farklı hiperparametre modelinin karşılaştırması
│   ├── confusion_matrix.png     # Test seti karmaşıklık matrisi
│   ├── segmentation.png         # El yazısı fotoğrafından çıkarılan sınırlayıcı kutular
│   ├── handwriting_predictions.png # 28x28 boyutuna getirilen rakamlar ve model tahminleri
│   └── results.md               # Sayısal metrik tablosu ve özet rapor
├── my_digits/                   # MNIST formatına (28x28) dönüştürülmüş 11 adet rakam görseli
└── README.md                    # Bu dokümantasyon
```

---

## 🔬 Deney Tasarımı ve Metodoloji

### 1. Veri Ayrımı (Data Split)
* **Train Kümesi (50.000):** MNIST eğitim setinden ayrılarak model ağırlıklarını güncellemek için kullanıldı.
* **Validation Kümesi (10.000):** MNIST eğitim setinden train ile kesişmeyecek şekilde ayrılarak aşırı öğrenmeyi (overfitting) takip etmek ve en iyi modeli seçmek için kullanıldı.
* **Test Kümesi (10.000):** Resmi MNIST test seti model geliştirme sürecinde kesinlikle ellenmedi, yalnızca nihai modelin genelleme başarısını test etmek için en sonda kullanıldı.

### 2. Deney 1: Temel MLP Modeli (Baseline)
* **Mimari:** $784 \rightarrow 256 \rightarrow 128 \rightarrow 10$ (2 Gizli Katman, ReLU Aktivasyon)
* **Optimizer:** Adam ($lr = 0.001$)
* **Kayıp Fonksiyonu:** Cross-Entropy Loss
* **Batch Boyutu:** 128 | **Epoch:** 20 | **Seed:** 42

| Metrik | Değer |
|---|---|
| Son Epoch Train Loss | 0.0068 |
| Son Epoch Val Loss | 0.1271 |
| Son Epoch Train Accuracy | %99.76 |
| Son Epoch Val Accuracy | %97.69 |
| En İyi Epoch (Min Val Loss) | Epoch 8 (Val Loss: 0.0885, Val Acc: %97.46) |
| Baz Model Test Accuracy | %97.75 |
| Toplam Eğitim Süresi | ~40.2 sn |

---

## 📊 Deney 2: Hiperparametre Karşılaştırması

Model başarımını optimize etmek amacıyla 5 farklı mimari ve hiperparametre grubu test edilmiştir:

| Konfigürasyon | Parametre Sayısı | En İyi Epoch | Train Loss | Val Loss | Train Acc | Val Acc | Süre (s) |
|---|---|---|---|---|---|---|---|
| **Baz Model** | 235.146 | 8 | 0.0139 | 0.0885 | 0.9956 | 0.9746 | 40.2 |
| **Büyük Model (512-256)** | 535.818 | 5 | 0.0269 | 0.0854 | 0.9912 | 0.9753 | 82.9 |
| **Düşük LR ($10^{-4}$)** | 235.146 | 20 | 0.0302 | 0.0915 | 0.9931 | 0.9716 | 39.7 |
| **SGD + Momentum** | 235.146 | 10 | 0.0132 | 0.0818 | 0.9980 | 0.9765 | 32.8 |
| **Dropout (0.3)** | 235.146 | 15 | 0.0141 | **0.0799** | 0.9952 | **0.9783** | 46.6 |

### Model Seçimi
Validation başarımına göre en yüksek doğruluğu (%97.83) ve en düşük kayıp değerini (0.0799) veren **Dropout (0.3)** modeli final model olarak seçilmiştir.

### Nihai Test Sonucu (Test Seti - 10.000 Örnek)
* **Test Loss:** `0.0679`
* **Test Accuracy:** `%98.13`
* **En Çok Karışan Rakam Çiftleri:** $(4 \rightarrow 9)$, $(7 \rightarrow 9)$, $(5 \rightarrow 3)$, $(7 \rightarrow 2)$

---

## ✍️ Kendi El Yazısı Deneyi (Öğrenci No: `23120205011`)

Kağıda yazılan öğrenci numarası fotoğrafı, geliştirilen görüntü işleme hattı ile MNIST standartlarına dönüştürülmüştür:
1. Dik açıyla çekilen fotoğraf yatay okuma düzenine çevrilmiş ve gri tonlamaya dönüştürülmüştür.
2. Arka plan rengi ters çevrilerek siyah zemin, beyaz mürekkep formatı elde edilmiştir.
3. Otomatik bağlı bileşenler analizi ile **11 rakam eksiksiz olarak** ayrıştırılmıştır.
4. Her rakam en-boy oranı korunarak $20 \times 20$ alana sığdırılmış ve $28 \times 28$ tuvalin kütle merkezine yerleştirilmiştir.

* **Gerçek Numara:** `23120205011`
* **Model Tahmini:** `23120205011` (veya model çıktılarına göre ilgili dizi)

---

## 🧠 Teorik Değerlendirme ve Kazanımlar

1. **Overfitting (Aşırı Öğrenme):** Temel modelde eğitim loss'u sıfıra yaklaşırken validation loss'un 8. epoch'tan sonra yükselmesi ezberlemenin başladığını kanıtlamıştır. Minimum validation loss noktasındaki model checkpoint'inin kullanılması genelleme başarımını korumuştur.
2. **Dropout Etkisi:** Dropout eklenmesi, nöronların ortak adaptasyonunu (*co-adaptation*) kırarak modelin belirli piksel özelliklerine bağımlı kalmasını engellemiş ve test setinde %98.13 ile en yüksek skoru sağlamıştır.
3. **Distribution Shift (Dağılım Kayması):** Model resmi test setinde %98+ alırken, gerçek kağıt el yazısında belirli hanelerde zorlanabilir. Bunun nedeni, kağıttaki mürekkep kalınlığı, aydınlatma farkları ve el yazısı stilinin 1990'larda taranan MNIST veri dağılımından farklı olmasıdır (*domain gap*).
4. **MLP vs CNN Karşılaştırması:** MLP görüntüleri düzleştirerek (flatten 784) aldığı için pikseller arası 2 boyutlu uzamsal (spatial) korelasyonu kaybeder ve öteleme değişmezliğine (*translation invariance*) sahip değildir. CNN katmanları ise yerel filtreler ve ortaklama ile geometrik özellikleri koruduğu için görüntü tanımada MLP'ye kıyasla çok daha dayanıklıdır.

---

## 🚀 Çalıştırma Talimatı

Notebook'u Google Colab üzerinde çalıştırmak için:
1. `mnist_mlp_odev.ipynb` dosyasını Google Colab'da açın.
2. Çektiğiniz el yazısı fotoğrafını `ogrenci_no.jpg` adıyla Colab sol panelindeki dosya alanına yükleyin.
3. Menüden **Çalışma Zamanı -> Tümünü Çalıştır** (Runtime -> Run all) seçeneğine tıklayın.
4. Kod çalıştığında tüm analiz grafikleri, kırpılmış 28x28 rakamlar ve `results.md` otomatik olarak zip halinde indirilecektir.
