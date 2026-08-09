# Farklılaşma Analizi ve Ürün Mimarisi Raporu (v2)


---

## 1. Yönetici Özeti — Tezin Yeniden Tanımı

Rekabet analizi, projenin gerçek bir boşluğa yöneldiğini ancak boş bir alana girmediğini göstermişti. Bu rapor, o boşluğu **teknik olarak tarif edilebilir tek bir probleme** indirger:

> ### "Son 300 Metre" Problemi
> Sektördeki tüm ürünler (Meshtastic, MeshCore, MeshGrid, Ulaş) **node sahibinden node sahibine** iletişimi çözüyor. Hiçbiri, **cihazı olmayan, uygulaması kurulu olmayan, afetten önce hiçbir hazırlık yapmamış** vatandaş ile ağ arasındaki son adımı çözmüyor.

Afetzedelerin ezici çoğunluğu bu tarife giriyor. Dolayısıyla projenin farklılaşma noktası "daha iyi bir node yapmak" değil, **bu son adımı kapatan ilk ürün olmaktır**.

### Bu raporda değişen üç şey

| # | Değişiklik | Neden |
|---|---|---|
| **D-1** | Kamu node'u, kişisel node ile **aynı donanım ailesinden çıkarıldı** | Meshtastic'in iki teknik kısıtı (tek BLE istemcisi, PSRAM zorunluluğu) kamu node'unun nRF52840 olmasını imkânsız kılıyor |
| **D-2** | Cihazsız kullanıcı erişimi **BLE'den WiFi captive portal'a** taşındı | BLE aynı anda tek istemci destekliyor ve uygulama kurulumu gerektiriyor. İkisi de afet anında kabul edilemez |
| **D-3** | Yazılım kapsamı "yapılandırma"dan **"gerçek geliştirme"ye** çıkarıldı | Projenin özgün katkısı artık donanımda değil, portal + SOS önceliklendirme + triage katmanında |

---

## 2. Sektör Ürünlerinin Eksik Yönleri

### 2.1 Meshtastic Çekirdeği (Temel Aldığımız Platform)

Meshtastic'i temel almak doğru karardır — ekosistem, ağ etkisi ve olgunluk oradadır. Ancak afet senaryosu için **beş yapısal eksiği** vardır ve bunların üçü projenin mevcut varsayımlarını doğrudan geçersiz kılar.

#### E-1 — Bir node'a aynı anda yalnızca tek telefon bağlanabiliyor ⚠️ KRİTİK

Meshtastic'in `PhoneAPI` mimarisi tek eşzamanlı istemci üzerine kuruludur; yeni bir istemci bağlandığında mevcut bağlantı düşürülür. Firmware deposundaki açık kayıt bunu mimari bir kusur olarak tanımlıyor: "*Only one connection to the device via PhoneAPI is allowed at any time*" (Issue #526). Topluluk forumlarında iki Android cihazın aynı T-Beam'e bağlanamadığı raporlanmıştır.

**Projeye etkisi — doğrudan ve yıkıcı:** Gereksinim Analizi Bölüm 3, projenin çekirdek mekanizmasını *"cihazsız kullanıcı BLE menzilindeki node'a bağlanır"* olarak tanımlıyor. Bir afet toplanma alanındaki kamu node'una 50 kişi yaklaştığında, bu mekanizma **sırayla tek kişiye** hizmet verir ve her yeni bağlanan bir öncekini ağdan düşürür. Kamu node'u kavramı bu haliyle çalışmaz.

> **Doğrulama notu:** Bu kısıt resmî dokümantasyonda açıkça yazmıyor; kaynağı firmware issue'su ve saha raporlarıdır. **Laboratuvarda iki telefonla doğrulanması zorunludur** (bkz. Bölüm 7, T-1). Ancak aşağıda önerilen çözüm bu sorudan bağımsız olarak doğru olduğu için, mimari kararı doğrulamayı beklemeden alabiliriz.

#### E-2 — Uygulama kurulumu gerektiriyor ⚠️ KRİTİK

Mevcut model, afetzedenin telefonunda **Meshtastic uygulamasının kurulu olmasını** şart koşuyor (SW-8). Afet anında:
- İnternet yok → uygulama indirilemez
- Afetten önce kurmuş olan nüfus oranı binde birler seviyesinde
- Uygulama teknik terimlerle dolu (kanal, PSK, preset, hop limit) — SW-7'nin hedeflediği "teknik bilgisi olmayan kullanıcı" ile uyumsuz

Bu, projenin hedef kitlesini "cihazsız vatandaş"tan "önceden hazırlık yapmış meraklı"ya daraltır — yani rekabet analizinde tespit edilen **boşluğu doldurmaz, TA Mesh ile aynı yere düşer**.

#### E-3 — Acil durum mesajlarının önceliği yok

Meshtastic'te SOS/panik mesajları normal paketlerle **aynı öncelikte** işlenir; yoğun trafikte gecikebilir veya düşebilir. Firmware deposunda 13 Eylül 2025'te açılan "Emergency Mode for Critical Packets" talebi (#7980) tam olarak bu eksiği tarif ediyor ve **kapatılmış durumda** — yani upstream'de böyle bir mekanizma yok.

**Projeye etkisi:** Gereksinim Analizi'ndeki **SW-6** ("SOS mesaj önceliklendirmesi") mevcut bir özelliğin yapılandırılması sanılıyordu. Değil — **sıfırdan geliştirilmesi gereken bir yetenek.** Bu aynı zamanda projenin en somut özgün katkı alanıdır.

#### E-4 — Store & Forward, seçilen donanımda çalışmıyor ⚠️ DONANIM RAPORUYLA ÇELİŞİYOR

Resmî dokümantasyon net: *"Only ESP32 based devices with onboard PSRAM like the T-Beam and T3S3 can be a Store & Forward Server."* **nRF52840 desteklenmiyor.**

Ek sınırlamalar:
- Varsayılan **public kanalda geçmiş mesaj talebi çalışmıyor** — yalnızca özel kanallarda
- Sunucu istemcinin hangi mesajları kaçırdığını bilmiyor → **yinelenen mesaj** gönderiyor
- Geçmiş talebi ağa ani yük bindiriyor

**Projeye etkisi:** Donanım Raporu **K-2** kararı nRF52840'ı seçiyor (pil ömrü gerekçesiyle — ki bu gerekçe kişisel node için doğrudur). Ancak bu, seçilen donanımın **mesaj biriktiremeyeceği** anlamına gelir. Afette en kritik senaryo şudur: *"Enkaz altındaki kişi SOS gönderdi, o anda menzilde kimse yoktu."* Mesaj biriktirilmezse **kaybolur.**

#### E-5 — Yoğun ortamda tıkanma (managed flooding)

Meshtastic'te her node gördüğü paketi tekrarlar. Bu, node arızalarına karşı dayanıklılık sağlar ama **yoğunlukta çöker**: birkaç düzine aktif kullanıcıda hava zamanı çekişmesi artar, varsayılan `LongFast` preset'i büyük ağlarda gecikme ve paket kaybına yol açar.

**Projeye etkisi:** Afet toplanma alanı, tanımı gereği **en yoğun senaryodur** — yüzlerce insan, onlarca node, hepsi aynı anda mesaj göndermeye çalışıyor. Yani Meshtastic'in en zayıf olduğu koşul, projenin birincil kullanım senaryosudur. Gereksinim Analizi **SW-13** bunu görmüş ama çözümü "ayarların optimize edilmesi"ne bırakmış; bu yeterli değildir, **rol disiplini** gerekir.

### 2.2 Rakip Ürünlerin Eksikleri

| Ürün | Güçlü yönü | Eksik / geliştirilebilir yönü | Bizim için fırsat |
|---|---|---|---|
| **MeshCore** | Kaynak-yönlendirmeli mimari, 64 hop'a kadar, çok daha az kanal gürültüsü; "room server"lar mesaj biriktirip istemci döndüğünde teslim ediyor | **Altyapı bağımlı** — istemci node'lar tekrarlama yapmaz; bölgedeki tek repeater düşerse o bölge tamamen kopar. Afet, tam olarak altyapının düştüğü andır | Store yeteneğini al, altyapı bağımlılığını alma. Kişisel node'lar mesh'i taşımaya devam etsin |
| **MeshGrid** | Özgün şifreli protokol (MeshLink), profesyonel saha UX'i, harita üzerinde ekip takibi | **Kapalı ve ücretli** (12,99 USD lisans) → afetzede için bariyer. **Kendi protokolü** → Meshtastic ağıyla konuşmaz, sıfırdan node yoğunluğu kurmak zorunda. Uygulama önceden kurulmuş olmalı | Açık kaynak + ücretsiz + mevcut ağla uyumlu olmak, üçü birden farklılaşma |
| **Ulaş** | Bütünsel ekosistem vizyonu, sensör entegrasyonu (yangın/gaz) | Cihaz başına **10–15 km menzil iddiası** açık alan rakamı; şehir içi gerçeklikle uyumsuz. Doğrulanabilir teknik detay kamuya açık değil | Ölçülmüş, dürüst menzil rakamlarıyla güven inşa etmek |
| **TA Mesh** | Gerçek ve yaşayan ağ, MQTT altyapısı, yerli kart programı (TMDC) | Hedef kitlesi **teknik meraklı**. Kamu node'u konumlandırma stratejisi ve halk katmanı yok. Süreklilik gönüllülüğe bağlı | Rakip değil taban. Onların ağı üzerine halk katmanını inşa etmek |
| **Kamu sistemleri** (KGHS, ASELSAN, ULAK) | Profesyonel, güvenilir, bütçeli | **Vatandaş katmanı yok** — hepsi kurumlar arası | Boşluğun kendisi |

### 2.3 Ortak Eksik: Kimse "Hazırlıksız Kullanıcıyı" Çözmüyor

Yukarıdaki tabloda dikey olarak okunduğunda görülen ortak boşluk şudur — **her ürün, kullanıcının afetten önce bir şey yapmış olmasını varsayıyor:**

| Ürün | Kullanıcıdan beklenen ön hazırlık |
|---|---|
| Meshtastic | Cihaz satın al + uygulama kur + kanal yapılandır |
| MeshCore | Cihaz satın al + uygulama kur |
| MeshGrid | Uygulama kur + lisans satın al |
| Amatör telsiz | Lisans sınavı + telsiz satın al |
| **AfetMesh (hedef)** | **Hiçbiri** |

---

## 3. Tasarım İlkeleri (Bu Eksiklerden Türetilmiş)

Aşağıdaki dört ilke, yukarıdaki eksiklerin doğrudan karşılığıdır ve bundan sonraki tüm tasarım kararlarının ölçütüdür.

| İlke | Tanım | Kapattığı eksik |
|---|---|---|
| **İ-1 — Kurulum gerekmez** | Kullanıcı hiçbir uygulama indirmeden, yalnızca telefonunun tarayıcısıyla ağa erişebilmeli | E-2 |
| **İ-2 — Ön hazırlık gerekmez** | Afetten önce hiçbir şey yapmamış kişi de sistemi kullanabilmeli. Hazırlık yükü **kamu node'unu kuran tarafta**, vatandaşta değil | E-2, rekabet boşluğu |
| **İ-3 — Mesaj kaybolmaz** | Alıcı o anda menzilde olmasa bile mesaj biriktirilir ve teslim edilir | E-4 |
| **İ-4 — SOS ayrıcalıklıdır** | Acil mesajlar ayrı, kısa, yapısal ve öncelikli taşınır; normal trafikle aynı kuyruğa girmez | E-3, E-5 |

> Bir tasarım kararı bu dört ilkeden birini ihlal ediyorsa, teknik olarak ne kadar zarif olursa olsun reddedilir. Örneğin "kullanıcıya özel bir AfetMesh uygulaması yazalım" fikri İ-1'i ihlal ettiği için kapsam dışıdır.

---

## 4. Yeni Ürün Mimarisi

### 4.1 Üç Bileşen

Önceki yapıda tek bir cihaz ailesi vardı ve kamu node'u onun bir varyantıydı. Yeni yapıda **iki farklı cihaz sınıfı ve bir yazılım katmanı** vardır:

```
   ┌─ AFETZEDE (hazırlıksız, uygulamasız) ─┐
   │        telefon → WiFi                  │
   └────────────────┬───────────────────────┘
                    │ (İ-1: tarayıcı, kurulum yok)
          ┌─────────▼──────────────────────────────┐
          │  ② AfetMesh NOKTA  (Kamu node'u)       │
          │  ESP32-S3 + PSRAM + solar              │
          │  • WiFi AP + Captive Portal            │
          │  • Store & Forward sunucusu (İ-3)      │
          │  • ROUTER rolü, yüksek noktada         │
          └─────────┬──────────────────────────────┘
                    │ LoRa mesh (868/433 MHz)
       ┌────────────┼────────────────┐
       │            │                │
┌──────▼──────┐ ┌───▼──────┐ ┌───────▼────────┐
│ ① AfetMesh  │ │ Diğer    │ │ ② Başka bir    │
│    CEP      │ │ Meshtastic│ │    NOKTA       │
│ nRF52840    │ │ node'ları │ │                │
│ CLIENT_MUTE │ │ (TA Mesh) │ └───────┬────────┘
│ SOS + buzzer│ └──────────┘         │
└─────────────┘                       │ (internet döndüğünde)
                                      ▼
                          ┌───────────────────────┐
                          │ ③ AfetMesh PANO       │
                          │ SOS triage listesi    │
                          └───────────────────────┘
```

### 4.2 ① AfetMesh CEP — Kişisel Node

**Değişiklik yok.** Donanım Raporu'nun tamamı (nRF52840 + SX1262, 18650, IP54, SOS butonu, buzzer) bu cihaz için geçerlidir ve doğrudur. Yalnızca iki ekleme:

| # | Yeni gereksinim | Gerekçe |
|---|---|---|
| **AF-C1** | Varsayılan rol **`CLIENT_MUTE`** olacak (paket tekrarlamayacak) | E-5. Binlerce cep node'unun hepsi tekrarlarsa toplanma alanında kanal tıkanır. Tekrarlama işi NOKTA'ların görevidir |
| **AF-C2** | **Enkaz Modu:** SOS butonuna uzun basıldığında cihaz periyodik buzzer sinyali verecek (ör. 30 sn'de bir çift bip) ve düşük güçte SOS yayınlayacak | Enkaz altında sesli bulunabilirlik, radyo menzilinden daha etkili olabilir. Donanım Raporu 5.4'te buzzer zaten var; bu onu bir kurtarma işlevine dönüştürür |

> Donanım Raporu **K-1** kararı (tek PCB, iki varyant) geçerliliğini koruyor — ancak **Varyant B artık "kamu node'u" değil, "saha ekibi node'u"dur** (e-ink + GPS + sensör, arama-kurtarma gönüllüsü için). Kamu node'u ayrı bir cihaz sınıfına taşındı.

### 4.3 ② AfetMesh NOKTA — Kamu Node'u (YENİ TASARIM)

Projenin farklılaşmasının **tamamı bu cihazda**.

| # | Gereksinim | Gerekçe |
|---|---|---|
| **AF-N1** | MCU **ESP32-S3 + PSRAM** olacak | E-1 ve E-4'ün ikisini birden çözen tek seçenek: WiFi AP yeteneği + Store & Forward için PSRAM. nRF52840 ikisini de yapamaz |
| **AF-N2** | **WiFi Access Point** açacak; SSID `AFETMESH-<mahalle>` biçiminde, şifresiz | İ-1. Afette insanlar zaten içgüdüsel olarak WiFi arar. Tanınabilir SSID keşfedilebilirliği çözer |
| **AF-N3** | **Captive portal** çalıştıracak; telefon ağa bağlandığında arayüz **otomatik açılacak** | İ-1. iOS ve Android'in yerleşik captive portal algılaması kullanılır; kullanıcı hiçbir şey yazmaz |
| **AF-N4** | Eşzamanlı **en az 8 istemci** desteklenecek | E-1. ESP32 SoftAP varsayılan 4, yapılandırmayla 8–10 istasyona çıkar |
| **AF-N5** | **Store & Forward sunucusu** olarak çalışacak; tüm SOS mesajlarını kalıcı olarak saklayacak | İ-3, E-4 |
| **AF-N6** | Meshtastic rolü **`ROUTER`** olacak | E-5. Tekrarlama yükü buraya toplanır |
| **AF-N7** | Solar + batarya ile **kesintisiz** çalışacak; hedef ≥ 7 gün güneşsiz otonomi | NFR-8 |
| **AF-N8** | Yüksek noktaya (çatı, direk) montaj ve harici yüksek kazançlı anten desteği | Menzil (Bölüm 6.5) |
| **AF-N9** | Üzerinde **görünür bilgilendirme etiketi** bulunacak: *"Telefonunuzun WiFi'ını açın → AFETMESH ağına bağlanın"* | İ-2. Sistemin kullanım kılavuzu cihazın kendisinde olmalı |

#### ⚠️ Güç bütçesi — dürüst hesap

WiFi AP sürekli açık tutmak pahalıdır ve bu, tasarımın en zayıf noktasıdır:

| Kalem | Değer |
|---|---|
| ESP32-S3 SoftAP boşta tüketim | ~80–120 mA @ 3.3 V |
| Günlük enerji (sürekli açık) | ≈ 8–10 Wh |
| LoRa TX + S&F yükü | ≈ +2 Wh |
| **Toplam günlük ihtiyaç** | **≈ 12 Wh** |
| Önerilen panel | **20 W** (kışın bulutlu günde ~15 Wh üretir) |
| Önerilen batarya | **≥ 100 Wh LiFePO4** (7+ gün otonomi, geniş sıcaklık aralığı, uzun çevrim ömrü) |

**Azaltma stratejisi — iki modlu çalışma:**
- **Normal mod:** WiFi AP kapalı; LoRa mesh aktif. Cihaz üzerindeki butona basıldığında AP 15 dakika açılır. Tüketim ~%15'e iner.
- **Afet modu:** Ağdan gelen bir afet sinyali veya ivmeölçer ile deprem algılandığında AP **72 saat boyunca sürekli açık** kalır.

> Bu ikili mod, sistemi hem sürdürülebilir hem de afet anında hazır kılar. Normal modda buton gerektirmesi İ-1'i ihlal etmez; afet anında — yani önemli olduğu anda — sistem tamamen otomatiktir.

> **Not:** Bu cihaz için özel PCB tasarımı **gerekmiyor.** Kamu node'ları adetçe az (bir mahallede 3–5) ve maliyete duyarsızdır (solar panel zaten cihazdan pahalı). Hazır bir ESP32-S3 + SX1262 kartı (Heltec V3/T3S3 sınıfı) + IP65 kutu + solar şarj kontrolcüsü ile **reçete olarak** tanımlanmalıdır. Bu, Donanım Raporu Bölüm 2'deki "düşük adette özel PCB pahalıdır" uyarısıyla tutarlıdır.

### 4.4 ③ AfetMesh PORTAL — Captive Portal Arayüzü (YENİ, PROJENİN ÖZGÜN YAZILIMI)

NOKTA üzerinde çalışan, ~50 KB'lık statik web arayüzü. Meshtastic firmware'ine **ayrı bir modül** olarak eklenir.

**Ekran akışı (tek sayfa, kaydırmasız):**

```
┌────────────────────────────────────┐
│        AFETMESH — Fatih Camii      │
│   Bu ağ internet gerektirmez.      │
├────────────────────────────────────┤
│   ┌──────────────────────────────┐ │
│   │   🆘  YARDIM İSTİYORUM        │ │  ← tek dokunuş, öncelikli
│   └──────────────────────────────┘ │
│   ┌──────────────────────────────┐ │
│   │   ✅  GÜVENDEYİM              │ │
│   └──────────────────────────────┘ │
│   ┌──────────────────────────────┐ │
│   │   🩹  YARALI VAR              │ │
│   └──────────────────────────────┘ │
├────────────────────────────────────┤
│   Adınız: [____________]           │
│   Mesaj:  [____________] (140 kr.) │
│   Kaç kişi: [ 1 ] [ 2-5 ] [ 5+ ]   │
│                        [ GÖNDER ]  │
├────────────────────────────────────┤
│   Gelen mesajlar (son 20)          │
└────────────────────────────────────┘
```

| # | Gereksinim |
|---|---|
| **AF-P1** | Arayüz Türkçe olacak, teknik terim (kanal, PSK, preset, hop) **hiç kullanılmayacak** |
| **AF-P2** | Tek dokunuşla gönderilebilen 3 acil durum butonu bulunacak |
| **AF-P3** | Serbest metin 140 karakter ile sınırlanacak (hava zamanı bütçesi) |
| **AF-P4** | Tamamen çevrimdışı çalışacak; **hiçbir harici kaynak (CDN, font, harita)** yüklemeyecek |
| **AF-P5** | Toplam sayfa boyutu < 50 KB olacak (yavaş bağlantı ve flash sınırı) |
| **AF-P6** | Gönderilen mesajın ağa ulaştığı kullanıcıya görsel olarak teyit edilecek |
| **AF-P7** | Kullanıcı kimliği için telefon numarası veya kişisel veri **istenmeyecek** (yalnızca isteğe bağlı ad) |

> **AF-P7 önemlidir:** Kişisel veri toplamak KVKK yükümlülüğü doğurur ve afet anında güven kırar. Sistem, yalnızca kullanıcının gönüllü yazdığı adı taşır.

### 4.5 Yapılandırılmış SOS Paketi (YENİ)

E-3'ün çözümü. Serbest metin yerine, kendi port numaramız üzerinden taşınan kompakt ikili yapı:

| Alan | Boyut | İçerik |
|---|---|---|
| Tip | 1 byte | 0=güvendeyim, 1=yardım, 2=yaralı, 3=enkaz altında |
| Kişi sayısı | 1 byte | 1 / 2-5 / 5+ kodlanmış |
| Konum | 8 byte | lat/lon (int32 ×1e7) — varsa |
| Zaman | 4 byte | Unix zaman damgası |
| Ad + mesaj | ≤ 45 byte | UTF-8, kırpılmış |
| **Toplam** | **≤ 59 byte** | Meshtastic'in 237 byte sınırının çok altında |

| # | Gereksinim |
|---|---|
| **AF-S1** | SOS paketleri ayrı bir Meshtastic port numarası kullanacak (normal metinden ayrışacak) |
| **AF-S2** | SOS paketleri firmware kuyruğunda **normal trafiğin önüne** alınacak |
| **AF-S3** | SOS paketi tekrar aralığı sınırlanacak (aynı kaynaktan ≤ 1 paket / 5 dk) — spam ve tıkanma koruması |
| **AF-S4** | Tüm NOKTA'lar SOS paketlerini **kalıcı** saklayacak; süre sınırı olmayacak |
| **AF-S5** | Yapı geriye dönük uyumlu olacak: özelliği desteklemeyen standart Meshtastic node'ları paketi **taşıyabilecek** (anlamasa bile) |

> **AF-S5 kritiktir.** Paketimizi standart node'lar taşıyabilmezse, TA Mesh ağının üzerine binemeyiz ve node yoğunluğu avantajını kaybederiz. Bu, yalnızca standart `Data` paketi içinde özel `portnum` kullanarak sağlanır — yeni bir protokol **icat edilmeyecektir.**

### 4.6 ④ AfetMesh PANO — Koordinasyon Arayüzü

Gereksinim Analizi SW-15/SW-16'nın somutlaştırılmış hali. İnternet döndüğünde veya bir dizüstü bilgisayara bağlı node üzerinden çalışan basit web panosu:

| # | Gereksinim |
|---|---|
| **AF-D1** | SOS mesajları **triage sırasına** göre listelenecek (enkaz altında > yaralı > yardım) |
| **AF-D2** | Her kayıt harita üzerinde konumuyla gösterilecek (çevrimdışı harita döşemeleri gömülü) |
| **AF-D3** | Kayıtlar "ulaşıldı / ulaşılmadı" olarak işaretlenebilecek |
| **AF-D4** | Liste CSV olarak dışa aktarılabilecek (AFAD/AKUT'a teslim için) |
| **AF-D5** | Pano internetsiz, tek bir dizüstü bilgisayarda çalışabilecek |

---

## 5. Farklılaşma Tablosu — Sonuç

| Yetenek | Meshtastic | MeshCore | MeshGrid | Ulaş | **AfetMesh (v2)** |
|---|:---:|:---:|:---:|:---:|:---:|
| Uygulama kurulumu gerekmez | ✗ | ✗ | ✗ | ✗ | **✓** |
| Aynı anda çok kullanıcı (kamu node) | ✗ | ✗ | ✗ | ? | **✓** |
| Ön hazırlık gerekmez | ✗ | ✗ | ✗ | ✗ | **✓** |
| Mesaj biriktirme (store & forward) | Kısmi | ✓ | ? | ? | **✓** |
| SOS önceliklendirme | ✗ | ✗ | ? | ? | **✓** |
| Yapısal triage verisi | ✗ | ✗ | ✗ | ? | **✓** |
| Mevcut Meshtastic ağıyla uyumlu | ✓ | ✗ | ✗ | ✗ | **✓** |
| Açık kaynak / ücretsiz | ✓ | ✓ | ✗ | ? | **✓** |
| Altyapı çökse de çalışır | ✓ | Kısmi | ✓ | ✓ | **✓** |

Bu tabloda **ilk üç satır** projenin varlık gerekçesidir; hiçbir rakip bunları sağlamıyor.

---

## 6. Mevcut Raporlarda Değişmesi Gereken Maddeler

Bu rapor, önceki iki raporu geçersiz kılmaz; aşağıdaki maddelerde **düzeltir**.

### 6.1 Gereksinim Analizi Raporu

| Madde | Mevcut hali | Yeni hali | Gerekçe |
|---|---|---|---|
| **Bölüm 3** | Cihazsız kullanıcı **BLE** ile bağlanır | Cihazsız kullanıcı **WiFi captive portal** ile bağlanır; BLE yalnızca kişisel node sahipleri için | E-1, E-2 |
| **SW-6** | "SOS önceliklendirmesi yapılandırılmalı" | "SOS önceliklendirmesi **geliştirilecek**" (AF-S1…S5) | E-3: upstream'de yok |
| **SW-8** | "Resmî Meshtastic uygulaması kullanılacak, özel uygulama kapsam dışı" | Geçerli — ancak **portal arayüzü** kapsam içine alındı. Özel *mobil uygulama* hâlâ kapsam dışı | İ-1 |
| **SW-11** | "En yakın node'a bağlan sihirbazı" | Gereksiz — captive portal bunu ortadan kaldırıyor | E-2 |
| **SW-13** | "Ayarlar optimize edilmeli" | "**Rol disiplini** uygulanacak: CEP = CLIENT_MUTE, NOKTA = ROUTER" | E-5 |
| **NFR-9** | Bölge `EU_868` sabit | **Karar askıya alındı** — TA Mesh ağının fiilî bandı teyit edilecek | Rekabet analizi 5.2 |
| **Bölüm 6.5** | Kamu node'u, temel node'un varyantı | Kamu node'u **ayrı cihaz sınıfı** (AF-N1…N9) | D-1 |

### 6.2 Donanım Gereksinim Raporu

| Madde | Mevcut hali | Yeni hali | Gerekçe |
|---|---|---|---|
| **K-1** | Tek PCB, iki varyant: Temel + Kamu | Tek PCB, iki varyant: **Temel + Saha ekibi**. Kamu node'u PCB kapsamı dışına çıktı | D-1 |
| **K-2** | nRF52840 (tüm cihazlar için) | nRF52840 **yalnızca CEP için**; NOKTA **ESP32-S3 + PSRAM** | E-1, E-4 |
| **Bölüm 5.2** | Güç sistemi 18650 tabanlı | CEP için geçerli. NOKTA için **20 W panel + 100 Wh LiFePO4** ayrı bütçe | AF-N7 |
| **Bölüm 8 (BOM)** | Varyant A/B maliyetleri | Geçerli. NOKTA için **ayrı BOM** gerekli (tahmin: $90–130, solar dahil) | D-1 |
| **Bölüm 9 (Test)** | Mevcut test listesi | **T-1…T-4 eklenecek** (aşağıda) | — |

> **Not:** Bu tablodaki değişiklikler bu raporda önerilmiştir; ilgili dosyalara henüz işlenmemiştir. Onay sonrası uygulanmalıdır.

---

## 7. Yeni Doğrulama Testleri

Mimari kararların dayandığı varsayımların **sahada doğrulanması** gerekir.

| # | Test | Başarı kriteri | Öncelik |
|---|---|---|---|
| **T-1** | İki telefonla aynı node'a eşzamanlı BLE bağlantısı | E-1'in geçerliliği kesinleşir. **Bu test mimariyi doğrular veya çürütür — ilk yapılacak testtir** | ⚠️ En yüksek |
| **T-2** | ESP32-S3'te WiFi AP + LoRa eşzamanlı çalışma | 8 istemci bağlıyken LoRa mesaj gidiş-dönüşü başarılı; radyo girişimi ölçülür | Yüksek |
| **T-3** | NOKTA güç bütçesi ölçümü | Gerçek tüketim ≤ 12 Wh/gün; solar bütçe doğrulanır | Yüksek |
| **T-4** | Yoğunluk testi: 1 NOKTA + 20 CEP + 8 telefon aynı anda | SOS mesajı gecikmesi < 60 sn; paket kaybı < %10 | Yüksek |
| **T-5** | Uyumluluk testi: Standart Meshtastic node'u SOS paketimizi taşıyor mu? | AF-S5 doğrulanır | Yüksek |
| **T-6** | Kullanılabilirlik: 10 teknik olmayan gönüllü, yönlendirme almadan portal üzerinden mesaj gönderir | ≥ 8/10 başarı, ortalama < 90 sn | Orta |

> **T-1 ve T-2 tamamlanmadan PCB tasarımına başlanmamalıdır.** İkisi de mevcut, hazır kartlarla (Heltec V3 + herhangi iki telefon) bir hafta içinde yapılabilir ve maliyeti sıfıra yakındır.

---

## 8. Güncellenmiş Yol Haritası

| Aşama | İçerik | Süre | Çıktı |
|---|---|---|---|
| **0 — Varsayım doğrulama** | T-1, T-2, T-5 testleri. Hazır kartlarla, PCB yok | 2 hafta | Mimarinin teyidi veya revizyonu |
| **1 — MVP: NOKTA + PORTAL** | Hazır ESP32-S3 kart üzerinde captive portal + S&F. **Projenin farklılaşan kısmı burası, önce bu yapılmalı** | 6–8 hafta | Çalışan tek kamu node'u |
| **2 — SOS paketi + önceliklendirme** | AF-S1…S5 firmware modülü | 4 hafta | Upstream'e katkı adayı |
| **3 — CEP prototipi** | Donanım Raporu Bölüm 11 adımları | 8–12 hafta | 5 adet PCB |
| **4 — PANO** | Koordinasyon arayüzü | 3 hafta | Çevrimdışı web panosu |
| **5 — Pilot** | Bir mahalle: 3 NOKTA + 20 CEP + saha tatbikatı | 4 hafta | Ölçülmüş saha verisi |

> **Sıralamadaki en önemli değişiklik:** Önceki planda donanım (PCB) ilk sıradaydı. Yeni planda **portal ve kamu node'u öne alındı.** Gerekçe: projenin farklılaşması orada; ayrıca hazır kartlarla yapılabildiği için çok daha hızlı ve ucuz doğrulanır. PCB, farklılaşma kanıtlandıktan sonra anlamlıdır.

---

## 9. Yeni Riskler

| Risk | Etki | Azaltma |
|---|---|---|
| Captive portal modülü Meshtastic upstream'inden sapma yaratır (NFR-10 ihlali) | Orta | Mesh çekirdeğine dokunulmayacak; portal **ayrı modül** olarak yazılacak, düzenli rebase |
| WiFi AP güç bütçesini aşar | Yüksek | İkili mod (Bölüm 4.3); T-3 ile erken ölçüm |
| ESP32'de WiFi ve LoRa'nın eşzamanlı çalışması radyo girişimi yaratır | Orta | T-2; gerekirse WiFi kanalı ve TX zamanlaması ayarlanır |
| SOS paketi standart node'lardan geçmez | Yüksek | T-5; standart `portnum` kullanımı zorunlu (AF-S5) |
| Kamu node'unun kurulacağı yer için izin alınamaz (çatı, cami, okul) | Orta | Pilot öncesi muhtarlık/kurum görüşmesi; taşınabilir direk alternatifi |
| Şifresiz açık WiFi kötüye kullanılır (spam, taciz) | Orta | AF-S3 hız sınırı; portal yalnızca mesaj gönderir, genel internet erişimi vermez |
| SOS önceliklendirmesi kötüye kullanılır | Orta | AF-S3; PANO'da tekrar eden kaynaklar işaretlenir |

---

## 10. Sonuç

Sektör araştırması, projenin başlangıçtaki varsayımlarından **ikisinin geçersiz** olduğunu gösterdi (BLE ile çoklu kullanıcı, nRF52840 üzerinde mesaj biriktirme). Bu, kötü bir haber değil — çünkü aynı araştırma, bu iki kısıtın **hiçbir rakip tarafından da çözülmediğini** ortaya koydu.

Yani projenin karşılaştığı duvar, aslında pazardaki boşluğun kendisidir.

**Yeni konumlandırma:**

> AfetMesh, mevcut Meshtastic ağının üzerine oturan; afetzedenin **hiçbir uygulama kurmadan, hiçbir ön hazırlık yapmadan**, yalnızca telefonunun WiFi'ını açarak yardım çağrısı gönderebildiği **halk erişim katmanıdır.**

Donanım artık projenin özü değil, taşıyıcısıdır. Özü, **NOKTA + PORTAL + öncelikli SOS** üçlüsüdür ve bu üçlü hazır kartlarla, düşük maliyetle, birkaç ay içinde kanıtlanabilir.

---

## Kaynaklar

**Meshtastic teknik kısıtları**
- [Meshtastic Firmware Issue #526 — Only one PhoneAPI connection allowed at a time](https://github.com/meshtastic/Meshtastic-device/issues/526)
- [Meshtastic Discourse — Multiple phones connected to 1 T-Beam](https://meshtastic.discourse.group/t/multiple-phones-connected-to-1-t-beam/4586)
- [Meshtastic Docs — Store & Forward Module Settings](https://meshtastic.org/docs/configuration/module/store-and-forward-module/)
- [Meshtastic Firmware Issue #7980 — Emergency Mode for Critical Packets](https://github.com/meshtastic/firmware/issues/7980)
- [Meshtastic Blog — Why Meshtastic Uses Managed Flood Routing](https://meshtastic.org/blog/why-meshtastic-uses-managed-flood-routing/)
- [Meshtastic Blog — Is LongFast Holding Your Mesh Back?](https://meshtastic.org/blog/why-your-mesh-should-switch-from-longfast/)
- [Meshtastic Blog — 2.6 Preview: MUI and Next-Hop Routing](https://meshtastic.org/blog/meshtastic-2-6-preview/)
- [Meshtastic Docs — Client API (Serial/TCP/BLE)](https://meshtastic.org/docs/development/device/client-api/)

**Rakip platform karşılaştırmaları**
- [MeshCore vs Meshtastic — Austin Mesh](https://www.austinmesh.org/about/meshcore-vs-meshtastic/)
- [Meshtastic vs MeshCore (2026) — D-Central](https://d-central.tech/meshtastic-vs-meshcore/)
- [MeshCore vs Meshtastic: Complete Comparison Guide (2026) — nodakmesh.org](https://nodakmesh.org/protocols)
- [Webrazzi — MeshGrid](https://webrazzi.com/2026/06/02/altyapilarin-tamamen-coktugu-zorlu-saha-kosullari-icin-gelistirilen-iletisim-cozumu-meshgrid/)
- [Meshtastic Türkiye Topluluğu (TA Mesh)](https://tamesh.org/)

**Saha deneyimi**
- [Hackaday — Meshtastic For The Greater Good](https://hackaday.com/2023/06/26/meshtastic-for-the-greater-good/)

---

> **Metodoloji notu:** Bu rapor açık kaynak dokümantasyon, firmware deposu kayıtları ve topluluk raporlarına dayanmaktadır. **E-1 (tek BLE istemcisi) laboratuvarda doğrulanmamıştır** ve T-1 testi ile teyit edilmelidir. Güç bütçesi rakamları veri sayfası tipik değerlerine dayalı tahminlerdir; T-3 ile ölçülmelidir. Bu rapor bir hukuki veya yatırım görüşü değildir.
