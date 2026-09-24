# CEP-T — Dokunmatik Kişisel Afet İletişim Cihazı: Tasarım Konsept Raporu

> ## 📎 BELGE STATÜSÜ: KONSEPT / ARAŞTIRMA RAPORU (v1.3)
>
> **✅ v1.3 (24 Eylül 2026) — A-7 kapandı:** Kullanıcı **dokunmatik ekranın zorunlu olmadığını** bildirdi. CEP-T = **nRF52840 (RAK4630) + 2,9" e-paper + 5 yön/Seç/Geri/SOS fiziksel butonları**. Belgedeki dokunmatik/ESP32-S3 içerikleri (Faz 0a T-Deck Plus, Faz 0b ILI9341+XPT2046, CT-K3, CT-N7, CT-A5, CT-A8, CT-T2) **tarihsel kayıt** olarak korunmuştur ve artık hedef tasarımı bağlamaz. Güncel prototip yolu: **Bölüm 7.2b**.
>
> **⚠️ v1.2 (24 Eylül 2026) — Donanım Raporu v2.0 ile hizalama:** Kullanıcının yeni birinci önceliği **uzun pil ömrü ve tasarruf modu** olduğu için işlemci kararı yeniden yapıldı ([`02_Donanim/Donanim_Gereksinim_Raporu.md`](../02_Donanim/Donanim_Gereksinim_Raporu.md) Bölüm 4). Sonuç: **nRF52840 önerilir; dokunmatik arayüz (MUI) nRF52'de desteklenmediği için CEP-T'nin arayüzü SGB açık kararı A-7'ye bağlandı.** Yol A (önerilen): nRF52840 + 2,9" e-paper + 5 yön/Seç/Geri/SOS butonları. Yol B: bu belgedeki ESP32-S3 + dokunmatik TFT (pil ömrü ~1–2 gün). Aşağıda CT-K1, CT-K2, CT-K4, CT-K6, CT-N1, CT-N2 ve Bölüm 7.3 buna göre güncellendi; eski metin gerekçesiyle birlikte korunmuştur.
>
> Bu belge **SGB'yi (00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md) değiştirmez.** CEP+ varyantının dokunmatik ekranlı, buton yedekli bir evrimi olarak sunulan bağımsız bir tasarım önerisidir. SGB'ye girmesi gereken herhangi bir karar, önce SGB'nin sahibiyle tartışılıp oraya işlenmelidir (repo kuralı: "Yeni bir gereksinim doğduğunda önce SGB'ye yazılır").
>
> Cihaz bu belgede **CEP-T** (CEP-Touch) olarak anılmaktadır — mevcut CEP (minimal, ekransız/e-ink) ve CEP+ (saha ekibi, e-ink) varyantlarından ayrı, dokunmatik+buton hibrit arayüzlü üçüncü bir kişisel node sınıfı.
>
> Kaynak: `Afet_Sonrası_İletişim_Sistemleri.pdf` (T.C. Çevre, Şehircilik ve İklim Değişikliği Bakanlığı, 2024) + harici araştırma (bkz. Bölüm 11 Kaynakça). Araştırma tarihi: 23 Eylül 2026.

---

## 1. Amaç ve Kapsam

Afet anında/sonrasında, elinde önceden hazırlık yapılmış özel bir cihaz bulunan bir kişinin (gönüllü, saha ekibi, evde bulunduran vatandaş) **kendi başına, internet ve GSM olmadan** başka insanlarla iletişim kurabildiği, tek elle taşınabilir bir cihaz tasarlamak.

Bakanlık kılavuzu (PDF) bu ihtiyacı **kurumlar arası telsiz altyapısı** (AKOM, ASELSAN, Motorola telsizleri, telsiz röleleri) düzeyinde ele alıyor — bireysel, kişisel taşınabilir bir cihaz tanımlamıyor. Japonya'nın 171 sistemi de GSM hattı üzerinden çalışıyor, GSM çöktüğünde işe yaramıyor. **Bu rapor, kılavuzun bıraktığı boşluğu** — AfetMesh'in kendi SGB'sinde zaten "Son 300 Metre Problemi" olarak adlandırdığı boşluğu — **kişisel, LoRa mesh tabanlı bir el cihazıyla** doldurmayı hedefliyor.

### 1.1 Kullanıcının belirttiği gereksinimler (girdi, ham liste)

- Kullanımı basit, ekranı sade ve anlaşılır
- Farklı koşullarda okunabilir ekran (güneşli, yağmurlu)
- Su ve toza karşı korumalı (kasa tasarımı sonraya bırakıldı, öncelik donanım/elektronik)
- Dokunmatik ekran
- Ekran bozulursa/dokunmatik çalışmazsa **fiziksel butonlarla tam arayüz gezinme** yedeği
- Sıcaklık, nem sensörü
- Gyro sensörü: (a) hareketle uyku modundan çıkma, (b) düşme algılama
- Telefona bağlanabilme (BLE)
- Harici GPS bağlanabilme
- LoRa ile uzun mesafe, çoklu node mesh
- Türkiye'de satılan hazır mesh cihazı varsa onu öner; yoksa benzerini yapacak malzemeleri bul
- Var olan benzer sistemleri araştır (Google Scholar, Reddit vb.), eksik/yanlış yapılan yerleri düzelt

---

## 2. Hazır Alternatif: Türkiye'de Satın Alınabilir mi?

Kısa cevap: Kullanıcının istediği tam kombinasyonu (dokunmatik + fiziksel buton yedeği + harici GPS + sıcaklık/nem + gyro düşme algılama + IP korumalı + Meshtastic mesh) karşılayan **hazır, tek parça bir ürün yok** — ne dünyada ne Türkiye'de. En yakın adaylar aşağıda; hiçbiri tüm gereksinimleri karşılamıyor, ama biri (**LILYGO T-Deck Plus**) donanım tabanı olarak kullanılabilir ve **Türkiye'de resmi distribütör üzerinden satılıyor.**

### 2.1 Hazır dokunmatik/LoRa mesh cihazları — karşılaştırma

| Cihaz | MCU + Radyo | Ekran | Dokunmatik | Fiziksel nav. yedeği | GPS | Sensör | IP | Fiyat | TR'de satılıyor mu |
|---|---|---|---|---|---|---|---|---|---|
| **LILYGO T-Deck** (temel) | ESP32-S3 + SX1262 (opsiyonel modül) | 2.8" LCD 320×240 | ❌ Yok | ✅ Trackball + fiziksel QWERTY klavye | ❌ Yok (eklenebilir) | — | Belirtilmemiş | ~$40-50 | ✅ motorobit.com (resmi distribütör), Amazon.com.tr |
| **LILYGO T-Deck Plus** | ESP32-S3 + SX1262 | 2.8" LCD 320×240 | ✅ GT911 kapasitif | ✅ Trackball + klavye (touch'a paralel) | ✅ Dahili | — | Belirtilmemiş (yok sayılabilir) | ~$45-70 | ✅ motorobit.com, Amazon.com.tr, teknobin.com |
| **LILYGO T-Watch S3/Plus** | ESP32-S3 + SX1262 | 1.54" 240×240 | ✅ Kapasitif | ❌ Tek fiziksel buton | S3 Plus'ta var | 3 eksen ivmeölçer, haptic | IP54 (sadece sıçrama) | — | Belirsiz |
| **Heltec Vision Master T190** | ESP32-S3R8 + SX1262 (opsiyonel) | 1.9" TFT 170×320 renkli | ✅ | — | ❌ | — | — | — | ❌ TR stok bulunamadı |
| **Heltec Vision Master E213** | ESP32-S3 + SX1262 | 2.13" E-Ink mono | Belirsiz | — | ❌ | — | — | — | ❌ |
| **Heltec Mesh Node T096** | **nRF52840** + SX1262 (28dBm PA) + UC6580 GNSS | 0.96" TFT (dokunmatiksiz) | ❌ | ❌ | ✅ Dahili çoklu-GNSS | Yok | — | $29.90 | ❌ TR stok bulunamadı |
| **RAK WisMesh Tap V2** | ESP32-S3 (RAK3312) + SX1262 | 320×240 TFT | ✅ + ekran klavyesi | ⚠️ Belirsiz (V1'de tek güç butonu) | ❌ | 3-eksen ivmeölçer (model belirsiz), buzzer, SD | **IP65** | Belirsiz (mağazadan teyit gerek) | ❌ TR stok bulunamadı |
| **Seeed SenseCAP Indicator** | ESP32-S3 + RP2040 + SX1262 | 3.95" 480×480 | ✅ | ❌ | ❌ | Grove genişleme | **IP65** | ~$48+ | ❌ TR stok bulunamadı |

**Değerlendirme:** T-Deck Plus, gereksinimlerin en büyük kısmını (dokunmatik + trackball/klavye fiziksel yedek + BLE + LoRa mesh) tek üründe karşılıyor ve **gerçekten Türkiye'de satılıyor** — motorobit.com kendini "LILYGO Resmi Türkiye Distribütörü" olarak tanımlıyor. Ancak: (1) GPS'i dahili, harici/takılabilir değil; (2) sıcaklık/nem/gyro sensörü yok; (3) IP koruma sınıfı belirtilmemiş; (4) ekranı standart TFT — güneşte/yağmurda okunabilirlik için tasarlanmamış. **Öneri: T-Deck Plus, hızlı prototip (Faz 0) donanım tabanı olarak kullanılsın; eksik gereksinimler (harici GPS, sensörler, IP koruma, güneş/yağmur okunabilirliği) Bölüm 5-7'de tarif edilen özel tasarımla Faz 3'te tamamlansın.** Bu, mevcut AfetMesh projesinin CEP donanım raporunda zaten benimsediği "önce hazır kartla doğrula, sonra özel PCB'ye geç" metodolojisiyle birebir örtüşüyor.

### 2.2 Ticari (Meshtastic-dışı) afet iletişim cihazları — başarı/başarısızlık dersleri

| Cihaz | Yaklaşım | Ders |
|---|---|---|
| **goTenna Mesh** (tüketici) | BLE mesh + telefon uygulaması | **2024'te tüketici hattı durduruldu.** 2017-2018'de bildirilen güvenlik açıkları eski cihazlarda hiç yamanmadı. Şirket, profesyonel/afet müdahale kullanıcılarının daha yüksek güç, uzun menzil ve sağlam kasa istediğini görüp goTenna Pro'ya (kurumsal hat) yöneldi — **tüketici donanımı gerçek afet müdahalesi için yetersiz kaldı.** *Ders: AfetMesh CEP-T'nin kasa/dayanıklılık gereksinimlerini (IP54+, düşme testi) hafife almamak gerekiyor; "tüketici seviyesi" yeterli olmayabilir.* |
| **Beartooth** | VHF/UHF + telefon BLE, iddia: 30 mil hat-görüşü menzil | Bağımsız saha testinde (panoplia.org), pazarlanan "4 mil hat-dışı" menzil gerçekte **~0.8 km'ye (yarım mil)** düştü. *Ders: SGB'nin zaten benimsediği SYS-7 ("menzil beyanları ölçülmüş değerlere dayanır, pazarlama rakamı kullanılmaz") ilkesi doğrulanıyor — CEP-T için de aynı disiplin uygulanmalı.* |
| **Somewear Labs** | LoRa mesh + Iridium uydu | Uydu veri hattı için **abonelik zorunlu** — goTenna/Beartooth'un aksine tek seferlik satın alma yetmiyor. *Ders: CEP-T'nin "abonelik yok, internet yok" ilkesi (SGB İ-1/İ-2) doğru bir farklılaşma noktası, korunmalı.* |
| **Zello** (uygulama, donanım değil) | Sadece internet/hücresel üzerinden çalışan telsiz-benzeri uygulama | 2017 Harvey Kasırgası'nda halk, uygulamanın internetsiz çalıştığını sanarak yanlış güvendi; Zello'nun kendi Twitter hesabı bunu kamuya düzeltmek zorunda kaldı. *Ders: CEP-T'nin arayüzünde ve pazarlamasında "internet gerektirmez" iddiası **sadece gerçekten gerektirmiyorsa** yapılmalı — POR-4/POR-10 (harici kaynak yok, internet erişimi verilmez) ilkesiyle tutarlı, ama kullanıcıya bunu **açıkça** göstermek (örn. ekranda sürekli "ÇEVRİMDIŞI ÇALIŞIYOR" göstergesi) yanlış beklenti oluşmasını önler — bu, CEP-T'ye yeni bir UI gereksinimi olarak eklenmeli (bkz. CT-UI-6).* |

### 2.3 Gerçek afet senaryosunda Meshtastic kullanımı — doğrulayan kanıt

**9 Eylül 2025 Berlin elektrik kesintisi** (kundaklama kaynaklı, ~60 saat, 25 yılın en uzun kesintisi, 3,7 milyon nüfuslu bölgeyi etkiledi): r/meshtastic üzerindeki ilk elden anlatımlara göre sıradan vatandaşlar, hücresel şebeke etkilendiğinde bağlantıda kalmak için Meshtastic kullandı; Alman yetkililerin bu olaydan afet hazırlığı dersleri çıkardığı bildiriliyor. **Bu, AfetMesh'in temel varsayımını (Meshtastic tabanının gerçek bir afette işe yaradığı) doğrulayan somut, güncel bir saha kanıtı.**

---

## 3. Akademik Literatür Bulguları

| # | Kaynak | Bulgu |
|---|---|---|
| 1 | Bridging Technical Capability and User Accessibility: Off-grid Civilian Emergency Communication (arXiv 2509.22568) | Zürih'te 10 node, ESP32-S3+SX1262, <0.4W ortalama güç, telefon-BLE köprüsü ile radyo karmaşıklığı kullanıcıdan gizleniyor. **868MHz LongFast: 1,2 km menzilde %92 paket teslim oranı** — 433MHz'i her metrikte geçti. Pil: 1 saatlik denemede bant farketmeksizin sadece %2-10 tüketim. **Eğitimsiz sivil kullanıcılarla System Usability Scale (SUS) = 74/100** — teknik olmayan kullanıcılar için kullanılabilir arayüz tasarımının mümkün olduğunun somut kanıtı. Kısıt: testler sadece iyi havada yapıldı, gerçek afette performans muhtemelen daha düşük; zorunlu ön-kayıt kimlik doğrulaması, spontane katılım için engel. |
| 2 | Resilience Analysis in Off-Grid LoRa Mesh Networks (arXiv 2605.17063) | Başlık doğrudan ilgili (spreading-factor/menzil ödünleşimi) ancak PDF içeriği otomatik araçla okunamadı — **kullanıcının bu makaleyi doğrudan okuması önerilir.** |
| 3 | "Meshtastic Example in Emergency Communication" (DergiPark, bilmes/1867445, ~Mayıs 2026, Türkçe kaynak) | 3 node, 433 ve 868 MHz'de test, çift yönlü iletişim + GPS takibi + WiFi köprüsü ile internete bağlanma. **Sonuç: Meshtastic'in Türkiye afet senaryosu için lisanssız ISM bantlarında uygulanabilir olduğunu doğruluyor.** |
| 4 | Centelles et al., "A LoRa-Based Communication System for Coordinated Response in an Earthquake Aftermath" (2019, UCAmI/MDPI) | Coquimbo, Şili (~28.000 kişi, 7.500 hane) senaryosu üzerinden vatandaş→yetkili durum bildirimi mimarisi modellendi. |
| 5 | Centelles/Meseguer, **LoRaMoto** (ScienceDirect, Future Generation Computer Systems) | LoRaWAN üzerine paket-yönlendirme (mesh-benzeri) ekleyerek önceden tanımlı kısa güvenlik mesajlarının spektrumu tıkamadan iletilmesini sağlıyor. **Kritik bulgu: şehir içi yoğun afet bölgesinde asıl darboğaz ham menzil değil, gateway/node yoğunluğu ve kapasitesi.** |
| 6 | Macaraeg et al., "LoRa-based Mesh Network for Off-grid Emergency Communications" (2020) | Değiştirilmiş AODV yönlendirme + RSSI metriği, yüksek trafik altında %99 başarılı mesaj teslimi (küçük ölçekli test — LoRaMoto'nun ölçeklenebilirlik endişesiyle doğrudan karşılaştırılamaz). |

### 3.1 Kritik risk: yoğun ağda Meshtastic ölçeklenme sorunu

Hacker News'te bulunan operatör yorumları (gerçek, büyük Meshtastic ağlarından): *"1 hop'un ötesi genelde kullanılamaz hale geliyor"*; Slovenya bölgesinde yüzlerce node'lu ağda *"kanal bant genişliğinin çoğu node'lar arası sinyalizasyonla tükeniyor"*; NYC ağı bir yılda 1000+ node'a büyüdü ama *"herkes sadece test amaçlı konuşuyor, söylenecek ilginç bir şey kalmıyor"*. Bir yorumcu, çoğu node'un varsayılan olarak paket tekrarlaması (router rolü) tasarımını suçluyor — Meshtastic istemcilerini *"kamp/yürüyüş için gürültülü bir protokol"* olarak tanımlıyor, **büyük ölçekli afet yoğunluğunda kanıtlanmamış.**

Bu bulgu, **LoRaMoto makalesinin bağımsız bulgusuyla örtüşüyor** (darboğaz menzil değil, yoğunluk) ve **AfetMesh'in SGB'sinde zaten var olan CEP-2 (`CLIENT_MUTE`, tekrarlama kapalı) kararını güçlü şekilde doğruluyor.** CEP-T için doğrudan sonucu: **CEP-T de varsayılan olarak `CLIENT_MUTE` rolünde olmalı** — deprem sonrası bir toplanma alanında yüzlerce CEP-T aynı anda paket tekrarlarsa ağ tıkanır. Bu konuya Bölüm 6'da CT-SYS-1 olarak girildi.

---

## 4. Tasarım Kararları

| # | Karar | Seçim | Gerekçe |
|---|---|---|---|
| **CT-K1** ✅ | MCU + Radyo | **v1.3: nRF52840 + SX1262 (RAK4630) — kesin** (A-7 kapandı). ~~Yol B: ESP32-S3~~ | **v1.2 notu:** Ölçülmüş saha verisinde ESP32-S3 el cihazları 10–30 saat, nRF52840 cihazları 100–450 saat dayanıyor (Donanım Raporu Bölüm 4.3). Pil ömrü birinci öncelik olduğu için Yol A önerilir; aşağıdaki v1.1 gerekçesi dokunmatik zorunlu tutulursa (Yol B) geçerlidir. — *v1.1 gerekçesi:* Mevcut CEP'in nRF52840 kararından **bilinçli sapma.** Bugün var olan **her** dokunmatik-ekranlı Meshtastic ürünü (T-Deck Plus, Heltec Vision Master, RAK WisMesh Tap, SenseCAP Indicator) ESP32-S3 kullanıyor — kapasitif dokunmatik + grafik arayüz + PSRAM ekosistemi burada olgunlaşmış. nRF52840 tarafında BLE+LoRa+GNSS birlikteliği kanıtlanmış (Heltec T096, 13µA uyku) ama **dokunmatik ekranla kanıtlanmış tek bir ürün bulunamadı** — 256KB RAM'in dokunmatik UI grafik render'ı için yeterli olup olmadığı belirsiz, mühendislik riski yüksek. ESP32-S3'ün WiFi radyosu **kullanılmayacak** (bu cihazın WiFi'a ihtiyacı yok — captive portal görevi NOKTA'nın), böylece BLE/WiFi çakışma riski de ortadan kalkar. |
| **CT-K2** ⚠️ | Ekran teknolojisi | **v1.2 — Yol A (önerilen): 2,9" e-paper (SSD1680, InkHUD), dokunmatiksiz; güneşte okunur, görüntüyü korumak için güç harcamaz.** Yol B için v1.1 metni: **Faz 0a (hazır kart): standart kapasitif TFT (T-Deck Plus, GT911). Faz 0b (DIY/perfboard, tercih edilen): 2.4" ILI9341 TFT + XPT2046 rezistif dokunmatik (hazırda mevcut parça). Faz 3 hedef: transflektif TFT + su-reddi modlu kapasitif dokunmatik** | Güneşte okunabilirlik + düşük güç + dokunmatik + yağmur toleransı **hepsi birden** tek bir hazır, Türkiye'de satılan üründe bulunamadı. Maliyet gerekçesiyle Faz 0'da DIY yola geçildi (bkz. CT-K9). Transflektif panel + su-reddi dokunmatik parçaları (Topfoison TF-LCM16003A-C-S0, VIEWE serisi, Crystalfontz) gerçek ve mevcut ama **hiçbiri Türkiye'de stoklu değil** — Faz 3'e ertelendi. |
| **CT-K3** | Dokunmatik + yağmur sorunu | **Fiziksel buton tam yedek, zorunlu (kullanıcı gereksinimi zaten buydu)** | Standart kapasitif dokunmatik paneller suyla "köprü" oluşturup hayalet dokunuş üretir — bu **genel, doğrulanmış bir mühendislik gerçeği**. **Faz 0b'de kullanılan XPT2046 rezistif dokunmatik, kapasitans değil fiziksel basınçla çalıştığı için bu sorunu farklı bir yoldan azaltıyor** (ıslak parmak/eldiven suyu "dokunuş" olarak yanlış algılamaz) — CT-N7'nin pratik bir kısmi çözümü, ama hassasiyeti kapasitife göre düşük. Endüstriyel su-reddi modlu **kapasitif** kontrolörler (Cypress/Infineon TrueTouch, FocalTech, Goodix) hedef tasarımda hâlâ değerlendirilecek. **Kullanıcının "ekran bozulursa buton yedeği olsun" isteği, aslında yağmur senaryosunda günlük bir ihtiyaç haline geliyor** — bu tasarımın en isabetli kararı. |
| **CT-K9** | Faz 0 donanım yolu | **DIY/perfboard: ayrık ESP32-S3 DevKitC-1 + SX1262 breakout modül (Ebyte E22 sınıfı) + kullanıcının elindeki 2.4" ILI9341+XPT2046 dokunmatik TFT** | T-Deck Plus (~$90-140/2 adet) yerine tercih edildi — maliyet gerekçesiyle. Ayrık modül yaklaşımı mevcut CEP donanım raporunun K-3 ilkesiyle (çıplak çip değil, hazır/antenli modül) uyumlu: SX1262 **breakout kartı** (anten eşlemesi üzerinde hazır) alınıp sadece SPI hattı pertinaks üzerinde el ile kablolanıyor, RF riski taşınmıyor. Toplam maliyet ~$40-50/2 node — T-Deck'in dörtte biri. Bu yol aslında mevcut projenin kendi "Aşama 0: hazır kartlarla, ~sıfır maliyetle önce varsayımı doğrula" ilkesine T-Deck Plus'tan daha sadık. |
| **CT-K4** | GPS | **Harici, takılabilir, güç anahtarlı UART modül** (kullanıcı isteği zaten buydu). **v1.2:** Hedef tasarımda **u-blox MAX-M10S** (~25 mW); NEO-6M yalnızca breadboard testi için (eski nesil, ~45 mA) | Mevcut CEP+'nın HW-SNS-1 kararıyla (GPS güç anahtarı arkasında, sürekli açık kalırsa pil ömrünü tek başına tüketir) birebir örtüşüyor. T-Deck Plus'ın dahili GPS'i Faz 0'da kullanılabilir; harici/takılabilir mimari Faz 3'te doğrulanmalı. |
| **CT-K5** | Sensör: sıcaklık/nem | **v1.2: SHT40** (BME280 alternatif, basınç gerekirse) | Mevcut CEP+ ile aynı parça, Türkiye'de robotistan.com/direnc.net'te stoklu, Meshtastic telemetri modülü doğrudan destekliyor. |
| **CT-K6** ⚠️ | Sensör: gyro + ivmeölçer | **v1.2: LSM6DS3TR-C** (6 eksen) — ~~BMI160~~ | **v1.2 düzeltmesi: BMI160 Meshtastic'in desteklediği sensörler listesinde yoktur**; LSM6DS3 desteklidir ve donanımsal wake-up/free-fall kesmesi vardır. Breadboard'da MPU6050 (destekli) kullanılabilir. — *v1.1 metni:* direnc.net'te ~71 TL stoklu, düşük güç. MPU6050 de stoklu (~69 TL) ama daha eski/yüksek güçlü — hızlı prototipte MPU6050 kabul edilebilir, hedef tasarımda BMI160. LIS3DH (mevcut CEP+'ta önerilen) sadece ivmeölçer, ayrı gyro gerektirir — bu cihaz için 6 eksenli tek çip daha basit. |
| **CT-K7** | Mesh rolü | **`CLIENT_MUTE`** (varsayılan, CEP-2 ile aynı) | Bölüm 3.1'deki ölçeklenme riski nedeniyle zorunlu. |
| **CT-K8** | BLE tek-telefon kısıtı | **Kabul edilir, değiştirilmez** | Meshtastic firmware issue #526: bu bir donanım kısıtı değil, `MeshService` singleton'ının firmware mimarisi kararı. Ancak CEP-T zaten **tek sahipli kişisel cihaz** (CEP-5 ile aynı varsayım) — bu kısıt bu kullanım senaryosunda sorun değil. Not olarak düşülür: ileride çok-telefonlu senaryo istenirse `meshtastic-ble-gateway` / MQTT Client Proxy gibi topluluk çözümleri mevcut (internet gerektirir, offline senaryoda kullanılmaz). |

---

## 5. Sistem Mimarisi

### 5.1 Donanım Mimarisi

```
                         ┌── LoRa SMA anten (433/868 MHz, SGB A-1 kararına bağlı) ──┐
                         │                                                          │
        ┌────────────────┴─────────┐                                               │
        │   ESP32-S3 (N16R8: 16MB  │◄── SPI ── SX1262 (sertifikalı, TCXO'lu) ──────┘
        │   flash, 8MB PSRAM)      │
        │   WiFi radyosu KAPALI    │◄── BLE (peripheral, telefon eşleştirme)
        └──┬──────┬──────┬─────┬───┘
           │      │      │     │
     ┌─────┴──┐ ┌─┴────┐┌┴────┴──────┐ ┌──────────────┐
     │ I2C:   │ │ I2C: ││ UART:       │ │ GPIO (kesme): │
     │ Dokun- │ │ BME  ││ Harici GPS  │ │ 5 yön buton + │
     │ matik  │ │ 280  ││ konnektör   │ │ Back + SOS    │
     │ (GT911)│ │ BMI  ││ (güç anah-  │ │ (silikon      │
     │        │ │ 160  ││ tarlı, JST) │ │ membran)      │
     └────────┘ └──────┘└─────────────┘ └──────────────┘
           │
     ┌─────┴──────────────────┐
     │ Transflektif TFT panel │ (Faz 3 hedef; Faz 0'da T-Deck Plus standart TFT)
     └─────────────────────────┘

   ┌──────────────── GÜÇ (mevcut CEP mimarisiyle aynı bileşenler) ──────────────┐
   │ USB-C → BQ24074 (power-path) → 18650 (yüksek kapasite, ≥3000mAh,          │
   │   değiştirilebilir, korumalı: DW01A+FS8205A) → TPS7A0233 LDO → 3.3V       │
   │ MAX17048 fuel-gauge · USBLC6-2SC6 ESD koruması                            │
   └──────────────────────────────────────────────────────────────────────────┘
```

### 5.2 Alt sistem gerekçeleri

- **BMI160 kesme pini doğrudan ESP32-S3'ün wake-on-GPIO girişine bağlanır** — hareket algılandığında MCU derin uykudan çıkar (kullanıcının "gyro ile uyku modundan çıkma" isteği). Bu, ekranı sürekli açık tutmaktan çok daha düşük güç maliyetiyle çözülür.
- **Düşme algılama aynı BMI160'ın hem ivmeölçer hem gyro verisini birlikte kullanır** (bkz. Bölüm 5.3, yazılım tarafı) — sadece ivmeölçer kullanan sistemler çantaya düşürülme gibi günlük hareketleri yanlış pozitif olarak algılıyor; gyro ile açısal hız verisi bu yanlış pozitifleri azaltmak için literatürde standart yöntem.
- **GPS UART hattı, ayrı bir MOSFET/yük anahtarı üzerinden güçlendirilir** (mevcut CEP+ HW-SNS-1 ile aynı desen) — GPS modülü fiziksel olarak takılı kalsa bile yazılım GPS'i tamamen kesebilir.
- **Dokunmatik kontrolör (GT911, Faz 0) ile fiziksel butonlar aynı girdi soyutlama katmanına (bkz. 5.3) bağlanır** — ikisi de aynı UI state machine'i sürer, biri arızalansa diğeri tam işlevsel kalır.

### 5.3 Yazılım Mimarisi

```
┌─────────────────────────────────────────────────────────────┐
│  Meshtastic firmware (upstream, PRIVATE_HW board variant)    │
│  ├── LoRa mesh stack (SX1262 sürücü, CLIENT_MUTE rolü)        │
│  ├── BLE PhoneAPI (tek istemci — CT-K8 kararı)                │
│  └── Telemetri modülü (BME280 verisi)                         │
├─────────────────────────────────────────────────────────────┤
│  CEP-T Özel Modülleri (SYS-4: çekirdek koda dokunmadan,        │
│  ayrı modül olarak)                                            │
│  ├── Girdi Soyutlama Katmanı                                   │
│  │     dokunmatik olayları + buton olayları → TEK ortak        │
│  │     "UI event" tipine dönüştürülür (yön/seç/geri/SOS)       │
│  │     → Ekran arızasında UI kodu HİÇ değişmeden buton ile     │
│  │       tam gezinme sağlanır                                  │
│  ├── Düşme Algılama Modülü                                     │
│  │     1) Serbest-düşüş fazı: 3 eksende toplam ivme < eşik     │
│  │        (LFT — Alt Düşüş Eşiği)                               │
│  │     2) Çarpma fazı: kısa süre içinde ani ivme sıçraması      │
│  │        eşiği aşılır (UFT — Üst Düşüş Eşiği)                  │
│  │     3) Gyro doğrulama: büyük, kontrolsüz yönelim değişimi   │
│  │        (bir cismin çantada sallanmasından ayırt etmek için) │
│  │     → SOS-benzeri "Enkaz Altında Olabilir" paketi otomatik  │
│  │       tetiklenir, kullanıcı 30 sn içinde iptal edebilir      │
│  ├── Güç Yönetimi Durum Makinesi                                │
│  │     Ekran zaman aşımı → uyku · BMI160 kesmesi → uyanma       │
│  │     GPS varsayılan KAPALI, kullanıcı açık/kapalı seçer       │
│  ├── Harici GPS Sürücüsü (UART, güç anahtarlı, NMEA parse)      │
│  └── "ÇEVRİMDIŞI ÇALIŞIYOR" durum göstergesi (CT-UI-6)          │
└─────────────────────────────────────────────────────────────┘
```

**Kritik tasarım ilkesi (SYS-4 ile uyumlu):** Yukarıdaki tüm CEP-T özel modülleri, Meshtastic'in çekirdek mesh/yönlendirme koduna **dokunmadan** ayrı katmanlar olarak yazılır — bu hem upstream ile uyumu korur hem de SOS-5 (standart Meshtastic node'ların paketi taşıyabilmesi) gereksinimini garantiler.

---

## 6. Alt Gereksinimler

### 6.1 İşlevsel

| # | Gereksinim |
|---|---|
| **CT-F1** | Cihaz Meshtastic firmware çalıştıracak; MCU ESP32-S3, radyo SX1262 |
| **CT-F2** | Varsayılan rol `CLIENT_MUTE` olacak (bkz. CT-K7, Bölüm 3.1) |
| **CT-F3** | Ekran dokunmatik olacak; **aynı zamanda** 5 yönlü buton + Seç + Geri + ayrı SOS butonu ile **ekranın tamamı gezinip kontrol edilebilecek** |
| **CT-F4** | Dokunmatik veya buton donanımından biri tamamen arızalansa bile diğeri üzerinden tüm işlevlere (mesaj gönderme, SOS, ayarlar) erişilebilecek |
| **CT-F5** | Sıcaklık ve nem ölçülüp telemetri paketiyle ağa iletilebilecek |
| **CT-F6** | Hareket algılandığında (gyro/ivmeölçer eşiği aşıldığında) cihaz uyku modundan otomatik çıkacak |
| **CT-F7** | Düşme/çarpma algılandığında otomatik "Enkaz Altında Olabilir" acil paketi hazırlanacak; kullanıcı 30 saniye içinde iptal edemezse gönderilecek |
| **CT-F8** | Telefonla BLE üzerinden eşleşilebilecek (tek istemci, CEP-5 ile aynı varsayım) |
| **CT-F9** | Harici GPS modülü takılabilir/çıkarılabilir olacak; takılıyken konum bilgisi mesaja eklenebilecek |
| **CT-F10** | LoRa üzerinden çoklu node ile mesh iletişimi kurulabilecek, mevcut Meshtastic ağlarıyla (TA Mesh dâhil) birlikte çalışabilecek (SYS-3 ile aynı) |

### 6.2 İşlevsel olmayan

| # | Gereksinim | Hedef | Not |
|---|---|---|---|
| **CT-N1** | Pil ömrü (ekran çoğunlukla kapalı, LoRa sürekli açık, GPS kapalı) | **v1.2: Yol A ≥ 14 gün** (SGB CEP-N10) · Yol B ≥ 48 saat | CEP'in 72 saat hedefinden **bilinçli olarak düşük** — gerçek dünya verisi (T-Deck Plus, 2000mAh: GPS+ekran açıkken 1-2 gün) dokunmatik+GPS'in pil bütçesini CEP'ten farklılaştırdığını gösteriyor. SYS-7 ilkesi gereği rakam ölçülene kadar **hedef**, iddia değil. |
| **CT-N2** | Kapalı (raf) modu akımı | **v1.2: ≤ 10 µA** (SGB CEP-N3; eski hedef < 150 µA) | CEP'in <100µA hedefinden gevşetildi (dokunmatik kontrolör + daha büyük RAM/flash payı) |
| **CT-N3** | Pil | ≥ 3000mAh 18650, kullanıcı tarafından değiştirilebilir | Mevcut CEP mimarisiyle aynı felsefe (HW-PWR-1), daha yüksek kapasite |
| **CT-N4** | Koruma sınıfı | IP54 asgari (CEP-N5 ile aynı) | Kasa tasarımı bu raporun kapsamı dışı (kullanıcı isteği), ama elektronik bu sınıfa **uyumlu** seçilmeli (bkz. CT-N7) |
| **CT-N5** | Çalışma sıcaklığı | −10°C … +50°C (CEP-N6 ile aynı) | |
| **CT-N6** | Düşme dayanıklılığı | 1,5 m betona düşme (CEP-N9 ile aynı) | |
| **CT-N7** | Dokunmatik kontrolör su-reddi | Faz 0b'de **rezistif (XPT2046) dokunmatik** ile kısmen azaltılıyor (bkz. CT-K3); Faz 3 hedef tasarımda kapasitif kullanılacaksa **su-reddi modlu** IC (TrueTouch/FocalTech/Goodix sınıfı) zorunlu | Rezistif panel su-köprüsü sorununu yaşamaz ama hassasiyeti düşük; GT911 gibi kapasitif kontrolörlerin yağmurdaki davranışı hâlâ doğrulanamadı — açık risk (bkz. Bölüm 10) |
| **CT-N8** | Düşme algılama yanlış-pozitif oranı | Normal taşıma/çanta kullanımında < %5 yanlış tetikleme (saha testiyle kalibre edilecek) | Literatür: en olgun ticari ürünler bile ~%80 gerçek-dünya doğruluğuna ulaşıyor — %100 beklenmemeli, kullanıcı iptal penceresi (CT-F7) bu yüzden zorunlu |

---

## 7. Prototip Malzeme Listesi (BOM)

### 7.1 Faz 0a — Hazır Entegre Kart Yolu (T-Deck Plus, ~sıfır tasarım riski, daha pahalı)

| Parça | Model | Yaklaşık fiyat | Türkiye temin |
|---|---|---|---|
| Ana gövde (MCU+radyo+ekran+dokunmatik+trackball+dahili GPS) | **LILYGO T-Deck Plus** | $45-70 | ✅ motorobit.com, Amazon.com.tr, teknobin.com |
| Sıcaklık/nem sensörü | BME280 (I2C breakout) | ~$2-3 | ✅ robotistan.com, direnc.net |
| Gyro + ivmeölçer | BMI160 veya MPU6050 (I2C breakout) | ~68-71 TL | ✅ direnc.net |
| Harici GPS (T-Deck Plus'ın dahili GPS'ine ek/alternatif, GPIO üzerinden test için) | u-blox NEO-6M (GY-NEO6MV2 breakout) | — | ✅ direnc.net (ürün kodu K1C-9) |
| SOS butonu (harici, T-Deck'in mevcut butonlarına ek) | 6×6mm taktil switch | ~$0.2 | ✅ genel elektronik satıcıları |

**Not:** T-Deck Plus'ın GPS'i dahili — bu kabul edilip "harici/takılabilir GPS" mimarisi ayrı bir breadboard/GPIO deneyiyle doğrulanır (CT-F9'un erken doğrulaması). IP koruma ve su-reddi dokunmatik, bu yolun kapsamı dışıdır (hazır kartın kasası zaten açık).

### 7.2 Faz 0b — DIY / Perfboard Yolu (tercih edilen, düşük maliyet, CT-K9)

Kullanıcının elinde delikli pertinaks ve bir adet **2.4" ILI9341 + XPT2046 dokunmatik TFT** bulunduğu için bu yol seçildi. Toplam maliyet, T-Deck Plus yolunun (2 adet için ~$90-140) yaklaşık dörtte biri (~$40-50/2 node).

| Parça | Adet | Model/Öneri | Not |
|---|---|---|---|
| MCU geliştirme kartı | 2 | **ESP32-S3 DevKitC-1** (N16R8 tercihen) | Hazır regülatörlü, USB-C'li — MCU devresini kendiniz kurmayın |
| LoRa radyo modülü | 2 | **SX1262 breakout kartı, anten konnektörlü** (Ebyte E22-900M22S/M30S sınıfı) | Çıplak çip değil, **antenli/eşlemeli hazır modül** — CT-K9 gerekçesi |
| Dokunmatik TFT ekran | 1 (kullanıcıda mevcut) | 2.4" ILI9341 + XPT2046 (SPI) | En az bir cihaza takılıp UI/buton-yedek konsepti test edilir; ikinci node ekransız da olabilir |
| Anten | 2 | SMA whip **veya** tel monopol (433 MHz: ~16,4 cm; 868 MHz: ~8,2 cm) | Tel anten masa üstü/ilk menzil testi için yeterli |
| Sıcaklık/nem sensörü | 1 | BME280 (I2C breakout) | ✅ robotistan.com, direnc.net |
| Gyro + ivmeölçer | 1 | BMI160 veya MPU6050 (I2C breakout) | ✅ direnc.net, ~68-71 TL |
| Harici GPS | 1 | u-blox NEO-6M (GY-NEO6MV2) | ✅ direnc.net, ürün kodu K1C-9 |
| Taktil buton | 4-6 | 6×6mm | Yön/Seç/Geri/SOS deneme seti |
| Pin header (erkek+dişi soket şeridi) | 1-2 şerit | — | Modülleri lehimlemeyin, soketleyin — hata durumunda sökülebilir |
| İnce bağlantı kablosu | 1 makara | Wire-wrap tel veya jumper | SPI/I2C hatları için |
| USB-C inline güç ölçer | 1 | — | Gerçek akım tüketimini ölçmek için (SYS-7 ilkesi) |

**SPI kablolama — LoRa modülü:**

| SX1262 modül pini | ESP32-S3 |
|---|---|
| VCC | 3.3V |
| GND | GND |
| MOSI / MISO / SCK | SPI bus (ekranla paylaşılır, örn. GPIO 11/13/12) |
| NSS / CS | Ayrı GPIO (örn. 10) |
| RESET | GPIO (örn. 9) |
| BUSY | GPIO (örn. 8) |
| DIO1 | GPIO (örn. 14) |

**SPI kablolama — 2.4" ILI9341 + XPT2046 ekran:**

| Ekran/dokunmatik pini | ESP32-S3 |
|---|---|
| VCC / GND | 3.3V / GND |
| CS / RESET / DC | Ayrı GPIO'lar (örn. 15/16/17) |
| SDI(MOSI) / SCK / SDO(MISO) | LoRa modülüyle **aynı** SPI bus (yalnızca CS pinleri farklı) |
| LED (arka ışık) | GPIO (PWM ile parlaklık kontrolü opsiyonel) |
| T_CS | Ayrı GPIO (örn. 4) — dokunmatik kendi chip-select'ine sahip |
| T_CLK / T_DIN / T_DO | Ana SPI hattıyla paylaşılır |
| T_IRQ | GPIO (örn. 2, opsiyonel) |

**Yazılım:** Ekran için **TFT_eSPI**, dokunmatik için **XPT2046_Touchscreen** kütüphaneleri (Arduino/PlatformIO) kullanılır — sıfırdan sürücü yazılmaz. LoRa/mesh tarafı için Meshtastic firmware'inde `variants/` altına yukarıdaki pin haritasını tanımlayan bir `variant.h` + `platformio.ini` girdisi eklenir, donanım modeli `PRIVATE_HW` olur (mevcut CEP donanım raporu HW-INT-6 ile aynı yöntem) — mesh/yönlendirme/BLE kodu sıfırdan yazılmaz, sadece pin ataması tanımlanır.

### 7.2b Faz 0b-nRF — Yol A için DIY yolu (v1.2, önerilen)

Meshtastic'in **resmî DIY InkHUD tarifi** doğrudan bu yolu destekliyor: **nRF52840 Pro Micro (~$6) + SX1262 modül (HT-RA62 / E22-xxxM22S / Wio-SX1262) + WeAct 2,9" e-paper (SSD1680)** + 5–7 taktil buton. Pin haritası `nrf52_promicro_diy_tcxo` varyantında hazır; ekran bağlantısı meshtastic.org *DIY InkHUD Builds* sayfasında. Maliyet 2 node için ~$40–50 (Faz 0b ile aynı bant) ve **Yol A'nın pil ömrünü PCB'den önce ölçmeyi** sağlar. ⚠️ Pro Micro'lar lehimlenmeden önce test edilmeli (bootloader eksik/eski çıkabiliyor).

### 7.3 Faz 3 — Hedef Tasarım (özel PCB adayı parçalar)

> **v1.2:** Bu tablonun güncel ve ayrıntılı hâli [`02_Donanim/Donanim_Gereksinim_Raporu.md`](../02_Donanim/Donanim_Gereksinim_Raporu.md) **Bölüm 6**'dadır. Aşağıdaki satırlar Yol A için güncellenmiştir.

| Alt sistem | Parça adayı | Not |
|---|---|---|
| MCU | **RAK4630 (nRF52840 + SX1262, L=433 / H=868)** | A-7 kapandı |
| LoRa radyo | Sertifikalı SX1262 modülü + TCXO (band kararı SGB A-1'e bağlı) | Mevcut CEP donanım raporundaki K-3 ilkesiyle aynı: çıplak çip değil, sertifikalı modül |
| Ekran | **Yol A: 2,9" e-paper SSD1680 (InkHUD)** · Yol B: Transflektif TFT + kapasitif dokunmatik, örn. Topfoison TF-LCM16003A-C-S0 sınıfı (1.6", 320×320, 500cd/m²) | Türkiye'de stoklu değil, doğrudan üretici siparişi (muhtemel MOQ) |
| Dokunmatik kontrolör | Su-reddi modlu IC (TrueTouch/FocalTech/Goodix sınıfı) | Açık karar — ekran tedarikçisiyle birlikte netleştirilmeli |
| Sıcaklık/nem | SHT40 (alt. BME280) | CEP+ ile ortak parça |
| Gyro+ivmeölçer | LSM6DS3TR-C | BMI160 Meshtastic destekli değil (v1.2) |
| GNSS | Kartta MAX-M10S + harici JST-SH 4 pin konnektör (3V3_SW, GND, TX, RX) + TPS22917 yük anahtarı | Harici modül takılabilir (CT-F9) |
| Fiziksel navigasyon | 5 yönlü + Seç + Geri: 6×6mm taktil switch + silikon membran kapak | Mevcut CEP HW-UI deseniyle aynı |
| SOS butonu | Yanlış basmaya karşı korumalı (gömme/kapaklı) | CEP-3 ile aynı |
| Şarj + power-path | BQ24074 | CEP ile ortak parça |
| Regülatör | **TPS63900 buck-boost** (v1.2; LDO soğukta brownout riski) | CEP ile ortak parça |
| Pil koruma | DW01A + FS8205A | CEP ile ortak parça |
| Pil ölçümü | MAX17048 | CEP ile ortak parça |
| Pil | 18650, ≥3000mAh | CEP'ten daha yüksek kapasite (CT-N3) |
| Anten | SMA dişi bulkhead, 50Ω | CEP ile aynı yaklaşım |

---

## 8. Test ve Doğrulama Planı

### 8.1 Faz 0b Devreye Alma (Bring-up) Sırası — PCB/entegrasyon öncesi

Perfboard yolunda (CT-K9), aşağıdaki sıra riski en yüksek varsayımdan başlayıp aşağı iner; SGB'nin kendi Aşama 0 mantığıyla (TST-1/2/5) aynı disiplin:

| # | Adım | Amaç |
|---|---|---|
| **CT-T0-1** | Sadece MCU'yu getir (LoRa/ekran takılı değil), USB üzerinden basit seri çıktı al | Lehimleme/kart sağlamlığını LoRa'yı karıştırmadan doğrula |
| **CT-T0-2** | SX1262 modülünü ekleyip SPI üzerinden chip-ID okuma testi (RadioLib veya benzeri, Meshtastic firmware'i değil) | "Lehimleme doğru mu" sorusuna, mesh yazılımını devreye sokmadan cevap ver |
| **CT-T0-3** | Ekran + dokunmatiği LoRa'dan bağımsız, tek başına bağlayıp TFT_eSPI ile metin çizdir, XPT2046 ile dokunuşu seri porta bastır | Sorun çıkarsa kaynağın ekran mı LoRa mı olduğunu karıştırma |
| **CT-T0-4** | `PRIVATE_HW` variant tanımlayıp Meshtastic firmware'ini iki karta da derle/flaşla | |
| **CT-T0-5** | İki kart arası doğrudan LoRa mesaj testi (masa üstü, artan mesafe) | **En kritik adım** — tüm mimarinin dayandığı temel varsayımı doğrular |
| **CT-T0-6** | TA Mesh'teki standart bir Meshtastic node'uyla birlikte çalışabilirlik | Bant kararının (A-1) doğru olduğunu saha koşulunda teyit et |
| **CT-T0-7** | Dokunmatiği yazılımsal kapatıp sadece butonlarla menüde gezinme | CT-F3/F4'ün ilk gerçek denemesi |
| **CT-T0-8** | USB-C güç ölçerle uyku/aktif akım taban ölçümü | CT-N1/N2 hedeflerinin gerçekçi olup olmadığını erken gör |

Bu adımlar tamamlanmadan aşağıdaki CT-T1-13 tablosundaki daha olgun/entegre testlere geçilmez.

### 8.2 Ana Test ve Doğrulama Planı

| # | Test | Yöntem | Beklenen/Başarı Kriteri |
|---|---|---|---|
| **CT-T1** | Dokunmatik/buton fonksiyonel eşdeğerlik | Dokunmatik kapatılmış durumda, sadece butonlarla tüm menüler (mesaj gönderme, SOS, ayarlar, GPS aç/kapa) gezilir | Hiçbir işleve erişim kaybı olmayacak |
| **CT-T2** | Yağmur altında dokunmatik davranışı | Cihaz su püskürtülürken dokunmatik ile gezinme denenir (Faz 0b'de rezistif XPT2046, Faz 3'te hedef kapasitif panel ayrı ayrı test edilmeli) | Hayalet dokunuş / kilitlenme oranı ölçülür ve raporlanır (SYS-7); rezistif panelde daha az, kapasitifte daha fazla bozulma beklenir — CT-T1'in bu senaryoda **zorunlu** yedek olduğu her iki teknolojide de doğrulanır |
| **CT-T3** | Düşme algılama — gerçek düşme | Cihaz çeşitli yükseklik/açılarda kontrollü düşürülür | "Enkaz Altında Olabilir" paketi tetiklenme oranı ölçülür, hedef ≥ %80 (literatür referansı) |
| **CT-T4** | Düşme algılama — yanlış pozitif | Cihaz normal taşıma, çanta içinde yürüme, masaya bırakma senaryolarında 24 saat taşınır | Yanlış tetikleme oranı < %5 (CT-N8) |
| **CT-T5** | Hareketle uyanma | Cihaz uykudayken hafif sarsılır/kaldırılır | Ekran/sistem < 1 sn içinde uyanacak |
| **CT-T6** | GPS takma/çıkarma | GPS modülü çalışırken takılıp çıkarılır, güç anahtarı test edilir | Modül yokken sistem akımı GPS'siz seviyeye düşecek (güç anahtarının çalıştığı doğrulanır) |
| **CT-T7** | BLE telefon eşleştirme | Bir telefon eşleştirilir, ikinci telefon bağlanmaya çalışır | CT-K8 kararı gereği: ikinci bağlantı reddedilecek veya ilkini düşürecek (beklenen, Meshtastic mimarisi gereği) |
| **CT-T8** | Mesh birlikte çalışabilirlik | CEP-T, standart bir Meshtastic node'uyla (TA Mesh dâhil) mesajlaşır | Karşılıklı mesaj iletimi başarılı (SOS-5/SYS-3 ile aynı) |
| **CT-T9** | Pil ömrü — gerçekçi kullanım | Ekran çoğunlukla uykuda, LoRa açık, GPS kapalı, günde birkaç mesaj | ≥ 48 saat (CT-N1); ölçülen değer SYS-7 gereği raporlanır, pazarlanmaz |
| **CT-T10** | Uyku akımı | Multimetre ile ölçüm | < 150 µA (CT-N2) |
| **CT-T11** | Kalabalık ağda tıkanıklık | ≥ 20 CEP-T + mevcut TST-4 senaryosuna benzer yoğunluk testi | CLIENT_MUTE rolünün paket tekrarlamadığı, ağın tıkanmadığı doğrulanır (Bölüm 3.1 riskine karşı) |
| **CT-T12** | Su sıçratma / toz (elektronik seviyesinde, kasa hariç) | Açık PCB üzerinde konformal kaplama sonrası temel dayanıklılık | Kısa devre/arıza olmayacak (kasa entegrasyonu bu raporun kapsamı dışı) |
| **CT-T13** | Kullanılabilirlik — teknik olmayan kullanıcı | 8-10 gönüllüye, yönlendirmesiz, hem dokunmatik hem buton-only modda görev verilir (mevcut TST-6 metodolojisiyle aynı) | Zürih çalışmasındaki SUS=74/100 referans alınarak karşılaştırılabilir bir skor hedeflenir |

---

## 9. İzlenebilirlik Matrisi (Gereksinim → Test)

| Gereksinim | Doğrulayan test |
|---|---|
| CT-K9 (DIY donanım yolunun sağlamlığı) | CT-T0-1, CT-T0-2, CT-T0-3 |
| CT-F10, CT-F2 (temel mesh varsayımı) | CT-T0-4, CT-T0-5, CT-T0-6 |
| CT-F3, CT-F4 | CT-T0-7, CT-T1, CT-T2 |
| CT-F6 | CT-T5 |
| CT-F7 | CT-T3, CT-T4 |
| CT-F8 | CT-T7 |
| CT-F9 | CT-T6 |
| CT-F10, CT-F2 | CT-T8, CT-T11 |
| CT-N1, CT-N2 | CT-T9, CT-T10 |
| CT-N7 | CT-T2 |
| CT-N8 | CT-T4 |

---

## 10. Riskler ve Açık Kararlar

| # | Konu | Etki | Durum |
|---|---|---|---|
| **CT-A1** | ESP32-S3'ün dokunmatik UI için nRF52840'a göre pil ömrü maliyeti | Yüksek | **v1.2: Topluluk ölçümleriyle doğrulandı** (ESP32-S3 el cihazları 10–30 saat). Yol A (nRF52840 + e-paper) önerildi → SGB A-7 |
| **CT-A2** | Transflektif ekran + su-reddi dokunmatik kontrolörün Türkiye'de stoklu olmaması | Orta | İthalat/MOQ süreci Faz 3 öncesi netleştirilmeli; 2026 itibariyle Türkiye'de kişisel/ticari ithalat gümrük muafiyeti kaldırıldı, CIF değeri üzerinden vergi hesaplanıyor — birim BOM maliyetine ithalat payı eklenmeli |
| **CT-A3** | Düşme algılama algoritmasının "elde taşınan cihaz" senaryosu için literatürde doğrudan karşılığı yok | Orta | Mevcut araştırma bilek/kemer tipi yaşlı bakım cihazlarına odaklı; CEP-T'nin kendi saha verisiyle eşik kalibrasyonu yapılması gerekecek — bu, projenin kendi özgün katkısı olabilir |
| **CT-A4** | LoRa band kararı (433 vs 868 MHz) | Yüksek | SGB A-1 ile aynı açık karar, CEP-T de bu karara bağlı — ayrıca çözülmeyecek |
| **CT-A5** | GT911 dokunmatik kontrolörün yağmurdaki gerçek davranışı doğrulanmadı | Orta | CT-T2 ile Faz 0'da erken test edilmeli |
| **CT-A6** | arXiv 2605.17063 makalesinin tam içeriği okunamadı | Düşük | Kullanıcının makaleyi doğrudan indirip incelemesi önerilir |
| **CT-A7** | Perfboard üzerinde el ile SPI kablolama (LoRa+ekran aynı bus) sinyal bütünlüğü sorunu (crosstalk, gevşek lehim) çıkarabilir | Orta | CT-T0-1/2/3 adımları bu riski erken, entegre teste geçmeden yakalamak için bilinçli olarak ayrıştırıldı |
| **CT-A8** | Rezistif (XPT2046) dokunmatiğin hassasiyeti kapasitife göre düşük; parmakla hassas hedefleme zor olabilir | Orta | Faz 0b UI tasarımında büyük dokunma hedefleri kullanılmalı; CT-T13 kullanılabilirlik testinde ayrıca ölçülmeli |

---

## 11. Kaynakça

1. LILYGO T-Deck / T-Deck Plus — meshtastic.org/docs/hardware/devices/lilygo/tdeck/, CNX Software, thesecuredad.com, liliputing.com
2. LILYGO T-Watch S3/Plus — meshtastic.org, hackster.io, rokland.com
3. Heltec Vision Master T190/E213 — heltec.org, wiki.heltec.org
4. Heltec Mesh Node T096 — heltec.org, nodakmesh.org, linuxgizmos.com, meshamerica.com
5. RAK WisMesh Tap V2 — meshtastic.org, docs.rakwireless.com, rokland.com
6. Seeed SenseCAP Indicator — cnx-software.com, wiki.seeedstudio.com, thepihut.com
7. goTenna — Wikipedia "GoTenna", nelos.app, TechCrunch
8. Beartooth — longrangehunting.com, panoplia.org, beartooth.com
9. Somewear Labs — iridium.com, somewearlabs.com
10. Zello / Hurricane Harvey yanlış anlaşılma — NBC News, Snopes
11. 2025 Berlin elektrik kesintisi ve Meshtastic kullanımı — heartlandemergencypreparedness.com, Wikipedia
12. Hacker News, Meshtastic ölçeklenme tartışması (thread id 46418866)
13. "Bridging Technical Capability and User Accessibility: Off-grid Civilian Emergency Communication," arXiv 2509.22568
14. "Resilience Analysis in Off-Grid LoRa Mesh Networks," arXiv 2605.17063
15. "Meshtastic Example in Emergency Communication," DergiPark, dergipark.org.tr/en/pub/bilmes/article/1867445
16. Centelles et al., "A LoRa-Based Communication System for Coordinated Response in an Earthquake Aftermath," UCAmI 2019 / MDPI
17. Centelles & Meseguer et al., "LoRaMoto," Future Generation Computer Systems, ScienceDirect
18. Macaraeg et al., "LoRa-based Mesh Network for Off-grid Emergency Communications," 2020
19. "HaLert: Resilient Smart City Architecture for Post-Disaster based on Wi-Fi HaLow Mesh+SDN," arXiv 2507.07841
20. Meshtastic firmware GitHub issue #526 (tek BLE istemci kısıtı)
21. meshtastic-websocket-proxy (liamcottle), meshtastic-ble-gateway (imagesafari) — GitHub
22. Meshtastic firmware GitHub discussion #3804 (GPS 15 dk fix zaman aşımı)
23. Sharp Memory LCD / ESP32 always-on display örneği — Adafruit
24. Transflektif dokunmatik ekranlar — Topfoison, VIEWE, Crystalfontz ürün sayfaları
25. Waveshare e-paper kapasitif dokunmatik HAT
26. Kapasitif dokunmatik su-reddi teknolojisi — Juniper Systems Mesa 2, UICO
27. Düşüş algılama eşik algoritması (LFT/UFT) — genel patent/mühendislik literatürü
28. "Engineering for Crisis Management: A User-Centred Analysis of Disaster Mobile Applications," arXiv 2407.08145
29. Dokunmatik-olmayan arayüz kullanılabilirliği, arXiv 2107.05408
30. motorobit.com — LILYGO Türkiye distribütörü
31. direnc.net, robotistan.com, Özdisan — Türkiye elektronik bileşen tedariki
32. Türkiye gümrük/ithalat mevzuatı 2026 — ticaret.gov.tr, shipentegra, hesapmatik, narinport
33. Meshtastic firmware GitHub discussion #7980 (SOS önceliklendirme — mevcut proje dokümanında zaten referanslı)
34. *Afet Sonrası İletişim Sistemleri Rehberlik Kılavuzu*, T.C. Çevre, Şehircilik ve İklim Değişikliği Bakanlığı, Coğrafi Bilgi Sistemleri Genel Müdürlüğü, 2024
35. TFT_eSPI (Arduino/PlatformIO ILI9341 sürücü kütüphanesi) ve XPT2046_Touchscreen (rezistif dokunmatik kütüphanesi) — açık kaynak, uygulama referansı

---

## 12. Değişiklik Geçmişi

| Sürüm | Değişiklik |
|---|---|
| 1.0 | İlk konsept rapor — araştırma bulguları, mimari, alt gereksinimler, Faz 0 (T-Deck Plus) ve Faz 3 BOM, test planı |
| **1.3** | **24 Eyl 2026 — A-7 kapandı:** dokunmatik zorunlu değil; CEP-T arayüzü e-paper + fiziksel butonlar, MCU nRF52840 kesinleşti. Dokunmatik içerik tarihsel kayıt olarak korundu. |
| 1.2 | **24 Eyl 2026 — Donanım Raporu v2.0 hizalaması:** CT-K1 A-7'ye bağlandı (önerilen nRF52840); CT-K4 NEO-6M → MAX-M10S; CT-K5 BME280 → SHT40; **CT-K6 BMI160 → LSM6DS3TR-C (BMI160 Meshtastic destekli değil)**; CT-N1/N2 hedefleri SGB CEP-N3/N10 ile hizalandı; Bölüm 7.2b (nRF52840 Pro Micro + e-paper DIY yolu) eklendi; Faz 3 BOM Yol A'ya güncellendi (regülatör TPS63900). |
| 1.1 | **CT-K9 eklendi:** maliyet gerekçesiyle Faz 0 donanım yolu, hazır entegre kart (T-Deck Plus, Faz 0a) yanına düşük maliyetli DIY/perfboard yoluna (Faz 0b: ayrık ESP32-S3 DevKit + SX1262 breakout modül + kullanıcının elindeki 2.4" ILI9341+XPT2046 dokunmatik TFT) genişletildi. CT-K2/CT-K3/CT-N7 güncellendi: rezistif dokunmatiğin yağmur toleransı bulgusu işlendi. Bölüm 7.2 (DIY BOM + kablolama tabloları) eklendi. Bölüm 8.1 (Faz 0b devreye alma/bring-up sırası, CT-T0-1…8) eklendi. CT-A7/CT-A8 riskleri eklendi.
