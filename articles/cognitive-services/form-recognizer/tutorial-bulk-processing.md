---
title: 教學課程：使用 Azure Data Factory 大量擷取表單資料 - 表單辨識器
titleSuffix: Azure Cognitive Services
description: 設定 Azure Data Factory 活動來觸發表單辨識器模型的定型和執行，以將大量的待辦文件數位化。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 1780aebc113fa68a9a89cfce9fd67c9b5911fc58
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606652"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>教學課程：使用 Azure Data Factory 大量擷取表單資料

在本教學課程中，我們將探討如何使用 Azure 服務，將一大批表單擷取到數位媒體內。 本教學課程將示範如何將資料自動地從文件的 Azure Data Lake 擷取到 Azure SQL 資料庫內。 只要按幾下滑鼠，就可以快速地為模型定型並處理新的文件。

## <a name="business-need"></a>商務需求

大部分組織現在都知道其不同格式的資料 (PDF、影像、影片) 有多寶貴。 其正在尋求最佳做法及最符合成本效益的方式來將這些資產數位化。

此外，客戶通常會從眾多用戶端和客戶收到不同類型的表單。 與[快速入門](./quickstarts/client-library.md)不同的是，本教學課程會示範如何使用以中繼資料驅動的方法，利用新的和不同類型的表單來自動地為模型定型。 如果您沒有適用於指定表單類型的現有模型，系統就會為您建立一個這樣的模型，並向您提供模型識別碼。 

藉由從表單中擷取資料，並將資料與現有的作業系統和資料倉儲結合，企業就可以取得見解並為客戶和商務使用者提供價值。

透過 Azure 表單辨識器，我們可協助組織利用其資料、將程序自動化 (發票付款、稅務處理等等)、節省金錢和時間，並提升資料的準確度。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定 Azure Data Lake 以儲存表單
> * 使用 Azure 資料庫來建立參數化資料表
> * 使用 Azure Key Vault 來儲存敏感性認證
> * 在 Databricks 筆記本中為表單辨識器模型定型
> * 使用 Databricks 筆記本擷取表單資料
> * 使用 Azure Data Factory 自動進行表單的定型和擷取

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="建立表單辨識器資源"  target="_blank">建立表單辨識器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，選取 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至表單辨識器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 至少有五個相同類型的表單。 在理想的情況下，此工作流程是為了支援大型文件集。 請參閱[為建置訓練資料集](./build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本教學課程中，您可以使用 [範例資料集](https://go.microsoft.com/fwlink/?linkid=2128080)中 **Train** 資料夾底下的檔案。

## <a name="project-architecture"></a>專案架構 

此專案代表一組 Azure Data Factory 管線，其會觸發 Python 筆記本，以針對 Azure Data Lake 儲存體帳戶中的文件資料進行定型、分析及擷取。

表單辨識器 REST API 需要將某些參數作為輸入。 為了安全起見，其中的某些參數會儲存在 Azure Key Vault 中，而其他較不敏感的參數 (例如儲存體 Blob 資料夾名稱) 則會儲存在 Azure SQL 資料庫的參數化資料表中。

為了分析表單類型，資料工程師或資料科學家會填入參數資料表的一個資料列。 然後，使用 Azure Data Factory 來逐一查看已偵測到的表單類型清單，並將相關參數傳遞至 Databricks 筆記本，以將表單辨識器模型定型或重新定型。 這裡也可以使用 Azure 函式。

接著，Azure Databricks 筆記本會使用已定型的模型來擷取表單資料，並將該資料匯出到 Azure SQL 資料庫。

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="專案架構":::


## <a name="set-up-azure-data-lake"></a>設定 Azure Data Lake

您的待辦表單可能位於內部部署環境或 (s)FTP 伺服器中。 本教學課程使用 Azure Data Lake Gen 2 儲存體帳戶中的表單。 您可以使用 Azure Data Factory、Azure 儲存體總管或 AzCopy 傳輸位於該處的檔案。 定型和評分資料集可以位於不同的容器，但所有表單類型的定型資料集都必須位於相同的容器中 (但可以位於不同的資料夾)。

若要建立新的 Data Lake，請遵循[建立儲存體帳戶以與 Azure Data Lake Storage Gen2 搭配使用](https://docs.microsoft.com/azure/storage/blobs/create-data-lake-storage-account)中的指示。

## <a name="create-a-parameterization-table"></a>建立參數化資料表

接下來，我們將在 Azure SQL Database 中建立中繼資料表。 此資料表將包含表單辨識器 REST API 所需的非敏感性資料。 每當資料集內有新類型的表單時，我們就會在此資料表中插入新的記錄，並觸發定型和評分管線 (以便之後實作)。

資料表中會使用下列欄位：

* **form_description**：這不是進行定型時的必要欄位。 其會提供要作為模型定型時所用表單類型的描述 (例如，「用戶端 A 表單」、「飯店 B 表單」)。
* **training_container_name**：此欄位是我們儲存定型資料集時所用儲存體帳戶容器的名稱。 其可以是與 **scoring_container_name** 相同的容器。
* **training_blob_root_folder**：在儲存用於為模型定型的檔案時，所用儲存體帳戶內的資料夾。
* **scoring_container_name**：此欄位是我們要從中擷取金鑰值組的檔案在儲存時所用儲存體帳戶容器的名稱。 其可以是與 **training_container_name** 相同的容器。
* **scoring_input_blob_folder**：要從中擷取資料的檔案將會儲存所在儲存體帳戶中的資料夾。
* **model_id**：想要重新定型的模型所具有的識別碼。 第一次執行時，此值必須設定為 -1，以便讓定型筆記本建立新的自訂模型來進行定型。 定型筆記本會將新建立的模型識別碼傳回給 Azure Data Factory 執行個體，而我們可以使用預存程序活動在 Azure SQL 資料庫中更新此值。

  每當您想要擷取新的表單類型時，就必須先手動地將模型識別碼重設為 -1 再為模型定型。

* **file_type**：支援的表單類型有 `application/pdf`、`image/jpeg`、`image/png` 和 `image/tif`。

  如果您有不同檔案類型的表單，則必須在為新表單類型定型時變更此值和 **model_id**。
* **form_batch_group_id**：一段時間之後，您可能會有多個用來對相同模型進行定型的表單類型。 **form_batch_group_id** 可讓您指定已使用特定模型來進行定型的所有表單類型。

### <a name="create-the-table"></a>建立資料表

[建立 Azure SQL Database](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase)，然後在[查詢編輯器](https://docs.microsoft.com/azure/azure-sql/database/connect-query-portal)中執行下列 SQL 指令碼，以建立所需的資料表。

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

執行下列指令碼來建立程序，以便自動在定型之後更新 **model_id**。

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>使用 Azure Key Vault 來儲存敏感性認證

為了安全起見，我們不想將特定敏感性資訊儲存在 Azure SQL 資料庫的參數化資料表中。 我們會將敏感性參數儲存為 Azure Key Vault 秘密。

### <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault

[建立 Key Vault 資源](https://ms.portal.azure.com/#create/Microsoft.KeyVault)。 然後，瀏覽至建立好的 Key Vault 資源，並在 [設定] 區段中選取 [秘密] 來新增參數。

隨即會出現新的視窗，這時請選取 [產生/匯入]。 輸入參數的名稱和值，然後按一下 [建立]。 針對下列參數執行此動作：

* **CognitiveServiceEndpoint**：表單辨識器 API 的端點 URL。
* **CognitiveServiceSubscriptionKey**：表單辨識器服務的存取金鑰。 
* **StorageAccountName**：我們想要從中擷取金鑰值組的定型資料集和表單在儲存時所用的儲存體帳戶。 如果這些項目位於不同帳戶，請將每個帳戶名稱輸入為個別的秘密。 請記住，所有表單類型的定型資料集必須位於相同容器中，但可以位於不同資料夾。
* **StorageAccountSasKey**：儲存體帳戶的共用存取簽章 (SAS)。 若要擷取 SAS URL，請移至儲存體資源，然後選取 [儲存體總管] 索引標籤。導覽至您的容器、按一下滑鼠右鍵，然後選取 [取得共用存取簽章]。 務必取得您容器的 SAS，而不是儲存體帳戶本身的 SAS。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，複製 [URL] 區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>在 Databricks 筆記本中為表單辨識器模型定型

您將使用 Azure Databricks 來儲存及執行 Python 程式碼，以便與表單辨識器服務互動。

### <a name="create-a-notebook-in-databricks"></a>在 Databricks 中建立筆記本

在 Azure 入口網站中[建立 Azure Databricks 資源](https://ms.portal.azure.com/#create/Microsoft.Databricks)。 瀏覽至建立好的資源，然後啟動工作區。

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>建立受 Azure Key Vault 支援的秘密範圍

若要參考上面所建立 Azure Key Vault 中的密碼，您必須在 Databricks 中建立秘密範圍。 請遵循[建立 Azure Key Vault 支援的秘密範圍](https://docs.microsoft.com/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope)底下的步驟來進行。

### <a name="create-a-databricks-cluster"></a>建立 Databricks 叢集

叢集是 Databricks 計算資源的集合。 若要建立叢集：

1. 在提要欄位中，按一下 [叢集] 按鈕。
1. 在 [叢集] 頁面上，按一下 [建立叢集]。
1. 在 [建立叢集] 頁面上指定叢集名稱，然後在 [Databricks Runtime 版本] 下拉式選單中選取 [7.2 (Scala 2.12, Spark 3.0.0)]。
1. 按一下 [建立叢集]。

### <a name="write-a-settings-notebook"></a>撰寫設定筆記本

現在您已準備好新增 Python 筆記本。 首先，建立名為 **設定** 的筆記本；此筆記本會將參數化資料表中的值指派給指令碼中的變數。 稍後，Azure Data Factory 會將這些值作為參數來傳入。 我們也會將 Key Vault 中的秘密值指派給變數。 

1. 若要建立 [設定] 筆記本，請按一下 [工作區] 按鈕，然後在 [新增] 索引標籤中，按一下下拉式清單並依序選取 [建立] 和 [筆記本]。
1. 在快顯視窗中，輸入您要賦予給筆記本的名稱，然後選取 [Python] 作為預設語言。 選取您的 Databricks 叢集，然後選取 [建立]。
1. 在第一個筆記本資料格中，我們會擷取 Azure Data Factory 傳遞的參數。

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. 在第二個資料格中，我們會擷取 Key Vault 中的秘密，然後指派給變數。

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>撰寫定型筆記本

我們已完成 [設定] 筆記本，接下來可以建立筆記本來為模型定型。 如先前所述，我們將使用儲存在 Azure Data Lake Gen 2 儲存體帳戶資料夾 (**training_blob_root_folder**) 中的檔案。 資料夾名稱已作為變數來傳入。 每一組表單類型都會位於相同的資料夾中，而且當我們對參數資料表進行迴圈處理時，我們會使用所有表單類型來為模型定型。 

1. 建立名為 **TrainFormRecognizer** 的新筆記本。 
1. 在第一個資料格中，執行 [設定] 筆記本：

    ```python
    %run "./Settings"
    ```

1. 在下一個資料格中，從 [設定] 檔案指派變數，並針對每個表單類型來為模型進行動態定型，以套用 [REST 快速入門](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20)中的程式碼。

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. 定型程序的最後一個步驟是取得 json 格式的定型結果。

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>使用筆記本來擷取表單資料

### <a name="mount-the-azure-data-lake-storage"></a>裝載 Azure Data Lake 儲存體

下一個步驟是使用已定型的模型來為不同的表單評分。 我們會在 Databricks 中裝載 Azure Data Lake 儲存體帳戶，並在擷取過程中參考此裝載。

就和定型階段一樣，我們將使用 Azure Data Factory 從表單叫用金鑰值組的擷取。 我們會針對參數資料表中所指定資料夾內的表單進行迴圈處理。

1. 讓我們建立筆記本以在 Databricks 中裝載儲存體帳戶。 我們會將其命名為 **MountDataLake**。 
1. 您必須先呼叫 [設定] 筆記本：

    ```python
    %run "./Settings"
    ```

1. 在第二個資料格中，我們將定義敏感性參數的變數 (會從 Key Vault 秘密中擷取)。

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. 接下來，我們會嘗試卸載儲存體帳戶，以免先前已裝載。

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. 最後，我們會裝載儲存體帳戶。


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > 我們只裝載了定型儲存體帳戶&mdash;在此案例中，定型檔案以及我們想要從中擷取金鑰值組的檔案位於相同的儲存體帳戶中。 如果評分和定型儲存體帳戶不同，則必須在這裡同時裝載這兩個儲存體帳戶。 

### <a name="write-the-scoring-notebook"></a>撰寫評分筆記本

現在，我們可以建立評分筆記本。 類似於定型筆記本，我們將使用剛剛裝載的 Azure Data Lake 儲存體帳戶中的資料夾內儲存的檔案。 資料夾名稱會以變數的形式傳遞。 我們會針對指定資料夾中的所有表單進行迴圈處理，並從中擷取金鑰值組。 

1. 建立新的筆記本，並將其命名為 **ScoreFormRecognizer**。 
1. 執行 [設定] 和 [MountDataLake] 筆記本。

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. 然後，新增下列程式碼，以呼叫[分析](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API。

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. 在下一個資料格中，我們將取得金鑰值組的擷取結果。 此資料格會輸出結果。 因為我們想要讓 JSON 格式的結果能夠進一步處理到 Azure SQL Database 或 Cosmos DB，所以我們會將結果撰寫至 .json 檔案。 輸出檔案名稱會是已評分檔案的名稱串連上 "_output.json"。 此檔案會儲存在與來源檔案相同的資料夾中。

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. 在新的資料格中執行檔案撰寫程序：

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>使用 Azure Data Factory 將定型和評分自動化

唯一剩下的步驟是設定 Azure Data Factory (ADF) 服務，以將定型和評分程序自動化。 首先，請遵循[建立資料處理站](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)底下的步驟進行。 建立 ADF 資源之後，您將需要建立三個管線：一個用於定型，兩個用於評分 (說明如下)。

### <a name="training-pipeline"></a>定型管線

定型管線中的第一個活動是查閱，目的是為了在 Azure SQL 資料庫的參數化資料表中讀取和傳回值。 由於所有定型資料集都位於相同的儲存體帳戶和容器中 (但可能位於不同的資料夾中)，因此我們會在查閱活動設定中保留預設值 [僅第一列] 屬性。 針對要用來為模型定型的每一種表單類型，我們會使用 **training_blob_root_folder** 中的所有檔案來為模型定型。

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="資料處理站中的定型管線":::

預存程序可接受兩個參數：**model_id** 和 **form_batch_group_id**。 用來從 Databricks 筆記本傳回模型識別碼的程式碼是 `dbutils.notebook.exit(model_id)`，而在資料處理站中用來讀取預存程序活動中程式碼的程式碼則是 `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`。

### <a name="scoring-pipelines"></a>評分管線

為了擷取金鑰值組，我們將掃描參數化資料表中的所有資料夾，而且針對每個資料夾，我們將會擷取其中所有檔案的金鑰值組。 就目前為止，ADF 不支援嵌套的 ForEach 迴圈。 因此，我們會改為建立兩個管線。 第一個管線會從參數化資料表執行查閱，並將資料夾清單作為參數傳遞至第二個管線。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="資料處理站中的第一個評分管線":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="資料處理站中的第一個評分管線，詳細資料":::

第二個管線會使用 GetMeta 活動來取得資料夾中的檔案清單，並將其作為參數傳遞給評分 Databricks 筆記本。

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="資料處理站中的第二個評分管線":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="資料處理站中的第二個評分管線，詳細資料":::

### <a name="specify-a-degree-of-parallelism"></a>指定平行處理原則的程度

在定型和評分管線中，您都可以指定平行處理原則的程度，以同時處理多個表單。

若要在 ADF 管線中設定平行處理原則的程度：

* 選取 [Foreach] 活動。
* 取消核取 [循序] 方塊。
* 在 [批次計數] 文字方塊中，設定平行處理原則的程度。 建議您使用 15 作為評分的批次計數上限。

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="ADF 中評分活動的平行處理原則設定":::

## <a name="how-to-use"></a>如何使用

現在您已有自動化管線可將待辦表單數位化，並在其上執行一些分析。 當您將熟悉類型的新表單新增至現有的儲存體資料夾時，只要重新執行評分管線，這些管線就會更新所有輸出檔案，包括新表單的輸出檔案。 

如果您加入新類型的新表單，則還必須將定型資料集上傳至適當的容器。 然後，在參數化資料表中加入新的資料列，以輸入新文件的位置及其定型資料集。 在 [model_ID] 中輸入 -1 的值，以指出必須針對這些表單來為新的模型定型。 然後，在 ADF 中執行定型管線。 其會從資料表讀取資料、為模型定型，以及覆寫資料表中的模型識別碼。 接著，您可以呼叫評分管線以開始撰寫輸出檔案。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已設定 Azure Data Factory 管線來觸發表單辨識器模型的定型和執行，以將大量的待辦檔案數位化。 接下來，請探索表單辨識器 API，以了解此 API 還有什麼功能。

* [表單辨識器 REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)
