# AfetMesh — ÇATI Ailesi (Çatı / Direk Röle Düğümleri) Donanım Gereksinim ve Sistem Tasarım Raporu

> ## 📎 BELGE STATÜSÜ: TEKNİK EK (v1.0) — **ÇATI-B / ÇATI-K / ÇATI-O röle düğümleri ve MERKEZ ağ geçidi için geçerlidir**
>
> **Tarih:** 24 Eylül 2026 · Yürürlükteki üst belge: **[`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](../00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md)** (SGB v2.5, Bölüm 7b)
>
> **Dayandığı araştırma:** [`04_Dokumanlar/Meshtastic_Sorunlari_ve_Cozum_Arastirmasi.md`](../04_Dokumanlar/Meshtastic_Sorunlari_ve_Cozum_Arastirmasi.md). Bu belgede `P-n` o belgedeki sorun envanterini, `D-n` sahadan dersleri, `[L-n]` / `[T-n]` kaynakları gösterir.
>
> **Amaç:** Çok katlı binaların arasında cep cihazından cep cihazına doğrudan gidemeyen sinyali **önce binanın çatısındaki düğüme, oradan hedefe veya ana merkeze** taşıyan; afette **şebeke elektriği olmadan en az 7 gün** çalışan; bakım için kimsenin çatıya çıkmasını gerektirmeyen sabit röle altyapısı.
>
> **Kardeş belge:** CEP ailesi → [`Donanim_Gereksinim_Raporu.md`](Donanim_Gereksinim_Raporu.md). ÇATI-B, CEP ile **aynı radyo modülünü (RAK4630)** ve aynı firmware varyant altyapısını kullanır.

---

## 1. Yönetici Özeti — Yedi Karar

| # | Karar | Seçim | Tek cümlelik gerekçe |
|---|---|---|---|
| **ÇT-K1** | Kaç sınıf? | **3 röle sınıfı + 1 ağ geçidi**: ÇATI-B (bina), ÇATI-K (kule / yüksek nokta), ÇATI-O (omurga), MERKEZ | Bir binaya hizmet eden düğüm ile şehir ölçeğinde taşıma yapan düğümün rolü, anteni, enerjisi ve sayısı farklıdır. Tek kutu ya aşırı pahalı olur ya da yetersiz kalır (D-2, D-3) |
| **ÇT-K2** | İşlemci + erişim radyosu | **nRF52840 + SX1262 (RAK4630/RAK4631)**. Bütün ÇATI sınıflarında aynı | Röle sürekli alışta çalışır. nRF52 ROUTER'ı 5–30 mA çeker, ESP32 ise 50–150 mA [T-44]. Güneş ve pil boyutu 3–5 kat küçülür. CEP ile ortak parça ve firmware |
| **ÇT-K3** | Rol | ÇATI-K: **`ROUTER`** · ÇATI-B: **`ROUTER_LATE`** (seçenek: `CLIENT_BASE`, SGB A-10) · ÇATI-O erişim tarafı: `ROUTER` | Az ROUTER + boşluk dolduran ROUTER_LATE, en az çarpışmayla en çok kapsamayı verir [T-29][T-30] (P-1, P-5) |
| **ÇT-K4** | Verici gücü | **SX1262'nin kendi 22 dBm'i. 1 W PA kullanılmaz.** TX gücü yasal ERP'ye göre anten ve kayıplardan geriye hesaplanır | TR 868'de sınır 27 dBm ERP'dir. 6 dBi antenle yasal üst sınır ~24,6 dBm'dir, yani 1 W PA en fazla +2,6 dB kazandırır. Asıl sorun alıcı tarafındadır (P-10) |
| **ÇT-K5** | Alıcı ön uç | **Bant geçiren filtre** (SAW veya kavite, ≤ 1 dB). ÇATI-K'da isteğe bağlı **LNA'lı ön uç** (SKY66122, RAK13302) → gürültü tabanı ölçümüyle karar (A-11) | Çatılarda LTE 800 ve GSM 900 vericileri alıcıyı doyurur (P-11, D-8). Filtresiz çatı düğümü "duyar ama anlamaz" |
| **ÇT-K6** | Enerji | **LiFePO4 + MPPT güneş şarjı + 0 °C altı şarj kesme**. 7 gün güneşsiz otonomi, **Aralık güneşine göre** boyutlandırma | Li-ion çatıda yazın 60 °C'ye ısınıp yaşlanır, kışın donar. LFP her iki uçta daha güvenli [T-44] (P-15, D-14…D-16) |
| **ÇT-K7** | Omurga (ÇATI-O) | **2,4 GHz LoRa (LR1121, `LORA_24`)**, aynı kutuda ayrı ikinci düğüm + **UART köprü modülü** | 868 MHz'te omurgaya yer yok (%10 sınırı, tek 250 kHz kanal). Meshtastic tek radyoyu destekliyor (P-9). İki düğüm + köprü modülü çekirdeğe dokunmaz (SYS-4) |

> ### ⚠️ ÇT-K4'ün A-1'e (433 mi 868 mi) etkisi — yeni bulgu
> TR'de 433 MHz sınırı **10 mW ERP**'dir. 5 dBi antenli bir çatı düğümünün yasal iletim gücü **~8,7 dBm** olur. Aynı düğüm 868 MHz'te **22 dBm** basabilir. Yani **433 MHz'te çatı rölesinin verici link bütçesi 868'e göre ~13 dB zayıftır.** Anten kazancı alışta yine işe yarar ama bağlantı asimetrik olur (P-10). Bu bulgu SGB A-1'e girdi olarak eklendi. 433 seçilirse ÇATI yoğunluğu artırılmalıdır.

---

## 2. Sistem Mimarisi

### 2.1 Katmanlar

```
                          ┌──────────────────────────────┐
                          │ MERKEZ (belediye kriz merkezi,│
                          │ AFAD il) — PANO + SOS-TEYİT   │
                          └──────────────▲───────────────┘
                                         │ 2,4 GHz LoRa omurga (görüş hattı)
            ┌────────────────────────────┼────────────────────────────┐
     ┌──────┴──────┐              ┌──────┴──────┐              ┌──────┴──────┐
     │  ÇATI-O #1  │◄════════════►│  ÇATI-O #2  │◄════════════►│  ÇATI-O #3  │   KATMAN 3: omurga
     │ (en yüksek  │   1,5–3 km   │ (su kulesi) │              │ (itfaiye    │   (her 1,5–3 km)
     │  bina)      │              │             │              │  kulesi)    │
     └──────▲──────┘              └──────▲──────┘              └──────▲──────┘
            │ 868 MHz                    │                            │
     ┌──────┴──────┐              ┌──────┴──────┐                     │         KATMAN 2: yüksek nokta
     │  ÇATI-K     │              │  ÇATI-K     │  ← ROUTER, 868 MHz  │         (her ~1–2 km)
     └──────▲──────┘              └──────▲──────┘                     │
            │                            │                            │
   ┌────────┼────────┐          ┌────────┼────────┐                   │         KATMAN 1: bina
┌──┴───┐ ┌──┴───┐ ┌──┴───┐   ┌──┴───┐ ┌──┴───┐ ┌──┴───┐           ┌──┴───┐     (her blok / 300–500 m)
│ÇATI-B│ │ÇATI-B│ │NOKTA │   │ÇATI-B│ │ÇATI-B│ │ÇATI-B│           │NOKTA │     ROUTER_LATE
└──▲───┘ └──▲───┘ └──▲───┘   └──▲───┘ └──▲───┘ └──▲───┘           └──▲───┘
   │ dikey  │        │ WiFi     │        │        │                  │
 [CEP]    [CEP]  [telefon]    [CEP]    [CEP]    [CEP]            [telefon]      KATMAN 0: vatandaş
 (bina içi, CLIENT_MUTE)                                                        (CEP / PORTAL)
```

### 2.2 Temel ilke: "Dikey çık, yatay taşı"

| Sorun | Neden | ÇATI'nın cevabı |
|---|---|---|
| Çok katlı binalar arasında CEP → CEP doğrudan bağlantı yok | Sokak kanyonu, çok sayıda duvar ve beton. Kent içinde stok antenle 0,5–1 km [T-57] | CEP yalnızca **kendi binasının çatısına** ulaşmak zorundadır. Dikey yol, yatay kanyondan kısadır |
| Çatıdan çatıya bağlantı | Fresnel bölgesi ara binalarla kapanır (1 km'de 868 MHz için hat ortasında ≥ 5,6 m açıklık gerekir, Araştırma 7.3) | ÇATI-K / ÇATI-O **bölgenin en yüksek noktalarına** konur. ÇATI-B yalnızca en yakın ÇATI-K'yı görmek zorundadır |
| Çok sıçrama kanalı tüketir (P-1) | Her sıçrama aynı 868 kanalında bir yayın daha demektir | Uzun mesafe **omurgaya** (ayrı kanal) alınır. 868'de her paket 1–2 sıçrama yapar |
| Afette binalar hasar görür | Tek ÇATI'ya bağlı alan kopar | **N+1 kapsama:** her sokak en az 2 ÇATI tarafından duyulur (ÇT-GEN-6) |

### 2.3 NOKTA ile ilişki

**NOKTA** (SGB Bölüm 7: ESP32-S3, WiFi portal, mesaj deposu) toplanma alanlarında **telefonu olan ama cihazı olmayan** vatandaşa hizmet eder. **ÇATI** WiFi açmaz. Yalnızca LoRa rölesi ve SOS emanetçisidir. Enerjisi 4–6 kat daha azdır ve sayıca çoktur. Bir ÇATI-K ile bir NOKTA aynı direğe konabilir. Bu durumda yalnızca biri `ROUTER` olur, diğeri `ROUTER_LATE` (birbirini gören iki ROUTER yasağı, ÇT-RF-12).

---

## 3. Sınıf Tanımları

| | **ÇATI-B** (bina) | **ÇATI-K** (kule / yüksek nokta) | **ÇATI-O** (omurga) | **MERKEZ** (ağ geçidi) |
|---|---|---|---|---|
| Yer | Konut ya da iş bloğu çatısı | Bölgenin en yüksek binası, su kulesi, itfaiye / kamu binası kulesi, tepe | Seçilmiş ÇATI-K noktaları | Belediye kriz merkezi, AFAD il müdürlüğü, hastane |
| Kapsadığı | Kendi binası + çevresindeki 2–4 bina | 1–2 km yarıçap, kendi altındaki ÇATI-B'ler | Omurga: 1,5–3 km aralıklı ÇATI-O'lar | Bütün ağ |
| Meshtastic rolü | `ROUTER_LATE` (A-10) | `ROUTER` | Erişim: `ROUTER` · Omurga: `ROUTER` (`LORA_24`) | Erişim + omurga: `CLIENT` (+ köprü + PANO) |
| Radyo | 1 × SX1262 (868) | 1 × SX1262 (868) + isteğe bağlı LNA ön uç | 868 SX1262 + **2,4 GHz LR1121** | ÇATI-O ile aynı |
| Anten (868) | 3–5 dBi fiberglas kolineer | 5–6 dBi fiberglas kolineer | 5–6 dBi + 2,4 GHz 8–10 dBi omni (veya sektör) | ÇATI-O ile aynı |
| Filtre | SAW, operatör vericisi varsa **zorunlu** | Kavite (zorunlu) | Kavite (zorunlu) | Kavite |
| Enerji | 1S LFP ~38 Wh + 10 W panel | 12 V LFP ~77 Wh + 20 W panel | 12 V LFP ~128–154 Wh + 30–40 W panel | Şebeke + jeneratör + UPS (12 V 100 Ah LFP) |
| Adet (örnek mahalle, ~1 km²) | 8–15 | 2–3 | 1–2 | 1 (ilçe) |
| Hedef birim maliyet (prototip, hazır kart) | **$200–310** (üretim hedefi $150–180) | **$330–450** | **$440–610** | $800–1.200 (bilgisayar dahil) |

> **Yoğunluk sayıları başlangıç varsayımıdır** (kent içi stok antenli NLoS menzili 0,5–1 km [T-57] ve Fresnel hesabından türetildi). SYS-7 gereği **TST-8 ve TST-9 ölçümleriyle güncellenecektir.**

---

## 4. Donanım Gereksinim Tablosu

**Öncelik:** Z = Zorunlu · Ö = Önemli · İ = İsteğe bağlı · **Doğrulama:** A = Analiz · Ö = Ölçüm · T = Test · İ = İnceleme · **Sınıf:** B / K / O / M (MERKEZ) / Hepsi

### 4.1 Genel ve Firmware (ÇT-GEN)

| # | Gereksinim | Önc. | Doğr. | Sınıf | Kaynak / sorun |
|---|---|---|---|---|---|
| ÇT-GEN-1 | Erişim radyosu **upstream Meshtastic** çalıştıracak. AfetMesh eklentileri yalnızca **ayrı modül** olacak | Z | İ | Hepsi | SGB SYS-3/4 |
| ÇT-GEN-2 | MCU **nRF52840**, radyo **SX1262** (RAK4630 veya RAK4631) | Z | İ | B, K, O | ÇT-K2 |
| ÇT-GEN-3 | Bütün ağ **aynı firmware ailesi ve sürüm penceresinde** tutulacak. Güncelleme uzaktan yönetici (PKC admin key) ile yapılabilecek | Z | T | Hepsi | D-22 (Berlin) |
| ÇT-GEN-4 | Kurulumdan sonra **BLE kapatılabilir** olacak. Yapılandırma PKC uzaktan yönetimle yapılacak | Ö | T | B, K, O | Güç + güvenlik |
| ÇT-GEN-5 | **Donanım bekçi köpeği** (MCU'dan bağımsız harici zamanlayıcı, ör. TPL5010) donmuş sistemi resetleyecek | Z | T | Hepsi | D-18 |
| ÇT-GEN-6 | Planlama kuralı: her sokak ya da blok **en az 2 ÇATI** tarafından duyulacak (N+1) | Z | A/Ö | Hepsi | Deprem hasarı yedekliliği |
| ÇT-GEN-7 | Konum sabit girilecek (`fixed_position`). GNSS takılmayacak | Z | İ | B, K, O | Güç, P-1 |
| ÇT-GEN-8 | **SOS emanet deposu:** en az **2.000 SOS kaydı** (59 B + meta) güç kesintisinden etkilenmeyen hafızada | Z | T | Hepsi | SGB SOS-8, P-7 |
| ÇT-GEN-9 | **Afet profili:** sarsıntı algılanınca (ivmeölçer) veya MERKEZ komutuyla telemetri, konum ve NodeInfo yayınları durur, SOS öncelik ve emanet açılır | Z | T | Hepsi | SGB NOK-8 ile aynı mantık |
| ÇT-GEN-10 | Sağlık telemetrisi (pil, ChUtil, AirUtilTX, kutu sıcaklığı) **≥ 3 saatte bir**, ayrı düşük öncelikli kanaldan | Ö | T | Hepsi | D-20 |

### 4.2 RF (ÇT-RF)

| # | Gereksinim | Önc. | Doğr. | Sınıf | Kaynak / sorun |
|---|---|---|---|---|---|
| ÇT-RF-1 | TX gücü, **yasal ERP**'yi aşmayacak biçimde anten kazancı ve hat kayıplarından geriye hesaplanarak firmware'de sabitlenecek (Bölüm 5.1) | Z | Ö | Hepsi | SGB REG-2/3, ÇT-K4 |
| ÇT-RF-2 | **Yalnız PA'lı güçlendirici kullanılmayacak.** Güçlendirici kullanılırsa LNA + SAW içeren entegre ön uç olacak | Z | İ | K, O | P-10, [T-49] |
| ÇT-RF-3 | **Bant geçiren filtre**: ekleme kaybı ≤ 1,0 dB, 833 MHz'te ≥ 40 dB, 900 MHz'te ≥ 40 dB bastırma | Z (K, O) / Ö (B) | Ö | Hepsi | P-11, [T-43] |
| ÇT-RF-4 | Radyo antene **≤ 1 m** koaksiyelle bağlanacak (radyo antenin dibinde, kutuda). Dış koaksiyel en az LMR-195 | Z | İ | Hepsi | D-10 |
| ÇT-RF-5 | Dış mekân RF konnektörleri **N tipi**. Bulkhead + dielektrik gres + kendinden kaynaşan bant | Z | İ | Hepsi | D-11 |
| ÇT-RF-6 | Koaksiyel hatta **DC topraklı yıldırım parafudru** (868 MHz'e uygun, ≤ 0,3 dB). Binanın topraklamasına eş potansiyelli bağlantı | Z | İ | Hepsi | D-12 |
| ÇT-RF-7 | Anten dikey polarizasyonlu omni. **ÇATI-B 3–5 dBi** (dikey hüzme bina içine insin), **ÇATI-K/O 5–6 dBi**. **8 dBi üstü kullanılmayacak** | Z | A | Hepsi | Araştırma 7.2 |
| ÇT-RF-8 | Anten, çatıdaki metal yapılardan (korkuluk, güneş enerjisi su ısıtıcısı, baz istasyonu direği) **≥ 1 λ (35 cm)** yukarıda ve operatör anteninden **≥ 3 m** uzakta | Z | İ | Hepsi | D-8, D-12 |
| ÇT-RF-9 | **Çatıdan çatıya bağlantılarda** hat ortasında birinci Fresnel yarıçapının %60'ı açık olacak (Araştırma 7.3) | Z | A | K, O | Fresnel |
| ÇT-RF-10 | Omurga radyosu (ÇATI-O): **2,4 GHz LoRa** (LR1121 veya PA'lı SX1280), Meshtastic `LORA_24` bölgesi | Z | T | O, M | ÇT-K7 |
| ÇT-RF-11 | Omurga EIRP'si ve ortam kullanım süresi **ETSI EN 300 328 / BTK** sınırına göre ayarlanacak (REG-4 ile teyit) | Z | Ö | O, M | AR-8 |
| ÇT-RF-12 | Birbirini **doğrudan duyan iki `ROUTER`** olmayacak. İkincisi `ROUTER_LATE` yapılır | Z | Ö | Hepsi | P-5, D-2 |
| ÇT-RF-13 | Rebroadcast modu `ALL` (TA Mesh dahil her şeyi taşır). **`CORE_PORTNUMS_ONLY` kullanılmayacak** (AfetMesh SOS portnum'unu atar) | Z | T | Hepsi | SGB A-12, SOS-5 |

### 4.3 Güç (ÇT-PWR)

| # | Gereksinim | Önc. | Doğr. | Sınıf | Kaynak / sorun |
|---|---|---|---|---|---|
| ÇT-PWR-1 | **Şebekeden bağımsız**: güneş + pil. Şebeke varsa ek giriş olarak kullanılabilir, zorunlu değil | Z | T | B, K, O | Deprem = elektrik kesintisi |
| ÇT-PWR-2 | **Güneşsiz otonomi ≥ 7 gün** (SGB NOK-N5 ile aynı) | Z | Ö | B, K, O | P-15 |
| ÇT-PWR-3 | Boyutlandırma **en kötü ay (Aralık)** ışınımıyla yapılacak. Tasarım değeri: **1,5 kWh/m²/gün** (İstanbul; Karadeniz ve Doğu illeri için PVGIS'ten yeniden hesaplanır). Panel ≥ 2× marj | Z | A | B, K, O | D-16 |
| ÇT-PWR-4 | Pil kimyası **LiFePO4** (veya LTO). Li-ion **yalnızca prototipte** kabul edilir | Z | İ | B, K, O | ÇT-K6, D-15 |
| ÇT-PWR-5 | **Şarj sıcaklık penceresi 0…45 °C** (NTC ile donanımsal kesme). Deşarj −20…+60 °C | Z | T | B, K, O | D-15 |
| ÇT-PWR-6 | **MPPT** şarj denetleyici (PWM kabul edilmez) | Ö | Ö | B, K, O | [T-44] |
| ÇT-PWR-7 | **Kendiliğinden toparlanma:** pil tamamen bitip güneş geri geldiğinde sistem insan müdahalesi olmadan açılacak (BMS kilitlenmesi yok, soğuk açılışta bootloop yok) | Z | T | Hepsi | D-18 |
| ÇT-PWR-8 | Düşük pil kademeleri: %30'da telemetri durur, %15'te yalnızca SOS rölesi, %5'te güvenli kapanma | Ö | T | Hepsi | Afet önceliği |
| ÇT-PWR-9 | DC girişlerinde TVS + sigorta. Panel kablosunda ters kutup koruması | Z | İ | Hepsi | [T-45] |
| ÇT-PWR-10 | ÇATI-O'da omurga kartının beslemesi **uzaktan kapatılıp açılabilen** yük anahtarı arkasında (uzaktan güç döngüsü) | Ö | T | O | D-18 |

### 4.4 Mekanik, Çevre ve Deprem (ÇT-MEC / ÇT-ENV)

| # | Gereksinim | Önc. | Doğr. | Sınıf | Kaynak / sorun |
|---|---|---|---|---|---|
| ÇT-ENV-1 | Kutu **IP67**, UV dayanımlı polikarbonat veya ASA, **açık renk** | Z | T | Hepsi | [T-44] |
| ÇT-ENV-2 | **Basınç dengeleme ventili** (Gore tipi) + yıllık değişen nem alıcı. Kablo rakorları aşağı bakar | Z | İ | Hepsi | D-13 |
| ÇT-ENV-3 | Çalışma −20…+70 °C (kutu içi). Pil panelin gölgesinde. Aktif parçalar ≥ −40…+85 °C sınıfı | Z | T | Hepsi | D-14 |
| ÇT-ENV-4 | Kutu içi sıcaklık ve nem sensörü (SHT40) sağlık telemetrisine girer | Ö | T | Hepsi | Arıza öngörüsü |
| ÇT-MEC-1 | Montaj **taşıyıcı elemana** yapılır (parapet betonu, kolon, çatı döşemesi, kimyasal dübel). **Balastlı serbest ayak kabul edilmez** (sarsıntıda kayar) | Z | İ | Hepsi | Deprem |
| ÇT-MEC-2 | Baca, su deposu, güneş enerjisi su ısıtıcısı ve çatı kaplamasına **montaj yapılmaz** (depremde ilk devrilenler) | Z | İ | Hepsi | Deprem |
| ÇT-MEC-3 | Direk boyu ≤ 3 m ise bağımsız. > 3 m ise gergi teli. Toplam kütle ≤ 10 kg (ÇATI-B ≤ 5 kg) | Z | A | Hepsi | Deprem, rüzgâr |
| ÇT-MEC-4 | Bütün bağlantı elemanları paslanmaz (A2/A4), kilitli somun | Z | İ | Hepsi | [T-45] |
| ÇT-MEC-5 | Güneş paneli güneye, **45–50° eğim** (kış optimumu + kar ve toz dökülmesi) | Ö | İ | B, K, O | [T-44] |
| ÇT-MEC-6 | Kutu, çatıya çıkmadan **yerden görülebilen** bir etiket ve kimlik taşır (QR + düğüm no) | Ö | İ | Hepsi | SGB NOK-10 benzeri |

### 4.5 İzleme ve Test Erişimi (ÇT-DBG)

| # | Gereksinim | Önc. | Doğr. | Sınıf |
|---|---|---|---|---|
| ÇT-DBG-1 | Kutu içinde USB-C (bakım) + SWD test pedi | Z | İ | Hepsi |
| ÇT-DBG-2 | Kurulumdan önce **48 saat ısı testi** (60 °C kabin veya güneşte kapalı kutu) | Z | T | Hepsi |
| ÇT-DBG-3 | Kurulum formu: anten yüksekliği, görülen düğümler, RSSI/SNR, gürültü tabanı (filtreli ve filtresiz), fotoğraf | Z | İ | Hepsi |

---

## 5. RF Tasarımı

### 5.1 Yasal ERP'den geriye TX gücü hesabı

`ERP (dBm) = P_TX − L_hat + G_anten(dBi) − 2,15`. Hat kaybı = filtre + parafudr + koaksiyel + konnektörler.

| Senaryo | Sınır (SGB REG-2) | Anten | L_hat | **Yasal P_TX üst sınırı** | Seçilen P_TX | ERP |
|---|---|---|---|---|---|---|
| ÇATI-B, 868 | 27 dBm ERP (500 mW) | 4 dBi | 1,5 dB | 26,6 dBm | **22 dBm** (SX1262 azamisi) | 22,4 dBm ✅ |
| ÇATI-K, 868 | 27 dBm ERP | 6 dBi | 1,5 dB | **24,6 dBm** | 22 dBm (LNA ön uçla ≤ 24 dBm) | 24,4 dBm ✅ |
| ÇATI-K + **1 W PA**, 868 | 27 dBm ERP | 6 dBi | 1,5 dB | 24,6 dBm | ❌ 30 dBm **yasa dışı** | 32,4 dBm ❌ |
| ÇATI-B, **433** | **10 dBm ERP** (10 mW) | 4 dBi | 1,0 dB | **9,6 dBm** | 9 dBm | 9,9 dBm ✅ |
| ÇATI-K, **433** | 10 dBm ERP | 5 dBi | 1,0 dB | **8,7 dBm** | 8 dBm | 9,9 dBm ✅ |
| ÇATI-O omurga, 2,4 GHz | EIRP 20 dBm (EN 300 328, **teyit edilecek**) | 8 dBi | 1,0 dB | 13 dBm | LR1121 azamisi ~13 dBm | ~20 dBm EIRP ✅ |

> **Sonuç:** 868 MHz'te **1 W güçlendirici gerekmez ve yasal değildir.** Menzil kazancı **alıcı tarafında** (filtre, gerekirse LNA, yükseklik, düşük kayıplı hat) aranır. 433 MHz'te çatı düğümünün verici gücü 868'e göre ~13 dB düşüktür (ÇT-K4 notu → SGB A-1).

### 5.2 RF zinciri

```
           ÇATI-B                                          ÇATI-K / ÇATI-O (868 tarafı)
 anten 3–5 dBi (N-dişi)                              anten 5–6 dBi (N-dişi)
      │ ≤ 1 m LMR-195 (0,3 dB)                            │ ≤ 1 m LMR-240
 [parafudr N-N, DC topraklı]──► bina topraklaması   [parafudr]──► topraklama
      │                                                   │
 [SAW BPF 868 (≤1 dB)]  ← operatör vericisi varsa   [KAVİTE BPF 868 (≤1 dB, ≥40 dB @833)]
      │ U.FL/SMA pigtail                                  │
 [RAK4630 / RAK4631: SX1262 22 dBm]                 [SX1262] ──► isteğe bağlı: [SKY66122 PA/LNA/SAW] (A-11)
                                                          │
                                                    ÇATI-O: 2,4 GHz zinciri ayrı:
                                                    [LR1121]─[parafudr 2,4]─ 8–10 dBi omni / 14 dBi panel
```

### 5.3 Anten seçimi ve dikey hüzme

| Anten (868) | Kazanç | Yaklaşık dikey hüzme genişliği | Kullanım |
|---|---|---|---|
| ½λ dipol / kısa fiberglas | 2–3 dBi | ~60–75° | ÇATI-B: alçak çatılar, bina içine iyi iner |
| Fiberglas kolineer (~50 cm) | 5 dBi | ~30° | ÇATI-B yüksek bina / ÇATI-K |
| Fiberglas kolineer (~1 m) | 6 dBi | ~20–25° | ÇATI-K / ÇATI-O |
| 8–9 dBi kolineer | 8–9 dBi | ~10–15° | ❌ Kent içinde kullanılmaz. Hüzme yakın binaların ve alt katların üstünden geçer |

> Hüzme değerleri tipik kolineer antenler içindir. Seçilen ürünün veri sayfasındaki desenle doğrulanır. TA Mesh dış mekân için ≥ 6 dBi önerir [T-55]. Bu öneri **açık alandaki uzun menzil** içindir. ÇATI-B'nin görevi **bina içine inmek** olduğu için daha düşük kazanç seçilmiştir.

---

## 6. Güç Bütçesi

### 6.1 Akım tahmini (3,3 V hat eşdeğeri)

| Bileşen | ÇATI-B | ÇATI-K (LNA'lı) | ÇATI-O | Kaynak |
|---|---|---|---|---|
| SX1262 alış (sürekli, boosted RX) | ~5–6 mA | ~5–6 mA | ~5–6 mA | SX1262 DS |
| LNA ön uç (alışta açık) | — | ~5–10 mA (ölçülecek) | ~5–10 mA | SKY66122 DS (A-11) |
| nRF52840 (BLE kapalı, boşta) | ~1–3 mA | ~1–3 mA | ~1–3 mA | [T-4] |
| TX (%2 tipik / %10 en kötü, 22 dBm ~118 mA) | 2,4 / 11,8 mA | 2,4 / 11,8 mA | 2,4 / 11,8 mA | Çalışma süresi sınırı |
| Omurga kartı (ESP32-S3 + LR1121, sürekli alış) | — | — | **~60–100 mA** | [T-1][T-44] |
| Sensör + bekçi köpeği + yakıt göstergesi | < 0,1 mA | < 0,1 mA | < 0,1 mA | — |
| **Tasarım değeri (en kötü, %10 TX)** | **20 mA** | **30 mA** | **130 mA** | Topluluk ölçümü: RAK4631 ROUTER 5–30 mA [T-44] |

### 6.2 Pil ve panel boyutlandırma (dönüşüm verimi %85, pil kullanım derinliği %80, Aralık PSH 1,5, panel verimi %70, 2× marj)

| | Günlük tüketim | 7 gün | Gereken pil | **Seçilen pil** | Gereken panel | **Seçilen panel** |
|---|---|---|---|---|---|---|
| **ÇATI-B** | 1,9 Wh | 13 Wh | 16 Wh | **1S LFP 3,2 V 12 Ah (≈ 38 Wh)** → ~16 gün | 3,5 W | **10 W** |
| **ÇATI-K** | 2,8 Wh | 20 Wh | 25 Wh | **12,8 V 6 Ah LFP (≈ 77 Wh)** → ~22 gün | 5,3 W | **20 W** |
| **ÇATI-O** | ~12 Wh | 85 Wh | 106 Wh | **12,8 V 10–12 Ah LFP (128–154 Wh)** → ~8–10 gün | ~23 W | **30–40 W** |
| **MERKEZ** | Şebeke | — | — | 12,8 V 100 Ah LFP UPS | — | İsteğe bağlı 100 W |

> **SYS-7 uyarısı:** Bu tablo **tahmindir.** Seçilen değerler 2–3× güvenlik payı içerir, çünkü (a) LNA ve ESP32 akımı ölçülmedi, (b) topluluk ölçümleri 5–30 mA aralığında geniş dağılıyor. **TST-11 (14 günlük kış testi) tamamlanmadan beyan edilmez.**

---

## 7. ÇATI-O Omurga ve Köprü Tasarımı

### 7.1 Neden iki ayrı düğüm?

Meshtastic bir firmware'de tek radyo çalıştırır. İki SPI modülle yapılan prototip yalnızca **aynı frekansta** çalıştı [T-39]. Çekirdeği değiştirmek SYS-4'ü ihlal eder. Bu yüzden:

```
┌─────────────────────── ÇATI-O kutusu ───────────────────────┐
│  [Düğüm A: RAK4631, 868 MHz, ROUTER]                        │
│        │ UART (3,3 V, 115200) — Meshtastic Serial/PROTO API │
│  [Düğüm B: LR1121 kartı, LORA_24, ROUTER]                    │
│        └── AfetMesh Köprü modülü (Düğüm B'de çalışır)       │
│  [Güç: 12,8 V LFP → 5 V buck → A ve B (B yük anahtarlı)]    │
└─────────────────────────────────────────────────────────────┘
```

### 7.2 Köprü kuralları (yayın fırtınasını önleyen beyaz liste)

| Yön | Aktarılan | Aktarılmayan |
|---|---|---|
| **868 → omurga** (yukarı) | SOS portnum'u · SOS-TEYİT · hedefi omurgadaki bir hücrede olan DM'ler · ÇATI sağlık telemetrisi (hız sınırlı) | Genel sohbet, konum, NodeInfo, TA Mesh genel trafiği |
| **omurga → 868** (aşağı) | Bu hücreye ait SOS-TEYİT · hedefi **son 30 dk'da bu hücrede duyulmuş** DM'ler · MERKEZ duyuruları (≤ 1/10 dk) | Başka hücrelerin SOS'ları, genel yayınlar |
| Her iki yön | Yinelenen kimlik (8 baytlık SOS kimliği + Meshtastic paket kimliği) **tekrar aktarılmaz** | — |

### 7.3 Omurga kartı seçenekleri

| Seçenek | MCU + radyo | Meshtastic | Güç | Not |
|---|---|---|---|---|
| **A (prototip, önerilen)** | ESP32-S3 + LR1121 (ör. LilyGO T3-S3 LR1121) | `LORA_24` destekli kart ailesi [T-39] (**model bazında teyit edilecek**) | ~60–100 mA | Hazır, hızlı. Enerji yüksek → ÇATI-O 12 V sistem |
| B | ESP32 + SX1280 (PA'lı) | Destekli (LORA_24) | benzer | LR1121 alt-GHz'i de kapsar, esneklik için A tercih edilir |
| C (v2) | nRF52840 + LR1121 (özel taşıyıcı kart) | Varyant tanımı gerekir | ~10–15 mA (tahmin) | Enerji 5–8 kat düşer. Özel PCB ile, A doğrulandıktan sonra |
| — | 5 GHz Wi-Fi noktadan noktaya (Ubiquiti vb.) | Meshtastic dışı (IP) | 3–7 W | **Yalnızca MERKEZ ↔ şebeke beslemeli kule** arasında kurumsal seçenek. Güneşle beslenen röleye uygun değil |

---

## 8. Malzeme Listesi (BOM)

### 8.1 Prototip — hazır kartlarla (Aşama 5a, pilot öncesi)

> Fiyatlar **Eylül 2026 yaklaşık liste fiyatlarıdır** (USD, kargo/vergi hariç), sipariş öncesi teyit edilmelidir.

**ÇATI-B prototipi**

| Kalem | Parça | ≈ Fiyat |
|---|---|---:|
| MCU + LoRa | RAK4631 (868) + RAK19007 taban (WisMesh Starter Kit) | $25–30 |
| Şarj | **MPPT LFP/LTO uyumlu güneş şarj kartı** (7–30 V panel girişli, ör. µArt.cz Meshtastic MPPT) | $20–30 |
| Pil | 1S LiFePO4 3,2 V 12 Ah (2 × 32700 6 Ah, BMS'li, düşük sıcaklık şarj korumalı) | $25–35 |
| Panel | 12 V 10 W monokristal | $15–20 |
| Filtre | 868 MHz SAW bant geçiren (SMA) | $15–25 |
| Anten | 868 MHz fiberglas kolineer 3–5 dBi, N-dişi | $25–40 |
| Hat | LMR-195 N-erkek ↔ SMA 1 m + U.FL-SMA pigtail | $10–15 |
| Parafudr | N-N, DC topraklı, 0–3 GHz | $15–25 |
| Kutu | IP67 polikarbonat ~200×150×75, açık renk + Gore ventil + 3 rakor | $20–30 |
| Sensör | LIS3DH / LSM6DS3 (sarsıntı) + SHT40 (kutu içi) — WisBlock modülleri veya breakout | $10–15 |
| Montaj | Direk kelepçesi, 1,5 m galvaniz boru, paslanmaz bağlantılar | $20–35 |
| **Toplam** | | **≈ $200–310** |
| *Hızlı pilot alternatifi* | *RAK WisMesh Repeater Mini V2 (nRF52840, IP67, solar, 3200 mAh Li-ion) [T-49] + harici anten + filtre* | *$90–100 + ~$60* |

> **Hızlı pilot alternatifinin eksikleri (bilinçli kabul):** Li-ion pil (kış ve yaz sınırı, ÇT-PWR-4 ihlali), 3,2 Ah kapasite (7 gün otonomi TST-11 ile kanıtlanmalı), sarsıntı sensörü yok. **Yalnızca kısa süreli pilot için.**

**ÇATI-K prototipi = ÇATI-B + şunlar**

| Ek / değişen kalem | ≈ Fiyat |
|---|---:|
| RAK4631 yerine **RAK3401 + RAK13302** (1 W Booster kiti: SX1262 + SKY66122 PA/LNA/SAW). TX ≤ 24 dBm'e **sınırlanır**, amaç LNA'dır (A-11) | $40–60 |
| SAW yerine **868 kavite filtre** (≤ 1 dB) | $40–80 |
| 6 dBi ~1 m fiberglas anten + LMR-240 | $40–60 |
| 12,8 V 6 Ah LFP + 20 W panel + 12→5 V buck + 10 A MPPT şarj denetleyici (düşük sıcaklık kesmeli) | $80–110 |
| **ÇATI-K toplam** | **≈ $330–450** |

**ÇATI-O prototipi = ÇATI-K + şunlar**

| Ek kalem | ≈ Fiyat |
|---|---:|
| ESP32-S3 + LR1121 kartı (2,4 GHz, `LORA_24`) | $25–35 |
| 2,4 GHz 8–10 dBi omni (veya 14 dBi panel) + 2,4 GHz parafudr | $30–50 |
| Pil 12,8 V 12 Ah LFP, panel 30–40 W (ÇATI-K'nınkinin yerine) | +$50–70 |
| Yük anahtarı (omurga kartı güç döngüsü) + UART kablosu | $5 |
| **ÇATI-O toplam** | **≈ $440–610** |

**MERKEZ** = ÇATI-O radyo takımı (şebeke beslemeli) + mini PC (N100 sınıfı) veya Raspberry Pi 5 (PANO) + 12,8 V 100 Ah LFP UPS + isteğe bağlı uydu terminali → **≈ $800–1.200** (uydu hariç).

### 8.2 Üretim yolu — "ÇATI taşıyıcı kartı" (özel PCB, Aşama 5b)

ÇATI-B/K için **tek taşıyıcı PCB**. CEP'in RAK4630 footprint'i ve varyant tanımı yeniden kullanılır.

| Blok | Seçim | Gerekçe |
|---|---|---|
| MCU + radyo | **RAK4630 (H)** | CEP ile aynı modül, aynı `variant.h` altyapısı (ÇT-GEN-2) |
| Güneş şarjı | **TI BQ25798** (buck-boost, MPPT, 3,6–24 V giriş, JEITA NTC, 1S LFP için VREG 3,6 V) | Tek çipte MPPT + sıcaklık penceresi + güç yolu. ÇT-PWR-5/6/7 |
| Regülatör | TPS63900 buck-boost 3,3 V (CEP ile ortak) | LFP 2,5–3,6 V aralığını karşılar |
| Yakıt göstergesi | MAX17048 (CEP ile ortak). LFP'de düz eğri nedeniyle **coulomb sayımı** (ör. BQ27441 / INA219) değerlendirilecek | LFP gerilim eğrisi düz → gerilimden SoC güvenilmez |
| Emanet deposu | **SPI NOR flash 16 Mbit** (W25Q16, ~2.000+ SOS kaydı + halka günlüğü) | ÇT-GEN-8. Dahili flash firmware güncellemesiyle paylaşılmaz |
| Bekçi köpeği | TPL5010 harici zamanlayıcı | ÇT-GEN-5 |
| Sensörler | LSM6DS3TR-C (sarsıntı, CEP ile ortak) + SHT40 | ÇT-GEN-9, ÇT-ENV-4 |
| Omurga arayüzü | UART + 5 V anahtarlı çıkış (TPS22917 / yük anahtarı) | ÇT-PWR-10, Bölüm 7 |
| RF çıkışı | U.FL → kutu içi SMA → filtre → N bulkhead | ÇT-RF-3/5 |
| Koruma | DC girişinde TVS (SMBJ24A) + sigorta + ideal diyot ters kutup | ÇT-PWR-9 |
| Test | TC2030 SWD, USB-C, test noktaları (CEP HW-DBG ile aynı) | ÇT-DBG-1 |

**Taşıyıcı kart elektroniği (10 adet, tahmini):** ≈ $55–75. Kutu, anten, filtre, parafudr, pil ve panel prototiple aynıdır. **ÇATI-B üretim hedefi (100 adet): ≈ $150–180.**

---

## 9. Firmware Yapılandırma Profili

| Ayar | ÇATI-B | ÇATI-K | ÇATI-O (868 / 2,4) | Gerekçe |
|---|---|---|---|---|
| `device.role` | `ROUTER_LATE` | `ROUTER` | `ROUTER` / `ROUTER` | ÇT-K3 |
| `lora.region` | A-1'e göre (`EU_868` / `EU_433`) | ← | ← / `LORA_24` | SGB A-1 |
| `lora.modem_preset` | A-2'ye göre (TA Mesh uyumu: LongFast) | ← | ← / omurga: hızlı preset (TST-9) | SGB A-2 |
| `lora.hop_limit` | 3 | 3 | 3 / 5 (omurga) | D-6 |
| `lora.tx_power` | Bölüm 5.1 tablosu | ← | ← | ÇT-RF-1 |
| `device.rebroadcast_mode` | `ALL` | `ALL` | `ALL` | ÇT-RF-13 |
| `position.fixed_position` | ✅, yayın 12 saatte bir | ← | ← | ÇT-GEN-7 |
| `telemetry.device_update_interval` | 3 saat (afet profilinde kapalı) | ← | ← | ÇT-GEN-10 |
| `bluetooth.enabled` | Kurulumdan sonra ❌ | ❌ | ❌ | ÇT-GEN-4 |
| `security.admin_key` | MERKEZ yönetici anahtarı | ← | ← | Uzaktan yönetim |
| Kanallar | 0: TA Mesh genel · 1: `AfetMesh-ACIL` (SOS-6) · 2: `AfetMesh-OPS` (telemetri, teyit) | ← | ← | SGB SOS-6 |
| AfetMesh modülleri | SOS öncelik, emanet, afet profili | ← | + **Köprü** | SGB SOS-2/7/8 |

---

## 10. Kurulum ve Saha Prosedürü

| Adım | İş | Kontrol |
|---|---|---|
| 1 | **Planlama:** Meshtastic Site Planner (SRTM) + MAKS bina yükseklikleri + toplanma alanları (NOK-11) ile ÇATI-K ve ÇATI-O adayları. Her ÇATI-B için en yakın 2 ÇATI-K'ya görüş | Fresnel %60 açık (ÇT-RF-9), N+1 (ÇT-GEN-6) |
| 2 | **İzin:** Çatı ortak alandır. **Kat malikleri kurulu / yönetim kararı** alınır (Kat Mülkiyeti Kanunu, hukuki teyit). Direk: belediye veya EDAŞ. Kamu binası: kurum yazısı | SGB risk kaydı "kurulum izni" |
| 3 | **Tebliğ:** Sakinlere bilgi notu. "Çıkış 0,025–0,5 W, zamanın ≤ %10'unda yayın, baz istasyonu değildir" | Kamuoyu hassasiyeti (Bölüm 11, R-Ç5) |
| 4 | **Masa testi:** 48 saat ısı testi + firmware + yapılandırma + emanet deposu testi | ÇT-DBG-2 |
| 5 | **Montaj:** taşıyıcı elemana kimyasal dübel. Anten metal yapıların ≥ 35 cm üstünde. Damla halkası. Parafudr → topraklama | ÇT-MEC-1…4, ÇT-RF-5/6/8 |
| 6 | **Devreye alma:** gürültü tabanı filtreli ve filtresiz, en yakın ÇATI'lara RSSI/SNR, bina içinde 3 katta (bodrum, orta, üst) CEP → ÇATI-B testi | ÇT-DBG-3, TST-8 |
| 7 | **Kayıt:** kurulum formu + fotoğraf + koordinat → MERKEZ envanterine | ÇT-DBG-3 |
| 8 | **Bakım:** yılda bir (nem alıcı, konnektör, panel temizliği). Uzaktan sağlık telemetrisi sürekli | ÇT-GEN-10 |

> **İş güvenliği:** Çatı çalışması yüksekte çalışmadır. Korkuluksuz çatıda emniyet kemeri ve yaşam hattı zorunludur. Kurulum ekipleri buna göre planlanır.

---

## 11. Riskler

| # | Risk | Etki | Azaltma |
|---|---|---|---|
| R-Ç1 | Çatı rölesi tek başına kapasite getirmez (LongFast'te röle başına ~7 paket/dk yasal sınır) | **Yüksek** | Araştırma Bölüm 4. Trafik disiplini + omurga + A-2 preset kararı |
| R-Ç2 | 2,4 GHz omurga menzili kent içinde yetersiz çıkar | Yüksek | TST-9 erken ölçüm. Yedek: ÇATI-O aralığını sıklaştırmak veya MERKEZ yakınında 5 GHz PtP |
| R-Ç3 | Standart TA Mesh ROUTER'ları `CORE_PORTNUMS_ONLY` kullanıyorsa SOS portnum'u taşınmaz | **Yüksek** | SGB A-12 (SOS kodlaması) + TST-5 genişletildi |
| R-Ç4 | Çatıdaki operatör vericisi alıcıyı sağırlaştırır | Orta | ÇT-RF-3 filtre. TST-10 ile ölçüm |
| R-Ç5 | Sakinlerin "anten" itirazı (baz istasyonu hassasiyeti) | Orta | Kurulum adımı 3 (bilgi notu). Düşük güç. Kamu binası öncelikli |
| R-Ç6 | Depremde ÇATI'nın kendisi veya binası hasar görür | Orta | N+1 kapsama, taşıyıcı elemana montaj, kütle sınırı |
| R-Ç7 | Kış güneşi yetersiz → düğüm Ocak'ta söner | Orta | Aralık ışınımıyla boyutlandırma + 2× marj + TST-11 |
| R-Ç8 | Hırsızlık / vandalizm (panel, pil) | Orta | Erişimi kısıtlı çatı, kilitli kutu, kimlik etiketi, sağlık telemetrisiyle kayıp tespiti |
| R-Ç9 | LNA'lı ön uç kentsel gürültüde fayda sağlamaz, sadece tüketimi artırır | Düşük | A-11: LNA'lı ve LNA'sız gürültü tabanı ile paket alım oranı karşılaştırması |
| R-Ç10 | Köprü modülü hatası omurgadan erişime yayın fırtınası taşır | Orta | Bölüm 7.2 beyaz listesi, hız sınırı, TST-12 |

---

## 12. Doğrulama ve Test Planı

| # | Test | Başarı kriteri | Gereksinim |
|---|---|---|---|
| **TST-8** | **Bina içi dikey bağlantı:** 8+ katlı betonarme binada bodrum, zemin, orta ve üst katlardaki CEP → aynı binanın ÇATI-B'si (3 anten kazancıyla) | Her katta paket teslimi ölçülür. **Hedef: zemin kattan ≥ %90** (SYS-7: ölçüm beyan edilir) | ÇT-RF-7, AR-9 |
| **TST-9** | **2,4 GHz omurga menzili:** çatıdan çatıya 0,5 / 1 / 2 / 3 km, LR1121, 812 kHz | 2 km'de ≥ %95 teslim → ÇATI-O aralığı kesinleşir | ÇT-RF-10/11, AR-7 |
| **TST-10** | **Filtre ve gürültü tabanı:** operatör vericili çatıda filtreli ve filtresiz gürültü tabanı + paket alım oranı. ÇATI-K'da LNA'lı ve LNA'sız | Filtre kazancı ölçülür. LNA kararı (A-11) | ÇT-RF-2/3, AR-12 |
| **TST-11** | **Kış enerji testi:** Aralık–Ocak'ta 14 gün, gerçek trafik, pil SoC kaydı | Pil hiçbir gün < %30 inmez. 7 gün karanlık simülasyonunda çalışır | ÇT-PWR-2/3 |
| **TST-12** | **Emanet zinciri uçtan uca:** SOS gönder → yol üstündeki ÇATI-O'yu kapat → 1 saat sonra aç | SOS kaybolmaz. MERKEZ'e ulaşır, teyit kaynağa döner, yinelenme yok | ÇT-GEN-8, SGB SOS-7/8 |
| **TST-13** | **Patlama testi:** 50 CEP'e aynı dakikada SOS bastır (jitter açık/kapalı) | Jitter açıkken ≥ %95 SOS 10 dk içinde MERKEZ'de. Karşılaştırma kaydedilir | SGB SOS-10 |
| TST-14 | Kendiliğinden toparlanma: pili sıfıra düşür, 0 °C'de güneş ver | İnsan müdahalesiz açılır, bootloop yok | ÇT-PWR-7 |
| TST-15 | Sarsıntı ile afet profili: sarsma masası veya el ile | Profil ≤ 10 s'de devreye girer, yanlış tetik 7 günde 0 | ÇT-GEN-9 |
| TST-16 | ERP ölçümü (SDR + kalibre anten veya iletimli ölçüm + anten verisi) | Bölüm 5.1 sınırları içinde | ÇT-RF-1 |

---

## 13. Açık Kararlar (SGB'ye taşınanlar)

| # | Karar | Seçenekler | Ölçüt | Ne zaman |
|---|---|---|---|---|
| **A-9** | Omurga teknolojisi | **2,4 GHz LoRa (öneri)** · 433 MHz ikinci radyo · 5 GHz Wi-Fi PtP (yalnızca şebekeli noktalar) | TST-9 menzili + REG-4 yasal teyit | Aşama 5a |
| **A-10** | ÇATI-B rolü | **ROUTER_LATE (öneri)** · CLIENT_BASE (bina sakinlerinin CEP'leri favori) | AR-10 (favori sınırı, kurulum yükü) + TST-8 | Aşama 5a |
| **A-11** | ÇATI-K'da LNA'lı ön uç | Evet (RAK13302, TX ≤ 24 dBm) · Hayır (yalnız SX1262 + kavite) | TST-10 gürültü tabanı + güç farkı | Aşama 5a |
| **A-12** | SOS paket kodlaması | Özel portnum (SGB SOS-1) · `TEXT_MESSAGE_APP` içinde yapılandırılmış metin (`#SOS1 …`) · ikisi birden | TST-5 (CORE_PORTNUMS_ONLY router'lı test) | Aşama 0 |

---

## Değişiklik Geçmişi

| Sürüm | Tarih | Değişiklik |
|---|---|---|
| **1.0** | **24 Eyl 2026** | İlk sürüm. ÇATI-B/K/O + MERKEZ sınıfları, 46 gereksinim, yasal ERP'den TX hesabı, güç bütçesi, köprü tasarımı, prototip ve üretim BOM'u, kurulum prosedürü, TST-8…TST-16, A-9…A-12 |
