---
title: 連線至 Azure 儲存體服務
titleSuffix: Azure Machine Learning
description: 了解如何在使用 Azure Machine Learning 定型期間使用資料存放區安全地連線至 Azure 儲存體服務
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 904738d73aaa0580773a085c70cd74f4240fc4b7
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773928"
---
# <a name="connect-to-azure-storage-services"></a>連線至 Azure 儲存體服務
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將了解如何透過 Azure Machine Learning 資料存放區連線至 Azure 儲存體服務。 資料存放區會儲存連線資訊，就像與工作區相關聯的 [Key Vault](https://azure.microsoft.com/services/key-vault/) 中的訂用帳戶識別碼和權杖授權一樣，因此您可以安全地存取儲存體，而無須在指令碼中對其進行硬式編碼。 若要了解如何將資料存放區納入 Azure Machine Learning 的整體資料存取工作流程中，請參閱[安全地存取資料](concept-data.md#data-workflow)一文。

您可以從[這些 Azure 儲存體解決方案](#matrix)建立資料存放區。 如果您使用不受支援的儲存體解決方案，以及要在機器學習實驗期間儲存資料輸出成本，建議您[將資料移至](#move)支援的 Azure 儲存體解決方案。 

## <a name="prerequisites"></a>Prerequisites

您需要：
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 具有 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)或 [Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)的 Azure 儲存體帳戶。

- [適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)，或 [Azure Machine Learning Studio](https://ml.azure.com/) 的存取權。

- Azure Machine Learning 工作區。
  
  [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或透過 Python SDK 使用現有的工作區。 匯入 `Workspace` 和 `Datastore` 類別，並使用 `from_config()` 函式從檔案 `config.json` 載入您的訂用帳戶資訊。 根據預設，此功能會在目前的目錄中尋找 JSON 檔案，但您也可以使用 `from_config(path="your/file/path")` 指定路徑參數來指向檔案。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```
<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支援的資料儲存體服務類型

資料存放區目前支援將連線資訊儲存至下列矩陣中所列的儲存體服務。

| 儲存體類型&nbsp; | 驗證類型&nbsp; | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/)
---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)&nbsp;| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| 服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;SQL&nbsp;Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 驗證 <br>服務主體| ✓ | ✓ | ✓ |✓
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL 驗證| ✓ | ✓ | ✓ |✓
[適用於&nbsp;MySQL&nbsp;的 Azure&nbsp;資料庫](https://docs.microsoft.com/azure/mysql/overview) | SQL 驗證|  | ✓* | ✓* |✓*
[Databricks&nbsp;檔案系統](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)&nbsp;| 不需要驗證 | | ✓** | ✓ ** |✓** 

*只有管線 [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) 支援 MySQL。 <br>
**只有管線 [DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py) 支援 Databricks

### <a name="storage-guidance"></a>儲存體指引

建議您為 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)建立資料存放區。 標準和進階儲存體都適用於 Blob。 雖然進階儲存體的成本較高，但其較快速的輸送量速度可能會加快定型執行的速度，特別是當您對大型資料集進行定型時。 如需儲存體帳戶成本的相關資訊，請參閱 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) 建置於 Azure Blob 儲存體之上，是專為企業巨量資料分析而設計的。 Data Lake Storage Gen2 的基礎部分是新增至 Blob 儲存體的[階層命名空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 階層命名空間會將物件/檔案組織成目錄階層，讓資料存取更有效率。

當您建立工作區時，將會自動向該工作區註冊 Azure Blob 容器和 Azure 檔案共用。 這兩者分別會命名為 `workspaceblobstore` 和 `workspacefilestore`。 `workspaceblobstore` 用來儲存工作區成品和您的機器學習實驗記錄。 `workspacefilestore` 則用來儲存透過[計算執行個體](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)授權的筆記本和 R 指令碼。 `workspaceblobstore` 容器會設定為預設資料存放區。

> [!IMPORTANT]
> 當您在設計工具首頁中開啟範例時，Azure Machine Learning 設計工具 (預覽) 會自動建立名為 **azureml_globaldatasets** 的資料存放區。 此資料存放區僅包含範例資料集。 請**不要**將此資料存放區用於任何機密資料存取。
> ![為設計工具範例資料集自動建立的資料存放區](media/how-to-access-data/datastore-designer-sample.png)

<a name="access"></a>

## <a name="create-and-register-datastores"></a>建立和註冊資料存放區

當您將 Azure 儲存體解決方案註冊為資料存放區時，將會自動建立該資料存放區，並將其註冊至特定工作區。 您可以使用 [Python SDK](#python-sdk) 或 [Azure Machine Learning Studio](#azure-machine-learning-studio) 建立資料存放區，並將其註冊至工作區。

>[!IMPORTANT]
> 在執行初始資料存放區建立和註冊的過程中，Azure Machine Learning 會驗證基礎儲存體服務確實存在，且使用者提供的主體 (使用者名稱、服務主體或 SAS 權杖) 具有該儲存體的存取權。 但對於 Azure Data Lake Storage Gen 1 和 2 資料存放區，此驗證將會在後續呼叫 [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py) 或 [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-parquet-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) 之類的資料存取方法時進行。 
<br><br>
資料存放區建立後，將只會對需要存取基礎儲存體容器的方法執行這項驗證，而**不會**在每次擷取資料存放區物件時執行。 例如，如果您想要從資料存放區下載檔案，則會進行驗證；如果您只是要變更預設資料存放區，則不會進行驗證。

### <a name="python-sdk"></a>Python SDK

所有註冊方法都用於 [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) 類別上，且形式為 `register_azure_*`。
> [!IMPORTANT]
> 如果您的儲存體帳戶位於虛擬網路中，則僅支援**透過 SDK** 建立資料存放區 。

您可以在 [Azure 入口網站](https://portal.azure.com)上找到填入 `register_azure_*()` 方法所需的資訊。

* 如果您打算使用帳戶金鑰或 SAS 權杖進行驗證，請在左窗格中選取 [儲存體帳戶]，然後選擇您要註冊的儲存體帳戶。 
  * [概觀] 頁面會提供帳戶名稱、容器和檔案共用名稱等資訊。 
      1. 針對帳戶金鑰，請移至 [設定] 窗格上的 [存取金鑰]。 
      1. 針對 SAS 權杖，請移至 [設定] 窗格上的 [共用存取簽章]。

* 如果您打算使用服務主體進行驗證，請移至您的**應用程式註冊**，並選取您要使用的應用程式。 
    * 其對應的 [概觀] 頁面將會包含租用戶識別碼和用戶端識別碼之類的必要資訊。

下列範例說明如何註冊 Azure Blob 容器、Azure 檔案共用，以及 Azure Data Lake Storage Generation 2 作為資料存放區。 這些範例中提供的參數，是建立和註冊資料存放區時**所需的參數**。 

若要為其他儲存體服務建立資料存放區，以及查看這些方法的選擇性參數，請參閱[適用 `register_azure_*` 方法的參考文件](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#methods)。

#### <a name="blob-container"></a>Blob 容器

若要將 Azure Blob 容器註冊作為資料存放區，請使用 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

下列程式碼會建立 `blob_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用提供的帳戶存取金鑰，存取 `my-account-name` 儲存體帳戶上的 `my-container-name` Blob 容器。

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
如果您的 Blob 容器位於虛擬網路中，請在 [`register_azure_blob-container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) 方法中包含參數 `skip_validation=True`。 

#### <a name="file-share"></a>檔案共用

若要將 Azure 檔案共用註冊為資料存放區，請使用 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

下列程式碼會建立 `file_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用提供的帳戶存取金鑰，存取 `my-account-name` 儲存體帳戶上的 `my-fileshare-name` 檔案共用。

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
如果您的檔案共用位於虛擬網路中，請在 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#register-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-) 方法中包含參數 `skip_validation=True`。 

#### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

針對 Azure Data Lake Storage Generation 2 (ADLS Gen 2) 資料存放區，請使用 [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 透過[服務主體權限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)註冊連線至 Azure DataLake Gen 2 儲存體的認證資料存放區。 若要使用您的服務主體，您必須[註冊應用程式](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，並且為服務主體授與*儲存體 Blob 資料讀取器*存取權。 深入了解 [ADLS Gen 2 的存取控制設定](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

若要利用您的服務主體，您必須[註冊應用程式](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)，並且為服務主體授與正確的資料存取權。 深入了解 [ADLS Gen 2 的存取控制設定](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

下列程式碼會建立 `adlsgen2_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用提供的服務主體認證，存取 `account_name` 儲存體帳戶中的檔案系統 `test`。

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

在 Azure Machine Learning Studio 中只需幾個步驟即可建立新的資料存放區：

> [!IMPORTANT]
> 如果您的儲存體帳戶位於虛擬網路中，則僅支援[透過 SDK](#python-sdk) 建立資料存放區 。 

1. 登入 [Azure Machine Learning Studio](https://ml.azure.com/)。
1. 在左窗格中，選取 [管理] 底下的 [資料存放區]。
1. 選取 [+ 新增資料存放區]。
1. 完成新資料存放區的表單。 此表單會根據您選取的 Azure 儲存體類型和驗證類型，以智慧方式自行更新。
  
您可以在 [Azure 入口網站](https://portal.azure.com)上找到填入表單所需的資訊。 在左窗格中選取 [儲存體帳戶]，然後選擇您要註冊的儲存體帳戶。 [概觀] 頁面會提供帳戶名稱、容器和檔案共用名稱等資訊。 

* 針對驗證項目 (如帳戶金鑰或 SAS 權杖)，請移至 [設定] 窗格上的 [存取金鑰]。 

* 針對租用戶識別碼和用戶端識別碼之類的服務主體項目，請移至您的**應用程式註冊**，並選取您要使用的應用程式。 其對應的 [概觀] 頁面會包含這些項目。 

下列範例示範表單在您建立 Azure Blob 資料存放區時所呈現的樣貌： 
    
![新資料存放區的表單](media/how-to-access-data/new-datastore-form.png)


<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>從您的工作區取得資料存放區

若要取得在目前的工作區中註冊的特定資料存放區，請對 `Datastore` 類別使用 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#get-workspace--datastore-name-) 靜態方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要取得向指定工作區註冊的資料存放區清單，您可以對工作區物件使用 [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py#datastores) 屬性：

```Python
# List all datastores registered in the current workspace
datastores = ws.datastores
for name, datastore in datastores.items():
    print(name, datastore.datastore_type)
```

若要取得工作區的預設資料存放區，請使用下列程式碼行：

```Python
datastore = ws.get_default_datastore()
```
您也可以使用下列程式碼來變更預設資料存放區。 此功能只能透過 SDK 來執行。 

```Python
 ws.set_default_datastore(new_default_datastore)
```
<a name="up-and-down"></a>
## <a name="upload-and-download-data"></a>上傳及下載資料

下列範例中說明的 [`upload()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#upload-src-dir--target-path-none--overwrite-false--show-progress-true-) 和 [`download()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py#download-target-path--prefix-none--overwrite-false--show-progress-true-) 方法僅供 [AzureBlobDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azureblobdatastore?view=azure-ml-py) 和 [AzureFileDatastore](https://docs.microsoft.com/python/api/azureml-core/azureml.data.azure_storage_datastore.azurefiledatastore?view=azure-ml-py) 類別專用，且兩者的運作方式相同。

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

`target_path` 參數可指定檔案共用 (或 Blob 容器) 中的位置以進行上傳。 其預設值為 `None`，因此資料會上傳至根目錄。 如果 `overwrite=True`，則會覆寫 `target_path` 上的任何現有資料。

您也可以透過 `upload_files()` 方法，將個別檔案清單上傳至資料存放區。

### <a name="download"></a>下載

將資料從資料存放區下載至本機檔案系統：

```Python
datastore.download(target_path='your target path',
                   prefix='your prefix',
                   show_progress=True)
```

`target_path` 參數是資料下載到的本機目錄位置。 若要指定檔案共用 (或 Blob 容器) 中資料夾的路徑以進行下載，請在 `prefix` 中提供該路徑。 如果 `prefix` 為 `None`，表示會下載檔案共用 (或 Blob 容器) 的所有內容。

<a name="train"></a>

## <a name="access-your-data-during-training"></a>在定型期間存取您的資料

若要與資料存放區中的資料互動，或將您的資料封裝到機器學習工作 (例如定型) 可取用的物件中，請[建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)。 資料集提供將表格式資料載入 Pandas 或 Spark DataFrame 中的功能。 資料集也能讓您從 Azure Blob 儲存體、Azure 檔案儲存體、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database 和適用於 PostgreSQL 的 Azure 資料庫下載或掛接任何格式的檔案。 [深入了解如何使用資料集進行定型](how-to-train-with-datasets.md)。

### <a name="accessing-source-code-during-training"></a>在定型期間存取原始程式碼

Azure Blob 儲存體的輸送量速度高於 Azure 檔案共用，且會調整為以平行方式啟動的大量作業。 因此，建議您將執行設定為使用 Blob 儲存體來傳輸原始程式碼檔案。

下列程式碼範例會在執行設定中指定要用於原始程式碼傳輸的 Blob 資料存放區。

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="access-data-during-scoring"></a>在評分期間存取資料

Azure Machine Learning 提供了數種方式讓您使用模型進行評分。 其中某些方法不會提供資料存放區的存取。 請使用下表了解哪些方法可讓您在評分期間存取資料存放區：

| 方法 | 資料存放區存取 | 描述 |
| ----- | :-----: | ----- |
| [批次預測](how-to-use-parallel-run-step.md) | ✔ | 以非同步的方式對大量資料進行預測。 |
| [Web 服務](how-to-deploy-and-where.md) | &nbsp; | 將模型部署為 Web 服務。 |
| [Azure IoT Edge 模組](how-to-deploy-and-where.md) | &nbsp; | 將模型部署至 IoT Edge 裝置。 |

如果 SDK 未提供資料存放區的存取，您可以使用相關 Azure SDK 建立自訂程式碼，用以存取資料。 例如，[適用於 Python 的 Azure 儲存體 SDK](https://github.com/Azure/azure-storage-python) 是用戶端程式庫，可讓您用來存取儲存在 Blob 或檔案中的資料。

<a name="move"></a>

## <a name="move-data-to-supported-azure-storage-solutions"></a>將資料移至支援的 Azure 儲存體解決方案

Azure Machine Learning 支援從 Azure Blob 儲存體、Azure Files、Azure Data Lake Storage Gen1、Azure Data Lake Storage Gen2、Azure SQL Database，以及適用於 PostgreSQL 的 Azure 資料庫存取資料。 如果您使用不受支援的儲存體，建議您使用 [Azure Data Factory 和這些步驟](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-copy-data-tool)，將您的資料移至支援的 Azure 儲存體解決方案。 將資料移至支援的儲存體，可協助您節省機器學習實驗期間的資料輸出成本。 

Azure Data Factory 免費提供超過 80 個預先建置的連接器，可讓您有效率且彈性地進行資料傳輸。 這些連接器包括 Azure 資料服務、內部部署資料來源、Amazon S3 和 Redshift，以及 Google BigQuery。

## <a name="next-steps"></a>後續步驟

* [建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)
* [將模型定型](how-to-train-ml-models.md)
* [部署模型](how-to-deploy-and-where.md)
