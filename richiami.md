---
title: Richiami di Laboratorio di Calcolo e programma del corso
exports:
   - format: pdf
abbreviations:
   UNICODE: null
---

# Laboratorio di calcolo

Ecco una lista degli argomenti che si studiano nel corso di Laboratorio di Calcolo, e che costituiscono quindi i prerequisiti computazionali di questo corso:

1. Introduzione al corso, accenno sui  sistemi posizionali, sistemi di numerazione decimale, binario ed esadecimale. Conversioni da un sistema a un altro.  Somme e sottrazioni di numeri binari.
2. Rappresentazione di numeri negativi, complemento a due e in eccesso.
3. Virgola mobile e IEEE754, problemi di arrotondamento, CPU, dispositivi I/O, ASCII e UNICODE, RAM.
4. Linguaggio macchina, linguaggi di alto livello, compilatore e interprete, introduzione al C.
5. Introduzione al C: variabili, tipi e cast, operatori aritmetici, incremento e decremento.
6. Il preprocessore (solo `#include`), input/output e printf.
7. scanf, operatori logici e relazionali, priorità degli operatori, librerie matematiche, il *linker*.
8. `#define` e compilazione condizionata.
9. Diagrammi di flusso, teorema di Bohm-Jacopini, `if/else`, `for/while/do`.
10. Array unidimensionali.
11. Bubblesort, ricerca binaria su array ordinati, numeri pseudocasuali, algoritmo lineare congruenziale.
12. Istogramma di valori interi e reali.
13. Array multidimensionali, esempi con matrici, introduzione ai puntatori.
14. Input/output su file.
15. Puntatori e array unidimensionali, qualificatore const, puntatori e array multidimensionali.
16. Introduzione alle funzioni.
17. Librerie, personali, argomenti passati *by reference* e *by value*, introduzione alle stringhe.
18. `getchar`, `putchar`, array multidimensionali di stringhe.
19. Manipolazione di stringhe, lettura di stringhe da file, integrazione numerica: rettangoli e trapezi.
20. Integrazione Monte Carlo. 

Sul [mio sito](https://www.roma1.infn.it/~rovigatl/labcalc/) trovate le slide che utilizzavo quando insegnavo il corso (2022-2026).

# Programma del corso

## Algoritmi

* Integrazione di equazioni differenziali ordinarie
  * Metodi di Eulero, Eulero-Cromer, Velocity Verlet e Runge Kutta
  * Accuratezza e stabilità
  * Applicazioni: oscillatore armonico, pendolo, pendolo smorzato, pendolo smorzato e forzato, moto di due pianeti intorno al Sole
* Cammini aleatori
  * Generazione di numeri pseudocasuali uniformi e non uniformi
  * Cammini aleatori (*random walk*) in una e più dimensioni
  * Applicazioni: Diffusione, *self-avoiding random walk*, gas reticolare
* Liste
  * Liste concatenate, alberi, e *clustering*
  * Applicazione: la percolazione

## Argomenti di C:

* Definizione di strutture dati tramite `struct`
* Definizione di tipi derivati tramite `typedef`
* Allocazione dinamica della memoria (`malloc`, `calloc` e `free`)
* Operazioni bit-a-bit (operatori `bitwise`)

# Libri e dispense

Il corso è basato sulla seconda parte del libro ["Programmazione scientifica"](https://he.pearson.it/bundle/632) di L. M. Barone, E. Marinari, G. Organtini e Federico Ricci-Tersenghi, edito da Pearson. Le note che state leggendo sono invece una rielaborazione di alcuni degli argomenti che ho compilato durante la preparazione del corso. Questo materiale quindi non si sostituisce al libro, ma lo integra in alcune parti.
