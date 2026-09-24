# AfetMesh — CEP Ailesi Donanım Gereksinim ve Malzeme Seçim Raporu

> ## 📎 BELGE STATÜSÜ: TEKNİK EK (v2.1) — **CEP / CEP+ / CEP-T kişisel cihaz ailesi için geçerlidir**
>
> **Tarih:** 24 Eylül 2026 · Yürürlükteki üst belge: **[`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](../00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md)** (SGB v2.4)
>
> **Bu sürümde ne değişti (v1.1 → v2.0):** Belge, PCB'yi tasarlayacak kişinin doğrudan kullanabileceği bir **gereksinim + malzeme seçim** belgesine dönüştürüldü.
> - Tüm donanım gereksinimleri tek bir numaralı tabloda toplandı (Bölüm 3) — öncelik, doğrulama yöntemi ve kaynak ile
> - **İşlemci seçimi** TI, STM32, ESP32, Raspberry Pi (RP2040/RP2350/Zero 2 W) ve Nordic (nRF52840/nRF54L15) arasında ağırlıklı puanlama ile yeniden yapıldı (Bölüm 4)
> - **Ölçülmüş verilere dayalı güç bütçesi** ve **5 güç modu** tanımlandı (Bölüm 5)
> - Her alt sistem için aday parça tablosu ve gerekçeli seçim eklendi (Bölüm 6)
> - v1.1'deki üç hata düzeltildi: **WS2812B LED** (sönükken bile ~1 mA çeker), **LDO + düşük kesme gerilimi** (soğukta brownout), **HT-N5262M** (433 MHz sürümü yok)
> - Akademik ve topluluk kaynakları ayrı belgeye taşındı: **[`04_Dokumanlar/Literatur_ve_Topluluk_Arastirmasi.md`](../04_Dokumanlar/Literatur_ve_Topluluk_Arastirmasi.md)** (30 akademik yayın + 26 topluluk kaynağı + 14 ticari cihaz). Bu belgede `[L-n]` makaleyi, `[T-n]` topluluk kaynağını, `[C-n]` ticari cihazı gösterir.
>
> **Kapsam dışı:** NOKTA (kamu node'u) — ESP32-S3 + PSRAM, hazır kart reçetesi, özel PCB yok (SGB Bölüm 7).

---

## 1. Yönetici Özeti — Altı Karar

| # | Karar | Seçim | Tek cümlelik gerekçe |
|---|---|---|---|
| **K-1** | Kaç kart? | **Tek PCB, montaj varyantları** (CEP / CEP+ / CEP-T) | Tek kişilik ekipte asıl maliyet parça değil, tasarım turudur. |
| **K-2** | İşlemci | **Nordic nRF52840** (Cortex-M4F, 64 MHz, 1 MB flash, 256 KB RAM, BLE 5) | Meshtastic'in en olgun düşük güç platformu. Ölçülmüş saha verisinde ESP32-S3 cihazlardan **5–15 kat** uzun pil ömrü veriyor [T-1][T-3]. Puanlamada 460/500 ile birinci (Bölüm 4). |
| **K-3** | LoRa radyo | **Semtech SX1262** (TCXO'lu, sertifikalı modül içinde) | Meshtastic'in önerdiği radyo ailesi; SX127x artık önerilmiyor [T-2]. |
| **K-4** | Modül | **RAK4630** — birincil. (L) = 433 MHz, (H) = 868 MHz, **aynı footprint** | Frekans kararı (SGB A-1) açıkken PCB layout'unu **bloke etmeyen** tek birleşik modül. HT-N5262M'nin 433 MHz sürümü yoktur. |
| **K-5** | Güç mimarisi | **18650 → BQ24074 (power-path) → TPS63900 buck-boost (3,3 V, 75 nA Iq)** + anahtarlı çevre birim hatları | LDO ile pil ~3,4 V'ta "boş" sayılır ve soğukta TX anında brownout olur [T-4]. Buck-boost hücreyi 3,0 V'a kadar kullanır. |
| **K-6** | Ekran + giriş | **2,9" e-paper (SSD1680) + fiziksel butonlar (5 yön + Geri + korumalı SOS)**. Dokunmatik **birincil arayüz değildir** | Dokunmatik arayüz (Meshtastic MUI) **yalnızca ESP32-S3/Linux'ta** çalışır; nRF52'de desteklenmez [T-5]. E-paper, nRF52'nin resmî düşük güçlü arayüzü InkHUD ile desteklenir [T-6]. Görüntüyü korumak için güç harcamaz. |

> ### ⚠️ K-2 ve K-6'nın CEP-T'ye etkisi (bilinçli ödünleşim)
> `05_Cihaz_Tasarimi/CEPT_Dokunmatik_Cihaz_Tasarim_Raporu.md` dokunmatik ekran için ESP32-S3 önermişti. Kullanıcının bu turdaki **birinci önceliği uzun pil ömrü ve tasarruf modudur.** İki hedef aynı işlemcide birleşmiyor:
>
> | | **Yol A — nRF52840 + e-paper + butonlar (SEÇİLEN)** | Yol B — ESP32-S3 + dokunmatik TFT |
> |---|---|---|
> | Ölçülmüş/beklenen ortalama akım | ~5–9 mA [T-1][T-4] | ~50–130 mA (dinamik light-sleep ile ~50 mA) [T-1][T-7] |
> | 3000 mAh 18650 ile (GPS kapalı) | **~12–25 gün** | ~1–2 gün |
> | Dokunmatik arayüz | ❌ (InkHUD + butonlar) | ✅ MUI |
> | Güneşte okunabilirlik | ✅ Mükemmel (yansımalı) | ⚠️ Zayıf (arka ışık gerekir) |
> | Cihaz kapansa bile son bilgi ekranda | ✅ | ❌ |
>
> ✅ **Karar verildi (24 Eyl 2026, SGB A-7 kapandı):** Kullanıcı dokunmatik ekranın **zorunlu olmadığını** bildirdi → **Yol A** uygulanır. Yol B yalnızca karşılaştırma kaydı olarak tutulmaktadır.

---

## 2. Tasarım Öncelikleri (Sıralı)

Çatışma durumunda üstteki öncelik kazanır.

| Sıra | Öncelik | Ölçüt |
|---|---|---|
| **P1** | **Uzun kullanım süresi** | Ölçülmüş ortalama akım; kapalıyken sızıntı; soğukta kullanılabilir kapasite |
| **P2** | **Meshtastic birlikte çalışabilirliği** | Upstream firmware'in desteklediği MCU + radyo; `PRIVATE_HW` varyantla derlenebilirlik |
| **P3** | **Sahada dayanıklılık** | IP54, 1,5 m düşme, −10…+50 °C, değiştirilebilir pil |
| **P4** | **Tek kişilik PCB tasarımına uygunluk** | Sertifikalı modül, el lehimine uygun paketler, ikinci kaynak |
| **P5** | **Maliyet** | 100 adette ≤ $40 (CEP), ≤ $60 (CEP+/CEP-T) |

---

## 3. Donanım Gereksinim Tablosu

**Öncelik:** **Z** = Zorunlu · **Ö** = Önemli (gerekçe olmadan düşürülmez) · **İ** = İsteğe bağlı / varyanta özel
**Doğrulama:** **A** = Analiz/tasarım incelemesi · **Ö** = Ölçüm · **T** = Test · **İ** = İnceleme (şematik/layout kontrol listesi)
**Varyant:** C = CEP · C+ = CEP+ · T = CEP-T · Hepsi = tüm aile

### 3.1 İşlemci ve Firmware Uyumluluğu (HW-MCU)

| # | Gereksinim | Önc. | Doğr. | Varyant | Kaynak |
|---|---|---|---|---|---|
| HW-MCU-1 | MCU, Meshtastic upstream firmware'inin **resmî olarak desteklediği** bir mimari olacak | Z | İ | Hepsi | SGB SYS-3/SYS-4 |
| HW-MCU-2 | MCU **nRF52840** olacak | Z | İ | Hepsi | K-2, Bölüm 4 |
| HW-MCU-3 | BLE 5 donanımı bulunacak (iOS ile tek bağlantı yolu BLE'dir) | Z | T | Hepsi | SGB CEP-5 |
| HW-MCU-4 | ≥ 1 MB flash, ≥ 256 KB RAM | Z | A | Hepsi | Meshtastic nRF52 hedefi |
| HW-MCU-5 | Firmware USB-C üzerinden **UF2 sürükle-bırak** ile güncellenebilecek (nRF52840 yerel USB) | Z | T | Hepsi | Topluluk pratiği [T-8] |
| HW-MCU-6 | **SWD erişimi** (SWDIO, SWCLK, GND, VDD, nRESET) — boş MCU'ya bootloader yazmak için | Z | İ | Hepsi | [T-9] |
| HW-MCU-7 | 32,768 kHz **LFXO kristali** (modül içinde yoksa kartta) — BLE düşük güç zamanlaması için | Z | İ | Hepsi | nRF52840 PS |
| HW-MCU-8 | Donanım varyant tanımı `PRIVATE_HW` ile yapılacak; pin haritası `variant.h` içinde belgelenecek | Z | İ | Hepsi | Meshtastic build doc |
| HW-MCU-9 | Donanım bekçi köpeği (WDT) ve brown-out reset firmware'de etkin olacak | Ö | T | Hepsi | [T-4] (soğukta bootloop) |

### 3.2 LoRa Radyo ve Anten (HW-RF)

| # | Gereksinim | Önc. | Doğr. | Varyant | Kaynak |
|---|---|---|---|---|---|
| HW-RF-1 | Radyo **SX1262** (veya SX1268 = 433 MHz ikizi) olacak | Z | İ | Hepsi | K-3 |
| HW-RF-2 | Radyo, **sertifikalı modül** içinde gelecek; çıplak çip RF tasarımı yapılmayacak | Z | İ | Hepsi | K-4 |
| HW-RF-3 | Radyo **TCXO** içerecek; `SX126X_DIO3_TCXO_VOLTAGE` firmware'de doğru tanımlanacak | Z | T | Hepsi | [T-10] |
| HW-RF-4 | PCB, SGB **A-1** kararına (433 / 868 MHz) **footprint değiştirmeden** uyum sağlayacak | Z | İ | Hepsi | K-4 |
| HW-RF-5 | LoRa çıkışı **SMA dişi bulkhead** konnektöre, **50 Ω** kontrollü empedanslı (CPWG) hatla ve **≤ 15 mm** uzunlukta bağlanacak | Z | İ/Ö | Hepsi | RF iyi uygulama |
| HW-RF-6 | Hatta **π-eşleme ağı footprint'i** (0402, varsayılan: seri 0 Ω, şönt DNP) bırakılacak | Ö | Ö | Hepsi | Anten ayarı için |
| HW-RF-7 | Anten hattının altında kesintisiz GND, iki yanında **dikiş via'ları** (≤ λ/20 aralık) | Z | İ | Hepsi | RF iyi uygulama |
| HW-RF-8 | **BLE anteni** (2,4 GHz çip/PCB anten) modülün `RF_BT` pinine bağlanacak; **anten keep-out** alanında bakır/parça olmayacak | Z | İ/T | Hepsi | RAK4630 datasheet |
| HW-RF-9 | Çıkış gücü firmware'de bölge limitine sınırlanacak (433: ~10 mW ERP · 868: alt banda göre) | Z | Ö | Hepsi | SGB REG-2/REG-3 |
| HW-RF-10 | Anten takılı değilken TX yapılmayacağı kullanıcıya etiket + kılavuzla bildirilecek | Z | İ | Hepsi | SGB REG-6 |
| HW-RF-11 | Radyo `DIO1` kesmesi MCU'nun uykudan uyandırabilen bir GPIO'suna bağlanacak | Z | İ | Hepsi | Beacon modu (Bölüm 5) |

### 3.3 Güç Sistemi (HW-PWR)

| # | Gereksinim | Önc. | Doğr. | Varyant | Kaynak |
|---|---|---|---|---|---|
| HW-PWR-1 | Tek hücre **18650 Li-ion**, **kullanıcı tarafından aletsiz değiştirilebilir** | Z | T | Hepsi | SGB CEP-N4 |
| HW-PWR-2 | USB-C 5 V ile şarj; şarj sırasında cihaz çalışmaya devam edecek (**power-path**) | Z | T | Hepsi | — |
| HW-PWR-3 | Hücre koruması: aşırı şarj, aşırı deşarj, aşırı akım, kısa devre (kartta DW01A+FS8205A **veya** korumalı hücre) | Z | T | Hepsi | [L-29] |
| HW-PWR-4 | **Ters takılan pile karşı koruma** (P-MOSFET ideal diyot) — değiştirilebilir pilde zorunlu | Z | T | Hepsi | Değiştirilebilir hücre riski |
| HW-PWR-5 | Şarj **sıcaklık denetimi** (NTC, 0…45 °C dışında şarj yok) | Z | T | Hepsi | Li-ion güvenliği [L-28] |
| HW-PWR-6 | 3,3 V ana hat regülatörü **sükûnet akımı ≤ 1 µA** ve pil **3,0 V'a inene kadar** 3,3 V'u tutabilecek (buck-boost) | Z | Ö | Hepsi | [T-4], K-5 |
| HW-PWR-7 | Regülatör, **TX tepe yükünü** (SX1262 @22 dBm ~120 mA + MCU + e-paper + GNSS ≈ 170 mA) %100 marjla karşılayacak | Z | Ö | Hepsi | SX1262 DS |
| HW-PWR-8 | GNSS, ekran ve sensörler **ayrı yük anahtarları** arkasında olacak; kapalıyken sıfır akım | Z | Ö | Hepsi | [T-11] (T114 kapalıyken 8 mA) |
| HW-PWR-9 | **Kapalı (raf) modu akımı ≤ 10 µA** — cihaz çekmecede aylarca bekleyebilmeli | Z | Ö | Hepsi | Afet cihazı gerçeği |
| HW-PWR-10 | **Raf ömrü:** tam şarjlı cihaz kapalı olarak 6 ay sonra ≥ %75 şarj göstermeli | Ö | T | Hepsi | HW-PWR-9 + hücre öz-deşarjı |
| HW-PWR-11 | Pil durumu **yakıt göstergesi** (fuel gauge) veya anahtarlı gerilim bölücüyle okunacak; bölücü uykuda akım çekmeyecek | Z | Ö | Hepsi | [T-4] |
| HW-PWR-12 | Pil düşük gerilim eşiği firmware'de **≥ 3,3 V (yük altında)** ayarlanacak; LPCOMP ile donanımsal uyanma | Ö | T | Hepsi | [T-4] |
| HW-PWR-13 | USB girişinde ESD + aşırı gerilim (TVS) koruması; CC1/CC2'de **5,1 kΩ** pull-down | Z | İ | Hepsi | USB-C spesifikasyonu |
| HW-PWR-14 | Harici **solar / 5–6 V DC** giriş için lehim pedi veya JST konnektör (power-path'e) | İ | T | C+, T | Uzun kesinti senaryosu |
| HW-PWR-15 | Harici güç var/yok bilgisi MCU'ya bildirilecek (VBUS algılama) | Ö | T | Hepsi | Meshtastic "power loss" özelliği |

### 3.4 Güç Modları (HW-MOD) — "pil tasarrufu moduna sokabilmeliyiz"

| # | Gereksinim | Önc. | Doğr. | Kaynak |
|---|---|---|---|---|
| HW-MOD-1 | Cihaz **5 güç modunu** donanımsal olarak destekleyecek (tanımlar Bölüm 5.2): Normal · Tasarruf · Enkaz/Beacon · Kapalı · Şarj | Z | T | Kullanıcı gereksinimi |
| HW-MOD-2 | Tasarruf moduna **butonla** (uzun basma) ve **pil eşiğiyle otomatik** geçilebilecek | Z | T | — |
| HW-MOD-3 | Her moddan fiziksel butonla (GPIO SENSE uyanma) çıkılabilecek | Z | T | nRF52840 System OFF |
| HW-MOD-4 | İvmeölçer kesmesi MCU'yu uyandırabilecek (hareketle ekran/uyanma) | Ö | T | CEP-T CT-F6 |
| HW-MOD-5 | **Kapalı modda** tüm çevre birimler + 3,3 V hattı donanımsal olarak kesilecek (yalnızca MCU'nun bekleme beslemesi değil) | Z | Ö | [T-11] |

### 3.5 Kullanıcı Arayüzü (HW-UI)

| # | Gereksinim | Önc. | Doğr. | Varyant | Kaynak |
|---|---|---|---|---|---|
| HW-UI-1 | **Ayrı SOS butonu**, yanlışlıkla basmaya karşı korumalı (gömme + kapak veya 3 sn basılı tutma) | Z | T | Hepsi | SGB CEP-3 |
| HW-UI-2 | **5 yönlü navigasyon + Seç + Geri** fiziksel butonları — tüm menüler butonla gezilebilecek | Z | T | C+, T | CEP-T CT-F3/F4 |
| HW-UI-3 | Güç/menü butonu (tek butonla da InkHUD tam kullanılabilir) | Z | T | Hepsi | [T-6] |
| HW-UI-4 | Butonlar eldivenle ve ıslakken çalışacak (fiziksel strok ≥ 0,25 mm, silikon kapak) | Z | T | Hepsi | Saha koşulu |
| HW-UI-5 | **Sesli uyarıcı:** 10 cm'de ≥ 85 dB(A), enkaz altında bulunabilirlik için | Z | Ö | Hepsi | SGB CEP-4 (Enkaz Modu) |
| HW-UI-6 | Durum LED'leri **sönükken sıfır akım** çekecek (adreslenebilir LED **kullanılmayacak**) | Z | Ö | Hepsi | v1.1 hatası düzeltmesi |
| HW-UI-7 | Titreşim motoru (sessiz uyarı) | İ | T | C+, T | CEP-T |
| HW-UI-8 | Tüm buton hatlarında ESD koruması (TVS dizisi veya seri direnç + kapasitör) | Ö | İ | Hepsi | Kasa dışına açılan hatlar |

### 3.6 Ekran (HW-DSP)

| # | Gereksinim | Önc. | Doğr. | Varyant | Kaynak |
|---|---|---|---|---|---|
| HW-DSP-1 | Ekran **doğrudan güneş altında okunabilir** olacak (yansımalı teknoloji) | Z | T | C+, T | Kullanıcı gereksinimi |
| HW-DSP-2 | Ekran **görüntüyü korumak için güç harcamayacak**; cihaz kapanınca son durum ekranda kalacak | Z | Ö | C+, T | E-paper [T-22] |
| HW-DSP-3 | Meshtastic'in nRF52 üzerinde **yerleşik sürücüsü olan** bir kontrolör seçilecek | Z | İ | C+, T | [T-6] |
| HW-DSP-4 | Ekran, karta **FPC konnektörle** bağlanacak; temel CEP'te konnektör boş kalacak | Z | İ | Hepsi | K-1 |
| HW-DSP-5 | Ekran beslemesi yük anahtarı arkasında olacak | Z | Ö | C+, T | HW-PWR-8 |
| HW-DSP-6 | −10 °C'de yenileme çalışacak (e-paper'ın soğukta yavaşladığı bilinir; test edilecek) | Ö | T | C+, T | Açık risk R-7 |
| ~~HW-DSP-7~~ | ~~Dokunmatik katman rezervi~~ — **kaldırıldı** (A-7 kapandı, dokunmatik zorunlu değil) | — | — | — | A-7 |

### 3.7 Konum ve Sensörler (HW-SNS)

| # | Gereksinim | Önc. | Doğr. | Varyant | Kaynak |
|---|---|---|---|---|---|
| HW-SNS-1 | GNSS modülü **yük anahtarı arkasında**; varsayılan KAPALI | Z | Ö | C+, T | [T-3] (GPS pil ömrünü ~yarıya indiriyor) |
| HW-SNS-2 | GNSS takip modunda ≤ 30 mW; çoklu takımyıldız (GPS+Galileo+BeiDou) | Ö | Ö | C+, T | — |
| HW-SNS-3 | GNSS anteni LoRa anteninden ≥ 30 mm uzakta, gökyüzüne bakan yüzde | Z | İ | C+, T | — |
| HW-SNS-4 | **Harici GNSS konnektörü** (JST-SH 4 pin: 3V3_SW, GND, TX, RX) | Ö | T | T | CEP-T CT-F9 |
| HW-SNS-5 | Sıcaklık/nem sensörü, **Meshtastic telemetri modülünün desteklediği** bir parça olacak | Z | T | C+, T | [T-12] |
| HW-SNS-6 | Sıcaklık sensörü PCB'de ısı kaynaklarından (regülatör, radyo PA) **termal olarak yalıtılacak** (yarık/slot) ve kasa havalandırmasına yakın olacak | Ö | Ö | C+, T | Ölçüm doğruluğu |
| HW-SNS-7 | **6 eksen IMU** (ivmeölçer + jiroskop), Meshtastic destekli, **kesme pini** MCU'ya bağlı | Ö | T | C+, T | CEP-T CT-F6/F7, [L-26][L-27] |
| HW-SNS-8 | Sensörler ortak I²C hattında; hat 3V3_SW'ye bağlı pull-up'larla (kapalıyken kaçak yok) | Z | İ | C+, T | HW-PWR-9 |

### 3.8 Programlama, Test ve Üretim (HW-DBG / HW-PCB)

| # | Gereksinim | Önc. | Doğr. | Kaynak |
|---|---|---|---|---|
| HW-DBG-1 | SWD için **Tag-Connect TC2030-NL** pedi | Z | İ | [T-9] |
| HW-DBG-2 | Test noktaları: VBAT, VSYS, 3V3, 3V3_SW, USB 5V, UART TX/RX, GND (≥ 3 adet) | Z | İ | — |
| HW-DBG-3 | **Akım ölçüm köprüsü:** 3V3 hattında 0 Ω / lehim köprüsü (PPK2 ile ölçüm için kesilebilir) | Z | İ | [T-4] PPK2 pratiği |
| HW-PCB-1 | **4 katman** (SIG / GND / PWR / SIG), 1,6 mm, 1 oz, ENIG | Z | İ | RF + güç gürültüsü |
| HW-PCB-2 | Boyut hedefi ≈ 90 × 55 mm (18650 yatay + 2,9" e-paper) | Ö | İ | — |
| HW-PCB-3 | Modül üreticisinin anten keep-out kuralları | Z | İ | RAK4630 DS |
| HW-PCB-4 | Buck-boost indüktörü ve anahtarlama düğümü RF hattından ≥ 10 mm uzak, katman değiştirmeden kısa döngü | Z | İ | TPS63900 DS |
| HW-PCB-5 | ≥ 4 × M2.5 montaj deliği, kasa boss'larıyla hizalı | Z | İ | — |
| HW-PCB-6 | Konformal kaplama (RF pedleri ve konnektörler maskelenerek) | Ö | T | Nem |
| HW-PCB-7 | Tüm pasifler ≥ 0402, IC'ler el lehimine/sıcak havaya uygun paket (QFN kabul, BGA yok) | Ö | İ | P4 |
| HW-PCB-8 | Her kritik parça için **ikinci kaynak** BOM'da belirtilecek | Ö | İ | SGB risk kaydı |

### 3.9 Çevresel ve Mekanik (HW-ENV) — kasa sonraya bırakıldı, elektronik uyumlu seçilir

| # | Gereksinim | Önc. | Doğr. | Kaynak |
|---|---|---|---|---|
| HW-ENV-1 | Çalışma: −10…+50 °C · Şarj: 0…45 °C | Z | T | SGB CEP-N6, [L-28] |
| HW-ENV-2 | Tüm aktif parçalar ≥ −20…+70 °C sınıfında | Z | İ | — |
| HW-ENV-3 | IP54 kasaya uygun: konnektörler (USB-C, SMA) kenarda ve contalanabilir konumda | Z | İ | SGB CEP-N5 |
| HW-ENV-4 | 1,5 m betona düşme sonrası çalışır — pil tutucu ve ağır parçalar mekanik destekli | Z | T | SGB CEP-N9 |
| HW-ENV-5 | Ağırlık (pil dâhil, kasa hariç) < 110 g | Ö | Ö | SGB CEP-N7 (< 150 g toplam) |

---

## 4. İşlemci Seçimi — TI, STM32, ESP32, Raspberry Pi, Nordic

### 4.1 Yöntem

1. **Kapı kriteri (geç / kal):** Meshtastic upstream firmware'i bu MCU'yu çalıştırıyor mu? Çalıştırmıyorsa, mevcut ağlarla (TA Mesh dâhil) birlikte çalışmak için **sıfırdan port** gerekir; bu SGB SYS-3/SYS-4'ü ihlal eder ve tek kişilik ekip için gerçekçi değildir.
2. **Ağırlıklı puanlama (1–5 puan × ağırlık, toplam 500):** Kapıyı geçen adaylar puanlanır.

### 4.2 Aday Listesi ve Kapı Kriteri

| Aile | Aday | Meshtastic desteği (Eylül 2026) | Kapı |
|---|---|---|---|
| **Texas Instruments** | CC1352R / CC1312R (Sub-GHz + BLE) | ❌ **Yok.** CC13xx radyosu LoRa modülasyonunu desteklemez (yalnızca Sidewalk/802.15.4g/proprietary FSK) [T-13] | ❌ Elendi |
| Texas Instruments | MSP430 / MSPM0 + harici SX1262 | ❌ Port yok | ❌ Elendi |
| **STMicroelectronics** | STM32WLE5 (M4 + SX126x tek çip; RAK3172, Wio-E5) | ⚠️ Topluluk desteği; **düşük flash/RAM nedeniyle özellikler çıkarılmış**, **BLE yok**, **uyku modu uygulanmamış** — ölçülen ortalama ~12–21 mA [T-14] | ⚠️ Geçti (koşullu) |
| STMicroelectronics | STM32L4 / U5 + harici SX1262 | ❌ Port yok | ❌ Elendi |
| **Espressif** | ESP32 (orijinal) | ✅ Destekli, ancak yeni tasarımda S3'e göre avantajı yok | ⚠️ Puanlanmadı |
| Espressif | **ESP32-S3** (+PSRAM) | ✅ Tam destek; MUI dokunmatik arayüz yalnızca burada | ✅ Geçti |
| Espressif | ESP32-C6 | ✅ Yeni destek (M5Stack C6L, T-LoRa C6) [T-2] | ✅ Geçti |
| **Raspberry Pi** | RP2040 / RP2350 (Pico / Pico 2) | ✅ Destekli; **BLE Meshtastic'te desteklenmiyor** (iOS bağlantısı imkânsız) [T-15] | ✅ Geçti |
| Raspberry Pi | Zero 2 W (Linux, `meshtasticd`) | ✅ Destekli (Linux native) — boşta ~100–180 mA @5 V [T-16] | ✅ Geçti |
| **Nordic** | **nRF52840** | ✅ Tam destek, "solar ve el cihazları için tercih edilir" [T-2] | ✅ Geçti |
| Nordic | nRF54L15 | ⚠️ **Deneysel** — bare-metal port 15 Eylül 2026'da birleştirildi [T-17] | ✅ Geçti (izleme) |

> **TI neden yok:** TI'ın sub-GHz ürünleri (CC13xx) güçlü ve düşük güçlüdür, ama **LoRa değildir.** Meshtastic ağına katılamayan bir cihaz, bu projenin temel varsayımını (mevcut ağın üzerine binmek) çöpe atar. TI parçaları bu tasarımda **güç yönetiminde** (BQ24074 şarj, TPS63900 regülatör, TPS22917 yük anahtarı) kullanılıyor — işlemci olarak değil.

### 4.3 Ağırlıklı Puanlama

| Kriter (ağırlık) | nRF52840 | ESP32-S3 | nRF54L15 | ESP32-C6 | RP2040/2350 | STM32WL | Pi Zero 2 W |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| **Güç tüketimi — ölçülmüş** (30) | **5** | 2 | 5 | 3 | 2 | 3 | 1 |
| Meshtastic olgunluğu / özellik seti (20) | **5** | 5 | 1 | 2 | 3 | 1 | 4 |
| İşlem gücü / bellek (10) | 3 | **5** | 4 | 3 | 4 | 2 | 5 |
| BLE (telefon + iOS) (10) | **5** | 4 | 5 | 4 | 1 | 1 | 1 |
| Hazır sertifikalı modül / ekosistem (10) | **5** | 5 | 2 | 3 | 3 | 3 | 4 |
| Kendi PCB'nde tasarım kolaylığı (10) | 4 | 4 | 3 | 4 | 4 | **5** | 1 |
| Maliyet / temin (10) | 4 | **5** | 4 | 5 | 5 | 5 | 3 |
| **TOPLAM (/500)** | **460** | 390 | 350 | 320 | 290 | 270 | 250 |

**Puanlama gerekçeleri (güç satırı — en ağır kriter):**

| MCU | Veri | Kaynak |
|---|---|---|
| nRF52840 | Meshtastic ile tüm cihaz ~4,5–9 mA; aynı 3000 mAh ile 154–453 saat (RAK19007), T114 GPS kapalı 104–220 saat; BLE'nin ek tüketimi ölçülemeyecek kadar küçük | [T-1][T-3][T-4] |
| ESP32-S3 | Varsayılan ayarlarla 10–30 saat; T-Deck 10–26 saat; Heltec V3 ~130 mA ortalama; dinamik light-sleep ile ~50 mA (yamalar henüz birleştirilmedi) | [T-1][T-3][T-7] |
| STM32WL | Uyku modu uygulanmamış: TX dışı ortalama ~12 mA, genel ~21 mA — teoride en verimli, pratikte değil | [T-14] |
| RP2040 | ~40 mA (ESP32'den ~10–20 mA az) | [T-18] |
| Pi Zero 2 W | ~100–180 mA @ 5 V (0,5–0,9 W) → 18650 ile < 1 gün | [T-16] |

### 4.4 "Güçlü işlemci" sorusu — nRF52840 Meshtastic'i kaldırır mı?

**Evet, kişisel cihaz rolü için.** Meshtastic, nRF52840 üzerinde geliştirilen iki birincil platformdan biridir (T-Echo, RAK4631, T114, T1000-E, MeshPocket, WisMesh Pocket hepsi nRF52840) [T-2]. İş yükü hesaplama değil, **radyo zamanlaması ve G/Ç** ağırlıklıdır. nRF52840'ın **sınırları** ve bu projede önemi:

| nRF52840 sınırı | Bu proje için önemi |
|---|---|
| NodeDB ~80 node (dolunca en eski/ilgisiz node düşer) [T-19] | ✅ Kabul edilebilir — CEP `CLIENT_MUTE`, yönlendirme yapmıyor. Kalabalık ağda "tüm node'ları görmek" CEP'in görevi değil. |
| Store & Forward sunucusu yok (PSRAM gerekir) | ✅ Bilinçli — mesaj biriktirme NOKTA'nın görevi (SGB NOK-5) |
| Dokunmatik MUI yok | ✅ Gerekmiyor — A-7 kapandı, arayüz e-paper + butonlar |
| WiFi yok | ✅ CEP'in WiFi'a ihtiyacı yok (captive portal NOKTA'da) |

> **Sonuç:** "Güçlü işlemci" ihtiyacı, **cihazın rolüne göre** okunmalı. Ağ yükünü taşıyan cihaz NOKTA'dır ve orada ESP32-S3 + PSRAM seçili kalır. Kişisel cihazda ek işlem gücü, pil ömründen ödenen bir bedeldir.

### 4.5 İzleme Listesi — nRF54L15

nRF54L15 (Cortex-M33 128 MHz, daha düşük aktif akım) Meshtastic'e Eylül 2026'da **deneysel** olarak girdi [T-17]. v1 için **seçilmedi** (olgunluk 1/5, hazır LoRa+nRF54 birleşik modül yok). **v2 revizyonunda (≥ 2027 Q2) yeniden değerlendirilecek.** Pin uyumlu değildir; geçiş yeni layout gerektirir.

---

## 5. Güç Bütçesi ve Güç Modları

### 5.1 Parça Bazında Akım Değerleri (3,3 V hat)

| Parça | Durum | Akım | Kaynak |
|---|---|---|---|
| SX1262 | RX (boosted gain, Meshtastic varsayılanı) | ~5,3 mA | SX1262 DS |
| SX1262 | TX @ +22 dBm | ~118 mA | SX1262 DS |
| SX1262 | Uyku (warm start) | ~0,6 µA | SX1262 DS |
| nRF52840 | Meshtastic boşta + BLE yayın (ortalama) | ~0,5–1,5 mA | [T-1] + tahmin |
| nRF52840 | System OFF (GPIO uyanma) | ~0,4 µA | nRF52840 PS |
| TPS63900 | Sükûnet | 75 nA | TPS63900 DS |
| MAX17048 | Aktif / hibernate | 23 µA / 4 µA | MAX17048 DS |
| DW01A + FS8205A | Sürekli | ~3 µA | DW01A DS |
| E-paper 2,9" | Tam yenileme (~2–3 s) | ~3–8 mA; statik 0 | [T-22] |
| u-blox MAX-M10S | Takip | ~25 mW ≈ 7,5 mA | u-blox DS |
| Quectel L76K | Takip | ~29–41 mA | Quectel DS |
| SHT40 / LSM6DS3TR-C | Düşük güç | ~0,4 µA / ~25 µA (ivmeölçer LP) | DS |

### 5.2 Güç Modları

| Mod | Tanım | LoRa | BLE | Ekran | GNSS | Beklenen ortalama | Firmware |
|---|---|---|---|---|---|---|---|
| **M0 — Normal** | Günlük kullanım, `CLIENT_MUTE` | RX sürekli | Açık | E-paper, olay başına yenileme | Kapalı | **~6–8 mA** | Upstream |
| **M1 — Tasarruf** | BLE kapalı, ekran yalnızca buton/mesajla | RX sürekli | Kapalı | Statik | Kapalı | **~5–6 mA** | Upstream (`is_power_saving`) |
| **M2 — Enkaz / Beacon** | Radyo uyur; 5 dk'da bir SOS TX; 30 sn'de bir bip; buton ile çıkış | Periyodik TX | Kapalı | Statik "ENKAZ MODU" | Son konum | **~0,8 mA** | **Özel modül** (SGB SOS modülüyle) |
| **M3 — Kapalı / Raf** | 3V3 hattı kesik, yalnızca güç butonu dinlenir | Kapalı | Kapalı | Son görüntü kalır | Kapalı | **≤ 10 µA** | Donanım (HW-MOD-5) |
| **M4 — Şarj** | USB/solar bağlı; sistem power-path'ten beslenir | M0 gibi | — | — | — | Şebekeden | Upstream |

> **M2 notu:** Meshtastic'te bu mod yoktur; SGB Aşama 2 (SOS modülü) kapsamında geliştirilecek. Donanımın bunu mümkün kılması için **HW-RF-11** (DIO1 → uyanma GPIO'su) ve **HW-MOD-3** zorunludur. M2'de alım yoktur — cihaz yalnızca "buradayım" der. Enkaz altındaki kişi için dinlemekten çok **duyulmak** önemlidir (SGB CEP-4).

### 5.3 Pil Ömrü Tahmini

**Varsayım:** 3000 mAh 18650 · buck-boost ile 3,0 V'a kadar kullanılabilir kapasite ≈ %90 → **2700 mAh** (oda sıcaklığı) · −10 °C'de ≈ %85 → **~2300 mAh** [L-28][L-29] · regülatör verimi ~%90, pil gerilimi ortalama 3,7 V ile birbirini dengeler (pil tarafı akım ≈ hat akımı).

| Senaryo | Ortalama akım | Oda sıcaklığı | −10 °C |
|---|---|---|---|
| M0 Normal (GPS kapalı) | 7 mA | **~16 gün** | ~13 gün |
| M0 + telefon bağlı, yoğun mesaj | 10 mA | ~11 gün | ~9 gün |
| M0 + GNSS sürekli (MAX-M10S) | 15 mA | ~7,5 gün | ~6 gün |
| M0 + GNSS sürekli (L76K) | 37 mA | ~3 gün | ~2,6 gün |
| M1 Tasarruf | 5,5 mA | ~20 gün | ~17 gün |
| M2 Enkaz/Beacon | 0,8 mA | ~140 gün (hedef) | ~120 gün |
| **Karşılaştırma: ESP32-S3 + TFT (Yol B)** | 50–130 mA | **~1–2 gün** | < 1–2 gün |
| **Karşılaştırma: LDO (3,45 V'ta kesme)** | 7 mA | ~14 gün | Soğukta TX'te brownout riski [T-4] |

> **SGB SYS-7 uyarısı:** Bu tablo **tahmindir, iddia değildir.** Topluluk ölçümleri nRF52 cihazlarında aynı sınıfta 4,5–9 mA arasında değişiyor [T-1][T-4]; fark çoğunlukla kart tasarımındaki kaçak akımlardan (bölücü, LED, anahtarlanmamış çevre birim) geliyor. Gereksinim olarak yazılan hedefler aşağıdadır; beyan edilecek rakamlar PPK2 ölçümlerinden gelecek.

**Gereksinim olarak hedefler (ölçümle doğrulanacak):**

| # | Hedef | Değer |
|---|---|---|
| HW-BAT-1 | M0 ortalama akım (GPS kapalı, BLE bağlı değil) | **≤ 8 mA** |
| HW-BAT-2 | M0'da 3000 mAh ile ömür | **≥ 14 gün** (SGB CEP-N2 asgarisi 72 saat — ~4,5 kat marj) |
| HW-BAT-3 | GNSS sürekli açıkken ömür | **≥ 72 saat** (CEP-N2 en kötü durumda da tutar) |
| HW-BAT-4 | M3 kapalı mod akımı | **≤ 10 µA** |

---

## 6. Alt Sistem Malzeme Seçimi

Her alt sistemde: aday tablosu → seçim → PCB tasarımcısı için notlar. Fiyatlar Eylül 2026, **tek adet, KDV/gümrük hariç, tahmini**; sipariş öncesi LCSC/Mouser/DigiKey'den teyit edilmelidir.

### 6.1 MCU + LoRa Modülü

| Seçenek | İçerik | 433 MHz | 868 MHz | TCXO | Fiyat | Artı | Eksi |
|---|---|:-:|:-:|:-:|---|---|---|
| **RAK4630 (L)/(H)** ⭐ | nRF52840 + SX1262, 15×23 mm, 44 pin | ✅ (L) | ✅ (H) | ✅ | ~$15–20 | **Aynı footprint iki band**; SWD + USB pinleri dışarıda; Meshtastic'te RAK4631 olarak birinci sınıf; 2,0–3,6 V besleme | BLE için **ayrı anten gerekir** (`RF_BT` pini); ikinci kaynak yok |
| Heltec HT-N5262M | nRF52840 + SX1262, 30×20 mm, 54 pin, 1,27 mm | ❌ | ✅ | ? | ~$15 | Ucuz, IPEX çıkışlı | **433 MHz sürümü yok** (470–510 / 863–870 / 902–928) — A-1 = 433 olursa kullanılamaz |
| Raytac MDBT50Q-1MV2 + Ebyte E22-400M22S / E22-900M22S | Ayrık MCU + radyo | ✅ (SX1268) | ✅ | ✅ | ~$8 + ~$6 | En ucuz; her parçanın ikinci kaynağı var; E22 PA'lı | İki modül arası SPI + **RXEN/TXEN** + TCXO 1,8 V ayarı sende; topluluk kararsızlık raporları var [T-10] |
| MDBT50Q + Heltec HT-RA62 / Seeed Wio-SX1262 | Ayrık | ❌/⚠️ | ✅ | ✅ | ~$8 + ~$5 | Dahili RF anahtar (RXEN gerekmez) [T-8] | 433 seçeneği sınırlı |

**Seçim: RAK4630.** Sipariş kodu band kararına göre: **A-1 = 433 MHz → RAK4630(L)**, **A-1 = 868 MHz → RAK4630(H)**. PCB aynı kalır, yalnızca anten ve eşleme değerleri değişir. **Yedek yol:** MDBT50Q-1MV2 + E22-xxxM22S (v2 maliyet düşürme; `nrf52_promicro_diy_tcxo` varyantı referans alınır [T-8]).

**PCB notları:**
- `RF_LoRa` → π-ağı → CPWG 50 Ω → SMA. `RF_BT` → π-ağı → 2,4 GHz çip anten (ör. Johanson 2450AT18A100, kart köşesinde, üreticinin keep-out'uyla).
- `VBUS`, `USB+`, `USB−` → USB-C (ESD dizisinden geçerek). nRF52840 yerel USB'si UF2 bootloader ve seri konsol sağlar.
- `SWDIO`, `SWCLK`, `nRESET` → TC2030 pedi.
- Modül 2,0–3,6 V kabul eder; **pili doğrudan bağlama** (4,2 V).
- 32,768 kHz kristalin modül içinde olup olmadığı RAK'tan teyit edilmeli; değilse P0.00/P0.01'e 32,768 kHz kristal + 2×12 pF (HW-MCU-7).

### 6.2 Anten

| Kullanım | Seçim | Not |
|---|---|---|
| LoRa konnektör | **SMA dişi, bulkhead, panel geçişli** (PCB kenar montajlı + pigtail veya doğrudan panel tipi) | O-ring'li; kasa duvarından geçer |
| LoRa anten (433) | ½λ veya ¼λ whip, ~17 cm, 2–3 dBi, katlanabilir | 433'te fiziksel boyut uzun — kasa tasarımında hesaba katılmalı |
| LoRa anten (868) | ½λ dipol-tipi whip, ~17–20 cm, 2–3 dBi | "5–10 dBi" pazarlama antenlerinden kaçınılmalı; el cihazında yönlülük dezavantajdır |
| BLE | 2,4 GHz SMD çip anten | Keep-out kritik |
| GNSS | 18×18 veya 25×25 mm pasif seramik patch (MAX-M10S'e UFL veya doğrudan) | Gökyüzüne bakan yüz; e-paper'ın altına **koyulmamalı** |

> **Gövde etkisi:** El/vücut yakınındaki antenler frekans kayması ve ciddi verim kaybı yaşar — 868 MHz giyilebilir bir LoRa anteninde verim %14,5'e kadar düşmüş [L-25]. Bu yüzden: (a) dahili anten değil harici whip, (b) π-eşleme footprint'i (HW-RF-6), (c) menzil ölçümleri **elde tutarak** da yapılmalı.

### 6.3 Güç Zinciri

```
USB-C 5V ──► TVS + USBLC6 ──► BQ24074 (power-path, 1 A, NTC) ──► VSYS
Solar/DC ──► (opsiyonel, JST) ─┘         │
                                          └──► BAT ◄── P-MOSFET ters kutup ◄── DW01A+FS8205A ◄── 18650
VSYS ──► TPS63900 (buck-boost, 3,3 V, EN = güç latch) ──► 3V3 ──┬──► RAK4630
                                                               ├──► TPS22917 ──► 3V3_GNSS
                                                               ├──► TPS22917 ──► 3V3_DISP
                                                               └──► TPS22917 ──► 3V3_SNS (I²C pull-up'lar dâhil)
BAT ──► MAX17048 (I²C, yakıt göstergesi)
```

| Fonksiyon | Seçim | Alternatif | Neden bu |
|---|---|---|---|
| Şarj + power-path | **TI BQ24074** (~$2) | BQ25185 (daha küçük, ship-mode'suz), MCP73871 (solar dostu) | Kanıtlanmış (Adafruit solar şarj kartları), DPPM ile solar paneli çökertmez, 28 V'a kadar giriş dayanımı, TS pini ile NTC |
| 3,3 V regülatör | **TI TPS63900** (~$1,5, 75 nA Iq, 400 mA) | TPS63802 (2 A, 11 µA Iq) · TPS7A0233 LDO (25 nA) — yalnızca yedek | Pili 3,0 V'a kadar kullanır (+%10–15 kapasite), soğukta TX brownout'unu önler [T-4]; 400 mA, ~170 mA tepe yüke yeterli |
| Yük anahtarları | **TI TPS22917** (×3, ~$0,4) | SiP32431 | 1 µA altı kapalı kaçak; kontrollü yükselme (GNSS/e-paper kapasitif ani akımı) |
| Hücre koruma | **DW01A + FS8205A** (~$0,3) | Korumalı hücre (ama boy 18650'den uzun olur, tutucuya sığmayabilir) | Hücre tipinden bağımsız koruma — [L-29] "host'tan bağımsız koruma" önerisi |
| Ters kutup | P-MOSFET (ör. DMG2305UX) | — | Değiştirilebilir pil = ters takma riski |
| Yakıt göstergesi | **MAX17048** (~$2) | Anahtarlı direnç bölücü (MOSFET ile kesilir) | Meshtastic destekli; OCV tablosu kalibrasyonuna gerek bırakmaz; soğukta daha doğru |
| USB ESD | USBLC6-2SC6 + VBUS'ta SMF5.0A TVS | TPD2E2U06 | — |
| Güç butonu latch | nRF GPIO + TPS63900 EN + buton (diyot-OR) | Özel buton kontrolörü (ör. MAX16150) | M3 modunda ≤ 10 µA (HW-PWR-9) |

> ⚠️ **Buck-boost RF riski:** Anahtarlamalı regülatör LoRa alıcı hassasiyetini düşürebilir. Azaltma: HW-PCB-4 layout kuralı + ilk turda **alıcı gürültü tabanı ölçümü** (aynı modül, LDO beslemeli referansla karşılaştırma; bkz. Test T-06). Sorun çıkarsa TPS7A0230 (3,0 V LDO) ile yeniden besleme — **3,0 V** seçimi tüm parçaların (RAK4630 ≥ 2,0 V, SSD1680 ≥ 2,2 V, MAX-M10S ≥ 2,7 V) çalışma aralığında kalır ve dropout sorununu 3,15 V'a indirir.

### 6.4 Pil

| Seçenek | Kapasite | −10 °C davranış | Not |
|---|---|---|---|
| **18650 NMC, 3000–3500 mAh** (Samsung 35E, LG MJ1, Panasonic NCR18650B, Molicel P30B) ⭐ | 3000–3500 mAh | −10 °C'de 3,3 A'de ~%85 kapasite (NCR18650B) [L-28] | Seçilen. Tüm dünyada ve Türkiye'de bulunur; powerbank'lerden sökülebilir |
| 18650 LiFePO4 | 1500–1800 mAh | Soğukta daha kararlı [L-29] | Kapasite yarıya iner; 3,2 V nominal — DW01A eşikleri LFP'ye uymaz (farklı koruma IC gerekir) |
| Yapıştırma LiPo | Değişken | — | ❌ Değiştirilemez (HW-PWR-1 ihlal) |

**Tutucu:** Keystone 1042 (SMD, 18650) veya Keystone 1043 — **düşmede pili tutacak** kilitli tip + kasada ek köpük (HW-ENV-4).

### 6.5 Ekran

| Seçenek | Güneşte | Statik güç | nRF52 Meshtastic sürücüsü | Dokunmatik | Fiyat | Değerlendirme |
|---|---|---|---|---|---|---|
| **E-paper 2,9" 296×128, SSD1680** (Good Display GDEY029T94 / WeAct 2.9") ⭐ | ✅ Mükemmel | **0** | ✅ InkHUD (WeAct 2,9" resmî DIY listesinde) [T-6] | ❌ | ~$8–12 | **Seçilen.** Cihaz kapanınca son mesaj/konum ekranda kalır |
| E-paper 2,13" 250×122, SSD1680 | ✅ | 0 | ✅ InkHUD | ❌ | ~$6–9 | Daha küçük kasa gerekirse |
| E-paper + kapasitif dokunmatik (ör. GDEY027T91 + FT6336) | ✅ | 0 | ⚠️ Sürücü var, **dokunmatik UI yok** | ✅ | ~$15–20 | Elendi (A-7 kapandı) |
| TFT 1,14" ST7789 (T114 tarzı) | ⚠️ | Arka ışık 10–30 mA | ✅ BaseUI | ❌ | ~$3 | Güneşte zayıf; P1'e aykırı |
| OLED 1,3" SH1106 | ⚠️ | 5–20 mA | ✅ | ❌ | ~$3 | Sadece temel CEP için ucuz seçenek |
| Transflektif TFT + dokunmatik | ✅ | Arka ışıksız düşük | ❌ nRF52'de UI yok | ✅ | $20+ | Yol B (ESP32-S3) ile anlamlı |

**PCB notları:** 24 pin FPC (0,5 mm) konnektör; e-paper için gerekli boost devresi (SSD1680 referans tasarımı: indüktör 10 µH, MOSFET, 3 diyot, 9–10 kapasitör) **ya kartta** (çıplak panel) **ya da** hazır sürücü kartlı modülde (WeAct) — ilk tur için **hazır sürücü kartlı modül + 8 pin konnektör** önerilir (daha az risk), v2'de çıplak panel.

### 6.6 Butonlar ve Kullanıcı Bildirimleri

| Fonksiyon | Seçim | Not |
|---|---|---|
| 5 yön + Seç | **Alps SKRHABE010** (5 yönlü joystick, SMD) veya 5 × 6×6 mm taktil | Joystick daha az kasa deliği = daha kolay sızdırmazlık |
| Geri / Güç | 6×6 mm taktil, silikon başlıklı | Güç butonu latch devresine de bağlı |
| **SOS** | 12×12 mm taktil + **menteşeli kapak** veya gömme + 3 sn basılı tutma | Kırmızı, dokunarak bulunabilir şekil |
| Sesli uyarıcı | **Piezo transdüser, harici sürücülü** (12–14 mm, rezonans ~4 kHz, rezonansta ~85–95 dB sınıfı) + MOSFET | Manyetik buzzer'a göre aynı ses için daha düşük akım; rezonans frekansında PWM |
| Durum LED'i | **Ayrık RGB LED** (ortak anot, 3 × seri direnç, düşük akım 1–2 mA) + ışık borusu | ❌ **WS2812B kullanılmayacak** — her biri sönükken ~0,6–1 mA çeker (v1.1 hatası) |
| Titreşim | Coin motor 10 mm + N-MOSFET + flyback diyot | Yalnızca C+/T |
| ESD | Buton hatlarında 100 Ω seri + 100 nF (veya TVS dizisi) | HW-UI-8 |

> **Firmware notu:** InkHUD tek butonla tam kullanılabilir (kısa bas = sonraki ekran, uzun bas = menü) [T-6]. 5 yönlü girişin InkHUD'a bağlanması varyant tanımıyla yapılır; tüm butonlar nRF52840'ın **SENSE** özellikli GPIO'larına bağlanmalı (System OFF'tan uyandırabilsin).

### 6.7 Sensörler

| Fonksiyon | Seçim | Alternatif | Meshtastic desteği | Neden |
|---|---|---|---|---|
| Sıcaklık / nem | **Sensirion SHT40** (~$1,5) | BME280 (basınç da ister isen) | ✅ SHT4X, BME280 [T-12] | ±0,2 °C, ~0,4 µA ortalama; BME280'e göre daha doğru nem ve daha az öz-ısınma |
| 6 eksen IMU | **ST LSM6DS3TR-C** (~$1,5) | LIS3DH (yalnız ivmeölçer, 2 µA) | ✅ LSM6DS3, LIS3DH [T-12] | Düşme algılamada jiroskop yanlış pozitifi azaltır [L-27]; donanımsal "wake-up" ve "free-fall" kesmesi var |
| ~~BMI160~~ | — | — | ❌ Meshtastic listesinde **yok** | CEP-T v1.1 önerisi **düzeltildi** |

**Düşme algılama için donanım gereği:** IMU'nun INT1 → nRF52840 SENSE GPIO (uyanma), INT2 → ayrı GPIO (serbest düşme olayı). Eşik yöntemi literatürde gövdeye takılı sensörde yüksek duyarlılık/özgüllük veriyor [L-26][L-27], ancak **elde/çantada taşınan** cihaz için doğrudan karşılığı yok — kalibrasyon projenin özgün katkısı olacak (CEP-T CT-A3).

### 6.8 GNSS

| Seçenek | Güç (takip) | Takımyıldız | Fiyat | Değerlendirme |
|---|---|---|---|---|
| **u-blox MAX-M10S** ⭐ | **~25 mW** (~7,5 mA) | 4 GNSS eşzamanlı | ~$10–12 | P1 için en iyi; Meshtastic u-blox protokolünü destekler |
| Quectel L76K | ~29–41 mA | GPS+BeiDou+GLONASS | ~$4–6 | T-Echo ve pek çok ticari cihazda; güç 4–5 kat fazla |
| ATGM336H | ~25–30 mA | GPS+BeiDou | ~$3 | Ucuz, yaygın |
| ~~u-blox NEO-6M~~ | ~45 mA | Yalnız GPS | — | **Eski nesil**, CEP-T v1.1 önerisi düzeltildi (yalnızca breadboard testi için) |

**PCB notları:** MAX-M10S'e 3V3_GNSS (yük anahtarlı) + **V_BCKP** için ayrı küçük besleme (sıcak başlatma, fix süresini dakikalardan saniyelere indirir; akımı onlarca µA mertebesinde — veri sayfasından teyit edilip M3 bütçesine eklenecek, istenirse kesilebilir); RF girişine 50 Ω hat + LNA'sız pasif anten veya harici aktif anten için bias-T footprint'i.

### 6.9 Konnektörler

| Konnektör | Tip | Not |
|---|---|---|
| USB-C | 16 pin, orta montaj, **su geçirmez sürüm tercih** (ör. IPX7 sınıfı USB-C) | Kasa tapası yerine konnektörün kendisi sızdırmaz |
| SMA | Bulkhead dişi | HW-RF-5 |
| Ekran | FPC 24 pin 0,5 mm veya 8 pin 2,54 mm (hazır modül) | HW-DSP-4 |
| Harici GNSS | JST-SH 4 pin | HW-SNS-4 |
| Solar | JST-PH 2 pin | HW-PWR-14 |
| SWD | TC2030-NL ped | HW-DBG-1 |

---

## 7. Topluluk Projelerinden Çıkarılan Dersler → Gereksinime Dönüşümü

| Ders | Kaynak | Bu belgedeki karşılığı |
|---|---|---|
| Stok ayarlı nRF52 node RX'te 7,5 mA çekerken, preamble ve OCV düzeltmeleriyle **4,5 mA**'e indi (3000 mAh ile 16 → 28 gün) | FARO Node [T-4] | HW-PWR-12, HW-BAT-1 |
| LDO, pil ~3,1 V'ta dropout'a girdi; TX tepe akımı soğukta **brownout/bootloop** yaptı | FARO Node [T-4] | K-5 (buck-boost), HW-MCU-9 |
| T114 "kapalıyken" bile ~8 mA çekiyor (ADC/çevre birimleri beslemede kalıyor) | Meshtastic issue #8801, disc. #9026 [T-11] | HW-PWR-8, HW-MOD-5 |
| GPS açık olunca T114 ömrü 220 → 119 saate düştü | 20 cihaz testi [T-3] | HW-SNS-1, MAX-M10S seçimi |
| Pro Micro kartlarında bootloader eksik/eski çıkabiliyor; lehimlemeden önce test et | fakeTec PCB [T-9] | HW-MCU-6 (SWD zorunlu) |
| E22-400M22S + nRF52840: TCXO/RF anahtar ayarı yanlışsa BUSY hattı takılı kalıyor | Meshtastic issue #6692 [T-10] | K-4 (birleşik modül), HW-RF-3 |
| Açıktaki RF anahtar pini HIGH kalırsa +55 µA | Hackaday Meshtastic DIY [T-20] | Varyant tanımında RF anahtar pin durumu doğrulanacak |
| ESP32 "power saving" modu telefonla bağlantıyı koparıyor, mobil cihaza uygun değil | Meshtastic issue #6660 [T-7] | K-2 |
| Ikoka Nano: 2 katman, 0603+ pasif, JLCPCB DRC — tek kişilik tasarımda yeterli, ama ekran/buton yok | [T-21] | HW-PCB-7 (el lehimi), 4 katman tercih (RF + switcher) |

---

## 8. Malzeme Listesi (BOM) — Tahmini

| Kalem | Parça | CEP (10 ad.) | CEP (100 ad.) | CEP+/T (10 ad.) |
|---|---|---:|---:|---:|
| MCU + LoRa | RAK4630 (L/H) | $18,00 | $14,00 | $18,00 |
| BLE çip anten + eşleme | 2450AT18A100 + 0402 | $0,60 | $0,40 | $0,60 |
| SMA bulkhead + LoRa anten | — | $4,00 | $2,80 | $4,00 |
| Şarj + power-path | BQ24074 + NTC | $2,20 | $1,60 | $2,20 |
| Regülatör | TPS63900 + indüktör | $2,00 | $1,40 | $2,00 |
| Yük anahtarları | TPS22917 ×3 | $0,60 | $0,45 | $1,20 |
| Koruma | DW01A + FS8205A + P-MOSFET | $0,60 | $0,40 | $0,60 |
| Yakıt göstergesi | MAX17048 | $2,00 | $1,50 | $2,00 |
| USB-C (sızdırmaz) + ESD + TVS | — | $2,50 | $1,80 | $2,50 |
| 18650 tutucu | Keystone 1042 | $1,50 | $1,10 | $1,50 |
| Butonlar | SOS + güç (+ joystick C+/T) | $1,20 | $0,80 | $3,00 |
| Piezo + sürücü, RGB LED | — | $1,50 | $1,00 | $1,50 |
| Titreşim motoru | — | — | — | $0,80 |
| Pasifler, konnektörler | — | $2,00 | $1,30 | $3,00 |
| PCB 4 katman ENIG | ~90×55 mm | $4,00 | $1,80 | $4,00 |
| 18650 hücre | 3000–3500 mAh | $4,00 | $3,00 | $4,00 |
| E-paper 2,9" | GDEY029T94 / WeAct | — | — | $10,00 |
| GNSS | MAX-M10S + patch | — | — | $13,00 |
| Sensörler | SHT40 + LSM6DS3TR-C | — | — | $3,00 |
| **Elektronik toplam (kasa hariç)** | | **≈ $47** | **≈ $33** | **≈ $81** |
| Kasa (IP54 hazır kutu / baskı) | | $5–8 | $3–4 | $6–9 |

> ⚠️ **Düşük adette özel PCB, hazır karttan pahalıdır.** 10 adetlik turda CEP elektroniği ≈ $47 iken hazır bir nRF52840 kartı (ör. RAK4631 + taban) benzer fiyattadır. Özel kartın getirisi maliyet değil: **IP54 sızdırmazlığa uygun konnektör yerleşimi, değiştirilebilir 18650, anahtarlı güç hatları ile ≤ 10 µA kapalı mod, Enkaz modu için donanım** — bunlar hazır kartlara sonradan eklenemez. Bu yüzden iki paralel yol korunur: *hazır kartla bugün yapılabilen node* (geliştirme ve pilot) + *AfetMesh özel kartı* (olgunlaştıkça).

> **SGB CEP-N1 ($25–40) ile karşılaştırma:** 100 adette CEP ≈ $36–37 (kasa dâhil) → **bandın içinde ama üst yarısında.** Artışın nedeni v1.1'e göre eklenen zorunlu kalemler: buck-boost, yük anahtarları, ters kutup koruması, BLE anteni, sızdırmaz USB-C. **Hiçbiri pil ömrü veya saha güvenilirliğinden ödün vermeden çıkarılamaz.** CEP+/CEP-T ~$87–90 ile SGB'deki ~$59 hedefini **aşıyor** — başlıca neden MAX-M10S ve e-paper; L76K'ya geçiş ~$8 düşürür ama GNSS açık pil ömrünü 7,5 → 3 güne indirir. **SGB'ye işlendi (CEP+ hedefi revize edildi).**

---

## 9. Doğrulama ve Test Planı

| # | Aşama | Test | Başarı kriteri | Gereksinim |
|---|---|---|---|---|
| T-01 | Kart | Güç açılış, hat gerilimleri | 3V3 ±%3, kısa devre yok | HW-PWR-6 |
| T-02 | Kart | SWD ile bootloader + UF2 yükleme | Başarılı | HW-MCU-5/6 |
| T-03 | Güç | **M3 kapalı mod akımı** (µA metre / PPK2) | ≤ 10 µA | HW-PWR-9, HW-BAT-4 |
| T-04 | Güç | **M0 ortalama akım**, 24 saat PPK2 kaydı | ≤ 8 mA | HW-BAT-1 |
| T-05 | Güç | Pil 3,0 V'a kadar deşarj, TX sırasında brownout | Reset yok | HW-PWR-6/7 |
| T-06 | RF | **Alıcı gürültü tabanı**: buck-boost vs LDO beslemeli karşılaştırma | Fark ≤ 2 dB | HW-PCB-4 |
| T-07 | RF | Bilinen Meshtastic node ile mesajlaşma (TA Mesh dâhil) | Çift yönlü başarılı | HW-MCU-1, SGB TST-5 |
| T-08 | RF | Çıkış gücü (SDR / spektrum analizörü) | Bölge limiti içinde | HW-RF-9 |
| T-09 | RF | Menzil: açık alan + şehir içi + **elde tutarak** | Ölçülen değer kaydedilir (SYS-7) | HW-RF-5/6 |
| T-10 | BLE | Telefon eşleşme, 10 m iç mekân | Kararlı bağlantı | HW-RF-8 |
| T-11 | Güç | Tam deşarj ömür testi (M0, GPS kapalı) | ≥ 14 gün | HW-BAT-2 |
| T-12 | Güç | GNSS sürekli açık ömür | ≥ 72 saat | HW-BAT-3 |
| T-13 | Güç | Raf testi: kapalı 30 gün, şarj kaybı ölçülür, 6 aya ekstrapole | ≥ %75 kalır | HW-PWR-10 |
| T-14 | Termal | −10 °C'de 12 saat çalışma + TX + e-paper yenileme | Çalışır, ekran yenilenir | HW-ENV-1, HW-DSP-6 |
| T-15 | Termal | Şarj 0 °C altında / 45 °C üstünde | Şarj durur | HW-PWR-5 |
| T-16 | Güvenlik | Pili ters takma | Hasar yok, cihaz açılmaz | HW-PWR-4 |
| T-17 | UI | Piezo ses basıncı 10 cm | ≥ 85 dB(A) | HW-UI-5 |
| T-18 | UI | Tüm menüler yalnızca butonla (eldivenle, ıslak) | Erişim kaybı yok | HW-UI-2/4 |
| T-19 | Sensör | Düşme algılama: gerçek düşme / 24 saat taşıma yanlış pozitif | ≥ %80 / < %5 | HW-SNS-7, CEP-T CT-N8 |
| T-20 | Mekanik | 1,5 m × 6 yön düşme (kasalı) | Çalışır, pil yerinde | HW-ENV-4 |

---

## 10. Riskler

| # | Risk | Etki | Azaltma |
|---|---|---|---|
| R-1 | Buck-boost anahtarlama gürültüsü LoRa hassasiyetini düşürür | Orta | HW-PCB-4 layout; T-06; 3,0 V LDO geri dönüş yolu (Bölüm 6.3) |
| R-2 | RAK4630 tek kaynak; stok/fiyat dalgalanması | Orta | Ayrık MDBT50Q + E22 yolu v2 için belgelendi |
| R-3 | A-1 bandı 433 çıkarsa 868'e göre ERP limiti çok düşük (~10 mW) | Yüksek | Menzil beklentisi SYS-7 ile ölçülerek beyan edilir; SGB A-1 |
| ~~R-4~~ | ~~Dokunmatik gereksinimi pil hedefini tutturmaz~~ | — | **Kapandı** — dokunmatik zorunlu değil (A-7) |
| R-5 | SWD pedi unutulursa kartlar programlanamaz | Yüksek | Şematik kontrol listesi (HW-MCU-6) |
| R-6 | CC dirençleri unutulursa USB-C şarj etmez | Orta | HW-PWR-13 |
| R-7 | E-paper −10 °C'de yavaş/yetersiz yenileme | Orta | T-14; yenileme dalga formu (LUT) sıcaklığa göre seçimi — SSD1680 dahili sıcaklık sensörü |
| R-8 | nRF54L15'in hızlı olgunlaşması tasarımı eskitir | Düşük | İzleme listesi (Bölüm 4.5); v2'de değerlendirme |
| R-9 | Enkaz/Beacon modu (M2) firmware geliştirmesi gecikir | Orta | Donanım hazır (HW-RF-11); SGB Aşama 2 ile birlikte |
| R-10 | İthalat vergisi BOM'u %30–60 artırır | Orta | Toplu sipariş; SGB risk kaydı |

---

## 11. Açık Kararlar (bu belgeden SGB'ye taşınanlar)

| # | Karar | Seçenekler | Önerilen | Bekleyen |
|---|---|---|---|---|
| **A-1** | Frekans bandı | 433 / 868 MHz (bina içi ve enkaz verisi: [L-9][L-10][L-18]) | — (SGB'de açık) | **Artık PCB'yi bloke etmiyor** (K-4) |
| **A-4** (revize) | Modül | RAK4630 (L/H) · MDBT50Q + E22 | **RAK4630** | Güncel fiyat/stok teyidi |
| ~~**A-7**~~ ✅ | CEP-T dokunmatik mi, e-paper + buton mu? | Yol A / Yol B | **Yol A — KAPANDI** | — |
| **A-8** (yeni) | 3,3 V buck-boost mu, 3,0 V LDO mu? | TPS63900 / TPS7A0230 | **TPS63900** | T-06 sonucu |

---

## 12. Sonraki Adımlar (PCB tasarımcısı için sıra)

1. ~~A-7 kararı~~ ✅ Verildi: dokunmatik yok, e-paper + butonlar — bu BOM geçerli.
2. **Breadboard prototipi kur ve ölç:** [`05_Cihaz_Tasarimi/CEP_Ilk_Prototip_Malzeme_Listesi.md`](../05_Cihaz_Tasarimi/CEP_Ilk_Prototip_Malzeme_Listesi.md) — nRF52840 Pro Micro + SX1262 + 2,9" e-paper + butonlar (opsiyonel referans: RAK4631 + RAK19007). Devreye alma B-1…B-11; PPK2 ile ortalama akımı ölç. PCB'ye başlamadan önce `PRIVATE_HW` firmware varyantı hazır olsun.
3. **Kasa / kutu seç** — iç ölçüler PCB boyutunu belirler (HW-PCB-2).
4. **Şematik** (KiCad): Bölüm 3 tablosunu **kontrol listesi** olarak kullan; her HW- maddesinin şematikte karşılığını işaretle.
5. **Layout:** önce RF (modül, SMA, BLE anten, keep-out), sonra güç (buck-boost döngüsü), en son dijital.
6. **İlk tur 5 adet** → T-01…T-06 → düzeltmeler → ikinci tur.
7. Ölçüm sonuçlarını bu belgeye ve SGB'ye işle (SYS-7).

---

## Kaynaklar

Birleşik kaynakça ve indirilen makale PDF'leri: **[`04_Dokumanlar/Kaynakca.md`](../04_Dokumanlar/Kaynakca.md)** · [`04_Dokumanlar/Kaynaklar/Makaleler/`](../04_Dokumanlar/Kaynaklar/Makaleler/) (18 PDF).

Tüm akademik makaleler (L-1…L-30), topluluk kaynakları (T-1…T-26) ve ticari cihaz verileri (C-1…C-14) için: **[`04_Dokumanlar/Literatur_ve_Topluluk_Arastirmasi.md`](../04_Dokumanlar/Literatur_ve_Topluluk_Arastirmasi.md)**

**Veri sayfaları (bu belgede doğrudan kullanılan):** nRF52840 Product Specification v1.11 (Nordic) · SX1261/2 Data Sheet Rev 1.2 (Semtech) · RAK4630 Datasheet (RAKwireless) · HT-N5262M (Heltec) · TPS63900, BQ24074, TPS22917 (TI) · MAX17048 (Analog Devices) · MAX-M10S Data Sheet UBX-20035208 (u-blox) · L76K Hardware Design V1.0 (Quectel) · SHT4x (Sensirion) · LSM6DS3TR-C (ST)

---

## Değişiklik Geçmişi

| Sürüm | Tarih | Değişiklik |
|---|---|---|
| 1.0 | — | İlk donanım raporu |
| 1.1 | 9 Ağu 2026 | SGB v2.0 hizalaması (NOKTA kapsam dışı, A-1 açık) |
| **2.1** | **24 Eyl 2026** | A-7 kapandı (dokunmatik zorunlu değil): Yol A kesinleşti; HW-DSP-7 ve R-4 kaldırıldı. Literatür raporu `04_Dokumanlar/`'a taşındı; prototip BOM ve birleşik kaynakçaya bağlantı verildi |
| **2.0** | **24 Eyl 2026** | Tamamen yeniden yazıldı: numaralı gereksinim tablosu (HW-MCU/RF/PWR/MOD/UI/DSP/SNS/DBG/PCB/ENV/BAT), 7 adaylı işlemci puanlaması, ölçüme dayalı güç bütçesi ve 5 güç modu, alt sistem malzeme seçimi, topluluk dersleri. **Düzeltmeler:** WS2812B → ayrık RGB LED; TPS7A0233 LDO → TPS63900 buck-boost; HT-N5262M → RAK4630 (433 MHz desteği); BME280 → SHT40 (BME280 alternatif); LIS3DH → LSM6DS3TR-C; ATGM336H → MAX-M10S. Kaynaklar ayrı belgeye taşındı. |
