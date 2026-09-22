---
title: Cammini aleatori
exports:
   - format: pdf
---

(sec:random-walk)=
# Introduzione

All'inizio del XIX secolo Robert Brown osservò al microscopio il moto irregolare di piccole particelle sospese in un fluido[^particelle]. Le particelle continuavano a cambiare direzione in modo apparentemente imprevedibile, anche in assenza di correnti macroscopiche visibili.

[^particelle]: Brown era un botanico, e le prime osservazioni furono fatte utilizzando grani di polline.

Oggi interpretiamo questo **moto browniano** come il risultato degli urti continui tra la particella sospesa e le molecole del fluido. A ogni istante la particella riceve un numero enorme di impulsi microscopici provenienti da direzioni diverse. In media questi impulsi si compensano, ma le compensazioni non sono mai esatte: rimane una forza risultante fluttuante, che cambia rapidamente intensità e direzione.

Seguire nel dettaglio tutti questi urti sarebbe non solo estremamente complicato, ma anche poco utile. Le scale microscopiche associate al moto delle molecole del fluido sono infatti molto più piccole delle scale spaziali e temporali sulle quali osserviamo la particella sospesa.

Possiamo quindi distinguere almeno due scale temporali:

- una scala microscopica, associata agli urti con le molecole del fluido;
- una scala macroscopica, associata all'evoluzione osservabile della posizione della particella.

Scegliendo un intervallo temporale $\Delta t$ molto più grande della durata e della separazione tipica dei singoli urti, non cerchiamo di descrivere ciò che accade durante ogni collisione. Descriviamo invece soltanto lo spostamento netto accumulato dalla particella durante ciascun intervallo
$\Delta t$.

Il modello che costruiamo è quindi una descrizione **coarse-grained** (cioè *a grana grossa*): sostituiamo la complicata dinamica microscopica con una successione di spostamenti casuali che tengono conto dell'effetto *medio* degli urti. Infatti, se volessimo (e potessimo) risolvere il moto alle scale microscopiche, vedremmo che a tempi molto brevi la particella reale possiede una velocità ben definita e il moto è approssimativamente balistico. La descrizione browniana emerge quando osserviamo il sistema su intervalli temporali molto più lunghi del tempo caratteristico degli urti microscopici. A queste scale di tempo intermedie, la traiettoria efficace del moto browniano risulta continua ma non differenziabile: ingrandendone un tratto continuano ad apparire nuove irregolarità. Quindi, poiché la traiettoria non è differenziabile, non è possibile associare alla traiettoria una velocità istantanea ordinaria. Il random walk discreto evita inizialmente questo problema, descrivendo il moto mediante spostamenti definiti su intervalli temporali finiti.

Una singola traiettoria browniana è estremamente irregolare. La posizione della particella dopo un certo tempo non può essere prevista conoscendo
soltanto la sua posizione iniziale: traiettorie preparate nelle stesse condizioni macroscopiche producono evoluzioni microscopiche diverse.

L'obiettivo non è quindi prevedere esattamente una particolare traiettoria, ma descrivere le proprietà statistiche di un insieme di possibili
traiettorie. Possiamo domandarci, per esempio,

- qual è lo spostamento medio;
- quanto si disperdono le posizioni rispetto alla media;
- come cresce nel tempo la distanza tipica dalla posizione iniziale;
- qual è la probabilità di trovare la particella in una certa regione di spazio ad un certo tempo.

Queste quantità possono essere definite considerando molte realizzazioni indipendenti dello stesso esperimento in cui poniamo una particella in $x_0$ al tempo 0. Se $x_n^{(\alpha)}$ è la posizione dopo $n$ passi nella traiettoria $\alpha$, allora la distanza media percorsa dalla particella al tempo $t_n$ è

$$
\langle x_n-x_0\rangle
\simeq
\frac{1}{N_{\mathrm{traj}}}
\sum_{\alpha=1}^{N_{\mathrm{traj}}}
\left(x_n^{(\alpha)}-x_0^{(\alpha)}\right),
$$

dove la media è effettuata su molte traiettorie osservate tutte dopo lo stesso numero di passi.

In alternativa, in molti sistemi è possibile ottenere informazioni statistiche anche osservando una singola traiettoria per un tempo molto
lungo. In questo caso si confrontano spostamenti che partono da istanti diversi della stessa traiettoria. Ad esempio, dividiamo l'intera traiettoria in $M$ segmenti di lunghezza $n$. In questo caso, la distanza media percorsa dalla particella è

$$
\langle x_n-x_0\rangle
\simeq
\frac{1}{M}
\sum_{m=0}^{M - 1}
\left(x_{mn + n}-x_{mn}\right),
$$

Notiamo subito come in entrambi i casi (medie calcolate su più traiettorie e medie calcolate sulla stessa traiettoria), ciò che conta è lo spostamento rispetto a una posizione iniziale. Per questo motivo conviene portarsi esplicitamente dietro la posizione iniziale $x_0$ e studiare la quantità

$$
X_n\equiv x_n-x_0.
$$

Nel caso di una media di insieme, $x_0$ è la posizione dalla quale vengono preparate le diverse realizzazioni. Nel caso di una media lungo una
traiettoria, il ruolo di $x_0$ può essere assunto di volta in volta dalla posizione all'inizio di ciascun intervallo osservato.

Studiare $x_n-x_0$, anziché direttamente $x_n$, permette inoltre di separare le proprietà del moto dalla scelta arbitraria dell'origine delle coordinate. Infatti, per un sistema omogeneo le statistiche degli spostamenti non devono dipendere dal punto dello spazio dal quale la particella è partita.

(sec:random-walk-1d)=
# Random walk unidimensionale discreto

Il modello più semplice che contiene questi ingredienti è il random walk
simmetrico unidimensionale.

Consideriamo una particella che si trova inizialmente nella posizione $x_0$.
Dividiamo il tempo in intervalli di durata $\Delta t$. Durante ogni intervallo
la particella compie uno spostamento di modulo $\Delta x$, verso destra oppure
verso sinistra con uguale probabilità:

$$
x_n=x_{n-1}+\xi_n,
$$

dove

$$
\xi_n=
\begin{cases}
+\Delta x & \text{con probabilità } 1/2,\\
-\Delta x & \text{con probabilità } 1/2.
\end{cases}
$$

Ne discende che gli spostamenti compiuti in intervalli temporali diversi sono statisticamente indipendenti, cioè che $\langle \xi_i \xi_j \rangle = 0$ per $i \neq j$.

```{figure} #cell:res_random_walk_traj
:label: fig:res_random_walk_traj
:align: center

Quattro diverse realizzazioni di un random walk unidimensionale simulato per $10^5$ passi.
```

La [](#fig:res_random_walk_traj) mostra quattro diverse realizzazioni numeriche di un random walk unidimensionale. Si vede come le traiettorie siano molto frastagliate e diverse tra loro. Qualitativamente, sembra anche che il camminatore non si sposti molto: se ci si muove di $N$ passi lungo una direzione, lo spostamento totale coinciderà con $N$, mentre in queste traiettorie il camminatore non si è allontanato per più di qualche centinaio di passi dall'origine, nonostante i $10^5$ passi compiuti. Questa osservazione qualitativa si può circostanziare come segue. Dopo $n$ passi,

$$
x_n=x_0+\sum_{i=1}^{n}\xi_i,
$$

e quindi lo spostamento rispetto alla posizione iniziale è

$$
X_n\equiv x_n-x_0=\sum_{i=1}^{n}\xi_i.
$$

Questa formulazione rende esplicito che $x_0$ determina soltanto una traslazione della traiettoria, mentre le proprietà statistiche del moto sono contenute nella somma degli incrementi casuali.

Per un singolo passo si ha

$$
\langle \xi_i\rangle = \frac{1}{2}\Delta x+\frac{1}{2}(-\Delta x)
=0.
$$

Usando la linearità del valor medio,

$$
\langle X_n\rangle = \left\langle\sum_{i=1}^n\xi_i\right\rangle = \sum_{i=1}^n\langle\xi_i\rangle = 0.
$$

Di conseguenza, $\langle x_n-x_0\rangle=0$ e quindi $\langle x_n\rangle=x_0$: la posizione media non cambia nel tempo. Questo non significa che la particella rimanga ferma: le singole traiettorie si allontanano in generale da $x_0$, ma gli spostamenti verso destra e verso sinistra si compensano quando si calcola la media su molte realizzazioni.

Calcoliamo ora il quadrato dello spostamento:

$$
X_n^2 = \left(\sum_{i=1}^n\xi_i\right)^2 = \sum_{i=1}^n\xi_i^2 + 2\sum_{i<j}\xi_i\xi_j.
$$

Prendendo il valor medio,

$$
\langle X_n^2\rangle = \sum_{i=1}^n\langle\xi_i^2\rangle + 2\sum_{i<j}\langle\xi_i\xi_j\rangle.
$$

Poiché i passi sono indipendenti, per $i\neq j$ vale

$$
\langle\xi_i\xi_j\rangle = \langle\xi_i\rangle\langle\xi_j\rangle = 0.
$$

Inoltre, indipendentemente dalla direzione del passo,

$$
\xi_i^2=\Delta x^2,
$$

e quindi

$$
\langle X_n^2\rangle = \sum_{i=1}^n\Delta x^2 = n\Delta x^2.
$$

Otteniamo dunque

$$
\left\langle (x_n-x_0)^2\right\rangle=n\Delta x^2.
$$

Dato che $\langle X_n\rangle=0$, questa quantità coincide con la varianza:

$$
\operatorname{Var}(X_n) = \langle X_n^2\rangle-\langle X_n\rangle^2 = n\Delta x^2.
$$

Analogamente,

$$
\operatorname{Var}(x_n)=n\Delta x^2.
$$

La posizione quadratica media, che in generale dipende dalla scelta dell'origine e/o dalla condizione iniziale $x_0$, è invece

$$
\langle x_n^2\rangle = x_0^2+\langle X_n^2\rangle = x_0^2+n\Delta x^2.
$$

(sec:diffusive-scaling)=
## Legge di scala diffusiva

Una misura della distanza tipica percorsa dalla particella è la radice dello spostamento quadratico medio,

$$
x_{\mathrm{rms}}
\equiv
\sqrt{\left\langle(x_n-x_0)^2\right\rangle}.
$$

Per il random walk,

$$
x_{\mathrm{rms}}=\Delta x\sqrt{n}.
$$

La distanza tipica cresce quindi come $\sqrt{n}$, e non come $n$: dopo $n$ passi la particella ha percorso una distanza totale $n\Delta x$, ma il suo spostamento netto è tipicamente soltanto dell'ordine di $\Delta x\sqrt n$.

Se a ogni passo associamo un intervallo temporale $\Delta t$, dopo $n$ passi è trascorso un tempo

$$
t=n\Delta t.
$$

Lo spostamento quadratico medio può allora essere scritto come

$$
\label{eq:D}
\left\langle(x(t)-x_0)^2\right\rangle = \frac{\Delta x^2}{\Delta t}t \equiv 2 D t,
$$

dove abbiamo introdotto il coefficiente di diffusione unidimensionale

$$
D\equiv\frac{\Delta x^2}{2\Delta t}.
$$

Questa relazione lineare tra spostamento quadratico medio e tempo rappresenta la caratteristica principale (la firma) del **moto diffusivo**.

```{figure} #cell:res_random_walk_msd
:label: fig:res_random_walk_msd
:align: center

Lo spostamento quadratico $\left\langle(x(t)-x_0)^2\right\rangle$ per un random-walk unidimensionale mediato su una o più traiettorie (vedi legenda), insieme alla curva teorica di pendenza unitaria (linea tratteggiata viola). **Nota Bene:** la linea rossa è quasi completamente nascosta dalla curva teorica.
```

La [](#fig:res_random_walk_msd) mostra come lo spostamento quadratico medio tenda al valore teorico, purché il numero di traiettorie su cui è mediato sia sufficientemente grande. Il grafico è in scala doppio logaritmica (o log-log): questa scelta è la migliore quando le quantità di interesse variano di diversi ordini di grandezza. Inoltre, se $A(t) = B t^\alpha$, allora $\log(A(t)) = \alpha \log(Bt) = \alpha \log(t) + \alpha \log B$: quantità che dipendono dall'ascissa con una legge a potenza appariranno rette di coefficiente angolare pari all'esponente della potenza.

```{note} Il limite al continuo
Immaginiamo di descrivere lo stesso processo usando passi temporali e spaziali, $\Delta t$ e $\Delta x$, sempre più piccoli. Poiché stiamo descrivendo lo stesso sistema fisico, le quantità osservabili devono rimanere le stesse, indipendentemente dal valore di $\Delta t$ e $\Delta x$. In particolare, la varianza osservata dopo lo stesso tempo $t$, eq. [](#eq:D), non deve cambiare quando scegliamo una discretizzazione più fine. Per questo, nel limite continuo, il rapporto

$$
\frac{\Delta x^2}{\Delta t}
$$

deve rimanere costante.

In una dimensione si definisce convenzionalmente

$$
D=\frac{\Delta x^2}{2\Delta t},
$$

così che la relazione precedente assuma la forma standard $\left\langle [x(t)-x_0]^2 \right\rangle=2Dt$. Il fattore $2$ fa quindi parte della definizione convenzionale di $D$; la condizione fisica importante è che $\Delta x^2/\Delta t$ rimanga costante.
```

Confrontiamo il moto diffusivo con quello di una particella che si muove con velocità costante (*moto balistico*). In questo caso la posizione evolve con la legge

$$
x(t)-x_0=vt,
$$

da cui si trova immediatamente

$$
[x(t)-x_0]^2=v^2t^2.
$$

Nel moto balistico gli spostamenti successivi sono tutti coerenti: la particella mantiene memoria della direzione del moto. Nel random walk, invece, la direzione di ogni passo è indipendente da quella dei passi precedenti e la memoria della direzione viene persa immediatamente.

:::{note} Distinguere il tipo di moto
Una quantità molto utile per studiare il moto di oggetti (che siano particelle, colloidi, persone, *ecc*) è lo spostamento quadratico medio (*mean-square displacement* o MSD) in funzione del tempo:

$$
\mathrm{MSD}(t)\equiv \left\langle[x(t)-x(0)]^2\right\rangle.
$$

È comune in molti sistemi fisici molto diversi tra loro che l'MSD e il tempo siano connessi da una legge a potenza del tipo $\mathrm{MSD}(t)\propto t^\beta$. Spesso, $\beta$ dipende dal tempo: in questo caso l'MSD mostra *regimi* differenti. Ad esempio, il moto di una particella può essere di tipo balistico ($\beta = 2$) a tempi brevi, e diffusivo ($\beta = 1$) a tempi lunghi.
:::

(sec:random-walk-distribution)=
## Distribuzione delle posizioni

Consideriamo ora il seguente sistema: un "camminatore" (cioè la versione semplificata della nostra particella) che può spostarsi lungo un binario. A ogni istante di tempo $\Delta t$, il camminatore può spostarsi a destra o a sinistra di $\Delta x$ con uguale probabilità. Dopo $n$ passi, indichiamo con $n_+$ il numero di passi verso destra e con $n_-$ il numero di passi verso sinistra. Si ha

$$
n_++n_-=n
$$

e

$$
\label{eq:x_n_discreto}
x_n-x_0=(n_+-n_-)\Delta x=(2n_+-n)\Delta x.
$$

La probabilità che in un percorso di $n$ passi il camminatore ne abbia fatti $n_+$ verso destra è data da una distribuzione binomiale:

$$
\mathcal{P}(n_+, n) = \frac{1}{2^n} \binom{n}{n_+},
$$

da cui si ottiene la distribuzione della posizione sostituendo la dipendenza di $n_+$ da $x_n$ trovata nell'equazione [](#eq:x_n_discreto)[^Px_valida]:

$$
P_n(x) = \frac{1}{2^n}
\binom{n}{
\frac{1}{2}
\left(
n+\frac{x-x_0}{\Delta x}
\right)
},
$$

[^Px_valida]: La distribuzione è ovviamente valida per i soli valori di $x$ accessibili al random walk.

La distribuzione discreta presenta alcune particolarità. Per esempio, se $n$ è dispari $P(x_0) = 0$, e dopo un numero pari di passi la particella può trovarsi soltanto a una distanza pari a un multiplo pari di $\Delta x$ da $x_0$. Questi dettagli diventano però irrilevanti quando $n$ è grande e si osserva il sistema su scale spaziali molto maggiori di $\Delta x$.

(sec:random-walk-clt)=
## Il limite continuo e il teorema del limite centrale

Lo spostamento dopo $n$ passi,

$$
X_n=\sum_{i=1}^n\xi_i,
$$

è la somma di $n$ variabili aleatorie indipendenti e identicamente distribuite, con

$$
\langle\xi_i\rangle=0,
\qquad
\operatorname{Var}(\xi_i)=\Delta x^2.
$$

Il [teorema del limite centrale](https://it.wikipedia.org/wiki/Teoremi_del_limite_centrale) afferma che, per $n$ grande, la variabile normalizzata

$$
Z_n
=
\frac{X_n}{\Delta x\sqrt n}
$$

tende ad avere una distribuzione normale con media nulla e varianza unitaria.

La distribuzione dello spostamento è quindi approssimativamente

$$
P(X_n) \simeq \frac{1}{\sqrt{2\pi n\Delta x^2}} \exp\left[ -\frac{X_n^2}{2n\Delta x^2} \right].
$$

Usando

$$
X_n=x-x_0,
\qquad
t=n\Delta t,
\qquad
D=\frac{\Delta x^2}{2\Delta t},
$$

otteniamo

$$
\label{eq:diff_gaussian}
P(x,t) = \frac{1}{\sqrt{4\pi Dt}} \exp\left[ -\frac{(x-x_0)^2}{4Dt} \right].
$$

Questa distribuzione ha media

$$
\langle x(t)\rangle=x_0
$$

e varianza

$$
\operatorname{Var}[x(t)]=2Dt.
$$

Nel limite continuo la distribuzione binomiale del random walk viene dunque sostituita da una distribuzione gaussiana la cui larghezza cresce come $\sqrt t$.

:::{note} Sul teorema del limite centrale
Il teorema del limite centrale non afferma che i singoli passi siano gaussiani. La distribuzione gaussiana emerge perché lo spostamento totale è la somma di un gran numero di contributi indipendenti.
:::

```{figure} #cell:res_random_walk_prob
:label: fig:res_random_walk_prob
:align: center

Distribuzioni di probabilità delle posizioni $x - x_0$ per un random walk unidimensionale a tre diversi istanti di tempo (da sinistra a destra, $n = 10, 100, 1000$), mediate su $10^5$ traiettorie. Gli istogrammi sono i valori numerici, mentre le righe continue sono le distribuzioni continue teoriche, eq. [](#eq:diff_gaussian).
```

La [](#fig:res_random_walk_prob) mostra come l'approssimazione continua funzioni piuttosto bene già a tempi corti ($n = 10$).

## Oltre il random walk destra/sinistra

Il teorema del limite centrale mostra che il comportamento diffusivo non dipende dalla scelta particolare di passi discreti verso destra o verso sinistra. Le ipotesi essenziali sono che gli incrementi $\xi_i$ siano indipendenti e identicamente distribuiti, con media nulla e varianza finita. Se queste condizioni sono verificate, per $n$ grande la distribuzione dello spostamento tenderà a una gaussiana, indipendentemente dalla forma dettagliata della distribuzione dei singoli passi. Il random walk destra/sinistra è quindi soltanto il più semplice esempio di una classe molto più generale di processi diffusivi.

:::{warning}
La finitezza della varianza è essenziale. Distribuzioni con code molto larghe e varianza infinita non soddisfano il teorema del limite centrale nella sua forma usuale e possono dare origine a dinamiche non diffusive.
:::

Una scelta comune, e utile anche per altre applicazioni, consiste nell'estrarre direttamente gli incrementi da una distribuzione gaussiana. Supponiamo di voler generare due variabili indipendenti $Z_1$ e $Z_2$, entrambe distribuite secondo una normale standard,

$$
Z_1,Z_2\sim\mathcal{N}(0,1).
$$

Poiché sono indipendenti, la loro densità congiunta è il prodotto delle due densità gaussiane:

$$
\frac{1}{2\pi}\exp\left[-\frac{z_1^2+z_2^2}{2}\right].
$$

Introduciamo le coordinate polari, $z_1=r\cos\theta$ e $z_2=r\sin\theta$, per cui $z_1^2+z_2^2=r^2$. Nel cambio di variabili bisogna inoltre includere lo Jacobiano, $dz_1,dz_2=r,dr,d\theta$. La densità congiunta di $R$ e $\Theta$ diventa quindi

$$
p_{R, \Theta}(r, \theta) = \frac{1}{2\pi}r e^{-r^2/2},
$$

con

$$
r\geq 0,\qquad0\leq\theta<2\pi.
$$

Questa densità si fattorizza:

$$
p_{R, \Theta}(r, \theta) = \underbrace{r e^{-r^2/2}}{p_R(r)}\underbrace{\frac{1}{2\pi}}{p_\Theta(\theta)}.
$$

Di conseguenza, $R$ e $\Theta$ sono indipendenti. In particolare, l'angolo è uniformemente distribuito nell'intervallo $[0,2\pi)$. Se $U_2$ è una variabile uniforme in $(0,1)$, possiamo quindi porre

$$
\Theta=2\pi U_2.
$$

Resta da generare la variabile radiale $R$, la cui densità è

$$
p_R(r)=r e^{-r^2/2}.
$$

La sua funzione di distribuzione cumulativa è

$$
F_R(r) = P(R\leq r) = \int_0^r s e^{-s^2/2},ds.
$$

Poiché

$$
\od{}{dx} e^{-s^2/2} = -s e^{-s^2/2},
$$

si ottiene

$$
F_R(r)=1-e^{-r^2/2}.
$$

Usiamo ora il metodo della trasformazione inversa. Se $U_1$ è uniforme in $[0,1)$, imponiamo

$$
U_1=F_R(r)=1-e^{-r^2/2}.
$$

Da questa relazione segue

$$
e^{-r^2/2}=1-U_1,
$$

e quindi $-\frac{r^2}{2}=\log(1-U_1)$, pertanto,

$$
r=\sqrt{-2\log(1-U_1)}.
$$

Poiché anche $1-U_1$ è uniforme (in $(0,1]$ piuttosto che in $[0, 1)$, ma questo non cambia le sue proprietà statistiche), possiamo rinominarlo semplicemente $U_1$ e scrivere[^U1]

$$
R=\sqrt{-2\log U_1}.
$$

Tornando infine alle coordinate cartesiane,

$$
Z_1=R\cos\Theta,\qquad Z_2=R\sin\Theta.
$$

Sostituendo le espressioni trovate per $R$ e $\Theta$, otteniamo la trasformazione di Box-Muller:

$$
\begin{align}
Z_1 &= \sqrt{-2\log U_1}\cos(2\pi U_2)\\
Z_2 &= \sqrt{-2\log U_1}\sin(2\pi U_2),
\end{align}
$$

dove $U_1$ e $U_2$ sono variabili uniformi indipendenti in $(0,1)$. Le variabili $Z_1$ e $Z_2$ così generate sono indipendenti e distribuite secondo una normale standard. Un incremento gaussiano di varianza $\sigma^2$ si ottiene quindi ponendo

### C: Variabili locali `static`, ovvero come ricordare un valore tra due chiamate

La trasformazione di Box–Muller genera due numeri gaussiani indipendenti, $Z_1$ e $Z_2$, usando la stessa coppia di numeri uniformi. Se la nostra funzione restituisse soltanto $Z_1$, getteremmo via metà del risultato appena calcolato:

```c
double gaussian(void) {
    double u1 = 1.0 - drand48();
    double u2 = drand48();

    double r = sqrt(-2.0 * log(u1));
    double theta = 2.0 * M_PI * u2;

    return r * cos(theta);
}
```

Potremmo invece restituire $Z_1$ e conservare $Z_2$ per la chiamata successiva. Una normale variabile locale, tuttavia, non è adatta a questo scopo:

```c
double gaussian(void) {
    double next_gaussian;

    /* ... */

    next_gaussian = z2;
    return z1;
}
```

La variabile `next_gaussian` viene creata ogni volta che la funzione viene chiamata e cessa di esistere quando la funzione termina. Il valore assegnato durante una chiamata non è quindi disponibile in quella successiva.

Per conservare il valore possiamo dichiarare la variabile locale mediante la parola chiave `static`:

```c
static double next_gaussian = 0.0;
```

Una variabile locale `static` ha proprietà particolari:

- è visibile soltanto all'interno della funzione in cui è dichiarata;
- viene inizializzata **una sola volta**;
- conserva il proprio valore tra chiamate successive della funzione;
- esiste per tutta la durata del programma.

Possiamo quindi implementare il generatore nel modo seguente:

```c
double gaussian(void) {
    static int has_spare = 0;
    static double spare = 0.0;

    if(has_spare) {
        has_spare = 0;
        return spare;
    }

    double u1 = 1.0 - drand48();
    double u2 = drand48();

    double r = sqrt(-2.0 * log(u1));
    double theta = 2.0 * M_PI * u2;

    double z1 = r * cos(theta);
    double z2 = r * sin(theta);

    spare = z2;
    has_spare = 1;

    return z1;
}
```

Le due variabili statiche hanno ruoli differenti:

- `spare` conserva il secondo numero gaussiano prodotto da Box–Muller;
- `has_spare` indica se `spare` contiene un numero ancora da utilizzare.

Durante la prima chiamata `has_spare` vale zero. La funzione genera quindi $Z_1$ e $Z_2$, restituisce $Z_1$ e conserva $Z_2$ in `spare`. Durante la seconda chiamata `has_spare` vale uno: la funzione restituisce immediatamente il valore conservato, senza generare nuovi numeri uniformi e senza valutare nuovamente logaritmo, seno e coseno (che sono tra le funzioni matematiche più "costose" in termini di cicli CPU). La terza chiamata genera una nuova coppia, la quarta usa nuovamente il valore conservato, e così via. Il costo della trasformazione di Box–Muller viene pertanto sostenuto una volta ogni due numeri gaussiani prodotti.

Se `has_spare` e `spare` non fossero `static`, verrebbero ricreate a ogni chiamata. In particolare, `has_spare` sarebbe inizializzata ogni volta a zero
e l'istruzione condizionale

```c
if(has_spare)
```

non sarebbe mai verificata.

:::{warning}
La funzione ora possiede uno **stato interno**: il suo risultato dipende anche da ciò che è accaduto nelle chiamate precedenti. Questa soluzione è semplice
ed efficiente per un programma sequenziale, ma in casi più complessi applicare soluzioni del genere richiede cautela.
:::

```{note} Una versione più veloce
:class: dropdown

Valutare funzioni trigonometriche, quali `sin` e `cos`, è un'operazione relativamente costosa. Esiste quindi una variante della trasformazione di Box-Muller, attribuita a Marsaglia, che non ne fa uso.

Si estraggono due variabili indipendenti $U$ e $V$, uniformi nell'intervallo $(-1,1)$, e si calcola $S=U^2+V^2$. Se $S\geq 1$ oppure $S=0$, la coppia viene scartata e si ripete l'estrazione. Quando invece $0<S<1$, si definisce

$$
F=\sqrt{\frac{-2\log S}{S}}.
$$

Le due quantità

$$
Z_1=UF,
\qquad
Z_2=VF
$$

sono variabili gaussiane indipendenti con media nulla e varianza unitaria.
```

[^U1]: Oppure possiamo mantenere il numero distribuito in $(0, 1]$ per evitare divergenze nel logaritmo, come viene fatto nella funzione di esempio riportata più in basso

(sec:diffusion-equation)=
## Dalla dinamica discreta all'equazione di diffusione

Indichiamo con $P(x,t)$ la probabilità di trovare la particella nella posizione $x$ al tempo $t$.

Per trovarsi in $x$ al tempo $t+\Delta t$, al passo precedente la particella deve essersi trovata

- in $x-\Delta x$ e aver compiuto un passo verso destra;
- oppure in $x+\Delta x$ e aver compiuto un passo verso sinistra.

La probabilità soddisfa quindi la *master equation*

$$
\label{eq:master_equation}
P(x,t+\Delta t) = \frac12P(x-\Delta x,t) + \frac12P(x+\Delta x,t).
$$

Supponiamo ora che $\Delta x$ e $\Delta t$ siano sufficientemente piccoli (o, equivalentemente, che $P(x,t)$ vari lentamente sulle scale microscopiche
$\Delta x$ e $\Delta t$). Possiamo allora sviluppare i due membri in serie di Taylor.

Per il membro sinistro,

$$
P(x,t+\Delta t) = P(x,t) + \Delta t\frac{\partial P}{\partial t} + \mathcal{O}(\Delta t^2).
$$

Per i due termini spaziali,

$$
P(x\pm\Delta x,t) = P(x,t) \pm \Delta x\frac{\partial P}{\partial x} + \frac{\Delta x^2}{2} \frac{\partial^2P}{\partial x^2} \pm \frac{\Delta x^3}{3!} \frac{\partial^3P}{\partial x^3} + \mathcal{O}(\Delta x^4).
$$

Sommando i due contributi, i termini dispari in $\Delta x$ si cancellano:

$$
\frac12 \left[ P(x-\Delta x,t)+P(x+\Delta x,t) \right] = P(x,t) + \frac{\Delta x^2}{2} \frac{\partial^2P}{\partial x^2} + \mathcal{O}(\Delta x^4).
$$

Inserendo gli sviluppi nell'equazione [](#eq:master_equation) si ottiene

$$
P + \Delta t\frac{\partial P}{\partial t} = P + \frac{\Delta x^2}{2} \frac{\partial^2P}{\partial x^2} + \mathcal{O}(\Delta t^2,\Delta x^4).
$$

Eliminando il termine $P(x,t)$ da entrambi i membri e dividendo per
$\Delta t$,

$$
\frac{\partial P}{\partial t} = \frac{\Delta x^2}{2\Delta t} \frac{\partial^2P}{\partial x^2} + \mathcal{O} \left( \Delta t, \frac{\Delta x^4}{\Delta t} \right).
$$

Ricordando la definizione di coefficiente di diffusione, eq. [](#eq:D), possiamo prendere il limite al continuo, $\Delta x\to0$ e $\Delta t\to0$, ottenendo

$$
\frac{\partial P(x,t)}{\partial t} = D\frac{\partial^2P(x,t)}{\partial x^2}.
$$

Questa è l'**equazione di diffusione**, formalmente identica all'[equazione del calore](https://it.wikipedia.org/wiki/Equazione_del_calore). Si può dimostrare (ma noi non lo faremo) che la soluzione di questa equazione differenziale per una particella che si trova con certezza in $x_0$ al tempo iniziale[^condizione_iniziale] è

$$
P(x,t) = \frac{1}{\sqrt{4\pi Dt}} \exp\left[ -\frac{(x-x_0)^2}{4Dt} \right].
$$

Si tratta, e non è un caso, della stessa distribuzione gaussiana ottenuta applicando il teorema del limite centrale al random walk discreto, eq. [](#eq:diff_gaussian).

La distribuzione è normalizzata,

$$
\int_{-\infty}^{+\infty}P(x,t)\,dx=1,
$$

e soddisfa

$$
\langle x(t)\rangle=x_0,
\qquad
\left\langle[x(t)-x_0]^2\right\rangle=2Dt.
$$

Il random walk discreto e l'equazione di diffusione descrivono quindi la
stessa fisica su scale differenti:

- il random walk fornisce una descrizione microscopica in termini di passi
  casuali;
- l'equazione di diffusione fornisce una descrizione continua e
  macroscopica dell'evoluzione della densità di probabilità.

[^condizione_iniziale]: Questo tipo di condizioni iniziali si può scrivere formalmente come
$$
P(x,0)=\delta(x-x_0),
$$
dove $\delta(x)$ è la [delta di Dirac](https://it.wikipedia.org/wiki/Delta_di_Dirac), un oggetto matematico che verrà introdotto durante il corso di Modelli e Metodi Matematici della Fisica.

(sec:langevin-equation)=
# L'equazione di Langevin

Il random walk descrive il moto browniano direttamente in termini di spostamenti casuali. Esiste però un secondo punto di vista, più vicino alla meccanica newtoniana: scrivere un'equazione del moto per la particella e rappresentare l'effetto del fluido mediante una forza dissipativa e una forza casuale.

Questo approccio fu introdotto da Paul Langevin all'inizio del Novecento. L'idea fondamentale consiste nel separare l'effetto delle molecole del fluido in due contributi:

1. un termine di attrito, che tende a frenare la particella;
2. una parte rapidamente fluttuante (detta spesso *di rumore*), dovuta al fatto che gli urti microscopici non si compensano mai esattamente.

In una dimensione l'equazione di Langevin più semplice è

$$
m\odd{x}{t} = -\gamma v(t) + \eta(t),
$$

dove $\gamma > 0$ è il coefficiente di attrito e quindi $-\gamma v(t)$ è la forza dissipativa, e $\eta(t)$ è una forza casuale che rappresenta gli urti con il fluido. La novità rispetto alle ODE considerate finora, come le equazioni differenziali dovute all'applicazione delle leggi di Newton, è che la forzante $\eta(t)$ non è una funzione deterministica del tempo, e non può essere scritta in termini di $x(t)$ e $v(t)$. In questo caso, infatti, "risolvere l'equazione" significa ottenere una traiettoria che non è unica, ma dipende dalla realizzazione. Come per il random walk discreto, anche in questo caso il sistema va studiato in termini probabilistici.

Prima di introdurre il rumore ripassiamo l'effetto della dissipazione. Ponendo $\eta(t) = 0$ l'equazione diventa

$$
m\odd{x}{t} = m\od{v}{t}=-\gamma v,
$$

che ha soluzione

$$
\label{eq:dissipazione_esatta}
v(t)=v_0e^{-\gamma t/m} = v_0e^{-t/\tau_v},
$$

dove abbiamo implicitamente definito il *tempo di rilassamento* della velocità come $\tau_v=\frac{m}{\gamma}$

La velocità iniziale viene quindi "dimenticata" su una scala temporale
dell'ordine di $\tau_v$. Infatti, per tempi molto più brevi, $t\ll\tau_v$, la velocità cambia poco e il moto è approssimativamente balistico. Per tempi molto più lunghi, $t\gg\tau_v$, la memoria della velocità iniziale è persa e diventano dominanti gli effetti
cumulativi delle fluttuazioni casuali.

Questa scala temporale è importante anche da un altro punto di vista: per risolvere numericamente la dinamica di un sistema con un termine di attrito, $\Delta t$ deve essere sufficientemente piccolo da poter risolvere correttamente il rilassamento, e quindi si deve avere $\Delta t \ll \tau_v$.

(sec:wiener-process)=
## Il processo di Wiener

Per rappresentare la forza casuale è utile introdurre il **processo di Wiener**, il più famoso tra i *processi stocastici*, spesso indicato con $W(t)$.

:::{admonition} Che cos'è un processo stocastico?
:class: note

Una variabile aleatoria descrive una quantità il cui valore non è noto con certezza. Un **processo stocastico** è una collezione di variabili aleatorie indicizzate dal tempo, diciamo $X(t)$. Per ogni istante $t$, $X(t)$ rappresenta l'insieme dei valori che il sistema potrebbe assumere, insieme alle rispettive probabilità.

Quando si osserva o si simula una singola evoluzione del sistema, si ottiene invece una funzione del tempo,

$$
t \mapsto x(t),
$$

chiamata **traiettoria** o **realizzazione** del processo.

Per esempio, nel random walk la successione delle posizioni

$$
X_0,X_1,X_2,\ldots
$$

è un processo stocastico a tempo discreto, mentre una sequenza specifica come

$$
0,\ \Delta x,\ 0,\ -\Delta x, - 2 \Delta x, \ldots
$$

è una sua traiettoria.

Il processo di Wiener $W(t)$ è invece un processo stocastico a tempo continuo: le sue traiettorie sono continue, ma estremamente irregolari.
:::

$W(t)$ non è una funzione propriamente detta, e quindi non possiamo definirlo tramite un'espressione chiusa (per esempio attraverso la sua derivata). È invece caratterizzabile mediante i suoi incrementi. Consideriamo due istanti separati da un intervallo $\Delta t$. L'incremento

$$
\Delta W
=
W(t+\Delta t)-W(t)
$$

è una variabile aleatoria gaussiana con

$$
\langle\Delta W\rangle=0
$$

e

$$
\langle(\Delta W)^2\rangle=\Delta t.
$$

```{note} La relazione tra il random walk discreto e il processo di Wiener 

Il collegamento con il random walk discreto è immediato. Se dividiamo l'intervallo $[0,t]$ in $n$ sottointervalli di durata $\Delta t$, allora

$$
W(t)-W(0)=\sum_{i=1}^n \Delta W_i,
$$

dove gli incrementi $\Delta W_i$ sono indipendenti e soddisfano

$$
\left\langle \Delta W_i^2 \right\rangle=\Delta t.
$$

Di conseguenza, le varianze si sommano:

$$
\left\langle [W(t)-W(0)]^2 \right\rangle
=
\sum_{i=1}^n \left\langle \Delta W_i^2 \right\rangle
=
n\Delta t
=
t.
$$

Ricordando come, nel limite del continuo, $\Delta x^2 / \Delta t \to const$, ritroviamo la stessa struttura del random walk discreto, per il quale la varianza dopo $n$ passi è $n\Delta x^2$.
```

Dal punto di vista implementativo, l'incremento si può generare numericamente come

$$
\Delta W=\sqrt{\Delta t}\,R,
$$

dove $R$ è una variabile normale standard (cioè di media nulla e varianza unitaria, $R\sim\mathcal{N}(0,1)$). Data questa definizione, come per il random walk anche in questo caso gli incrementi associati a intervalli temporali distinti sono indipendenti.

In pratica, un processo di Wiener può essere costruito iterativamente:

$$
W_{n+1}=W_n+\sqrt{\Delta t}\,R_n,
$$

con $R_n$ indipendenti e distribuiti secondo una normale standard.

Questo è precisamente un random walk con passi gaussiani, che implica

$$
\langle W_n-W_0\rangle=0
$$

e

$$
\left\langle(W_n-W_0)^2\right\rangle=n\Delta t=t_n.
$$

:::{note} Perché $\sqrt{\Delta t}$?
Se per generare una traiettoria utilizzassimo $\Delta W \propto \Delta t$, la varianza del processo stocastico risultante sarebbe proporzionale a $\Delta t^2$ invece che a $\Delta t$, e quindi simuleremmo un moto di tipo balistico invece che diffusivo. Infatti, si può dimostrare come questa scelta farebbe scomparire le fluttuazioni nel limite $\Delta t\to0$.
:::

La forza ideale $\eta(t)$ varia su tempi arbitrariamente brevi e non deve essere interpretata come una normale funzione regolare. Conviene quindi, come fatto per il processo di Wiener, scrivere l'equazione direttamente in termini degli incrementi prodotti in un intervallo finito:

$$
m \Delta v = -\gamma v \Delta t + \sigma\Delta W,
$$

dove il parametro $\sigma$ determina l'intensità delle fluttuazioni.

Come vedrete più avanti nel corso di Meccanica Statistica, considerando una particella in equilibrio con un fluido alla temperatura $T$, il [teorema di equipartizione](https://it.wikipedia.org/wiki/Teorema_di_equipartizione_dell%27energia) richiede

$$
\frac{1}{2}m\langle v^2\rangle = \frac{1}{2}k_{\mathrm B}T,
$$

dove $k_B$ è la costante di Boltzmann. Dissipazione e rumore non possono quindi essere scelti indipendentemente: l'energia sottratta alla particella dalla forza di attrito deve essere restituita dal sistema sotto forma di rumore. Non lo dimostriamo, ma la condizione di equilibrio termico fissa

$$
\sigma=\sqrt{2\gamma k_{\mathrm B}T}.
$$

L'equazione di Langevin diventa quindi

$$
m\Delta v = -\gamma v\Delta t + \sqrt{2\gamma k_{\mathrm B}T}\Delta W,
$$

o, equivalentemente,

$$
\Delta v = -\frac{v}{\tau_v}\Delta t + \sqrt{\frac{2k_{\mathrm B}T}{m\tau_v}}\Delta W = -\frac{v}{\tau_v}\Delta t + \sqrt{
\frac{2k_{\mathrm B}T}{m\tau_v}\Delta t
} R.
$$

(sec:langevin-euler)=
## Integrazione numerica con il metodo di Eulero

Discretizziamo il tempo, $t_n=n\Delta t$. Applicando il metodo di Eulero all'equazione per la velocità otteniamo

$$
\label{eq:langevin_euler}
v_{n+1} = v_n - \frac{\Delta t}{\tau_v}v_n + \sqrt{\frac{2k_{\mathrm B}T}{m\tau_v}\Delta t}R_n,
$$

dove, come specificato sopra, gli $R_n$ sono numeri casuali indipendenti estratti da una gaussiana di media nulla e varianza unitaria.

La posizione al passo successivo è quindi

$$
x_{n+1}=x_n+v_n\Delta t.
$$

Come per il random walk discreto, ogni esecuzione dell'algoritmo produce una diversa traiettoria. Le proprietà fisiche si ottengono mediando su molte realizzazioni oppure, sotto opportune condizioni, studiando una singola traiettoria sufficientemente lunga.

Come abbiamo ampiamente dimostrato in passato, il metodo di Eulero è semplice, ma soffre di problemi strutturali che possono spesso portare a comportamenti non fisici, indipendentemente dal valore id $\Delta t$. Nel caso dell'equazione di Langevin, dimostriamo che la dinamica di Eulero non riproduce esattamente la distribuzione di equilibrio della velocità.

Considerando l'aggiornamento [](#eq:langevin_euler) e definendo $q=1-\frac{\Delta t}{\tau_v}$, possiamo calcolare la varianza della velocità, che evolve secondo

$$
\langle v_{n+1}^2\rangle = q^2\langle v_n^2\rangle + \frac{2k_{\mathrm B}T}{m\tau_v}\Delta t.
$$

In condizioni stazionarie la varianza deve rimanere costante, quindi

$$
\langle v_{n+1}^2\rangle=\langle v_n^2\rangle = \langle v^2\rangle_{\mathrm{Euler}},
$$

da cui si ottiene

$$
\langle v^2\rangle_{\mathrm{Euler}} = \frac{k_{\mathrm B}T}{m} \frac{1}{1-\Delta t/(2\tau_v)} \neq \frac{k_B T}{m}.
$$

La temperatura cinetica misurata numericamente è quindi leggermente più alta di quella desiderata. L'errore scompare nel limite

$$
\Delta t\to0,
$$

ma è sempre presente, e può diventare visibile se il passo temporale non è sufficientemente piccolo. Questo fornisce un utile test numerico: variando $\Delta t$, si può verificare se e come $m\langle v^2\rangle$ converga a $k_{\mathrm B}T$.

````{note} Approfondimento: un aggiornamento diverso per la velocità
:class: dropdown

Abbiamo visto che la parte dissipativa dell'equazione di Langevin ha come soluzione esatta quella data dall'equazione [](#eq:dissipazione_esatta). Nel caso discreto, il fattore di decadimento esatto si può quindi scrivere come

$$
a=e^{-\Delta t/\tau_v},
$$

da cui possiamo derivare, espandendo al primo ordine per piccoli valori di $\Delta t$, il fattore di Eulero:

$$
1-\frac{\Delta t}{\tau_v}.
$$

Utilizzando la soluzione analitica e il principio di equipartizione, possiamo tener conto sia della dissipazione sia della varianza del rumore durante il passo. L'aggiornamento della velocità diventa così

$$
v_{n+1} = a v_n + \sqrt{\frac{k_{\mathrm B}T}{m} \left(1-a^2\right)}R_n.
$$

Questa espressione ha un'interpretazione semplice:

- il contributo della vecchia velocità viene ridotto dal fattore $a$ (e quindi decresce esponenzialmente);
- il rumore reintegra esattamente la parte di varianza persa per dissipazione.

Infatti, se

$$
\langle v_n^2\rangle=\frac{k_{\mathrm B}T}{m},
$$

allora

$$
\langle v_{n+1}^2\rangle = a^2\langle v_n^2\rangle + \frac{k_{\mathrm B}T}{m}(1-a^2) = \frac{k_{\mathrm B}T}{m}.
$$

La distribuzione di equilibrio della velocità viene quindi preservata per qualunque valore di $\Delta t$. In particolare, per $\Delta t\ll\tau_v$,

$$
a=e^{-\Delta t/\tau_v} \simeq 1-\frac{\Delta t}{\tau_v},
$$

e quindi

$$
1-a^2 \simeq \frac{2\Delta t}{\tau_v}.
$$

L'aggiornamento esatto si riduce allora al metodo di Eulero:

$$
v_{n+1} \simeq v_n -\frac{\Delta t}{\tau_v}v_n + \sqrt{ \frac{2k_{\mathrm B}T}{m\tau_v}\Delta t }\,R_n.
$$

Questa forma mostra con chiarezza quale sia il peso corretto da attribuire alla parte dissipativa e quale debba essere, di conseguenza, l'ampiezza delle fluttuazioni.

:::{warning} La scelta di $\Delta t$
Utilizzando la soluzione esatta otteniamo un'espressione per l'aggiornamento della velocità che è esatta per il sistema considerato qui (problema lineare a forza esterna nulla). L'aggiornamento della posizione rimane invece un'approssimazione di Eulero. Quindi, l'uso del decadimento esponenziale non rende irrilevante la scelta di $\Delta t$. Infatti, un passo troppo grande può ancora descrivere male la posizione e non permette di risolvere la dinamica alle scale temporali più brevi. In effetti, il vantaggio che si ottiene con questo metodo è spesso piccolo o addirittura irrisorio rispetto al metodo di Eulero, che in questi casi è ancora l'algoritmo più utilizzato per integrare le equazioni di Langevin.
:::
````

(sec:langevin-diffusion)=
## Dalla dinamica di Langevin alla diffusione

```{figure} #cell:res_langevin
:label: fig:res_langevin
:align: center

A sinistra: lo spostamento quadratico medio ottenuto risolvendo l'equazione di Langevin di parametri $\gamma = 1$, $m = 1$, $k_B T = 1$ integrata con il metodo di Eulero con $\Delta t = 0.01$. I risultati sono stati ottenuti mediando su 10000 traiettorie. Le linee tratteggiate sono gli andamenti teorici balistico e diffusivo nei rispettivi regimi di validità. A destra: le distribuzioni degli spostamenti numeriche (linee continue) e teoriche (eq [](#eq:diff_gaussian), linee tratteggiate) a $t = 10$ e $t = 100$.
```

La [](#fig:res_langevin) mostra alcuni risultati di simulazione ottenuti mediando molte traiettorie. Nel pannello di sinistra, che mostra lo spostamento quadratico medio, si può vedere come l'equazione di Langevin contenga sia il regime balistico sia quello diffusivo. Infatti, per tempi molto brevi rispetto a $\tau_v$, la velocità non ha ancora perso memoria del suo valore iniziale e

$$
x(t)-x_0\simeq v_0t.
$$

Di conseguenza,

$$
\left\langle[x(t)-x_0]^2\right\rangle
\propto t^2.
$$

Per tempi molto lunghi rispetto a $\tau_v$, la velocità iniziale viene dimenticata e lo spostamento è il risultato della somma di molti contributi debolmente correlati. Si può dimostrare (ma non lo faremo) come in questo regime si ottenga allora il regime diffusivo,

$$
\left\langle[x(t)-x_0]^2\right\rangle \simeq 2Dt.
$$

Il coefficiente di diffusione è legato all'attrito e alla temperatura dalla relazione di Einstein,

$$
D=\frac{k_{\mathrm B}T}{\gamma}.
$$

La dinamica di Langevin fornisce quindi un collegamento tra la descrizione microscopica in termini di velocità, attrito e fluttuazioni e la descrizione macroscopica in termini di diffusione.

(sec:langevin-exercises)=
## Esperimenti numerici

Una simulazione dell'equazione di Langevin permette di verificare direttamente diversi risultati:

1. La velocità media decade come
   $$
   \langle v(t)\rangle=v_0e^{-t/\tau_v}.
   $$

2. A tempi lunghi la velocità soddisfa l'equipartizione,
   $$
   \langle v^2\rangle=\frac{k_{\mathrm B}T}{m}.
   $$

3. Lo spostamento quadratico medio è balistico a tempi brevi,
   $$
   \left\langle[x(t)-x_0]^2\right\rangle\propto t^2,
   $$

   e diffusivo a tempi lunghi,
   $$
   \left\langle[x(t)-x_0]^2\right\rangle\propto t.
   $$

4. Nel regime diffusivo il coefficiente misurato soddisfa
   $$
   D=\frac{k_{\mathrm B}T}{\gamma}.
   $$

5. Il metodo di Eulero converge al risultato corretto diminuendo $\Delta t$, mentre l'aggiornamento esponenziale riproduce più    accuratamente la distribuzione delle velocità anche a passi temporali maggiori.
