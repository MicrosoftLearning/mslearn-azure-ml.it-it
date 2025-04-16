---
lab:
  title: Eseguire il training di un modello con la finestra di progettazione di Azure Machine Learning
---

# Eseguire il training di un modello con la finestra di progettazione di Azure Machine Learning

La finestra di progettazione di Azure Machine Learning offre un'interfaccia di trascinamento della selezione con cui è possibile definire un flusso di lavoro. È possibile creare un flusso di lavoro per eseguire il training di un modello, testarlo e confrontare più algoritmi con facilità.

In questo esercizio si userà la finestra di progettazione per eseguire rapidamente il training e confrontare due algoritmi di classificazione.

## Prima di iniziare

Sarà necessaria una [sottoscrizione di Azure](https://azure.microsoft.com/free?azure-portal=true) con accesso di livello amministrativo.

## Effettuare il provisioning di un'area di lavoro di Azure Machine Learning

Un'*area di lavoro* di Azure Machine Learning offre una posizione centrale per gestire tutte le risorse e gli asset necessari per eseguire il training e gestire i modelli. È possibile interagire con l'area di lavoro di Azure Machine Learning tramite l'interfaccia della riga di comando di Studio, Python SDK e Azure.

Si userà uno script Shell che usa l'interfaccia della riga di comando di Azure per effettuare il provisioning dell'area di lavoro e delle risorse necessarie. Si userà quindi la finestra di progettazione nello studio di Azure Machine Learning per eseguire il training e confrontare i modelli.

### Creare l'area di lavoro e il cluster di elaborazione

Per creare l'area di lavoro di Azure Machine Learning e un cluster di elaborazione, si userà l'interfaccia della riga di comando di Azure. Tutti i comandi necessari sono raggruppati in uno script shell da eseguire.

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

1. Dopo aver clonato il repository, immettere i comandi seguenti per passare alla cartella per questo lab ed eseguire lo script setup.sh che contiene:

    ```azurecli
    cd azure-ml-labs/Labs/05
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

## Configurare una nuova pipeline

Dopo aver creato l'area di lavoro e il cluster di elaborazione necessario, è possibile aprire studio di Azure Machine Learning e creare una pipeline di training con la finestra di progettazione.

1. Nel portale di Azure, passare all'area di lavoro di Azure Machine Learning denominata **mlw-dp100-...**.
1. Selezionare l'area di lavoro di Azure Machine Learning e nella relativa pagina **Panoramica** selezionare **Avvio Studio**. Nel browser verrà aperta un'altra scheda per aprire studio di Azure Machine Learning.
1. Chiudere tutti i popup visualizzati in Studio.
1. All'interno di studio di Azure Machine Learning, passare alla pagina **Calcolo** e verificare che esista il cluster di elaborazione creato nella sezione precedente. Il cluster deve essere inattivo e avere 0 nodi in esecuzione.
1. Passare alla pagina **Finestra di progettazione**.
1. Selezionare la scheda **Personalizzata** nella parte superiore della pagina.
1. Creare una nuova pipeline vuota usando componenti personalizzati.
1. Modificare il nome predefinito della pipeline (**Pipeline-Creata-il-data***) in `Train-Diabetes-Classifier` selezionando l'icona a forma di matita a destra.


## Crea una nuova pipeline

Per eseguire il training di un modello, sono necessari dei dati. È possibile usare tutti i dati archiviati in un archivio dati o usare un URL accessibile pubblicamente.

1. Nel menu a sinistra selezionare la scheda **Dati**.
1. Trascinare e rilasciare il componente **diabetes-folder** nel pannello Canvas.

    Ora che si dispone dei dati, è possibile passare alla creazione di una pipeline usando componenti personalizzati già esistenti nell'area di lavoro (sono stati creati automaticamente durante l'installazione).

1. Nel menu a sinistra selezionare la scheda **Componenti**.
1. Trascinare e rilasciare il componente **Rimuovi righe vuote** nel pannello Canvas, sotto la cartella **diabetes-folder**.
1. Connettere l'output dei dati all'input del nuovo componente.
1. Trascinare e rilasciare il componente **Normalizza colonne numeriche** nel pannello Canvas, sotto a **rimuovi righe vuote**.
1. Connettere l'output del componente precedente all'input del nuovo componente.
1. Trascinare e rilasciare il componente **Esegui training di un modello di classificazione albero delle decisioni** nel pannello Canvas, sotto le **colonne numeriche normalizzate**.
1. Connettere l'output del componente precedente all'input del nuovo componente.
1. Selezionare **Configura e invia** e nella pagina **Configura processo pipeline** creare un nuovo esperimento e denominarlo `diabetes-designer-pipeline`, quindi selezionare **Avanti**.
1. In **Input e output** non apportare modifiche e selezionare **Avanti**.
1. Nelle **impostazioni di runtime** selezionare **Cluster di elaborazione** e in **Seleziona il cluster di elaborazione di Azure ML** selezionare il *cluster-aml*.
1. Selezionare **Rivedi e invia**, quindi **Invia** per avviare l'esecuzione della pipeline.
1. È possibile controllare lo stato dell'esecuzione passando alla pagina **Pipeline** e selezionando la pipeline **Train-Diabetes-Classifier**.
1. Attendere il completamento di tutti i componenti.

    L'invio del processo inizializzerà il cluster di elaborazione. Poiché il cluster di elaborazione era inattivo finora, il ridimensionamento del cluster in più di 0 nodi potrebbe richiedere un po’ di tempo. Una volta ridimensionato il cluster, verrà avviata automaticamente l'esecuzione della pipeline.

Sarà possibile tenere traccia dell'esecuzione di ogni componente. Quando la pipeline ha esito negativo, sarà possibile esplorare il componente che ha dato errore e il motivo. I messaggi di errore verranno visualizzati nella scheda **Output e log** della panoramica del processo.

## Eseguire il training di un secondo modello per ottenere un confronto

Per confrontare algoritmi e valutare le prestazioni migliori, è possibile eseguire il training di due modelli all'interno di una pipeline e confrontarli.

1. Tornare alla **finestra di progettazione** e selezionare la bozza della pipeline **Train-Diabetes-Classifier**.
1. Aggiungere il componente **Esegui training di un modello di classificazione di regressione logistica** al pannello Canvas accanto all'altro componente di training.
1. Connettere l'output del componente **Normalizza colonne numeriche** all'input del nuovo componente di training.
1. Nella parte superiore selezionare **Configura e invia**.
1. Nella pagina **Informazioni di base** creare un nuovo esperimento denominato `designer-compare-classification` ed eseguirlo.
1. Selezionare **Rivedi e invia**, quindi **Invia** per avviare l'esecuzione della pipeline.
1. È possibile controllare lo stato dell'esecuzione passando alla pagina **Pipeline** e selezionando la pipeline **Train-Diabetes-Classifier** con l'esperimento **designer-compare-classification**.
1. Attendere il completamento di tutti i componenti.  
1. Selezionare **Panoramica del processo**, quindi selezionare la scheda **Metriche** per esaminare i risultati per entrambi i componenti di training.
1. Provare a determinare quale modello ha prestazioni migliori.

## Eliminare le risorse di Azure

Quando si finisce di esplorare Azure Machine Learning, è necessario eliminare le risorse create per evitare costi di Azure non necessari.

1. Chiudere la scheda Studio di Azure Machine Learning e tornare al portale di Azure.
1. Nella **Home page** del portale di Azure selezionare **Gruppi di risorse**.
1. Selezionare il gruppo di risorse **rg-dp100-...**.
1. Nel parte superiore della pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
1. Immettere il nome del gruppo di risorse per confermare l'eliminazione e selezionare **Elimina**.
