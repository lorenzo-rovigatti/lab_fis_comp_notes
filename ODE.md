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

In questo modo un'equazione differenziale viene trasformata in una relazione algebrica tra valori della funzione calcolati in istanti successivi. Questa semplice idea è alla base di tutti gli algoritmi di integrazione numerica che vedremo in questa parte del corso.



Per comprendere l'idea alla base di tutti i metodi che introdurremo nelle prossime sezioni, integriamo entrambi i membri dell'equazione [](#eq:ODE_first_order) tra due istanti consecutivi della griglia temporale, $t_n$ e $t_{n+1}$. Otteniamo

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
x(t) = A \cos(\omega_0 t) + B \sin(\omega_0 t),
$$

oppure, in forma equivalente,

$$
x(t) = C \cos(\omega_0 t + \phi),
$$

dove le costanti $A$, $B$ (oppure $C$ e $\phi$) sono determinate dalle condizioni iniziali, $x(0) = x_0$ e $v(0) = v_0$.

Come accennato precedentemente, per risolvere numericamente l'equazione differenziale del secondo ordine [](#eq:ODE_oscillatore) conviene trasformarla nel seguente sistema di due equazioni del primo ordine:

$$
\begin{cases}
x' = v, \\
v' = -\omega_0^2 x.
\end{cases}
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


# Eulero e Eulero-Cromer

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
v_{n+1} = v_n + a_n\Delta t\\
x_{n+1} = x_n + v_{n+1}\Delta t.
\end{cases}
$$

Questa semplice modifica produce risultati significativamente migliori in molti problemi meccanici, in particolare nei sistemi oscillanti.

## Velocity Verlet

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

