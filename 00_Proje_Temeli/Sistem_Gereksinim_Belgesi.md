# AfetMesh — Sistem Gereksinim Belgesi (SGB)

**Sürüm:** 2.0
**Tarih:** 9 Ağustos 2026
**Statü:** ✅ Yürürlükte — **projenin tek yetkili gereksinim kaynağı**

---

## 0. Belgenin Statüsü ve Belge Hiyerarşisi

Bu belge, daha önce yazılmış beş raporun **birleştirilmiş ve çelişkileri giderilmiş** halidir. Bir konuda bu belge ile başka bir rapor çelişirse, **bu belge geçerlidir.**

| Belge | Rolü | Statü |
|---|---|---|
| **`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`** (bu belge) | **Tek yetkili gereksinim kaynağı** | ✅ Yürürlükte |
| `01_Gereksinim_Analizi/Gereksinim_Analizi_Raporu.md` | İlk kapsam çalışması | 📎 Girdi belgesi — v2.0 ile hizalandı |
| `02_Donanim/Donanim_Gereksinim_Raporu.md` | Donanım tasarım detayı (CEP için geçerli) | 📎 Teknik ek — v2.0 ile hizalandı |
| `04_Dokumanlar/Ihtiyac_ve_Rekabet_Analizi_Raporu.md` | Pazar ve rekabet araştırması | 📎 Araştırma girdisi (9 Ağu 2026) |
| `04_Dokumanlar/Farklilasma_ve_Urun_Mimarisi_Raporu.md` | Mimari gerekçelendirme | 📎 Karar gerekçesi (9 Ağu 2026) |
| `04_Dokumanlar/Bakanlik_Kilavuzu_Uyum_ve_Bosluk_Analizi.md` | Kurumsal uyum analizi | 📎 Araştırma girdisi (9 Ağu 2026) |

> **Kural:** Yeni bir gereksinim doğduğunda önce **bu belgeye** yazılır. Araştırma raporları güncellenmez — onlar belirli bir tarihteki bulguların kaydıdır.

---

## 1. Proje Tanımı

**AfetMesh**, afet sonrasında GSM ve internet altyapısı çöktüğünde çalışan, LoRa tabanlı, merkeziyetsiz bir **halk erişim katmanıdır.**

### 1.1 Tek cümlelik tanım

> AfetMesh, mevcut Meshtastic ağının üzerine oturan; afetzedenin **hiçbir uygulama kurmadan, hiçbir ön hazırlık yapmadan**, yalnızca telefonunun WiFi'ını açarak yardım çağrısı gönderebildiği halk erişim katmanıdır.

### 1.2 Çözdüğü problem

Türkiye'deki mevcut afet haberleşme çözümlerinin tamamı (AFAD KGHS, ASELSAN sistemleri, TAMP Haberleşme Hizmet Grubu, amatör telsiz) **kurumlar arası** katmandadır. Bakanlık kılavuzu paydaş listesinde "Vatandaşlar"a yer verir, ancak vatandaşa dokunan tek bir teknik bileşen tanımlamaz.

Sektördeki sivil çözümler (Meshtastic, MeshCore, MeshGrid) ise kullanıcının **afetten önce hazırlık yapmış olmasını** varsayar: cihaz satın almış, uygulama kurmuş, yapılandırmış olmak.

**Boşluk:** Cihazı olmayan, uygulaması olmayan, hazırlık yapmamış vatandaş ile ağ arasındaki son adım. Bu belgede **"Son 300 Metre Problemi"** olarak anılır.

### 1.3 Kurumsal konumlandırma

> AfetMesh, Japonya'nın **171 Afet Acil Haberleşme Sistemi**'nin GSM olmadan çalışan halidir.

Bu, Çevre ve Şehircilik Bakanlığı'nın 2024 tarihli *Afet Sonrası İletişim Sistemleri* kılavuzunda örnek gösterilen ve *"Türkiye'de GSM operatörleri ve AFAD iş birliği ile çalışmalar başlatılan"* modeldir. Aradaki fark taşıyıcıdadır: 171 GSM üzerinden çalışır ve büyük afette (kılavuzun kendi tanımıyla "iletişim tamamen kopar" senaryosunda) devre dışı kalır; AfetMesh LoRa mesh üzerinden çalışır ve altyapı gerektirmez.

---

## 2. Tasarım İlkeleri

Bu dört ilke tüm tasarım kararlarının ölçütüdür. Bir karar bu ilkelerden birini ihlal ediyorsa, teknik olarak ne kadar zarif olursa olsun reddedilir.

| İlke | Tanım |
|---|---|
| **İ-1 — Kurulum gerekmez** | Kullanıcı hiçbir uygulama indirmeden, yalnızca telefonunun tarayıcısıyla ağa erişebilmeli |
| **İ-2 — Ön hazırlık gerekmez** | Afetten önce hiçbir şey yapmamış kişi de sistemi kullanabilmeli. Hazırlık yükü kamu node'unu kuran tarafta, vatandaşta değil |
| **İ-3 — Mesaj kaybolmaz** | Alıcı o anda menzilde olmasa bile mesaj biriktirilir ve teslim edilir |
| **İ-4 — SOS ayrıcalıklıdır** | Acil mesajlar ayrı, kısa, yapısal ve öncelikli taşınır; normal trafikle aynı kuyruğa girmez |

---

## 3. Kapsam

### 3.1 Kapsam içi

- **CEP** — kişisel node donanım referans tasarımı (PCB, BOM, kasa, montaj rehberi)
- **NOKTA** — kamu node'u kurulum reçetesi (hazır kart + solar + kasa)
- **PORTAL** — NOKTA üzerinde çalışan captive portal web arayüzü
- **SOS protokolü** — yapılandırılmış acil durum paketi ve önceliklendirme firmware modülü
- **PANO** — çevrimdışı çalışan koordinasyon/triage arayüzü
- Kurumsal entegrasyon formatları, regülasyon uyumu, saha test protokolleri

### 3.2 Kapsam dışı

| Kapsam dışı | Gerekçe |
|---|---|
| Sıfırdan LoRa çipi/RF tasarımı | Sertifikalı hazır modül kullanılacak (Donanım Raporu K-3) |
| **Yeni bir mesh protokolü yazmak** | Meshtastic tabanı korunacak; ağ etkisi ve birlikte çalışabilirlik buna bağlı |
| **Özel mobil uygulama (Android/iOS)** | İ-1'i ihlal eder. Kullanıcı arayüzü PORTAL'dır |
| Kitlesel üretim ve endüstriyel sertifikasyon | Prototip ve pilot aşamasının dışında |
| Sesli iletişim | LoRa bant genişliği elvermez. Telsiz sistemlerinin yerine geçilmiyor |
| Video/görüntü aktarımı | Aynı gerekçe |

> **Netleştirme (eski SW-8 çelişkisi):** "Özel mobil uygulama geliştirmek kapsam dışı" ifadesi geçerlidir. PORTAL bir mobil uygulama **değildir** — node üzerinde barındırılan, tarayıcıda açılan bir web sayfasıdır ve kapsam içidir.

---

## 4. Sistem Mimarisi

```
   ┌─ AFETZEDE (hazırlıksız, uygulamasız) ─┐
   │        telefon → WiFi                  │
   └────────────────┬───────────────────────┘
                    │ (İ-1: tarayıcı, kurulum yok)
          ┌─────────▼──────────────────────────────┐
          │  ② NOKTA  (Kamu node'u)                │
          │  ESP32-S3 + PSRAM + solar              │
          │  • WiFi AP + PORTAL                    │
          │  • SOS kayıt deposu (İ-3)              │
          │  • ROUTER rolü, yüksek noktada         │
          └─────────┬──────────────────────────────┘
                    │ LoRa mesh
       ┌────────────┼────────────────┐
       │            │                │
┌──────▼──────┐ ┌───▼───────┐ ┌──────▼─────────┐
│ ① CEP       │ │ Diğer     │ │ ② Başka NOKTA  │
│ nRF52840    │ │ Meshtastic│ └──────┬─────────┘
│ CLIENT_MUTE │ │ (TA Mesh) │        │ (internet döndüğünde)
│ SOS + buzzer│ └───────────┘        ▼
└─────────────┘              ┌───────────────────┐
                             │ ③ PANO (triage)   │
                             └───────────────────┘
```

### 4.1 Bileşen özeti

| # | Bileşen | Tip | Rol | Hedef maliyet |
|---|---|---|---|---|
| ① | **CEP** | Donanım (özel PCB) | Kişisel node, mesh taşıyıcı, SOS kaynağı | $25–40 |
| ①+ | **CEP+** | Donanım (aynı PCB, farklı montaj) | Saha ekibi node'u (e-ink + GPS + sensör) | ~$59 |
| ② | **NOKTA** | Donanım (hazır kart reçetesi) | Kamu erişim noktası, router, mesaj deposu | $90–130 |
| ③ | **PORTAL** | Yazılım (gömülü web) | Cihazsız kullanıcı arayüzü | — |
| ④ | **SOS modülü** | Yazılım (firmware) | Yapısal paket + önceliklendirme | — |
| ⑤ | **PANO** | Yazılım (çevrimdışı web) | Koordinasyon ve triage | — |

---

## 5. Sistem Geneli Gereksinimleri (SYS)

| # | Gereksinim | Doğrulama |
|---|---|---|
| **SYS-1** | Sistem, GSM ve internet altyapısı olmadan tam işlevsel çalışacak | TST-7 |
| **SYS-2** | Sistem merkeziyetsiz olacak; çökmesi tüm ağı durduran merkezî bir bileşen bulunmayacak | Tasarım incelemesi |
| **SYS-3** | Sistem, **mevcut Meshtastic ağlarıyla (TA Mesh dâhil) birlikte çalışacak**; standart node'lar AfetMesh paketlerini taşıyabilecek | TST-5 |
| **SYS-4** | Meshtastic upstream firmware'inden sapma minimumda tutulacak; tüm özelleştirmeler **ayrı modül** olarak yazılacak, çekirdek mesh koduna dokunulmayacak | Kod incelemesi |
| **SYS-5** | Tüm yazılım açık kaynak lisansla yayımlanacak; kullanıcıdan ücret veya lisans talep edilmeyecek | — |
| **SYS-6** | Sistem hiçbir noktada kullanıcıdan kişisel veri (TCKN, telefon numarası, kimlik) **talep etmeyecek** | POR-7 |
| **SYS-7** | Menzil, kapsama ve pil ömrü beyanları **ölçülmüş değerlere** dayanacak; ideal koşul rakamları pazarlama amaçlı kullanılmayacak | Test kayıtları |

---

## 6. CEP — Kişisel Node Gereksinimleri

Donanım tasarım detayı için: `02_Donanim/Donanim_Gereksinim_Raporu.md` (bu bileşen için tamamen geçerlidir).

### 6.1 İşlevsel

| # | Gereksinim |
|---|---|
| **CEP-1** | Meshtastic firmware çalıştıracak; MCU **nRF52840**, radyo **SX1262** olacak |
| **CEP-2** | Varsayılan rol **`CLIENT_MUTE`** olacak — paket tekrarlamayacak |
| **CEP-3** | Yanlışlıkla basmaya karşı korumalı, ayrı bir **SOS butonu** bulunacak |
| **CEP-4** | **Enkaz Modu:** SOS butonuna uzun basıldığında periyodik sesli sinyal (≈30 sn'de bir çift bip) verecek ve düşük güçte SOS yayınlayacak |
| **CEP-5** | Telefonla eşleşme **BLE** üzerinden olacak (yalnızca cihaz sahibi için; cihazsız kullanıcı erişimi NOKTA üzerindendir) |
| **CEP-6** | Pil ve ağ durumu görsel (LED) ve sesli (buzzer) olarak bildirilecek |

> **CEP-2 gerekçesi:** Meshtastic'te her node varsayılan olarak gördüğü paketi tekrarlar. Toplanma alanında yüzlerce CEP aynı anda tekrarlarsa kanal tıkanır. Tekrarlama görevi NOKTA'lara aittir.

### 6.2 İşlevsel olmayan

| # | Gereksinim | Hedef |
|---|---|---|
| **CEP-N1** | Birim maliyet | $25–40 (100 adette hedef $25) |
| **CEP-N2** | Pil ömrü | Tek şarjla ≥ **72 saat** aktif çalışma |
| **CEP-N3** | Uyku akımı | < 100 µA |
| **CEP-N4** | Pil | 18650, **kullanıcı tarafından değiştirilebilir** |
| **CEP-N5** | Koruma sınıfı | **IP54** (asgari) |
| **CEP-N6** | Çalışma sıcaklığı | −10 °C … +50 °C |
| **CEP-N7** | Ağırlık | < 150 g |
| **CEP-N8** | Kurulum süresi | Deneyimsiz kullanıcı için < 15 dakika |
| **CEP-N9** | Dayanıklılık | 1,5 m'den betona düşme testini geçecek |

### 6.3 CEP+ varyantı (saha ekibi)

Aynı PCB, farklı montaj listesi: **e-ink ekran + GPS + sensörler**. Hedef maliyet ~$59. Rol `CLIENT` (tekrarlama açık — saha ekibi hareketli menzil uzatıcısı olarak çalışır).

> **Not — eski "Varyant B" tanımı değişmiştir:** Donanım Raporu'nda Varyant B "kamu node'u" olarak tarif edilmişti. Kamu node'u artık **ayrı bir cihaz sınıfıdır (NOKTA)**. Varyant B = CEP+ = saha ekibi node'u.

---

## 7. NOKTA — Kamu Node'u Gereksinimleri

Projenin farklılaşmasının tamamı bu bileşendedir.

### 7.1 İşlevsel

| # | Gereksinim |
|---|---|
| **NOK-1** | MCU **ESP32-S3 + PSRAM** olacak (WiFi AP yeteneği ve mesaj deposu için zorunlu) |
| **NOK-2** | Şifresiz **WiFi Access Point** açacak; SSID biçimi `AFETMESH-<yer adı>` |
| **NOK-3** | **Captive portal** çalıştıracak; telefon bağlandığında PORTAL arayüzü otomatik açılacak |
| **NOK-4** | Eşzamanlı **en az 8 istemci** destekleyecek |
| **NOK-5** | Alınan tüm SOS paketlerini **kalıcı olarak saklayacak** (süre sınırı yok) |
| **NOK-6** | Meshtastic rolü **`ROUTER`** olacak |
| **NOK-7** | İki modlu çalışacak: **Normal mod** — WiFi AP kapalı, butonla 15 dk açılır. **Afet modu** — tetiklendiğinde AP 72 saat sürekli açık |
| **NOK-8** | Afet modu, ağdan gelen afet sinyali **veya** cihaz üzerindeki ivmeölçerle sarsıntı algılanması ile otomatik devreye girecek |
| **NOK-9** | Yüksek noktaya (çatı, direk) montaj ve harici yüksek kazançlı anten desteği bulunacak |
| **NOK-10** | Üzerinde görünür bilgilendirme etiketi bulunacak: *"Telefonunuzun WiFi'ını açın → AFETMESH ağına bağlanın"* |
| **NOK-11** | Konumlandırma, resmî **toplanma alanları**, **kamu binaları** ve **MAKS adres verisi** kullanılarak planlanacak; rastgele yerleşim yapılmayacak |

> **NOK-1 gerekçesi (kritik):** Bu, CEP'ten farklı bir MCU seçimidir ve zorunludur. (a) Meshtastic'in `PhoneAPI` mimarisi aynı anda tek BLE istemcisi desteklemektedir — kamu node'u kavramı BLE üzerine kurulamaz. (b) Meshtastic'in mesaj biriktirme yeteneği **yalnızca PSRAM'li ESP32** cihazlarda çalışır; nRF52840 desteklemez. Her iki kısıt da tek bir çözüme işaret eder: NOKTA = ESP32-S3 + PSRAM + WiFi.

> **NOK-11 kaynağı:** Bakanlık kılavuzu Bölüm 1.3 "Proje Girdileri". Bu veriler belediyelerin kent bilgi sistemlerinden veya MAKS'tan temin edilebilir.

### 7.2 İşlevsel olmayan

| # | Gereksinim | Hedef |
|---|---|---|
| **NOK-N1** | Birim maliyet (solar dâhil) | $90–130 |
| **NOK-N2** | Enerji tüketimi | ≤ 12 Wh/gün (afet modunda, AP sürekli açık) |
| **NOK-N3** | Solar panel | ≥ 20 W |
| **NOK-N4** | Batarya | ≥ 100 Wh LiFePO4 |
| **NOK-N5** | Güneşsiz otonomi | ≥ 7 gün |
| **NOK-N6** | Koruma sınıfı | **IP65** (dış mekân, kalıcı montaj) |
| **NOK-N7** | Yoğuşma önleme | Gore tipi basınç dengeleme ventili |
| **NOK-N8** | Özel PCB | **Gerekmiyor** — hazır ESP32-S3 + SX1262 kartı kullanılacak |

> **NOK-N8 gerekçesi:** Kamu node'ları adetçe azdır (bir mahallede 3–5) ve maliyete duyarsızdır — solar panel zaten karttan pahalıdır. Donanım Raporu Bölüm 2'deki *"düşük adette özel PCB pahalıya gelir"* tespiti burada geçerlidir.

---

## 8. PORTAL — Captive Portal Arayüzü

### 8.1 Ekran akışı

```
┌────────────────────────────────────┐
│        AFETMESH — <yer adı>        │
│   Bu ağ internet gerektirmez.      │
├────────────────────────────────────┤
│   [ 🆘  YARDIM İSTİYORUM       ]   │  ← tek dokunuş, öncelikli
│   [ ✅  GÜVENDEYİM             ]   │
│   [ 🩹  YARALI VAR             ]   │
├────────────────────────────────────┤
│   Adınız: [__________] (isteğe bağlı)│
│   Mesaj:  [__________] (140 karakter)│
│   Kaç kişi: [ 1 ] [ 2-5 ] [ 5+ ]   │
│                        [ GÖNDER ]  │
├────────────────────────────────────┤
│   Gelen mesajlar (son 20)          │
└────────────────────────────────────┘
```

### 8.2 Gereksinimler

| # | Gereksinim |
|---|---|
| **POR-1** | Arayüz Türkçe olacak; teknik terim (kanal, PSK, preset, hop) **hiç kullanılmayacak** |
| **POR-2** | Tek dokunuşla gönderilebilen 3 acil durum butonu bulunacak |
| **POR-3** | Serbest metin **140 karakter** ile sınırlı olacak |
| **POR-4** | **Hiçbir harici kaynak** (CDN, font, harita servisi, analitik) yüklenmeyecek — tamamen çevrimdışı |
| **POR-5** | Toplam sayfa boyutu **< 50 KB** olacak |
| **POR-6** | Mesajın ağa iletildiği kullanıcıya görsel olarak teyit edilecek |
| **POR-7** | Kişisel veri istenmeyecek; yalnızca kullanıcının gönüllü yazdığı ad taşınacak (SYS-6) |
| **POR-8** | Kullanıcı, NOKTA'da saklanan son mesajları **WiFi üzerinden** görebilecek |
| **POR-9** | Aynı istemciden gönderim **hız sınırına** tabi olacak (bkz. SOS-3) |
| **POR-10** | Portal yalnızca mesajlaşma sunacak; **genel internet erişimi vermeyecek** |

> **POR-8 — önemli mimari karar:** Mesaj geçmişi, Meshtastic'in Store & Forward modülü üzerinden **değil**, NOKTA'nın kendi deposundan **PORTAL üzerinden HTTP ile** sunulur. Gerekçe: Meshtastic S&F, varsayılan genel kanalda geçmiş talebini desteklemez ve istemcinin hangi mesajları kaçırdığını bilmediği için yinelenen mesaj gönderir. Kendi depomuz bu iki kısıtı da ortadan kaldırır. Meshtastic S&F modülü, node'lar arası ikincil yedek olarak açık bırakılabilir.

---

## 9. SOS — Yapılandırılmış Acil Durum Paketi

### 9.1 Paket yapısı

| Alan | Boyut | İçerik |
|---|---|---|
| Tip | 1 byte | 0 = güvendeyim · 1 = yardım · 2 = yaralı · 3 = enkaz altında |
| Kişi sayısı | 1 byte | 1 / 2-5 / 5+ kodlanmış |
| Konum | 8 byte | lat/lon (int32 × 1e7) — varsa |
| Zaman | 4 byte | Unix zaman damgası |
| Ad + mesaj | ≤ 45 byte | UTF-8, kırpılmış |
| **Toplam** | **≤ 59 byte** | Meshtastic'in 237 byte sınırının çok altında |

### 9.2 Gereksinimler

| # | Gereksinim |
|---|---|
| **SOS-1** | SOS paketleri, normal metinden ayrışan **özel bir Meshtastic port numarası** kullanacak |
| **SOS-2** | SOS paketleri firmware iletim kuyruğunda **normal trafiğin önüne** alınacak |
| **SOS-3** | Aynı kaynaktan gönderim hızı sınırlanacak: **≤ 1 paket / 5 dakika** |
| **SOS-4** | Tüm NOKTA'lar SOS paketlerini kalıcı saklayacak (NOK-5) |
| **SOS-5** | Yapı **geriye dönük uyumlu** olacak: özelliği desteklemeyen standart Meshtastic node'ları paketi anlamasa bile **taşıyabilecek** |
| **SOS-6** | SOS trafiği, S&F ve gizlilik için **ayrı bir kanalda** taşınacak: `AfetMesh-ACIL`, PSK'sı kamuya açık yayımlanmış |

> **SOS-2 gerekçesi:** Meshtastic'te acil mesajların önceliği **yoktur**; SOS paketleri normal paketlerle aynı kuyruğa girer ve yoğun trafikte gecikebilir veya düşebilir. Upstream'de bu yönde bir talep (firmware #7980, Eylül 2025) açılmış ve kapatılmıştır. Bu yetenek **sıfırdan geliştirilecektir** ve projenin en somut özgün katkısıdır.

> **SOS-5 kritiktir:** Paketimiz standart node'lardan geçemezse, TA Mesh ağının üzerine binemez ve node yoğunluğu avantajını kaybederiz. Bu nedenle **yeni bir protokol icat edilmeyecek** — standart Meshtastic `Data` paketi içinde özel `portnum` kullanılacaktır.

---

## 10. PANO — Koordinasyon Arayüzü

| # | Gereksinim |
|---|---|
| **PAN-1** | SOS kayıtları **triage sırasına** göre listelenecek: enkaz altında > yaralı > yardım > güvendeyim |
| **PAN-2** | Kayıtlar harita üzerinde gösterilecek; harita döşemeleri **gömülü/çevrimdışı** olacak |
| **PAN-3** | Kayıtlar "ulaşıldı / ulaşılmadı" olarak işaretlenebilecek |
| **PAN-4** | Tekrar eden veya şüpheli kaynaklar işaretlenecek |
| **PAN-5** | Pano internetsiz, tek bir dizüstü bilgisayarda çalışabilecek |
| **PAN-6** | Çıktı **AYDES / GAMER / 112 / AHC** entegrasyonuna uygun formatta üretilecek (asgari: CSV + coğrafi katman) |
| **PAN-7** | Üretilen veriler açık veri politikasına uygun, paydaşlarla paylaşılabilir formatta olacak |

> **PAN-6 ve PAN-7 kaynağı:** Bakanlık kılavuzu Bölüm 1.4 ve 2.4. Kurumsal teklifte bu entegrasyonların adının geçmesi, projeyi kurumun kendi çerçevesine yerleştirir.

---

## 11. Regülasyon Gereksinimleri (REG)

| # | Gereksinim |
|---|---|
| **REG-1** | Cihazlar Türkiye'de **lisans gerektirmeyen** SRD/ISM bandında çalışacak |
| **REG-2** | Çıkış gücü, seçilen bandın ERP limitini aşmayacak (433 MHz: ~10 mW ERP · 868 MHz: ~500 mW ERP, duty-cycle sınırlı) |
| **REG-3** | PA'lı modül kullanılırsa çıkış gücü firmware'de sınırlanacak — bu **yasal zorunluluktur** |
| **REG-4** | BTK'nın güncel SRD düzenlemesi (Milli Frekans Planı, Sınıf 1 Cihaz Kategorileri) resmî kaynaktan **teyit edilecek** |
| **REG-5** | Kullanıcıdan kişisel veri toplanmadığı için KVKK veri sorumlusu yükümlülüğü asgari düzeyde tutulacak (SYS-6, POR-7) |
| **REG-6** | Anten takılı değilken TX yapılmayacak; kullanım kılavuzunda uyarı bulunacak |

---

## 12. Açık Kararlar (Karar Bekleyen Konular)

Bu maddeler **bilerek açık bırakılmıştır.** Hiçbiri varsayım yapılarak kapatılmamalıdır.

| # | Karar | Karar ölçütü | Ne zaman |
|---|---|---|---|
| **A-1** | **Frekans bandı: 433 MHz mi 868 MHz mi?** | TA Mesh ağının fiilî bandı + BTK teyidi. **Ağda yalnız kalmamak, teknik üstünlükten önceliklidir** | Aşama 0 |
| **A-2** | Modem preset (`LongFast` mi alternatif mi) | Yoğunluk testi TST-4 sonucu | Aşama 0 sonrası |
| **A-3** | NOKTA için hazır kart modeli | Güncel fiyat/stok + TST-2 sonucu | Aşama 1 |
| **A-4** | CEP modülü: HT-N5262M mi RAK4630 mu | Güncel fiyat/stok teyidi | Aşama 3 |
| **A-5** | Pilot bölge ve muhatap kurum | İlçe belediyesi bilgi işlem / afet koordinasyon birimi ile görüşme | Aşama 1 |
| **A-6** | PORTAL'da mesaj saklama süresi ve silme politikası | KVKK değerlendirmesi | Aşama 1 |

> **A-1 neden hâlâ açık:** Önceki raporlar bölge ayarını `EU_868` olarak sabitlemişti. Ancak Türkiye'de topluluk ağı ağırlıklı olarak **433 MHz** kullanmaktadır. 868 MHz daha yüksek güç bütçesi sunar (500 mW vs 10 mW ERP), ancak yanlış band seçimi bizi mevcut ağdan tamamen koparır. Node yoğunluğu bu projede menzilden daha kritik bir başarı faktörü olduğu için, karar ölçümle değil **ağ gerçeğiyle** verilecektir.

---

## 13. Doğrulama Planı

### 13.1 Öncelikli varsayım testleri

Bu testler **PCB tasarımından önce**, hazır kartlarla ve yaklaşık sıfır maliyetle yapılır.

| # | Test | Başarı kriteri | Öncelik |
|---|---|---|---|
| **TST-1** | İki telefonla aynı node'a eşzamanlı BLE bağlantısı | Tek istemci kısıtının geçerliliği kesinleşir. **Mimariyi doğrulayan veya çürüten ilk testtir** | ⚠️ En yüksek |
| **TST-2** | ESP32-S3'te WiFi AP + LoRa eşzamanlı çalışma | 8 istemci bağlıyken LoRa gidiş-dönüş başarılı; radyo girişimi ölçülür | Yüksek |
| **TST-3** | NOKTA güç bütçesi ölçümü | Gerçek tüketim ≤ 12 Wh/gün | Yüksek |
| **TST-4** | Yoğunluk testi: 1 NOKTA + 20 CEP + 8 telefon | SOS gecikmesi < 60 sn; paket kaybı < %10 | Yüksek |
| **TST-5** | Uyumluluk: standart Meshtastic node'u SOS paketimizi taşıyor mu | SOS-5 doğrulanır | Yüksek |
| **TST-6** | Kullanılabilirlik: 10 teknik olmayan gönüllü, yönlendirmesiz | ≥ 8/10 başarı, ortalama < 90 sn | Orta |
| **TST-7** | Tam kopukluk testi: GSM ve internet kapalı, uçtan uca senaryo | SYS-1 doğrulanır | Orta |

> **TST-1 ve TST-2 tamamlanmadan PCB tasarımına başlanmayacaktır.**

### 13.2 Donanım doğrulama

`02_Donanim/Donanim_Gereksinim_Raporu.md` Bölüm 9'daki test listesi geçerlidir (güç açılış, uyku akımı, RF eşleşme, menzil, çıkış gücü, pil ömrü, IP54, düşürme, termal).

### 13.3 Kurumsal performans göstergeleri

Bakanlık kılavuzu Bölüm 1.5'teki yedi resmî gösterge, raporlama dili olarak benimsenmiştir:

| Kılavuzun göstergesi | AfetMesh karşılığı | Hedef |
|---|---|---|
| İletişim süresi | SOS paketi uçtan uca gecikme | < 60 sn |
| Kapsama alanı | NOKTA başına yarıçap × node sayısı | Ölçülecek (TST-4) |
| Erişilebilirlik | Eşzamanlı portal kullanıcısı | ≥ 8 |
| Haberleşme güvenilirliği | Paket teslim oranı | ≥ %90 |
| Acil durum yanıt süresi | SOS → PANO'da görünme | < 2 dk |
| Veri aktarım hızı | Etkin veri hızı | ~1–5 kbps *(düşük — açıkça beyan edilir)* |
| Yedekleme ve kurtarma | Saklanan kayıt sayısı/süresi | Süresiz, ≥ 10.000 kayıt |

---

## 14. Yol Haritası

| Aşama | İçerik | Süre | Çıktı |
|---|---|---|---|
| **0 — Varsayım doğrulama** | TST-1, TST-2, TST-5. Hazır kartlarla, PCB yok. A-1 kararı verilir | 2 hafta | Mimarinin teyidi veya revizyonu |
| **1 — MVP: NOKTA + PORTAL** | Hazır ESP32-S3 kart üzerinde captive portal + mesaj deposu | 6–8 hafta | Çalışan tek kamu node'u |
| **2 — SOS modülü** | SOS-1…SOS-6 firmware modülü | 4 hafta | Upstream'e katkı adayı |
| **3 — CEP prototipi** | Donanım Raporu Bölüm 11 adımları | 8–12 hafta | 5 adet PCB |
| **4 — PANO** | Koordinasyon arayüzü | 3 hafta | Çevrimdışı web panosu |
| **5 — Pilot** | Bir mahalle: 3 NOKTA + 20 CEP + saha tatbikatı | 4 hafta | Ölçülmüş saha verisi |

> **Sıralama gerekçesi:** Projenin farklılaşması donanımda değil PORTAL'dadır. Ayrıca PORTAL hazır kartlarla doğrulanabildiği için çok daha hızlı ve ucuzdur. PCB, farklılaşma kanıtlandıktan sonra anlamlıdır.

---

## 15. Risk Kaydı

| Risk | Etki | Azaltma |
|---|---|---|
| Yetersiz node yoğunluğu → cihazsız kullanıcı ağa giremez | **Yüksek** | NOK-11 (resmî veriyle konumlandırma); TA Mesh ağıyla birleşme (SYS-3) |
| Yanlış frekans bandı → mevcut ağdan kopma | **Yüksek** | A-1 kararı ölçümle değil ağ gerçeğiyle verilecek |
| SOS paketi standart node'lardan geçmez | **Yüksek** | SOS-5; TST-5 ile erken doğrulama |
| WiFi AP güç bütçesini aşar | **Yüksek** | NOK-7 ikili mod; TST-3 ile erken ölçüm |
| Şehir içi LoRa menzilinin düşmesi | **Yüksek** | NOK-9 yüksek nokta montajı; node sayısını artırma |
| Özelleştirmelerin upstream'den kopması | Orta | SYS-4 ayrı modül politikası; düzenli rebase |
| ESP32'de WiFi–LoRa radyo girişimi | Orta | TST-2; WiFi kanalı ve TX zamanlaması ayarı |
| Kurulum izni alınamaması (çatı, cami, okul) | Orta | Pilot öncesi muhtarlık/kurum görüşmesi; taşınabilir direk alternatifi |
| Şifresiz açık WiFi'ın kötüye kullanılması | Orta | SOS-3 hız sınırı; POR-10 (internet erişimi yok) |
| Parça tedarik ve kur riski | Orta | Her kritik parça için ikinci kaynak |
| Regülasyon belirsizliği | Düşük-Orta | REG-4 erken teyit |
| Paralel topluluk çalışmasıyla çabanın tekrarı | Orta | TA Mesh ile erken temas ve işbirliği |

---

## 16. Çözülen Çelişkiler Kaydı

Aşağıdaki 14 çelişki, önceki raporlar arasında tespit edilmiş ve bu belgede çözülmüştür. Kayıt, kararların **neden** böyle olduğunun izini korumak için tutulmaktadır.

| # | Çelişki | Eski durum | Karar |
|---|---|---|---|
| **Ç-1** | Cihazsız kullanıcı erişimi | Gereksinim R.: BLE | **WiFi captive portal** (NOK-2/3, POR-*). BLE yalnızca CEP sahibi için (CEP-5) |
| **Ç-2** | Kamu node'u donanımı | Gereksinim R. 6.5 + Donanım K-1/K-2: nRF52840 varyantı | **Ayrı cihaz sınıfı: ESP32-S3 + PSRAM** (NOK-1) |
| **Ç-3** | Frekans bandı | NFR-9/SW-2/HW-INT-3: `EU_868` kesin | **Açık karar A-1.** Ölçüt: TA Mesh fiilî bandı |
| **Ç-4** | SOS önceliklendirme | SW-6: "yapılandırılmalı" | **Geliştirilecek** (SOS-2) — upstream'de yok |
| **Ç-5** | Mesaj biriktirme | Gereksinim R.'de yok; nRF52840 seçimi imkânsız kılıyor | **NOKTA'da** (NOK-5), PSRAM'li ESP32 üzerinde |
| **Ç-6** | Kullanıcı arayüzü | SW-8: resmî Meshtastic uygulaması | **PORTAL birincil**; Meshtastic uygulaması yalnızca CEP sahipleri için opsiyonel |
| **Ç-7** | Tıkanma çözümü | SW-13: "ayarlar optimize edilmeli" | **Rol disiplini** (CEP-2 `CLIENT_MUTE`, NOK-6 `ROUTER`) |
| **Ç-8** | Maliyet hedefi | NFR-1: tek hedef $25–40 | **Cihaz sınıfına göre ayrı:** CEP $25–40 · CEP+ ~$59 · NOKTA $90–130 |
| **Ç-9** | Pil ömrü hedefi | NFR-4: 72 saat (tüm node'lar) | **CEP: 72 saat** (CEP-N2) · **NOKTA: süreklilik + 7 gün otonomi** (NOK-N5) |
| **Ç-10** | Koruma sınıfı | NFR-5: IP54 hedef IP65 (belirsiz) | **CEP: IP54 asgari** (CEP-N5) · **NOKTA: IP65 zorunlu** (NOK-N6) |
| **Ç-11** | Node konumlandırma | 6.5: "toplanma alanları" (genel ifade) | **Resmî veri zorunlu** (NOK-11): toplanma alanları + kamu binaları + MAKS |
| **Ç-12** | "Özel uygulama kapsam dışı" vs PORTAL | Görünürde çelişki | **Netleştirildi:** PORTAL mobil uygulama değildir; node üzerinde barındırılan web sayfasıdır (Bölüm 3.2) |
| **Ç-13** | Menzil beyanları | Raporlar arası farklı rakamlar (1–3 km / ≥2 km / 5 km) | **SYS-7:** yalnızca ölçülmüş değerler beyan edilir |
| **Ç-14** | Store & Forward genel kanalda çalışmıyor | Fark edilmemişti | **PORTAL kendi deposundan HTTP ile sunar** (POR-8); Meshtastic S&F ikincil. Ayrıca SOS için ayrı kanal (SOS-6) |

---

## 17. İzlenebilirlik: Eski → Yeni Gereksinim Eşleşmesi

| Eski ID (Gereksinim Analizi Raporu) | Yeni ID | Durum |
|---|---|---|
| NFR-1 (maliyet) | CEP-N1 / NOK-N1 | Bölündü |
| NFR-2 (temin edilebilirlik) | — | Korundu (BOM ikinci kaynak politikası) |
| NFR-3 (DIY uygunluğu) | — | Korundu |
| NFR-4 (pil ömrü) | CEP-N2 / NOK-N5 | Bölündü |
| NFR-5 (dayanıklılık) | CEP-N5 / NOK-N6 | Bölündü |
| NFR-6 (kurulum süresi) | CEP-N8 | Korundu |
| NFR-7 (kullanım kolaylığı) | POR-1, TST-6 | PORTAL'a taşındı |
| NFR-8 (enerji bağımsızlığı) | NOK-N3/N4/N5 | Detaylandırıldı |
| NFR-9 (yasal uygunluk) | REG-1…REG-4, A-1 | Frekans kararı açıldı |
| NFR-10 (upstream uyumu) | SYS-4 | Korundu, güçlendirildi |
| SW-1 (Meshtastic tabanı) | CEP-1, SYS-3 | Korundu |
| SW-2 (bölge ayarı) | A-1 | **Askıya alındı** |
| SW-3 (kanal şifreleme) | SOS-6 | Yeniden tanımlandı |
| SW-4 (PKC uçtan uca) | — | Korundu (Meshtastic 2.5+ yerleşik) |
| SW-5 (uyku modu) | CEP-N3 | Korundu |
| SW-6 (SOS önceliklendirme) | SOS-1, SOS-2 | **Yapılandırma → geliştirme** |
| SW-7 (node etiketleme) | NOK-2 (SSID biçimi), NOK-10 | Yeniden tanımlandı |
| SW-8 (resmî mobil uygulama) | Bölüm 3.2, POR-* | **Değiştirildi** |
| SW-9 (hızlı mesaj şablonları) | POR-2 | PORTAL'a taşındı |
| SW-10 (çevrimdışı harita) | PAN-2 | PANO'ya taşındı |
| SW-11 ("en yakın node'a bağlan" sihirbazı) | — | **İptal** — captive portal gereksiz kılıyor |
| SW-12 (mesaj boyutu) | SOS paket yapısı (Bölüm 9.1) | Somutlaştırıldı |
| SW-13 (tıkanıklık) | CEP-2, NOK-6, SOS-3 | **Ayar → rol disiplini** |
| SW-14 (MQTT köprüleme) | PAN-6 | Yeniden tanımlandı |
| SW-15 (node haritası) | PAN-2 | Korundu |
| SW-16 (SOS izleme arayüzü) | PAN-1, PAN-3 | Detaylandırıldı |
| **Donanım Raporu** | | |
| K-1 (tek PCB iki varyant) | CEP / CEP+ | **Kapsamı daraldı** — NOKTA hariç |
| K-2 (nRF52840 + SX1262) | CEP-1 | **Yalnızca CEP için geçerli** |
| K-3 (sertifikalı modül) | — | Korundu |
| HW-INT-1…7 | — | CEP için korundu; HW-INT-3 → A-1 |
| HW-PWR-*, HW-RF-*, HW-UI-*, HW-MEC-* | — | CEP için tamamen korundu |
| **Farklılaşma Raporu** | | |
| AF-C1, AF-C2 | CEP-2, CEP-4 | Korundu |
| AF-N1…AF-N9 | NOK-1…NOK-10 | Korundu |
| AF-N10 | NOK-11 | Korundu |
| AF-P1…AF-P7 | POR-1…POR-7 | Korundu |
| AF-S1…AF-S5 | SOS-1…SOS-5 | Korundu |
| AF-D1…AF-D5 | PAN-1…PAN-5 | Korundu |
| AF-D6, AF-D7, AF-X1 | PAN-6, PAN-7 | Birleştirildi |
| İ-1…İ-4 | Bölüm 2 | Korundu |
| T-1…T-6 | TST-1…TST-6 | Korundu + TST-7 eklendi |

---

## 18. Değişiklik Geçmişi

| Sürüm | Tarih | Değişiklik |
|---|---|---|
| 1.0 | — | Gereksinim Analizi Raporu (ilk kapsam) |
| 1.1 | — | Donanım Gereksinim Raporu (donanım detayı) |
| **2.0** | **9 Ağu 2026** | Rekabet, mimari ve bakanlık analizleri birleştirildi. 14 çelişki çözüldü. Kamu node'u ayrı cihaz sınıfına alındı. Cihazsız erişim BLE'den WiFi portala taşındı. Gereksinim ID şeması birleştirildi |

---

> **Bu belge canlıdır.** Yeni bir karar alındığında Bölüm 12 (Açık Kararlar) güncellenir, ilgili gereksinim düzenlenir ve Bölüm 18'e kayıt düşülür. Araştırma raporları geriye dönük düzenlenmez.
