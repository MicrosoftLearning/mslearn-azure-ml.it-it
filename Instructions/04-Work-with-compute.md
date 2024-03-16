---
lab:
  title: Usare le risorse di calcolo in Azure Machine Learning
---

# Usare le risorse di calcolo in Azure Machine Learning

Uno dei principali vantaggi del cloud è la possibilità di usare risorse di calcolo scalabili su richiesta per un'elaborazione conveniente di dati di grandi dimensioni.

In questo esercizio si apprenderà come usare il cloud compute in Azure Machine Learning per eseguire esperimenti e codice di produzione su larga scala.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'area di lavoro di* Azure Machine Learning *offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite studio, Python SDK e l'interfaccia della riga di comando di Azure.

Per creare l'area di lavoro di Azure Machine Learning, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari vengono raggruppati in uno script shell da eseguire.

1. In un browser aprire il portale di Azure in `https://portal.azure.com/`, accedere con l'account Microsoft.
1. Selezionare il \[pulsante >_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Selezionare **Bash** se richiesto. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che la sottoscrizione corretta sia specificata e selezionare Crea archiviazione **** se viene chiesto di creare l'archiviazione per Cloud Shell. Attendere la creazione dello spazio di archiviazione.
1. Per evitare conflitti con le versioni precedenti, rimuovere tutte le estensioni dell'interfaccia della riga di comando di Ml (versione 1 e 2) eseguendo questo comando nel terminale:

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

1. Attendere il completamento del comando. In genere sono necessari circa 5-10 minuti.

## Creare lo script di installazione del calcolo

Per eseguire notebook all'interno dell'area di lavoro di Azure Machine Learning, è necessaria un'istanza di calcolo. È possibile usare uno script di installazione per configurare l'istanza di calcolo durante la creazione.

1. Nella portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-labs**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa **pagina Panoramica** selezionare **Avvia studio**. Nel browser verrà aperta un'altra scheda per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio.
1. All'interno del studio di Azure Machine Learning passare alla **pagina Notebook.**
1. **Nel riquadro File** selezionare l'icona &#10753; per **Aggiungere file**.
1. Selezionare **Create new file** (Crea nuovo file).
1. Verificare che il percorso del file sia **Users/* your-user-name**.
1. Modificare il tipo di file in **Bash (*.sh)**.
1. Modificare il nome file in `compute-setup.sh`.
1. Aprire il file compute-setup.sh** appena creato **e incollarne il contenuto:

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Salvare il **file compute-setup.sh** .

## Creare l'istanza di calcolo

Per creare l'istanza di calcolo, è possibile usare studio, Python SDK o l'interfaccia della riga di comando di Azure. Si userà lo studio per creare l'istanza di calcolo con lo script di installazione appena creato.

1. Passare alla **pagina Calcolo** usando il menu a sinistra.
1. Nella **scheda Istanze** di calcolo selezionare **Nuovo**.
1. Configurare (non creare ancora) l'istanza di calcolo con le impostazioni seguenti: 
    - **Nome dell'ambiente di calcolo**: *immettere un nome univoco*
    - **Tipo di** macchina virtuale: *CPU*
    - **Dimensioni macchina** virtuale: *Standard_DS11_v2*
1. Selezionare **Avanti: Impostazioni** avanzate.
1. Selezionare Aggiungi pianificazione** e configurare la pianificazione per **arrestare** l'istanza di calcolo ogni giorno alle **18:00** o **alle 16:00**.**
1. Selezionare l'interruttore provisioning **con lo script** di installazione.
1. Selezionare lo **script compute-setup.sh** creato in precedenza.
1. Esaminare le altre impostazioni avanzate, ma **non** selezionarle:
    - **Abilitare l'accesso** SSH: *è possibile usare questa opzione per abilitare l'accesso diretto alla macchina virtuale usando un client SSH.*
    - **Abilitare la rete** virtuale: *in genere si usa questa funzionalità in un ambiente aziendale per migliorare la sicurezza di rete.*
    - **Assegna a un altro utente**: *è possibile usarlo per assegnare un'istanza di calcolo a un altro data scientist.*
1. **Creare** l'istanza di calcolo e attendere l'avvio e il relativo stato in **Esecuzione**.
1. Quando l'istanza di calcolo è in esecuzione, passare alla **pagina Notebook.** **Nel riquadro File** fare clic su **&#8635;** per aggiornare la visualizzazione e verificare che sia stata creata una nuova **cartella Users/*your-user-name*/dp100-azure-ml-labs**.

## Configurare l'istanza di calcolo

Dopo aver creato l'istanza di calcolo, è possibile eseguirvi notebook. Potrebbe essere necessario installare determinati pacchetti per eseguire il codice desiderato. È possibile includere pacchetti nello script di installazione o installarli usando il terminale.

1. Nella **scheda Istanze** di calcolo individuare l'istanza di calcolo e selezionare l'applicazione **Terminale** .
1. Nel terminale installare Python SDK nell'istanza di calcolo eseguendo i comandi seguenti nel terminale:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi di errore che dicono che i pacchetti non sono stati installati.

1. Quando i pacchetti vengono installati, è possibile chiudere la scheda per terminare il terminale.

## Creare un cluster di elaborazione

I notebook sono ideali per lo sviluppo o il lavoro iterativo durante la sperimentazione. Durante l'esperimento, è necessario eseguire notebook in un'istanza di calcolo per testare ed esaminare rapidamente il codice. Quando si passa all'ambiente di produzione, è consigliabile eseguire script in un cluster di calcolo. Si creerà un cluster di calcolo con Python SDK e quindi lo si userà per eseguire uno script come processo.

1. Aprire il **notebook Labs/04/Work with compute.ipynb (Usare il notebook compute.ipynb** ).

    > Selezionare **Autentica** e seguire i passaggi necessari se viene visualizzata una notifica che chiede di eseguire l'autenticazione.

1. Verificare che il notebook usi il **kernel Python 3.8 - AzureML** .
1. Eseguire tutte le celle nel notebook.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il **gruppo di risorse rg-dp100-labs** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
