---
title: Percolazione
exports:
   - format: pdf
---

(sec:percolazione)=
# Introduzione

Immaginate una popolazione di animali selvatici che vive in un territorio via via più frammentato dall'urbanizzazione: strade, campi coltivati, quartieri residenziali spezzano l'ambiente naturale in tante piccole isole verdi. Un singolo animale può muoversi liberamente all'interno di un'isola, ma per raggiungerne un'altra ha bisogno che le due siano connesse da un corridoio di habitat idoneo. Finché le isole verdi occupano una piccola frazione del territorio è molto probabile che siano tutte isolate tra loro, con il risultato che gli animali resteranno confinati in un'area piccola rispetto all'estensione totale del territorio. Se però la frazione di territorio "verde" aumenta, a un certo punto si formeranno dei corridoi che collegheranno le isole tra loro, fino a creare un singolo agglomerato che attraverserà l'intero territorio da un capo all'altro. Da quel momento un animale potrà, in linea di principio, spostarsi da un estremo all'altro del territorio senza mai lasciare l'habitat idoneo.

Questo cambiamento non avviene gradualmente: al crescere della frazione di territorio disponibile, la probabilità che esista un simile corridoio "da un capo all'altro" passa, in modo piuttosto brusco, da (quasi) nulla a (quasi) certa. Il valore critico della frazione di territorio al quale avviene questo cambiamento è l'analogo di ciò che chiameremo **soglia di percolazione**.

Lo stesso tipo di fenomeno compare in contesti molto diversi tra loro:

* un incendio boschivo si propaga da un albero a quelli vicini; se la densità di alberi è troppo bassa, il fuoco si spegne dopo aver bruciato pochi alberi isolati, mentre sopra una certa densità critica può attraversare l'intera foresta;
* le reti (elettriche, idriche, o di altra natura) funzionano fintantoché esistono abbastanza connessioni funzionanti che permettono di collegare i nodi principali della rete; se la "densità di guasti" è troppo alta la rete smette di funzionare;
* in un materiale composito fatto di grani conduttori dispersi in una matrice isolante, la corrente può attraversare il materiale solo se i grani conduttori formano un cammino continuo da un elettrodo all'altro: sotto una certa concentrazione di grani il materiale è isolante, sopra diventa conduttore;
* la diffusione di un'epidemia in una popolazione richiede che esista una catena continua di contatti tra individui suscettibili: se i contatti sono troppo radi, l'epidemia si esaurisce localmente; se sono abbastanza densi, può raggiungere una frazione estesa della popolazione.

In tutti questi esempi la domanda fisica è la stessa: **esiste un cammino continuo che attraversa l'intero sistema?** E la risposta cambia bruscamente al variare di un solo parametro, la densità (di habitat, di alberi, di connessioni, di contatti). Un cambiamento improvviso di comportamento macroscopico al variare con continuità di un parametro microscopico è la firma di una **transizione di fase**, un concetto che incontrerete anche nel corso di termodinamica in un contesto molto diverso (per esempio la transizione liquido-vapore). La percolazione è probabilmente il modello più semplice in cui si possa studiare una transizione di fase al calcolatore, con pochissimi ingredienti fisici: per questo è un ottimo punto di partenza.

```{note} Da dove viene il nome
Il termine "percolazione" nasce in un contesto ancora più quotidiano: descrive il filtraggio di un fluido attraverso un mezzo poroso, come l'acqua calda che attraversa la polvere di caffè in una moka, o il petrolio che si muove attraverso la roccia porosa di un giacimento. Il fluido riesce ad attraversare il mezzo solo se esiste un cammino continuo di pori collegati tra loro: la stessa domanda che ci porremo qui, applicata a un caso concreto.
```

(sec:percolazione-reticolo)=
# Percolazione su un reticolo

## Il modello

Per studiare la percolazione al calcolatore ne costruiamo una versione semplificata e discreta. Consideriamo un reticolo quadrato $L \times L$, i cui siti sono identificati da una coppia di indici interi $(i,j)$, con $i,j = 0, \dots, L-1$, dove $i$ indica la colonna e $j$ la riga. A differenza di quanto avete visto per il gas reticolare, qui non useremo condizioni al bordo periodiche: il reticolo ha bordi veri e propri, oltre i quali non esistono altri siti, dato che ci chiederemo se esiste un cammino che attraversa il sistema da un bordo a quello opposto.

Ogni sito del reticolo viene dichiarato **occupato** con probabilità $p$, e **vuoto** con probabilità $1-p$, indipendentemente da tutti gli altri siti. Questa è la cosiddetta **percolazione di sito** (*site percolation*). Due siti occupati sono considerati **connessi** se sono primi vicini sul reticolo, cioè se differiscono di un'unità in una sola delle due coordinate:

$$
(i,j) \sim (i', j') \iff |i-i'|+|j-j'| = 1.
$$

Un **cluster** è un insieme massimale di siti occupati, tutti raggiungibili l'uno dall'altro attraverso una catena di connessioni tra primi vicini. In altre parole, due siti occupati appartengono allo stesso cluster se e solo se esiste un cammino di siti occupati, ciascuno primo vicino del successivo, che li collega.

```{note} Percolazione di legame
Un modello strettamente imparentato è la **percolazione di legame** (*bond percolation*): qui tutti i siti sono presenti, ma è ciascun *legame* tra due primi vicini a essere presente con probabilità $p$ e assente con probabilità $1-p$, indipendentemente dagli altri legami. Due siti appartengono allo stesso cluster se sono collegati da una catena di legami presenti. Concettualmente il modello è identico a quello di sito: cambia solo l'oggetto (sito o legame) sottoposto al sorteggio casuale. Nel seguito lavoreremo sempre con la percolazione di sito, ma la variante di legame ricompare spesso in letteratura.
```

Diciamo che un cluster **percola** (o è *percolante*) se collega due bordi opposti del reticolo, cioè se contiene almeno un sito nella prima colonna ($i=0$) e almeno uno nell'ultima ($i=L-1$), oppure almeno un sito nella prima riga ($j=0$) e almeno uno nell'ultima ($j=L-1$). In questo caso esiste un cammino continuo di siti occupati che attraversa il reticolo da sinistra a destra o dall'alto in basso. Dato che il reticolo è quadrato, le due direzioni sono equivalenti per simmetria, e non c'è motivo di privilegiarne una.

```{figure} figures/percolazione.png
:name: fig:percolazione
:align: center
:width: 600px

Due configurazioni di un reticolo 8x8 con 16 siti occupati ($p = 0.25$) generate casualmente. In (a) i siti formano cinque cluster (colorati diversamente), mentre (b) contiene tre cluster, di cui uno percolante (in rosso).
```

La [](#fig:percolazione) mostra due configurazioni di esempio generate casualmente con lo stesso $p$. Siti colorati nello stesso modo fanno parte dello stesso cluster. Il pannello (b) mostra un esempio di cluster percolante: partendo dal bordo di sinistra e passando da un sito occupato a un suo primo vicino occupato si può arrivare al bordo di destra.

## La soglia di percolazione

Per $p$ molto piccolo, quasi tutti i siti occupati sono isolati o formano piccoli cluster: è estremamente improbabile che esista un cluster percolante. Le due configurazioni rappresentate in [](#fig:percolazione) sono vicine a questo limite: se generassimo molte altre configurazioni, la gran parte conterrebbe tanti piccoli cluster, e solo pochissime percolerebbero. D'altro canto, per $p$ vicino a 1, quasi tutti i siti sono occupati, ed è quasi certo che esista un cluster percolante (nel caso estremo $p=1$ tutto il reticolo è un unico cluster). Ci aspettiamo quindi che la probabilità di osservare un cluster percolante, che indichiamo con $P_{\rm perc}(p, L)$, sia una funzione crescente di $p$, che passa da valori vicini a 0 a valori vicini a 1.

Si può dimostrare (non lo faremo qui) che nel limite di reticolo infinito, $L \to \infty$, questa transizione diventa **discontinua**: esiste un valore critico $p_c$, detto **soglia di percolazione**, tale che

$$
\lim_{L\to\infty} P_{\rm perc}(p, L) =
\begin{cases}
0 & \text{se } p < p_c,\\
1 & \text{se } p > p_c.
\end{cases}
$$

Il valore di $p_c$ dipende dal tipo di reticolo (quadrato, triangolare, esagonale, cubico, $\ldots$) e dal tipo di percolazione (di sito o di legame): non esiste un unico $p_c$ universale. Per la percolazione di sito su reticolo quadrato 2D non è nota una formula analitica per $p_c$, ma il suo valore è stato determinato numericamente con grande precisione ([](doi:10.1088/1751-8113/47/13/135001)),

$$
p_c \simeq 0.592746.
$$

```{note} Un caso in cui $p_c$ si conosce esattamente
Per la percolazione di *legame* sul reticolo quadrato 2D vale invece un risultato esatto, $p_c = 1/2$, che si può dimostrare con un argomento di simmetria legato al concetto di *dualità* ([](doi:10.1112/S002460930601842X)). È un fatto piuttosto raro nella teoria della percolazione: nella stragrande maggioranza dei casi, incluso quello che consideriamo qui, il valore di $p_c$ si può ottenere solo numericamente.
```

Anche se il valore numerico di $p_c$ dipende dai dettagli del reticolo, molte proprietà del sistema vicino a $p_c$ (per esempio con quale legge di potenza diverge una certa grandezza) sono invece le stesse per intere classi di modelli molto diversi tra loro: questo fenomeno si chiama [**universalità**](https://it.wikipedia.org/wiki/Classe_di_universalit%C3%A0). Non lo studieremo in dettaglio in questo corso, ma è uno dei motivi per cui la percolazione, nonostante la sua semplicità, è un modello di riferimento nello studio delle transizioni di fase.

(sec:percolazione-algoritmo)=
# Come trovare i cluster al calcolatore

Per studiare la percolazione numericamente dobbiamo risolvere un problema molto concreto: data una configurazione di siti occupati e vuoti, identificare i cluster e calcolarne la taglia, cioè il numero di siti che contengono.

## Rappresentare il reticolo

Da un punto di vista logico-matematico, la rappresentazione più naturale di un reticolo bidimensionale è un array bidimensionale `int occupato[L][L]`, dove `L` è la taglia del reticolo e `occupato[i][j]` vale 1 se il sito $(i, j)$ è occupato, 0 altrimenti. Questa definizione richiede che il valore di `L` sia noto al momento della compilazione. D'altro canto, è molto più comodo poter passare il valore della taglia del reticolo da riga di comando. In questo caso, l'array bidimensionale deve venire allocato dinamicamente, ad esempio in questo modo:

```c
int L = atoi(argv[1]);

// allochiamo prima un array di puntatori
int **occupato = malloc(L * sizeof(int *));
int i;
// e poi i singoli array
for(i = 0; i < L; i++) {
    occupato[i] = malloc(L * sizeof(int));
}

... // qui utilizziamo l'array

// alla fine dobbiamo liberare la memoria richiesta
for(i = 0; i < L; i++) {
    free(occupato[i]);
}
free(occupato);
```

**L'unico** vantaggio di questa tecnica è che si accede all'elemento $(i, j)$ del reticolo con la sintassi `occupato[i][j]`. D'altra parte, gli svantaggi sono almeno due:

1. Allocare e liberare la memoria di array multidimensionali è macchinoso. Pensate ad esempio a come diventerebbe il codice precedente se volessimo simulare un reticolo tridimensionale...
2. A differenza di un vero array bidimensionale, le caselle di un array costruito dinamicamente a partire da una variabile `int **` non sono contigue in memoria. Questo ha un effetto pratico, perché la contiguità migliora le performance, specialmente quando si lavora con acceleratori hardware dedicati (ad esempio le GPU, utilizzatissime in ambito AI).

Una tecnica comune che non ha questi problemi è quella di utilizzare un array unidimensionale, indicizzandolo in maniera appropriata. In questo modo, un reticolo $L \times L$ viene rappresentato come un array di lunghezza $L^2$, e il sito $(i, j)$ corrisponde all'indice $i + j \cdot L$. Ad esempio, per generare una configurazione casuale in cui ogni sito è occupato con probabilità $p$ si può utilizzare il seguente codice:

```c
int *occupato = malloc(L * L * sizeof(int));
for(int j = 0; j < L; j++) {
    for(int i = 0; i < L; i++) {
        occupato[i + j * L] = (drand48() < p);
    }
}
```

## L'algoritmo: *flood fill*

Per identificare i cluster useremo un algoritmo di tipo *flood fill* ("riempimento per inondazione"), chiamato anche *burning algorithm* per la sua somiglianza con la propagazione di un incendio in una foresta. L'idea è di partire da un sito occupato e "incendiarlo", assegnandogli un'etichetta che identifica il cluster; ad ogni passo il fuoco si propaga ai primi vicini occupati non ancora bruciati, finché non ci sono più siti raggiungibili. A quel punto tutti i siti bruciati formano un cluster.

Per esplorare un singolo cluster serve tenere traccia dei siti già raggiunti ma i cui vicini non sono ancora stati esaminati. Li conserviamo in una lista (detta *coda*), e l'algoritmo procede così:

1. si parte da un sito occupato non ancora etichettato, gli si assegna l'etichetta del cluster e lo si inserisce nella coda;
2. si prende il primo sito della coda e se ne esaminano i primi vicini: ogni vicino occupato e non ancora etichettato riceve l'etichetta del cluster e viene aggiunto in fondo alla coda;
3. si ripete il punto 2 finché la coda non è esaurita; il numero di siti passati per la coda è la taglia del cluster.

Un sito viene etichettato nel momento in cui viene aggiunto alla coda, non quando se ne esaminano i vicini: in questo modo non può essere inserito più di una volta, anche se è primo vicino di più siti del cluster.

```{figure} figures/flood_fill.png
:name: fig:flood_fill
:align: center
:width: 400px

L'algoritmo *flood fill* che utilizzeremo per studiare la percolazione di sito. Cominciando a scorrere i siti dall'angolo in alto a sinistra, il primo sito occupato che si incontra è nella posizione $(0, 2)$ (passo 0, in giallo). Una volta incontrato un sito occupato si aggiungono i suoi primi vicini alla coda (passo 1 e 2), finché la coda non è esaurita (passo 3): il cluster è ora stato completamente identificato.
```

La [](#fig:flood_fill) mostra graficamente come avviene l'identificazione dei cluster. Per etichettare tutti i cluster del reticolo si scorrono i siti uno dopo l'altro, e ogni volta che se ne incontra uno occupato non ancora etichettato si avvia l'esplorazione di un nuovo cluster con una nuova etichetta. Ogni sito viene etichettato ed esaminato una sola volta, quindi il costo complessivo dell'algoritmo cresce linearmente con il numero di siti, $O(L^2)$: possiamo permetterci di ripeterlo molte volte, per molte configurazioni indipendenti, senza che diventi il collo di bottiglia della simulazione.

Resta da capire come realizzare la coda. Il numero di siti che vi passano è la taglia del cluster, che non possiamo conoscere prima di averlo esplorato: può trattarsi di un sito solo come di quasi tutto il reticolo. Ci serve quindi una struttura dati flessibile che possa crescere un elemento alla volta, senza doverne fissare la dimensione in anticipo.

## C: liste concatenate

Finora abbiamo sempre organizzato i nostri dati in array, uni o multidimensionali: sequenze di elementi dello stesso tipo, memorizzati in blocchi contigui di memoria, in cui l'accesso all'elemento $k$-esimo è immediato conoscendone l'indice. Gli array sono la scelta giusta quando conosciamo (o possiamo calcolare) in anticipo quanti elementi dovremo memorizzare, e quando ci interessa soprattutto un accesso rapido a un elemento qualsiasi conoscendone la posizione. Per i casi in cui questo schema non funziona, come il nostro, l'informatica ha sviluppato diverse strutture dati alternative, come liste concatenate, alberi, grafi, tabelle hash, per citarne solo alcune, ciascuna pensata per rendere efficienti operazioni diverse (inserimento, ricerca, rimozione, attraversamento in un certo ordine, $\ldots$)[^strutture_dati]. Qui ci concentriamo sulla più semplice, che è anche quella di cui abbiamo bisogno: la *lista concatenata*.

[^strutture_dati]: Il libro di testo del corso ne discute diverse in maggiore dettaglio.

Una lista concatenata (*linked list*) è una sequenza di elementi, chiamati *nodi*, in cui ciascun nodo contiene, oltre ai propri dati, un puntatore al nodo successivo. A differenza di un array, i nodi di una lista concatenata non occupano necessariamente posizioni contigue in memoria: è il puntatore contenuto in ciascun nodo, non la sua posizione, a determinare l'ordine della sequenza. Per questo aggiungere un nuovo elemento non richiede di sapere in anticipo quanti elementi ci saranno in totale.

In C, un nodo si definisce naturalmente come una `struct` che contiene un puntatore a una struttura dello stesso tipo (una struct *auto-referenziale*):

```c
typedef struct nodo {
    int valore;
    struct nodo *next;   /* puntatore al nodo successivo, o NULL */
} Nodo;
```

:::{note} Perché `struct nodo` e non `Nodo` dentro la struttura?
Nel momento in cui il compilatore legge il campo `next`, il tipo `Nodo` (introdotto dal `typedef` che si sta ancora definendo) non esiste ancora: bisogna usare il nome `struct nodo`, che invece è già disponibile perché è proprio quello che si sta definendo. Il nome breve `Nodo` diventa utilizzabile solo *dopo* il punto e virgola finale, quando il `typedef` è completo.
:::

Il valore `NULL` in un campo `next` di un nodo identifica quel nodo come l'ultimo della lista. Per manipolare una lista basta un puntatore alla sua testa (il primo nodo): il resto della lista si raggiunge seguendo i puntatori `next` uno dopo l'altro. Vediamo un piccolo programma completo che costruisce una lista di cinque interi, la stampa, e libera la memoria occupata:

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct nodo {
    int valore;
    struct nodo *next;
} Nodo;

int main(void) {
    Nodo *testa = NULL;

    /* costruiamo la lista inserendo ogni nuovo valore in testa */
    for(int v = 0; v < 5; v++) {
        Nodo *nuovo = malloc(sizeof(Nodo));
        if(nuovo == NULL) {
            fprintf(stderr, "Errore: memoria esaurita\n");
            exit(1);
        }
        nuovo->valore = v;
        nuovo->next = testa;   /* il nuovo nodo punta a quella che era la vecchia testa */
        testa = nuovo;         /* e diventa lui stesso la nuova testa */
    }

    /* percorriamo la lista, stampando i valori */
    for(Nodo *corrente = testa; corrente != NULL; corrente = corrente->next) {
        printf("%d\n", corrente->valore);
    }

    /* liberiamo la memoria, un nodo alla volta */
    Nodo *corrente = testa;
    while(corrente != NULL) {
        Nodo *prossimo = corrente->next;   /* salviamo il puntatore prima di liberare il nodo corrente */
        free(corrente);
        corrente = prossimo;
    }

    return 0;
}
```

Il programma stampa i numeri da 4 a 0, non da 0 a 4: inserendo ogni nuovo nodo in testa, l'ultimo elemento inserito è il primo che si incontra percorrendo la lista. Questo modo di costruire la lista si comporta quindi come una pila (chiamata anche *stack* o *LIFO*, cioè "*last in first out*"), ed è la maniera più semplice di inserire un elemento in una lista concatenata, perché richiede di conoscere solo la testa della lista.

:::{warning} Non dimenticate `free`
Ogni nodo allocato con `malloc` va liberato con `free` quando non serve più, altrimenti il programma perde memoria (*memory leak*) a ogni inserimento. Da notare, nel codice sopra, il salvataggio esplicito di `corrente->next` in `prossimo` **prima** di chiamare `free(corrente)`: una volta liberato un nodo non è più lecito leggerne i campi, quindi bisogna prima annotarsi dove si trova il nodo successivo.
:::

Il flood fill richiede invece una coda (una struttura *FIFO*, "*first in first out*"): i siti vanno esaminati nell'ordine in cui sono stati aggiunti, e i nuovi siti vanno aggiunti in fondo. Inserire in fondo a una lista richiede di conoscerne anche l'ultimo nodo: per questo, oltre al puntatore alla testa, terremo un secondo puntatore all'ultimo nodo, così che ogni inserimento abbia un costo indipendente dalla lunghezza della lista. Il vantaggio di questa scelta è che per scorrere la coda basta percorrere la lista dalla testa seguendo i puntatori `next`, esattamente come nel ciclo di stampa qui sopra: i siti aggiunti in fondo durante il percorso verranno raggiunti a loro volta, e l'esplorazione termina quando si arriva a un `next` uguale a `NULL`.

## Una struct per il reticolo: sito e nodo insieme

Nell'esempio precedente ogni nodo della lista viene allocato con una `malloc` dedicata, e liberato con una `free` quando non serve più. Applicando lo stesso schema al nostro problema finiremmo per allocare (e liberare) fino a $L^2$ nodi per ogni singola realizzazione del reticolo, da ripetere per le molte realizzazioni necessarie a fare delle medie.

Possiamo evitarlo del tutto. Ogni sito del reticolo entra nella coda al più una volta durante l'intera esplorazione: possiamo quindi usare la memoria già allocata per il sito stesso come nodo della lista, invece di allocarne uno a parte. Per farlo sostituiamo l'`int` che rappresentava ogni sito con una `struct` che contiene due interi (se il sito è occupato e l'etichetta del cluster di cui fa parte, che vale 0 finché il sito non è stato raggiunto) e il campo `next` necessario per la lista:

```c
typedef struct sito {
    int occupato;
    int cluster_id;
    struct sito *next;    /* usato solo mentre il sito è nella coda */
} Sito;
```

A questo punto allochiamo il reticolo come un unico array di `Sito`:

```c
Sito *reticolo = malloc(L * L * sizeof(Sito));
```

Il sito in posizione $(i,j)$ si trova, con la stessa indicizzazione usata sopra, in `reticolo[i + j * L]`. Questo array continua a essere un blocco contiguo di memoria (ogni elemento è semplicemente più grande di un singolo `int`), quindi manteniamo tutti i vantaggi discussi in precedenza. Per generare una configurazione si procede come prima, impostando `reticolo[idx].occupato = (drand48() < p)` e `reticolo[idx].cluster_id = 0` per ogni sito.

Con questa scelta, "aggiungere un sito alla coda" non significa più allocare un nuovo nodo, ma semplicemente far puntare il campo `next` dell'ultimo sito già in coda all'indirizzo del nuovo sito:

```c
void aggiungi_in_coda(Sito **coda, Sito *nuovo) {
    nuovo->next = NULL;
    (*coda)->next = nuovo;
    *coda = nuovo;
}
```

Non serve una funzione per "estrarre" un sito dalla coda: dato che non liberiamo mai i nodi, ci basta avanzare lungo la lista con `corrente = corrente->next`. L'intera esplorazione non richiede nessuna chiamata a `malloc` o `free`: tutta la memoria necessaria è stata allocata una volta sola, insieme al reticolo.

## L'implementazione

La funzione che esplora un singolo cluster a partire dal sito di indice `idx0`, assegnandogli l'etichetta `id` e restituendone la taglia, è la seguente:

```c
int esplora_cluster(Sito *reticolo, int L, int idx0, int id) {
    Sito *testa = &reticolo[idx0];
    Sito *coda = testa;
    int taglia = 0;

    testa->cluster_id = id;
    testa->next = NULL;

    for (Sito *corrente = testa; corrente != NULL; corrente = corrente->next) {
        taglia++;

        int idx = corrente - reticolo;   /* posizione del sito corrente nell'array */
        int i = idx % L;
        int j = idx / L;

        /* i quattro primi vicini: sinistra, destra, sopra, sotto */
        int di[4] = {-1, +1,  0,  0};
        int dj[4] = { 0,  0, -1, +1};

        for (int k = 0; k < 4; k++) {
            int vi = i + di[k];
            int vj = j + dj[k];

            /* scartiamo i vicini fuori dal reticolo */
            if (vi < 0 || vi >= L || vj < 0 || vj >= L) {
                continue;
            }

            Sito *vicino = &reticolo[vi + vj * L];

            if (vicino->occupato && vicino->cluster_id == 0) {
                vicino->cluster_id = id;
                aggiungi_in_coda(&coda, vicino);
            }
        }
    }

    return taglia;
}
```

Notate come `corrente - reticolo` (differenza tra due puntatori a elementi dello stesso array) restituisca direttamente la posizione del sito corrente nell'array: è un modo comodo per recuperare $(i,j)$ senza doverli memorizzare esplicitamente nel nodo, anche se avreste potuto scegliere di farlo, aggiungendo `i` e `j` come campi di `Sito`.

Il ciclo che etichetta tutti i cluster del reticolo è quindi:

```c
int id = 0;
int *taglia = malloc(L * L * sizeof(int));   /* nel caso peggiore ci sono L*L cluster */

for (int idx = 0; idx < L * L; idx++) {
    if (reticolo[idx].occupato && reticolo[idx].cluster_id == 0) {
        id++;
        taglia[id - 1] = esplora_cluster(reticolo, L, idx, id);
    }
}
int n_cluster = id;
```

## Identificare il cluster percolante

Per sapere se un cluster percola basta tenere traccia, durante l'esplorazione, di quali bordi del reticolo tocca ciascun cluster. Il modo più semplice è usare quattro array di booleani, `tocca_sinistra[id]`, `tocca_destra[id]`, `tocca_alto[id]` e `tocca_basso[id]`, aggiornati in `esplora_cluster` ogni volta che si incontra un sito con $i=0$, $i=L-1$, $j=0$ oppure $j=L-1$. Un cluster `id` percola se `tocca_sinistra[id] && tocca_destra[id]` oppure `tocca_alto[id] && tocca_basso[id]` è vero, e una volta trovati tutti i cluster basta scorrerne gli identificativi per stabilire se il reticolo percola e qual è la taglia del cluster percolante.

Su un reticolo finito possono esistere anche più cluster percolanti contemporaneamente (pensate a due righe interamente occupate separate da una riga vuota), anche se si tratta di un evento raro, e nel limite $L \to \infty$ il cluster percolante diventa unico. Per questo motivo conviene controllare tutti i cluster, e non fermarsi al primo percolante che si trova.

````{note} Approfondimento: l'algoritmo di Hoshen-Kopelman
:class: dropdown

L'algoritmo che abbiamo descritto esplora un cluster alla volta, completandone l'esplorazione prima di passare al successivo. Esiste un'alternativa, nota come **algoritmo di Hoshen-Kopelman**, che etichetta tutti i cluster in un solo passaggio sul reticolo (per esempio riga per riga), senza mai fermarsi a esplorare un singolo cluster fino in fondo. L'idea di base è assegnare via via nuove etichette ai siti occupati, e tenere una struttura ausiliaria (un cosiddetto *union-find*, o insieme di classi di equivalenza) per ricordare quando due etichette assegnate in momenti diversi risultano in realtà appartenere allo stesso cluster, perché due bracci dello stesso cluster si "incontrano" più avanti nella scansione.

Questo algoritmo è più efficiente di quello basato sul flood fill (in particolare usa meno memoria, perché non serve mai una coda grande quanto il cluster) ed è lo standard *de facto* nella letteratura sulla percolazione. La sua implementazione, però, richiede di gestire con cura le classi di equivalenza tra etichette, il che lo rende concettualmente meno immediato del flood fill. Se volete approfondire, implementarlo è un ottimo esercizio.
````

(sec:percolazione-misure)=
# Misure, medie e risultati

Una singola configurazione del reticolo, a un dato $p$, è solo una realizzazione tra le tante possibili: due sorteggi indipendenti con la stessa probabilità $p$ possono dare cluster completamente diversi, soprattutto vicino a $p_c$. Come per i cammini aleatori, le grandezze fisicamente rilevanti si ottengono **mediando su molte realizzazioni indipendenti** della configurazione, a $p$ e $L$ fissati.

## Distribuzione delle taglie dei cluster

Fissato $p$, possiamo costruire un istogramma delle taglie di tutti i cluster trovati, accumulando i risultati su molte realizzazioni indipendenti. Lontano da $p_c$ questa distribuzione decade rapidamente (circa esponenzialmente) al crescere della taglia $s$: i cluster grandi sono rari. Esattamente a $p = p_c$, invece, non esiste più una scala di taglia caratteristica, e la distribuzione dei cluster segue una **legge di potenza**,

$$
n_s(p_c) \sim s^{-\tau},
$$

dove $n_s$ è il numero (normalizzato) di cluster di taglia $s$, e $\tau$ è un esponente numerico[^tau]. Su un grafico in scala log-log, una legge di potenza appare come una retta: è questo il modo più semplice per riconoscerla numericamente.

[^tau]: Il valore di $\tau$ è uno degli esponenti critici della percolazione, e per la percolazione di sito su reticolo quadrato 2D vale $\tau = 187/91 \simeq 2.05$. Qui ci basterà osservare qualitativamente l'andamento a potenza.

```{figure} #cell:distribuzione_taglie_cluster
:label: fig:distribuzione_taglie_cluster
:align: center

Distribuzione delle taglie dei cluster $n_s(p)$, in scala log-log, per tre valori di $p$: lontano da $p_c$ da entrambi i lati la distribuzione decade rapidamente, mentre a $p=p_c$ l'andamento è approssimativamente una retta, segno di una legge di potenza.
```

## Effetti di taglia finita e stima di $p_c$

La discontinuità di $P_{\rm perc}(p,L)$ in $p_c$ si ha solo per $L\to\infty$. Su un reticolo finito, $P_{\rm perc}(p, L)$ è sempre una funzione continua di $p$, che cresce da 0 a 1 in un intervallo attorno a $p_c$ tanto più stretto quanto più $L$ è grande.

Questo suggerisce un modo semplice per stimare numericamente $p_c$ senza dover mandare $L$ all'infinito: calcoliamo $P_{\rm perc}(p, L)$ mediando su molte realizzazioni, per diversi valori di $L$, e rappresentiamo le curve ottenute sullo stesso grafico in funzione di $p$. Al crescere di $L$ le curve diventano sempre più ripide attorno a un punto comune, che si avvicina progressivamente al vero $p_c$: il punto in cui le curve relative a $L$ diversi si incrociano (approssimativamente) è quindi una stima di $p_c$, tipicamente già ragionevole con reticoli di taglia moderata. La convergenza è tanto più lenta quanto più contano gli effetti di bordo: con i bordi aperti che abbiamo scelto, i siti vicino al contorno hanno meno vicini degli altri, e questo introduce correzioni di taglia finita che vanno tenute in conto se si vuole stimare $p_c$ con grande precisione.

```{figure} #cell:probabilita_percolazione
:label: fig:probabilita_percolazione
:align: center

Probabilità di trovare un cluster percolante, $P_{\rm perc}(p, L)$, in funzione di $p$, per diversi valori di $L$. All'aumentare di $L$ la transizione diventa più ripida, e le curve si incrociano approssimativamente in un unico punto, che fornisce una stima di $p_c$.
```

## Suscettibilità

Vicino a $p_c$ ci interessa anche quantificare quanto sia grande, tipicamente, il cluster "più importante" del sistema, escluso quello percolante (se esiste). Definiamo quindi, per ogni realizzazione, la taglia del cluster più grande non percolante, e mediamo questa quantità su molte realizzazioni indipendenti a $p$ e $L$ fissati: chiamiamo questa grandezza **suscettibilità**, $\chi(p, L)$.

Lontano da $p_c$, da entrambi i lati, $\chi$ è piccola: sotto $p_c$ perché nessun cluster è ancora molto grande, sopra $p_c$ perché quasi tutti i siti occupati appartengono ormai al cluster percolante, e ne restano pochi per formare cluster secondari di taglia rilevante. Vicino a $p_c$, invece, $\chi$ presenta un massimo pronunciato, tanto più alto quanto più $L$ è grande: è il segnale numerico più diretto della transizione.

```{figure} #cell:suscettibilita_percolazione
:label: fig:suscettibilita_percolazione
:align: center

Suscettibilità $\chi(p, L)$ (taglia media del cluster più grande, escluso quello percolante) in funzione di $p$, per diversi valori di $L$. Il picco, localizzato vicino a $p_c$, cresce con $L$.
```

```{note} Un parallelo con la termodinamica
Nel corso di termodinamica incontrerete la compressibilità di un fluido, che misura la risposta del volume a una variazione di pressione e che, per un sistema infinito, diverge nel punto critico della transizione liquido-vapore. La suscettibilità della percolazione gioca un ruolo del tutto analogo: anch'essa diverge (per $L \to \infty$) nel punto critico, segnalando che il sistema si organizza su tutte le scale di lunghezza. Non è un caso che si usi lo stesso linguaggio in contesti fisici tanto diversi: è proprio l'[universalità](#sec:percolazione-reticolo) delle transizioni di fase a rendere sensato questo confronto.
```

# Esperimenti numerici

Un'analisi numerica della percolazione può seguire l'organizzazione di questo capitolo:

1. implementare la generazione del reticolo e l'algoritmo di identificazione dei cluster tramite flood fill con lista concatenata; verificare il funzionamento su reticoli piccoli, per esempio stampando o colorando i cluster trovati e controllando a mano il risultato;
2. calcolare, mediando su molte realizzazioni indipendenti, la probabilità di percolazione $P_{\rm perc}(p, L)$ per almeno tre o quattro valori di $L$, e stimare $p_c$ dal punto in cui le curve si incrociano approssimativamente;
3. a un valore di $p$ vicino al $p_c$ stimato, costruire l'istogramma delle taglie dei cluster in scala log-log, e verificare qualitativamente l'andamento a legge di potenza;
4. calcolare la suscettibilità $\chi(p, L)$ in funzione di $p$, per gli stessi valori di $L$ usati al punto 2, e verificare che il picco cresce al crescere di $L$;
5. facoltativo: confrontare la stima di $p_c$ ottenuta al punto 2 con il valore noto in letteratura, $p_c \simeq 0.592746$.
