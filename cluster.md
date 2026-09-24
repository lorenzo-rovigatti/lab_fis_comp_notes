---
title: Percolazione
exports:
   - format: pdf
---

(sec:percolazione)=
# Introduzione

Immaginate una popolazione di animali selvatici che vive in un territorio via via più frammentato dall'urbanizzazione: strade, campi coltivati, quartieri residenziali spezzano l'ambiente naturale in tante piccole isole verdi. Un singolo animale può muoversi liberamente all'interno di un'isola, ma per raggiungerne un'altra ha bisogno che le due siano connesse da un corridoio di habitat idoneo. Finché le isole verdi occupano una piccola frazione del territorio è molto probabile che siano tutte isolate tra loro, con il risultati che gli animali resteranno confinati in un'area piccola rispetto all'estensione totale del territorio. Se però la frazione di territorio "verde" aumenta, a un certo punto si formeranno dei corridoi che collegheranno le isole tra loro, fino a creare un singolo agglomerato che attraverserà l'intero territorio da un capo all'altro. Da quel momento un animale potrà, in linea di principio, spostarsi da un estremo all'altro del territorio senza mai lasciare l'habitat idoneo.

Questo cambiamento non avviene gradualmente: al crescere della frazione di territorio disponibile, la probabilità che esista un simile corridoio "da un capo all'altro" passa, in modo piuttosto brusco, da (quasi) nulla a (quasi) certa. Il valore critico della frazione di territorio al quale avviene questo cambiamento è l'analogo di ciò che chiameremo tra poco **soglia di percolazione**.

Lo stesso tipo di fenomeno compare in contesti molto diversi tra loro:

* un incendio boschivo si propaga da un albero a quelli vicini; se la densità di alberi è troppo bassa, il fuoco si spegne dopo aver bruciato pochi alberi isolati, mentre sopra una certa densità critica può attraversare l'intera foresta[^incendio];
* le reti (elettriche, idriche, o di altra natura) funzionano fintantoché esistono abbastanza connessioni funzionanti che permettono di collegare i nodi principali della rete; se la "densità di guasti" è troppo alta la rete smette di funzionare;
* in un materiale composito fatto di grani conduttori dispersi in una matrice isolante, la corrente può attraversare il materiale solo se i grani conduttori formano un cammino continuo da un elettrodo all'altro: sotto una certa concentrazione di grani il materiale è isolante, sopra diventa conduttore;
* la diffusione di un'epidemia in una popolazione richiede che esista una catena continua di contatti tra individui suscettibili: se i contatti sono troppo radi, l'epidemia si esaurisce localmente; se sono abbastanza densi, può raggiungere una frazione estesa della popolazione.

[^incendio]: Questa analogia non è casuale: nella [sezione 3](#sec:percolazione-algoritmo) vedremo che il nome dell'algoritmo più semplice per identificare le "isole" (che chiameremo *cluster*) al calcolatore è proprio *burning algorithm*.

In tutti questi esempi la domanda fisica è la stessa: **esiste un cammino continuo che attraversa l'intero sistema?** E la risposta cambia bruscamente al variare di un solo parametro, la densità (di habitat, di alberi, di connessioni, di contatti). Questo tipo di cambiamento improvviso di comportamento macroscopico al variare con continuità di un parametro microscopico è la firma di una **transizione di fase**, un concetto che incontrerete a breve (o forse avete già incontrato) anche nel corso di termodinamica, sebbene in un contesto molto diverso (per esempio la transizione liquido-vapore). La percolazione è probabilmente il modello più semplice in cui si possa studiare una transizione di fase al calcolatore, con pochissimi ingredienti fisici: per questo è un ottimo punto di partenza.

```{note} Da dove viene il nome
Il termine "percolazione" nasce in un contesto ancora più quotidiano: descrive il filtraggio di un fluido attraverso un mezzo poroso, come l'acqua calda che attraversa la polvere di caffè in una moka, o il petrolio che si muove attraverso la roccia porosa di un giacimento. Il fluido riesce ad attraversare il mezzo solo se esiste un cammino continuo di pori collegati tra loro: la stessa domanda che ci porremo qui, applicata a un caso concreto.
```

(sec:percolazione-reticolo)=
# Percolazione su un reticolo

## Il modello

Per studiare la percolazione al calcolatore ne costruiamo una versione semplificata e discreta. Consideriamo un reticolo quadrato $L \times L$, i cui siti sono identificati da una coppia di indici interi $(i,j)$, con $i,j = 0, \dots, L-1$. A differenza di quanto avete visto per il gas reticolare, qui non useremo condizioni al bordo periodiche: il reticolo ha bordi veri e propri, oltre i quali non esistono altri siti[^pbc].

[^pbc]: Le condizioni periodiche renderebbero il reticolo omogeneo (nessun sito sarebbe "speciale"), il che semplificherebbe alcuni aspetti teorici. Al prezzo di qualche effetto di bordo in più (i siti vicini al contorno hanno meno vicini degli altri), le condizioni al bordo finite hanno il vantaggio di essere più semplici da implementare e più vicine all'idea intuitiva di un territorio, o di un campione di materiale, con un'estensione limitata.

Ogni sito del reticolo viene dichiarato **occupato** con probabilità $p$, e **vuoto** con probabilità $1-p$, indipendentemente da tutti gli altri siti. Questa è la cosiddetta **percolazione di sito** (*site percolation*). Due siti occupati sono considerati **connessi** se sono primi vicini sul reticolo, cioè se differiscono di un'unità in una sola delle due coordinate:

$$
(i,j) \sim (i', j') \iff |i-i'|+|j-j'| = 1.
$$

Un **cluster** è un insieme massimale di siti occupati, tutti raggiungibili l'uno dall'altro attraverso una catena di connessioni tra primi vicini. In altre parole, due siti occupati appartengono allo stesso cluster se e solo se esiste un cammino di siti occupati, ciascuno primo vicino del successivo, che li collega.

```{note} Percolazione di legame
Un modello strettamente imparentato è la **percolazione di legame** (*bond percolation*): qui tutti i siti sono presenti, ma è ciascun *legame* tra due primi vicini a essere presente con probabilità $p$ e assente con probabilità $1-p$, indipendentemente dagli altri legami. Due siti appartengono allo stesso cluster se sono collegati da una catena di legami presenti. Concettualmente il modello è identico a quello di sito: cambia solo l'oggetto (sito o legame) sottoposto al sorteggio casuale. Nel seguito lavoreremo sempre con la percolazione di sito, ma tenete a mente che esiste anche questa variante, perché ricompare spesso in letteratura.
```

Diciamo che un cluster **percola** (o è *percolante*) se contiene almeno un sito nella prima riga ($i=0$) e almeno un sito nell'ultima riga ($i=L-1$), collegati tra loro all'interno dello stesso cluster: in questo caso esiste un cammino continuo di siti occupati che attraversa il reticolo da un capo all'altro nella direzione verticale[^direzione].

[^direzione]: Avremmo potuto scegliere ugualmente di richiedere l'attraversamento orizzontale, da sinistra a destra: per un reticolo quadrato le due scelte sono equivalenti per simmetria. Non lo sono più se il reticolo non è quadrato, o se si usano condizioni al bordo diverse nelle due direzioni.

```{figure} figures/percolazione.png
:name: fig:percolazione
:align: center
:width: 600px

Due configurazioni di un reticolo 8x8 con 16 siti occupati ($p = 0.25$) generate casualmente. In (a) i siti formano cinque cluster (colorati diversamente), mentre (b) contiene tre cluster, di cui uno percolante (in rosso).
```

La [](#fig:percolazione) mostra due configurazioni di esempio generate casualmente con lo stesso $p$. Siti colorati nello stesso modo fanno parte dello stesso cluster. Il pannello (b) mostra un esempio di cluster percolante: partendo dal bordo di sinistra e percorrendo i legami tra i siti occupati si può arrivare al bordo di destra.

## La soglia di percolazione

Per $p$ molto piccolo, quasi tutti i siti occupati sono isolati o formano piccoli cluster: è estremamente improbabile che esista un cluster percolante. Le due configurazioni rappresentate in [](#fig:percolazione) sono vicine a questo limite: se generassimo molte altre conformazioni, la gran parte conterrebbe tanti piccoli cluster, e solo pochissime percolerebbero. D'altro canto, per $p$ vicino a 1, quasi tutti i siti sono occupati, ed è quasi certo che esista un cluster percolante (nel caso estremo $p=1$ tutto il reticolo è un unico cluster). Ci aspettiamo quindi che la probabilità di osservare un cluster percolante, che indichiamo con $P_{\rm perc}(p, L)$, sia una funzione crescente di $p$, che passa da valori vicini a 0 a valori vicini a 1.

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
2. A differenza di un vero array bidimensionale, le caselle di un array costruito dinamicamente a partire da una variabile `int **` non sono contigue in memoria. Questo ha un effetto pratico, perché la contiguità migliora le performance, specialmente quando si lavora con acceleratori hardware dedicati (ad esempio le GPU, che sono acceleratori *hardware* utilizzatissimi, ad esempio in ambito AI).

Per ovviare a questi problemi una tecnica comune è quella di utilizzare un array unidimensionale, indicizzandolo in maniera appropriato. In questo modo, un reticolo $L \times L$ viene rappresentato come un array di lunghezza $L^2$, e il sito $(i, j)$ corrisponde all'indice $i + j \cdot L$. Ad esempio, per inizializzare casualmente una configurazione contenente una frazione $p$ di siti occupati si può utilizzare il seguente codice:

```c
// definizioni di p, L, occupati, ecc e inizializzazione di tutti gli elementi dell'array a 0
int N_occ_target = L * L * p;
int N_occ = 0;
while(N_occ < N_occ_target) {
    int i = drand48() * L;
    int j = drand48() * L;
    if(occupati[i + j * L] == 0) {
        occupati[j + j * L] = 1;
        N_occ++;
    }
}
```

## C: liste concatenate

Per identificare un cluster useremo un algoritmo di tipo *flood fill*: partiamo da un sito occupato, lo marchiamo come appartenente al cluster, ed esploriamo i suoi vicini occupati non ancora marcati, ripetendo il procedimento finché non ci sono più vicini da esplorare. Ad ogni passo, i siti "scoperti" ma non ancora esplorati formano una **frontiera** la cui dimensione non è nota in anticipo: può contenere pochi siti o quasi tutto il cluster, a seconda della forma e della taglia del cluster stesso. Non potendo prevedere quanto grande sarà questa frontiera, un array di dimensione fissa non è la struttura dati adatta a contenerla: ci serve qualcosa che possa crescere e ridursi dinamicamente, un sito alla volta. Questo è esattamente il problema che risolvono le **liste concatenate**.

Una lista concatenata è una sequenza di elementi (*nodi*), ciascuno dei quali contiene, oltre ai propri dati, un puntatore al nodo successivo. In C un nodo si definisce come una `struct` che contiene un puntatore a una struttura dello stesso tipo:

```c
typedef struct nodo {
    int i, j;             /* coordinate del sito */
    struct nodo *next;    /* puntatore al nodo successivo, o NULL */
} Nodo;
```

Il valore `NULL` nel campo `next` dell'ultimo nodo segnala che la lista termina lì. Useremo la lista concatenata come **coda** (una struttura *FIFO*, *first in first out*): i nuovi siti scoperti vengono aggiunti in fondo, e i siti da esplorare vengono estratti dall'inizio. Per fare questo in modo efficiente conviene tenere due puntatori, uno alla testa e uno alla coda della lista:

```c
/* Aggiunge il sito (i, j) in fondo alla lista */
void aggiungi_in_coda(Nodo **testa, Nodo **coda, int i, int j) {
    Nodo *nuovo = malloc(sizeof(Nodo));
    if (nuovo == NULL) {
        fprintf(stderr, "Errore: memoria esaurita\n");
        exit(1);
    }
    nuovo->i = i;
    nuovo->j = j;
    nuovo->next = NULL;

    if (*testa == NULL) {
        /* la lista era vuota: il nuovo nodo è sia testa che coda */
        *testa = nuovo;
        *coda = nuovo;
    } else {
        (*coda)->next = nuovo;
        *coda = nuovo;
    }
}

/* Estrae il sito in testa alla lista in (i, j), liberandone la memoria.
   Restituisce 0 se la lista era vuota, 1 altrimenti. */
int estrai_dalla_testa(Nodo **testa, int *i, int *j) {
    if (*testa == NULL) {
        return 0;
    }

    Nodo *primo = *testa;
    *i = primo->i;
    *j = primo->j;
    *testa = primo->next;
    free(primo);

    return 1;
}
```

:::{warning} Non dimenticate `free`
Ogni nodo allocato con `malloc` deve essere liberato con `free` quando non serve più, altrimenti il programma perde memoria (*memory leak*) a ogni chiamata. `estrai_dalla_testa` libera il nodo che estrae: se al termine dell'algoritmo la lista è sempre vuota, tutta la memoria allocata è stata correttamente liberata. Se interrompete l'esplorazione prima che la lista si svuoti (per esempio per un errore), ricordatevi di liberare esplicitamente i nodi rimasti.
:::

Usare due puntatori (testa e coda) invece di uno solo evita di dover scorrere l'intera lista ogni volta che si aggiunge un elemento: l'inserimento in coda costa un tempo costante, indipendente dalla lunghezza della lista, esattamente come l'estrazione dalla testa.

## L'algoritmo: flood fill con una coda

Oltre alla matrice `occupato`, teniamo una seconda matrice di interi, `cluster_id[L][L]`, inizializzata a 0, che useremo per etichettare ciascun sito occupato con il numero del cluster a cui appartiene (0 significa "non ancora esplorato", oppure sito vuoto). L'algoritmo per esplorare un singolo cluster a partire da un sito $(i_0, j_0)$ è il seguente:

```c
int esplora_cluster(int **occupato, int **cluster_id, int L,
                     int i0, int j0, int id) {
    Nodo *testa = NULL, *coda = NULL;
    int taglia = 0;

    cluster_id[i0][j0] = id;
    aggiungi_in_coda(&testa, &coda, i0, j0);

    int i, j;
    while (estrai_dalla_testa(&testa, &i, &j)) {
        taglia++;

        /* i quattro primi vicini: su, giù, sinistra, destra */
        int di[4] = {-1, +1,  0,  0};
        int dj[4] = { 0,  0, -1, +1};

        for (int k = 0; k < 4; k++) {
            int vi = i + di[k];
            int vj = j + dj[k];

            /* scartiamo i vicini fuori dal reticolo: non ci sono
               condizioni al bordo periodiche */
            if (vi < 0 || vi >= L || vj < 0 || vj >= L) {
                continue;
            }

            if (occupato[vi][vj] && cluster_id[vi][vj] == 0) {
                cluster_id[vi][vj] = id;
                aggiungi_in_coda(&testa, &coda, vi, vj);
            }
        }
    }

    return taglia;
}
```

Notate che un sito viene marcato con `cluster_id[vi][vj] = id` nello stesso momento in cui viene aggiunto alla coda, non quando viene estratto: questo evita di inserirlo più volte nella coda, nel caso in cui sia primo vicino di più di un sito già esplorato.

Per etichettare tutti i cluster del reticolo basta scorrere i siti una sola volta, e ogni volta che si incontra un sito occupato non ancora etichettato avviare l'esplorazione con un nuovo identificativo:

```c
int id = 0;
int taglia[L*L];   /* nel caso peggiore ci sono al più L*L cluster */

for (int i = 0; i < L; i++) {
    for (int j = 0; j < L; j++) {
        if (occupato[i][j] && cluster_id[i][j] == 0) {
            id++;
            taglia[id - 1] = esplora_cluster(occupato, cluster_id, L, i, j, id);
        }
    }
}
int n_cluster = id;
```

Ogni sito viene visitato ed etichettato una sola volta, quindi il costo complessivo dell'algoritmo cresce linearmente con il numero di siti del reticolo, $O(L^2)$: possiamo permetterci di ripeterlo molte volte, per molte configurazioni indipendenti, senza che diventi il collo di bottiglia della simulazione.

## Identificare il cluster percolante

Per sapere se un cluster percola basta tenere traccia, durante l'esplorazione, di quali cluster toccano la riga $i=0$ e quali toccano la riga $i=L-1$. Il modo più semplice è usare due array di booleani, `tocca_alto[id]` e `tocca_basso[id]`, aggiornati ogni volta che, durante l'esplorazione di un cluster, si incontra un sito con $i=0$ oppure $i=L-1$. Un cluster `id` percola se e solo se `tocca_alto[id] && tocca_basso[id]` sono entrambi veri. Su un dato reticolo può esistere al più un cluster percolante alla volta[^unicita], quindi è sufficiente scorrere gli identificativi dei cluster una volta trovati tutti per stabilire se il reticolo percola, e in caso affermativo qual è la taglia del cluster percolante.

[^unicita]: Due cluster diversi sono per definizione insiemi di siti non connessi tra loro: se entrambi attraversassero il reticolo da cima a fondo, dovrebbero incrociarsi da qualche parte, il che significherebbe che sono in realtà lo stesso cluster.

````{note} Approfondimento: l'algoritmo di Hoshen-Kopelman
:class: dropdown

L'algoritmo che abbiamo descritto esplora un cluster alla volta, arrestandosi completamente prima di passare al successivo. Esiste un'alternativa, nota come **algoritmo di Hoshen-Kopelman**, che etichetta tutti i cluster in un solo passaggio sul reticolo (per esempio riga per riga), senza mai fermarsi a esplorare un singolo cluster fino in fondo. L'idea di base è assegnare via via nuove etichette ai siti occupati, e tenere una struttura ausiliaria (un cosiddetto *union-find*, o insieme di classi di equivalenza) per ricordare quando due etichette assegnate in momenti diversi risultano in realtà appartenere allo stesso cluster, perché due bracci dello stesso cluster si "incontrano" più avanti nella scansione.

Questo algoritmo è più efficiente di quello basato sul flood fill (in particolare usa meno memoria, perché non serve mai una coda grande quanto il cluster) ed è lo standard *de facto* nella letteratura sulla percolazione. La sua implementazione, però, richiede di gestire con cura le classi di equivalenza tra etichette, il che lo rende concettualmente meno immediato del flood fill. Se volete approfondire, è un ottimo esercizio successivo a questo capitolo.
````

(sec:percolazione-misure)=
# Misure, medie e risultati

Una singola configurazione del reticolo, a un dato $p$, è solo una realizzazione tra le tante possibili: due sorteggi indipendenti degli stessi siti con la stessa probabilità $p$ possono dare cluster completamente diversi, soprattutto vicino a $p_c$. Come per i cammini aleatori, le grandezze fisicamente rilevanti si ottengono **mediando su molte realizzazioni indipendenti** della configurazione, a $p$ e $L$ fissati.

## Distribuzione delle taglie dei cluster

Fissato $p$, possiamo costruire un istogramma delle taglie di tutti i cluster trovati, accumulando i risultati su molte realizzazioni indipendenti. Lontano da $p_c$ questa distribuzione decade rapidamente (circa esponenzialmente) al crescere della taglia $s$: i cluster grandi sono rari. Esattamente a $p = p_c$, invece, non esiste più una scala di taglia caratteristica, e la distribuzione dei cluster segue una **legge di potenza**,

$$
n_s(p_c) \sim s^{-\tau},
$$

dove $n_s$ è il numero (normalizzato) di cluster di taglia $s$, e $\tau$ è un esponente numerico[^tau]. Su un grafico in scala log-log, una legge di potenza appare come una retta: è questo il modo più semplice per riconoscerla numericamente.

[^tau]: Il valore di $\tau$ è uno degli esponenti critici della percolazione, e per la percolazione di sito su reticolo quadrato 2D vale $\tau \simeq 187/91 \simeq 2.05$. Non useremo questo valore nel seguito: ci basta osservare qualitativamente l'andamento a potenza.

```{figure} #cell:distribuzione_taglie_cluster
:label: fig:distribuzione_taglie_cluster
:align: center

Distribuzione delle taglie dei cluster $n_s(p)$, in scala log-log, per tre valori di $p$: lontano da $p_c$ da entrambi i lati la distribuzione decade rapidamente, mentre a $p=p_c$ l'andamento è approssimativamente una retta, segno di una legge di potenza.
```

## Effetti di taglia finita e stima di $p_c$

Il limite $p\to p_c^{\pm}$ che rende discontinua $P_{\rm perc}(p,L)$ vale solo per $L\to\infty$. Su un reticolo finito, $P_{\rm perc}(p, L)$ è sempre una funzione continua di $p$, che cresce in modo più o meno brusco da 0 a 1 in un intervallo attorno a $p_c$ tanto più stretto quanto più $L$ è grande.

Questo suggerisce un modo semplice per stimare numericamente $p_c$ senza dover mandare $L$ all'infinito: calcoliamo $P_{\rm perc}(p, L)$ mediando su molte realizzazioni, per diversi valori di $L$, e rappresentiamo le curve ottenute sullo stesso grafico in funzione di $p$. Al crescere di $L$ le curve diventano sempre più ripide attorno a un punto comune, che si avvicina progressivamente al vero $p_c$: il punto in cui le curve relative a $L$ diversi si incrociano (approssimativamente) è quindi una stima di $p_c$, tipicamente già ragionevole con reticoli di taglia moderata.

```{figure} #cell:probabilita_percolazione
:label: fig:probabilita_percolazione
:align: center

Probabilità di trovare un cluster percolante, $P_{\rm perc}(p, L)$, in funzione di $p$, per diversi valori di $L$. All'aumentare di $L$ la transizione diventa più ripida, e le curve si incrociano approssimativamente in un unico punto, che fornisce una stima di $p_c$.
```

```{warning} Le condizioni al bordo finite complicano un po' le cose
Avendo scelto condizioni al bordo finite invece che periodiche, i siti vicino al contorno hanno meno vicini degli altri, e questo introduce correzioni di taglia finita relativamente più marcate rispetto al caso periodico. Il metodo dell'incrocio delle curve resta valido, ma il punto di incrocio converge più lentamente al vero $p_c$ al crescere di $L$ rispetto a quanto accadrebbe con condizioni periodiche. Per una stima grossolana di $p_c$, come quella che faremo qui, questo non è un problema; lo diventerebbe se voleste stimare $p_c$ con grande precisione.
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
Se avete già incontrato, o incontrerete a breve nel corso di termodinamica, il concetto di compressibilità di un fluido vicino a una transizione di fase, la suscettibilità della percolazione gioca un ruolo del tutto analogo. In entrambi i casi si tratta della risposta di una grandezza estensiva (il volume, la taglia del cluster più grande) a una variazione del parametro di controllo (la pressione, la probabilità di occupazione $p$), e in entrambi i casi questa risposta diverge (per un sistema infinito) proprio nel punto critico. Non è un caso che si usi lo stesso tipo di linguaggio in contesti fisici tanto diversi: è proprio l'universalità delle transizioni di fase, menzionata nella [sezione 2](#sec:percolazione-reticolo), a rendere sensato questo confronto.
```

# Esperimenti numerici

Un'analisi numerica della percolazione può seguire l'organizzazione di questo capitolo:

1. implementare la generazione del reticolo e l'algoritmo di identificazione dei cluster tramite flood fill con lista concatenata; verificare il funzionamento su reticoli piccoli, per esempio stampando o colorando i cluster trovati e controllando a mano il risultato;
2. calcolare, mediando su molte realizzazioni indipendenti, la probabilità di percolazione $P_{\rm perc}(p, L)$ per almeno tre o quattro valori di $L$, e stimare $p_c$ dal punto in cui le curve si incrociano approssimativamente;
3. a un valore di $p$ vicino al $p_c$ stimato, costruire l'istogramma delle taglie dei cluster in scala log-log, e verificare qualitativamente l'andamento a legge di potenza;
4. calcolare la suscettibilità $\chi(p, L)$ (taglia media del cluster più grande, escluso quello percolante) in funzione di $p$, per gli stessi valori di $L$ usati al punto 2, e verificare che il picco cresce al crescere di $L$;
5. facoltativo: confrontare la stima di $p_c$ ottenuta al punto 2 con il valore noto in letteratura, $p_c \simeq 0.592746$, per la percolazione di sito sul reticolo quadrato.
