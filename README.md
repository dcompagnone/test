# Voxelmania — Documentazione del Configuratore & Preventivatore 3D

Applicazione web single-page (HTML/CSS/JS) progettata per il calcolo rapido e preciso dei preventivi di stampa 3D (Resina e FDM), gestione dei listini per fascia dimensionale, packaging, margini e persistenza locale delle impostazioni.

---

## 1. Architettura e Gestione Dati (`localStorage` & `settings.json`)

L'applicazione gira interamente lato client (nel browser) senza necessità di un database o server backend:
* **Chiave di memoria:** Tutti i parametri vengono salvati localmente nel browser dell'utente tramite `localStorage` sotto la chiave `voxelmania_settings_json`.
* **Controllo all'avvio:** Al caricamento della pagina, lo script verifica l'esistenza di una configurazione valida in memoria. Se assente (es. primo accesso o cache svuotata), apre automaticamente la **Modale Impostazioni**.
* **Portabilità:** È possibile esportare la configurazione corrente in un file `settings.json` locale e reimportarla su qualsiasi altro dispositivo o browser con validazione automatica della struttura dati.

---

## 2. Struttura dell'Interfaccia Principale (SPA)

L'interfaccia è divisa in due aree operative principali: **l'area di configurazione del preventivo** (a sinistra/centro) e **la barra laterale di riepilogo e confronto** (a destra).

### A. Barra Superiore (Header)
* **Titolo e Versione:** Identifica l'istanza corrente dell'applicazione (es. `v12.0`).
* **Pulsante ⚙️ Impostazioni:** Apre in qualsiasi momento la modale di gestione dei listini e dei parametri di base.
* **Pulsante Reset / Nuovo Preventivo:** Azzera i campi selezionati per iniziare un nuovo calcolo mantenendo intatti i listini salvati.

### B. Sezione Parametri del Modello / Stampa
Permette di inserire le specifiche tecniche del pezzo da produrre:
* **Tecnologia di Stampa:** Selezione tra **Resina** (alta definizione, miniature/statue) e **FDM** (filamento, parti strutturali o grandi dimensioni).
* **Fascia Dimensionale (Taglia):** Selettore rapido tra le taglie preconfigurate (**S**, **M**, **L**, **XL**), ciascuna associata a range volumetrici/dimensionali e tariffe base specifiche.
* **Parametri Variabili (Peso / Volume / Ore):** Campi numerici per inserire il consumo effettivo di materiale (grammi o millilitri) e il tempo macchina stimato, utilizzati per calcolare il costo vivo reale.
* **Quantità:** Moltiplicatore di copie per lo stesso modello (attiva eventuali sconti volume se configurati).

### C. Sezione Lavorazioni Extra e Packaging
* **Post-Processing / Finitura:** Opzioni per includere o escludere il lavoro manuale (rimozione supporti, lavaggio IPA, cura UV, primerizzazione o verniciatura).
* **Packaging e Imballaggio:** Gestione del costo di protezione per la spedizione (scatola dedicata, pluriball, materiale antiurto), calcolato come quota fissa o in base alla taglia selezionata.
* **Spedizione:** Voce opzionale per aggiungere il costo del corriere al totale finale del cliente.

---

## 3. I Box di Riepilogo (Sidebar Destra)

La colonna di destra mostra in tempo reale l'elaborazione economica del preventivo, suddivisa per offrire sia un riferimento commerciale rapido sia il dettaglio analitico.

### 📦 Box 1: Listino Indicativo (Prezzo al Pubblico / Standard)
Rappresenta la stima commerciale basata sulle fasce dimensionali preimpostate (**S, M, L, XL**) e sui moltiplicatori standard di categoria.
* **Funzione:** Serve per dare una risposta immediata al cliente ("quanto costa indicativamente una statua di questa taglia?") prima ancora di effettuare lo slicing preciso del file 3D.
* **Voci incluse:** Tariffa base della taglia selezionata, coefficiente di complessità/tecnologia e packaging standard associato alla dimensione.

### 📊 Box 2: Listino Effettivo (Calcolo Analitico Reale)
Rappresenta il preventivo esatto calcolato sui parametri reali inseriti dopo lo slicing del modello.
* **Costo Vivo Materiale & Macchina:** Calcolo basato su grammi/ml consumati, costo al kg/litro della resina o filamento, consumo elettrico e ammortamento stampante.
* **Manodopera & Post-Produzione:** Costo orario o forfettario per pulizia, rimozione supporti e preparazione.
* **Packaging & Logistica:** Costo reale dell'imballaggio selezionato.
* **Margine / Ricarico Applicato:** Mostra il ricarico netto o la percentuale di profitto applicata sopra i costi vivi.
* **Totale Finale (IVA inclusa/esclusa):** Il prezzo definitivo da comunicare al cliente, affiancato dal confronto diretto (differenza in € o %) rispetto al *Listino Indicativo*.

---

## 4. La Modale di Configurazione (`Impostazioni`)

La modale permette di personalizzare ogni singolo parametro matematico e commerciale senza dover modificare il codice sorgente HTML/JS.

### Barra Azioni della Modale (Header)
* **📂 Importa (`settings.json`):** Apre il selettore file del sistema operativo. Legge un file `.json`, ne verifica la validità strutturale (controllando che non manchino chiavi essenziali) e popola tutti i campi della modale.
* **💾 Esporta (`settings.json`):** Scarica sul computer un file `.json` aggiornato con tutti i valori attualmente presenti nella modale, utile per backup o per condividere la configurazione con altri collaboratori.
* **✅ Salva:** Scrive i dati nel `localStorage` del browser, aggiorna istantaneamente i calcoli nella pagina principale e chiude la modale.
* **❌ Chiudi / Annulla:** Chiude la modale scartando le eventuali modifiche non salvate.

### Campi di Configurazione Interni
I campi sono organizzati in griglie compatte ottimizzate per l'inserimento rapido di valori numerici fino a 4 cifre:
1. **Tabella Taglie (S, M, L, XL):** Permette di definire per ciascuna taglia i limiti dimensionali (es. altezza in cm), il prezzo base del *Listino Indicativo* e il costo di packaging associato.
2. **Costi Materie Prime & Macchina:** Tariffe al kg/litro per le diverse resine/filamenti, costo energetico orario e tasso di ammortamento/manutenzione.
3. **Parametri Manodopera & Margini:** Costo orario dell'operatore per il post-processing e moltiplicatori di ricarico (markup) per ottenere il prezzo finale al pubblico.