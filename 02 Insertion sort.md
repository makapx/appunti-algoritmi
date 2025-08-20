**Definizione di ordinamento**
Dato un insieme di $n$ numeri $<a_1, a_2, ..., a_n>$ trovare un'opportuna permutazione degli indici in $1,...,n$ tale che $a_{\pi (1)} \le a_{\pi (2)} \le ... \le a_{\pi (n)}$

Insertion sort
```typescript
const insertionSort = (array: Array<number>) => {
	for(let i = 1; i < array.length; i++){
		let current = array[i];
		let j = i - 1;
		while( j >= 0 && array[j] > current){
			array[j+1] = array[j];
			j--;
		}
		array[j+1] = current;
	}
}
```
