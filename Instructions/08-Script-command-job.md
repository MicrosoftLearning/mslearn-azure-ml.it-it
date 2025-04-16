---
lab:
  title: Eseguire uno script di training come processo di comando in Azure Machine Learning
---

# Eseguire uno script di training come processo di comando in Azure Machine Learning

Un notebook è ideale per la sperimentazione e lo sviluppo. Dopo aver sviluppato un modello di Machine Learning ed è pronto per la produzione, è necessario eseguirne il training con uno script. È possibile eseguire uno script come processo di comando.

In questo esercizio si testerà uno script e quindi lo si eseguirà come processo di comando.

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
    cd azure-ml-labs/Labs/08
    ./setup.sh
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. Questa operazione richiede in genere circa 5-10 minuti.

    <details>
    <summary><b>Suggerimento</b> per la risoluzione dei problemi: errore di creazione dell'area di lavoro</summary><br>
    <p>Se viene visualizzato un errore durante l'esecuzione dello script di installazione tramite l'interfaccia della riga di comando, è necessario effettuare manualmente il provisioning delle risorse:</p>
    <ol>
        <li>Nella home page portale di Azure selezionare <b>+ Crea una risorsa</b>.</li>
        <li><i>Cercare Machine Learning</i> e quindi selezionare <b>Azure Machine Learning</b>. Selezionare <b>Crea</b>.</li>
        <li>Creare una nuova risorsa di Azure Machine Learning con le impostazioni seguenti: <ul>
                <li><b>Sottoscrizione</b>: <i>la sottoscrizione di Azure usata</i></li>
                <li><b>Gruppo di risorse</b>: rg-dp100-labs</li>
                <li><b>Nome</b> area di lavoro: mlw-dp100-labs</li>
                <li><b>Area</b>: <i>selezionare l'area geografica più vicina</i></li>
                <li><b>Account di archiviazione</b>: <i>prendere nota del nuovo account di archiviazione predefinito che verrà creato per l'area di lavoro</i></li>
                <li><b>Insieme di credenziali delle chiavi</b>: <i>prendere nota del nuovo insieme di credenziali delle chiavi predefinito che verrà creato per l'area di lavoro</i></li>
                <li><b>Application Insights</b>: <i>prendere nota della nuova risorsa Application Insights predefinita che verrà creata per l'area di lavoro</i></li>
                <li><b>Registro contenitori</b>: nessuno (<i>ne verrà creato uno automaticamente la prima volta che si distribuisce un modello in un contenitore</i>)</li>
            </ul>
        <li>Selezionare <b>Rivedi e crea</b> e attende che l'area di lavoro e le relative risorse associate vengano create. In genere sono necessari circa 5 minuti.</li>
        <li>Selezionare <b>Vai alla risorsa</b> e nella relativa <b>pagina Panoramica</b> selezionare <b>Avvia studio</b>. Nel browser verrà aperta un'altra scheda per aprire studio di Azure Machine Learning.</li>
        <li>Chiudere tutti i popup visualizzati in studio.</li>
        <li>All'interno del studio di Azure Machine Learning passare alla <b>pagina Calcolo</b> e selezionare+ <b>Nuovo</b> nella <b>scheda Istanze</b> di calcolo.</li>
        <li>Assegnare all'istanza di calcolo un nome univoco e quindi selezionare <b>Standard_DS11_v2</b> come dimensione della macchina virtuale.</li>
        <li>Selezionare <b>Rivedi e crea</b> e quindi <b>Crea</b>.</li>
        <li>Selezionare quindi la <b>scheda Cluster di</b> calcolo e selezionare <b>+ Nuovo</b>.</li>
        <li>Scegliere la stessa area di quella in cui è stata creata l'area di lavoro e quindi selezionare <b>Standard_DS11_v2</b> come dimensioni della macchina virtuale. Selezionare <b>Avanti</b></li>
        <li>Assegnare al cluster un nome univoco e quindi selezionare <b>Crea</b>.</li>
    </ol>
    </details>

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

1. Dopo il completamento del comando, nel riquadro **File** fare clic su **&#8635;** per aggiornare la vista e verificare che sia stata creata una nuova cartella **Utenti/*nome-utente*/azure-ml-labs**.

## Convertire un notebook in uno script

L'uso di un notebook collegato a un'istanza di calcolo è ideale per la sperimentazione e lo sviluppo, in quanto consente di eseguire immediatamente il codice scritto ed esaminarne l'output. Per passare dallo sviluppo all'ambiente di produzione, è consigliabile usare gli script. Come primo passaggio, è possibile usare Azure Machine Learning Studio per convertire il notebook in uno script.

1. Aprire il notebook **Labs/08/src/Train classification model.ipynb**.

    > Selezionare **Autentica** e, se viene visualizzata una notifica che chiede di eseguire l'autenticazione, seguire i passaggi necessari.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML**.
1. Eseguire tutte le celle per esplorare il codice ed eseguire il training di un modello.
1. Selezionare l’icona &#9776; nella parte superiore del notebook per visualizzare il **menu notebook**.
1. Espandere **Esporta come** e selezionare **Python (.py)** per convertire il notebook in uno script Python.
1. Assegnare al nuovo file il nome `train-classification-model`.
1. Dopo aver creato il nuovo file, lo script deve essere aperto automaticamente. Esplorare il file e notare che contiene lo stesso codice del notebook.
1. Selezionare l’icona &#9655;&#9655; nella parte superiore del notebook per **salvare ed eseguire lo script nel terminale**.
1. Lo script viene avviato dal comando **python train-classification-model.py** e l'output deve essere illustrato sotto il comando.

   > **Nota:** se lo script restituisce un importError per libstdc++6, eseguire i comandi seguenti nel terminale prima di eseguire di nuovo lo script:
   > ```bash
   > sudo add-apt-repository ppa:ubuntu-toolchain-r/test
   > sudo apt-get update
   > sudo apt-get upgrade libstdc++6
   > ```

## Testare uno script con il terminale

Dopo aver convertito un notebook in uno script, è possibile perfezionarlo ulteriormente. Una procedura consigliata quando si usano gli script consiste nell'usare le funzioni. Quando lo script è costituito da funzioni, è più semplice eseguire unit test del codice. Quando si usano le funzioni, lo script sarà costituito da blocchi di codice, dove ogni blocco esegue un'attività specifica.

1. Aprire lo **script Labs/08/src/train-model-parameters.py** ed esplorarne il relativo contenuto.
    Si noti che è presente una funzione principale che include quattro altre funzioni:

    - Leggere i dati
    - Dividere i dati
    - Eseguire il training del modello
    - Valutazione del modello

    Dopo la funzione main, viene definita ogni funzione. Si noti che ogni funzione definisce l'input e l'output previsti.

1. Selezionare l’icona &#9655;&#9655; nella parte superiore del notebook per **salvare ed eseguire lo script nel terminale**. Dovrebbe essere visualizzato un errore dopo la **lettura dei dati...** che indica che non è stato possibile ottenere i dati perché il percorso del file non è valido.

    Gli script consentono di parametrizzare il codice per modificare facilmente i dati o i parametri di input. In questo caso, lo script prevede un parametro di input per il percorso dati che non è stato specificato. È possibile trovare i parametri definiti e previsti alla fine dello script nella funzione **parse_args()**.

    Sono definiti due parametri di input:
    - **--training_data** che prevede una stringa.
    - **--reg_rate** che prevede un numero, ma ha un valore predefinito pari a 0,01.

    Per eseguire correttamente lo script, è necessario specificare il valore per i parametri dei dati di training. A questo scopo, fare riferimento al file **diabetes.csv** archiviato nella stessa cartella dello script di training.

1. Eseguire i comandi seguenti nel terminale:

    ```
    cd azure-ml-labs/Labs/08/src/
    python train-model-parameters.py --training_data diabetes.csv
    ```

Lo script dovrebbe essere eseguito correttamente e, di conseguenza, l'output dovrebbe mostrare l'accuratezza e l'interfaccia utente del modello sottoposto a training.

Il test dello script nel terminale è ideale per verificare se lo script funziona come previsto. Se si verifica un problema con il codice, verrà visualizzato un errore nel terminale.

**Facoltativamente**, modificare il codice per forzare un errore ed eseguire di nuovo il comando nel terminale per eseguire lo script. Ad esempio, rimuovere la riga **import pandas as pds**, salvare ed eseguire lo script con il parametro di input per esaminare il messaggio di errore.

## Eseguire uno script come processo di comando

Se si conosce il funzionamento dello script, è possibile eseguirlo come processo di comando. Eseguendo lo script come processo di comando, sarà possibile tenere traccia di tutti gli input e gli output dello script.

1. Aprire il notebook **Labs/08/Esegui come comando job.ipynb**.
1. Eseguire tutte le celle nel notebook.
1. In Azure Machine Learning Studio passare alla pagina **Processi**.
1. Passare al processo **diabetes-train-script** per esplorare la panoramica del processo di comando eseguito.
1. Passare alla scheda **Codice**. Tutti i contenuti della cartella **src**, ovvero il valore del parametro di **codice** del processo di comando, vengono copiati qui. È possibile esaminare lo script di training eseguito dal processo di comando.
1. Passare alla scheda **Output e log**.
1. Aprire il file **std_log.txt** ed esplorarne il contenuto. Il contenuto di questo file è l'output del comando. Tenere presente che lo stesso output è stato visualizzato nel terminale quando è stato testato lo script. Se il processo non riesce a causa di un problema con lo script, il messaggio di errore verrà visualizzato qui.

**Facoltativamente**, modificare il codice per forzare un errore e usare il notebook per avviare di nuovo il processo del comando. Ad esempio, rimuovere la riga **importare pandas come pd** dallo script e salvare lo script. In alternativa, modificare la configurazione del processo di comando per esplorare i messaggi di errore quando si verifica un errore nella configurazione del processo invece dello script.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda Studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
