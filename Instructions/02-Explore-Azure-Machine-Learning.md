---
lab:
  title: Esplorare l'area di lavoro di Azure Machine Learning
---

# Esplorare l'area di lavoro di Azure Machine Learning

Azure Machine Learning offre una piattaforma di data science per eseguire il training e gestire i modelli di Machine Learning. In questo lab si creerà un'area di lavoro di Azure Machine Learning e si esamineranno i vari modi per usare l'area di lavoro. Il lab è progettato come introduzione delle varie funzionalità di base di Azure Machine Learning e degli strumenti di sviluppo. Per altre informazioni sulle funzionalità in modo più approfondito, sono disponibili altri lab da esplorare.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

**Un'area di lavoro di** Azure Machine Learning offre una posizione centrale per la gestione di tutte le risorse e gli asset necessari per eseguire il training e la gestione dei modelli. È possibile effettuare il provisioning di un'area di lavoro usando l'interfaccia interattiva nella portale di Azure oppure usare l'interfaccia della riga di comando di Azure con l'estensione Azure Machine Learning. Nella maggior parte degli scenari di produzione è consigliabile automatizzare il provisioning con l'interfaccia della riga di comando in modo da poter incorporare la distribuzione delle risorse in un processo di sviluppo e operazioni ripetibili (*DevOps*). 

In questo esercizio si userà il portale di Azure per eseguire il provisioning di Azure Machine Learning per esplorare tutte le opzioni.

1. Accedere a `https://portal.azure.com/`.
2. Creare una nuova risorsa di **Azure Machine Learning** con le impostazioni seguenti:
    - **Sottoscrizione**: *la propria sottoscrizione di Azure*
    - **Gruppo di risorse**: `rg-dp100-labs`
    - **Nome area di lavoro**: `mlw-dp100-labs`
    - **Area**: *selezionare l'area geografica più vicina*
    - **Account di archiviazione**: *prendere nota del nuovo account di archiviazione predefinito che verrà creato per l'area di lavoro*
    - **Insieme di credenziali delle chiavi**: *prendere nota del nuovo insieme di credenziali delle chiavi predefinito che verrà creato per l'area di lavoro*
    - **Application Insights**: *prendere nota della nuova risorsa Application Insights predefinita che verrà creata per l'area di lavoro*
    - **Registro contenitori**: Nessuno (*ne verrà creato uno automaticamente la prima volta che si distribuisce un modello in un contenitore*)
3. Attendere la creazione dell'area di lavoro e delle relative risorse associate. Questa operazione richiede in genere circa 5 minuti. 

> **Nota**: quando si crea un'area di lavoro di Azure Machine Learning, è possibile usare alcune opzioni avanzate che consentono di limitare l'accesso tramite un *endpoint* privato e di specificare chiavi personalizzate per la crittografia dei dati. Queste opzioni non verranno usate in questo esercizio, ma è importante sapere che sono disponibili.

## Esplorare il studio di Azure Machine Learning

*studio di Azure Machine Learning* è un portale basato sul Web tramite il quale è possibile accedere all'area di lavoro di Azure Machine Learning. È possibile usare il studio di Azure Machine Learning per gestire tutti gli asset e le risorse all'interno dell'area di lavoro. 

1. Passare al gruppo di risorse denominato **rg-dp100-labs**.
1. Verificare che il gruppo di risorse contenga l'area di lavoro di Azure Machine Learning, application Insights, un Key Vault e un account di archiviazione. 
1. Selezionare l'area di lavoro di Azure Machine Learning.
1. Selezionare **Launch Studio (Avvia Studio** ) nella pagina **Panoramica** . Nel browser verrà aperta un'altra scheda per aprire il studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati nello studio. 
1. Si notino le diverse pagine visualizzate sul lato sinistro dello studio. Se nel menu sono visibili solo i simboli, selezionare l'icona &#9776; per espandere il menu ed esplorare i nomi delle pagine. 
1. Si noti la sezione **Autore** , che include **notebook**, **Machine Learning automatizzato** e **Finestra di progettazione**. Questi sono i tre modi in cui è possibile creare modelli di Machine Learning personalizzati all'interno del studio di Azure Machine Learning.
1. Si noti la sezione **Asset** , che include **dati**, **processi** e **modelli** , tra le altre cose. Gli asset vengono utilizzati o creati durante il training o l'assegnazione di punteggi a un modello. Gli asset vengono usati per eseguire il training, distribuire e gestire i modelli e possono essere sottoposti a controllo delle versioni per tenere traccia della cronologia.
1. Si noti la sezione **Gestione** , che include **risorse di calcolo** e **archivi dati** , tra le altre cose. Si tratta di risorse infrastrutturali necessarie per eseguire il training o la distribuzione di un modello di Machine Learning. 

## Creare una pipeline di training

Per esplorare l'uso di asset e risorse nell'area di lavoro di Azure Machine Learning, provare a eseguire il training di un modello. 

Un modo rapido per creare una pipeline di training del modello consiste **nell'usare** progettazione.

> [!Note]
> I popup possono essere visualizzati in tutto per guidare l'utente attraverso lo studio. È possibile chiudere e ignorare tutti i popup e concentrarsi sulle istruzioni di questo lab.

1. Selezionare la pagina **Progettazione** dal menu a sinistra dello studio.
1. Selezionare l'esempio **Regressione - Automobile Price Prediction (Basic).** 
    
    Viene visualizzata una nuova pipeline. Nella parte superiore della pipeline viene visualizzato un componente per caricare **i dati sui prezzi delle automobili (non elaborati).** La pipeline elabora i dati ed esegue il training di un modello di regressione lineare per stimare il prezzo per ogni automobile.
1. Selezionare **Invia** nella parte superiore della pagina. Viene visualizzato un errore quando non è ancora stata selezionata una destinazione di calcolo. La pipeline non può essere eseguita senza risorse di calcolo. 

Verrà ora creata una destinazione di calcolo.

## Creare una destinazione di calcolo

Per eseguire qualsiasi carico di lavoro all'interno dell'area di lavoro di Azure Machine Learning, è necessaria una risorsa di calcolo. Uno dei vantaggi offerti da Azure Machine Learning è la possibilità di creare un ambiente di calcolo basato sul cloud in cui è possibile eseguire esperimenti e script di training su larga scala.

1. Nella studio di Azure Machine Learning **selezionare** la pagina Calcolo dal menu a sinistra. Esistono quattro tipi di risorse di calcolo che è possibile usare:
    - **Istanze di calcolo**: una macchina virtuale gestita da Azure Machine Learning. Ideale per lo sviluppo durante l'esplorazione dei dati e l'esperimento iterativo con i modelli di Machine Learning. 
    - **Cluster di elaborazione**: cluster scalabili di macchine virtuali per l'elaborazione su richiesta del codice di un esperimento. Ideale per l'esecuzione di codice di produzione o processi automatizzati.
    - **Cluster di inferenza**: cluster Kubernetes usato durante l'inferenza. Ideale per la distribuzione di modelli in tempo reale su larga scala.
    - **Calcolo collegato**: collegare le risorse di calcolo di Azure esistenti all'area di lavoro, ad esempio i cluster Macchine virtuali o Azure Databricks.

Per eseguire il training di un modello di Machine Learning creato con Progettazione, è possibile usare un'istanza di calcolo o un cluster di calcolo.

2. Nella scheda **Istanze di ambiente di calcolo** aggiungere una nuova istanza di ambiente di calcolo con le impostazioni specificate di seguito. 
    - **Nome dell'ambiente di calcolo**: *immettere un nome univoco*
    - **Località**: *automaticamente la stessa posizione dell'area di lavoro*
    - **Tipo di macchina virtuale**: `CPU`
    - **Dimensioni macchina virtuale**: `Standard_DS11_v2`
    - **Quota disponibile**: mostra i core dedicati disponibili.
    - **Mostra impostazioni avanzate**: esaminare le impostazioni seguenti, ma non selezionarle.
        - **Abilitare l'accesso SSH**: `Unselected` *è possibile usare questa opzione per abilitare l'accesso diretto alla macchina virtuale usando un client SSH.*
        - **Abilitare la rete virtuale**: `Unselected` *(in genere si usa questo in un ambiente aziendale per migliorare la sicurezza di rete)*
        - **Assegnare a un altro utente**: `Unselected` *(è possibile usarlo per assegnare un'istanza di calcolo a un data scientist)*
        - **Effettuare il provisioning con lo script di installazione**: `Unselected` *(è possibile usare questa opzione per aggiungere uno script da eseguire nell'istanza remota al momento della creazione)*

3. Selezionare **Crea** e attendere l'avvio dell'istanza di calcolo e il relativo stato in **Esecuzione**.

> **Nota**: Le istanze di calcolo e i cluster sono basati su immagini di macchine virtuali di Azure Standard. Per questo esercizio, è consigliabile usare l'immagine *Standard_DS11_v2* per ottenere un equilibrio ottimale tra costi e prestazioni. Se la quota della sottoscrizione in uso non include questa immagine, scegliere un'immagine alternativa, ma tenere presente che un'immagine superiore può generare costi più elevati e un'immagine inferiore potrebbe non essere sufficiente per completare le attività. In alternativa, chiedere all'amministratore di Azure di estendere la quota.

## Eseguire la pipeline di training

È stata creata una destinazione di calcolo ed è ora possibile eseguire la pipeline di training di esempio in Progettazione.

1. Passare alla pagina **Progettazione** .
1. Selezionare la scheda **predefinita Classica** .
1. Selezionare la bozza della pipeline **Regression - Automobile Price Prediction (basic).**
1. Selezionare **Impostazioni** in alto a destra per espandere il riquadro **Impostazioni** .
1. **Selezionare Istanza di** calcolo in **Selezionare il tipo di calcolo**.
1. Selezionare l'istanza di calcolo appena creata in **Selezionare il cluster di calcolo di Azure ML**. 
1. Selezionare **Invia** per eseguire di nuovo la pipeline di training.
1. Verrà visualizzata una finestra popup per configurare il processo della pipeline. Configurare e inviare un nuovo processo della pipeline con le impostazioni seguenti:
    - **Esperimento**: `Create new`
    - **Nuovo nome esperimento**: `train-regression-designer`
    - Mantenere tutte le altre impostazioni predefinite.

La pipeline di training verrà ora inviata all'istanza di calcolo. La pipeline richiederà circa 10 minuti per il completamento della pipeline. Esaminiamo alcune altre pagine nel frattempo.

## Usare i processi per visualizzare la cronologia

Ogni volta che si esegue uno script o una pipeline nell'area di lavoro di Azure Machine Learning, viene registrato come **processo**. I processi consentono di tenere traccia dei carichi di lavoro eseguiti e confrontarli tra loro. I processi appartengono a un **esperimento**, che consente di raggruppare i processi insieme.

1. Passare alla pagina **Processi** usando il menu a sinistra della studio di Azure Machine Learning.
1. Selezionare l'esperimento **train-regression-designer** per visualizzarne le esecuzioni. Qui verrà visualizzata una panoramica di tutti i processi che fanno parte di questo esperimento. Se sono state eseguite più pipeline di training, questa visualizzazione consente di confrontare le pipeline e identificare quella migliore.
1. Selezionare l'ultimo processo nell'esperimento **train-regression-designer** .
1. Si noti che la pipeline di training viene visualizzata in cui è possibile visualizzare i componenti eseguiti correttamente o non riusciti. Se il processo è ancora in esecuzione, è anche possibile identificare ciò che è attualmente in esecuzione.
1. Per visualizzare i dettagli del processo della pipeline, selezionare la **panoramica** del processo in alto a destra per espandere la **panoramica del processo pipeline**. 
1. Si noti che nei parametri **Panoramica** è possibile trovare lo stato del processo, che ha creato la pipeline, quando è stata creata e quanto tempo è necessario eseguire la pipeline completa (tra le altre cose).

    Quando si esegue uno script o una pipeline come processo, è possibile definire qualsiasi input e documentare eventuali output. Azure Machine Learning tiene traccia automaticamente delle proprietà del processo. Usando i processi, è possibile visualizzare facilmente la cronologia per comprendere cosa sono già stati eseguiti o i colleghi. 
    
    Durante la sperimentazione, i processi consentono di tenere traccia dei diversi modelli che si esegue il training per confrontare e identificare il modello migliore. Durante la produzione, i processi consentono di verificare se i carichi di lavoro automatizzati sono stati eseguiti come previsto.

1. Al termine del processo, è anche possibile visualizzare i dettagli di ogni singola esecuzione del componente, incluso l'output. È possibile esplorare la pipeline per comprendere come viene eseguito il training del modello.

## Eliminare le risorse di Azure

Al termine dell'esplorazione di Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda studio di Azure Machine Learning e tornare alla portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-labs** .
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**. 
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.