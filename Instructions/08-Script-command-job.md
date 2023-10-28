---
lab:
  title: Eseguire uno script di training come processo di comando in Azure Machine Learning
---

# Eseguire uno script di training come processo di comando in Azure Machine Learning

Un notebook è ideale per la sperimentazione e lo sviluppo. Dopo aver sviluppato un modello di Machine Learning ed è pronto per la produzione, è necessario eseguirne il training con uno script. È possibile eseguire uno script come processo di comando.

In questo esercizio si testerà uno script e lo si eseguirà come processo di comando.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

*Un'area di lavoro di* Azure Machine Learning offre una posizione centrale per la gestione di tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite studio, Python SDK e l'interfaccia della riga di comando di Azure.

Si userà l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e le risorse di calcolo necessarie e si userà Python SDK per eseguire un processo di comando.

### Creare l'area di lavoro e le risorse di calcolo

Per creare l'area di lavoro di Azure Machine Learning, un'istanza di calcolo e un cluster di calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari vengono raggruppati in uno script shell da eseguire.

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
    cd azure-ml-labs/Labs/08
    ./setup.sh
    ```

    > Ignorare eventuali messaggi di errore che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. L'operazione richiede in genere circa 5-10 minuti.

## Clonare i materiali del lab

Dopo aver creato l'area di lavoro e le risorse di calcolo necessarie, è possibile aprire il studio di Azure Machine Learning e clonare i materiali del lab nell'area di lavoro.

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella pagina **Panoramica** selezionare **Avvia studio**. Nel browser verrà aperta un'altra scheda per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. All'interno del studio di Azure Machine Learning passare alla pagina **Calcolo** e verificare che l'istanza di calcolo e il cluster creati nella sezione precedente esistano. L'istanza di calcolo deve essere in esecuzione, il cluster deve essere inattiva e avere 0 nodi in esecuzione.
1. Nella scheda **Istanze** di calcolo individuare l'istanza di calcolo e selezionare l'applicazione **Terminale** .
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

1. Al termine del comando, nel riquadro **File** fare clic ** su&#8635;** per aggiornare la visualizzazione e verificare che sia stata creata una nuova cartella **Users/*your-user-name*/azure-ml-labs** .

## Convertire un notebook in uno script

L'uso di un notebook collegato a un'istanza di calcolo è ideale per la sperimentazione e lo sviluppo, in quanto consente di eseguire immediatamente il codice scritto ed esaminarne l'output. Per passare dallo sviluppo alla produzione, è necessario usare gli script. Come primo passaggio, è possibile usare il studio di Azure Machine Learning per convertire il notebook in uno script.

1. Aprire il notebook **Labs/08/src/Train classification model.ipynb** .

    > Selezionare **Autentica** e seguire i passaggi necessari se viene visualizzata una notifica che richiede di eseguire l'autenticazione.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML** .
1. Eseguire tutte le celle per esplorare il codice ed eseguire il training di un modello.
1. Selezionare l'icona &#9776; nella parte superiore del notebook per visualizzare il **menu del notebook**.
1. Espandere **Esporta come** e selezionare **Python (.py)** per convertire il notebook in uno script Python.
1. Assegnare al nuovo file il nome `train-classification-model`.
1. Dopo aver creato il nuovo file, lo script deve essere aperto automaticamente. Esplorare il file e notare che contiene lo stesso codice del notebook.
1. Selezionare l'icona &#9655;&#9655; nella parte superiore del notebook per **salvare ed eseguire lo script nel terminale**.
1. Lo script viene avviato dal comando **python train-classification-model.py** e l'output deve essere mostrato sotto il comando .

## Testare uno script con il terminale

Dopo aver convertito un notebook in uno script, è possibile perfezionarlo ulteriormente. Una procedura consigliata quando si usano gli script consiste nell'usare le funzioni. Quando lo script è costituito da funzioni, è più semplice eseguire unit test del codice. Quando si usano funzioni, lo script sarà costituito da blocchi di codice, ogni blocco che esegue un'attività specifica.

1. Aprire lo script **Labs/08/src/train-model-parameters.py** ed esplorarne il contenuto.
    Si noti che esiste una funzione principale che include quattro altre funzioni:

    - Leggere i dati
    - Dividere i dati
    - Eseguire il training del modello
    - Valutazione del modello

    Dopo la funzione main, ogni funzione viene definita. Si noti che ogni funzione definisce l'input e l'output previsti.

1. Selezionare l'icona &#9655;&#9655; nella parte superiore del notebook per **salvare ed eseguire lo script nel terminale**. Dovrebbe essere visualizzato un errore dopo la **lettura dei dati.** Indica che non è stato possibile ottenere i dati perché il percorso del file non è valido.

    Gli script consentono di parametrizzare il codice per modificare facilmente i dati o i parametri di input. In questo caso, lo script prevede un parametro di input per il percorso dati che non è stato specificato. È possibile trovare i parametri definiti e previsti alla fine dello script nella funzione **parse_args().**

    Sono stati definiti due parametri di input:
    - **--training_data** che prevede una stringa.
    - **--reg_rate** che prevede un numero, ma ha un valore predefinito pari a 0,01.

    Per eseguire correttamente lo script, è necessario specificare il valore per i parametri dei dati di training. A questo scopo, fare riferimento al file **diabetes.csv** archiviato nella stessa cartella dello script di training.

1. Eseguire il comando seguente nel terminale:

    ```
    python train-model-parameters.py --training_data diabetes.csv
    ```

Lo script deve essere eseguito correttamente e, di conseguenza, l'output dovrebbe mostrare l'accuratezza e l'AUC del modello sottoposto a training.

Il test dello script nel terminale è ideale per verificare se lo script funziona come previsto. Se si verifica un problema con il codice, verrà visualizzato un errore nel terminale.

**Facoltativamente**, modificare il codice per forzare un errore ed eseguire di nuovo il comando nel terminale per eseguire lo script. Ad esempio, rimuovere l'importazione della riga **pandas come pd**, salvare ed eseguire lo script con il parametro di input per esaminare il messaggio di errore.

## Eseguire uno script come processo di comando

Se si conosce il funzionamento dello script, è possibile eseguirlo come processo di comando. Eseguendo lo script come processo di comando, sarà possibile tenere traccia di tutti gli input e gli output dello script.

1. Aprire il notebook **Labs/08/Run come comando job.ipynb** .
1. Eseguire tutte le celle nel notebook.
1. Nella studio di Azure Machine Learning passare alla pagina **Processi**.
1. Passare al processo **diabetes-train-script** per esplorare la panoramica del processo di comando eseguito.
1. Passare alla scheda **Codice** . Tutti i contenuti della cartella **src** , ovvero il valore del parametro di **codice** del processo di comando, vengono copiati qui. È possibile esaminare lo script di training eseguito dal processo di comando.
1. Passare alla scheda **Output e log** .
1. Aprire il file **std_log.txt** ed esplorarne il contenuto. Il contenuto di questo file è l'output del comando . Tenere presente che lo stesso output è stato visualizzato nel terminale quando è stato testato lo script. Se il processo non riesce a causa di un problema con lo script, il messaggio di errore verrà visualizzato qui.

**Facoltativamente**, modificare il codice per forzare un errore e usare il notebook per avviare di nuovo il processo del comando. Ad esempio, rimuovere la riga **import pandas come pd** dallo script e salvare lo script. In alternativa, modificare la configurazione del processo di comando per esplorare i messaggi di errore quando si verifica un errore nella configurazione del processo invece dello script.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare alla portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
