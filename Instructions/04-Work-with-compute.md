---
lab:
  title: Usare le risorse di calcolo in Azure Machine Learning
---

# Usare le risorse di calcolo in Azure Machine Learning

Uno dei principali vantaggi del cloud è la possibilità di usare risorse di calcolo scalabili su richiesta per un'elaborazione conveniente di dati di grandi dimensioni.

In questo esercizio si imparerà a usare il calcolo cloud in Azure Machine Learning per eseguire esperimenti e codice di produzione su larga scala.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'*area di lavoro* di Azure Machine Learning offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e gestire i modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite l'interfaccia della riga di comando di studio, Python SDK e Azure.

Per creare l'area di lavoro di Azure Machine Learning, si userà l’interfaccia della riga di comando di Azure. Tutti i comandi necessari sono raggruppati in uno script shell da eseguire.

1. Nel browser, aprire il portale di Azure all'indirizzo `https://portal.azure.com/`, eseguendo l'accesso con l'account Microsoft.
1. Selezionare il pulsante \[>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Se richiesto, selezionare **Bash**. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che sia specificata la sottoscrizione corretta e che **non sia selezionato Nessun account di archiviazione necessario** . Selezionare **Applica**.
1. Per evitare conflitti con le versioni precedenti, rimuovere tutte le estensioni dell'interfaccia della riga di comando di AML (entrambe le versioni, 1 e 2) eseguendo questo comando nel terminale:

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

1. Attendere il completamento del comando, in genere sono necessari circa 5-10 minuti.

## Creare lo script di installazione del calcolo

Per eseguire i notebook all'interno dell'area di lavoro di Azure Machine Learning, è necessaria un'istanza di ambiente di calcolo. Per configurare l’istanza di ambiente di calcolo durante la creazione è possibile usare uno script di installazione.

1. Nel portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-labs**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvio Studio**. Nel browser verrà aperta un'altra scheda per aprire studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati in Studio.
1. All’interno di studio di Azure Machine Learning, passare alla pagina **Notebook**.
1. Nel riquadro **File** selezionare l’icona &#10753; per **Aggiungere file**.
1. Selezionare **Create new file** (Crea nuovo file).
1. Verificare che il percorso del file sia **Utenti/* nome-utente***.
1. Modificare il tipo di file in **Bash (*.sh)**.
1. Modificare il nome file in `compute-setup.sh`.
1. Aprire il file **compute-setup.sh** appena creato e incollare quanto di seguito nel contenuto:

    ```azurecli
    #!/bin/bash

    # clone repository
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Salvare il **file compute-setup.sh**.

## Creare l'istanza di ambiente di calcolo

Per creare l'istanza ambiente di calcolo, è possibile usare Studio, Python SDK o l'interfaccia della riga di comando di Azure. Si userà Studio per creare l'istanza di ambiente di calcolo con lo script di installazione appena creato.

1. Usando il menu a sinistra, passare alla pagina **Calcolo**.
1. Nella scheda **istanze di ambiente di calcolo** selezionare **Nuovo**.
1. Configurare (ma non creare) l'istanza di ambiente di calcolo con le impostazioni seguenti: 
    - **Nome dell'ambiente di calcolo**: *immettere un nome univoco*
    - **Tipo di macchina virtuale**: *CPU*
    - **Dimensioni macchina virtuale**: *Standard_DS11_v2*
1. Selezionare **Avanti**.
1. Selezionare **Aggiungi pianificazione** e configurare la pianificazione per **arrestare** l'istanza di ambiente di calcolo, ogni giorno alle **18:00** o **6:00 PM**.
1. Selezionare **Avanti**.
1. Esaminare le impostazioni di sicurezza ma **non** selezionarle:
    - **Abilita accesso SSH**: *è possibile usarla per abilitare l'accesso diretto alla macchina virtuale usando un client SSH.*
    - **Abilita rete virtuale**: *viene solitamente usata negli ambienti aziendali per migliorare la sicurezza di rete.*
    - **Assegna a un altro utente**: *è possibile usarla per assegnare un'istanza di ambiente di calcolo a uno scienziato dei dati.*
1. Selezionare **Avanti**.
1. Selezionare l'interruttore per **Effettuare il provisioning con uno script di creazione**.
1. Selezionare lo script **compute-setup.sh** creato in precedenza.
1. Selezionare **Rivedi e crea** per creare l'istanza di ambiente di calcolo e attendere l'avvio e il relativo stato per passare a **In esecuzione**.
1. Quando l'istanza di calcolo è in esecuzione, passare alla pagina **Notebook**. Nel riquadro **File** fare clic su **&#8635;** per aggiornare la visualizzazione e verificare che sia stata creata una nuova cartella **Utenti/*nome-utente*/dp100-azure-ml-labs**.

## Configurare l'istanza di ambiente di calcolo

Dopo aver creato l'istanza di ambiente di calcolo, è possibile eseguirvi i notebook. Per eseguire il codice desiderato, potrebbe essere necessario installare determinati pacchetti. È possibile includere pacchetti nello script di installazione, oppure installarli usando il terminale.

1. Nella scheda **Istanze di calcolo** individuare l'istanza di ambiente di calcolo e selezionare l'applicazione **Terminale**.
1. Nel terminale installare Python SDK nell'istanza di ambiente di calcolo, eseguendo nel terminale i comandi seguenti:

    ```
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che i pacchetti non sono stati installati.

1. Una volta che siano stati installati, è possibile chiudere la scheda per terminare il terminale.

## Creare un cluster di elaborazione

I notebook sono ideali per lo sviluppo o per il lavoro iterativo durante le fasi di sperimentazione. Per tali fasi sarà necessario eseguire i notebook in un'istanza di ambiente di calcolo per testare ed esaminare rapidamente il codice. Quando si passa all'ambiente di produzione, è consigliabile eseguire script in un cluster di elaborazione. Si creerà un cluster di elaborazione con Python SDK, quindi lo si userà per eseguire uno script come processo.

1. Aprire il notebook **Labs/04/Work with compute.ipynb**.

    > Selezionare **Autenticare** e, se viene visualizzata una notifica che chiede di eseguire l'autenticazione, seguire i passaggi necessari.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML**.
1. Eseguire tutte le celle nel notebook.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda Studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-labs**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
