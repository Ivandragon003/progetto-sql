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


