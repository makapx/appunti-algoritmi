Le statistiche d'ordine sono strumenti utilizzati in statistica per descrivere e analizzare la posizione e la distribuzione dei dati osservati in un campione ordinato.

In un insieme ordinato quindi abbiamo:
- Il **minimo** ($1^a$ statistica d'ordine) è il valore più piccolo
- Il **massimo** ($n$-esima statistica d'ordine) è il valore più grande
- La **mediana inferiore** ($\lfloor \frac{n+1}{2} \rfloor$ esima statistica d'ordine) è il valore medio inferiore 
- La **mediana superiore** ($\lceil\frac{n+1}{2} \rceil$ esima statistica d'ordine) è il valore medio superiore 
- Gli altri valori ordinati ($2^a, 3^a, …,( n-1)^a$), detti anche $i$-esima statistica d'ordine, sono l'$i$-esimo elemento più piccolo

Le due mediane coincidono in caso di un numero pari di elementi.

## Algoritmi per la ricerca della i-esima statistica d'ordine
Supponiamo che gli elementi siano memorizzati in un array **non ordinato**. Per trovare l'$i$-esima statistica d'ordine possiamo:

1. Ordinare l'array e puntare all'indice (complessità minima $O(n \ log \ n$)
2. Estrarre l'$i$ esima statistica d'ordine in tempo atteso lineare con un algoritmo apposito, basato su [quicksort](04%20Quicksort.md)

Si configurano dei casi particolari, risolvibili in tempo lineare, per quanto riguarda il minimo e il massimo

### Minimo e massimo
È possibile ricercare il minimo effettuando $n - 1$ confronti, assegnando il valore min al primo elemento e aggiornandolo di volta in volta quando si incontra un elemento più piccolo.

```typescript
const findMin = (arr: number[]): number => {
  if (arr.length === 0) throw new Error("Array vuoto");
  let min = arr[0];
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] < min) {
      min = arr[i]; 
    }
  }
  return min;
};
```

Un algoritmo analogo può essere scritto per la ricerca del massimo.

Con qualche accorgimento, una volta trovato il minimo, è possibile ricercare sugli elementi rimanenti il massimo, con un totale di $2n - 3$ confronti

```typescript
const findMinMax = (arr: number[]): { min: number, max: number } => {
  if (arr.length === 0) throw new Error("Array vuoto");

  // Trova il minimo in n-1 confronti
  let min = arr[0];
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] < min) {
      min = arr[i];
    }
  }

  // Trova il massimo in n-2 confronti (escludendo il minimo)
  // Se il minimo è il primo, inizia da arr[1]
  let max = arr[0] === min ? arr[1] : arr[0];
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] !== min && arr[i] > max) {
      max = arr[i];
    }
  }

  return { min, max };
};
```

Il primo `for` fa $n-1$ confronti, il secondo $n-2$, quindi si hanno un totale di $2n - 3$ confronti.

È possibile ottenere una complessità addirittura migliore utilizzando un algoritmo di selezione a turni, detto **Tournament Method**
#### Tournament Method
Il tournament method prevede di accoppiare i valori e stabilito quale sia il maggiore e quale il minore, porli in un insieme di vincitori (i maggiori) o di perdenti (i minori). Se il numero di elementi è dispari l'elemento che rimane disaccoppiato viene aggiunto ad entrambi.

La ricerca del minimo avviene sull'insieme dei perdenti e viene effettuata su $\frac{n}{2}$, analogamente avviene per la ricerca del massimo sull'insieme dei vincitori. 

```typescript
const findMinMax = (arr: number[]): { min: number, max: number } => {
  if (arr.length === 0) throw new Error("Array vuoto");
  
  let min: number, max: number;
  let i = 0;

  // Se l'array ha un numero dispari di elementi, prendi il primo elemento separato
  if (arr.length % 2 !== 0) {
    min = arr[0];
    max = arr[0];
    i = 1; // Inizia dalla seconda coppia
  } else {
    // Se l'array ha un numero pari, inizializza la prima coppia
    if (arr[0] < arr[1]) {
      min = arr[0];
      max = arr[1];
    } else {
      min = arr[1];
      max = arr[0];
    }
    i = 2; // Inizia dalla terza coppia
  }

  // Confronta gli altri elementi due a due
  for (; i < arr.length - 1; i += 2) {
    let localMin: number, localMax: number;

    if (arr[i] < arr[i + 1]) {
      localMin = arr[i];
      localMax = arr[i + 1];
    } else {
      localMin = arr[i + 1];
      localMax = arr[i];
    }

    // Aggiorna il minimo e massimo globali
    if (localMin < min) min = localMin;
    if (localMax > max) max = localMax;
  }

  // Se l'array ha un numero dispari di elementi, confronta l'ultimo elemento
  if (arr.length % 2 !== 0) {
    const lastElement = arr[arr.length - 1];
    if (lastElement < min) min = lastElement;
    if (lastElement > max) max = lastElement;
  }

  return { min, max };
};
```

Questo algoritmo effettua un totale di $\lceil \frac{3n}{2} \rceil -2$ confronti

### Quickselect (o randomized-select)
L'algoritmo quickselect (chiamato anche randomized select), basato su [quicksort](04%20Quicksort.md), permette di trovare l'$i$-esima statistica d'ordine **in tempo lineare nel caso medio**.
Il caso pessimo dell'algoritmo coincide con il caso pessimo del [quicksort](04%20Quicksort.md), ovvero $O(n^2)$, esiste però una variante, detta **Median of Medians (mediana di mediane)** che ha anche nel caso peggiore una complessità lineare.

Scelto un pivot, l'array viene partizionato in:
- valori inferiori al pivot (insieme sinistro o $L$)
- valori superiori (insieme destro o $G$)
- valori uguali (insieme di valori uguali o $E$, omesso se gli elementi sono distinti)

La statistica d'ordine $k$ che si sta cercando viene poi confrontata con le dimensioni dei sotto-insiemi:
- se $k \le | L |$ viene ricercato il valore su $L$
- se $k \le |L| + |E|$ il pivot è l' $i$-esima statistica d'ordine
- altrimenti cerca in $G$ usando come riferimento un nuovo valore $k' = k - |L| - |E|$

```typescript
const quickselect = (arr: number[], k: number): number => {
  if (k < 1 || k > arr.length) {
    throw new Error("k deve essere compreso tra 1 e la lunghezza dell'array");
  }
  
  return select([...arr], 0, arr.length - 1, k - 1);
};

const partition = (array: number[], left: number, right: number, pivotIndex: number): number => {
  const pivotValue = array[pivotIndex];
  // swap
  [array[pivotIndex], array[right]] = [array[right], array[pivotIndex]];
  let storeIndex = left;

  for (let i = left; i < right; i++) {
    if (array[i] < pivotValue) {
      [array[storeIndex], array[i]] = [array[i], array[storeIndex]];
      storeIndex++;
    }
  }

  [array[right], array[storeIndex]] = [array[storeIndex], array[right]];
  return storeIndex;
};

const select = (array: number[], left: number, right: number, kSmallest: number): number => {
  if (left === right) return array[left]; // Base case

  const pivotIndex = left + Math.floor(Math.random() * (right - left + 1));
  const pivotNewIndex = partition(array, left, right, pivotIndex);

  if (kSmallest === pivotNewIndex) {
    return array[kSmallest];
  } else if (kSmallest < pivotNewIndex) {
    return select(array, left, pivotNewIndex - 1, kSmallest);
  } else {
    return select(array, pivotNewIndex + 1, right, kSmallest);
  }
};
```

L'equazione di ricorrenza di quickselect nel **caso medio** è:
$$T(n) = T(\frac{n}{2}) + cn \Rightarrow T(n) = O(n)$$Mentre nel **caso peggiore** è:
$$T(n) = T(n-1) + \Theta(n) \Rightarrow T(n) =\Theta(n^2)$$

Visto che parliamo di un algoritmo basato su [quicksort](04%20Quicksort.md), il caso peggiore lo otteniamo quando il partizionamento è sbilanciato
#### Median of medians
La variante di quickselect con mediana di mediane prevede di dividere l'array in gruppi di 5 elementi e calcolare la mediana di ognuno.
Quickselect viene poi applicato ricorsivamente per trovare la mediana delle mediane, valore che verrà utilizzato come pivot robusto.

```typescript
const medianOfMedians = (arr: number[]): number => {
  if (arr.length === 0) throw new Error("Array vuoto");
  const groupSize = 5;
  // Trova la mediana in un piccolo array
  const getMedian = (group: number[]): number => {
    group.sort((a, b) => a - b);
    return group[Math.floor(group.length / 2)];
  };

  // Caso base: array con 5 o meno elementi → ritorna la mediana diretta
  if (arr.length <= groupSize) {
    return getMedian(arr);
  }

  // Dividi l'array in gruppi di 5
  const groups: number[][] = [];
  for (let i = 0; i < arr.length; i += groupSize) {
    groups.push(arr.slice(i, i + groupSize));
  }

  // Calcola la mediana di ogni gruppo
  const medians: number[] = groups.map(getMedian);

  // Ricorsivamente trova la mediana delle mediane
  return medianOfMedians(medians);
};
```

```typescript
const quickSelect = (arr: number[], k: number): number => {
  if (arr.length === 0 || k < 0 || k >= arr.length) {
    throw new Error("Indice non valido");
  }
  return select(arr, k);
};

 const select = (array: number[], k: number): number => {
    if (array.length === 1) return array[0];
    const pivot = medianOfMedians(array);
    
    const lows = array.filter(el => el < pivot);
    const highs = array.filter(el => el > pivot);
    const pivots = array.filter(el => el === pivot);
    
    if (k < lows.length) {
      return select(lows, k);
    } else if (k < lows.length + pivots.length) {
      return pivot;
    } else {
      return select(highs, k - lows.length - pivots.length);
    }
  };
```

L'equazione di ricorrenza per quickselect con mediana di mediane è:
$$T(n) \le T(\lceil \frac{n}{5}\rceil + T(\frac{7n}{10}) + cn$$
Dove:
- $T(\lceil \frac{n}{5} \rceil)$ è il tempo impiegato nel calcolo della mediana di mediane
- - $T(\lceil \frac{7n}{10} \rceil)$ è il numero di elementi rimanenti nel caso del partizionamento peggiore
- $cn$ è il lavoro lineare per dividere il gruppi, calcolare mediane, partizionare etc...

La complessità asintotica, anche nel caso peggiore è
 $$T(n)=O(n)$$