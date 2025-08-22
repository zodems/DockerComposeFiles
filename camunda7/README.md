# Camunda 7 + PostgreSQL Kurulum Rehberi

Camunda Platform 7’yi PostgreSQL ile **lokalde** hızlı ve sağlam şekilde çalıştırmak için kılavuz.  
Kurulum **.env** dosyasıyla parametrik; veritabanı şemasını **doğru sürüm CREATE SQL scriptleriyle** kurmanız önerilir. (Alternatif olarak ilk kurulumda Camunda’nın kendisine kurdurabilirsiniz.)

──────────────────────────────────────────────────────────────────────────────

### 1) Klasör Yapısı
```
camunda7/
├─ docker-compose.yml 
├─ .env.camunda 
├─ drivers/ 
│  └─ postgresql-42.7.4.jar 
└─ sql/ 
   ├─ 10-engine-create-postgres-7.23.sql 
   └─ 20-identity-create-postgres-7.23.sql

```
Notlar:
- **`drivers/postgresql-*.jar`** Camunda Run için JDBC sürücüsüdür; container içinde **`/camunda/configuration/userlib/`** altına mount edilir.
- `sql/` klasörüne **Camunda imajı ile aynı sürümdeki** (örn. 7.23.x) **CREATE** scriptlerini koyun (upgrade dosyaları değil). Dosyalar alfabetik sırayla çalışır.

### 4) Çalıştırma
docker compose --env-file .env.camunda up -d

#### İlk kurulumdan sonra UI:
 - UI  : http://localhost:8080/camunda/app/cockpit/default/
 - REST: http://localhost:8080/engine-rest/engine/

──────────────────────────────────────────────────────────────────────────────

### 5) Hızlı Doğrulama

#### Servisler
- docker compose --env-file .env.camunda ps
- docker logs -f Camunda

#### Sağlık
- curl http://127.0.0.1:8080/actuator/health

──────────────────────────────────────────────────────────────────────────────

### 6) Sık Hatalar

- column "... does not exist":
  → Script sürümü ile Camunda sürümü uyumsuz. Doğru CREATE scriptlerini kullanın veya ilk kurulumda SCHEMA_UPDATE=true.

- ports are not available (80):
  → 80 doluysa 8080 kullanın veya çakışan servisi durdurun.

- UI gelmiyor:
  → WEBAPPS/REST env’leri açık mı? JDBC driver mount edildi mi? Logları kontrol edin.
