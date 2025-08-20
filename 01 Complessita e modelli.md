**Definizione informale di algoritmo**
Un algoritmo è un procedimento formato da una sequenza finita di operazioni elementari (o passi) che ci consentono di risolvere un dato problema computazionale.

Ogni problema computazionale è caratterizzato da dei dati in ingresso (valori in input) e da dei dati in uscita (valori in output). Risolvere un problema computazionale significa fornire un algoritmo che consente di ottenere i risultati attesi a partire da un insieme di dati in input.

**Definizione formale di algoritmo**
Un algoritmo è un insieme finito e ordinato di passi semplici, materialmente eseguibili da un agente di calcolo e non ambigui, che definiscono un procedimento atto a risolvere un problema (o una classe di problemi) utilizzando dei dati in input e producendo dei dati in output.

Un algoritmo deve avere queste quattro caratteristiche fondamentali:
1. Finitezza: la sequenza di passi di cui si compone deve essere finita
2. Risolutività: i passi di cui si compone devono portare ad un risultato (la soluzione del problema o una diagnostica sulla mancata soluzione)
3. Effettuabilità: i passi devono essere eseguibili materialmente, deve quindi esistere un agente di calcolo in grado di eseguire ogni passo in un tempo finito
4. Definitezza: ogni passo deve essere non ambiguo - non solo i passi devono essere espressi chiaramente, ma il passaggio da un passo al successivo deve avvenire in modo esplicitamente previsto dall'algoritmo

**Definizione di struttura dati**
Una struttura dati è un insieme di valori logicamente correlati e opportunamente memorizzati, per i quali sono stati definiti dei costruttori, degli operatori di selezione e degli operatori di manipolazione.

Possono essere:
- **statiche**: la quantità di memoria di cui necessitano è determinata a priori
- **dinamiche**: la quantità di memoria varia in base al tempo e può essere diversa di esecuzione in esecuzione

## Modelli di calcolo
Nel modello Mono - processore + RAM (Random Access Memory) abbiamo assenza di concorrenza e parallelismo.
Analizzare un algoritmo significa determinare:
- la quantità di memoria utilizzata durante l'esecuzione
- il tempo computazione (o di esecuzione)

Il tempo di esecuzione nel senso comune varia a seconda della potenza della macchina e di altre condizioni a contorno quindi quando si parla di tempo di esecuzione negli algoritmi si intente una misura assoluta, una complessità, data dal numero di volte in cui una determinata istruzione viene valutata.
