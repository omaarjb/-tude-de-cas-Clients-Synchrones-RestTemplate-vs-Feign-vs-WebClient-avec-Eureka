# Résultats des Tests de Performance - Comparaison des Charges

## Objectif

Comparer les performances des services **service-client** et **service-voiture** sous différentes charges :
- 10 utilisateurs simultanés
- 50 utilisateurs simultanés
- 100 utilisateurs simultanés
- 200 utilisateurs simultanés

## Configuration des Tests

- **Outils** : JMeter, Prometheus, Grafana
- **Endpoints testés** :
  - `/api/clients/1/car/rest` (RestTemplate)
  - `/api/clients/1/car/feign` (Feign)
  - `/api/clients/1/car/webclient` (WebClient)
- **Itérations** : 50 par thread
- **Ramp-up** : Progressif selon le nombre d'utilisateurs

---

## Résultats - Service Client

### Tableau Comparatif

| Charge | CPU (%) | Memory (MB) | Response Time (ms) | Request Rate (req/s) |
|--------|---------|-------------|-------------------|---------------------|
| 10 utilisateurs | 10.1 | 142 | 70-80 | 0-50 |
| 50 utilisateurs | 11.1 | 143 | 70-90 | 0-200 (pic à 190) |
| 100 utilisateurs | 12.2 | 145 | 70-100 | 0-200 (pic à 190) |
| 200 utilisateurs | 13.5 | 147 | 70-110 | 0-200 (pic à 190) |

### Screenshots Grafana - Service Client

#### 10 Utilisateurs
<img width="1907" height="865" alt="image" src="https://github.com/user-attachments/assets/4a163f91-205d-434e-979f-48ff29134c15" />

#### 50 Utilisateurs
<img width="1909" height="873" alt="image" src="https://github.com/user-attachments/assets/661ca9db-3a26-4c7f-8061-273b9568c1e3" />

#### 100 Utilisateurs
<img width="1912" height="881" alt="image" src="https://github.com/user-attachments/assets/89cfdea5-60fa-4470-a90f-febffcd821ae" />

#### 200 Utilisateurs
<img width="1913" height="878" alt="image" src="https://github.com/user-attachments/assets/ae5c6416-b57c-4fb8-aad4-7c7aba5af33e" />


---

## Résultats - Service Voiture

### Tableau Comparatif

| Charge | CPU (%) | Memory (MB) | Response Time (ms) | Request Rate (req/s) |
|--------|---------|-------------|-------------------|---------------------|
| 10 utilisateurs | 4.89 | 115 | 50-60 | 0-600 (pic à 600) |
| 50 utilisateurs | 5.53 | 116 | 50-65 | 0-600 (pic à 600) |
| 100 utilisateurs | 6.27 | 116 | 50-65 | 0-600 (pic à 600) |
| 200 utilisateurs | 7.22 | 116 | 50-65 | 0-600 (pic à 600) |

### Screenshots Grafana - Service Voiture

#### 10 Utilisateurs
<img width="1911" height="749" alt="image" src="https://github.com/user-attachments/assets/69342c40-2d76-4b4d-8117-01c46732f4db" />

#### 50 Utilisateurs
<img width="1911" height="747" alt="image" src="https://github.com/user-attachments/assets/85dd5e0d-e2fd-4665-8c41-e5098e79460d" />

#### 100 Utilisateurs
<img width="1915" height="747" alt="image" src="https://github.com/user-attachments/assets/27b6c476-7179-45e5-9840-82fbb055e05e" />

#### 200 Utilisateurs
<img width="1917" height="746" alt="image" src="https://github.com/user-attachments/assets/f8d38701-29e7-4a67-8822-6f7293827440" />

---

## Analyse Comparative

### Observations

#### Service Client
- **CPU** : Augmentation progressive de 10.1% à 13.5% avec l'augmentation de la charge
- **Memory** : Légère augmentation de 142 MB à 147 MB, consommation mémoire stable
- **Response Time** : Augmentation de 70-80ms (10 users) à 70-110ms (200 users), dégradation modérée
- **Request Rate** : Pics jusqu'à 190-200 req/s observés lors des pics de charge

#### Service Voiture
- **CPU** : Augmentation progressive de 4.89% à 7.22% avec l'augmentation de la charge
- **Memory** : Très stable autour de 115-116 MB, consommation mémoire constante
- **Response Time** : Stable entre 50-65ms, excellent temps de réponse même sous charge
- **Request Rate** : Pics très élevés jusqu'à 600 req/s, capacité de traitement importante

### Conclusions

1. **Service Voiture** montre de meilleures performances que **Service Client** en termes de temps de réponse (50-65ms vs 70-110ms)
2. **Service Voiture** peut gérer un débit beaucoup plus élevé (600 req/s vs 200 req/s)
3. La consommation CPU et mémoire reste raisonnable pour les deux services même sous charge maximale (200 utilisateurs) 

---

## Métriques Collectées

Les métriques suivantes ont été collectées via Prometheus et visualisées dans Grafana :

- **CPU Usage** : `(avg(process_cpu_usage{application="service-xxx"}) * 100) / 6`
- **Memory Usage** : `avg(sum(jvm_memory_used_bytes{application="service-xxx"}) / 1024 / 1024)`
- **Response Time** : `rate(http_server_requests_seconds_sum{application="service-xxx"}[1m]) / rate(http_server_requests_seconds_count{application="service-xxx"}[1m]) * 1000`
- **Request Rate** : `rate(http_server_requests_seconds_count{application="service-xxx"}[1m])`

---

## Notes

- Les tests ont été effectués avec JMeter
- Les métriques ont été collectées en temps réel via Prometheus
- Visualisation réalisée avec Grafana
- Système : 6 cœurs CPU

