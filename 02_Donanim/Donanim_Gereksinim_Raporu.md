# AfetMesh — Donanım Gereksinim Raporu

**Doküman:** HW-REQ-001
**Sürüm:** 1.0
**Tarih:** 2026-08-08
**Kapsam:** Özel PCB tasarımı, komponent seçimi, mekanik/IP54 gereksinimleri
**Durum:** Taslak — komponent siparişi öncesi gözden geçirilecek

---

## 1. Yönetici Özeti — Üç Temel Karar

Bu rapor üç kararı gerekçeleriyle birlikte önerir. Diğer her şey bu kararlardan türer.

| # | Karar | Öneri | Gerekçe |
|---|---|---|---|
| **K-1** | Kaç model tasarlanacak? | **Tek PCB, iki montaj varyantı** | İki ayrı kart tasarlamak; iki layout, iki firmware varyantı, iki kasa kalıbı ve iki stok kalemi demek. Tek kişilik bir ekip için asıl maliyet parça değil, **tasarım turu**dur. |
| **K-2** | Hangi işlemci/radyo? | **nRF52840 + SX1262** | Pil ömrü NFR-4'ün (72 saat) ana belirleyicisi. nRF52840 uyku akımı ~2 µA; ESP32-S3'te bu değer katlarca yüksek ve saha ölçümleri nRF52 kartlarda **günler–bir hafta**, ESP32 kartlarda **onlarca saat** pil ömrü gösteriyor. |
| **K-3** | Radyoyu nasıl tasarlayacağız? | **Hazır sertifikalı modül kullan, çıplak çip kullanma** | Çıplak SX1262; empedans kontrollü RF layout, eşleme devresi, harmonik filtre ve sıfırdan CE/RED test süreci demek. Sertifikalı modül bu riskin tamamını ortadan kaldırır. |

### K-1 nasıl çalışır: tek kart, iki varyant

Ekran, kartın üzerine **bir konektörle** bağlanır ve temel modelde o konektör boş kalır. Aynı PCB, farklı montaj listesiyle iki ürün olur:

| | **AfetMesh Node** (Temel) | **AfetMesh Node+** (Gelişmiş) |
|---|---|---|
| PCB | Aynı kart | Aynı kart |
| Ekran | Yok veya küçük OLED | E-ink ekran |
| GPS | Takılmaz (DNP) | Takılır |
| Sensörler | Takılmaz | Sıcaklık/nem + ivmeölçer |
| Kasa | Kapalı kapak | Pencereli kapak |
| Hedef kullanıcı | Evde/çantada bulundurulan yaygın node | Saha ekibi, kamu node'u |

Kasa ailesi ortaktır; yalnızca **kapak** değişir. Bu, tek bir kalıp gövde + iki kapak ile iki ürün çıkarmanı sağlar.

---

## 2. Dürüst Maliyet Uyarısı (Önce Bunu Oku)

> ⚠️ **Özel PCB, düşük adetlerde hazır karttan pahalıya gelir.** 10 adetlik bir üretimde AfetMesh temel varyantı yaklaşık **$32–38**'e mal olur; Heltec WiFi LoRa 32 V3 ise hazır olarak **$20–30**. Özel kart, ancak **100+ adette** ve kasa/sızdırmazlık entegrasyonu hesaba katıldığında ekonomik olarak öne geçer.

Özel PCB'nin gerçek getirisi maliyet değil, şunlardır:

- **IP54 sızdırmazlık**, hazır geliştirme kartlarına sonradan eklenemez; konektör yerleşimi, buton ve anten geçişleri kasayla birlikte tasarlanmalıdır
- Afet senaryosunun ihtiyaç duymadığı parçalar (WiFi, gereksiz konektörler) taşınmaz
- Ölçeklenebilir üretim ve üzerinde tam kontrol

**Bu yüzden iki paralel yol önerilir:** Depoda hem *"hazır kartla bugün yapabileceğin node"* rehberi (Heltec/RAK tabanlı), hem de *"AfetMesh özel kartı"* birlikte yaşasın. Birincisi projeyi hemen kullanılabilir kılar, ikincisi olgunlaştıkça devreye girer.

---

## 3. Birlikte Çalışabilirlik Gereksinimleri (Kritik)

Cihazın sahadaki mevcut Meshtastic cihazlarıyla haberleşebilmesi için aşağıdakiler **zorunludur**. Bunlar donanım seçimini doğrudan kısıtlar.

| # | Gereksinim | Detay |
|---|---|---|
| HW-INT-1 | Meshtastic'in desteklediği bir MCU mimarisi kullanılmalı | nRF52840, ESP32/S3 veya RP2040. Özel/desteklenmeyen MCU birlikte çalışabilirliği kırar. |
| HW-INT-2 | Meshtastic'in desteklediği bir LoRa transceiver kullanılmalı | **SX1262 önerilir** (SX1276/78 ve LR1121 de destekli). |
| HW-INT-3 | Radyo parametreleri ağ ile aynı olmalı | Bölge `EU_868`, modem preset `LONG_FAST` (varsayılan), aynı frekans slotu. Farklı preset = ağı hiç görmez. |
| HW-INT-4 | TCXO kullanılıyorsa firmware'de doğru tanımlanmalı | `SX126X_DIO3_TCXO_VOLTAGE`. Yanlış tanım, sessiz frekans kaymasına ve tek yönlü iletişime yol açar. |
| HW-INT-5 | RF switch kontrol pinleri firmware'e bildirilmeli | PA'lı modüllerde (E22 vb.) `SX126X_RXEN` / `SX126X_TXEN` tanımlanmazsa cihaz alır ama gönderemez. |
| HW-INT-6 | Firmware'e board variant tanımı eklenmeli | `variants/` altında yeni klasör, `variant.h` + `platformio.ini`. Donanım modeli olarak **`PRIVATE_HW`** kullanılır. |
| HW-INT-7 | Antenin empedansı ve bandı doğru olmalı | 868 MHz, 50 Ω. Yanlış/eksik anten hem menzili öldürür hem de çıkış katını yakabilir. |

> **Önemli süreç notu:** Meshtastic'in resmi `HardwareModel` enum listesine yeni bir donanım numarası eklenmesi, yalnızca **Meshtastic Backer/Partner** statüsündeki üreticilere açıktır. Kendi kartın için doğru ve desteklenen yol `PRIVATE_HW` tanımıyla derlemektir; bu, ağ üzerindeki birlikte çalışabilirliği **etkilemez** — cihaz diğer node'larla sorunsuz haberleşir, yalnızca arayüzlerde jenerik bir donanım adıyla görünür.

---

## 4. Sistem Blok Şeması

```
                    ┌──────────── 868 MHz SMA (O-ring geçiş) ─────────────┐
                    │                                                     │
        ┌───────────┴────────────┐                                        │
        │  nRF52840 + SX1262     │◄──── SWD (bootloader) ──── Tag-Connect │
        │  birleşik modül        │                                        │
        │  (BLE 5 + LoRa)        │◄──── SPI/I2C ────┐                     │
        └───────┬────────────────┘                  │                     │
                │                          ┌────────┴────────┐            │
   ┌────────────┴───────────┐              │ Ekran konektörü │ (varyant B)│
   │ Butonlar (güç, SOS)    │              │ OLED / E-ink    │            │
   │ LED + buzzer           │              └─────────────────┘            │
   └────────────────────────┘              ┌─────────────────┐            │
                                           │ GPS (güç anahtarlı)│ (var. B)│
   ┌─────────────── GÜÇ ──────────────┐    └─────────────────┘            │
   │ USB-C → BQ24074 (power-path)     │    ┌─────────────────┐            │
   │   → 18650 (koruma devreli)       │    │ BME280 + ivmeölçer│(var. B)  │
   │   → TPS7A02 LDO (25 nA) → 3.3 V  │    └─────────────────┘            │
   └──────────────────────────────────┘                                   │
                                                                          ┘
```

---

## 5. Alt Sistem Gereksinimleri ve Komponent Önerileri

### 5.1 İşlemci + Radyo

| # | Gereksinim |
|---|---|
| HW-MCU-1 | nRF52840 (BLE 5.x) + SX1262 (LoRa) mimarisi kullanılacak |
| HW-MCU-2 | Sertifikalı, lehimlenebilir modül formunda olacak — çıplak çip + RF tasarım yapılmayacak |
| HW-MCU-3 | Modül 868 MHz bandını desteklemeli ve TCXO içermeli (frekans kararlılığı için) |
| HW-MCU-4 | En az 1 MB flash / 256 KB RAM (Meshtastic + e-ink UI için) |

**Komponent adayları:**

| Parça | Tip | Yaklaşık fiyat | Değerlendirme |
|---|---|---|---|
| **Heltec HT-N5262M** | nRF52840 + SX1262 **birleşik** | ~$9–12 | **v1 için önerilen.** Tek parça, Meshtastic uyumlu olarak pazarlanıyor, RF işi minimumda. |
| RAK4630 | nRF52840 + SX1262 birleşik | ~$18–22 | En olgun ekosistem, RAK4631 sayesinde Meshtastic'te birinci sınıf destek. Pahalı. |
| Raytac MDBT50Q-1MV2 + Ebyte E22-900M22S | Ayrık MCU + radyo | ~$8 + ~$8 | Daha esnek ve adette daha ucuz; ama iki modül arası SPI + RXEN/TXEN + TCXO işini sen çözersin. **v2 için.** |
| Ai-Thinker Ra-01SH | Sadece SX1262 | ~$4 | En ucuz radyo; PA yok, ayrı MCU gerekir. |

> **Karar:** v1 prototipte **birleşik modül** (HT-N5262M veya RAK4630) kullan. Kart çalıştıktan, kasa oturduktan ve firmware doğrulandıktan sonra v2'de ayrık modüle geçerek maliyet düşür. Bu sıralama, ilk turda aynı anda hem RF hem güç hem mekanik riski almanı engeller.

> ⚠️ **PA'lı modül uyarısı:** Ebyte E22-900M22S gibi güç yükselteçli modüller 22 dBm (≈158 mW) çıkabilir. EU868 kurallarında çoğu alt bant **14 dBm (25 mW ERP)** ile sınırlıdır. PA'lı modül seçersen çıkış gücünü firmware'de sınırlaman **yasal zorunluluktur**.

### 5.2 Güç Sistemi

| # | Gereksinim |
|---|---|
| HW-PWR-1 | Li-ion 18650 hücre, **kullanıcı tarafından değiştirilebilir** olacak |
| HW-PWR-2 | USB-C ile şarj; şarj sırasında cihaz çalışmaya devam edecek (power-path) |
| HW-PWR-3 | Aşırı şarj / aşırı deşarj / kısa devre koruması bulunacak |
| HW-PWR-4 | Uyku modunda toplam kart akımı **< 100 µA** olacak |
| HW-PWR-5 | Pil seviyesi yazılımdan okunabilecek |
| HW-PWR-6 | Solar giriş için hazırlık bulunacak (kamu node'u varyantı) |

**Neden 18650:** Afette en kritik özellik, pilin **değiştirilebilir** olmasıdır. Kullanıcı bitmiş hücreyi çıkarıp yenisini takabilir; hücreler her yerde bulunur ve powerbank'lerden sökülebilir. Yapıştırılmış LiPo bu esnekliği yok eder.

| Fonksiyon | Önerilen parça | Neden |
|---|---|---|
| Şarj + power-path | **BQ24074** (~$2) | Şarj sırasında yükü besler, giriş akımı sınırlar, pil yokken bile çalışır. TP4056'nın power-path'i yoktur. |
| Regülatör | **TPS7A0233** (~$0.8) | 25 nA sükûnet akımı. Uyku akımı bütçesinin belirleyicisi budur; sıradan LDO'lar (XC6206 ~1–3 µA) pil ömrünü kısaltır. |
| Pil koruma | DW01A + FS8205A (~$0.4) veya korumalı hücre | Çıplak hücre kullanılacaksa kartta koruma **zorunlu**. |
| Pil ölçümü | MAX17048 (~$2) veya MOSFET'li direnç böleci | Fuel-gauge doğru yüzde verir; direnç böleci ucuz ama uykuda MOSFET ile kesilmeli. |
| USB koruma | USBLC6-2SC6 (~$0.3) | ESD koruması, sahada zorunlu. |

> ⚠️ **Gerilim tuzağı:** SX1262'nin mutlak maksimum besleme gerilimi **3.7 V**'tur; dolu Li-ion ise **4.2 V**. Modülü doğrudan pile bağlamak çipi zamanla öldürür. Araya LDO **şarttır**.

> 🔌 **USB-C notu:** Sadece güç alacak olsan bile CC1 ve CC2 hatlarına **ikişer adet 5.1 kΩ** direnç koy. Bunlar olmadan modern USB-C şarj adaptörlerinin çoğu hiç güç vermez — özel kartlarda en sık yapılan hata budur.

### 5.3 Anten ve RF

| # | Gereksinim |
|---|---|
| HW-RF-1 | Harici, değiştirilebilir anten; **SMA dişi** bulkhead konektör |
| HW-RF-2 | Modül–konektör arası 50 Ω kontrollü empedans hattı, mümkün olan en kısa mesafede |
| HW-RF-3 | Anten hattı altında kesintisiz zemin (ground) düzlemi, yanlarda dikişli via |
| HW-RF-4 | Kasa dışına çıkan SMA geçişi O-ring/conta ile sızdırmaz olacak |

**Neden dahili anten değil:** Dahili anten kasayı, pili ve eli "görür"; menzil öngörülemez şekilde düşer. Afet cihazında menzil pazarlık konusu değildir. Ayrıca kullanıcı, kamu node'u için yüksek kazançlı bir antene geçebilmelidir.

> ⚠️ **Antensiz çalıştırma yasağı:** Cihaz anten takılı değilken TX yaparsa güç katı hasar görebilir. Kasa tasarımında SMA'nın daima erişilebilir ve takılı olması sağlanmalı; kullanım kılavuzunda uyarı verilmeli.

### 5.4 Kullanıcı Arayüzü

| # | Gereksinim |
|---|---|
| HW-UI-1 | Güç/menü butonu ve **ayrı bir SOS butonu** bulunacak |
| HW-UI-2 | SOS butonu **yanlışlıkla basılmaya karşı korumalı** olacak (gömme veya kapaklı) |
| HW-UI-3 | Durum bildirimi için LED bulunacak (şarj + ağ durumu) |
| HW-UI-4 | Sesli uyarı için buzzer bulunacak |
| HW-UI-5 | Butonlar IP54 uyumlu geçişe sahip olacak |

| Parça | Öneri |
|---|---|
| Butonlar | 6×6 mm taktil switch + **silikon kapak/membran** (sızdırmazlık burada sağlanır) |
| LED | RGB (WS2812B veya ayrık) + kasa üstünde ışık borusu (light pipe) |
| Buzzer | Manyetik buzzer (~$0.5) — enkaz altında **sesli bulunabilirlik** kritik bir işlevdir |
| Titreşim | Opsiyonel coin motor; sessiz uyarı gerektiren senaryolar için |

> Buzzer'ı "ekstra" olarak görme. Telefonu bitmiş veya elinde cihaz olmayan biri için, cihazın kendisinin ses çıkarabilmesi doğrudan bir kurtarma işlevidir.

### 5.5 Ekran (Varyant B, tek konektörden)

| # | Gereksinim |
|---|---|
| HW-DSP-1 | Ekran, karta **konektörle** bağlanacak; temel varyantta konektör boş kalacak |
| HW-DSP-2 | Konektör hem I²C (OLED) hem SPI (e-ink) sinyallerini taşıyacak |
| HW-DSP-3 | Ekran beslemesi yazılımdan kesilebilecek (load switch) |

| Seçenek | Fiyat | Değerlendirme |
|---|---|---|
| **E-ink 2.13" (SSD1680 sınıfı)** | ~$9–13 | **Varyant B için önerilen.** Güneş altında okunur, görüntü için güç harcamaz, cihaz kapansa bile son mesaj/konum ekranda kalır — afet senaryosunda bu özellik tek başına belirleyicidir. |
| OLED 0.96"/1.3" (SSD1306/SH1106) | ~$2–3 | Ucuz, Meshtastic'te yerleşik destek. Güneşte zayıf, sürekli açık kalırsa burn-in ve güç sorunu. |
| TFT 2.4" (ST7789/ILI9341) | ~$6–9 | Renkli ve hızlı, ancak nRF52840'ın 256 KB RAM'i için ağır; güç tüketimi yüksek. **Önerilmez.** |

### 5.6 Konum ve Sensörler (Varyant B)

| # | Gereksinim |
|---|---|
| HW-SNS-1 | GPS modülü **güç anahtarı arkasında** olacak (kullanılmadığında tamamen kesilecek) |
| HW-SNS-2 | GPS anteni, LoRa anteninden mümkün olduğunca uzağa yerleştirilecek |
| HW-SNS-3 | İvmeölçer, hareketle uyanma ve ekran tetikleme için kullanılacak |

| Parça | Fiyat | Not |
|---|---|---|
| u-blox MAX-M10S | ~$9–12 | Düşük güçlü, hızlı fix. Pil ömrü öncelikliyse bu. |
| ATGM336H | ~$3–4 | Çok ucuz, GPS+BeiDou, Meshtastic'te yaygın. Güç tüketimi daha yüksek. |
| BME280 | ~$2–3 | Sıcaklık/nem/basınç — Meshtastic telemetri modülü doğrudan destekler |
| LIS3DH / LSM6DS3 | ~$1–2 | Hareketle uyanma, düşme algılama |

> GPS, kartın en büyük güç tüketicisidir. Sürekli açık bırakılan bir GPS, 72 saatlik pil hedefini tek başına ihlal eder. Bu yüzden HW-SNS-1 (güç anahtarı) opsiyonel değil, **zorunlu** bir gereksinimdir.

### 5.7 Programlama ve Devreye Alma

| # | Gereksinim |
|---|---|
| HW-DBG-1 | **SWD erişimi bulunacak** (SWDIO, SWCLK, GND, VDD, RESET) |
| HW-DBG-2 | Kart üzerinde test noktaları: pil gerilimi, 3.3 V, UART TX/RX |
| HW-DBG-3 | USB-C üzerinden UF2 ile firmware güncellenebilecek |

> ⚠️ **Bu adımı atlama:** Fabrikadan gelen boş nRF52840'a, USB üzerinden firmware yükleyebilmen için önce **SWD ile bootloader yazman gerekir**. Kartta SWD pedi yoksa, elindeki tüm kartlar programlanamaz durumda kalır. Yer kazanmak için [Tag-Connect](https://www.tag-connect.com) tipi konektörsüz ped kullanılabilir.

---

## 6. PCB Gereksinimleri

| # | Gereksinim | Değer |
|---|---|---|
| HW-PCB-1 | Katman sayısı | **4 katman** (SIG–GND–PWR–SIG). 2 katman mümkün ama RF ve gürültü riski artar; 4 katmanın ek maliyeti adette birkaç dolardır. |
| HW-PCB-2 | Kalınlık / bakır | 1.6 mm, 1 oz |
| HW-PCB-3 | Boyut hedefi | ≈ 75 × 45 mm (18650 hücre 65×18 mm'ye göre) |
| HW-PCB-4 | Modül anten keepout | Modül üreticisinin belirttiği keepout alanında **hiçbir bakır/parça olmayacak** |
| HW-PCB-5 | Montaj delikleri | En az 4 adet M2.5, kasa boss'larına hizalı |
| HW-PCB-6 | Yüzey işlem | ENIG (nem/korozyon direnci için HASL'a tercih edilir) |
| HW-PCB-7 | Konformal kaplama | Nem yoğuşmasına karşı üretim sonrası uygulanacak |

---

## 7. Mekanik ve IP54 Gereksinimleri

**IP54 tanımı:** *5 = toza karşı korumalı* (tam sızdırmaz değil, zararlı miktarda toz girmez) — *4 = her yönden gelen su sıçramasına dayanıklı*. Yağmur ve toz için yeterli; **suya batırmayı kapsamaz**.

| # | Gereksinim |
|---|---|
| HW-MEC-1 | Kapak, çevresel conta (silikon O-ring veya die-cut EPDM) ile kapanacak |
| HW-MEC-2 | Kapak en az 4 vida ile eşit baskı uygulayacak şekilde sıkılacak |
| HW-MEC-3 | USB-C portu silikon tapa/kapak ile kapatılacak |
| HW-MEC-4 | SMA geçişi O-ring + somun ile sızdırmaz olacak |
| HW-MEC-5 | Butonlar silikon membran veya sızdırmaz taktil switch kullanacak |
| HW-MEC-6 | Ekran penceresi, PC/akrilik + çift taraflı conta bant (3M VHB) ile yapıştırılacak |
| HW-MEC-7 | Çalışma sıcaklığı −10 °C … +50 °C |
| HW-MEC-8 | 1.5 m yükseklikten betona düşme testini geçecek |
| HW-MEC-9 | Kayış/karabina takılabilecek bir bağlantı noktası bulunacak |

**Malzeme önerisi:**

| Aşama | Malzeme | Not |
|---|---|---|
| Prototip | **PETG veya ASA** (3D baskı) | **PLA kullanma.** PLA ~55–60 °C'de deforme olur; yazın araç içi sıcaklığı bunu rahatlıkla aşar ve kasa çöker. ASA ayrıca UV dayanımlıdır. |
| Küçük seri | Hazır IP65 ABS kutu (Hammond, Gainta) + CNC kesim | **Hızlı yol:** sızdırmazlık zaten çözülmüş; sadece delik açarsın. İlk 10–20 adet için en akılcı seçenek. |
| Seri üretim | Enjeksiyon kalıp, ABS/PC | Kalıp maliyeti yüksek; ancak yüksek adette birim maliyeti düşürür. |

> 💡 **Pratik tavsiye:** İlk prototipte kasayı sıfırdan tasarlama. **Hazır IP65 sertifikalı bir kutu al**, PCB'yi onun iç ölçüsüne göre tasarla. Böylece sızdırmazlık riskini tamamen devre dışı bırakır, tüm dikkatini elektroniğe verirsin. Özel kasa tasarımını kart doğrulandıktan sonra yap.

Ek olarak: dış mekân/kamu node'u varyantında **Gore tipi basınç dengeleme ventili** (~$1) kullanılması, gece–gündüz sıcaklık farkından kaynaklanan iç yoğuşmayı engeller.

---

## 8. Tahmini Maliyet (BOM)

| Kalem | Varyant A (10 ad.) | Varyant A (100 ad.) | Varyant B (10 ad.) |
|---|---:|---:|---:|
| nRF52840+SX1262 modül | $12.00 | $9.00 | $12.00 |
| SMA konnektör + anten | $3.00 | $2.00 | $3.00 |
| Şarj + power-path (BQ24074) | $2.00 | $1.40 | $2.00 |
| LDO + pil ölçüm | $2.50 | $1.60 | $2.50 |
| USB-C + ESD | $1.00 | $0.60 | $1.00 |
| 18650 tutucu + koruma | $1.50 | $1.00 | $1.50 |
| Buton/LED/buzzer | $2.00 | $1.20 | $2.00 |
| Pasif komponentler | $1.50 | $1.00 | $2.00 |
| PCB (4 katman) | $3.00 | $1.50 | $3.00 |
| Kasa | $5.00 | $3.00 | $6.00 |
| 18650 hücre | $3.50 | $2.50 | $3.50 |
| E-ink ekran | — | — | $12.00 |
| GPS modülü | — | — | $5.00 |
| Sensörler | — | — | $4.00 |
| **Toplam** | **≈ $37** | **≈ $25** | **≈ $59** |

> Fiyatlar Ağustos 2026 tahminidir; kur, gümrük ve tedarikçiye göre değişir. **Sipariş öncesi LCSC/Mouser üzerinden güncel fiyat ve stok teyidi yapılmalıdır.** Türkiye'ye ithalatta gümrük ve KDV bu rakamların üzerine eklenir.

Varyant A, 100 adette **$25** ile NFR-1 hedef bandının ($25–40) alt sınırında kalıyor. 10 adetlik prototip turunda hedefin üzerine çıkması normaldir ve beklenmelidir.

---

## 9. Doğrulama ve Test Planı

| Aşama | Test | Başarı kriteri |
|---|---|---|
| Kart | Güç açılış testi | 3.3 V kararlı, kısa devre yok, akım beklenen aralıkta |
| Kart | Uyku akımı ölçümü | < 100 µA (HW-PWR-4) |
| RF | Bilinen bir Meshtastic cihazıyla eşleşme | Mesaj gidiş-dönüş başarılı |
| RF | Menzil testi | Açık alanda ≥ 2 km; şehir içinde ölçüm kayıt altına alınır |
| RF | Çıkış gücü ölçümü | EU868 limitleri içinde (spektrum analizörü veya SDR ile) |
| Güç | Pil ömrü testi | Gerçek kullanımda ≥ 72 saat (NFR-4) |
| Mekanik | Su sıçratma testi | IP54 (her yönden sıçratma, 10 dk, sonra içeride su yok) |
| Mekanik | Toz testi | IP54 |
| Mekanik | Düşürme testi | 1.5 m × 6 yön, sonrasında çalışır durumda |
| Termal | Sıcaklık döngüsü | −10 °C … +50 °C arasında çalışmaya devam |
| Saha | Çoklu node testi | En az 5 cihaz ile mesh oluşumu ve yönlendirme doğrulaması |

---

## 10. Riskler

| Risk | Etki | Azaltma |
|---|---|---|
| İlk kart turunda RF çalışmaması | Yüksek | Sertifikalı birleşik modül; RF tasarımını devre dışı bırak |
| SWD unutulması → kartlar programlanamaz | Yüksek | HW-DBG-1 zorunlu; layout gözden geçirmede kontrol listesine al |
| USB-C CC dirençlerinin unutulması | Orta | Şematik gözden geçirme kontrol listesi |
| SX1262'nin 4.2 V'a maruz kalması | Yüksek | LDO zorunlu; şematikte gerilim seviyelerini işaretle |
| IP54'ün ilk denemede tutmaması | Orta | Prototipte hazır IP65 kutu kullan |
| PLA kasanın sıcakta deforme olması | Orta | ASA/PETG zorunlu; malzeme BOM'da açıkça belirtilsin |
| GPS'in pil ömrünü tüketmesi | Orta | Güç anahtarı (HW-SNS-1) |
| PA'lı modülle yasal limitin aşılması | Orta | Firmware'de güç sınırı; çıkış gücü ölçümü |
| Komponent tedarik/stok sorunu | Orta | Her kritik parça için ikinci kaynak belirle |

---

## 11. Sonraki Adımlar

1. **Modül kararı:** HT-N5262M mi RAK4630 mu — güncel fiyat ve stok teyidi yapılarak kesinleştirilir
2. **Hazır kutu seçimi:** Prototip için IP65 kutu modeli seçilir; iç ölçüler PCB'yi kısıtlayacağı için bu, layout'tan **önce** yapılmalıdır
3. **Şematik tasarımı:** KiCad (açık kaynak, projeye uygun) ile şematik çizimi
4. **Şematik gözden geçirme:** Bu rapordaki kontrol listeleriyle karşılaştırma
5. **PCB layout ve üretim:** 5 adetlik ilk tur
6. **Firmware variant tanımı:** `PRIVATE_HW` ile Meshtastic derlemesi (bir sonraki rapor: yazılım gereksinimleri)
7. **Devreye alma ve saha testi**

> Sıralamadaki en önemli nokta **2. adımdır.** Kasa seçimi PCB'nin boyut ve konektör yerleşimini belirler; layout'a başlanıp sonra kutu aranırsa kart büyük ihtimalle yeniden çizilir.

---

## Kaynaklar

- [Meshtastic — Building Firmware / Custom Variants](https://meshtastic.org/docs/development/firmware/build/)
- [Meshtastic — Supported Hardware](https://meshtastic.org/docs/hardware/devices/)
- [nRF52840 vs ESP32-S3 for Meshtastic — Rokland](https://store.rokland.com/pages/nrf52840-vs-esp32-s3-dual-core-for-meshtastic-use)
- [Meshtastic cihazlarının güç tüketimi karşılaştırması — tutoduino.fr](https://tutoduino.fr/en/power-consumption-meshtastic/)
- [Heltec HT-N5262M (nRF52840 + SX1262 modül)](https://heltec.org/project/ht-n5262m/)
- [Meshtastic DIY SX126x referans tasarımı — GitHub](https://github.com/nicolabalzano/Meshtastic-DIY-sx126x)
- [Ebyte E22 modülleri — Meshtastic firmware tartışması](https://github.com/meshtastic/firmware/discussions/3948)
