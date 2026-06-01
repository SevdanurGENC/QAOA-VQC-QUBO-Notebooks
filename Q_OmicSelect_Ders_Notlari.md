# 📚 Q-OmicSelect Detaylı Ders Notları
## Sıfırdan QAOA/VQC, QUBO ve Omik Veri Optimizasyonu

---

# BÖLÜM 1: QUBO — Optimizasyon Problemlerinin Kuantum Dili

## 1.1 Neden QUBO?

Düşünün: Bir kanser hastasından alınan dokudan 20.000 gen ölçüyorsunuz. Hangi 50 gen en önemli? Bu problem **kombinatoryal optimizasyon** problemidir.

Olası kombinasyon sayısı: C(20000, 50) ≈ 10^190

Bu kadar büyük bir uzayı klasik bilgisayarlar verimli tarayamaz. İşte burada QUBO devreye giriyor — problemi **kuantum bilgisayarın anlayabileceği** bir formata dönüştürüyor.

---

## 1.2 QUBO'nun Tam Matematiksel Tanımı

**QUBO = Quadratic Unconstrained Binary Optimization**

$$\min_{x \in \{0,1\}^n} \quad E(\mathbf{x}) = \mathbf{x}^T Q \mathbf{x} = \sum_{i=1}^{n} Q_{ii} x_i + \sum_{i<j} Q_{ij} x_i x_j$$

### Terimler ne anlama gelir?

| Sembol | Boyut | Anlam |
|--------|-------|-------|
| $\mathbf{x}$ | n×1 | Karar vektörü: $x_i = 1$ (gen seçildi), $x_i = 0$ (seçilmedi) |
| $Q$ | n×n | QUBO matrisi: üst üçgensel kullanılır |
| $Q_{ii}$ | Skaler | Gen $i$'nin tek başına maliyeti/faydası |
| $Q_{ij}$ | Skaler | Gen $i$ ve $j$ birlikte seçilince ekstra maliyet/fayda |

### Önemli özellik: Binary'de x² = x

$$x_i \in \{0,1\} \implies x_i^2 = x_i$$

Çünkü: $0^2 = 0$ ve $1^2 = 1$. Bu sayede karesel terimler sadece köşegene katkı yapar.

---

## 1.3 Gen Seçimini QUBO'ya Dönüştürme (Detaylı)

**Problem**: n gen arasından en iyi k geni seç.

**Amaç fonksiyonu** (maximize):
$$f(\mathbf{x}) = \sum_{i=1}^{n} s_i x_i - \sum_{i<j} c_{ij} x_i x_j$$

- $s_i$: Gen $i$'nin önemi (mutual information, differential expression, vb.)
- $c_{ij}$: Gen $i$-$j$ arasındaki gereksiz örtüşme (korelasyon)

**Kısıt**: Tam k gen seçilmeli:
$$\sum_{i=1}^{n} x_i = k$$

**Kısıtı penaltiye dönüştürme**:
$$\text{Penaltı} = \lambda \left(\sum_{i=1}^{n} x_i - k\right)^2$$

$\lambda > 0$ büyük seçilirse kısıt çok güçlü olur (ihlal çok pahalı olur).

**Genişletme** (binary özelliği $x_i^2 = x_i$ kullanılarak):
$$\left(\sum_i x_i - k\right)^2 = \sum_i x_i^2 + 2\sum_{i<j}x_i x_j - 2k\sum_i x_i + k^2$$
$$= \sum_i x_i + 2\sum_{i<j}x_i x_j - 2k\sum_i x_i + k^2$$
$$= (1-2k)\sum_i x_i + 2\sum_{i<j}x_i x_j + k^2$$

**Toplam QUBO** (minimize):
$$E(\mathbf{x}) = -f(\mathbf{x}) + \lambda \cdot \text{Penaltı}$$

**QUBO Matrisi Q'nun Elemanları**:
$$Q_{ii} = -s_i + \lambda(1 - 2k)$$
$$Q_{ij} = c_{ij} + 2\lambda \quad (i < j)$$

> **Not**: QUBO matrisi üst üçgenseldir (sadece $i \leq j$ terimleri doldurulur).

---

## 1.4 QUBO'yu Ising Modeline Dönüştürme

Kuantum bilgisayarlar için QUBO'yu **Ising modeline** dönüştürmek gerekir:

**Dönüşüm**: $x_i = \frac{1 - z_i}{2}$, burada $z_i \in \{-1, +1\}$

Ising Hamiltonian:
$$H_{Ising} = \sum_i h_i z_i + \sum_{i<j} J_{ij} z_i z_j + \text{const}$$

**Katsayı dönüşümleri**:
$$h_i = \frac{Q_{ii}}{2} + \frac{1}{4}\sum_{j \neq i} Q_{ij}$$
$$J_{ij} = \frac{Q_{ij}}{4}$$

Bu dönüşüm QAOA'da Pauli-Z operatörleri ile çalışmamıza imkan tanır.

---

# BÖLÜM 2: KUANTUM BİLGİSAYAR TEMELLERİ

## 2.1 Qubit — Kuantum Bit

**Klasik bit**: Kesinlikle 0 veya kesinlikle 1.

**Qubit**: Ölçüm yapılana kadar belirsiz — hem 0 hem de 1 **olabilir**.

Matematiksel gösterim (Dirac notasyonu):
$$|\psi\rangle = \alpha|0\rangle + \beta|1\rangle$$

burada $\alpha, \beta \in \mathbb{C}$ ve $|\alpha|^2 + |\beta|^2 = 1$.

- $|\alpha|^2$ = 0 ölçme **olasılığı**
- $|\beta|^2$ = 1 ölçme **olasılığı**

### Sütun vektör gösterimi:
$$|0\rangle = \begin{pmatrix}1\\0\end{pmatrix}, \quad |1\rangle = \begin{pmatrix}0\\1\end{pmatrix}, \quad |\psi\rangle = \begin{pmatrix}\alpha\\\beta\end{pmatrix}$$

---

## 2.2 Bloch Küresi

Tüm olası qubit durumları bir küre yüzeyinde gösterilebilir:

```
         |0⟩ (Kuzey Kutubu)
          ↑
          |
    |−y⟩←─┼─→|+y⟩
          |
          ↓
         |1⟩ (Güney Kutubu)
```

- **Kuzey kutubu** = |0⟩
- **Güney kutubu** = |1⟩  
- **Ekvator** = Süperpozisyon durumları
- **|+⟩** = (|0⟩+|1⟩)/√2 (x pozitif yönü)
- **|-⟩** = (|0⟩-|1⟩)/√2 (x negatif yönü)

Bir qubit'e kapı uygulamak = Bloch küresi üzerinde **rotasyon** yapmak.

---

## 2.3 Temel Kuantum Kapıları

### Hadamard Kapısı (H)

$$H = \frac{1}{\sqrt{2}}\begin{pmatrix}1&1\\1&-1\end{pmatrix}$$

**Etkisi**: $H|0\rangle = |+\rangle = \frac{|0\rangle+|1\rangle}{\sqrt{2}}$

**Kullanım**: Süperpozisyon yaratmak. QAOA'da başlangıç durumu için her qubit'e uygulanır.

---

### Pauli Kapıları

$$X = \begin{pmatrix}0&1\\1&0\end{pmatrix}, \quad Y = \begin{pmatrix}0&-i\\i&0\end{pmatrix}, \quad Z = \begin{pmatrix}1&0\\0&-1\end{pmatrix}$$

- **X**: Klasik NOT kapısı. $|0\rangle \leftrightarrow |1\rangle$
- **Y**: X + Z kombinasyonu
- **Z**: Faz kapısı. $|0\rangle \to |0\rangle$, $|1\rangle \to -|1\rangle$

---

### Döndürme Kapıları (Parametreli)

$$R_X(\theta) = e^{-i\theta X/2} = \begin{pmatrix}\cos(\theta/2) & -i\sin(\theta/2)\\-i\sin(\theta/2) & \cos(\theta/2)\end{pmatrix}$$

$$R_Y(\theta) = e^{-i\theta Y/2} = \begin{pmatrix}\cos(\theta/2) & -\sin(\theta/2)\\\sin(\theta/2) & \cos(\theta/2)\end{pmatrix}$$

$$R_Z(\theta) = e^{-i\theta Z/2} = \begin{pmatrix}e^{-i\theta/2} & 0\\0 & e^{i\theta/2}\end{pmatrix}$$

**Bu kapılar QAOA ve VQC'nin temelidir** — $\theta$ optimize edilecek parametredir.

---

### CNOT Kapısı (İki Qubit)

$$CNOT = \begin{pmatrix}1&0&0&0\\0&1&0&0\\0&0&0&1\\0&0&1&0\end{pmatrix}$$

**Etkisi**: Kontrol qubit (q0) = 1 ise hedef qubit (q1)'i çevir.

**Neden önemli?** Dolaşıklık (entanglement) yaratır. QAOA'da ZZ etkileşimini kodlamak için kullanılır.

---

### ZZ Etkileşimi (QAOA'da kritik)

$$e^{-i\gamma Z_0 Z_1} = CNOT \cdot (I \otimes R_Z(2\gamma)) \cdot CNOT$$

Bu işlem, QUBO matrisinin $Q_{ij}$ çift terimini kodlar.

---

## 2.4 n Qubit Sistemleri

n qubit = $2^n$ boyutlu Hilbert uzayı

Örneğin 3 qubit için temel durumlar:
$$|000\rangle, |001\rangle, |010\rangle, |011\rangle, |100\rangle, |101\rangle, |110\rangle, |111\rangle$$

Her durum bir gen kombinasyonunu temsil eder:
- $|000\rangle$: Hiç gen seçilmedi
- $|101\rangle$: Gen 0 ve Gen 2 seçildi, Gen 1 seçilmedi

---

# BÖLÜM 3: QAOA — Kuantum Yaklaşık Optimizasyon Algoritması

## 3.1 Tarihsel Arka Plan

2014'te Farhi, Goldstone ve Gutmann tarafından önerildi.

**Fikir**: Adyabatik kuantum hesaplamadan ilham aldı.  
Adyabatik teorem: Sistemi yeterince yavaş değiştirirseniz, zemin durumda kalırsınız.

QAOA bunu **ayrık katmanlar** ile yaklaşık olarak yapar — tam adyabatik değil ama NISQ (Noisy Intermediate-Scale Quantum) cihazlara uygun.

---

## 3.2 QAOA'nın Tam Teorisi

### Hamiltonian'lar

**Problem Hamiltonian** $H_C$: QUBO problemini kodlar.

$$H_C = \sum_i Q_{ii} Z_i + \sum_{i<j} Q_{ij} Z_i Z_j$$

(Burada $Z_i$ = qubit i'ye uygulanan Pauli-Z operatörü)

**Mixer Hamiltonian** $H_B$: Çözüm uzayını keşfeder.

$$H_B = \sum_{i=1}^{n} X_i$$

(Burada $X_i$ = qubit i'ye uygulanan Pauli-X operatörü)

---

### QAOA Devresi

**Adım 1**: Eşit süperpozisyon başlangıcı

$$|s\rangle = |+\rangle^{\otimes n} = H^{\otimes n}|0\rangle^{\otimes n}$$

**Adım 2**: p katman uygula (her katmanda iki operatör)

$$|\psi(\gamma, \beta)\rangle = \prod_{l=1}^{p} U_B(\beta_l) U_C(\gamma_l) |s\rangle$$

burada:
$$U_C(\gamma_l) = e^{-i\gamma_l H_C}$$
$$U_B(\beta_l) = e^{-i\beta_l H_B} = \prod_{i=1}^{n} R_X(2\beta_l)$$

**Adım 3**: Beklenti değerini ölçerek klasik optimizer'a geri bildir

$$F_p(\gamma, \beta) = \langle\psi(\gamma,\beta)|H_C|\psi(\gamma,\beta)\rangle$$

**Adım 4**: Klasik optimizer $(\gamma, \beta)$ parametrelerini günceller

---

### Problem Hamiltonian Açılımı

$U_C(\gamma)$ nasıl devreye dönüştürülür?

$$e^{-i\gamma H_C} = \prod_i e^{-i\gamma Q_{ii} Z_i} \cdot \prod_{i<j} e^{-i\gamma Q_{ij} Z_i Z_j}$$

- Tek qubit terimleri: $e^{-i\gamma Q_{ii} Z_i} = R_Z(2\gamma Q_{ii})$
- İki qubit terimleri: $e^{-i\gamma Q_{ij} Z_i Z_j} = CNOT \cdot R_Z(2\gamma Q_{ij}) \cdot CNOT$

---

## 3.3 p Parametresi: Derinlik vs Kalite

| p (Katman) | Parametre Sayısı | Devre Derinliği | Kalite |
|-----------|-----------------|----------------|--------|
| 1 | 2 | Sığ | Yaklaşık |
| 2 | 4 | Orta | Daha iyi |
| 5 | 10 | Derin | İyi |
| ∞ | ∞ | ∞ | Tam çözüm (adyabatik limit) |

**Pratik kural**: NISQ cihazlarda p=1 veya p=2 tercih edilir (gürültü birikimi nedeniyle).

---

## 3.4 Klasik Optimizer Seçimi

QAOA'da kullanılan optimizer'lar:

| Optimizer | Türü | Avantaj | Dezavantaj |
|-----------|------|---------|-----------|
| **COBYLA** | Gradient-free | Robust, az ölçüm | Yavaş yakınsama |
| **SPSA** | Stokastik gradient | Gürültüye dayanıklı | Yüksek varyans |
| **BFGS** | Gradient tabanlı | Hızlı | Gürültüye hassas |
| **Adam** | Momentum tabanlı | ML'den bilinen | Ayarlama gerektirir |

**Q-OmicSelect için öneri**: COBYLA (başlangıçta), sonra SPSA (kuantum cihazda).

---

## 3.5 Barren Plateau Sorunu

**Ne oluyor?** Qubit sayısı arttıkça gradient sıfıra yaklaşır:

$$\text{Var}\left[\frac{\partial F}{\partial \theta}\right] \propto 2^{-n}$$

n=20 gen için: gradient ≈ $2^{-20}$ ≈ 10^{-6} → öğrenmek imkansız!

**Çözümler**:
1. **QAOA**: Problem odaklı yapı barren plateaus'u azaltır
2. **Katmanlı başlatma**: Önceki katmanı iyi başlat
3. **Yerel maliyet fonksiyonları**: Küresel yerine yerel ölçüm
4. **Küçük başla**: 4-6 qubit ile başlayıp ölçekle

---

# BÖLÜM 4: VQC — DEĞİŞKEN KUANTUM DEVRESİ

## 4.1 VQC Mimarisi

VQC = Parameterized Quantum Circuit (PQC) olarak da bilinir.

**Üç temel bileşen**:

```
Girdi Verisi     Parametreli Devre       Çıktı
(Genler)              |                   |
x₁, x₂, ... → [Kodlama | Ansatz Katmanları] → Ölçüm → Tahmin
```

---

## 4.2 Veri Kodlama Yöntemleri

### 1. Açı Kodlama (Angle Encoding)

$$|x\rangle = \prod_{i=1}^{n} R_Y(\pi x_i)|0\rangle^{\otimes n}$$

- n özellik için n qubit gerekir
- Her özellik [0,1] normalize edilmeli
- Basit ve yaygın

### 2. Genlik Kodlama (Amplitude Encoding)

$$|x\rangle = \frac{1}{||x||}\sum_{i=0}^{N-1} x_i|i\rangle$$

- $N = 2^n$ veri noktası, $n$ qubit ile
- Çok verimli ama hazırlama maliyeti yüksek

### 3. IQP Kodlama

$$|x\rangle = U(x)|+\rangle^{\otimes n}$$

Makine öğrenmesi için daha güçlü, Q-OmicSelect için önerilir.

---

## 4.3 Ansatz Tasarımı

Ansatz = Öğrenilebilir parametreli devre yapısı.

### Hardware Efficient Ansatz (HEA)

```
[RY(θ₁)][RZ(θ₂)] ─── CNOT ─── [RY(θ₅)][RZ(θ₆)]
[RY(θ₃)][RZ(θ₄)] ───/CNOT ─── [RY(θ₇)][RZ(θ₈)]
```

**Avantajı**: Gerçek donanıma uygun, az derin.

### Strongly Entangling Layers (SEL)

Her katmanda tam bağlantılı dolaşıklık + rotasyonlar.

**Q-OmicSelect için öneri**: Önce HEA ile başla, parametre optimizasyonu yerleştikten sonra SEL dene.

---

## 4.4 Parameter-Shift Rule: VQC'de Gradient Hesaplama

Gerçek kuantum cihazlarda backpropagation çalışmaz. Bunun yerine:

$$\frac{\partial \langle H\rangle}{\partial \theta_k} = \frac{1}{2}\left[\langle H\rangle(\theta_k + \frac{\pi}{2}) - \langle H\rangle(\theta_k - \frac{\pi}{2})\right]$$

Her parametre için **2 ek devre çalıştırması** gerekir. n parametre = 2n ek çalışma.

**Pratik etki**: Q-OmicSelect'te 4 parametreli VQC için eğitimde her adımda 8 devre çalıştırılır.

---

## 4.5 QAOA vs VQC: Q-OmicSelect'te Ne Zaman Hangisi?

| Senaryo | Tercih |
|---------|--------|
| QUBO doğrudan çözme | **QAOA** |
| Gen ekspresyon sınıflandırma | **VQC** |
| Küçük gen seti (≤10) | **QAOA** |
| Büyük gen seti (>10) | **VQC + boyut indirgeme** |
| Gürültülü kuantum cihaz | **VQC** (daha esnek) |
| Simülatörde prototip | Her ikisi de uygun |

---

# BÖLÜM 5: OMİK VERİ ANALİZİ

## 5.1 scRNA-seq Nedir?

**Single-Cell RNA Sequencing (scRNA-seq)**:
- Her hücrenin gen ekspresyonunu ayrı ayrı ölçer
- Tipik veri: 1.000-50.000 hücre × 20.000-30.000 gen
- Sonuç: Büyük, seyrek matris (çoğu değer sıfır)

**Veri yapısı (AnnData)**:
```python
adata.X          # Ekspresyon matrisi (hücre × gen)
adata.obs        # Hücre meta verisi (tip, hasta ID, vb.)
adata.var        # Gen meta verisi (gen adı, konum, vb.)
adata.uns        # Ek bilgiler
```

---

## 5.2 Standart scRNA-seq Ön İşleme Pipeline

```
Ham veri (counts)
      ↓
Kalite kontrol (düşük kaliteli hücreleri çıkar)
      ↓
Normalizasyon (total counts normalize et)
      ↓
Log dönüşümü (log1p)
      ↓
Yüksek değişkenli gen seçimi (HVG)
      ↓
PCA (50-100 bileşen)
      ↓
UMAP görselleştirme
      ↓
Kümeleme (Leiden/Louvain)
```

**Kritik adım**: HVG seçimi ile tipik olarak 2.000-5.000 gen kalır. QUBO için daha da azaltmak gerekir.

---

## 5.3 Gen Önem Skoru Hesaplama Yöntemleri

### 1. Diferansiyel Ekspresyon Analizi

Mann-Whitney U test veya t-test ile hasta vs. kontrol arasındaki fark:
$$\text{Score}_i = -\log_{10}(p_i) \times \text{sign}(\text{logFC}_i)$$

### 2. Mutual Information

$$MI(X_i; Y) = \sum_{x,y} p(x_i, y) \log\frac{p(x_i, y)}{p(x_i)p(y)}$$

Gen $X_i$ ile hastalık etiketi $Y$ arasındaki bilgi paylaşımı.

### 3. Random Forest Önem Skoru

$$\text{Önem}_i = \frac{1}{T}\sum_{t=1}^{T} \sum_{\text{node } n \in t, \text{split on } i} \Delta \text{impurity}(n)$$

**Q-OmicSelect için öneri**: Differential expression + MI kombinasyonu → QUBO skoru.

---

## 5.4 Gen Korelasyonunu QUBO'ya Dönüştürme

İki gen arasındaki korelasyon fazla bilgi (redundancy) anlamına gelir. Yüksek korelasyonlu genleri birlikte seçmekten kaçınmak için QUBO çift terimlerine penaltı eklenir:

$$Q_{ij} = \underbrace{2\lambda_{\text{kısıt}}}_{\text{seçim kısıtı}} + \underbrace{\alpha \cdot |r_{ij}|}_{\text{korelasyon cezası}} + \underbrace{\beta \cdot d_{ij}}_{\text{yol benzerliği}}$$

- $r_{ij}$: Pearson korelasyon katsayısı
- $d_{ij}$: GO yol (pathway) benzerlik skoru (opsiyonel)
- $\alpha, \beta$: Ayarlanabilir ağırlıklar

---

# BÖLÜM 6: CPO/GWO WRAPPER ÖZNITELIK SEÇİMİ

## 6.1 Wrapper Yöntemi Nedir?

Öznitelik seçim yaklaşımları üçe ayrılır:

| Yaklaşım | Yöntem | Hız | Performans |
|----------|--------|-----|------------|
| **Filter** | MI, Chi-Square, ANOVA | Hızlı | Orta |
| **Wrapper** | RFE, CPO, GWO | Yavaş | Yüksek |
| **Embedded** | Lasso, Random Forest | Orta | Yüksek |

Wrapper yöntemler: Her aday subset için uygunluk değerlendirmesi yapar → Q-OmicSelect'te bu değerlendirme **kuantum alt-rutin** ile yapılıyor!

---

## 6.2 Grey Wolf Optimizer (GWO)

2014'te önerilen sürü zekası algoritması. Kurt sürüsü hiyerarşisinden ilham alır.

```
Alfa (α): En iyi çözüm
Beta (β): İkinci en iyi
Delta (δ): Üçüncü en iyi
Omega (ω): Geri kalan çözümler
```

**Güncelleme kuralları**:
$$\mathbf{D}_\alpha = |C_1 \cdot X_\alpha - X|$$
$$X_1 = X_\alpha - A_1 \cdot D_\alpha$$

(Benzer şekilde $X_2$ beta için, $X_3$ delta için)

$$X(t+1) = \frac{X_1 + X_2 + X_3}{3}$$

**Binary GWO** (gen seçimi için): Sigmoid fonksiyon ile sürekli değerleri binary'e dönüştür.

---

## 6.3 Coati Optimization Algorithm (CPO)

2023'te önerilen yeni metasezgisel algoritma. Karabasan (coati) avlanma davranışından ilham alır.

**İki faz**:
1. **Keşif (Exploration)**: Iguanayı treeden düşürme → geniş arama
2. **Kullanım (Exploitation)**: Iguana kaçma davranışı → yerel arama

**Q-OmicSelect'te CPO'nun rolü**:
- Aday gen alt kümelerini önerir
- QAOA/VQC'yi uygunluk fonksiyonu olarak çağırır
- Her iterasyonda kuantum skoru alır ve yeni popülasyon üretir

---

## 6.4 Q-OmicSelect'te CPO-Kuantum Entegrasyonu

```python
def cpo_fitness(gen_subset, quantum_solver):
    """
    CPO popülasyonundaki bir aday için uygunluk skoru.
    """
    # 1. Gen subsetini QUBO'ya dönüştür
    Q_matrix = build_gene_qubo(gen_subset, mi_scores, X_data)
    
    # 2. QAOA/VQC ile QUBO'yu çöz (kuantum alt-rutin)
    quantum_result = quantum_solver.solve(Q_matrix)
    
    # 3. Kuantum çözümünü sınıflandırma skoru ile birleştir
    ml_score = evaluate_classifier(quantum_result['selected_genes'])
    
    return quantum_result['energy'] * 0.4 + ml_score * 0.6
```

**Kritik tasarım kararı**: Kuantum ve klasik skorun ağırlıkları (0.4/0.6) → hiperparametre.

---

# BÖLÜM 7: LLM AJAN MİMARİSİ

## 7.1 ReAct Framework

**ReAct = Reason + Act**

```
Kullanıcı sorusu
      ↓
[Düşün]: Hangi araçları kullanmalıyım?
      ↓
[Hareket Et]: GO analizi aracını çağır
      ↓
[Gözlemle]: GO sonuçlarını oku
      ↓
[Düşün]: QUBO'ya nasıl entegre ederim?
      ↓
[Hareket Et]: Kuantum çözücüyü çağır
      ↓
...
      ↓
[Son cevap]: Biyolojik yorumla birlikte gen seti
```

---

## 7.2 Q-OmicSelect Ajan Araçları

| Araç Adı | Girdi | Çıktı | Kullanım |
|----------|-------|-------|---------|
| `go_prior_generator` | Gen listesi | GO term öncelikleri | QUBO başlatma |
| `quantum_feature_selector` | QUBO matrisi | Seçili genler | Ana optimizasyon |
| `cpo_optimizer` | Veri + parametreler | Aday gen setleri | Wrapper iterasyon |
| `pathway_analyzer` | Gen seti | Pathway zenginleştirme | Biyolojik doğrulama |
| `biology_explainer` | Seçili genler | Doğal dil açıklama | Kullanıcı raporu |

---

## 7.3 Sistem Promptu Tasarımı

```
Sen biyoinformatik uzmanı bir yapay zeka ajanısın. 
Görevin: Omik verilerden hastalıkla ilgili en önemli genleri seç.

Araçlarını şu sıraya göre kullan:
1. GO analizi ile biyolojik bağlam al
2. CPO optimizer ile aday setler üret
3. Kuantum çözücü ile QUBO optimize et
4. Pathway analizi ile biyolojik doğrulama yap
5. Sonuçları bilimsel gerekçelerle açıkla

Her adımda kararını gerekçelendir. 
Belirsizlik durumunda daha fazla analiz yap.
```

---

# BÖLÜM 8: BENCHMARK VE DEĞERLENDİRME

## 8.1 Performans Metrikleri

### Gen Seçim Kalitesi

| Metrik | Formül | Ne Ölçer? |
|--------|--------|----------|
| **Accuracy** | (TP+TN)/(P+N) | Genel sınıflandırma |
| **F1-Score** | 2·P·R/(P+R) | Dengesiz sınıflar için |
| **MCC** | (TP·TN-FP·FN)/√... | Güvenilir genel metrik |
| **Stability** | Jaccard(S₁,S₂) | Tekrarlanabilirlik |

### Kuantum Performansı

| Metrik | Anlam |
|--------|-------|
| Approximation ratio | QAOA çözümü / Optimal çözüm |
| Convergence iterations | Optimizer kaç adımda yakınsadı? |
| Circuit depth | Gürültüye ne kadar maruz? |

---

## 8.2 Karşılaştırma Yöntemleri (Baseline)

Q-OmicSelect şunlarla karşılaştırılmalı:

1. **Klasik wrapper**: GWO + SVM-RFE
2. **Filter yöntemleri**: MI, mRMR
3. **Embedded**: LASSO, Random Forest
4. **Saf kuantum**: Sadece QAOA (LLM ve CPO olmadan)

---

## 8.3 Gerçek Veri Kaynakları

| Kaynak | Veri Türü | Erişim |
|--------|----------|--------|
| GEO (NCBI) | scRNA-seq, microarray | geo.ncbi.nlm.nih.gov |
| TCGA | Kanser genomik | portal.gdc.cancer.gov |
| GTEx | Normal doku ekspresyonu | gtexportal.org |
| 10x Genomics | scRNA-seq örnekler | 10xgenomics.com/datasets |

**Başlangıç için öneri**: GSE96870 (fare scRNA-seq, küçük ve anlaşılır)

---

# BÖLÜM 9: KODA DÖNÜŞÜM REHBERİ

## 9.1 Proje Klasör Yapısı

```
q_omicselect/
├── data/
│   ├── raw/           # Ham omik veriler
│   ├── processed/     # Ön işlenmiş veriler
│   └── results/       # Sonuçlar
├── quantum/
│   ├── qubo.py        # QUBO formülasyonu
│   ├── qaoa.py        # QAOA implementasyonu
│   └── vqc.py         # VQC implementasyonu
├── omics/
│   ├── preprocessing.py  # scRNA-seq ön işleme
│   ├── scoring.py        # Gen önem skorları
│   └── go_analysis.py    # GO entegrasyonu
├── optimization/
│   ├── cpo.py         # CPO optimizer
│   └── gwo.py         # GWO optimizer
├── agent/
│   ├── tools.py       # LLM araçları
│   └── agent.py       # Ajan döngüsü
├── notebooks/
│   └── tutorial.ipynb # Bu notebook
└── README.md
```

---

## 9.2 QUBO Sınıfı Şablonu

```python
class GeneQUBO:
    """
    Gen seçimi için QUBO matrisi oluşturur.
    """
    def __init__(self, n_genes, k_select, lambda_constraint=5.0,
                 alpha_corr=1.0, beta_go=0.5):
        self.n = n_genes
        self.k = k_select
        self.lambda_c = lambda_constraint
        self.alpha = alpha_corr
        self.beta = beta_go
    
    def build(self, scores, corr_matrix, go_similarity=None):
        """
        Q matrisi oluştur.
        
        scores: n boyutlu array, her genin önem skoru
        corr_matrix: n×n korelasyon matrisi
        go_similarity: n×n GO benzerlik matrisi (opsiyonel)
        """
        Q = np.zeros((self.n, self.n))
        
        # Diyagonal terimleri
        for i in range(self.n):
            Q[i, i] = -scores[i] + self.lambda_c * (1 - 2*self.k)
        
        # Çift terimleri
        for i in range(self.n):
            for j in range(i+1, self.n):
                corr_pen = self.alpha * abs(corr_matrix[i, j])
                go_pen = 0
                if go_similarity is not None:
                    go_pen = self.beta * go_similarity[i, j]
                Q[i, j] = 2*self.lambda_c + corr_pen + go_pen
        
        return Q
    
    def to_ising(self, Q):
        """QUBO → Ising dönüşümü"""
        n = Q.shape[0]
        h = np.zeros(n)
        J = np.zeros((n, n))
        
        for i in range(n):
            h[i] = Q[i,i]/2 + sum(Q[i,j]/4 + Q[j,i]/4 
                                   for j in range(n) if j != i)
        for i in range(n):
            for j in range(i+1, n):
                J[i,j] = (Q[i,j] + Q[j,i]) / 4
        
        return h, J
```

---

## 9.3 QAOA Sınıfı Şablonu

```python
class GeneQAOA:
    """
    Gen seçimi için QAOA çözücü.
    """
    def __init__(self, n_qubits, p_layers=1, shots=1024):
        self.n = n_qubits
        self.p = p_layers
        self.shots = shots
        self.simulator = AerSimulator()
    
    def build_circuit(self, gamma, beta, Q):
        """QAOA devresini oluştur"""
        h, J = self._qubo_to_ising(Q)
        qc = QuantumCircuit(self.n, self.n)
        
        # Başlangıç
        qc.h(range(self.n))
        
        for layer in range(self.p):
            # Maliyet katmanı
            for i in range(self.n):
                qc.rz(2 * gamma[layer] * h[i], i)
            for i in range(self.n):
                for j in range(i+1, self.n):
                    if J[i,j] != 0:
                        qc.cx(i, j)
                        qc.rz(2 * gamma[layer] * J[i,j], j)
                        qc.cx(i, j)
            
            # Karıştırıcı katman
            for i in range(self.n):
                qc.rx(2 * beta[layer], i)
        
        qc.measure(range(self.n), range(self.n))
        return qc
    
    def optimize(self, Q, max_iter=100):
        """Klasik optimizasyon döngüsü"""
        def objective(params):
            gamma = params[:self.p]
            beta = params[self.p:]
            qc = self.build_circuit(gamma, beta, Q)
            # ... devre çalıştır, beklenti hesapla
        
        result = minimize(objective, x0=np.random.uniform(0, np.pi, 2*self.p),
                         method='COBYLA', options={'maxiter': max_iter})
        return result
```

---

# ÖZET VE HIZLI BAŞVURU TABLOSU

## Kritik Formüller

| Formül | Kullanım |
|--------|---------|
| $E = \mathbf{x}^T Q \mathbf{x}$ | QUBO enerji hesaplama |
| $x_i = (1-z_i)/2$ | QUBO → Ising dönüşümü |
| $U_C(\gamma) = e^{-i\gamma H_C}$ | QAOA maliyet operatörü |
| $U_B(\beta) = \prod_i R_X(2\beta_i)$ | QAOA mixer operatörü |
| $\partial F/\partial\theta = [F(\theta+\pi/2) - F(\theta-\pi/2)]/2$ | Parameter-shift rule |

## Yaygın Hatalar ve Çözümleri

| Hata | Neden? | Çözüm |
|------|--------|-------|
| QAOA hiç yakınsamıyor | λ çok küçük (kısıt zayıf) | λ ≥ max_score artır |
| Tüm çözümler geçersiz | λ çok büyük (enerji baskın) | λ'yı dengeli seç |
| Barren plateau | Derin devre, random init | Katmanlı başlatma |
| Shot gürültüsü | Az ölçüm | shots ≥ 1024 kullan |
| Korelasyon etkisi yok | α=0 | α > 0 ayarla |

---

*Bu ders notları Q-OmicSelect projesi için hazırlanmıştır.*  
*Tüm kod örnekleri eğitsel amaçlıdır ve production ortamı için optimize edilmemiştir.*
