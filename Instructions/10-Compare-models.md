---
lab:
  title: Creare ed esplorare la dashboard Responsible AI
---

# Creare ed esplorare la dashboard Responsible AI

Dopo aver eseguito il training del modello, è necessario testarlo per verificare se funziona come previsto. Oltre a valutare le prestazioni, è possibile prendere in considerazione altri fattori. La dashboard Responsible AI di Azure Machine Learning consente di analizzare i dati e le previsioni del modello per identificare eventuali distorsioni o irregolarità.

In questo esercizio si prepareranno i dati e si creerà una dashboard Responsible AI in Azure Machine Learning.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'*area di lavoro* di Azure Machine Learning offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e gestire i modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite Studio, Python SDK e l'interfaccia della riga di comando di Azure.

Si userà l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e dell'ambiente di calcolo necessario, e si userà Python SDK per eseguire un processo di comando.

### Creare l'area di lavoro e le risorse dell'ambiente di calcolo

Per creare l'area di lavoro di Azure Machine Learning, un'istanza di ambiente di calcolo e un cluster di elaborazione, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari sono raggruppati in uno script shell da eseguire.

1. Nel browser, aprire il portale di Azure all'indirizzo `https://portal.azure.com/`, eseguendo l'accesso con l'account Microsoft.
1. Selezionare il pulsante \[>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Se richiesto, selezionare **Bash**. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che sia specificata la sottoscrizione corretta e che **non sia selezionato Nessun account di archiviazione necessario** . Selezionare **Applica**.
1. Nel terminale, immettere i comandi seguenti per clonare questo repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare in Cloud Shell il codice copiato.

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo script **setup.sh** che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/10
    ./setup.sh
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. Questa operazione richiede in genere circa 5-10 minuti.

## Clonare i materiali del lab

Dopo aver creato l'area di lavoro e le risorse di calcolo necessarie, è possibile aprire studio di Azure Machine Learning e clonare i materiali del lab nell'area di lavoro.

1. Nel portale di Azure, passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvio Studio**. Nel browser verrà aperta un'altra scheda per aprire studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati in studio.
1. All'interno dello studio di Azure Machine Learning, passare alla pagina **Ambiente di calcolo** e verificare che l'istanza di ambiente di calcolo e il cluster creati nella sezione precedente esistano. L'istanza di ambiente di calcolo deve essere in esecuzione, il cluster deve essere inattivo e avere 0 nodi in esecuzione.
1. Nella scheda **Istanze di ambiente di calcolo**, individuare l'istanza di ambiente di calcolo e selezionare l'applicazione **Terminale**.
1. Nel terminale, installare Python SDK nell'istanza di ambiente di calcolo, eseguendo i comandi seguenti:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che non è stato possibile trovare e disinstallare i pacchetti.

1. Eseguire il comando seguente per clonare nell'area di lavoro un repository Git contenente notebook, dati e altri file:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Dopo il completamento del comando, nel riquadro **File**, fare clic su **&#8635;** per aggiornare la vista e verificare che sia stata creata una nuova cartella **Utenti/*nome-utente*/azure-ml-labs**.

## Creare una pipeline per valutare i modelli e inviarli da un notebook

Ora che si dispone di tutte le risorse necessarie, è possibile eseguire il notebook per recuperare i componenti di AI responsabili predefiniti, creare una pipeline e inviare la pipeline per generare una dashboard Responsible AI.

1. Aprire il notebook **Labs/10/Create Responsible AI dashboard.ipynb**.

    > Selezionare **Autenticare** e, se compare una notifica che chiede di eseguire l'autenticazione, seguire i passaggi necessari.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML**.
1. Eseguire tutte le celle nel notebook.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda Studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
