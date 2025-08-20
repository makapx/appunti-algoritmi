## Matrici e liste di adiacenza
Ci sono due modi standard per rappresentare un grago $G = (V,E)$:
- come una collezione di liste di adiacenza
- come un matrice di adiacenza
Entrambi i modi vanno bene sia per i grafi orientati che per quelli non orientati. Le liste di adiacenza vengono preferite nel caso di **grafi sparsi** (dove il numero di archi $E$ è molto minore rispetto al numero di vertici $V$), mentre le matrici di adiacenza vengono preferite quando il grafo è particolarmente denso (il numero di archi è prossimo a $V^2$) 

## Visita in profondità
La visita in profondità o (DFS) è un tipo di visita del grafo che prevede l'esplorazione ricorsiva dei nodi legati agli archi uscenti, uno per volta, anziché una visita contemporanea con un andamento in ampiezza. Viene anche utilizzata nell'ordinamento topologico e nell'algoritmo di cammini minimi su DAG.

```typescript
Dfs(G: Graph)
	foreach u in G.V
		u.color = WHITE;
		u.predecessor = NULL;
	time = 0;
	foreach u in G.V
		if u.color === WHITE
			DfsVisit(G,u)
```

```typescript
DfsVisit(G: Graph, u: Node)
	time = time + 1;
	u.d = time;
	u.color = GRAY;
	foreach v in G.adj[u]
		if(v.color === WHITE)
			v.predecessor = u;
			DfsVisit(G,v);
	u.color = BLACK;
	time = time + 1;
	u.f = time;
```

## Cammini di peso minimo

Sia $G = (V,E)$ un grafo orientato e pesato, con una funzione peso $w: E \rightarrow \mathbb{R}$, definiamo:
- Il peso di un cammino $p = <v_0, v_1, ... , v_n>$ come la somma dei pesi degli archi che lo compongono $$ w(p) = \sum^n _{i=1} w(v_{i-1}, v_i)$$
- Il peso di un cammino minimo da $u$ a $v$, se suddetto cammino esiste: $$ \delta(u,v) min\{ w(p) : u \xrightarrow p v\}$$
- Altrimenti se non esiste lo poniamo ad $\infty$

Esistono differenti tipi di cammini minimi.
- Cammino minimo da sorgente unica
- Cammino minimo per una coppia di nodi
- Cammino minimo per ogni coppia di nodi
## Sotto-struttura ottima del problema
**Definizione albero di copertura (spanning tree)**
Dato un grafo $G = (V,E)$ non orientato e connesso, un **albero di copertura** di $G$ è un sotto-grafo $T = (V,E_T)$ tale che:
- $T$ è un albero
- $E_T \subseteq E$
- $T$ contiene tutti i vertici di $G$

L'**albero dei cammini minimi** è un albero di copertura radicato in un nodo $s$ avente un cammino da $s$ a tutti i nodi raggiungibili a partire da $s$.

![[Screenshot_2025-08-04-10-56-14-521_com.fluidtouch.noteshelf3.png]]

**Definizione di soluzione ammissibile**
Una soluzione **ammissibile** può essere descritta da un **albero di copertura** $T$ radicato in $s$ e da un **vettore di distanza** $d$, i cui valori $d[u]$ rappresentano il costo del cammino da $s$ ad $u$ in $T$

**Teorema di Bellman per la sottostruttura ottima**
Una soluzione ammissibile $T$ è **ottima** se e solo se:
- $d[v] = d[u] + w(u,v) , \ \forall (u,v) \in T$
- $d[v] \le d[u] + w(u,v) , \ \forall (u,v) \in E$
**Dimostrazione**
**Prima implicazione**: sia quindi $T$ una soluzione ottima e sia $(u,v) \in E$
- Se $(u,v) \in T$ allora $d[v] = d[u] + w(u,v)$
- Se $(u,v) \notin T$ allora $d[v] \le d[u] + w(u,v)$ perchè altrimenti esisterebbe nel grafo $G$ un cammino da $s$ a $v$ più corto di quello in $T$, il che è un assurdo
**Seconda implicazione**:
Supponiamo per assurdo che $T$ non sia ottimo. Allora esiste un cammino $C$ da $s$ ad un nodo $u$ in $T$ non ottimo. Allora esiste un albero ottimo $T'$, in cui il cammino $C'$ da $s$ a $u$ ha una distanza $d'[u] < d[u]$.

Sia il vettore $d'$ delle distanze associato a $T'$.
Poiché $d'[s] = d[s] = 0$ ma $d'[u] < d[u]$, esiste un arco $(h,k)$ in $C'$ tale che:
- $d'[h] = d[h]$
- $d'[k] < d[k]$

Per costruzione: $d'[h] = d[h]$ e $d'[k] = d'[h] + w(h,k)$
Per ipotesi: $d[k] \le d[h] + w(h,k)$

Combinando le relazioni otteniamo: $d'[k] = d'[h] + w(h,k) = d[h] + w(h,k) \ge d[k]$
Quindi $d'[k] \ge d[k]$ che contraddice $d'[k] < d[k]$
## Cammini minimi da sorgente unica
Dato un nodo sorgente, è desiderabile scoprire i cammini minimi per arrivare da suddetto nodo a tutti gli altri. Questo problema è detto problema dei **cammini minimi da sorgente unica**.

Alcuni metodi di utility per inizializzare e rilassare il grafo che verranno utilizzati dagli algoritmi di cammino minimo a seguire:
```typescript
InitializeSingleSource(G: Graph, s: node)
	foreach v in G.V {
		v.d = INFINITY;
		v.predecessor = NULL;
	}
	s.d = 0;
```

```typescript
Relax(u: Node, v: Node, w: WeightFunction)
	if v.d > u.d + w(u,v) {
		v.d = u.d + w(u,v)
		v.predecessor = u
	}
```
### Algoritmo Dijkstra
L'algoritmo di Dijkstra permette di trovare i cammini minimi per una singola sorgente in **grafi con pesi positivi**. Si basa sul concetto di coda di priorità.

```typescript
Dijkstra(G: Graph, s: Node)
	InitialiseSingleSource(G, s);
	S = new empty set;
	Q = G.V
	while !Q.isEmpty() {
		u = Q.extractMin();
		S.addIfNotExist(u);
		foreach v in G.adj[u]{
			Relax(u,v,w);
		}
	}
```

L'algoritmo di Dijkstra nella sua implementazione originale ha un costo computazionale di $O(V^2)$ dato di due cicli interni. Alcune varianti, che prevedono algoritmi differenti per l'implementazione della coda, hanno complessità differenti.

Se la coda di priorità interna è implementata tramite **heap binario**, scelta **adatta per i grafi abbastanza sparsi**, la complessità scende a $O(|V| + |E| log |V|)$, che se tutti i vertici sono raggiungibili dalla sorgente diviene $O(|E| log |V|)$.

Utilizzando un **heap di Fibonacci** la complessità scende invece a $O |V| log |V| + |E|)$.
### Algoritmo di Bellman-Ford
L'algoritmo di Bellman-Ford è computazionale più pesante di Dijkstra ma nativamente adatto anche ai **pesi negativi**.

Dato un grado orientato pesato $G = (V,E)$, un nodo sorgente $s$ e una funzione peso $w: E \rightarrow \mathbb{R}$, l'algoritmo di Bellman-Ford restistuisce un valore booleano che indica se esiste oppure no un ciclo di peso negativo che è raggiungibile dalla sorgente. Se un tale ciclo esiste, l'algoritmo indica che il problema non ha soluzione. Se non esiste invece l'algoritmo fornisce i cammini minimi con i rispettivi pesi.
```typescript
BellmanFord(G: Graph, s: Node)
	initializeSingleSource(G,s);
	for i = 1 to |G.V| - 1 {
		foreach (u,v) in G.E {
			Relax(u,v,w);
		}
	}
	foreach (u,v) in G.E {
		if v.d > u.d + w(u,v) {
			return false
		}
	}
	return true;
```

L'algoritmo di Bellman-Ford ha complessità $O(VE)$
### Cammini minimi da sorgente unica nei grafi orientati aciclici
Rilassando gli archi in un DAG (grafo orientato aciclico) pesato $G = (V,E)$, secondo un ordine topologico dei suoi vertici, è possibile calcolare i cammini minimi da una sorgente unica in tempo $\Theta(V + E)$. I cammini minimi sono sempre ben definiti in un DAG, perchè anche se ci sono archi di peso negativo, non possono esistere cicli di peso negativo.

```typescript
​DagShortestPaths(G: Graph, w: WeightFunction, s: Node)
	topologicalSortedGraph = topologicalSort(G);
	initializeSingleSource(G,s);
	foreach u in topologicalSortedGraph
		foreach v in G.adj[u]
			Relax(u,v, w)
```

## Cammini minimi tra tutte le coppie
### Algoritmo di Floyd-Warshall
L'algoritmo di Floyd-Warshall consente di calcolare i cammini minimi tra tutte le coppie in tempo $\Theta(V^3)$. Possono essere presenti in questo caso anche archi di peso negativo, ma non cicli di peso negativo.

L'idea centrale dell'algoritmo è: invece di calcolare il percorso più breve tra due nodi, consideriamo la possibilità di migliorare un percorso già esistente ma passando da un vertice intermedio.

Per questo algoritmo utilizziamo la rappresentazione tramite **matrice di adiacenza**, che è proprio il parametro passato inizialmente.

Passaggi principali:
1. Creiamo una matrice $D$, di dimensioni $V \times V$ ($V$ numero di vertici del grafo)
2. Per ogni coppia di vertici $(i,j)$ inizializziamo $D[i][j]$:
	- Con il peso dell'arco che collega direttamente $i$ e $j$ 
	- Se non esiste un arco diretto, ad $\infty$
	- Se $i=j$ (la distanza di un vertice da se stesso) allora poniamo a $0$
3. Per ogni terna $(i,j,k)$ l'algoritmo confronta la distanza attuale $D[i][j]$ con quella che passa per $k$, cioè $D[i][k] + D[k][j]$
4. Se il percorso che passa per $k$ è più breve allora il valore viene aggiornato: $D[i][j] = min(D[i][j], D[i][k]+D[k][j])$
5. Alla fine dell'algoritmo $D$ conterrà le lunghezze dei cammini minimi tra tutte le coppie di vertici del grafo

```typescript
FloydWarshall(W)
	n = W.V.length; // Numero di vertici
	D = new Matrix(n,n);

	for i = 1 to n
		for j = 1 to n
			// 0 se i = j, inf se non esiste un cammino
			// peso del cammino altrimenti
			D[i][j] = W[i][j]

	for k = 1 to n
		for i = 1 to n
			for j = 1 to n
				if(D[i][j] < D[i][k] + D[k][j])
					D[i][j] = D[i][k] + D[k][j]
	return D;
```

Esempio di una matrice di pesi:
- Sulla diagonale troviamo $0$, la distanza di un vertice da sé stesso è nulla
- Dove troviamo $\infty$ non abbiamo cammini possibili
- I restanti valori sono i pesi dei cammini minimi (NB: non sono indicativi di tutti i cammini esistenti ovviamente)

|     | 1        | 2        | 3        | 4        |
| --- | -------- | -------- | -------- | -------- |
| 1   | 0        | 3        | $\infty$ | 7        |
| 2   | 8        | 0        | 2        | $\infty$ |
| 3   | $\infty$ | $\infty$ | 0        | 1        |
| 4   | $\infty$ | $\infty$ | 5        | 0        |
L'algoritmo di Floyd-Warshall si basa sulla caratteristica della **sottostruttura ottima**: un percorso ottima è composto da sotto-percorsi a loro volta ottima.

Per ogni passo $k$ ci chiediamo qual è il percorso più breve per arrivare da $i$ a $j$ potendo passare solo attraverso i nodi appartenenti all'insieme $\{ 1, 2 , ... , k \}$. Nello specifico tendiamo a togliere di volta in volta un nodo per verificare le il percorso ottimo può anche non passare per $k$ e quando questo avviene aggiorniamo la matrice dei pesi. 
#### Complessità di Floyd-Warshall
Visto che l'algoritmo è dominato da tre cicli for annidati ha complessità temporale $\Theta(V^3)$.

In termini di complessità spaziale invece dobbiamo tenere conto del fatto che stiamo memorizzando la matrice delle distanze, che è quadrata rispetto al numero di vertici, ovvero $O(V^2)$.
### Algoritmo Faster All-Pairs Shortest Paths (Faster APSP)
 L'algoritmo Faster APSP tratta il problema dei cammini minimi tra tutte le coppie come un prodotto tra matrici, nello specifico ridefinisce operazioni come somme, prodotti ed elevamenti a potenza e le sostituisce invece con le operazioni che abbiamo già visto.
#### Moltiplicazione di matrici Min-Plus
Supponiamo che $A$ e $B$ siano due matrici di pesi e il loro prodotto sia la matrice $C$. Invece di calcolare la matrice prodotto come si fa nella consueta algebra la definiamo come:
$$C[i][j] = min_{k=1} ^ V (A[i][k] + B[k][j])$$
Ottenendo un comportamento simile alla parte più interna di Floyd-Warshall.
#### Elevamento a potenza ripetuto
Al fine di ottenere tutti i percorsi possibili attraverso il metodo di moltiplicazione dovremmo fare un prodotto ripetuto. Eleviamo ripetutamente al quadrato finché l'esponente non supera $V-1$. Il numero di moltiplicazioni necessarie sarà $O(log \ V)$.

La complessità finale di Faster All-Pairs Shortest Paths é $O(V^3 \ log \ V)$.
#### Pseudocodice 
Scriviamo il metodo come segue, isolando la routine che esegue il nostro prodotto tra matrici e chiamandola `ExtendShortestPath`.

```typescript
	ExtendShortestPath(L,W) // L pesi attuali, W, pesi originali
		n = L.rows;
		L1 = new Matrix(n,n)
		for i = 1 to n
			for j = 1 to n
				L1[i][j] = INFINITE;
				for k = 1 to n
					L1[i][j] = min(L1[i][j], L[i][k] + W[k][j])
	return L1;
```

```typescript
FasterAllPairsShortestPaths(W)
	n = W.rows;
	L = W;
	m = 1;

	while (m < n - 1)
		L = ExtendShortestPath(L, L);
		m = 2*m;
	return L;
```

Sebbene Faster APSP abbia una complessità maggiore di Floyd-Warshall può essere utilizzato con altri algoritmi interni per il calcolo dei prodotti che ottimizzino la complessità complessiva:
- Algoritmo di Strassen -> Complessità $O(V^{2.807})$
- Algoritmo di Coppersmith-Winograd -> Complessità $O(V^{2.373})$
- E altri ancora
### Stampare i cammini minimi tra tutte le coppie
Una routine di utility che può essere utilizzata sulla matrice dei cammini minimi è la seguente, per stampare i cammini minimi tra nodi:
```typescript
PrintAllPairsShortestPath(Pi: Matrix, i: Node, j: Node)
	if (i === j)
		print(i);
	else if (Pi[i][j] === NULL);
		print("Non esiste un cammino da i a j");
	else PrintAllPairsShortestPath(Pi,i, Pi[i][j])
		print(j);
```