# Nexus Audio Platform (AI-Driven & Secure MVP)

Nexus Audio Platform, modern WebRTC şifreleme katmanları ile Socket.io tabanlı merkezî sinyalleşme ve yapay zeka analiz motorlarını bir araya getiren siber güvenlik ve yapay zeka odaklı gerçek zamanlı bir sesli sohbet platformudur. 

Bu depo (repository), hafif ve güvenli bir Dockerize Node.js sinyalleşme sunucusu ile asenkron emniyet kilitlerine sahip çok platformlu bir Flutter istemcisinin kaynak kodlarını barındırmaktadır.

---

## 🚀 Öne Çıkan Mimari Bileşenler

Platformun çekirdek iş mantığı ve tamamlanan mühendislik işleri üç ana katmanda koordine edilmiştir:

1. **İstemci Katmanı (Flutter & Dart):** `main.dart` giriş noktasından itibaren koyu mod (`ThemeData.dark()`) optimizasyonları yapılmış, web ve mobil platformlarda kilitlenmeleri önleyen asenkron yapılarla donatılmış esnek arayüz mimarisi.
2. **Sinyalleşme ve İşleme Sunucusu (Node.js & Express):** Odaları bellek içi `Map` yapılarında izole eden, Winston ile loglama gerçekleştiren ve tüm analitik süreçleri yöneten merkezî sunucu altyapısı.
3. **DevOps ve Proxy Katmanı (Docker & Caddy):** Gelişmiş sıkıştırma protokolleri barındıran, root yetkilerini sınırlandırarak siber riskleri minimize eden konteynerizasyon sistemi.

---

## 🛠️ Yapılan İşlerin Teknik Açıklamaları (Fonksiyonel Süreçler)

### 1. Karşılama ve Animasyon Altyapısı (Splash Katmanı)
Uygulamanın asenkron başlatma süreçlerini (bellek optimizasyonları, soket ön hazırlıkları) yöneten bir ön katman tasarlandı. Donanım kaynaklarını tüketmeyen ve arayüz akıcılığını bozmayan hafif bir `SingleTickerProviderStateMixin` mimarisi kuruldu. Bu yapı üzerinden, ses dalgalarını simüle eden 2 saniyelik dinamik bir nabız (pulse) animasyonu kodlandı.

### 2. Akıllı Donanım ve Erişim Denetimi (İzin Paneli)
Uygulamanın çalışması için kritik öneme sahip olan mikrofon erişimi, `permission_handler` kütüphanesiyle asenkron bir boru hattına (`_checkInitialPermissions`) bağlandı. Kullanıcıyı doğrudan sistem diyaloğuyla boğmamak adına, işletim sistemi düzeyindeki izin durumunu arka planda sorgulayan kontrol mekanizması tamamlandı. İzin verilmediği senaryolarda devreye giren ve kullanıcıya uzamsal ses filtreleri ile akıllı gürültü engelleme (ANC) süreçlerini açıklayan bilgilendirici bir onay katmanı (`AnimatedSwitcher`) arayüze entegre edildi.

### 3. Tekli Canlı Oda Bağlantısı ve Ses Yoğunluğu Takibi
`SocketService` üzerinden merkezî sinyalleşme sunucusuna güvenli bir şekilde `join-room` isteği gönderilerek canlı oda bağlantısı sağlandı. Kullanıcının lokal mikrofonundan alınan ses akışı, gerçek zamanlı desibel seviyesi analizine bağlandı. Bu ses yoğunluğuna (`voiceLevel`) bağlı olarak dinamik parçacıklar üreten bir sistem simüle edildi. Konuşma esnasında ses eşiği yükseldikçe karakterin dikey eksende hareket etmesi sağlanarak, sesin sisteme başarıyla ulaştığına dair kullanıcıya oyunlaştırılmış bir görsel geri bildirim mekanizması sunuldu. Medya akışındaki ses hatlarının asenkron olarak kapatılıp açılabilmesi (`_toggleMute`) ve fısıltı odası geçişleri kontrol altına alındı.

### 4. Canlı Duygu Durumu ve Ses Analytics Veri Akışı
İstemci tarafında `Timer.periodic` ile her 150 milisaniyede bir mikrofondan örnekleme alan bir lokal motor simülasyonu kuruldu. Bu motor, kullanıcının ses dalgalarını analiz ederek duygu durumunu belirlemektedir. Yakalanan veriler `voice-analytics-update` soket olayı üzerinden sunucuya iletilmekte, sunucu ise odadaki diğer eşleri anlık olarak besleyerek herkesin duygu durumunun (*Enerjik ⚡, Gergin/Yüksek 🔥 veya Analiz Ediliyor...*) gecikmesiz olarak listelenmesini (Peer Analytics Management) sağlamaktadır.

### 5. İzole ve Güvenli Fısıltı Modu (Kriptografik Ayrıştırma)
Odadaki genel ses akışından tamamen bağımsız, seçilen hedef partnerlerle birebir şifreli görüşme yapılabilmesini sağlayan **"Secure Whisper"** mimarisi geliştirildi. Kullanıcı "Güvenli Fısıltı Aç" butonuna bastığı an, sunucu tarafında `start-whisper` alt grubu oluşturulmaktadır. Genel WebRTC havuzundan izole edilen ve sadece bu fısıltı kanalına ait olan kriptografik `_whisperConnections` ses köprüsü kurulmaktadır. Fısıltı modu aktif olduğunda, kullanıcının gizli siber kanalda olduğunu hatırlatmak amacıyla ekran sınırlarına parıltılı pembe renkli görsel sınırlar eklenmiştir.

### 6. Chrome Uyumlu STT Motoru ve Canlı Çeviri Katmanı
Web ve Chrome tarayıcılarında ses motorunun donanımı boşa çıkaramaması veya döngüsel çakışmalara girmesini engellemek amacıyla `_sttLock` adında özel bir boolean emniyet kilidi algoritması geliştirildi. `speech_to_text` kütüphanesiyle entegre çalışan bu kilit sayesinde, ses yakalama işlemi tamamlandığında donanım güvenli bir şekilde serbest bırakılmakta ve konuşmalar anlık olarak metne (`audio-transcription`) dönüştürülmektedir. Sunucu katmanına iletilen bu transkripsiyonlar, seçilen hedef dile göre (TR, EN, DE, RU) işlenerek odadaki tüm istemcilere canlı alt yazı (`live-subtitle`) olarak basılmaktadır.

### 7. Siber Güvenlik ve Donanım Kimliği Doğrulama (HardwareDNA)
Sinyalleşme sunucusuna sızma girişimlerini engellemek adına cihaz bazlı kimlik doğrulama katmanı kodlandı. Her istemci odaya dahil olurken milisaniye doğruluğunda ve rastgele tuzlama (salt) algoritmasıyla benzersiz bir `hardwareDNA` imzası üretir. `index.js` katmanında yapılan kontrolde, bu imzaya sahip olmayan veya boş gelen tüm soket istekleri `access-denied` olayını tetikler ve sunucu bağlantısı merkezî algoritma tarafından derhal sonlandırılır.

### 8. Regex Tabanlı Yapay Zeka Görev Çıkarıcı
Sohbet esnasında dönen konuşmalar sunucu tarafında anlık olarak gelişmiş bir Düzenli İfade (`taskRegex`) süzgecine sokulur. Cümle içinde gelecek zaman eki barındıran ("edecek", "yapacak", "hazırlayacak") iş bildirimleri ve cümlenin başındaki ilk kelime (sorumlu kişi) otomatik yakalanır. Sunucu `action-item-extracted` olayı ile odaya görevi basar ve eşzamanlı olarak istemci cihazlarda ağır dokunsal geri bildirim (`HapticFeedback.heavyImpact()`) tetiklenerek iş takibi otomatize edilir.

### 9. Akıllı Yapay Zeka Moderatörü ve Otomatik ASMR Koruması
Oda içerisindeki ses yoğunluğu (intensity) ve duygu durum analizleri arka planda sunucu tarafından sürekli puanlanır. Eğer odadaki ardışık ses paketlerinde gürültü eşiği `0.85` değerini aşarsa ve duygu durumlarında "Gergin" ibaresi yakalanırsa, oda tension seviyesi artırılır. Eşik 3 kez üst üste aşıldığında AI Moderatör odaya müdahale eder; arayüz rengini sakinleştirici Teal tonuna çeker ve stresi azaltmak amacıyla arka planda otomatik koruyucu ASMR frekans yayınını başlatır.

### 10. Çok Aşamalı Güvenli Docker Yapısı (Multi-Stage Build)
Uygulamanın sunucu katmanı, imaj boyutunu küçültmek ve siber saldırı yüzeyini minimuma indirmek için `node:20-alpine` tabanlı çok aşamalı (Multi-Stage) mimariyle derlendi. Bağımlılıklar `builder` aşamasında yalıtılmış olarak kurulup sadece gerekli üretim dosyaları son imaja aktarıldı. Konteyner içi güvenlik ihlallerini önlemek adına varsayılan `root` yetkileri tamamen terk edilerek, kısıtlı haklara sahip `USER node` profiline geçiş yapıldı.

### 11. Caddy Tersine Vekil ve Otomatik Performans Sıkıştırması
Canlı ortam dağıtımında `api.seslisohbetmvp.com` trafiğini yönetmek üzere Caddy sunucusu entegre edildi. Socket.io bağlantılarının kararlı çalışması için gerekli WebSocket header kurguları (`Host`, `X-Real-IP`) otomatik boru hattına bağlandı. Gerçek zamanlı ses paketlerinin ağ darboğazlarına takılmasını engellemek ve gecikme (latency) sürelerini düşürmek amacıyla Caddy katmanında `gzip` ve `zstd` veri sıkıştırma algoritmaları aktif edildi.

---

## 🛠️ Proje Bağımlılık Matrisi

| Paket Adı | Katman | Görev / Sorumluluk |
| :--- | :--- | :--- |
| `express` | Backend | HTTP servis altyapısı ve API yönlendirmeleri. |
| `socket.io` | Backend | Gerçek zamanlı çift yönlü sinyalleşme ve oda yönetimi. |
| `winston` | Backend | Sistem olaylarının ve güvenlik ihlallerinin loglanması. |
| `flutter_webrtc` | Frontend | Eşler arası doğrudan şifreli ses medya akışı. |
| `speech_to_text` | Frontend | Canlı ses verilerinin lokalde metne dönüştürülmesi. |
| `permission_handler` | Frontend | Cihaz donanım yetkilerinin asenkron denetimi. |

---

## Görseller
<img width="1118" height="629" alt="image" src="https://github.com/user-attachments/assets/7e9a6caa-cfd0-45e0-aa8f-9e540435baea" />
<img width="1120" height="628" alt="image" src="https://github.com/user-attachments/assets/2047df91-8a62-4edf-a86a-31ee1a790018" />
<img width="1119" height="625" alt="image" src="https://github.com/user-attachments/assets/1ec2b6dd-92f5-4613-a38b-969cb74267ed" />
<img width="1118" height="627" alt="image" src="https://github.com/user-attachments/assets/791b3f0d-7d3b-4f11-81f0-4523befefb4f" />
<img width="1117" height="633" alt="image" src="https://github.com/user-attachments/assets/46f2e1df-2f40-44d8-ad8b-062f4c665265" />
<img width="1118" height="626" alt="image" src="https://github.com/user-attachments/assets/6fa38d5c-5307-4894-996e-f20d7ff4847d" />


Nexus Audio Platform MVP Teknik Şartname ve Özellik Analiz Raporu © 2026. Tüm Hakları Saklıdır.
