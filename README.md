# QAOA-VQC-QUBO-Notebooks


## 1. Colab Notebook (`Q_OmicSelect_Egitim_Notebook.ipynb`)

**6 bölüm**, sıfırdan başlayan biri için tasarlandı:

- **Bölüm 0**: Kütüphane kurulumu (tek tıkla)
- **Bölüm 1**: QUBO — 3 genli somut örnekle, her kombinasyonun enerjisini hesaplayarak
- **Bölüm 2**: Kuantum kapılar — H, X, RZ, CNOT, Bloch küresi görseli
- **Bölüm 3**: QAOA — Sıfırdan inşa, scipy optimizer, optimizasyon geçmişi grafikleri
- **Bölüm 4**: VQC — Gen ekspresyonu sınıflandırması, karar sınırı görselleştirmesi
- **Bölüm 5**: Mini Q-OmicSelect prototipi — Sentetik scRNA-seq verisiyle tam pipeline
- **Bonus**: p=1 vs p=2 QAOA karşılaştırması

---

## 2. Çalışma Akış Planı (`Q_OmicSelect_Calisma_Plani.md`)

**5 Faz, 12-16 hafta:**

| Faz | Süre | İçerik |
|-----|------|--------|
| 1 | Hafta 1-3 | Lineer cebir, qubit, QUBO |
| 2 | Hafta 4-7 | QAOA derinleşme, VQC, barren plateau |
| 3 | Hafta 8-10 | scRNA-seq, CPO/GWO, Gen Ontolojisi |
| 4 | Hafta 11-13 | LLM ajan, araç entegrasyonu |
| 5 | Hafta 14-16 | Makale yazımı, hedef dergiler |

---

## 3. Detaylı Ders Notları (`Q_OmicSelect_Ders_Notlari.md`)

9 bölüm, her formülün Türkçe açıklamasıyla:
- QUBO matematiksel türetme (kısıt penaltısı, binary özelliği)
- QAOA Hamiltonian'lar, parametreli kapılar
- VQC veri kodlama yöntemleri, parameter-shift rule
- CPO/GWO entegrasyon kodu şablonu
- LLM ajan araç tasarımı
- Benchmark metrikleri ve yaygın hatalar tablosu

