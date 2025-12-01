# MigrateMe Community Telemetry

Dieses Repository sammelt anonymisierte Backup-Berichte von MigrateMe-Nutzern, um die Qualitätsbewertung (Sterne-System) zu verbessern.

## Wie es funktioniert

1. **User erstellt Backup** in MigrateMe
2. **Opt-in Dialog** fragt nach Zustimmung zum Teilen
3. **Anonymisierte Daten** werden an dieses Repo gesendet
4. **GitHub Action** aggregiert die Daten automatisch
5. **MigrateMe lädt** `community_quality.json` herunter

## Datenschutz

- **Keine persönlichen Daten**: Benutzernamen werden durch `{USER}` ersetzt
- **Keine Dateiinhalte**: Nur Metadaten (Programmnamen, Pfade, Erfolgsraten)
- **Vollständig anonym**: Kein GitHub-Account für User erforderlich
- **Open Source**: Alle Daten und Code sind öffentlich einsehbar

## Datenstruktur

### Eingehende Berichte (`data/reports/`)

```json
{
  "upload_id": "uuid",
  "timestamp": "2024-12-01T10:30:00",
  "reports": [
    {
      "program_name": "Firefox",
      "backup_paths": ["C:\\Users\\{USER}\\AppData\\..."],
      "success": true,
      "restore_tested": false,
      "files_count": 847,
      "total_size_bytes": 13107200
    }
  ]
}
```

### Aggregierte Daten (`data/aggregated/community_quality.json`)

```json
{
  "last_updated": "2024-12-01T12:00:00",
  "programs": {
    "Firefox": {
      "total_reports": 150,
      "successful_backups": 147,
      "successful_restores": 89,
      "stars": 5,
      "discovered_paths": [
        "C:\\Users\\{USER}\\AppData\\Roaming\\Mozilla\\Firefox"
      ]
    }
  }
}
```

## Sterne-Berechnung

| Sterne | Kriterien |
|--------|-----------|
| ☆☆☆☆☆ | Keine Daten |
| ★☆☆☆☆ | 1+ Berichte |
| ★★☆☆☆ | 5+ Berichte, >50% Erfolgsrate |
| ★★★☆☆ | 20+ Berichte, >80% Erfolgsrate |
| ★★★★☆ | 50+ Berichte, >90% Erfolg, Restore getestet |
| ★★★★★ | 100+ Berichte, >95% Erfolg, mehrere Restores |

## API Endpunkt

Telemetrie-Daten werden via `repository_dispatch` Event gesendet:

```bash
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer $TOKEN" \
  https://api.github.com/repos/stlas/migrateme-telemetry/dispatches \
  -d '{"event_type":"telemetry_upload","client_payload":{"data":"..."}}'
```

## Links

- [MigrateMe Hauptprojekt](https://github.com/stlas/MigrateMe)
- [Telemetrie-Dokumentation](https://github.com/stlas/MigrateMe/blob/main/docs/TELEMETRY.md)
