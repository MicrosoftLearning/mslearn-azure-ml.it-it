---
lab:
  title: Eseguire il training di un modello con La finestra di progettazione di Azure Machine Learning
---

# Eseguire il training di un modello con La finestra di progettazione di Azure Machine Learning

Progettazione di Azure Machine Learning offre un'interfaccia di trascinamento della selezione con cui è possibile definire un flusso di lavoro. È possibile creare un flusso di lavoro per eseguire il training di un modello, il test e il confronto di più algoritmi con facilità.

In questo esercizio si userà Progettazione per eseguire rapidamente il training e confrontare due algoritmi di classificazione.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'area di lavoro di* Azure Machine Learning *offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite studio, Python SDK e l'interfaccia della riga di comando di Azure.

Si userà uno script shell che usa l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e delle risorse necessarie. Successivamente, si userà la finestra di progettazione nel studio di Azure Machine Learning per eseguire il training e confrontare i modelli.

### Creare l'area di lavoro e il cluster di calcolo

Per creare l'area di lavoro di Azure Machine Learning e un cluster di calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari vengono raggruppati in uno script shell da eseguire.

1. In un browser aprire il portale di Azure in `https://portal.azure.com/`, accedere con l'account Microsoft.
1. Selezionare il \[pulsante >_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Verrà aperto un riquadro di Cloud Shell nella parte inferiore del portale.
1. Selezionare **Bash** se richiesto. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che la sottoscrizione corretta sia specificata e selezionare Crea archiviazione **** se viene chiesto di creare l'archiviazione per Cloud Shell. Attendere la creazione dello spazio di archiviazione.
1. Nel terminale immettere i comandi seguenti per clonare questo repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare il codice copiato in Cloud Shell.

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo script setup.sh che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/05
    ./setup.sh
    ```

    > Ignorare eventuali messaggi di errore che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. Questa operazione richiede in genere circa 5-10 minuti.

## Configurare una nuova pipeline

Dopo aver creato l'area di lavoro e il cluster di calcolo necessario, è possibile aprire il studio di Azure Machine Learning e creare una pipeline di training con progettazione.

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa **pagina Panoramica** selezionare **Avvia studio**. Nel browser verrà aperta un'altra scheda per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. All'interno della studio di Azure Machine Learning passare alla **pagina Calcolo** e verificare che il cluster di calcolo creato nella sezione precedente esista. Il cluster deve essere inattiva e avere 0 nodi in esecuzione.
1. Passare alla **pagina Progettazione** .
1. Selezionare la **scheda Personalizzata** nella parte superiore della pagina.
1. Creare una nuova pipeline vuota usando componenti personalizzati.
1. Modificare il nome predefinito della pipeline (**Pipeline-Created-on-date***) `Train-Diabetes-Classifier` in selezionando l'icona a forma di matita a destra.


## Crea una nuova pipeline

Per eseguire il training di un modello, sono necessari dati. È possibile usare tutti i dati archiviati in un archivio dati o usare un URL accessibile pubblicamente.

1. Nel menu a sinistra selezionare la **scheda Dati** .
1. Trascinare e rilasciare il **componente diabetes-folder** nell'area di disegno.

    Ora che si dispone dei dati, è possibile continuare creando una pipeline usando componenti personalizzati già esistenti nell'area di lavoro (sono stati creati automaticamente durante l'installazione).

1. Nel menu a sinistra selezionare la **scheda Componenti** .
1. Trascinare e rilasciare il **componente Rimuovi righe** vuote nell'area di disegno, sotto la **cartella diabetes-folder**.
1. Connessione l'output dei dati all'input del nuovo componente.
1. Trascinare e rilasciare il **componente Normalize Numeric Columns (Normalizza colonne** numeriche) nell'area di disegno, sotto rimuovi **righe** vuote.
1. Connessione l'output del componente precedente all'input del nuovo componente.
1. Trascinare e rilasciare il **componente Train a Decision Tree Classifier Model (Esegui training di un modello** di classificazione albero delle decisioni) nell'area di disegno, sotto le **colonne** numeriche normalizzate.
1. Connessione l'output del componente precedente all'input del nuovo componente.
1. Selezionare **Configura e invia** e nella **pagina Configura processo pipeline** creare un nuovo esperimento e denominarlo `diabetes-designer-pipeline`e quindi selezionare **Avanti**.
1. **In Input e output** non apportare modifiche e selezionare **Avanti**.
1. **Nelle impostazioni** di runtime selezionare **Cluster** di calcolo e in Selezionare il **cluster** di calcolo di Azure ML selezionare il *cluster* aml.
1. Selezionare **Rivedi e invia** e quindi invia **** per avviare l'esecuzione della pipeline.
1. È possibile controllare lo stato dell'esecuzione passando alla **pagina Pipeline** e selezionando la **pipeline Train-Diabetes-Classifier** .
1. Attendere il completamento di tutti i componenti.

    L'invio del processo inizializzerà il cluster di calcolo. Poiché il cluster di calcolo è inattivo fino ad ora, il ridimensionamento del cluster in più di 0 nodi potrebbe richiedere del tempo. Dopo aver ridimensionato il cluster, verrà avviata automaticamente l'esecuzione della pipeline.

Sarà possibile tenere traccia dell'esecuzione di ogni componente. Quando la pipeline ha esito negativo, sarà possibile esplorare il componente non riuscito e il motivo per cui non è riuscito. I messaggi di errore verranno visualizzati nella **scheda Output e log** della panoramica del processo.

## Eseguire il training di un secondo modello per confrontare

Per confrontare tra algoritmi e valutare le prestazioni migliori, è possibile eseguire il training di due modelli all'interno di una pipeline e confrontare.

1. Tornare alla **finestra di progettazione** e selezionare la bozza della **pipeline Train-Diabetes-Classifier** .
1. Aggiungere il **componente Train a Logistic Regression Classifier Model (Esegui training di un modello** di classificazione di regressione logistica) all'area di disegno accanto all'altro componente di training.
1. Connessione l'output dell'oggetto **Normalizzare il componente Colonne** numeriche per l'input del nuovo componente di training.
1. Nella parte superiore selezionare **Configura e invia**.
1. Nella **pagina Informazioni di** base creare un nuovo esperimento denominato `designer-compare-classification`ed eseguirlo.
1. Selezionare **Rivedi e invia** e quindi invia **** per avviare l'esecuzione della pipeline.
1. È possibile controllare lo stato dell'esecuzione passando alla **pagina Pipeline** e selezionando la **pipeline Train-Diabetes-Classifier** con l'esperimento **designer-compare-classification** .
1. Attendere il completamento di tutti i componenti.  
1. Selezionare **Panoramica** del processo, quindi selezionare la **scheda Metriche** per esaminare i risultati per entrambi i componenti di training.
1. Provare a determinare quale modello ha prestazioni migliori.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il **gruppo di risorse rg-dp100-...** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
