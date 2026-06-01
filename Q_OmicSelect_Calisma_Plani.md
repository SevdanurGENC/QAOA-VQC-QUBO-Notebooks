# 🧬⚛️ Q-OmicSelect: Çalışma Akış Planı

**Hedef**: QAOA/VQC ile QUBO tabanlı kuantum öznitelik seçimi + LLM ajan entegrasyonunu sıfırdan öğrenmek ve Q-OmicSelect'i hayata geçirmek.

---

## 🗓️ GENEL ZAMAN ÇİZELGESİ

```
Toplam Süre: ~12-16 Hafta (yarı zamanlı çalışma için)
Günde 2-3 saat varsayımıyla
```

---

## 📍 FAZ 1: TEMEL ZEMIN (Hafta 1-3)

### Hafta 1: Matematiksel Temel
**Hedef**: Kuantum bilgisayarın matematiksel dilini anlamak

| Gün | Konu | Kaynak Önerileri |
|-----|------|-----------------|
| 1-2 | Lineer Cebir: vektörler, matrisler, öz değerler | 3Blue1Brown: "Essence of Linear Algebra" (YouTube) |
| 3-4 | Kompleks sayılar, Dirac notasyonu \|ψ⟩ | Nielsen & Chuang Bölüm 1 |
| 5-6 | Qubit, Bloch küresi, süperpozisyon | Qiskit Textbook Bölüm 1 |
| 7 | Tekrar + Mini Quiz (notebook'taki QUBO bölümünü çalış) | |

**Pratik**: Qiskit'i kurun, ilk devrenizi çalıştırın (H kapısı → ölçüm)

---

### Hafta 2: Kuantum Kapılar ve Devreler
**Hedef**: Temel kuantum kapılarını ve devreleri anlamak

| Gün | Konu |
|-----|------|
| 1-2 | Pauli kapıları (X, Y, Z), Hadamard |
| 3-4 | Parametreli kapılar (RX, RY, RZ), rotasyon matrisleri |
| 5-6 | CNOT, Toffoli — çok qubitli kapılar, dolaşıklık |
| 7 | Bell durumları, ölçüm, decoherence kavramı |

**Pratik**: Notebook Bölüm 2'yi çalıştırın, kendi devrenizi yazın

---

### Hafta 3: Optimizasyon ve QUBO
**Hedef**: Gerçek problemleri QUBO'ya dönüştürebilmek

| Gün | Konu |
|-----|------|
| 1-2 | Kombinatoryal optimizasyon nedir? Max-Cut, TSP örnekleri |
| 3-4 | QUBO matematiksel tanımı, enerji manzarası |
| 5-6 | Ising modeli, QUBO ↔ Ising dönüşümü |
| 7 | Gen seçimini QUBO olarak formüle etme |

**Pratik**: Notebook Bölüm 1'i bitirin, kendi 4-gen QUBO'nuzu yazın

---

## 📍 FAZ 2: QAOA & VQC DERİNLEŞME (Hafta 4-7)

### Hafta 4: QAOA Teorisi
**Hedef**: QAOA'nın matematiğini anlamak

| Gün | Konu |
|-----|------|
| 1-2 | Hamiltonian simülasyonu, zaman evrimi |
| 3-4 | Adiabatik kuantum hesaplama → QAOA bağlantısı |
| 5-6 | Maliyet Hamiltonian'ı H_C, Mixer Hamiltonian H_B |
| 7 | p katman teoremi, performans garanti |

---

### Hafta 5: QAOA Uygulaması
**Hedef**: Kendi QAOA implementasyonunuzu yazmak

| Gün | Konu |
|-----|------|
| 1-2 | Qiskit ile QAOA p=1 (Notebook Bölüm 3) |
| 3-4 | Klasik optimizer seçimi: COBYLA, SPSA, L-BFGS-B |
| 5-6 | QAOA p=2, p=3 karşılaştırması |
| 7 | Gerçek QUBO problemini QAOA ile çöz |

---

### Hafta 6: VQC (Değişken Kuantum Devresi)
**Hedef**: VQC'yi anlayıp sınıflandırma için kullanmak

| Gün | Konu |
|-----|------|
| 1-2 | VQC mimarisi: encoding + ansatz + measurement |
| 3-4 | Veri kodlama yöntemleri: amplitude, angle, basis |
| 5-6 | Gradient hesaplama: parameter-shift rule |
| 7 | Notebook Bölüm 4'ü çalıştır, VQC eğit |

---

### Hafta 7: Barren Plateau ve Pratik Sorunlar
**Hedef**: QAOA/VQC'nin sınırlarını ve çözümlerini anlamak

| Konu | Ne Öğrenilecek |
|------|---------------|
| Barren plateau sorunu | Gradient'lar neden yok olur? |
| Noise modellemesi | Gerçek kuantum cihazlarında gürültü |
| Shot gürültüsü | Az ölçüm → belirsiz sonuç |
| Hiper-parametre ayarı | p, shots, optimizer seçimi |

---

## 📍 FAZ 3: BİYOİNFORMATİK ENTEGRASYONU (Hafta 8-10)

### Hafta 8: Omik Veri Analizi
**Hedef**: scRNA-seq ve gen ekspresyon verilerini analiz etmek

| Konu | Araçlar |
|------|---------|
| scRNA-seq veri yapısı | Scanpy, AnnData |
| Normalizasyon, QC filtreleme | Seurat benzeri preprocessing |
| Boyut indirgeme (PCA, UMAP) | scikit-learn, scanpy |
| Gen korelasyon analizi | Pearson, Spearman |

**Pratik**: GEO'dan küçük bir scRNA-seq verisi indirin, scanpy ile analiz edin

---

### Hafta 9: Öznitelik Seçimi Algoritmaları
**Hedef**: CPO ve GWO algoritmalarını anlamak

| Konu | Açıklama |
|------|---------|
| Wrapper vs Filter vs Embedded | Öznitelik seçim kategorileri |
| GWO (Grey Wolf Optimizer) | Sürü zekası tabanlı optimizasyon |
| CPO (Coati Optimization) | Yeni metasezgisel algoritma |
| Uygunluk fonksiyonu tasarımı | Kuantum alt-rutini nasıl çağırılır? |

---

### Hafta 10: Gen Ontolojisi ve Biyolojik Yorum
**Hedef**: GO önsellerini modele entegre etmek

| Konu | Araçlar |
|------|---------|
| Gene Ontology (GO) kavramı | go-basic.obo dosyası |
| GO zenginleştirme analizi | goatools, g:Profiler |
| QUBO'ya biyolojik prior ekleme | Q matrisine biyolojik bilgi kodlama |
| Pathway analizi | KEGG, Reactome |

---

## 📍 FAZ 4: LLM AJAN ENTEGRASYONu (Hafta 11-13)

### Hafta 11: LLM Ajan Mimarisi
**Hedef**: Araç kullanan LLM ajanını tasarlamak

| Konu | Araçlar |
|------|---------|
| ReAct framework | LangChain, LlamaIndex |
| Araç tanımlama (tool use) | OpenAI function calling / Anthropic tool use |
| Ajan döngüsü tasarımı | Observe → Think → Act |
| Kuantum alt-rutini araç olarak sarma | API wrapper |

---

### Hafta 12: Ajan Implementasyonu

```python
# Hedef kod yapısı
tools = {
    "quantum_feature_selector": qaoa_vqc_qubo_solver,
    "go_prior_generator": gene_ontology_recommender,
    "cpo_optimizer": cpo_gwo_wrapper,
    "biology_explainer": pathway_analyzer
}

agent = LLMAgent(
    model="claude-3-5-sonnet",
    tools=tools,
    system_prompt=bio_expert_prompt
)
```

---

### Hafta 13: Entegrasyon ve Test

| Görev |
|-------|
| Tüm bileşenleri birleştir |
| Benchmark: klasik vs kuantum karşılaştırması |
| Tekrarlanabilirlik testleri |
| Gerçek scRNA-seq verisi üzerinde çalıştır |

---

## 📍 FAZ 5: MAKALE YAZIMI (Hafta 14-16)

### Yazılacak Bölümler

| Bölüm | İçerik |
|-------|--------|
| Abstract | Q-OmicSelect'in 3 katkısı: LLM+Quantum+CPO |
| Introduction | Omik veri boyutluluğu, mevcut yöntemlerin eksikleri |
| Methods | QUBO formülasyonu, QAOA/VQC detayları, CPO entegrasyonu |
| Results | Benchmark karşılaştırmaları, gen seçim performansı |
| Discussion | Kuantum avantajı, sınırlamalar, gelecek çalışmalar |

### Hedef Dergiler (Yüksek Etki)
1. **Nucleic Acids Research** (IF: ~19) — biyoinformatik odaklı
2. **Bioinformatics** (IF: ~7) — yöntem odaklı
3. **npj Quantum Information** (IF: ~9) — kuantum odaklı
4. **Briefings in Bioinformatics** (IF: ~13) — review + yöntem

---

## 🎯 KRİTİK BAŞARI FAKTÖRLERİ

### ✅ Yapmanız Gerekenler
- [ ] Her hafta notebook bölümünü çalıştırın ve değiştirin
- [ ] Simülasyon ile başlayın, gerçek donanım sonra
- [ ] Küçük başlayın: 4 gen → 8 gen → 20 gen
- [ ] Benchmark karşılaştırması: klasik SVM-RFE ile kıyasla
- [ ] Sonuçları kaydedin (MLflow veya W&B kullanın)

### ❌ Yapmayın
- Geniş veri ile başlamayın (önce concept proof)
- QAOA'dan gerçek kuantum avantajı beklemeyin (NISQ çağı)
- Her şeyi mükemmel yapmaya çalışmayın (iteratif geliştirin)

---

## 📚 ÖĞRENME KAYNAKLARI

### Ücretsiz Kaynaklar
| Kaynak | Link | Ne İçin? |
|--------|------|----------|
| Qiskit Textbook | qiskit.org/learn | QAOA, VQC |
| PennyLane Demos | pennylane.ai/qml | VQC, gradient |
| Nielsen & Chuang | Kitap | Kuantum temel |
| D-Wave QUBO Guide | docs.dwavesys.com | QUBO detayı |
| Scanpy Tutorial | scanpy.readthedocs.io | scRNA-seq |

### Anahtar Makaleler
1. Farhi et al. 2014 — QAOA orijinal makale
2. Cerezo et al. 2021 — VQC review
3. Glover et al. 2018 — QUBO tutorial
4. Hao et al. 2021 — scRNA-seq feature selection review

---

## 📊 İLERLEME TAKİP TABLOSU

```
FAZ 1 (Temel)        □□□□□□□□□□  0%
FAZ 2 (QAOA/VQC)     □□□□□□□□□□  0%
FAZ 3 (Biyoinf.)     □□□□□□□□□□  0%
FAZ 4 (LLM Ajan)     □□□□□□□□□□  0%
FAZ 5 (Makale)       □□□□□□□□□□  0%
```

---

*Son güncelleme: 2026 | Q-OmicSelect Projesi*
