# Cruscotto Conto – guida all'installazione

App per il telefono che mostra l'andamento del conto rispetto al target, i residui delle carte, gli extra/contanti prelevabili e il condominio.
I dati arrivano dalla dashboard del Mac tramite Google Drive, **cifrati con una password scelta da te**. Su GitHub viene pubblicato solo il codice dell'app, mai i dati.

---

## 1. Google Drive sul Mac (una tantum)

1. Installa **Google Drive per desktop** (google.com/drive/download) e accedi con il tuo account Gmail personale.
2. Nel Finder, dentro *Google Drive › Il mio Drive*, crea la cartella **DashboardFinanziario**.

## 2. Primo collegamento della dashboard (una tantum)

1. Apri la dashboard in Chrome e premi il pulsante **📱** accanto al pulsante blu di salvataggio.
2. Seleziona la cartella **DashboardFinanziario** e autorizza l'accesso.
3. Scegli una **password di cifratura** (almeno 8 caratteri, non riutilizzata altrove) e ripetila.
4. Nella cartella compare il file `cruscotto.enc.json` (scritto solo dalla dashboard). L'app creerà il proprio file `spese_app.enc.json` (scritto solo dall'app).

Da quel momento:
- **ogni volta che salvi** la dashboard (pulsante blu), la sintesi per l'app viene aggiornata automaticamente, se nella sessione hai già inserito la password;
- premendo **📱** aggiorni l'app e importi le spese registrate dal telefono (con conferma).

## 3. Pubblicazione dell'app su GitHub Pages (una tantum)

1. Su github.com crea un repository chiamato **cruscotto** (pubblico: contiene solo codice).
2. *Add file › Upload files*: carica tutti i file di questa cartella (`index.html`, `config.js`, `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png`, `README.md`).
3. *Settings › Pages*: in "Build and deployment" scegli **Deploy from a branch**, branch **main**, cartella **/(root)** e salva.
4. Dopo 1–2 minuti l'app è raggiungibile a: `https://NOMEUTENTE.github.io/cruscotto/`

## 4. Autorizzazione Google (una tantum, circa 10 minuti)

Su **console.cloud.google.com**, con il tuo account Gmail personale:

1. **Nuovo progetto**: nome "Cruscotto Conto".
2. *API e servizi › Libreria*: abilita **Google Drive API** e **Google Picker API**.
3. *API e servizi › Schermata consenso OAuth*: tipo **Esterno**; nome app "Cruscotto Conto"; email di supporto: la tua. Lascia lo stato **In test** e, in *Utenti di test*, aggiungi il tuo indirizzo Gmail.
4. *Credenziali › Crea credenziali › ID client OAuth*:
   - tipo **Applicazione web**;
   - *Origini JavaScript autorizzate*: `https://NOMEUTENTE.github.io`
   - copia l'**ID client**.
5. *Credenziali › Crea credenziali › Chiave API*: copia la chiave, poi *Modifica chiave*:
   - *Restrizioni applicazione*: **Referrer HTTP** → `https://NOMEUTENTE.github.io/*`
   - *Restrizioni API*: Google Drive API e Google Picker API.
6. *Impostazioni progetto* (menu ⋮ in alto): copia il **Numero di progetto**.
7. Su GitHub apri `config.js` › icona matita, inserisci i tre valori e salva (*Commit changes*):

```js
CLIENT_ID: "…apps.googleusercontent.com",
API_KEY:   "AIza…",
APP_ID:    "numero di progetto"
```

L'app usa le autorizzazioni `drive.readonly` (lettura, usata solo per trovare `cruscotto.enc.json` nella cartella) e `drive.file` (scrittura, limitata al file creato dall'app `spese_app.enc.json`). App in modalità test: utilizzabile solo dal tuo account.

## 5. Installazione sul Samsung

1. Apri `https://NOMEUTENTE.github.io/cruscotto/` in **Chrome**.
2. **Collega Google Drive** → accedi → se compare "Google non ha verificato questa app" scegli *Continua* (è la tua app, in modalità test).
3. Seleziona la cartella **DashboardFinanziario** → *Seleziona*.
4. Inserisci la password di cifratura (puoi spuntare "Ricorda su questo telefono").
5. Menu **⋮ › Installa app** (o *Aggiungi a schermata Home*).

## Uso quotidiano

| Dove | Cosa fai | Effetto |
|---|---|---|
| Mac | Aggiorni la dashboard e salvi | L'app riceve la nuova sintesi (tocca ↻ Aggiorna) |
| Telefono | **+** → spesa extra/contanti o carta | La spesa è subito conteggiata nell'app e inviata su Drive "in sospeso" |
| Mac | Premi **📱** | Ti vengono proposte le spese dal telefono da importare |

**Prova senza dati reali:** `https://NOMEUTENTE.github.io/cruscotto/?demo=1`

## Sicurezza

- Dati cifrati con AES-GCM 256 bit, chiave derivata dalla password (PBKDF2-SHA256, 250.000 iterazioni). Google vede solo file illeggibili.
- La password non viene mai inviata a nessuno. Se la perdi: premi 📱 in una nuova sessione della dashboard dopo aver cancellato i due file dalla cartella; ne verrà chiesta una nuova.
- "Scollega / reimposta" (in fondo all'app) cancella dal telefono collegamento, password memorizzata e dati.
