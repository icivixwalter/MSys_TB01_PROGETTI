# MSys_TB01 – Master Plan dei progetti e dei loro oggetti

Mini-progetto **TB01_PROGETTI** del super-progetto MSys, cartella `C:\CASA\LINGUAGGI\ACCESS\PROGETTI_MDB\MSYS\MSys_TB01_PROGETTI\`.

Tre database back-end, usati insieme, servono a:

1. **creare il progetto**: codice progetto, descrizione, note esplicative, gruppo e database di origine (TB01 – PROGETTI);
2. **dire quali tipi di oggetto usa ogni progetto**: form, query, tabelle, report, macro, moduli (TB02 – TIPO OGGETTI);
3. **collegare a ogni progetto gli oggetti da utilizzare**, uno per uno, con nome, tipo, numero d'ordine e note (TB03 – ELENCO OGGETTI).

Il database front-end `GE_Msys_TB01_PROGETTI.mdb` contiene la maschera di gestione, che mostra le tre tabelle una dentro l'altra: progetto → tipi di oggetto → oggetti.

Aggiornato il 15/11/2026, ricavato dalla lettura dei database (struttura, dati, query, maschere e codice).

## 1. I file in sintesi

| File (in `MDB\`) | Ruolo | Tabella principale | Righe | Legame |
| --- | --- | --- | --- | --- |
| `Msys_TB01_PROGETTI.mdb` | back-end: **progetti** | `Msys_TB01_PROGETTI` (copia: `PROGETTI_Msys_TB01_PROJECT`) | 24 | `COD_PROGETTO_s` |
| `Msys_TB02_TIPO_OGGETTI.MDB` | back-end: **tipi di oggetto per progetto** | `Msys_TB02_TIPO_OGGETTI` (copia: `PROGETTI_Msys_TB02_TIPO_OGGETTI`) | 43 | `COD_PROGETTO_s` + `TIPOGGETTO_s` |
| `Msys_TB03_ELENCO_ObjProgetti.mdb` | back-end: **oggetti di ogni progetto** | `Msys_TB03_ELENCO_ObjProgetti` (copia: `PROGETTI_Msys_TB03_ELENCO_ObjProgetti`) | 377 | `COD_PROGETTO_s` + `TIPOGGETTO_s` |
| `GE_Msys_TB01_PROGETTI.mdb` | front-end: maschere, query, codice | (collegamenti e alcune tabelle locali) | – | – |

In ogni back-end la tabella principale esiste **due volte**: con il nome `Msys_TB0x_...` e con il prefisso `PROGETTI_...`. Il 15/11/2026 le due copie contengono gli stessi dati. Il front-end usa le tabelle `PROGETTI_...`.

## 2. Come si collegano

```
  Msys_TB01_PROGETTI                        (1 riga = 1 progetto)
  COD_PROGETTO_s  es. MSys, LLPPGest, TREE_VIEW
  PROGETTO_s, NOTE_PROGETTO_s, GROUP_QUOTE_s, DISCO/PATH/DATABASE_ORIG_s
        |
        | COD_PROGETTO_s         1 : N
        v
  Msys_TB02_TIPO_OGGETTI                    (1 riga = 1 tipo di oggetto usato dal progetto)
  COD_PROGETTO_s + TIPOGGETTO_s   es. MSys + FORMS, MSys + QUERY
        |
        | COD_PROGETTO_s + TIPOGGETTO_s   1 : N
        v
  Msys_TB03_ELENCO_ObjProgetti              (1 riga = 1 oggetto del progetto)
  COD_PROGETTO_s + TIPOGGETTO_s + NOMEOGGETTO_s, NRO_OGGETTO_i, NOTA_OGGETTO_s ...

  Tabelle di supporto (in altri mini-progetti):
  Msys_DF11_COD_PROGETTO   elenco dei codici progetto (38)       -> combo del codice progetto
  MSys_DF02_TIPO_OGGETTO   dizionario dei tipi di oggetto (10)   -> FORMS, QUERY, TABLE, ...
  MSYS_TB04_DB_ESTERNO     database esterni di destinazione (4)
```

- I legami sono `COD_PROGETTO_s` tra TB01 e TB02, e `COD_PROGETTO_s` + `TIPOGGETTO_s` tra TB02 e TB03.
- Nei database **non** sono definite relazioni: i legami valgono solo nelle sottomaschere (campi di collegamento) e nel codice.

## 3. TB01 – Progetti (Msys_TB01_PROGETTI)

### 3.1 Campi

| Campo | Tipo | Significato |
| --- | --- | --- |
| `COD_PROGETTO_s` | Testo 45 | **codice del progetto**; è la chiave che lega tutte le tabelle |
| `PROGETTO_s` | Testo 50 | **descrizione** breve del progetto |
| `NOTE_PROGETTO_s` | Testo 50 | **note esplicative** (solo 50 caratteri) |
| `NOTA_PROG_ORIG_s` | Testo 50 | nota sull'origine del progetto |
| `GROUP_QUOTE_s` | Testo 50 | gruppo di quote del progetto (per esempio `GROUP_QUOTA_MSys`) |
| `GROUP_COD_PROG_s`, `NOTE_GROUP_COD_PROG_s` | Testo 50 / 255 | gruppo di progetti e sue note (oggi vuoti) |
| `DISCO_ORIG_s`, `PATH_ORIG_s`, `DATABASE_ORIG_s` | Testo 5 / 255 / 50 | disco, cartella e file del database in cui vive il progetto |
| `NOTE_DATABASE_ORIG_s` | Testo 255 | note sul database di origine |
| `IdOrd_lng` | Lungo | ordine di visualizzazione |
| `Scel_b`, `Stam_b`, `Canc_b`, `Agg_b`, `BLOCC_b` | Sì/No | flag: scelto, da stampare, da cancellare, aggiornato, bloccato |
| `IDKeyArch_lng`, `Msg01_s`, `ID_lng` | | chiave di archivio, messaggio, identificativo |
| `DATAINS_d`, `DATAAGG_d`, `TIMEOPER_d`, `ORA_AGG_d` | Data | date di inserimento e aggiornamento |

### 3.2 Progetti presenti (24)

| IdOrd | COD_PROGETTO_s | PROGETTO_s | NOTE_PROGETTO_s |
| --- | --- | --- | --- |
| 1 | `MSys` | PROGETTO MSys | PROGETTO DI GESTIONE OGGETTI DB |
| 2 | `TREE_VIEW` | PROGETTO TREEVIEW | PROGETTO CON LA TREE VIEW |
| 3 | `CDM_ANGRAFE` | PROGETTO ANAGRAFE CONDOMINIALE | SOLO ANAGRAFICA |
| 4 | `FORM_AUTOCARICA` | PROGETTO FORM AUTOCARICANTE | FORM_AUTOCARICANTE |
| 5 | `LLPPGest` | PROGETTO LAVORI PUBBLICI | LAVORI PUBBLICI |
| 6 | `GeCasa` | PROGETTO CASA | GESTIONE CASA |
| 7 | `GeCondom` | PROGETTO CONDOMINIO | GESTIONE DEI CONDOMINI |
| 8 | `LTT` | PROGETTO LOTTO | GESTIONE DEL LOTTO |
| 9 | `Pres3000` | PROGETTO PRES3000 | PROGETTO DI GESTIONE DELLE PRESENZE |
| 10 | `RUST` | PROGETTO GESTIONE RUST | PROGETTO DEI PROGRAMMI DI RUST |
| 11 | `MsysDbEst` | PROGETTO IMPORTA DB ESTERNO | PROGETTO PER IMPORTARE OGGETTI DAL DB ESTERNO |
| 0 | `ESPORTA_IMPORTA_Msys` | PROGETTO ESPORTA IMPORTA OGGETTI MSYS | GLI OGGETTI DEL PROGETTO ESPORTA ED IMPORTA |
| 0 | `ESPORTA_OGGETTI_MSYS` | PROGETTO_ESPORTA_OGGETTI | ESPORTO IN UN DB ESTERNO GLI OGGETTI DI UN PROGETTO |
| 0 | `GE_ESPROPRI` | PROGETTO MENU | PROGETTO DI GESTIONE DEGLI ESPROPRI |
| 0 | `GE_MENU` | PROGETTO MENU | PROGETTO MENU DI MSYS |
| 0 | `GE_Object_PROGETTI` | GE_OGGETTI_PROGETTI | LA GESTIONE DEGLI OGGETTI DEI PROGETTI |
| 0 | `GE_PROGETTI_MSYS` | PROGETTO GESTIONE PROGETTI MSYS | GESTISCO GLI OGGETTI DEI PROGETTI DEFINITI |
| 0 | `LLPP_IMPEGNI` | PROGETTO_IMPEGNI_LLPP | PROGETTI IMPEGNI DEL LAVORI PUBBLICI |
| 0 | `Msys_DF11` | PROGETTO_Msys_DF11 | PROGETTO CHE IMPOSTA LA DEFINIZIONE DEI PROGETTI |
| 0 | `MSys_GE_FILE` | PROGETTO GESTIONE FILE | UTILIZZO LE API WINDOWS PER LA GESTIONE DEI FILE |
| 0 | `MSys_TB01_PROGETTI` | PROGETTO TB01 PROGETTI | COME GESTIRE I PROGETTI CREATI |
| 0 | `Pres30002` | PROGETTO PRES3000 | PROGETTO PRESENZE PRES3000 |
| 0 | `TB04_to_GE_TB04` | PROGETTO ESPORTA OGGETTI TB04 IN GE TB04 | ESPORTA OGGETTI DATABASE ESTERNI SU GE_TB04 |
| 0 | `UTILITA_Msys` | PROGETTO Msys UTILITA ALTRI PROGETTI | PROGETTO UTILITA PER LA GESTIONE DEI VARI OGGETTI |

Il database di origine (`DISCO_ORIG_s` + `PATH_ORIG_s` + `DATABASE_ORIG_s`) è compilato per intero solo per 8 progetti, e punta quasi sempre alla vecchia struttura `MSYS_OGGETTI` o a `C:\GESTIONI\...` (capitolo 9).

### 3.3 Tabella di lavoro PROGETTI_Msys_TB01_PROJECT_TMP

Contiene **il progetto scelto** nella maschera (oggi `TB04_to_GE_TB04`). Ha in più i campi di destinazione (`DISCO_DEST_s`, `PATH_DEST_s`, `DATABASE_DEST_s`, `NOTA_PROG_DEST_s`): servono per esportare gli oggetti del progetto in un database di destinazione.

## 4. TB02 – Tipi di oggetto per progetto (Msys_TB02_TIPO_OGGETTI)

### 4.1 Campi

| Campo | Tipo | Significato |
| --- | --- | --- |
| `COD_PROGETTO_s` | Testo 45 | progetto (legame con TB01) |
| `TIPOGGETTO_s` | Testo 255 | tipo di oggetto: `FORMS`, `QUERY`, `TABLE`, `REPORTS`, `MACRO`, `MODULES`, `PAGES` |
| `PROGETTO_s`, `NOTE_PROGETTO_s` | Testo 50 | descrizione e note, ricopiate dal progetto |
| `GROUP_QUOTE_s` | Testo 50 | gruppo di quote del tipo di oggetto |
| `IdOrd_lng`, flag, date, `ID_lng` | | servizio, come in TB01 |

### 4.2 Dizionario dei tipi (MSys_DF02_TIPO_OGGETTO)

I tipi ammessi sono definiti nel mini-progetto DF02: `FORMS`, `TABLE`, `QUERY`, `REPORTS`, `PAGES`, `MACRO`, `MODULES`, `GROUP`, `TUTTI`, `TABLES_LINK`.

### 4.3 Tipi presenti per progetto

| COD_PROGETTO_s | Tipi |
| --- | --- |
| `MSys` | 7 |
| `GE_ESPROPRI`, `TREE_VIEW` | 6 ciascuno |
| `MSys_TB01_PROGETTI` | 5 |
| `CDM_ANGRAFE`, `GE_Object_PROGETTI`, `UTILITA_Msys` | 4 ciascuno |
| `FORM_AUTOCARICA` | 3 |
| `LLPP_IMPEGNI` | 2 |
| `MSys_GE_FILE`, `TB04_to_GE_TB04` | 1 ciascuno |

13 progetti di TB01 non hanno ancora tipi di oggetto, per esempio `LLPPGest`, `GeCasa`, `GeCondom`, `LTT`, `RUST`, `Pres3000`.

## 5. TB03 – Oggetti di ogni progetto (Msys_TB03_ELENCO_ObjProgetti)

### 5.1 Campi principali

| Campo | Tipo | Significato |
| --- | --- | --- |
| `COD_PROGETTO_s` | Testo 45 | progetto a cui appartiene l'oggetto |
| `TIPOGGETTO_s` | Testo 50 | tipo di oggetto (legame con TB02) |
| `NOMEOGGETTO_s` | Testo 255 | **nome dell'oggetto** in Access |
| `NRO_OGGETTO_i` | Intero | numero d'ordine dell'oggetto nel progetto |
| `TIPO_s` | Testo 50 | sottotipo (per esempio tabella locale o collegata) |
| `NOTA_OGGETTO_s`, `NOTEex_s` | Testo 50 / Memo | note brevi ed estese sull'oggetto |
| `EXPORT_b` | Sì/No | oggetto da esportare |
| `CODICE_QUOTE_s`, `NOTE_CODICE_QUOTE_s` | Testo 50 | gruppo di quote dell'oggetto |
| `DISCO_s`, `PATH_s`, `DATABASE_s` | Testo | database in cui si trova l'oggetto |
| `PROPRIETA`, `VALOREPROPRIETA`, `COMANDO_s` | Testo / Memo | proprietà e comando associati |
| `Attribute_by`, `Expression_m`, `Flag_b`, `LvExtra_i`, `Name1_s`, `Name2_s`, `ObjectId_lng`, `Order_b` | | copia delle informazioni di sistema di Access (`MSysObjects`) |
| `IdOrd_lng`, flag, date, `ID_lng` | | servizio |

### 5.2 Oggetti presenti (377)

| COD_PROGETTO_s | FORMS | QUERY | TABLE | MODULES | MACRO | REPORTS | Totale |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `MSys` | 52 | 66 | 8 | 49 | 1 | 2 | 178 |
| `GE_Object_PROGETTI` | 13 | 19 | 11 | – | 3 | – | 46 |
| `LLPP_IMPEGNI` | 40 | – | – | – | – | – | 40 |
| `UTILITA_Msys` | 4 | 9 | 27 | – | – | – | 40 |
| `FORM_AUTOCARICA` | 11 | 3 | 3 | – | – | – | 17 |
| `CDM_ANGRAFE` | 2 | 5 | 8 | – | – | – | 15 |
| `TREE_VIEW` | 4 | 3 | 4 | – | 1 | 2 | 14 |
| `GE_ESPROPRI` | 10 | – | – | – | 1 | – | 11 |
| `PRES200` | – | 11 | – | – | – | – | 11 |
| `MSys_GE_FILE` | 1 | – | – | 1 | – | – | 2 |
| `TB04_to_GE_TB04` | – | 2 | – | – | – | – | 2 |
| `LTT` | 1 | – | – | – | – | – | 1 |

## 6. Il front-end GE_Msys_TB01_PROGETTI.mdb

### 6.1 Tabelle

- **Collegate:** `PROGETTI_Msys_TB01_PROJECT` (e `_TMP`), `PROGETTI_Msys_TB02_TIPO_OGGETTI`, `PROGETTI_Msys_TB03_ELENCO_ObjProgetti`, più le versioni `Msys_TB0x_...`, `Msys_DF11_COD_PROGETTO`, `MSys_DF02_TIPO_OGGETTO`, `MSYS_TB04_DB_ESTERNO`, `MSys_TB0502_VISUAL_PAG_SEMPLICE`.
- **Attenzione:** tutti questi collegamenti puntano ai file della vecchia struttura `PROGETTI_MDB\MSYS_OGGETTI\MSYS\MDB\...`, non ai file in questa cartella (capitolo 9, A1).
- **Locali:** `Msys_ESPORTA_DB_EST` (e `_TMP`) e `Msys_ESPORTA_ELENCO_OGGETTI_DB` (e `_TMP`), che preparano l'esportazione degli oggetti in un database esterno; le tabelle `MODELLO_Tb01_*` sono modelli.

### 6.2 La maschera principale PROGETTI_Msys_TB01Frm00_GE_PROGETTI

In testata c'è la combo **FILTRO** (`TXT_FILTRO`, origine `PROGETTI_Msys_TB01Qry01_04_CODICI_PROJECT`) per scegliere il codice progetto. Nel corpo ci sono 11 schede:

| Scheda | Sottomaschera | Contenuto |
| --- | --- | --- |
| PROGETTI | `PROGETTI_Msys_TB01Frm01_01_PROJECT` | elenco dei progetti; dentro, i tipi di oggetto del progetto |
| PROGETTO TMP | `PROGETTI_Msys_TB01Frm01_01_PROJECT_TMP` | il progetto scelto, con i suoi tipi di oggetto |
| TIPO DI OGGETTI | `PROGETTI_Msys_TB02Frm01_01_TIPO_OGGETTI` | tipi di oggetto; dentro, l'elenco degli oggetti di quel tipo |
| ELENCO OGGETTI | `PROGETTI_Msys_TB03Frm01_01_ELENCO_ObjProgetti` | tutti gli oggetti |
| LE FORM | `PROGETTI_Msys_TB03Frm01_02_ObjProgetti_FORM=COD_PROGETTO_s` | oggetti di tipo `FORMS` |
| LE MACRO | `..._ObjProgetti_MACRO=COD_PROGETTO_s` | oggetti di tipo `MACRO` |
| LE QUERY | `..._ObjProgetti_QUERY=COD_PROGETTO_s` | oggetti di tipo `QUERY` |
| I REPORT | `..._ObjProgetti_REPORT=COD_PROGETTO_s` | oggetti di tipo `REPORTS` |
| LE TABLE | `..._ObjProgetti_TABLE=COD_PROGETTO_s` | oggetti di tipo `TABLE` |
| CODICE_PROGETTO | `Msys_DF11_Frm01_M01_GE_CODICE_PROGETTI_TUTTI` | elenco dei codici progetto (DF11) |
| LE FORM DEL PROGETTO SCELTO | `MSys_TAB502_Frm01_01_VISUAL_PAG_SEMPLICE` | form del progetto da visualizzare nelle pagine |

### 6.3 Sottomaschere annidate

```
  PROGETTI_Msys_TB01Frm01_01_PROJECT            (un progetto)
     └─ PROGETTI_Msys_TB02Frm01_01_TIPO_OGGETTI    collegata su COD_PROGETTO_s
           └─ PROGETTI_Msys_TB03Frm01_01_ELENCO_ObjProgetti
                                                  collegata su COD_PROGETTO_s;TIPOGGETTO_s
```

Scorrendo i progetti si vedono i loro tipi di oggetto; scorrendo i tipi si vedono gli oggetti di quel tipo.

### 6.4 Come si lavora

1. **Nuovo progetto.** Nella scheda PROGETTI si scrive una riga nuova. La combo `COD_PROGETTO_s` propone i codici di DF11: scegliendone uno, l'evento `COD_PROGETTO_s_TXT_Change` compila da solo descrizione, note, disco, cartella e database, prendendoli dalle colonne della combo.
2. **Scelta del progetto di lavoro.** Il **doppio clic** sul codice progetto (`COD_PROGETTO_s_TXT_DblClick`):
   - svuota `PROGETTI_Msys_TB01_PROJECT_TMP` e vi scrive il codice scelto;
   - se la maschera principale è aperta, completa la riga con la query `PROGETTI_Msys_TB01Qry01_03_PROJECT_TMP_UPDATE`;
   - copia il codice nella combo FILTRO e filtra la scheda PROGETTI su quel progetto.
3. **Filtro.** Cambiando la combo FILTRO (`TXT_FILTRO_AfterUpdate` → `TXT_FILTRO_DblClick`), la scheda PROGETTI mostra i progetti il cui codice contiene il testo scelto. Con il filtro vuoto, tutte le schede tornano alle query di base.
4. **Tipi di oggetto.** Nella sottomaschera TIPO DI OGGETTI si aggiunge un tipo. `PARENT_DINAMICO_Funct` risale le maschere fino a quella principale, legge il progetto dalla combo FILTRO e lo scrive nel campo `COD_PROGETTO_s` della riga.
5. **Oggetti.** Nella sottomaschera ELENCO OGGETTI si aggiungono gli oggetti, con nome, numero d'ordine e note. Le schede LE FORM, LE QUERY, ecc. li mostrano divisi per tipo.

### 6.5 Query principali

| Query | Tipo | Uso |
| --- | --- | --- |
| `PROGETTI_Msys_TB01Qry01_01_PROJECT` | selezione | progetti (scheda PROGETTI) |
| `PROGETTI_Msys_TB01Qry01_01_PROJECT_TMP` | selezione | progetto scelto |
| `PROGETTI_Msys_TB01Qry01_01_PROJECT_TMP_Delete`, `..._02_PROJECT_TMP_Delete` | eliminazione | svuotano la tabella del progetto scelto |
| `PROGETTI_Msys_TB01Qry01_03_PROJECT_TMP_UPDATE` | aggiornamento | completa il progetto scelto con i dati dell'archivio |
| `PROGETTI_Msys_TB01Qry01_04_CODICI_PROJECT` | selezione | codici per la combo FILTRO |
| `PROGETTI_Msys_TB02Qry01_01_TIPO_OGGETTI` | selezione | tipi di oggetto |
| `PROGETTI_Msys_TB03Qry01_01_ELENCO_ObjProgetti` | selezione | tutti gli oggetti |
| `PROGETTI_Msys_TB03Qry01_02_ObjProgetti_<TIPO>=COD_PROGETTO_s` | selezione | oggetti di un tipo (`FORMS`, `MACRO`, `QUERY`, `REPORTS`, `TABLE`) |
| `PROGETTI_Msys_TB03Qry01_03_GROUP_QUOTE_s` | selezione | gruppi di quote dai tipi di oggetto |
| `PROGETTI_Msys_TB04Qry01_01_Database_DEST` | selezione | database esterni di destinazione |
| `Msys_DF11_Qry01_01_COD_PROGETTO` | selezione | codici progetto di DF11 |

### 6.6 Altre maschere e codice

| Oggetto | Contenuto |
| --- | --- |
| `UTILITA_Msys_Frm22_GESTIONE_Oggetti_MSYS_new`, `UTILITA_Msys_Frm22_IMPORTA_CANCELLA_Oggetti_Modello_MSYS` | 29 pulsanti per **importare o cancellare** in blocco form, query, moduli, report e tabelle collegate di un altro database |
| `UTILITA_Msys_Frm25_ESPORTA_Modello_MSYS` (e `_DaAprire`) | **esporta** le form in un database esterno |
| `MSys_TUTTI_PROG_FrmM01_01_PROGETTI` | elenco progetti con le pagine da caricare |
| `Msys_DF11_Frm01_M01_GE_CODICE_PROGETTI_TUTTI` | gestione dei codici progetto DF11, con apertura del PDF del progetto |
| `FileList` | scelta di file e cartelle |
| `CLASSE_GESTIONE_OGGETTI_DATABASE` (8.313 righe) | classe di servizio: controlla se la maschera principale è aperta, cerca oggetti nel database, salva parametri nelle tabelle `_TMP` |
| macro `COLLEGA_Mcr01_GEST_MENU_Object`, `CANCELLA_Mcr01_GEST_MENU_Object` | ricollegano o cancellano le tabelle del menu |

## 7. Come si usa

### 7.1 Creare un progetto

1. Se il codice non esiste, aggiungerlo in DF11 (scheda CODICE_PROGETTO).
2. Nella scheda PROGETTI inserire una riga e scegliere il codice. Descrizione e note si compilano da sole, ma si possono completare: `NOTE_PROGETTO_s` ha solo 50 caratteri, le note lunghe vanno in `NOTE_DATABASE_ORIG_s` o in `NOTE_GROUP_COD_PROG_s`.
3. Compilare il database di origine (`DISCO_ORIG_s`, `PATH_ORIG_s`, `DATABASE_ORIG_s`) con il percorso in `PROGETTI_MDB\MSYS\`.
4. Impostare `IdOrd_lng` per l'ordine.

### 7.2 Dichiarare i tipi di oggetto del progetto

Fare doppio clic sul codice progetto (diventa il progetto di lavoro), poi nella scheda TIPO DI OGGETTI aggiungere una riga per ogni tipo usato: `FORMS`, `QUERY`, `TABLE`, `MODULES`, `REPORTS`, `MACRO`.

### 7.3 Collegare gli oggetti al progetto

Nella sottomaschera ELENCO OGGETTI (dentro il tipo) aggiungere ogni oggetto con `NOMEOGGETTO_s`, `NRO_OGGETTO_i` e una nota in `NOTA_OGGETTO_s`. Il tipo e il progetto vengono dal collegamento con la maschera madre. Gli oggetti segnati con `EXPORT_b` sono quelli da esportare.

## 8. Collegamenti con il resto di MSys

- **DF11:** fornisce l'elenco dei codici progetto.
- **DF02:** fornisce il dizionario dei tipi di oggetto.
- **TB04:** contiene i database esterni di destinazione.
- **TB0502:** gestisce la visualizzazione delle form nelle pagine.
- **Catalogo collegamenti TB99** (`MSys_TB99_LINK.mdb`): oggi contiene le tabelle di `Msys_TB01_PROGETTI.mdb` di questa cartella. TB02 e TB03 risultavano solo in `MSYS_OGGETTI`, ma ora i loro file sono anche qui e si possono aggiungere al catalogo.
- **Menu TB06:** il progetto `GE_MENU` di TB01 corrisponde al mini-progetto menu `MSys_TB06_MENU`, che ha un suo master plan. I codici progetto del menu (`GeCasa`, `GeCondom`, `LLPPGest`, `LTT`, `MSys`, `Pres3000`, `RUST`) sono gli stessi di TB01.

## 9. Anomalie trovate il 15/11/2026

| # | Dove | Problema | Effetto |
| --- | --- | --- | --- |
| A1 | front-end | tutti i collegamenti puntano ai file in `MSYS_OGGETTI`, non a quelli in `MSYS\MSys_TB01_PROGETTI\MDB\` | le modifiche fatte dalla maschera finiscono nei file vecchi; le copie di questa cartella resteranno indietro |
| A2 | i tre back-end | ogni tabella principale esiste due volte (`Msys_TB0x_...` e `PROGETTI_...`) | due archivi da tenere allineati; oggi i dati coincidono |
| A3 | front-end | i link del menu `GEST_MENU_*` puntano a `y:\LAVORI_PUBBLICI...`, percorso non raggiungibile | link morti |
| A4 | front-end | le query `MSys_TUTTI_PROG_*` leggono `MSys_TUTTI_PROG` e `MSys_TUTTI_PROG_VIS_PAGINE`, che non sono collegate | la maschera `MSys_TUTTI_PROG_FrmM01_01_PROGETTI` va in errore |
| A5 | TB01 | `DISCO/PATH/DATABASE_ORIG_s` vuoti o con il solo disco per 16 progetti; gli altri puntano a `MSYS_OGGETTI` o `C:\GESTIONI`; un percorso è scritto male (`C:c:\Casa\...`) | il database di origine non è affidabile |
| A6 | TB01 | `IdOrd_lng = 0` per 13 progetti; `GROUP_COD_PROG_s` sempre vuoto | ordine e raggruppamento non usati |
| A7 | TB01 | `NOTE_PROGETTO_s` lungo solo 50 caratteri | le note esplicative sono troncate |
| A8 | TB01 | due progetti presenze, `Pres3000` e `Pres30002`; in TB03 gli oggetti presenze sono sotto `PRES200`, codice che non esiste in TB01 | 11 oggetti orfani |
| A9 | TB02/TB03 | `LTT` ha un oggetto in TB03 ma nessun tipo in TB02; `MSys_TB01_PROGETTI` ha 5 tipi ma nessun oggetto | dati incompleti, non visibili nelle sottomaschere annidate |
| A10 | TB02 | tipi scritti in due modi: `MODULES` e `MODULI` | lo stesso tipo conta come due |
| A11 | front-end | manca una scheda per i **moduli**, mentre `MSys` ha 49 moduli in TB03 | i moduli si vedono solo in ELENCO OGGETTI |
| A12 | front-end | le query `ObjProgetti_<TIPO>` filtrano solo sul tipo; il progetto, nonostante il nome `=COD_PROGETTO_s`, non è nel filtro | le schede per tipo mostrano gli oggetti di tutti i progetti |
| A13 | TB03 | righe segnaposto (`ANAGRAFE  QUAELE OGGETTO ???`, `anagrafe ii oggetto`) | dati da completare |
| A14 | `Msys_TB01_PROGETTI.mdb` | contiene anche `DF03_GROUP_QUOTE` (tabella di DF03) e link a `MSys_DF02.mdb` con percorso sbagliato | dati fuori posto |
| A15 | tutti | tabelle "Errori di incollamento" e "Errori di salvataggio correzione automatica nome" | residui di Access |

## 10. Piano dei prossimi passi

### 10.1 Fase 1 – Un solo archivio, nella cartella giusta

1. Ricollegare il front-end ai tre back-end in `MSYS\MSys_TB01_PROGETTI\MDB\` (A1). Si può fare con il catalogo TB99 e il modulo `Mdl05_INVIA_LINK_FRONTEND`, dopo aver registrato TB02 e TB03 nel catalogo.
2. Scegliere una sola versione per ogni tabella (A2): il front-end usa `PROGETTI_...`. L'altra versione va tolta, oppure lasciata come semplice collegamento.
3. Togliere i link morti e quelli non usati (A3, A4, A14).

### 10.2 Fase 2 – Dati dei progetti completi

1. Per ogni progetto compilare descrizione, note e database di origine con il percorso in `PROGETTI_MDB\MSYS\` (A5); correggere `C:c:\Casa\...`.
2. Allargare `NOTE_PROGETTO_s` a 255 caratteri (A7) e dare un `IdOrd_lng` a tutti (A6).
3. Unificare i progetti presenze: un solo codice per `Pres3000`, `Pres30002` e `PRES200` (A8).
4. Uniformare i tipi: solo i codici di DF02 (`MODULES`, non `MODULI`) (A10).

### 10.3 Fase 3 – Oggetti completi per ogni progetto

1. Per i 13 progetti senza tipi di oggetto, dichiarare i tipi (TB02) e gli oggetti (TB03).
2. Allineare TB02 e TB03: ogni tipo usato in TB03 deve esistere in TB02 per lo stesso progetto (A9).
3. Aggiungere la scheda LE MODULES (A11) e il filtro per progetto nelle query `ObjProgetti_<TIPO>` (A12).
4. Riempire TB03 in automatico leggendo gli oggetti dal database di origine di ogni progetto. `MSysObjects` e la classe `CLASSE_GESTIONE_OGGETTI_DATABASE` hanno già le funzioni di ricerca; le righe segnaposto vanno poi tolte (A13).

### 10.4 Fase 4 – Usare l'elenco degli oggetti

1. Con l'elenco TB03 di un progetto, importare o esportare solo gli oggetti previsti (maschere `UTILITA_Msys_Frm22` e `Frm25`, tabelle `Msys_ESPORTA_*`).
2. Per le tabelle del progetto, inviare i collegamenti al suo front-end con TB99 (`Msys_TB99_LINK_FRONTEND`).
3. Togliere le tabelle residue di Access (A15).
