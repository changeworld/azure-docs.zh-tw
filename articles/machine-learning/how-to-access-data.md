---
title: 連接到 Azure 儲存體服務
titleSuffix: Azure Machine Learning
description: 瞭解如何使用資料存放區安全地連線到 Azure 儲存體服務，並使用 Azure Machine Learning 進行定型
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: f5a7605a1fa68c3a600c77ded762722990d7a514
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231198"
---
# <a name="connect-to-azure-storage-services"></a>連接到 Azure 儲存體服務
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何透過 Azure Machine Learning 資料存放區來連線到 Azure 儲存體服務。 資料存放區會在與工作區相關聯的[Key Vault](https://azure.microsoft.com/services/key-vault/)中儲存連接資訊，例如您的訂用帳戶識別碼和權杖授權，讓您可以安全地存取您的儲存體，而不需要在腳本中硬式編碼它們。 若要瞭解資料存放區如何納入 Azure Machine Learning 的整體資料存取工作流程，請參閱[安全存取資料](concept-data.md#data-workflow)一文。

您可以從[這些 Azure 儲存體解決方案](#matrix)建立資料存放區。 針對不支援的儲存體解決方案，以及在機器學習實驗期間儲存資料輸出成本，建議您[將資料移](#move)至支援的 Azure 儲存體解決方案。 

## <a name="prerequisites"></a>Prerequisites

您需要：
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 具有[azure blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或[azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 azure 儲存體帳戶。

- [適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或[Azure Machine Learning studio](https://ml.azure.com/)的存取權。

- Azure Machine Learning 工作區。
  
  請[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或透過 Python SDK 使用現有的工作區。 匯入`Workspace`和`Datastore`類別，並`config.json`使用函式從檔案載入您的訂用`from_config()`帳戶資訊。 根據預設，這會在目前的目錄中尋找 JSON 檔案，但您也可以使用`from_config(path="your/file/path")`來指定 path 參數，以指向該檔案。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支援的資料儲存體服務類型

資料存放區目前支援將連接資訊儲存至下列矩陣中所列的儲存體服務。

| 儲存&nbsp;類型 | 驗證&nbsp;類型 | [Azure&nbsp;Machine&nbsp;Learning studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;MACHINE&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;檔案&nbsp;共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;儲存體 Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| 服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;儲存體 Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;SQL&nbsp;Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 驗證 <br>服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;于 postgresql](https://docs.microsoft.com/azure/postgresql/overview) | SQL 驗證| ✓ | ✓ | ✓ |✓
[適用&nbsp;于&nbsp;MySQL&nbsp;的 Azure 資料庫](https://docs.microsoft.com/azure/mysql/overview) | SQL 驗證|  | ✓* | ✓* |✓*
[Databricks&nbsp;檔&nbsp;系統](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)| 不需要驗證 | | ✓** | ✓ ** |✓** 

* 只有管線[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py)支援 MySQL。 <br>
* * Databricks 僅支援管線[DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py)

### <a name="storage-guidance"></a>儲存體指引

我們建議您建立[Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)的資料存放區。 Standard 和 premium 儲存體都適用于 blob。 雖然高階儲存體的成本較高，但其更快速的輸送量速度可能會改善定型執行的速度，特別是當您針對大型資料集進行定型時。 如需儲存體帳戶成本的相關資訊，請參閱[Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json)建置於 Azure Blob 儲存體之上，並專為企業海量資料分析所設計。 Data Lake Storage Gen2 的基礎部分是新增至 Blob 儲存體的[階層命名空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 階層命名空間會將物件/檔案組織成目錄階層，讓資料存取更有效率。

當您建立工作區時，會自動向工作區註冊 Azure blob 容器和 Azure 檔案共用。 它們分別命名`workspaceblobstore`為`workspacefilestore`和。 `workspaceblobstore`用來儲存工作區成品和您的機器學習實驗記錄。 `workspacefilestore`是用來儲存透過[計算實例](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)授權的筆記本和 R 腳本。 `workspaceblobstore`容器會設定為預設資料存放區。

> [!IMPORTANT]
> 當您在設計工具首頁中開啟範例時，Azure Machine Learning 設計工具（預覽）將會自動建立名為**azureml_globaldatasets**的資料存放區。 此資料存放區僅包含範例資料集。 請**不要使用此**資料存放區進行任何機密資料存取。
> ![設計工具範例資料集的自動建立資料存放區](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>建立並註冊資料存放區

當您將 Azure 儲存體解決方案註冊為數據存放區時，您會自動建立該資料存放區，並將其註冊至特定工作區。 您可以使用[PYTHON SDK](#python-sdk)或[Azure Machine Learning studio](#azure-machine-learning-studio)建立資料存放區，並將其註冊至工作區。

>[!IMPORTANT]
> 在初始資料存放區建立和註冊程式的過程中，Azure Machine Learning 會驗證基礎儲存體服務是否存在，以及使用者提供的主體（使用者名稱、服務主體或 SAS 權杖）是否具有該存放裝置的存取權。 不過，針對 Azure Data Lake Storage Gen 1 和2資料存放區，在稍後呼叫或[`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-)之類的資料存取方法時，就會進行這種驗證。 
<br><br>
建立資料存放區之後，只會針對需要存取基礎儲存體容器的方法執行這項驗證，而**不**是每次抓取資料存放區物件。 例如，如果您想要從資料存放區下載檔案，則會進行驗證;但是，如果您只想要變更預設資料存放區，則不會進行驗證。

### <a name="python-sdk"></a>Python SDK

所有的暫存器方法都在[`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py)類別上，並具有`register_azure_*`表單。
> [!IMPORTANT]
> 如果您的儲存體帳戶位於虛擬網路中，則只支援透過**SDK**建立資料存放區。

您可以在`register_azure_*()` [Azure 入口網站](https://portal.azure.com)上找到填入方法所需的資訊。

* 如果您打算使用帳戶金鑰或 SAS 權杖進行驗證，請在左窗格中選取 [**儲存體帳戶**]，然後選擇您要註冊的儲存體帳戶。 
  * [**總覽**] 頁面會提供帳戶名稱、容器和檔案共用名稱等資訊。 
      1. 針對 [帳戶金鑰]，移至 [**設定**] 窗格上的 [**存取金鑰**]。 
      1. 針對 SAS 權杖，請移至 [**設定**] 窗格上的 [**共用存取**簽章]。

* 如果您打算使用服務主體進行驗證，請移至您的**應用程式註冊**，然後選取您要使用的應用程式。 
    * 其對應的 **[總覽**] 頁面將包含 [租使用者識別碼] 和 [用戶端識別碼] 等必要資訊

下列範例示範如何註冊 Azure blob 容器、Azure 檔案共用，以及 Azure Data Lake Storage 層代2作為資料存放區。 這些範例中提供的參數是建立和註冊資料存放區**所需的參數**。 

若要建立其他儲存體服務的資料存放區，並查看這些方法的選擇性參數，請參閱[適用`register_azure_*`方法的參考檔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)。

#### <a name="blob-container"></a>Blob 容器

若要將 Azure blob 容器註冊為數據存放區[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)，請使用。

下列程式碼會建立資料存放`blob_datastore_name` `ws`區，並將其註冊到工作區。 此資料`my-account-name`存放區`my-container-name`會使用所提供的帳戶存取金鑰來存取儲存體帳戶上的 blob 容器。

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
如果您的 blob 容器位於虛擬網路中，請在`skip_validation=True`您[`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)的方法中包含參數。 

#### <a name="file-share"></a>檔案共用

若要將 Azure 檔案共用註冊為數據存放區[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)，請使用。 

下列程式碼會建立資料存放`file_datastore_name` `ws`區，並將其註冊到工作區。 此資料`my-account-name`存放區`my-fileshare-name`會使用所提供的帳戶存取金鑰來存取儲存體帳戶上的檔案共用。

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
如果您的檔案共用位於虛擬網路中，請在`skip_validation=True`您[`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)的方法中包含參數。 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage 層代2

若為 Azure Data Lake Storage 層代2（ADLS Gen 2）資料存放區，請使用[register_azure_data_lake_gen2 （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-)來註冊與 Azure DataLake Gen 2 儲存體（具有[服務主體許可權](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)）連線的認證資料存放區。 若要利用您的服務主體，您必須[註冊您的應用程式](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，並將*儲存體 Blob 資料擁有*者存取權授與服務主體。 深入瞭解[ADLS Gen 2 的存取控制設定](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

若要利用您的服務主體，您必須[註冊您的應用程式](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，並使用正確的資料存取權來授與服務主體。 深入瞭解[ADLS Gen 2 的存取控制設定](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

下列程式碼會建立資料存放`adlsgen2_datastore_name` `ws`區，並將其註冊到工作區。 此資料`account_name`存放區會使用`test`所提供的服務主體認證來存取儲存體帳戶中的檔案系統。

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

在 Azure Machine Learning studio 中的幾個步驟中建立新的資料存放區：

> [!IMPORTANT]
> 如果您的儲存體帳戶位於虛擬網路中，則只支援透過[SDK](#python-sdk)建立資料存放區。 

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。
1. 在左窗格的 [**管理**] 底下選取 [**資料存放區**]。
1. 選取 [+ 新增資料存放區]  。
1. 完成新資料存放區的表單。 表單會根據您選取的 Azure 儲存體類型和驗證類型，以智慧方式自行更新。
  
您可以在 [ [Azure 入口網站](https://portal.azure.com)上找到填入表單所需的資訊。 在左窗格中選取 [**儲存體帳戶**]，然後選擇您要註冊的儲存體帳戶。 [**總覽**] 頁面會提供帳戶名稱、容器和檔案共用名稱等資訊。 

* 如需驗證專案（如帳戶金鑰或 SAS 權杖），請移至 [**設定**] 窗格上的 [**存取金鑰**]。 

* 針對 [租使用者識別碼] 和 [用戶端識別碼] 等服務主體專案，移至您的**應用程式註冊**，然後選取您要使用的應用程式。 其對應的 **[總覽**] 頁面將包含這些專案。 

下列範例示範當您建立 Azure blob 資料存放區時，表單看起來的樣子： 
    
![新資料存放區的表單](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>從您的工作區取得資料存放區

若要取得在目前工作區中註冊的特定資料存放[`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-)區，請在`Datastore`類別上使用靜態方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要取得向指定工作區註冊的資料存放區清單，您可以使用工作[`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores)區物件上的屬性：

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

若要取得工作區的預設資料存放區，請使用以下這一行：

```Python
datastore = ws.get_default_datastore()
```
您也可以使用下列程式碼來變更預設資料存放區。 只有透過 SDK 才支援這種功能。 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>上傳及下載資料

下列[`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-)範例[`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-)中所描述的和方法，是針對[AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py)和[AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py)類別所特有的，且其運作方式相同。

> [!NOTE]
> 目前不支援上傳至 AzureDataLakeGen2 資料存放區。

### <a name="upload"></a>上傳

使用 Python SDK 將目錄或個別檔案上傳至資料存放區：

```Python
datastore.upload(src_dir='your source directory',
                 target_path='your target path',
                 overwrite=True,
                 show_progress=True)
```

`target_path`參數會指定要上傳之檔案共用（或 blob 容器）中的位置。 預設為`None`，因此資料會上傳到根。 如果`overwrite=True` `target_path`為，則會覆寫中的任何現有資料。

您也可以透過`upload_files()`方法，將個別檔案清單上傳至資料存放區。

### <a name="download"></a>下載

將資料從資料存放區下載到本機檔案系統：

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path`參數是要將資料下載到其中的本機目錄位置。 若要指定檔案共用 (或 Blob 容器) 中資料夾的路徑以進行下載，請在 `prefix` 中提供該路徑。 如果`prefix`為`None`，則會下載檔案共用（或 blob 容器）的所有內容。

<a name="train"></a>

## <a name="access-your-data-during-training"></a>在定型期間存取您的資料

若要與資料存放區中的資料互動，或將您的資料封裝到機器學習工作的可取用物件（例如訓練），請[建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。 資料集提供將表格式資料載入 pandas 或 Spark 資料框架的功能。 資料集也能讓您從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫下載或掛接任何格式的檔案。 [深入瞭解如何使用資料集進行定型](how-to-train-with-datasets.md)。

### <a name="accessing-source-code-during-training"></a>在定型期間存取原始程式碼

Azure Blob 儲存體的輸送量速度高於 Azure 檔案共用，而且會調整為以平行方式啟動的大量作業。 基於這個理由，我們建議您將執行設定為使用 Blob 儲存體來傳送原始程式碼檔案。

下列程式碼範例會在執行設定中指定要用於來來源程式代碼傳輸的 blob 資料存放區。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在評分期間存取資料

Azure Machine Learning 提供數種方式來使用您的模型進行評分。 其中一些方法不會提供資料存放區的存取權。 使用下表瞭解哪些方法可讓您在計分期間存取資料存放區：

| 方法 | 資料存放區存取 | 描述 |
| ----- | :-----: | ----- |
| [批次預測](how-to-use-parallel-run-step.md) | ✔ | 以非同步的方式對大量資料進行預測。 |
| [Web 服務](how-to-deploy-and-where.md) | &nbsp; | 將模型部署為 web 服務。 |
| [Azure IoT Edge 模組](how-to-deploy-and-where.md) | &nbsp; | 將模型部署到 IoT Edge 裝置。 |

針對 SDK 不提供資料存放區存取權的情況，您可以使用相關的 Azure SDK 來存取資料，以建立自訂程式碼。 例如，適用于[Python 的 AZURE 儲存體 SDK](https://github.com/Azure/azure-storage-python)是用戶端程式庫，可讓您用來存取儲存在 blob 或檔案中的資料。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>將資料移至支援的 Azure 儲存體解決方案

Azure Machine Learning 支援從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫存取資料。 如果您使用不支援的儲存體，建議您使用[Azure Data Factory 和下列步驟](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)，將您的資料移至支援的 Azure 儲存體解決方案。 將資料移至支援的儲存體，可協助您節省機器學習實驗期間的資料輸出成本。 

Azure Data Factory 提供具有超過80個預先建立連接器的有效率且彈性的資料傳輸，且不需額外費用。 這些連接器包括 Azure 資料服務、內部部署資料來源、Amazon S3 和 Redshift，以及 Google BigQuery。

## <a name="next-steps"></a>後續步驟

* [建立 Azure machine learning 資料集](how-to-create-register-datasets.md)
* [定型模型](how-to-train-ml-models.md)
* [部署模型](how-to-deploy-and-where.md)
