---
lab:
  title: Rendere disponibili i dati in Azure Machine Learning
---

# Rendere disponibili i dati in Azure Machine Learning

Sebbene i dati vengano generalmente usati sul file system locale, in un ambiente aziendale può essere più vantaggioso archiviare i dati in una posizione centrale a cui possano accedere più data scientist e professionisti del Machine Learning.

In questo esercizio verranno presi in esame gli *archivi dati* e gli *asset di dati*, che sono gli oggetti solitamente usati per astrarre l'accesso ai dati in Azure Machine Learning.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'*area di lavoro* di Azure Machine Learning offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e gestire i modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite l'interfaccia della riga di comando di Studio, Python SDK e Azure.

Si userà uno script Shell che usa l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e delle risorse necessarie. Si userà quindi la finestra di progettazione nello studio di Azure Machine Learning per eseguire il training e confrontare i modelli.

### Creare l'area di lavoro e le risorse dell’ambiente di calcolo

Per creare l'area di lavoro di Azure Machine Learning e le risorse dell’ambiente calcolo, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari sono raggruppati in uno script shell da eseguire.

1. Nel browser, aprire il portale di Azure all'indirizzo `https://portal.azure.com/`, eseguendo l'accesso con l'account Microsoft.
1. Selezionare il pulsante \[>_] (*Cloud Shell*) nella parte superiore della pagina a destra della casella di ricerca. Si aprirà un riquadro di Cloud Shell nella parte inferiore del portale.
1. Se richiesto, selezionare **Bash**. La prima volta che si apre Cloud Shell, verrà chiesto di scegliere il tipo di shell da usare (*Bash* o *PowerShell*).
1. Verificare che sia specificata la sottoscrizione corretta e selezionare **Crea risorsa di archiviazione**, se è richiesto di creare la risorsa di archiviazione per la cloud shell. Attendere la creazione della risorsa di archiviazione.
1. Immettere i comandi seguenti nel terminale per clonare questo repository:

    ```azurecli
    rm -r azure-ml-labs -f
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

    > Usare `SHIFT + INSERT` per incollare in Cloud Shell il codice copiato.

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo script **setup.sh** che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/03
    ./setup.sh
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che le estensioni non sono state installate.

1. Attendere il completamento dello script. Questa operazione richiede in genere circa 5-10 minuti.

## Esplorare gli archivi dati predefiniti

Quando si crea un'area di lavoro Azure Machine Learning, viene creato un account di archiviazione che viene connesso automaticamente all'area di lavoro. Si esaminerà il modo in cui l'account di archiviazione è connesso.

1. Nel portale di Azure passare al nuovo gruppo di risorse denominato **rg-dp100-...**.
1. Selezionare l'account di archiviazione nel gruppo di risorse. Il nome inizia spesso con il nome specificato per l'area di lavoro (senza trattini).
1. Esaminare la pagina **Panoramica** dell'account di archiviazione. Si noti che l'account di archiviazione include diverse opzioni per l’**archiviazione dei dati**, come illustrato nel riquadro Panoramica e nel menu a sinistra.
1. Selezionare **Contenitori** per esplorare la parte di archiviazione BLOB dell'account di archiviazione.
1. Prendere nota del contenitore **azureml-blobstore-...**. L'archivio dati predefinito per gli asset di dati usa questo contenitore per archiviare i dati.
1. Tramite il pulsante **Contenitore** &#43; nella parte superiore della schermata, creare un nuovo contenitore e denominarlo `training-data`.
1. Selezionare **Condivisioni file** dal menu a sinistra per esplorare la parte Condivisione file dell'account di archiviazione.
1. Prendere nota del **codice-...** condivisione file. Tutti i notebook dell'area di lavoro vengono archiviati qui. Dopo aver clonato i materiali del lab, è possibile trovare i file in questa condivisione file, nella cartella **code-.../Users/*your-user-name*/azure-ml-labs**.

## Copiare la chiave di accesso

Per creare un archivio dati nell'area di lavoro Azure Machine Learning, è necessario fornire alcune credenziali. Un modo semplice per fornire all'area di lavoro l'accesso a un archivio BLOB consiste nell'usare la chiave dell'account.

1. Nell'account di archiviazione selezionare la scheda **Chiavi di accesso** dal menu a sinistra.
1. Si noti che vengono fornite due chiavi: chiave1 e chiave2. Ogni chiave ha la stessa funzionalità. 
1. Selezionare **Mostra** per il campo **Chiave** in **chiave1**.
1. Copiare il valore del campo **Chiave** in Blocco note. Sarà necessario incollare questo valore nel notebook in un secondo momento.
1. Copiare il nome dell'account di archiviazione nella parte superiore della pagina. Il nome deve iniziare con **mlwdp100storage...** Sarà necessario incollare questo valore anche nel notebook.

> **Nota**: Copiare la chiave e il nome dell'account in Blocco note per evitare l'uso automatico delle maiuscole (che si verifica in Word). La chiave fa distinzione tra maiuscole e minuscole.

## Clonare i materiali del lab

Per creare un archivio dati e asset di dati con l’SDK Python, è necessario clonare i materiali del lab nell'area di lavoro.

1. Nel portale di Azure passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-labs**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvio Studio**. Nel browser sarà aperta un'altra scheda per aprire studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati in Studio.
1. All'interno di studio di Azure Machine Learning, passare alla pagina **Calcolo** e verificare che esistano l'istanza di ambiente di calcolo e il cluster creati nella sezione precedente. L'istanza di ambiente di calcolo deve essere in esecuzione, il cluster deve essere inattivo e avere 0 nodi in esecuzione.
1. Nella scheda **Istanze di ambiente di calcolo** individuare l'istanza di ambiente di calcolo e selezionare l'applicazione **Terminale**.
1. Nel terminale installare Python SDK nell'istanza di ambiente di calcolo, eseguendo nel terminale i comandi seguenti:

    ```azurecli
    pip uninstall azure-ai-ml
    pip install azure-ai-ml
    pip install mltable
    ```

    > Ignorare eventuali messaggi (di errore) che dicono che i pacchetti non sono stati installati.

1. Eseguire il comando seguente per clonare nell'area di lavoro un repository Git contenente notebook, dati e altri file:

    ```azurecli
    git clone https://github.com/MicrosoftLearning/mslearn-azure-ml.git azure-ml-labs
    ```

1. Dopo il completamento del comando, nel riquadro **File** fare clic su **&#8635;** per aggiornare la vista e verificare che sia stata creata una nuova cartella **Utenti/*nome-utente*/azure-ml-labs**.

**Facoltativamente**, in un'altra scheda del browser tornare al [portale di Azure](https://portal.azure.com?azure-portal=true). Esplorare di nuovo il **codice-...** di condivisione file nell'account di archiviazione per trovare i materiali del lab clonati nella cartella **azure-ml-labs** appena creata.

## Creare un archivio dati e asset di dati

Il codice per creare un archivio dati e asset di dati con l’SDK Python viene fornito in un notebook.

1. Aprire il notebook **Labs/03/Work with data.ipynb**.

    > Selezionare **Autenticare** e, se viene visualizzata una notifica che chiede di eseguire l'autenticazione, seguire i passaggi necessari.

1. Verificare che il notebook usi il kernel **Python 3.8 - AzureML**.
1. Eseguire tutte le celle nel notebook.

## Facoltativo: Esplorare gli asset di dati

**Facoltativamente**, è possibile esplorare il modo in cui gli asset di dati vengono archiviati nell'account di archiviazione associato.

1. Passare alla scheda **Dati** in studio di Azure Machine Learning per esplorare gli asset di dati.
1. Selezionare il nome dell'asset di dati **diabetes-local** per esplorarne i dettagli. 

    In **Origini dati** per l'asset di dati **diabetes-local** è possibile trovare il percorso in cui è stato caricato il file. Il percorso che inizia con **LocalUpload/...** mostra il percorso all'interno del contenitore dell'account di archiviazione **azureml-blobstore-...**. È possibile verificare l’esistenza del file accedendo a tale percorso dal portale di Azure.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
