# Bakanlık Kılavuzu Uyum ve Boşluk Analizi

**İncelenen belge:** *Afet Sonrası İletişim Sistemleri Uygulaması — Rehberlik Kılavuzu*
**Yayınlayan:** T.C. Çevre, Şehircilik ve İklim Değişikliği Bakanlığı, Coğrafi Bilgi Sistemleri Genel Müdürlüğü — Akıllı Şehir Rehberlik Uygulamaları Projesi, **2024**
**Dayandığı çalışma:** TÜBİTAK-TÜSSİDE, *Esenler Belediyesi Akıllı Şehir Uygulamaları Fizibilite Projesi* (Nisan 2021)
**Rapor tarihi:** 9 Ağustos 2026
**Amaç:** Bakanlık kılavuzunda tanımlanan resmî ihtiyaçları tespit etmek, AfetMesh'in bu ihtiyaçlarla örtüşen ve örtüşmeyen yönlerini belirlemek, projeyi kılavuzun diline ve ölçütlerine hizalamak.

---

## 1. En Önemli Bulgu: Önceki Raporumuzda Bir Düzeltme Gerekiyor

`Ihtiyac_ve_Rekabet_Analizi_Raporu.md` şunu iddia ediyordu:

> *"Devlet kurumları bu projeyi talep etti mi? **Hayır** — Yayımlanmış resmî bir ihtiyaç beyanı, şartname veya ihale bulunamadı."*

**Bu iddia yanlıştır ve düzeltilmiştir.** Bakanlık kılavuzu, tam olarak bu alanda:

- **Resmî bir ihtiyaç tanımı** yapıyor (Bölüm 2.4 "İhtiyaç Analizi")
- Kurumlara **ne yapmaları gerektiğini** anlatıyor ("*bu kılavuz, 'Afet Sonrası İletişim Sistemleri' yapmak isteyen kurum ve kuruluşlara... destekleyici rehber doküman olması amacıyla hazırlanmıştır*")
- Örnek bir **proje büyüklüğü, süresi ve bütçesi** veriyor (1000 Ha / 200.000 kişi / 12 ay / **$1.053.464**)
- **Fizibilite şablonu** sunarak belediyelerin bütçelendirme yapmasını sağlıyor

Yani bu, bir ihale ilanı değil ama ondan daha değerli bir şeydir: **belediyeler için resmî bir satın alma gerekçesi ve bütçe şablonu.** Bir belediyeye teklif götürüldüğünde, o teklifin dayanacağı belge budur.

> **Sonuç:** Talep durumu artık "örtük" değil. Bakanlık, belediyelere bu sistemi kurmalarını **tavsiye ediyor** ve nasıl fizibilite yapacaklarını gösteriyor. Projenin kurumsal kapısı bu belgedir.

---

## 2. Kılavuz Ne Öneriyor?

### 2.1 Kılavuzun sistem mimarisi

Kılavuz, tek bir teknoloji ailesi üzerine kurulu: **sayısal telsiz + araç + tablet + merkez.**

| Bileşen | Adet (örnek vaka) | Birim fiyat | Toplam |
|---|---:|---:|---:|
| Röle alıcıları | 59 | $10.448 | ≈ $614.562 |
| Dokunmatik tablet | 59 | $549 | ≈ $32.281 |
| Telsiz | 118 | $804 | ≈ $94.548 |
| Telsiz rölesi | 2 | (mevcut altyapı kullanılabilir) | — |
| **Donanım toplamı** | | | **$741.391** |

**Yazılım bileşenleri:** Merkezi Ekip Yönetim Uygulaması ($43.232), Telsiz Sunucusu ($19.049), Mobil Haberleşme Yazılımı ($11.418), Röle Kontrol Yazılımı ($30.918) — toplam $104.617.

**Genel toplam (ilk kurulum + 1 yıl bakım):** **$1.053.464** → 200.000 kişi için **kişi başı ≈ $5,27**

### 2.2 Kılavuzun tanımladığı resmî ihtiyaçlar

Bölüm 2.2 ve 2.4'te tekrarlanan gerekçe üç maddede toplanıyor. Sistem şunlardan **etkilenmeyecek** yapıda olmalı:

1. **Enerji kesintileri**
2. **Fiziksel zararlar**
3. **Aşırı kullanım yoğunluğu**

Ayrıca afetin ölçeğine göre üç kademeli sorun tanımı yapıyor (Bölüm 3):

| Afet ölçeği | Kılavuzun tespit ettiği sorun |
|---|---|
| Küçük | Telefon santralleri ve mobil ağlar sıkışır |
| Orta | İletişim hatlarında hasar; afet bölgesiyle bağlantı kurulamaz |
| Büyük | Kuleler yıkılır, elektrik kesilir, ulaşım ağı zarar görür → **iletişim tamamen kopar** |

---

## 3. Kılavuzun AfetMesh'i Doğrudan Destekleyen Üç Bulgusu

### 3.1 ✅ Bakanlık, LoRaWAN'ı resmen alternatif teknoloji olarak sayıyor

Kılavuz Bölüm 3, "Alternatif teknolojilerin değerlendirilmesi" başlığı altında şunu yazıyor:

> *"**LoraWAN altyapısının kullanılması, yatırım maliyetini azaltmanın yanı sıra kablosuz iletişim sayesinde çevresel etkenlerden daha az etkilenebilir.** Ayrıca, esnek ve kolay kurulum, sensörlerin yer değiştirmesine ve ilave sensörlerin eklenmesine olanak tanır."*

Ve devamında:

> *"M2M iletişim kesintiye uğrarsa, **LoraWAN ve RF altyapısı kullanarak 5 km kapsama alanı ve atlamalı haberleşme protokolü** ile sensör verileri afet koordinasyon merkezine iletilir."*

**Bu ifadenin projeye değeri büyüktür.** "Atlamalı haberleşme protokolü" = mesh/hop tabanlı iletişim. Yani projenin teknoloji seçimi, bir bakanlık belgesinde **zaten meşrulaştırılmış** durumda. Bir kuruma teklif götürüldüğünde teknoloji tercihini savunmak gerekmiyor — kurumun kendi kılavuzu onu sayıyor.

> ⚠️ **Dürüstlük notu:** Kılavuz LoRaWAN'ı **sensör veri iletimi** bağlamında anıyor, kişiler arası mesajlaşma bağlamında değil. Yine de teknoloji ailesinin kabul görmüş olması, savunma zeminini önemli ölçüde kolaylaştırır.

### 3.2 ✅ Kılavuz "Vatandaşlar"ı paydaş sayıyor ama onlara hiçbir bileşen sunmuyor

Bölüm 2.4'te proje paydaşları listeleniyor: İçişleri Bakanlığı, Çevre Şehircilik Bakanlığı, Sağlık Bakanlığı, Millî Savunma Bakanlığı, Ulaştırma Bakanlığı, Enerji Bakanlığı, AFAD, GSM Operatörleri, Yerel Yönetimler, Valilikler, Üniversiteler, Sivil Toplum Kuruluşları, **Vatandaşlar**, BTK.

Ancak kılavuzun **teknik bileşen listesinin tamamı** kurum içi:

> Telsiz röleleri · Araç telsizleri · Endüstriyel tabletler · Merkezi sunucu · Coğrafi bilgi sistemleri · Telsiz sunucu servisi · Ekip yönetim yazılımı · Cihaz yazılımları

**Vatandaşın sisteme dokunabileceği tek bir bileşen yok.** Kılavuz vatandaşı paydaş sayıyor, ama vatandaşa yalnızca *bilgilendirilecek özne* rolü veriyor — sisteme *veri gönderebilen özne* rolü değil.

> Bu, `Farklilasma_ve_Urun_Mimarisi_Raporu.md`'de tanımlanan **"Son 300 Metre Problemi"nin resmî bir bakanlık belgesindeki doğrudan kanıtıdır.** Artık bu boşluk bizim tespitimiz değil; bakanlığın kendi belgesinden okunabilen bir boşluk.

### 3.3 ✅ Japonya'nın "171" modeli — AfetMesh PORTAL'ın tam karşılığı

Kılavuz Bölüm 2.3.2'de Japonya'nın **171 Afet Acil Haberleşme Sistemi**'ni uzun uzun örnek gösteriyor:

> *"Afet bölgelerindeki bireylerin **güvenlik durumlarını ve diğer bilgileri** sesli olarak kaydedip iletebilmelerine imkân tanımaktadır... kullanıcılar 1 tuşuna basarak bulundukları konumla ilgili bir mesaj bırakabilir ve 2 tuşuna basarak gelen mesajları dinleyebilirler."*

Ve kritik cümle:

> *"Japonya'da başarılı bir şekilde uygulanan '171 Afet Acil Haberleşme Sistemi', **Türkiye'de GSM operatörleri ve AFAD'ın iş birliği ile uygulama için çalışmalar başlatılmıştır**."*

**AfetMesh PORTAL, işlevsel olarak 171'in ta kendisidir** — "Güvendeyim / Yardım istiyorum / Yaralı var" + konum + mesaj bırakma + gelen mesajları görme.

Aradaki tek ve belirleyici fark:

| | Japonya 171 / Türkiye'deki muadili | AfetMesh PORTAL |
|---|---|---|
| Taşıyıcı | GSM / sabit hat / fiber | **LoRa mesh** |
| GSM çöktüğünde | **Çalışmaz** | **Çalışır** |
| Altyapı gereksinimi | Operatör şebekesi ayakta olmalı | Yok |

Kılavuzun kendi tespiti — "büyük çaplı afetlerde kuleler yıkılır, iletişim tamamen kopar" — 171 modelinin tam olarak en gerekli olduğu anda çalışmayacağını söylüyor.

> **Konumlandırma cümlesi:** *"AfetMesh, 171 modelinin GSM olmadan çalışan halidir."*
> Bu tek cümle, kurumsal muhataba projeyi anlatmanın en kısa yoludur; çünkü referans çerçevesi zaten onların belgesinde.

Kılavuz ayrıca **AKUT Güvendeyim** uygulamasını (1 milyondan fazla kullanıcı) örnek veriyor — ancak o uygulama da konum bilgisini **SMS ile** ilettiği için GSM'e bağımlıdır. Aynı boşluk.

### 3.4 ✅ Kılavuzun dünyadaki örnekleri bizim yaklaşımımızı işaret ediyor

Bölüm 2.3.2'de sayılan uygulamalar — **FireChat** (Bluetooth+WiFi mesh), **Bridgefy** (Bluetooth, 100 m), **MeshTalk** (OPPO, şebekesiz 3 km) — hepsi cihazdan cihaza, altyapısız, vatandaş katmanı çözümleridir.

Kılavuz bunları **tanıyor ve listeliyor**, ancak kendi önerdiği mimariye **dahil etmiyor.** Yani bakanlık bu kategorinin varlığından haberdar; sadece kendi çözüm setine koymamış. AfetMesh tam bu kategoriye oturuyor — üstelik sayılanların hepsinden daha uzun menzille (Bluetooth 100 m yerine LoRa km'ler).

---

## 4. Kılavuzun AfetMesh Aleyhine Olan Bulguları (Dürüst Değerlendirme)

### 4.1 ❌ Veri kapasitesi

Kılavuzun teknoloji seçim kriterlerinden biri **"Yüksek veri trafiği kapasitesi"**. Kılavuzun mimarisi video, kamera görüntüsü, harita katmanı, doküman paylaşımı taşıyor. AfetMesh **kısa metin ve konumdan başka hiçbir şey taşıyamaz** (paket başına ~237 byte, pratikte ≤59 byte SOS paketi).

**Bu bir eksiklik değil, bir tasarım tercihidir** — ama kurumsal muhataba bunu açıkça söylemek gerekir. AfetMesh telsiz sisteminin **yerine geçmez.**

### 4.2 ❌ Ses yok

Kılavuzun tüm mimarisi telsiz **konuşması** üzerine kurulu. Arama-kurtarma ekipleri sesli haberleşir. AfetMesh sesli iletişim sunmaz ve sunamaz.

### 4.3 ❌ Kapsama alanı

Kılavuz ASELSAN/Kenwood/Motorola telsizlerin geniş kapsamasını vurguluyor; ASELSAN mobil aracı 15–30 km çapında kapsama sağlıyor. AfetMesh şehir içinde node başına 1–3 km, bina arasında yüz metreler mertebesinde. Kapsama **node yoğunluğuyla** telafi edilir; bu da dağıtım gerektirir.

### 4.4 ❌ Kurumsal entegrasyon olgunluğu

Kılavuz AYDES, GAMER, 112, AHC, TAKBİS, MAKS entegrasyonlarını şart koşuyor. AfetMesh'in bu entegrasyonların hiçbiri yok. (Bölüm 6'da bu bir gereksinime dönüştürülmüştür.)

---

## 5. Maliyet Karşılaştırması — Projenin En Güçlü Argümanı

Aynı örnek vaka (1000 Ha, 200.000 kişi) için:

| | Kılavuzun modeli | AfetMesh (halk katmanı) |
|---|---:|---:|
| Röle/kapsama donanımı | $614.562 (59 röle alıcısı) | **$3.600** (30 NOKTA × $120) |
| Uç cihazlar | $126.829 (118 telsiz + 59 tablet) | **$50.000** (2.000 CEP × $25) |
| Yazılım | $104.617 | Açık kaynak (geliştirme maliyeti ayrı) |
| Lisans | $123.078 | **$0** |
| **Donanım toplamı** | **$741.391** | **≈ $53.600** |
| Kapsanan kişi sayısı | Saha ekipleri (~118 telsiz kullanıcısı) | **200.000 vatandaş** |

**Çarpıcı tek rakam:** Kılavuzdaki **bir adet röle alıcısı ($10.448)** bedeline, yaklaşık **87 adet AfetMesh NOKTA** kurulabilir.

> ⚠️ **Bu karşılaştırma dürüstçe okunmalıdır.** İki sistem aynı işi yapmıyor: kılavuzun sistemi sesli, yüksek bant genişlikli, profesyonel ekip yönetimi; AfetMesh metin/SOS temelli vatandaş katmanı. Doğru argüman "AfetMesh telsiz sisteminin yerine geçsin" değil, şudur:
>
> **"Mevcut projenin bütçesine %5–7 ekleyerek, bugün hiç kapsanmayan 200.000 vatandaşı sisteme dahil edebilirsiniz."**
>
> Bu, bir belediye bütçesinde savunması kolay bir tekliftir ve kılavuzun kendi paydaş listesindeki "Vatandaşlar" satırını gerçekten doldurur.

---

## 6. Projeye Eklenen Yeni Gereksinimler

Kılavuzun okunmasından doğan, önceki raporlarda bulunmayan gereksinimler:

### 6.1 Kurumsal veri ve entegrasyon

| # | Gereksinim | Kaynak |
|---|---|---|
| **AF-N10** | NOKTA konumları, resmî **toplanma alanları**, **kamu binaları** ve **MAKS adres verisi** kullanılarak planlanacak; rastgele/gönüllü yerleşim yapılmayacak | Kılavuz Bölüm 1.3 "Proje Girdileri" |
| **AF-D6** | PANO çıktısı **AYDES / GAMER / 112 / AHC** entegrasyonuna uygun formatta üretilecek | Kılavuz Bölüm 2.4 beklentiler |
| **AF-D7** | Veriler **açık veri politikasına** uygun, paydaşlar ve akademisyenlerle paylaşılabilir formatta olacak | Kılavuz Bölüm 1.4 |
| **AF-X1** | Sistemin **CBS tabanlı** olduğu gösterilecek; NOKTA'lar, kapsama alanları ve SOS kayıtları coğrafi katman olarak üretilecek | Kılavuzun tüm çerçevesi CBS Genel Müdürlüğü'nündür |

### 6.2 Kılavuzun performans göstergelerinin benimsenmesi

Kılavuz Bölüm 1.5, yedi resmî performans göstergesi tanımlıyor. Bunlar doğrudan test planımıza alınmalıdır — böylece ölçümlerimiz kurumun kendi diliyle raporlanabilir:

| Kılavuzun göstergesi | AfetMesh'teki karşılığı | Hedef |
|---|---|---|
| İletişim süresi | SOS paketi uçtan uca gecikme | < 60 sn |
| Kapsama alanı | NOKTA başına yarıçap × node sayısı | Ölçülecek (T-4) |
| Erişilebilirlik | Portal'a bağlanabilen eşzamanlı kullanıcı | ≥ 8 istemci |
| Haberleşme güvenilirliği | Paket teslim oranı | ≥ %90 |
| **Acil durum yanıt süresi** | SOS → PANO'da görünme süresi | < 2 dk |
| Veri aktarım hızı | Etkin veri hızı | ~1–5 kbps (düşük — açıkça beyan edilecek) |
| Yedekleme ve kurtarma | Store & Forward kayıt sayısı ve süresi | Sınırsız süre, ≥ 10.000 kayıt |

### 6.3 Kılavuzun teknoloji seçim kriterlerine göre öz değerlendirme

Kılavuz Bölüm 3, teknoloji seçimi için 11 kriter tanımlıyor. AfetMesh'in dürüst karnesi:

| # | Kriter | AfetMesh | Not |
|---|---|:---:|---|
| 1 | Teknoloji yeni mi | ✅ | LoRa + mesh, güncel |
| 2 | Teknoloji yerli mi | ⚠️ | Firmware açık kaynak; donanım modülü ithal |
| 3 | Yerlileştirilebilir mi | ✅ | Açık kaynak + yerli PCB tasarımı (Donanım Raporu) |
| 4 | Maliyet | ✅✅ | Kılavuz modelinin ~%7'si |
| 5 | Kesintisiz servis | ✅ | Self-healing mesh + solar |
| 6 | Kurulum kolaylığı | ✅ | NFR-6: < 15 dk |
| 7 | Entegrasyon kolaylığı | ⚠️ | AF-D6 ile ele alındı, henüz yok |
| 8 | Dayanıklılık | ✅ | IP54/IP65, dağıtık — tek nokta arızası yok |
| 9 | Kapsama alanı | ❌ | Zayıf yön; node yoğunluğuyla telafi edilir |
| 10 | Haberleşme protokolü | ✅ | Açık ve belgelenmiş (Meshtastic) |
| 11 | Yüksek veri trafiği kapasitesi | ❌ | Zayıf yön; yalnızca kısa metin |

**9 kriterden 7'si olumlu, 2'si zayıf, 2'si kısmi.** Zayıf olan iki kriter (kapsama, veri kapasitesi) tam olarak telsiz sistemlerinin güçlü olduğu kriterlerdir — bu da iki sistemin **rakip değil tamamlayıcı** olduğunu gösterir. Teklifte bu açıkça söylenmelidir.

### 6.4 Kılavuzun kendi "zayıf yönler" listesine cevabımız

Kılavuz Bölüm 2.4'te kendi önerdiği sistemin zayıf yönlerini sıralıyor. AfetMesh bunların üçüne doğrudan cevap veriyor:

| Kılavuzun kendi tespit ettiği zayıflık | AfetMesh'in cevabı |
|---|---|
| *"İletişim altyapısının dayanıksız binalara yerleştirilmesi, sistemlerin zarar görmesine neden olabilir"* | Dağıtık mimari: tek bir NOKTA'nın yıkılması ağı durdurmaz (self-healing) |
| *"Saha bakımı için yüksek maliyetler gerekebilir"* | NOKTA birim maliyeti ~$120; arızalı birim tamir edilmez, değiştirilir |
| *"Afet sonrası çöken iletişim sistemleri, acil durumda iletişim kopukluğuna neden olabilir"* | Merkezî bileşen yok; çökecek bir merkez yok |

Bu üç satır, bir teklif sunumunda doğrudan kullanılabilir — çünkü zayıflığı biz değil, kurumun kendi belgesi tespit etmiştir.

---

## 7. Kurumsal Teklif Stratejisi

Kılavuzun okunması, projeye somut bir kurumsal yol açıyor:

### 7.1 Muhatap kim?

Kılavuz **belediyelere** yazılmış (örnek vaka bir belediye fizibilitesinden türetilmiş: Esenler Belediyesi / TÜBİTAK-TÜSSİDE). Yani ilk kapı AFAD değil, **ilçe/büyükşehir belediyesinin bilgi işlem veya afet koordinasyon birimidir.**

### 7.2 Teklifin çerçevesi

Yeni bir proje önermek yerine, kılavuzun tanımladığı projeye **bir bileşen eklemek** olarak konumlandırılmalı:

> *"Bakanlık kılavuzunun tanımladığı Afet Sonrası İletişim Sistemi'nin paydaş listesinde 'Vatandaşlar' yer alıyor, ancak teknik bileşen listesinde vatandaşa dokunan hiçbir unsur yok. AfetMesh bu bileşendir: toplam bütçenin %5–7'si ile, kılavuzda örneklenen 200.000 kişilik nüfusa afet anında yardım çağrısı gönderme yeteneği kazandırır."*

### 7.3 Teklifte kullanılacak dört dayanak

1. **Kılavuzun kendi LoRaWAN/atlamalı protokol ifadesi** — teknoloji tercihi zaten meşru (Bölüm 3.1)
2. **171 modeli** — kurumun tanıdığı, benimsemek istediği model; biz onu GSM'siz çalıştırıyoruz (Bölüm 3.3)
3. **Kılavuzun kendi zayıflık listesi** — dağıtık mimarinin cevap verdiği üç madde (Bölüm 6.4)
4. **Maliyet oranı** — bir röle alıcısı = 87 NOKTA (Bölüm 5)

### 7.4 Teklifte kesinlikle yapılmaması gereken

- **"Telsiz sistemlerinin yerine geçer" demek.** Geçmez; ses ve veri kapasitesi yok. Bu iddia teklifi ilk teknik incelemede bitirir.
- **Kapsama alanı rakamlarını abartmak.** Ulaş'ın "10–15 km" iddiasına benzer bir söylem güvenilirliği yok eder. Ölçülmüş rakam verilmelidir.

---

## 8. Önceki Raporlarda Yapılan/Yapılacak Düzeltmeler

| Belge | Madde | Durum |
|---|---|---|
| `Ihtiyac_ve_Rekabet_Analizi_Raporu.md` | "Resmî ihtiyaç beyanı bulunamadı" iddiası | ✅ **Düzeltildi** — bakanlık kılavuzu bu iddiayı çürütüyor |
| `Ihtiyac_ve_Rekabet_Analizi_Raporu.md` | Bölüm 5.4 "PDF incelenmeli" aksiyonu | ✅ **Tamamlandı** — bu rapor o incelemedir |
| `Farklilasma_ve_Urun_Mimarisi_Raporu.md` | Bölüm 4.3, 4.6 — NOKTA ve PANO gereksinimleri | ⏳ AF-N10, AF-D6, AF-D7, AF-X1 eklenmeli |
| `Farklilasma_ve_Urun_Mimarisi_Raporu.md` | Bölüm 7 — Test planı | ⏳ Kılavuzun 7 performans göstergesi entegre edilmeli |
| `01_Gereksinim_Analizi/Gereksinim_Analizi_Raporu.md` | Bölüm 12, madde 5 "Pilot bölge" | ⏳ Muhatap netleşti: **ilçe belediyesi bilgi işlem / afet birimi** |

---

## 9. Sonuç

Bakanlık kılavuzu, projeye üç şey kazandırdı:

1. **Meşruiyet.** Teknoloji tercihi (LoRa, atlamalı protokol) ve hedef işlev (171 tipi güvenlik durumu bildirimi) bakanlığın kendi belgesinde geçiyor. Artık savunulması gereken bir tez değil, tamamlanması gereken bir boşluk.

2. **Muhatap ve dil.** Teklifin gideceği yer (belediye), kullanılacak ölçütler (11 teknoloji kriteri, 7 performans göstergesi) ve karşılaştırma zemini (kişi başı $5,27) netleşti.

3. **Boşluğun resmî kanıtı.** Kılavuz "Vatandaşlar"ı paydaş sayıp onlara hiçbir bileşen sunmuyor. `Farklilasma_ve_Urun_Mimarisi_Raporu.md`'de teorik olarak tanımladığımız "Son 300 Metre Problemi", artık bir bakanlık belgesinden okunabilen somut bir eksikliktir.

**Projenin tek cümlelik kurumsal konumlandırması:**

> **AfetMesh, Bakanlık kılavuzunun paydaş listesinde yazan ama teknik bileşen listesinde bulunmayan "Vatandaşlar" satırının karşılığıdır — ve 171 modelinin GSM olmadan çalışan halidir.**

---

## Kaynak

- T.C. Çevre, Şehircilik ve İklim Değişikliği Bakanlığı, Coğrafi Bilgi Sistemleri Genel Müdürlüğü (2024). *Afet Sonrası İletişim Sistemleri Uygulaması — Akıllı Şehir Rehberlik Uygulamaları Projesi.* — proje kök dizinindeki `Afet_Sonrası_İletişim_Sistemleri.pdf` (41 sayfa)
- Kılavuzun dayandığı çalışma: TÜBİTAK-TÜSSİDE (Nisan 2021). *Esenler Belediyesi Akıllı Şehir Uygulamaları Fizibilite Projesi — Afet Sonrası Ön Fizibilite Raporu.*
- [Akıllı Şehir Bilgi Paylaşım Portalı](https://www.akillisehirler.gov.tr)

> **Metodoloji notu:** Bu rapordaki tüm alıntı ve rakamlar, adı geçen PDF'in tam metninden doğrudan alınmıştır. Kılavuzdaki maliyet tablolarında birim fiyat × adet ile verilen toplamlar arasında küçük yuvarlama farkları bulunmaktadır; bu raporda belgedeki toplam değerler esas alınmıştır. AfetMesh tarafındaki maliyet rakamları Donanım Gereksinim Raporu'na dayalı tahminlerdir ve tedarik teyidi gerektirir.
