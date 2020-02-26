---
title: 企業安全性
titleSuffix: Azure Machine Learning
description: 安全地使用 Azure Machine Learning：驗證、授權、網路安全性、資料加密和監視。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/09/2020
ms.openlocfilehash: 7b6bd33346df9496c4c30353b68c11bdd7fad7a2
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486388"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure Machine Learning 的企業安全性

在本文中，您將瞭解 Azure Machine Learning 可用的安全性功能。

當您使用雲端服務時，最佳做法是限制只有需要它的使用者才能存取。 一開始先瞭解服務所使用的驗證和授權模型。 您也可能想要限制網路存取，或安全地將內部部署網路中的資源與雲端聯結。 資料加密也很重要，不論是待用，還是資料在服務之間移動。 最後，您必須能夠監視服務，並產生所有活動的審核記錄。

> [!NOTE]
> 本文中的資訊適用于 Azure Machine Learning Python SDK 1.0.83.1 或更高版本。

## <a name="authentication"></a>驗證

如果 Azure Active Directory （Azure AD）設定為使用多重要素驗證，則支援。 以下是驗證程式：

1. 用戶端登入以 Azure AD 並取得 Azure Resource Manager token。  完全支援使用者和服務主體。
1. 用戶端會呈現權杖，以 Azure Resource Manager 和所有 Azure Machine Learning。
1. Machine Learning 服務會為使用者計算目標提供 Machine Learning 的服務權杖（例如，Machine Learning Compute）。 在執行完成之後，使用者計算目標會使用此權杖來回呼 Machine Learning 服務。 範圍僅限於工作區。

[Azure Machine Learning 中的 ![驗證](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

如需詳細資訊，請參閱[設定 Azure Machine Learning 資源和工作流程的驗證](how-to-setup-authentication.md)。 本文提供驗證的資訊和範例，包括使用服務主體和自動化工作流程。


### <a name="authentication-for-web-service-deployment"></a>Web 服務部署的驗證

Azure Machine Learning 針對 web 服務支援兩種形式的驗證：金鑰和權杖。 每個 web 服務一次只能啟用一種形式的驗證。

|驗證方法|描述|Azure Container Instances|AKS|
|---|---|---|---|
|Key|金鑰是靜態的，不需要重新整理。 可以手動重新產生金鑰。|預設為停用| 預設啟用|
|Token|權杖會在指定的時間週期後過期，而且需要重新整理。| 無法使用| 預設為停用 |

如需程式碼範例，請參閱[web 服務驗證一節](how-to-setup-authentication.md#web-service-authentication)。

## <a name="authorization"></a>授權

您可以建立多個工作區，而且每個工作區都可由多人共用。 當您共用工作區時，您可以藉由將這些角色指派給使用者來控制其存取權：

* 擁有者
* 參與者
* 讀取者

下表列出一些主要 Azure Machine Learning 作業，以及可以執行它們的角色：

| Azure Machine Learning 作業 | 擁有者 | 參與者 | 讀取者 |
| ---- |:----:|:----:|:----:|
| 建立工作區 | ✓ | ✓ | |
| 共用工作區 | ✓ | |  |
| 將工作區升級至 Enterprise edition | ✓ | |
| 建立計算目標 | ✓ | ✓ | |
| 附加計算目標 | ✓ | ✓ | |
| 附加資料存放區 | ✓ | ✓ | |
| 執行實驗 | ✓ | ✓ | |
| 視圖執行/計量 | ✓ | ✓ | ✓ |
| 註冊模型 | ✓ | ✓ | |
| 建立映射 | ✓ | ✓ | |
| 部署 Web 服務 | ✓ | ✓ | |
| 視圖模型/影像 | ✓ | ✓ | ✓ |
| 呼叫 web 服務 | ✓ | ✓ | ✓ |

如果內建角色不符合您的需求，您可以建立自訂角色。 只有工作區上的作業和 Machine Learning Compute 支援自訂角色。 自訂角色可以具有工作區的讀取、寫入或刪除許可權，以及該工作區中的計算資源。 您可以在特定工作區層級、特定的資源群組層級或特定的訂用帳戶層級上，讓角色可供使用。 如需詳細資訊，請參閱[管理 Azure Machine Learning 工作區中的使用者和角色](how-to-assign-roles.md)。

### <a name="securing-compute-targets-and-data"></a>保護計算目標和資料

擁有者和參與者可以使用附加至工作區的所有計算目標和資料存放區。  

每個工作區也有相關聯的系統指派受控識別，其名稱與工作區相同。 受控識別在工作區中使用的附加資源上具有下列許可權。

如需受控識別的詳細資訊，請參閱[適用于 Azure 資源的受控](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)識別。

| 資源 | 權限 |
| ----- | ----- |
| 工作區 | 參與者 |
| 儲存體帳戶 | 儲存體 Blob 資料參與者 |
| 金鑰保存庫 | 存取所有金鑰、秘密、憑證 |
| Azure Container Registry | 參與者 |
| 包含工作區的資源群組 | 參與者 |
| 包含金鑰保存庫的資源群組（如果與包含工作區的不同） | 參與者 |

我們不建議系統管理員撤銷上表所述資源的受控識別存取權。 您可以使用 [重新同步金鑰] 作業來還原存取權。

Azure Machine Learning 會針對每個工作區區域，在您的訂用帳戶中，建立具有參與者層級存取權的其他應用程式（名稱開頭為 `aml-` 或 `Microsoft-AzureML-Support-App-`）。 例如，如果您在美國東部有一個工作區，而在同一個訂用帳戶中有一個在北歐中，您會看到這兩個應用程式。 這些應用程式可讓 Azure Machine Learning 協助您管理計算資源。

## <a name="network-security"></a>網路安全性

Azure Machine Learning 依賴其他 Azure 服務來計算資源。 計算資源 (計算目標) 用於定型和部署模型。 您可以在虛擬網路中建立這些計算目標。 例如，您可以使用 Azure 資料科學虛擬機器來定型模型，然後將模型部署至 AKS。  

如需詳細資訊，請參閱[如何在虛擬網路中執行實驗和推斷](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>資料加密

### <a name="encryption-at-rest"></a>待用加密

> [!IMPORTANT]
> 如果您的工作區包含機密資料，建議您在建立工作區時設定[hbi_workspace 旗](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)標。 這會控制 Microsoft 針對診斷目的收集的資料量，並在 Microsoft 管理的環境中啟用額外的加密。

如需 Azure 中待用加密運作方式的詳細資訊，請參閱[azure 待用資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

#### <a name="azure-blob-storage"></a>Azure Blob 儲存體

Azure Machine Learning 會將快照、輸出和記錄儲存在與 Azure Machine Learning 工作區和您的訂用帳戶相關聯的 Azure Blob 儲存體帳戶中。 Azure Blob 儲存體中儲存的所有資料都會使用 Microsoft 管理的金鑰進行待用加密。

如需如何針對儲存在 Azure Blob 儲存體中的資料使用您自己的金鑰的相關資訊，請參閱[Azure Key Vault 中的 Azure 儲存體使用客戶管理的金鑰加密](../storage/common/storage-encryption-keys-portal.md)。

定型資料通常也會儲存在 Azure Blob 儲存體中，以供定型計算目標。 此儲存體不受 Azure Machine Learning 管理，而是裝載至計算目標做為遠端檔案系統。

如需重新產生存取金鑰的詳細資訊，請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning 會在 Azure Cosmos DB 實例中儲存計量和中繼資料。 這個實例與 Azure Machine Learning 所管理的 Microsoft 訂用帳戶相關聯。 Azure Cosmos DB 中儲存的所有資料都會使用 Microsoft 管理的金鑰進行待用加密。

若要使用您自己（客戶管理的）金鑰來加密 Azure Cosmos DB 實例，您可以建立專用的 Cosmos DB 實例以搭配您的工作區使用。 如果您想要將資料（例如執行歷程記錄資訊）儲存在 Microsoft 訂用帳戶所裝載的多租使用者 Cosmos DB 實例之外，我們建議採用這種方法。 

> [!NOTE]
> 這項功能目前僅適用于美國東部、美國西部2、美國中南部。

若要使用客戶管理的金鑰在您的訂用帳戶中啟用 Cosmos DB 實例，請執行下列動作：

* 啟用客戶管理的金鑰功能以進行 Cosmos DB。 此時，您必須要求存取權才能使用這項功能。 若要這麼做，請洽詢[cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)。

* 在您的訂用帳戶中註冊 Azure Machine Learning 和 Azure Cosmos DB 資源提供者（如果尚未這麼做）。

* 使用訂用帳戶的參與者許可權，授權 Machine Learning 應用程式（在身分識別和存取管理中）。

    ![在入口網站中授權「身分識別和存取管理」中的「Azure Machine Learning 應用程式」](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* 建立 Azure Machine Learning 工作區時，請使用下列參數。 這兩個參數都是必要的，而且在 SDK、CLI、REST Api 和 Resource Manager 範本中都有支援。

    * `resource_cmk_uri`：此參數是金鑰保存庫中客戶管理的金鑰的完整資源 URI，包括金鑰的[版本資訊](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)。 

    * `cmk_keyvault`：此參數是您訂用帳戶中金鑰保存庫的資源識別碼。 此金鑰保存庫必須位於您將用於 Azure Machine Learning 工作區的相同地區和訂用帳戶中。 
    
        > [!NOTE]
        > 此金鑰保存庫實例可以與您布建工作區時 Azure Machine Learning 所建立的金鑰保存庫不同。 如果您想要針對工作區使用相同的金鑰保存庫實例，請在使用[key_vault 參數](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)布建工作區時，傳遞相同的金鑰保存庫。 

此 Cosmos DB 實例是在您訂用帳戶中由 Microsoft 管理的資源群組中建立。 

> [!IMPORTANT]
> * 如果您需要刪除此 Cosmos DB 實例，您必須刪除使用它的 Azure Machine Learning 工作區。 
> * 此 Cosmos DB 帳戶的預設[__要求單位__](../cosmos-db/request-units.md)設定為__8000__。 不支援變更此值。 

如需 Cosmos DB 的客戶管理金鑰的詳細資訊，請參閱[為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰](../cosmos-db/how-to-setup-cmk.md)。

#### <a name="azure-container-registry"></a>Azure Container Registry

登錄（Azure Container Registry）中的所有容器映射都會在待用時加密。 Azure 會在儲存映射之前自動將它加密，並在 Azure Machine Learning 提取映射時將它解密。

若要使用您自己（客戶管理的）金鑰來加密您的 Azure Container Registry，您必須建立自己的 ACR，並在布建工作區時加以附加，或加密在布建工作區時所建立的預設實例。

如需使用現有 Azure Container Registry 建立工作區的範例，請參閱下列文章：

* [使用 Azure CLI 建立 Azure Machine Learning 的工作區](how-to-manage-workspace-cli.md)。
* [使用 Azure Resource Manager 範本來建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure 容器執行個體

Azure 容器實例不支援磁片加密。 如果您需要磁片加密，建議您改[為部署至 Azure Kubernetes Service 實例](how-to-deploy-azure-kubernetes-service.md)。 在此情況下，您可能也會想要使用 Azure Machine Learning 的角色型存取控制支援，以防止在您的訂用帳戶中部署至 Azure 容器實例。

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

您可以隨時使用客戶管理的金鑰來加密已部署的 Azure Kubernetes Service 資源。 如需詳細資訊，請參閱[使用 Azure Kubernetes Service 整合您自己的金鑰](../aks/azure-disk-customer-managed-keys.md)。 

此程式可讓您對 Kubernetes 叢集中已部署之虛擬機器的資料和 OS 磁片進行加密。

> [!IMPORTANT]
> 此程式僅適用于 AKS K8s 1.17 版或更高版本。 Azure Machine Learning 已在2020年1月13日新增 AKS 1.17 的支援。

#### <a name="machine-learning-compute"></a>Machine Learning Compute

儲存在 Azure 儲存體中的每個計算節點的 OS 磁片都會使用 Azure Machine Learning 儲存體帳戶中 Microsoft 管理的金鑰進行加密。 此計算目標是暫時的，而且叢集通常會在沒有任何執行排入佇列時相應減少。 基礎虛擬機器已解除布建，且作業系統磁片已刪除。 OS 磁片不支援 Azure 磁碟加密。

每部虛擬機器也會有本機暫存磁片供 OS 作業使用。 如果您想要的話，可以使用磁片來暫存定型資料。 根據預設，如果工作區的 [`hbi_workspace`] 參數設定為 [`TRUE`]，則磁片會進行加密。 此環境只有在執行期間才會短暫存留，而且加密支援僅限於系統管理的金鑰。

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks 可以在 Azure Machine Learning 管線中使用。 根據預設，Azure Databricks 使用的 Databricks 檔案系統（DBFS）會使用 Microsoft 管理的金鑰進行加密。 若要設定 Azure Databricks 使用客戶管理的金鑰，請參閱[在預設（root） DBFS 設定客戶管理的金鑰](/azure/databricks/security/customer-managed-keys-dbfs)。

### <a name="encryption-in-transit"></a>傳輸中加密

您可以使用 SSL 來保護 Azure Machine Learning 微服務之間的內部通訊，以及保護對評分端點的外部呼叫。 所有的 Azure 儲存體存取也會透過安全通道進行。

如需詳細資訊，請參閱[使用 SSL 透過 Azure Machine Learning 保護 web 服務](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure Machine Learning 使用與工作區相關聯的 Azure Key Vault 實例來儲存各種類型的認證：

* 相關聯的儲存體帳戶連接字串
* Azure 容器存放庫實例的密碼
* 資料存放區的連接字串

SSH 密碼和計算目標（例如 Azure HDInsight 和 Vm）的金鑰會儲存在與 Microsoft 訂用帳戶相關聯的個別金鑰保存庫中。 Azure Machine Learning 不會儲存使用者所提供的任何密碼或金鑰。 相反地，它會產生、授權及儲存自己的 SSH 金鑰，以連線到 Vm 和 HDInsight 來執行實驗。

每個工作區都有相關聯的系統指派受控識別，其名稱與工作區相同。 此受控識別可存取金鑰保存庫中的所有金鑰、秘密和憑證。

## <a name="data-collection-and-handling"></a>資料收集和處理

### <a name="microsoft-collected-data"></a>Microsoft 收集的資料

Microsoft 可能會收集非使用者識別資訊，例如資源名稱（如資料集名稱或機器學習實驗名稱），或作業環境變數以供診斷之用。 所有這類資料都是使用 microsoft 所擁有的訂用帳戶儲存在儲存體中，並遵循[microsoft 的標準隱私權原則和資料處理標準](https://privacy.microsoft.com/privacystatement)來儲存。

Microsoft 也建議您不要將敏感資訊（例如帳戶金鑰秘密）儲存在環境變數中。 我們會記錄、加密及儲存環境變數。 同樣地，在命名[runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)時，請避免包含敏感性資訊，例如使用者名稱或密碼專案名稱。 這項資訊可能會出現在 Microsoft 支援服務工程師可存取的遙測記錄中。

在布建工作區時，您可以將 `hbi_workspace` 參數設定為 `TRUE`，以選擇不要收集診斷資料。 使用 AzureML Python SDK、CLI、REST Api 或 Azure Resource Manager 範本時，支援此功能。

### <a name="microsoft-generated-data"></a>Microsoft 產生的資料

使用自動化 Machine Learning 這類服務時，Microsoft 可能會產生暫時性、預先處理的資料來定型多個模型。 這項資料會儲存在您工作區中的資料存放區，讓您能夠適當地強制執行存取控制和加密。

您可能也會想要將[從部署的端點記錄的診斷資訊](how-to-enable-app-insights.md)加密到 Azure 應用程式 Insights 實例。

## <a name="monitoring"></a>監視

### <a name="metrics"></a>度量

您可以使用 Azure 監視器計量來查看和監視 Azure Machine Learning 工作區的計量。 在  [Azure 入口網站](https://portal.azure.com)中，選取您的工作區，然後選取 **計量**]：

[![螢幕擷取畫面，其中顯示工作區的範例計量](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

計量包含有關執行、部署和註冊的資訊。

如需詳細資訊，請參閱[Azure 監視器中的計量](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活動記錄檔

您可以查看工作區的活動記錄，以查看工作區上執行的各種作業。 此記錄檔包含基本資訊，例如作業名稱、事件起始端和時間戳記。

此螢幕擷取畫面顯示工作區的活動記錄：

[![螢幕擷取畫面，其中顯示工作區的活動記錄](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

評分要求詳細資料會儲存在 Application Insights 中。 當您建立工作區時，會在您的訂用帳戶中建立 Application Insights。 記錄的資訊包括下欄欄位：

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure Machine Learning 工作區中的某些動作不會將資訊記錄到活動記錄中。 例如，不會記錄定型執行的開始和模型的註冊。
>
> 其中有些動作會出現在工作區的 [**活動**] 區域中，但這些通知不會指出起始活動的人員。

## <a name="data-flow-diagrams"></a>資料流程圖

### <a name="create-workspace"></a>建立工作區

下圖顯示 [建立工作區工作流程]。

* 您可以從其中一個支援的 Azure Machine Learning 用戶端（Azure CLI、Python SDK、Azure 入口網站）登入 Azure AD，並要求適當的 Azure Resource Manager token。
* 您會呼叫 Azure Resource Manager 來建立工作區。 
* Azure Resource Manager 聯絡 Azure Machine Learning 資源提供者以布建工作區。

建立工作區時，會在使用者的訂用帳戶中建立額外的資源：

* Key Vault （用來儲存秘密）
* Azure 儲存體帳戶（包括 blob 和檔案共用）
* Azure Container Registry （儲存 Docker 映射以進行推斷/評分和實驗）
* Application Insights （用來儲存遙測資料）

使用者也可以視需要布建附加至工作區（例如 Azure Kubernetes Service 或 Vm）的其他計算目標。

[![建立工作區工作流程](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>儲存原始碼（訓練腳本）

下圖顯示程式碼快照集工作流程。

與 Azure Machine Learning 工作區相關聯的目錄（實驗）包含原始程式碼（訓練腳本）。 這些腳本會儲存在您的本機電腦和雲端（在您的訂用帳戶的 Azure Blob 儲存體中）。 程式碼快照集會用來執行或檢查歷程記錄。

[![程式碼快照集工作流程](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>訓練

下圖顯示訓練工作流程。

* 會使用上一節中所儲存之程式碼快照集的快照集識別碼來呼叫 Azure Machine Learning。
* Azure Machine Learning 會建立執行識別碼（選擇性）和 Machine Learning 服務權杖，稍後由計算目標（例如 Machine Learning Compute/Vm）用來與 Machine Learning 服務進行通訊。
* 您可以選擇受控計算目標（例如 Machine Learning Compute）或非受控計算目標（例如 Vm）來執行定型作業。 以下是這兩種案例的資料流程：
   * 由 Microsoft 訂用帳戶中的金鑰保存庫中的 SSH 認證存取的 Vm/HDInsight。 Azure Machine Learning 會在計算目標上執行管理程式碼，其：

   1. 準備環境。 （Docker 是 Vm 和本機電腦的選項。 如需瞭解如何在 Docker 容器上執行實驗的運作方式 Machine Learning Compute，請參閱下列步驟。）
   1. 下載程式代碼。
   1. 設定環境變數和配置。
   1. 執行使用者腳本（上一節中所述的程式碼快照集）。

   * Machine Learning Compute，透過工作區管理的身分識別來存取。
因為 Machine Learning Compute 是受管理的計算目標（也就是由 Microsoft 管理），所以它會在您的 Microsoft 訂用帳戶下執行。

   1. 如有需要，會啟動遠端 Docker 結構。
   1. 管理程式碼會寫入使用者的 Azure 檔案儲存體共用。
   1. 使用初始命令啟動容器。 也就是上一個步驟中所述的管理程式碼。

#### <a name="querying-runs-and-metrics"></a>查詢執行和計量

在下面的流程圖中，當定型計算目標將執行計量從 Cosmos DB 資料庫中的儲存體寫回 Azure Machine Learning 時，就會發生此步驟。 用戶端可以呼叫 Azure Machine Learning。 Machine Learning 接著會從 Cosmos DB 資料庫中提取計量，並將其傳回用戶端。

[![訓練工作流程](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>建立 web 服務

下圖顯示推斷工作流程。 推斷或模型計分，是部署的模型用於預測的階段，最常見的是生產資料。

詳細資料如下：

* 使用者會使用 Azure Machine Learning SDK 之類的用戶端來註冊模型。
* 使用者會使用模型、分數檔案和其他模型相依性來建立影像。
* Docker 映射會建立並儲存在 Azure Container Registry 中。
* Web 服務會使用在上一個步驟中建立的映射，部署到計算目標（容器實例/AKS）。
* 評分要求詳細資料會儲存在使用者訂用帳戶中的 Application Insights。
* 遙測也會推送至 Microsoft/Azure 訂用帳戶。

[![推斷工作流程](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>後續步驟

* [使用 SSL 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [使用部署為 web 服務的 Machine Learning 模型](how-to-consume-web-service.md)
* [如何執行批次預測](how-to-use-parallel-run-step.md)
* [使用 Application Insights 監視您的 Azure Machine Learning 模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [搭配 Azure 虛擬網路使用 Azure Machine Learning](how-to-enable-virtual-network.md)
* [建置建議系統的最佳作法](https://github.com/Microsoft/Recommenders)
* [在 Azure 上建置即時建議 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
