# AfetMesh — Sistem Gereksinim Belgesi (SGB)

**Sürüm:** 2.6
**Tarih:** 25 Eylül 2026
**Statü:** ✅ Yürürlükte — **projenin tek yetkili gereksinim kaynağı**

---

## 0. Belgenin Statüsü ve Belge Hiyerarşisi

Bu belge, daha önce yazılmış beş raporun **birleştirilmiş ve çelişkileri giderilmiş** halidir. Bir konuda bu belge ile başka bir rapor çelişirse, **bu belge geçerlidir.**

| Belge | Rolü | Statü |
|---|---|---|
| **`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`** (bu belge) | **Tek yetkili gereksinim kaynağı** | ✅ Yürürlükte |
| `01_Gereksinim_Analizi/Gereksinim_Analizi_Raporu.md` | İlk kapsam çalışması | 📎 Girdi belgesi — v2.0 ile hizalandı |
| `02_Donanim/Donanim_Gereksinim_Raporu.md` | CEP ailesi donanım gereksinimleri, işlemci seçimi, güç bütçesi, malzeme seçimi (v2.0) | 📎 Teknik ek — v2.2 ile hizalandı |
| `02_Donanim/CATI_Node_Donanim_Gereksinim_Raporu.md` | **ÇATI ailesi** (çatı/direk röle düğümleri ÇATI-B/K/O + MERKEZ ağ geçidi): 46 gereksinim, yasal ERP hesabı, güç bütçesi, köprü tasarımı, BOM, kurulum, test (v1.0) | 📎 Teknik ek — v2.5 ile hizalı |
| `04_Dokumanlar/Meshtastic_Sorunlari_ve_Cozum_Arastirmasi.md` | Meshtastic'in 15 sorunluk envanteri, kapasite hesabı, çatı röle saha dersleri, merkeze iletim çözümleri | 📎 Araştırma girdisi (24 Eyl 2026) |
| `05_Cihaz_Tasarimi/CEPT_Dokunmatik_Cihaz_Tasarim_Raporu.md` | CEP-T (e-paper + buton arayüzlü kişisel cihaz) konsept raporu | 📎 Konsept — v1.4, SGB v2.4 ile hizalandı |
| `05_Cihaz_Tasarimi/CEP_Ilk_Prototip_Malzeme_Listesi.md` | PCB öncesi prototip BOM + devreye alma sırası (nRF52840 + e-paper) | 📎 Uygulama belgesi (v2.0) |
| `04_Dokumanlar/Literatur_ve_Topluluk_Arastirmasi.md` | Donanım literatürü: 30 akademik yayın, 26 topluluk kaynağı, 14 ticari cihaz | 📎 Araştırma girdisi (24 Eyl 2026) |
| `04_Dokumanlar/Kaynakca.md` + `04_Dokumanlar/Kaynaklar/Makaleler/` | Birleşik kaynakça + indirilen 18 açık erişimli makale PDF'i | 📎 Kaynak dizini (24 Eyl 2026) |
| `04_Dokumanlar/Ihtiyac_ve_Rekabet_Analizi_Raporu.md` | Pazar ve rekabet araştırması | 📎 Araştırma girdisi (9 Ağu 2026) |
| `04_Dokumanlar/Farklilasma_ve_Urun_Mimarisi_Raporu.md` | Mimari gerekçelendirme | 📎 Karar gerekçesi (9 Ağu 2026) |
| `04_Dokumanlar/Bakanlik_Kilavuzu_Uyum_ve_Bosluk_Analizi.md` | Kurumsal uyum analizi | 📎 Araştırma girdisi (9 Ağu 2026) |
| `04_Dokumanlar/Uluslararasi_Karsilastirma_Raporu.md` | Ülkelerin acil haberleşme mimarileri, 5 katmanlı çerçeve | 📎 Araştırma girdisi (9 Ağu 2026) |
| `06_Basvuru/HANGAR_BIGG_Basvuru_Konsepti.md` | TUSAŞ HANGAR BİGG başvuru yapısı: seçenekler, **HAVA katmanı** gerekçesi, çift kullanım, rekabet (Lifeseeker), iş modeli | 📎 Karar gerekçesi (25 Eyl 2026) — **Seçenek B onaylandı** |

> **Kural:** Yeni bir gereksinim doğduğunda önce **bu belgeye** yazılır. Araştırma raporlarının **orijinal bulguları silinmez**; sonradan alınan kararlar rapora **tarihli güncelleme notu** (`⚠️ vX.Y`) olarak eklenir. *(v2.4: "araştırma raporları güncellenmez" kuralı bu şekilde gevşetildi.)*
>
> **PDF sürümleri:** Her `.md` belgesinin yanında aynı adlı `.pdf` bulunur; PDF'ler md'den üretilir, **md esastır.**

---

## 1. Proje Tanımı

**AfetMesh**, afet sonrasında GSM ve internet altyapısı çöktüğünde çalışan, LoRa tabanlı, merkeziyetsiz bir **halk erişim katmanıdır.**

### 1.1 Tek cümlelik tanım

> AfetMesh, mevcut Meshtastic ağının üzerine oturan; afetzedenin **hiçbir uygulama kurmadan, hiçbir ön hazırlık yapmadan**, yalnızca telefonunun WiFi'ını açarak yardım çağrısı gönderebildiği halk erişim katmanıdır.

> **v2.6 — HAVA katmanıyla genişletilmiş tanım** (kurumsal başvuru ve sunumlarda kullanılır):
> AfetMesh, altyapının çöktüğü bölgede **enkaz altındaki insanları havadan bulan** ve onlarla **uygulama gerektirmeden haberleşen**, yerli, İHA taşınabilir bir yük modülü (**HAVA-Y**) ile onu besleyen kalıcı yer ağıdır. Ayrıntı: Bölüm 1.5 ve 7c.

### 1.2 Çözdüğü problem

Türkiye'deki mevcut afet haberleşme çözümlerinin tamamı (AFAD KGHS, ASELSAN sistemleri, TAMP Haberleşme Hizmet Grubu, amatör telsiz) **kurumlar arası** katmandadır. Bakanlık kılavuzu paydaş listesinde "Vatandaşlar"a yer verir, ancak vatandaşa dokunan tek bir teknik bileşen tanımlamaz.

Sektördeki sivil çözümler (Meshtastic, MeshCore, MeshGrid) ise kullanıcının **afetten önce hazırlık yapmış olmasını** varsayar: cihaz satın almış, uygulama kurmuş, yapılandırmış olmak.

**Boşluk:** Cihazı olmayan, uygulaması olmayan, hazırlık yapmamış vatandaş ile ağ arasındaki son adım. Bu belgede **"Son 300 Metre Problemi"** olarak anılır.

### 1.3 Kurumsal konumlandırma

> AfetMesh, Japonya'nın **171 Afet Acil Haberleşme Sistemi**'nin GSM olmadan çalışan halidir.

Bu, Çevre ve Şehircilik Bakanlığı'nın 2024 tarihli *Afet Sonrası İletişim Sistemleri* kılavuzunda örnek gösterilen ve *"Türkiye'de GSM operatörleri ve AFAD iş birliği ile çalışmalar başlatılan"* modeldir. Aradaki fark taşıyıcıdadır: 171 GSM üzerinden çalışır ve büyük afette (kılavuzun kendi tanımıyla "iletişim tamamen kopar" senaryosunda) devre dışı kalır; AfetMesh LoRa mesh üzerinden çalışır ve altyapı gerektirmez.

### 1.4 İKAS ile ilişki — tamamlayıcılık (rekabet değil)

Türkiye'de AFAD ve TÜBİTAK BİLGEM tarafından geliştirilen **İKAS** (İkaz ve Alarm Bütünleşik Sistemi), **Cell Broadcast** teknolojisiyle **112. kanaldan** vatandaşın telefonuna uyarı göndermektedir. Yani Türkiye'de **uyarı katmanı mevcuttur.**

> **AfetMesh bu katmanın eksiğini kapatmaz — yönünü tamamlar:**
> ### İKAS afeti size haber verir. AfetMesh sizin haberinizi dışarı çıkarır.
> İKAS **aşağı yönlüdür** (kurum → vatandaş). AfetMesh **yukarı yönlüdür** (vatandaş → kurum).

Teknik olarak da çakışmazlar. Bakanlık kılavuzu afet haberleşmesinin üç düşmanını sayar: *enerji kesintileri, fiziksel zararlar, aşırı kullanım yoğunluğu.* Cell Broadcast bunlardan yalnızca **aşırı yoğunluğu** çözer (trafik yükünden etkilenmez); baz istasyonu yıkıldığında veya elektriği kesildiğinde yayın yapacak kimse kalmaz. **AfetMesh diğer iki senaryo için vardır.**

> **Kurumsal iletişim kuralı:** Teklif ve sunumlarda "Türkiye'de böyle bir sistem yok" denmeyecektir — yanlıştır ve gereksiz direnç doğurur. Kullanılacak çerçeve tamamlayıcılıktır.
> Ayrıntı: [`04_Dokumanlar/Uluslararasi_Karsilastirma_Raporu.md`](../04_Dokumanlar/Uluslararasi_Karsilastirma_Raporu.md)

### 1.5 HAVA katmanı ve çift kullanım *(v2.6)*

25 Eylül 2026'da ekip, TUSAŞ HANGAR BİGG başvurusu için **Seçenek B**'yi onayladı: yer ağına bir **İHA yük modülü (HAVA-Y)** ve bir **konum kestirim yazılımı (KONUM MOTORU)** eklenir. Gerekçe: program *"çift kullanım odaklı havacılık ve uzay"* girişimlerini hedefler; yalnızca yerde çalışan bir LoRa ağı bu tanıma zayıf uyar (ayrıntı: `06_Basvuru/HANGAR_BIGG_Basvuru_Konsepti.md`).

| Yetenek | Sivil kullanım | Savunma / güvenlik kullanımı |
|---|---|---|
| Havadan beacon konum tespiti | Enkaz altı afetzede · çığ · kayıp kişi | Personel kurtarma (CSAR): düşen pilot / kayıp personel beacon'ı |
| Uçan erişim noktası | Altyapısı çökmüş bölgede geçici SOS kanalı | Altyapısız bölgede geçici veri rölesi |
| Veri katırı | Kopuk yer düğümlerinden SOS toplama | Kopuk sensör düğümlerinden veri toplama |

**Neden termal kamera yetmez:** Termal kamera beton ve molozun içini görmez. 433/868 MHz ve 2,4 GHz radyo sinyali moloz katmanlarından zayıflayarak da olsa geçer. HAVA-Y termal kameranın **yanına** gelir, yerine değil.

> **Kurumsal iletişim kuralı (v2.6):**
> - "Dünyada böyle bir şey yok" **denmeyecek.** Havadan hücresel telefon tespiti yapan yabancı ürün (Centum **Lifeseeker**, 35+ müşteri) vardır. Doğru çerçeve: *"Pazar kanıtlanmış. Biz yerli, lisans gerektirmeyen bantlarda çalışan ve kalıcı yer ağıyla entegre bir mimari sunuyoruz."*
> - AfetMesh **askeri haberleşme sistemi olarak konumlandırılmayacak.** Meshtastic protokolü açıktır, yayını kolay tespit edilir ve anahtar yönetimi paylaşılan PSK ile sınırlıdır. Savunma tarafındaki değer **haberleşmede değil, konum tespitindedir** (HV-16).
> - Belirli bir TUSAŞ platformuna (ANKA, AKSUNGUR vb.) entegrasyon **vaat edilmeyecek.** İddia: "platform bağımsız yük".

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
- **ÇATI** — çatı/direk röle düğümleri (ÇATI-B bina, ÇATI-K kule, ÇATI-O omurga) ve **MERKEZ** ağ geçidi *(v2.5)*
- **PORTAL** — NOKTA üzerinde çalışan captive portal web arayüzü
- **SOS protokolü** — yapılandırılmış acil durum paketi ve önceliklendirme firmware modülü
- **PANO** — çevrimdışı çalışan koordinasyon/triage arayüzü
- **HAVA-Y** — platform bağımsız İHA yük modülü: havadan sinyal ölçümü, uçan erişim noktası, veri katırı *(v2.6)*
- **KONUM MOTORU** — havadan ölçümlerden kaynak konumu kestiren yer istasyonu yazılımı ve uçuş planı önerici *(v2.6)*
- Kurumsal entegrasyon formatları, regülasyon uyumu, saha test protokolleri

### 3.2 Kapsam dışı

| Kapsam dışı | Gerekçe |
|---|---|
| Sıfırdan LoRa çipi/RF tasarımı | Sertifikalı hazır modül kullanılacak (Donanım Raporu K-4, HW-RF-2) |
| **Yeni bir mesh protokolü yazmak** | Meshtastic tabanı korunacak; ağ etkisi ve birlikte çalışabilirlik buna bağlı |
| **Özel mobil uygulama (Android/iOS)** | İ-1'i ihlal eder. Kullanıcı arayüzü PORTAL'dır |
| Kitlesel üretim ve endüstriyel sertifikasyon | Prototip ve pilot aşamasının dışında |
| Sesli iletişim | LoRa bant genişliği elvermez. Telsiz sistemlerinin yerine geçilmiyor |
| Video/görüntü aktarımı | Aynı gerekçe |
| **İHA platformunun kendisi** *(v2.6)* | Drone hazır alınır; ürün **yük modülüdür** (HV-1). Uçuşu yetkili kurum operatörü yapar (REG-7) |
| **Hücresel bantta yayın (sahte baz istasyonu)** *(v2.6)* | Lisanslı spektrum, BTK ve güvenlik kurumu yetkisi gerektirir. HAVA-Y yalnızca ISM/SRD bantlarında çalışır (REG-1) |
| **Askeri sınıf taktik haberleşme** *(v2.6)* | Kripto onayı, askeri standart testleri ve uzmanlık gerektirir (Başvuru Konsepti Seçenek C — elendi) |

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

> **v2.5 — Çatı röle katmanı ve merkeze iletim.** Yukarıdaki şemaya iki katman eklendi (ayrıntı: Bölüm 7b ve `02_Donanim/CATI_Node_Donanim_Gereksinim_Raporu.md` Bölüm 2):
>
> `CEP (bina içi) → ÇATI-B (bina çatısı) → ÇATI-K (yüksek nokta) → ÇATI-O ══ 2,4 GHz omurga ══ MERKEZ (PANO)`
>
> İlke **"dikey çık, yatay taşı"**: CEP yalnızca kendi binasının çatısına ulaşır. Uzun mesafe, 868 MHz erişim kanalını tüketmeden omurgadan taşınır. Gerekçe (kapasite hesabı): LongFast'te bir röle, %10 çalışma süresi sınırı yüzünden **dakikada yalnızca ~7 paket** yayınlayabilir. Bu yüzden çatı rölesi tek başına kapasite getirmez (`04_Dokumanlar/Meshtastic_Sorunlari_ve_Cozum_Arastirmasi.md` Bölüm 2 ve 4).

> **v2.6 — HAVA katmanı.** Yer ağının üstüne, geçici ve hareketli bir katman eklendi (ayrıntı: Bölüm 7c):
>
> ```
>              HAVA-Y (İHA üzerinde, 50–120 m)
>        ┌────────┼──────────────┬──────────────────┐
>        │ ölçer  │ erişim verir │ toplar           │
>        ▼        ▼              ▼                  │
>   CEP konum   telefon →     NOKTA / ÇATI          │ iniş sonrası
>   beacon'ı    PORTAL        emanet deposu         │ (ve canlı özet)
>   (CEP-12)    (HV-4)        (HV-6)                ▼
>                                     KONUM MOTORU + PANO (yer istasyonu)
>                                     → konum elipsi, ısı haritası, triage
> ```
>
> İlke **"yer kalıcıdır, hava anlıktır"**: yer ağı haftalarca çalışır ve sinyal kaynağıdır. HAVA-Y, yer ağının ulaşamadığı yere dakikalar içinde gelir ve **mesh'e yük bindirmez** (HV-8).

### 4.1 Bileşen özeti

| # | Bileşen | Tip | Rol | Hedef maliyet |
|---|---|---|---|---|
| ① | **CEP** | Donanım (özel PCB) | Kişisel node, mesh taşıyıcı, SOS kaynağı | $25–40 |
| ①+ | **CEP+ / CEP-T** | Donanım (aynı PCB, farklı montaj) | Saha ekibi / gelişmiş kişisel node (e-paper + GNSS + sensör + 5 yön buton) | ~$85–90 *(v2.2'de revize)* |
| ② | **NOKTA** | Donanım (hazır kart reçetesi) | Kamu erişim noktası, router, mesaj deposu | $90–130 |
| ②+ | **ÇATI-B / ÇATI-K / ÇATI-O** *(v2.5)* | Donanım (prototip: hazır kart · üretim: taşıyıcı PCB) | Çatı/direk röle, SOS emanetçisi. ÇATI-O: 2,4 GHz omurga | $200–310 / $330–450 / $440–610 (prototip) |
| ②++ | **MERKEZ** *(v2.5)* | Donanım + yazılım | Kriz merkezinde ağ geçidi: SOS toplar, **SOS-TEYİT** üretir, PANO'yu besler | $800–1.200 |
| ③ | **PORTAL** | Yazılım (gömülü web) | Cihazsız kullanıcı arayüzü | — |
| ④ | **SOS modülü** | Yazılım (firmware) | Yapısal paket + önceliklendirme | — |
| ⑤ | **PANO** | Yazılım (çevrimdışı web) | Koordinasyon ve triage | — |
| ⑥ | **HAVA-Y** *(v2.6)* | Donanım (prototip: hazır kart + 3D baskı kutu) | İHA yük modülü: havadan ölçüm, uçan NOKTA, veri katırı | $100–180 (prototip, tahmini) |
| ⑦ | **KONUM MOTORU** *(v2.6)* | Yazılım (yer istasyonu) | Havadan ölçümlerden konum kestirimi + uçuş planı önerisi. PANO ile aynı makinede | — |

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
| **SYS-7** | Menzil, kapsama ve pil ömrü beyanları **ölçülmüş değerlere** dayanacak; ideal koşul rakamları pazarlama amaçlı kullanılmayacak. *(v2.6: konum doğruluğu beyanları da bu kurala tabidir — HV-N4)* | Test kayıtları |

> ⚠️ **v2.6 — SYS-5 askıda (KONUM MOTORU için):** Yatırım programına başvuru, korunabilir fikri mülkiyet gerektirir. KONUM MOTORU'nun açık kaynak mı yoksa kapalı çekirdek mi olacağı **A-16** ile açık karara bağlandı. Yer ağı yazılımı (PORTAL, SOS modülü, PANO) için SYS-5 geçerliliğini korur.

---

## 6. CEP — Kişisel Node Gereksinimleri

Donanım tasarım detayı için: `02_Donanim/Donanim_Gereksinim_Raporu.md` (bu bileşen için tamamen geçerlidir).

### 6.1 İşlevsel

| # | Gereksinim |
|---|---|
| **CEP-1** | Meshtastic firmware çalıştıracak; MCU **nRF52840**, radyo **SX1262** olacak. Modül: **RAK4630** — (L) 433 MHz / (H) 868 MHz, aynı footprint (Donanım Raporu K-2/K-4) |
| **CEP-2** | Varsayılan rol **`CLIENT_MUTE`** olacak — paket tekrarlamayacak |
| **CEP-3** | Yanlışlıkla basmaya karşı korumalı, ayrı bir **SOS butonu** bulunacak |
| **CEP-4** | **Enkaz Modu:** SOS butonuna uzun basıldığında periyodik sesli sinyal (≈30 sn'de bir çift bip) verecek ve düşük güçte SOS yayınlayacak |
| **CEP-5** | Telefonla eşleşme **BLE** üzerinden olacak (yalnızca cihaz sahibi için; cihazsız kullanıcı erişimi NOKTA üzerindendir) |
| **CEP-6** | Pil ve ağ durumu görsel (LED) ve sesli (buzzer) olarak bildirilecek |
| **CEP-7** | Cihaz **beş güç modunu** destekleyecek: Normal · Tasarruf · Enkaz/Beacon · Kapalı (raf) · Şarj. Tasarruf moduna butonla ve düşük pil eşiğinde otomatik geçilecek (Donanım Raporu Bölüm 5.2) |
| **CEP-8** | **Enkaz/Beacon modu:** radyo alımı kapalı, periyodik SOS yayını + sesli sinyal; hedef ortalama akım ≤ 1 mA. CEP-4'ün düşük güçlü uygulamasıdır; SOS modülü (Aşama 2) ile geliştirilecek |
| **CEP-9** *(v2.5)* | **Afet profili (ağ dostu istemci):** konum, telemetri ve NodeInfo periyodik yayınları kapanır. Konum yalnızca SOS paketinin içinde gider. Kanal doluluğu > %25 iken SOS dışı mesajlar bekletilir, SOS bekletilmez. Sarsıntı algılanınca veya MERKEZ duyurusuyla otomatik devreye girer |
| **CEP-10** *(v2.5)* | **Üç aşamalı teslim göstergesi:** "Gönderildi → Çatıya ulaştı (örtük ACK) → MERKEZ aldı (SOS-TEYİT)". LED + e-paper. Amaç: kullanıcının tekrar tekrar basmasını kaynağında engellemek (Helene dersi) |
| **CEP-11** *(v2.5)* | Kendi binasının ÇATI-B'sini ve MERKEZ'i **favori** olarak tutacak (NodeDB'den atılmaz, sahteciliğe karşı korunur) |
| **CEP-12** *(v2.6)* | **Konum beacon'ı (Enkaz modunda):** SOS paketinden ayrı, yalnızca cihaz kimliği (SOS-9) + sayaç + pil seviyesi taşıyan **≤ 12 baytlık** kısa çerçeve. Hızlı modülasyonla (ör. SF7, ~30 ms hava süresi), **10–30 s** aralıkla gönderilir. **Mesh'te tekrarlanmaz** (sıçrama sınırı 0) — yalnızca HAVA-Y ve yakındaki ÇATI/NOKTA dinler. Hava süresi oranı ≤ %0,3 (REG-2 içinde). Ortalama akım bütçesi CEP-8'in ≤ 1 mA hedefi içinde kalır |

> **CEP-2 gerekçesi:** Meshtastic'te her node varsayılan olarak gördüğü paketi tekrarlar. Toplanma alanında yüzlerce CEP aynı anda tekrarlarsa kanal tıkanır. Tekrarlama görevi NOKTA'lara aittir.

> **CEP-12 gerekçesi (v2.6, Ç-15):** Havadan konum kestirimi, drone'un üzerinden geçtiği birkaç dakika içinde **onlarca ölçüm** ister. SOS-3 ise aynı kaynaktan 5 dakikada en fazla 1 SOS paketine izin verir; bu hızla bir uçuş geçişinde 0–1 ölçüm alınır. Çözüm SOS-3'ü gevşetmek **değil**, mesh'e girmeyen ayrı ve çok kısa bir konum çerçevesidir. Güç hesabı (tahmini): 30 ms × ~100 mA / 10 s ≈ **0,3 mA** ortalama. Uygulama yolu (ham LoRa çerçevesi mi, Meshtastic paketi mi) açık karar **A-15**.

### 6.2 İşlevsel olmayan

| # | Gereksinim | Hedef |
|---|---|---|
| **CEP-N1** | Birim maliyet | $25–40 (100 adette hedef $25) |
| **CEP-N2** | Pil ömrü | Tek şarjla ≥ **72 saat** aktif çalışma — **GNSS sürekli açık en kötü durumda dahi** |
| **CEP-N3** | Kapalı (raf) modu akımı | **≤ 10 µA** *(v2.2: < 100 µA'dan sıkılaştırıldı — cihaz çekmecede aylarca bekler)* |
| **CEP-N4** | Pil | 18650, **kullanıcı tarafından değiştirilebilir** |
| **CEP-N5** | Koruma sınıfı | **IP54** (asgari) |
| **CEP-N6** | Çalışma sıcaklığı | −10 °C … +50 °C |
| **CEP-N7** | Ağırlık | < 150 g |
| **CEP-N8** | Kurulum süresi | Deneyimsiz kullanıcı için < 15 dakika |
| **CEP-N9** | Dayanıklılık | 1,5 m'den betona düşme testini geçecek |
| **CEP-N10** | Normal mod ortalama akım | **≤ 8 mA** (GNSS kapalı) → 3000 mAh ile hedef **≥ 14 gün**. SYS-7 gereği ölçülmeden beyan edilmez |
| **CEP-N11** | Raf ömrü | Tam şarjlı cihaz kapalı olarak 6 ay sonra ≥ %75 şarj |
| **CEP-N12** | Pil gerilimi kullanım aralığı | 3,3 V hat pil **3,0 V'a inene kadar** korunacak (buck-boost); soğukta TX anında brownout olmayacak |

### 6.2b Donanım Özeti (Donanım Raporu v2.1'den — bağlayıcı seçimler)

| Alt sistem | Seçim | Gereksinim / karar |
|---|---|---|
| İşlemci + radyo | **RAK4630** — nRF52840 + SX1262 (TCXO); (L) 433 MHz / (H) 868 MHz aynı footprint | CEP-1, A-4 |
| Anten | LoRa: SMA bulkhead + harici ½λ whip, 50 Ω CPWG, π-eşleme · BLE: 2,4 GHz çip anten | HW-RF-5…8 |
| Pil | 18650 Li-ion 3000–3500 mAh, aletsiz değiştirilebilir, ters kutup korumalı | CEP-N4 |
| Şarj | BQ24074 power-path + NTC + DW01A/FS8205A | HW-PWR-2…5 |
| Regülatör | **TPS63900 buck-boost 3,3 V** (75 nA Iq) — yedek: 3,0 V LDO | CEP-N12, A-8 |
| Güç dağıtımı | TPS22917 yük anahtarları (ekran / sensör / GNSS) + MAX17048 yakıt göstergesi | CEP-N3 |
| Ekran | **2,9" e-paper (SSD1680, InkHUD)** — dokunmatik yok | A-7 ✅ |
| Giriş | Korumalı SOS + güç butonu; CEP+/CEP-T'de 5 yön + Seç + Geri | CEP-3 |
| Bildirim | Piezo (≥ 85 dB @10 cm), ayrık RGB LED (WS2812B yasak), titreşim (CEP+/T) | CEP-4, CEP-6 |
| Sensör (CEP+/T) | SHT40 · LSM6DS3TR-C (6 eksen) · u-blox MAX-M10S GNSS | HW-SNS-* |
| Programlama | SWD (TC2030) + USB-C UF2 | HW-MCU-5/6 |
| PCB | 4 katman, ENIG, ~90 × 55 mm | HW-PCB-* |
| Güç modları | Normal · Tasarruf · Enkaz/Beacon · Kapalı · Şarj | CEP-7, CEP-8 |

> **İşlemci seçim gerekçesi (özet):** TI CC13xx (LoRa yok), STM32WL (BLE yok, Meshtastic'te uyku yok), RP2040/RP2350 (BLE desteklenmiyor), Raspberry Pi Zero 2 W (~0,5–0,9 W), ESP32-S3 (el cihazlarında ölçülen 10–30 saat) ve nRF54L15 (deneysel port) karşılaştırıldı; **nRF52840 460/500 puanla seçildi.** Ayrıntı: Donanım Raporu Bölüm 4.

### 6.3 CEP+ varyantı (saha ekibi)

Aynı PCB, farklı montaj listesi: **2,9" e-paper + GNSS (MAX-M10S) + SHT40 + 6 eksen IMU + 5 yön buton**. Hedef maliyet **~$85–90** *(v2.2: ~$59'dan revize — gerekçe Donanım Raporu Bölüm 8; L76K GNSS ile ~$8 düşer ama GNSS açık pil ömrü 7,5 → 3 güne iner)*.

> **CEP-T ile ilişki (v2.3):** A-7 kapandı — dokunmatik ekran **zorunlu değildir**. CEP-T, CEP+ montajının 5 yönlü butonlu hâlidir ve **ayrı PCB gerektirmez**. Arayüz: 2,9" e-paper + fiziksel butonlar (nRF52840). Rol `CLIENT` (tekrarlama açık — saha ekibi hareketli menzil uzatıcısı olarak çalışır).

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

> **NOK-N8 gerekçesi:** Kamu node'ları adetçe azdır (bir mahallede 3–5) ve maliyete duyarsızdır — solar panel zaten karttan pahalıdır. Donanım Raporu Bölüm 8'deki *"düşük adette özel PCB pahalıya gelir"* tespiti burada geçerlidir.

---

## 7b. ÇATI — Çatı / Direk Röle Düğümleri ve MERKEZ Ağ Geçidi *(v2.5)*

Donanım tasarım detayı: `02_Donanim/CATI_Node_Donanim_Gereksinim_Raporu.md` (46 gereksinim: ÇT-GEN, ÇT-RF, ÇT-PWR, ÇT-ENV, ÇT-MEC, ÇT-DBG). Araştırma gerekçesi: `04_Dokumanlar/Meshtastic_Sorunlari_ve_Cozum_Arastirmasi.md`.

### 7b.1 Sınıflar

| Sınıf | Yer | Rol | Radyo | Enerji |
|---|---|---|---|---|
| **ÇATI-B** | Konut/iş bloğu çatısı (her blok, ~300–500 m) | `ROUTER_LATE` (A-10) | nRF52840 + SX1262 (RAK4630/4631) | 1S LFP ~38 Wh + 10 W panel |
| **ÇATI-K** | En yüksek bina, su/itfaiye kulesi, tepe (~1–2 km) | `ROUTER` | + kavite filtre, isteğe bağlı LNA ön uç (A-11) | 12 V LFP ~77 Wh + 20 W |
| **ÇATI-O** | Seçilmiş ÇATI-K noktaları (1,5–3 km) | 868: `ROUTER` · omurga: `ROUTER` | + **2,4 GHz LoRa (LR1121, `LORA_24`)** ayrı düğüm + köprü modülü | 12 V LFP 128–154 Wh + 30–40 W |
| **MERKEZ** | Belediye kriz merkezi / AFAD il | Ağ geçidi | ÇATI-O takımı | Şebeke + jeneratör + UPS |

### 7b.2 İşlevsel

| # | Gereksinim |
|---|---|
| **ÇAT-1** | Erişim radyosu **nRF52840 + SX1262** ve **upstream Meshtastic** olacak. AfetMesh işlevleri ayrı modül (SYS-3/4) |
| **ÇAT-2** | Rol hiyerarşisi: yüksek noktada az sayıda `ROUTER`, bina çatısında `ROUTER_LATE`. **Birbirini doğrudan duyan iki `ROUTER` olmayacak** |
| **ÇAT-3** | Verici gücü **yasal ERP'den geriye** hesaplanıp sabitlenecek. **Yalnız PA'lı 1 W güçlendirici kullanılmayacak** (TR 868'de 6 dBi antenle yasal üst sınır ~24,6 dBm) |
| **ÇAT-4** | Operatör vericisi bulunan çatılarda **868 MHz bant geçiren filtre** zorunlu (≤ 1 dB kayıp, 833 MHz'te ≥ 40 dB). ÇATI-K/O'da her durumda zorunlu |
| **ÇAT-5** | **SOS emanetçisi:** aldığı her SOS'u kalıcı hafızaya yazacak (≥ 2.000 kayıt) ve MERKEZ teyidi gelene kadar yeniden sunacak (SOS-8) |
| **ÇAT-6** | **Afet profili:** sarsıntı algılanınca veya MERKEZ komutuyla telemetri, konum ve NodeInfo durur, SOS önceliği ve emanet açılır (NOK-8 ile aynı mantık) |
| **ÇAT-7** | `rebroadcast_mode = ALL`. **`CORE_PORTNUMS_ONLY` kullanılmayacak** (A-12) |
| **ÇAT-8** | **ÇATI-O köprüsü:** 868 ile omurga arasında yalnızca beyaz listedeki trafik aktarılacak (SOS, SOS-TEYİT, hücresi bilinen DM'ler, hız sınırlı duyurular). Omurga trafiği erişim hücrelerine toptan basılmayacak |
| **ÇAT-9** | **MERKEZ**, aldığı her yeni SOS için **SOS-TEYİT** üretecek (SOS-7), kayıtları PANO'ya aktaracak, internet veya uydu varsa özel MQTT broker'ı üzerinden üst merkeze iletecek |
| **ÇAT-10** | Kurulumdan sonra uzaktan yönetilebilir (PKC admin key). BLE kapatılabilir. Sağlık telemetrisi ≥ 3 saatte bir |
| **ÇAT-11** | Planlama: her sokak ya da blok **en az 2 ÇATI** tarafından duyulacak (N+1, deprem hasarı yedekliliği). Konumlandırma NOK-11 verileriyle yapılacak |

### 7b.3 İşlevsel olmayan

| # | Gereksinim | Hedef |
|---|---|---|
| **ÇAT-N1** | Şebekesiz güneşsiz otonomi | **≥ 7 gün** (Aralık ışınımıyla boyutlandırma, ≥ 2× panel marjı) |
| **ÇAT-N2** | Pil kimyası | **LiFePO4** (Li-ion yalnızca prototipte). Şarj 0…45 °C dışında donanımsal kesme |
| **ÇAT-N3** | Koruma sınıfı | **IP67**, açık renk, basınç dengeleme ventili |
| **ÇAT-N4** | Kendiliğinden toparlanma | Pil sıfırlanıp güneş geri gelince insan müdahalesiz açılış |
| **ÇAT-N5** | Montaj | Taşıyıcı elemana. Baca, su deposu ve güneş enerjisi su ısıtıcısına montaj yok. Balastlı ayak yok |
| **ÇAT-N6** | RF hat | Radyo antenin dibinde (≤ 1 m koaksiyel), N tipi, DC topraklı parafudr + binaya eş potansiyel bağlantı |
| **ÇAT-N7** | Birim maliyet (prototip) | ÇATI-B $200–310 · ÇATI-K $330–450 · ÇATI-O $440–610. **Üretim hedefi ÇATI-B ≤ $180** (taşıyıcı PCB, 100 adet) |

> **ÇAT-3 gerekçesi:** TR sınırları 868 MHz'te 500 mW ERP, 433 MHz'te 10 mW ERP'dir (REG-2). 1 W PA 868'de en fazla +2,6 dB yasal kazanç sağlar ve yalnız PA'lı tasarımlar alıcıyı sağırlaştırarak asimetrik bağlantı üretir. Menzil kazancı alıcı tarafında (filtre, yükseklik, düşük kayıplı hat) aranır.

---

## 7c. HAVA — İHA Yük Modülü ve Konum Motoru *(v2.6)*

Karar gerekçesi: `06_Basvuru/HANGAR_BIGG_Basvuru_Konsepti.md`. Bu bölüm, HANGAR BİGG başvurusunda projenin **anlatı merkezidir**. Yer ağı (CEP, NOKTA, ÇATI, MERKEZ) bu katmanın sinyal kaynağı ve veri altyapısıdır.

### 7c.1 HAVA-Y — İşlevsel

| # | Gereksinim |
|---|---|
| **HV-1** | **Platform bağımsız yük:** kendi pili ve kendi GNSS'i olacak; İHA'ya elektriksel veya veri bağlantısı **gerektirmeyecek**. Basit mekanik montaj (kelepçe / standart bağlantı). Harici besleme girişi (5–12 V) kablolu (tethered) İHA kullanımı için isteğe bağlı |
| **HV-2** | Donanım: **ESP32-S3 + PSRAM** (NOKTA ile aynı yazılım tabanı) + **iki SX1262** radyo: ① mesh preset'inde dinleme (SOS, emanet senkronu) ② konum beacon'ı preset'inde dinleme (CEP-12) · **u-blox MAX-M10S** GNSS (≥ 5 Hz) · microSD kayıt |
| **HV-3** | **Ölçüm kaydı:** alınan her LoRa çerçevesi için zaman damgası, GNSS konumu ve irtifası, RSSI, SNR, kaynak kimliği ve kanal. Ham kayıt microSD'ye yazılır; silinmez |
| **HV-4** | **Uçan NOKTA:** WiFi AP + captive portal (NOK-2/3, POR-* aynen). SSID `AFETMESH-HAVA`. Bağlanan telefonun sinyal gücü, kullanıcının gönderdiği SOS ile birlikte KONUM MOTORU'na girdi olarak kaydedilir |
| **HV-5** | **Pasif WiFi ölçümü** *(A-13 onayına bağlı)*: yalnızca **afet modunda ve yetkili operatör açtığında**. MAC adresleri uçuş oturumuna özgü tuzlu özetle ayrıştırılır, **ham MAC saklanmaz**, özetler görev sonunda silinir (SYS-6, REG-8) |
| **HV-6** | **Veri katırı:** üzerinden geçtiği NOKTA ve ÇATI düğümlerinin emanet deposundaki **teyitsiz** SOS kayıtlarını toplar (SOS-8, SOS-11 ③). SOS-9 kimliğiyle yinelenme üretmez. Aktarım yolu A-14 |
| **HV-7** | **Canlı özet:** uçuş sırasında yer istasyonuna yalnızca özet (kaynak kimliği, en güçlü RSSI ve konumu, yeni SOS sayısı) iletilir. Tam hesap iniş sonrası ham kayıtla yapılır |
| **HV-8** | **Mesh'e yük bindirmeme:** varsayılan rol **`CLIENT_MUTE`**. 50–120 m irtifadaki bir düğüm çok sayıda hücreyi aynı anda duyar; tekrarlama yaparsa hepsini tıkar. Röle modu yalnızca operatör açarsa ve yalnızca **beyaz listeli trafik** (ÇAT-8 mantığı: SOS, SOS-TEYİT) için çalışır |

### 7c.2 KONUM MOTORU — İşlevsel

| # | Gereksinim |
|---|---|
| **HV-10** | Girdi: HV-3 ölçüm kayıtları. Çıktı: kaynak başına **konum kestirimi + %95 güven elipsi + kullanılan ölçüm sayısı** |
| **HV-11** | Yöntem: yol kaybı parametrelerini ölçümlerle birlikte kestiren model + **parçacık filtresi** (veya eşdeğer olasılıksal yöntem). Çıktı hiçbir zaman tek nokta olarak sunulmaz |
| **HV-12** | Tamamen **çevrimdışı** çalışır; PANO ile aynı dizüstü bilgisayarda (PAN-5) |
| **HV-13** | **Uçuş planı önerici:** ilk tarama geçişinden sonra olasılığı yüksek bölgeler için daha sık ve alçak ikinci geçiş rotası önerir. Çıktı standart görev noktası dosyası (KML ve yaygın yer kontrol yazılımı formatları) |
| **HV-14** | **PANO entegrasyonu:** konum elipsleri ve sinyal ısı haritası PANO haritasında katman olarak gösterilir. Beacon kimliği, aynı cihazın SOS kaydıyla (SOS-9) eşleştirilir → "enkaz altında, 3 kişi, konum elipsi ±X m" |
| **HV-15** | Birden fazla uçuşun ölçümleri **birleştirilebilir**; her yeni uçuş kestirimi daraltır |
| **HV-16** | **Protokol bağımsızlığı:** motor ölçümü soyut biçimde alır (kaynak kimliği, RSSI, SNR, alıcı konumu, zaman). Beacon protokolü değişse de (ör. savunma sürümünde farklı beacon) motor değişmeden çalışır |

### 7c.3 İşlevsel olmayan

| # | Gereksinim | Hedef |
|---|---|---|
| **HV-N1** | Ağırlık (anten + pil + kutu dahil) | **≤ 300 g** |
| **HV-N2** | Kendi pille çalışma süresi | ≥ 2 saat (birden fazla uçuş) |
| **HV-N3** | Birim maliyet (prototip, tahmini) | $100–180 |
| **HV-N4** | Konum doğruluğu | **Tasarım hedefi** açık alanda ≤ 20 m (%95 elips yarıçapı). Enkaz ortamı hedefi TST-16 sonrası belirlenir. **SYS-7 gereği ölçülmeden beyan edilmez** |
| **HV-N5** | Hazırlık süresi | Montaj + açılış < 5 dk, tek düğme |
| **HV-N6** | Çalışma sıcaklığı | −10 °C … +45 °C |
| **HV-N7** | Koruma | IP54 (yağmurda kısa uçuş) |

> **Neden iki SX1262 (HV-2):** Bir SX1262 aynı anda yalnızca tek modülasyon ayarında dinler. Mesh trafiği TA Mesh preset'indedir (A-2), konum beacon'ı ise kısa hava süresi için hızlı bir preset kullanır (CEP-12). Tek radyoyla ikisi arasında geçiş yapmak, drone'un geçiş süresindeki ölçümlerin yarısını kaybettirir.

> **Kanıt tabanı:** Çığ senaryosunda drone + gömülü LoRa vericisiyle RSSI/SNR ölçümüne dayalı konumlama deneyleri, yeterli ölçüm sayısında **metre mertebesinde** doğruluk raporlamıştır (Başvuru Konsepti Kaynaklar). Enkaz ortamı karmaşıktır; bu sonuçlar **doğrudan aktarılmaz**, TST-14…16 ile ölçülür.

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
| **SOS-7** *(v2.5)* | **Uçtan uca teyit:** MERKEZ her yeni SOS için `SOS-TEYİT` paketi üretecek. Teyit kaynağa **DM olarak** (next-hop yönlendirme, 2.6+) gidecek, taşkınla yayılmayacak |
| **SOS-8** *(v2.5)* | **Emanet zinciri:** SOS'u ilk alan ÇATI veya NOKTA kaydı kalıcı hafızaya yazar. MERKEZ teyidi gelene kadar artan aralıklarla (5/10/20/40 dk) yeniden sunar. Teyitten sonra "teslim edildi" olarak işaretler, silmez |
| **SOS-9** *(v2.5)* | Her SOS **8 baytlık benzersiz kimlik** (düğüm no + sayaç) taşır. Yinelenen kopyalar her katmanda elenir |
| **SOS-10** *(v2.5)* | **Patlama önleme (CEP ve PORTAL):** ilk gönderimde 0–30 s rastgele gecikme. Teyit gelmezse üstel geri çekilme (2, 4, 8 dk). SOS-3 hız sınırı geçerli kalır |
| **SOS-11** *(v2.5)* | **Yedek teslim yolları:** ① mesh + omurga (birincil) · ② internete kavuşan herhangi bir düğümden özel MQTT broker'ına yükleme · ③ saha ekibi veya araçla veri katırı (NOKTA/ÇATI deposunun kopyalanması) · ④ MERKEZ'de uydu. Kimlik (SOS-9) sayesinde yollar yinelenme üretmez |

> **SOS-2 gerekçesi:** Meshtastic'te acil mesajların önceliği **yoktur**; SOS paketleri normal paketlerle aynı kuyruğa girer ve yoğun trafikte gecikebilir veya düşebilir. Upstream'de bu yönde bir talep (firmware #7980, Eylül 2025) açılmış ve kapatılmıştır. Bu yetenek **sıfırdan geliştirilecektir** ve projenin en somut özgün katkısıdır.

> **SOS-5 kritiktir:** Paketimiz standart node'lardan geçemezse, TA Mesh ağının üzerine binemez ve node yoğunluğu avantajını kaybederiz. Bu nedenle **yeni bir protokol icat edilmeyecek** — standart Meshtastic `Data` paketi içinde özel `portnum` kullanılacaktır.

> ⚠️ **v2.5 — SOS-5'e yeni risk:** Meshtastic'in `rebroadcast_mode = CORE_PORTNUMS_ONLY` ayarı yalnızca çekirdek portnum'ları (metin, konum, telemetri, NodeInfo, yönlendirme) yeniden yayınlar. Kalabalık kent ağlarında router'lara bu ayar **öneriliyor** (2bn.de, Kasım 2025). Bu ayarı kullanan standart router'lar özel portnum'lu SOS'u **taşımayabilir.** → Açık karar **A-12** (özel portnum mu, `TEXT_MESSAGE_APP` içinde yapılandırılmış metin mi). TST-5 bu senaryoyu kapsayacak şekilde genişletildi.

> **SOS-7…SOS-11 gerekçesi (Meshtastic sorun envanteri P-4, P-7, P-8, P-13):** Meshtastic'te yayınların uçtan uca teyidi yoktur, "merkez" kavramı yoktur ve MQTT köprüsü afette çöken internete bağlıdır. Teyit görmeyen kullanıcı tekrar gönderir ve tıkanıklık katlanır (Hurricane Helene'de 30 saniyelik durum spamı ağı kilitledi). Teyit, emanet ve kimlik birlikte, **İ-3 "mesaj kaybolmaz" ilkesinin ağ düzeyindeki karşılığıdır.**

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
| **REG-7** *(v2.6)* | HAVA-Y uçuşları **SHGM İHA mevzuatına** uygun yapılacak. Ürün uçuş yapmaz; uçuşu kayıtlı İHA ve yetkili operatör (kurum ekibi) yapar. Afet bölgesinde uçuş, AFAD koordinasyonunda. Prototip testleri yalnızca **izinli alanda** |
| **REG-8** *(v2.6)* | Pasif WiFi ölçümü (HV-5) için KVKK değerlendirmesi yapılacak. Kullanım yalnızca afet modunda ve yetkili kurum eliyle; ham tanımlayıcı saklanmaz |
| **REG-9** *(v2.6)* | Yerden yükseklikte yayın da REG-2 ERP sınırlarına tabidir; HAVA-Y verici gücü yer düğümleriyle aynı yasal hesaba göre sabitlenir (ÇAT-3) |

---

## 12. Açık Kararlar (Karar Bekleyen Konular)

Bu maddeler **bilerek açık bırakılmıştır.** Hiçbiri varsayım yapılarak kapatılmamalıdır.

| # | Karar | Karar ölçütü | Ne zaman |
|---|---|---|---|
| **A-1** | **Frekans bandı: 433 MHz mi 868 MHz mi?** | TA Mesh ağının fiilî bandı + BTK teyidi. **Ağda yalnız kalmamak, teknik üstünlükten önceliklidir** | Aşama 0 |
| **A-2** | Modem preset (`LongFast` mi alternatif mi) | Yoğunluk testi TST-4 sonucu | Aşama 0 sonrası |
| **A-3** | NOKTA için hazır kart modeli | Güncel fiyat/stok + TST-2 sonucu | Aşama 1 |
| **A-4** | CEP modülü — *v2.2: **RAK4630 önerildi*** (HT-N5262M'nin 433 MHz sürümü yok) | Güncel fiyat/stok teyidi | Aşama 3 |
| ~~**A-7**~~ ✅ | **KAPANDI (24 Eyl 2026): Yol A** — nRF52840 + 2,9" e-paper + fiziksel butonlar. Kullanıcı kararı: dokunmatik ekran zorunlu değil. ~~Yol B (ESP32-S3 + dokunmatik TFT)~~ elendi | Pil ömrü önceliği (Donanım Raporu P1) ↔ dokunmatik isteği | Aşama 3 öncesi |
| **A-8** | 3,3 V regülatör: TPS63900 buck-boost mu, 3,0 V LDO mu? Öneri: **TPS63900** | İlk kartta alıcı gürültü tabanı ölçümü (Donanım Raporu T-06) | Aşama 3 ilk tur |
| **A-5** | Pilot bölge ve muhatap kurum | İlçe belediyesi bilgi işlem / afet koordinasyon birimi ile görüşme | Aşama 1 |
| **A-6** | PORTAL'da mesaj saklama süresi ve silme politikası | KVKK değerlendirmesi | Aşama 1 |
| **A-9** *(v2.5)* | **Omurga teknolojisi:** 2,4 GHz LoRa (öneri) · 433 MHz ikinci radyo · 5 GHz Wi-Fi PtP (yalnızca şebekeli noktalar) | TST-9 menzili + REG-4 yasal teyit (ETSI EN 300 328 / BTK) | Aşama 5a |
| **A-10** *(v2.5)* | **ÇATI-B rolü:** `ROUTER_LATE` (öneri) · `CLIENT_BASE` (bina sakinlerinin CEP'leri favori) | Favori sayısı sınırı, kurulum yükü + TST-8 | Aşama 5a |
| **A-11** *(v2.5)* | **ÇATI-K'da LNA'lı ön uç** (SKY66122, TX ≤ 24 dBm) kullanılsın mı? | TST-10: gürültü tabanı + paket alım oranı + güç farkı | Aşama 5a |
| **A-12** *(v2.5)* | **SOS kodlaması:** özel portnum (SOS-1) · `TEXT_MESSAGE_APP` içinde yapılandırılmış metin (`#SOS1 …`) · ikisi birden | TST-5: `CORE_PORTNUMS_ONLY` ayarlı standart router'dan geçiş | **Aşama 0** |
| **A-13** *(v2.6)* | **Pasif telefon WiFi ölçümü (HV-5) kapsamda mı?** Kapsam içi (daha çok kaynak) · kapsam dışı (yalnızca CEP beacon'ı + portala bağlanan telefon) | KVKK görüşü (REG-8) + TST-17 katkısı | Aşama H3 |
| **A-14** *(v2.6)* | **Veri katırı aktarım yolu (HV-6):** NOKTA'dan WiFi (öneri) · ÇATI'dan BLE (drone < 30 m yaklaşır) veya LoRa toplu DM | TST-18: aktarım süresi, 2.000 kayıtlık emanet deposu için | Aşama H3 |
| **A-15** *(v2.6)* | **Konum beacon'ı uygulaması (CEP-12):** Meshtastic dışı ham LoRa çerçevesi (öneri — kısa, hızlı preset) · Meshtastic paketi (sıçrama 0) | Enkaz modunda radyonun modül tarafından devralınabilirliği (SYS-4) + TST-19 güç ölçümü | Aşama H1 |
| **A-16** *(v2.6)* | **KONUM MOTORU lisansı:** açık kaynak (SYS-5) · **kapalı çekirdek + açık yer ağı** (yatırım için öneri) | Patent ön araştırması + ekip kararı. Karar verilene kadar motor kodu **açık depoya konmaz** | HANGAR BİGG başvurusundan önce |

> **A-1 ve PCB (v2.2):** RAK4630'un (L) ve (H) sürümleri aynı footprint'i paylaştığı için A-1 kararı artık **PCB layout'unu bloke etmez**; yalnızca modül sipariş kodu, anten ve eşleme değerleri banda göre seçilir.

> **A-1 neden hâlâ açık:** Önceki raporlar bölge ayarını `EU_868` olarak sabitlemişti. Ancak Türkiye'de topluluk ağı ağırlıklı olarak **433 MHz** kullanmaktadır. 868 MHz daha yüksek güç bütçesi sunar (500 mW vs 10 mW ERP), ancak yanlış band seçimi bizi mevcut ağdan tamamen koparır. Node yoğunluğu bu projede menzilden daha kritik bir başarı faktörü olduğu için, karar ölçümle değil **ağ gerçeğiyle** verilecektir.

> **A-1'e yeni girdi (v2.5, ÇATI raporu Bölüm 5.1):** 433 MHz'in 10 mW ERP sınırı **çatı rölelerini de bağlar.** 5 dBi antenli bir ÇATI'nın yasal iletim gücü ~8,7 dBm'dir, 868'de ise 22 dBm. Çatı katmanının verici link bütçesi 433'te **~13 dB zayıftır.** 433 seçilirse ÇATI yoğunluğu artırılmalı ve bu maliyet A-1 kararında hesaba katılmalıdır.

> **A-1'e yeni girdi (v2.6, HAVA katmanı):** Düşük frekans moloz ve betondan daha iyi geçer. Enkaz altı konum beacon'ı (CEP-12) için **433 MHz lehine** bir teknik gerekçe doğmuştur. HAVA-Y alıcı olduğu için 433'ün 10 mW ERP sınırı beacon menzilini (drone doğrudan üstte, 50–120 m) büyük ölçüde etkilemez. TST-16 her iki bantta ölçülecektir.

> **A-2'ye yeni girdi (v2.5):** Kapasite hesabı (Araştırma Bölüm 4): 100 cihaz, dakikada 1 mesaj, düz mesh'te kanal doluluğu LongFast'te %564, MediumFast'te %162, ShortFast'te %51. Hızlı preset TA Mesh ile birlikte çalışmayı bozar (SYS-3). **Önerilen uzlaşma:** erişim kanalı TA Mesh preset'inde kalır, çok sıçramalı taşıma ÇATI-O omurgasına alınır (A-9). Bu durumda hücre başına 25 cihazda MediumFast %20, LongFast %71 doluluk verir. LongFast'te kalınırsa **SOS disiplini (SOS-3, CEP-9) zorunludur.**

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
| **TST-5** | Uyumluluk: standart Meshtastic node'u SOS paketimizi taşıyor mu. *(v2.5: rebroadcast modu `ALL`, `LOCAL_ONLY` ve **`CORE_PORTNUMS_ONLY`** olan router'larla ayrı ayrı → A-12)* | SOS-5 doğrulanır | Yüksek |
| **TST-6** | Kullanılabilirlik: 10 teknik olmayan gönüllü, yönlendirmesiz | ≥ 8/10 başarı, ortalama < 90 sn | Orta |
| **TST-7** | **"Şebekesiz gün" tatbikatı:** GSM ve internet erişimi kapalı varsayılarak, pilot bölgede gerçek katılımcılarla uçtan uca senaryo | SYS-1 doğrulanır; ölçülmüş saha verisi üretilir | Orta |

> **TST-7 modeli:** Tayvan, 2026 kentsel dayanıklılık tatbikatlarında **ilk kez ulusal ölçekte mobil internet kesintisi simülasyonu** uygulamıştır. Aynı yaklaşım pilot bölgede uygulanmalıdır — laboratuvar testinden farklı olarak gerçek kullanıcı davranışını ölçer ve Bakanlık kılavuzunun *"toplumun hazırlıklı olması için tatbikatlar düzenlenmesi"* maddesiyle örtüşür.

> **TST-1 ve TST-2 tamamlanmadan PCB tasarımına başlanmayacaktır.**

**v2.5 — ÇATI katmanı testleri** (ayrıntı ve başarı kriterleri: `02_Donanim/CATI_Node_Donanim_Gereksinim_Raporu.md` Bölüm 12):

| # | Test | Başarı kriteri | Öncelik |
|---|---|---|---|
| **TST-8** | Bina içi dikey bağlantı: 8+ katlı binada bodrum, zemin, orta ve üst kattan CEP → ÇATI-B | Zemin kattan ≥ %90 teslim (ölçüm beyan edilir) | Yüksek |
| **TST-9** | 2,4 GHz omurga menzili, çatıdan çatıya 0,5 / 1 / 2 / 3 km | 2 km'de ≥ %95 → A-9 | Yüksek |
| **TST-10** | Operatör vericili çatıda filtreli ve filtresiz gürültü tabanı. LNA'lı ve LNA'sız | A-11 kararı | Orta |
| **TST-11** | 14 günlük kış enerji testi (Aralık–Ocak) | Pil hiçbir gün < %30 | Yüksek |
| **TST-12** | Emanet zinciri: yol üstündeki ÇATI-O 1 saat kapalıyken SOS | Kayıp yok, teyit döner, yinelenme yok (SOS-7/8/9) | Yüksek |
| **TST-13** | Patlama: 50 CEP aynı dakikada SOS (jitter açık/kapalı) | Jitter açıkken ≥ %95 SOS 10 dk içinde MERKEZ'de (SOS-10) | Yüksek |

**v2.6 — HAVA katmanı testleri:**

| # | Test | Başarı kriteri | Öncelik |
|---|---|---|---|
| **TST-14** | **Dronesuz konum testi:** beacon yere / bodruma / araç bagajına konur; GNSS'li alıcı yürüyerek, direkle veya araçla ızgara deseninde gezdirilir; KONUM MOTORU kestirir | Gerçek konum %95 elipsin içinde; elips yarıçapı ölçüm sayısına göre raporlanır. **HAVA katmanının ilk ve en kritik testi — drone gerektirmez** | ⚠️ En yüksek |
| **TST-15** | Açık alan uçuş testi: gömülü / örtülü beacon, 50 / 80 / 120 m irtifa, çim biçme deseni | HV-N4 hedefiyle karşılaştırma; ölçüm/geçiş sayısı | Yüksek |
| **TST-16** | Enkaz benzeri ortam: yıkım sahası, otopark bodrumu veya eğitim enkazı. **433 ve 868 MHz ayrı ayrı** | Beacon alım oranı ve konum hatası; A-1 girdisi | Yüksek |
| **TST-17** | Uçan NOKTA: 50–120 m irtifada telefonun `AFETMESH-HAVA` ağına bağlanma ve SOS gönderme menzili (açık alan + bina içi) | Menzil ve başarı oranı raporlanır; A-13 girdisi | Orta |
| **TST-18** | Veri katırı: 2.000 kayıtlı emanet deposunun NOKTA (WiFi) ve ÇATI (BLE / LoRa) üzerinden aktarımı | Aktarım süresi; yinelenme yok (SOS-9) → A-14 | Orta |
| **TST-19** | CEP-12 güç ölçümü: Enkaz modu + konum beacon'ı 10 s ve 30 s aralıkla, PPK2, 24 saat | Ortalama ≤ 1 mA (CEP-8) → A-15 | Yüksek |

### 13.2 Donanım doğrulama

`02_Donanim/Donanim_Gereksinim_Raporu.md` Bölüm 9'daki test listesi (**T-01…T-20**) geçerlidir: güç açılış, SWD/UF2, **kapalı mod ≤ 10 µA**, **normal mod ≤ 8 mA (PPK2, 24 saat)**, 3,0 V'a kadar brownout, **buck-boost alıcı gürültüsü (A-8)**, Meshtastic birlikte çalışabilirlik, çıkış gücü, menzil (elde tutarak dahil), tam deşarj, raf ömrü, −10 °C, şarj sıcaklık kesmesi, ters pil, piezo ses basıncı, buton-only gezinme, düşme algılama, 1,5 m düşme. PCB öncesi prototip sırası: `05_Cihaz_Tasarimi/CEP_Ilk_Prototip_Malzeme_Listesi.md` (B-1…B-11).

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
| **2 — SOS modülü** | SOS-1…SOS-11 firmware modülü (v2.5: teyit, emanet, kimlik, patlama önleme eklendi) | 4–6 hafta | Upstream'e katkı adayı |
| **3a — CEP breadboard prototipi** | nRF52840 Pro Micro + SX1262 + 2,9" e-paper + butonlar; PPK2 ile güç ölçümü (`05_Cihaz_Tasarimi/CEP_Ilk_Prototip_Malzeme_Listesi.md`) | 2–3 hafta | Ölçülmüş pil ömrü, çalışan `PRIVATE_HW` varyantı |
| **3b — CEP PCB** | Donanım Raporu Bölüm 12 adımları | 8–12 hafta | 5 adet PCB |
| **4 — PANO** | Koordinasyon arayüzü | 3 hafta | Çevrimdışı web panosu |
| **4b — ÇATI prototipi** *(v2.5)* | Hazır kartlarla 3 ÇATI-B + 1 ÇATI-K + 1 ÇATI-O + MERKEZ. Köprü modülü. TST-8…TST-13 (`02_Donanim/CATI_Node_Donanim_Gereksinim_Raporu.md` Bölüm 8.1) | 6–8 hafta | Ölçülmüş kat zayıflaması, omurga menzili, kış enerji verisi |
| **5 — Pilot** | Bir mahalle: 3 NOKTA + **8–15 ÇATI-B + 2–3 ÇATI-K + 1–2 ÇATI-O + MERKEZ** + 20 CEP + saha tatbikatı | 4 hafta | Ölçülmüş saha verisi |
| **5b — ÇATI taşıyıcı PCB** *(v2.5)* | RAK4630 + BQ25798 MPPT + emanet flash + bekçi köpeği (ÇATI raporu Bölüm 8.2) | 6–8 hafta | Üretime uygun ÇATI-B/K |

**v2.6 — HAVA katmanı iş kolu** (yer ağıyla **paralel** yürür; hedef: 2027 HANGAR BİGG başvurusunda çalışan prototip + ölçülmüş veri, TRL 2 → 4):

| Aşama | İçerik | Süre | Çıktı |
|---|---|---|---|
| **H1 — Dronesuz konum testi** | Herhangi bir LoRa kartı beacon olarak (CEP-12 taslağı) + GNSS'li alıcı + KONUM MOTORU ilk sürümü. TST-14, TST-19. A-15 kararı | 4–6 hafta | Doğruluk grafiği (kestirim ↔ gerçek konum) |
| **H2 — HAVA-Y prototipi** | Hazır ESP32-S3 kart + 2× SX1262 + MAX-M10S + LiPo + 3D baskı kutu. HV-1…HV-8 | 4 hafta | Ağırlık, pil süresi, çalışan kayıt |
| **H3 — İlk uçuşlar** | Hazır drone ile izinli alanda TST-15, TST-17, TST-18. A-13, A-14 kararları | 3 hafta | **1–2 dakikalık demo videosu** |
| **H4 — Enkaz benzeri test** | TST-16, 433 / 868 karşılaştırması. Mümkünse AFAD/JAK eğitim alanında | 3 hafta | Moloz altı zayıflama ve konum hatası verisi |

> **H1 neden ilk:** Drone yalnızca alıcıyı yukarı taşır; **algoritma yerde kanıtlanabilir.** H1 neredeyse maliyetsizdir ve jürinin en önemli sorusunu ("gerçekten bulabiliyor mu?") ölçümle cevaplar. H1 için Aşama 3a (CEP breadboard) beklenmez.

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
| RAK4630 tek kaynaklı modül | Orta | Yedek yol belgelendi: MDBT50Q-1MV2 + Ebyte E22 (Donanım Raporu 6.1) |
| Buck-boost anahtarlama gürültüsü LoRa hassasiyetini düşürür | Orta | Layout kuralı HW-PCB-4; ilk turda T-06 ölçümü; 3,0 V LDO yedeği (A-8) |
| Özel PCB'de kapalıyken kaçak akım (topluluk kartlarında görülen ~8 mA) | Orta | Anahtarlı hatlar (HW-PWR-8, HW-MOD-5); T-03 |
| Regülasyon belirsizliği | Düşük-Orta | REG-4 erken teyit |
| Paralel topluluk çalışmasıyla çabanın tekrarı | Orta | TA Mesh ile erken temas ve işbirliği |
| **Uydu-doğrudan-telefon (D2C) yaygınlaşması → problemin telefon üreticilerince çözülmesi** | **Orta-Yüksek (orta vade)** | Apple Emergency SOS (Globalstar), Starlink Direct-to-Cell (Text-to-911 beta), AST SpaceMobile hızla yaygınlaşıyor. **Karşı konumlandırma:** D2C açık gökyüzü gerektirir — enkaz altında, bodrumda ve kapalı mekânda çalışmaz; ayrıca yalnızca yeni/pahalı telefonlarda bulunur. AfetMesh telefon modelinden bağımsızdır. **Pilot öncesi bu alan yeniden gözden geçirilecektir** |
| **Çatı rölesi tek başına kapasite getirmez** (LongFast'te röle başına yasal ~7 paket/dk) *(v2.5)* | **Yüksek** | Trafik disiplini (SOS-3, CEP-9), omurga (A-9), preset kararı (A-2). Araştırma Bölüm 4 |
| **Standart router'lar `CORE_PORTNUMS_ONLY` ile SOS'u taşımaz** *(v2.5)* | **Yüksek** | A-12, genişletilmiş TST-5 |
| **SOS patlaması:** deprem anında herkes aynı dakikada basar, teyit görmeyen tekrar basar *(v2.5)* | **Yüksek** | SOS-7 (teyit), SOS-10 (jitter + geri çekilme), CEP-10 (gösterge), TST-13 |
| Çatı kurulumuna sakin itirazı ("anten" / baz istasyonu hassasiyeti) *(v2.5)* | Orta | ÇATI raporu Bölüm 10 adım 3: bilgi notu, düşük güç, kamu binası önceliği |
| 2,4 GHz omurga menzili kentte yetersiz *(v2.5)* | Orta | TST-9 erken. Yedek: ÇATI-O sıklaştırma, MERKEZ yakınında 5 GHz PtP |
| **Moloz altında beacon sinyali beklenenden çok zayıflar** *(v2.6)* | **Yüksek** | 433 MHz seçeneği (A-1 notu); TST-16 erken; doğruluk hedefi ölçümden sonra ilan edilir (HV-N4) |
| RSSI tabanlı kestirimin kentte yansımalarla bozulması *(v2.6)* | Orta | Çok sayıda ölçüm + olasılıksal yöntem (HV-11); çıktı nokta değil güven elipsi; çoklu uçuş birleştirme (HV-15) |
| Afetzedede CEP yok ve telefonun WiFi'ı kapalı *(v2.6)* | Yüksek | İki kaynak birden (CEP-12 + HV-4/HV-5); CEP yaygınlığı belediye dağıtım modeline bağlı |
| Pasif telefon ölçümünün KVKK sorunu doğurması *(v2.6)* | Orta | A-13, REG-8; ham MAC saklanmaz; yalnızca afet modu ve yetkili kurum |
| İHA uçuş izni (SHGM, afet bölgesi) *(v2.6)* | Orta | Ürün platform bağımsız yük (HV-1); uçuşu yetkili kurum yapar (REG-7) |
| Yabancı muadilin (Lifeseeker) Türkiye pazarına girmesi *(v2.6)* | Orta | Lisanssız bant, yerlilik, yer ağıyla entegrasyon, beacon + telefon, maliyet (Bölüm 1.5) |
| Yüksekteki HAVA-Y'nin çok sayıda hücreyi aynı anda tıkaması *(v2.6)* | Yüksek | HV-8: varsayılan `CLIENT_MUTE`, röle yalnızca beyaz listeli trafik |
| Açık depo (MIT) nedeniyle fikri mülkiyetin zayıflaması *(v2.6)* | Orta | A-16; KONUM MOTORU karar verilene kadar açık depoya konmaz |
| Kullanıcıların sesli iletişim beklentisi (metin yetersiz bulunabilir) | Orta | Hurricane Helene saha raporu bu beklentiyi doğruluyor. Ürün sohbet için değil **tek yönlü SOS + triage** için konumlandırılacak; telsizle rekabet edilmeyecek (Bölüm 3.2) |

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
| **Ç-15** *(v2.6)* | Enkaz modunda sinyal sıklığı | CEP-8 + SOS-3: 5 dk'da en fazla 1 SOS → bir drone geçişinde 0–1 ölçüm | **Ayrı konum beacon'ı** (CEP-12): mesh'te taşınmaz, ≤ 12 bayt, 10–30 s aralık. SOS-3 **gevşetilmedi** |

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
| **Donanım Raporu v2.0/v2.1 (24 Eyl 2026)** | | |
| HW-PWR-9/10 (kapalı mod, raf ömrü) | CEP-N3, CEP-N11 | Eklendi |
| HW-MOD-1…5 (güç modları) | CEP-7, CEP-8 | Eklendi |
| HW-BAT-1/2 (≤ 8 mA, ≥ 14 gün) | CEP-N10 | Eklendi |
| HW-PWR-6 (buck-boost, 3,0 V'a kadar) | CEP-N12, A-8 | Eklendi |
| K-4 (RAK4630) | CEP-1, A-4 | Değiştirildi (HT-N5262M → RAK4630) |
| K-6 (e-paper + buton) | A-7 | Kapandı |
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
| **HANGAR BİGG Başvuru Konsepti (25 Eyl 2026)** | | |
| Bölüm 3.4 HAVA-Y | HV-1…HV-8, HV-N1…N7 | Eklendi |
| Bölüm 3.4 KONUM MOTORU, uçuş planı önerici | HV-10…HV-16 | Eklendi |
| Bölüm 9 yol haritası 1–4 | H1…H4, TST-14…TST-19 | Eklendi |
| Bölüm 10 riskler | Bölüm 15 (v2.6 satırları) | Eklendi |
| Bölüm 12 K-3 (motor lisansı) | A-16 | Açık karar |

---

## 18. Değişiklik Geçmişi

| Sürüm | Tarih | Değişiklik |
|---|---|---|
| 1.0 | — | Gereksinim Analizi Raporu (ilk kapsam) |
| 1.1 | — | Donanım Gereksinim Raporu (donanım detayı) |
| **2.0** | **9 Ağu 2026** | Rekabet, mimari ve bakanlık analizleri birleştirildi. 14 çelişki çözüldü. Kamu node'u ayrı cihaz sınıfına alındı. Cihazsız erişim BLE'den WiFi portala taşındı. Gereksinim ID şeması birleştirildi |
| **2.6** | **25 Eyl 2026** | **HAVA katmanı (HANGAR BİGG Seçenek B onayı):** genişletilmiş tanım (1.1) ve **Bölüm 1.5** (çift kullanım, termal kamera karşılaştırması, kurumsal iletişim kuralları — Lifeseeker, askeri iddia yok) · kapsam: HAVA-Y ve KONUM MOTORU içeride; İHA platformu, hücresel bant, askeri taktik haberleşme dışarıda · mimari notu ve bileşen ⑥ ⑦ · **Bölüm 7c: HV-1…HV-8, HV-10…HV-16, HV-N1…N7** · **CEP-12** konum beacon'ı ve **Ç-15** · SYS-5 KONUM MOTORU için askıda, SYS-7 konum doğruluğunu kapsar · **REG-7…REG-9** · yeni açık kararlar **A-13…A-16**, A-1'e 433 MHz girdisi · **TST-14…TST-19** · yol haritasına **H1…H4** · risk kaydına 8 risk · belge tablosuna Başvuru Konsepti |
| **2.5** | **24 Eyl 2026** | **ÇATI katmanı ve merkeze iletim:** yeni bileşenler ÇATI-B/K/O + MERKEZ (4.1, **Bölüm 7b**: ÇAT-1…11, ÇAT-N1…N7) · CEP-9 (afet profili), CEP-10 (üç aşamalı teslim göstergesi), CEP-11 (favoriler) · **SOS-7…SOS-11** (teyit, emanet zinciri, kimlik, patlama önleme, yedek teslim yolları) · SOS-5 için `CORE_PORTNUMS_ONLY` riski · A-1/A-2 notları (433'te çatı link bütçesi −13 dB, kapasite hesabı) · yeni açık kararlar **A-9…A-12** · TST-5 genişletildi, **TST-8…TST-13** · yol haritasına 4b ve 5b · risk kaydına 5 risk · belge tablosuna ÇATI Donanım Raporu ve Meshtastic Sorunları Araştırması |
| **2.4** | **24 Eyl 2026** | Belge ailesi hizalaması: **Bölüm 6.2b Donanım Özeti** eklendi · belge tablosuna prototip BOM, birleşik kaynakça ve makale arşivi eklendi · literatür raporu `04_Dokumanlar/`'a taşındı · araştırma raporu kuralı "tarihli güncelleme notu" olarak gevşetildi · 13.2 test listesi T-01…T-20 ile güncellendi · yol haritasında Aşama 3 → 3a (breadboard) + 3b (PCB) · risk kaydına 3 donanım riski · izlenebilirlik tablosuna Donanım Raporu v2.x eşleşmeleri |
| **2.3** | **24 Eyl 2026** | **A-7 kapandı:** kullanıcı dokunmatik ekranın zorunlu olmadığını bildirdi → CEP-T arayüzü e-paper + fiziksel butonlar (Yol A), MCU nRF52840; CEP-T ayrı cihaz sınıfı değil, CEP+ montaj varyantı |
| **2.2** | **24 Eyl 2026** | Donanım Raporu v2.0 işlendi: işlemci karşılaştırması (TI/STM32/ESP32/Raspberry Pi/Nordic) sonucu **nRF52840 teyit edildi** · modül **RAK4630 (L/H)** önerildi (A-4) · **CEP-7/CEP-8** (güç modları, Enkaz/Beacon) ve **CEP-N10…N12** eklendi · CEP-N3 < 100 µA → ≤ 10 µA · CEP+ maliyet hedefi ~$59 → ~$85–90 · yeni açık kararlar **A-7** (CEP-T dokunmatik ↔ pil) ve **A-8** (regülatör) · belge tablosuna literatür raporu ve CEP-T eklendi |
| **2.1** | **9 Ağu 2026** | Uluslararası karşılaştırma işlendi: **Bölüm 1.4** (İKAS ile tamamlayıcılık konumlandırması) eklendi · risk kaydına **uydu D2C** ve **sesli iletişim beklentisi** riskleri eklendi · **TST-7** "şebekesiz gün tatbikatı" olarak somutlaştırıldı |

---

> **Bu belge canlıdır.** Yeni bir karar alındığında Bölüm 12 (Açık Kararlar) güncellenir, ilgili gereksinim düzenlenir ve Bölüm 18'e kayıt düşülür. Araştırma raporları geriye dönük düzenlenmez.
