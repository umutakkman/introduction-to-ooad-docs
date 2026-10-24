# Proje Kararları

Bu doküman, **Automated Gherkin and BDD Test Scenario Generator** projesi için alınan kararları, henüz verilmemiş kararları ve açık soruları takip eder.

Temel referans: [Automated Gherkin and BDD Test Scenario Generator.md](Automated%20Gherkin%20and%20BDD%20Test%20Scenario%20Generator.md)

- Son güncelleme: 2026-10-24
- Durum etiketleri: **Kesinleşti**, **Kısmen karar verildi**, **Karar bekliyor**

---

## 1. Kararlar Özeti

| ID | Konu | Karar | Durum |
|----|------|-------|-------|
| D-01 | Programlama dili | Java | Kesinleşti |
| D-02 | Kullanıcı arayüzü | Masaüstü mı web mi, henüz seçilmedi | Karar bekliyor |
| D-03 | Girdi kaynağı | Jira CSV çıktısının user story alanını parse etme | Kısmen karar verildi |
| D-04 | Yapay zeka entegrasyonu | Üretilen Gherkin'i doğrulamak için opsiyonel bir AI bileşeni (örnek amaçlı) | Kısmen karar verildi |
| D-05 | Veri saklama ve kullanıcı sistemi | Hafif veritabanı, login ve kullanıcı geçmişi | Kısmen karar verildi |
| D-06 | Spesifikasyon bağlılığı | Referans dokümandaki gereksinimlere ve tasarım kalıplarına sadık kalınacak | Kesinleşti |

---

## 2. Kararların Detayı

### D-01: Programlama Dili

- **Karar:** Proje Java ile geliştirilecek.
- **Durum:** Kesinleşti.
- **Etkisi:** Derleme aracı, bağımlılık yönetimi ve test çatısı için Java ekosistemi kullanılacak. Hangi araçların kullanılacağı henüz belirlenmedi (bkz. Açık Sorular).

---

### D-02: Kullanıcı Arayüzü

- **Karar:** Henüz verilmedi. Masaüstü uygulama ya da web uygulaması olarak iki seçenek değerlendiriliyor.
- **Durum:** Karar bekliyor.
- **Notlar:**
  - D-05'teki login ve kullanıcı geçmişi gereksinimi, web uygulamasına doğal bir uyum sağlıyor. Bu, karar için bir ipucu niteliğinde, kesin bir gereksinim değil.
  - Arayüz seçimi, kullanılacak Java çatısını (framework) doğrudan etkiler.
- **Karar verilince güncellenecek alanlar:** D-02 kararı, D-05 içindeki kimlik doğrulama yaklaşımı, Java çatısı seçimi.

---

### D-03: Girdi Kaynağı (Jira CSV)

- **Karar:** Girdi olarak bir Jira CSV dosyası alınacak ve bu dosyanın **user story** kısmı parse edilecek.
- **Durum:** Kısmen karar verildi. Ayrıntılar ve implementasyon henüz belirlenmedi, yalnızca fikir düzeyinde.
- **Referans:** Spesifikasyondaki "Requirement Input" ve "Requirement Parsing" bölümleri. Jira kaynağı, spesifikasyondaki "Standard user story format" girdisine karşılık gelir.
- **Olası parser yapısı:** Spesifikasyonun Adapter yaklaşımına uygun olarak Jira CSV için ayrı bir adapter (örneğin `JiraCsvAdapter`) ve parser yazılacak. Bu, D-06 ile uyumludur.
- **Açık noktalar:** Bkz. Açık Sorular, A-03 ve A-04.

---

### D-04: Yapay Zeka Entegrasyonu

- **Karar:** Projeye küçük bir yapay zeka bileşeni gömülecek. Bu, spesifikasyonun "Extensibility" (3.11) bölümündeki "AI-assisted requirement interpretation" ve "Scenario quality analysis" genişletmelerinden biridir.
- **Kullanım alanı (örnek):** Üretilen Gherkin senaryolarının içeriğini doğrulamak. Yapay zeka, senaryo üretim hattının **ana parçası değildir**. Üretilen metni kontrol eden isteğe bağlı bir adımdır.
- **Durum:** Kısmen karar verildi. Kullanım alanı bir örnek olarak belirlendi, kesinleşmedi. Bu amaç yerine başka bir kullanım da seçilebilir.
- **Tasarım ilkeleri:**
  - AI bileşeni bir arayüz (interface) arkasında tutulacak. Böylece model ya da sağlayıcı değiştirilebilir.
  - AI kapalı olduğunda sistem AI olmadan tam çalışmalıdır.
  - Yapay zeka çıktısı, kural tabanlı doğrulamanın yerine geçmez. Ek bir kalite katmanı olarak çalışır.
- **Açık noktalar:** Hangi sağlayıcı ve model kullanılacak, API anahtarı ve maliyet nasıl yönetilecek (bkz. A-06).

---

### D-05: Veri Saklama ve Kullanıcı Sistemi

- **Karar:** Veriler bir veritabanında kalıcı olarak saklanacak. Uygulamaya bir login sistemi eklenecek ve kullanıcıların geçmiş (history) kayıtları takip edilecek.
- **Veritabanı:** Hafif bir veritabanı yeterlidir. Mevcut öneri **SQLite**'tır.
  - Not: "LiteSQL" adıyla bilinen ayrı bir kütüphane de vardır (C++ tabanlı bir ORM). Java tarafında yaygın olan seçenek SQLite'tır. Kullanılacak kütüphane ve erişim katmanı (JDBC, ORM vb.) henüz belirlenmedi.
- **Saklanacak veriler (spesifikasyondan):** Requirement'lar, requirement formatı, parse edilmiş temsil (AST), üretilen feature'lar, senaryolar ve üretim meta verisi. Requirement ile senaryo arasındaki ilişki açıkça modellenecek.
- **Durum:** Kısmen karar verildi. Veritabanı türü ve kullanıcı geçmişi kapsamı belirlendi. Kimlik doğrulama yöntemi ve şema ayrıntıları karar bekliyor.
- **Kapsam notu:** Spesifikasyon, veritabanını basit bir uygulama için "optional", geçmiş tutulacaksa "recommended" olarak tanımlar. Bu proje geçmiş tuttuğu için veritabanı kullanılacak.

---

### D-06: Spesifikasyon Bağlılığı

- **Karar:** Referans dokümandaki gereksinimler esas alınacak. Projenin kapsamı ve tasarımı bu dokümana sadık kalacaktır.
- **Zorunlu tasarım kalıpları (spesifikasyondan):**
  - **Chain of Responsibility:** Doğrulama ve işleme adımları için (Syntax Validator → Tokenizer → Structural Validator → AST Builder → Scenario Builder).
  - **Adapter:** Girdi formatlarını ortak bir `RequirementModel`'e çevirmek için.
  - **Builder:** Karmaşık `GherkinScenario` nesnelerini oluşturmak için.
- **Zorunlu katman ayrımı:** Arayüz, requirement işleme hattı, alan modeli (domain), senaryo üretimi ve Gherkin formatlayıcı birbirinden ayrı tutulacak.
- **Durum:** Kesinleşti.

---

## 3. Açık Sorular

Bu liste, karar verildikçe ilgili kararlara taşınacak ve buradan kaldırılacaktır.

| ID | Soru | İlgili karar |
|----|------|--------------|
| A-01 | Masaüstü mı web mi? | D-02 |
| A-02 | Hangi Java çatısı (framework) ve derleme aracı kullanılacak? (örn. Maven/Gradle, Spring Boot/JavaFX) | D-01, D-02 |
| A-03 | Jira CSV'nin hangi sütunu user story olarak kabul edilecek? Dosya formatı ve alan adları nasıl belirlenecek? | D-03 |
| A-04 | Jira'da bir user story birden fazla satıra veya alt göreve yayılırsa nasıl işlenecek? | D-03 |
| A-05 | AI bileşeninin kullanım amacı kesinleşecek mi? Doğrulama mı, başka bir kullanım mı? | D-04 |
| A-06 | Hangi AI sağlayıcısı kullanılacak? Ağ bağlantısı ve API maliyeti kabul edilebilir mi? | D-04 |
| A-07 | Kimlik doğrulama nasıl yapılacak? (Kendi kullanıcı tablosu, şifre hash'leme, oturum yönetimi) | D-05 |
| A-08 | Veritabanı şeması ve erişim katmanı: JDBC mi, ORM mi? | D-05 |
| A-09 | Spesifikasyonda geçen EARS formatı bu sürümde desteklenecek mi? | D-03, D-06 |
| A-10 | Üretilen Gherkin dosyaları nasıl dışa aktarılacak? (Kopyalama, `.feature` dosyası indirme) | D-02 |

---

## 4. Kapsam Dışı (Şimdilik)

Bu maddeler karar verilene kadar uygulanmayacaktır:

- Spesifikasyondaki diğer genişletme önerileri (Other BDD formats, alternative test-generation targets, scenario quality analysis) ilk sürüme dahil değildir.
- Birden fazla kullanıcı için rol tabanlı yetkilendirme.

---

## 5. Bu Dokümanın Güncellenme Kuralı

- Bir karar verildiğinde ilgili bölümün **Durum** alanı güncellenir ve **Açık Sorular** tablosundan ilgili madde kaldırılır.
- Her güncelleme, aşağıdaki Değişiklik Günlüğü'ne bir satır olarak eklenir.
- Her değişiklik, ayrı bir commit ile GitHub'a gönderilir (örn. `Karar güncellemesi: A-01 web seçildi`).

### Değişiklik Günlüğü

| Tarih | Değişiklik |
|-------|------------|
| 2026-10-24 | İlk sürüm. D-01 ile D-06 arasındaki kararlar ve açık sorular eklendi. |
