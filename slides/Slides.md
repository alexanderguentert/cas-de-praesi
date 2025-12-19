---
marp: true
title: Orchestrierung von Open-Data-Aktualisierungen mit Apache Airflow
author: Alexander Güntert
paginate: true
theme: weber
---

<!-- _class: titlepage -->
# Orchestrierung Open-Data-Aktualisierungen mit Apache Airflow
## CAS Data Engineering HS25
### Use Case Präsentation 
#### Alexander Güntert

<style>
.bottom-img-row {
  display: flex;
  justify-content: right;
  align-items: center;
  gap: 24px;            
  margin-top: 24px;
}
.bottom-img {
  height: 100px;
}
</style>
<div class="bottom-img-row">
  <img class="bottom-img" src="https://upload.wikimedia.org/wikipedia/commons/7/71/AirflowLogo.svg" />
  <img class="bottom-img" src="https://data.stadt-zuerich.ch/logo.svg" />
</div>


---

# Warum jetzt?

- Open Data Zürich betreibt **zahlreiche historisch gewachsene Aktualisierungsprozesse**
- Unterschiedliche Kanäle:
  - Dropzone (WebDAV Harvester)
  - GitHub Actions
  - GitLab Pipelines
  - CRON-Jobs
  - Manuelle Uploads
- **Zunehmende Anzahl und Komplexität** der Datensätze
- Höhere Anforderungen an:
  - Zuverlässigkeit
  - Monitoring
  - Nachvollziehbarkeit

➡️ Jetzt ist der richtige Zeitpunkt für eine **Standardisierung und Zentralisierung**

---

<div class="columns">
<div>

Open Data Zürich betreibt **zahlreiche historisch gewachsene Aktualisierungsprozesse** über unterschiedliche Kanäle:
- Dropzone (WebDAV Harvester)
- GitHub Actions
- GitLab Pipelines
- CRON-Jobs
- Manuelle Uploads

</div>  
<div>    
        
**Zunehmende Anzahl und Komplexität** der Datensätze stellt höhere Anforderungen an:
- Zuverlässigkeit
- Monitoring
- Nachvollziehbarkeit

</div> 
</div>

---

# Was passiert, wenn wir nichts tun?

**Pain (Ist-Zustand):**
- Kein zentrales Monitoring
- Fehler werden oft  spät erkannt
- Unterschiedliche Metadatenverarbeitung je nach Kanal
- Kein automatisches Retry bei temporären API-Fehlern
- Gesamte Prozesse müssen bei Fehlern neu gestartet werden

**Erwarteter Win:**
- Weniger manuelle Eingriffe
- Schnellere Fehlerbehebung
- Höhere Stabilität und Transparenz

---

# Welches Problem lösen wir?

- Fehlende **zentrale Orchestrierung** aller Datenaktualisierungen
- Keine einheitliche:
  - Überwachung
  - Fehlerbehandlung
  - Wiederholbarkeit
- Abhängigkeit von **externen Services** (z. B. GitHub Actions)

➡️ Ziel: **Einheitlicher, robuster und skalierbarer Aktualisierungsprozess**

---

# Wie sieht die Lösung aus?
      
**Apache Airflow als zentraler Orchestrator**

- Steuerung aller Aktualisierungsprozesse über DAGs
- Einheitliches Monitoring & Logging
- Konfigurierbare Retry-Mechanismen
- Klare Trennung:
  - Orchestrierung (Airflow)
  - Fachlogik (Docker-Container)

**Ergebnis:**
- Standardisierte Pipelines
- Bessere Wartbarkeit
- Zukunftssichere Architektur

![bg right:40% h:50%](img/airflow_architektur.svg)

---

# Beispiel-Pipeline: Inventar Hauptarchiv Stadtarchiv Zürich

**Warum dieser Use Case?**
- Typischer Open-Data-Aktualisierungsprozess
- Hohe Komplexität (API-Abfrage (SRU), PDF-Downloads, KI-basierte Textzusammenfassungen, CSV & Parquet, Metadaten-Update in CKAN)

➡️ Ideal als **Blaupause für weitere Pipelines**

![bg right:40% h:90%](img/mermaid_download_sru.svg)

---


# Technische Umsetzung (POC)

- Eigener Airflow-DAG für den Use Case
- Verarbeitungsschritte als Docker-Container in separatem [Repository](https://github.com/alexanderguentert/cas-de-airflow-container)
- Gemeinsames Volume für Datenaustausch
- Parallelisierung einzelner Tasks (z. B. CSV & Parquet Upload)

**Technologien:**
- Python
- Apache Airflow
- Docker Operator
- CKAN API
- Google Gemini API (für Zusammenfassungen)

---

# Was wurde bereits umgesetzt?

- Vollständiger **Proof of Concept**
- Funktionsfähiger Airflow-DAG
- Erfolgreiche Aktualisierung:
  - Daten (CSV & Parquet)
  - Metadaten im CKAN
- Laufzeit deutlich reduziert gegenüber Originalprozess
- Dokumentation & reproduzierbares Setup 

➡️ Lösung ist **demonstrierbar und erweiterbar**: https://github.com/alexanderguentert/cas-de-airflow/

---

# Mehrwert der Lösung

- Zentrales Monitoring aller Pipelines
- Einheitliche Metadatenverarbeitung
- Automatische Retries bei Fehlern
- Keine vollständigen Neustarts bei Teilausfällen
- Unabhängigkeit von externen CI-Systemen

➡️ **Robuste Basis für zukünftige Open-Data-Prozesse**

---

# Call-to-Action

**Nächste Schritte:**
- Vorstellung bei stadtinternen Stakeholdern
- Testdeployment des Proof of Concept
- Definition eines Standard-DAG-Templates
- Einbindung zusätzlicher Datenquellen:
  - Externe APIs
  - Filesysteme
- Perspektivisch:
  - Integration des städtischen Metadatenkatalogs (SDK)
  - Integration DWH

