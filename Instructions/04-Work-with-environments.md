---
lab:
  title: Usare gli ambienti in Azure Machine Learning
---

# Usare gli ambienti in Azure Machine Learning

Per eseguire notebook e script, è necessario accertare che siano installati i pacchetti necessari. Gli ambienti consentono di specificare i runtime e i pacchetti Python da usare con il calcolo per eseguire il codice.

In questo esercizio saranno fornite le informazioni sugli ambienti e su come usarli durante il training dei modelli di Machine Learning con l'ambiente di calcolo di Azure Machine Learning.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'*area di lavoro* di Azure Machine Learning offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e gestire i modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite l'interfaccia della riga di comando di Studio, Python SDK e Azure.

Si userà l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e il calcolo necessario. Inoltre si userà Python SDK per eseguire il training di un modello di classificazione con Machine Learning automatizzato.

### Creare l'area di lavoro e le risorse dell’ambiente di calcolo

Per creare l'area di lavoro di Azure Machine Learning e le risorse dell’ambiente calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari sono raggruppati in uno script shell da eseguire.

1. Nel browser, aprire il portale di Azure all'indirizzo `https://portal.azure.com/`, eseguendo l'accesso con l'account Microsoft.
1. Selezionare il pulsante \[>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Se richiesto, selezionare **Bash**. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che sia specificata la sottoscrizione corretta e selezionare **Crea risorsa di archiviazione**, se è richiesto di creare la risorsa di archiviazione per la cloud shell. Attendere la creazione della risorsa di archiviazione.
1. Nel terminale, immettere i comandi seguenti per clonare questo repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare in Cloud Shell il codice copiato.

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo script **setup.sh** che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/04
    ./setup.sh
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. Questa operazione richiede in genere circa 5-10 minuti.

## Clonare i materiali del lab

Dopo aver creato l'area di lavoro e le risorse di calcolo necessarie, è possibile aprire studio di Azure Machine Learning e clonare i materiali del lab. 

1. Nel portale di Azure, passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvio Studio**. Nel browser sarà aperta un'altra scheda per aprire studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati in Studio.
1. All'interno di studio di Azure Machine Learning, passare alla pagina **Calcolo** e verificare che esistano l'istanza di ambiente di calcolo e il cluster creati nella sezione precedente. L'istanza di ambiente di calcolo deve essere in esecuzione, il cluster deve essere inattivo e avere 0 nodi in esecuzione.
1. Nella scheda **Istanze di ambiente di calcolo** individuare l'istanza di ambiente di calcolo e selezionare l'applicazione **Terminale**.
1. Nel terminale installare Python SDK nell'istanza di ambiente di calcolo, eseguendo nel terminale i comandi seguenti:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che non è stato possibile trovare e disinstallare i pacchetti.

1. Eseguire il comando seguente per clonare nell'area di lavoro un repository Git contenente notebook, dati e altri file:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Dopo il completamento del comando, nel riquadro **File** fare clic su **&#8635;** per aggiornare la vista e verificare che sia stata creata una nuova cartella **Utenti/*nome-utente*/azure-ml-labs**.

## Utilizzare gli ambienti

Il codice per creare e gestire ambienti con Python SDK viene fornito in un notebook.

1. Aprire il notebook **Labs/04/Work with environments.ipynb**.

    > Selezionare **Autenticare** e, se viene visualizzata una notifica che chiede di eseguire l'autenticazione, seguire i passaggi necessari.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML**.
1. Eseguire tutte le celle nel notebook.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
