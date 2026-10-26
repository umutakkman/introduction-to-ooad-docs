# Proje Kararları

Bu doküman, **Automated Gherkin and BDD Test Scenario Generator** projesi için alınan kararları, henüz verilmemiş kararları ve açık soruları takip eder.

Temel referans: [Automated Gherkin and BDD Test Scenario Generator.md](Automated%20Gherkin%20and%20BDD%20Test%20Scenario%20Generator.md)
Gereksinim dokümanı: [CSE443 - RAD_Template.docx](CSE443%20-%20RAD_Template.docx) (FR-1 – FR-38, NFR-1 – NFR-20)
Örnek girdi: [Jira Backlog Örneği.csv](Jira%20Backlog%20%C3%96rne%C4%9Fi.csv)

- Son güncelleme: 2026-10-26
- Durum etiketleri: **Kesinleşti**, **Kısmen karar verildi**, **Karar bekliyor**

---

## 1. Kararlar Özeti

| ID | Konu | Karar | Durum |
|----|------|-------|-------|
| D-01 | Programlama dili | Java | Kesinleşti |
| D-02 | Kullanıcı arayüzü | Masaüstü mü web mi, henüz seçilmedi | Karar bekliyor |
| D-03 | Girdi kaynağı | Jira CSV'de **Issue Type** ve **Summary** sütunları zorunlu, **Issue key** sütunu opsiyonel olarak okunacak. Summary, "As a…" kalıbında ya da EARS kalıbında olabilir | Kısmen karar verildi |
| D-04 | Yapay zeka entegrasyonu | Üretilen Gherkin'i doğrulamak için opsiyonel bir AI bileşeni (örnek amaçlı). Varsayılan olarak kapalı | Kısmen karar verildi |
| D-05 | Veri saklama ve kullanıcı sistemi | Gömülü hafif veritabanı, e-posta ve şifre ile login, hash'li şifre, kullanıcı geçmişi | Kısmen karar verildi |
| D-06 | Spesifikasyon bağlılığı | Referans dokümandaki gereksinimlere ve tasarım kalıplarına sadık kalınacak | Kesinleşti |
| D-07 | Kalite hedefleri (NFR) | Nonfunctional requirement'lar öğrenci demosu seviyesinde tutulacak | Kesinleşti |

---

## 2. Kararların Detayı

### D-01: Programlama Dili

- **Karar:** Proje Java ile geliştirilecek (RAD: NFR-11).
- **Durum:** Kesinleşti.
- **Etkisi:** Derleme aracı, bağımlılık yönetimi ve test çatısı için Java ekosistemi kullanılacak. Hangi araçların kullanılacağı henüz belirlenmedi (bkz. A-02).

---

### D-02: Kullanıcı Arayüzü

- **Karar:** Henüz verilmedi. Masaüstü uygulama ya da web uygulaması olarak iki seçenek değerlendiriliyor.
- **Durum:** Karar bekliyor (bkz. A-01).
- **Notlar:**
  - D-05'teki login ve kullanıcı geçmişi gereksinimi, web uygulamasına doğal bir uyum sağlıyor. Bu bir ipucu, kesin bir gereksinim değil.
  - Arayüz seçimi, kullanılacak Java çatısını (framework) doğrudan etkiler.
  - RAD'deki gereksinimler (FR, NFR) arayüzden bağımsız yazıldı. Arayüz seçilince NFR-1'deki "üç kullanıcı eylemi" ve dışa aktarma yöntemi netleştirilecek.
- **Karar verilince güncellenecek alanlar:** D-02, D-05 içindeki oturum yönetimi, Java çatısı seçimi, RAD 3.4.5 (arayüz taslakları).

---

### D-03: Girdi Kaynağı (Jira CSV)

- **Karar:** Girdi olarak bir Jira CSV dosyası alınacak. Sistem bu dosyadan üç sütunu okuyacak, bunlardan ikisi zorunlu, biri opsiyonel:
  - **Issue key (opsiyonel):** Örneğin `KAN-10`. Varsa üretilen senaryoların kaynak user story'sini dışa aktarılan `.feature` dosyalarında izlenebilir kılmak için tag olarak kullanılır (örn. `@KAN-10`). Sütun yoksa sistem yine çalışır ve kaynak user story'nin Summary metni, dışa aktarılan `.feature` dosyasında yorum satırı olarak (`# Source: <Summary>`) belirtilir (FR-8, FR-30, FR-31).
  - **Issue Type (zorunlu):** Hangi satırların user story olduğunu belirler (FR-9).
  - **Summary (zorunlu):** Requirement metnini içerir ve parse edilecek kısım budur. Metin iki formattan birinde olabilir: user story kalıbı ("As a [role] I want [capability] so that [benefit]") veya EARS kalıbı ("The system shall…", "When [trigger], the system shall…" vb.).
- **Terminoloji:** "User story" Jira'daki **Story** issue tipini ifade eder. Bu öğenin Summary metninin formatı (user story kalıbı ya da EARS) ayrı bir kavramdır. Genel girdi birimi için "requirement" terimi kullanılır.
- **CSV biçimi (NFR-14):** Alanlar virgülle ayrılır, metin alanları çift tırnak içinde olabilir, kodlama UTF-8'dir. Örnek dosya doğrulandı: geçerli UTF-8, BOM yok.
- **İşleme kuralları** (RAD'deki FR'lerden):
  - Issue Type'ı "Story" olmayan öğeler (Epic dahil) senaryo üretiminden hariç tutulur (FR-10). Epic'ler Feature adı olarak kullanılmaz, çünkü export'ta Story-Epic bağlantısı yok.
  - Summary'de "so that" bölümü yoksa uyarı verilir ve story işlenmeye devam edilir (FR-19).
  - Summary ne user story kalıbına ne de EARS'a uyuyorsa "unsupported" olarak işaretlenir ve kullanıcıya raporlanır (FR-16). İngilizce olmayan Summary'ler de bu kapsama girer.
  - Description sütunu ilk sürümde kullanılmaz. User story metni yalnızca Summary'den alınır.
  - Bir story işlenemezse dosyanın geri kalanı işlenmeye devam eder (NFR-3).
- **İzlenebilirlik (traceability):** Her üretilen senaryo, sistem içinde kaynak user story'ye bağlanır (FR-26). Bu bağlantı, import kaydındaki satıra iç referans olarak tutulur. Böylece Summary tekrar etse ya da Jira'da sonradan değişse bile her import kendi kaydını korur. Issue key, bu iç bağlantıyı dışa aktarılan dosyalara taşımak için kullanılır.
- **Durum:** Kısmen karar verildi. Sütunlar, CSV biçimi ve işleme kuralları belirlendi. Format tespit yöntemi (A-15) ve çok satırlı alanların ele alınışı (A-13) açık.
- **Örnek CSV'den gözlemler** (`Jira Backlog Örneği.csv`):
  - Toplam 16 satır: 3 **Epic** (UI, Shopping Cart, Checkout and Payment) ve 13 **Story** (KAN-10 – KAN-22). Her satırda **Issue key** dolu (KAN-7 – KAN-22).
  - Tüm Story'lerin **Summary** alanı, "As a [role] I want [capability] so that [benefit]." kalıbına yakın. Bununla birlikte 13 Story'nin 2'sinde (KAN-10, KAN-11) "so that" cümlesi yok. Örnekte henüz EARS formatında bir satır yok.
  - **Description** sütunu örnekte tüm satırlarda boş.
  - Export'ta **Parent / Epic bağlantısı** sütunu yok. Story'ler hangi Epic'e ait, bu dosyadan anlaşılamıyor.
  - Dosyanın sonunda boş bir satır var. Parser bunu hata olarak değil, boş satır olarak ele almalı.
- **Referans:** Spesifikasyondaki "Requirement Input" ve "Requirement Parsing" bölümleri. Spesifikasyon iki format ister: EARS tabanlı requirement'lar ve standart user story formatı. Jira Summary'leri ikisini de içerebilir.
- **Olası parser yapısı:** Spesifikasyonun Adapter yaklaşımına uygun olarak her format için ayrı parser ve adapter (spesifikasyondaki örnek adlarla `UserStoryAdapter` ve `EARSRequirementAdapter`). Jira CSV okuma işi bunlardan ayrı tutulur. Bu, D-06 ile uyumlu.
- **Format ayrımı:** Summary'nin hangi formatta olduğunun nasıl belirleneceği (örneğin "As a" ile başlıyorsa user story, "shall" içeriyorsa EARS) henüz belirlenmedi (bkz. A-15).

---

### D-04: Yapay Zeka Entegrasyonu

- **Karar:** Projeye küçük bir yapay zeka bileşeni gömülecek. Bu, spesifikasyonun "Extensibility" (3.11) bölümündeki "AI-assisted requirement interpretation" ve "Scenario quality analysis" genişletmelerinden biridir.
- **Kullanım alanı (örnek):** Üretilen Gherkin senaryolarının içeriğini doğrulamak. Yapay zeka, senaryo üretim hattının **ana parçası değildir**. Üretilen metni kontrol eden isteğe bağlı bir adımdır (FR-37).
- **Durum:** Kısmen karar verildi. Kullanım alanı bir örnek olarak belirlendi, kesinleşmedi (bkz. A-05). Sağlayıcı seçimi açık (bkz. A-06).
- **Tasarım ilkeleri:**
  - AI bileşeni tek bir arayüz (interface) arkasında tutulacak. Böylece model ya da sağlayıcı değiştirilebilir (NFR-16).
  - AI varsayılan yapılandırmada **kapalıdır** (NFR-18). Kapalıyken AI bileşenine hiçbir veri gönderilmez (NFR-20).
  - AI bileşeni kullanılamıyorsa ya da hata verirse üretim AI doğrulaması olmadan tamamlanır ve kullanıcı bilgilendirilir (FR-38).
  - Yapay zeka çıktısı, kural tabanlı doğrulamanın yerine geçmez. Ek bir kalite katmanı olarak çalışır.

---

### D-05: Veri Saklama ve Kullanıcı Sistemi

- **Karar:** Veriler bir veritabanında kalıcı olarak saklanacak. Uygulamaya bir login sistemi eklenecek ve kullanıcıların geçmiş (history) kayıtları takip edilecek (FR-33 – FR-36).
- **Veritabanı:** Ayrı bir sunucu gerektirmeyen, gömülü, hafif bir ilişkisel veritabanı (NFR-12). Öneri **SQLite**.
  - Not: "LiteSQL" adıyla bilinen ayrı bir kütüphane de var (C++ tabanlı bir ORM). Java tarafında yaygın olan seçenek SQLite.
- **Kullanıcı sistemi:**
  - Kullanıcı e-posta adresi ve şifre ile kayıt olur (FR-1, FR-2).
  - Şifreler yalnızca hash'li saklanır (NFR-6).
  - Kişisel veri olarak yalnızca e-posta adresi ve şifre hash'i saklanır (NFR-19).
  - Bir kullanıcının geçmişi başka kullanıcıya gösterilmez (FR-36). Geçmiş, uygulama yeniden başlatıldığında korunur (NFR-5).
- **Saklanacak veriler (spesifikasyondan):** Requirement'lar, requirement formatı, parse edilmiş temsil (AST), üretilen feature'lar, senaryolar ve üretim meta verisi. Requirement ile senaryo arasındaki ilişki açıkça modellenecek. Issue key (varsa) requirement kaydıyla birlikte saklanır.
- **Durum:** Kısmen karar verildi. Veritabanı türü, kullanıcı geçmişi kapsamı ve kullanıcı sistemi kuralları belirlendi. Oturum yönetimi, hash algoritması ve şema ayrıntıları karar bekliyor (bkz. A-07, A-08).
- **Kapsam notu:** Spesifikasyon, veritabanını basit bir uygulama için "optional", geçmiş tutulacaksa "recommended" olarak tanımlar. Bu proje geçmiş tuttuğu için veritabanı kullanılacak.

---

### D-06: Spesifikasyon Bağlılığı

- **Karar:** Referans dokümandaki gereksinimler esas alınacak. Projenin kapsamı ve tasarımı bu dokümana sadık kalacak.
- **Zorunlu tasarım kalıpları (spesifikasyondan, RAD: NFR-13):**
  - **Chain of Responsibility:** Doğrulama ve işleme adımları için (Syntax Validator → Tokenizer → Structural Validator → AST Builder → Scenario Builder).
  - **Adapter:** Girdi formatlarını ortak bir `RequirementModel`'e çevirmek için.
  - **Builder:** Karmaşık `GherkinScenario` nesnelerini oluşturmak için.
- **Zorunlu katman ayrımı (RAD: NFR-10):** Arayüz, requirement işleme hattı, alan modeli (domain), senaryo üretimi ve Gherkin formatlayıcı birbirinden ayrı tutulacak.
- **Durum:** Kesinleşti.

---

### D-07: Kalite Hedefleri (NFR)

- **Karar:** RAD'deki nonfunctional requirement'lar (NFR-1 – NFR-20) öğrenci demosu seviyesinde tutulacak. Hedef değerler gerçekçi ve zorlamayan düzeyde belirlendi.
- **Belirlenen hedefler:**
  - **Performans:** 100 öğeye kadar bir CSV dosyası, AI doğrulaması hariç 10 saniye içinde işlenir (NFR-7).
  - **Kullanılabilirlik:** Giriş yapmış kullanıcı en fazla üç eylemle CSV yükleyip sonucu görür (NFR-1). Arayüz metinleri İngilizcedir (NFR-2).
  - **Güvenilirlik:** Bir story'nin hatası dosyanın geri kalanını durdurmaz (NFR-3). Beklenmeyen hata durumunda uygulama kullanılabilir kalır (NFR-4).
  - **Genişletilebilirlik:** Yeni requirement formatı, yeni parser ve adapter yazılarak eklenir (NFR-8). Yeni doğrulama kuralı mevcut kuralları değiştirmeden eklenir (NFR-9).
  - **Teslim:** Sistem, kaynak kodu, dokümantasyonu ve build/çalıştırma talimatlarını içeren bir GitHub reposu olarak teslim edilir (NFR-17).
- **Kapsam dışı bırakılanlar:** Eşzamanlı kullanıcı sayısı hedefi, erişilebilirlik standartları (WCAG), çoklu dil desteği, yüksek erişilebilirlik (uptime) hedefleri.
- **Durum:** Kesinleşti. Hedef değerler ilerleyen aşamalarda gözden geçirilebilir.

---

## 3. Açık Sorular

Bu liste, karar verildikçe ilgili kararlara taşınacak ve buradan kaldırılacaktır. Kaldırılan ID'ler yeniden kullanılmaz.

| ID | Soru | İlgili karar | Öneri |
|----|------|--------------|-------|
| A-01 | Masaüstü mü web mi? | D-02 | Login ve geçmiş gereksinimi için web |
| A-02 | Hangi Java çatısı (framework) ve derleme aracı? (örn. Maven/Gradle, Spring Boot/JavaFX) | D-01, D-02 | A-01'e bağlı |
| A-05 | AI bileşeninin kullanım amacı kesinleşecek mi? Doğrulama mı, başka bir kullanım mı? | D-04 | Doğrulama (örnekte olduğu gibi) |
| A-06 | Hangi AI sağlayıcısı kullanılacak? Ağ bağlantısı ve API maliyeti kabul edilebilir mi? | D-04 | Sağlayıcı arayüzün arkasında kalır, seçim ileriye bırakılabilir |
| A-07 | Oturum yönetimi ve şifre hash algoritması nasıl olacak? | D-05 | Yerleşik bir parola hash algoritması (örn. bcrypt). Oturum yönetimi A-02'ye bağlı |
| A-08 | Veritabanı erişim katmanı: JDBC mi, ORM mi? | D-05 | A-02'ye bağlı |
| A-10 | Dışa aktarılan `.feature` dosyası kullanıcıya nasıl sunulacak? (Web'de indirme, masaüstünde kaydetme penceresi). Kopyalama ve dışa aktarma FR-28 ve FR-29 ile desteklenecek. | D-02 | A-01'e bağlı |
| A-13 | Tırnak içinde yeni satır içeren çok satırlı alanlar nasıl ele alınacak? (Kodlama UTF-8 olarak belirlendi.) | D-03 | Standart CSV kütüphanesi kullan (tırnaklı alanları doğru okur) |
| A-15 | Summary'nin formatı (user story kalıbı mı EARS mı) nasıl tespit edilecek? | D-03 | Önce kalıp eşleştirme ile tespit et, uymayanı "unsupported" olarak raporla (FR-16) |

---

## 4. Kapsam Dışı (Şimdilik)

- Spesifikasyondaki diğer genişletme önerileri (Other BDD formats, alternative test-generation targets, scenario quality analysis) ilk sürüme dahil değildir.
- Birden fazla kullanıcı için rol tabanlı yetkilendirme.
- User story kalıbı ve EARS dışındaki girdi formatları.
- Jira Description sütununun parse edilmesi.
- Epic'lerin Feature adı olarak kullanılması (Story-Epic bağlantısı export'ta bulunmadığı için).
- İngilizce dışındaki Summary'lerin işlenmesi. Bunlar "unsupported" olarak raporlanır.

---

## 5. Bu Dokümanın Güncellenme Kuralı

- Bir karar verildiğinde ilgili bölümün **Durum** alanı güncellenir ve **Açık Sorular** tablosundan ilgili madde kaldırılır.
- Her güncelleme, aşağıdaki Değişiklik Günlüğü'ne bir satır olarak eklenir.
- Her değişiklik, ayrı bir commit ile GitHub'a gönderilir (örn. `Karar güncellemesi: A-01 web seçildi`).

### Değişiklik Günlüğü

| Tarih | Değişiklik |
|-------|------------|
| 2026-10-24 | İlk sürüm. D-01 ile D-06 arasındaki kararlar ve açık sorular eklendi. |
| 2026-10-24 | D-03 güncellendi: Summary hem user story kalıbında hem EARS kalıbında olabilir, EARS desteklenecek. Terminoloji eklendi ("user story" = Jira Story issue tipi, "requirement" = genel girdi birimi). A-09 karara bağlandığı için kaldırıldı, A-15 eklendi. |
| 2026-10-24 | D-03 güncellendi: girdi sütunları Issue Type ve Summary olarak belirlendi. Örnek CSV'den gözlemler eklendi. A-03 karara bağlandığı için kaldırıldı. A-11 – A-14 eklendi, A-04 ve A-09 güncellendi. |
| 2026-10-26 | D-03 güncellendi: Issue key opsiyonel üçüncü sütun olarak eklendi, dışa aktarılan dosyalarda tag olarak kullanılacak. İç izlenebilirlik (senaryo → kaynak satır) açıklandı. D-05'e Issue key'in saklanması eklendi. |
| 2026-10-26 | D-03 güncellendi: Issue key yoksa kaynak Summary, dışa aktarılan dosyada yorum satırı olarak belirtilecek. A-16 karara bağlandığı için kaldırıldı. |
| 2026-10-26 | RAD'de 3.2 (FR-1 – FR-38) ve 3.3 (NFR-1 – NFR-20) yazıldığı için karar dokümanı RAD'le hizalandı. D-03'e CSV biçimi (UTF-8) ve işleme kuralları, D-04'e varsayılan kapalı AI, D-05'e kullanıcı sistemi kuralları eklendi. D-07 (kalite hedefleri) eklendi. A-04, A-11, A-12, A-14 karara bağlandığı için kaldırıldı. A-06, A-07, A-10, A-13 daraltıldı. Kapsam dışı listesi genişletildi. |
