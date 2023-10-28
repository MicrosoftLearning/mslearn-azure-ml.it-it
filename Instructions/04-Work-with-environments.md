---
lab:
  title: Usare gli ambienti in Azure Machine Learning
---

# Usare gli ambienti in Azure Machine Learning

Per eseguire notebook e script, è necessario assicurarsi che i pacchetti necessari siano installati. Gli ambienti consentono di specificare i runtime e i pacchetti Python che devono essere usati dal calcolo per eseguire il codice.

In questo esercizio verranno fornite informazioni sugli ambienti e su come usarli durante il training dei modelli di Machine Learning con calcolo di Azure Machine Learning.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

*Un'area di lavoro* di Azure Machine Learning offre un posto centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite studio, Python SDK e l'interfaccia della riga di comando di Azure.

Si userà l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e il calcolo necessario e si userà Python SDK per eseguire il training di un modello di classificazione con Machine Learning automatizzato.

### Creare l'area di lavoro e le risorse di calcolo

Per creare l'area di lavoro di Azure Machine Learning e le risorse di calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari vengono raggruppati in uno script shell da eseguire.

1. In un browser aprire il portale di Azure in `https://portal.azure.com/`, accedere con l'account Microsoft.
1. Selezionare il \[ pulsante>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Verrà aperto un riquadro di Cloud Shell nella parte inferiore del portale.
1. Selezionare **Bash** se richiesto. La prima volta che si apre cloud shell, verrà chiesto di scegliere il tipo di shell che si vuole usare (*Bash* o *PowerShell*).
1. Verificare che la sottoscrizione corretta sia specificata e selezionare Crea archiviazione se viene chiesto di creare **l'archiviazione** per cloud shell. Attendere la creazione dell'archiviazione.
1. Nel terminale immettere i comandi seguenti per clonare il repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare il codice copiato nella Cloud Shell.

1. Dopo aver clonato il repository, immettere i comandi seguenti per modificare la cartella per questo lab ed eseguire lo script **di setup.sh** che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/04
    ./setup.sh
    ```

    > Ignorare eventuali messaggi (errore) che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script: in genere sono necessari circa 5-10 minuti.

## Clonare i materiali del lab

Dopo aver creato l'area di lavoro e le risorse di calcolo necessarie, è possibile aprire il studio di Azure Machine Learning e clonare i materiali del lab. 

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvia studio**. Un'altra scheda verrà aperta nel browser per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. Nella studio di Azure Machine Learning passare alla pagina **Calcolo** e verificare che l'istanza di calcolo e il cluster creati nella sezione precedente esistano. L'istanza di calcolo deve essere in esecuzione, il cluster deve essere inattiva e avere 0 nodi in esecuzione.
1. Nella scheda **Istanze di calcolo** individuare l'istanza di calcolo e selezionare l'applicazione **Terminale** .
1. Nel terminale installare Python SDK nell'istanza di calcolo eseguendo i comandi seguenti nel terminale:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi (errore) che dicono che i pacchetti non sono stati trovati e disinstallati.

1. Eseguire il comando seguente per clonare un repository Git contenente notebook, dati e altri file nell'area di lavoro:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Al termine del comando, nel riquadro **File** fare clic ** su&#8635;** per aggiornare la visualizzazione e verificare che sia stata creata una nuova cartella **Users/*your-user-name/azure-ml-labs***.

## Usare gli ambienti

Il codice per creare e gestire gli ambienti con Python SDK viene fornito in un notebook.

1. Aprire il notebook **Labs/04/Work with environment.ipynb** .

    > Selezionare **Autenticare** e seguire i passaggi necessari se viene visualizzata una notifica che richiede l'autenticazione.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML** .
1. Eseguire tutte le celle nel notebook.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare alla portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
