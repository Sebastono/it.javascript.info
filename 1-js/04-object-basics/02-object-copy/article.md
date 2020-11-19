# Copia e riferimento degli oggetti

Una delle fondamentali differenze tra gli oggetti e i tipi primitivi è che i primi vengono copiati "per riferimento", al contrario degli ultimi, come stringhe, numeri, booleani, etc -- i quali vengono sempre copiati come "valore intero".

Questo concetto è facile da comprendere se analizziamo con più attenzione "under a cover" of what happens when we copy a value.

Iniziamo con un primitivo, come una stringa.

Mettiamo qui una copia di `message` all'interno `phrase`:

```js
let message = "Ciao!";
let phrase = message;
```
Come risultato avremo due variabili indipendenti, ognuna delle quali contiene la stringa `"Ciao!"`.

![](variable-copy-value.svg)

Un risultato abbastanza scontato, giusto?

Gli oggetti non funzionano allo stesso modo.

**Una variabile assegnata ad un oggetto non contiene l'oggetto in sè, ma il suo "indirizzo nella memoria", o, in altre parole "un riferimento" all'oggetto.**

Studiamo un esempio di tale variabile:

```js
let user = {
  name: "John"
};
```

Ecco come viene fisicamente salvato in memoria:

![](variable-contains-reference.svg)

L'oggetto è salvato da qualche parte all'interno della memoria (a destra in figura), mentre la variabile `user` (a sinistra) contiente solamente un "riferimento" ad esso.

Potremmo considerare una variabile oggetto, come ad esempio `user`, come se fosse un foglio di carta con il suo indirizzo.

Quando eseguiamo azioni sull'oggetto, e.g. take a property `user.name`, JavaScript engine va alla ricerca dell'oggetto all'indirizzo indicato ed esegue le operazioni sull'oggetto stesso.

Questo è il motivo per cui è importante.

**Quando una variabile oggetto viene copiata -- è soltato il riferimento a venir copiato, ma l'oggetto in sè non viene duplicato.**

Per esempio:

```js no-beautify
let user = { name: "John" };

let admin = user; // copia del riferimento
```

Ora sono presenti due variabili, ognuna con un riferimento allo stesso oggetto:

![](variable-copy-reference.svg)

Come potete vedere, rimane comunque un singolo oggetto, però con due variabili che si riferiscono ad esso.

Possiamo quindi utilizzare una qualsiasi variabile per accedere all'oggetto e modificarne i contenuti:

```js run
let user = { name: 'John' };

let admin = user;

*!*
admin.name = 'Pete'; // changed by the "admin" reference
*/!*

alert(*!*user.name*/!*); // 'Pete', changes are seen from the "user" reference
```


It's just as if we had a cabinet with two keys and used one of them (`admin`) to get into it. Then, if we later use another key (`user`) we can see changes.

## Confronto per riferimento 

Due oggetti sono uguali solo se sono lo stesso oggetto.

Ad esempio, prendiamo due riferimenti allo stesso oggetto `a` e `b`, perciò sono uguali:

```js run
let a = {};
let b = a; // copia del riferimento

alert( a == b ); // vero, entrambe le variabili si riferiscono allo stesso oggetto
alert( a === b ); // vero
```
Qui invece due oggetti indipendenti non sono uguali, nonostante appaiano simili (entrambi sono vuoti):

```js run
let a = {};
let b = {}; // due oggetti indipendenti

alert( a == b ); // falso
```

Per confronti come  `obj1 > obj2` o per confronti con primitivi `obj == 5`, gli oggetti sono converiti in primitivi. Studieremo molto presto come funziona la conversione degli oggetti, ma, a dire il vero, tali confornti sono molto rari, ed appaiono di solito come conseguenza di un errore di programmazione.

## Cloning and merging, Object.assign

Quindi, copiare una variabile oggetto crea un ulteriore riferimento allo stesso oggetto.

Ma come potremmo fare se dovessimo duplicare l'oggetto? Crearne una copia indipendente, un clone?

Anche questo è fattibile, solamente un po' più complicato, considerato che non c'è un metodo di base in Javascript per farlo. In realtà, fare un'operazione del genere è raramente necessario, copiare per riferimento è sufficiente il più delle volte.

Ma se dovessimo veramente farlo, allora avremmo bisogno di creare un nuvo oggetto e replicare la struttura di quello esistente iterando tutte le sue proprietà e copiandole man mano sul livello primitivo dell'oggetto "clone".

In tale maniera:

```js run
let user = {
  name: "John",
  age: 30
};

*!*
let clone = {}; // il nuovo oggetto vuoto

// copiamo tutte le proprietà di "user" al suo interno
for (let key in user) {
  clone[key] = user[key];
}
*/!*

// ora il clone è un oggetto completamente indipendente con lo stesso contenuto.
clone.name = "Pete"; // abbiamo cambiato il suo contenuto 

alert( user.name ); //  John è comunque rimasto inalterato nell'oggetto originario
```

Possiamo inoltre usare il metodo [Object.assign](mdn:js/Object/assign) per fare la stessa operazione.

La sintassi è:

```js
Object.assign(dest, [src1, src2, src3...])
```

- Il primo argomento `dest` è l'oggetto bersaglio.
- Gli argomenti aggiuntivi `src1, ..., srcN` (possono essere quanti ne abbiamo bisogno) sono oggetti sorgente.
- Copia le proprietà di tutti gli oggetti sorgente `src1, ..., srcN` nell'bersaglio `dest`. In altre parole, le proprietà di tutti gli argomenti a partire dal secondo vengono copiati nel primo oggetto.
- La chiamata ritorna `dest`.

Per esempio, possiamo utilizzarlo per combinare molteplici oggetti in uno solo:
```js
let user = { name: "John" };

let permissions1 = { canView: true };
let permissions2 = { canEdit: true };

*!*
// copia tutte le proprietà da permissions1 e permissions2 all'interno di utente
Object.assign(user, permissions1, permissions2);
*/!*

// now user = { name: "John", canView: true, canEdit: true }
```

Se il nome della proprietà copiata esiste già, viene sovrascritto:

```js run
let user = { name: "John" };

Object.assign(user, { name: "Pete" });

alert(user.name); // now user = { name: "Pete" }
```

Possiamo anche usare `Object.assign` per rimpiazzare il ciclo iterativo `for..in` per delle clonazioni semplici:

```js
let user = {
  name: "John",
  age: 30
};

*!*
let clone = Object.assign({}, user);
*/!*
```

Copia tutte le proprietà di `user` all'interno dell'oggetto vuoto e lo ritorna.

## Clonazione annidata 

Fino a questo punto abbiamo assunto che tutte le proprietà di `user` fossero primitive. Ma le proprietà possono anche essere riferimenti ad altri oggetti. Come possiamo gestirle?

In tale maniera:
```js run
let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};

alert( user.sizes.height ); // 182
```

Però non è sufficiente copiare `clone.sizes = user.sizes`, perchè `user.sizes`, essendo un oggetto, verrà copiato per riferimento. Perciò `clone` e `user` condivideranno la stessa dimensione:

In questo modo:

```js run
let user = {
  name: "John",
  sizes: {
    height: 182,
    width: 50
  }
};

let clone = Object.assign({}, user);

alert( user.sizes === clone.sizes ); // vero, stesso oggetto

// user e clone condividono la stessa dimesione
user.sizes.width++;       // cambia una proprietà da un posto
alert(clone.sizes.width); // 51, vede il risultato dall'altra
```

Per sistemarlo, dovremmo usare il ciclo iterativo che esamina ogni valore di  cloning loop  `user[key]` e, nel caso si tratti di un oggetto, ne replichi anche la struttura. Questo viene chiamato "deep cloning".

Possiamo avvalerci della ricorsione per implementarlo. Oppure, per non dover riscoprire l'acqua calda, utilizzare un implementazione già esistente, come ad esempio[_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep) dalla the JavaScript library [lodash](https://lodash.com).

## Riassunto 
Gli oggetti sono assegnati e copiati per riferimento. In altre parole, una variabile non contiente il "valore dell'oggetto", ma un  "riferimento" (indirizzo nella memoria) per il valore. Perciò quando quella variabile viene copiata o passata come argomento ad una funzione è il riferimento ad essere copiato, e non l'oggetto stesso.

Tutte le operazioni svolte attraverso una copia del riferimento (like adding/removing properties) vengono eseguite sullo stesso singolo oggetto.

Per fare una "copia reale" (un clone) possiamo utilizzare `Object.assign` per la così chiamata "copia per indirizzo" (gli oggetti annidati sono copiati per riferimento) oppure una funzione "copia in profondità" , come [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep).
