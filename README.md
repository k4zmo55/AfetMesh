# AfetMesh

**Afet anında, cihazı ve uygulaması olmayan vatandaşın yardım çağrısı gönderebildiği LoRa mesh halk erişim katmanı.**

Afetzede telefonunun WiFi'ını açar → `AFETMESH-<mahalle>` ağına bağlanır → arayüz kendiliğinden açılır → tek dokunuşla *"Yardım istiyorum"* gönderir. **Uygulama kurulumu yok, internet yok, ön hazırlık yok.**

---

## Depo Yapısı

| Klasör / Dosya | İçerik | Statü |
|---|---|---|
| **[`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md)** | **Tek yetkili gereksinim kaynağı (SGB v2.0)** | ✅ Yürürlükte |
| [`01_Gereksinim_Analizi/`](01_Gereksinim_Analizi/) | İlk kapsam çalışması | 📎 Girdi belgesi |
| [`02_Donanim/`](02_Donanim/) | CEP donanım tasarım detayı (PCB, BOM, kasa) | 📎 Teknik ek |
| [`03_Yazilim/`](03_Yazilim/) | PORTAL, SOS modülü, PANO kaynak kodu | 🚧 Henüz boş |
| [`04_Dokumanlar/`](04_Dokumanlar/) | Pazar, rekabet ve kurumsal uyum araştırmaları | 📎 Araştırma girdileri |
| `Afet_Sonrası_İletişim_Sistemleri.pdf` | Çevre ve Şehircilik Bakanlığı rehberlik kılavuzu (2024) | 📄 Referans |

> **Kural:** Yeni bir gereksinim doğduğunda **önce SGB'ye** yazılır. Araştırma raporları belirli bir tarihteki bulguların kaydıdır; geriye dönük güncellenmez.

---

## Sistem Bileşenleri

| Bileşen | Nedir | Donanım | Maliyet |
|---|---|---|---|
| **CEP** | Kişisel node — evde/çantada taşınan, SOS butonlu | nRF52840 + SX1262, özel PCB | $25–40 |
| **CEP+** | Saha ekibi node'u — e-ink + GPS + sensör | Aynı PCB, farklı montaj | ~$59 |
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

**Aşama 0 — Varsayım doğrulama** (henüz başlanmadı)

PCB tasarımından önce, hazır kartlarla ve ~sıfır maliyetle yapılacak üç test:

- **TST-1** — İki telefon aynı node'a eşzamanlı BLE ile bağlanabiliyor mu? *(Mimariyi doğrulayan veya çürüten ilk test)*
- **TST-2** — ESP32-S3'te WiFi AP ve LoRa aynı anda çalışıyor mu?
- **TST-5** — Standart bir Meshtastic node'u bizim SOS paketimizi taşıyor mu?

Ayrıca bu aşamada **A-1 frekans kararı** verilecek (433 MHz mi 868 MHz mi) — bu karar verilmeden anten ve RF hattı tasarlanamaz.

---

## Temel Kabuller

- **Yeni bir mesh protokolü yazılmayacak.** Meshtastic tabanı korunur; mevcut ağlarla (TA Mesh dâhil) birlikte çalışabilirlik buna bağlıdır.
- **Telsiz sistemlerinin yerine geçilmiyor.** Ses ve video taşınmaz; AfetMesh kurumsal çözümlerin eksik olduğu **vatandaş katmanını** doldurur.
- **Menzil ve pil ömrü beyanları ölçülmüş değerlere dayanır.** İdeal koşul rakamları pazarlama amaçlı kullanılmaz.

---

## Lisans

Bkz. [LICENSE](LICENSE)
