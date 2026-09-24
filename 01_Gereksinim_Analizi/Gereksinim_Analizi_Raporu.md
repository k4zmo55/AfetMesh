# Afet Anı Meshtastic Tabanlı İletişim Ağı — Gereksinim Analizi Raporu

> ## 📎 BELGE STATÜSÜ: GİRDİ BELGESİ (v1.0)
>
> Bu rapor projenin **ilk kapsam çalışmasıdır** ve tarihsel kayıt olarak korunmaktadır.
> Yürürlükteki gereksinimler için: **[`00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md`](../00_Proje_Temeli/Sistem_Gereksinim_Belgesi.md)** (SGB v2.0)
>
> **Bu belgedeki bazı maddeler SGB v2.0 ile değiştirilmiştir.** Değişen maddelerin yanında `⚠️ SGB v2.0` işareti bulunur. Çelişki halinde **SGB geçerlidir.**
>
> Değişen ana maddeler: Bölüm 3 (cihazsız erişim modeli) · NFR-9 / SW-2 (frekans) · SW-6 · SW-8 · SW-11 · SW-13 · Bölüm 6.5 (kamu node'u)

---

## 1. Proje Tanımı ve Amaç

Doğal afetler (deprem, sel, yangın vb.) sonrasında GSM şebekesi ve internet altyapısı çoğunlukla çöker veya aşırı yüklenir. Bu projenin amacı; **GSM/internet gerektirmeyen, LoRa tabanlı Meshtastic protokolü üzerine kurulu, ucuz ve kendin-yap (DIY) yapılabilir bir afet iletişim cihazı** tasarlamaktır. Hedef:

- İnsanların düşük maliyetle satın alabileceği veya parçalarını sipariş ederek kendilerinin monte edebileceği bir **Meshtastic node cihazı** tanımlamak,
- Fiziksel cihazı olmayan kişilerin, **yakınlarındaki bir node'a akıllı telefonlarıyla (Bluetooth/BLE üzerinden Meshtastic uygulaması ile) bağlanarak** ağa dahil olabilmesini sağlamak,
- Bu cihazların bir araya gelerek afet bölgesinde kendiliğinden organize olan (self-forming, self-healing) bir **mesh iletişim ağı** oluşturmasını sağlamak.

---

## 2. Kapsam

**Kapsam içi:**
- Tek bir "temel node" donanım referans tasarımı (BOM, montaj rehberi)
- Meshtastic firmware üzerinde afet senaryosuna özel gerekli yapılandırma/eklentiler
- Mobil uygulama (mevcut Meshtastic app) üzerinden kullanıcı deneyimi gereksinimleri
- Şebekesiz/cihazsız kullanıcının ağa dahil olma modeli
- Güç, dayanıklılık, maliyet ve regülasyon gereksinimleri

**Kapsam dışı (bu aşamada):**
- Sıfırdan özel bir LoRa çipi/PCB tasarımı (mevcut açık kaynak modüller kullanılacak)
- Kendi mesh protokolünü yazmak (Meshtastic firmware'i temel alınacak, üzerine gerekirse özelleştirme yapılacak)
- Kitlesel üretim/endüstriyel sertifikasyon süreci

---

## 3. Kritik Teknik Kısıt: "Cihazsız Kullanıcı" Nasıl Çalışır?

> ### ⚠️ SGB v2.0 — BU BÖLÜM DEĞİŞTİRİLMİŞTİR
>
> Aşağıda tarif edilen **BLE tabanlı model geçersizdir.** İki nedenle:
> 1. Meshtastic'in `PhoneAPI` mimarisi aynı anda **tek istemci** destekler; yeni bağlanan öncekini düşürür. Kamu node'una 50 kişi yaklaştığında model çalışmaz.
> 2. BLE modeli, kullanıcının telefonunda **Meshtastic uygulamasının kurulu olmasını** gerektirir — afet anında internet olmadığı için indirilemez.
>
> **Yürürlükteki model:** Cihazsız kullanıcı, NOKTA'nın açtığı **WiFi ağına bağlanır** ve **captive portal** otomatik açılır. Uygulama kurulumu gerekmez, eşzamanlı en az 8 kullanıcı desteklenir.
> → SGB Bölüm 7 (NOK-2, NOK-3, NOK-4) ve Bölüm 8 (PORTAL)

Bu netleştirme, projenin doğru kurgulanması için kritik önemde:

> Akıllı telefonlarda LoRa radyosu **bulunmaz**. Bu nedenle bir telefon, tek başına ve hiçbir fiziksel node menzilinde olmadan mesh ağına giremez. "Cihazsız kullanıcı" katılımı şu şekilde gerçekleşir: kullanıcı, **Meshtastic mobil uygulamasını** telefonuna kurar ve **Bluetooth Low Energy (BLE) menzilinde (~10–30 m)** bulunan bir fiziksel node'a bağlanır. O node, kullanıcının mesajlarını LoRa üzerinden mesh ağına taşır.

**Bunun proje için sonucu:** Sistemin başarısı, sahadaki **node yoğunluğuna** bağlıdır. Bu yüzden gereksinim analizinde şu ikili strateji öneriliyor:
1. Bireylerin ucuza edinip evinde/aracında bulundurabileceği **kişisel node'lar**,
2. Toplu kullanım alanları (afet toplanma alanları, okullar, cami/mahalle muhtarlıkları, AFAD konteynerleri gibi) için sabit/solar beslemeli **"kamu node'ları"** — böylece cihazsız kişiler bu noktalara yaklaştığında telefonlarıyla ağa girebilir.

Bu iki bileşen ayrı ayrı gereksinim listelerinde ele alınmıştır (bkz. Bölüm 6.4 ve 6.5).

---

## 4. Paydaşlar ve Kullanıcı Profilleri

| Paydaş | Tanım | İhtiyaç |
|---|---|---|
| Afetzede (node sahibi) | Kendi node'unu önceden edinmiş kişi | Basit kurulum, uzun pil ömrü, dayanıklı gövde |
| Afetzede (cihazsız) | Sadece akıllı telefonu olan kişi | Yakın bir node'a kolayca bağlanabilme, sezgisel uygulama |
| Gönüllü / arama-kurtarma ekibi | Sahada koordinasyon sağlayan kişi | Konum paylaşımı, grup mesajlaşma, SOS/triage etiketleme |
| Topluluk / muhtarlık / STK | Sabit node kuran kurum | Solar güç, dış mekân dayanıklılığı, geniş kapsama alanı |
| Geliştirici / gönüllü üretici | Cihazı kendi üreten/dağıtan kişi | Açık BOM, düşük maliyet, kolay bulunur parçalar, net montaj talimatı |

---

## 5. Fonksiyonel Olmayan Gereksinimler (Genel Hedefler)

| # | Gereksinim | Hedef Değer / Açıklama |
|---|---|---|
| NFR-1 | Düşük maliyet | Temel node için hedef: **$25–40** (Türkiye'de KDV/gümrük dahil ~1000–1800 TL bandı, kur ve ithalat vergisine bağlı) |
| NFR-2 | Kolay temin edilebilirlik | Parçalar Türkiye'de veya AliExpress/Alibaba üzerinden kolayca sipariş edilebilir olmalı |
| NFR-3 | Kendin-yap uygunluğu | Lehimsiz veya minimum lehimle (JST konektörler, hazır modüller) monte edilebilmeli |
| NFR-4 | Pil ömrü | Tek şarjla en az **72 saat** aktif node çalışması (uyku modu optimizasyonu ile) |
| NFR-5 | Dayanıklılık | Minimum **IP54**, hedef **IP65** muhafaza; -10°C ile +50°C çalışma aralığı |
| NFR-6 | Kurulum süresi | Deneyimsiz kullanıcı için kutudan çıkış → çalışır node: **< 15 dakika** |
| NFR-7 | Kullanım kolaylığı | Teknik bilgisi olmayan biri Meshtastic mobil uygulamasını kurup 5 dakikada bağlanabilmeli |
| NFR-8 | Enerji bağımsızlığı | Şebeke elektriği olmadan (solar/powerbank) en az 1 hafta çalışabilme (kamu node'ları için) |
| NFR-9 ⚠️ | Yasal uygunluk | Türkiye'de lisanssız kullanılabilen ISM bandında çalışmalı. **⚠️ SGB v2.0: `EU_868` sabitlemesi kaldırıldı.** Türkiye'de topluluk ağı ağırlıklı olarak 433 MHz kullanıyor; yanlış band seçimi mevcut ağdan kopmaya yol açar. Karar askıya alındı → SGB **A-1** |
| NFR-10 | Sürdürülebilirlik | Açık kaynak Meshtastic firmware'inden mümkün olduğunca sapmadan, upstream ile uyumlu kalmak |

---

## 6. Donanım Gereksinimleri

> **⚠️ SGB v2.2 (24 Eyl 2026):** Bu bölüm ilk kapsam çalışmasıdır. Kişisel cihazın güncel donanım gereksinimleri, işlemci karşılaştırması ve malzeme seçimi için: [`02_Donanim/Donanim_Gereksinim_Raporu.md`](../02_Donanim/Donanim_Gereksinim_Raporu.md) v2.0. Özetle: kişisel node **nRF52840 + SX1262 (RAK4630)**; aşağıdaki tabloda "kişisel node için ideal" denen Heltec V3 (ESP32-S3) ölçülmüş verilerde 10–30 saat pil ömrü gösterdiği için kişisel node olarak **önerilmez** (geliştirme/test kartı olarak kullanılabilir).

### 6.1 Zorunlu Bileşenler (Temel Node)

| Bileşen | Gereksinim | Aday Ürün(ler) |
|---|---|---|
| MCU + LoRa modülü ⚠️ | SX1262 tabanlı, Meshtastic destekli, **A-1 ile seçilecek bandı destekleyen** | Heltec WiFi LoRa 32 V3, RAK4631 (nRF52840+SX1262), LILYGO T-Beam Supreme |
| Anten ⚠️ | **Seçilen banda uygun** (433 veya 868 MHz), harici, SMA konnektörlü | Standart "duck" anten (üreticiyle birlikte gelir; menzil için harici katlanır anten önerilir) |
| Pil | 18650 Li-ion veya LiPo, min. 2000 mAh | 18650 pil + tutucu (değiştirilebilir, yaygın bulunur) |
| Şarj devresi | USB-C girişli, entegre BMS | Çoğu geliştirme kartında dahili (TP4056 vb.) |
| Muhafaza | Su/toz dirençli, darbeye dayanıklı | 3D baskı gövde (STL dosyası dahil edilecek) veya hazır IP65 kutu |
| Anahtar/Buton | Açma-kapama + opsiyonel SOS butonu | Basit taktil buton, GPIO'ya bağlı |

### 6.2 Opsiyonel Bileşenler (Genişletilmiş Node)

| Bileşen | Amaç |
|---|---|
| GPS modülü (ör. dahili T-Beam GPS) | Otomatik konum paylaşımı, "son bilinen konum" broadcast |
| Küçük OLED/e-ink ekran | Pilsiz telefon durumunda dahi node durumunu/mesajları görebilme |
| Solar panel (5–10 W) + güneş şarj kontrolcüsü | Kamu/sabit node'lar için sınırsız çalışma süresi |
| Harici yüksek kazançlı anten | Kamu node'ları için menzil artırımı (tepe noktalarına kurulacaklar için) |

### 6.3 Referans Donanım Karşılaştırması (Ağustos 2026 itibarıyla)

| Model | Yaklaşık Fiyat | Notlar |
|---|---|---|
| Heltec WiFi LoRa 32 V3 | $20–30 | En düşük maliyetli giriş noktası, ESP32-S3 + SX1262 + OLED, kişisel node için ideal |
| RAK4631 + WisBlock taban kartı | ~$35 (başlangıç kiti) | Modüler, düşük güç tüketimi (nRF52840), lehimsiz GPS/solar/sensör eklenebilir — **DIY için önerilen** |
| LILYGO T-Beam (Supreme) | $85–120 | Dahili GPS + 18650 pil yuvası, mobil/arama-kurtarma senaryosu için uygun ama daha pahalı |
| T-Echo | Değişken | E-ink ekran + GPS + BLE, düşük güç, ekran gerektiren kullanım senaryoları için |

> Not: Kesin fiyatlar tedarikçiye, kura ve gümrük/ithalat koşullarına göre değişir; nihai BOM seçimi Türkiye'de tedarik araştırması yapılarak netleştirilmeli.

### 6.4 Kişisel Node Gereksinimleri
- Cepte/çantada taşınabilir boyut (< 150 g hedef)
- Tek elle çalıştırılabilir SOS butonu
- Görsel/titreşimli/sesli durum bildirimi (pil, bağlantı durumu)

### 6.5 Kamu / Sabit Node Gereksinimleri

> **⚠️ SGB v2.0:** Kamu node'u artık temel node'un bir varyantı değil, **ayrı bir cihaz sınıfıdır (NOKTA)** — ESP32-S3 + PSRAM + WiFi + solar. Aşağıdaki maddeler geçerliliğini korur ancak eksiktir; tam liste SGB Bölüm 7'dedir (NOK-1…NOK-11).
- Dış mekâna montaj için ayaklı/duvar tipi muhafaza
- Solar + batarya ile sürekli çalışma
- Yüksek nokta kurulumuna uygun (çatı, direk, minare/cami gibi yüksek yapılar) — menzili maksimize etmek için
- Görünür/işaretli olmalı ("Buraya telefonla bağlanabilirsiniz" bilgilendirme etiketi)

---

## 7. Yazılım Gereksinimleri

### 7.1 Firmware (Node üzerinde çalışan)
| # | Gereksinim |
|---|---|
| SW-1 | Meshtastic açık kaynak firmware'i temel alınacak (özelleştirme upstream ile uyumlu tutulacak) |
| SW-2 ⚠️ | ~~Bölge ayarı **EU_868** olarak yapılandırılmalı~~ → **SGB v2.0: askıya alındı, bkz. A-1** |
| SW-3 | Varsayılan kanal şifrelemesi **açık (PSK ayarlı)** olmalı — herkese açık afet kanalı yayın amaçlı şifresiz/bilinen PSK, özel gruplar için PSK korumalı kanal desteklenmeli |
| SW-4 | Doğrudan mesajlarda PKC (public-key) uçtan uca şifreleme aktif olmalı (Meshtastic 2.5+ firmware ile gelir) |
| SW-5 | Düşük güç / uyku modu, pil tasarrufu için yapılandırılmalı |
| SW-6 ⚠️ | SOS/acil durum mesaj önceliklendirmesi. **⚠️ SGB v2.0: Bu bir yapılandırma değil, geliştirme kalemidir.** Meshtastic'te acil mesaj önceliği **yoktur** (upstream talebi #7980 kapatılmıştır). Sıfırdan geliştirilecek → SGB **SOS-1, SOS-2** |
| SW-7 | Node'un "genel/kamu node'u" olduğunu belirten bir isimlendirme/etiketleme standardı (ör. `AFAD-`, `HALK-` ön eki) |

### 7.2 Mobil Uygulama (Kullanıcı Tarafı)
| # | Gereksinim |
|---|---|
| SW-8 ⚠️ | **⚠️ SGB v2.0: Değiştirildi.** Cihazsız kullanıcının birincil arayüzü **PORTAL**'dır (NOKTA üzerinde barındırılan web sayfası — mobil uygulama değildir, kapsam içidir). Resmî Meshtastic uygulaması yalnızca **CEP sahibi** kullanıcılar için opsiyoneldir. Özel mobil uygulama geliştirmek hâlâ kapsam dışıdır |
| SW-9 | Kullanıcıya afet moduna özel önceden tanımlı hızlı mesaj şablonları sunulmalı ("Güvendeyim", "Yardım gerekiyor", "Yaralı var", "Konumumu paylaşıyorum") — Meshtastic app'in "Quick Chat" özelliği ile uygulanabilir |
| SW-10 | Offline harita (önceden indirilmiş) ile GPS konum gösterimi |
| SW-11 ⚠️ | ~~"En yakın node'a bağlan" sihirbazı~~ → **SGB v2.0: İPTAL.** Captive portal bu ihtiyacı ortadan kaldırıyor: kullanıcı WiFi'a bağlanır, arayüz kendiliğinden açılır |

### 7.3 Ağ / Mesaj Yönetimi
| # | Gereksinim |
|---|---|
| SW-12 | Mesaj boyutu sınırı (Meshtastic paket başına ~237 byte) göz önünde bulundurularak kısa/öz mesaj şablonları tasarlanmalı |
| SW-13 ⚠️ | **⚠️ SGB v2.0: Ayar optimizasyonu yeterli değil, rol disiplini gerekir.** Meshtastic managed-flooding kullanır; her node tekrarlarsa toplanma alanında kanal tıkanır. Çözüm: **CEP = `CLIENT_MUTE`** (tekrarlamaz), **NOKTA = `ROUTER`** → SGB **CEP-2, NOK-6, SOS-3** |
| SW-14 | Opsiyonel: İnternetin kısmen geri geldiği durumlarda MQTT üzerinden mesh verisinin bir "durum panosu"na (dashboard) köprülenmesi — arama-kurtarma koordinasyon merkezleri için |

### 7.4 Koordinasyon / İzleme (Opsiyonel, İleri Aşama)
| # | Gereksinim |
|---|---|
| SW-15 | Node haritası: hangi node'ların aktif olduğunu gösteren basit bir web panosu (MQTT + Meshtastic entegrasyonu ile) |
| SW-16 | SOS mesajlarının otomatik olarak öne çıkarıldığı bir izleme arayüzü (gönüllü koordinasyon merkezleri için) |

---

## 8. Üst Düzey Sistem Mimarisi (Kavramsal)

```
[Telefon - cihazsız kullanıcı] --BLE--> [Kişisel/Kamu Node] --LoRa mesh--> [Diğer Node'lar] --LoRa mesh--> [Kamu Node (solar, yüksek nokta)]
                                                                                                        |
                                                                                              (opsiyonel, internet varsa)
                                                                                                        v
                                                                                          [MQTT Gateway --> Koordinasyon Panosu]
```

- Ağ tamamen **merkeziyetsiz**; tek bir node'un çökmesi ağın geri kalanını etkilemez (self-healing mesh).
- İnternet/GSM olmadan tamamen çalışır; internet varsa (kısmi kurtarma sonrası) opsiyonel köprüleme ile dış dünyaya bilgi aktarılabilir.

---

## 9. Regülasyon ve Yasal Hususlar (Türkiye)

- 868 MHz bandı, Avrupa'da olduğu gibi Türkiye'de de genel olarak **lisanssız kısa menzilli cihaz (SRD)** bandı kapsamında değerlendirilir; ancak **BTK'nın güncel "Telsiz Kullanılmasına İzin Verilmeyen Cihazlar" ve SRD teknik düzenlemeleri ile kesin teyit edilmesi gerekir** — bu, projenin ilk araştırma adımlarından biri olmalı.
- ~~Meshtastic firmware'de bölge **EU_868** olarak ayarlanmalı~~ **⚠️ SGB v2.0: Band kararı açıktır (A-1).** 868 MHz seçilirse `EU_868` bölge ayarı ETSI EN 300 220 SRD kurallarına (güç, duty-cycle) uyumludur; 433 MHz seçilirse ERP limiti ~10 mW'tır. Karar, sahadaki topluluk ağının fiilî bandına göre verilecektir.
- Anten kazancı ve verici gücü, ilgili SRD güç limitlerini (genelde 25 mW ERP bandına göre değişen alt bantlar) aşmayacak şekilde yapılandırılmalı.
- Projenin "afet senaryosu" kapsamında STK/AFAD/yerel yönetim iş birliği olursa, resmi izin/koordinasyon süreci ayrıca değerlendirilmeli.

**Aksiyon:** BTK'nın güncel SRD/ISM bant düzenlemesi resmi kaynaktan (btk.gov.tr) teyit edilmeli — bu rapor bir hukuki görüş değildir.

---

## 10. Riskler ve Kısıtlar

| Risk | Etki | Azaltma Stratejisi |
|---|---|---|
| Yetersiz node yoğunluğu → cihazsız kullanıcı ağa giremez | Yüksek | Kamu node'larının stratejik (toplanma alanı, yüksek nokta) yerleşimi; önceden dağıtım planı |
| LoRa menzili şehir içi bina yoğunluğunda düşer (tipik 1–3 km açık alan, yüz metreler bina arası) | Yüksek | Kamu node'larını yüksek noktalara (çatı, minare, direk) yerleştirme; node sayısını artırma |
| Pil / güç kaynağı afet sonrası tükenir | Orta | Solar şarj, powerbank önerisi, düşük güç modu varsayılan |
| Kullanıcıların teknik bilgi eksikliği | Orta | Çok basit, görsel montaj ve kullanım rehberi; önceden test edilmiş "tak-çalıştır" kutu |
| Parça tedarik zinciri riski (döviz kuru, gümrük) | Orta | Birden fazla tedarikçi/alternatif parça listesi (Heltec, RAK vb.) |
| Regülasyon belirsizliği | Düşük-Orta | BTK ile erken teyit |
| Meshtastic upstream firmware değişiklikleri ile uyumsuzluk | Düşük | Özelleştirmeleri minimumda tutup upstream'i takip etme |

---

## 11. Test ve Doğrulama Kriterleri

- **Menzil testi:** Açık alanda ve şehir içi yerleşimde node-to-node menzil ölçümü
- **Pil ömrü testi:** Gerçek kullanım senaryosunda (periyodik konum + mesajlaşma) 72 saat dayanım doğrulaması
- **Dayanıklılık testi:** IP derecesi doğrulama (su püskürtme/toz testi), düşürme testi
- **Kullanılabilirlik testi:** Teknik bilgisi olmayan gönüllülerle kurulum süresi ve anlaşılabilirlik testi
- **Ağ ölçeklenebilirlik testi:** 20–50 node ile simüle edilmiş afet senaryosu, mesaj gecikmesi/kaybı ölçümü
- **Enerji testi:** Solar node'un bulutlu havada da çalışabilirliğinin doğrulanması

---

## 12. Özet ve Sonraki Adımlar (Karar Bekleyen Konular)

> **⚠️ SGB v2.0:** Bu listedeki kararların güncel durumu **SGB Bölüm 12 (Açık Kararlar A-1…A-6)** ve **Bölüm 14 (Yol Haritası)** altındadır. Madde 4 (yazılım kapsamı) ve madde 5 (pilot bölge) yanıtlanmıştır: yazılım kapsamı **PORTAL + SOS modülü + PANO**'yu içerir; pilot muhatabı **ilçe belediyesinin bilgi işlem / afet koordinasyon birimidir.**

Bu rapor, geliştirmeye başlamadan önce üzerinde hizalanmamız gereken temel gereksinimleri ortaya koyar. Bir sonraki oturumda birlikte karar vermemiz gereken ana konular:

1. **Hedef donanım platformu seçimi** — Heltec V3 mü, RAK4631 mü, yoksa her ikisi için de mi (kişisel/kamu ayrımı) destek verilecek?
2. **Bütçe/BOM kesinleştirmesi** — Türkiye'de gerçek tedarik fiyatlarının araştırılması
3. **Muhafaza tasarımı** — 3D baskı mı, hazır kutu mu?
4. **Yazılım özelleştirme kapsamı** — Sadece firmware yapılandırması mı, yoksa özel bir onboarding/koordinasyon katmanı da eklenecek mi?
5. **Pilot bölge/senaryo** — İlk prototipin test edileceği somut bir yer/senaryo belirlenmeli (ör. bir mahalle, kampüs)

---

## Kaynaklar (Araştırma sırasında referans alınan)

- [Meshtastic Hardware Buyer's Guide 2026 — nodakmesh.org](https://nodakmesh.org/meshtastic/devices)
- [Meshtastic Device Comparison — d-central.tech](https://d-central.tech/meshtastic-device-comparison/)
- [Meshtastic Encryption — meshtastic.org](https://meshtastic.org/docs/overview/encryption/)
- [Introducing Public Key Cryptography in v2.5 — meshtastic.org blog](https://meshtastic.org/blog/introducing-new-public-key-cryptography-in-v2_5/)
- [BTK Amatör Telsizcilik](https://www.btk.gov.tr/amator-telsizcilik)
