---
title: 連接到 Azure 儲存服務
titleSuffix: Azure Machine Learning
description: 瞭解如何使用資料儲存在使用 Azure 機器學習進行培訓期間安全地連接到 Azure 儲存服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4a2102f442fc176762b7d5d69f7b367a94633ef5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758785"
---
# <a name="connect-to-azure-storage-services"></a>連接到 Azure 儲存服務
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,瞭解如何通過 Azure 機器學習數據儲存連接到 Azure 儲存服務。 數據儲存在與工作區關聯的[密鑰保管庫中](https://azure.microsoft.com/services/key-vault/)儲存連接資訊,如訂閱 ID 和權杖授權,因此您可以安全地存取記憶體,而無需在腳本中硬編碼它們。

可以從[這些 Azure 儲存解決方案](#matrix)創建數據存儲。 對於不受支援的存儲解決方案,以及為了在機器學習實驗期間節省數據出口成本,我們建議您[將數據移動到](#move)支援的 Azure 儲存解決方案。 

## <a name="prerequisites"></a>Prerequisites

您需要：
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試著[Azure 機器學習的免費或付費版本](https://aka.ms/AMLFree)。

- 具有 Azure blob[容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或[Azure 檔案共享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 Azure 儲存帳戶。

- 用於 Python 的[Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或造訪 Azure[機器學習工作室](https://ml.azure.com/)。

- Azure Machine Learning 工作區。
  
  [創建 Azure 機器學習工作區](how-to-manage-workspace.md)或透過 Python SDK 使用現有工作區。 導入`Workspace``Datastore`和 類,並使用`config.json``from_config()`函數 從檔載入訂閱資訊。 預設情況下,這將查找目前目錄中的 JSON 檔,但您也可以指定路徑參數以`from_config(path="your/file/path")`使用 指向該檔。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支援的資料儲存服務型態

數據存儲當前支援將連接資訊儲存到以下矩陣中列出的存儲服務。

| 儲存&nbsp;型態 | 認證&nbsp;類型 | [Azure&nbsp;&nbsp;機器學習工作室](https://ml.azure.com/) | [Azure&nbsp;&nbsp;機器&nbsp;學習 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;&nbsp;機器學習 CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;&nbsp;機器&nbsp;學習 休息 API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;&nbsp;Blob 儲存](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;檔案分享](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;資料&nbsp;儲存&nbsp;第 1 代](https://docs.microsoft.com/azure/data-lake-store/)| 服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;資料&nbsp;儲存&nbsp;第 2 代](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;&nbsp;SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 驗證 <br>服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL 驗證| ✓ | ✓ | ✓ |✓
[MySQL&nbsp;&nbsp;的 Azure&nbsp;資料庫](https://docs.microsoft.com/azure/mysql/overview) | SQL 驗證|  | ✓* | ✓* |✓*
[資料磚&nbsp;檔案&nbsp;系統](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| 不需要驗證 | | ✓** | ✓ ** |✓** 

*MySQL 僅支援導[管 資料傳輸步驟](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)。 <br>
*僅支援導管[資料磚步驟的資料磚](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>儲存體指引

我們建議為[Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)創建數據存儲。 標準和進階儲存都可用於 Blob。 儘管高級存儲成本更高,但其更快的輸送量速度可能會提高訓練運行速度,尤其是在針對大型數據集進行訓練時。 有關儲存帳戶成本的資訊,請參閱[Azure 定價計算器](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure 資料湖儲存 Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json)構建在 Azure Blob 存儲之上,專為企業大數據分析而設計。 Data Lake Storage Gen2 的基礎部分是新增至 Blob 儲存體的[階層命名空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 階層命名空間會將物件/檔案組織成目錄階層，讓資料存取更有效率。

創建工作區時,Azure Blob 容器和 Azure 檔共享將自動註冊到工作區。 它們分別被命名`workspaceblobstore`與`workspacefilestore`。 `workspaceblobstore`用於存儲工作區工件和機器學習實驗日誌。 `workspacefilestore`用於存儲通過[計算實例](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)授權的筆記本和 R 腳本。 容器`workspaceblobstore`設定為默認數據存儲。

> [!IMPORTANT]
> 在設計器主頁中打開範例時,Azure 機器學習設計器(預覽版)將自動創建名為**azureml_globaldatasets**的數據存儲。 此資料存儲僅包含示例數據集。 **請不要**使用此數據存儲進行任何機密數據訪問。
> ![自動建立設計器範例資料集的資料儲存](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>建立與註冊資料儲存

將 Azure 儲存解決方案註冊為數據儲存時,將自動創建該資料存儲並將其註冊到特定工作區。 可以使用 Python SDK 或 Azure 機器學習工作室創建數據存儲並將其註冊到工作區。

>[!IMPORTANT]
> 作為初始數據存儲創建和註冊過程的一部分,Azure 機器學習驗證基礎存儲服務是否存在,並且使用者提供的委託(使用者名、服務主體或 SAS 權杖)是否有權存取該存儲。 但是,對於 Azure 數據存儲第 1 代和第 2 代數據存儲,[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py)此[`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)驗證將在以後調用 數據 訪問方法時進行。 
<br><br>
建立資料儲存後,僅對需要存取基礎儲存容器的方法執行此驗證,**而不是**每次檢索資料儲存物件。 例如,如果要從數據存儲下載檔,則進行驗證;但是,如果您只想更改默認數據存儲,則不會發生驗證。

### <a name="python-sdk"></a>Python SDK

所有寄存器方法都在類上[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py),並且具有窗體`register_azure_*`。

您可以在 Azure`register()`[門戶](https://portal.azure.com)上找到填充方法所需的資訊。
選擇左側窗格中的**存儲帳戶**,然後選擇要註冊的存儲帳戶。 **"概述"** 頁提供帳戶名稱、容器和檔共用名稱等資訊。 

* 對於認證認證(如帳號金鑰或 SAS 權杖),轉到 **「設定」** 窗格上的 **「存取金鑰**」 。 

* 對於服務主體專案(如租戶ID和用戶端ID),請轉到**應用註冊**並選擇要使用的應用。 其相應的 **「概述」** 頁將包含這些專案。

> [!IMPORTANT]
> 如果您的儲存帳戶位於虛擬網路中,則僅支援**透過 SDK**創建資料儲存。

以下範例展示如何將 Azure Blob 容器、Azure 檔共享和 Azure 資料儲存第 2 代作為資料儲存註冊。 有關其他儲存服務,請參閱[適用`register_azure_*`方法的參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)。

#### <a name="blob-container"></a>Blob 容器

要將 Azure Blob 容器註冊為[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)資料儲存, 請使用 。

以下代碼創建`blob_datastore_name`資料存儲並將註冊到`ws`工作區。 此資料存儲通過使用提供的帳戶存取`my-container-name`密鑰存取儲存`my-account-name`帳戶 上的Blob容器。

```Python
blob_datastore_name='azblobsdk' # Name of the datastore to workspace
container_name=os.getenv("BLOB_CONTAINER", "<my-container-name>") # Name of Azure blob container
account_name=os.getenv("BLOB_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("BLOB_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

blob_datastore = Datastore.register_azure_blob_container(workspace=ws, 
                                                         datastore_name=blob_datastore_name, 
                                                         container_name=container_name, 
                                                         account_name=account_name,
                                                         account_key=account_key)
```
如果 Blob 容器位於虛擬網路中,`skip_validation=True`[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)請使用設定。

#### <a name="file-share"></a>檔案共用

要將 Azure 檔案分享註冊為資料儲存,[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)請使用 。 

以下代碼創建`file_datastore_name`資料存儲並將註冊到`ws`工作區。 此資料存儲通過使用提供的帳戶存取`my-fileshare-name`密鑰存取儲存`my-account-name`帳戶 上的檔共用。

```Python
file_datastore_name='azfilesharesdk' # Name of the datastore to workspace
file_share_name=os.getenv("FILE_SHARE_CONTAINER", "<my-fileshare-name>") # Name of Azure file share container
account_name=os.getenv("FILE_SHARE_ACCOUNTNAME", "<my-account-name>") # Storage account name
account_key=os.getenv("FILE_SHARE_ACCOUNT_KEY", "<my-account-key>") # Storage account access key

file_datastore = Datastore.register_azure_file_share(workspace=ws,
                                                     datastore_name=file_datastore_name, 
                                                     file_share_name=file_share_name, 
                                                     account_name=account_name,
                                                     account_key=account_key)
```
如果文件共用在虛擬網路中,請使用`skip_validation=True`[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)設定。 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure 資料儲存第 2 代

對於 Azure 資料湖儲存第 2 代 (ADLS 第 2 代) 資料儲存,請使用[register_azure_data_lake_gen2()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-)註冊連線到 Azure DataLake 第 2 代儲存的認證資料儲存,並具有[服務主體許可權](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。 為了利用服務主體,您需要[註冊應用程式](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals),並授予服務主體正確的數據訪問許可權。 詳細瞭解為[ADLS 第 2 代設定的存取控制](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

以下代碼創建`adlsgen2_datastore_name`資料存儲並將註冊到`ws`工作區。 此資料存儲通過使用提供的服務主體憑據存取`test`存儲 帳戶`account_name`上的檔案系統。

```python 
adlsgen2_datastore_name = 'adlsgen2datastore'

subscription_id=os.getenv("ADL_SUBSCRIPTION", "<my_subscription_id>") # subscription id of ADLS account
resource_group=os.getenv("ADL_RESOURCE_GROUP", "<my_resource_group>") # resource group of ADLS account

account_name=os.getenv("ADLSGEN2_ACCOUNTNAME", "<my_account_name>") # ADLS Gen2 account name
tenant_id=os.getenv("ADLSGEN2_TENANT", "<my_tenant_id>") # tenant id of service principal
client_id=os.getenv("ADLSGEN2_CLIENTID", "<my_client_id>") # client id of service principal
client_secret=os.getenv("ADLSGEN2_CLIENT_SECRET", "<my_client_secret>") # the secret of service principal

adlsgen2_datastore = Datastore.register_azure_data_lake_gen2(workspace=ws,
                                                             datastore_name=adlsgen2_datastore_name,
                                                             account_name=account_name, # ADLS Gen2 account name
                                                             filesystem='test', # ADLS Gen2 filesystem
                                                             tenant_id=tenant_id, # tenant id of service principal
                                                             client_id=client_id, # client id of service principal
                                                             client_secret=client_secret) # the secret of service principal
```

### <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio 

在 Azure 機器學習工作室中,通過幾個步驟創建新的數據存儲:

> [!IMPORTANT]
> 如果您的儲存帳戶位於虛擬網路中,則僅支援[透過 SDK](#python-sdk)創建資料儲存。 

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。
1. 在 **'管理**' 的欄位中, 將選擇左邊窗格上的**資料儲存**。
1. 選取 [+ 新增資料存放區]  。
1. 填寫新數據存儲的表單。 表單根據 Azure 儲存類型和身份驗證類型的選擇自行更新。
  
您可以在[Azure 門戶](https://portal.azure.com)上找到填充窗體所需的資訊。 選擇左側窗格中的**存儲帳戶**,然後選擇要註冊的存儲帳戶。 **"概述"** 頁提供帳戶名稱、容器和檔共用名稱等資訊。 

* 對於認證認證(如帳號金鑰或 SAS 權杖),轉到 **「設定」** 窗格上的 **「存取金鑰**」 。 

* 對於服務主體專案(如租戶ID和用戶端ID),請轉到**應用註冊**並選擇要使用的應用。 其相應的 **「概述」** 頁將包含這些專案。 

下面的範例展示建立 Azure Blob 資料儲存時表單外觀: 
    
![新資料儲存的表單](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>從工作區取得資料儲存

要在當前工作區中註冊特定數據存儲,請使用 類[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)`Datastore`上的 靜態方法:

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
要取得在給定工作區中註冊的資料儲存清單,可以在工作區物件上使用[`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)屬性 :

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

要取得工作區的預設資料儲存,請使用此行:

```Python
datastore = ws.get_default_datastore()
```

<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>上傳及下載資料

以下[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)示例[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)中 介紹的 和 方法特定於[AzureBlob 數據存儲](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)類,並且操作相同。

### <a name="upload"></a>上傳

使用 Python SDK 將目錄或單一檔案載到資料儲存:

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

參數`target_path`指定要上載的檔案共享(或 blob 容器)中的位置。 它預設為`None`,因此數據上載到 root。 如果`overwrite=True`,上`target_path`的任何現有數據將被覆蓋。

還可以通過方法`upload_files()`將單個檔的清單上載到數據存儲。

### <a name="download"></a>下載

將資料從資料儲存下載到本地端檔案系統:

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

參數`target_path`是要將資料下載到的本地目錄的位置。 若要指定檔案共用 (或 Blob 容器) 中資料夾的路徑以進行下載，請在 `prefix` 中提供該路徑。 如果是`prefix``None`,將下載檔共用(或 blob 容器)的所有內容。

<a name="train"></a>

## <a name="access-your-data-during-training"></a>在訓練期間存取您的資料

要與資料儲存中的資料進行互動,或將資料打包到機器學習工作的可消耗物件(如培訓)時,[請建立 Azure 機器學習資料集](how-to-create-register-datasets.md)。 數據集提供將表格數據載入到貓熊或Spark資料幀中的函數。 數據集還提供從 Azure Blob 儲存、Azure 檔、Azure 資料儲存第 1 代、Azure 資料儲存第 2 代、Azure SQL 資料庫和 PostgreSQL Azure 資料庫下載或裝載任何格式的檔案的功能。 [詳細瞭解如何使用資料集進行訓練](how-to-train-with-datasets.md)。

### <a name="accessing-source-code-during-training"></a>在訓練期間存取原始碼

Azure Blob 儲存的輸送量比 Azure 檔共用高,並將擴展到並行啟動的大量作業。 因此,我們建議將運行配置為使用 Blob 儲存來傳輸原始程式碼檔。

以下代碼示例在運行配置中指定用於原始程式碼傳輸的 Blob 資料儲存。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>評分期間存取資料

Azure 機器學習提供了幾種使用模型進行評分的方法。 其中一些方法不提供對數據存儲的訪問。 使用下表瞭解哪些方法允許您在評分期間存取資料儲存:

| 方法 | 資料儲存存取 | 描述 |
| ----- | :-----: | ----- |
| [批次預測](how-to-use-parallel-run-step.md) | ✔ | 以非同步的方式對大量資料進行預測。 |
| [Web 服務](how-to-deploy-and-where.md) | &nbsp; | 將模型部署為 Web 服務。 |
| [Azure IoT 邊緣模組](how-to-deploy-and-where.md) | &nbsp; | 將模型部署到IoT邊緣設備。 |

對於 SDK 不提供對資料儲存的存取的情況,您可能能夠使用相關的 Azure SDK 來存取資料來創建自訂代碼。 例如[,Python 的 Azure 儲存 SDK](https://github.com/Azure/azure-storage-python)是用戶端庫,可用於存取記憶體在 blob 或檔案中的數據。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>將資料移至受支援的 Azure 儲存解決方案

Azure 機器學習支援從 Azure Blob 儲存、Azure 檔、Azure 資料儲存第 1 代、Azure 資料儲存第 2 代、Azure SQL 資料庫和用於 PostgreSQL 的 Azure 資料庫存取資料。 如果使用不支援的存儲,我們建議您使用[Azure 數據工廠和這些步驟](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)將數據移動到受支援的 Azure 儲存解決方案。 將數據移至支援的儲存可説明您在機器學習實驗期間節省資料出口成本。 

Azure 資料工廠提供高效且具有彈性的數據傳輸,具有 80 多個預建構連接器,無需額外費用。 這些連接器包括 Azure 數據服務、本地數據來源、Amazon S3 和 Redshift 以及 Google BigQuery。

## <a name="next-steps"></a>後續步驟

* [建立 Azure 機器學習資料集](how-to-create-register-datasets.md)
* [將模型定型](how-to-train-ml-models.md)
* [部署模型](how-to-deploy-and-where.md)
