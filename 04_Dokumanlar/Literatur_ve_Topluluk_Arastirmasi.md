# AfetMesh — Donanım Literatür ve Topluluk Araştırması

> ## 📎 BELGE STATÜSÜ: ARAŞTIRMA GİRDİSİ (24 Eylül 2026)
>
> Bu belge, **[`02_Donanim/Donanim_Gereksinim_Raporu.md`](../02_Donanim/Donanim_Gereksinim_Raporu.md) v2.1**'deki kararların dayandığı kaynakların kaydıdır. Rapordaki `[L-n]` akademik makaleye, `[T-n]` topluluk/forum/doküman kaynağına, `[C-n]` ticari cihaza karşılık gelir.
>
> **Kapsam:** 30 akademik yayın (Google Scholar, arXiv, IEEE Xplore, MDPI, ACM, ScienceDirect, DergiPark) · 26 topluluk kaynağı (meshtastic.org, Meshtastic GitHub issue/discussion'ları, r/meshtastic ve bağlantılı proje sayfaları, Hackaday, Printables, PCBWay) · 14 ticari Meshtastic cihazı.
>
> **📄 Tam metinler:** Açık erişimli olup indirilebilen makalelerin PDF'leri [`Kaynaklar/Makaleler/`](Kaynaklar/Makaleler/) klasöründedir; tabloda **📄** ile işaretlidir. Tüm projenin birleşik kaynakçası: [`Kaynakca.md`](Kaynakca.md).
>
> **Okuma notu:** Bu bir araştırma kaydıdır; SGB kuralı gereği sonradan geriye dönük düzenlenmez. Güncel bulgular yeni tarihli bir belgeyle eklenir.
>
> ⚠️ **Güncelleme notu (24 Eyl 2026, SGB v2.5):** Ağ katmanı sorunları (ölçeklenme, çatı röleleri, merkeze iletim) için yeni araştırma: [`Meshtastic_Sorunlari_ve_Cozum_Arastirmasi.md`](Meshtastic_Sorunlari_ve_Cozum_Arastirmasi.md) — numaralandırma **L-31…L-35** ve **T-27…T-60** ile sürdürüldü.

---

## 1. Yöntem

| Adım | Ne yapıldı |
|---|---|
| Arama terimleri | *LoRa mesh disaster / emergency communication*, *Meshtastic evaluation*, *LoRa energy consumption model*, *LoRa 433 vs 868 building / rubble*, *LoRa search and rescue*, *wearable LoRa antenna body effect*, *threshold fall detection accelerometer gyroscope*, *18650 low temperature discharge*, *low-power microcontroller comparison*, *nRF52840 vs ESP32 Meshtastic battery* |
| Seçim ölçütü | (a) Doğrudan donanım kararını etkileyen nicel bulgu içermesi · (b) Afet/acil durum veya LoRa fiziksel katmanı ile ilgili olması · (c) Hakemli yayın **veya** hakemsiz ise (arXiv) yöntemi açıkça tarif edilmiş olması |
| Topluluk kaynakları | Yalnızca **ölçüm verisi**, **tasarım dosyası** veya **tekrarlanabilir hata raporu** içerenler alındı; salt görüş bildiren gönderiler alınmadı |
| Sınırlılık | Bazı makalelerin yalnızca özetine erişilebildi (tabloda "Özet" ile işaretli). Bazı topluluk ölçümleri grafik içinde olduğundan sayıya dökülemedi. Reddit'e doğrudan erişim kısıtlı olduğundan, r/meshtastic'te paylaşılan projeler bağlantı verdikleri GitHub/Printables/Hackaday sayfalarından incelendi |

---

## 2. Akademik Yayınlar

### 2.1 LoRa Fiziksel Katmanı, Menzil ve Ölçeklenebilirlik

| # | Künye | Tür | Temel bulgu | Projeye etkisi |
|---|---|---|---|---|
| **L-1** [📄](Kaynaklar/Makaleler/L-01_Augustin_2016_Study_of_LoRa.pdf) | Augustin, A., Yi, J., Clausen, T., Townsley, W. M. — *A Study of LoRa: Long Range & Low Power Networks for the Internet of Things.* **Sensors** 16(9):1466, 2016 | Dergi | LoRa modülasyonunun SF/BW/CR ödünleşimlerinin ve LoRaWAN kapasitesinin temel analizi; yüksek SF'de menzil artışı karşılığında hava süresi (ve enerji) üstel artar | Modem preset seçimi (SGB A-2) enerji bütçesini doğrudan etkiler → güç bütçesinde TX süresi preset'e göre hesaplanmalı |
| **L-2** | Petäjäjärvi, J., Mikhaylov, K., Roivainen, A., Hänninen, T., Pettissalo, M. — *On the Coverage of LPWANs: Range Evaluation and Channel Attenuation Model for LoRa Technology.* **ITST 2015** | Konferans | 868 MHz, SF12: karada ~15 km, su üzerinde ~30 km bağlantı; kanal zayıflama modeli önerilir | İdeal koşul menzilidir — SYS-7 gereği **beyan edilmez**; yalnızca üst sınır referansı |
| **L-3** | Bor, M., Roedig, U., Voigt, T., Alonso, J. M. — *Do LoRa Low-Power Wide-Area Networks Scale?* **ACM MSWiM 2016**, s. 59–67 | Konferans | Statik ayarlar ve tek alıcıyla LoRa **iyi ölçeklenmez**; çarpışma ve yakalama etkisi kapasiteyi sınırlar (LoRaSim) | CEP'in `CLIENT_MUTE` rolünü (SGB CEP-2) destekler — kişisel cihazın tekrarlama yapmaması kanal kapasitesini korur |
| **L-4** [📄](Kaynaklar/Makaleler/L-04_Liando_2019_Known_Unknown_Facts_LoRa.pdf) | Liando, J. C., Gamage, A., Tengourtius, A. W., Li, M. — *Known and Unknown Facts of LoRa: Experiences from a Large-scale Measurement Study.* **ACM TOSN** 15(2), 2019 | Dergi | LoS'ta > 10 km; NLoS'ta parametre seçim algoritması; enerji ve kapasite üzerine büyük ölçekli ölçüm | Şehir içinde (NLoS) menzil çok daha kısa — node yoğunluğu menzilden kritik (SGB risk kaydıyla uyumlu) |
| **L-5** [📄](Kaynaklar/Makaleler/L-05_Cattani_2017_LoRa_Reliability.pdf) | Cattani, M., Boano, C. A., Römer, K. — *An Experimental Evaluation of the Reliability of LoRa Long-Range Low-Power Wireless Communication.* **J. Sensor and Actuator Networks** 6(2):7, 2017 | Dergi | **Sıcaklık artışı alınan sinyal gücünü belirgin düşürür**; yüksek SF her zaman daha güvenilir değildir | HW-ENV-1 (+50 °C) testinde RF performansı da ölçülmeli; güneş altında bırakılan cihazda menzil düşer |
| **L-6** | Casals, L., Mir, B., Vidal, R., Gomez, C. — *Modeling the Energy Performance of LoRaWAN.* **Sensors** 17(10):2364, 2017 | Dergi | LoRa cihazının durum bazlı (uyku/TX/RX) enerji modeli ve pil ömrü tahmini | Bölüm 5 güç bütçesinin yöntem referansı |
| **L-7** [📄](Kaynaklar/Makaleler/L-07_Bouguera_2018_LoRa_Energy_Model.pdf) | Bouguera, T., Diouris, J.-F., Chaillout, J.-J., Jaouadi, R., Andrieux, G. — *Energy Consumption Model for Sensor Nodes Based on LoRa and LoRaWAN.* **Sensors** 18(7):2104, 2018 | Dergi | SF, CR, yük boyutu, onaylı iletim ve mesafenin enerjiye etkisi modellenmiş | ACK'lı (onaylı) SOS mesajlarının enerji maliyeti hesaba katılmalı — M2 beacon modu ACK'sız tasarlandı |
| **L-8** | Lee, H.-C., Ke, K.-H. — *Monitoring of Large-Area IoT Sensors Using a LoRa Wireless Mesh Network System: Design and Evaluation.* **IEEE Trans. Instrum. Meas.** 67(9), 2018 | Dergi | LoRa üzerinde mesh yönlendirme ile geniş alanda teslim oranı ve gecikme ölçümü | Mesh'in tek-hop'a göre kapsama avantajı; ama her hop gecikme ekler (SGB SOS gecikme hedefi < 60 sn) |
| **L-9** | Bobkov, I., Rolich, A. vd. — *Study of LoRa Performance at 433 MHz and 868 MHz Bands Inside a Multistory Building.* **IEEE MWENT 2020** | Konferans | 433 MHz daha güçlü sinyal (RSSI/SNR), **868 MHz daha yüksek paket alım oranı**; 9 katlı betonarmede 868 MHz SF10 önerilir | SGB A-1 için doğrudan veri: bina içinde iki band da çalışır, teknik fark belirleyici değil → karar **ağ gerçeğine** göre verilmeli (SGB ile tutarlı) |
| **L-10** | *Performance Evaluations of LoRa Wireless Communication in Building Environments.* **Sensors** 20(14):3828, 2020 | Dergi | 868 MHz'in bina penetrasyonu teorik olarak 434 MHz'den kötü; ölçümlerle bina içi performans karakterize edilmiş | A-1 girdisi; anten ve band kararı kasa/anten boyutunu da etkiler (433'te anten ~2× uzun) |
| **L-11** [📄](Kaynaklar/Makaleler/L-11_LoRa_Rural_Nepal_2024.pdf) | *Performance Evaluation of LoRa Technology for Rural Connectivity: An Experimental Analysis in Nepal.* arXiv:2412.04563, 2024 | Ön baskı (Özet) | Engebeli arazide LoRa bağlantı ölçümleri | Türkiye'nin dağlık deprem bölgeleri için arazi etkisinin önemini gösterir |

### 2.2 Afet ve Acil Durum Haberleşmesi

| # | Künye | Tür | Temel bulgu | Projeye etkisi |
|---|---|---|---|---|
| **L-12** [📄](Kaynaklar/Makaleler/L-12_Matracia_2022_PostDisaster_Communications.pdf) | Matracia, M., Saeed, N., Kishk, M. A., Alouini, M.-S. — *Post-Disaster Communications: Enabling Technologies, Architectures, and Open Challenges.* **IEEE Open J. Commun. Soc.** 2022 (arXiv:2203.13621) | Derleme | Afet sonrası haberleşme teknolojilerinin kapsamlı taraması; enkaz altı kurban **konumlandırma** açık bir sorun | M2 Enkaz modu ve konum paylaşımı (GNSS son konum) gereksinimini destekler |
| **L-13** | Centelles, R. P. vd. — *A LoRa-Based Communication System for Coordinated Response in an Earthquake Aftermath.* **UCAmI 2019 / MDPI Proceedings** | Konferans | Şili, Coquimbo senaryosu (~28.000 kişi): vatandaş → yetkili durum bildirimi mimarisi | SGB'nin "yukarı yönlü" konumlandırması ile aynı; SOS paket yapısı (SGB 9.1) benzer kısa yapısal mesaj yaklaşımını kullanır |
| **L-14** | Macaraeg, K. C. V. G. vd. — *LoRa-based Mesh Network for Off-grid Emergency Communications.* **IEEE GHTC 2020** (IEEE Xplore 9342944) | Konferans | Değiştirilmiş AODV + RSSI metriği; küçük ölçekte yüksek trafik altında ~%99 teslim | Küçük ölçek sonucu; büyük ağda Meshtastic yönetimli taşkın yönlendirmesiyle karşılaştırılamaz |
| **L-15** [📄](Kaynaklar/Makaleler/L-15_Hochst_2020_LoRa_D2D_Smartphone_ISCRAM.pdf) | Höchst, J., Baumgärtner, L., Kuntke, F., Penning, A., Sterz, A., Freisleben, B. — *LoRa-based Device-to-Device Smartphone Communication for Crisis Scenarios.* **ISCRAM 2020**, s. 996–1011 | Konferans | Akıllı telefonları düşük maliyetli LoRa modemlerle (rf95modem) eşleştirerek Android/iOS'ta altyapısız sohbet | BLE üzerinden telefon köprüsü yaklaşımını doğrular (HW-MCU-3) — iOS için BLE tek yol |
| **L-16** | Sciullo, L., Fossemò, F., Trotta, A., Di Felice, M. — *LOCATE: A LoRa-based Mobile Emergency Management System.* **IEEE ISCC 2018** | Konferans | Afetzede ile arama-kurtarma ekipleri arasında LoRa + telefon tabanlı iletişim; enkaz altında tespit senaryoları simüle edilmiş | Enkaz Modu (SGB CEP-4) ve M2 beacon modu için akademik öncül |
| **L-17** | Bianco, G. M., Giuliano, R., Marrocco, G., Mazzenga, F., Mejia-Aguilar, A. — *LoRa System for Search and Rescue: Path-Loss Models and Procedures in Mountain Scenarios.* **IEEE Internet of Things J.** 8(3), 2021 | Dergi | Dağlık arazide LoRa ile arama-kurtarma; yol kaybı ölçümüyle konumlandırma; ARVA/RECCO'nun onlarca metrelik menziline karşı km mertebesi | Periyodik beacon (M2) + RSSI ile kurtarma ekibinin yön bulması mümkün — **düşük güçte uzun süre yayın** donanım gereksinimi (HW-RF-11) |
| **L-18** | Di Carlofelice, A. vd. — L'Aquila deprem enkazında **434 ve 868 MHz** sürekli dalga vericilerle yol kaybı ve geliş açısı ölçümleri (enkaz altı konumlandırma) | Konferans/dergi (Özet) | Enkaz altındaki vericinin sinyali çevreden ölçülebilmiş ve **konum kayda değer doğrulukla bulunabilmiş**; her iki band enkazdan çıkabiliyor | Enkaz altında sub-GHz yayının fiziksel olarak mümkün olduğunun doğrudan kanıtı → M2 modu gerekçesi. **Künye (yıl/dergi) teyit edilmeli** |
| **L-19** [📄](Kaynaklar/Makaleler/L-19_Offgrid_Civilian_Emergency_Comm_2025.pdf) | *Bridging Technical Capability and User Accessibility: Off-grid Civilian Emergency Communication.* arXiv:2509.22568, 2025 | Ön baskı | Zürih, 10 node, ESP32-S3 + SX1262, < 0,4 W ortalama; 868 MHz LongFast **1,2 km'de %92** teslim, 433'ü her metrikte geçmiş; eğitimsiz sivillerle **SUS = 74** | Kullanılabilirlik hedefi referansı (SGB TST-6); ESP32-S3'ün 0,4 W'lık ortalaması 18650 ile ~1 gün demektir — K-2 kararını destekler |
| **L-20** [📄](Kaynaklar/Makaleler/L-20_Meshtastic_Smart_Campus_2026.pdf) | *A Meshtastic-based LoRa Mesh System for Smart Campus Applications.* arXiv:2605.20379, 2026 | Ön baskı | Raspberry Pi Pico + SX1262 solar node, SenseCAP T1000-E izleyici; kampüste **2,47 km**, ortalama RSSI −110 dBm, SNR +2,75 dB | Gerçekçi yarı-kentsel menzil referansı; RP2040 tabanlı node'un sabit (solar) kullanımda uygun olduğu, taşınabilirde değil |
| **L-21** [📄](Kaynaklar/Makaleler/L-21_Meshtastic_Profiles_Resilience_2026.pdf) | *Resilience Analysis in Off-Grid LoRa Mesh Networks: Evaluation of Meshtastic Profiles in Long-Range Propagation Scenarios.* arXiv:2605.17063, 2026 | Ön baskı | 8 Meshtastic preset × 3 güç seviyesi, kılavuzlu bağlantı: **Short (SF7–8) 110–120 dB**, **Medium (SF9–10) 135–150 dB**, **Long Slow (SF11–12) ~180 dB** yol kaybında kopuyor; SF12'de −18 dB SNR'ye kadar çözme | Preset seçimi (A-2) bina içi/enkaz senaryosunda belirleyici; Long preset'lerin hava süresi uzun → **TX enerji** ve kanal doluluğu artar (güç bütçesinde hesaba katıldı) |
| **L-22** [📄](Kaynaklar/Makaleler/L-22_Vakhnovskyi_2026_DualRadio_BLE_LoRa.pdf) | Vakhnovskyi, A. — *Dual-Radio BLE-LoRa Hierarchical Mesh for Infrastructure-Free Emergency Communication.* arXiv:2604.15532, 2026 | Ön baskı | **nRF52840 + SX1262**; trafiği mümkün olduğunda BLE'ye aktararak LoRa enerjisinde **%79 azalma**; 3,0 KB RAM ile çalışır | Aynı donanım platformunun (K-2) afet mesh'i için akademik olarak kullanıldığının kanıtı |
| **L-23** [📄](Kaynaklar/Makaleler/L-23_Cetinkaya_2026_Meshtastic_Emergency_DergiPark.pdf) | Çetinkaya, Ö. — *Meshtastic Example in Emergency Communication.* **International Scientific and Vocational Studies Journal** 10(1), 2026 (DergiPark) | Dergi (TR) | 3 el tipi node, 433 ve 868 MHz, lisanssız ISM bandında Meshtastic ile iletişim + GPS takibi | Türkiye bağlamında Meshtastic'in uygulanabilirliğinin yerli kaynağı |
| **L-24** [📄](Kaynaklar/Makaleler/L-24_HaLert_2025.pdf) | *HaLert: A Resilient Smart City Architecture for Post-Disaster Based on Wi-Fi HaLow Mesh and SDN.* arXiv:2507.07841, 2025 | Ön baskı | Afet sonrası Wi-Fi HaLow (sub-GHz) mesh + SDN mimarisi | Alternatif teknoloji; ticari yaygınlık ve topluluk ağı olmadığı için LoRa/Meshtastic tercihini değiştirmez |

### 2.3 Anten, Sensör ve Pil

| # | Künye | Tür | Temel bulgu | Projeye etkisi |
|---|---|---|---|---|
| **L-25** | Van Torre, P., Ameloot, T., Rogier, H. — *Wearable 868 MHz LoRa Wireless Sensor Node on a Substrate-Integrated-Waveguide Antenna Platform.* **EuMC 2019** | Konferans | Vücut üzerindeki 868 MHz antende tepe kazanç **−4,86 dBi**, radyasyon verimi **%14,5** | Vücut yakınında büyük kayıp → **harici whip anten** (HW-RF-5) ve π-eşleme footprint'i (HW-RF-6); menzil testleri elde tutarak da yapılmalı |
| **L-26** | Bourke, A. K., O'Brien, J. V., Lyons, G. M. — *Evaluation of a Threshold-Based Tri-Axial Accelerometer Fall Detection Algorithm.* **Gait & Posture** 26(2):194–199, 2007 | Dergi | Gövdeye takılı ivmeölçerle üst eşikte %100 özgüllük; alt eşikte %91,25 | Eşik tabanlı düşme algılama donanım açısından yalnızca ivmeölçer + kesme gerektirir; ama sensör **gövdeye sabit** — elde taşınan cihaz farklı |
| **L-27** | *Threshold-Based Fall Detection Using a Hybrid of Tri-Axial Accelerometer and Gyroscope.* **Physiological Measurement** 39(10), 2018 | Dergi | İvme büyüklüğü + açısal hız birleşik eşikleri, tek parametreli yöntemlere göre daha az yanlış sınıflama | **6 eksen IMU** seçimi (LSM6DS3TR-C) gerekçesi — HW-SNS-7 |
| **L-28** | Zhang, S. S., Xu, K., Jow, T. R. — *On the Discharge Capability and Its Limiting Factors of Commercial 18650 Li-ion Cell at Low Temperatures.* **J. Power Sources** 115, 2003 | Dergi | Düşük sıcaklıkta kapasite ve güç kaybının kaynakları (elektrolit iletkenliği, yük transfer direnci) | −10 °C'de kullanılabilir kapasite düşürülerek hesaplandı (Bölüm 5.3); **iç direnç artışı → TX tepe akımında gerilim çökmesi** → buck-boost kararı (K-5) |
| **L-29** | Ševčík vd. — *Battery Pack for IoT Devices in a Harsh Outdoor Environment.* **Sensors** 26(16):5232, 2026 | Dergi | −30 °C'de LiFePO4, Li-ion referansa göre 50 mA'de 89,1 s'e karşı 66,0 s dayandı; öneriler: **host'tan bağımsız donanımsal koruma**, coulomb sayımıyla doğrulama, kimyaya uygun koruma eşikleri | HW-PWR-3 (kartta bağımsız koruma), yakıt göstergesi doğrulaması (T-11), LFP seçilirse koruma IC değişmeli (Bölüm 6.4) |
| **L-30** [📄](Kaynaklar/Makaleler/L-30_LowPower_MCU_Wearables_2024.pdf) | *Enabling Efficient Wearables: An Analysis of Low-Power Microcontrollers for Biomedical Applications.* arXiv:2411.09534, 2024 | Ön baskı | Düşük güçlü MCU'ların giyilebilir iş yüklerinde enerji/performans karşılaştırması; ESP32 yüksek çıkarım gücü (~130–157 mW) | MCU puanlamasında güç satırının bağımsız akademik dayanağı (ESP32 ailesi yüksek aktif güç) |

### 2.4 Akademik Bulguların Özeti — Donanıma Etkisi

| Tema | Bulgu (kaynak) | Donanım kararı |
|---|---|---|
| **Enerji** | LoRa'da enerji hava süresine bağlı; SF arttıkça üstel artar (L-1, L-6, L-7, L-21) | Güç bütçesi preset'e göre; M2 beacon ACK'sız |
| **MCU** | Aynı radyo ile nRF52840 afet mesh'inde kullanılıyor (L-22); ESP32 ailesi yüksek aktif güç (L-19, L-30) | K-2: nRF52840 |
| **Band** | 433 daha güçlü sinyal, 868 daha kararlı teslim; ikisi de enkazdan çıkabiliyor (L-9, L-10, L-18) | A-1 teknik değil ağ kararı; PCB iki banda uyumlu (K-4) |
| **Ölçek** | Tekrarlama yapan çok sayıda node kapasiteyi tüketir (L-3, L-4) | CLIENT_MUTE (SGB CEP-2) |
| **Kurtarma** | Enkaz altı sub-GHz yayın ölçülebilir ve konumlandırılabilir (L-16, L-17, L-18) | M2 Enkaz/Beacon modu; HW-RF-11; HW-UI-5 (≥ 85 dB) |
| **Anten** | Vücut yakınında verim %15'e düşebilir (L-25) | Harici whip + eşleme footprint'i |
| **Pil** | Soğukta kapasite ve iç direnç kötüleşir (L-28, L-29) | Buck-boost (K-5); bağımsız koruma; −10 °C testi |
| **Sensör** | İvme + jiroskop birleşimi yanlış pozitifi azaltır (L-26, L-27) | 6 eksen IMU |
| **Kullanılabilirlik** | Eğitimsiz sivil SUS = 74 mümkün (L-19) | Buton tam yedek + e-paper sade arayüz |

---

## 3. Topluluk Kaynakları (meshtastic.org, GitHub, Reddit bağlantılı projeler, forumlar)

### 3.1 Ölçüm ve Karşılaştırma Kaynakları

| # | Kaynak | İçerik | Kullanıldığı yer |
|---|---|---|---|
| **T-1** | tutoduino.fr — *Compare the power consumption of Meshtastic devices* | 5 V USB'den güç kaynağı ile ölçüm; Heltec V3 ortalama ~130 mA; XIAO nRF52840'ta **BLE'nin ek tüketimi ölçülemeyecek kadar küçük** | K-2, Bölüm 4.3 |
| **T-2** | meshtastic.org — *Devices / Supported Hardware Overview* | "nRF52 cihazları ESP32'den az güç kullanır, **solar ve el cihazları için tercih edilir**"; SX127x yerine SX126x/LR11xx önerisi; ESP32-C6 cihazları | K-2, K-3, Bölüm 4.2 |
| **T-3** | keepteen.com — *After testing 20 devices…* (Meshtastic pil testi) | Varsayılan ayarla: Heltec V3.1 10–30 s, T-Deck 10–26 s, **T114 (GPS kapalı) 104–220 s**, **RAK19007 154–442 s**; light-sleep ile Heltec V3.2 61–156 s; **T114'te GPS açınca 220 → 119 s** | Bölüm 4.3, HW-SNS-1 |
| **T-4** | GitHub meshtastic Discussion #478 — *FARO Node: nRF52840 + Wio-SX1262, halved RX current, fixed cold-weather brownouts (PPK2)* | Stok 7,5 mA → düzeltmelerle **4,5 mA** (3000 mAh: 16 → 28 gün); preamble 8 → 5 sembol; OCV eşiği 3,1 → 3,4 V; LPCOMP uyanma; **LDO dropout'unda TX tepe akımıyla brownout/bootloop** | K-5, HW-PWR-6/12, HW-MCU-9 |
| **T-5** | meshtastic.org — *Meshtastic UI (MUI)* | MUI **yalnızca ESP32-S3 ve gömülü Linux**; nRF52 desteklenmiyor | K-6, A-7 |
| **T-6** | meshtastic.org — *InkHUD* ve *DIY InkHUD Builds* | E-ink için düşük güçlü arayüz; **tek butonla** kullanım; DIY listesi: T114 ve nRF52 Pro-Micro + WeAct 1,54"/2,13"/2,9"/4,2" | K-6, HW-DSP-3, HW-UI-3 |
| **T-7** | GitHub meshtastic firmware Issue #6660 + PR #7121 (dinamik light-sleep) | Mevcut ESP32 power-saving **telefon bağlantısını koparıyor**; yama ile Heltec V3 ~100 → ~50 mA; PR **henüz birleştirilmedi** | K-2, Bölüm 1 tablo |
| **T-16** | CNX Software / Jeff Geerling — Raspberry Pi Zero 2 W güç ölçümleri | Boşta ~100 mA @5 V; WiFi açık ~100–180 mA | Bölüm 4.3 |
| **T-18** | GitHub meshtastic Discussion #435 — *RP2040 Node Power Consumption* | RP2040 ~40 mA, ESP32'den ~10–20 mA az | Bölüm 4.3 |
| **T-22** | Winstar, Pervasive Displays — e-paper / OLED / MIP güç karşılaştırmaları (sektör) | E-paper yalnızca yenilemede güç çeker, statik 0; LCD enerjisinin %70–85'i arka ışık | HW-DSP-2 |
| **T-23** | Rokland — *nRF52840 vs ESP32-S3 (for Meshtastic use)* | Nitel: nRF52840 "çok daha düşük boşta ve aktif güç", ESP32-S3 "sabit beslemeli node'lara uygun" | Bölüm 4 |
| **T-24** | Seeed Studio Forum — *XIAO nRF52840 & Wio-SX1262 Kit for Meshtastic Power Consumption* | Kullanıcı ölçüm tartışması | Bölüm 5 çapraz kontrol |

### 3.2 Açık Kaynak / DIY Donanım Projeleri

| # | Proje | MCU + Radyo | Öne çıkan tasarım | Ders |
|---|---|---|---|---|
| **T-4** | FARO Node v1.0 (özel PCB, solar) | XIAO nRF52840 + Wio-SX1262 | Tam özel PCB, solar şarj | Soğukta LDO dropout → brownout; firmware eşikleri kart tasarımına göre ayarlanmalı |
| **T-8** | Meshtastic `nrf52_promicro_diy_tcxo` varyantı | nRF52840 Pro Micro + E22 / RA-01SH / HT-RA62 / Wio-SX1262 / LR1121 | Genel şema; TXEN-DIO2 köprüsü, RXEN → P0.17; `rfswitch.h` modül matrisi | Ayrık modül yolunun resmî referansı (v2 yedek yolu) |
| **T-9** | fakeTec PCB (gargomoma) | nRF52840 ProMicro/SuperMini + HT-RA62 / RA-01SH | Heltec V3 kasalarına uyan boyut; opsiyonel SSD1306; 2 buton | **"Lehimlemeden önce ProMicro'yu test et"** — bootloader eksik/eski olabiliyor → SWD zorunlu |
| **T-10** | Meshtastic firmware Issue #6692 | nRF52840 ProMicro + E22-400M22S (SX1268) | — | TCXO/RF anahtar ayarı yanlışsa **BUSY hattı takılı kalıyor**; çözümsüz kapatıldı → birleşik modül tercihi |
| **T-11** | Meshtastic firmware Issue #8801 + Discussion #9026 | Heltec T114 (nRF52840) | — | "Kapalıyken" ~8 mA (çevre birimler beslemede kalıyor) → **HW-PWR-8, HW-MOD-5** |
| **T-20** | Hackaday.io — *Meshtastic DIY – How To Build Your Own* | $6 nRF52840 Pro Micro + $6 LoRa | ~$12 node | RF anahtar pini HIGH kalırsa **+55 µA**; SPI uykuya alınmalı |
| **T-21** | Ikoka Nano (ndoo.sg, GitHub) | XIAO nRF52840 + E22-xxxMxxS | KiCad 8, **2 katman**, 0603+ pasif, JLCPCB DRC | Tek kişilik açık kaynak PCB örneği; ekran/buton yok |
| **T-25** | PCBWay — *Meshtastic Compatible Node* | nRF52 Pro Micro + Wio SX1262 | IP5306 şarj, 4 LED, GPS | IP5306 bir powerbank IC'sidir — düşük yükte kendini kapatabilir; **power-path şarj IC'si tercih** (BQ24074) |
| **T-26** | Printables — *X-Mini-Faketec "TinyTiny"* ve *LWC "Meshformer"* | nRF52840 FakeTec | Ekran + navigasyon anahtarı + buzzer + titreşim (400 mAh) / CardKB klavye + 4000 mAh | El cihazı formunda **navigasyon anahtarı + buzzer + titreşim** üçlüsü topluluğun ortak tercihi (HW-UI-2/5/7) |
| — | nicolabalzano/Meshtastic-DIY-sx126x (GitHub) | ESP32 + Ebyte E22 | Kablolama rehberi + PlatformIO derlemesi | ESP32 + E22 kablolaması referansı (CEP-T Faz 0b breadboard için) |

### 3.3 Firmware / Platform Durum Kaynakları

| # | Kaynak | Bulgu | Kullanıldığı yer |
|---|---|---|---|
| **T-12** | HarukiToreda — *Meshtastic Compatible Peripherals* | Desteklenen sensörler: SHT4X, SHT31, SHTC3, AHT10, BME280/680, LIS3DH, LSM6DS3, MPU6050, QMI8658, BMX160, INA219/260/3221… (BMI160 listede **yok**) | HW-SNS-5/7, Bölüm 6.7 |
| **T-13** | TI — CC1352R ürün sayfası / DigiKey | CC13xx: BLE, Zigbee, Thread, 802.15.4g, Sidewalk — **LoRa yok** | Bölüm 4.2 (TI elendi) |
| **T-14** | holad.de — *Running Meshtastic on a ST STM32WLE5CC (RAK3172)* | PPK2 @3,5 V: TX tepe ~107 mA, ortalama ~21 mA, TX dışı ~12,3 mA; **STM32'de light/deep sleep uygulanmamış** | Bölüm 4.3 |
| **T-15** | meshtastic.org — *Raspberry Pi Pico & Pico 2* | Pico 2 W'da BLE **Meshtastic'te desteklenmiyor** | Bölüm 4.2 |
| **T-17** | Meshtastic firmware PR #11842, #11867, Discussion #10124 | nRF54L15 bare-metal port (s145 SoftDevice), 15 Eylül 2026; `xiao_nrf54l15`, `nrf54l15dk` | Bölüm 4.5 |
| **T-19** | Meshtastic Discourse *Limit for max node count* + Issue #11798 | nRF52'de NodeDB ~80 node (flash "user data" alanı ~28 KB) | Bölüm 4.4 |
| — | meshtastic.org — *Power Configuration* | `is_power_saving`: BLE, seri, WiFi, ekran kapanır; `ls_secs` yalnızca ESP32; güç kaybında kapanma ayarı | Bölüm 5.2 (M1) |

---

## 4. Ticari Meshtastic Cihazları — Karşılaştırma

| # | Cihaz | MCU | Radyo | Ekran | GNSS | Pil | Ölçülen / bildirilen ömür | Not |
|---|---|---|---|---|---|---|---|---|
| **C-1** | LILYGO T-Echo | nRF52840 | SX1262 | 1,54" e-ink | L76K | Dahili LiPo | — | InkHUD destekli; e-ink + nRF52 formunun öncüsü |
| **C-2** | Heltec Mesh Node T114 | nRF52840 | SX1262 | 1,14" TFT (ops.) | L76K (ops.) | Harici | **104–220 s (GPS kapalı), 119 s (GPS açık)** [T-3] | Kapalıyken ~8 mA sorunu [T-11] |
| **C-3** | Heltec Mesh Node T096 | nRF52840 | SX1262 | 0,96" TFT | UC6580 | — | — | Çok bantlı GNSS |
| **C-4** | Heltec MeshPocket | nRF52840 | SX1262 | 2,13" e-ink | — | Dahili | — | Qi2 kablosuz şarj |
| **C-5** | RAK WisMesh Pocket V2 | nRF52840 | SX1262 | — | ✅ | Dahili | — | RAK ekosistemi |
| **C-6** | Seeed SenseCAP T1000-E | nRF52840 | LR1110 | Yok | ✅ | Dahili | 64 s (GPS kapalı, varsayılan) [T-3] | Kart boyutu izleyici |
| **C-7** | Elecrow ThinkNode M1 | nRF52840 | SX1262 | E-ink | ✅ | Dahili | — | — |
| **C-8** | Seeed Wio Tracker L1 | nRF52840 | SX1262 | OLED / e-ink | ✅ | — | — | InkHUD çeşidi var |
| **C-9** | RAK4631 + RAK19007 | nRF52840 | SX1262 | Ops. | Ops. | Harici | **154–442 s** [T-3] | Modüler; bu projenin geliştirme platformu (Bölüm 12 adım 2) |
| **C-10** | LILYGO T-Deck Plus | ESP32-S3 | SX1262 | 2,8" LCD dokunmatik | ✅ | 2000 mAh | **10–26 s** (T-Deck) [T-3] | Klavye + trackball; MUI |
| **C-11** | LILYGO T-Deck Pro | ESP32-S3 | SX1262 | 3,1" e-paper dokunmatik | ✅ | 1400 mAh | — | E-paper + dokunmatik ESP32-S3 örneği (A-7 Yol B referansı) |
| **C-12** | Heltec WiFi LoRa 32 V3/V4 | ESP32-S3 | SX1262 | 0,96" OLED | — | Harici | **10–30 s** (V3.1), light-sleep 61–156 s [T-3] | En yaygın ucuz kart |
| **C-13** | LILYGO T-Beam Supreme | ESP32-S3 | SX1262 | OLED | ✅ | 18650 | — | 18650 yuvalı; yüksek tüketim |
| **C-14** | RAK WisMesh Tap V2 | ESP32-S3 (RAK3312) | SX1262 | 320×240 TFT dokunmatik | — | Dahili | — | **IP65** kasa |

> **Gözlem:** Pil ömrü öncelikli **bütün** el tipi ürünler (C-1…C-9) nRF52840 seçmiş; dokunmatik/renkli arayüzlü **bütün** ürünler (C-10, C-11, C-14) ESP32-S3 seçmiş. Pazarda **nRF52840 + dokunmatik** bir ürün yok — bu, K-6 / A-7 ödünleşiminin sektörde de aynı şekilde çözüldüğünü gösteriyor.

---

## 5. Açık Kalan Araştırma Soruları

| # | Soru | Neden önemli | Nasıl kapanır |
|---|---|---|---|
| AR-1 | L-18 (L'Aquila enkaz ölçümü) tam künyesi ve 434/868 kayıp değerleri | A-1 kararına doğrudan enkaz verisi sağlar | Google Scholar'da "Di Carlofelice rubble 434 868" ile tam metin; yazarla iletişim |
| AR-2 | Buck-boost anahtarlamasının SX1262 alıcı hassasiyetine etkisi (nicel) | K-5 / A-8 | Kendi ölçümümüz (Test T-06) |
| AR-3 | E-paper (SSD1680) −10 °C yenileme süresi ve kontrastı | HW-DSP-6 | Kendi ölçümümüz (Test T-14) |
| AR-4 | Elde/çantada taşınan cihaz için düşme algılama eşikleri | Literatürde yok (L-26/27 gövdeye sabit) | Saha verisi toplama — projenin özgün katkısı |
| AR-5 | nRF54L15 port'unun güç ölçümleri | v2 platform kararı | Meshtastic topluluk ölçümleri yayınlandıkça |
| AR-6 | RAK4630'un 32,768 kHz kristal içerip içermediği | HW-MCU-7 | RAK teknik destek / modül şeması |
