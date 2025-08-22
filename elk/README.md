# 🧩 ELK Stack + Beats Kurulum Rehberi

## 📑 İçindekiler
- [Önkoşullar](#önkoşullar)
- [Dizin Yapısı](#dizin-yapısı)
- [Hızlı Başlangıç (5 adım)](#hızlı-başlangıç-5-adım)
- [Bileşenlerin Yapılandırılması](#bileşenlerin-yapılandırılması)
  - [Elasticsearch ve Kullanıcılar (setup servisi)](#elasticsearch-ve-kullanıcılar-setup-servisi)
  - [Logstash pipeline](#logstash-pipeline)
  - [Filebeat (container)](#filebeat-container)
  - [Metricbeat (container)](#metricbeat-container)
  - [Packetbeat (container) - isteğe bağlı](#packetbeat-container---isteğe-bağlı)
  - [Heartbeat (container) - isteğe bağlı](#heartbeat-container---isteğe-bağlı)
- [Windows Host’tan Veri Göndermek (Opsiyonel)](#windows-hosttan-veri-göndermek-opsiyonel)
  - [Windows’taki klasörden log (Filebeat for Windows)](#windowstaki-klasörden-log-filebeat-for-windows)
  - [Windows sistem metric’leri (Metricbeat for Windows)](#windows-sistem-metricleri-metricbeat-for-windows)
  - [Windows Event Log (Winlogbeat)](#windows-event-log-winlogbeat)
- [Kibana’da Veri Görme ve Data View](#kibanada-veri-görme-ve-data-view)
- [Örnek Dashboard (CPU/RAM/Process)](#örnek-dashboard-cpuramprocess)
- [Test, Doğrulama ve Faydalı Komutlar](#test-doğrulama-ve-faydalı-komutlar)
- [Sık Karşılaşılan Sorunlar & Çözümler](#sık-karşılaşılan-sorunlar--çözümler)
- [Notlar ve Sınırlamalar](#notlar-ve-sınırlamalar)

---

## 🔧 Önkoşullar
- Docker ve Docker Compose kurulu olmalı.
- Linux/macOS/WSL2/Windows desteklenir.
- En az 4 GB RAM (tercihen 8 GB+).

## 📁 Dizin Yapısı
```
elk-stack/
├── docker-compose.yml
├── .env
├── logstash/
│   └── pipeline/
│       └── logstash.conf
├── filebeat/
│   └── filebeat.yml
├── metricbeat/
│   └── metricbeat.yml
├── setup/
│   └── users/
│       ├── roles.yml
│       ├── users.yml
│       └── users_roles.yml
```

## ⚡ Hızlı Başlangıç (5 adım)

1️⃣ Repoyu klonla:
```bash
git clone https://github.com/kullanici/elk-stack.git
cd elk-stack
```

2️⃣ `.env` dosyasını içindeki kullanıcı/şifreleri mevcut şlifre kurallarına uygun olacak şekilde tekrar ayarla.

3️⃣ Logstash pipeline ve Beats konfigürasyonlarını isteğine göre düzenle.

4️⃣ Stack’i başlat:
```bash
docker compose up -d
```

5️⃣ Kibana’yı aç:  
👉 http://localhost:5601

---

## ⚙️ Bileşenlerin Yapılandırılması

### Elasticsearch ve Kullanıcılar (setup servisi)
- `setup` servisi ilk çalıştırmada kullanıcıları, şifreleri ve rolleri ayarlar.

### Logstash pipeline
- `logstash/pipeline/logstash.conf` dosyasında input/filter/output ayarlanır.

### Filebeat (container)
- Container içinden `/logs` klasörü veya host’taki `D:/Logs` mount edilerek loglar gönderilir.

### Metricbeat (container)
- Docker içindeki container’ların CPU, memory, network metric’lerini toplar.

### Packetbeat (container) - isteğe bağlı
- Ağ trafiğini analiz eder.

### Heartbeat (container) - isteğe bağlı
- HTTP/TCP/ICMP endpoint’lerini periyodik olarak ping’ler.

---

## 🪟 Windows Host’tan Veri Göndermek (Opsiyonel)

### Windows’taki klasörden log (Filebeat for Windows)
- `filebeat.yml` içinde `paths` ile `D:\Logs\*.log` gibi path tanımlanır.

### Windows sistem metric’leri (Metricbeat for Windows)
- CPU, RAM, process bilgileri alınır.

### Windows Event Log (Winlogbeat)
- Güvenlik, sistem, uygulama event log’larını toplar.

---

## 📊 Kibana’da Veri Görme ve Data View
- Kibana’ya gir → Stack Management → Data Views  
- `logstash-*`, `filebeat-*`, `metricbeat-*` gibi index pattern’ler oluştur.

---

## 📈 Örnek Dashboard (CPU/RAM/Process)
- Metricbeat ile gelen veriler için hazır dashboard’lar import edilebilir.

---

## 🧪 Test, Doğrulama ve Faydalı Komutlar

### Container loglarını izle:
```bash
docker logs -f logstash
docker logs -f filebeat
```

### Elasticsearch health check:
```bash
curl -u elastic:password http://localhost:9200/_cluster/health?pretty
```

---

## 🛠️ Sık Karşılaşılan Sorunlar & Çözümler

- **Kibana “Failed to fetch” hatası**  
  → Encryption key eksik olabilir. `.env` veya compose dosyasında ekleyin.

- **Auditbeat WSL2’de çalışmıyor**  
  → Kernel izinleri eksik, sadece native Linux’ta çalışır.

---

## ⚠️ Notlar ve Sınırlamalar

- Windows’ta bazı Beat modülleri çalışmaz (ör. Auditbeat).
- İlk çalıştırmada `setup` servisi bitmeden Kibana açılmayabilir → bekleyin.
- RAM tüketimi yüksek olabilir, özellikle metricbeat + packetbeat aktifse.

