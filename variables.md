# variables.md - Konfiguration

_Vom Agenten befuellt. FEHLT = noch einzutragen, OK = vorhanden_

---

## Agent-Verhalten

```
AUTONOMY_LEVEL=3
# 1 = Bestaetigung bei jedem Schritt
# 2 = Bestaetigung zwischen Phasen, innerhalb autonom
# 3 = Voll autonom, fragt nur bei echten Problemen
```

---

## Referenzen
# Trage hier Links, Workflow-IDs, Artikel oder Docs ein die der Agent nutzen soll.
# Der Agent liest diese Referenzen bevor er mit dem Aufbau beginnt.

```
REF_1=    # FEHLT  (z.B. n8n-workflow-id:12345. oder https://github.com/user/repo)
REF_2=    # optional
REF_3=    # optional
```

### Beispiele fuer Referenzen
```
# n8n Workflow als Vorlage:
REF_1=n8n-workflow-id:12345

# Dokumentations-Link:
REF_2=https://docs.airtable.com/api

# GitHub Beispielprojekt:
REF_3=https://github.com/user/repo

# Lokale Datei:
REF_4=./examples/mein-beispiel.json

# Artikel:
REF_5=https://medium.com/...
```

## Status-Uebersicht

| Variable | Status |
|---|---|
| AUTONOMY_LEVEL | OK - 3 (voll autonom) |
| Referenzen | FEHLT |
| Airtable Base ID | FEHLT |
| Airtable Table | FEHLT |
| Airtable Credential ID | FEHLT |
| Airtable Spalten | FEHLT |
| n8n Base URL | OK - tmkpi4:5000 |
| n8n Webhook URL | OK - tomekness.ddns.net:5000 |
| AI Credential | FEHLT |
| AI Modell | FEHLT |
| Trigger-Typ | FEHLT |
| Test-URLs (5x) | FEHLT |
