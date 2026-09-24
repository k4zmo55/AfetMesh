# CEP / CEP-T — İlk Prototip Malzeme Listesi (v2)

> ## 📎 BELGE STATÜSÜ: PROTOTİP BOM (v2.0) — 24 Eylül 2026
>
> **PCB öncesi breadboard / pertinaks prototipi** içindir. Amaç: özel PCB'yi çizmeden önce **seçilen mimariyi (nRF52840 + SX1262 + e-paper + fiziksel butonlar)** Meshtastic ile çalıştırmak ve **pil ömrünü ölçmek** (SGB SYS-7).
>
> - Gereksinimler: [`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](../00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md) (SGB v2.4) · PCB parçaları: [`02_Donanim/Donanim_Gereksinim_Raporu.md`](../02_Donanim/Donanim_Gereksinim_Raporu.md) Bölüm 6
> - **Geçersiz kılınan belge:** v1 listesi (ESP32-S3 DevKit + 2,4" dokunmatik TFT + BMI160 + NEO-6M) → `Arsiv/CEPT_Ilk_Prototip_Malzeme_Listesi_v1_ESP32_GECERSIZ.pdf`. Gerekçe: A-7 kapandı (dokunmatik zorunlu değil), işlemci nRF52840 seçildi, BMI160 Meshtastic destekli değil, NEO-6M eski nesil.
> - Fiyatlar tahminidir (Eylül 2026, tek adet, KDV/gümrük hariç); sipariş öncesi teyit edilmelidir.

---

## 1. Mimari (Prototip)

```
  nRF52840 Pro Micro ──SPI──► SX1262 modülü ──► anten (A-1 bandı)
        │  (Meshtastic: nrf52_promicro_diy_tcxo varyantı)
        ├──SPI──► WeAct 2,9" e-paper (SSD1680) — InkHUD
        ├──I²C──► SHT40 (sıcaklık/nem) + LSM6DS3TR-C (6 eksen IMU, INT → GPIO)
        ├──UART─► GNSS modülü (yük anahtarı / jumper ile kesilebilir)
        ├──GPIO─► 5 yön + Seç + Geri + SOS butonları, piezo, RGB LED
        └──BAT──► 18650 + koruma kartı (prototipte), USB-C ile şarj
```

Referans pin haritaları: Meshtastic firmware `variants/nrf52840/diy/nrf52_promicro_diy_tcxo` (LoRa) ve meshtastic.org **DIY InkHUD Builds** sayfası (e-paper: SDA→1.15, SCL→1.11, CS→1.07, D/C→1.02, RES→1.01, BUSY→1.06).

---

## 2. Malzeme Listesi — 2 node için

### 2.1 Zorunlu (her node)

| # | Parça | Model / öneri | Adet (2 node) | Birim fiyat | Not |
|---|---|---|---:|---|---|
| P-1 | MCU kartı | **nRF52840 Pro Micro** (nice!nano uyumlu / "SuperMini nRF52840") | 2 (+1 yedek) | ~$5–8 | ⚠️ **Lehimlemeden önce USB'ye tak, UF2 sürücüsünün göründüğünü ve bootloader ≥ 0.8 olduğunu doğrula** — boş/eski bootloader'lı kartlar çıkabiliyor |
| P-2 | LoRa modülü — **A-1 = 868 MHz** ise | **Heltec HT-RA62** (SX1262, TCXO, dahili RF anahtarı) veya Ebyte **E22-900M22S** | 2 | ~$5–8 | HT-RA62'de RXEN/TXEN gerekmez; E22'de TXEN↔DIO2 köprüsü, RXEN → P0.17 |
| P-2' | LoRa modülü — **A-1 = 433 MHz** ise | Ebyte **E22-400M22S** (SX1268, TCXO) | 2 | ~$6–8 | TCXO 1,8 V tanımı ve RF anahtar pinleri doğru ayarlanmazsa BUSY takılır (Meshtastic issue #6692) — Bölüm 4'teki adım sırasına uy |
| P-3 | Anten + pigtail | Banda uygun ½λ whip (SMA) + IPEX→SMA pigtail | 2 | ~$3–5 | **Anten takılmadan TX yapma** (REG-6) |
| P-4 | Ekran | **WeAct Studio 2,9" e-paper modülü** (SSD1680, 296×128, siyah-beyaz) | 2 | ~$8–10 | InkHUD resmî DIY listesinde; 2,13" sürüm de destekli |
| P-5 | Butonlar | 6×6 mm taktil (Yukarı/Aşağı/Sol/Sağ/Seç/Geri) + 12×12 mm (SOS) | 7 ×2 | ~$0,1–0,3 | Hedef tasarımdaki 5 yönlü joystick (Alps SKRHABE010) de alınabilir |
| P-6 | Pil | **18650 Li-ion 3000–3500 mAh** (Samsung 35E / LG MJ1 / Panasonic NCR18650B / Molicel P30B) | 2 | ~$4–6 | Orijinal hücre; "9900 mAh" etiketli sahte hücrelerden kaçın |
| P-7 | Pil tutucu | 18650 tutucu (kablolu veya PCB tipi) | 2 | ~$0,5–1 | — |
| P-8 | Koruma / şarj (prototip) | **Korumalı TP4056 kartı (DW01A + FS8205A'lı, USB-C)** | 2 | ~$0,5–1 | Yalnızca prototip içindir; özel PCB'de **BQ24074 power-path** kullanılacak (Donanım Raporu K-5) |
| P-9 | Sesli uyarıcı | Piezo buzzer (pasif, ~4 kHz) + 2N7002 / AO3400 MOSFET | 2 | ~$0,5 | Enkaz modu (CEP-4/CEP-8) denemesi |
| P-10 | Durum LED'i | Ortak anot RGB LED (ayrık) + 3 × 1 kΩ | 2 | ~$0,2 | ❌ WS2812B kullanma (sönükken ~1 mA) |

### 2.2 CEP+ / CEP-T sensör seti (en az 1 node)

| # | Parça | Model / öneri | Adet | Birim fiyat | Not |
|---|---|---|---:|---|---|
| S-1 | Sıcaklık / nem | **SHT40 breakout** (I²C) | 1 | ~$3–6 | Meshtastic destekli (SHT4X). Alternatif: BME280 (basınç da ölçer) |
| S-2 | 6 eksen IMU | **LSM6DS3TR-C breakout** (I²C) | 1 | ~$3–6 | Meshtastic destekli; INT1 → P0.xx (uyanma). Bulunamazsa **MPU6050** (destekli) ile başlanabilir. ❌ BMI160 desteklenmiyor |
| S-3 | GNSS | **u-blox MAX-M10S breakout** (hedef parça, ~25 mW) | 1 | ~$15–25 | Ucuz alternatif: **Quectel L76K** modülü (~$5–8, güç 4–5 kat fazla). NEO-6M **önerilmez** |
| S-4 | GNSS güç anahtarı | P-MOSFET (AO3401) veya yük anahtarı breakout | 1 | ~$0,3 | GNSS'i yazılımdan tamamen kesmek için (HW-SNS-1) |

### 2.3 Ölçüm ve montaj

| # | Parça | Öneri | Adet | Fiyat | Not |
|---|---|---|---:|---|---|
| M-1 | **Akım ölçer (µA–mA)** | **Nordic Power Profiler Kit II (PPK2)** | 1 | ~$100 | **Şiddetle önerilir.** Pil ömrü hedefleri (CEP-N3 ≤ 10 µA, CEP-N10 ≤ 8 mA) USB güç ölçerle doğrulanamaz. Topluluk ölçümlerinin tamamı PPK2 ile yapılıyor |
| M-2 | USB-C inline güç ölçer | — | 1 | ~$8–12 | Şarj akımı ve kaba kontrol için |
| M-3 | Pertinaks / breadboard, pin header, dişi soket | — | — | ~$5 | Modülleri **soketle**, lehimleme |
| M-4 | İnce kablo (wire-wrap / jumper) | — | 1 set | ~$3–5 | SPI hatlarını kısa tut |
| M-5 | (Opsiyonel) SWD programlayıcı | J-Link EDU Mini veya Raspberry Pi Pico (picoprobe/debugprobe) | 1 | ~$5–60 | Bootloader'ı bozulan Pro Micro'yu kurtarmak ve özel PCB'deki RAK4630'a bootloader yazmak için **PCB aşamasında zorunlu** |

### 2.4 Referans / karşılaştırma node'u (opsiyonel)

| Parça | Neden |
|---|---|
| **RAK4631 + RAK19007** (WisBlock başlangıç kiti, ~$35) | Hedef PCB'deki **RAK4630 ile aynı çekirdek.** Firmware varyantını ve güç ölçümlerini özel PCB öncesi birebir modülde denemek için |

---

## 3. Bütçe Özeti

| Kalem | 2 node (sensörsüz) | + 1 node sensör seti | Not |
|---|---:|---:|---|
| MCU + LoRa + anten | ~$26–42 | — | Banda göre |
| E-paper × 2 | ~$16–20 | — | — |
| Pil + tutucu + koruma × 2 | ~$10–16 | — | — |
| Buton, piezo, LED, pasifler | ~$4–6 | — | — |
| Sensörler + GNSS | — | ~$20–40 | MAX-M10S ile üst sınır |
| Montaj malzemesi | ~$8–10 | — | — |
| **Ara toplam** | **~$64–94** | **~$20–40** | — |
| PPK2 (tek seferlik ölçüm aracı) | ~$100 | | Önerilir |

> v1 (ESP32-S3 + TFT) listesine göre biraz daha pahalı (~$40–50'den ~$65–95'e): iki e-paper ekran ve 18650 güç zinciri eklendi. Buna karşılık bu prototip, **hedef PCB ile aynı işlemci ailesini ve aynı arayüzü** test eder — ESP32 prototipinde ölçülen pil ömrü nRF52840 PCB'sine hiçbir şey söylemezdi.

---

## 4. Devreye Alma Sırası (CEP-T raporu Bölüm 8.1'in Yol A karşılığı)

| # | Adım | Başarı ölçütü |
|---|---|---|
| B-1 | Pro Micro'yu tek başına USB'ye tak; UF2 sürücüsü görünüyor mu, bootloader sürümü? | Sürücü görünür, sürüm ≥ 0.8 |
| B-2 | Meshtastic `nrf52_promicro_diy_tcxo` (veya kendi `PRIVATE_HW` varyantın) derle, UF2 ile yükle; LoRa takılı değilken seri konsolu izle | Açılış günlüğü, çökme yok |
| B-3 | LoRa modülünü ekle; TCXO ve RF anahtar ayarlarını doğrula; anteni tak | Radyo başlatma hatası yok, BUSY takılmıyor |
| B-4 | İki node arasında mesaj (Meshtastic uygulaması, BLE) | Çift yönlü mesaj |
| B-5 | TA Mesh / standart bir Meshtastic node'u ile haberleşme | SGB TST-5 ön denemesi |
| B-6 | E-paper'ı ekle, InkHUD'u etkinleştir | Ekran yenileniyor, butonla ekranlar arası geçiş |
| B-7 | 5 yön + Seç + Geri + SOS butonlarını varyanta tanımla | Tüm menüler yalnızca butonla gezilebiliyor |
| B-8 | SHT40 + LSM6DS3 ekle; telemetri modülünü aç | Sıcaklık/nem ağda görünür; hareketle uyanma |
| B-9 | GNSS'i ekle; güç anahtarıyla kapat/aç | Kapalıyken GNSS akımı 0 |
| B-10 | **PPK2 ile 24 saat ortalama akım** (GNSS kapalı, BLE bağlı değil) | Hedef ≤ 8 mA (CEP-N10); sonuç SGB'ye işlenir |
| B-11 | Tam deşarj testi (3000 mAh hücre) | Ölçülen gün sayısı kaydedilir (SYS-7) |

---

## Değişiklik Geçmişi

| Sürüm | Tarih | Değişiklik |
|---|---|---|
| 1.0 | 23 Eyl 2026 | İlk liste (PDF): ESP32-S3 DevKitC-1 + SX1262 + 2,4" ILI9341/XPT2046 dokunmatik TFT + BME280 + BMI160/MPU6050 + NEO-6M — **geçersiz, arşivde** |
| **2.0** | **24 Eyl 2026** | Donanım Raporu v2.1 ve A-7 kararıyla yeniden yazıldı: nRF52840 Pro Micro + SX1262/SX1268 + WeAct 2,9" e-paper + fiziksel butonlar + 18650; sensörler SHT40 + LSM6DS3TR-C; GNSS MAX-M10S (alt. L76K); PPK2 ölçüm aracı; RAK4631 referans node; devreye alma sırası B-1…B-11 |
