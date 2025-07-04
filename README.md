# HAsync - Home Assistant Integration für IP-Symcon

[![Version](https://img.shields.io/badge/version-2.0.0-blue)](https://github.com/your-repo)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![IP-Symcon](https://img.shields.io/badge/IP--Symcon-4.0+-orange)](https://symcon.de)

Eine professionelle Bibliothek zur Integration von Home Assistant in IP-Symcon mit automatischer Geräteerkennung, Echtzeitaktualisierung und bidirektionaler Kommunikation.

## 🌟 Features

- ✅ **Automatische Geräteerkennung** über REST API Configurator
- ✅ **Echtzeitaktualisierung** über MQTT (optional)
- ✅ **Intelligente Typerkennung** für verschiedene Home Assistant Entitäten
- ✅ **Bidirektionale Kommunikation** - Steuern von HA-Geräten aus IP-Symcon
- ✅ **Saubere Architektur** mit getrennten Modulen für verschiedene Aufgaben
- ✅ **Icon-Mapping** von Home Assistant zu IP-Symcon
- ✅ **Variablenprofile** für Temperatur, Feuchtigkeit, Helligkeit etc.
- ✅ **Zweisprachige Lokalisierung** (DE/EN)

## 📦 Module

### HAconnect - REST API Configurator
**Typ:** Configurator (Typ 4)  
**GUID:** `{32D99DCD-A530-4907-3FB0-44D7D472771D}`

- Verbindung zu Home Assistant über REST API
- Automatische Geräteerkennung und Configurator
- Polling-basierte Aktualisierung (Fallback)
- Verwaltung der Home Assistant Verbindungsparameter

### HAdevice - Entitäts-Repräsentation
**Typ:** Device (Typ 3)  
**GUID:** `{8DF4E3B9-1FF2-B0B3-649E-117AC0B355FD}`

- Repräsentiert einzelne Home Assistant Entitäten
- Automatische Variablenerstellung mit intelligenter Typerkennung
- Bidirektionale Kommunikation (Lesen/Schreiben)
- Unterstützt alle gängigen HA-Domains (light, switch, sensor, etc.)
- Metadaten-Filterung (keine Variablen für icon, friendly_name, editable)

### HAmqtt - MQTT Echtzeit-Integration
**Typ:** Device (Typ 3)  
**GUID:** `{7A107D38-75A8-41C8-B57D-2D8E8FC1CF6A}`

- Echtzeitaktualisierung über MQTT
- Automatische Erkennung bestehender HAdevice Instanzen
- Discovery-Nachrichten verarbeitung
- Vollständig eigenständig (kein HAconnect Parent erforderlich)

## 🚀 Installation

### 1. Über IP-Symcon Store

1. IP-Symcon Management Konsole öffnen
2. **Kern Instanzen** → **Module Control**
3. **Store** → Nach "HAsync" suchen
4. **Installieren** klicken

### 2. Manuelle Installation

1. Repository herunterladen oder klonen
2. Ordner nach `/var/lib/symcon/modules/` kopieren
3. Management Konsole → **Module Control** → **Module laden**

## ⚙️ Konfiguration

### Schritt 1: Home Assistant Token erstellen

1. Home Assistant aufrufen → **Profil** → **Long-lived access tokens**
2. **Create Token** → Name vergeben (z.B. "IP-Symcon")
3. Token kopieren und sicher aufbewahren

### Schritt 2: HAconnect konfigurieren

1. **Instanz hinzufügen** → **HAconnect**
2. **Home Assistant URL** eingeben (z.B. `http://192.168.1.100:8123`)
3. **Long-lived Access Token** einfügen
4. **Übernehmen** → Configurator öffnet sich automatisch
5. Gewünschte Geräte auswählen und **Erstellen** klicken

### Schritt 3: MQTT einrichten (Optional für Echtzeitaktualisierung)

1. **MQTT Server** Modul in IP-Symcon installieren und konfigurieren
2. **Instanz hinzufügen** → **HAmqtt**
3. **Parent** → MQTT Server auswählen
4. **Discovery Prefix** auf `homeassistant` lassen (Standard)
5. **Übernehmen** → MQTT Integration ist aktiv

## 🔧 Unterstützte Entitätstypen

| Domain | Variablentyp | Profil | Editierbar | Beispiel |
|--------|--------------|--------|------------|----------|
| `light` | Boolean | ~Switch | ✅ | Licht an/aus |
| `switch` | Boolean | ~Switch | ✅ | Schalter |
| `input_boolean` | Boolean | ~Switch | ✅ | Input Boolean |
| `binary_sensor` | Boolean | ~Switch | ❌ | Bewegungsmelder |
| `sensor` | Auto¹ | Auto¹ | ❌ | Temperatur, Feuchtigkeit |
| `input_number` | Float | ~Intensity | ✅ | Schieberegler |
| `device_tracker` | Boolean | ~Presence | ❌ | Anwesenheit |
| `automation` | Boolean | ~Switch | ❌ | Automation |

¹ *Automatische Erkennung basierend auf Attributnamen und Werten*

## 📊 Intelligente Typerkennung

Das HAdevice Modul erkennt automatisch den korrekten Variablentyp:

- **Temperatur-Attribute** (`temperature`, `current_temperature`) → Float mit ~Temperature
- **Feuchtigkeit** (`humidity`) → Integer mit ~Humidity  
- **Helligkeit** (`brightness`, `illuminance`) → Integer mit ~Intensity
- **Prozent-Werte** (`battery_level`, `position`) → Integer mit ~Intensity
- **Boolean-Domains** (light, switch) → Boolean mit ~Switch
- **Numerische Werte** → Integer/Float je nach Inhalt
- **Fallback** → String

## 🔄 Funktionsweise

### REST API Polling (HAconnect)
- Regelmäßige Abfrage aller Entitätszustände
- Standard: 30 Sekunden Intervall
- Zuverlässig, aber nicht Echtzeit

### MQTT Echtzeit-Updates (HAmqtt)
- Sofortige Aktualisierung bei Änderungen
- Automatische Weiterleitung an HAdevice Instanzen
- Unterstützt Discovery-Nachrichten

### Bidirektionale Steuerung
- IP-Symcon → Home Assistant über REST API Service Calls
- Automatische Bestimmung des korrekten Services
- Unterstützt alle editierbaren Entitäten

## 📋 Statuscodes

| Code | Status | Beschreibung |
|------|--------|-------------|
| 102 | ✅ OK | Modul funktioniert korrekt |
| 104 | ⚠️ Fehler | Keine Verbindung zu Home Assistant |
| 201 | ❌ Fehler | Konfigurationsfehler |
| 202 | ⚠️ Warnung | Teilweise Funktionalität |

## 🛠️ Troubleshooting

### Verbindungsprobleme
- Home Assistant URL und Token prüfen
- Firewall-Einstellungen überprüfen
- Home Assistant API-Zugriff testen: `curl -H "Authorization: Bearer YOUR_TOKEN" http://YOUR_HA_URL/api/states`

### MQTT funktioniert nicht
- MQTT Server Modul korrekt konfiguriert?
- Home Assistant MQTT Integration aktiv?
- Discovery Topics richtig abonniert?

### Variablen werden nicht erstellt
- Entität in Home Assistant verfügbar?
- HAdevice Status-Variable vorhanden?
- Logs in IP-Symcon prüfen

## 🔗 Links

- [Home Assistant](https://www.home-assistant.io/)
- [IP-Symcon](https://www.symcon.de/)
- [MQTT Integration Guide](https://www.home-assistant.io/integrations/mqtt/)

## 📝 Changelog

### Version 2.0.0
- ✅ Komplette Code-Bereinigung und Optimierung
- ✅ Metadaten-Attribute Ausschluss implementiert
- ✅ MQTT Integration vollständig funktionsfähig
- ✅ Intelligente Typerkennung verbessert
- ✅ Debug-Ausgaben reduziert
- ✅ Dokumentation überarbeitet

## 📄 Lizenz

MIT License - siehe [LICENSE](LICENSE) für Details.

---

**Entwickelt von [Windsurf.io](https://windsurf.io) • Version 2.0.0 • 2025**