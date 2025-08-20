I principali metodi per risolvere un problema che può essere scomposto in sotto problemi sono:
1. Divide-et-impera
2. Approccio [[10 Greedy|greedy]]
3. Programmazione dinamica

Per alcuni tipi di problemi, sebbene l'approccio divide-et-impera possa funzionare e portare ad una soluzione corretta, è spesso più dispendioso rispetto agli altri due, nello specifico, quando i sotto-problemi **non sono indipendenti**, ovvero figurano più volte durante la costruzione della soluzione finale. Risolvere un problema di questo tipo con il metodo classico significa risolvere più e più volte lo stesso problema quando questo fa parte di una problema più grande.

Nella programmazione dinamica si risolve invece ciascun problema una sola volta e si salva la soluzione in una tabella (**memoizzazione**). La programmazione dinamica, così come l'approccio greedy, si applicano tipicamente ai problemi di ottimizzazione, per cui spesso ci sono più soluzioni possibili. 

Risolvere un **problema di ottimizzazione** significa ricercare (e trovare) la soluzione con valore ottimo. Determinati problemi infatti hanno spesso un insieme di soluzioni corrette ma solo alcune di essere sono ottime (massimizzare il profitto, ridurre la complessità computazionale etc...), ci inoltre essere più soluzioni ottime (equivalenti).

La programmazione dinamica risolve la gran parte dei problemi di ottimizzazione, mentre l'approccio greedy risolve solo un sotto-insieme dei problemi risolvibili dalla programmazione dinamica, questo perchè l'approccio greedy ha alcuni prerequisiti aggiuntivi.

**Sviluppare un algoritmo di programmazione dinamica**
Per sviluppare un algoritmo di programmazione dinamica, occorre:
1. Caratterizzare la soluzione ottima
2. Definire in modo ricorsivo il valore della soluzione ottima
3. Calcolare il valore della soluzione ottima (di solito in maniera bottom-up)
4. Costruire una soluzione ottima partendo dalle soluzioni per i sotto problemi
## Sottostruttura ottima
Uno dei prerequisiti per applicare le tecniche di programmazione dinamica e l'approccio greedy è che il problema goda della **sottostruttura ottima**, vale a dire che, data una soluzione ottima per il problema, questa rimane ottima anche se riproposta per i sotto-problemi.

Il problema dei cammini minimi da sorgente unica nei grafi gode ad esempio di questa proprietà e dunque una soluzione ottima per il cammino minimo da un nodo ad un altro contiene al suo interno sempre sotto-cammini minimi.

Non tutti i problemi godono della sottostruttura ottima, ad esempio il problema del percorso semplice più lungo in un grafo non gode delle proprietà appena citate.
## Problema del taglio delle aste
Il problema del taglio delle aste (rod-cutting) è un classico esempio di problema di ottimizzazione risolvibile con la programmazione dinamica.

Data un'asta di metallo di una lunghezza $n$, questa può essere tagliata secondo un certo numero di unità intere (es: pollici o centimetri) in $2^{n-1}$ modi.
Ciascun pezzo di una data lunghezza può essere venduto per uno specifico prezzo $p_i, i = \{ 1, ..., n\}$. Tagliare l'asta in maniera tale che il ricavo sia massimo, ovvero trovare la decomposizione ottima.
$$n=i_1, i2, ..., i_k$$
$$r_n = p_{i_1} + p_{i_2}+ ... + p_{i_n}$$
Per ciascun passo si sceglierà quindi:
- Se tagliare o meno l'asta (potrebbe essere più profittevole lasciarla intera)
- Se si è scelto di tagliarla, va scelto come. Dividiamo qui l'asta in due parti che possono essere o meno uguali.

Un esempio di tabella dei prezzi per pezzo:

| lunghezza $i$    | 1   | 2   | 3   | 4   | 5   | 6   | 7   | 8   | 9   | 10  |
| ---------------- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **prezzo $p_i$** | 1   | 5   | 8   | 9   | 10  | 17  | 17  | 20  | 24  | 30  |

**Funzione ricorsiva del problema**
Possiamo identificare il problema con la funzione ricorsiva:
$$r_n =
\begin{cases}
	max(p_n, r_1 + r_{n-1}, r_2 + r_{n-2}, ...r_{n-1}+r_1) \ se \ n > 0 \\ \\
	0 \ altrimenti 
\end{cases}$$
**Pseudo-codice**
Esistono diverse varie implementazioni dell'algoritmo rod-cutting, alcune forniscono solo i tagli senza ricostruire per intero la soluzione finale, altre leggermente più articolate consentono invece di farlo.

**Pseudo-codice - Versione ricorsiva con memoizzazione**
```typescript
RodCutting(n: number, prices: Array<number>)
	memTable = new Table(n, NEG_INFINITE); // riempita con - infinito
	return RodCuttingAux(n, prices, memTable);

RodCuttingAux(n: number, prices: Array<number>, memTable: Table)
	if(memTable[n] >= 0)
		return memTable[n];

	quantity = (n === 0) ? 0 : NEG_INFINITE;

	for(i = 1 to n)
		quantity = max(
			quantity,
			prices[i] + RodCuttingAux(n - i, prices, memTable)
		)

	memTable[n] = quantity;
	return quantity;
```

**Pseudo-codice - Versione iterativa bottom-up con memoizzazione**
Senza ricostruire la soluzione finale:
```typescript
RodCutting(n: number, prices: Array<number>)
	memTable = new Table(n, 0); // dimensione n, riempita di 0
	for (len = 1 to n)
		for(i = 1 to len)
			tempPrice = prices[i] + memTable[len - i];
			if( tempPrice > memTable[len])
				memTable[len] = tempPrice;
	return memTable[n];
```

Ricostruendo la soluzione finale:
```typescript
RodCutting(n: number, prices: Array<number>)
	memTable = new Table(n, 0); // dimensione n, riempita di 0
	cuts = new Table(n,0);      // tabella dei tagli fatti
	for (len = 1 to n)
		for(i = 1 to len)
			tempPrice = prices[i] + memTable[len - i];
			if( tempPrice > memTable[len])
				memTable[len] = tempPrice;
				cuts[len] = i;

	answerset = new Set();
	while(n > 0)
		answerSet.add(cuts[n]);
		n = n - cuts[n];
	return answerSet;
```

**Complessità**
Tutte queste versioni hanno una complessità quadratica ($O(n^2)$) e richiedono uno spazio aggiuntivo $O(n)$ per memorizzare i risultati intermedi. 
## Problema della moltiplicazione di matrici
Data una sequenza di matrici compatibili $<A_1, ... A_n>$ vogliamo calcolare il prodotto $A_1 * ... * A_n$.

Due matrici $A,B$ si dicono compatibili se e solo se:
- $A$ è una matrice di dimensione $p \times q$
- $B$ è una matrice di dimensione $q \times r$
Il prodotto di queste due matrici sarà una terza matrice $C$ di dimensioni $p \times r$. 

L'ordine in cui scegliamo di moltiplicare le matrici ha impatto sulla complessità finale in quanto uno specifico ordine può aumentare o ridurre il numero di prodotti scalari da svolgere. L'ottimizzazione in questo consiste nel minimizzare il numero di prodotti scalari. Scegliamo di stabilire uno specifico ordine tramite l'uso delle parentesi (o **parentesizzazione**).

Un prodotto tra matrici è **completamente parentesizzato** se è una singola matrice oppure un prodotto, racchiuso tra parentesi, di due prodotti di matrici completamente parentesizzati.
Una sequenza di $4$ matrici $A_1, A_2, A_3, A_4$ può essere parentesizzata come:
1. $A_1 ( A_2(A_3A_4))$
2. $A_1((A_2 A_3)A_4)$
3. $(A_1 A_2)(A_3 A_4)$
4. $(A_1 (A_2 A_3))A_4$
5. $((A_1 A_2)A_3)A_4$

**Funzione ricorsiva**
La funzione ricorsiva che descrive **tutte le possibili parentesizzazioni** è:
$$ P(n) =
\begin{cases}
	 \sum^{n-1}_{k = 1} \ P(k)P(n-k) \ se \ n \ge 2 \\ \\
	1 \ se  \ n = 1
\end{cases}$$
Trascurando i dettagli matematici, risulta esponenziale in $n$. $$P(n) = \Omega(2^n)$$Questo esclude qualsiasi approccio di brute force.

Applichiamo la programmazione dinamica al problema della parentesizzazione:
1. Definiamo la struttura di una parentesizzazione ottima
2. Definiamo una soluzione ricorsiva
3. Calcoliamo i costi ottimi

**Provare la sottostruttura ottima**
Avendo una generica sequenza di matrici compatibili $<A_i, ..., A_j>, i \le j$ , se scegliamo di parentesizzare il prodotto ci sarà un qualche valore per cui "spezziamo" la sequenza in due, diciamo un indice $k$. Supponiamo che una parentesizzazione ottima di $A_i, A_{i+1}, ... A_j$ suddivida il prodotto fra $A_k,A_{k+1}$ (ciò che c'è prima e ciò che c'è dopo l'indice scelto). Se la soluzione scelta è ottima allora devono anche esserlo le parentesizzazioni delle due parti $<A_i, ... A_k>$ e $<A_{k+1}, ... A_j>$. Se non lo fossero la soluzione finale sarebbe migliore di quella che già abbiamo, portando ad una contraddizione.

A questo punto dobbiamo solo esaminare tutti i possibili valori di $k$ in cui dividere la sequenza, così da essere sicuri di spezzarlo al punto preciso che ci fornirà la soluzione ottima.

**Definire la soluzione ricorsiva**
Sia $m[i,j]$ il **numero minimo di prodotti** scalari richiesi per calcolare la matrice prodotto $A_{i,...,j}$. Possiamo definirlo tramite la funzione ricorsiva:
$$m[i,j] =
	\begin{cases}
		min_{k=i}^j \{ m[i,k] + m[k + 1 ,j] + p_{i-1} \ p_k \ p_j\} \ se \ i < j \\ \\
		0 \ se \ i = j 
	\end{cases}
$$
**Costruire la soluzione ottima**
```typescript
MatrixChainOrder(p)
	n = p.length - 1;
	m = new Table(1,n,1,n);
	s = new Table(1,n-1, 2, n);
	
	for i = 1 to n
		m[i,i] = 0;

	for l = 2 to n
		for i = 1 to n - l + 1
			j = i + l - 1;
			m[i,j] = INFINITE;
			for k = i to j - 1
				q = m[i,k] + m[k+1, j] + (p[i-1] * p[k] * p[j])
				if q < m[i,j]
					m[i,j] = q;
					s[i,j] = k;
	return (m,s)
```

```typescript
PrintOptimalParens(s,i,j)
	if i === j
		print(A[i]);
	else
		print("(");
		PrintOptimalParens(s, i, s[i,j]);
		PrintOptimalParens(s, s[i,j] + 1, j);
		print(")");
```
## Problema dello zaino 0 - 1
Supponiamo che un ladro entri in un magazzino contenente $n$ oggetti. Ciascun oggetto ha un proprio peso $p_i$ e un determinato valore $v_i$.

Avendo uno zaino di capacità $W$ all'interno del quale stipare la refurtiva, il ladro vuole realizzare il furto di maggior valore, compatibile con la capacità del proprio zaino.

Ciascun oggetto può essere preso per intero o lasciato indietro, non possono essere prese frazioni di un dato oggetto. Quindi:
$$ max( \sum^n _{i=1} x_i * v_i)$$
Dove: $$\sum ^n _{i=1} x_i * v_i \le W, x_i \in \{ 0,1 \} \ \ \ \forall i = 1 , ..., n$$Il problema dello zaino così formulato (detto a volte problema dello zaino intero o $0-1$ per distinguerlo dalla variante frazionaria) è un classico problema di ottimizzazione risolvibile tramite tecnica di programmazione dinamica in tempo polinomiale. Risolto tramite tecniche di brute force, avrebbe un costo di computazione $O(2^n)$, il che rende la sua versione polinomiale un netto miglioramento.

**Sottostruttura ottima per il problema dello zaino 0-1**
Indichiamo il problema originale con $S(n,W)$.
Con $S(i, w)$ ci riferiamo invece al sottoproblema in cui il ladro può scegliere oggetti nell'insieme $\{ a_1, ... , a_i \}, 1 \le i \le n$, per realizzare il furto di maggior valore compatibile con la capacità residua dello zaino $1 \le w \le W$.

Incappiamo in questo sotto-problema ogni qual volta che, scelto un oggetto, dobbiamo fare i conti con lo spazio residuo e gli oggetti che ci sono rimasti da prendere.

Casi banali del problema:
1. $S(0,w)$, l'insieme degli oggetti da prendere è vuoto
2. $S(i,0)$ la capacità residua è $0$ (lo zaino è pieno)

Sia $S(i,w)$ un sotto-problema non banale. Esaminiamo il peso dell'$i$-esimo oggetto e consideriamo il da farsi:
- $w_i > w$: l'oggetto $a_i$ non può essere selezionato perchè il suo peso eccede la capacità rimanente. Risolviamo il sottoproblema $S(i -1, w)$
- $wi \le w$: l'oggetto può essere contenuto perchè rientra nella capacità disponibile. Abbiamo due possibili scelte a questo punto:
	- Prendiamo l'oggetto: scegliamo di prenderlo e risolvere il sotto-problema successivo, con una capacità residua inferiore $S(i -1, w - w_i)$
	- Non prendiamo l'oggetto: ignoriamo l'oggetto e risolviamo il sotto-problema per l'oggetto a seguire $S(i-1, w)$

Tra le opzioni disponibili scegliamo quella con maggior valore. In ogni caso la soluzione ottima per $S(i,w)$ contiene almeno una soluzione ottima per uno dei sotto-problemi $S(i-1,w)$ e $S(i -1 , w - w_i)$.

**Dimostrazione**
Sia quindi $A(i,w)$ una soluzione ottima per il problema $S(i,w), i,j > 0$.
Se $A(i,w) = {a_i} \cup A(i-1, w-w_i)$ (la scelta corrente unita alle soluzioni precedenti avendo preso l'oggetto) allora $A(i-1, w-w_i)$ è una soluzione ottima per il problema precedente $S(i - 1, w - w_i)$.

Assumiamo **per assurdo** che $A(i-1, w-w_i)$ non sia una soluzione ottima. Se è così allora esiste una soluzione alternativa $A'$ per lo stesso sottoproblema che però è ottima. 
Sia quindi la soluzione corrente definita come: $A'(i,w) = {a_i} \cup A'(i - 1, w- w_i)$.
$$ v(A'(i,w)) = v_i + v(A'(i-1, w - w_i)) > v_i + v(A(i-1, w-w_i)) = v(A(i,w)) $$
Dove $v$ indica il valore. Questa contraddizione dimostra che le soluzioni ai sotto-problemi sono anche esse ottime.

**Dimensione dei sotto-problemi e calcolo della soluzione ottima**
Durante la risoluzione ci imbattiamo in $Wn$ sotto-problemi dalla forma $S(i,w), 1 \le i \le n, \ 1 \le w \le W$.

Contiamo i sotto-problemi banali (i casi base in cui ci imbattiamo):
- $W$ sotto-problemi nella forma $S(0,w)$
- $n$ sotto-problemi della forma $S(i,0)$
- Quindi: $Wn + W + n = O(Wn)$ sotto-problemi

Sia $v(i,w)$ il valore della soluzione ottima per il sotto-problema $S(i,w), i = 0, ..., n, w = 0, ..., W$

La **funzione ricorsiva** che descrive $v(i, w)$ nei casi non banali è:
$$v(i,w) =
\begin{cases}
	v(i -1, w) \qquad \qquad \qquad \qquad \qquad \qquad se \ w_i > w \\
	max\{ v(i-1, w), v(i-1, w- w_i)\} \qquad se \ w_i \le w
\end{cases}$$
## Problema della catena di montaggio
## Problema del resto
## Problema della più lunga sottostringa comune