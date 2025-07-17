***README per la comprensione del file .sql del progetto UninaFoodLab***

1) Creazione delle tabelle.


NOTA: Sono state create prima le tabelle senza alcuna FK (Foreign Key)


1.1 	Creazione tabella "ristorante"

- A partitaIva è stata aggiunta una clausola CHECK per garantire la lunghezza esatta di 11 cifre.
- A stelleMichelin è stata aggiunta una clausola CHECK che permette solo valori tra 0 e 3.
- È stata aggiunta una clausola UNIQUE su (partitaIva, nome) per evitare ristoranti duplicati con stessa partiva IVA e nome.

1.2 	Creazione tabella "ricetta"

1.3 	Creazione tabella "ingrediente"

1.4 	Creazione tabella "chef"

- A codFiscale sono stati aggiunte due clausole CHECK che garantiscono lunghezza e formato del codice fiscale italiano.
- È stata aggiunta una clausola UNIQUE sull’email, insieme a una clausola CHECK per verificare il formato email.
- È stata aggiunta una clausola CHECK su anniEsperienza per assicurare che sia maggiore o uguale a 0.

1.5 Creazione del tipo ENUM "frequenza"

- Serve a specificare la frequenza dei corsi di cucina (esempio : giornaliero, ogniDueGiorni, settimanale, biSettimanale, mensile, bimestrale).

1.6 	Creazione tabella "corsoCucina"

- È stata aggiunta una clausola CHECK sul prezzo la quale assicuri che il prezzo di un corso di cucina non sia minore di 0.
- È stata aggiunta una clausola CHECK su numeroPosti la quale assicuri che sia maggiore di 0.  
- È stata aggiunta una clausola CHECK la quale assicura che la data di inizio di un corso sia minore della data di fine.
- È stata aggiunta una clausola UNIQUE su (nomeCorso, dataInizioCorso, dataFineCorso) per evitare corsi di cucina con stesso nome, stessa data di inizio e di fine.


  
1.7 	Creazione tabella "utente"

- A codFiscale sono stati aggiunte due clausole CHECK che garantiscono lunghezza e formato del codice fiscale italiano.
- È stata aggiunta una clausola UNIQUE su email, insieme a una clausola CHECK per verificare il formato email.



1.8 	Creazione tabella "iscritto"

-È stata aggiunta una clausola CHECK su votiAvuti la quale assicuri che i voti avuti siano maggiore o uguali a 0 e minori o uguali a 10.



1.9 	Creazione del tipo ENUM "tipoSessione"

- Serve a distinguere le sessioni “inPresenza” e “online”.

1.10 	Creazione tabella "sessione"

- È stata aggiunta una clausola CHECK su cap la quale assicuri che il CAP abbia dimensione 5 e che siano inseriti solo numeri.
- È stata aggiunta una clausola CHECK su numeroPosti la quale assicuri che il numero di posti abbiano valore NULL o maggiore di 0.
- È stata aggiunta una clausola CHECK su tipo la quale assicura che se il tipo di sessione è 'online', allora l'attributo 'piattaformaStreaming' non può essere NULL, mentre via, citta, cap, numeroPosti e idRicetta siano NULL; se il ipo di sessione è 'inPresenza', allora via, citta, cap, numeroPosti e idRicetta non possono essere NULL, mentre l'attributo 'piattaformaStreaming' sarà NULL;
- È stata aggiunta una clausola CHECK la quale assicura che la data di inizio di una sessione sia minore della data di fine.



1.11 	Creazione tabella "adesione"


1.12	Creazione tabella "segue"


1.13 	Creazione tabella "tiene"


1.14 	Creazione tabella "usa"
- È stata aggiunta una clausola CHECK su quantita la quale assicura che la quantità di un ingredienta sia maggiore di 0; 





2) Creazione funzioni/trigger


2.1 checkRistorante()

-La funzione serve ad impedire che un ristorante rimanga privo di chef.

-Controlla se, a seguito dell'eliminazione del legame tra uno chef e un ristorante, esistano ancora altri chef associati allo stesso ristorante.

-Se nessun altro chef è presente, la funzione lancia un'eccezione con un messaggio di errore e impedisce l’operazione, garantendo l’integrità del sistema. In caso contrario, il legame tra chef e ristorante viene eliminato.

-Il trigger trg_checkRistorante si attiva prima di eseguire una DELETE su lavora, eseguendo la funzione checkRistorante().



2.2 checkCorsoUtente()

-La funzione serve a rimuovere automaticamente un corso di cucina quando l’ultimo utente iscritto viene eliminato e il corso è già iniziato.

-Dopo la cancellazione di un record dalla tabella iscritto, la funzione verifica se il corso associato ha ancora utenti iscritti. Se non ci sono più iscritti e la data di inizio del corso è passata, allora elimina il corso stesso dalla tabella corsoCucina.

-Il trigger trg_checkCorsoUtente si attiva dopo un'operazione di DELETE sulla tabella iscritto. Si attiva per ogni riga eliminata e invoca la funzione checkCorsoUtente() per gestire automaticamente la rimozione dei corsi senza iscritti e già avviati.



2.3 checkIntervalloSessione()

-La funzione serve a garantire che ogni sessione di un corso venga programmata esclusivamente all’interno dell’intervallo temporale previsto dal corso stesso.

-Prima di inserire o modificare una sessione nella tabella sessione, la funzione confronta le date di inizio e fine della sessione (dataInizioSessione, dataFineSessione) con le date di inizio e fine del corso corrispondente (dataInizioCorso, dataFineCorso). Se le date della sessione non rientrano nell’intervallo del corso, viene sollevata un’eccezione e l’operazione viene bloccata.

-Il trigger trg_checkIntervalloSessione si attiva prima di ogni INSERT o UPDATE sulla tabella sessione, per ciascuna riga. Il suo scopo è assicurare che ogni nuova sessione o modifica a una sessione esistente rispetti i limiti temporali imposti dal corso associato.



2.3 checkIntervalloSessione()

-La funzione serve a garantire che ogni sessione venga programmata in un periodo compreso tra la data di inizio e la data di fine del corso a cui appartiene.

-Prima dell’inserimento o della modifica di una sessione nella tabella sessione, la funzione verifica che le date dataInizioSessione e dataFineSessione siano contenute nell’intervallo temporale stabilito per il corso corrispondente (dataInizioCorso, dataFineCorso). Se le date della sessione risultano al di fuori di questo intervallo, viene sollevata un’eccezione e l’operazione viene bloccata.

-Il trigger trg_checkIntervalloSessione si attiva prima di ogni INSERT o UPDATE sulla tabella sessione, per ciascuna riga. Ha il compito di far rispettare il vincolo temporale tra corso e sessioni.



2.4 checkPostiCorso()

-La funzione serve a controllare automaticamente la disponibilità di posti in un corso di cucina quando un utente tenta di iscriversi.

-Quando viene eseguita un’operazione di inserimento nella tabella iscritto, la funzione recupera il numero massimo di posti disponibili per il corso (numeroPosti) dalla tabella corsoCucina, e conta quanti utenti risultano già iscritti con stato confermato (stato = TRUE).

Se il numero di iscritti ha già raggiunto o superato il limite, la funzione imposta stato a FALSE, impedendo l’iscrizione. Altrimenti, lo imposta a TRUE.

Se il campo numeroPosti nella tabella corsoCucina risulta NULL (cioè non è stato specificato un limite massimo), la funzione considera che i posti siano illimitati e imposta stato a TRUE, permettendo sempre l’iscrizione.

-Il trigger trg_checkPostiCorso si attiva prima di ogni inserimento nella tabella iscritto, per verificare la capienza del corso e aggiornare dinamicamente lo stato dell’iscrizione in base alla disponibilità residua.



2.5 bloccoAdesioniOnline()

-La funzione serve a garantire che gli utenti possano inviare richieste di adesione esclusivamente per sessioni in presenza, impedendo adesioni a sessioni online.

-La funzione recupera il tipo della sessione alla quale l’utente sta cercando di aderire, leggendo il valore dell’attributo tipo dalla tabella sessione. Se la sessione non è di tipo 'inPresenza', l’inserimento viene bloccato sollevando un’eccezione, impedendo quindi che l’adesione venga registrata.

-Il trigger trg_bloccoAdesioniOnline si attiva prima di ogni inserimento nella tabella adesione, eseguendo la funzione bloccoAdesioniOnline() per validare la correttezza del tipo di sessione associata.




2.6 checkDataRichieste()

-La funzione serve a garantire che un utente possa inviare una richiesta di adesione solo prima dell’inizio della sessione a cui vuole partecipare.

-La funzione recupera la data di inizio della sessione e la confronta con la data in cui viene effettuata l’adesione. Se la richiesta viene effettuata dopo l’inizio della sessione, viene sollevata un’eccezione che blocca l’inserimento.

-Il trigger trg_checkDataRichieste si attiva prima di ogni inserimento nella tabella adesione, eseguendo la funzione checkDataRichieste() per impedire che vengano registrate adesioni in ritardo.




2.7 checkPresenzaAdesioneSegue()

-La funzione ha lo scopo di impedire che un utente venga registrato nella tabella segue come partecipante a una sessione se non ha prima ottenuto un’adesione valida (cioè accettata) alla stessa sessione.

-Prima di permettere l’inserimento o l’aggiornamento di una riga nella tabella segue, la funzione controlla che nella tabella adesione esista un record con lo stesso idSessione e codFiscale, e che il campo stato sia impostato a TRUE. Se questa condizione non è soddisfatta, la funzione solleva un’eccezione e annulla l’operazione.

-Il trigger trg_checkPresenzaAdesioneSegue si attiva prima di ogni INSERT o UPDATE sulla tabella segue, eseguendo la funzione checkPresenzaAdesioneSegue() per assicurare che solo gli utenti con adesione confermata possano seguire una sessione.



2.8 checkIscrittoCorso()

-La funzione serve a garantire che un utente possa seguire una sessione o aderirvi solo se risulta formalmente iscritto al corso di cucina associato alla sessione stessa.

-Quando viene effettuata un’operazione di inserimento o aggiornamento nelle tabelle segue o adesione, la funzione recupera l’identificativo del corso associato alla sessione (idCorsoCucina) e verifica se l’utente (codFiscale) risulta iscritto a quel corso nella tabella iscritto. Se non è presente un’iscrizione valida, la funzione solleva un’eccezione e blocca l’operazione.

-Il trigger trg_checkSegueCorso si attiva prima di ogni INSERT o UPDATE sulla tabella segue, mentre il trigger trg_checkAdesioneCorso si attiva prima di ogni INSERT o UPDATE sulla tabella adesione. Entrambi eseguono la funzione checkIscrittoCorso(), assicurando che un utente possa partecipare a una sessione o seguirla solo se è effettivamente iscritto al corso corrispondente.

-L’uso della stessa funzione per due trigger diversi consente di evitare duplicazioni di codice, migliorando la manutenibilità e la chiarezza del progetto.)



2.9 emailUnivocaPersona()

-La funzione serve a garantire l’unicità dell’email tra le tabelle chef e utente, impedendo che lo stesso indirizzo venga associato a persone diverse nel sistema.

-Quando viene inserito o aggiornato un record nelle tabelle chef o utente, la funzione controlla se l’email specificata in NEW.email è già presente nella tabella opposta. Se l'email è già utilizzata da un'altra persona, viene sollevata un'eccezione e l’operazione viene annullata.

-Il trigger trg_emailUnivocaChef si attiva prima di ogni INSERT o UPDATE sulla tabella chef, mentre trg_emailUnivocaUtente si attiva prima di ogni INSERT o UPDATE sulla tabella utente. Entrambi i trigger invocano la stessa funzione emailUnivocaPersona().

-L’impiego della stessa funzione per due trigger distinti consente di evitare ridondanza di codice, migliorando l’efficienza e la chiarezza dell’implementazione.



2.10 checkCorsoChef()

-La funzione serve a impedire che un corso di cucina resti privo di uno chef. Viene utilizzata per garantire la continuità didattica del corso stesso.

-Quando si tenta di eliminare un legame tra uno chef e un corso nella tabella tiene, la funzione controlla se esistono altri chef associati allo stesso corso. Se nessun altro chef è presente, viene sollevata un'eccezione che blocca l'operazione di eliminazione.

-Il trigger trg_checkCorsoChef si attiva prima di eseguire una DELETE sulla tabella tiene, richiamando la funzione checkCorsoChef(). Questo assicura che ogni corso abbia almeno uno chef associato in ogni momento.



2.11 checkCodFiscaleUnico()

-La funzione serve a garantire l’unicità del codice fiscale tra le tabelle chef e utente. In questo modo, si evita che una stessa persona venga registrata contemporaneamente in entrambe le tabelle con lo stesso codice fiscale.

-Quando si tenta di inserire o aggiornare un record nelle tabelle chef o utente, la funzione verifica se il codice fiscale (codFiscale) è già presente nell’altra tabella. Se sì, l’operazione viene interrotta e viene sollevata un’eccezione.

-Il trigger trg_checkCodFiscale_chef si attiva prima di un inserimento o aggiornamento nella tabella chef, mentre trg_checkCodFiscale_utente si attiva prima di un inserimento o aggiornamento nella tabella utente. Entrambi i trigger invocano la funzione checkCodFiscaleUnico().

-Nota: l’utilizzo della stessa funzione per entrambi i trigger evita la duplicazione di codice, favorendo la manutenzione e la leggibilità del progetto.



2.12 checkConflittoSessioneChef()

-La funzione serve a verificare che uno chef non venga assegnato a due sessioni che si sovrappongono temporalmente, evitando quindi conflitti di orario tra sessioni dello stesso chef.

-Quando si inserisce o aggiorna una sessione, la funzione controlla se esistono altre sessioni collegate a corsi tenuti dallo stesso chef e con un intervallo temporale sovrapposto. In tal caso, viene sollevata un'eccezione che impedisce l’operazione.

-Il trigger trg_checkConflittoSessioneChef si attiva prima di un'operazione di INSERT o UPDATE sulla tabella sessione, eseguendo la funzione checkConflittoSessioneChef() per garantire la coerenza nella pianificazione delle attività degli chef.



2.13 checkNumeroPostiSessione()

-La funzione serve a verificare che il numero di posti disponibili per una sessione non superi quello previsto dal corso di cucina corrispondente.

-Al momento dell’inserimento o aggiornamento di una sessione, la funzione confronta il valore di numeroPosti specificato per la sessione con quello del relativo corso. Se entrambi i valori sono non nulli e quello della sessione risulta maggiore, viene lanciata un’eccezione che impedisce l’operazione. In caso contrario, la modifica è accettata.

-Il trigger trg_checkNumeroPostiSessione si attiva prima di un’operazione di INSERT o UPDATE sulla tabella sessione, eseguendo la funzione checkNumeroPostiSessione() per garantire la coerenza tra sessioni e corsi in termini di capacità massima.



2.14 checkConflittoLuogoSessione()

-La funzione serve a impedire che due sessioni in presenza si svolgano nello stesso luogo e nello stesso intervallo temporale, evitando sovrapposizioni logistiche.

-Quando si tenta di inserire o aggiornare una sessione con tipo 'inPresenza', la funzione controlla se esistono già altre sessioni con lo stesso indirizzo e con un intervallo temporale che si sovrappone. Se una tale sessione è trovata, viene sollevata un'eccezione che blocca l’operazione. In caso contrario, l’operazione viene permessa.

-Il trigger trg_checkConflittoLuogoSessione si attiva prima di un’operazione di INSERT o UPDATE sulla tabella sessione, eseguendo la funzione checkConflittoLuogoSessione() per garantire che due sessioni non vengano organizzate simultaneamente nello stesso luogo fisico.





3) Creazione della view

3.1 iscrittiConfermatiCorso

-La View iscrittiConfermatiCorso serve a mostrare l'elenco degli utenti che risultano effettivamente iscritti a un corso di cucina, cioè coloro per cui lo stato dell'iscrizione risulta confermato (stato = TRUE).

-Per ogni utente iscritto, la vista mostra alcuni dati del corso di cucina e dati dell'utente. Per quanto riguarda il corso, vengono forniti: il codice del corso e il nome del corso.
Invece, per dell'utente vengono forniti: il codice fiscale, il nome, il cognome e i voti ottenuti.



3.2 VistaPartecipantiInPresenza

-La View VistaPartecipantiInPresenza serve a visualizzare l’elenco dei partecipanti confermati alle sessioni che si svolgono in presenza.

-Per ciascun partecipante, la vista mostra: il codice fiscale, il nome e il cognome. Inoltre della sessione vengono mostrati: identificativo, date di inizio e fine e il corso di cucina associato.



3.3 QuantitaIngredientiRicetta

-La View QuantitaIngredientiRicetta calcola la quantità totale necessaria di ciascun ingrediente per ogni sessione in presenza, tenendo conto del numero effettivo di partecipanti.

-Per ogni sessione, la vista mostra il nome della ricetta, l’identificativo e il nome di ciascun ingrediente, e la quantità complessiva necessaria, ottenuta moltiplicando la quantità per porzione per il numero di partecipanti.



4) Inserimento dati
Infine, dopo aver creato tutte le tabelle e i vincoli con le funzioni di controllo
si passa a popolare il database attraverso l'uso della INSERT INTO.
