---
lab:
  title: Esplorare gli strumenti di sviluppo per l'interazione con l'area di lavoro
---

# Esplorare gli strumenti di sviluppo per l'interazione con l'area di lavoro

È possibile usare vari strumenti per interagire con l'area di lavoro di Azure Machine Learning. A seconda dell'attività da eseguire e della preferenza per lo strumento di sviluppo, è possibile scegliere quale strumento usare quando. Questo lab è progettato come introduzione agli strumenti di sviluppo comunemente usati per l'interazione con l'area di lavoro. Per informazioni su come usare uno strumento specifico in modo più approfondito, sono disponibili altri lab da esplorare.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

Gli strumenti di sviluppo comunemente usati per interagire con l'area di lavoro di Azure Machine Learning sono:

- **** Interfaccia della riga di comando di Azure con l'estensione Azure Machine Learning: questo approccio da riga di comando è ideale per l'automazione dell'infrastruttura.
- **** studio di Azure Machine Learning: usare l'interfaccia utente intuitiva per esplorare l'area di lavoro e tutte le relative funzionalità.
- **Python SDK** per Azure Machine Learning: usare per inviare processi e gestire modelli da un notebook di Jupyter, ideale per i data scientist.

Verranno esaminati ognuno di questi strumenti per le attività comunemente eseguite con tale strumento.

## Effettuare il provisioning dell'infrastruttura con l'interfaccia della riga di comando di Azure

Per consentire a un data scientist di eseguire il training di un modello di Machine Learning con Azure Machine Learning, è necessario configurare l'infrastruttura necessaria. È possibile usare l'interfaccia della riga di comando di Azure con l'estensione Azure Machine Learning per creare un'area di lavoro e risorse di Azure Machine Learning come un'istanza di calcolo.

Per iniziare, aprire Azure Cloud Shell, installare l'estensione Azure Machine Learning e clonare il repository Git.

1. In un browser aprire il portale di Azure in `https://portal.azure.com/`, accedere con l'account Microsoft.
1. Selezionare il \[pulsante >_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Selezionare **Bash** se richiesto. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che la sottoscrizione corretta sia specificata e selezionare Crea archiviazione **** se viene chiesto di creare l'archiviazione per Cloud Shell. Attendere la creazione dello spazio di archiviazione.
1. Rimuovere tutte le estensioni dell'interfaccia della riga di comando di ML (versione 1 e 2) per evitare conflitti con le versioni precedenti con questo comando:
    
    ```azurecli
    az extension remove -n azure-cli-ml
    az extension remove -n ml
    ```

    > Usare `SHIFT + INSERT` per incollare il codice copiato in Cloud Shell.

    > Ignorare eventuali messaggi di errore che dicono che le estensioni non sono state installate.

1. Installare l'estensione Azure Machine Learning (v2) con il comando seguente:
    
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

1. Attendere che l'area di lavoro e le relative risorse associate vengano create, in genere sono necessari circa 5 minuti.

## Creare un'istanza di calcolo con l'interfaccia della riga di comando di Azure

Un'altra parte importante dell'infrastruttura necessaria per eseguire il training di un modello di Machine Learning è **il calcolo**. Anche se è possibile eseguire il training dei modelli in locale, è più scalabile e conveniente usare il cloud compute.

Quando i data scientist sviluppano un modello di Machine Learning nell'area di lavoro di Azure Machine Learning, vogliono usare una macchina virtuale in cui possono eseguire notebook jupyter. Per lo sviluppo, un'istanza **** di calcolo è ideale.

Dopo aver creato un'area di lavoro di Azure Machine Learning, è anche possibile creare un'istanza di calcolo usando l'interfaccia della riga di comando di Azure.

In questo esercizio si creerà un'istanza di calcolo con le impostazioni seguenti:

- **** Nome calcolo: *nome dell'istanza di calcolo. Deve essere univoco e meno di 24 caratteri.*
- **Dimensioni macchina** virtuale: STANDARD_DS11_V2
- **Tipo di** calcolo (istanza o cluster): ComputeInstance
- **Nome** dell'area di lavoro di Azure Machine Learning: mlw-dp100-labs
- **Gruppo** di risorse: rg-dp100-labs

1. Usare il comando seguente per creare un'istanza di calcolo nell'area di lavoro. Se il nome dell'istanza di calcolo contiene "XXXX", sostituirlo con numeri casuali per creare un nome univoco.

    ```azurecli
    az ml compute create --name "ciXXXX" --size STANDARD_DS11_V2 --type ComputeInstance -w mlw-dp100-labs -g rg-dp100-labs
    ```

    Se viene visualizzato un messaggio di errore che indica che esiste già un'istanza di calcolo con il nome, modificare il nome e ripetere il comando.

## Creare un cluster di calcolo con l'interfaccia della riga di comando di Azure

Anche se un'istanza di calcolo è ideale per lo sviluppo, un cluster di calcolo è più adatto quando si vuole eseguire il training di modelli di Machine Learning. Solo quando un processo viene inviato per usare il cluster di calcolo, verrà ridimensionato in più di 0 nodi ed eseguirà il processo. Una volta che il cluster di calcolo non è più necessario, verrà ridimensionato automaticamente in 0 nodi per ridurre al minimo i costi. 

Per creare un cluster di calcolo, è possibile usare l'interfaccia della riga di comando di Azure, in modo analogo alla creazione di un'istanza di calcolo.

Si creerà un cluster di calcolo con le impostazioni seguenti:

- **Nome** calcolo: aml-cluster
- **Dimensioni macchina** virtuale: STANDARD_DS11_V2
- **Tipo di** calcolo: AmlCompute *(crea un cluster di calcolo)*
- **Numero massimo di istanze**: *numero massimo di nodi*
- **Nome** dell'area di lavoro di Azure Machine Learning: mlw-dp100-labs
- **Gruppo** di risorse: rg-dp100-labs

1. Usare il comando seguente per creare un cluster di calcolo nell'area di lavoro.
    
    ```azurecli
    az ml compute create --name "aml-cluster" --size STANDARD_DS11_V2 --max-instances 2 --type AmlCompute -w mlw-dp100-labs -g rg-dp100-labs
    ```

## Configurare la workstation con il studio di Azure Machine Learning

Anche se l'interfaccia della riga di comando di Azure è ideale per l'automazione, è consigliabile esaminare l'output dei comandi eseguiti. È possibile usare il studio di Azure Machine Learning per verificare se sono state create risorse e asset e verificare se i processi sono stati eseguiti correttamente o verificare il motivo per cui un processo non è riuscito. 

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-labs**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa **pagina Panoramica** selezionare **Avvia studio**. Nel browser verrà aperta un'altra scheda per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. All'interno del studio di Azure Machine Learning passare alla **pagina Calcolo** e verificare che l'istanza di calcolo e il cluster creati nella sezione precedente esistano. L'istanza di calcolo deve essere in esecuzione, il cluster deve essere inattiva e avere 0 nodi in esecuzione.

## Usare Python SDK per eseguire il training di un modello

Dopo aver verificato che il calcolo necessario è stato creato, è possibile usare Python SDK per eseguire uno script di training. Si installerà e si userà Python SDK nell'istanza di calcolo ed eseguirà il training del modello di Machine Learning nel cluster di calcolo.

1. Selezionare l'applicazione Terminale** per l'istanza **** di calcolo per **avviare il terminale.
1. Nel terminale installare Python SDK nell'istanza di calcolo eseguendo i comandi seguenti nel terminale:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi di errore che dicono che i pacchetti non sono stati installati.

1. Eseguire il comando seguente per clonare un repository Git contenente notebook, dati e altri file nell'area di lavoro:

    ```
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Al termine del comando, nel riquadro File** selezionare **&#8635;** per aggiornare la visualizzazione e verificare che sia stata creata una nuova **cartella Users/*your-user-name*/azure-ml-labs**.**
1. Aprire il **notebook Labs/02/Run training script.ipynb** .

    > Selezionare **Autentica** e seguire i passaggi necessari se viene visualizzata una notifica che chiede di eseguire l'autenticazione.

1. Verificare che il notebook usi il **kernel Python 3.8 - AzureML** . Ogni kernel ha una propria immagine con un proprio set di pacchetti preinstallati.
1. Eseguire tutte le celle nel notebook.

Verrà creato un nuovo processo nell'area di lavoro di Azure Machine Learning. Il processo tiene traccia degli input definiti nella configurazione del processo, del codice usato e degli output come le metriche per valutare il modello.

## Esaminare la cronologia dei processi nel studio di Azure Machine Learning

Quando si invia un processo all'area di lavoro di Azure Machine Learning, è possibile esaminarne lo stato nel studio di Azure Machine Learning.

1. Selezionare l'URL del processo fornito come output nel notebook oppure passare alla **pagina Processi** nel studio di Azure Machine Learning.
1. Viene elencato un nuovo esperimento denominato **diabetes-training**. Selezionare il processo più recente **diabetes-pythonv2-train**.
1. Esaminare le proprietà** del **processo. Prendere nota dello stato del** processo**:
    - **In** coda: il processo è in attesa che il calcolo diventi disponibile.
    - **** Preparazione: il cluster di calcolo sta ridimensionando o l'ambiente viene installato nella destinazione di calcolo.
    - **Esecuzione**: viene eseguito lo script di training.
    - **Finalizzazione**: lo script di training è stato eseguito e il processo viene aggiornato con tutte le informazioni finali.
    - **Completato**: il processo è stato completato e terminato.
    - **Operazione non riuscita**: il processo non è riuscito e viene terminato.
1. In **Output e log** è possibile trovare l'output dello script in **user_logs/std_log.txt**. Gli output delle **istruzioni di stampa** nello script verranno visualizzati qui. Se si verifica un errore a causa di un problema con lo script, verrà visualizzato anche il messaggio di errore.
1. In **Codice** si troverà la cartella specificata nella configurazione del processo. Questa cartella include lo script di training e il set di dati.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il **gruppo di risorse rg-dp100-labs** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**. 
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
