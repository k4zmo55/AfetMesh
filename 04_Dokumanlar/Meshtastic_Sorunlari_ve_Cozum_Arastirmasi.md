# AfetMesh — Meshtastic'in Çözülmemiş Sorunları, Çatı Röle Deneyimleri ve Merkeze İletim Çözümleri

> ## 📎 BELGE STATÜSÜ: ARAŞTIRMA GİRDİSİ (24 Eylül 2026)
>
> **Soru:** Aynı bölgede 50–100 cihaz aynı anda mesajlaşmak istediğinde Meshtastic nerede tıkanıyor? Bina çatılarına ve direklere konacak sabit düğümler bu sorunları nasıl çözer, cep cihazı (CEP) buna nasıl katkı verir? Acil durum mesajları **ana merkeze** güvenilir biçimde nasıl ulaştırılır?
>
> **Bu belgenin çıktıları:**
> - **15 sorunluk Meshtastic sorun envanteri** (Bölüm 3). Her sorunun kanıtı ve upstream'deki güncel durumu verilir.
> - **Kapasite hesabı**: mevcut düz mesh ile önerilen çatı mimarisi karşılaştırılır (Bölüm 4).
> - **Çözüm eşlemesi**: her sorun için ÇATI / CEP / MERKEZ katmanında ne yapılacağı (Bölüm 5).
> - **Merkeze iletim** için üç yollu teslim ve emanet zinciri (custody) tasarımı (Bölüm 6).
> - **Sahadan dersler**: forum, YouTube ve saha raporlarından çıkarılan 24 kural (Bölüm 7).
>
> Donanım karşılığı: **[`02_Donanim/CATI_Node_Donanim_Gereksinim_Raporu.md`](../02_Donanim/CATI_Node_Donanim_Gereksinim_Raporu.md)**. Bağlayıcı gereksinimler: **SGB v2.5** (Bölüm 7b ve 9).
>
> Kaynak kodları [`Literatur_ve_Topluluk_Arastirmasi.md`](Literatur_ve_Topluluk_Arastirmasi.md) numaralandırmasını sürdürür. `L-31…L-35` yeni akademik yayınlardır, `T-27…T-60` yeni topluluk ve doküman kaynaklarıdır. Tam liste Bölüm 9'da ve [`Kaynakca.md`](Kaynakca.md) içindedir.

---

## 1. Yöntem

| Adım | Ne yapıldı |
|---|---|
| Akademik tarama | arXiv, MDPI, ACM, IEEE, Springer, PMC. Terimler: *Meshtastic evaluation / scalability*, *LoRa mesh multi-hop survey*, *LoRa urban path loss*, *LoRa multi-hop earthquake*, *hierarchical LoRa BLE emergency* |
| Resmî proje kaynakları | meshtastic.org blog + dokümantasyon. GitHub `meshtastic/firmware` üzerinde issue, discussion ve PR'lar (Eylül 2026 durumu dahil) |
| Topluluk | Topluluk mesh grupları (NodakMesh, MeshAtlas, Austin Mesh, NC Mesh, 2bn.de), radyo amatörü blogları (NI3N, SQ5BPF, filipnet), Hackaday ve yorumları, Printables, TA Mesh |
| Video | YouTube: kule röle istasyonu, çatı solar node kurulumu, Am Mellensee belediyesi acil iletişim çantası tanıtımı |
| Saha raporları | Berlin elektrik kesintisi (Eyl 2025), Hurricane Helene (2024), Am Mellensee belediye tatbikatı (27 Eyl 2025), DEF CON 32/33, Burning Man |
| Sınırlılık | **Reddit ve LinkedIn içeriğine doğrudan erişilemedi.** Araç erişimi engelli, LinkedIn oturum istiyor. r/meshtastic'te paylaşılan projeler, bağlantı verdikleri blog, GitHub ve Printables sayfalarından incelendi. Meshtastic Discourse forumu tarama sırasında erişilemezdi. Bazı akademik kaynaklarda yalnızca özete ulaşıldı ("Özet" ile işaretli). Topluluk kaynaklarındaki sayılar **kaynak sahibinin beyanıdır**, bağımsız doğrulanmamıştır |

---

## 2. Önce Temel Gerçek: Kanal Tek, Hava Süresi Kıttır

Aynı frekans ve preset'teki bütün Meshtastic düğümleri **tek bir ortak kanalı** paylaşır. Aynı anda yalnızca bir düğüm konuşabilir [L-3][L-31]. Sistemin kapasitesini üç şey belirler:

1. **Bir paketin havada kalma süresi** (preset'e bağlı)
2. **Paketin kaç kez yeniden yayınlandığı** (yönlendirme yöntemi, rol dağılımı, hop limiti)
3. **Yasal çalışma süresi sınırı**: EU_868 alt bandında (869,4–869,65 MHz) her cihaz için **%10**, kayan 1 saatlik pencerede [T-37][T-54]

Hesaplanan hava süreleri (Semtech formülü, 16 sembol preamble, CR 4/5, 250 kHz):

| Preset | 40 bayt | **79 bayt (SOS paketi + başlık)** | 120 bayt | Veri hızı [T-32] | %10 çalışma süresiyle bir düğümün **dakikada gönderebileceği en fazla paket** (79 B) |
|---|---|---|---|---|---|
| LongFast (SF11) — varsayılan, TA Mesh | 559 ms | **846 ms** | 1133 ms | 1,07 kbps | **7** |
| MediumSlow (SF10) | 300 ms | 443 ms | 628 ms | 1,95 kbps | 14 |
| MediumFast (SF9) | 160 ms | 242 ms | 334 ms | 3,52 kbps | 25 |
| ShortFast (SF7) | 45 ms | 76 ms | 104 ms | 10,94 kbps | 79 |

> **Bu tablonun en kritik satırı son sütundur.** LongFast'te bir çatı röle düğümü yasal olarak **dakikada yalnızca ~7 paket** yayınlayabilir. Bina içinde 25 kişi dakikada birer mesaj atarsa röle bunların ancak üçte birini iletebilir. Kalanlar kuyrukta bekler ya da düşer. **"Çatıya röle koyalım" fikrinin tek başına yetmemesinin sayısal nedeni budur.** Çözüm için trafik azaltılmalı (Bölüm 5), preset hızlandırılmalı ya da trafik ikinci bir kanala (omurgaya) taşınmalıdır (Bölüm 6).

---

## 3. Meshtastic Sorun Envanteri (Eylül 2026)

**Durum:** 🔴 Açık (çözüm yok) · 🟠 Kısmen (hafifletme var, kök sorun sürüyor) · 🟢 Çözüldü (upstream'de).

| # | Sorun | Kanıt | Upstream durumu | Durum |
|---|---|---|---|---|
| **P-1** | **Yayın fırtınası / ölçeklenme.** Yönetimli taşkın yönlendirmede her mesajı birçok düğüm yeniden yayınlar. Varsayılan ayarlarla pratik sınır **50–80 düğüm**, gerçek kurulumlarda **~%80 teslim oranı** | [L-22] (Burning Man 2024 dahil), [T-27] ("100+ düğümde **uygun trafik kontrolüyle** çalışır"), [T-42] | Tıkanıklığa duyarlı hop ölçekleme (PR #11826) **15 Eyl 2026'da develop dalına birleşti** [T-36]. Trafik yönetimi modülü kullanımda [T-35]. Hop ağırlıklı gecikme önerisi yanıtsız [T-34] | 🟠 |
| **P-2** | **Düşük veri hızı ve tek kanal.** LongFast 1,07 kbps. Preset hızlandıkça menzil düşer. **Farklı preset'teki düğümler birbirini hiç duymaz** | [T-32][T-57][L-21] | Tasarım gereği. Wellington ağı %65 kanal doluluğundan ShortFast'e geçerek kurtuldu [T-32] | 🔴 (fizik) |
| **P-3** | **%10 çalışma süresi sınırı** röleleri kilitler. Sınır aşılınca düğüm susar. "Her biri %10'un altındaki 10 düğüm kanalı neredeyse sürekli doldurabilir" | [T-54][T-37] | Hesaplama doğruluğu ve ACK rezervi PR #11927'de **açık, birleşmedi** (21 Eyl 2026) [T-37] | 🟠 |
| **P-4** | **Acil durum önceliği yok.** SOS normal kuyruğa girer. Kuyruk dolunca **en eski paket atılır**, bu da genelde SOS'un kendisidir | [T-38], kuyruk analizi [T-59] | Acil durum modu önerisi #7980 **kapatıldı**. QoS önerisi #8262 tartışmada [T-38] | 🔴 |
| **P-5** | **Yanlış rol dağılımı.** Birbirini gören çok sayıda ROUTER çarpışma üretir. Bir kent ağında 120 düğümden **10'dan azı** varsayılan dışı rolde | [T-29][T-42][T-46] | Dokümantasyon ile çözülmeye çalışılıyor. ROUTER_LATE ve CLIENT_BASE rolleri eklendi [T-29][T-40] | 🟠 (insan) |
| **P-6** | **NodeDB sınırı.** nRF52'de ~80, ESP32'de ~100 kayıt. Büyük ağda DM başarısızlığı ve eski kaydın yerine **sahte NodeInfo** kabul edilmesi | [T-33], [T-19] | Favoriler DB'den atılmaz, istemci tarafında ek kontroller var [T-33] | 🟠 |
| **P-7** | **Store & Forward sınırlı.** Yalnızca PSRAM'li ESP32'de çalışır, LongFast genel kanalında çalışmaz, istemcinin neyi kaçırdığını bilmez (yinelenen mesaj) | [T-41], SGB Ç-14 | Değişmedi. Kanal karışması hatası (#8877) raporlandı | 🔴 |
| **P-8** | **Uçtan uca teslim teyidi yok (yayınlarda).** Gönderen yalnızca ilk sıçramadaki yeniden yayını duyar (örtük ACK). Mesajın **merkeze ulaşıp ulaşmadığını bilemez** ve tekrar gönderir | [T-28] | DM'lerde gerçek ACK var. Yayın ve kanal mesajlarında yok | 🔴 |
| **P-9** | **Düğüm başına tek radyo.** Aynı kutuda erişim ve omurga için iki frekans ya da iki preset kullanılamaz | [T-39] | Kasım 2025'te iki SPI modülle aynı frekansta prototip. Farklı frekans için çekirdek değişikliği gerekiyor | 🔴 |
| **P-10** | **Asimetrik bağlantı.** Yüksek güçlü ya da yüksek kazançlı çatı düğümü el cihazına ulaşır, el cihazı ise çatıya geri ulaşamaz. PA'sı olup LNA'sı olmayan tasarımlar alıcıyı sağırlaştırır | [T-49] | Donanımla çözülür (PA + LNA + SAW), yazılım çözümü yok | 🟠 (donanım) |
| **P-11** | **Kent RF gürültüsü.** Çatıdaki LTE 800 (Band 20) ve GSM 900 vericileri LoRa alıcısını doyurur. Belirtisi: RSSI normal, SNR dalgalı, çok sayıda bozuk paket | [T-43] | Donanımla çözülür (kavite ya da SAW filtre) | 🟢 (donanım) |
| **P-12** | **İnsan faktörü.** Helene'de yeni kullanıcılar 30 saniyede bir durum mesajı atarak ağı kilitledi. Berlin'de firmware sürüm uyumsuzluğu yaşandı ve kriz öncesi düğüm yoğunluğu düşüktü | [T-52][T-51] | Yazılımda hız sınırı yok. Trafik yönetimi modülü düğüm başına hız sınırlayabiliyor [T-35] | 🔴 |
| **P-13** | **Yetkiliye köprü yok.** Meshtastic eşler arası çalışır. MQTT köprüsü internet ister, internet de afette gider. Genel MQTT gizlilik riski taşır | [T-51][T-52], MQTT doc | Tasarım gereği | 🔴 |
| **P-14** | **Açık PSK'lı kanallarda sahtecilik.** Bilinen anahtarlı kanalda sahte düğüm ve sahte mesaj üretilebilir. SGB SOS-6'nın açık PSK'sı bu riski taşır | [T-33] | PKC (2.5+) DM'leri korur, kanal yayınlarını korumaz | 🟠 |
| **P-15** | **Röle enerji ve saha arızaları.** Kışın güneş yetersiz kalır. 0 °C altında şarj pili bozar. Kutu içi yaz sıcaklığı 46 °C'yi geçer. 2. yılda nem girer. BMS kilitlenir | [T-44][T-47][T-48] | Donanım ve montaj konusu | 🟢 (mühendislik) |

> **Özet:** 15 sorunun **6'sı protokolden kaynaklanıyor ve açık** (P-2, P-4, P-7, P-8, P-9, P-13). **6'sı yazılım ayarı veya insan davranışından kaynaklanıyor**, upstream'de kısmen hafifletilmiş ya da operasyonel kuralla hafifletilebilir (P-1, P-3, P-5, P-6, P-12, P-14). **3'ü donanım ve montajla çözülebilir** (P-10, P-11, P-15). AfetMesh'in özgün katkısı açık sorunlara odaklanmalıdır: **P-4 önceliklendirme, P-8 teslim teyidi, P-9 çift radyo, P-13 merkeze köprü.** SGB SYS-4 gereği bunlar çekirdeğe dokunmadan **ayrı modül** olarak yazılır.

---

## 4. Kapasite: Düz Mesh ile Çatı Mimarisi

Hesap varsayımları: 79 baytlık paket. Düz mesh'te her mesaj ortalama **4 kez** havaya çıkar (kaynak + 3 yeniden yayın). Çatı mimarisinde (Bölüm 6) her mesaj erişim kanalında **2 kez** çıkar (CEP → ÇATI + ÇATI'nin yerel yayını) ve gerisi omurgadan taşınır. Hücre başına 25 cihaz vardır.

| Preset | **A.** Düz mesh, 100 cihaz, dakikada 1 mesaj | **B.** Düz mesh, 100 cihaz, **SOS disiplini** (5 dk'da 1, SGB SOS-3) | **C.** Çatı mimarisi, hücre başına 25 cihaz, dakikada 1 mesaj | **D.** Deprem anı patlaması: 100 cihaz aynı dakikada |
|---|---|---|---|---|
| LongFast | **%564** ❌ | **%113** ❌ | %71 ❌ | %564 ❌ |
| MediumSlow | %296 ❌ | %59 ⚠️ | %37 ⚠️ | %296 ❌ |
| MediumFast | %162 ❌ | **%32** ⚠️ | **%20** ✅ | %162 ❌ |
| ShortFast | %51 ⚠️ | %10 ✅ | %6 ✅ | %51 ⚠️ |

Eşikler: ≤ %25 sağlıklı ✅ · %25–60 tıkanık ⚠️ · > %60 çöker ❌ (Meshtastic: kanal doluluğu %25'in altında tutulmalı [T-29]. Hop ölçekleme %20'de devreye girer, %40'ta bütün trafik bekletilir [T-36]).

**Çıkarımlar:**
1. **Hiçbir tek önlem yetmez.** LongFast'te SOS disiplini bile düz mesh'i kurtarmaz (B = %113).
2. **Üç önlem birlikte gereklidir:** (a) trafik disiplini (SOS-only, hız sınırı, telemetri kapatma), (b) çok sıçramanın erişim kanalından alınması (çatı + omurga), (c) mümkünse daha hızlı preset.
3. **D sütunu (patlama) hiçbir preset'te taşınamaz.** Bu yüzden CEP tarafında **rastgele gecikmeli gönderim** (jitter) ve **teslim teyidi** zorunludur. Teyit gelmezse kullanıcı tekrar basar ve patlama katlanarak büyür (Helene dersi [T-52]).
4. **Preset kararı (SGB A-2) bir ödünleşimdir.** Hızlı preset, TA Mesh ile birlikte çalışabilirliği bozar (SYS-3, farklı preset birbirini duymaz). Önerilen uzlaşma: **erişim kanalı TA Mesh ile ortak kalır, çok sıçramalı taşıma ayrı omurgaya alınır** (Bölüm 6.2). Erişim kanalında her paket en fazla 1–2 sıçrama yapar ve bu, C sütunu koşullarını sağlar.

---

## 5. Çözüm Eşlemesi — Kim Neyi Çözer?

### 5.1 Çatı düğümleri (ÇATI) neyi çözer?

| Sorun | ÇATI'nin çözümü | Mekanizma / kaynak |
|---|---|---|
| P-1, P-5 | **Rol hiyerarşisi:** yüksek noktada az sayıda `ROUTER` (ÇATI-K). Bina çatılarında `ROUTER_LATE` ya da `CLIENT_BASE` (ÇATI-B). Birbirini gören iki ROUTER yok | ROUTER_LATE boşluk doldurur, başkası yayınlamadıysa geç pencerede yayınlar [T-29]. CLIENT_BASE favori düğümleri için öncelikli röledir [T-30][T-40] |
| P-1 | **Hop sınırı:** erişim kanalında 3. ÇATI'lar hop'u düşürmez (hop koruma) [T-35] | Yerleşik: altyapı rolleri tıkanıklıkta bile 3 hop tabanını korur [T-36] |
| P-2, P-3, P-9 | **ÇATI-O omurga radyosu:** çok sıçramalı taşıma 868 MHz erişim kanalından alınır | Bölüm 6.2. Akademik karşılığı: kümeleme + omurga [L-22], "mesh of stars" [L-33] |
| P-4 | **SOS öncelik kuyruğu** (SGB SOS-2) + ÇATI'da **SOS için ayrılmış kuyruk yuvası** | AfetMesh modülü (upstream'de yok) |
| P-7, P-8, P-13 | **Emanet zinciri:** ÇATI, SOS kaydını MERKEZ'den teyit gelene kadar kalıcı hafızada saklar ve periyodik olarak yeniden iletir | Bölüm 6.3. DTN "custody transfer" yaklaşımı |
| P-10 | **Alıcıyı güçlendir, vericiyi yasal sınırda tut:** SAW filtre + LNA, TX ≤ yasal ERP. **1 W PA kullanılmaz** | [T-49]. TR 868: 500 mW ERP. TR 433: 10 mW ERP (SGB REG-2) |
| P-11 | **Bant geçiren filtre** (kavite ya da SAW, ekleme kaybı ≤ 1 dB, 833 MHz'te ≥ 40 dB bastırma) | [T-43]. Türkiye'de konut çatılarında operatör baz istasyonu yaygındır |
| P-12 | **Hız sınırlama** (düğüm başına), telemetri/konum dedup, NodeInfo'yu yerelden yanıtlama | Trafik yönetimi modülü: bir ağda %40–50 → %20–30 [T-35] |
| P-15 | LiFePO4, 0 °C altı şarj kesme, kış güneşine göre boyutlandırma, açık renk IP67 kutu, nem ventili, BMS kendiliğinden toparlanma | [T-44][T-47][T-48]. Ayrıntı: ÇATI Donanım Raporu |
| Deprem | **Sarsıntı algılayınca afet profiline geçiş** (NOK-8 ile aynı). Telemetri durur, SOS önceliği ve emanet açılır. Her alan **en az 2 ÇATI** tarafından kapsanır (bina hasarı yedekliliği) | SGB NOK-8, CATI gereksinimleri |

### 5.2 Cep cihazı (CEP) neyi çözer?

"Cep cihazları da bu problemleri çözsün" talebinin karşılığı: **ağı az yoran, akıllı davranan istemci.**

| Sorun | CEP'in çözümü | Neden etkili |
|---|---|---|
| P-1, P-5 | Varsayılan `CLIENT_MUTE` (SGB CEP-2). Hiçbir paketi tekrarlamaz | Toplanma alanında yüzlerce CEP'in yeniden yayınını sıfırlar [T-30] |
| P-1, P-12 | **Afet profili:** konum, telemetri ve NodeInfo yayınları kapanır. Konum yalnızca SOS paketinin içinde gider | Kentsel ağlarda doluluğun ana kaynağı bu arka plan yayınlarıdır (%65 tepe [T-32]). Meshtastic ölçekleme formülü bunu yalnızca 40+ düğümde ve yavaşça yapar [T-28] |
| P-4, P-12 | **Tek tuş = tek SOS.** Uygulama katmanında hız sınırı (≤ 1/5 dk, SGB SOS-3) | Helene'deki 30 saniyelik spam senaryosu donanımla engellenir [T-52] |
| P-8, P-12 | **Teslim teyidi göstergesi:** "Gönderildi → Çatıya ulaştı → MERKEZ aldı" (3 aşama, LED/e-paper) | Kullanıcı teyidi görünce tekrar basmaz. Gereksiz tekrarı kaynağında keser |
| D (patlama) | **Rastgele ilk gecikme** (0–30 s jitter) + teyit gelmezse **üstel geri çekilme** (2, 4, 8 dk) | Sarsıntı anında herkesin aynı saniyede basmasının çarpışma patlamasını dağıtır |
| P-1, P-3 | **Kanal doluluğunu okuyup SOS dışı trafiği erteleme:** ChUtil > %25 ise sohbet mesajları kuyrukta bekler, SOS beklemez | Firmware'in %25 "kibar trafik bekletme" eşiğiyle uyumlu [T-36] |
| P-10 | Harici ½λ whip anten (SGB HW-RF-5). Kullanıcı yönlendirmesi: "pencere kenarına / balkona" | Pencereden balkona taşımak bir kullanıcıda görülen düğüm sayısını "ayda bir"den "~100"e çıkardı [T-42] |
| P-6 | Kendi binasının ÇATI'sını ve MERKEZ'i **favori** olarak tutar | Favoriler NodeDB'den atılmaz, sahteciliğe karşı korunur [T-33] |
| P-14 | SOS paketini **cihaz anahtarıyla imzalama** (PKC, isteğe bağlı alan) | PANO imzasız SOS'u "doğrulanmamış" diye işaretler, yine de gösterir |

### 5.3 Protokol düzeyinde ne yapılmayacak

- **Yeni mesh protokolü yazılmayacak** (SGB Bölüm 3.2). MeshCore'un "yalnızca röleler iletir + yol öğrenme" modeli [T-53] ilgi çekicidir. Meshtastic'te bunun karşılığı zaten var: CLIENT_MUTE + ROUTER hiyerarşisi + 2.6 next-hop yönlendirme [T-31]. Protokol değiştirmek TA Mesh ile uyumu bozar.
- **Hop limiti artırılmayacak.** Topluluk tavsiyesi nettir: "3 yeterlidir" [T-30][T-42]. Am Mellensee tatbikatında hop artırımı gerekti [T-50], ancak bu seyrek kırsal ağ içindi. Kentte menzil açığı hop'la değil ÇATI ile kapatılır.

---

## 6. Ana Merkeze İletim — Üç Yollu Teslim ve Emanet Zinciri

### 6.1 Sorun

Meshtastic'te "merkez" kavramı yoktur. SOS bir yayındır, kimin aldığı belli değildir (P-8). İnternet köprüsü (MQTT) afette çöker (P-13). Berlin'de ağ vatandaşlar arasında çalıştı, **resmî birimlere nasıl ulaşıldığı raporlanmadı** [T-51]. Helene'de ağ "karşılıklı yardım aracı" olarak kaldı, AFAD'ın karşılığı olan EOC entegrasyonu tarif edilmedi [T-52]. Belgelenmiş tek kurumsal örnek Am Mellensee'dir: 9 bilgi noktası, çatı ve itfaiye kulesindeki solar ROUTER'lar ve belediye kriz merkezi. Orada da vatandaş cihazı yoktur, mesajı görevli girer [T-50].

### 6.2 Mimari: erişim hücresi + omurga + merkez

```
  [CEP]  [CEP]  [telefon→NOKTA/PORTAL]          ← vatandaş (868 MHz, CLIENT_MUTE)
     \     |      /
      \    |     /        ERİŞİM KANALI: TA Mesh ile ortak preset, 1–2 sıçrama
   ┌───▼───▼────▼───┐
   │  ÇATI-B (bina)  │  ROUTER_LATE / CLIENT_BASE · SOS emanet deposu
   └───────┬────────┘
           │ 868 MHz (aynı kanal, 1 sıçrama)
   ┌───────▼────────┐        OMURGA: 2,4 GHz LoRa (LORA_24) — ayrı kanal,
   │ ÇATI-O (omurga)│◄──────►  868'in %10 sınırından bağımsız, yalnızca
   │ 868 + 2,4 GHz  │         SOS + koordinasyon trafiği taşır
   └───────┬────────┘
           │ omurga, 2–5 sıçrama (çatıdan çatıya görüş hattı)
   ┌───────▼────────────────────────────┐
   │ MERKEZ (belediye kriz merkezi/AFAD) │  ÇATI-O + PANO bilgisayarı + UPS
   │ • SOS alındı teyidini yayınlar     │  • isteğe bağlı: uydu/fiber ile
   │ • PANO triage, AYDES/CSV dışa aktarım│   üst merkeze MQTT (özel broker)
   └────────────────────────────────────┘
```

**Kural (yayın fırtınasını önlemek için):** Omurga, erişim hücrelerine **her şeyi geri basmaz.** Omurgadan erişime yalnızca şunlar iner: (a) hedefi o hücrede son 30 dakikada duyulmuş DM'ler, (b) MERKEZ'in **SOS teyitleri** ve (c) hız sınırlı resmî duyurular. SOS **yalnızca yukarı**, MERKEZ'e doğru akar. Bu kural, projenin "yukarı yönlü" konumlandırmasıyla birebir örtüşür (SGB 1.4).

**Neden 2,4 GHz LoRa?**
- 868 MHz'in diğer alt bantları düşük güç (25 mW) ve %0,1–1 çalışma süresi sınırı taşır [T-54]. Omurga için yer yoktur.
- 433 MHz'te sınır 10 mW ERP'dir (SGB REG-2).
- 2,4 GHz LoRa (SX1280 ya da LR1121) Meshtastic'te `LORA_24` bölgesi olarak dünya genelinde desteklenir. Alt-GHz çalışma süresi kuralları yoktur, 812 kHz ve 1,6 MHz bant genişlikleri kullanılabilir [T-39].
- Menzil daha kısadır, ama çatıdan çatıya görüş hattında bu kabul edilebilir. Fresnel hesabı Bölüm 7.3'te.
- AB ve Türkiye'deki yasal çerçeve (ETSI EN 300 328: EIRP ve ortam kullanım sınırları) **SGB REG-4 kapsamında teyit edilecektir.**

**Tek radyo kısıtı (P-9) nasıl aşılır?** Meshtastic bir firmware'de iki radyoyu desteklemiyor [T-39]. Bu yüzden ÇATI-O, **aynı kutuda iki ayrı Meshtastic düğümü** olarak tasarlanır: erişim için nRF52840 + SX1262, omurga için LR1121 kartı. İkisi UART ile bağlanır ve aralarında **AfetMesh Köprü modülü** çalışır. Modül yalnızca SOS portnum'unu, teyitleri ve beyaz listedeki DM'leri aktarır. Bu yaklaşım çekirdek koda dokunmaz (SYS-4 uyumlu). Ayrıntı: ÇATI Donanım Raporu, Bölüm 7.

### 6.3 Emanet zinciri (custody) ve uçtan uca teyit

```
CEP ──SOS(id=a1b2)──► ÇATI-B ──► ÇATI-O ══omurga══► MERKEZ
 │   ◄─"çatıya ulaştı"─┘  (örtük ACK: ilk sıçrama yeniden yayını)
 │                                               │ PANO kaydı oluştu
 │   ◄══════════ SOS-TEYİT(id=a1b2) ═════════════┘ (DM, next-hop yönlendirme)
 └─ e-paper: "MERKEZ ALDI 14:32" → kullanıcı tekrar göndermez
```

| Adım | Kural |
|---|---|
| 1 | Her SOS'a **8 baytlık benzersiz kimlik** verilir (kaynak düğüm no + sayaç). Yinelenen kopyalar her katmanda elenir |
| 2 | SOS'u ilk alan ÇATI ya da NOKTA, kaydı **kalıcı hafızaya yazar** ve "emanetçi" olur |
| 3 | Emanetçi, MERKEZ teyidi gelene kadar kaydı artan aralıklarla (5, 10, 20, 40 dk) omurgaya yeniden sunar. Teyit gelince kaydı "teslim edildi" diye işaretler, ama silmez (NOK-5) |
| 4 | MERKEZ her yeni SOS için **SOS-TEYİT** paketi üretir. Teyit, next-hop yönlendirmeyle (2.6+) [T-31] kaynağa DM olarak gider, taşkınla yayılmaz |
| 5 | CEP üç aşamalı göstergeyi günceller. Teyit gelmezse üstel geri çekilmeyle yeniden dener (Bölüm 5.2) |

### 6.4 Yedek teslim yolları (çok yollu)

| Yol | Ne zaman çalışır | Nasıl |
|---|---|---|
| **① Mesh + omurga** (birincil) | Her zaman | Bölüm 6.2–6.3 |
| **② Fırsatçı internet** | Herhangi bir NOKTA, ÇATI-O ya da MERKEZ interneti geri aldığında (fiber, sağ kalan baz istasyonu, uydu terminali) | O düğüm **özel bir MQTT broker'ına** (genel broker değil, gizlilik gereği) bekleyen SOS kayıtlarını yükler. MERKEZ teyidi aynı yoldan geri gelir |
| **③ Veri katırı (data mule)** | Mesh bölündüğünde (ada kalmış mahalle) | Saha ekibi CEP+/CEP-T ya da araçtaki ÇATI-O kutusu NOKTA'ların yanından geçerken WiFi/BLE ile SOS deposunu kopyalar ve MERKEZ'e taşır. Kayıt kimliği yinelenmeyi önler |
| **④ Uydu** (isteğe bağlı, kurumsal) | Kriz merkezinin kendisi izole kaldığında | MERKEZ'de Starlink / Iridium ile üst merkeze (il AFAD) CSV/GeoJSON aktarımı (SGB PAN-6) |

---

## 7. Sahadan Dersler — Çatı ve Direk Kurulumları

### 7.1 Forum, YouTube ve saha raporlarından 24 kural

| # | Ders | Kaynak |
|---|---|---|
| D-1 | **"Yükseklik güçten iyidir":** 9–12 m'deki 100 mW'lık düğüm, yerdeki yüksek güçlü düğümü rutin olarak geçer | [T-45][T-57] |
| D-2 | **Az sayıda iyi yerleştirilmiş ROUTER**, çok sayıda ROUTER'dan iyidir. Birbirini gören ROUTER'lar çarpışma üretir | [T-29][T-46] |
| D-3 | Her yüksek düğüm otomatik olarak ROUTER yapılmamalıdır. Normal taşkın yönetimi sürekli açık düğümleri zaten kullanır | [T-46] |
| D-4 | Görüş alanı çok geniş dağ ya da kule düğümleri ROUTER'dan **ROUTER_LATE'e** alınınca alt düğümler doğrudan konuşabildi | [T-29] |
| D-5 | Kanal doluluğu < %25, düğüm yayın süresi < %7–8 tutulmalı | [T-29] |
| D-6 | Hop limiti 3. Artırmak kapasiteyi yer | [T-30][T-42] |
| D-7 | Yoğun ağda LongFast'ten MediumFast/ShortFast'e geçiş %65 tepe doluluğu çözdü (Wellington). Bay Area 150+ düğümü MediumSlow'da | [T-32] |
| D-8 | Çatıda operatör vericisi varsa **kavite ya da SAW filtre** (≤ 1 dB kayıp). Belirti: RSSI iyi, SNR kötü, bozuk paket çok | [T-43] |
| D-9 | **1 W modül alıyorsan LNA'lı olanı al.** Yalnız PA alıcıyı sağırlaştırır, bağlantı asimetrik olur | [T-49] |
| D-10 | Radyoyu antene yakın koy. İnce koaksiyel link bütçesinin büyük kısmını yer (RG-58 ~1 dB/m, LMR-195 ~0,2–0,4 dB/m) | [T-44][T-46] |
| D-11 | Dış mekânda **N tipi** konnektör, UV dayanımlı kablo rakoru, dielektrik gres, kendinden kaynaşan bant | [T-45][T-44] |
| D-12 | **Koaksiyel yıldırım parafudru** + direğin binanın topraklamasına bağlanması. Kablo girişinde damla halkası | [T-44][T-45] |
| D-13 | Kablo rakorları **aşağı bakacak**. Silika jel her yıl değişmeli. Aksi halde IP67 kutu 2. yılda nem alıyor | [T-44] |
| D-14 | Doğrudan güneşte kutu içi **46 °C** ölçüldü (dış hava ~20 °C). Açık renk kutu + havalandırma + panel gölgesi gerekli | [T-48] |
| D-15 | **LiFePO4 0 °C altında şarj edilmez** (lityum kaplama). Li-ion −10 °C altında %25–40 kapasite kaybeder | [T-44] |
| D-16 | Kış için boyutlandır, yıl ortalamasına göre değil: "Yaza göre boyutlanan düğüm Ocak'ta biter" | [T-44] |
| D-17 | Bahçe lambası paneli ESP32 düğümünü taşıyamadı. nRF52 düğüm 6 W panelle gece %85 şarjda kaldı | [T-47][T-48] |
| D-18 | Elektrik geri geldiğinde **kimse çatıya çıkmadan** toparlanmalı (BMS kilitlenmesi, bootloop) | [T-46] |
| D-19 | Kurulumdan önce masada **ısı testi** (heat-soak) yap. Uzaktan kurulacak düğüm önce yerde test edilir | [T-45][T-30] |
| D-20 | Telemetri ve konum aralığı altyapı düğümlerinde ≥ 15–30 dk. Gereksiz sensör kapatılır | [T-45] |
| D-21 | Kule istasyonu: 15 m kulede **2 sektör anten + 2 radyo** ile yönlü röle | [T-56] |
| D-22 | Ağdaki bütün düğümler **aynı firmware ailesinde** olmalı. Berlin'de sürüm uyumsuzluğu iletişimi kesti | [T-51] |
| D-23 | Kriz öncesi topluluk ve düğüm yoğunluğu şart. Kriz günü kurulan ağ geç kalır | [T-51] |
| D-24 | Kurum projesinde çalışan model: çatı ve itfaiye kulesinde solar ROUTER, bilgi noktasında görevli terminali, kriz merkezinde sabit düğüm. En büyük menzil ~7 km, "kısa optimizasyondan sonra kararlı" | [T-50] |

### 7.2 Çok katlı binalar arasında sinyal kaybı — sorunun fiziği

- **Bina içinden dışarı:** Her kat ve duvar ciddi zayıflama ekler. 18 katlı bir binada çatı ağ geçidiyle yapılan ölçümde RSSI ve SNR, kat ve duvar engelleriyle belirgin düştü [arXiv 2505.06375]. 9 katlı betonarmede 868 MHz SF10 önerildi [L-9]. **Sonuç:** CEP'ten komşu binaya doğrudan bağlantı güvenilmezdir. CEP'in **kendi binasının çatısına** dikey olarak ulaşması, sokak kanyonundan geçmekten çok daha olasıdır.
- **Çatıdan çatıya:** Engelsiz görüş hattı ve Fresnel bölgesinin açık olması gerekir. Terminaller alçakta olduğunda mesh topolojilerinde yol kaybı belirgin artar [L-31]. Kent içi yol kaybı log-mesafe modeliyle makul tahmin edilebilir [L-34]. Yükselik artışının etkisi: rölelerin ≥ 10 m yükseltilmesi çok sıçramalı iletimi belirgin iyileştirdi [L-33].
- **Anten kazancı ile kapsama ödünleşimi:** Yüksek kazançlı kolineer anten ufka doğru uzar, ama **dikey hüzmesi daralır**. Çatının hemen altındaki katlar ve komşu sokaklar hüzmenin altında kalabilir. Bina içi erişim için 3–5 dBi, kule için 5–6 dBi uygundur (ayrıntı Donanım Raporu).

### 7.3 Fresnel bölgesi — anten direği ne kadar yüksek olmalı?

Hat ortasında birinci Fresnel yarıçapının **%60'ı** (açık tutulması gereken asgari yarıçap):

| Bağlantı uzunluğu | 433 MHz | **868 MHz** | 2,4 GHz (omurga) |
|---|---|---|---|
| 500 m | 5,6 m | 3,9 m | 2,4 m |
| 1 km | 7,9 m | **5,6 m** | 3,3 m |
| 2 km | 11,2 m | 7,9 m | 4,7 m |
| 3 km | 13,7 m | 9,7 m | 5,8 m |

**Yorum:** 868 MHz'te 1 km'lik çatı-çatı bağlantısında hat ortasındaki engeller (ara binalar), anten hattının **5,6 m altında** kalmalıdır. Bu yüzden ÇATI-K ve ÇATI-O **bölgenin en yüksek binalarına ve kulelere** konur. 2,4 GHz omurga daha küçük Fresnel bölgesi sayesinde çatıdan çatıya görüş hattında avantajlıdır.

---

## 8. Açık Araştırma Soruları (bu belgeden doğan)

| # | Soru | Neden önemli | Nasıl kapanır |
|---|---|---|---|
| AR-7 | 2,4 GHz LoRa'nın (LR1121, 812 kHz) Türkiye kent çatıları arasındaki gerçek menzili | ÇATI-O omurga aralığı | TST-9 saha ölçümü |
| AR-8 | ETSI EN 300 328 / BTK kurallarında 2,4 GHz LoRa için EIRP ve ortam kullanımı sınırı | ÇATI-O yasallığı | REG-4 teyidi |
| AR-9 | Kat başına zayıflama: CEP (bina içi, farklı katlar) → aynı binanın ÇATI-B'si | ÇATI-B'nin kaç kata hizmet ettiği | TST-8 ölçümü (bodrum, zemin, ara kat, üst kat) |
| AR-10 | CLIENT_BASE favori sayısı üst sınırı ve bir binadaki CEP'lerin favori yapılmasının kurulum yükü | ÇATI-B rol seçimi (SGB A-10) | Firmware kaynak incelemesi + deneme |
| AR-11 | PR #11826 (hop ölçekleme) ve #11927 (çalışma süresi) kararlı sürüme ne zaman girecek | Kapasite hesabındaki varsayımlar | Upstream takibi |
| AR-12 | Türkiye'de çatıdaki operatör vericisinin 868 MHz alıcı gürültü tabanına ölçülmüş etkisi | Filtrenin zorunlu mu, isteğe bağlı mı olduğu | Filtreli ve filtresiz gürültü tabanı ölçümü (TST-10) |

---

## 9. Kaynaklar

### 9.1 Akademik (yeni)

| # | Künye | Temel bulgu | Bu belgede |
|---|---|---|---|
| **L-31** | Wong, A. W.-L., Goh, S. L., Hasan, M. K., Fattah, S. — *Multi-Hop and Mesh for LoRa Networks: Recent Advancements, Issues, and Recommended Applications.* **ACM Computing Surveys** 56(6):136, 2024 | 2015–2023 çok sıçramalı LoRa çalışmalarının taraması: enerji, eşzamanlı erişim ve çalışma süresi, yönlendirme, güvenlik. Alçak terminallerde yol kaybı yüksektir | Bölüm 2, 7.2 |
| **L-32** | Suryadevara, N. K., Dutta, A. — *Meshtastic Infrastructure-less Networks for Reliable Data Transmission to Augment IoT Applications.* **WiSATS 2021**, LNICST 410, Springer, 2022 | Kampüste Heltec ile Meshtastic veri iletimi. Adresleme ve tünelleme ile IoT entegrasyonu | Arka plan (L-22'nin kaynağı) |
| **L-33** | Ranasinghe, V. vd. — *Rapid and Resilient LoRa Leap: A Novel Multi-Hop Architecture for Decentralised Earthquake Early Warning Systems.* **Sensors** 24(18):5960, 2024 | "Mesh of stars": uç düğüm + röle. SF8 ile 30 km kentsel alanda 2,4 s'de %100 yayılım, ~3,2 km röle aralığı. **Röle yüksekliği ≥ 10 m performansı belirgin artırdı.** Sahada çok sıçramalı teslim %48–90 | Bölüm 5.1, 7.2 |
| **L-34** | Rademacher, M., Linka, H., Horstmann, T., Henze, M. — *Path Loss in Urban LoRa Networks: A Large-Scale Measurement Study.* **IEEE VTC2021-Fall** (arXiv:2109.07768) | 230 gün, 200 km², 112.000 örnek. Kentte log-mesafe modeli makul, arazi tabanlı modeller avantaj sağlamıyor | Bölüm 7.2 |
| **L-35** | *Performance Evaluation of a Mesh-Topology LoRa Network.* **Sensors** 25(5):1602, 2025 (MDPI) (Özet — yazarlar teyit edilecek) | Meshtastic'in CSMA/CA + taşkın yaklaşımını LoRaWAN'a tamamlayıcı mesh olarak değerlendirir | Arka plan |
| — | *A Comprehensive Data Description for LoRaWAN Path Loss Measurements in an Indoor Office Setting.* arXiv:2505.06375, 2025 | 18 katlı binada çatı ağ geçidi. Kat ve duvar engelleri RSSI/SNR'yi belirgin düşürür | Bölüm 7.2 |

Mevcut kaynaklardan yeniden kullanılanlar: L-3 (LoRa ölçeklenmez), L-9 (çok katlı bina 433/868), L-21 (preset yol kaybı eşikleri), **L-22 (Meshtastic 50–80 düğüm, %80 PDR; BLE+LoRa hiyerarşisi)**, L-24 (HaLow afet mesh), EK-05 (yeraltı LoRa yönlendirme).

### 9.2 Topluluk, doküman ve saha kaynakları (yeni)

| # | Kaynak | İçerik |
|---|---|---|
| **T-27** | meshtastic.org — *Why Meshtastic Uses Managed Flood Routing* | Dinle, başkası yayınladıysa yayınlama. "Uygun trafik kontrolüyle 100+ düğüm" |
| **T-28** | meshtastic.org — *Mesh Broadcast Algorithm* | SNR tabanlı çekişme penceresi, örtük ACK, 3 yeniden deneme, next-hop geri düşme. 40+ düğümde aralık ölçekleme formülü |
| **T-29** | meshtastic.org — *Demystifying ROUTER_LATE* + *Choosing the Right Device Role* | ROUTER yalnızca en iyi çatılar için. ROUTER_LATE boşluk doldurur. ChUtil < %25, AirUtilTX < %7–8 |
| **T-30** | meshtastic.org — *Configuration Tips* | CLIENT varsayılan, CLIENT_MUTE yoğun alan, CLIENT_BASE çatı/tavan arası, "MAX HOPS 3 kalsın" |
| **T-31** | meshtastic.org — *Meshtastic 2.6 Preview: Next-Hop Routing* | DM'lerde röle öğrenme, geriye uyumlu |
| **T-32** | meshtastic.org — *Is LongFast Holding Your Mesh Back?* | Preset veri hızları. Wellington %65 → ShortFast. Bay Area 150+ düğüm MediumSlow |
| **T-33** | meshtastic.org — *That one time at DEF CON* | 2.000+ düğüm. NodeDB ~100. Sahte NodeInfo tekrar oynatma saldırısı |
| **T-34** | GitHub firmware Discussion #9433 — hop ağırlıklı yeniden yayın gecikmesi | Öneri (Ocak 2026), yanıtsız |
| **T-35** | GitHub firmware Discussion #9437 — *Traffic management module* | Konum dedup, hız sınırı, telemetri hop tüketme, NodeInfo'yu yerelden yanıtlama. %40–50 → %20–30 |
| **T-36** | GitHub firmware PR #11826 — tıkanıklığa duyarlı hop ölçekleme | 15 Eyl 2026 develop. %20 devreye girer, %12 çıkar, %25 kibar trafik bekler, %40 bütün trafik bekler. Altyapı rolleri 3 hop tabanı |
| **T-37** | GitHub firmware PR #11927 — çalışma süresi muhasebesi | Kayan pencere düzeltmesi, paket bazlı kabul, ACK rezervi. Açık (21 Eyl 2026) |
| **T-38** | GitHub firmware Issue #7980 (acil durum modu, kapatıldı) + Discussion #8262 (QoS) | Acil paketler normal paketle aynı muamele görüyor |
| **T-39** | GitHub firmware Discussion #3831 — çoklu radyo arayüzü · meshtastic.org cihaz sayfaları (`LORA_24`, SX1280/LR1121) | Tek radyo kısıtı. 2,4 GHz LoRa: dünya geneli, alt-GHz kısıtı yok, 203–1625 kHz BW |
| **T-40** | GitHub firmware Issue #7863 — CLIENT_BASE | Favoriler için ROUTER, diğerleri için CLIENT davranışı |
| **T-41** | meshtastic.org — *Store & Forward Module* | Yalnızca PSRAM'li ESP32. LongFast'te çalışmaz. Yinelenen mesaj olasılığı |
| **T-42** | 2bn.de — *Meshtastic Optimization* (Kasım 2025) | 120 düğümden < 10'u varsayılan dışı rolde. `CORE_PORTNUMS_ONLY`. Balkon ile pencere farkı |
| **T-43** | filipnet.de — *Meshtastic: RF Bandpass Filter* · SQ5BPF filtre notları · RAK kavite filtre | LTE Band 20 (832–862 MHz) en kritik bastırma bandı. ≤ 1 dB kayıp, 833 MHz'te ≥ 40 dB |
| **T-44** | D-Central — *Solar Meshtastic Relay Node Build Guide* | RAK4631 ROUTER 5–30 mA. LFP zorunlu, 0 °C altı şarj yok. Kış PSH. Kablo kaybı. Parafudr. 6 arıza modu |
| **T-45** | NI3N — *Step-by-Step: Rooftop Meshtastic Router Node (Ham-Optimized)* | N tipi, LMR-240/400, parafudr, topraklama, ferrit, ısı testi. "Yükseklik güçten iyidir" |
| **T-46** | MeshAtlas — *Home, Rooftop and Fixed Base Meshtastic Nodes* | Radyo antene yakın. Her çatı ROUTER değildir. Servis edilebilirlik, kendiliğinden toparlanma |
| **T-47** | Hackaday — *The Practicality of Solar Powered Meshtastic* (Eyl 2025) + yorumlar | Bahçe lambası paneli yetmiyor. LTO/LFP önerisi. "Yönlendirme güvenilir değil" şikâyeti |
| **T-48** | Muldrf's Hobbytronic — *Meshtastic Outdoor Solar Node* (Eyl 2025) | RAK4631 + 6 W panel + 18650. Kutu içi 115 °F (46 °C). Gece %85 şarj |
| **T-49** | RAKwireless — WisMesh Repeater Mini V2, RAK13302 (SX1262 + SKY66122 PA/LNA + SAW), *Why Meshtastic needs a true 1 W radio* | nRF52840, IP67, 22/30 dBm, $90–100. Yalnız PA'lı tasarımlarda asimetrik bağlantı |
| **T-50** | TJK-Solutions — *Notfallkommunikation mit Meshtastic in Am Mellensee* + YouTube *Notfallkommunikations-Koffer der Gemeinde Am Mellensee* + BBK *Katastrophenschutz-Leuchttürme* | 9 bilgi noktası, SenseCAP Solar P1-Pro ROUTER, Heltec V3, T-Deck Plus. 27 Eyl 2025 tatbikatı, ~7 km |
| **T-51** | Heartland Emergency Preparedness — *Berlin Power Outage case study* (Eyl 2025) | 100+ aktif düğüm, kent içi 5–10 km, firmware uyumsuzluğu, güç tükenmesi, çatıya çıkma |
| **T-52** | PAM Finds — *Disaster Comms with Meshtastic + MeshCore (2026)* · adrelien — Helene yazısı | Helene'de 30 saniyede bir durum spamı ağı kilitledi. Mesaj disiplini, kanal ayrımı |
| **T-53** | NodakMesh / Austin Mesh — *MeshCore vs Meshtastic* | MeshCore'da yalnızca tekrarlayıcılar iletir, yol öğrenilir |
| **T-54** | RF.Guru — *Meshtastic and MeshCore in Europe: The Legal RF Framework* | 869,4–869,65 MHz, 27 dBm ERP, %10. "10 düğüm × %10 kanalı doldurabilir". AB Kararı 2025/105 |
| **T-55** | TA Mesh — SSS ve MQTT ayarları (purgatoria.tr) | TR: 433 MHz 10 mW ERP, 868 MHz 500 mW ERP. Dış mekânda ≥ 6 dBi anten tavsiyesi. `msh` kök konusu |
| **T-56** | YouTube — *Meshtastic Long Lines Relay Tower Station* (Mar 2024) · *FINALLY Solar Meshtastic On My Roof! LRZNode* (Nis 2025) | 15 m kulede 2 sektör anten + 2 Heltec V3. Çatı solar kurulum pratiği |
| **T-57** | Mesh Underground — *Maximize Meshtastic Range* | Preset link bütçeleri (ShortTurbo 140 dB … VLongSlow 158,5 dB). Kentte 0,5–1 km stok anten, 2–3 km iyileştirilmiş |
| **T-58** | Meshtastic Discourse — *Yet another Solar Node (RAK19007)* · RAK forum — RAK19007 şarj akımı | RAK19007 en fazla 350 mA şarj. Çok hücreli paketlerde yetersiz |
| **T-59** | TrekLink docs PR #12 — *on-device queue decisions / firmware queue ground truth* | SOS izi + telemetri kuyruğu ~80 s'de dolduruyor. En eski atılınca SOS ilk kaybolan oluyor (**ikincil kaynak, doğrulanacak**) |
| **T-60** | GitHub meshtastic/burntastic — Burning Man 2024 firmware | Varsayılan preset'ler önceki yıllarda tıkandı. Etkinliğe özel firmware ile çözüldü |
