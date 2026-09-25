# AfetMesh — HANGAR BİGG Başvuru Konsepti

> 📎 **Karar gerekçesi — 25 Eylül 2026.** Bu belge, projenin TUSAŞ HANGAR BİGG (TÜBİTAK 1812 BiGG Yatırım) programına **hangi yapıyla** başvuracağına karar vermek için hazırlanmıştır.
>
> ✅ **K-1 kararı verildi (25 Eyl 2026): Seçenek B.** Gereksinimler **SGB v2.6**'ya işlendi: Bölüm 1.5, **Bölüm 7c (HV-1…HV-16, HV-N1…N7)**, CEP-12, REG-7…9, A-13…A-16, TST-14…19, yol haritası H1…H4. Bu belgedeki yol haritası ve riskler için **SGB esastır.**
>
> Program: <https://kariyer.tusas.com/hangar-bigg> · Yatırım: **%3 hisse karşılığı 1.350.000 TL** · Son dönem 6 Nisan 2026'da kapandı, **sonraki dönem: 2027**

---

## 1. Neden yeniden yapılandırma gerekiyor?

HANGAR BİGG'in tanımı: *"Çift kullanım odaklı **havacılık ve uzay** girişimcilerine…"*. Değerlendirmede jürinin soracağı üç soru, projenin **şu anki haliyle** zayıf cevaplanıyor:

| Jüri sorusu | Bugünkü cevabımız | Sorun |
|---|---|---|
| Havacılık/uzay ile ilgisi ne? | Yok — yerde çalışan LoRa ağı | ❌ İlk elemede "konu dışı" riski |
| Bu Meshtastic değil mi? | Portal + SOS önceliği + çatı röle | ⚠️ Doğru ama teknik olmayan jüriye "üstüne eklenti" gibi görünür |
| Savunmada kim kullanır? | — | ❌ Çift kullanım anlatısı yok |

**Güçlü yanlarımız** (korunacak): gerçek problem (6 Şubat), olgun dokümantasyon (SGB v2.5, 2 donanım raporu, BOM), tamamen yerli ve düşük maliyetli mimari, ekibin donanım + yazılımı kendi yapabilmesi.

---

## 2. Üç seçenek

| | **A — Mevcut hali** | **B — AfetMesh + HAVA katmanı** ⭐ | **C — Taktik askeri mesh** |
|---|---|---|---|
| Özü | Yerde LoRa halk erişim ağı | Yer ağı **+ İHA yük modülü: havadan enkaz altı konum tespiti ve geçici haberleşme** | Birlikler için şifreli ekip haberleşmesi |
| Havacılık/uzay uyumu | ❌ Zayıf | ✅ Güçlü (İHA yükü = TUSAŞ'ın alanı) | ⚠️ Orta |
| Çift kullanım | ⚠️ Zorlama | ✅ Doğal: AFAD arama-kurtarma ↔ personel kurtarma (CSAR) | ✅ Ama sivil taraf zayıf |
| Rakip durumu | Meshtastic ekosistemi | Yabancı muadil var (Lifeseeker), yerlisi yok | ASELSAN — çok güçlü rakip |
| Ekibin yapabilirliği | ✅ | ✅ Drone hazır alınır, **yük modülü** tasarlanır | ❌ Kripto, sertifikasyon, LPI/LPD uzmanlığı yok |
| Mevcut çalışmanın kullanımı | %100 | ~%85 (CEP, NOKTA, SOS, PANO aynen kullanılır) | ~%30 |
| **Değerlendirme** | Konu dışı riski yüksek | **Önerilen** | Önerilmez |

> **Neden C önerilmiyor:** Askeri haberleşme ürünü satmak; kriptografik onay, TEMPEST/EMC, askeri standart testleri ve ASELSAN ile doğrudan rekabet demek. 4 kişilik, ilk şirketini kuracak bir ekip için gerçekçi değil. Askeri kullanım **B içinde, yol haritasında** yer alır.

---

## 3. Önerilen yapı (Seçenek B)

### 3.1 Tek cümle

> **AfetMesh**, altyapının çöktüğü bölgede **enkaz altındaki insanları havadan bulan** ve onlarla **uygulama gerektirmeden haberleşen**, yerli, İHA taşınabilir bir yük modülü ile onu besleyen kalıcı yer ağıdır.

Başvuru başlığı önerisi: *"İHA Taşınabilir, Lisans Gerektirmeyen Bantlarda Çalışan Enkaz Altı Konum Tespit ve Afet Haberleşme Sistemi"*

### 3.2 Hikâyenin sırası değişiyor

| | Eski anlatı | Yeni anlatı |
|---|---|---|
| **Kahraman** | NOKTA (yer kamu node'u) | **HAVA-Y** (İHA yük modülü) |
| **Ana vaat** | "Uygulamasız SOS" | "Enkaz altındakini **havadan bul**, **uygulamasız konuş**" |
| **Yer ağı** | Ürünün kendisi | Sistemi besleyen **kalıcı altyapı** (sinyal kaynakları + veri) |
| **Müşteri** | Belediye/AFAD (belirsiz) | AFAD, JAK, itfaiye, UMKE → **sonra** MSB/Hava Kuvvetleri (CSAR) |

### 3.3 Mimari

```
                        ┌────────────────────────────────────┐
                        │  HAVA-Y  (İHA yük modülü, < 300 g) │
                        │  • LoRa alıcı (SX1262) + RSSI/SNR  │
                        │  • WiFi AP + captive portal        │  ← "uçan NOKTA"
                        │  • WiFi sinyal ölçümü (pasif)      │
                        │  • GNSS + kendi pili               │
                        │  • Veri katırı: S&F senkronu       │
                        └──────┬──────────────┬──────────────┘
         havadan konum tespiti │              │ SOS toplama / geçici röle
       ┌───────────────────────┘              └───────────────────┐
       ▼                                                          ▼
┌──────────────────┐   ┌──────────────────┐            ┌────────────────────┐
│ CEP (Enkaz modu) │   │ Afetzedenin      │            │ NOKTA / ÇATI       │
│ LoRa beacon,     │   │ telefonu         │            │ (kopuk kalmış yer  │
│ günlerce yayın   │   │ WiFi → PORTAL    │            │  düğümleri)        │
└──────────────────┘   └──────────────────┘            └────────────────────┘
                                   │
                                   ▼
                  ┌───────────────────────────────────────┐
                  │ PANO + KONUM MOTORU (yer istasyonu)   │
                  │ Ölçümleri birleştirir → olası konum   │
                  │ elipsi + ısı haritası + triage listesi│
                  └───────────────────────────────────────┘
```

### 3.4 Bileşenler — ne yeni, ne mevcut?

| Bileşen | Durum | Açıklama |
|---|---|---|
| **HAVA-Y** — İHA yük modülü | 🆕 **Yeni (projenin yeni kalbi)** | ESP32-S3 + SX1262 + GNSS + LiPo, < 300 g, **platform bağımsız** (kendi pili, basit montaj — drone'a elektriksel bağımlılık yok) |
| **KONUM MOTORU** — konum kestirim yazılımı | 🆕 **Yeni (asıl fikri mülkiyet)** | Drone'un GNSS etiketli RSSI/SNR ölçümlerinden kaynak konumu kestirir (yol kaybı modeli + parçacık filtresi). Çıktı: konum + güven elipsi |
| **Uçuş planı önerici** | 🆕 Yeni (yazılım) | İlk geçişteki sinyal haritasına göre ikinci, daha sık tarama rotası önerir |
| **CEP — Enkaz modu** (SGB CEP-8) | ✅ Mevcut | Periyodik düşük güçlü SOS beacon'ı. **Telefon pili 1–2 günde biter; CEP beacon'ı haftalarca yayın yapar** → havadan bulunacak en güvenilir kaynak |
| **PORTAL** | ✅ Mevcut | HAVA-Y üzerinde de aynen çalışır → drone uçarken afetzede telefonundan SOS gönderir |
| **SOS paketi + kimlik** (SOS-9, SOS-11 ③ veri katırı) | ✅ Mevcut | Veri katırı rolünü artık İHA da üstlenir |
| **NOKTA, ÇATI, MERKEZ** | ✅ Mevcut | Kalıcı yer ağı. Başvuruda "sistemin yer ayağı" olarak yer alır |
| **PANO** | ✅ Mevcut + genişleme | Konum elipsleri ve ısı haritası katmanı eklenir |

### 3.5 Neden termal kamera yetmiyor? (jüri soracak)

Arama-kurtarma dronelarının standart yükü termal kameradır. Ancak termal kamera **enkazın içini görmez** — beton ve moloz ısıyı geçirmez. Radyo sinyali (433/868 MHz LoRa, 2,4 GHz WiFi) moloz katmanlarından zayıflayarak da olsa geçer. HAVA-Y termal kameranın **yerine değil yanına** gelir: termal yüzeydekini, HAVA-Y enkazın içindekini bulur.

---

## 4. "Bu Meshtastic değil mi?" — cevap

**Kısa cevap:** Hayır. Meshtastic, sistemin yerdeki taşıma katmanında kullandığımız açık kaynak bir **altyapı parçasıdır** — tıpkı bir drone şirketinin Linux kullanması gibi.

| Katman | Meshtastic'te var mı? | Bizim katkımız |
|---|:---:|---|
| LoRa mesh taşıma | ✅ | Kullanıyoruz (ağ etkisi + uyumluluk) |
| Uygulamasız erişim (captive portal) | ❌ | ✅ Özgün |
| Öncelikli, yapısal SOS + teslim teyidi | ❌ (upstream talebi kapatıldı, #7980) | ✅ Özgün |
| **Havadan konum kestirimi** | ❌ | ✅ **Özgün — Meshtastic ile hiçbir ilgisi yok** |
| **İHA yük modülü donanımı** | ❌ | ✅ Özgün |
| Triage ve konum panosu | ❌ | ✅ Özgün |

> **Askeri kullanım notu:** Meshtastic'in kendisi askeri sınıf **değildir** — protokolü açıktır, yayınları kolay tespit edilir, anahtar yönetimi paylaşılan tek anahtarla (PSK) sınırlıdır. Bu yüzden başvuruda **"askeri haberleşme sistemi"** iddiası kurulmaz. Savunma tarafındaki değer **haberleşmede değil, konum tespitindedir:** düşen pilot veya kayıp personelin taşıdığı düşük güçlü beacon'ı İHA ile bulmak (Bölüm 5). Konum motoru, beacon protokolünden bağımsız tasarlanır; savunma sürümünde beacon kendi protokolüyle çalışabilir.

---

## 5. Çift kullanım

| Yetenek | Sivil kullanım | Savunma / güvenlik kullanımı |
|---|---|---|
| **Havadan beacon konum tespiti** | Enkaz altı afetzede (AFAD, JAK, UMKE) · çığ · kayıp dağcı | **Personel kurtarma (CSAR):** düşen pilot/mürettebatın beacon'ının İHA ile bulunması · kayıp asker/ekip |
| **Uçan erişim noktası** | Altyapısı çökmüş mahallede geçici SOS kanalı | Altyapısız bölgede geçici veri rölesi |
| **Veri katırı** | Kopuk kalmış yer düğümlerinden SOS toplama | Kopuk sensör/gözetleme düğümlerinden veri toplama |
| **Kalıcı düşük güçlü beacon** (CEP) | Afet çantası cihazı | Personel ve pilot hayatta kalma kitine eklenen beacon |

> **TUSAŞ bağlantısı:** Yük modülü İHA'larda taşınır; CSAR senaryosu doğrudan hava platformlarının (pilot kurtarma) ihtiyacıdır. Başvuruda iddia **"TUSAŞ platformlarına entegre edilebilir, platform bağımsız yük"** düzeyinde tutulur; belirli bir platforma (ANKA, AKSUNGUR vb.) entegrasyon **vaat edilmez**.

---

## 6. Rekabet — dürüst tablo

| Çözüm | Nasıl çalışıyor | Güçlü | Zayıf / bizim fırsatımız |
|---|---|---|---|
| **Centum Lifeseeker** (İspanya) | Drone üzerinde **sahte hücresel baz istasyonu** — telefonu kendine bağlar, konumlar; SMS/ses rölesi | Olgun ürün, 35+ müşteri, 2025 ödülü → **pazar kanıtlanmış** | Hücresel bantlarda yayın → Türkiye'de **BTK izni ve güvenlik kurumu yetkisi** gerektirir (IMSI yakalayıcı sınıfı); **yabancı ürün**; yalnızca telefonu bulur (telefon pili bitince kaynak kaybolur); yer ağı yok |
| **SARDO** (akademik, 2020) | Drone üzerinde LTE baz istasyonu + makine öğrenmesi | Yayınlanmış doğruluk sonuçları | Prototip; aynı lisans sorunu |
| **Termal kameralı drone** | Isı imzası | Her ekipte var | Enkazın içini göremez |
| **Çığ LoRa + drone çalışmaları** (akademik, 2024–25) | Gömülü LoRa vericisi + drone ile RSSI ölçümü | **Metre mertebesinde doğruluk raporlanmış** → yöntem kanıtlı | Ürün değil; afet senaryosu ve yer ağı yok |
| **AfetMesh HAVA** | LoRa beacon + WiFi, **lisanssız bantlarda (ISM)**, yer ağıyla entegre | Yerli · lisans gerektirmez · telefon + beacon · iki yönlü SOS · düşük maliyet | Olgunluk düşük (TRL 2–3) → **prototip şart** |

> **Kurumsal iletişim kuralı (SGB 1.4 ile aynı ruh):** "Dünyada böyle bir şey yok" **denmeyecek.** Doğru çerçeve: *"Yabancı muadili 35+ kurumda kullanılıyor; biz yerli, lisans gerektirmeyen ve kalıcı yer ağıyla entegre bir mimari sunuyoruz."* Lifeseeker'ın varlığı bir tehdit değil, **pazarın kanıtıdır.**

---

## 7. İş modeli (taslak — program içinde olgunlaşacak)

| Gelir kalemi | Müşteri | Not |
|---|---|---|
| **HAVA-Y yük modülü + konum yazılımı** (set satışı) | AFAD il müdürlükleri, JAK, büyükşehir itfaiyeleri, UMKE, AKUT | Ana ürün. Fiyat, program içinde müşteri görüşmeleriyle belirlenecek |
| **Yıllık yazılım + bakım + eğitim** | Aynı | Tekrarlayan gelir |
| **Yer ağı kurulumu** (NOKTA/ÇATI/MERKEZ) | Belediyeler | Mevcut SGB kapsamı; ikincil gelir |
| **Savunma sürümü** (CSAR beacon + bulucu) | SSB/MSB alt yüklenici yolu, platform üreticileri | Yol haritası — 2. aşama sonrası |
| **İhracat** | Deprem riski yüksek ülkeler | Yol haritası |

> **Satış döngüsü riski:** Kamu alımı yavaştır. Azaltma: pilot için **tek bir belediye/AFAD il müdürlüğü** ile protokol; niyet mektubu başvuru öncesi hedeflenir (Bölüm 9).

---

## 8. Ekip ve görev dağılımı

| Kişi | Alan | Sorumluluk |
|---|---|---|
| **Girişimci (başvuru sahibi)** — Bilgisayar Mühendisi (mezun) | Sistem mimarisi, firmware | Proje lideri · HAVA-Y firmware · KONUM MOTORU algoritması · iş planı ve kurum görüşmeleri |
| **Elektrik-Elektronik** (öğrenci) | Donanım, RF | HAVA-Y kartı ve anten · CEP enkaz modu · güç ve ağırlık bütçesi · RF ölçümleri |
| **Bilgisayar** (öğrenci) | Yazılım | PANO: harita, ısı haritası, triage · uçuş planı önerici |
| **Bilgisayar** (öğrenci) | Yazılım, test | PORTAL · veri katırı senkronu · saha test otomasyonu ve veri kaydı |

**Açık:** İş geliştirme / kamu satış deneyimi. Çözüm: program mentorları + bir AFAD/JAK gönüllüsü veya emekli personel **danışman** olarak.

> ⚠️ **Girişimci taahhüdü:** TÜBİTAK taahhütnamesine göre girişimci, yatırım sözleşmesi sonrasında *"yalnızca desteklenecek şirket için çalışacağını"* taahhüt eder. Başvuru sahibinin iş durumu başvurudan önce netleşmelidir. Ayrıca **dört kişinin hiçbiri** başvuru tarihinde bir şirkette ortak olmamalıdır.

---

## 9. 2027 başvurusuna kadar yol haritası

Hedef: Başvuruda **"fikir"** değil **"çalışan prototip + ölçülmüş veri"** sunmak (TRL 2 → TRL 4).

| # | Aşama | İçerik | Süre | Başvuruda gösterilecek çıktı |
|---|---|---|---|---|
| **0** | Karar + SGB | Bu belgenin onayı; `HV-` gereksinimleri SGB'ye | 1 hafta | SGB v2.6 |
| **1** | **Dronesuz konum testi** ⭐ | CEP beacon'ı yere/bodruma koy; alıcıyı **GNSS'li bir direkle yürüyerek veya araçla** gezdir; KONUM MOTORU ile kestir | 4–6 hafta | Doğruluk grafiği (kestirim ↔ gerçek konum) |
| **2** | HAVA-Y prototip | Hazır ESP32-S3 + SX1262 kartı + GNSS + pil, 3D baskı kutu | 4 hafta | Ağırlık, pil süresi, fotoğraf |
| **3** | İlk uçuş | Hazır bir drone (hobi/ticari) ile açık alanda gömülü beacon bulma. **SHGM kurallarına uygun, izinli alanda** | 3 hafta | **1–2 dakikalık demo videosu** |
| **4** | Enkaz benzeri test | Yıkım sahası, otopark bodrumu veya AFAD/JAK eğitim alanı | 3 hafta | Moloz altı sinyal zayıflama verisi |
| **5** | Kurum görüşmesi | 1 AFAD il müdürlüğü / JAK / itfaiye → görüş veya niyet mektubu | Paralel | Mektup / görüşme notu |
| **6** | Fikri mülkiyet | KONUM MOTORU + beacon-İHA yöntemi için patent ön araştırması; **başvuru öncesi** detayların korunması | Paralel | Patent araştırma raporu |

> **1. aşama neden en kritik:** Drone gerektirmez, neredeyse maliyetsizdir ve jürinin en önemli sorusunu ("gerçekten bulabiliyor mu?") ölçümle cevaplar. Drone yalnızca alıcıyı yukarı taşır; **algoritma yerde kanıtlanabilir.**

---

## 10. Riskler

| Risk | Etki | Azaltma |
|---|---|---|
| Moloz altında LoRa sinyali beklenenden çok zayıflar | Yüksek | 433 MHz (L) bandı tercih edilir (daha iyi nüfuz); 4. aşamada ölçülür; hedef doğruluk ölçümden sonra ilan edilir |
| RSSI tabanlı kestirim şehirde yansımalardan bozulur | Orta | Çok sayıda ölçüm + parçacık filtresi; çıktı **nokta değil güven elipsi** olarak sunulur |
| Afetzedenin CEP'i yok, telefon WiFi'ı kapalı | Yüksek | Sistem **iki kaynağı** birden kullanır; CEP'in yaygınlaşması belediye dağıtım modeline bağlı (SGB) |
| Telefonların pasif tespiti KVKK sorunu doğurur | Orta | Yalnızca **afet modunda ve yetkili kurum kullanımında**; MAC adresleri saklanmaz, yalnızca konum ve sayım |
| İHA uçuş izni (SHGM, afet bölgesi) | Orta | Ürün **platform bağımsız yük**; uçuşu yetkili kurum ekipleri yapar |
| Lifeseeker Türkiye'ye girer | Orta | Lisans gerektirmeyen mimari + yerlilik + yer ağı entegrasyonu + fiyat |
| "Meshtastic tabanlı" algısı yenilik puanını düşürür | Orta | Anlatının merkezi KONUM MOTORU ve HAVA-Y; Meshtastic yalnızca yer taşıma katmanında |
| MIT lisanslı açık depo → fikri mülkiyet zayıflar | Orta | KONUM MOTORU ve HAVA-Y tasarımı **başvuru öncesi açık depoya konmaz** (ekip kararı gerekir) |

---

## 11. Jürinin soracağı 10 soru

| # | Soru | Cevabın dayanağı |
|---|---|---|
| 1 | Havacılıkla ilgisi ne? | İHA yük modülü + CSAR (Bölüm 5) |
| 2 | Bu Meshtastic değil mi? | Bölüm 4 tablosu |
| 3 | Lifeseeker varken neden siz? | Lisanssız bant, yerli, beacon + telefon, yer ağı (Bölüm 6) |
| 4 | Termal kamera yetmez mi? | Enkazın içini görmez (Bölüm 3.5) |
| 5 | Doğruluk ne? | **1. ve 4. aşama ölçümleri** — ölçmeden rakam verilmez |
| 6 | Enkaz altında sinyal çıkar mı? | 4. aşama verisi + 433 MHz tercihi |
| 7 | Kim satın alır, ne kadara? | Bölüm 7 + niyet mektubu |
| 8 | Telefon pili bitince? | CEP beacon'ı haftalarca yayın yapar |
| 9 | Ekip bunu yapabilir mi? | Bölüm 8 + çalışan prototip videosu |
| 10 | 1,35 milyon TL nereye harcanacak? | Program içinde iş planıyla: pilot seti, saha testleri, sertifikasyon ön çalışması |

---

## 12. Ekibin vereceği kararlar

| # | Karar | Seçenekler |
|---|---|---|
| ~~**K-1**~~ ✅ | Başvuru yapısı | **B seçildi (25 Eyl 2026)** |
| **K-2** | Başvuru sahibi (girişimci) kim olacak ve iş durumu uygun mu? | — |
| **K-3** → SGB **A-16** | KONUM MOTORU açık kaynak mı, kapalı mı olacak? | Açık depo (MIT) / **Kapalı çekirdek + açık yer ağı** |
| **K-4** | 1. aşama (dronesuz test) ne zaman başlıyor, sorumlusu kim? | — |
| **K-5** | Hangi kuruma ilk görüşme için gidilecek? | AFAD il müdürlüğü / JAK / büyükşehir itfaiyesi |
| **K-6** | Ürün adı | "AfetMesh" korunur / HAVA bileşeni için ayrı ad |

---

## Kaynaklar

- [TUSAŞ HANGAR BİGG program sayfası](https://kariyer.tusas.com/hangar-bigg) · [Başvuru adımları yönergesi (PDF)](https://kariyer.tusas.com/storage/HANGAR_BIGG_Basvuru_Adimlari_Yoonergesi.pdf) · [Belge paketi](https://kariyer.tusas.com/hngrbgb)
- [Centum — Lifeseeker](https://centum.com/en/products/lifeseeker/) · [UST — Lifeseeker Mini İHA entegrasyonu (Ara 2025)](https://www.unmannedsystemstechnology.com/2025/12/centum-integrates-lifeseeker-mini-on-uav-platform-for-search-rescue/) · [DroneLife — Hücresel sinyalle arama-kurtarma](https://dronelife.com/2020/05/18/search-and-rescue-drones-finding-a-cell-phone-signal-and-a-missing-person/)
- [SARDO: An Automated Search-and-Rescue Drone-based Solution for Victims Localization (arXiv 2003.05819)](https://arxiv.org/pdf/2003.05819)
- [An experimental dataset using UAVs and LoRa technology in avalanche scenarios (PMC)](https://pmc.ncbi.nlm.nih.gov/articles/PMC12664345/) · [Zenodo veri seti](https://zenodo.org/records/13932869)
- [Radio Frequency Identification and Localization by Wearable LoRa for Search and Rescue in Mountains](https://www.researchgate.net/publication/361445162_Radio_Frequency_Identification_and_Localization_by_Wearable_LoRa_for_Search_and_Rescue_in_Mountains)
- [Catch Me If You Can: Deep Meta-RL for Search-and-Rescue using LoRa UAV Networks (arXiv 2306.02911)](https://arxiv.org/pdf/2306.02911)
- [Meshtastic Firmware Issue #7980 — Emergency Mode for Critical Packets](https://github.com/meshtastic/firmware/issues/7980)

> **Metodoloji notu:** Program bilgileri 25 Eylül 2026'da program sayfasından ve belge paketinden alınmıştır; 2027 çağrısında koşullar değişebilir. Rakip bilgileri üreticilerin kendi açıklamalarına dayanır. Lifeseeker'ın Türkiye'deki lisans durumu **doğrulanmamıştır** — "BTK izni gerektirir" ifadesi hücresel bantta yayın yapan cihazların genel mevzuat durumuna dayalı bir çıkarımdır ve başvuru öncesi teyit edilmelidir. Konum doğruluğu için hiçbir rakam, 1. ve 4. aşama ölçümleri yapılmadan **iddia edilmeyecektir.**
