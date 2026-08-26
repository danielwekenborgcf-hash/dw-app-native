# iPhone-App ohne Mac installieren (Sideloading)

Du brauchst dafür keinen Mac und keinen bezahlten Apple-Developer-Account (99 $/Jahr) — nur deine ganz normale, kostenlose Apple-ID.

## 1. Unsignierte IPA-Datei bauen (einmalig pro Version, in der Cloud)

1. Auf GitHub in diesem Repository auf den Tab **Actions** gehen.
2. Links **"Build iOS App (unsigned IPA)"** auswählen.
3. Rechts auf **"Run workflow"** klicken → nochmal **"Run workflow"** bestätigen.
4. Warten, bis der Lauf einen grünen Haken hat (dauert ca. 3–6 Minuten, macOS-Builds sind langsamer als der Android-Build).
5. In den Lauf klicken → ganz unten bei **"Artifacts"** die Datei **dw-personal-trainer-unsigned-ipa** herunterladen (ZIP).
6. ZIP entpacken → darin liegt `dw-personal-trainer-unsigned.ipa`.

Dieser Workflow läuft **nicht automatisch** bei jedem Push (anders als der Android-Build), weil Mac-Build-Minuten bei GitHub teurer sind als Linux-Minuten. Du startest ihn nur, wenn du wirklich eine neue iOS-Version testen willst.

## 2. Sideloadly installieren (einmalig, auf deinem Windows-PC)

1. https://sideloadly.io öffnen und die Windows-Version herunterladen und installieren.
2. Falls noch nicht vorhanden: iTunes bzw. die "Apple Mobile Device Support"-Treiber installieren (Sideloadly bietet das beim ersten Start selbst an) — nötig, damit Windows dein iPhone per USB erkennt.

## 3. App aufs iPhone bringen

1. iPhone per Kabel an den PC anschließen, auf dem iPhone "Diesem Computer vertrauen" bestätigen.
2. Sideloadly öffnen — dein iPhone sollte oben erscheinen.
3. Die Datei `dw-personal-trainer-unsigned.ipa` in das große Sideloadly-Fenster ziehen.
4. Bei "Apple ID" deine normale Apple-ID-E-Mail eintragen und das Passwort daneben eingeben (bei aktivierter Zwei-Faktor-Authentifizierung verlangt Sideloadly ggf. ein **App-spezifisches Passwort** — das erstellst du unter https://appleid.apple.com → Anmelden und Sicherheit → App-spezifische Passwörter).
5. Auf **Start** klicken. Sideloadly signiert die App mit deiner Apple-ID und installiert sie automatisch aufs Handy.
6. Auf dem iPhone einmalig: **Einstellungen → Allgemein → VPN & Geräteverwaltung** → deinen Apple-ID-Entwicklerprofil antippen → **"Vertrauen"**. Erst danach lässt sich die App öffnen.

## Wichtige Einschränkungen der kostenlosen Variante

- Das Signatur-Zertifikat läuft nach **7 Tagen** ab. Die App lässt sich danach nicht mehr öffnen, bis du sie über Sideloadly (Kabel anschließen, "Start" erneut klicken) neu installierst. Das dauert dann nur noch ~1 Minute, da die IPA-Datei ja schon vorhanden ist.
- Mit einer kostenlosen Apple-ID kannst du gleichzeitig nur wenige Apps (~3) auf wenigen Geräten (~10 pro Jahr) signieren.
- Push-Benachrichtigungen funktionieren mit dieser Methode nicht zuverlässig (dafür bräuchte man eine echte Push-Berechtigung vom kostenpflichtigen Account) — für Android klappt das wie in `PUSH_SETUP.md` beschrieben aber unabhängig davon.

## Alternative: AltStore (kein Kabel alle 7 Tage nötig)

Statt Sideloadly kannst du auch **AltStore** (https://altstore.io) nutzen: Der zugehörige "AltServer" läuft auf deinem PC und kann die App automatisch übers WLAN neu signieren, solange iPhone und PC im selben Netzwerk sind — du musst dann nicht jede Woche das Kabel anschließen. Die Ersteinrichtung ist etwas aufwändiger als bei Sideloadly, das Grundprinzip (kostenlose Apple-ID, 7-Tage-Zertifikat) ist aber dasselbe.
