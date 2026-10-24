# Proje Kararları

Bu doküman, **Automated Gherkin and BDD Test Scenario Generator** projesi için alınan kararları, henüz verilmemiş kararları ve açık soruları takip eder.

Temel referans: [Automated Gherkin and BDD Test Scenario Generator.md](Automated%20Gherkin%20and%20BDD%20Test%20Scenario%20Generator.md)
Örnek girdi: [Jira Backlog Örneği.csv](Jira%20Backlog%20%C3%96rne%C4%9Fi.csv)

- Son güncelleme: 2026-10-24
- Durum etiketleri: **Kesinleşti**, **Kısmen karar verildi**, **Karar bekliyor**

---

## 1. Kararlar Özeti

| ID | Konu | Karar | Durum |
|----|------|-------|-------|
| D-01 | Programlama dili | Java | Kesinleşti |
| D-02 | Kullanıcı arayüzü | Masaüstü mı web mi, henüz seçilmedi | Karar bekliyor |
| D-03 | Girdi kaynağı | Jira CSV'de yalnızca **Issue Type** ve **Summary** sütunları kullanılacak | Kısmen karar verildi |
| D-04 | Yapay zeka entegrasyonu | Üretilen Gherkin'i doğrulamak için opsiyonel bir AI bileşeni (örnek amaçlı) | Kısmen karar verildi |
| D-05 | Veri saklama ve kullanıcı sistemi | Hafif veritabanı, login ve kullanıcı geçmişi | Kısmen karar verildi |
| D-06 | Spesifikasyon bağlılığı | Referans dokümandaki gereksinimlere ve tasarım kalıplarına sadık kalınacak | Kesinleşti |

---

## 2. Kararların Detayı

### D-01: Programlama Dili

- **Karar:** Proje Java ile geliştirilecek.
- **Durum:** Kesinleşti.
- **Etkisi:** Derleme aracı, bağımlılık yönetimi ve test çatısı için Java ekosistemi kullanılacak. Hangi araçların kullanılacağı henüz belirlenmedi (bkz. A-02).

---

### D-02: Kullanıcı Arayüzü

- **Karar:** Henüz verilmedi. Masaüstü uygulama ya da web uygulaması olarak iki seçenek değerlendiriliyor.
- **Durum:** Karar bekliyor (bkz. A-01).
- **Notlar:**
  - D-05'teki login ve kullanıcı geçmişi gereksinimi, web uygulamasına doğal bir uyum sağlıyor. Bu bir ipucu, kesin bir gereksinim değil.
  - Arayüz seçimi, kullanılacak Java çatısını (framework) doğrudan etkiler.
- **Karar verilince güncellenecek alanlar:** D-02, D-05 içindeki kimlik doğrulama yaklaşımı, Java çatısı seçimi.

---

### D-03: Girdi Kaynağı (Jira CSV)

- **Karar:** Girdi olarak bir Jira CSV dosyası alınacak. Sistem bu dosyadan yalnızca iki sütunu okuyacak:
  - **Issue Type:** Hangi satırların user story olduğunu belirler.
  - **Summary:** User story metnini içerir ve parse edilecek kısım budur.
- **Durum:** Kısmen karar verildi. Kullanılacak sütunlar belirlendi. Epic'lerin işlenmesi, Description kullanımı ve format sınırları açık.
- **Örnek CSV'den gözlemler** (`Jira Backlog Örneği.csv`):
  - Toplam 16 satır: 3 **Epic** (UI, Shopping Cart, Checkout and Payment) ve 13 **Story** (KAN-10 – KAN-22).
  - Tüm Story'lerin **Summary** alanı, "As a [role] I want [capability] so that [benefit]." kalıbına yakın. Bununla birlikte 13 Story'nin 2'sinde (KAN-10, KAN-11) "so that" cümlesi yok.
  - **Description** sütunu örnekte tüm satırlarda boş. Bu yüzden ilk sürümde user story metni yalnızca Summary'den alınacak.
  - Export'ta **Parent / Epic bağlantısı** sütunu yok. Story'ler hangi Epic'e ait, bu dosyadan anlaşılamıyor.
  - Dosyanın sonunda boş bir satır var. Parser bunu hata olarak değil, boş satır olarak ele almalı.
- **Referans:** Spesifikasyondaki "Requirement Input" ve "Requirement Parsing" bölümleri. Jira kaynağı, "Standard user story format" girdisine karşılık gelir.
- **Olası parser yapısı:** Spesifikasyonun Adapter yaklaşımına uygun olarak `JiraCsvAdapter` ve ilgili parser. Bu, D-06 ile uyumlu.

---

### D-04: Yapay Zeka Entegrasyonu

- **Karar:** Projeye küçük bir yapay zeka bileşeni gömülecek. Bu, spesifikasyonun "Extensibility" (3.11) bölümündeki "AI-assisted requirement interpretation" ve "Scenario quality analysis" genişletmelerinden biridir.
- **Kullanım alanı (örnek):** Üretilen Gherkin senaryolarının içeriğini doğrulamak. Yapay zeka, senaryo üretim hattının **ana parçası değildir**. Üretilen metni kontrol eden isteğe bağlı bir adımdır.
- **Durum:** Kısmen karar verildi. Kullanım alanı bir örnek olarak belirlendi, kesinleşmedi (bkz. A-05).
- **Tasarım ilkeleri:**
  - AI bileşeni bir arayüz (interface) arkasında tutulacak. Böylece model ya da sağlayıcı değiştirilebilir.
  - AI kapalı olduğunda sistem AI olmadan tam çalışmalıdır.
  - Yapay zeka çıktısı, kural tabanlı doğrulamanın yerine geçmez. Ek bir kalite katmanı olarak çalışır.

---

### D-05: Veri Saklama ve Kullanıcı Sistemi

- **Karar:** Veriler bir veritabanında kalıcı olarak saklanacak. Uygulamaya bir login sistemi eklenecek ve kullanıcıların geçmiş (history) kayıtları takip edilecek.
- **Veritabanı:** Hafif bir veritabanı yeterlidir. Öneri **SQLite**.
  - Not: "LiteSQL" adıyla bilinen ayrı bir kütüphane de var (C++ tabanlı bir ORM). Java tarafında yaygın olan seçenek SQLite.
- **Saklanacak veriler (spesifikasyondan):** Requirement'lar, requirement formatı, parse edilmiş temsil (AST), üretilen feature'lar, senaryolar ve üretim meta verisi. Requirement ile senaryo arasındaki ilişki açıkça modellenecek.
- **Durum:** Kısmen karar verildi. Veritabanı türü ve kullanıcı geçmişi kapsamı belirlendi. Kimlik doğrulama yöntemi ve şema ayrıntıları karar bekliyor (bkz. A-07, A-08).
- **Kapsam notu:** Spesifikasyon, veritabanını basit bir uygulama için "optional", geçmiş tutulacaksa "recommended" olarak tanımlar. Bu proje geçmiş tuttuğu için veritabanı kullanılacak.

---

### D-06: Spesifikasyon Bağlılığı

- **Karar:** Referans dokümandaki gereksinimler esas alınacak. Projenin kapsamı ve tasarımı bu dokümana sadık kalacak.
- **Zorunlu tasarım kalıpları (spesifikasyondan):**
  - **Chain of Responsibility:** Doğrulama ve işleme adımları için (Syntax Validator → Tokenizer → Structural Validator → AST Builder → Scenario Builder).
  - **Adapter:** Girdi formatlarını ortak bir `RequirementModel`'e çevirmek için.
  - **Builder:** Karmaşık `GherkinScenario` nesnelerini oluşturmak için.
- **Zorunlu katman ayrımı:** Arayüz, requirement işleme hattı, alan modeli (domain), senaryo üretimi ve Gherkin formatlayıcı birbirinden ayrı tutulacak.
- **Durum:** Kesinleşti.

---

## 3. Açık Sorular

Bu liste, karar verildikçe ilgili kararlara taşınacak ve buradan kaldırılacaktır. Kaldırılan ID'ler yeniden kullanılmaz.

| ID | Soru | İlgili karar | Öneri |
|----|------|--------------|-------|
| A-01 | Masaüstü mı web mi? | D-02 | Login ve geçmiş gereksinimi için web |
| A-02 | Hangi Java çatısı (framework) ve derleme aracı? (örn. Maven/Gradle, Spring Boot/JavaFX) | D-01, D-02 | A-01'e bağlı |
| A-04 | Epic satırları ne olacak? Örnekte 3 Epic var (UI, Shopping Cart, Checkout and Payment). Senaryo üretiminde atlansın mı, yoksa Feature adı olarak mı kullanılsın? | D-03 | Atla. Bağlantı sütunu gelirse Feature adı olarak kullan |
| A-05 | AI bileşeninin kullanım amacı kesinleşecek mi? Doğrulama mı, başka bir kullanım mı? | D-04 | Doğrulama (örnekte olduğu gibi) |
| A-06 | Hangi AI sağlayıcısı? Ağ bağlantısı ve API maliyeti kabul edilebilir mi? | D-04 | Sağlayıcıdan bağımsız arayüz, varsayılan kapalı |
| A-07 | Kimlik doğrulama nasıl yapılacak? (Kendi kullanıcı tablosu, şifre hash'leme, oturum yönetimi) | D-05 | Kendi kullanıcı tablosu, hash'li şifre |
| A-08 | Veritabanı erişim katmanı: JDBC mi, ORM mi? | D-05 | A-02'ye bağlı |
| A-09 | Spesifikasyondaki EARS formatı bu sürümde desteklenecek mi? | D-03, D-06 | Hayır, ilk sürümde yalnızca Jira user story |
| A-10 | Üretilen Gherkin dosyaları nasıl dışa aktarılacak? (Kopyalama, `.feature` dosyası indirme) | D-02 | A-01'e bağlı |
| A-11 | Summary'de "so that" bölümü olmayan story'ler (örnekte KAN-10 ve KAN-11) nasıl işlenecek? Hata mı, uyarı mı, eksik alan olarak kabul mi? | D-03 | Uyarı ver, "so that" olmadan da senaryo üret |
| A-12 | Description sütunu ilk sürümde hiç kullanılmayacak mı, yoksa Summary'deki bilgiyi tamamlamak için kullanılacak mı? | D-03 | İlk sürümde kullanma, sonra ekle |
| A-13 | Jira CSV'nin kodlaması ve çok satırlı (tırnak içinde yeni satır içeren) alanlar nasıl ele alınacak? Örnek dosyanın kodlamasını doğrula. | D-03 | Standart CSV kütüphanesi kullan (tırnaklı alanları doğru okur) |
| A-14 | Girdi dosyasında İngilizce dışı Summary olursa ne olacak? | D-03 | İlk sürümde yalnızca İngilizce, kural dışı satırlar uyarı ile atlansın |

---

## 4. Kapsam Dışı (Şimdilik)

- Spesifikasyondaki diğer genişletme önerileri (Other BDD formats, alternative test-generation targets, scenario quality analysis) ilk sürüme dahil değildir.
- Birden fazla kullanıcı için rol tabanlı yetkilendirme.
- EARS formatı ve diğer girdi formatları (A-09 karara bağlanana kadar).

---

## 5. Bu Dokümanın Güncellenme Kuralı

- Bir karar verildiğinde ilgili bölümün **Durum** alanı güncellenir ve **Açık Sorular** tablosundan ilgili madde kaldırılır.
- Her güncelleme, aşağıdaki Değişiklik Günlüğü'ne bir satır olarak eklenir.
- Her değişiklik, ayrı bir commit ile GitHub'a gönderilir (örn. `Karar güncellemesi: A-01 web seçildi`).

### Değişiklik Günlüğü

| Tarih | Değişiklik |
|-------|------------|
| 2026-10-24 | İlk sürüm. D-01 ile D-06 arasındaki kararlar ve açık sorular eklendi. |
| 2026-10-24 | D-03 güncellendi: girdi sütunları Issue Type ve Summary olarak belirlendi. Örnek CSV'den gözlemler eklendi. A-03 karara bağlandığı için kaldırıldı. A-11 – A-14 eklendi, A-04 ve A-09 güncellendi. |
