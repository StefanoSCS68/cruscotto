# Cruscotto Conto – guida all'installazione

App per il telefono: **tableau de bord** degli Obiettivi 2027 (margine sul fido, fine 2027 vs fine 2026), saldi, budget del mese (carte ed extra), traiettoria del minimo mensile rispetto al piano, ultimo mese chiuso e mesi da presidiare. Tutti i valori sono calcolati dalla dashboard (sintesi v2): l'app li mostra soltanto.
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

Dal 16/10/2026 l'app usa solo l'autorizzazione `drive.file`: può leggere esclusivamente il file `cruscotto.enc.json` che selezioni con il selettore di Google, nessun altro file del tuo Drive (prima chiedeva anche `drive.readonly`, cioè la lettura di tutto Drive). App in modalità test: utilizzabile solo dal tuo account.

## 5. Installazione sul Samsung

1. Apri `https://NOMEUTENTE.github.io/cruscotto/` in **Chrome**.
2. **Collega Google Drive** → accedi → se compare "Google non ha verificato questa app" scegli *Continua* (è la tua app, in modalità test).
3. Apri la cartella **DashboardFinanziario** e seleziona il file **cruscotto.enc.json** → *Seleziona*.
4. Inserisci la password di cifratura (puoi spuntare "Ricorda su questo telefono": si conserva una chiave non leggibile, mai la password).
5. Menu **⋮ › Installa app** (o *Aggiungi a schermata Home*).

## Uso quotidiano

| Dove | Cosa fai | Effetto |
|---|---|---|
| Mac | Apri la dashboard o premi **Salva** | La dashboard aggiorna `cruscotto.enc.json` |
| Telefono | **↻ Aggiorna** | L'app scarica la sintesi più recente |

Dal 07/10/2026 l'app è in sola lettura (nessuna registrazione di spese dal telefono). Sotto la data dei dati l'app mostra la data dell'ultimo movimento del conto importato nella dashboard; oltre 2 giorni lavorativi compare «⚠ movimenti fermi al …».

**Prova senza dati reali:** `https://NOMEUTENTE.github.io/cruscotto/?demo=1`

## Sicurezza

- Dati cifrati con AES-GCM 256 bit, chiave derivata dalla password (PBKDF2-SHA256, 250.000 iterazioni). Google vede solo file illeggibili.
- La password non viene mai inviata a nessuno. Se la perdi: premi 📱 in una nuova sessione della dashboard dopo aver cancellato i due file dalla cartella; ne verrà chiesta una nuova.
- «Ricorda su questo telefono» conserva una chiave di decifratura **non estraibile** (IndexedDB del browser), mai la password. Se cambi la password nella dashboard, l'app la richiede una volta.
- Se il file `cruscotto.enc.json` su Drive viene sostituito o cestinato, l'app chiede di selezionarlo di nuovo.
- "Scollega / reimposta" (in fondo all'app) cancella dal telefono collegamento, chiave memorizzata e dati.

## Aggiornamento del 16/10/2026 (versione cache cruscotto-v8)

1. Su GitHub, repository **cruscotto**: *Add file › Upload files* → carica `index.html`, `sw.js` e `README.md` (sostituiscono quelli esistenti) → *Commit changes*.
2. Sul telefono apri l'app, chiudila e riaprila (o tocca ↻): parte la nuova versione. La password ricordata viene convertita in chiave e cancellata.
3. Tocca **Seleziona il file su Google Drive**: al primo tocco Google revoca il vecchio permesso; al secondo concedi il solo accesso ai file scelti, poi seleziona `cruscotto.enc.json` nella cartella DashboardFinanziario.
4. Verifica: su myaccount.google.com › Sicurezza › Le tue connessioni a app e servizi di terze parti, «Cruscotto Conto» deve avere solo l'accesso ai file usati con l'app.
