# AfetMesh

**Afet anında, cihazı ve uygulaması olmayan vatandaşın yardım çağrısı gönderebildiği LoRa mesh halk erişim katmanı.**

Afetzede telefonunun WiFi'ını açar → `AFETMESH-<mahalle>` ağına bağlanır → arayüz kendiliğinden açılır → tek dokunuşla *"Yardım istiyorum"* gönderir. **Uygulama kurulumu yok, internet yok, ön hazırlık yok.**

---

## Depo Yapısı

| Klasör / Dosya | İçerik | Statü |
|---|---|---|
| **[`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md)** | **Tek yetkili gereksinim kaynağı (SGB v2.4)** — 6.2b'de donanım özeti | ✅ Yürürlükte |
| [`01_Gereksinim_Analizi/`](01_Gereksinim_Analizi/) | İlk kapsam çalışması (v2.4 notlarıyla hizalı) | 📎 Girdi belgesi |
| [`02_Donanim/Donanim_Gereksinim_Raporu.md`](02_Donanim/Donanim_Gereksinim_Raporu.md) | CEP ailesi: 83 maddelik donanım gereksinim tablosu, **işlemci karşılaştırması** (TI / STM32 / ESP32 / Raspberry Pi / Nordic), güç bütçesi ve 5 güç modu, alt sistem malzeme seçimi, BOM, test planı (v2.1) | 📎 Teknik ek |
| `03_Yazilim/` | PORTAL, SOS modülü, PANO kaynak kodu | 🚧 Henüz boş |
| [`04_Dokumanlar/`](04_Dokumanlar/) | Pazar, rekabet, kurumsal uyum ve uluslararası karşılaştırma raporları | 📎 Araştırma girdileri |
| ↳ [`Literatur_ve_Topluluk_Arastirmasi.md`](04_Dokumanlar/Literatur_ve_Topluluk_Arastirmasi.md) | Donanım literatürü: 30 akademik yayın, 26 topluluk kaynağı, 14 ticari cihaz | 📎 Araştırma girdisi |
| ↳ [`Kaynakca.md`](04_Dokumanlar/Kaynakca.md) | **Projenin birleşik kaynakçası** | 📚 Kaynak dizini |
| ↳ [`Kaynaklar/Makaleler/`](04_Dokumanlar/Kaynaklar/Makaleler/) | İndirilen **18 açık erişimli makale** (PDF) | 📚 Arşiv |
| [`05_Cihaz_Tasarimi/`](05_Cihaz_Tasarimi/) | CEP-T konsept raporu (v1.4) + **PCB öncesi prototip malzeme listesi** (v2.0) | 📎 Konsept / uygulama |
| `Afet_Sonrası_İletişim_Sistemleri.pdf` | Çevre ve Şehircilik Bakanlığı rehberlik kılavuzu (2024) | 📄 Referans |

> **Kural:** Yeni bir gereksinim doğduğunda **önce SGB'ye** yazılır. Araştırma raporlarının orijinal bulguları silinmez; sonraki kararlar tarihli güncelleme notu (`⚠️ vX.Y`) olarak eklenir.
>
> **PDF'ler:** Her `.md` belgesinin yanında aynı adlı `.pdf` sürümü bulunur. PDF'ler md'den üretilir — **düzenleme her zaman md üzerinde yapılır.**

---

## Sistem Bileşenleri

| Bileşen | Nedir | Donanım | Maliyet |
|---|---|---|---|
| **CEP** | Kişisel node — evde/çantada taşınan, SOS butonlu, ~2 hafta pil hedefi | nRF52840 + SX1262 (RAK4630), 18650, özel PCB | $25–40 (100 adette ≈ $37) |
| **CEP+ / CEP-T** | Saha ekibi / gelişmiş kişisel node — 2,9" e-paper + GNSS + sensör + 5 yön buton | Aynı PCB, farklı montaj | ~$85–90 |
| **NOKTA** | Kamu node'u — WiFi portal + mesaj deposu + router | ESP32-S3 + PSRAM + solar, hazır kart | $90–130 |
| **PORTAL** | Captive portal arayüzü — cihazsız kullanıcının ekranı | NOKTA üzerinde gömülü web | — |
| **SOS modülü** | Yapısal acil paket + önceliklendirme | Meshtastic firmware modülü | — |
| **PANO** | Triage ve koordinasyon arayüzü | Çevrimdışı web | — |

---

## Tasarım İlkeleri

| | |
|---|---|
| **İ-1** | Kurulum gerekmez — tarayıcı yeterli |
| **İ-2** | Ön hazırlık gerekmez — yük vatandaşta değil, node'u kuranda |
| **İ-3** | Mesaj kaybolmaz — alıcı menzilde olmasa bile biriktirilir |
| **İ-4** | SOS ayrıcalıklıdır — ayrı, kısa, yapısal, öncelikli |

---

## Mevcut Durum ve Sonraki Adım

### Donanım kararları (24 Eylül 2026)

| Alt sistem | Seçim |
|---|---|
| İşlemci + LoRa | **nRF52840 + SX1262** — RAK4630 modülü, (L) 433 / (H) 868 MHz aynı footprint |
| Güç | 18650 (değiştirilebilir) → BQ24074 power-path → **TPS63900 buck-boost** 3,3 V; anahtarlı çevre birim hatları |
| Ekran / giriş | **2,9" e-paper** (InkHUD) + fiziksel butonlar (5 yön, Seç, Geri, korumalı SOS) — dokunmatik yok |
| Sensörler (CEP+/T) | SHT40 · LSM6DS3TR-C · u-blox MAX-M10S GNSS |
| Güç modları | Normal (hedef ≤ 8 mA, ≥ 14 gün) · Tasarruf · Enkaz/Beacon · Kapalı (≤ 10 µA) · Şarj |

Gerekçeler: [`02_Donanim/Donanim_Gereksinim_Raporu.md`](02_Donanim/Donanim_Gereksinim_Raporu.md) · Özet: SGB Bölüm 6.2b

### Aşamalar

**Aşama 0 — Varsayım doğrulama** (henüz başlanmadı)

PCB tasarımından önce, hazır kartlarla ve ~sıfır maliyetle yapılacak üç test:

- **TST-1** — İki telefon aynı node'a eşzamanlı BLE ile bağlanabiliyor mu? *(Mimariyi doğrulayan veya çürüten ilk test)*
- **TST-2** — ESP32-S3'te WiFi AP ve LoRa aynı anda çalışıyor mu?
- **TST-5** — Standart bir Meshtastic node'u bizim SOS paketimizi taşıyor mu?

Ayrıca bu aşamada **A-1 frekans kararı** verilecek (433 MHz mi 868 MHz mi). Seçilen RAK4630 modülünün (L)/(H) sürümleri aynı footprint'i paylaştığı için bu karar artık PCB layout'unu bloke etmez; yalnızca modül sipariş kodu ve anten banda göre seçilir.

**Aşama 3a — CEP breadboard prototipi:** nRF52840 Pro Micro + SX1262 + e-paper + butonlarla Meshtastic varyantını çalıştır ve PPK2 ile pil ömrünü ölç → [`05_Cihaz_Tasarimi/CEP_Ilk_Prototip_Malzeme_Listesi.md`](05_Cihaz_Tasarimi/CEP_Ilk_Prototip_Malzeme_Listesi.md). Ardından **Aşama 3b — PCB** (Donanım Raporu Bölüm 12).

**Açık donanım kararları:** A-1 (frekans bandı) · A-8 (buck-boost mu 3,0 V LDO mu — ilk kartta gürültü ölçümüyle). **Kapanan:** A-4 (RAK4630) · A-7 (dokunmatik yok).

---

## Temel Kabuller

- **Mevcut sistemlerin eksiğini kapatmıyoruz, yönünü tamamlıyoruz.** Türkiye'de AFAD **İKAS** (Cell Broadcast, 112 kanalı) uyarıyı *size* getirir; AfetMesh *sizin haberinizi dışarı çıkarır*. İKAS aşağı yönlü, AfetMesh yukarı yönlüdür.
- **Yeni bir mesh protokolü yazılmayacak.** Meshtastic tabanı korunur; mevcut ağlarla (TA Mesh dâhil) birlikte çalışabilirlik buna bağlıdır.
- **Telsiz sistemlerinin yerine geçilmiyor.** Ses ve video taşınmaz; AfetMesh kurumsal çözümlerin eksik olduğu **vatandaş katmanını** doldurur.
- **Menzil ve pil ömrü beyanları ölçülmüş değerlere dayanır.** İdeal koşul rakamları pazarlama amaçlı kullanılmaz.

---

## Lisans

Bkz. [LICENSE](LICENSE)
