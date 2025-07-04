Quicksort è un algoritmo di ordinamento basato sul paradigma **divide et impera**. Nel caso medio ha complessità $\Theta(n \ log \ n)$ e in quello peggiore $\Theta(n^2)$. Nonostante il caso peggiore abbia complessità più alta rispetto ad altri algoritmi, come ad esempio heapsort o mergesort, è spesso preferito perchè è poco probabile avere un input (e dei pivot) che portino al caso pessimo.

Preso un elemento del (sotto-) array, detto pivot, gli elementi alla sua destra e alla sua sinistra vengono ordinati rispetto ad esso. I più grandi dopo, i più piccoli prima. Le parti sinistre e destre vengono poi ricorsivamente partizionate, ovvero divise, e viene ricorsivamente selezionato un pivot, riproponendo lo stesso meccanismo. Arrivati ad un array di un singolo elemento otteniamo il caso base, ovvero una struttura già ordinata.

La scelta del pivot è indipendente dal meccanismo di funzionamento del quicksort, anche se scegliere un buon pivot può migliorare la complessità.

## Quicksort con ultimo elemento come pivot

```typescript
const partition = (arr: number[], low: number, high: number) => {
  const pivot = arr[high];
  // Utilizzo spazio aggiuntivo per copiare parte destra e sinistra
  // Alternativamente posso usare dei puntatori e
  // creare un loop che sposti i puntatori finchè non si incontrano
  const left = arr.slice(low, high).filter((x) => x < pivot);
  const right = arr.slice(low, high).filter((x) => x >= pivot);

  return [...left, pivot, ...right];
};

const quicksort = (arr: number[], low: number = 0, high: number = arr.length - 1): number[] => {
  if (low < high) {
    const partitioned = partition(arr, low, high);
    // Ultimo elemento
    const pivotIndex = partitioned.indexOf(arr[high]);
    return [
      ...quicksort(partitioned.slice(0, pivotIndex)),
      arr[high],
      ...quicksort(partitioned.slice(pivotIndex + 1)),
    ];
  }

  return arr;
};
```

Il metodo `partition`, in particolare la scelta del pivot, influenza la complessità finale dell'algoritmo.

Con un partizionamento non bilanciato, che genera una delle metà del sotto-problema quasi vuota o totalmente vuota e l'altra quasi o pari ad $n$ (scelgo un valore estremo o molto vicino all'estremo) si ha una complessità di $\Theta (n)$ , il che porta l'intero algoritmo alla complessità quadratica. Si ha una complessità analoga quando l'input di partenza è già ordinato o quasi ordinato.

Nel caso migliore, che prevede un partizionamento perfettamente bilanciato a metà, quicksort a un equazione di ricorrenza 
$$T(n) \le 2T(\frac{n}{2}) + \Theta(n)$$

La cui soluzione è 
$$T(n)=O(n \ log \ n)$$
## Quicksort con pivot randomizzato
Un partizionamento bilanciato, oltre che alla metà esatta, si può ottenere tramite la proporzionalità costante dei pivot. Dovendo selezionare un pivot ad ogni livello di ricorsione, scegliendone di volta in volta uno casuale, alcuni livelli avranno un buon partizionamento e altri meno, la soluzione che viene fuori però da un partizionamento misto è molto simile a quella di un partizionamento sempre ottimo, a meno della grandezza di un fattore costante, per questo motivo, scegliendo in maniera randomica il pivot, si ha una complessità migliore.

Un esempio di quicksort con **campionamento casuale**, ovvero selezionare un indice a caso nel sotto-array che si sta esaminando.

```typescript
const partition = (arr: number[], pivotIndex: number) => {
  const pivot = arr[pivotIndex];

  const left = arr.filter((_, i) => i !== pivotIndex && arr[i] < pivot);
  const right = arr.filter((_, i) => i !== pivotIndex && arr[i] >= pivot);

  return { left, pivot, right };
};

const quicksort = (arr: number[]): number[] => {
  if (arr.length <= 1) {
    return arr;
  }

  const pivotIndex = Math.floor(Math.random() * arr.length);
  const { left, pivot, right } = partition(arr, pivotIndex);
  return [...quicksort(left), pivot, ...quicksort(right)];
};

// Test con un input di 100 elementi
const arr = Array.from({ length: 100 }, () => Math.floor(Math.random() * 1000));
console.log("Unsorted", arr);
console.log("Sorted:", quicksort(arr));
```

## Complessità

| Caso         | Complessità Tempo | Complessità Spazio                    |
| ------------ | ----------------- | ------------------------------------- |
| **Migliore** | `O(n log n)`      | `O(log n)` (ricorsione)               |
| **Medio**    | `O(n log n)`      | `O(log n)`                            |
| **Peggiore** | `O(n²)`           | `O(log n)` o `O(n)` se non bilanciato |

| Algoritmo          | Complessità Migliore | Complessità Media | Complessità Peggiore | Stabilità |
| ------------------ | -------------------- | ----------------- | -------------------- | --------- |
| **Quicksort**      | `O(n log n)`         | `O(n log n)`      | `O(n²)`              | No        |
| **Merge Sort**     | `O(n log n)`         | `O(n log n)`      | `O(n log n)`         | Sì        |
| **Heap Sort**      | `O(n log n)`         | `O(n log n)`      | `O(n log n)`         | No        |
| **Bubble Sort**    | `O(n)`               | `O(n²)`           | `O(n²)`              | Sì        |
| **Insertion Sort** | `O(n)`               | `O(n²)`           | `O(n²)`              | Sì        |
| **Selection Sort** | `O(n²)`              | `O(n²)`           | `O(n²)`              | No        |

## Link utili
- [Quicksort in Java - Coding with John](https://youtu.be/h8eyY7dIiN4?si=rVS3szccKXb1x7AD)