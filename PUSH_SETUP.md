# Push-Benachrichtigungen einrichten (Firebase Cloud Messaging)

Die App ist technisch bereits vorbereitet: das `@capacitor/push-notifications` Plugin ist installiert, die Android-Berechtigung ist gesetzt, und `android/app/build.gradle` aktiviert das Google-Services-Gradle-Plugin automatisch, sobald eine echte `google-services.json` vorliegt (aktuell liegt dort nur ein Platzhalter, damit der Build nicht fehlschlägt).

Drei Dinge fehlen noch, bis echte Push-Nachrichten ankommen:

## 1. Firebase-Projekt anlegen (einmalig, ~5 Minuten)

1. Auf https://console.firebase.google.com mit deinem Google-Konto einloggen.
2. "Projekt hinzufügen" → Namen vergeben (z.B. "DW Personal Trainer") → Google Analytics kannst du deaktivieren, ist nicht nötig.
3. Im neuen Projekt: "Android-App hinzufügen" → als Package Name exakt `de.dwpersonaltrainer.app` eintragen (muss 1:1 mit der App-ID übereinstimmen).
4. Firebase lässt dich die Datei `google-services.json` herunterladen — das ist die Datei, die diese App braucht.

## 2. google-services.json einsetzen

**Für lokale Builds** (Android Studio auf deinem Rechner): Lege die heruntergeladene Datei einfach unter `android/app/google-services.json` ab (ersetzt den Platzhalter).

**Für den automatischen GitHub-Actions-Build**: Damit der echte Schlüssel nicht offen im Repository liegt, wird er als verschlüsseltes GitHub-Secret hinterlegt:

1. Datei base64-kodieren, z.B. im Terminal: `base64 -w0 google-services.json` (Mac/Linux) oder per Online-Tool.
2. In deinem GitHub-Repo: Settings → Secrets and variables → Actions → "New repository secret".
3. Name: `GOOGLE_SERVICES_JSON`, Wert: der base64-String aus Schritt 1.
4. Beim nächsten Push/Workflow-Lauf ersetzt die Pipeline automatisch den Platzhalter durch deine echte Datei, bevor sie baut.

## 3. Push-Registrierung in der Web-App ergänzen

Die Web-App (die live von app.dw-personal-trainer.de geladen wird) muss selbst aktiv um Erlaubnis fragen und sich bei Firebase registrieren — das kann die native Hülle nicht automatisch für sie tun. Da Capacitor seine native Bridge automatisch in jede geladene Seite einschleust, reicht folgender Code, den du (bzw. wer an der Web-App arbeitet) einmal einbindet, z.B. beim App-Start/Login:

```js
import { PushNotifications } from '@capacitor/push-notifications';

async function setupPush(userId) {
  const perm = await PushNotifications.requestPermissions();
  if (perm.receive !== 'granted') return;

  await PushNotifications.register();

  PushNotifications.addListener('registration', (token) => {
    // Token an dein Backend senden und dort dem eingeloggten User zuordnen,
    // damit du gezielt an ihn/sie senden kannst.
    fetch('/api/push/register', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ userId, token: token.value }),
    });
  });

  PushNotifications.addListener('registrationError', (err) => {
    console.error('Push-Registrierung fehlgeschlagen', err);
  });

  PushNotifications.addListener('pushNotificationReceived', (notification) => {
    console.log('Push erhalten', notification);
  });
}
```

Falls die Web-App das `@capacitor/push-notifications` npm-Paket noch nicht als Abhängigkeit hat, einmal `npm install @capacitor/push-notifications` dort ausführen.

## 4. Push von deinem Backend aus verschicken

Sobald du Tokens gespeichert hast, verschickst du Nachrichten serverseitig über die Firebase Admin SDK (Node/Express, passt zu deinem bestehenden Backend):

```js
const admin = require('firebase-admin');
admin.initializeApp({
  credential: admin.credential.cert(require('./firebase-service-account.json')),
});

async function sendPush(token, title, body) {
  await admin.messaging().send({
    token,
    notification: { title, body },
  });
}
```

Den Service-Account-Schlüssel dafür lädst du in der Firebase Console unter Projekteinstellungen → Dienstkonten → "Neuen privaten Schlüssel generieren" herunter. Diese Datei niemals ins Repository committen — nur als Umgebungsvariable/Secret auf deinem Server hinterlegen.

---

Sobald Schritt 1–2 erledigt sind, baut die App bereits mit echter Firebase-Anbindung (Schritt 3–4 sind für die eigentliche Zustellung an Nutzer nötig, betreffen aber die Web-App/das Backend, nicht dieses native Projekt).
