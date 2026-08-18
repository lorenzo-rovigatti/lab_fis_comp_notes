---
title: Strumenti e nozioni utili per esercitarsi
exports:
   - format: pdf
---

```{warning} Inglesismi
Purtroppo il mondo del software (e quindi anche della fisica computazionale) è pieno di inglesismi. Nonostante ci si debba sforzare di usarli solo quando sono necessari (quando, cioè, lo stesso termine in italiano assume una connotazione diversa), dovremo usarli frequentemente. Questo non ci autorizza a utilizzare espressini aberranti quali "printare" o "quittare".
```

# Git, GitHub e Classroom 50: il minimo indispensabile

Durante questo corso useremo [Git](#git), [GitHub](#github) e [Classroom 50](#classroom-50) per organizzare le esercitazioni e tenere traccia del lavoro svolto. Alcuni controlli verranno eseguiti automaticamente; figure, analisi dei risultati e risposte alle domande verranno invece esaminate dai docenti.

Git e GitHub sono strumenti molto generali e possono essere usati in modi assai più complessi di quanto ci servirà qui. Lo scopo di questa sezione non è imparare ad usarli in modo completo, ma costruire il **modello mentale minimo** necessario per lavorare alle esercitazioni senza usare i comandi alla cieca.

```{warning} Chiedere aiuto agli LLM (ChatGPT, Gemini, ecc.)
Sia Git che GitHub sono strumenti molto comuni e utilizzati, quindi gli LLM sono perfettamente in grado di aiutarvi qualora aveste problemi di natura tecnica. Il punto è descrivere accuratamente il problema, in modo che possano fornire la soluzione più adatta **per voi**.

Poiché l'uso che faremo di questi strumenti è piuttosto basilare, vi invito a provare a risolvere i problemi utilizzando quanto scritto in queste note, oppure a chiedere ai docenti (o, perché no, parlarne tra di voi), per evitare di utilizzare comandi e soluzioni che, al vostro livello, potrebbero non solo risultare incomprensibili, ma anche peggiorare il problema.
```

Nel seguito descriveremo brevemente questi strumenti in un ordine che rispecchia la logica con cui sono utilizzati:

1. prima **Git**, che funziona sul vostro computer;
2. poi **GitHub**, che permette di condividere un repository Git attraverso Internet;
3. infine **Classroom 50**, che usa GitHub e alcuni degli strumenti che mette a disposizione degli sviluppatori (chiamati GitHub Actions) per organizzare le esercitazioni e fornire feedback automatico.

# Git

Quando si lavora a un software, è naturale modificarlo molte volte. Alcune modifiche funzionano, altre introducono errori, di cui magari ci accorgiamo più avanti. In altri casi, vogliamo provare a cambiare o aggiungere funzionalità anche complesse con la certezza di poter tornare indietro, o di rivedere i cambi fatti. In questo contesto capire quando e come una certa riga è stata modificata, recuperare una versione precedente o ottenere facilmente una lista di differenze tra versioni è utilissimo. Queste funzionalità sono fornite dai cosiddetti software di controllo di versione, di cui [Git](https://it.wikipedia.org/wiki/Git_(software)) è senz'altro il più comune. Semplificando molto, un sistema di controllo di versione come Git registra la storia di un insieme di file come una lista di versioni. Ogni versione, che in Git ha un nome unico detto *hash*[^hash], è una foto scattata in un dato momento di tutti i file gestiti da Git nello spazio di lavoro considerato.

[^hash]: Un "git hash" è composto da 40 caratteri esadecimali (spesso abbreviato in 7 per comodità).

Questo spazio di lavoro, la directory controllata da Git, viene chiamata *repository*, o più brevemente *repo*. Per questo corso potete pensare a un repository come a una normale directory di lavoro, con in più una storia delle versioni che decidiamo di conservare. Git funziona innanzitutto *localmente*: potete creare nuove versioni, consultare la storia e confrontare versioni anche senza essere collegati a Internet.

## Il workflow locale: modificare, preparare, registrare

Il modo più semplice per capire Git è partire da quello che succede mentre lavorate davvero.

Supponiamo di avere un repository e di aprire uno dei suoi file con un editor. Modificate il codice, salvate il file, magari lo compilate e lo provate. In questa fase state modificando soltanto i **file presenti sul vostro computer**: la storia conservata da Git non è ancora cambiata.

Git, però, si accorge che quei file sono diversi rispetto all'ultima versione registrata. Potete controllare la situazione in qualunque momento con

```bash
git status
```

che mostra quali file sono stati modificati o creati, senza cambiare nulla nel repository.

Per vedere più precisamente quali righe avete cambiato potete usare

```bash
git diff
```

A un certo punto decidete che alcune delle modifiche fatte formano una versione sensata del lavoro e che volete conservarla nella storia del repository. Prima di creare questa nuova versione dovete indicare a Git quali modifiche ne faranno parte.

Per aggiungere tutte le modifiche correnti usate[^git_add]

```bash
git add -A
```

[^git_add]: Se invece volete essere più precisi e aggiungere solo alcuni file specifici, potete passarne l'elenco a `git add`, omettendo il `-A`. Ad esempio, se volete aggiungere solo `eulero.c` il comando corretto è `git add eulero.c`

Le modifiche selezionate in questo modo entrano nella *staging area*. Potete pensarla come all'insieme delle modifiche che avete preparato per la prossima versione del repository: non sono ancora state registrate nella storia, ma avete detto a Git «queste sono le modifiche che voglio includere nella prossima versione».

Dopo `git add` potete controllare nuovamente la situazione con

```bash
git status
```

Ora possiamo prepararci al *commit*, che è l'operazione che registra una nuova versione del progetto nella storia del repository. Se volete vedere nel dettaglio ciò che avete preparato per il commit, potete dare il comando

```bash
git diff --staged
```

Solo quando siete soddisfatti create il commit:

```bash
git commit -m "Implementato il metodo di Eulero"
```

Questo comando prende le modifiche che avevate preparato nella staging area, le applica alla versione precedente e ne crea una nuova, accludendo (con `-m`) un breve messaggio che descrive il lavoro svolto.

Il workflow locale è quindi, in pratica:

```bash
# modifico, compilo e provo i file

git status
git diff

git add -A # oppure git add file.c altro_file.c
git status
git diff --staged

git commit -m "Implementata la prima parte"
```

I controlli intermedi non sono formalità (soprattutto le prime volte!): servono a evitare di registrare per errore file o modifiche che non volevate includere.

Quando aggiungete un commit, è utile perdere qualche secondo a scrivere un messaggio sensato. Messaggi come

```text
Implementato il metodo di Eulero
Corretto il calcolo dell'energia
Aggiunta la figura del periodo
```

sono più utili di

```text
prova
cose
ultimo
```

Non serve però cercare il messaggio perfetto: basta che descriva in modo comprensibile ciò che avete fatto.

A questo punto il nuovo commit esiste nella storia Git **sul vostro computer**. Non è stato ancora inviato da nessuna parte: vedremo nella sezione su GitHub come condividere questi commit con gli altri membri del gruppo.

## Consultare la storia

La storia di un repository si può riassumere in un elenco di commit ordinati dal più al meno recente con il comando

```bash
git log --oneline
```

a cui si può opzionalmente passare un ulteriore argomento `-N` per limitarci agli ultimi `N` commit (esempio: `git log --oneline -5`). Non avremo bisogno di analizzare in dettaglio la struttura della storia di Git[^git_history]. Ci basta sapere che i commit formano una successione di versioni del progetto e che Git ne conserva traccia.

[^git_history]: Se siete interessati potete studiare come funziona `git diff`, che permette di ottenere la lista di cambi fatti tra la versione attuale e una o più versioni specifiche, oppure tra versioni specificate, di tutto il repository o di particolari file.

# GitHub

Finora tutto ciò che abbiamo descritto avviene sul vostro computer. Per lavorare in gruppo, però, è utile avere una copia del repository accessibile a tutti.

[GitHub](https://github.com/) è un servizio online che, fra le altre cose, ospita repository Git. Nel nostro caso avremo quindi (almeno) due copie dello stesso progetto:

* Uno (o più, se lavorate su altri computer oltre quello di laboratorio) repository *locale*.
* Un repository *remoto*, ospitato sui server di GitHub.

```{warning}
Come vedremo subito, la sincronizzazione tra un repository locale e quello remoto non è automatica, né in un verso né nell'altro, ma va fatta con specifici comandi Git. Inoltre, ricordatevi che la sincronizzazione (sia in un verso che nell'altro) richiede una connessione a internet!
```

Vediamo ora quali sono i comandi Git che ci serviranno per far comunicare il repository locale con quello remoto (e viceversa).

## `git clone`: ottenere una copia del repository

```{figure} figures/clone.png
:name: fig:clone
:align: center
:width: 600px

Per ottenere l'indirizzo del repository da clonare, andate sulla pagina GitHub del repo, premete su `Code` e poi sull'icona di copia, qui evidenziata, insieme all'indirizzo stesso, dal rettangolo rosso.
```

Quando un repository esiste già su GitHub e volete iniziare a lavorarci su un computer, dovete **clonarlo**. La [](#fig:clone) mostra come ottenere l'indirizzo del repository da clonare. Una volta copiato l'indirizzo, eseguite

```bash
git clone INDIRIZZO_DEL_REPOSITORY
```

per esempio

```bash
git clone https://github.com/nome-organizzazione/esercitazione-gruppo42.git
```

Git crea una nuova directory contenente i file del progetto e la sua storia. Entrate quindi nella directory:

```bash
cd esercitazione-gruppo42
```

`git clone` si esegue normalmente **una sola volta per ogni repository e per ogni computer sul quale volete lavorare**.

## `git push`: inviare i propri commit a GitHub

Dopo aver creato uno o più commit sul repository locale, potete inviarli al repository remoto con

```bash
git push
```

Il punto importante è ricordare che salvare un file **non equivale** a creare un commit, e creare un commit **non cambia la storia**  del repository remoto di GitHub. In altre parole,

* salvataggio nell'editor  $\to$ modifica i file locali
* `git commit` $\to$ registra una versione nella storia locale
* `git push` $\to$ invia i nuovi commit a GitHub

È solo dopo un `git push` che gli altri membri del gruppo (o voi stessi da casa!) possono ottenere i vostri commit.

## `git pull`: ricevere i commit degli altri

Il repository sul vostro computer **non si aggiorna automaticamente** quando un compagno fa `git push`. Per ottenere da GitHub i commit più recenti usate

```bash
git pull
```

Quindi:

* `git push` vostro computer $\longrightarrow$ GitHub
* `git pull` vostro computer $\longleftarrow$ GitHub

:::{note} `clone` o `pull`?
Nonostante ciò che all'inizio potrebbe sembrare, `git clone` e `git pull` hanno ruoli molto diversi:

* `git clone` crea per la prima volta una copia locale del repository. Si usa una volta soltanto per repository.
* `git pull` aggiorna una copia locale che esiste già. Si utilizza ogni volta che vogliamo aggiornare il repository locale.
:::

## Lavorare in gruppo e i conflitti

Supponiamo che Alice e Bob abbiano entrambi clonato lo stesso repository.

Alice modifica un file, crea un commit e fa `git push`. Il repository su GitHub ora contiene il suo nuovo commit. Bob, eseguendo `git pull`, riceve quel commit e aggiorna la propria copia locale.

Se Alice e Bob modificano parti diverse del progetto, Git spesso riesce a combinare automaticamente il loro lavoro. Se invece modificano in modo incompatibile le stesse righe, Git non può decidere da solo quale versione mantenere: si verifica un **conflitto**.

Potreste vedere messaggi contenenti parole come

```text
CONFLICT
Automatic merge failed
```

Un conflitto non significa che il lavoro sia andato perso. Significa soltanto che è necessario decidere manualmente come combinare due modifiche incompatibili.

Per questo corso, se incontrate un conflitto e non sapete come risolverlo, **non usate** `git push --force` o altri comandi che trovate in rete (o che vi fornisce il vostro LLM preferito), e non cancellate il repository. Mostrate ai docenti l'output del comando e il risultato di `git status`.

## GitHub Actions e Continuous Integration

GitHub non si limita a conservare repository. Può anche eseguire automaticamente operazioni e programmi quando nel repository accadono determinati eventi. Questa funzionalità si chiama **GitHub Actions**.

Un *workflow* di GitHub Actions è una sequenza di operazioni eseguita automaticamente da GitHub. Per esempio, dopo un `git push`, un workflow potrebbe

1. ottenere la nuova versione del repository;
2. compilare il programma;
3. eseguirlo;
4. controllare che siano stati prodotti i file richiesti;
5. verificare alcuni risultati numerici.

L'uso sistematico di controlli automatici di questo tipo rientra in ciò che viene normalmente chiamato [*Continuous Integration*](https://en.wikipedia.org/wiki/Continuous_integration), o *CI*. Per questo corso non è necessario conoscere l'architettura della CI. È sufficiente sapere che i vostri `push` faranno partire automaticamente dei controlli che vi daranno del feedback iniziale sull'esercitazione che state svolgendo.

```{figure} figures/actions_tab.png
:name: fig:actions_tab
:align: center
:width: 600px

Un esempio di scheda "Actions" su github. Dei quattro worfklow mostrati, quattro si sono conclusi con successo, mentre il terzo ha dato errore.
```

Potete vedere le esecuzioni dalla scheda "Actions" del repository su GitHub, di cui un esempio è mostrato in [](#fig:actions_tab). In generale le possibilità sono tre:

* il workflow è ancora in esecuzione (icona gialla);
* il workflow è terminato con successo (icona verde);
* il workflow non è terminato con successo: almeno uno dei controlli è fallito (icona rossa).

Se un controllo fallisce, aprite l'esecuzione e leggete l'output dello step che ha dato errore. Il messaggio potrebbe indicare, per esempio, che il programma non compila, che manca un file richiesto o che un risultato numerico non è quello atteso.

## Pull request

GitHub possiede anche uno strumento chiamato *pull request*, spesso abbreviato in *PR*. Nonostante il nome, una pull request **non è** il comando `git pull`, che come abbiamo visto aggiorna il repository sul vostro computer. Una pull request è invece una pagina di GitHub che permette di mostrare, confrontare e commentare un insieme di modifiche.

Nel normale sviluppo software le pull request sono spesso usate per discutere modifiche prima di integrarle in un progetto. Nel nostro corso non sarà necessario imparare il workflow generale delle pull request: quando ne useremo una, servirà soprattutto come luogo in cui visualizzare il lavoro e ricevere commenti dai docenti.

# Classroom 50

A questo punto abbiamo tutti gli ingredienti necessari per parlare di [Classroom 50](https://github.com/foundation50/classroom50/wiki), il sistema che useremo per organizzare le esercitazioni. Non sostituisce Git o GitHub: li usa. In particolare, Classroom 50 può preparare per un'esercitazione

* un repository GitHub sul quale lavorerà il gruppo;
* i file iniziali e la traccia del lavoro;
* i workflow GitHub Actions usati per i controlli automatici;
* una pagina da cui consultare il risultato della valutazione automatica dei codici;
* quando previsto, una pull request usata per il feedback dei docenti.

Il punto importante è che, una volta creato il repository dell'esercitazione, voi lavorerete con **normali comandi Git** e con la normale interfaccia di GitHub.

Classroom 50 serve quindi a "montare" questi strumenti in una struttura adatta al corso.

## Come si svolgeranno le esercitazioni

Ora abbiamo gli strumenti necessari per capire come si svolgerà un'esercitazione. All'inizio di ogni esercitazione, il docente vi metterà a disposizione un link, dopodiché dovrete eseguire i passi seguenti:

1. Seguite il link fornito dai docenti e accettate l'esercitazione in Classroom 50. Verrà preparato il repository GitHub associato al vostro gruppo.

2. Clonate il repository (da fare ogni volta che cominciate a lavorare all'esercitazione su un certo computer):
```bash
git clone URL_DEL_REPOSITORY
cd NOME_DEL_REPOSITORY
```
Leggete quindi il `README.md` e gli altri file che contengono la traccia.

3. Se il repository è già presente sul computer, all'inizio di una sessione controllate il suo stato e scaricate eventuali commit fatti dai vostri compagni:
```bash
git status
git pull
```

4. Modificate il codice, compilatelo ed eseguitelo sul vostro computer come richiesto dalla traccia. Git non sostituisce il normale ciclo di sviluppo: prima di tutto dovete provare il programma voi stessi.

5. Quando avete completato una parte sensata, fate una "foto" alla versione del lavoro aggiungendo un commit:
```bash
git status
git diff
git add -A
git status
git commit -m "Descrizione delle modifiche"
```
Il commit è ora registrato localmente.

5. Condividete il lavoro inviando il nuovo commit a GitHub con
```bash
git push
```
Questo rende il lavoro disponibile agli altri componenti del gruppo e, nelle esercitazioni configurate per farlo, avvia i controlli automatici.

6. Leggete il feedback automatico: dopo il `push`, controllate il risultato su GitHub nella scheda **Actions** oppure attraverso la pagina di Classroom 50 indicata nella traccia. Se un controllo fallisce, leggete il messaggio, correggete il problema e create un nuovo commit. Non è necessario aspettare di aver terminato tutta l'esercitazione prima di fare `push`: commit piccoli e ragionevoli rendono più semplice capire cosa è cambiato e permettono di ricevere feedback durante il lavoro.

7. I controlli automatici possono verificare soltanto ciò che è stato programmato esplicitamente. Possono, per esempio, controllare che il codice compili, che un file esista, che l'output abbia un certo formato o che un valore numerico rientri in una certa tolleranza. Non possono sostituire la valutazione scientifica di una figura, di un'analisi o di una risposta argomentata. Queste parti verranno esaminate dai docenti. Quindi il fatto che i controlli automatici previsti sono stati superati non significa necessariamente che l'intera esercitazione sia corretta o completa.

## I comandi da conoscere

Per il normale svolgimento delle esercitazioni vi serviranno soprattutto questi comandi:

```bash
# crea sul computer una copia di un repository esistente su GitHub
git clone URL

# mostra lo stato del repository
git status

# mostra le modifiche non ancora registrate
git diff

# prepara le modifiche per il prossimo commit
git add -A # oppure git add file1.c file2.c ...

# registra un nuovo commit nella storia locale
git commit -m "Descrizione"

# invia a GitHub i nuovi commit locali
git push

# scarica da GitHub i nuovi commit e aggiorna la copia locale
git pull

# mostra in forma compatta gli ultimi 5 commit
git log --oneline -5
```

Se non sapete cosa sta succedendo, il primo comando da provare è quasi sempre

```bash
git status
```

perché mostra lo stato del repository senza modificarlo.

# Il workflow da ricordare

Una volta compresi i concetti precedenti, il ciclo di lavoro può essere riassunto in modo compatto.

All'inizio di una sessione:

```bash
git status
git pull
```

Durante il lavoro:

1. Modifico e provo il codice
2. Controllo le modifiche fatte con `git status` e/o `git diff`
3. Dico a git quali sono i file da modificare con `git add -A`
4. Salvo le modifiche su git con `git commit`
5. Invio le modifiche a GitHub con `git push`
6. Controllo il feedback automatico sul sito di Classroom 50 o direttamente sulla tab "Actions" di GitHub. Se il feedback segnala un problema, correggete il codice e ripetete il ciclo.

Quando lavorate a casa ma in gruppo, è **importantissimo** ricordare di fare `git pull` prima di iniziare a lavorare e `git push` quando volete condividere i vostri nuovi commit.

# Alcuni problemi comuni

:::{dropdown} «Ho modificato un file, ma GitHub mostra ancora la vecchia versione»

Salvare il file non basta. Controllate `git status`: probabilmente dovete ancora eseguire

```bash
git add -A
git commit -m "..."
git push
```
:::

:::{dropdown} «Ho fatto un commit, ma GitHub non cambia»

Il commit è locale. Dovete ancora fare

```bash
git push
```
:::

:::{dropdown} «Il mio compagno ha fatto `git push`, ma io non vedo le sue modifiche»

La vostra copia locale non si aggiorna automaticamente. Eseguite

```bash
git pull
```
:::

:::{dropdown} «`git push` viene rifiutato perché su GitHub ci sono nuovi commit»

Probabilmente un vostro compagno ha fatto `push` prima di voi. Non usate `git push --force`.

Controllate lo stato del repository e provate a sincronizzarlo:

```bash
git status
git pull
```

Se Git riesce a combinare le modifiche, potrete poi fare `git push`. Se compare un conflitto e non sapete come procedere, chiedete ai docenti.
:::

:::{dropdown} «GitHub Actions mostra un errore»

Il `push` è arrivato correttamente a GitHub; è uno dei controlli automatici ad aver trovato un problema. Aprite la scheda **Actions**, selezionate l'ultima esecuzione e leggete l'output dello step che è fallito.
:::

:::{dropdown} «Ho scaricato il repository come ZIP»

Lo ZIP contiene i file, ma non contiene una normale copia di lavoro Git. Tornate alla pagina GitHub del repository ed eseguite `git clone INDIRIZZO_DEL_REPOSITORY`.
:::

:::{dropdown} «Ho eseguito `git init` dentro un repository clonato»

Non serve: `git clone` crea già un repository Git completo. Se non siete sicuri di cosa avete modificato, fermatevi e chiedete ai docenti prima di provare comandi correttivi trovati online.
:::

:::{dropdown} «Posso modificare gli altri file del repo?»

In generale, meglio di no. Il repository può contenere file usati da Classroom 50 o dai workflow di GitHub Actions, per esempio file sotto

```text
.github/
```

o altri file di configurazione indicati nella traccia. Salvo istruzioni esplicite, non modificateli e non cancellateli. Analogamente, non aggiungete al repository grandi quantità di file temporanei, eseguibili o dati generati se la traccia non vi chiede di farlo.
:::
