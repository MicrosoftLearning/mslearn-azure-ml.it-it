---
lab:
  title: Esplorare gli strumenti di sviluppo per l'interazione con l'area di lavoro
---

# Esplorare gli strumenti di sviluppo per l'interazione con l'area di lavoro

È possibile usare vari strumenti per interagire con l'area di lavoro Azure Machine Learning. A seconda dell'attività da eseguire e della preferenza per lo strumento di sviluppo, è possibile scegliere quale strumento usare e quando. Questo lab è pensato come introduzione agli strumenti di sviluppo comunemente usati per l'interazione con l'area di lavoro. Per informazioni su come usare uno strumento specifico in modo più approfondito, sono disponibili altri lab da esplorare.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

Gli strumenti di sviluppo comunemente usati per interagire con l'area di lavoro Azure Machine Learning sono:

- **Interfaccia della riga di comando di Azure** con l'estensione Azure Machine Learning: Questo approccio basato sulla riga di comando è ideale per l'automazione dell'infrastruttura.
- **Studio di Azure Machine Learning**: Usare l'interfaccia utente intuitiva per esplorare l'area di lavoro e le relative funzionalità.
- **Python SDK** per Azure Machine Learning: Usare per inviare processi e gestire modelli da un notebook Jupyter, ideale per data scientist.

Verrà esaminato ognuno di questi strumenti sulla base delle attività comunemente eseguite con tale strumento.

## Effettuare il provisioning dell'infrastruttura con l'interfaccia della riga di comando di Azure

Per consentire a un data scientist di eseguire il training di un modello di Machine Learning con Azure Machine Learning, è necessario configurare l'infrastruttura necessaria. È possibile usare l'interfaccia della riga di comando di Azure con l'estensione Azure Machine Learning per creare un'area di lavoro Azure Machine Learning e risorse come un'istanza di ambiente di calcolo.

Per iniziare, aprire Azure Cloud Shell, installare l'estensione Azure Machine Learning e clonare il repository Git.

1. Nel browser, aprire il portale di Azure all'indirizzo `https://portal.azure.com/`, eseguendo l'accesso con l'account Microsoft.
1. Selezionare il pulsante \[>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Se richiesto, selezionare **Bash**. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che sia specificata la sottoscrizione corretta e che **non sia selezionato Nessun account di archiviazione necessario** . Selezionare **Applica**.
1. Rimuovere tutte le estensioni dell'interfaccia della riga di comando di ML (versione 1 e 2) per evitare conflitti con le versioni precedenti in relazione a questo comando:
    
    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Usare `SHIFT + INSERT` per incollare in Cloud Shell il codice copiato.

    > Ignorare eventuali messaggi (di errore) che dicono che le estensioni non sono state installate.

1. Installare l'estensione Azure Machine Learning (v2) con il seguente comando:
    
    ```azurecli
    az extension add -n ml -y
    ```

1. Crea un gruppo di risorse. Scegliere una località vicina.
    
    ```azurecli
    az group create --name "rg-dp100-labs" --location "eastus"
    ```

1. Creare un'area di lavoro:
    
    ```azurecli
    az ml workspace create --name "mlw-dp100-labs" -g "rg-dp100-labs"
    ```

1. Attendere che vengano create l'area di lavoro e le relative risorse associate, in genere sono necessari circa 5 minuti.

    <details>  
    <summary><b>Suggerimento</b> per la risoluzione dei problemi: errore di creazione dell'area di lavoro</summary><br>
    <p>Se viene visualizzato un errore durante la creazione di un'area di lavoro tramite l'interfaccia della riga di comando, è necessario effettuare il provisioning manuale della risorsa:</p>
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
    </ol>
    </details>

## Creare un'istanza di ambiente di calcolo con l'interfaccia della riga di comando di Azure

Un'altra parte importante dell'infrastruttura necessaria per eseguire il training di un modello di Machine Learning è l’ambiente di **calcolo**. Anche se è possibile eseguire il training dei modelli in locale, è più scalabile e conveniente usare il cloud compute.

Quando i data scientist sviluppano un modello di Machine Learning nell'area di lavoro Azure Machine Learning, vogliono usare una macchina virtuale su cui poter eseguire notebook Jupyter. Per lo sviluppo, un'**istanza di ambiente di calcolo** è l’ideale.

Dopo aver creato un'area di lavoro Azure Machine Learning, è possibile creare anche un'istanza di ambiente di calcolo tramite l'interfaccia della riga di comando di Azure.

In questo esercizio si creerà un'istanza di ambiente di calcolo con le impostazioni seguenti:

- **Nome dell’ambiente di calcolo**: *Nome dell'istanza dell’ambiente di calcolo. Deve essere univoco e contenere meno di 24 caratteri.*
- **Dimensioni macchina virtuale**: STANDARD_DS11_V2
- **Tipo di calcolo** (istanza o cluster): ComputeInstance
- **Nome dell'area di lavoro Azure Machine Learning**: mlw-dp100-labs
- **Gruppo di risorse**: rg-dp100-labs

1. Usare il comando seguente per creare un'istanza di ambiente di calcolo nell'area di lavoro. Se il nome dell'istanza di ambiente di calcolo contiene "XXXX", sostituirlo con numeri casuali per creare un nome univoco.

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    Se compare un messaggio di errore che indica che esiste già un'istanza di ambiente di calcolo con quel nome, modificare il nome e ripetere il comando.

    <details>  
    <summary><b>Suggerimento</b> per la risoluzione dei problemi: Errore di creazione del calcolo</summary><br>
    <p>Se viene visualizzato un errore durante la creazione di un'istanza di calcolo tramite l'interfaccia della riga di comando, è necessario effettuare manualmente il provisioning della risorsa:</p>
    <ol>
        <li>Nel portale di Azure passare all'area di lavoro di Azure Machine Learning denominata <b>mlw-dp100-labs</b>.</li>
        <li>Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina <b>Panoramica</b> selezionare <b>Avvio Studio</b>. Nel browser verrà aperta un'altra scheda per aprire studio di Azure Machine Learning.</li>
        <li>Chiudere tutti i popup visualizzati in studio.</li>
        <li>All'interno del studio di Azure Machine Learning passare alla <b>pagina Calcolo</b> e selezionare+ <b>Nuovo</b> nella <b>scheda Istanze</b> di calcolo.</li>
        <li>Assegnare all'istanza di calcolo un nome univoco e quindi selezionare <b>Standard_DS11_v2</b> come dimensione della macchina virtuale.</li>
        <li>Selezionare <b>Rivedi e crea</b> e quindi <b>Crea</b>.</li>
    </ol>
    </details>

## Creare un cluster di elaborazione con l'interfaccia della riga di comando di Azure

Anche se un'istanza di ambiente di calcolo è ideale per lo sviluppo, un cluster di elaborazione è più adatto quando si vuole eseguire il training di modelli di Machine Learning. Solo quando un processo viene inviato per usare il cluster di ambiente di calcolo, verrà ridimensionato in più di 0 nodi ed eseguirà il processo. Una volta che il cluster di elaborazione non è più necessario, verrà ridimensionato automaticamente in 0 nodi per ridurre al minimo i costi. 

Per creare un cluster di elaborazione, è possibile usare l'interfaccia della riga di comando di Azure, in modo analogo alla creazione di un'istanza di ambiente di calcolo.

Si creerà un cluster di elaborazione con le impostazioni seguenti:

- **Nome dell’ambiente di calcolo**: aml-cluster
- **Dimensioni macchina virtuale**: STANDARD_DS11_V2
- **Tipo di ambiente di calcolo**: AmlCompute * (crea un cluster di elaborazione)*
- **Numero massimo istanze**: *Numero massimo di nodi*
- **Nome dell'area di lavoro Azure Machine Learning**: mlw-dp100-labs
- **Gruppo di risorse**: rg-dp100-labs

1. Usare il comando seguente per creare un cluster di elaborazione nell'area di lavoro.
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

    <details>  
    <summary><b>Suggerimento</b> per la risoluzione dei problemi: Errore di creazione del calcolo</summary><br>
    <p>Se viene visualizzato un errore durante la creazione di un cluster di calcolo tramite l'interfaccia della riga di comando, è necessario effettuare il provisioning manuale della risorsa:</p>
    <ol>
        <li>Nel portale di Azure passare all'area di lavoro di Azure Machine Learning denominata <b>mlw-dp100-labs</b>.</li>
        <li>Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina <b>Panoramica</b> selezionare <b>Avvio Studio</b>. Nel browser verrà aperta un'altra scheda per aprire studio di Azure Machine Learning.</li>
        <li>Chiudere tutti i popup visualizzati in studio.</li>
        <li>All'interno della studio di Azure Machine Learning passare alla <b>pagina Calcolo</b> e selezionare+ <b>Nuovo</b> nella <b>scheda Cluster di</b> calcolo.</li>
        <li>Scegliere la stessa area di quella in cui è stata creata l'area di lavoro e quindi selezionare <b>Standard_DS11_v2</b> come dimensioni della macchina virtuale. Selezionare <b>Avanti</b></li>
        <li>Assegnare al cluster un nome univoco e quindi selezionare <b>Crea</b>.</li>
    </ol>
    </details>

## Configurare la workstation con lo studio di Azure Machine Learning

Anche se l'interfaccia della riga di comando di Azure è ideale per l'automazione, è consigliabile esaminare l'output dei comandi eseguiti. È possibile usare lo studio di Azure Machine Learning per verificare se sono state create risorse e asset e controllare se i processi sono stati eseguiti correttamente o verificare il motivo per cui un processo non è riuscito. 

1. Nel portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-labs**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvio Studio**. Nel browser verrà aperta un'altra scheda per aprire studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati in studio.
1. All'interno dello studio di Azure Machine Learning, passare alla pagina **Ambiente di calcolo** e verificare che l'istanza di ambiente di calcolo e il cluster creati nella sezione precedente esistano. L'istanza di calcolo deve essere in esecuzione, il cluster deve essere in stato Succeeded e avere 0 nodi in esecuzione.

## Usare l'SDK Python per eseguire il training di un modello

Dopo aver verificato che l’ambiente di calcolo necessario è stato creato, è possibile usare l’SDK Python per eseguire uno script di training. Si installerà e si userà l’SDK Python nell'istanza di ambiente di calcolo e si eseguirà il training del modello di Machine Learning nel cluster di elaborazione.

1. **Nell'istanza** di calcolo sono disponibili diverse opzioni nel **campo Applicazioni**. Selezionare l'applicazione **Terminale** per avviare il terminale (potrebbe essere necessario fare clic sui puntini di sospensione per espandere la selezione).
1. Nel terminale installare Python SDK nell'istanza di ambiente di calcolo, eseguendo nel terminale i comandi seguenti:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che i pacchetti non sono stati installati.

1. Eseguire il comando seguente per clonare nell'area di lavoro un repository Git contenente notebook, dati e altri file:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Dopo il completamento del comando, nel riquadro **File** selezionare **&#8635;** per aggiornare la vista e verificare che sia stata creata una nuova cartella **Utenti/*nome-utente*/azure-ml-labs**.
1. Aprire il notebook **Labs/02/Run training script.ipynb**.

    > Selezionare **Autenticare** e, se viene visualizzata una notifica che chiede di eseguire l'autenticazione, seguire i passaggi necessari.

1. Verificare che il notebook usi il **kernel Python 3.10 - AzureML** nell'angolo superiore destro dell'ambiente notebook. Ogni kernel ha una propria immagine con un proprio set di pacchetti preinstallati.
1. Eseguire tutte le celle nel notebook.

Verrà creato un nuovo processo nell'area di lavoro di Azure Machine Learning. Il processo tiene traccia degli input definiti nella configurazione del processo, del codice usato e degli output come le metriche per valutare il modello.

## Esaminare la cronologia dei processi nello studio di Azure Machine Learning

Quando si invia un processo all'area di lavoro di Azure Machine Learning, è possibile esaminarne lo stato in studio di Azure Machine Learning.

1. Selezionare l'URL del processo fornito come output nel notebook oppure passare alla pagina **Processi** in studio di Azure Machine Learning.
1. Viene elencato un nuovo esperimento denominato **diabetes-training**. Selezionare il processo più recente **diabetes-pythonv2-train**.
1. Esaminare le **proprietà** del processo. Prendere nota dello **stato** del processo:
    - **In coda**: Il processo è in attesa che le risorse di calcolo diventino disponibili.
    - **In fase di preparazione**: Il cluster di elaborazione si sta ridimensionando o l'ambiente è in fase di installazione nella destinazione di calcolo.
    - **In esecuzione**: Lo script di training è in fase di esecuzione.
    - **In fase di completamento**: Lo script di training è stato eseguito e il processo si sta aggiornando con tutte le informazioni finali.
    - **Completato**: Il processo è stato completato e terminato.
    - **Non riuscito**: Il processo non è riuscito e viene terminato.
1. In **Output e log** è possibile trovare l'output dello script in **user_logs/std_log.txt**. Gli output delle istruzioni di **stampa** nello script verranno visualizzati qui. Se si verifica un errore a causa di un problema con lo script, verrà visualizzato anche il messaggio di errore.
1. In **Codice** si troverà la cartella specificata nella configurazione del processo. Questa cartella include lo script di training e il set di dati.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda Studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-labs**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**. 
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
