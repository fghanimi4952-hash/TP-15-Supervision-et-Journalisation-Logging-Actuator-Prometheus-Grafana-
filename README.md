# TP15 - Monitoring Spring Boot avec Actuator, Prometheus et Grafana

## Objectif

Mettre en place une stack complète de monitoring pour une application Spring Boot avec collecte, stockage et visualisation des métriques.

---

##  Architecture

```
Application Spring Boot (8080)
        ↓
   Spring Boot Actuator
        ↓
    Métriques Prometheus
        ↓
   Prometheus (9090) - Collecte
        ↓
   Grafana (3000) - Visualisation
```


##  Installation et Démarrage

### 1. Compiler le projet
```powershell
mvn clean install
```

### 2. Démarrer les services

**Application Spring Boot :**
```powershell
mvn spring-boot:run
```
Vérifier : http://localhost:8080/health

**Prometheus :**
```powershell
cd monitoring/prometheus
.\prometheus.exe --config.file=prometheus.yml
```
 Vérifier : http://localhost:9090

**Grafana :**
```powershell
cd monitoring/grafana/bin
.\grafana-server.exe
```
 Vérifier : http://localhost:3000

---

## Configuration

### `prometheus.yml`
```yaml
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: 'tp15-spring-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['localhost:8080']
```

### Grafana
1. Se connecter : http://localhost:3000 (`admin` / `admin`)
2. Ajouter Data Source : `Prometheus-TP15` → http://localhost:9090
3. Importer Dashboard : ID `4701` (Spring Boot Statistics)

---

##  Endpoints Principaux

| Endpoint | Description |
|----------|-------------|
| `/health` | Santé de l'application |
| `/actuator/health` | Santé complète Actuator |
| `/actuator/prometheus` | Métriques Prometheus |
| `/actuator/metrics` | Liste des métriques |

---

##  Métriques Clés

- `tp15_requests_total` - Requêtes personnalisées
- `http_server_requests_seconds_count` - Requêtes HTTP
- `jvm_memory_used_bytes` - Mémoire JVM
- `process_cpu_usage` - CPU processus

---

##  Résultat :


### Application Spring Boot

#### Endpoint `/health`
**URL :** http://localhost:8080/health

![Health Endpoint](https://github.com/user-attachments/assets/20def021-e667-4372-badb-7737526e225f)

#### Endpoint `/process`
**URL :** http://localhost:8080/process

![Process Endpoint](https://github.com/user-attachments/assets/35ab006d-a9bc-4d52-8055-415ec62d0994)

### Spring Boot Actuator

#### Endpoint `/actuator/health`
**URL :** http://localhost:8080/actuator/health

![Actuator Health](https://github.com/user-attachments/assets/cf034881-0175-41fb-8607-d3e66904f738)

#### Endpoint `/actuator/prometheus`
**URL :** http://localhost:8080/actuator/prometheus

![Prometheus Metrics](https://github.com/user-attachments/assets/c48493f3-3a78-458a-a07d-7c4aadc52494)

### Interface Prometheus

**URL :** http://localhost:9090

#### Métrique personnalisée
![Custom Metric](https://github.com/user-attachments/assets/05cf87cf-92db-4f81-8ebf-fa0eabe79497)

#### Métriques HTTP Spring Boot
![HTTP Metrics](https://github.com/user-attachments/assets/278c0f07-aa9e-4cc0-b588-3c81168a9dc2)

#### Taux de requêtes par seconde
![Request Rate](https://github.com/user-attachments/assets/173a6c4c-18e4-4347-aa65-af97d5c47bbf)

#### Mémoire JVM utilisée
![JVM Memory](https://github.com/user-attachments/assets/7aa50070-669a-47c1-b434-e8e2cf3823b6)

### Dashboard Grafana

**URL :** http://localhost:3000

![Grafana Dashboard](https://github.com/user-attachments/assets/0e74007f-3440-4142-bedf-47fa433cf177)

---

##  Script de Test

```powershell
# Génération de trafic pour tester le monitoring
for ($i = 1; $i -le 10; $i++) {
    Invoke-RestMethod -Uri "http://localhost:8080/process"
    Start-Sleep -Seconds 2
}
```

---

## Configuration Application

### `application.properties`
```properties
server.port=8080
spring.application.name=tp15-monitoring
management.endpoints.web.exposure.include=*
management.endpoint.health.show-details=always
logging.file.name=logs/tp15-application.log
```

---

##  Vérification

```powershell
# Vérifier les services
Invoke-RestMethod -Uri "http://localhost:8080/health"
Invoke-WebRequest -Uri "http://localhost:9090"
Invoke-WebRequest -Uri "http://localhost:3000"
```

---

