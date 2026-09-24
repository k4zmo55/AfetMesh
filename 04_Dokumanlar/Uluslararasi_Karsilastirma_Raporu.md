# Uluslararası Acil Haberleşme Sistemleri Karşılaştırma Raporu

> 📎 **Araştırma girdisi — 9 Ağustos 2026.** Gereksinim kaynağı değildir.
> Buradan doğan gereksinimler SGB'ye işlenmiştir (Bölüm 1.3 konumlandırma, risk kaydı, TST-7).
> Yürürlükteki gereksinimler: [`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](../00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md)
>
> **🔄 Güncelleme notu — 24 Eylül 2026 (SGB v2.4, Donanım Raporu v2.1):** Bu raporun orijinal bulguları korunmuştur; sonradan alınan donanım kararları ilgili yerlere `⚠️ v2.4` işaretiyle eklenmiştir. Özet: kişisel cihaz **nRF52840 + SX1262 (RAK4630)**, **2,9" e-paper + fiziksel butonlar** (dokunmatik yok, A-7 kapandı), **18650 + BQ24074 + TPS63900**, 5 güç modu, normal modda hedef **≥ 14 gün**. **Bu rapordaki etkisi:** yok — rapor donanım seçimine bağlı değildir. Katman 5 (topluluk mesh) için akademik afet haberleşmesi literatürü [`Literatur_ve_Topluluk_Arastirmasi.md`](Literatur_ve_Topluluk_Arastirmasi.md) Bölüm 2.2'ye eklenmiştir (ör. L-12 Matracia vd. 2022 derlemesi). Birleşik kaynakça: [`Kaynakca.md`](Kaynakca.md).

**Amaç:** Farklı ülkelerin afet haberleşmesini nasıl kurguladığını tespit etmek; AfetMesh'in bu manzaradaki yerini ve Türkiye'ye özgü konumlandırmasını netleştirmek.

---

## 1. Yönetici Özeti

Ülkeler tek bir "afet haberleşme sistemi" kurmuyor; **üst üste binen beş katman** inşa ediyor. Her katmanın farklı bir arıza senaryosuna cevabı var.

| # | Katman | Yön | Olgunluk (dünya) | Türkiye'de var mı? |
|---|---|---|---|---|
| 1 | **Uyarı** (Cell Broadcast) | Kurum → Vatandaş | ✅ Çok olgun, yaygınlaşıyor | ✅ **Var** — AFAD İKAS, 112 kanalı |
| 2 | **Öncelikli erişim** | Kurum ↔ Kurum | ✅ Olgun (ABD) | ⚠️ Kısmi |
| 3 | **Profesyonel ayrık şebeke** | Ekip ↔ Merkez | ✅ Çok olgun | ✅ Var — ASELSAN DMR, KGHS |
| 4 | **Uydu** | Her yön | 🔄 Hızla gelişiyor | ⚠️ Kurumsal düzeyde var |
| 5 | **Vatandaş katmanı** | **Vatandaş → Kurum** | ❌ **Dünyada da zayıf** | ❌ **Yok** |

**Ana bulgu:** Katman 5, yalnızca Türkiye'de değil **dünya genelinde eksik.** Olgun tek örnek Japonya'nın 171 sistemidir — o da GSM üzerinde çalıştığı için şebeke çöktüğünde devre dışı kalır.

**Konumlandırma sonucu:** AfetMesh'i "Türkiye'de eksik olanı yapıyoruz" diye sunmak yanlıştır ve gereksiz direnç doğurur. Doğru çerçeve:

> **İKAS afeti size haber verir; AfetMesh sizin haberinizi dışarı çıkarır.**
> Biri aşağı yönlü, diğeri yukarı yönlüdür. Rakip değil, tamamlayıcıdır.

---

## 2. Katman 1 — Uyarı (Kurum → Vatandaş)

### 2.1 Teknoloji: Cell Broadcast

Bugün dünya standardı **Cell Broadcast**'tir. SMS'ten farkı kritiktir:

| | SMS | Cell Broadcast |
|---|---|---|
| Gönderim | Abone abone, tek tek | Hücredeki **tüm** cihazlara aynı anda |
| Yoğunlukta | Kuyruğa girer, gecikir, düşer | **Trafik yükünden etkilenmez** |
| Abone listesi | Gerekir | Gerekmez |
| Konum hedefleme | Zayıf | Hücre bazında hassas |

> Bu fark, Van depremi (2011) ve 26 Eylül 2019 İstanbul depreminde yaşanan *"herkes aynı anda aradı, baz istasyonu kapasitesi aşıldı"* sorununun doğrudan cevabıdır.

### 2.2 Ülke uygulamaları

| Ülke | Sistem | Öne çıkan özellik |
|---|---|---|
| **Japonya** | **J-Alert** | FDMA merkezli ulusal anlık uyarı. **Superbird-B3 uydusu + karasal yedek** üzerinden yayın. Ayrıca **EWBS**: bekleme modundaki TV/radyoyu özel sinyalle **otomatik açar** |
| **Japonya** | **L-Alert** | Yerel tahliye ve hizmet bilgisi platformu — J-Alert'in yerel tamamlayıcısı |
| **ABD** | **WEA / IPAWS** | FEMA koordinasyonlu ulusal uyarı altyapısı |
| **AB** | **EU-Alert** | 2018 AB direktifi ile üye ülkelere **zorunlu** kılındı (son tarih 2022). Düzenleyici zorunluluk, yaygınlaşmayı hızlandıran en etkili yöntem oldu |
| **Hollanda** | NL-Alert | **Yılda 12 test** — her ayın ilk pazartesi 12:00. Düzenli test = toplumsal aşinalık |
| **G. Kore** | KPAS | Mayıs 2026'da mesaj uzunluğu **157 karaktere** çıkarıldı; 2024'te yabancılar için İngilizce anahtar kelimeler eklendi |
| **İngiltere** | Emergency Alerts | 19 Mart 2023'te devreye girdi |
| **Fransa** | FR-Alert | |
| **Avustralya** | **AusAlert** | 2026'da devreye giriyor; ilk ulusal test **27 Temmuz 2026** |
| Gelişme aşamasında | Hindistan, Polonya, Ukrayna, İsveç, Brezilya | |

### 2.3 Türkiye: İKAS

Türkiye bu katmana **sahiptir.** AFAD ve TÜBİTAK BİLGEM tarafından geliştirilen **İKAS (İkaz ve Alarm Bütünleşik Sistemi)**:

- Haber Alma ve Yayma Sistemi + Siren Sistemi + Mesajla Uyarı Sistemi
- AFAD'daki merkezden, GSM operatörlerinin sağladığı **Cell Broadcast** ile **112. kanaldan** yayın
- Sirenlerin duyuru sahası dışındaki tehlike bölgelerine ulaşmayı hedefliyor
- Fiziki kurulum sürerken alt sistemler devreye alındı; İstanbul, Kocaeli, Sakarya, Düzce ve Zonguldak'ta uyarı gönderildi

### 2.4 ⚠️ Bu katmanın sınırı — projenin başladığı yer

Bakanlık kılavuzu, afet haberleşmesinin **üç düşmanını** tanımlıyor: *enerji kesintileri, fiziksel zararlar, aşırı kullanım yoğunluğu.*

**Cell Broadcast bunlardan yalnızca birini çözer:**

| Sorun | Cell Broadcast'in cevabı |
|---|---|
| Aşırı kullanım yoğunluğu | ✅ **Çözer** — trafik yükünden etkilenmez |
| Fiziksel zarar (kule yıkılması) | ❌ Çözmez — yayın yapacak baz istasyonu yok |
| Enerji kesintisi | ❌ Çözmez — beslemesiz istasyon yayın yapamaz |

Bakanlık kılavuzunun kendi ölçek tanımıyla: Cell Broadcast **küçük çaplı afetlerde** (santral/şebeke sıkışması) etkilidir; **büyük çaplı afetlerde** (kuleler yıkılır, elektrik kesilir) devre dışıdır.

> **AfetMesh tam olarak burada başlar.** İki sistem farklı arıza senaryolarına cevap verir; bu yüzden çakışmazlar.

---

## 3. Katman 2 — Öncelikli Erişim (Şebeke Ayakta, Tıkalı)

Şebekenin fiziksel olarak sağlam ama tıkalı olduğu durumda yetkililere öncelik veren mekanizmalar. ABD'de:

| Sistem | Kapsam |
|---|---|
| **GETS** (Government Emergency Telecommunications Service) | Sabit hat üzerinden öncelikli çağrı |
| **WPS** (Wireless Priority Service) | Mobil şebekede öncelik |
| **TSP** (Telecommunications Service Priority) | Devre kurulum/onarım önceliği |

11 Eylül saldırıları, Katrina ve Harvey kasırgaları, 7 Temmuz 2005 Londra saldırılarında etkinliği kanıtlandı.

**Vatandaşa dokunmaz** — tanımı gereği yetkili kullanıcılara ayrılmıştır.

---

## 4. Katman 3 — Profesyonel Ayrık Şebeke

| Sistem | Bölge | Durum |
|---|---|---|
| **TETRA** | Avrupa, Ortadoğu, Afrika, Asya-Pasifik | Hâlâ baskın standart |
| **P25** | Kuzey Amerika | |
| **FirstNet** | ABD | **Band 14 — kamu güvenliğine tahsisli 20 MHz spektrum.** 7 milyar $ ve AT&T ile 25 yıllık sözleşme. 2023'te ulusal kurulum tamamlandı, 2024-25'te 1.000 saha eklendi. 2026'da **uydu-cihaz** yeteneği ve MCPTT için çekirdek yükseltiliyor |
| **ESN** | İngiltere | Airwave/TETRA'nın yerini alıyor |
| **RRF** (Réseau Radio du Futur) | Fransa | 4G/5G MCX üzerine |
| **BDBOS** | Almanya | Geniş bant geçişini değerlendiriyor |

### Sektörün yönü ve önemli bir uyarı

Sektör TETRA/P25'ten **MCX/MCPTT**'ye (LTE/5G üzerinden görev-kritik bas-konuş, video, veri) geçiyor. Ancak geçiş **ikame değil, ekleme** biçiminde:

> *MCX şu an TETRA'nın radikal bir ikamesi değil, stratejik bir eklentisidir; klasik TETRA telsiz özellikle kritik durumlarda ve **şebeke arızalarında** sağlam ses omurgası olmaya devam etmektedir.*

**Bu, AfetMesh için doğrudan öğretici:** Profesyoneller bile, geniş bantlı ve zengin sistemleri benimserken **basit, dayanıklı ve altyapısız çalışan katmanı elden bırakmıyorlar.** Aynı mantık, vatandaş katmanında AfetMesh'in var olma gerekçesidir.

Türkiye'nin bu katmandaki karşılığı: ASELSAN kriptolu DMR sistemleri, mobil muhabere aracı, AFAD KGHS (fiber + GSM + uydu + HF).

---

## 5. Katman 4 — Uydu

En hızlı değişen ve **AfetMesh açısından en dikkat gerektiren** katman.

| Çözüm | Durum (2026) |
|---|---|
| **Apple Emergency SOS via satellite** (Globalstar) | iPhone 14+. Avrupa, Japonya, K. Amerika, Meksika, Avustralya, Yeni Zelanda'da aktif. iOS 18 ile acil aramanın ötesine geçip **iMessage ve SMS**'e açıldı |
| **Starlink Direct-to-Cell / T-Satellite** | Beta; tüm operatör ve tarifeler için **Text-to-911** destekliyor. 2026'da ses ve veriye genişleme planı. 2024 sonunda afet müdahalesinde kullanıldı |
| **AST SpaceMobile** | 2026 civarında tam ticari lansman hedefi |
| **Tayvan — yerli dirençli uydu haberleşmesi** | Kurumlar, itfaiye, sağlık kuruluşları ve uzak topluluklar için ulusal çözüm açıklandı |
| **Çin** | 2008 Wenchuan depreminde yollar çöküp hava koşulları kötüleşince **yalnızca uydu tabanlı sistemler çalışabildi** → deprem riski yüksek ve ulaşımı zor bölgelerde uyduya öncelik veriyor |

### ⚠️ Bu, AfetMesh için orta vadeli bir tehdittir

Uydu-doğrudan-telefon yaygınlaşırsa, "cihazsız vatandaşın yardım çağrısı" problemi telefon üreticileri tarafından çözülebilir. Bu risk **SGB risk kaydına eklenmiştir.**

**Karşı argümanlar (gerçek ve savunulabilir):**

| Uydu D2C'nin sınırı | AfetMesh'in durumu |
|---|---|
| Açık gökyüzü gerektirir — **enkaz altında, bodrumda, kapalı mekânda çalışmaz** | LoRa bina içinden ve moloz altından sınırlı da olsa geçer |
| Yalnızca yeni ve pahalı telefonlarda | Kullanıcının telefon modelinden bağımsız (WiFi yeterli) |
| Operatör/üretici ekosistemine ve aboneliğe bağımlı | Bağımsız |
| Kapasite sınırlı; kitlesel eşzamanlı kullanımda darboğaz | Yerel mesh yerel trafiği taşır |

---

## 6. Katman 5 — Vatandaş Katmanı (Yukarı Yönlü) ← Projenin Yeri

**Dünyanın en zayıf katmanı budur.**

### 6.1 Japonya — tek olgun örnek

| Servis | İşlev |
|---|---|
| **171 Afet Acil Haberleşme Sistemi** | Vatandaş güvenlik durumunu **sesli** kaydeder; yakınları dinler. `1` tuşu mesaj bırak, `2` tuşu dinle. Sabit hat, mobil, ankesörlü ve fiber üzerinden erişilebilir |
| **web171** | Aynı işlevin internet üzerinden sürümü |
| **Operatör panoları** (NTT, KDDI) | Disaster message board, acil e-posta, sesli mesaj — güvenlik durumu teyidi |

**Ortak zaafı:** Hepsi **GSM/internet üzerinde** çalışır. Bakanlık kılavuzunun tarif ettiği "iletişim tamamen kopar" senaryosunda hiçbiri devrede değildir.

> Bakanlık kılavuzu 171 modelini örnek gösteriyor ve *"Türkiye'de GSM operatörleri ve AFAD'ın iş birliği ile uygulama için çalışmalar başlatıldığını"* belirtiyor. Türkiye bu modeli benimserse bile **aynı zaafı miras alacaktır.**

### 6.2 Diğer yaklaşımlar ve sınırları

| Yaklaşım | Örnek | Sınırı |
|---|---|---|
| Telefon-telefon mesh uygulamaları | FireChat, Bridgefy, MeshTalk (OPPO) | Bluetooth menzili **~100 m**; uygulama **önceden kurulmuş** olmalı |
| Gönüllü telsizci ağları | ARES/RACES (ABD), TRAC (Türkiye) | **Lisans gerekir** — halk kullanamaz |
| Güvenlik durumu uygulamaları | AKUT Güvendeyim (TR, 1M+ kullanıcı) | Konumu **SMS ile** iletir → GSM'e bağımlı |
| Sosyal medya | Van 2011'de Facebook/Twitter fiilen koordinasyon merkezi işlevi gördü | İnternet gerektirir |
| Topluluk LoRa mesh | Meshtastic ağları, TA Mesh | Cihaz + uygulama + yapılandırma gerektirir |

### 6.3 Türkiye'nin katman haritası

```
Katman 1 (Uyarı)          ✅ İKAS / Cell Broadcast 112
Katman 2 (Öncelik)        ⚠️ Kısmi
Katman 3 (Profesyonel)    ✅ ASELSAN DMR · KGHS · TAMP Haberleşme Grubu
Katman 4 (Uydu)           ⚠️ Kurumsal düzeyde (KGHS uydu bacağı)
Katman 5 (Vatandaş →)     ❌ YOK  ← AfetMesh
```

---

## 7. Sahadan Ders: Hurricane Helene (Eylül–Ekim 2024)

Bu rapordaki en değerli bulgu, bir pazarlama iddiası değil **gerçek bir afetten gelen kullanıcı geri bildirimidir.**

Helene, ABD'nin güneydoğusunda ilk müdahale ekiplerinin haberleşme sistemlerini yok etti. Amatör telsizciler boşluğu doldurdu. Kuzey Carolina'da kırsal bir toplulukta bir operatör **Meshtastic'i topluluk çözümü olarak denedi.** Raporu:

> *"Meshtastic, şebeke dışı iletişim için harika bir tamamlayıcı; ancak komşular taşınabilir telsizleri ve sesli iletişimi tercih ediyor. Elektronik meraklıları ve maker'lar için nispeten basit olsa da, **ortalama kullanıcı için hâlâ biraz tuhaf ve tam anlamıyla kullanıcı dostu değil.**"*

Ve topluluğun genel değerlendirmesi:

> *"Helene, Amatör Telsiz topluluğuna yüksek sesle bir uyandırma çağrısı yaptı — ticari ve devlet altyapısının yenilmez olmadığının açık bir hatırlatması."*

### Bu iki alıntının projeye anlamı

**✅ Tezimizi doğruluyor:** Kullanılabilirlik boşluğu gerçektir ve sahada ölçülmüştür. Meshtastic'in ham hali sıradan vatandaş için yeterli değildir — bu tam olarak PORTAL'ın var olma gerekçesidir (SGB İ-1, İ-2).

**⚠️ Aynı zamanda uyarı:** İnsanlar **konuşmak** istiyor, yazmak değil. AfetMesh sesli iletişim sunamaz ve sunmaya çalışmamalıdır. Bu yüzden ürün, telsizle *sohbet* alanında yarışmamalı; **tek yönlü SOS + triage** işlevine odaklanmalıdır. Kapsam belgesindeki "ses kapsam dışı" kararı (SGB Bölüm 3.2) bu saha verisiyle desteklenmiştir.

---

## 8. Tayvan'ın 2026 Tatbikatı — Pilot İçin Model

Tayvan'ın 2026 kentsel dayanıklılık tatbikatları, **ilk kez ulusal ölçekte mobil internet kesintisi simülasyonu** içeriyor; yedek haberleşme yetenekleri gerçek koşulda test ediliyor.

**Öneri:** Pilot bölgede benzer bir **"şebekesiz gün" tatbikatı** düzenlenmeli. Bu:
- SGB **TST-7**'yi (tam kopukluk testi) laboratuvar yerine gerçek koşulda doğrular
- Bakanlık kılavuzunun *"toplumun afetlere hazırlıklı olması için tatbikatlar düzenlenmesi"* maddesiyle örtüşür
- Kurumsal muhataba somut ve ölçülebilir bir çıktı sunar

---

## 9. Projeye Çıkarımlar

| # | Çıkarım | SGB'ye etkisi |
|---|---|---|
| **U-1** | **Konumlandırma:** İKAS aşağı yönlü, AfetMesh yukarı yönlü. "Eksik olanı yapıyoruz" değil, "tamamlıyoruz" | ✅ SGB Bölüm 1.3'e işlendi |
| **U-2** | **Uydu D2C orta vadeli tehdit.** Karşı argüman: enkaz altı + telefon modelinden bağımsızlık | ✅ SGB Bölüm 15 risk kaydına işlendi |
| **U-3** | **"Şebekesiz gün" tatbikatı** pilot planına alınmalı | ✅ SGB TST-7'ye işlendi |
| **U-4** | **Ses beklentisi gerçektir.** Ürün sohbet değil, SOS + triage odaklı kalmalı | ✅ SGB Bölüm 3.2 mevcut kararı destekliyor |
| **U-5** | Katman 5'in dünyada da eksik olması, projenin özgünlüğünün yerel değil **küresel** olduğunu gösteriyor | Konumlandırma argümanı |
| **U-6** | Hollanda'nın **yılda 12 test** pratiği, sistem aşinalığının teknolojiden önemli olduğunu gösteriyor | Pilot sonrası yaygınlaştırma stratejisi için not |

---

## Kaynaklar

**Katman 1 — Uyarı**
- [EU-Alert](https://en.wikipedia.org/wiki/EU-Alert) · [NL-Alert](https://en.wikipedia.org/wiki/NL-Alert) · [Emergency Alerts (UK)](https://en.wikipedia.org/wiki/Emergency_Alerts_(United_Kingdom)) · [FR-Alert](https://en.wikipedia.org/wiki/FR-Alert)
- [ITU — Cell broadcast early warning system](https://www.itu.int/en/ITU-D/Emergency-Telecommunications/Pages/EW4ALL/cell-broadcast.aspx)
- [Hackaday — Cell Broadcast: The Modern Emergency Alert System](https://hackaday.com/2026/08/05/cell-broadcast-the-modern-emergency-alert-system/)
- [EENA — Public Warning](https://eena.org/portfolio/public-warning/)
- [J-Alert](https://en.wikipedia.org/wiki/J-Alert) · [Emergency Warning Broadcast System (Japonya)](https://en.wikipedia.org/wiki/Emergency_Warning_Broadcast_System_(Japan))
- [GSMA — Japan's Early Warning System: The Role of Mobile Network Operators](https://www.gsma.com/solutions-and-impact/connectivity-for-good/mobile-for-development/gsma_resources/japans-early-warning-system-the-role-of-mobile-network-operators/)

**Türkiye**
- [AFAD — Bütünleşik İkaz Alarm Sistemi Projesi (İKAS)](https://www.afad.gov.tr/butunlesik-ikaz-alarm-sistemi-projesi-ikas0)
- [AFAD — İKAS yağışlardan önce telefonlara erken uyarı gönderdi](https://www.afad.gov.tr/afad-ikaz-ve-alarm-sistemi-ikas-yagislardan-once-telefonlara-erken-uyari-gonderdi)
- [TÜBİTAK — Savaş ve Doğal Afet Durumlarında Vatandaşlar Uyarılacak](https://tubitak.gov.tr/en/node/15650)

**Katman 3 — Profesyonel şebeke**
- [FirstNet Authority — The Network](https://firstnet.gov/network) · [FirstNet Authority — Ağ genişletme ve uydu bağlantısı](https://www.firstnet.gov/newsroom/press-releases/firstnet-authority-expand-network-boost-coverage-public-safety-more-sites)
- [Hytera — P25, TETRA, DMR ve LTE PTT karşılaştırması](https://www.hytera.com/en/connect/blog/how-agencies-compare-p25-tetra-dmr-lte-ptt)
- [Teamwire — MCX: The Future of Operational Communications](https://teamwire.eu/en/blog/mcx-operational-communication-public-safety-critical-infrastructure/)

**Katman 4 — Uydu**
- [SatelliteInternet — Direct-to-Cell Satellite Phone Service](https://www.satelliteinternet.com/resources/satellite-phone-service/)
- [SatelliteInternet — iPhone Satellite Features Guide (2026)](https://www.satelliteinternet.com/resources/iphone-satellite-guide/)
- [III (Tayvan) — Resilient Satellite Communication](https://www.iii.org.tw/en/news/newsroom/iii-news/2e8852ee-8499-4621-a637-ef790e0932dc)

**Katman 5 ve saha deneyimi**
- [QRPer — Helene Aftermath: Adopting an Off-Grid Community Radio Network](https://qrper.com/2024/10/helene-aftermath-update-adopting-an-off-grid-community-radio-network-thursday-october-24-2024/)
- [QRPer — Hurricane Helene: One Year Later](https://qrper.com/2025/09/hurricane-helene-one-year-later/)
- [ARRL — Helene Storm Updates](https://www.arrl.org/news/helene-storm-updates)
- [AIT — Taiwan 2026 Urban Resilience Exercises](https://www.ait.org.tw/message-for-u-s-citizens-2026-urban-resilience-air-defense-exercises/)
- T.C. Çevre, Şehircilik ve İklim Değişikliği Bakanlığı (2024). *Afet Sonrası İletişim Sistemleri Uygulaması* — proje kök dizinindeki PDF (Japonya 171, FireChat, Bridgefy, MeshTalk, AKUT Güvendeyim bölümleri)

---

> **Metodoloji notu:** Bu rapor açık kaynak araştırmasına dayanmaktadır. Katman 1 ve 4'teki bilgiler hızla değişmektedir; özellikle uydu-doğrudan-telefon alanı 2026 içinde önemli ölçüde değişebilir ve **pilot öncesi yeniden kontrol edilmelidir.** Türkiye'ye ilişkin İKAS bilgileri AFAD ve TÜBİTAK'ın kendi duyurularına dayanmakta olup, sistemin ülke genelindeki güncel kapsama durumu doğrulanmamıştır.
