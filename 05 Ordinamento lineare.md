## Alberi di decisione
Un albero di decisione è un albero binario usato per rappresentare una sequenza di confronti che vengono effettuati da un ordinamento per confronti.

Ogni nodo interno rappresenta l'esito di un confronto tra due elementi:
- è etichettato con $i \ : \ j$ dove $1 \le i, j \le n$
- i sotto-alberi definiscono i successivi confronti nel caso in cui $a_i \le a_j$ (il sinistro) o $a_i > a_j$ (il destro)

Ogni foglia corrisponde invece ad una particolare permutazione dell'input $<\pi(1), \pi(2), ... \pi(n)>$. Quando si raggiunge una foglia, l'algoritmo di ordinamento ha stabilito l'ordinamento $a_{\pi(1)} \le a_{\pi(2)} \le \ ...\ \le a_{\pi(n)}$.

Visto che un requisito fondamentale degli algoritmi di ordinamento è la correttezza, un albero di decisione per un algoritmo di ordinamento per confronti deve sempre avere $n!$ foglie, altrimenti significa che l'algoritmo non è in grado di produrre tutte le possibili permutazioni.

Per gli ordinamenti basati su confronti si ha un limite inferiore di $\Omega(n \ log \ n)$ **sotto il quale non è possibile scendere**, per tale ragione algoritmi come mergesort o [[03 Heap, heapsort e code di priorità|heapsort]], la cui complessità è proprio $\Theta( n \ log \ n)$ sono considerati **asintoticamente ottimali**.

**Teorema**: un qualsiasi algoritmo di ordinamento per confronti richiede $\Omega( n \ log \ n)$ nel caso peggiore.

**Dimostrazione**: dimostriamo l'altezza dell'albero di decisione che rappresenta l'ordinamento, dove ogni permutazione appare come una foglia raggiungibile.

Sia $T$ un albero di decisione di altezza $h$ con $n!$ foglie. $n! \le 2^h$, dove $2^h$ è il numero di foglie di un albero binario completo di altezza $h$.

Usiamo l'**approssimazione di Stirling**:
$$
(\frac{n}{e})^n \le n! \le n(\frac{n}{e})^n
$$
E otteniamo quindi:
$$
h \ge log \ n! \ge log (\frac{n}{e})^n = n \ log (\frac{n}{e})= \Theta(n \ log \ n)
$$
Cioè:
$$
h = \Omega(n \ log \ n)
$$

## Ordinamento lineare
Cambiando modello di riferimento e facendo alcune assunzioni sull'input di partenza è possibile "eludere" il limite inferiore dell'ordinamento per confronti (di fatto non siamo più in quello schema).
### Counting-sort
Counting-sort è un algoritmo di ordinamento **non basato sul confronto**. È particolarmente efficiente quando **l'intervallo di valori di input è piccolo rispetto al numero di elementi da ordinare**. L'idea di base di è **contare la frequenza di ogni singolo elemento nell'array di input e utilizzare tale informazione per posizionare gli elementi nelle posizioni ordinate corrette**.

```c
countingSort(input, k)
	count ← array of k + 1 filled with 0
    output ← array of same length as input
    
    for i = 0 to length(input) - 1 do
        j = key(input[i])
        count[j] = count[j] + 1

    for i = 1 to k do
        count[i] = count[i] + count[i - 1]

    for i = length(input) - 1 down to 0 do
        j = key(input[i])
        count[j] = count[j] - 1
        output[count[j]] = input[i]

    return output
```

Alcuni esempi reali di counting-sort, una prima versione più primitiva ed una più compatta, sfruttando il metodo `flatMap`.

```typescript
const countingSort = (arr: number[]): number[] => {
  if (arr.length === 0) return [];

  const max = Math.max(...arr);
  const count = new Array(max + 1).fill(0);

  for (let i = 0; i < arr.length; i++) {
    count[arr[i]]++;
  }

  const sorted: number[] = [];

  for (let i = 0; i < count.length; i++) {
    while (count[i] > 0) {
      sorted.push(i);
      count[i]--;
    }
  }

  return sorted;
};

const nums = [4, 2, 2, 8, 3, 3, 1];
console.log(countingSort(nums)); // Output: [1, 2, 2, 3, 3, 4, 8]
```

```typescript
const countingSort = (arr: number[]): number[] => {
  if (arr.length === 0) return [];

  const max = Math.max(...arr);
  const count = Array.from({ length: max + 1 }, () => 0);

  arr.forEach(num => count[num]++);
  // Trasforma ogni frequenza in una lista di valori ripetuti, e li appiattisce
  return count.flatMap((freq, num) => Array.from({ length: freq }, () => num));
};

// Esempio d'uso
const nums = [4, 2, 2, 8, 3, 3, 1];
console.log(countingSort(nums)); // [1, 2, 2, 3, 3, 4, 8]
```
#### Limiti di counting-sort
Sebbene abbia una complessità di $O(n+m)$, dove $n$ ed $m$ sono le dimensioni degli array di output e di count, counting sort ha delle limitazioni non indifferenti:
- Non funziona con i valori decimali
- È inefficiente se l'intervallo di valori da ordinare è molto ampio
- Non è un algoritmo di ordinamento in place, ma utilizza spazio extra per ordinare gli elementi dell'array
### Bucket-sort
Bucket-sort è una algoritmo di ordinamento che prevede la suddivisione degli elementi in vari gruppi, o bucket. Questi bucket vengono formati distribuendo uniformemente gli elementi. Una volta suddivisi in bucket, gli elementi possono essere ordinati utilizzando qualsiasi altro algoritmo di ordinamento. Infine, gli elementi ordinati vengono riuniti in modo ordinato.

Bucket sort è utilizzato per ordinare valori decimali in un range compreso tra $0$ (incluso) ed $1$ (escluso), oppure per valori interi, compresi in particolari range, ad esempio $[ 0, 10 ($ o $[ 10, 100 ($ 

```c
bucketSort(array, k)
    buckets ← new array of k empty lists
    M ← 1 + the maximum key value in the array
    for i = 0 to length(array) do
        insert array[i] into buckets[floor(k × array[i] / M)]
    for i = 0 to k do
        anySort(buckets[i])
    return the concatenation of buckets[0], ...., buckets[k]
```

```typescript
const bucketSort = (arr: number[], bucketSize: number = 5): number[] => {
  if (arr.length === 0) return [];

  const min = Math.min(...arr);
  const max = Math.max(...arr);
  const bucketCount = Math.floor((max - min) / bucketSize) + 1;

  const buckets: number[][] = Array.from({ length: bucketCount }, () => []);

  arr.forEach(num => {
    const index = Math.floor((num - min) / bucketSize);
    buckets[index].push(num);
  });

  const sortedBuckets = buckets.map(bucket => bucket.sort((a, b) => a - b));
  return ([] as number[]).concat(...sortedBuckets);
};

const nums = [0.78, 0.17, 0.39, 0.26, 0.72, 0.94, 0.21, 0.12, 0.23, 0.68];
console.log(bucketSort(nums));
```

La complessità di bucket-sort è influenzata dall'algoritmo interno al bucket per l'ordinamento, quindi il limite superiore per il caso peggiore è labile a seconda di ciò.
#### Caso peggiore
Il caso peggiore si configura quando tutti gli elementi sono posti nello stesso bucket (iniziano tutti per lo stesso digit). La complessità in questo è $O(n^2)$ se ad esempio si ordina con algoritmo come insertion sort o $O(n \ log \ n)$ se si utilizza ad esempio mergesort o heapsort. 

#### Caso medio e ottimo
Quando l'input è **uniformemente distribuito nei bucket** si viene a configurare il caso medio. La complessità è data dal costo della suddivisione in bucket e dall'ordinamento di ciascuno.

Il limite superiore in questo caso è quindi:
$$O(n + \frac{n^2}{k} + k)$$
Con $k$ numero di bucket ed $n$ dimensione dell'input.

Nel caso in cui $k$ sia dello stesso ordine di $n$, $k \approx n$, si ha **complessità lineare**, ovvero $O(n)$.
### Radix-sort
Radix-sort è un algoritmo di ordinamento lineare che ordina gli elementi elaborandoli cifra per cifra, efficiente per interi o stringhe con chiavi di dimensione fissa. 

Invece di confrontare gli elementi direttamente, li distribuisce in gruppi in base al valore di ciascuna cifra. Ordinando ripetutamente gli elementi in base alle loro cifre significative, dalla meno significativa alla più significativa, ottiene l'ordinamento finale.

Come nel caso del bucket sort, l'ordinamento per digit può essere fatto con un algoritmo a scelta. Solitamente è utilizzato il counting-sort.

```c
radixSort(array, n):
	max = array[0]
	for i = 1 to n - 1 do
		if array[i] > max
			max = a[i]

	for pos = 1 to max/pos > 0 do
		countingSort(array, n, pos)
		pos = pos * 10
```
## Complessità

| Algoritmo     | Caso migliore | Caso medio                 | Caso peggiore | Spazio     | Stabile | Note         |
| ------------- | ------------- | -------------------------- | ------------- | ---------- | ------- | ------------ |
| Counting sort | $O(n+m)$      | $O(n+m)$                   | $O(n+m)$      | $O(n+m)$   | Sì      | (\*\*\*)     |
| Bucket sort   | $O(n)$        | $O(n + \frac{n^2}{k} + k)$ | (\*)          | $O(n+k)$   | (\*)    | (\*\*\*\*)   |
| Radix sort    | $O(d*(n+k))$  | $O(d*(n+k))$               | $O(d*(n+k))$  | $O(n + k)$ | (\*\*)  | (\*\*\*\*\*) |
(\*) Dipende dall'algoritmo interno
(\*\*) Dipende dall'implementazione. Con counting sort sì.

(\*\*\*) $n$ dimensione input, $m$ dimensione array di count
(\*\*\*\*) $n$ dimensione input, $k$ numero di bucket
(\*\*\*\*\*) $d$ massimo numero di digits,  $n$ dimensione input, $k$  intervallo di valori che ciascuna cifra può assumere. Questa complessità si riferisce al radix sort che utilizza counting sort come sub-routine.
## Link utili
- [MIT Lecture 7: Counting Sort, Radix Sort, Lower Bounds for Sorting](https://youtu.be/Nz1KZXbghj8?si=MHFDDG3fFKuqJ5ft)
- [Linear Time Sorting: Counting Sort, Radix Sort, and Bucket Sort](https://youtu.be/pJ1IQD5rv4o?si=K4LoJQ9GkUydvnm3)
- [W3Schools radix sort ](https://www.w3schools.com/dsa/dsa_algo_radixsort.php)