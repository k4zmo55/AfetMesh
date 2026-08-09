# Türkiye'de İhtiyaç ve Rekabet Analizi Raporu

> 📎 **Araştırma girdisi — 9 Ağustos 2026 tarihli bulguların kaydı.** Gereksinim kaynağı değildir.
> Yürürlükteki gereksinimler: [`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](../00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md)

---

## 1. Yönetici Özeti

| Soru | Cevap |
|---|---|
| Türkiye'de böyle bir ihtiyaç var mı? | **Evet** — 6 Şubat 2023 depremleri sonrası meslek odası raporlarında belgelenmiş, ulusal ölçekte tescilli bir boşluk |
| Devlet kurumları bu projeyi talep etti mi? | **Kısmen evet** — Çevre, Şehircilik ve İklim Değişikliği Bakanlığı'nın 2024 tarihli *Afet Sonrası İletişim Sistemleri* rehberlik kılavuzu, belediyeler için resmî ihtiyaç tanımı, teknoloji önerisi ve bütçe şablonu sunuyor. İhale değil, ancak satın alma gerekçesi. Bkz. [Bakanlık Kılavuzu Uyum ve Boşluk Analizi](Bakanlik_Kilavuzu_Uyum_ve_Bosluk_Analizi.md) |
| Benzer proje geliştiriliyor mu? | **Evet, en az 3 tane** — TA Mesh topluluğu, MeshGrid (ticari girişim), Ulaş ekosistemi |
| Projenin savunulabilir yeri var mı? | **Evet** — Mevcut tüm çözümler kurumsal/profesyonel katmanda; "cihazsız vatandaş" katmanı boş |

**Ana sonuç:** Problem tanımı gerçek ve belgeli, ancak proje boş bir alana girmiyor. Farklılaşma noktası donanım tasarımı değil, **kamu node'u konumlandırma stratejisi + cihazsız kullanıcı onboarding'i + SOS/koordinasyon katmanı** olmalıdır.

---

## 2. İhtiyacın Kanıtı: 6 Şubat 2023 Sonrası Tespitler

Deprem sonrası hazırlanan bağımsız meslek odası raporları, projenin problem tanımını doğrudan doğrulamaktadır:

- **Baz istasyonu çöküşü:** Baz istasyonları hem yapısal yıkımdan hem de enerji kesintisinden servis dışı kaldı. Kule üzerindeki istasyonlar görece az hasar görürken, kent içi ve yıkılan binalardaki istasyonlar tamamen devre dışı kaldı.
- **Kesintinin süresi:** Depremin üzerinden **günler geçtikten sonra bile** birçok yerleşimde mobil iletişim ve internet bağlantısı kurulamadı.
- **Can kaybına doğrudan etkisi (kritik tespit):** İletişim kesintileri arama-kurtarma çalışmalarını engelledi; enkazlardan gelen yardım istekleri duyulamadığı için karşılanamadı ve **can kayıplarının artmasına yol açtı**.
- **İnternet altyapısı:** GSM'dekine benzer kesintiler internet altyapısında da yaşandı; yedekleme önlemlerinin alınmamış olması sorunu derinleştirdi.

> Bu tespitler BMO (Bilgisayar Mühendisleri Odası) Deprem Raporu, EMO'nun "Afetlerde Haberleşme ve Elektrik" raporu ve TMMOB değerlendirme raporlarında yer almaktadır.

**Sonuç:** Projenin çıkış noktası varsayımsal değildir. Ulusal ölçekte belgelenmiş bir boşluğu hedeflemektedir.

---

## 3. Mevcut Kamu Çözümleri ve Boşluğun Konumu

Devletin afet haberleşme çözümleri mevcuttur, ancak **tamamı kurumlar arası / profesyonel katmana** odaklıdır:

| Sistem | Sorumlu Kurum | Kapsam ve Sınır |
|---|---|---|
| **KGHS** (Kesintisiz ve Güvenli Haberleşme Sistemi) | AFAD | 81 il AFAD merkezi ve paydaş kurum merkezleri arası. Fiber optik + GSM + uydu + HF telsiz, otomatik aktarım. IP/MPLS altyapısı 2016'da 81 ile kuruldu. **Vatandaş erişimi yok** |
| **TAMP Haberleşme Hizmet Grubu** | Ulaştırma ve Altyapı Bakanlığı / Haberleşme Genel Müdürlüğü | TAMP'ın 28 çalışma grubundan biri. Ulusal düzeyde kurumsal haberleşme koordinasyonu. Plan 2015'te onaylandı. **Kurum düzeyinde** |
| **Mobil Muhabere Aracı** | ASELSAN (Emniyet için) | Araç çevresinde 15–30 km çapında telsiz kapsaması, jeneratörle 24 saat bağımsız çalışma, %70 yerlilik oranı, uydu üzerinden PolNet ve kriptolu DMR entegrasyonu. **Kolluk/kamu kullanımı** |
| **ULAK Haberleşme çözümleri** | ULAK | Baz istasyonu / altyapı seviyesi. **Operatör katmanı** |
| **Amatör telsiz (TA)** | Gönüllü telsizciler + AFAD koordinasyonu | Altyapı çöktüğünde alternatif kanal sağlar; frekans/kanal planı önceden yapılmalı. **Lisans zorunlu — halk kullanamaz** |

### Boşluğun tanımı

Yukarıdaki sistemlerin hepsi lisanslı, profesyonel veya kurumsaldır. Cevaplanmayan soru şudur:

> **"Enkaz altındaki veya mahalledeki sıradan vatandaş, elindeki telefonla kime, nasıl ulaşacak?"**

Bu sorunun resmî bir cevabı yoktur. Gereksinim Analizi Raporu'nun Bölüm 3'ünde tanımlanan **"kamu node'u + BLE üzerinden cihazsız kullanıcı katılımı"** modeli tam olarak bu boşluğa oturmaktadır. Projenin en güçlü savunma argümanı budur.

### ⚠️ Bu bölüm düzeltilmiştir (9 Ağustos 2026)

Bu raporun ilk sürümü *"hiçbir kamu kurumu tarafından yayımlanmış resmî bir ihtiyaç beyanı bulunamamıştır"* diyordu. **Bu tespit eksikti.**

T.C. Çevre, Şehircilik ve İklim Değişikliği Bakanlığı, CBS Genel Müdürlüğü tarafından yayımlanan **2024 tarihli *Afet Sonrası İletişim Sistemleri Uygulaması* rehberlik kılavuzu**, tam olarak bu alanda:

- Resmî bir **ihtiyaç analizi** yapıyor (kılavuz Bölüm 2.4)
- **LoRaWAN ve "atlamalı haberleşme protokolü"nü** alternatif teknoloji olarak açıkça sayıyor
- Belediyeler için örnek **proje büyüklüğü ve bütçesi** veriyor (1000 Ha / 200.000 kişi / 12 ay / **$1.053.464**)
- Paydaş listesinde **"Vatandaşlar"** yer alıyor — ancak teknik bileşen listesinde vatandaşa dokunan **hiçbir unsur yok**

Güncellenmiş talep durumu:

- **Örtük talep:** VAR (meslek odası raporlarında tespit edilmiş, çözülmemiş problem)
- **Resmî ihtiyaç tanımı:** VAR (bakanlık kılavuzu — belediyelere yönelik tavsiye ve fizibilite şablonu)
- **Yayımlanmış ihale/şartname:** Hâlâ bulunamadı — kılavuz bir satın alma zorunluluğu değil, gerekçesidir

> Kılavuzun ayrıntılı incelemesi ve projeye etkileri için: **[Bakanlık Kılavuzu Uyum ve Boşluk Analizi](Bakanlik_Kilavuzu_Uyum_ve_Bosluk_Analizi.md)**

---

## 4. Benzer Projeler ve Rekabet Ortamı

Bu bölüm projenin konumlandırması açısından en kritik bulgudur. Aynı hedefe yönelen en az üç aktif oluşum mevcuttur.

### 4.1 TA Mesh — Meshtastic Türkiye Topluluğu

**En yakın örtüşme.** Bu projeyle hedefi birebir aynıdır.

- Purgatoria çatısı altında, kâr amacı gütmeyen gönüllü topluluk
- Hedef: Türkiye genelinde LoRa tabanlı, internetten bağımsız, afete dayanıklı iletişim ağı kurmak
- Faaliyetler: kurulum/kullanım kılavuzları, MQTT sunucu altyapısı, düzenli buluşmalar, teknik destek, açık kaynak katkı
- Kanallar: Discord, WhatsApp, Facebook, GitHub organizasyonu
- **TMDC (TA Mesh Topluluk Destekli Cihaz Programı):** Türkiye'de yerli üretim Meshtastic uyumlu LoRa geliştirme kartı tasarımını teşvik ediyor

**Projeye etkisi:** Gereksinim Analizi Raporu'ndaki "donanım referans tasarımı / BOM" çıktısı bu program ile örtüşmektedir. Bu alanda sıfırdan çalışmak tekrar üretim riski taşır.

### 4.2 MeshGrid — Ticari Girişim

- Nisan 2026'da Toygar Dündaralp tarafından kuruldu
- Teknoloji: Bluetooth + LoRa + GPS; harita üzerinde ekip takibi ve mesajlaşma
- Piyasadaki hazır kütüphaneler yerine **MeshLink** adlı özgün şifreli iletişim protokolü geliştirilmiş
- Hedef kitle: arama-kurtarma ekipleri, saha personeli, gönüllü afet ağları, güvenlik ve etkinlik koordinasyon birimleri (hem kamu hem özel sektör)
- Durum: Kendi sermayesiyle ilerliyor, Tech İstanbul Nova hızlandırma programına kabul edildi, yatırımcı görüşmeleri sürüyor
- Gelir modeli: App Store üzerinden 12,99 USD ömür boyu lisans
- Rakip olarak açıkça **Meshtastic** ve **iTAK**'ı işaret ediyor; farklılaşma iddiası "hızlı kurulum altyapısı ve bağlantı modeli"

**Projeye etkisi:** Bu alanda ticari talep olduğunu ve yatırım ekosisteminin ilgi gösterdiğini kanıtlıyor — yani pazar tamamen ölü değil.

### 4.3 Ulaş Ekosistemi

- LoRa tabanlı mesh ağ + yapay zeka destekli koordinasyon
- Afetzede – arama-kurtarma ekibi – yardım kuruluşu üçgeninde haberleşme
- Ek sensörlerle yangın, doğalgaz ve elektrik arızası riski tespiti iddiası
- Cihaz başına **10–15 km menzil** iddiası

> **Değerlendirme:** 10–15 km menzil iddiası açık alan/ideal koşul rakamıdır ve pazarlama amaçlıdır. Gereksinim Analizi Raporu Bölüm 10'daki *"tipik 1–3 km açık alan, bina arası yüz metreler"* tahmini şehir içi gerçekliğe çok daha yakındır. Bu iddiaya karşı kendi rakamlarımızı savunmakta bir sakınca yoktur.

### 4.4 Rekabet Karşılaştırma Tablosu

| Oluşum | Tip | Hedef Kitle | Cihazsız Vatandaş Katmanı | Kamu Node Stratejisi |
|---|---|---|---|---|
| TA Mesh | Gönüllü topluluk | Meraklılar, teknik kullanıcılar | Kısmen (topluluk ağı) | Planlı değil |
| MeshGrid | Ticari girişim | Profesyonel saha ekipleri | Hayır (lisanslı uygulama) | Hayır |
| Ulaş | Proje/girişim | Kurumlar, AKUT tipi ekipler | Belirsiz | Belirsiz |
| **Bu proje** | Açık kaynak / DIY | **Sıradan vatandaş** | **Evet (çekirdek tasarım)** | **Evet (Bölüm 6.5)** |

---

## 5. Projeye Etkisi ve Somut Öneriler

### 5.1 TA Mesh ile rekabet değil, işbirliği

Aynı hedefe yönelen bir topluluk varken paralel ağ kurmak node yoğunluğunu böler — ki Gereksinim Analizi Bölüm 10'da **"yetersiz node yoğunluğu"** en yüksek etkili risk olarak listelenmiştir. Mevcut ağa bağlanmak bu riski doğrudan azaltır.

**Farklılaşma alanı:** TA Mesh'te görünmeyen, bu projede tanımlı olan üç şey:
1. Kamu node'u konumlandırma stratejisi (toplanma alanları, yüksek noktalar — Bölüm 6.5)
2. Cihazsız kullanıcı onboarding'i ve teknik olmayan kullanıcı deneyimi (SW-11)
3. SOS/triage önceliklendirme ve koordinasyon panosu katmanı (SW-14, SW-15, SW-16)

Enerji bu üç başlığa yoğunlaştırılmalı; donanım BOM'u için mevcut topluluk çalışması referans alınmalıdır.

### 5.2 Frekans varsayımı gözden geçirilmeli — AKSİYON GEREKTİRİR

Gereksinim Analizi Raporu **NFR-9** ve **SW-2** maddelerinde bölge ayarını **EU_868** olarak sabitlemektedir. Ancak saha gerçeği farklıdır:

| Band | Türkiye'de ERP güç limiti | Topluluk kullanımı |
|---|---|---|
| 433 MHz | ~10 mW (10 dBm) ERP | Türkiye'de **daha yaygın** |
| 868 MHz | ~500 mW (~27 dBm) ERP, duty-cycle sınırlı | Kullanılıyor, ancak daha az yaygın |

**Sorun:** Teknik olarak 868 MHz daha yüksek güç bütçesi sunar, ancak mevcut TA Mesh ağı ağırlıklı olarak 433 MHz üzerindeyse, EU_868 seçimi projeyi **ağda yalnız bırakır**. Node yoğunluğu bu projede menzilden daha kritik bir başarı faktörüdür.

**Aksiyon:** Frekans kararı verilmeden önce TA Mesh topluluğuna fiilî standardın hangisi olduğu sorulmalı; ayrıca BTK'nın güncel SRD düzenlemesi (Milli Frekans Planı, Sınıf 1 Cihaz Kategorileri) resmî kaynaktan teyit edilmelidir. Bu, Bölüm 12'deki karar bekleyen konular listesine **yeni bir madde** olarak eklenmelidir.

### 5.3 Pilot bölge için kurumsal kapı çalınmalı

Gereksinim Analizi Bölüm 12, madde 5'te "pilot bölge/senaryo belirlenmeli" denmektedir. Öneri:

- Hedef: bir **ilçe belediyesi** veya **AFAD il müdürlüğü** ile düşük maliyetli pilot
- Argüman: teklif iki resmî dayanağa oturtulmalıdır — (1) BMO/EMO/TMMOB raporlarındaki *"iletişim kesintisi can kaybını artırdı"* tespiti, (2) **Bakanlık kılavuzunun kendi ihtiyaç analizi ve bütçe şablonu**. İkincisi, muhatabın kendi kurumsal çerçevesi olduğu için daha etkilidir
- Avantaj: cihaz başına maliyet (NFR-1: ~$25–40) kurumsal alternatiflerin (ASELSAN mobil muhabere aracı vb.) yanında ihmal edilebilir düzeydedir — bu, satış hikâyesinin en güçlü kısmıdır

### 5.4 Bakanlık kılavuzu — ✅ incelendi

Çevre, Şehircilik ve İklim Değişikliği Bakanlığı **Akıllı Şehirler Portalı**'nda yayımlanan *"Afet Sonrası İletişim Sistemleri"* rehberlik kılavuzu incelenmiştir (41 sayfa, 2024).

Aranan resmî ihtiyaç ifadesi **bu belgededir.** Kılavuz ayrıca LoRaWAN'ı alternatif teknoloji olarak sayıyor, Japonya'nın 171 modelini örnek gösteriyor ve paydaş listesinde "Vatandaşlar"a yer verip onlara hiçbir teknik bileşen sunmuyor — yani projenin hedeflediği boşluğu resmî bir belgeyle kanıtlıyor.

> Tam analiz: **[Bakanlık Kılavuzu Uyum ve Boşluk Analizi](Bakanlik_Kilavuzu_Uyum_ve_Bosluk_Analizi.md)**

---

## 6. Risk Güncellemesi

Gereksinim Analizi Bölüm 10'daki risk tablosuna eklenmesi önerilen maddeler:

| Risk | Etki | Azaltma Stratejisi |
|---|---|---|
| Mevcut TA Mesh ağıyla frekans uyumsuzluğu → ağda izole kalma | **Yüksek** | Frekans kararı öncesi topluluğa danışma; gerekirse çift band desteği |
| Paralel topluluk çalışması → çabanın tekrarı, node yoğunluğunun bölünmesi | Orta-Yüksek | TA Mesh ile erken temas, işbirliği/katkı modeli |
| Ticari rakiplerin (MeshGrid vb.) kurumsal pazarı önce kapması | Orta | Vatandaş katmanına odaklanma; açık kaynak/DIY konumlandırmasını koruma |
| Resmî talep olmaması → kurumsal pilot bulmakta zorluk | Orta | Meslek odası raporlarına dayalı problem-odaklı teklif; düşük maliyet vurgusu |

---

## 7. Sonuç

Proje, Türkiye'de **gerçek ve belgelenmiş bir boşluğa** yöneliyor: kamu çözümlerinin tamamı kurumsal katmanda kalıyor, vatandaş katmanı boş. Bu, projenin varlık gerekçesidir ve savunulabilirdir.

Buna karşılık proje **boş bir alana girmiyor**. Aynı hedefe yönelen bir gönüllü topluluk (TA Mesh), bir ticari girişim (MeshGrid) ve bir proje ekosistemi (Ulaş) hâlihazırda çalışıyor. Bu, ihtiyacın gerçekliğini doğrulaması açısından olumlu; ancak konumlandırmanın netleştirilmesini zorunlu kılıyor.

**Tavsiye:** Projeye devam edilmeli, ancak odak *"bir Meshtastic node'u nasıl yapılır"* sorusundan *"afet anında cihazı olmayan bir vatandaş bu ağa nasıl dahil olur ve SOS'u nasıl bir koordinasyon merkezine ulaşır"* sorusuna kaydırılmalıdır. Rekabet avantajı burada, donanımda değil.

---

## Kaynaklar

**İhtiyaç tespiti (meslek odası raporları)**
- [BMO 6 Şubat 2023 Kahramanmaraş Depremleri Raporu](https://www.bmo.org.tr/wp-content/uploads/2023/10/BMO-Deprem-Raporu.pdf)
- [EMO — Afetlerde Haberleşme ve Elektrik](https://www.emo.org.tr/ekler/266a71a5d6013b1_ek.pdf)
- [TMMOB — 6 Şubat 2023 Depremi Değerlendirmesi](http://www.tmmob.org.tr/icerik/6-subat-2023-depremi-degerlendirmesi-kamuoyu-ile-paylasildi)
- [İMO — 6 Şubat 2023 Depremleri Gözlem ve Değerlendirme Raporu](https://www.imo.org.tr/Eklenti/8624,deprem-rapor-2-webpdf.pdf?0=)

**Kamu sistemleri ve planlar**
- [AFAD — Kesintisiz ve Güvenli Haberleşme Sistemi (KGHS)](https://www.afad.gov.tr/kesintisiz-ve-guvenli-haberlesme-sistemi)
- [AFAD 2024–2028 Stratejik Planı](https://www.afad.gov.tr/kurumlar/afad.gov.tr/e_Kutuphane/Planlar/2024_2028-AFAD-Stratejik-Plani.pdf)
- [Türkiye Afet Müdahale Planı (TAMP)](https://www.afad.gov.tr/kurumlar/afad.gov.tr/2419/files/Afet_Mud_Pl_ResmiG_20122013.pdf)
- [HGM — Afet ve Acil Durum Haberleşmesi (Haberleşme Hizmet Grubu)](https://hgm.uab.gov.tr/afet-ve-acil-durum-haberlesmesi)
- [Akıllı Şehirler Portalı — Afet Sonrası İletişim Sistemleri (fizibilite raporu)](https://akillisehirler.csb.gov.tr/wp-content/uploads/fizibilite-rapor/3-Afet%20Sonras%C4%B1%20%C4%B0leti%C5%9Fim%20Sistemleri.pdf)
- [TRT Haber — ASELSAN'dan afet ve acil durumlarda kesintisiz telsiz iletişimi](https://www.trthaber.com/haber/bilim-teknoloji/aselsandan-afet-ve-acil-durumlarda-kesintisiz-telsiz-iletisimi-603580.html)
- [CNN Türk — ASELSAN Mobil Muhabere Aracı](https://www.cnnturk.com/video/turkiye/deprem-senaryolari-icin-tasarlandi-aselsanin-mobil-muhabere-araci-iletisim-cokerse-devreye-giriyor-2371626)

**Benzer projeler ve rekabet**
- [Meshtastic Türkiye Topluluğu (TA Mesh)](https://tamesh.org/)
- [TA Mesh — Sıkça Sorulan Sorular](https://tamesh.org/sss)
- [TA Mesh — GitHub organizasyonu](https://github.com/Meshtastic-Turkiye-Toplulugu)
- [TA Mesh Topluluk Destekli Cihaz Programı (TMDC)](http://blog.purgatoria.tr/2026/05/ta-mesh-topluluk-destekli-cihaz-programi.html)
- [Webrazzi — MeshGrid](https://webrazzi.com/2026/06/02/altyapilarin-tamamen-coktugu-zorlu-saha-kosullari-icin-gelistirilen-iletisim-cozumu-meshgrid/)

**Regülasyon**
- [BTK — Milli Frekans Planı](https://www.btk.gov.tr/uploads/pages/milli-frekans-plani.pdf)
- [BTK — Sınıf 1 Cihaz Kategorileri ve Frekans Bandları](https://www.btk.gov.tr/uploads/pages/sinif-1-guncelleme-son-2019-03-25.pdf)
- [BTK — Amatör Telsizcilik](https://www.btk.gov.tr/amator-telsizcilik)

---

> **Metodoloji notu:** Bu rapor 9 Ağustos 2026 tarihinde yapılan açık kaynak web araştırmasına dayanmaktadır. Kamu kurumlarının yayımlanmamış iç ihtiyaç belgeleri, EKAP üzerindeki kapalı ihale kayıtları ve akademik veri tabanları taranmamıştır. "Resmî talep bulunamadı" ifadesi, "resmî talep yoktur" anlamına gelmez — yalnızca kamuya açık kaynaklarda tespit edilemediğini belirtir. Bu rapor bir hukuki veya yatırım görüşü değildir.
