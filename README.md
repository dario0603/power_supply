# Linear Power Supply Project

Progetto universitario per la realizzazione e simulazione di un **Alimentatore da Banco Stabilizzato** , completo di schemi elettrici KiCad, simulazioni LTspice, documentazione tecnica e file Gerber per la produzione del PCB.

---


## Panoramica del Progetto

Il progetto riguarda lo studio, la modellizzazione e la progettazione hardware di un alimentatore stabilizzato.

## 1. Obiettivi del Progetto
Il progetto illustra la progettazione di un alimentatore da banco stabilizzato con le seguenti caratteristiche principali:
*   **Tensione di uscita regolabile:** da $0V$ a $20V$ (continua).
*   **Corrente di uscita limitabile e regolabile:** da $0A$ a $5A$.
*   **Modalità Tracking:** permette di erogare in uscita una tensione pari a quella applicata su appositi pin esterni (utile per generare tensioni duali).
*   **Monitoraggio:** Misura in tempo reale di tensione e corrente con visualizzazione a schermo.

## 2. Architettura del Circuito di Potenza
L'alimentatore è strutturato in blocchi funzionali classici:
*   **Trasformatore:** Abbassa la tensione di rete (230V, 50Hz) a un valore di $25V$ efficaci sul secondario.
*   **Raddrizzatore:** È stato scelto un **raddrizzatore a doppia semi-onda** (ponte di diodi) poiché garantisce un fattore di forma migliore e richiede un condensatore di filtro di capacità dimezzata rispetto alla singola semi-onda ($C \approx 14286 \mu F$ per un ripple di $3,5V$).
*   **Filtro Capacitivo:** Livella la tensione raddrizzata riducendo la tensione di ripple.
*   **Regolatore Lineare:** Prende in ingresso la tensione filtrata e la stabilizza al valore desiderato tramite un amplificatore di errore e un transistor di passo.

## 3. Circuito Regolatore e Limitazione di Corrente
Le scelte progettuali più rilevanti per la regolazione e la protezione includono:
*   **Integrato LM723:** Utilizzato come base per il circuito di regolazione della tensione.
*   **Transistor di Potenza (Pass Transistors):** Viene utilizzata una configurazione Darlington per garantire un guadagno di corrente ($\beta_f$) sufficiente. Sono impiegati più transistor di potenza (TIP3055 o 2N3055) **collegati in parallelo** per dividere il carico di corrente.
    *   *Problema critico risolto:* Per evitare il "run-down termico" (squilibrio di corrente dovuto alle variazioni di $\beta_f$ con la temperatura), sono state inserite piccole resistenze di emettitore in serie ai BJT.
*   **Limitatore di Corrente:** Sfrutta una resistenza di shunt ($R_{sh} = 0,2 \Omega$) in serie al carico. La caduta di tensione ai suoi capi è letta da un amplificatore differenziale e confrontata (comparatore) con una tensione di riferimento ($V_{zi}$). Se la corrente supera la soglia, un transistor devia la corrente di base del BJT di passo, limitando l'uscita.

## 4. Alimentazioni Ausiliarie
Per il corretto funzionamento degli amplificatori operazionali (TL082) e della logica di controllo, il circuito ricava tensioni di servizio secondarie:
*   $\pm12V$ stabilizzate (utilizzando LM7812 e LM7912).
*   $+5V$ stabilizzati (tramite LM7805).
*   Tensioni negative specifiche create con diodi Zener per rispettare i limiti di alimentazione degli op-amp nella sezione di tracking.

## 5. Circuiti di Misura (Voltmetro e Amperometro)
Per fornire misurazioni precise all'ADC del microcontrollore (fondo-scala $5V$):
*   **Voltmetro a Guadagno Variabile:** Utilizza un amplificatore non invertente dove il guadagno viene modificato selezionando resistenze di retroazione diverse tramite switch C-MOS analogici (74HC4066). Gestisce fondo-scala di $2V, 10V, 25V, 100V$.
*   **Amperometro con Resistore di Shunt:** Amplifica il segnale prelevato dalla stessa resistenza di shunt della limitazione. Anche qui si usano guadagni variabili per fondo-scala di $200mA, 1A, 2.5A, 8A$.

## 6. Integrità del Segnale e Analisi Termica
*   **Signal Integrity:** È stata calcolata l'induttanza, la capacità e la resistenza parassita delle piste del PCB più critiche. Le simulazioni sul "ringing" hanno confermato che con tempi di salita/discesa $> 5ns$, l'overshoot è trascurabile (sotto il 5%).
*   **Analisi Termica:** Essenziale per i dispositivi di potenza. Analizzando le curve di derating, è stato calcolato il valore di resistenza termica del dissipatore ($R_{thda}$) per i transistor e per il ponte di diodi, evidenziando la necessità (nel caso del TIP3055) di usare la pasta termica e l'eventuale ventilazione forzata per mantenere i componenti sotto le temperature critiche di $125^\circ C / 150^\circ C$.
*   **FMEA (Failure Mode and Effects Analysis):** La relazione contiene tabelle dettagliate per valutare Gravità (S), Occorrenza (O) e Rilevabilità (D) in caso di guasti a condensatori o componenti chiave, calcolando l'indice di rischio globale (RPN).

---

## Simulazioni LTspice

All'interno della directory `Alimentatore taratura manuale/LTspice/` sono presenti due modelli di simulazione (da `v1.0` a `v2.0`):
contenenti vari analisi, tra cui l'Analisi di Ripple e Filtraggio

![Simulazione Tensioni](Relazione/Immagini/Simulazione%20filtro%20e%20raddrizzatore%20tensioni.PNG)
![Simulazione Ripple](Relazione/Immagini/Simulazione%20filtro%20e%20raddrizzatore%20ripple.PNG)

---

## 🛠️ Progettazione Hardware (KiCad)

I file di progetto PCB e schematici sono realizzati con **KiCad**.

**BOM Interattiva**: Disponibile all'interno delle cartelle KiCad sotto forma di `ibom.html` per facilitare l'assemblaggio dei componenti.

---

## 📄 Documentazione e Relazione

Per un'analisi approfondita dei calcoli teorici, delle scelte progettuali e delle prestazioni misurate, fare riferimento alla relazione completa presente nella repository:

* [Relazione Costruzioni (PDF)](Relazione/Relazione%20Costruzioni.pdf)

---

## Struttura della Repository

```text
.
├── Relazione/                        # Documentazione e report tecnico
│   ├── Relazione Costruzioni.pdf     # Documento finale in formato PDF
│   ├── Immagini/                     # Schemi, grafici e diagrammi usati nella relazione
│   └── Simulazioni/                  # File di simulazione generali LTspice
│
├── Alimentatore taratura manuale     # Schemi KiCad e simulazioni LTspice
│   ├── LtSpice
│   └── KiCad      
└── Controllore taratura manuale      # Schemi del controllore manuale
    
