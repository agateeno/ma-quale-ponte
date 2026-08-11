# Ma quale ponte

Clicca qui per pianificare il tuo viaggio: https://agateeno.github.io/ma-quale-ponte

Un pianificatore di viaggio per una rete ad alta velocità siciliana che **non esiste**.

Sei linee, diciotto fermate, ventitré tratte e un orario cadenzato completo. Scegli partenza e arrivo e
ottieni corse, coincidenze, cambi e tempi di attesa esattamente come faresti su un sito ferroviario vero.
Solo che i treni non ci sono.

![Licenza MIT](https://img.shields.io/badge/licenza-MIT-black)
![Nessuna dipendenza](https://img.shields.io/badge/dipendenze-nessuna-black)

---

## Perché

Un po' per divertimento, perché disegnare una rete è un gioco bellissimo.

Un po' per curiosità tecnica: volevo vedere fin dove arriva Claude se gli chiedi una cosa intera e non un
frammento — la geometria della mappa, le coste in proiezione, l'algoritmo delle coincidenze, i percorsi
alternativi.

Un po' — parecchio — per satira. La cosa più istruttiva di questo progetto non è quanto sia bello l'orario:
è quanto sia *ordinario*. Niente treni a levitazione magnetica, niente tunnel sotto l'Etna. Solo una regione
in cui da un capoluogo si arriva a un altro capoluogo in un'ora e mezza, con partenze a ogni ora tonda. È il
minimo sindacale di qualunque regione europea, ed è diventato materiale da fantascienza solo perché siamo noi.

E soprattutto perché vale la pena dirlo ad alta voce, agli italiani e a chi governa in particolare: le
priorità della Sicilia non sono un monumento, sono i pendolari, gli studenti fuori sede, i turisti che
atterrano a Palermo e devono raggiungere Agrigento, i paesi dell'interno che si svuotano perché arrivarci è
un'impresa. La Sicilia è una regione fantastica e merita un'infrastruttura altrettanto fantastica.

C'è anche un interruttore, nelle impostazioni, che accende l'unica opera di cui si parla davvero. Lasciarlo
spento è il senso di tutto il resto.

---

## Come si apre

La pagina legge gli orari da due file JSON affiancati, e i browser vietano queste letture a un file aperto
con doppio clic (`file://`). Serve quindi un server, anche il più banale:

```bash
python3 -m http.server 8765
```

Poi apri <http://localhost:8765>.

Su GitHub Pages funziona senza configurazione: basta attivare Pages sul branch `main`. Se apri il file da
disco la pagina non si rompe, ma mostra un riquadro che spiega il problema.

---

## Struttura

| File | Contenuto |
|---|---|
| `index.html` | Pagina intera: interfaccia, geometria della mappa, algoritmi. |
| `orari-linee.json` | Linee, fermate, cadenze, prime e ultime partenze. |
| `tempi-percorrenza.json` | Minuti fra fermate adiacenti e coincidenza minima. |

I due JSON sono l'unica fonte dei numeri: gli stessi valori finiscono sulle etichette della mappa e dentro
l'orario, quindi non possono divergere. Nell'HTML restano solo le coordinate dello schema e i colori.

---

## Modificare la rete

**Cambiare un tempo di percorrenza** — apri `tempi-percorrenza.json` e cambia i minuti. Si aggiornano insieme
l'etichetta sulla mappa, gli orari di tutte le corse di quella linea e il calcolo dei percorsi.

```json
{ "linea": "L1", "da": "Cefalù", "a": "Milazzo", "minuti": 50 }
```

**Cambiare la frequenza o gli estremi del servizio** — apri `orari-linee.json`. `cadenza` è l'intervallo fra
due partenze in minuti, `cadenzaPunta` quello nelle fasce dichiarate in `orePunta` (facoltativo), `andata` e
`ritorno` la prima e l'ultima partenza dai due capolinea.

```json
{
  "id": "L5",
  "nome": "L5 Sicana",
  "colore": "var(--c5)",
  "numeroBase": 9500,
  "fermate": ["Palermo Centrale", "Agrigento"],
  "cadenza": 60,
  "andata":  { "prima": "06:10", "ultima": "21:10" },
  "ritorno": { "prima": "06:00", "ultima": "21:00" }
}
```

Le corse hanno numero `numeroBase`, `+2` a ogni partenza successiva; i dispari sono il senso di ritorno.

**Aggiungere una linea** servono tre cose: la voce in `orari-linee.json`, una tratta in
`tempi-percorrenza.json` per ogni coppia di fermate consecutive, e nell'HTML il tracciato in `EDGES` più la
variabile CSS del colore (`--c1` … `--c7`, in entrambi i temi e nella palette per daltonici).

Se una linea dichiara due fermate consecutive per cui non esiste una tratta, la pagina si ferma e dice quale:
meglio un errore in faccia che un orario sbagliato in silenzio.

---

## Come funziona

- La rete è un grafo di 18 fermate e 23 tratte. Il percorso si calcola con Dijkstra sugli stati
  *(stazione, linea)* anziché sulle sole stazioni, così ogni cambio treno costa davvero (8 minuti).
- Le alternative si ottengono escludendo a turno una tratta del percorso migliore e tenendo quelle che non
  perdono troppo tempo.
- L'orario nasce dai cadenzamenti: nessuna corsa è scritta a mano, sono tutte generate.
- Le coste di Sicilia e Calabria sono spezzate in proiezione equirettangolare (parallelo di riferimento
  37,5°) addolcite con Catmull–Rom, quindi le stazioni stanno alle loro coordinate geografiche vere.

---

## Crediti

Idea, rete, orario e testi: **agateeno**.

Scritto insieme a **Claude** di Anthropic, che ha messo il grosso del codice. Metterne alla prova le capacità
era una delle ragioni del progetto, quindi sarebbe scorretto non dirlo.

Nessuna libreria, nessun framework, nessuna CDN, nessun font remoto, nessuna analitica, nessun cookie.
Caratteri di sistema, icone emoji Unicode. Non c'è niente da attribuire a terzi perché non c'è niente
di terzi.

---

## Licenza

[MIT](LICENSE). Forka, cambia i tempi, aggiungi la tua linea, disegna la tua regione, costruisci i ponti.
