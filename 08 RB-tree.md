Un albero rosso-nero (red-black tree) è un albero binario di ricerca addizionato con alcune caratteristiche e vincoli particolari che permettono di mantenerlo bilanciato, ovvero che evitino che l'albero (o qualche suo particolare ramo) degeneri in lista. Per fare ciò "coloriamo" i nodi di rosso o di nero e ne cambiamo all'occorrenza la disposizione in modo tale da mantenere le proporzioni, nello specifico, gli alberi rosso-neri garantiscono che **nessuno dei cammini dalla radice ad una delle foglie sia lungo più del doppio di qualsiasi altro**.

Essendo un albero binario di ricerca eredita le proprietà tipiche di questa struttura dati:
- Ogni nodo ha un valore maggiore rispetto al figlio sinistro e minore rispetto al destro, vale a dire $left(x) \le x \le right(x)$
- Operazioni di ricerca, inserimento e cancellazione avvengono in tempo logaritmico

Le proprietà particolari di un albero rosso nero sono invece le seguenti:
1. Ogni nodo è rosso oppure nero
2. La radice è nera
3. Le foglie (puntatori a NULL o NIL) sono nere
4. Se un nodo è rosso, entrambi i suoi figli sono neri
5. Per ogni nodo, tutti i cammini semplici che vanno dal nodo ad una delle foglie hanno lo stesso numero di nodi neri (altezza nera)

Un esempio di definizione delle interfacce potrebbe essere:

```typescript
type Color = 'RED' | 'BLACK';

interface Node<T> {
  value: T;
  color: Color;
  left: Node<T> | null;
  right: Node<T> | null;
  parent: Node<T> | null;
}

interface Tree<T> {
  root: Node<T> | null;
}
```

📓**Definizione: altezza nera**
L'altezza nera di un nodo $x$, indicata con  $bh(x)$, è il numero di nodi neri lungo un cammino semplice che parte da suddetto nodo (escluso) e finisce in una foglia qualsiasi.
L'altezza nera di un albero rosso-nero è l'altezza nera della sua radice

**Lemma**: L'altezza massima di un albero rosso-nero con $n$ nodi interni è $2 \ log(n+1)$

Le operazioni di ricerca, minimo, massimo, predecessore e successore sono tutte implementate in tempo $O(log \ n)$

## Rotazioni
Dopo un inserimento o un'eliminazione di nodo in un albero rosso-nero alcune delle proprietà fondamentali potrebbero ritrovarsi temporaneamente violate. Per ristabilire la situazione ricorriamo all'operazione fondamentale di **rotazione**, ovvero lo spostamento del sotto-albero che si origina a partire dal nodo. 

La rotazione di un nodo può avvenire verso destra o vestro sinistra, in entrambi i casi richiede tempo costante $O(1)$ visto che non è nient'altro che uno scambio di puntatori / riferimenti.

Quando effettuiamo una rotazione, assumiamo che il figlio presente nel verso opposto sia diverso da NULL. Per la rotazione a sinistra è il figlio destro, per quella destra invece è il sinistro. In mancanza di questo requisito, non è possibile effettuare la rotazione

```typescript
const leftRotate = <T>(tree: Tree<T>, x: Node<T>): void => {
  const y = x.right;
  if (!y) {
    throw new Error("Cannot perform left rotation: right child is null.");
  }

  // Passo 1: spostare il sottoalbero sinistro di y come figlio destro di x
  x.right = y.left;
  if (y.left) {
    y.left.parent = x;
  }

  // Passo 2: collegare il genitore di x a y
  y.parent = x.parent;
  if (!x.parent) {
    // x era la radice -> y diventa la nuova radice
    tree.root = y;
  } else if (x === x.parent.left) {
    x.parent.left = y;
  } else {
    x.parent.right = y;
  }

  // Passo 3: mettere x come figlio sinistro di y
  y.left = x;
  x.parent = y;
};

```

```typescript
const rightRotate = <T>(tree: Tree<T>, y: Node<T>): void => {
  const x = y.left;
  if (!x) {
    throw new Error("Cannot perform right rotation: left child is null.");
  }

  // Passo 1: spostare il sottoalbero destro di x come figlio sinistro di y
  y.left = x.right;
  if (x.right) {
    x.right.parent = y;
  }

  // Passo 2: collegare il genitore di y a x
  x.parent = y.parent;
  if (!y.parent) {
    // y era la radice -> x diventa la nuova radice
    tree.root = x;
  } else if (y === y.parent.left) {
    y.parent.left = x;
  } else {
    y.parent.right = x;
  }

  // Passo 3: mettere y come figlio destro di x
  x.right = y;
  y.parent = x;
};
```

![[Screenshot_2025-06-16-11-39-25-525_md.obsidian.png]]

## Inserimento
Per inserire un nodo in un albero rosso-nero si ricerca la posizione ideale come avviene per l'inserimento classico e si inserisce il nodo con colore rosso. Si effettuano poi eventuali rotazioni e ricolorazioni per evitare che le proprietà vengano violate (radice nera, un nodo rosso ha solo figli neri).

Il metodo di `INSERT-FIXUP`, una volta effettuato l'inserimento normalmente, si occupa di ricolorare i nodi ed effettuare le rotazioni, in maniera tale che eventuali proprietà violate vengano ripristinate.

In linea di principio il comportamento è il seguente:
1.  Se il nodo che ho inserito è un figlio sinistro
	1. Se lo zio è rosso: coloro il padre e lo zio di nero e il nonno di rosso. Assegno il puntatore del nodo corrente al nonno, così se questa mossa ha creato altre irregolarità verrà fixata alla prossima iterazione del ciclo.
	 2. Se lo zio è nero e se il padre è un figlio destro effettuo una rotazione sinistra sul nonno
	3. Se lo zio è nero e il padre è figlio sinistro coloro il padre di nero e il nonno di rosso ed eseguo una rotazione destra sul nonno.
2.  Se il nodo che ho inserito è un figlio destro (speculare):
	1. Se lo zio è rosso ricoloro in maniera uguale al caso $1.1$
	2.  Se lo zio è nero e il padre è figlio destro effettuo una rotazione destra sul nonno
	3.  Se lo zio è nero e il padre è figlio sinistro effettuo una rotazione sinistra sul nonno
Coloro infine la radice di nero per eventuali incongruenze.

```typescript
INSERT-FIXUP(T, z)
	WHILE z.parent.color = ROSSO:
	    IF z.parent = z.parent.parent.left:
	        y = z.parent.parent.right // z è figlio sinistro → y è zio destro
	        IF y.color = ROSSO:
	            // Caso 1
	            z.parent.color = NERO
	            y.color = NERO
	            z.parent.parent.color = ROSSO
	            z = z.parent.parent
	        ELSE:
	            IF z = z.parent.right:
	                // Caso 2
	                z = z.parent
	                LEFT-ROTATE(T, z)
	            // Caso 3
	            z.parent.color = NERO
	            z.parent.parent.color = ROSSO
	            RIGHT-ROTATE(T, z.parent.parent)
	    ELSE:
	        y = z.parent.parent.left    // caso speculare
	        IF y.color = ROSSO:
	            z.parent.color = NERO
	            y.color = NERO
	            z.parent.parent.color = ROSSO
	            z = z.parent.parent
	        ELSE:
	            IF z = z.parent.left:
	                z = z.parent
	                RIGHT-ROTATE(T, z)
	            z.parent.color = NERO
	            z.parent.parent.color = ROSSO
	            LEFT-ROTATE(T, z.parent.parent)
	
	T.root.color = NERO
```

## Eliminazione
La cancellazione di un nodo che ha entrambi i figli è relativamente semplice, occorre infatti solo ricercare il successore del nodo da eliminare e sostituirlo con esso. Avendo entrambi i figli il successore si troverà per certo nella parte più a sinistra del sotto-albero destro.

La situazione è la seguente se invece il nodo da eliminare ha un solo figlio.

Dato quindi un nodo $z$ da cancellare e siano $x$ l'unico figlio di $z$ ($NIL$ se $z$ è una foglia) e $p$ il padre di $z$:
1. Rimuoviamo il collegamento con padre e figlio ($x$ diventa figlio di $p$ e $p$ padre di $x$)
2. Se $z$ era rosso nessuna proprietà viene violata dalla sua eliminazione, concludiamo qui
3. Se $z$ era nero potrebbe insorgere una violazione sull'altezza nera 

Nel caso di una violazione della proprietà dell'altezza nera:
- se $x$ è rosso lo coloriamo di nero
- se $x$ è nero gli assegniamo un coloro fittizio, detto **doppio-nero**, da contare per due nel calcolo dell'altezza nera
Eseguiamo un operazione di `DELELE-FIXUP`, con un procedimento simile a quello della insert, per ripristinare le proprietà e muovere il doppio nero fino alla radice

Confrontiamo il colore del nodo $x$ con quello del fratello $w$.
1. Se $w$ è nero e ha almeno un figlio rosso:
	1. Se il figlio sinistro è nero e quello destro rosso: scambiamo il colore di $p$ con quello di $w$ ed eseguiamo una rotazione a sinistra su $p$. Togliamo quindi il doppio nero da destra e coloriamo il figlio destro di $w$ di nero![[Screenshot_2025-06-16-15-19-57-247_com.fluidtouch.noteshelf3.png]]
	2. Se il sinistro è rosso e il destro nero: coloriamo sia $p$ che il figlio destro di $w$ di nero e ruotiamo $p$ a sinistra. Ci ritroviamo nel caso $1.1$ e possiamo risolverlo come già indicato![[Screenshot_2025-06-16-15-23-35-936_com.fluidtouch.noteshelf3.png]]
2. Se $w$ è nero e ha entrambi i figli neri:
	1. Se il padre di $w$ (e di $x$) è rosso: togliamo il doppio nero da $x$ e coloriamo $w$ di rosso, facendo risalire il nero su $p$![[Screenshot_2025-06-16-15-27-58-508_com.fluidtouch.noteshelf3.png]]
	2. Se il padre di $w$ è nero: togliamo il doppio nero a $x$ e coloriamo $w$ di rosso, trasformando $p$ in un doppio nero. $p$ diventerà il nuovo $x$ e la reiterazione della procedura risolverà le violazioni successivamente![[Screenshot_2025-06-16-15-30-04-268_md.obsidian.png]]
3. $w$ è rosso: viene trasformato in uno dei casi precedenti ruotando $p$ a sinistra![[Screenshot_2025-06-16-15-31-44-834_md.obsidian.png]]


```c
RB-DELETE-FIXUP(T, x)
  // Continua finché x non è la radice e ha ancora un "nero extra"
  while x != T.root AND x.color == BLACK
    // Caso: x è un figlio sinistro
    if x == x.parent.left
      w = x.parent.right // w è il fratello di x

      // ---- CASO 1: Il fratello w è ROSSO ----
      if w.color == RED
        w.color = BLACK
        x.parent.color = RED
        LEFT-ROTATE(T, x.parent)
        w = x.parent.right // Aggiorna w al nuovo fratello
      
      // ---- CASO 2: Il fratello w è NERO e i suoi figli sono NERI ----
      if w.left.color == BLACK AND w.right.color == BLACK
        w.color = RED
        x = x.parent // Propaga il problema al padre
      else
        // ---- CASO 3: Il fratello w è NERO, figlio sx ROSSO, figlio dx NERO
        if w.right.color == BLACK
          w.left.color = BLACK
          w.color = RED
          RIGHT-ROTATE(T, w)
          w = x.parent.right // Aggiorna w al nuovo fratello
        
        // ---- CASO 4: Il fratello w è NERO e suo figlio destro è ROSSO ----
        w.color = x.parent.color
        x.parent.color = BLACK
        w.right.color = BLACK
        LEFT-ROTATE(T, x.parent)
        x = T.root // Problema risolto, termina il ciclo

    // Caso simmetrico: x è un figlio destro
    else
      w = x.parent.left // w è il fratello di x

      // ---- CASO 1 (simmetrico) ----
      if w.color == RED
        w.color = BLACK
        x.parent.color = RED
        RIGHT-ROTATE(T, x.parent)
        w = x.parent.left

      // ---- CASO 2 (simmetrico) ----
      if w.right.color == BLACK AND w.left.color == BLACK
        w.color = RED
        x = x.parent
      else
        // ---- CASO 3 (simmetrico) ----
        if w.left.color == BLACK
          w.right.color = BLACK
          w.color = RED
          LEFT-ROTATE(T, w)
          w = x.parent.left

        // ---- CASO 4 (simmetrico) ----
        w.color = x.parent.color
        x.parent.color = BLACK
        w.left.color = BLACK
        RIGHT-ROTATE(T, x.parent)
        x = T.root
  
  // Assicura che la radice o il nodo che ha assorbito il "nero extra" sia NERO
  x.color = BLACK
```



Teorema: Un albero rosso-nero con n nodi interni ha un'altezza: $h≤2log_2(n+1)$

Dimostrazione: consideriamo un sottoalbero radicato in un nodo $x$. Sia $bh(x)$ l'altezza nera di $x$. Vogliamo dimostrare per induzione sull'altezza di $x$ che il sottoalbero radicato in $x$ contiene almeno $2 \ bh(x) −1$ nodi interni.

Caso Base: Se l'altezza di $x$ è 0, allora $x$ è una foglia (NIL). L'altezza nera $bh(x)=0$. Il numero di nodi interni nel sottoalbero è $2^0−1=1−1=0$. Questo è vero poiché le foglie non sono nodi interni.

Passo Induttivo: Assumiamo che la proprietà sia vera per tutti i nodi con altezza minore dell'altezza di $x$.
Sia $x$ un nodo interno con altezza positiva e altezza nera $bh(x)$.
I figli di $x$, chiamiamoli $c_1$ e $c_2$, hanno un'altezza nera di:
- $bh(c_i) = bh(x)$ se il figlio $c_i$ è rosso.
- $bh(c_i)=bh(x)−1$ se il figlio $c_i$ è nero.

Visto che per le proprietà se un nodo è rosso, i suoi figli sono neri, sappiamo che se $x$ è rosso, i suoi figli devono essere neri. In ogni caso, i figli di un nodo $x$ hanno un'altezza nera di almeno $bh(x)−1$.

Applicando l'ipotesi induttiva ai figli di $x$, ogni sottoalbero radicato in un figlio di $x$ contiene almeno $2 bh(x)^{−1}−1$ nodi interni.

Quindi, il numero di nodi interni nel sottoalbero radicato in $x$, denotato con $n(x)$, è: $n(x)≥(2 bh(x)^{−1}−1)+(2 bh(x)^{−1}−1)+1$ (i nodi dei due sottoalberi figli più $x$ stesso). Quindi:
$n(x)≥2⋅(2bh(x)^{−1})−2+1$
$n(x)≥2 bh(x)−1$

Questo completa la prova induttiva che un sottoalbero radicato in $x$ con altezza nera $bh(x)$ contiene almeno $2bh(x)−1$ nodi interni.

Ora consideriamo l'intero albero rosso-nero con $n$ nodi interni e altezza $h$. Sia $r$ la radice.
Sappiamo che la radice è nera, quindi $bh(r)$ è l'altezza nera dell'albero.
Visto che se un nodo è rosso, i suoi figli sono neri, almeno la metà dei nodi in qualsiasi cammino semplice dalla radice a una foglia devono essere neri (al massimo possono esserci nodi rossi alternati a nodi neri).
Quindi, l'altezza nera della radice $bh(r)$ deve essere almeno $h/2$.
Cioè, $bh(r)≥h/2$.

Abbiamo dimostrato che il numero di nodi interni $n$ nell'albero è:
$n≥2^{bh(r)}−1$

Sostituendo $bh(r)≥h/2$:
$n≥2^{h/2}−1$

Ora, riorganizziamo per trovare $h$:
$n+1≥2^{h/2}$
$log_2(n+1)≥log_2(2^{h/2})$
$log_2(n+1)≥h/2$
$2log_2(n+1)≥h$

Quindi, $h≤2log_2(n+1)$, che è la tesi.
