# DS-TOM – Datenschutz-Maßnahmenplaner

**DS-TOM** ist ein browserbasiertes Werkzeug zur strukturierten Erstellung von Datenschutz-Maßnahmenplänen auf Basis von Gefährdungsanalysen gemäß DSGVO. Die Anwendung läuft vollständig lokal im Browser – ohne Server, ohne Installation, ohne Internetverbindung.

**Repository:** https://github.com/dsgvo-dev/ds-tom

---

## Repository-Inhalt

```
DS-TOM.html                   ← Webanwendung (einzelne, eigenständige HTML-Datei)
gefaehrdungen/                ← Gefährdungsprofile (THREAT_*.json)
massnahmen/                   ← Maßnahmenprofile (TOM_*.json)
README.md                     ← diese Datei
LICENSE                       ← CC BY 4.0 Lizenz
```

Alle Inhalte werden über das GitHub-Repository bereitgestellt und können dort heruntergeladen oder geklont werden.

---

## Systemvoraussetzungen

### Browser

DS-TOM verwendet die **File System Access API** des Browsers für den lokalen Dateizugriff. Folgende Browser werden unterstützt:

| Browser | Mindestversion | Status |
|---------|---------------|--------|
| Google Chrome | 86+ | Empfohlen |
| Microsoft Edge | 86+ | Empfohlen |
| Mozilla Firefox | 111+ | Unterstützt |
| Safari | 15.2+ | Eingeschränkt |

Internet Explorer sowie ältere Browserversionen werden nicht unterstützt.

### Betriebssystem

Windows, macOS und Linux werden unterstützt. Der Browser muss Zugriff auf das lokale Dateisystem erhalten dürfen. In Umgebungen mit restriktiven Gruppenrichtlinien ist ggf. eine IT-seitige Freigabe der Browser-API `showDirectoryPicker` erforderlich (siehe [Bekannte Einschränkungen](#bekannte-einschränkungen)).

### Internetverbindung

Nicht erforderlich. DS-TOM arbeitet vollständig offline. Es findet keine Datenübertragung statt.

---

## Installation und Inbetriebnahme

### Schritt 1 – Dateien beziehen

Das Repository klonen oder als ZIP herunterladen:

```bash
git clone https://github.com/dsgvo-dev/ds-tom.git
```

Alternativ steht im Repository unter *Releases* ein Archiv zum Download bereit.

### Schritt 2 – Ordnerstruktur prüfen

Nach dem Entpacken muss folgende Struktur vorhanden sein:

```
ds-tom/                        ← Ordnername frei wählbar
  gefaehrdungen/               ← muss exakt so heißen
  massnahmen/                  ← muss exakt so heißen
  DS-TOM.html
  README.md
```

Die Unterordner `gefaehrdungen` und `massnahmen` müssen exakt so benannt sein. Der übergeordnete Ordner kann beliebig benannt werden.

### Schritt 3 – Anwendung starten

`DS-TOM.html` per Doppelklick oder Drag & Drop in einen unterstützten Browser öffnen.

### Schritt 4 – Daten laden

Auf die Schaltfläche **„Daten laden"** klicken. Im Dateisystem-Dialog den Ordner auswählen, der `gefaehrdungen/` und `massnahmen/` enthält. Der Browser fragt einmalig nach der Leseberechtigung für diesen Ordner.

DS-TOM speichert den Ordner-Handle im browser-eigenen IndexedDB-Speicher. Bei späteren Sitzungen genügt ein Klick auf **„Erneut laden"**.

---

## Bedienung

DS-TOM führt in vier aufeinander aufbauenden Schritten durch die Erstellung eines Maßnahmenplans:

```
① Metadaten     → Organisation, Verfahren, Datum, Verantwortliche eingeben
② Gefährdungen  → Relevante Gefährdungen auswählen
③ Maßnahmenplan → Umsetzungsstatus und Notizen je Maßnahme festlegen
④ Graph         → Abhängigkeiten zwischen Gefährdungen und Maßnahmen visualisieren
```

Restrisiken (Kategorie RR) sind in Schritt ② stets vorausgewählt und nicht abwählbar.

### Schritt ④ – Abhängigkeitsgraph

Der Graph stellt Gefährdungen (linke Spalte) und Maßnahmen (rechte Spalte) als Knoten dar und verbindet sie durch Kanten, die die jeweiligen Zuordnungen aus den JSON-Dateien abbilden. Ein Klick auf einen Knoten hebt alle zugehörigen Verbindungen hervor und öffnet ein Detailpanel mit ID, Bezeichnung und weiteren Informationen. Der hervorgehobene Zustand bleibt erhalten, bis der Knoten erneut angeklickt oder das Panel geschlossen wird. Die Ansicht kann nach Kategorie gefiltert werden.

### Export und Import

Der CSV-Export ist der einzige Weg, den Arbeitsstand dauerhaft zu sichern. Alle im Browser eingegebenen Daten (Metadaten, Gefährdungsauswahl, Umsetzungsstatus, Notizen) gehen beim Schließen des Tabs verloren, sofern kein Export erfolgt ist.

**Workflow für Folgesitzungen:**

1. `DS-TOM.html` öffnen und **„Erneut laden"** klicken (lädt die JSON-Quelldaten)
2. Über **„CSV importieren"** die zuvor gespeicherte Datei einlesen
3. Maßnahmenplan wird mit allen Metadaten, der Gefährdungsauswahl sowie Status und Notizen wiederhergestellt

### CSV-Format

Die exportierte Datei ist UTF-8-kodiert und enthält zwei Abschnitte:

**Kopfzeilen (Metadaten):** Zeilen im Format `#Schlüssel;Wert`, z. B. Organisation, Verfahrensbezeichnung, Datum und Verantwortliche.

**Datenzeilen (Maßnahmenplan):** Eine Zeile je Maßnahme mit den Spalten `TOM-ID`, `Maßnahmenname`, `Kategorie`, `Umsetzungsstatus` und `Notiz`. Nur Maßnahmen, die zu den ausgewählten Gefährdungen gehören, werden exportiert.

Die CSV-Datei kann in Tabellenkalkulationsprogrammen geöffnet oder maschinell weiterverarbeitet werden.

---

## Datenhaltung

| Datenart | Speicherort |
|----------|-------------|
| JSON-Quelldateien (Gefährdungen, Maßnahmen) | Lokales Dateisystem |
| Metadaten (Organisation, Verfahren, Datum, Verantwortliche) | Arbeitsspeicher – nur während der Sitzung |
| Maßnahmenplan (Gefährdungsauswahl, Status, Notizen) | Arbeitsspeicher – nur während der Sitzung |
| Ordner-Handle (Sitzungspersistenz) | Browser-interner IndexedDB-Speicher |
| Exportierte CSV-Datei | Frei wählbarer lokaler Speicherort |

**Hinweis:** Metadaten und Maßnahmenplan werden ausschließlich im Arbeitsspeicher gehalten. Der CSV-Export ist die einzige Möglichkeit, diese Daten zwischen Sitzungen zu erhalten.

Es werden keine personenbezogenen oder organisationsbezogenen Daten an externe Systeme übertragen.

---

## Inhalte erweitern und anpassen

### Neue Gefährdung

Eine neue JSON-Datei in `gefaehrdungen/` ablegen. Namensschema:

```
THREAT_<KATEGORIE>_<Bezeichnung>_<TYP>_<Version>.json
```

Mindeststruktur:

```json
{
  "id": "THREAT-XY-001",
  "kategorie": "XY",
  "bedrohungstyp": "GB",
  "beschreibung": "Beschreibung der Gefährdung",
  "schutzziele": ["Vertraulichkeit"],
  "empfohlene_massnahmen": ["TOM-ORG-001"],
  "metadaten": { "version": "1.0" }
}
```

### Neue Maßnahme

Eine neue JSON-Datei in `massnahmen/` ablegen. Namensschema:

```
TOM_<KATEGORIE>_<Bezeichnung>_<Typ>_<Version>.json
```

Mindeststruktur:

```json
{
  "id": "TOM-XY-001",
  "name": "Bezeichnung der Maßnahme",
  "kategorie": "XY",
  "maßnahmentyp": "Grundmaßnahme",
  "beschreibung": "Beschreibung der Maßnahme",
  "wirkungspunktzahl": 2,
  "anwendbarkeit": { "schutzziele": ["Vertraulichkeit"] }
}
```

### Bestehende Inhalte ändern

Namen, Beschreibungen und Zuordnungen können direkt in den JSON-Dateien bearbeitet werden. Änderungen werden nach dem nächsten Ladevorgang in DS-TOM sichtbar.

### Maßnahme einer Gefährdung zuordnen

Im jeweiligen THREAT-JSON das Feld `empfohlene_massnahmen` anpassen:

```json
"empfohlene_massnahmen": ["TOM-ORG-001", "TOM-ORG-003", "TOM-SW-005"]
```

---

## Kategorien

| Kürzel | Inhalt |
|--------|--------|
| ORG | Allgemein-organisatorische Maßnahmen |
| SW | Software und technische Systeme |
| MS365 | Microsoft 365 |
| KI | Künstliche Intelligenz |
| AV | Auftragsverarbeitung |
| CL | Cloud-Dienste |
| RR | Restrisiken (strukturell, nicht eliminierbar) |

---

## Bekannte Einschränkungen

**Mehrere Browser-Tabs:** Der Ordner-Handle gilt pro Tab. Beim Öffnen eines zweiten Tabs kann die Berechtigungsabfrage erneut erscheinen.

**Browser-Updates:** Nach größeren Browser-Updates kann es erforderlich sein, den Datenordner einmalig erneut auszuwählen. Dies ist ein sicherheitsbedingtes Verhalten der File System Access API.

**Unternehmens-Gruppenrichtlinien:** Wenn `showDirectoryPicker` durch IT-Richtlinien gesperrt ist, muss eine Ausnahme durch die IT-Administration eingerichtet werden. Betroffen sind typischerweise verwaltete Chrome- oder Edge-Installationen mit restriktiver Richtlinienkonfiguration.

**Sitzungsverlust:** Alle im Browser eingegebenen Daten gehen beim Schließen des Tabs oder Fensters verloren, sofern zuvor kein CSV-Export durchgeführt wurde. Der Ordner-Handle bleibt davon unberührt.

---

## Technische Informationen

DS-TOM ist eine einzelne, eigenständige HTML-Datei ohne externe Laufzeitabhängigkeiten:

- Kein serverseitiges Backend, kein Framework, keine CDN-Aufrufe
- Keine Cookies, kein `localStorage`
- Lokaler Dateizugriff über die W3C-standardisierte **File System Access API**
- Sitzungspersistenz des Ordner-Handles über **IndexedDB** (browser-intern, kein Netzwerkzugriff)
- Alle Verarbeitungsschritte laufen im Browser (JavaScript)
- Exportformat: UTF-8-kodiertes CSV

Netzwerk-Freischaltungen sind nicht erforderlich. Virenschutz-Ausnahmen sind nicht erforderlich.

---

## Lizenz

Dieses Projekt steht unter der **Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)** Lizenz.

Das bedeutet: Weitergabe und Bearbeitung sind erlaubt, solange eine angemessene Quellenangabe gemacht wird und keine kommerzielle Nutzung erfolgt.

Copyright (c) 2026 dsgvo-dev

Vollständiger Lizenztext: https://creativecommons.org/licenses/by-nc/4.0/legalcode
