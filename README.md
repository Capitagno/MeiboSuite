# Meibosuite: analisi morfologica delle ghiandole di Meibomio

Meibosuite è un ambiente di calcolo avanzato in ambiente Python/Colab sviluppato per l'analisi quantitativa e qualitativa della meibomiografia a infrarossi. il software processa le immagini cliniche per isolare il tessuto tarsale (diga) ed estrarre le frequenze spaziali dei tubuli ghiandolari, calcolando in tempo reale la percentuale di atrofia (meiboscore).

## architettura matematica e ottica

la pipeline di elaborazione affronta i limiti fisici dell'acquisizione clinica (vignettatura, gradienti della lampada, riflessi speculari) attraverso un approccio ibrido che unisce fotometria, morfologia matematica e analisi in frequenza.

### 1. pre-processing e calibrazione spaziale
* **correzione flat-field sottrattiva:** l'algoritmo neutralizza le aberrazioni di illuminazione del cheratografo calcolando una mappa della luce di fondo (tramite sfocatura gaussiana estrema) e sottraendola all'immagine originale per ripristinare un campo visivo omogeneo senza distruggere la gamma dinamica locale.
* **target matching fotometrico:** il recinto anatomico isolato viene forzato ad assumere una firma statistica ideale (luminosità $\mu = 180$, contrasto $\sigma = 40$) tramite trasformazione lineare esatta. questo garantisce che gli algoritmi successivi operino sempre su gradienti costanti, indipendentemente dall'esposizione originale.

### 2. identificazione del tarso (diga)
* **morfologia matematica:** la delineazione dell'area tarsale avviene applicando un'operazione di chiusura morfologica con un elemento strutturante ellittico ampio (kernel 70x70 pixel). questa "coperta" matematica scavalca le valli inter-ghiandolari scure senza collassare, garantendo solidità strutturale.
* **binarizzazione di Otsu e inviluppo convesso:** l'immagine domata viene binarizzata e il perimetro più esterno viene tracciato calcolando l'inviluppo convesso, escludendo i riflessi del menisco lacrimale.

### 3. estrazione dei tubuli (motore ibrido)
* **trasformata di Fourier:** l'immagine equalizzata passa attraverso il dominio delle frequenze. applicando una maschera passa-banda asimmetrica, il software isola esclusivamente le frequenze spaziali verticali, ignorando il rumore orizzontale e i riflessi trasversali.
* **amplificazione locale:** la trasformata inversa viene normalizzata e processata con un equalizzatore adattivo (filtro Clahe) per massimizzare il micro-contrasto delle singole ghiandole.
* **riconnessione morfologica:** una chiusura con kernel direzionale (7x3 pixel) salda i segmenti ghiandolari frammentati, restituendo tubuli continui e anatomicamente coerenti.

## interfaccia clinica interattiva

il motore logico comunica in tempo reale con un'interfaccia Javascript renderizzata su browser, permettendo al clinico di:
* calibrare i muri di delimitazione anatomica.
* intervenire manualmente con strumenti a pennello per perfezionare la maschera tarsale (rossa) o ghiandolare (verde).
* gestire le correzioni tramite una coda di memoria protetta (undo stack) per l'annullamento chirurgico degli errori.
* visualizzare in diretta il calcolo percentuale della perdita ghiandolare e il relativo grado clinico.

## dipendenze
* Python 3.x
* OpenCV (cv2)
* NumPy
* Matplotlib
* Ipywidgets
