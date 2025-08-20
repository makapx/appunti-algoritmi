## Algoritmi greedy
Un algoritmo greedy (goloso) sceglie una soluzione **localmente ottima**, sperando che tale scelta porterà ad una soluzione **globalmente ottima**. Non tutti gli algoritmi greedy sono adatti e/o trovano soluzioni ottime ai problemi ma per molti problemi sono più efficaci delle tecniche di programmazione dinamica e danno soluzioni ottimali.

Mentre la programmazione dinamica agisce in maniera bottom-up – risolvendo i sottoproblemi e combinando le soluzioni – gli algoritmi greedy seguono una strategia top-down, scegliendo ogni volta quella che sembra la soluzione più appetibile.

**Correttezza degli algoritmi greedy**
Per dimostrare che un dato algoritmo greedy è corretto dobbiamo:
1. **Verificare la proprietà della scelta greedy**: una soluzione globalmente ottimale può essere costruita facendo scelte localmente ottime. Lo dimostriamo con la **tecnica cut-and-paste**:
	- Sia $g$ la prima scelta fatta dall'algoritmo
	- Prendiamo una qualsiasi soluzione ottimale
	- Se la soluzione ottimale contiene la scelta $g$ la proprietà è dimostrata per questo passo
	- Se la soluzione ottimale non contiene $g$, possiamo modificarla in maniera tale che la contenga, dando una soluzione altrettanto buona o migliore
2. **Verificare la sottostruttura ottima del problema**: una soluzione ottima contiene la suo interno soluzioni ottime per i sotto problemi. La dimostriamo così:
	- Assumiamo di aver fatto la scelta greedy $g$, questo ci lascia con un sotto problema $P'$ da risolvere
	- Ipotizziamo che la soluzione non sia ottimale, possiamo sostituirla con una migliore
	- Sostituendo con una soluzione migliore miglioriamo la soluzione del problema finale. Abbiamo una contraddizione e abbiamo dimostrato che la soluzione al sotto problema è per forza ottimale.
## Problema della selezione della attività
Supponendo di avere una risorsa condivisa, ad esempio un'aula, e una serie di attività che non possono occupare contemporaneamente la risorsa, ad esempio delle lezioni, nel problema della selezione delle attività ci si interroga su quale sia il modo più efficiente di occupare suddetta risorsa (massimizzare il numero di attività eseguibili). Ciascuna attività è caratterizzata da un tempo di inizio e uno di fine.

![[Pasted image 20250817152904.png]]

Il problema della selezione delle attività può essere risolto sia tramite tecnica di programmazione dinamica sia dall'approccio greedy. Volendo risolvere il problema in maniera greedy ci basta ordinare le attività in base al tempo di fine e scegliere di volta in volta l'attività che finisce prima compatibile con quelle già selezionate.

```typescript
// Versione ricorsiva
RecursiveActivitySelector(
	stars: Array<number>, // Tempi di inizio
	ends: Array<number>,  // Tempi di fine
	index: number,        // Indice attività corrente
	n: number             // Numero totale di attività
)
	m = index + 1;
		while (m <= n && starts[m] < ends[index]){
			m = m + 1;
		}
	if (m <= n){
		return [...activities[m], ...RecursiveActivitySelector(stars,ends,m,n)]
	}
	return []; // O un set vuoto
```

```typescript
// Versione iterativa
GreedyActivitySelector(stars: Array<number>, ends: Array<number>)
	n = stars.lenght;
	selectedActivities.push(activities[1]); // O di 0 se gli array partono da 0
	index = 1;
	for (m = 2 to n) {
		if (stars[m] >= ends[index]){
			selectedActivities = [...selectedActivities, activities[m]];
			index = m;
		}
	}
	return selectedActivities;
```
La selezione delle attività greedy iterativa ha una complessità lineare ($\Theta(n)$) nel caso in cui le **attività siano già ordinate rispetto ai loro tempi di fine**.
### Sottostruttura ottima e proprietà di scelta greedy per la selezione attività 
**Dimostrazione: il problema della selezione attività gode della sottostruttura ottima**
Per ipotesi abbiamo una lista di attività $<a_1, ..., a_n>$,  ognuna con un orario di inizio e uno di fine. Il problema principale consiste nel trovare il numero massimo di attività compatibili.

Supponiamo di aver trovato una soluzione ottima (massimo numero di attività) per il problema principale.

Prendiamo prima attività $a_1$ che fa parte del nostro insieme di attività ottimale. Dopo aver scelto questa attività, ci resta da risolvere il sottoproblema:
Trovare il numero massimo di attività compatibili che iniziano dopo la fine di $a_1$. La soluzione che troveremo per questo sottoproblema sarà ottimale a sua volta, il che prova ciò che vogliamo dimostrare.

Ipotizziamo per assurdo che la soluzione al sottoproblema non sia ottimale:
La soluzione principale è ottima ma il sotto problema ha una soluzione migliore (un modo diverso per inserire più attività dopo la fine di $a_1$).
Se esiste una soluzione migliore a questo sottoproblema includendola nel problema finale avremo una nuova soluzione totale, migliore della precedente, portandoci ad un assurdo, visto che per ipotesi quella era già una soluzione ottimale.

**Dimostrazione: il problema della selezione attività gode della proprietà di scelta greedy**
Nella selezione di attività l'approccio greedy prevede di:
1. Ordinare le attività in ordine crescente in base al tempo di fine
2. Scegliere l'attività $A$ che finisce prima e che lascia la risorsa libera per più tempo possibile
3. Scartare le attività incompatibili con quella scelta
4. Ripetere fino a che le attività disponibili (compatibili) finiscono

Supponiamo di scegliere un'altra attività $B$ al posto di $A$, che inizia prima e finisce dopo $A$.
$$B_{start} < A_{start} < A_{end} < B_{end}$$
Qualsiasi altra attività compatibile con $B$, che di conseguenza è anche compatibile con $A$ visti i tempi, potrà venire aggiunta alla soluzione ma non c'è alcun guadagno in termini di numero di attività eseguibili dopo.

Se una data attività è compatibile con $A$ ma non con $B$ si ha una perdita, scegliere quindi $B$ al posto di $A$ può quindi portare esclusivamente ad una soluzione uguale o peggiore, il che prova che la scelta in questione porta ad una soluzione globalmente ottima. 
## Problema dello zaino (frazionario)
Mentre il problema dello zaino classico (detto anche $0-1$) non può essere risolto adottando la strategia greedy ma richiede l'utilizzo di tecniche di programmazione dinamica, la sua variante "frazionaria" (prendere una certa quantità di un dato oggetto) è facilmente risolvibile utilizzando la strategia greedy. ![[Pasted image 20250818123646.jpg]]
```typescript
// Nota: questo pseudo-codice non è stato presentato a lezione
// Aggiunto per completezza ma ad oggi non necessario per l'esame
FractionalKnapsack(
	weights: Array<number>,
	values: Array<number>,
	capacity: number
)
    items = [];
    for i = 0 to weights.length - 1  {
        ratio = values[i] / weights[i];
        items.add(new Item(weights[i], values[i], ratio));
    }

    //Ordinare gli oggetti in base al loro rapporto, in ordine decrescente
    items = sortBy(items, item => item.ratio, DESCENDING);
    maxValue = 0;
    currentCapacity = capacity;

    foreach item in items {
        if (currentCapacity == 0) {
            break; // Lo zaino è pieno
        }

        // Se l'oggetto intero può entrare nello zaino
        if (item.weight <= currentCapacity) {
            // Prendi tutto l'oggetto
            currentCapacity = currentCapacity - item.weight;
            maxValue = maxValue + item.value;
        } else {
            // Prendi solo una frazione dell'oggetto
            fraction = currentCapacity / item.weight;
            maxValue = maxValue + (item.value * fraction);
            currentCapacity = 0; // Lo zaino è ora completamente pieno
        }
    }
    return maxValue;
```

Ordinando gli oggetti in maniera decrescente in base al rapporto peso / valore prendiamo l'oggetto che a parità di minor peso vale di più. Se possiamo prenderlo per intero così facciamo, altrimenti ne prendiamo quanto più possibile. Così facendo massimizziamo il profitto, azzerando o quasi lo spazio sprecato.

L'algoritmo greedy per questa variante del problema dello zaino ha complessità $O(n \ log \ n)$.
### Sottostruttura ottima e proprietà di scelta greedy per il problema dello zaino frazionario
**Dimostrazione: il problema dello zaino frazionario gode della sottostruttura ottima**
Il problema dello zaino si riassume in: dato uno zaino di capacità $W$, riempirlo con frazioni di oggetti in maniera tale da ottenere il valore massimo possibile.

Supponiamo di aver trovato una soluzione ottima per il sottoproblema, ovvero prendere quanto più possibile dell'oggetto più prezioso e riempire lo spazio rimanente. Sullo spazio rimanente si crea il nostro sottoproblema.

Supponiamo di avere una soluzione migliore per il sotto-problema, diversa dallo schema prefissato (scegliere l'oggetto più prezioso a parità di peso). Questa ipotetica soluzione ottimale al sottoproblema migliorerebbe la soluzione al problema principale, facendo aumentare il valore complessivo ottenuto, il che porta ad una contraddizione e dimostra che se la soluzione complessiva è ottima lo sono anche quelle ai sotto-problemi.

**Dimostrazione: il problema dello zaino frazionario gode della proprietà di scelta greedy**
La scelta greedy nel problema dello zaino frazionario consiste nel prendere quanto più possibile dell'oggetto che, a parità di peso, è più prezioso.

Supponiamo che esista una soluzione ottima che non segua questa regola (scegliere un oggetto meno prezioso). A parità di peso, scegliendo un oggetto più prezioso il guadagno finale sarà più alto, quindi la soluzione ipotizzata non è ottima. Questa contraddizione prova che la scelta greedy è corretta.
## Codifica di Huffman
Quando si presenta l'esigenza di codificare una serie di stringhe in binario (es: il contenuto di un file) è possibile utilizzare due tecniche principali:
- codifica a lunghezza fissa
- codifica a lunghezza variabile
Concettualmente la prima è più semplice da comprendere ma richiede spesso più spazio. I codici a lunghezza variabile consentono di rappresentare i caratteri più comuni con serie più brevi di bit, permettendo di risparmiare quindi spazio.

L'esigenza di occupare meno spazio a parità di dati da immagazzinare è detto anche **problema della compressione**.

**Codici prefissi**
Quando nessuna parola **non è prefisso di una qualche altra** parliamo di **codici prefissi** (sarebbe più intuitivo chiamarli codici senza prefissi ma è questo il nome utilizzato di solito).

Vantaggi dei codici prefissi:
- ammesso che un contenuto sia codificabile, è sempre possibile ottenere una compressione ottima
- i codici prefissi semplificano il sistema di decodifica

Quando parliamo di codici prefissi tendiamo a rappresentarli come un **albero binario pieno**, dove:
- ogni nodo contiene un attributo $frequenza$ (numero di occorrenze)
- le foglie sono i caratteri dell'alfabeto che stiamo utilizzando
- i nodi interni sono dei nodi "senza nome" che utilizziamo per tenere conto delle frequenze di apparizione dei caratteri figli
- la radice è il totale delle frequenze di tutti i caratteri, ovvero la lunghezza complessiva del testo
- gli archi verso il figlio sinistro sono etichettati con il valore $0$, quelli verso il figlio destro con il valore $1$

Percorrendo il cammino dalla radice a ciascuna foglia otteniamo la parola di codice che codifica il carattere (il nodo foglia).
![[Pasted image 20250817142357.jpg]]

**Costi della codifica**
Una volta costruito l'albero che rappresenta un codice prefisso, possiamo calcolare il numero di bit richiesti per codificare un contenuto. Abbiamo:
- $C$ l'alfabeto di partenza, dove $c$ è il nodo del singolo carattere
- $c.freq$ la frequenza del singolo carattere
- $d_T(c)$ la lunghezza della parola in codice per il carattere $c$

Otteniamo il costo di tutta la codifica (o meglio dell'albero che rappresenta la codifica):
$$
B(T) = \sum_{c \ \in \ C} c.freq * d_T(c)
$$
**Algoritmo di Huffman**
L'algoritmo greedy ideato da Huffman consente di costruire un codice prefisso ottimo, detto anche **codice di Huffman**.
```typescript
Huffman(alphabet: Array<Node>)
	n = alphabet.lenght;
	queue = new MinPriorityQueue(alphabet);
	for (i = 1 to n - 1){
		z = new Node();
		x = queue.extractMin();
		y = queue.extractMin();
		z.left = x;
		z.right = y;
		z.freq = x.freq + y.freq;
		queue.insert(z);
	}
	return queue.extractMin(); // Restituisce la radice;
```

Partendo dall'alfabeto (una lista con nodi dove ogni nodo tiene traccia di carattere e frequenza) e sfruttando una **coda di min-priorità** (realizzata con un min-heap, l'algoritmo di Huffman costruisce una codifica ottimale adottando passo dopo passo la scelta greedy di prendere dalla coda il valore dalla frequenza minore.

Inizializziamo la coda con i valori dati e durante ciascuna iterazione aggiungiamo anche i nodi interni (i nodi "senza nome" che usiamo per tener traccia della somma delle frequenze).  Durante ciascuna iterazione estrarremo due volte il minimo, ponendo il primo come figlio sinistro e il secondo come figlio destro del nuovo nodo che andremo a creare. 

Il nodo appena creato viene a sua volta inserito nella coda e a tempo debito verrà estratto e diverrà figlio di qualche altro nodo "senza nome". I nodi contenenti i caratteri sono sempre nodi foglie invece.

**Complessità**
La complessità dell'algoritmo di Huffman è legata al modo in cui è implementata la coda al suo interno, supponendo l'utilizzo di un min-heap che sfrutti la procedura `buildMinHeap`
per un insieme di $n$ caratteri l'algoritmo ha complessità $O(n \ log \ n)$ ($log \ n$ per la costruzione della coda, $n$ per le iterazioni date dal ciclo `for`).

### Sottostruttura ottima e proprietà di scelta greedy per l'algoritmo di Huffman
**Dimostrazione: il problema della codifica gode della sottostruttura ottima**
Per ipotesi l'albero di codifica di tutti i caratteri è ottimale. Chiamiamo questo albero ottimo $A$. In questo albero i due caratteri meno frequenti $x$ e $y$ sono fratelli e si trovano al livello più profondo.

Prendiamo il sotto-albero dove $x$ e $y$ si fondono in un nodo $z$, creando un albero più piccolo per il nostro sottoproblema. Chiamiamo questo albero $B$.

Supponiamo che l'albero $B$ non rappresenti una soluzione ottimale al sottoproblema, questo implica che esiste un terzo albero, $C$, migliore.

Sostituiamo allora $C$ a $B$. Questo creerebbe una soluzione complessiva migliore di quella di partenza, che avevamo detto essere ottima. Questa contraddizione prova che le soluzioni ai sotto-problemi sono a loro volta ottime.

**Dimostrazione: il problema della codifica gode della proprietà di scelta greedy**
La scelta greedy di Huffman consiste nel prendere i due caratteri dalle frequenze più basse e unirli. Questa scelta localmente ottima relega i caratteri meno frequenti alla parte più in basso dell'albero di codifica finale.

Prendiamo per ipotesi un albero di codifica ottimale, in questo albero troviamo all'ultimo livello i nodi figli che hanno il codice prefisso più lungo (non stiamo facendo assunzioni sulla frequenza). Chiamiamo questi nodi $a$ e $b$. 

Prendiamo adesso i due caratteri con le frequenze più basse, chiamiamoli $x$ ed $y$.

Sappiamo per certo che: $$x \le a \le y \le b$$
Se $x$ e $y$ sono diversi da $a$ e $b$, supponiamo di sostituirceli. Vista la relazione sopra il costo totale è uguale se non minore.

Se fosse minore migliorerebbe la soluzione complessiva che avevamo già detto essere ottimale, il che quindi non può essere.

Ci ritroviamo nel caso in cui il costo è invariato. Questo dimostra che possiamo sempre modificare un albero ottimale per fare in modo che i due caratteri meno frequenti siano fratelli al livello più basso, senza peggiorare il risultato, ottenendo quindi un'altra soluzione ottimale senza invalidare la precedente.
## Complessità

| Algoritmo          | Complessità      | Note                                                                                                                     |
| ------------------ | ---------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Zaino frazionario  | $O(n \ log \ n)$ | Complessità data dall'ordinamento, il riempimento avviene in ordine lineare ma viene sovrastato in termini di grandezza. |
| Selezione attività | $\Theta(n)$      | Se le attività sono già ordinate in base ai tempi di fine                                                                |
| Huffman            | $O(n \ log \ n)$ | La complessità si riferisce all'implementazione con min-heap                                                             |

## Guida agli esercizi
- Dimostrare la correttezza degli algoritmi citati: per farlo bisogna dimostrare la proprietà della sottostruttura ottima e quella della scelta greedy, oppure dimostrare una sola delle due
- Riportare gli pseudo codici e le loro complessità
- Dato un alfabeto e una stringa, disegnare l'albero di codifica e riportare le varie parole di codice associate ai caratteri