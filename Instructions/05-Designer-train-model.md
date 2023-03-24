---
lab:
  title: Eseguire il training di un modello con Azure Machine Learning Designer
---

# Eseguire il training di un modello con Azure Machine Learning Designer

Azure Machine Learning Designer offre un'interfaccia di trascinamento e rilascio con cui è possibile definire un flusso di lavoro. È possibile creare un flusso di lavoro per eseguire il training di un modello, testare e confrontare più algoritmi con facilità.

In questo esercizio si userà progettazione per eseguire rapidamente il training e confrontare due algoritmi di classificazione.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

*Un'area di lavoro* di Azure Machine Learning offre un posto centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite studio, Python SDK e l'interfaccia della riga di comando di Azure. 

Si userà uno script shell che usa l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e le risorse necessarie. Successivamente, si userà progettazione nella studio di Azure Machine Learning per eseguire il training e confrontare i modelli.

### Creare l'area di lavoro e il cluster di calcolo

Per creare l'area di lavoro di Azure Machine Learning e un cluster di calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari vengono raggruppati in uno script shell da eseguire.

1. In un browser aprire il portale di Azure in `https://portal.azure.com/`, accedere con l'account Microsoft.
1. Selezionare il \[ pulsante>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Verrà aperto un riquadro di Cloud Shell nella parte inferiore del portale.
1. Selezionare **Bash** se richiesto. La prima volta che si apre cloud shell, verrà chiesto di scegliere il tipo di shell che si vuole usare (*Bash* o *PowerShell*). 
1. Verificare che la sottoscrizione corretta sia specificata e selezionare Crea archiviazione se viene chiesto di creare **l'archiviazione** per cloud shell. Attendere la creazione dell'archiviazione.
1. Nel terminale immettere i comandi seguenti per clonare questo repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare il codice copiato nella Cloud Shell. 

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo script di setup.sh che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/05
    ./setup.sh
    ```

    > Ignorare eventuali messaggi (errore) che dicono che le estensioni non sono state installate. 

1. Attendere il completamento dello script: in genere sono necessari circa 5-10 minuti. 

## Configurare una nuova pipeline

Dopo aver creato l'area di lavoro e il cluster di calcolo necessario, è possibile aprire il studio di Azure Machine Learning e creare una pipeline di training con progettazione. 

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-labs**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvia studio**. Un'altra scheda verrà aperta nel browser per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. Nella studio di Azure Machine Learning passare alla pagina **Calcolo** e verificare che il cluster di calcolo creato nella sezione precedente esista. Il cluster deve essere inattiva e avere 0 nodi in esecuzione.
1. Passare alla pagina **Progettazione** .
1. Selezionare la scheda **Personalizzata** nella parte superiore della pagina.
1. Creare una nuova pipeline vuota usando componenti personalizzati.
1. Modificare il nome della pipeline predefinito (**Pipeline-Create-on-date***) in `Train-Diabetes-Classifier` facendo clic sull'icona **&#9881;** a destra per aprire il riquadro **Impostazioni** .
1. È necessario specificare una destinazione di calcolo in cui eseguire la pipeline. Nel riquadro **Impostazioni** , in **Selezionare il tipo di calcolo** **e selezionare** Cluster di calcolo e in **Selezionare cluster di calcolo di Azure ML** e selezionare **aml-cluster**. Chiudere il riquadro delle impostazioni.

## Creare una nuova pipeline
Per eseguire il training di un modello, sono necessari dati. È possibile usare tutti i dati archiviati in un archivio dati o usare un URL accessibile pubblicamente.

1. Nel menu a sinistra selezionare la scheda **Dati** .
1. Trascinare e rilasciare il componente **della cartella diabete** nell'area di disegno.

    Dopo aver creato i dati, è possibile continuare creando una pipeline usando componenti personalizzati già esistenti all'interno dell'area di lavoro (creati per l'utente durante l'installazione).

1. Nel menu a sinistra selezionare la scheda **Componenti** .
1. Trascinare e rilasciare il componente **Rimuovi righe vuote** nell'area di disegno, sotto la **cartella diabete**. 
1. Connettere l'output dei dati all'input del nuovo componente.
1. Trascinare e rilasciare il componente **Normalizzare colonne numeriche** nell'area di disegno, sotto le **righe vuote Rimuovi**. 
1. Connettere l'output del componente precedente all'input del nuovo componente.
1. Trascinare e rilasciare il componente **Train a Decision Tree Classifier Model** nell'area di disegno, sotto l'oggetto **Rimuovi righe vuote**.
1. Connettere l'output del componente precedente all'input del nuovo componente. 
1. Inviare la pipeline. 
1. Creare un nuovo esperimento e denominarlo `diabetes-designer-pipeline`. 
1. Attendere il completamento di tutti i componenti.

    L'invio del processo inizializzerà il cluster di calcolo. Poiché il cluster di calcolo è stato inattivo fino a questo momento, potrebbe richiedere tempo per il ridimensionamento del cluster in più di 0 nodi. Dopo aver ridimensionato il cluster, verrà avviato automaticamente l'esecuzione della pipeline. 

1. È possibile visualizzare lo stato della pipeline eseguita selezionando **Dettagli processo** nel riquadro **Processi inviati** a sinistra.

Sarà possibile tenere traccia dell'esecuzione di ogni componente. Quando la pipeline ha esito negativo, sarà possibile esplorare quale componente non è riuscito e perché non è riuscito. I messaggi di errore verranno visualizzati nella scheda **Output e log** della panoramica del processo. 

## Eseguire il training di un secondo modello per confrontare

Per confrontare tra algoritmi e valutare le prestazioni migliori, è possibile eseguire il training di due modelli all'interno di una pipeline e confrontare.

1. All'interno della stessa pipeline in cui si è lavorato finora
1. Aggiungere il componente **Train a Logistic Regression Classifier Model** nell'area di disegno accanto all'altro componente di training.
1. Connettere l'output del componente **Normalizzare colonne numeriche** all'input del nuovo componente di training. 
1. Nella parte superiore selezionare **Invia**. 
1. Quando richiesto, creare un nuovo esperimento denominato `designer-compare-classification`e eseguirlo.  
1. Al termine della pipeline, esaminare le **metriche** per entrambi i componenti di training.
1. Provare e determinare quale modello ha eseguito meglio.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare alla portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-labs** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**. 
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.