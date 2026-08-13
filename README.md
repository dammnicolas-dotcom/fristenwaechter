# Fristenwächter

Der Fristenwächter berechnet gerichtliche und außergerichtliche Fristen nach deutschem Zivilprozessrecht. Grundlage ist § 222 ZPO, der für die Berechnung von Fristen auf die allgemeinen Vorschriften der §§ 187–193 BGB verweist.

## Rechtliche Grundlagen

### § 187 BGB – Fristbeginn

Ist für den Anfang einer Frist ein Ereignis oder ein in den Lauf eines Tages fallender Zeitpunkt maßgebend (z. B. eine Zustellung), so wird bei der Berechnung der Frist der Tag nicht mitgerechnet, in welchen das Ereignis oder der Zeitpunkt fällt (§ 187 Abs. 1 BGB). Die Frist beginnt also erst mit dem folgenden Tag zu laufen.

Ist dagegen der Beginn eines Tages der für den Fristanfang maßgebende Zeitpunkt, so wird dieser Tag bei der Berechnung mitgerechnet (§ 187 Abs. 2 BGB) – im Fristenwächter-Kontext relevant z. B. bei Fristen, die kalendermäßig mit dem Ereignistag selbst zu laufen beginnen.

### § 188 BGB – Fristende

- Tagesfristen (§ 188 Abs. 1 BGB): Die Frist endet mit dem Ablauf des letzten Tages der Frist.
- Wochen-, Monats- und Mehrmonatsfristen (§ 188 Abs. 2 BGB): Die Frist endet mit Ablauf desjenigen Tages der letzten Woche oder des letzten Monats, welcher durch seine Benennung oder Zahl dem Tag entspricht, in den das fristauslösende Ereignis fällt.
- Sonderfall Monatsende (§ 188 Abs. 3 BGB): Fehlt einer nach Monaten bestimmten Frist im letzten Monat der für ihr Ende maßgebliche Tag (z. B. der 31. bei einem Zielmonat mit nur 28 oder 30 Tagen), endet die Frist mit Ablauf des letzten Tages dieses Monats.

### § 222 ZPO – Anwendung im Zivilprozess und Fristverlängerung bei Feiertagen

§ 222 Abs. 1 ZPO erklärt die §§ 187–193 BGB für die Berechnung zivilprozessualer Fristen für anwendbar. § 222 Abs. 2 ZPO ergänzt eine prozessrechtliche Besonderheit: Fällt das Ende einer Frist auf einen Sonntag, einen allgemeinen Feiertag oder einen Sonnabend, so endet die Frist mit dem Ablauf des nächsten Werktages. Da gesetzliche Feiertage bundeslandabhängig sind, muss die maßgebliche Feiertagsliste je nach Gerichtsort/Wohnsitz gesondert berücksichtigt werden.

## Implementierung

Die Fristberechnung ist in `frist_berechnung.py` umgesetzt. Die Funktion `berechne_frist` bildet die oben beschriebene Logik ab:

1. Tages- und Wochenfristen werden direkt als Kalendertage ab dem Ereignisdatum berechnet (§ 187 Abs. 1, § 188 Abs. 1 BGB).
2. Monatsfristen nutzen echte Kalenderarithmetik inklusive Behandlung des Sonderfalls aus § 188 Abs. 3 BGB (z. B. 31.1. + 1 Monat → 28./29.2.).
3. Fällt das errechnete Fristende auf einen Samstag, Sonntag oder einen der übergebenen Feiertage, wird das Fristende gemäß § 222 Abs. 2 ZPO auf den nächsten Werktag verschoben.

## Nutzungsbeispiel

```python
from datetime import date
from frist_berechnung import berechne_frist

# Zustellung eines Urteils am Montag, 3.8.2026.
# Berufungsfrist: 1 Monat (§ 517 ZPO)
zustellung = date(2026, 8, 3)
fristende = berechne_frist(zustellung, monate=1)
print(fristende)  # 2026-09-03

# Frist von 2 Wochen ab Zustellung an einem Freitag,
# Fristende fällt automatisch auf den nächsten Werktag, falls es auf ein
# Wochenende oder einen Feiertag trifft
zustellung = date(2026, 7, 31)
fristende = berechne_frist(zustellung, wochen=2)
print(fristende)

# Mit Berücksichtigung gesetzlicher Feiertage (bundeslandabhängig)
feiertage = {date(2026, 10, 3)}  # Tag der Deutschen Einheit
fristende = berechne_frist(date(2026, 9, 19), tage=14, feiertage=feiertage)
print(fristende)
```

## Hinweis

Dieses Projekt dient der technischen Unterstützung bei der Fristberechnung und ersetzt keine rechtliche Prüfung im Einzelfall. Insbesondere die Ermittlung der maßgeblichen gesetzlichen Feiertage sowie fristauslösender Ereignisse (z. B. Zustellungsdatum) obliegt weiterhin der eigenständigen rechtlichen Prüfung.
