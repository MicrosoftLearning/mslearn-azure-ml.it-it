---
lab:
  title: Esplorare l'area di lavoro Azure Machine Learning
---

# Esplorare l'area di lavoro Azure Machine Learning

Azure Machine Learning offre una piattaforma di data science per eseguire il training e la gestione di modelli di Machine Learning. In questo lab si creerà un'area di lavoro di Azure Machine Learning e si esamineranno i vari modi per usare l'area di lavoro. Il lab è progettato come introduzione delle varie funzionalità di base di Azure Machine Learning e degli strumenti di sviluppo. Per altre informazioni sulle funzionalità più approfondite, sono disponibili altri lab da esplorare.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'**area di lavoro** di Azure Machine Learning offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e gestire i modelli. È possibile effettuare il provisioning di un'area di lavoro usando l'interfaccia interattiva nel portale di Azure oppure usare l'interfaccia della riga di comando di Azure con l'estensione Azure Machine Learning. Nella maggior parte degli scenari di produzione è consigliabile automatizzare il provisioning con l'interfaccia della riga di comando in modo da poter incorporare la distribuzione delle risorse in un processo di sviluppo e operazioni ripetibili (*DevOps*). 

In questo esercizio si userà il portale di Azure per eseguire il provisioning di Azure Machine Learning per esplorare tutte le opzioni.

1. Accedere a `https://portal.azure.com/`.
2. Creare una nuova risorsa di **Azure Machine Learning** con le impostazioni seguenti:
    - **Sottoscrizione**: *la sottoscrizione di Azure usata*
    - **Gruppo di risorse**: `rg-dp100-labs`
    - **Nome dell'area di lavoro**: `mlw-dp100-labs`
    - **Area**: *selezionare l'area geografica più vicina*
    - **Account di archiviazione**: *prendere nota del nuovo account di archiviazione predefinito che verrà creato per l'area di lavoro*
    - **Insieme di credenziali delle chiavi**: *prendere nota del nuovo insieme di credenziali delle chiavi predefinito che verrà creato per l'area di lavoro*
    - **Application Insights**: *prendere nota della nuova risorsa Application Insights predefinita che verrà creata per l'area di lavoro*
    - **Registro contenitori**: nessuno (*ne verrà creato uno automaticamente la prima volta che si distribuisce un modello in un contenitore*)
3. Attendere che vengano create l'area di lavoro e le relative risorse associate, in genere sono necessari circa 5 minuti.

> **Nota**: quando si crea un'area di lavoro di Azure Machine Learning, è possibile usare alcune opzioni avanzate che consentono di limitare l'accesso tramite un *endpoint* privato e di specificare chiavi personalizzate per la crittografia dei dati. Queste opzioni non verranno usate in questo esercizio, ma è importante sapere che sono disponibili.

## Esplorare lo studio di Azure Machine Learning

*Azure Machine Learning Studio* è un portale basato sul Web tramite il quale è possibile accedere all'area di lavoro di Azure Machine Learning. È possibile usare Azure Machine Learning Studio per gestire tutti gli asset e le risorse all'interno dell'area di lavoro.

1. Passare al gruppo di risorse denominato **rg-dp100-labs**.
1. Verificare che il gruppo di risorse contenga l'area di lavoro di Azure Machine Learning, un Application Insights, un insieme di credenziali delle chiavi e un account di archiviazione.
1. Selezionare l'area di lavoro di Azure Machine Learning.
1. Selezionare **Avvia studio** nella pagina **Panoramica**. Nel browser sarà aperta un'altra scheda per aprire studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati in Studio.
1. Si notino le diverse pagine visualizzate sul lato sinistro dello studio. Se nel menu sono visibili solo i simboli, selezionare l’icona &#9776; per espandere il menu ed esplorare i nomi delle pagine.
1. Si noti la sezione **Creazione**, che include **Notebook**, **ML automatizzato** e **Finestra di progettazione**. Questi sono i tre modi in cui è possibile creare modelli di Machine Learning personalizzati all'interno di Azure Machine Learning Studio.
1. Si noti la sezione **Asset** , che include **Dati**, **Processi**e **Modelli**. Gli asset vengono utilizzati o creati durante il training o l'assegnazione di punteggi a un modello. Gli asset vengono usati per eseguire il training, distribuire e gestire i modelli e possono essere sottoposti a controllo delle versioni per tenere traccia della cronologia.
1. Si noti la sezione **Gestione**, che include **Calcolo** tra le altre cose. Si tratta di risorse infrastrutturali necessarie per eseguire il training o la distribuzione di un modello di Machine Learning.

## Eseguire il training di un modello con AutoML

Per esplorare l'uso degli asset e delle risorse nell'area di lavoro di Azure Machine Learning, provare a eseguire il training di un modello.

Un modo rapido per eseguire il training e trovare il modello migliore per un'attività in base ai dati consiste nell'usare l'opzione **AutoML** .

> **Nota**: Potrebbero apparire dei pop-up per guidare l'utente attraverso lo studio. È possibile chiudere e ignorare tutti i popup e concentrarsi sulle istruzioni di questo lab.

1. Scaricare i dati di training che verranno usati in `https://github.com/MicrosoftLearning/mslearn-azure-ml/raw/refs/heads/main/Labs/02/diabetes-data.zip` e estrarre i file compressi.
1. Tornare alla studio di Azure Machine Learning, selezionare la **pagina AutoML** dal menu sul lato sinistro dello studio.
1. Selezionare **+ Nuovo processo** di Machine Learning automatizzato.
1. **Nel passaggio Impostazioni di base** assegnare un nome univoco al processo di training e all'esperimento oppure usare i valori predefiniti assegnati. Selezionare **Avanti**.
1. **Nel passaggio Tipo di attività e dati** selezionare **Classificazione** come tipo di attività e selezionare **+ Crea** per aggiungere i dati di training.
2. **Nella pagina Crea asset** di dati, nel **passaggio Tipo di** dati assegnare un nome all'asset di dati , ad esempio `training-data`, e selezionare **Avanti**.
1. **Nel passaggio Origine** dati selezionare **Da file** locali per caricare i dati di training scaricati in precedenza. Selezionare **Avanti**.
1. **Nel passaggio Tipo di** archiviazione di destinazione verificare che **Archiviazione BLOB di Azure** sia selezionato come tipo di archivio dati e che **workspaceblobstore** sia l'archivio dati selezionato. Selezionare **Avanti**.
1. **Nel passaggio di selezione** MLTable selezionare **Carica cartella** e selezionare la cartella estratta dal file compresso scaricato in precedenza. Selezionare **Avanti**.
1. Esaminare le impostazioni per l'asset di dati e selezionare **Crea**.
1. Tornare al **passaggio Tipo di attività e dati** , selezionare i dati appena caricati e selezionare **Avanti**.

> **Suggerimento**: potrebbe essere necessario selezionare di nuovo il **tipo di attività Classificazione** prima di passare al passaggio successivo.

1. **Nel passaggio Impostazioni attività** selezionare **Diabetic (Boolean)** come colonna di destinazione, quindi aprire l'opzione **Visualizza impostazioni** di configurazione aggiuntive.
1. **Nel riquadro Configurazione** aggiuntiva modificare la metrica primaria in **Accuratezza** e quindi selezionare **Salva**.
1. Espandere l'opzione **Limiti** e impostare le proprietà seguenti:
    * **Numero massimo di versioni di valutazione**: 10
    * **Timeout dell'esperimento (minuti):** 60
    * **Timeout di iterazione (minuti):** 15
    * **Abilitare la terminazione** anticipata: selezionata

1. Per **Dati** di test selezionare Divisione **train-test** e verificare che il **test percentuale dei dati** sia 10. Selezionare **Avanti**.
1. **Nel passaggio Calcolo** verificare che il tipo di calcolo sia **Serveless** e che la dimensione della macchina virtuale selezionata sia **Standard_DS3_v2**. Selezionare **Avanti**.

> **Nota**: Le istanze di calcolo e i cluster sono basati su immagini di macchine virtuali di Azure Standard. Per questo esercizio, l'immagine *Standard_DS3_v2* è consigliata per ottenere il bilanciamento ottimale dei costi e delle prestazioni. Se la quota della sottoscrizione in uso non include questa immagine, scegliere un'immagine alternativa, ma tenere presente che un'immagine più grande può generare costi più elevati e un'immagine più piccola potrebbe non essere sufficiente per completare le attività. In alternativa, chiedere all'amministratore di Azure di estendere la quota.

1. Esaminare tutte le impostazioni e selezionare **Invia processo** di training.

## Usare i processi per visualizzare la cronologia

Dopo aver inviato il processo, si verrà reindirizzati alla pagina del processo. I processi consentono di tenere traccia dei carichi di lavoro eseguiti e confrontarli tra loro. I processi appartengono a un **esperimento**, che consente di raggruppare le esecuzioni di processi. 

1. Si noti che nei **parametri Panoramica** è possibile trovare lo stato del processo, chi lo ha creato, quando è stato creato e quanto tempo è necessario per l'esecuzione (tra le altre cose).
1. Il completamento del processo di training richiederà 10-20 minuti. Al termine, è anche possibile visualizzare i dettagli di ogni singola esecuzione del componente, incluso l'output. È possibile esplorare la pagina del processo per comprendere come vengono sottoposti a training i modelli.

    Azure Machine Learning tiene automaticamente traccia delle proprietà del processo. Usando i processi, è possibile visualizzare facilmente la cronologia per comprendere cosa hanno già fatto l'utente o i colleghi.
    Durante la sperimentazione, i processi consentono di tenere traccia dei diversi modelli di cui si esegue il training per confrontare e identificare il modello migliore. Durante l'ambiente di produzione, i processi consentono di verificare se i carichi di lavoro automatizzati sono stati eseguiti come previsto.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda Studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-labs**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
