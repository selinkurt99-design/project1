# Enerji Yönetim Sistemi Mimarisi

## Sistem Genel Bakış

```mermaid
graph TD
    A["Şoför Girişi"] --> B["Araç Dinamikleri"]
    B --> C["Güç Talebi<br/>P_dem"]
    
    C --> D["MPC<br/>Kontrolcü"]
    E["Sistem Durumu<br/>SoC, V"] --> D
    
    D --> F["Batarya<br/>Akımı Referansı"]
    D --> G["Süperkapasitör<br/>Akımı Referansı"]
    
    F --> H["Batarya<br/>Kontrolcüsü"]
    G --> I["SC<br/>Kontrolcüsü"]
    
    H --> J["Batarya<br/>Dönüştürücüsü"]
    I --> K["SC<br/>Dönüştürücüsü"]
    
    J --> L["500V DC Güç Hattı"]
    K --> L
    
    L --> M["Motor Sürücü"]
    M --> N["Mekanik Güç"]
    
    N -.-> B
    J -.-> E
    K -.-> E
    
    style A fill:#e3f2fd
    style B fill:#e3f2fd
    style C fill:#fff3e0
    style D fill:#f3e5f5
    style E fill:#f3e5f5
    style F fill:#e8f5e9
    style G fill:#e8f5e9
    style H fill:#e8f5e9
    style I fill:#e8f5e9
    style J fill:#e8f5e9
    style K fill:#e8f5e9
    style L fill:#fce4ec
    style M fill:#fce4ec
    style N fill:#fce4ec
```

## Katmanlar Nedir?

### 📊 1. Algılama Katmanı (Giriş)
- Şoför komutları alınır
- Araç dinamikleri hesaplanır
- Güç talebi belirlenir

### 🎯 2. Karar Katmanı (Optimizasyon)
- **MPC**: Batarya ve süperkapasitör arasında optimal enerji dağılımını belirler
- Sistem durumunu (SoC, gerilim) takip eder
- Her bir kaynağın referans akımını hesaplar

### ⚡ 3. Kontrol Katmanı (Uygulanış)
- PI kontrolcüler referans akımları takip eder
- DC/DC dönüştürücüler güçü dönüştürür
- 20 kHz frekansında çalışır (hızlı yanıt)

### 🔋 4. Güç Katmanı (Fiziksel)
- Batarya ve süperkapasitör birleştirilir
- 500V DC hattı üzerinden motor sürücüsüne güç verilir
- Motor mekanik güce dönüştürür

## İşleyiş Akışı

1. **Giriş** → Şoför pedal basar, güç talebi belirlenir
2. **Karar** → MPC optimal dağılımı hesaplar
3. **Kontrol** → Kontrolcüler hedef akımları ayarlar
4. **Çıkış** → Motor güç üretir
5. **Geri Bildirim** → Sistem durumu güncellenir (kesikli çizgiler)

## Temel Özellikler

| Özellik | Açıklama |
|---------|----------|
| **Kaynaklar** | Batarya + Süperkapasitör |
| **Güç Hattı** | 500V DC |
| **Kontrol Hızı** | 20 kHz |
| **Yöntem** | Model Öngörülü Kontrol (MPC) |
