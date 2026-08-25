# ConsalusTV per Windows — canale aggiornamenti

Questo repository **non contiene il codice**: contiene i file che l'applicazione
installata scarica per aggiornarsi da sola.

- `aggiornamento-windows.json` — il manifesto: versione, note, e per ogni file
  la sua impronta SHA-256 e il suo indirizzo.
- `jar/` — i file dell'applicazione.

## Come funziona

L'app legge il manifesto, confronta l'impronta di ogni file con quello che ha
già sul disco e **scarica solo ciò che è diverso**. Dei 54 MB elencati, a un
rilascio normale ne cambia uno solo: il nostro, che pesa 600 kB. Le librerie si
muovono qualche volta l'anno, il runtime Java quasi mai.

I file scaricati non sostituiscono niente subito — su Windows un jar aperto non
si può riscrivere. Vengono messi da parte, verificati, e scambiati al riavvio
successivo.

## Perché è separato dal repository dei rilasci Android

L'APK legge il suo manifesto da `releases/latest/download/` sul repo condiviso.
Pubblicando lì anche Windows, un rilascio Windows sarebbe diventato il `latest`
del repository — e nel nuovo `latest` il manifesto dell'APK non c'è. I Fire TV
avrebbero smesso di aggiornarsi **senza dire niente**.

## Come si pubblica

Dal repository del codice (`ConsalusTV-Win`):

```
gradlew preparaPacchetto
node tools/genera-rilascio-windows.mjs \
  https://raw.githubusercontent.com/meloniwastaken/consalustv-releases-win/main/jar
```

Poi si copiano qui `build/staging/*.jar` e `build/aggiornamento-windows.json`, e
si fa push. Le impronte le calcola lo strumento **dagli stessi file che si
pubblicano**: scriverle a mano è solo un modo per sbagliarle, e una SHA-256
sbagliata non si vede a occhio — si vede come un programma che scarica e butta
senza che nessuno capisca perché.
