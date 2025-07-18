Una tabella è una sequenza di elementi $E_i$, ciascuno dei quali costituito da:
- una chiave $K_i$ che identifica univocamente l'elemento. Può essere una stringa o un numero
- l'informazione $I_i$ associata alla chiave

Lo scopo della tabella è memorizzare e rendere reperibili le informazioni. Una entry della tabella è quindi una coppia $(K,I)$.

Le operazioni possibili su una tabella sono:
- Ricerca di un elemento a partire dalla chiave
- Eliminazione
- Inserimento

Una delle operazioni tipiche da eseguire su una tabella è la ricerca di un elemento la sua chiave. Per ogni elemento $E_i$ possiamo definire la **lunghezza di ricerca $S_i$**, cioè il numero di elementi da analizzare prima che si trovi $E_i$.

La lunghezza media di ricerca $S$ è la media di tutti gli $S_i$, cioè di tutte le lunghezze di ricerca.

Esistono due modi per implementare una tabella:

- Per indirizzamento diretto
- Usando le funzioni hash

## Tabelle ad indirizzamento diretto
L'indirizzamento diretto è una tecnica semplice ma limitata, adatta quando l'universo di chiavi (le possibili chiavi utilizzabili) $U =\{ 0,...m-1 \}$ è **ragionevolmente piccolo**.

La tabella utilizzata è della stessa dimensione della cardinalità dell'universo delle chiavi:
$$
T[0, \ ... , \ m-1], \ m = |U|
$$
Quindi data una determinata chiave $k$, l'elemento si trova nella cella $T[k]$. In questo caso abbiamo una **corrispondenza biunivoca** tra la chiave $k$ e la cella $k$.

![[Screenshot_2025-07-04-11-16-06-730_md.obsidian.png]]

Le operazioni di ricerca, inserimento e cancellazione in questo tipo di tabella hanno tutte complessità $\Theta(1)$ ed equivalgono alle classiche operazioni su un array, sfruttando gli indici.

```typescript
const directAddressInsert = (T,k) => {
	return T[k];
};

const directAddressDelete = (T, x) => {
	T[x.key] = NULL;
};

const directAdressInsert = (T,x) => {
	T[x.key] = x.value
};
```

### Limiti delle tabelle ad indirizzamento diretto

1. La dimensione della tabella è pari al numero di chiavi possibili, quindi per valori possibili molto grandi si hanno tabelle molto grandi, in alcuni contesti potrebbe essere impossibile allocare così tanto spazio
2. L'insieme di chiavi effettivamente utilizzare potrebbe essere molto più piccolo di quelle possibili, lasciando la maggior parte dello spazio inutilizzato
### Fattore di carico
La proporzione con cui una tabella è riempita è detta **fattore di carico $\alpha$** . Per una tabella ad indirizzamento aperto il fattore di carico è dato da:
$$
\alpha = \frac{n}{m}
$$
Dove:
- $n$ è il numero di chiavi effettivamente utilizzate
- $m$ è la dimensione della tabella

In questo caso il fattore di carico non supera mai $1$, ovvero non posso memorizzare più elementi di quante posizioni esistano. Se la tabella è molto grande e le chiavi effettive molto meno il fattore di carico sarà un numero piccolo, indicativo di spreco di spazio.
## Tabelle hash

Una buona alternativa che richiede meno spazio sono le tabelle hash.
La dimensione della tabella hash si basa sulla stima del numero di **elementi attesi** (di solito molti meno della dimensione dell'universo delle chiavi possibili) ed utilizza una funzione hash per indicizzare la tabella.

Una funzione hash è una funzione che data una chiave $k \in U$ restituisce la posizione della tabella in cui l'elemento con chiave $k$ si trova.

$$ h : U \rightarrow [0,1,...,m-1] $$
Data una funzione hash, l'elemento con chiave $k$ viene quindi memorizzato nella cella $h(k)$ e non più sull'indice $k$ come avveniva per l' indirizzamento diretto.

Gli aspetti negativi di questo metodo sono principalmente:
- si perde la corrispondenza tra chiavi e posizione
- se la tabella è troppo piena o se la funzione hash assegna a due chiavi distinte lo stesso valore, possono verificarsi delle **collisioni**

Definizione: due chiavi $k_1$, $k_2$ **collidono** quando corrispondono alla stessa posizione della tabella, ovvero quando $h(k_1)=h(k_2)$

Definizione: una funzione hash **perfetta** è una funzione iniettiva, cioè:
$$\forall k_1,k_2 \in U, k_1 \ne k_2 \Rightarrow h(k_1) \ne h(k_2)$$
L'iniettività è possibile solo se $|U| \le m$, in caso contrario le collisioni sono inevitabili. Per minimizzare opportunamente il numero di collisioni è opportuno scegliere una buona funzione hash.

Esistono poi due principali metodi per trattare le collisioni quando avvengono:
- con **liste di collisione**
- tramite **indirizzamento aperto**

### Hash uniforme semplice
Una buona funzione hash ha due principali requisiti:
1. il calcolo ha costo costante
2. soddisfa l'ipotesi di **hash uniforme semplice**

**Definizione di uniformità semplice**
Ogni chiave deve avere la stessa probabilità di essere associata a uno qualsiasi degli $m$ slot possibili, indipendentemente dallo slot a cui sarà associata una qualsiasi altra chiave.

Sia $P(k)$ la probabilità che sia estratta una chiave $k$ tale che $h(k) = j$, allora:
$$\sum_{k:h(k) = j} = \frac{1}{m} \ per \ j=0,...,m-1$$
Il requisito di uniformità semplice è spesso difficile da verificare e rispettare quindi di solito si scelgono delle euristiche che diano buoni risultati
### Metodo della divisione
Il metodo della divisione associa alla chiave $k$ il valore:
$$h(k) = k \ mod \ m$$
Il calcolo è relativamente semplice e contenuto ma bisogna scegliere $m$ (la dimensione della tabella) in modo tale che non sia una potenza di $2$.

Se $m=2^p$ la funzione hash otterrà solo i $p$ bit meno significativi di $k$, ponendo un grosso limite alla casualità con cui la funzione hash può generale i valori. Per rendere $h$ indipendente dalla chiave è utile scegliere un **numero primo non troppo vicino ad una potenza di $2$** per $m$
### Metodo della moltiplicazione
Il metodo della moltiplicazione associa alla chiave $k$ il valore hash
$$h(k)=\lfloor m \ * \ (kA - \lfloor kA \rfloor ) \rfloor$$
Dove $A$ è una costante nell'intervallo aperto $]0,1[$. Per questo metodo la scelta di $m$ non è critica quindi si può anche scegliere una potenza di $2$ per la dimensione della tabella.

Un valore prossimo a
$$\frac{(\sqrt{5} -1)}{2}$$
sembra essere una buona scelta. Questo valore è l'inverso della sezione aurea e permette di ottenere una migliore e più uniforme distribuzione, minimizzando le collisioni.
### Liste di collisione
Una volta stabilite delle buone funzioni hash dobbiamo comunque gestire i (si spera rari) casi in cui avvengono le collisioni.

Se scegliamo il metodo con liste di collisione andiamo a memorizzare i valori collidenti in una lista che ha origine a partire dell'indice della tabella ottenuto.

![[Screenshot_2025-07-04-15-59-50-580_com.fluidtouch.noteshelf3 1.png]]

Le operazioni di inserimento, eliminazione e ricerca vanno quindi effettuate sulle relative liste.

```typescript
const chainedHashSearch = (T,k) => {
	// Complessità O(n)
	return T[h(k)].find((x) => x.key === k);
};

const chainedHashInsert = (T,x) => {
	// Complessità O(1)
	T[h(k)].push(x);
};

const chainedHashDelete = (T,x) => {
	// Complessità al più O(n), dovuta alla ricerca del valore
	T[h(k)].delete(x);
};
```


**Caso peggiore**: utilizzando le liste di collisione alla peggior la tabella degenera in una singola lista (tutti i valori collidono) e quindi la complessità per l'operazione di ricerca è $\Theta(n) + \Theta(c_h)$, ovvero il costo per calcolare la funzione hash, che per le ipotesi sulle buone funzioni hash dovrebbe essere costante. La complessità nel peggiore dei casi è quindi lineare.

**Caso medio**: in questo modello il fattore di carico $\alpha$ rappresenta anche il numero medio degli elementi per lista. Nello specifico:
- se $\alpha < 1$ ci sono molte posizioni disponibili rispetto agli elementi memorizzati
- se $\alpha = 1$ il numero di elementi memorizzati è uguale alla dimensione della tabella
- se $\alpha > 1$ ci sono più elementi  memorizzati che posizioni libere (sono avvenute collisioni)
Dato questo valore di $\alpha$, le ipotesi di uniformità semplice e il valore costante per calcolare $h$, la ricerca con e senza successo nel caso medio ha un valore di $\Theta(1+\alpha)$.

**Teorema della complessità della ricerca senza successo**
In una tabella hash in cui le collisioni sono risolte mediante liste di collisione, nell'ipotesi di uniformità semplice della funzione hash, la ricerca senza successo richiede in media un tempo di
$$\Theta(1+\alpha)$$
**Dimostrazione**
Dato il costo costante della funzione hash e la necessità di scorrere una data lista per intero, la cui lunghezza è appunto $\alpha$, la complessità è $\Theta(1+\alpha)$.

**Teorema della complessità della ricerca con successo**
In una tabella hash in cui le collisioni sono risolte mediante liste di collisione, nell'ipotesi di uniformità semplice della funzione hash, la ricerca con successo richiede in media un tempo di
$$\Theta(1+\alpha)$$
**Dimostrazione**
Se la ricerca avviene con successo significa che abbiamo dovuto analizzare solo $j$ chiavi, con $j$ minore della lunghezza della lista specifica.

Dato quindi $j = 1,...,n$, sia $T_j$ la tabella $T$ contenente solo le prime $j$ chiavi $k_1,...,k_j$. Sia inoltre $\alpha_j = \frac{j}{m}$ il fattore di carico di $T_j$

La ricerca con successo della chiave $k = k_i$ in $T$ ha lo stesso costo della ricerca senza successo di $k$ in $T_{i-1} + 1$. Tutte le chiavi per ricercare $k$ in $T_i$ sono quindi già state esaminate nella ricerca in $T_{i-1}$.

Il costo della singola chiave è quindi:
$$C_i = 1 + \alpha_{i-1} = 1 + \frac{i-1}{m}$$
E il costo medio totale è la sommatoria di tutti i costi singoli:

$$T(n) = \sum^{n}_{i=1} C_i Pr\{ k = k_i\}$$
Cioè:
$$T(n) = \sum^{n}_{i=1} C_i \frac{1}{n}$$
E quindi:
$$
T(n) =\frac{1}{n}\sum^{n}_{i=1}(1+ \frac{i-1}{m})
$$

Svolgendo la sommatoria otteniamo:
$$\frac{1}{n}\sum^{n}_{i=1}(1+ \frac{i-1}{m}) = \frac{1}{n}\sum^{n}_{i=1} 1 + \sum^{n}_{i=1} \frac{i-1}{m}) =$$
Abbiamo diviso la sommatoria in due, una di cui solo uni fino ad $n$ (che è uguale ad $n$ quindi).
$$
\frac{1}{n} (n + \sum^{n}_{i=1} \frac{i-1}{m})
$$
Portiamo $\frac{1}{m}$ fuori dalla sommatoria
$$
\frac{1}{n} (n + \frac{1}{m} \sum^{n-1}_{i=0}i)
$$
Eliminiamo la sommatoria riscrivendo come $n * n - 1$
$$
\frac{1}{n} (n \ + \ \frac{1}{m}  \frac{n(n-1)}{2} )
$$
Otteniamo
$$1 + \frac{n-1}{2m} = $$
Dove $\frac{n}{m}$ è il fattore di carico e quindi scriviamo
$$
1 + \frac{\alpha}{2} - \frac{1}{2m} = \Theta(1+\alpha)  
$$
Che è la tesi.

Sia la ricerca con successo che quella senza successo hanno lo stesso costo. 
Quando $n$ è proporzionale ad $m$, cioè $n=O(m)$, il costo medio della ricerca è **costante**.
$$\alpha = \frac{n}{m} = \frac{O(m)}{m} = O(1)$$
$$\Theta(1+\alpha) = \Theta(1+1) = \Theta(1)$$

### Indirizzamento aperto
Un altro modo di gestire le collisioni è l' indirizzamento aperto.
Nell'indirizzamento aperto quando si vuole inserire una chiave $k$ ma la sua posizione predefinita è occupata si ricerca una nuova posizione. Usando questo metodo tutti gli elementi sono memorizzati direttamente nella tabella, il fattore di carico non supera mai $1$ e c'è un piccolo risparmio di memoria dato dall'assenza dei puntatori.

Per applicare l' indirizzamento aperto modifichiamo in metodi in modo tale che tengano conto della sequenza di indici già esaminati. 

**Inserimento**
Per inserire una nuova chiave si esegue una **scansione**, ovvero si esamina una successione di posizioni. La sequenza delle posizioni da esaminare dipende dalla chiave che deve essere inserita.

```typescript
const hashInsert = (T, k) => {
	let i = 0;
	while(i <= m){
		let j = h(k,i);
		if(T[j] === null || T[j] === 'DELETED' ){
			T[j] = k;
			return j;
		}
		else {
			i = i + 1;
		}
	}
	// Tabella piena
};
```

**Ricerca**
Discorso analogo va fatto per la ricerca
```typescript
const hashSearch = (T, k) => {
	let i = 0;
	let j = h(k,i);
	while(T[j] !== null && i < m){
		if(T[j] === k) return j;
		
		i = i+1;
		j = h(k,i);
	}
	// Elemento non trovato
	return null;
};
```

**Cancellazione**
La cancellazione introduce un ulteriore fattore da tenere in considerazione per quanto riguarda il costo delle operazioni.

Sostituire l'elemento con `null` spezzerebbe la **sequenza di scansione**, quindi viene generalmente sostituito con una costante che indica lo stato di eliminazione (es. `'DELETED'`). Ammettendo l'eliminazione in questo modo, i tempi di ricerca non dipendono più soltanto dal fattore di carico ma anche dal numero di cancellazioni. Se i requisiti prevedono un elevato numero di cancellazioni è preferibile scegliere quindi le liste di collisione.

#### Ricerca senza successo
**Teorema della complessità della ricerca senza successo**
In una tabella hash ad indirizzamento aperto con fattore di carico $\alpha = \frac{n}{m} < 1$ il numero medio di accessi per una ricerca senza successo è al più $\frac{1}{(1-\alpha)}$, assumendo l'uniformità della funzione hash.

**Dimostrazione**
Il costo di una ricerca dipende dal numero di tentativi che va fatto prima di riuscire a trovare l'elemento. Nel caso della ricerca senza successo andiamo ad esplorare tutta la sequenza di scansione (e non troviamo l'elemento).

La probabilità che lo slot al primo tentativo sia occupato è pari al fattore di carico $\alpha$, mentre la possibilità che sia vuoto è il suo opposto $\alpha - 1$. Se lo slot del primo tentativo è vuoto significa che l'elemento non è stato inserito e quindi la ricerca senza successo termina.

Se il primo slot è occupato, la probabilità che lo sia anche il secondo è $\alpha$. La possibilità che anche il terzo slot sia occupato è $\frac{n -1 }{m -1}$ e il numero di tentativi diventa $\alpha ^ 2$. L'$i$-esimo tentativo è $\alpha ^{i -1}$.

Il numero medio di tentativi $E$, può essere scritto come:
$$E = \sum_{i=1} ^{ \infty} P(i)$$ Dove:
- $P(i_1)$ è $1$
- $P(i_2) \le \alpha$
- $P(i_3) \le \alpha^2$
- $P(i) \le \alpha^{(i-1)}$

La progressione è quindi:

$$E \le \sum_{i=1} ^{ \infty} \alpha^{(i-1)} = 1 + \alpha + \alpha^2 + \alpha^3 ... $$

Essendo una serie geometrica standard ed essendo $\alpha < 1$ la serie converge a:
$$E \le \frac{1}{1-\alpha}$$
E quindi la ricerca senza successo è limitata superiormente.
$$O(\frac{1}{1-\alpha})$$

#### Inserimento
**Teorema della complessità dell'inserimento**
L'inserimento in una tabella hash ad indirizzamento aperto con fattore di carico $\alpha$ richiede un numero medio di accessi pari a $\frac{1}{(1-\alpha)}$, assumendo l'uniformità della funzione hash.

**Dimostrazione**
Il processo di inserimento ricadono nel caso della ricerca senza successo. La dimostrazione è identica.

#### Ricerca con successo
**Teorema della complessità della ricerca con successo**
In una tabella hash ad indirizzamento aperto con fattore di carico $\alpha = \frac{n}{m} < 1$ il numero medio di accessi per una ricerca con successo è al più $$\frac{1}{\alpha}log_e(\frac{1}{1-\alpha})$$
**Dimostrazione**
Affinché la ricerca abbia successo la chiave deve essere precedentemente stata inserita, quindi è paragonabile all'inserimento di un $i$-esimo elemento. Questo significa che la tabella contiene $i-1$ elementi e il fattore di carico è $x = \frac{i-1}{m}$.

Il costo per l'inserimento di un $i$-esimo elemento è $$\frac{1}{ (1- \frac{(i -1)}{m})}$$
Quindi il numero di tentativi per una ricerca con successo è:
$$E_S = \frac{1}{n} \sum^{n-1} _{i=0}  \frac{1}{1 - \frac{i}{m}}$$
Per $n$ abbastanza grande possiamo ricondurre la sommatoria all'integrale:

$$E_S \approx \frac{1}{\alpha} \int ^ \alpha _0 \frac{1}{1 - x} dx$$
Risolviamo l'integrale:
$$E_S \approx \frac{1}{\alpha} [ -log_e (1-x)]{^\alpha _0}$$
$$E_S \approx \frac{1}{\alpha} (-log_e (1-\alpha) - (-log_e (1 -0)))$$
$$E_S \approx \frac{1}{\alpha} (-log_e (1-\alpha) + log_e(1)$$
$$E_S \approx \frac{1}{\alpha} (-log_e (1-\alpha)$$
Usando l'inverso $-log_e(a) = log_e(\frac{1}{a})$ otteniamo la formula:
$$E_S \approx \frac{1}{\alpha} log_e (\frac{1}{1-\alpha})$$
Che è la tesi.

Se $0 < alpha < 1$ valgono le disequazioni:
- $\frac{1}{1-\alpha} > 1 + \alpha$
- $\frac{1}{\alpha}log_e(\frac{1}{1-\alpha}) > 1 + \frac{\alpha}{2}$
- $\frac{1}{1-\alpha} > \frac{1}{\alpha}log_e(\frac{1}{1-\alpha})$

#### Tecniche di scansione per l'indirizzamento aperto
##### Scansione lineare
Nel caso dell'indirizzamento aperto le funzioni hash devono, come già accennato, tenere in considerazione anche l'indice (o tentativo) per generare la **sequenza di scansione**. 

Il metodo di **scansione lineare** usa la funzione hash:
$$h(k,i) = (h'(k) + i) \ mod \ m$$
Che però soffre di un fenomeno, detto **agglomerazione primaria**, ovvero gli indici ottenuti sono consecutivi (in modulo) e ciò può aumentare il tempo di accesso.

La scansione lineare genera solo $m$ sequenze di scansione distinte, mentre l'ideale sarebbe averne $m!$ (per ipotesi di uniformità semplici delle funzioni hash). Avendo $m!$ permutazioni si avrebbe una certa equiprobabilità per quanto riguarda le collisioni infatti.

##### Scansione quadratica
Il metodo di scansione quadrata risolve il problema dell'aggregazione primaria. In questo caso la funzione hash è definita come:
$$h(k,i) = (h'(k) + c_1i + c_2i^{2} ) mod \ m$$
con $c_1, c_2 \ne 0$ costanti ausiliarie.

Questo metodo soffre di una forma di agglomerazione più lieve, detta **agglomerazione secondaria** (due chiavi diverse possono avere la stessa identica sequenza di scansione).

Inoltre:
 - La scelta delle costanti diventa critica perchè una scelta errata potrebbe non generare sequenze di scansione che coprono tutta la tabella (lasciando spazio inutilizzato).
 - Anche in questo caso ci sono solo $m$ sequenze distinte di scansione

##### Hashing doppio
Il metodo dell'hashing doppio prevede l'utilizzo di due funzioni hash più interne:
$$h(k,i) = (h_1(k) + ih_2(k))\ mod \ m$$
Questo metodo non soffre di nessuna agglomerazione e funziona meglio rispetto agli altri due visto che ogni possibile coppia $h_1(k),h_2(k)$ produce una sequenza di scansione distinta. Abbiamo quindi $m^2$ scansioni distinte possibili. 

Per avere dei risultati ottimali:
- Il valore $h_2(k)$ deve essere primo con la dimensione $m$, questo per ogni chiave.
- Se $m$ è una potenza di $2$ basta invece definire $h_2$ in maniera tale che restituisca sempre un valore dispari
- Scegliere $m$ numero primo e porre $h_1(k) = k \ mod \ m$ e $h_2(k) = 1 + (k \ mod \ m')$ con $m'$ poco più piccolo di $m$. 

**Link utili**
- [Hashing with Chaining MIT](https://youtu.be/0M_kIqhwbFo?si=neA2XGCeqpYup4fj)
- [Hashing MIT](https://youtu.be/Nu8YGneFCWE?si=11Po2AutRE8Gbj1-)
