---
title: Integrazione di equazioni differenziali ordinarie
exports:
   - format: pdf
---

# Equazioni differenziali ordinarie

Moltissimi problemi di fisica si possono formalizzare in termini di equazioni differenziali, cioè relazioni che connettono una funzione incognita alle sue derivate. La maggior parte delle equazioni differenziali di interesse non possono essere risolte analiticamente, e richiedono quindi di essere affrontate con metodi numerici. In questo corso ci occuperemo principalmente delle cosiddette equazioni differenziali ordinarie (spesso chiamate ODE, per *ordinary differential equations*), in cui la funzione incognita è di una sola variabile.

Un'equazione differenziale si dice dell'ordine $n$-esimo se contiene al suo interno derivate $n$-esime della funzione incognita. La soluzione *generale* di una ODE di ordine $n$-esimo contiene $n$ costanti di integrazione indipendenti il cui valore viene fissato specificando $n$ condizioni (dette solitamente condizioni *iniziali* o *al bordo*) per ottenere una soluzione *particolare*.

Nel seguito ci occuperemo principalmente di metodi per la risoluzione numerica di ODE del *primo* ordine, cioè del tipo

$$
\label{eq:ODE_first_order}
x' = f(x, t),
$$

dove $x = x(t)$ è la funzione incognita, $x' = x'(t)$ è la sua derivata prima e $t$ è la variable indipendente. Se siete studenti di fisica a Sapienza e state affrontando per la prima volta questo corso, avrete probabilmente già seguito (e magari passato con profitto) il corso di Meccanica. Durante il corso avrete senz'altro incontrato equazioni differenziali *del secondo ordine* del tipo

$$
\label{eq:ODE_second_order}
\odd{\vec{x}}{t} = \vec{a}(\vec{x}, \vec{v}, t),
$$

dove $\vec{x}(t)$ è la posizione di un punto materiale, $\vec{a}(\vec{x}, \vec{v}, t) = \vec{F}(\vec{x}, \vec{v}, t) / m$ la sua accelerazione, $\vec{F}(t, \vec{x}, \vec{v})$ la forza a cui è sottoposto e $m$ la sua massa[^3D]. Ricordando il legame tra velocità e posizione, un'equazione del tipo [](#eq:ODE_second_order) può essere trasformata in nel seguente sistema di equazioni del primo ordine:

$$
\begin{cases}
\od{\vec{x}}{t} = \vec{v}(t)\\
\od{\vec{v}}{t} = \vec{a}(\vec{x}, \vec{v}, t),
\end{cases}
$$

cui di solito si affiancano le condizioni iniziali $\vec{v}(t_0) = \vec{v}_0$ e $\vec{x}(t_0) = \vec{x}_0$.

[^3D]: La notazione $\vec{a}$ indica che $a$ è una quantità vettoriale, quindi [](#eq:ODE_second_order) è un *sistema* di equazioni del secondo ordine.

## Richiamo: l'oscillatore armonico

Come esempio di sistema dinamico utilizzeremo frequentemente l'oscillatore armonico unidimensionale, uno dei modelli più importanti della fisica. Oltre a descrivere direttamente numerosi fenomeni fisici, esso presenta il vantaggio di possedere una soluzione analitica semplice, che potrà essere utilizzata per valutare l'accuratezza dei diversi algoritmi di integrazione numerica.

Consideriamo una particella di massa $m$ soggetta a una forza elastica proporzionale allo spostamento dalla posizione di equilibrio,

$$
F = -kx,
$$

dove $k$ è la costante elastica della molla. Applicando la seconda legge di Newton, e usando la notazione $x'$ e $x''$ per indicare le derivate prime e seconde, rispettivamente, otteniamo

$$
m x'' = -kx,
$$

ovvero

$$
\label{eq:ODE_oscillatore}
x'' = -\omega_0^2 x,
$$

dove abbiamo introdotto la pulsazione naturale del sistema

$$
\omega_0 \equiv \sqrt{\frac{k}{m}}.
$$

La soluzione generale di questa equazione è

$$
\begin{split}
x(t) & = A \cos(\omega_0 t) + B \sin(\omega_0 t)\\
v(t) & = -A \omega_0 \sin(\omega_0 t) + B \omega_0 \cos(\omega_0 t),
\end{split}
$$

oppure, in forma equivalente,

$$
\begin{split}
x(t) & = C \cos(\omega_0 t + \phi)\\
v(t) & = -C \omega_0 \sin(\omega_0 t + \phi),
\end{split}
$$

dove le costanti $A$, $B$ (oppure $C$ e $\phi$) sono determinate dalle condizioni iniziali, $x(0) = x_0$ e $v(0) = v_0$. Poiché si tratta di un sistema senza attrito sottoposto a una forza che non dipende esplicitamente dal tempo, l'energia totale, somma di energia potenziale $U(t)$ ed energia cinetica $K(t)$, si conserva:

$$
\label{eq:E_harmonic_oscillator}
E(t) = U(t) + K(t) = \frac{1}{2} kx^2(t) + \frac{1}{2} m v^2(t).
$$

Come accennato precedentemente, per risolvere numericamente l'equazione differenziale del secondo ordine [](#eq:ODE_oscillatore) conviene trasformarla nel seguente sistema di due equazioni del primo ordine:

$$
\begin{cases}
x' = v, \\
v' = -\omega_0^2 x.
\end{cases}
\label{eq:ODE_harmonic_oscillator}
$$

Nel resto del capitolo considereremo anche una versione più generale del problema, che include sia l'attrito viscoso sia una *forzante*, ovvero una forza esterna dipendente dal tempo:

$$
x'' = -\omega_0^2 x - \gamma x' + \frac{F(t)}{m}.
$$

A seconda della scelta dei parametri si ottengono diversi casi di interesse fisico:

* $\gamma = 0$ e $F(t) = 0$: oscillatore armonico semplice;
* $\gamma > 0$ e $F(t) = 0$: oscillatore armonico smorzato;
* $\gamma > 0$ e $F(t) \neq 0$: oscillatore armonico forzato.

Questo sistema costituirà il principale banco di prova per gli algoritmi di integrazione numerica discussi nelle sezioni successive.

## Dal continuo al discreto

I computer sono macchine discrete e, come tali, non possono rappresentare esattamente quantità continue, ma soltanto approssimarle mediante un numero finito di valori. Quando vogliamo studiare numericamente un sistema descritto da equazioni differenziali, dobbiamo quindi trovare un modo per tradurre un problema continuo in una forma compatibile con l'architettura discreta del calcolatore.

Esistono diverse strategie per affrontare questo problema; in queste note ci concentreremo sui cosiddetti *metodi delle differenze finite* ([*finite difference methods*](https://en.wikipedia.org/wiki/Finite_difference_method))[^oltre_fd]. Per semplicità cominciamo la trattazione considerando casi unidimensionali, in cui la funzione incognita è $x(t)$. L'idea fondamentale consiste nel sostituire il dominio continuo della variabile indipendente (ad esempio il tempo $t$) con una successione discreta di punti separati da un intervallo $\Delta t$. In altre parole, invece di descrivere l'evoluzione del sistema in ogni istante, ne consideriamo soltanto una sequenza di "fotogrammi" successivi. Senza perdità di generalità, consideriamo un intervallo temporale $[t_0, t_{\rm max}]$ e suddividiamolo in $N$ intervalli uguali. Definiamo

$$
\Delta t \equiv \frac{t_{\rm max} - t_0}{N}
$$

e i punti della griglia 

$$
t_n = t_0 + n \Delta t, \qquad n = 0, 1, \ldots, N.
$$

Nel seguito per alleggerire la trattazione utilizzeremo spesso la notazione $y(t_i) = y(t_0 + i\Delta t) = y_i$.

[^oltre_fd]: Altri esempi di metodi comunemente utilizzati per risolvere sistemi di equazioni differenziali (spesso alle derivate parziali) sono gli elementi finiti ([*finite element methods*](https://en.wikipedia.org/wiki/Finite_element_method)) e la risoluzione in spazio di Fourier tramite [*Fast Fourier transform* (FFT)](https://en.wikipedia.org/wiki/Fast_Fourier_transform).

Una volta discretizzato il dominio, le derivate che compaiono nelle equazioni differenziali possono essere approssimate mediante opportune differenze tra i valori della funzione nei punti della griglia. Ad esempio, la derivata prima di $x(t)$ nel punto $t_d$ può essere approssimata come

$$
x'(t_d) = x'_d = \left.\od{x}{t}\right|_{t=t_d} \approx \frac{x_{d+1}-x_d}{\Delta t}.
$$

In questo modo un'equazione differenziale viene trasformata in una relazione algebrica tra valori della funzione calcolati in istanti successivi. Per comprendere l'idea alla base tutti i metodi che introdurremo nelle prossime sezioni, integriamo entrambi i membri dell'equazione [](#eq:ODE_first_order) tra due istanti consecutivi della griglia temporale, $t_n$ e $t_{n+1}$, ottenendo

$$
x_{n+1} - x_n = \int_{t_n}^{t_{n+1}} f(x,t) dt,
$$

ovvero

$$
\label{eq:ODE_full_solution}
x_{n+1} = x_n + \int_{t_n}^{t_{n+1}} f(x,t) dt.
$$

Introducendo il passo temporale $\Delta t = t_{n+1} - t_n$, possiamo riscrivere questa espressione come

$$
\label{eq:ODE_esatta}
x_{n+1} = x_n + \Delta t \langle f \rangle_n,
$$

dove

$$
\langle f \rangle_n \equiv \frac{1}{\Delta t} \int_{t_n}^{t_{n+1}} f(x,t) dt
$$

rappresenta il valore medio di $f(x,t)$ nell'intervallo $[t_n,t_{n+1}]$.

La relazione [](#eq:ODE_esatta) è esatta e costituisce il punto di partenza di tutti i metodi di integrazione numerica che vedremo. La difficoltà risiede nel fatto che, in generale, il valore medio $\langle f \rangle_n$ non è noto, poiché dipende dall'andamento della soluzione all'interno dell'intervallo stesso. I diversi algoritmi che presenteremo possono essere interpretati come diversi modi di approssimare questa quantità.

# Eulero e Eulero-Cromer

## Formulazione

Il metodo di Eulero è il più semplice algoritmo di integrazione numerica per equazioni differenziali ordinarie. L'idea consiste nell'approssimare il valore medio della funzione $f(x,t)$ nell'intervallo $[t_n,t_{n+1}]$ con il suo valore all'inizio dell'intervallo:

$$
\langle f \rangle_n \approx f(x_n,t_n).
$$

Sostituendo questa approssimazione nell'equazione [](#eq:ODE_esatta) si ottiene

$$
x_{n+1} = x_n + \Delta t f(x_n,t_n).
$$

L'algoritmo può quindi essere interpretato come un'estrapolazione lineare della soluzione a partire dalla sua derivata nel punto iniziale dell'intervallo. 

Nel caso di ODE del secondo ordine, come l'oscillatore armonico, le equazioni di aggiornamento diventano

$$
\label{eq:eulero}
\begin{cases}
x_{n+1} = x_n + v_n \Delta t\\
v_{n+1} = v_n + a_n \Delta t,
\end{cases}
$$

dove $a_n \equiv a(x_n,v_n,t_n)$. Abbiamo quindi approssimato sia l'accelerazione media sia la velocità media nell'intervallo $[t_n,t_{n+1}]$ utilizzando i rispettivi valori all'inizio dell'intervallo, cioè

$$
\begin{cases}
\langle a \rangle_n \approx a_n\\
\langle v \rangle_n \approx v_n.
\end{cases}
$$

Il metodo di Eulero è semplice da implementare, ma la sua accuratezza è limitata e può produrre risultati qualitativamente scorretti quando viene applicato a sistemi oscillanti per tempi lunghi. Un miglioramente a volte sostanziale si può ottenere utilizzando il metodo di Eulero-Cromer, che è una semplice modifica del metodo di Eulero particolarmente adatta allo studio di sistemi meccanici.

Dal punto di vista concettuale, il metodo di Eulero-Cromer utilizza il valore iniziale dell'accelerazione per stimare l'accelerazione media ($\langle a\rangle_n \approx a_n$, come Eulero), ma il valore finale della velocità per stimare la velocità media ($\langle v \rangle_n \approx v_{n+1}$). L'algoritmo completo assume pertanto la forma

$$
\begin{cases}
\label{eq:eulero_cromer}
v_{n+1} = v_n + a_n\Delta t\\
x_{n+1} = x_n + v_{n+1}\Delta t.
\end{cases}
$$

Questa semplice modifica produce risultati significativamente migliori in molti problemi meccanici, in particolare nei sistemi oscillanti.

## Esempio

```{figure} #cell:res_eulero
:label: fig:res_eulero
:align: center

Il risultato dell'integrazione del sistema [](#eq:ODE_harmonic_oscillator) con il metodo di Eulero. Dall'alto verso il basso, i tre pannelli mostrano la posizione $x(t)$, la velocità $y(t)$ e l'energia meccanica $E(t)$ in funzione del tempo per tre diversi valori del passo temporale $\Delta t$ ($10^{-1}$ in blu, $10^{-2}$ in arancione e $10^{-3}$ in verde), oltre al risultato esatto (riga tratteggiata). Considerando, per comodità, grandezze adimensionali, il sistema simulato ha $k = m = 1$ (e quindi $\omega_0 = 1$) e, come condizioni iniziali, $x_0 = 2$ e $v_0 = 1$.
```

Applichiamo i due metodi appena introdotti al sistema di equazioni differenziali [](#eq:ODE_harmonic_oscillator), cercando di valutare la qualità della soluzione numerica discretizzata al variare della grandezza del passo temporale $\Delta t$.

Cominciamo ad analizzare i risultati ottenuti con il metodo di Eulero, mostrati in [](#fig:res_eulero). Notiamo prima di tutto che solo le curva verdi (relative a $\Delta t = 10^{-3}$) sembrano ricalcare fedelmente, almeno alla scala della figura, la soluzione teorica. Per valori maggiori di $\Delta t$ tutte le quantità mostrate si discostano anche sensibilmente dalla teoria. È preoccupante non tanto il fatto che ci sia una differenza tra i valori numerici e quelli teorici, quanto che questa differenza aumenti nel tempo. Infatti, una delle principali proprietà dell'oscillatore armonico è la sua periodicità: il moto si ripete esattamente ogni periodo $T = 2 \pi / \omega_0$. Come si può vedere dalla figura, questa proprietà non è affatto rispettata dalla soluzione ottenuta con il metodo di Eulero: le oscillazioni di posizione e velocità aumentano di ampiezza col tempo. Questo aumento si riflette nell'energia totale, che a sua volta aumenta monotonicamente: l'errore dovuto alla discretizzazione ha l'effetto netto di *immettere* energia nel sistema.

```{figure} #cell:res_eulero_cromer
:label: fig:res_eulero_cromer
:align: center

Risultati analoghi a quelli di [](#fig:res_eulero), ottenuti però con il metodo di Eulero-Cromer. Notate l'intervallo dell'asse y del pannello di $E(t)$, decisamente più ristretto rispetto a quello della [](#fig:res_eulero).
```

Passiamo ad analizzare i risultati ottenuti con Eulero-Cromer e mostrati in [](#fig:res_eulero_cromer). Nonostante l'apparente similitudine dei due metodi, il comportamento che si osserva è molto diverso. In questo caso posizione e velocità sembrano venir riprodotte quasi perfettamente per tutti i valori di $\Delta t$, almeno alla scala della figura[^occhio]. Per quanto riguarda l'energia, questa sembra comportarsi in una maniera più strana: in tutti i casi (anche se, per $\Delta t = 10^{-1}$, non si vede bene) $E(t)$ non è costante nel tempo ma oscilla con periodo uguale a quello di $x(t)$ e $v(t)$ e ampiezza che decresce al diminuire di $\Delta t$. Quindi, se da un lato è vero che l'energia non si conserva, il suo *valore medio* rimane costante nel tempo: non c'è immissione o dissipazione netta di energia. Questa proprietà di "conservazione media" dell'energia è il massimo che possiamo chiedere a un algoritmo di integrazione numerico.

[^occhio]: Se avete un occhio attento potete notare qualche discrepanza tra la posizione teorica e quella ottenuta con $\Delta t = 10^{-1}$ in prossimità di massimi e minimi

```{iframe} ../simulations/eulero.html
:width: 100%

Simulazione di un oscillatore armonico integrato con Eulero (pallina rossa) ed Eulero-Cromer (pallina blu).
```

Il confronto fatto tra i risultati ottenuti con Eulero ed Eulero-Cromer ci permette di introdurre due proprietà fondamentali degli algoritmi per l'integrazione numerica: *stabilità* e *accuratezza*. Questi due concetti non sono necessariamente legati: un algoritmo può essere poco stabile ma molto accurato, un altro molto stabile ma poco accurato.

## Stabilità

Un algoritmo di integrazione numerica si dice stabile se piccoli errori introdotti durante l'evoluzione (dovuti, ad esempio, all'approssimazione del metodo o all'arrotondamento numerico, che sono fonti di errore sempre presenti su un calcolatore) non vengono amplificati in modo incontrollato al procedere dei passi temporali. Un metodo si dice **incondizionatamente stabile** se rimane stabile per qualunque valore del passo temporale $\Delta t$. Si dice invece **condizionatamente stabile** se la stabilità è garantita solo quando $\Delta t$ soddisfa una certa condizione, ad esempio $\Delta t < \Delta t_{\rm max}$. Sia la proprietà di essere condizionatamente/incodizionatamente stabile che l'eventuale valore di $\Delta t_{\rm max}$ dipendono non solo dall'algoritmo, ma anche dal problema che vogliamo risolvere. Vediamo come studiare la stabilità nel caso dell'oscillatore armonico, un sistema lineare che rende questo tipo di analisi più trasparente.

Le equazioni di aggiornamento del metodo di Eulero, Eq. [](#eq:eulero), possono essere riscritte per l'oscillatore armonico come

$$
\label{eq:eulero_matrix_step}
\begin{cases}
x_{n+1} = x_n + v_n \Delta t\\
v_{n+1} = - \omega^2 x_n \Delta t + v_n.
\end{cases}
$$

Introduciamo ora il concetto di *spazio delle fasi*: questo è l'insieme di tutte le possibili configurazioni (o stati) del sistema. Nel caso dell'oscillatore armonico unidimensionale[^spazio_fasi_1D], per identificare una configurazione è sufficiente specificare posizione $x$ e velocità $v$, e quindi lo spazio delle fasi comprende l'intero piano $(x, v)$. Un punto su questo piano, cioè una configurazione del sistema, si può identificare tramite un vettore $\mathbf{y} \equiv \begin{pmatrix} x \\ v\end{pmatrix}$. Discretizzando la notazione, possiamo definire lo stato del sistema al generico tempo $t_k$, $\mathbf{y}_k \equiv \begin{pmatrix} x_k \\ v_k\end{pmatrix}$, così da poter riscrivere il passo di integrazione temporale [](#eq:eulero_matrix_step) in forma compatta:

$$
\mathbf{y}_{n+1} = \hat{M} \mathbf{y}_n,
$$

dove

$$
\hat{M} = 
\begin{pmatrix}
1 & \Delta t\\
-\omega_0^2 \Delta t & 1.
\end{pmatrix}
$$

Utilizzando questo formalismo possiamo scrivere direttamente l'evoluzione del sistema dalle condizioni iniziali $\mathbf{y}_0 = \begin{pmatrix} x_0 \\ v_0\end{pmatrix}$ ad un generico tempo $t_n$ come

$$
\label{eq:eulero_matrix}
\mathbf{y}_{n+1} = \hat{M}^n \mathbf{y}_0.
$$

[^spazio_fasi_1D]: in effetti questo vale per qualunque sistema dinamico unidimensionale

Invece di calcolare la potenza $n$-esima di $\hat{M}$ componente per componente, possiamo utilizzare la decomposizione spettrale in autovalori e autovettori per ottenere direttamente l'operatore che determina l'evoluzione del sistema al tempo voluto. Poiché $\hat{M}$ è una matrice $2\times2$, essa ammette due autovalori $\lambda_1$ e $\lambda_2$, ai quali corrispondono due autovettori linearmente indipendenti $\mathbf{v}_1$ e $\mathbf{v}_2$, tali per cui:

$$
\hat{M}\mathbf{v}_1 = \lambda_1 \mathbf{v}_1, \quad \hat{M}\mathbf{v}_2 = \lambda_2 \mathbf{v}_2.
$$

Poiché i due autovettori formano una base dello spazio delle fasi, possiamo esprimere qualsiasi condizione iniziale $\mathbf{y}_0$ come una loro combinazione lineare:

$$
\mathbf{y}_0 = c_1 \mathbf{v}_1 + c_2 \mathbf{v}_2,
$$

dove $c_1$ e $c_2$ sono coefficienti (in generale complessi) che dipendono dallo stato iniziale scelto. Sfruttando la linearità della matrice $\hat{M}$, l'applicazione ripetuta dell'operatore di evoluzione per $n$ passi si riduce a

$$
\mathbf{y}_n = \hat{M}^n \mathbf{y}_0 = \hat{M}^n (c_1 \mathbf{v}_1 + c_2 \mathbf{v}_2) = c_1 \hat{M}^n \mathbf{v}_1 + c_2 \hat{M}^n \mathbf{v}_2.
$$

```{note} Se autovalori e autovettori sono complessi, $x$ e $v$ sono reali?
:class: dropdown

Un dubbio legittimo sorge spontaneo: se gli autovalori $\lambda$ e gli autovettori $\mathbf{v}$ sono numeri complessi, come fa lo stato fisico del sistema $\mathbf{y}_n$ a rimanere composto da coordinate puramente reali (posizione e velocità) a ogni passo? 

La risposta risiede in una proprietà fondamentale delle matrici reali. Dimostriamolo in tre passi:

1. Poiché la matrice di evoluzione $\hat{M}$ ha elementi puramente reali, se i suoi autovalori non sono reali allora sono complessi coniugati. Infatti, se ammette un autovalore complesso $\lambda_1 = \lambda$, anche il suo complesso coniugato $\lambda_2 = \bar{\lambda}$ deve essere un autovalore. Se $\mathbf{v}$ è l'autovettore associato a $\lambda$ (ovvero $\hat{M}\mathbf{v} = \lambda \mathbf{v}$), coniugando entrambi i membri otteniamo:
   $$\hat{M}\bar{\mathbf{v}} = \bar{\lambda}\bar{\mathbf{v}}$$
   Questo mostra che l'autovettore associato a $\bar{\lambda}$ è esattamente il coniugato del primo, cioè $\mathbf{v}_2 = \bar{\mathbf{v}}$.

2. Esprimiamo la condizione iniziale reale $\mathbf{y}_0$ nella base degli autovettori:
   $$\mathbf{y}_0 = c_1 \mathbf{v} + c_2 \bar{\mathbf{v}}$$
   Poiché $\mathbf{y}_0$ è reale, deve valere $\mathbf{y}_0 = \bar{\mathbf{y}}_0$. Coniugando l'espressione sopra si ottiene $\mathbf{y}_0 = \bar{c}_1 \bar{\mathbf{v}} + \bar{c}_2 \mathbf{v}$. Uguagliando le due relazioni e sfruttando l'indipendenza lineare di $\mathbf{v}$ e $\bar{\mathbf{v}}$, deduciamo che i coefficienti devono essere l'uno il coniugato dell'altro, cioè $c_2 = \bar{c}_1$. Possiamo quindi definire $c_1 = c$ e $c_2 = \bar{c}$

3. Sostituiamo ora queste relazioni nella formula generale dell'evoluzione al passo $n$, Eq. [](#eq:eulero_matrix), ottenendo
   $$\mathbf{y}_n = c \lambda^n \mathbf{v} + \bar{c} \bar{\lambda}^n \bar{\mathbf{v}}$$
   Poiché il prodotto di coniugati è il coniugato del prodotto, il secondo termine non è altro che il complesso coniugato del primo: $\bar{c} \bar{\lambda}^n \bar{\mathbf{v}} = \overline{c \lambda^n \mathbf{v}}$. L'equazione diventa quindi:
   $$\mathbf{y}_n = c \lambda^n \mathbf{v} + \overline{c \lambda^n \mathbf{v}}$$

Ricordando l'identità algebrica per cui la somma di un numero complesso e del suo coniugato è pari a due volte la sua parte reale ($z + \bar{z} = 2 \text{Re}(z)$), arriviamo al risultato finale:
$$\mathbf{y}_n = 2 \text{Re}\left( c \lambda^n \mathbf{v} \right)$$

Poiché la parte reale di qualunque quantità è, per definizione, un numero reale, lo stato del sistema $\mathbf{y}_n$ è garantito essere reale ad ogni istante di tempo.
```

Poiché per definizione di autovettore si ha $\hat{M}^n \mathbf{v} = \lambda^n \mathbf{v}$, otteniamo l'espressione formale per lo stato del sistema al passo $n$:

$$
\label{eq:evoluzione_autovalori}
\mathbf{y}_n = c_1 \lambda_1^n \mathbf{v}_1 + c_2 \lambda_2^n \mathbf{v}_2.
$$

Per comprendere a fondo il comportamento di questa equazione senza dover calcolare immediatamente $\lambda$ e $\mathbf{v}$, analizziamo il sistema da una prospettiva geometrica e strutturale, partendo dal determinante della matrice di evoluzione, che ha un significato geometrico profondo: rappresenta il fattore di scala con cui vengono modificate le aree (o i volumi) nello spazio delle fasi.

Consideriamo prima di tutto l'effetto che l'evoluzione temporale discreta ha sulla propagazione degli errori. Immaginiamo che a un certo passo $k$ l'elaboratore introduca un piccolissimo errore di arrotondamento $\boldsymbol{\delta}_k$ sullo stato del sistema (ad esempio, a causa della rappresentazione a precisione finita dei numeri in virgola mobile, che in doppia precisione hanno errori tipici dell'ordine di $\epsilon \sim 10^{-16}$).

Lo stato numerico reale al generico tempo $t_k$ diventa $\mathbf{y}_k + \boldsymbol{\delta}_k$. Se decomponiamo questa perturbazione microscopica nella base degli autovettori possiamo scrivere

$$
\boldsymbol{\delta}_k = \epsilon_1 \mathbf{v}_1 + \epsilon_2 \mathbf{v}_2.
$$

Dopo $m$ passi di calcolo, l'errore iniziale si sarà evoluto in:

$$
\label{eq:propagazione_errore}
\hat{M}^m \boldsymbol{\delta}_k = \epsilon_1 \lambda_1^m \mathbf{v}_1 + \epsilon_2 \lambda_2^m \mathbf{v}_2.
$$

Ipotizziamo che $\lambda_1 \geq \lambda_2$, e consideriamo il caso $|\lambda_1| > 1$. In queste condizioni, anche se l'errore iniziale $\epsilon_1$ è microscopicamente irrilevante (per esempio $\approx 10^{-16}$), il fattore $\lambda_1^m$, che cresce esponenzialmente con $m$, può portare il termine di errore $\epsilon_1 \lambda_1^m$ a diventare dello stesso ordine di grandezza del segnale fisico (si veda [il box qui sotto](#box:error_amplification) per una dimostrazione rigorosa). In questo regime, detto *di instabilità*, i risultati dell'integrazione numerica sono del tutto privi di senso. 

```{note} Dimostrazione dell'amplificazione dell'errore
:label: box:error_amplification

Per stimare la grandezza dell'errore dopo $m$ passi, prendiamo la norma di ambo i membri dell'equazione [](#eq:propagazione_errore):

$$
\label{eq:norma_propagazione_errore}
\|\hat{M}^m \boldsymbol{\delta}_k\| = \|\epsilon_1 \lambda_1^m \mathbf{v}_1 + \epsilon_2 \lambda_2^m \mathbf{v}_2\|
$$

che, se senza perdita di generalità assumiamo $|\lambda_1| \geq |\lambda_2|$ e $|\lambda_1| > 1$, diventa

$$
\|\hat{M}^m \boldsymbol{\delta}_k\| = |\lambda_1|^m \cdot \left\| \epsilon_1 \mathbf{v}_1 + \epsilon_2 \left(\frac{\lambda_2}{\lambda_1} \right)^m \mathbf{v}_2 \right\|.
$$

Poiché $|\lambda_1| > 1$, il fattore $|\lambda_1|^m$ diverge per $m \to \infty$. Per capire il comportamento del secondo fattore, consideriamo i due casi possibili per una matrice reale:

1. I due autovalori sono reali. In questo caso $(\lambda_2 / \lambda_1)^m \to 0$[^limite_uguali] per $m \to \infty$, e quindi:$$\lim_{m \to \infty} \|\hat{M}^m \boldsymbol{\delta}_k\| = \lim_{m \to \infty} |\lambda_1|^m \cdot \| \epsilon_1 \mathbf{v}_1\| = \infty.$$
2. I due autovalori sono complessi coniugati, e quindi hanno lo stesso modulo $\rho > 1.$ Possiamo esprimerli in forma polare come $\lambda_1 = \rho e^{i \theta}$ e $\lambda_2 = \rho e^{-i \theta}$, con $\theta \neq n\pi$ ($n \in \mathbb{Z}$). In questo caso il rapporto è $(\lambda_2 / \lambda_1)^m = e^{-2 i \theta m}$, un numero complesso di modulo 1 che, al variare di $m$, si muove lungo la circonferenza unitaria. Di conseguenza, il secondo fattore è una quantità reale e positiva $R(m) \in [R_{\rm min}, R_{\rm max}]$ (con $R_{\rm min} > 0$) che oscilla senza decadere. Valutando il limite tramite il [teorema del confronto](https://it.wikipedia.org/wiki/Teorema_del_confronto) si ottiene:
$$
\lim_{m \to \infty} \|\hat{M}^m \boldsymbol{\delta}_k\| \geq \lim_{m \to \infty} \rho^m \cdot R_{\rm min} = \infty.
$$

[^limite_uguali]: Oppure $\to 1$ se $\lambda_1 = \lambda_2$, che non cambia il limite per $m \to \infty$ dell'equazione [](#eq:norma_propagazione_errore).
```

Passiamo ora ad analizzare come la trasformazione determinata da $\hat{M}$ agisce nello spazio delle fasi. Se consideriamo una regione di condizioni iniziali che racchiude un'area $A_0$ (ad esempio, un quadratino di stati possibili), dopo un passo di integrazione questa regione si deformerà in un parallelogramma la cui area $A_1$ sarà pari a:

$$
A_1 = |\det(\hat{M})| A_0
$$

Nei sistemi fisici reali conservativi, l'evoluzione temporale non espande né contrae lo spazio delle fasi. Questa proprietà geometrica fondamentale è nota in meccanica classica come [teorema di Liouville](https://it.wikipedia.org/wiki/Teorema_di_Liouville_(meccanica_hamiltoniana)). Affinché un algoritmo numerico sia un buon modello della fisica reale, deve rispettare questa struttura.

Nel caso di uno spazio delle fasi bidimensionale (come nel nostro caso), la conservazione dell'area corrisponde a richiedere che[^simpletticità_generica]

$$
\label{eq:simpletticita}
\det(\hat{M}) = 1.
$$

```{note} Perché se $\det(\hat{M}) = 1$ l'area si conserva?

Possiamo dimostrare questa proprietà in modo semplice considerando una generica regione dello spazio delle fasi. Siano $\mathbf{u} = \begin{pmatrix} u_x \\ u_v \end{pmatrix}$ e $\mathbf{w} = \begin{pmatrix} w_x \\ w_v \end{pmatrix}$ due vettori linearmente indipendenti che definiscono i lati di un parallelogramma iniziale nello spazio delle fasi.

L'area $A_0$ di questo parallelogramma è pari al valore assoluto del determinante della matrice formata affiancando i due vettori:

$$A_0 = \left| \det \begin{pmatrix} u_x & w_x \\ u_v & w_v \end{pmatrix} \right| = |\det(\mathbf{u}, \mathbf{w})|$$

Applichiamo ora un passo di integrazione numerica tramite la matrice $\hat{M}$. I vettori $\mathbf{u}$ e $\mathbf{w}$ si trasformano rispettivamente in $\mathbf{u}' = \hat{M}\mathbf{u}$ e $\mathbf{w}' = \hat{M}\mathbf{w}$. La nuova area $A_1$ del parallelogramma deformato sarà:

$$A_1 = |\det(\mathbf{u}', \mathbf{w}')| = |\det(\hat{M}\mathbf{u}, \hat{M}\mathbf{w})|$$

Sfruttando le proprietà del prodotto tra matrici, la matrice affiancata $(\hat{M}\mathbf{u}, \hat{M}\mathbf{w})$ può essere scritta esattamente come il prodotto della matrice $\hat{M}$ per la matrice iniziale $(\mathbf{u}, \mathbf{w})$:

$$
(\hat{M}\mathbf{u}, \hat{M}\mathbf{w}) = \hat{M} \begin{pmatrix} u_x & w_x \\ u_v & w_v \end{pmatrix}.
$$

Grazie al [teorema di Binet](https://it.wikipedia.org/wiki/Teorema_di_Binet), il determinante del prodotto di due matrici è pari al prodotto dei loro determinanti:

$$A_1 = |\det(\hat{M} (\mathbf{u}, \mathbf{w}))| = |\det(\hat{M})| \cdot |\det(\mathbf{u}, \mathbf{w})| = |\det(\hat{M})| A_0$$

Se ne deduce che se $\det(\hat{M}) = 1$, allora $A_1 = A_0$ per qualunque scelta di condizioni iniziali. Il parallelogramma si deformerà (subendo allungamenti e rotazioni), ma la sua area rimarrà immutata a ogni passo temporale.
```

[^simpletticità_generica]: In spazi delle fasi a più dimensioni (sistemi con $N \ge 2$ gradi di libertà, dove lo spazio delle fasi ha dimensione $2N \ge 4$), la simpletticità è una condizione molto più restrittiva della semplice conservazione del volume. Un algoritmo simplettico deve conservare non solo il volume totale ($\det(\hat{M}) = 1$), ma anche le proiezioni delle aree orientate su tutte le coppie di piani coordinati coniugati $(x_i, p_i)$, dove $p_i$ è il *momento coniugato* a $x_i$.

Vediamo ora come si collega la conservazione dell'area con il comportamento dei singoli stati descritto dall'equazione [](#eq:evoluzione_autovalori). Dall'algebra lineare sappiamo che il determinante di una matrice è pari al prodotto dei suoi autovalori:

$$
\label{eq:det_eigenvalues}
\det(\hat{M}) = \lambda_1 \lambda_2.
$$

Discutiamo prima il caso in cui l'equazione [](#eq:simpletticita) non è rispettata. Se $\det(\hat{M}) < 1$, aree (o volumi) dello spazio delle fasi si contraggono man mano che si evolvono nel tempo. In questo caso l'equazione [](#eq:det_eigenvalues) implica che almeno uno degli autovalori è minore di uno. Se l'altro ha modulo maggiore di uno si ricade nell'amplificazione dell'errore discussa prima. Se invece entrambi gli autovalori hanno modulo minore di uno, i termini $\lambda^n$ tenderanno a zero per $n \to \infty$. L'evoluzione numerica smorzerà artificialmente le oscillazioni, comportandosi come se nel sistema fosse presente un attrito fittizio non fisico.

Di converso, se $\det(\hat{M}) > 1$, almeno uno degli autovalori deve avere modulo maggiore di 1 per via dell'equazione [](#eq:det_eigenvalues), e darà quindi luogo ad un'espansione verso l'infinito di aree (o volumi) dello spazio delle fasi, oltre che ad un'amplificazione incontrollata degli errori. In questo caso, dell'energia viene *immessa* artificialmente nel sistema.

D'altro canto, se l'equazione [](#eq:simpletticita) è rispettata, allora l'area occupata da un insieme di stati nello spazio delle fasi rimane rigorosamente costante nel tempo. La condizione $\det(\hat{M}) = 1$ è quindi una condizione necessaria per garantire la stabilità a lungo termine e la quasi-conservazione[^quasi_conservazione] dell'energia numerica, la cui violazione porta ad un'alterazione artificiale della fisica del sistema ad ogni passo temporale, con conseguenze più o meno gravi a seconda del sistema studiato. Questa proprietà geometrica è nota come simpletticità (e l'algoritmo di integrazione che ne è provvisto si dice *simplettico*). 

[^quasi_conservazione]: con *quasi conservazione* si intende quella proprietà per cui l'energia meccanica di un sistema fluttua intorno a un valore costante. Quando integriamo numericamente delle equazioni differenziali non possiamo sperare di fare meglio.

Per un integratore simplettico, gli autovalori sono rigidamente vincolati dalla relazione $\lambda_1 \lambda_2 = 1$. Questo vincolo fa sì che esistano diversi scenari da analizzare.

Consideriamo il caso di due autovalori reali e diversi da $1$. A causa del vincolo $\lambda_1 \lambda_2 = 1$, è impossibile che entrambi abbiano modulo unitario. Uno dei due autovalori (supponiamo $\lambda_1$) dovrà essere maggiore di $1$ in modulo, mentre l'altro ($\lambda_2$) dovrà essere minore di $1$. L'effetto geometrico combinato sulla dinamica del sistema prende il nome di *strain* (o deformazione a forbice):

* Lungo la direzione dell'autovettore $\mathbf{v}_1$, lo stato viene allungato esponenzialmente dal fattore $\lambda_1^n \to \infty$.
* Lungo la direzione dell'autovettore $\mathbf{v}_2$, lo stato viene compresso esponenzialmente a ogni passo dal fattore $\lambda_2^n \to 0$.

L'area totale del parallelogramma nello spazio delle fasi si conserva (poiché la compressione bilancia esattamente l'allungamento), ma la forma si allunga indefinitamente come una striscia infinitamente sottile e lunga. Fisicamente, il sistema diverge ed "esplode". In questo regime, l'algoritmo è numericamente instabile, in maniera del tutto simile al caso $\det(\hat{M}) > 1$.

Questa divergenza catastrofica viene evitata quando gli autovalori non sono reali ma complessi e coniugati: $\lambda_{1,2} = \lambda, \bar{\lambda}$. In questo caso, il vincolo del determinante si può scrivere come

$$
\lambda_1 \lambda_2 = \lambda \bar{\lambda} = |\lambda|^2 = 1 \implies |\lambda| = 1,
$$

cioè il modulo di entrambi gli autovalori deve essere esattamente pari a 1. Possiamo quindi scrivere gli autovalori in forma polare come $\lambda_{1,2} = e^{\pm i \theta}$, che mostra esplicitamente come l'evoluzione temporale rappresenti una *pura rotazione periodica* nel piano complesso. Le traiettorie rimangono limitate e la simulazione è numericamente stabile: gli errori di arrotondamento non vengono amplificati, ma si limitano a oscillare insieme al sistema.

Per visualizzare concretamente il legame profondo tra la conservazione dell'area e la stabilità numerica, analizziamo ora l'animazione mostrata in Figura [](#fig:euler_volume_conservation), che confronta l'evoluzione di una regione dello spazio delle fasi secondo i metodi di Eulero ed Eulero-Cromer.

```{figure} #cell:euler_volume_conservation
:label: fig:euler_volume_conservation
:align: center

L'evoluzione di un volume di spazio delle fasi (che per l'oscillatore armonico è un piano) delimitato da un rettangolo ottenuto con i metodi di Eulero (in rosso) ed Eulero-Cromer (in blue). I parametri della simulazione sono $k = 1$, $m = 1$ (quindi $\omega = 1$) e $\Delta t = 0.1$.
```

L'animazione mostra come nelle condizioni di simulazione (cioè per i valori di $\omega$ e $\Delta t$ utilizzati), l'algoritmo di Eulero mostra un'espansione dell'area dello spazio delle fasi, che invece non si verifica con Eulero-Cromer. Verifichiamo questi comportamenti calcolando esplicitamente determinanti ed autovalori associati all'oscillatore armonico integrato con i due metodi.

### Eulero

La matrice di propagazione per il metodo di Eulero è

$$
\hat{M}_E = 
\begin{pmatrix} 1 & \Delta t \\
-\omega_0^2 \Delta t & 1,
\end{pmatrix}
$$

da cui possiamo immediatamente ottenere il determinante:

$$
\det(\hat{M}_E) = 1 \cdot 1 - (\Delta t)(-\omega_0^2 \Delta t) = 1 + \omega_0^2 \Delta t^2.
$$

Poiché $\Delta t > 0$ e $\omega_0 > 0$, si ha che  $\det(\hat{M}_E) > 1$ per qualunque valore di $\Delta t$. Essendo il modulo strettamente maggiore di 1, l'errore globale cresce esponenzialmente a ogni passo temporale. Il metodo è quindi incondizionatamente instabile per l'oscillatore armonico; nello spazio delle fasi, la soluzione numerica descrive una spirale che diverge verso l'infinito, accumulando energia artificiale.

Poiché $\omega_0^2 \Delta t^2$ è un numero strettamente positivo, il determinante della matrice è sempre maggiore di 1. Di conseguenza, il metodo di Eulero è **incondizionatamente instabile** per l'oscillatore armonico: l'ampiezza delle oscillazioni numeriche crescerà artificialmente all'infinito per qualunque scelta di $\Delta t$. Nello spazio delle fasi, questo comportamento si manifesta come mostrato in figura [](#fig:euler_volume_conservation): la soluzione numerica descrive una spirale che diverge verso l'infinito, accumulando energia artificiale.

Calcoliamo ora gli autovalori di $\hat{M}_E$. Risolvendo il polinomio caratteristico $\det(\hat{M}_E - \lambda \hat{I}) = (1-\lambda)^2 + \omega_0^2 \Delta t^2 = 0$ si trova $(1-\lambda)^2 = -\omega_0^2 \Delta t^2$, da cui si ottengono i due autovalori complessi coniugati

$$
\lambda_{1,2} = 1 \pm i \omega_0 \Delta t.
$$

Poiché sono complessi coniugati, i due autovalori hanno lo stesso modulo, che vale[^lambda2_equal_det]

$$
|\lambda_1| = |\lambda_2| = \sqrt{1 + \omega_0^2 \Delta t^2},
$$

cioè un numero maggiore di 1, indipendentemente dal passo di integrazione. Come abbiamo dimostrato precedentemente, se il modulo degli autovalori è strettamente maggiore di 1, l'errore cresce esponenzialmente, dimostrando ancora una volta l'instabilità del metodo di Eulero.

[^lambda2_equal_det]: Questo risultato si può ottenere immediatamente ricordando che $\det(\hat{M}) = \lambda_1 \lambda_2$.

### Eulero-Cromer

Nel caso di Eulero-Cromer, la matrice di propagazione del metodo nello spazio delle fasi è

$$
M_{EC} = 
\begin{pmatrix}
1 - \omega_0^2 \Delta t^2 & \Delta t \\
-\omega_0^2 \Delta t & 1,
\end{pmatrix}
$$

che ha determinante

$$
\det(M_{EC}) = (1 - \omega_0^2 \Delta t^2)(1) - (\Delta t)(-\omega_0^2 \Delta t) = 1 - \omega_0^2 \Delta t^2 + \omega_0^2 \Delta t^2 = 1
$$

Poiché $\det(M_{EC}) = 1$, il metodo conserva l'area nello spazio delle fasi, che per sistemi unidimensionali come l'oscillatore armonico implica simpletticità. Questo garantisce l'assenza di derive energetiche artificiali a lungo termine. In questo caso, il polinomio caratteristico è 

$$
\label{eq:characteristic_pol_ec}
\lambda^2 - (2 - \omega_0^2 \Delta t^2)\lambda + 1 = 0,
$$

da cui si ottengono gli autovalori 

$$
\lambda_{1,2} = \frac{(2-\omega_0^2 \Delta t^2) \pm \sqrt{(2-\omega_0^2 \Delta t^2)^2 - 4}}{2} = \frac{(2-\omega_0^2 \Delta t^2) \pm \omega_0 \Delta t \sqrt{\omega_0^2 \Delta t^2 - 4}}{2}.
$$

Il comportamento del sistema dipende dal segno del radicando ($\omega_0^2 \Delta t^2 - 4$):

1. $\omega_0 \Delta t < 2$. Il radicando è negativo, producendo autovalori complessi coniugati. Poiché il determinante è unitario, e in forza all'equazione [](#eq:det_eigenvalues), i due autovalori devono avere anche modulo 1, e quindi trovarsi sulla circonferenza unitaria. In questo regime il metodo è stabile e genera orbite ellittiche chiuse nello spazio delle fasi.
2. $\omega_0 \Delta t > 2$. Il radicando è positivo, quindi i due autovalori sono reali e distinti. Poiché il loro prodotto deve rimanere pari a $1$, uno dei due autovalori sarà necessariamente maggiore di 1 in modulo: il sistema diventa instabile e l'errore diverge esponenzialmente. Questa dipendenza della stabilità dai parametri del sistema (e dell'integrazione numerica) fa sì che il metodo di Eulero-Cromer sia **condizionatamente stabile**. Nel caso in esame, la condizione di stabilità matematica, che richiede che gli autovalori abbiano modulo 1, impone infatti un limite superiore rigoroso al passo temporale:
$$
\label{eq:stability_eulero_cromer}
\Delta t < \frac{2}{\omega_0}.
$$

## Accuratezza

Per valutare la bontà (e quindi l'accuratezza) di un metodo di integrazione numerica è fondamentale distinguere tra due definizioni di errore:

* Errore di Troncamento Locale (LTE): rappresenta l'errore introdotto dal metodo in un singolo passo temporale $\Delta t$, assumendo che tutti i dati al passo precedente siano esatti. Si esprime matematicamente come la differenza tra la soluzione esatta del sistema continuo e quella fornita dallo schema numerico dopo un passo.
* Errore Globale: rappresenta l'errore totale accumulato dall'inizio della simulazione fino al tempo finale $T$. Se l'errore locale è dell'ordine di $O(\Delta t^{p+1})$, su un intervallo di tempo limitato $T$ (che richiede un numero di passi pari a $m = T/\Delta t$) l'errore globale scala come $m \cdot O(\Delta t^{p+1}) = O(\Delta t^p)$. L'esponente $p$ definisce l'ordine di accuratezza del metodo.

### Eulero 

La derivazione dell'accuratezza per il metodo di Eulero discende direttamente dallo sviluppo in serie di Taylor di posizione $x(t)$ e velocità $v(t)$ attorno all'istante $t_n$:

$$
\begin{align}
x(t_{n+1}) & = x(t_n) + v(t_n) \Delta t + \frac{1}{2} a(t_n)  \Delta t^2+ O(\Delta t^3)\\
v(t_{n+1}) & = v(t_n) + a(t_n)  \Delta t+ \frac{1}{2} \frac{da(t_n)}{dt} \Delta t^2 + O(\Delta t^3).
\end{align}
$$

Confrontando queste espressioni con le equazioni di aggiornamento dello schema di Eulero, eq. [](#eq:eulero), si nota immediatamente che lo schema numerico recide i termini di Taylor a partire dal secondo ordine. Definendo l'errore come la differenza tra il dato locale esatto e quello ottenuto numericamente, l'errore locale di troncamento risulta:

$$
\begin{align}
x(t_{n+1}) - x_{n+1} &= \frac{1}{2} a(t_n) + O(\Delta t^3) \Delta t^2 = O(\Delta t^2)\\
v(t_{n+1}) - v_{n+1} &= \frac{1}{2} \frac{da}{dt}(t_n) \Delta t^2 + O(\Delta t^3) = O(\Delta t^2).
\end{align}
$$

Poiché l'errore locale è $O(\Delta t^2)$, l'accumulo globale su $N \propto 1/\Delta t$ passi produce un errore complessivo di ordine $O(\Delta t)$. Eulero Esplicito è pertanto un metodo del primo ordine.

### Eulero-Cromer

Nel caso di Eulero-Cromer, lo schema definito in eq. [](#eq:eulero_cromer) fa uso della velocità aggiornata al tempo successivo per calcolare la nuova posizione. Mentre la relazione per l'aggiornamento di $v_{n+1}$ è identica a quella di Eulero Esplicito, e di conseguenza preserva un errore locale pari a $O(\Delta t^2)$, l'analisi della posizione richiede cautela. Sostituendo $v_{n+1}$ nella definizione di $x_{n+1}$, possiamo scrivere l'espressione per la variabile $x_{n+1}$ in funzione delle sole quantità al tempo $t_n$:

$$
x_{n+1} = x_n + \Delta t (v_n + \Delta t a_n) = x_n + \Delta t v_n + \Delta t^2 a_n.
$$

Confrontiamo ora questa equazione dello schema con lo sviluppo esatto di Taylor di $x(t_{n+1})$ ricavato in precedenza. Calcolando la differenza, si ottiene l'errore di troncamento locale sulla posizione:

$$
x(t_{n+1}) - x_{n+1} = x(t_n) + \Delta t v(t_n) + \frac{\Delta t^2}{2} a(t_n) + O(\Delta t^3) - x_n + \Delta t v_n + \Delta t^2 a_n.
$$

Imponendo l'esattezza dei dati al passo $n$, i termini di ordine zero e primo si cancellano, lasciando la discrepanza unicamente sul coefficiente del secondo ordine:

$$
x(t_{n+1}) - x_{n+1} = -\frac{1}{2} \Delta t^2 a(t_n) + O(\Delta t^3) = -\frac{\Delta t^2}{2} a(t_n) + O(\Delta t^3) = O(\Delta t^2).
$$

Poiché l'errore locale di troncamento è pari a $O(\Delta t^2)$ sia per la velocità che per la posizione, l'integrazione accumula un errore globale proporzionale a $O(\Delta t)$, esattamente come per il metodo di Eulero. Quindi, nonostante l'utilizzo di informazioni temporalmente più avanzate per la coordinata spaziale, il metodo di Eulero-Cromer rimane un metodo del primo ordine. L'errore locale sulla posizione ha lo stesso modulo di quello di Eulero Esplicito, ma segno opposto.

```{figure} #cell:error_eulero
:label: fig:error_eulero
:align: center

La differenza tra la posizione finale teorica e quella ottenuta tramite i due algoritmi di Eulero ed Eulero-Cromer. Il tempo totale di simulazione è $t_f = 20$, mentre i parametri utilizzati sono $\omega_0 = 1$, $x_0 = 2$, $v_0 = 1$.
```

Se la soluzione teorica è nota (come in questo caso), l'errore si può anche calcolare direttamente dai risultati numerici. Possiamo infatti definire l'errore globale come 

$$
\epsilon_G = |x_f - x(t_f)|,
$$

dove $t_f$ è il tempo finale, mentre $x_f$ e $x(t_f)$ sono le posizioni ottenute numericamente e teoricamente. La [](fig:error_eulero) mostra $\epsilon_G$ per sistemi con $\omega_0 = 1$, $x_0 = 2$ e $v_0 = 1$, simulati per $t_f = 20$ (in unità adimensionali) con Eulero ed Eulero-Cromer. La figura mostra come l'errore che commette Eulero-Cromer sia, in questo caso, di più di un ordine di grandezza minore rispetto a quello che si ottiene con Eulero, ma la dipendenza da $\Delta t$ è la stessa. Per grandi valori di $\Delta t$ l'errore di Eulero sembra crescere più che linearmente, per via dell'instabilità del metodo. Questo comportamento non si verifica con Eulero-Cromer dato che tutti i valori di $\Delta t$ considerati rispecchiano la relazione di stabilità condizionata, eq. [](#eq:stability_eulero_cromer).

# Velocity Verlet

Introduciamo ora uno dei metodi simplettici, cioè che conserva l'energia e il volume nello spazio delle fasi, migliori e più utilizzati. Sviluppiamo la posizione $x(t)$ in serie di Taylor attorno a $t$:

$$
\begin{aligned}
x(t + \Delta t) = x(t) + v(t) \Delta t + \frac{1}{2} a(t) \Delta t^2 + \frac{1}{6} \od{a}{t} \Delta t^3 + \mathcal{O}(\Delta t^4)\\
x(t - \Delta t) = x(t) - v(t) \Delta t + \frac{1}{2} a(t) \Delta t^2 - \frac{1}{6} \od{a}{t} \Delta t^3 + \mathcal{O}(\Delta t^4)
\end{aligned}
$$

Sommando i due sviluppi notiamo che, per simmetria, i termini con potenze dispari di $\Delta t$ si elidono e si ottiene

$$
x(t + \Delta t) + x(t - \Delta t) = 2x(t) + a(t) \Delta t^2 + \mathcal{O}(\Delta t^4).
$$

Se trascuriamo i termini di ordine superiore $\mathcal{O}(\Delta t^4)$ e discretizziamo il tempo, $t \to t_n$, l'aggiornamento della posizione diventa

$$
x_{n+1} = 2x_n - x_{n-1} + a_n \Delta t^2.
$$

Questo è l'algoritmo di Verlet, che permette di calcolare la posizione $x_{n+1}$ al passo temporale successivo utilizzando la posizione corrente $x_n$, la posizione precedente $ x_{n-1}$ e l'accelerazione corrente $a_n$. Sebbene scritto in questo modo il metodo di Verlet non coinvolge esplicitamente la velocità, se consideriamo lo sviluppo fino al secondo ordine possiamo scrivere esplicitamente

$$
v_n = \frac{x_{n+1} - x_{n-1}}{2\Delta t} + \mathcal{O}(\Delta t^2),
$$

dove è importante la differenza di accuratezza ($\mathcal{O}(\Delta t^2)$ *vs* $\mathcal{O}(\Delta t^4)$) rispetto a $x$. Inoltre, per $\Delta t$ sufficientemente piccolo, $x_{n+1}$ e $x_{n-1}$ saranno molto simili, quindi la loro differenza può dare problemi quando i numeri vengono rappresentanti sul calcolatore. Per questo motivo il metodo è raramente usato in questa forma, ma si modifica per includere un aggiornamento esplicito per la velocità, ottenendo il ben più comune metodo "Velocity Verlet". Invece di basarsi sulle posizioni dei passi temporali precedente e corrente, l'algoritmo Velocity Verlet aggiorna la posizione e la velocità in un processo a due fasi.

In primo luogo, utilizziamo la velocità e l'accelerazione correnti per aggiornare la posizione al tempo $t + \Delta t$. Questo viene fatto in modo simile al metodo Verlet di base, ma con il termine della velocità esplicitamente incluso:

$$
\label{eq:velocity_verlet_x}
x_{n+1} = x_n + v_n \Delta t + \frac{1}{2} a_n \Delta t^2
$$

Questa equazione utilizza la posizione corrente $x_n$, la velocità corrente $v_n$ e l'accelerazione corrente $a_n$ per calcolare la nuova posizione $x_{n+1}$. Successivamente, dopo aver aggiornato la posizione, dobbiamo calcolare la nuova accelerazione al tempo $t_{n+1}$ perché la forza (e quindi l'accelerazione) è cambiata a causa della posizione aggiornata. La nuova accelerazione è data da:

$$
a_{n+1} = \frac{F_{n+1}}{m}
$$

Avendo a disposizione questa nuova accelerazione, possiamo aggiornare la velocità. Invece di usare solo l'accelerazione corrente, il metodo Velocity Verlet usa la media delle accelerazioni corrente e nuova per aggiornare la velocità:

$$
v_{n+1} = v_n + \frac{1}{2} (a_n + a_{n+1}) \Delta t
$$ (eq:velocity_verlet_v)

Questa equazione di aggiornamento della velocità tiene conto della variazione dell'accelerazione nell'intervallo di tempo, fornendo un aggiornamento della velocità più accurato rispetto al semplice utilizzo dell'accelerazione corrente. Il metodo Velocity Verlet è lo standard *de facto* per i codici di [Dinamica Molecolare](https://it.wikipedia.org/wiki/Dinamica_molecolare) (MD), una tecnica utilizzata per studiare la dinamica e la termodinamica di atomi, molecole, colloidi, *ecc*. Il modo comune per implementarlo consiste nel suddividere la fase di integrazione della velocità in due, in modo che un passo di integrazione completo diventi:

1. Aggiornamento della velocità, prima fase: $v_{n+1/2} = v_n + \frac{1}{2} a_n \Delta t$.
2. Aggiornamento della posizione: $x_{n+1} = x_n + v_{n+1/2}\Delta t = x(t) + v_n \Delta t + \frac{1}{2} a_n \Delta t^2$ (cioè l'eq. [](#eq:velocity_verlet_x)).
3. Calcolo della forza (e quindi dell'accelerazione) utilizzando la nuova posizione: $x_{n+1} \to a_{n+1} = F_{n+1} / m$.
4. Aggiornamento della velocità, seconda fase: $v_{n+1} = v_{n+1/2} + \frac{1}{2} a_{n+1}\Delta t = v_n + \frac{1}{2} (a_n + a_{n+1}) \Delta t$ (cioè l'eq. [](#eq:velocity_verlet_v)).

### Stabilità ed accuratezza

Consideriamo le equazioni di aggiornamento del metodo Velocity Verlet:

$$
\label{eq:velocity_verlet}
\begin{cases}
x_{n+1} &= x_n + v_n \Delta t + \frac{1}{2} a_n \Delta t^2\\
v_{n+1} &= v_n + \frac{1}{2} (a_n + a_{n+1}) \Delta t.
\end{cases}
$$

Nel caso dell'oscillatore armonico, $a_n = -\omega_0 x_n$ e $a_{n+1} = -\omega_0 x_{n+1}$, quindi

$$
\begin{cases}
x_{n+1} &= x_n + v_n \Delta t - \frac{1}{2} \omega_0 x_n \Delta t^2\\
v_{n+1} &= v_n - \frac{1}{2} \omega_0(x_n + x_{n+1}) \Delta t.
\end{cases}
$$

Sostituendo la prima equazione nella seconda otteniamo

$$
v_{n+1} = v_n - \frac{1}{2} \omega_0^2 \Delta t x_n - \frac{1}{2} \omega_0^2 \Delta t \left[ \left(1 - \frac{1}{2} \omega_0^2 \Delta t^2\right) x_n + \Delta t v_n \right]$$

che, raccogliendo i termini associati a $x_n$ e $v_n$, diventa:

$$
v_{n+1} = -\omega_0^2 \Delta t \left(1 - \frac{1}{4} \omega_0^2 \Delta t^2\right) x_n + \left(1 - \frac{1}{2} \omega_0^2 \Delta t^2\right) v_n.
$$

La matrice di propagazione è quindi

$$
\label{eq:verlet_matrix}
\hat{M}_{VV} = 
\begin{pmatrix}
1 - \frac{1}{2} \omega^2 \Delta t^2 & \Delta t \\
-\omega^2 \Delta t \left(1 - \frac{1}{4} \omega^2 \Delta t^2\right) & 1 - \frac{1}{2} \omega^2 \Delta t^2.
\end{pmatrix}
$$

Calcoliamo il determinante della matrice:

$$
\begin{align}
\det(\hat{M}_{\text{VV}}) &= \left(1 - \frac{1}{2} \omega^2 \Delta t^2\right)^2 - \left[ -\omega^2 \Delta t^2 \left(1 - \frac{1}{4} \omega^2 \Delta t^2\right) \right] = \\
&= \left( 1 - \omega^2 \Delta t^2 + \frac{1}{4} \omega^4 \Delta t^4 \right) + \omega^2 \Delta t^2 - \frac{1}{4} \omega^4 \Delta t^4
\\
&= 1
\end{align}
$$

Quindi, il determinante è esattamente pari a 1, indipendentemente dal valore del passo temporale $\Delta t$: Velocity Verlet è un algoritmo simplettico (conserva l'area nello spazio delle fasi) per qualsiasi parametro di discretizzazione scelto. Non introduce alcuna dissipazione o amplificazione artificiale dell'area di stati iniziali. 

Studiato ora gli autovalori. Il polinomio caratteristico di Velocity Verlet è:

$$
\label{eq:char_verlet}
\lambda^2 - \left(2 - \omega_0^2 \Delta t^2\right)\lambda + 1 = 0.
$$

Questa equazione vi ricorda qualcosa? Questa equazione è assolutamente identica a quella ottenuta con il metodo di Eulero-Cromer, eq. [](#eq:characteristic_pol_ec)!

Sebbene le due matrici di evoluzione siano diverse (Velocity Verlet ha coefficienti simmetrici sulla diagonale ed è un metodo del secondo ordine, mentre Eulero-Cromer è asimmetrico ed è del primo ordine), esse condividono lo stesso polinomio caratteristico e di conseguenza hanno gli stessi identici autovalori e quindi la stessa condizione di stabilità, eq. [](#eq:stability_eulero_cromer). La differenza maggiore tra i due metodi è nella loro accuratezza. Utilizzando la stessa logica applicata a Eulero ed Eulero-Cromer, definiamo l'errore di troncamento locale come la differenza tra la soluzione numerica e quella teorica dopo un passo di integrazione. Sviluppando posizione e velocità fino al quarto ordine, scriviamo le soluzioni esatte come

$$
\begin{cases}
x(t_{n+1}) & = x(t_n) + v(t_n) \Delta t + \frac{1}{2} a(t_n) \Delta t^2 + \frac{}{6} \frac{da(t_n)}{dt} \Delta t^3 + O(\Delta t^4)\\
v(t_{n+1}) & = v(t_n) + a(t_n) \Delta t + \frac{1}{2} \frac{da(t_n)}{dt} \Delta t^2 + \frac{1}{6} \frac{d^2a(t_n)}{dt^2} \Delta t^3 + O(\Delta t^4).
\end{cases}
$$

Se ora sottriamo queste quantità da quelle numeriche, eq. [](#eq:velocity_verlet), e sostituiamo a $a_{n+1}$ il suo sviluppo di Taylor, $a_{n+1} = a(t_n) + \dot{a}(t_n) \Delta t + \frac{1}{2} \ddot{a}(t_n) \Delta t^2 + \mathcal{O}(\Delta t^3)$[^box_an1], otteniamo

$$
\begin{cases}
x_{n+1} - x(t_n + \Delta t) &= -\frac{1}{6} \frac{d^3 x}{dt^3}(t_n) \Delta t^3 + \mathcal{O}(\Delta t^4)\\
v_{n+1} - v(t_n + \Delta t) &= \frac{1}{12} \ddot{a}(t_n) \Delta t^3 + \mathcal{O}(\Delta t^4),
\end{cases}
$$

e quindi sia per la posizione che per la velocità, l'errore locale dell'algoritmo di Verlet (e quindi, equivalentemente, quello di Velocity Verlet) è di ordine $\mathcal{O}(\Delta t^3)$, che implica come per l'errore globale scali come $\mathcal{O}(\Delta t^2)$.

```{note} Perché possiamo sviluppare $a_{n+1}$?
:label: box:an1
:class: dropdown

Potrebbe sorgere un legittimo dubbio teorico: l'accelerazione futura $a_{n+1}$ è calcolata dall'algoritmo e quindi, per definizione, valutata sulla posizione numerica approssimata ($a_{n+1} = a(x_{n+1})$) e non sulla posizione reale lungo la traiettoria fisica ($a(x(t_n + \Delta t))$). Com'è possibile allora sviluppare $a_{n+1}$ in serie di Taylor nel tempo come se ci trovassimo sulla traiettoria esatta?

La giustificazione formale risiede nell'ordine dell'errore locale spaziale. Abbiamo appena dimostrato che l'errore sulla posizione al passo $n+1$ è di terzo ordine:

$$
x_{n+1} = x(t_n + \Delta t) + \mathcal{O}(\Delta t^3).
$$

Se effettuiamo uno sviluppo spaziale in serie di Taylor della funzione continua $a(x)$ attorno al punto esatto $x(t_n + \Delta t)$, otteniamo:

$$
a(x_{n+1}) = a\left( x(t_n + \Delta t) + \mathcal{O}(\Delta t^3) \right) = a(x(t_n + \Delta t)) + a'(x(t_n + \Delta t)) \cdot \mathcal{O}(\Delta t^3) + \dots
$$

Poiché la differenza tra la coordinata numerica e quella reale è già di ordine $\mathcal{O}(\Delta t^3)$, l'errore derivante dal non valutare l'accelerazione sulla traiettoria esatta è di ordine superiore e finisce interamente nel termine di errore generico:

$$
a(x_{n+1}) = a(x(t_n + \Delta t)) + \mathcal{O}(\Delta t^3)
$$

Di conseguenza, per ricavare i termini d'errore fino all'ordine $\Delta t^2$ necessari alla nostra dimostrazione, è perfettamente lecito sostituire ad $a_{n+1}$ lo sviluppo temporale esatto dell'accelerazione fisica:

$$
a(x(t_n + \Delta t)) = a_n + \dot{a}_n \Delta t + \frac{1}{2} \ddot{a}_n \Delta t^2 + \mathcal{O}(\Delta t^3).
$$

L'approssimazione numerica spaziale non altera i coefficienti dei termini di ordine inferiore dello sviluppo.
```

[^box_an1]: Si veda il [box più in basso](#box:an1) sul perché possiamo farlo.

```{figure} #cell:error_velocity_verlet
:label: fig:error_velocity_verlet
:align: center

Come in [](#fig:error_eulero), con, in aggiunta, l'errore ottenuto applicando l'algoritmo di Velocity Verlet.
```

La [](#fig:error_velocity_verlet) illustra vividamente l'enorme impatto del passaggio da un errore globale di ordine $\mathcal{O}(\Delta t)$ a uno di ordine $\mathcal{O}(\Delta t^2)$. Per apprezzare concretamente questa differenza, si consideri un passo temporale tipico delle simulazioni reali, ad esempio $\Delta t = 10^{-3}$: in questo scenario, l'accuratezza di Velocity Verlet supera quella di Eulero-Cromer di ben tre ordini di grandezza, riducendo drasticamente l'errore sistematico accumulato sulla traiettoria.

# Runge-Kutta

## Metodo Runge-Kutta del secondo ordine (RK2)

I metodi introdotti finora approssimano il valore medio della derivata nell'intervallo $[t_n,t_{n+1}]$ utilizzando informazioni disponibili agli estremi dell'intervallo stesso. Possiamo però ottenere una stima più accurata osservando che, per una funzione sufficientemente regolare, il valore della derivata nel punto medio dell'intervallo costituisce spesso una buona approssimazione del suo valore medio.

L'idea alla base del metodo Runge-Kutta del secondo ordine consiste quindi nello stimare la derivata nel punto medio dell'intervallo e utilizzarla per aggiornare la soluzione. Partendo dal valore noto $x_n$, si esegue innanzitutto un mezzo passo con il metodo di Eulero:

$$
\begin{cases}
x'_n = f(x_n,t_n)\\
x_{n+\frac{1}{2}} = x_n + x'_n\frac{\Delta t}{2}.
\end{cases}
$$

Questa quantità fornisce una stima della soluzione al tempo $t_{n+\frac12} = t_n + \Delta t / 2$. Possiamo quindi calcolare una nuova stima della derivata nel punto medio:

$$
x'_{n+1/2} = f\left(x_{n+\frac12}, t_{n+\frac12}\right).
$$

Infine, utilizziamo questa derivata per avanzare di un passo completo:

$$
x_{n+1} = x_n + x'_{n+1/2} \Delta t.
$$

Ricordando che, nel nostro caso, $x'(t) = v(t)$, applicando il metodo al nostro sistema di equazioni otteniamo

$$
\begin{cases}
x_{n+1} = x_n + \left(v_n + a_n \frac{\Delta t}{2}\right) \Delta t\\
v_{n+1} = v_n + a_{n+1/2} \Delta t,
\end{cases}
$$

dove $a_{n+1/2} = a(x_{n+1/2}, v_{v+1/2}, t_{n+1/2})$ è l'accelerazione calcolata nel punto medio dell'intervallo, ottenuta integrando di mezzo passo $x_n$ e $v_n$. Calcolare due volte l'accelerazione (in $n$ e in $n + 1/2$) è il prezzo computazionale che si paga per migliorare l'accuratezza rispetto al metodo di Eulero. Questo è un prezzo che molte volte (ma non necessariamente sempre) vale la pena di pagare.

## Metodo Runge-Kutta del quarto ordine (RK4)

Il metodo RK2 migliora l'accuratezza dell'integrazione utilizzando una stima della derivata nel punto medio dell'intervallo. Possiamo però ottenere una stima ancora più accurata del valore medio della derivata combinando informazioni provenienti da più punti dell'intervallo stesso.

L'idea alla base del metodo Runge-Kutta del quarto ordine consiste nel costruire una successione di stime della derivata e combinarle opportunamente per ottenere una migliore approssimazione del valore medio di $f(x,t)$ tra $t_n$ e $t_{n+1}$.

Si definiscono innanzitutto quattro stime della derivata:

$$
k_1 = f(x_n,t_n),
$$

che rappresenta la derivata all'inizio dell'intervallo,

$$
k_2 = f \left(x_n+k_1\frac{\Delta t}{2}, t_{n+1/2}\right),
$$

che fornisce una prima stima della derivata nel punto medio,

$$
k_3 = f \left(x_n+k_2 \frac{\Delta t}{2}, t_{n+1/2}\right),
$$

che costituisce una stima migliorata della derivata nel punto medio,

e infine

$$
k_4 = f\left(x_n+k_3 \Delta t ,t_{n+1}\right),
$$

che rappresenta una stima della derivata alla fine dell'intervallo.

Queste quattro quantità, pesate opportunamente, si possono combinare per ottenere una stima della derivata media:

$$
\label{eq:RK4_f_avg}
\langle f \rangle_n \approx \sum_{i=1}^4 b_i k_i = b_1 k_1 + b_2 k_2 + b_3 k_3 + b_4 k_4,
$$

dove i $b_i$ vanno scelti in modo da minimizzare l'errore. Si può dimostrare (vedi box sotto per una derivazione semplificata) che fissando $b_1 = b_4 = 1/6$ e $b_2 = b_3 = 1/3$ il metodo fornisce una stima particolarmente accurata della derivata media nell'intervallo, raggiungendo un'accuratezza molto superiore rispetto a Eulero ed RK2, al costo di quattro valutazioni della funzione $f$ per ogni passo temporale.

L'aggiornamento della soluzione assume pertanto la forma

$$
\label{eq:RK4}
x_{n+1} = x_n + \frac{\Delta t}{6} (k_1 + 2k_2 + 2k_3 + k_4).
$$

```{tip}  Approfondimento: determinazione dei coefficienti $b_i$

Vogliamo capire in modo più preciso da dove provengono i pesi

$$
\label{eq:RK4_coeffs}
b_1 = \frac{1}{6}, \quad b_2 = \frac{1}{3}, \quad b_3 = \frac{1}{3}, \quad b_4 = \frac{1}{6}
$$

che compaiono nel metodo RK4.

Consideriamo la forma generale dell'aggiornamento, eq. [](#eq:RK4),

$$
x_{n+1} = x_n + \Delta t (b_1 k_1 + b_2 k_2 + b_3 k_3 + b_4 k_4).
$$

Dimostriamo come ottenere i valori dei coefficienti, eq. [](#eq:RK4_coeffs), nel caso semplice in cui la derivata dipende soltanto dal tempo, $x' = f(t)$. In questo caso le quantità $k_i$ diventano semplicemente

$$
k_i = f(t_n+c_i\Delta t),
$$

dove non abbiamo ancora fissato i coefficienti $c_i$, che determinano i punti in cui calcoliamo le derivate.

L'aggiornamento numerico diventa quindi

$$
\label{eq:RK4_formal_update}
x_{n+1} = x_n + \Delta t \sum_{i=1}^{4} b_i f(t_n + c_i\Delta t).
$$

Espandiamo ora ciascun termine in serie di Taylor attorno a $t_n$ fino al terzo ordine:

$$
\begin{split}
f(t_n+c_i\Delta t) = & f(t_n) + c_i\Delta t f'(t_n) + \frac{c_i^2\Delta t^2}{2}f''(t_n) + \\
& \frac{c_i^3\Delta t^3}{6}f'''(t_n) + O(\Delta t^4).
\end{split}
$$

Sostituendo nell'aggiornamento numerico, eq. [](#eq:RK4_formal_update), otteniamo

$$
\begin{split}
x_{n+1} = & x_n + \Delta t \left[ \left(\sum_i b_i\right) f(t_n) + \Delta t \left(\sum_i b_i c_i\right) f'(t_n) + \right. \\
& \left.\frac{\Delta t^2}{2} \left(\sum_i b_i c_i^2\right) f''(t_n) + \frac{\Delta t^3}{6} \left(\sum_i b_i c_i^3\right) f'''(t_n) \right] +
O(\Delta t^5).
\end{split}
$$

D'altra parte, la soluzione esatta soddisfa l'equazione [](#eq:ODE_full_solution), che espansa in serie di Taylor al quarto ordine diventa

$$
\begin{split}
x(t_{n+1}) = & x(t_n) + \Delta t f(t_n) + \frac{\Delta t^2}{2}f'(t_n) + \frac{\Delta t^3}{6}f''(t_n) + \\
& \frac{\Delta t^4}{24}f'''(t_n) + O(\Delta t^5).
\end{split}
$$

Affinché il metodo numerico riproduca questa espansione fino ai termini di ordine $\Delta t^4$, dobbiamo imporre

$$
\label{eq:b_system}
\begin{cases}
\sum_i b_i = 1\\
\sum_i b_i c_i = \frac{1}{2}\\
\sum_i b_i c_i^2 = \frac{1}{3}\\
\sum_i b_i c_i^3 = \frac{1}{4}.
\end{cases}
$$

Se ora fissiamo i valori dei punti in cui calcoliamo le derivate, $c_1 = 0$, $c_2 = c_3 = 1/2$, $c_4 = 1$, le condizioni [](#eq:b_system) diventano

$$
\begin{cases}
b_1+b_2+b_3+b_4=1\\
\frac{1}{2}b_2+\frac{1}{2}b_3+b_4=\frac{1}{2}\\
\frac{1}{4}b_2+\frac{1}{4}b_3+b_4=\frac{1}{3}\\
\frac{1}{8}b_2+\frac{1}{8}b_3+b_4=\frac{1}{4}.
\end{cases}
$$

Ponendo $s \equiv b_2+b_3$ e sottraendo la terza equazione dalla seconda si trova $s=\frac{2}{3}$. Sostituendo nella seconda equazione si ottiene $b_4=\frac{1}{6}$.

Sostituendo questi valori nella prima equazione si ottiene $b_1=\frac{1}{6}$.

Questa analisi determina quindi

$$
b_1=\frac{1}{6},
\qquad
b_4=\frac{1}{6},
\qquad
b_2+b_3=\frac{2}{3}.
$$

In mancanza di informazioni (e quindi vincoli) aggiuntivi, poniamo per semplicità $b_2 = b_3 = 1/3$, ottenendo quindi

$$
\langle f \rangle_n \approx \sum_{i=1}^4 b_i k_i = \frac{1}{6} (k_1 + 2 k_2 + 2 k_3 + k_4),
$$

da cui deriva l'equazione [](#eq:RK4). Nel caso generale $x'=f(x,t)$ la dimostrazione completa richiede ulteriori condizioni, ma il risultato finale coincide con la formula appena ottenuta.

Chiudiamo facendo un parallelo con la famosa [regola di Simpson](https://it.wikipedia.org/wiki/Regola_di_Cavalieri-Simpson) (o di Cavalieri-Simpson), una delle formule classiche (e più accurate, considerando la sua semplicità) per approssimare un integrale, che applicata al caso di interesse dà

$$
\langle f \rangle_n = \frac{1}{\Delta t} \int_{t_n}^{t_{n+1}} f(t) dt \approx \frac{1}{6} \left[ f(t_n) + 4f (t_{n + 1/2}) + f(t_{n+1}) \right].
$$

La somiglianza di questa relazione con quella del metodo di RK4 fornisce un'intuizione utile: il metodo RK4 può essere visto come una versione della regola di Simpson adattata al caso in cui la derivata dipenda dalla soluzione stessa e debba quindi essere stimata durante il processo di integrazione.
```

# Altri metodi: punto centrale e mezzo passo

