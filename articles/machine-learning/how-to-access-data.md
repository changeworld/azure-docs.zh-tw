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
ms.date: 07/22/2020
ms.custom: how-to, contperfq1, devx-track-python
ms.openlocfilehash: 639e4cde82fd8496c90f8ebf263dd42661a75748
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296802"
---
# <a name="connect-to-azure-storage-services"></a>連線至 Azure 儲存體服務

在本文中，您將瞭解如何透過 **Azure Machine Learning 資料存放區連線到 Azure 儲存體服務**。 資料存放區會安全地連線到您的 Azure 儲存體服務，而不會將您的驗證認證與原始資料來源的完整性保持在風險之下。 他們會將連接資訊（例如您的訂用帳戶識別碼和權杖授權）儲存在與工作區相關聯的 [Key Vault](https://azure.microsoft.com/services/key-vault/) 中，以便您可以安全地存取儲存體，而不需要在腳本中進行硬程式碼撰寫。 您可以使用 [Azure Machine Learning PYTHON SDK](#python) 或 [Azure Machine Learning studio](how-to-connect-data-ui.md) 來建立和註冊資料存放區。

如果您想要使用 Azure Machine Learning VS Code 擴充功能來建立和管理資料存放區;如需深入瞭解，請造訪 [VS Code 資源管理操作指南](how-to-manage-resources-vscode.md#datastores) 。

您可以從[這些 Azure 儲存體解決方案](#matrix)建立資料存放區。 **針對不支援的儲存體解決方案**，以及在 ML 實驗期間儲存資料輸出成本，請 [將您的資料移](#move) 至支援的 Azure 儲存體解決方案。  

若要了解如何將資料存放區納入 Azure Machine Learning 的整體資料存取工作流程中，請參閱[安全地存取資料](concept-data.md#data-workflow)一文。

## <a name="prerequisites"></a>Prerequisites

您需要：
- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

- 具有 [支援之儲存體類型](#matrix)的 Azure 儲存體帳戶。

- [適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)，或 [Azure Machine Learning Studio](https://ml.azure.com/) 的存取權。

- Azure Machine Learning 工作區。
  
  [建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)，或透過 Python SDK 使用現有的工作區。 

    匯入 `Workspace` 和 `Datastore` 類別，並使用 `from_config()` 函式從檔案 `config.json` 載入您的訂用帳戶資訊。 根據預設，此功能會在目前的目錄中尋找 JSON 檔案，但您也可以使用 `from_config(path="your/file/path")` 指定路徑參數來指向檔案。

   ```Python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

    當您建立工作區時，Azure blob 容器和 Azure 檔案共用會自動註冊為工作區的資料存放區。 這兩者分別會命名為 `workspaceblobstore` 和 `workspacefilestore`。 `workspaceblobstore`會用來儲存工作區成品和您的機器學習實驗記錄。 它也會設定為 **預設資料** 存放區，而且無法從工作區刪除。 `workspacefilestore`是用來儲存透過[計算實例](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance#accessing-files)授權的筆記本和 R 腳本。
    
    > [!NOTE]
    > 當您在設計工具首頁中開啟範例時，Azure Machine Learning 設計工具將會自動建立名為 **azureml_globaldatasets** 的資料存放區。 此資料存放區僅包含範例資料集。 請**不要**將此資料存放區用於任何機密資料存取。

<a name="matrix"></a>

## <a name="supported-data-storage-service-types"></a>支援的資料儲存體服務類型

資料存放區目前支援將連線資訊儲存至下列矩陣中所列的儲存體服務。

| 儲存體類型&nbsp; | 驗證類型&nbsp; | [Azure&nbsp;Machine&nbsp;Learning Studio](https://ml.azure.com/) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) |  [Azure&nbsp;Machine&nbsp;Learning CLI](reference-azure-machine-learning-cli.md) | [Azure&nbsp;Machine&nbsp;Learning&nbsp; Rest API](https://docs.microsoft.com/rest/api/azureml/) | VS 程式碼
---|---|---|---|---|---|---
[Azure&nbsp;Blob&nbsp;儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓ |✓
[Azure&nbsp;檔案共用](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)&nbsp;| 帳戶金鑰 <br> SAS 權杖 | ✓ | ✓ | ✓ |✓|✓
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;1](https://docs.microsoft.com/azure/data-lake-store/)| 服務主體| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;Data Lake&nbsp;Storage Gen&nbsp;2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)| 服務主體| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;SQL&nbsp;Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)| SQL 驗證 <br>服務主體| ✓ | ✓ | ✓ |✓|
[Azure&nbsp;PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview) | SQL 驗證| ✓ | ✓ | ✓ |✓|
[適用於&nbsp;MySQL&nbsp;的 Azure&nbsp;資料庫](https://docs.microsoft.com/azure/mysql/overview) | SQL 驗證|  | ✓* | ✓* |✓*|
[Databricks&nbsp;檔案系統](https://docs.microsoft.com/azure/databricks/data/databricks-file-system)&nbsp;| 不需要驗證 | | ✓** | ✓ ** |✓** |

\*只有管線[DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py&preserve-view=true)支援 MySQL<br />
\*\*只有管線[DatabricksStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricks_step.databricksstep?view=azure-ml-py&preserve-view=true)支援 Databricks


### <a name="storage-guidance"></a>儲存體指引

建議您為 [Azure Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)建立資料存放區。 標準和進階儲存體都適用於 Blob。 雖然進階儲存體的成本較高，但其較快速的輸送量速度可能會加快定型執行的速度，特別是當您對大型資料集進行定型時。 如需儲存體帳戶成本的相關資訊，請參閱 [Azure 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=machine-learning-service)。

[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction?toc=/azure/storage/blobs/toc.json) 建置於 Azure Blob 儲存體之上，是專為企業巨量資料分析而設計的。 Data Lake Storage Gen2 的基礎部分是新增至 Blob 儲存體的[階層命名空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 階層命名空間會將物件/檔案組織成目錄階層，讓資料存取更有效率。

## <a name="storage-access-and-permissions"></a>儲存體存取和許可權

若要確保您安全地連線到您的 Azure 儲存體服務，Azure Machine Learning 要求您有權存取對應的資料儲存體容器。 此存取權取決於用來註冊資料存放區的驗證認證。 

### <a name="virtual-network"></a>虛擬網路 

如果您的資料儲存體帳戶位於 **虛擬網路**中，則需要額外的設定步驟，以確保 Azure Machine Learning 可以存取您的資料。 請參閱 [在 Azure 虛擬網路中使用 Azure Machine Learning studio](how-to-enable-studio-virtual-network.md) ，以確保當您建立和註冊資料存放區時，會套用適當的設定步驟。  

### <a name="access-validation"></a>存取驗證

**在初始資料存放區建立和註冊**程式中，Azure Machine Learning 會自動驗證基礎儲存體服務是否存在，以及使用者提供的主體 (使用者名稱、服務主體或 SAS 權杖，) 可以存取指定的儲存體。

**建立資料**存放區之後，只會針對需要存取基礎儲存體容器的方法執行這項驗證， **而不** 是每次抓取資料存放區物件。 例如，如果您想要從資料存放區下載檔案，則會進行驗證；如果您只是要變更預設資料存放區，則不會進行驗證。

若要驗證您對基礎儲存體服務的存取權，您可以在想要建立的資料存放區類型的對應方法中，提供帳戶金鑰、共用存取簽章 (SAS) 權杖或服務主體 `register_azure_*()` 。 [儲存體類型矩陣](#matrix)會列出對應至每個資料存放區類型的支援驗證類型。

您可以在 [Azure 入口網站](https://portal.azure.com)上找到帳戶金鑰、SAS 權杖和服務主體資訊。

* 如果您打算使用帳戶金鑰或 SAS 權杖進行驗證，請在左窗格中選取 [儲存體帳戶]，然後選擇您要註冊的儲存體帳戶。 
  * [概觀] 頁面會提供帳戶名稱、容器和檔案共用名稱等資訊。 
      1. 針對帳戶金鑰，請移至 [設定] 窗格上的 [存取金鑰]。 
      1. 針對 SAS 權杖，請移至 [設定] 窗格上的 [共用存取簽章]。

* 如果您打算使用服務主體進行驗證，請移至您的 **應用程式註冊** ，然後選取您要使用的應用程式。 
    * 其對應的 **總覽** 頁面將包含必要的資訊，例如租使用者識別碼和用戶端識別碼。

> [!IMPORTANT]
> 基於安全性理由，您可能需要變更 Azure 儲存體帳戶的存取金鑰 (帳戶金鑰或 SAS 權杖) 。 當您這樣做時，請務必將新的認證與您的工作區和連線的資料存放區同步。 瞭解如何 [同步處理已更新的認證](how-to-change-storage-access-key.md)。 

### <a name="permissions"></a>權限

針對 Azure blob 容器和 Azure Data Lake Gen 2 儲存體，請確定您的驗證認證具有 **儲存體 Blob 資料讀取器** 存取權。 深入瞭解 [儲存體 Blob 資料讀取器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)。 帳戶 SAS 權杖預設為沒有許可權。 針對資料讀取權限，您的驗證認證必須具有容器和物件的最小清單和讀取權限。 若要進行資料寫入存取，也需要 [寫入] 和 [新增] 許可權。

<a name="python"></a>

## <a name="create-and-register-datastores"></a>建立和註冊資料存放區

當您將 Azure 儲存體解決方案註冊為資料存放區時，將會自動建立該資料存放區，並將其註冊至特定工作區。 請參閱 [儲存體存取 & 許可權](#storage-access-and-permissions) 一節，以取得虛擬網路案例的指引，以及尋找所需驗證認證的位置。 

本節中的範例將說明如何透過 Python SDK 針對下列儲存體類型建立和註冊資料存放區。 這些範例中提供的參數，是建立和註冊資料存放區時**所需的參數**。

* [Azure Blob 容器](#azure-blob-container)
* [Azure 檔案共用](#azure-file-share)
* [Azure Data Lake Storage Generation 2](#azure-data-lake-storage-generation-2)

 若要為其他支援的儲存體服務建立資料存放區，請參閱 [適用 `register_azure_*` 方法的參考檔](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=truemethods)。

如果您偏好低程式碼體驗，請參閱 [使用 Azure Machine Learning Studio 連接到資料](how-to-connect-data-ui.md)。

> [!NOTE]
> 資料存放區名稱只可包含小寫字母、數位和底線。 

### <a name="azure-blob-container"></a>Azure Blob 容器

若要將 Azure Blob 容器註冊作為資料存放區，請使用 [`register_azure_blob_container()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-)。

下列程式碼會建立 `blob_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用提供的帳戶存取金鑰，存取 `my-account-name` 儲存體帳戶上的 `my-container-name` Blob 容器。 請參閱 [儲存體存取 & 許可權](#storage-access-and-permissions) 一節，以取得虛擬網路案例的指引，以及尋找所需驗證認證的位置。 

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

### <a name="azure-file-share"></a>Azure 檔案共用

若要將 Azure 檔案共用註冊為資料存放區，請使用 [`register_azure_file_share()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-file-share-workspace--datastore-name--file-share-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false-)。 

下列程式碼會建立 `file_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用提供的帳戶存取金鑰，存取 `my-account-name` 儲存體帳戶上的 `my-fileshare-name` 檔案共用。 請參閱 [儲存體存取 & 許可權](#storage-access-and-permissions) 一節，以取得虛擬網路案例的指引，以及尋找所需驗證認證的位置。 

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

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage Generation 2

針對 Azure Data Lake Storage Generation 2 (ADLS Gen 2) 資料存放區，請使用 [register_azure_data_lake_gen2 ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore.datastore?view=azure-ml-py&preserve-view=true#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) 透過[服務主體權限](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)註冊連線至 Azure DataLake Gen 2 儲存體的認證資料存放區。  

若要使用您的服務主體，您必須 [註冊您的應用程式](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) ，並將服務主體授與 **儲存體 Blob 資料讀取器** 存取權。 深入了解 [ADLS Gen 2 的存取控制設定](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 

下列程式碼會建立 `adlsgen2_datastore_name` 資料存放區，並將其註冊至 `ws` 工作區。 此資料存放區會使用提供的服務主體認證，存取 `account_name` 儲存體帳戶中的檔案系統 `test`。 請參閱 [儲存體存取 & 許可權](#storage-access-and-permissions) 一節，以取得虛擬網路案例的指引，以及尋找所需驗證認證的位置。 

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

<a name="arm"></a>

## <a name="create-datastores-using-azure-resource-manager"></a>使用 Azure Resource Manager 建立資料存放區

有一些範本 [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-datastore-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) 可用於建立資料存放區。

如需使用這些範本的詳細資訊，請參閱 [使用 Azure Resource Manager 範本建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)。

<a name="train"></a>
## <a name="use-data-in-your-datastores"></a>使用資料存放區中的資料

建立資料存放區之後，請 [建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md) 來與您的資料互動。 資料集會將您的資料封裝成機器學習工作的延遲評估取用物件（例如定型）。 它們也提供從 Azure 儲存體服務（例如 Azure Blob 儲存體和 ADLS Gen 2） [下載或掛接](how-to-train-with-datasets.md#mount-vs-download) 任何格式檔案的功能。 您也可以使用它們來將表格式資料載入至 pandas 或 Spark 資料框架。

<a name="get"></a>

## <a name="get-datastores-from-your-workspace"></a>從您的工作區取得資料存放區

若要取得在目前的工作區中註冊的特定資料存放區，請對 `Datastore` 類別使用 [`get()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=trueget-workspace--datastore-name-) 靜態方法：

```Python
# Get a named datastore from the current workspace
datastore = Datastore.get(ws, datastore_name='your datastore name')
```
若要取得向指定工作區註冊的資料存放區清單，您可以對工作區物件使用 [`datastores`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedatastores) 屬性：

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
