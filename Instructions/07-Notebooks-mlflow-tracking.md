---
lab:
  title: Tenere traccia del training del modello nei notebook con MLflow
---

# Tenere traccia del training del modello nei notebook con MLflow

Spesso si inizierà un nuovo progetto di data science sperimentando e eseguendo il training di più modelli. Per tenere traccia del lavoro e mantenere una panoramica dei modelli di cui si esegue il training e delle prestazioni, è possibile usare il rilevamento MLflow.

In questo esercizio si eseguirà MLflow all'interno di un notebook in esecuzione in un'istanza di calcolo per registrare il training del modello.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

*Un'area di lavoro di* Azure Machine Learning offre una posizione centrale per la gestione di tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite studio, Python SDK e l'interfaccia della riga di comando di Azure.

Si userà l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e le risorse di calcolo necessarie e si userà Python SDK per eseguire il training di un modello di classificazione con Machine Learning automatizzato.

### Creare l'area di lavoro e le risorse di calcolo

Per creare l'area di lavoro di Azure Machine Learning e un'istanza di calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari vengono raggruppati in uno script shell da eseguire.
1. In un browser aprire il portale di Azure all'indirizzo `https://portal.azure.com/`, accedere con l'account Microsoft.
1. Selezionare il \[ pulsante>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Verrà aperto un riquadro di Cloud Shell nella parte inferiore del portale.
1. Selezionare **Bash** se richiesto. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell che si vuole usare (*Bash* o *PowerShell*).
1. Verificare che la sottoscrizione corretta sia specificata e selezionare **Crea archiviazione** se viene chiesto di creare l'archiviazione per Cloud Shell. Attendere la creazione dello spazio di archiviazione.
1. Nel terminale immettere i comandi seguenti per clonare il repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare il codice copiato nel Cloud Shell. 

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo script **setup.sh** che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/07
    ./setup.sh
    ```

    > Ignorare eventuali messaggi di errore che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. L'operazione richiede in genere circa 5-10 minuti.

## Clonare i materiali del lab

Dopo aver creato l'area di lavoro e le risorse di calcolo necessarie, è possibile aprire il studio di Azure Machine Learning e clonare i materiali del lab nell'area di lavoro.

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella pagina **Panoramica** selezionare **Avvia studio**. Nel browser verrà aperta un'altra scheda per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. All'interno della studio di Azure Machine Learning passare alla pagina **Calcolo** e verificare che l'istanza di calcolo creata nella sezione precedente esista. L'istanza di calcolo deve essere in esecuzione.
1. Nella scheda **Istanze** di calcolo individuare l'istanza di calcolo e selezionare l'applicazione **Terminale** .
1. Nel terminale installare Python SDK e la libreria MLflow nell'istanza di calcolo eseguendo i comandi seguenti nel terminale:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mlflow
    ```

    > Ignorare eventuali messaggi di errore che dicono che non è stato possibile trovare e disinstallare i pacchetti.

1. Eseguire il comando seguente per clonare un repository Git contenente un notebook, dati e altri file nell'area di lavoro:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Al termine del comando, nel riquadro **File** fare clic ** su&#8635;** per aggiornare la visualizzazione e verificare che sia stata creata una nuova cartella **Users/*your-user-name*/azure-ml-labs** .

## Tenere traccia del training del modello con MLflow

Ora che sono disponibili tutte le risorse necessarie, è possibile eseguire il notebook per configurare e usare MLflow durante il training dei modelli in un notebook.

1. Aprire il **training del modello Labs/07/Track con il notebook MLflow.ipynb** .

    > Selezionare **Autentica** e seguire i passaggi necessari se viene visualizzata una notifica che richiede di eseguire l'autenticazione.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML** .
1. Eseguire tutte le celle nel notebook.
1. Esaminare il nuovo processo creato ogni volta che si esegue il training di un modello.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare alla portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
