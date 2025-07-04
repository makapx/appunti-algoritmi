Un heap binario è una struttura dati composta da un array che possiamo interpretare come un albero binario **quasi completo**, ovvero dove tutti i livelli sono riempiti, tranne eventualmente l'ultimo, che può essere riempito da **sinistra verso destra**.

Ogni nodo dell'albero corrisponde ad un elemento dell'array.

**Attributi chiave**
- $arrayLength$ (o solo $length$) la dimensione dell'array
- $heapSize$ la dimensione dell'heap

Vale la relazione
$$0 \le heapSize \le arrayLength$$
Per semplificare le operazioni poniamo la radice dell'albero alla posizione $1$ dell'array (se scegliamo la posizione $0$ dobbiamo poi addizionare in $1$ tutte le operazione).

Abbiamo bisogno di due attributi distinti perchè non sempre la dimensione dell'heap e dell'array corrispondono, ad esempio quando effettuiamo un heapsort ci ritroviamo a *cancellare logicamente* i valori dal heap ma rimangono comunque scritti nella parte finale dell'array.
## Altezza
- altezza di un nodo: numero di archi che compongono il cammino semplice dalla radice a suddetto nodo
- altezza dell'heap: numero di archi che vanno dalla radice ad una foglia di ultimo livello.

L'altezza della radice è quindi 0, mentre l'altezza del heap è $\Theta(log \ n)$ con $n$ numero di elementi dell'heap. Tutte le operazioni effettuate sull'heap sono proporzionali all'altezza, quindi richiedono al più un tempo $O(log \ n)$.

## Relazioni
Se $i$ è un generico indice di un nodo dell'albero, otteniamo il genitore e i figli (destro e sinistro) con le operazioni:

```typescript
const parent = (i: number) => Math.floor(i/2)
//oppure
const parent = (i: number) => i >> 1
```

```typescript
const left = (i: number) => 2 * i
//oppure
const left = (i: number) => i << 1
```

```typescript
const right = (i: number) => (2 * i) + 1
//oppure
const right = (i: number) => (i << 1) | 1
```

Questo significa che se un nodo si trova ad esempio alla posizione $2$ i suoi figli si troveranno agli indici $4$ e $5$ dell'array.
## Min heap e max heap
Esistono due tipi di heap binario:
- **min heap**, dove per ogni elemento vale la relazione $A[parent(i)] \le A[i]$
- **max heap**, dove per ogni elemento vale la relazione $A[parent(i)] \ge A[i]$
Per questa relazione:
- alla radice del min heap troviamo sempre il valore **minimo**
- alla radice del max heap troviamo sempre il valore **massimo**

Il max heap viene spesso impiegato nell'heapsort e nelle code di max priorità, il min heap invece nelle code di min priorità.
Riferendosi all'heapsort per brevità si parlerà solo di heap, dando per scontato che si tratti di un max heap.
## Heapify
Durante la costruzione di un heap a partire da un array disordinato e durante il processo di heapsort può capitare che i nodi violino la relazione fondamentale dell'heap:
$$ A[parent(i)] \ge A[i] $$
Per questo motivo utilizziamo una procedura ricorsiva, detta **heapify**, per ristabilire l'ordine.
```typescript
const heapify = (arr: number[], heapSize: number, i: number): void => {
	let largest = i;
	const left = 2 * i;
	const right = 2 * i + 1;
	
	if (left <= heapSize && arr[left] > arr[largest]) {
		largest = left;
	}

	if (right <= heapSize && arr[right] > arr[largest]) {
		largest = right;
	}

	if (largest !== i) {
		// scambio massimo con valore corrente 
		[arr[i], arr[largest]] = [arr[largest], arr[i]];
		heapify(arr, heapSize, largest);
	}
}
```

Heapify non fa nient'altro che ricercare il massimo tra un elemento e i suoi figli e scambiarli. Nel caso in cui ci sia stare uno spostamento, si controlla ricorsivamente il valore che si è scambiato con il massimo finché non risulta nella posizione appropriata.
Avendo ad esempio un heap dove il nodo alla radice è fuori posto: $[3,7,12,5,6,10,11]$ heapify scambierà prima il $3$ con il 12 e poi il $3$ con l'$11$.

La complessità di heapify è data dall'equazione di ricorrenza
$$T(n) \le T(\frac{2n}{3}) + \Theta(1)$$
Dove:
- il membro $\frac{2n}{3}$ rappresenta l'ultimo livello dell'albero, pieno per metà nel caso peggiore
- $\Theta(1)$ rappresenta la complessità degli swap

Per il **Teorema master* l'equazione ha soluzione
$$T(n) = O(log \ n) = O(h)$$
Dove $h$ è l'altezza dell'albero.

## Inserimento
Ci sono due modi per costruire un heap:
1. Inserendo la lista di valori uno per volta, nell'ordine in cui vengono dati
2. Da un array già data, utilizzando il metodo $buildHeap$

Nel primo caso l'inserimento avviene sempre alla fine dell'array e il valore viene scambiato con il padre finché non risale in una posizione appropriata che non violi le proprietà dell'heap.

```typescript
const insert = (value: number): void => {
	heap.push(value);
    let currentIndex = heap.length - 1
    while (
      currentIndex > 0 &&
      this.heap[currentIndex] > heap[parent(currentIndex)]
    ) {
      const parentIndex = parent(currentIndex);
      swap(currentIndex, parentIndex);
      currentIndex = parentIndex;
}
```

La porzione di codice che segue il `push` viene spesso chiamata `heapifyUp` e incapsulata in un metodo a sé stante. L'inserimento ha una complessità di $O(log(n))$.
## Build heap
Possiamo utilizzare heapify per costruire un heap a partire da un array non ordinato di elementi. Lo eseguiamo su metà dell'array perché i restanti nodi sono singole foglie, non necessitano di venire ordinate.

```typescript
const buildHeap = (arr: number[]): void => {
  const n = arr.length - 1;
  for (let i = Math.floor(n / 2); i >= 1; i--) {
    heapify(arr, n, i);
  }
}
```

La complessità di buildHeap ad un dato passo varia a seconda del nodo su cui viene chiamata. Mentre i nodi più in alto nell'albero effettueranno più chiamate di heapify, quelli più in basso ne effettueranno meno, questo porta allo sviluppo di una serie numerica che converge i $n$, limitando superiormente buildHeap al tempo lineare.

L'equazione di ricorrenza di buildHeap è:
$$T(n) = \sum_{h=0}^{log \ n} \frac{n}{2^{h+1}}\ \cdot \ h$$
Dove:
- $\sum_{h=0}^{log \ n} \frac{n}{2^{h+1}}$ rappresenta il discendere attraverso l'albero
- $h$ rappresenta il costo di heapify

Raccogliendo per $n$:
$$ T(n) = n \cdot \sum_{h=0}^{log \ n}  \frac{h}{2^{h+1}}$$
Isoliamo la sommatoria e la riconduciamo ad una serie numerica nota:
$$\sum_{h=0}^{\infty} \frac{h}{2^{h+1}} = 1$$
Possiamo quindi scrivere l'equazione di ricorrenza come:
$$
T(n) = n \cdot 1 = O(n)
$$
## Heapsort
Date le procedure heapify e buildHeap, possiamo infine costruire l'algoritmo di ordinamento heapsort

```typescript
const heapSort = (arr: number[]): void => {
  buildMaxHeap(arr);
  let heapSize = arr.length - 1;
  for (let i = heapSize; i >= 2; i--) {
    [arr[1], arr[i]] = [arr[i], arr[1]]; // swap
    heapSize--;
    heapify(arr, heapSize, 1);
  }
}
```

La cui complessità è data dall'equazione a seguire, unione delle complessità precedenti.
$$
T(n) = O(n \ log \ n)
$$

Alcune implementazioni di heapsort compattano le righe interne al `for` in un ulteriore metodo, chiamato `extractMax`.
### Comparazione con altri algoritmi di ordinamento

| Algoritmo  | Caso migliore    | Caso medio       | Caso peggiore    | Note                   |
| ---------- | ---------------- | ---------------- | ---------------- | ---------------------- |
| BubbleSort | $O(n)$           | $O(n²)$          | $O(n²)$          | Stabile                |
| QuickSort  | $O(n \ log \ n)$ | $O(n \ log\ n)$  | $O(n²)$          | Non stabile (standard) |
| MergeSort  | $O(n \ log \ n)$ | $O(n \ log \ n)$ | $O(n \ log \ n)$ | Stabile                |
| HeapSort   | $O(n \ log \ n)$ | $O(n \ log \ n)$ | $O(n \ log \ n)$ | Non stabile, in-place  |

## Code di priorità
La struttura heap e le procedure viste possono essere utilizzate per implementare sistemi di priorità.

Una coda di priorità è una struttura dati su cui possiamo svolgere le operazioni di:
- inserimento
- massimo e minimo (senza rimuoverlo)
- estrazione di massimo e minimo 
- aumento e decremento di priorità

A seconda del tipo di heap utilizzato otteniamo
- una coda di min priorità (min heap)
- una coda di max priorità (max heap)

Le operazioni citate in merito al massimo riguardano la coda di max priorità, quelle inerenti il minimo invece la coda di min priorità.

```typescript
// Ritorna il massimo, senza estrarlo dalla coda
const maximum = (arr: number[]): number => arr[1];
```

```typescript
// Estrae il massimo e ristabilisce le proprità dell'heap
const extractMax = (arr: number[]): number|undefined => {
  const heapSize = arr.length - 1;
  if (heapSize < 1) throw new Error("Heap vuoto");

  const max = arr[1];            // massimo = radice
  arr[1] = arr[heapSize];        // sposta l'ultimo in cima
  arr.pop();                     // rimuove l'ultimo elemento
  heapify(arr, heapSize - 1, 1); // ristabilisce la max-heap
  
  return max;
}
```

```typescript
// Incrementa il valore delle chiavi
// Eventualmente effettua degli swap per mantenere le proprietà dell'heap
const increaseKey = (arr: number[], i: number, newKey: number): void => {
  if (newKey < arr[i]) {
    throw new Error("La nuova chiave è più piccola di quella attuale");
  }

  arr[i] = newKey;

  while (i > 1 && arr[parent(i)] < arr[i]) {
    const parent = parent(i);
    // swap
    [arr[i], arr[parent]] = [arr[parent], arr[i]];
    i = parent;
  }
}
```

```typescript
// Inserimento di una nuova chiave
const insert = (arr: number[], key: number): void => {
  arr.push(-Infinity); // oppure arr[heapSize] = -Infinity
  increaseKey(arr, arr.length - 1, key); // La aumenta al valore corretto
}
```
### Complessità delle operazioni

| Operazione                  | Complessità |
| --------------------------- | ----------- |
| Inserimento                 | $O(log\ n)$ |
| Incremento / decremento     | $O(log\ n)$ |
| Massimo / Minimo            | $O(1)$      |
| Estrazione massimo / minimo | $O(log\ n)$ |
## Guida agli esercizi
**Più comuni**
Gli esercizi d'esame tipo hanno la seguente struttura:
- costruire un heap inserendo $n$ chiavi nell'ordine dato
	- Inserisco alla fine dell'array e scambio con il padre se le proprietà sono violate. Faccio risalire il valore fino alla posizione corretta. 
- costruire un heap partendo da un array non ordinato utilizzando una procedura $buildHeap$
	- Eseguo heapify da $\lfloor \frac{n}{2} \rfloor$ fino ad arrivare all'indice 1

Dopo la costruzione dell'heap può venire chiesto:
- Estrarre massimo/minimo (una o più volte)
	- Scambio con l'ultimo valore, riduco $heapsize$ e chiamo heapify finché il valore che ho messo al posto della radice non scende nella posizione appropriata
- Eseguire un heap sort
	- Equivale a estrarre il minimo / massimo $n$ volte e poi leggere l'array (da fine a inizio o da inizio a fine a seconda del se si vuole l'ordine crescente o decrescente)

**Meno comuni**
Più raramente può venire chiesto pseudo codice e complessità di:
- heapsort
- heapify
- extract max / min
- buildHeap

La maggioranza degli esercizi riguarda il max heap o non è specificato, in alcuni è esplicitamente chiesto il min heap.

**Rari**
Vengono richieste le equazioni di buildHeap o heapify e la loro risoluzione