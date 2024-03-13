---
lab:
  title: Trovare il modello di classificazione migliore con Machine Learning automatizzato
---

# Trovare il modello di classificazione migliore con Machine Learning automatizzato

Per determinare l'algoritmo e le trasformazioni di pre-elaborazione appropriati per il training dei modelli può essere necessario eseguire numerose ipotesi e sperimentazioni.

In questo esercizio si userà Machine Learning automatizzato per determinare l'algoritmo e i passaggi di pre-elaborazione più idonei per un modello avviando più esecuzioni di training in parallelo.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'area di lavoro di* Azure Machine Learning *offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite studio, Python SDK e l'interfaccia della riga di comando di Azure.

Si userà l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e le risorse di calcolo necessarie e si userà Python SDK per eseguire il training di un modello di classificazione con Machine Learning automatizzato.

### Creare l'area di lavoro e le risorse di calcolo

Per creare l'area di lavoro di Azure Machine Learning, un'istanza di calcolo e un cluster di calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari vengono raggruppati in uno script shell da eseguire.

1. In un browser aprire il portale di Azure in `https://portal.azure.com/`, accedere con l'account Microsoft.
1. Selezionare il \[pulsante >_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Selezionare **Bash** se richiesto. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che la sottoscrizione corretta sia specificata e selezionare Crea archiviazione **** se viene chiesto di creare l'archiviazione per Cloud Shell. Attendere la creazione dello spazio di archiviazione.
1. Nel terminale immettere i comandi seguenti per clonare questo repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare il codice copiato in Cloud Shell.

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo **script setup.sh** che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/06
    ./setup.sh
    ```

    > Ignorare eventuali messaggi di errore che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. Questa operazione richiede in genere circa 5-10 minuti.

## Clonare i materiali del lab

Dopo aver creato l'area di lavoro e le risorse di calcolo necessarie, è possibile aprire il studio di Azure Machine Learning e clonare i materiali del lab nell'area di lavoro.

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa **pagina Panoramica** selezionare **Avvia studio**. Nel browser verrà aperta un'altra scheda per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. All'interno del studio di Azure Machine Learning passare alla **pagina Calcolo** e verificare che l'istanza di calcolo e il cluster creati nella sezione precedente esistano. L'istanza di calcolo deve essere in esecuzione, il cluster deve essere inattiva e avere 0 nodi in esecuzione.
1. Nella **scheda Istanze** di calcolo individuare l'istanza di calcolo e selezionare l'applicazione **Terminale** .
1. Nel terminale installare Python SDK nell'istanza di calcolo eseguendo i comandi seguenti nel terminale:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi di errore che dicono che non è stato possibile trovare e disinstallare i pacchetti.

1. Eseguire il comando seguente per clonare un repository Git contenente notebook, dati e altri file nell'area di lavoro:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Al termine del comando, nel **riquadro File** fare clic su **&#8635;** per aggiornare la visualizzazione e verificare che sia stata creata una nuova **cartella Users/*your-user-name*/azure-ml-labs** .

## Eseguire il training di un modello di classificazione con Machine Learning automatizzato

Ora che sono disponibili tutte le risorse necessarie, è possibile eseguire il notebook per configurare e inviare il processo di Machine Learning automatizzato.

1. Aprire il **notebook Labs/06/Classification con machine learning.ipynb** automatizzato.

    > Selezionare **Autentica** e seguire i passaggi necessari se viene visualizzata una notifica che chiede di eseguire l'autenticazione.

1. Verificare che il notebook usi il **kernel Python 3.8 - AzureML** .
1. Eseguire tutte le celle nel notebook.

    Verrà creato un nuovo processo nell'area di lavoro di Azure Machine Learning. Il processo tiene traccia degli input definiti nella configurazione del processo, dell'asset di dati usato e degli output come le metriche per valutare i modelli.

    Si noti che i processi di Machine Learning automatizzati contengono processi figlio, che rappresentano singoli modelli sottoposti a training e altre attività necessarie per l'esecuzione.
1. Passare a **Processi** e selezionare l'esperimento **auto-ml-class-dev** .
1. Selezionare il processo nella **colonna Nome** visualizzato.
1. Attendere che lo stato cambi in **Completato**.
1. Quando lo stato del processo di Automazione di Machine Learning è stato modificato **in Completato**, esplorare i dettagli del processo in Studio:
    - La **scheda Protezione dati indica se i** dati di training hanno avuto problemi.
    - Nella **scheda Modelli** verranno visualizzati tutti i modelli sottoposti a training. Selezionare **Visualizza spiegazione** per il modello migliore per comprendere quali funzionalità hanno influenzato maggiormente il valore di destinazione.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il **gruppo di risorse rg-dp100-...** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
