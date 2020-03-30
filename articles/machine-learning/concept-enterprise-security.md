---
title: 企業安全性
titleSuffix: Azure Machine Learning
description: 安全地使用 Azure 機器學習：身份驗證、授權、網路安全、資料加密和監視。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 01/09/2020
ms.openlocfilehash: d945540a769f01c33ca3d3e467fe7c983fb5e286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287351"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Azure 機器學習的企業安全性

在本文中，您將瞭解可用於 Azure 機器學習的安全功能。

使用雲服務時，最佳做法是僅將存取權限限制為需要雲服務的使用者。 首先瞭解服務使用的身份驗證和授權模型。 您可能還希望限制網路訪問或與雲安全地將本地網路中的資源聯接在一起。 資料加密也至關重要，無論是在靜態的還是在服務之間移動資料時。 最後，您需要能夠監視服務並生成所有活動的稽核記錄。

> [!NOTE]
> 本文中的資訊適用于 Azure 機器學習 Python SDK 版本 1.0.83.1 或更高版本。

## <a name="authentication"></a>驗證

如果將 Azure 活動目錄 （Azure AD） 配置為使用它，則支援多重要素驗證。 下面是身份驗證過程：

1. 用戶端登錄到 Azure AD 並獲得 Azure 資源管理器權杖。  完全支援使用者和服務主體。
1. 用戶端向 Azure 資源管理器和所有 Azure 機器學習顯示權杖。
1. 機器學習服務為使用者提供機器學習服務權杖（例如，機器學習計算）。 使用者計算目標使用此權杖在運行完成後調用回機器學習服務。 範圍僅限於工作區。

[![Azure 機器學習中的身份驗證](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

有關詳細資訊，請參閱為[Azure 機器學習資源和工作流設置身份驗證](how-to-setup-authentication.md)。 本文提供有關身份驗證的資訊和示例，包括使用服務主體和自動化工作流。

### <a name="authentication-for-web-service-deployment"></a>Web 服務部署的身份驗證

Azure 機器學習支援 Web 服務的兩種身份驗證形式：金鑰和權杖。 每個 Web 服務一次只能啟用一種形式的身份驗證。

|驗證方法|描述|Azure Container Instances|AKS|
|---|---|---|---|
|Key|鍵是靜態的，不需要刷新。 可以手動重新生成金鑰。|預設為停用| 預設已啟用|
|Token|權杖將在指定時間段後過期，需要刷新。| 無法使用| 預設為停用 |

有關代碼示例，請參閱[Web 服務身份驗證部分](how-to-setup-authentication.md#web-service-authentication)。

## <a name="authorization"></a>授權

您可以建立多個工作區，而且每個工作區都可由多人共用。 共用工作區時，可以通過將以下角色指派給使用者來控制對工作區的訪問：

* 擁有者
* 參與者
* 讀取者

下表列出了一些主要的 Azure 機器學習操作以及可以執行這些操作的角色：

| Azure 機器學習操作 | 擁有者 | 參與者 | 讀取者 |
| ---- |:----:|:----:|:----:|
| 建立工作區 | ✓ | ✓ | |
| 共用工作區 | ✓ | |  |
| 將工作區升級到企業版 | ✓ | |
| 創建計算目標 | ✓ | ✓ | |
| 附加計算目標 | ✓ | ✓ | |
| 附加資料存儲 | ✓ | ✓ | |
| 執行實驗 | ✓ | ✓ | |
| 查看運行/指標 | ✓ | ✓ | ✓ |
| 註冊模型 | ✓ | ✓ | |
| 建立映像 | ✓ | ✓ | |
| 部署 Web 服務 | ✓ | ✓ | |
| 查看模型/圖像 | ✓ | ✓ | ✓ |
| 致電 Web 服務 | ✓ | ✓ | ✓ |

如果內置角色不能滿足您的需要，則可以創建自訂角色。 自訂角色僅支援工作區和機器學習計算上的操作。 自訂角色可以對工作區和該工作區中的計算資源具有讀取、寫入或刪除許可權。 您可以在特定工作區級別、特定資源組級別或特定訂閱級別提供該角色。 有關詳細資訊，請參閱在[Azure 機器學習工作區中管理使用者和角色](how-to-assign-roles.md)。

> [!WARNING]
> Azure 活動目錄業務對企業協作當前不支援 Azure 機器學習。

### <a name="securing-compute-targets-and-data"></a>保護計算目標和資料

擁有者和參與者可以使用附加到工作區的所有計算目標和資料存儲。  

每個工作區還具有與工作區同名的關聯的系統分配的託管標識。 託管標識對工作區中使用的附加資源具有以下許可權。

有關託管標識的詳細資訊，請參閱 Azure[資源的託管標識](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

| 資源 | 權限 |
| ----- | ----- |
| 工作區 | 參與者 |
| 儲存體帳戶 | 儲存體 Blob 資料參與者 |
| 金鑰保存庫 | 訪問所有金鑰、機密和證書 |
| Azure Container Registry | 參與者 |
| 包含工作區的資源組 | 參與者 |
| 包含金鑰保存庫的資源組（如果與包含工作區的資源庫不同） | 參與者 |

我們不建議管理員撤銷託管標識對上表中提到的資源的存取權限。 您可以使用重新同步鍵操作恢復存取權限。

Azure 機器學習為每個工作區區域的訂閱中創建具有參與者`aml-`級`Microsoft-AzureML-Support-App-`存取權限的其他應用程式（名稱以 或 開頭）。 例如，如果在美國東部有一個工作區，在北歐有一個工作區在同一訂閱中，您將看到其中兩個應用程式。 這些應用程式使 Azure 機器學習可説明您管理計算資源。

## <a name="network-security"></a>網路安全性

Azure 機器學習依賴于其他 Azure 服務來計算資源。 計算資源 (計算目標) 用於定型和部署模型。 您可以在虛擬網路中創建這些計算目標。 例如，可以使用 Azure 資料科學虛擬機器訓練模型，然後將模型部署到 AKS。  

有關詳細資訊，請參閱[如何在虛擬網路中運行實驗和推理](how-to-enable-virtual-network.md)。

## <a name="data-encryption"></a>資料加密

### <a name="encryption-at-rest"></a>待用加密

> [!IMPORTANT]
> 如果您的工作區包含敏感性資料，我們建議您在創建工作區時設置[hbi_workspace標誌](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)。 這將控制 Microsoft 為診斷目的收集的資料量，並在 Microsoft 託管環境中啟用其他加密。

有關靜態加密在 Azure 中的工作方式的詳細資訊，請參閱[靜態的 Azure 資料加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)。

#### <a name="azure-blob-storage"></a>Azure Blob 儲存體

Azure 機器學習將快照、輸出和日誌存儲在與 Azure 機器學習工作區和訂閱關聯的 Azure Blob 存儲帳戶中。 存儲在 Azure Blob 存儲中的所有資料都使用 Microsoft 管理的金鑰在靜態加密。

有關如何將自己的金鑰用於存儲在 Azure Blob 存儲中的資料的資訊，請參閱[Azure 金鑰保存庫中使用客戶管理的金鑰的 Azure 存儲加密](../storage/common/storage-encryption-keys-portal.md)。

訓練資料通常也存儲在 Azure Blob 存儲中，以便訓練計算目標可以訪問它。 此存儲不是由 Azure 機器學習管理，而是裝載到將目標作為遠端檔案系統進行計算。

有關重新生成訪問金鑰的資訊，請參閱[重新生成存儲訪問金鑰](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure 機器學習在 Azure Cosmos DB 實例中存儲指標和中繼資料。 此實例與 Azure 機器學習管理的 Microsoft 訂閱相關聯。 存儲在 Azure Cosmos DB 中的所有資料都使用 Microsoft 管理的金鑰進行靜態加密。

要使用自己的（客戶管理）金鑰來加密 Azure Cosmos DB 實例，可以創建專用 Cosmos DB 實例，以便與工作區一起使用。 如果要將資料存儲在 Microsoft 訂閱中託管的多租戶 Cosmos DB 實例之外的資料（如執行歷程記錄資訊），我們建議使用此方法。 

要使用客戶管理的金鑰在訂閱中啟用預配 Cosmos DB 實例，請執行以下操作：

* 為 Cosmos DB 啟用客戶管理的關鍵功能。 此時，您必須請求訪問才能使用此功能。 為此，請聯繫[cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)。

* 在訂閱中註冊 Azure 機器學習和 Azure Cosmos 資料庫資來源提供者（如果尚未完成）。

* 授權機器學習應用（在標識和訪問管理中）對訂閱具有參與者許可權。

    ![在門戶中的標識和訪問管理中授權"Azure 機器學習應用"](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* 創建 Azure 機器學習工作區時，請使用以下參數。 這兩個參數都是必需的，在 SDK、CLI、REST API 和資源管理器範本中都有支援。

    * `resource_cmk_uri`：此參數是金鑰保存庫中客戶託管金鑰的完整資源 URI，包括[金鑰的版本資訊](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning)。 

    * `cmk_keyvault`：此參數是訂閱中金鑰保存庫的資源識別碼。 此金鑰保存庫需要位於將用於 Azure 機器學習工作區的同一區域和訂閱中。 
    
        > [!NOTE]
        > 此金鑰保存庫實例可能不同于 Azure 機器學習在預配工作區時創建的金鑰保存庫。 如果要對工作區使用相同的金鑰保存庫實例，請使用[key_vault 參數](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)在預配工作區時傳遞相同的金鑰保存庫。 

此 Cosmos DB 實例是在訂閱中的 Microsoft 託管資源組中創建的。 

> [!IMPORTANT]
> * 如果需要刪除此 Cosmos DB 實例，則必須刪除使用它的 Azure 機器學習工作區。 
> * 此 Cosmos DB 帳戶的預設[__請求單位__](../cosmos-db/request-units.md)設置為__8000__。 不支援更改此值。 

有關使用 Cosmos DB 的客戶管理金鑰的詳細資訊，請參閱[為 Azure Cosmos DB 帳戶配置客戶管理的金鑰](../cosmos-db/how-to-setup-cmk.md)。

#### <a name="azure-container-registry"></a>Azure Container Registry

註冊表（Azure 容器註冊表）中的所有容器映射都靜態加密。 Azure 在存儲映射之前會自動加密映射，並在 Azure 機器學習提取映射時對其進行解密。

要使用自己的（客戶管理）金鑰來加密 Azure 容器註冊表，您需要創建自己的 ACR 並在預配工作區或加密在工作區預配時創建的預設實例時附加它。

有關使用現有 Azure 容器註冊表創建工作區的示例，請參閱以下文章：

* [使用 Azure CLI 為 Azure 機器學習創建工作區](how-to-manage-workspace-cli.md)。
* [使用 Azure 資源管理器範本為 Azure 機器學習創建工作區](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure 容器執行個體

Azure 容器實例不支援磁片加密。 如果需要磁片加密，我們建議改為部署到[Azure 庫伯內斯服務實例](how-to-deploy-azure-kubernetes-service.md)。 在這種情況下，您可能還希望使用 Azure 機器學習對基於角色的訪問控制項的支援，以防止部署到訂閱中的 Azure 容器實例。

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

您可以隨時使用客戶管理的金鑰加密已部署的 Azure Kubernetes 服務資源。 有關詳細資訊，請參閱使用[Azure 庫伯奈斯服務自帶金鑰](../aks/azure-disk-customer-managed-keys.md)。 

此過程允許您加密 Kubernetes 群集中已部署虛擬機器的資料和 OS 磁片。

> [!IMPORTANT]
> 此過程僅適用于 AKS K8s 版本 1.17 或更高版本。 Azure 機器學習在 2020 年 1 月 13 日增加了對 AKS 1.17 的支援。

#### <a name="machine-learning-compute"></a>機器學習計算

存儲在 Azure 存儲中的每個計算節點的 OS 磁片使用 Azure 機器學習存儲帳戶中的 Microsoft 管理金鑰進行加密。 此計算目標是短暫的，並且群集通常在沒有排隊時縮小。 基礎虛擬機器已取消預配，並且 OS 磁片將被刪除。 作業系統磁片不支援 Azure 磁片加密。

每個虛擬機器還有一個用於作業系統操作的本地臨時磁片。 如果需要，可以使用磁片暫用訓練資料。 預設情況下，將參數設置為`hbi_workspace``TRUE`的工作區的磁片進行加密。 此環境僅在運行期間是短暫的，加密支援僅限於系統管理的金鑰。

#### <a name="azure-databricks"></a>Azure Databricks

Azure 資料塊可用於 Azure 機器學習管道。 預設情況下，Azure 資料磚塊使用的資料塊檔案系統 （DBFS） 使用 Microsoft 管理的金鑰進行加密。 要將 Azure 資料塊配置為使用客戶管理的金鑰，請參閱[在預設（根）DBFS 上配置客戶管理的金鑰](/azure/databricks/security/customer-managed-keys-dbfs)。

### <a name="encryption-in-transit"></a>傳輸中加密

可以使用 TLS 來保護 Azure 機器學習微服務之間的內部通信，並保護對評分終結點的外部調用。 所有 Azure 存儲訪問也通過安全通道進行。

有關詳細資訊，請參閱使用[TLS 通過 Azure 機器學習來保護 Web 服務](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure 機器學習使用與工作區關聯的 Azure 金鑰保存庫實例來存儲各種憑據：

* 關聯的存儲帳戶連接字串
* Azure 容器存儲庫實例的密碼
* 與資料存儲的連接字串

SSH 密碼和用於計算目標（如 Azure HDInsight 和 VM）的金鑰存儲在與 Microsoft 訂閱關聯的單獨金鑰保存庫中。 Azure 機器學習不會存儲使用者提供的任何密碼或金鑰。 相反，它生成、授權和存儲自己的 SSH 金鑰，以連接到 VM 和 HDInsight 以運行實驗。

每個工作區都有一個關聯的系統分配的託管標識，該標識的名稱與工作區相同。 此託管標識有權訪問金鑰保存庫中的所有金鑰、機密和證書。

## <a name="data-collection-and-handling"></a>資料收集和處理

### <a name="microsoft-collected-data"></a>微軟收集資料

Microsoft 可能會收集非使用者標識資訊，如資源名稱（例如資料集名稱或機器學習實驗名稱）或用於診斷目的的作業環境變數。 所有這些資料都使用微軟管理的金鑰存儲在微軟擁有的訂閱中託管的存儲中，並遵循[微軟的標準隱私政策和資料處理標準](https://privacy.microsoft.com/privacystatement)。

Microsoft 還建議不要將敏感資訊（如帳戶金鑰機密）存儲在環境變數中。 環境變數由我們記錄、加密和存儲。 同樣，在命名[runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)時，請避免包含敏感資訊，如使用者名或機密專案名稱。 此資訊可能顯示在 Microsoft 支援工程師可訪問的遙測日誌中。

您可以在預配工作區`hbi_workspace``TRUE`時將參數設置為 從正在收集的診斷資料中退出宣告。 使用 AzureML Python SDK、CLI、REST API 或 Azure 資源管理器範本時，此功能受支援。

### <a name="microsoft-generated-data"></a>微軟生成的資料

使用自動機器學習等服務時，Microsoft 可能會生成臨時的預處理資料，用於訓練多個模型。 此資料存儲在工作區中的資料存儲中，這允許您適當地實施存取控制和加密。

您可能還希望將[從已部署終結點記錄的診斷資訊](how-to-enable-app-insights.md)加密到 Azure 應用程式見解實例中。

## <a name="monitoring"></a>監視

### <a name="metrics"></a>計量

可以使用 Azure 監視器指標查看和監視 Azure 機器學習工作區的指標。 在[Azure 門戶](https://portal.azure.com)中，選擇工作區，然後選擇**指標**：

[![顯示工作區示例指標的螢幕截圖](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

這些指標包括有關運行、部署和註冊的資訊。

有關詳細資訊，請參閱[Azure 監視器 中的指標](/azure/azure-monitor/platform/data-platform-metrics)。

### <a name="activity-log"></a>活動記錄檔

您可以查看工作區的活動日誌，以查看在工作區上執行的各種操作。 日誌包括操作名稱、事件開始器和時間戳記等基本資訊。

此螢幕截圖顯示工作區的活動日誌：

[![顯示工作區活動日誌的螢幕截圖](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

評分請求詳細資訊存儲在應用程式見解中。 創建工作區時，將在訂閱中創建應用程式見解。 記錄的資訊包括欄位，例如：

* HTTP方法
* UserAgent
* 計算類型
* 請求 Url
* StatusCode
* RequestId
* Duration

> [!IMPORTANT]
> Azure 機器學習工作區中的某些操作不會將資訊記錄到活動日誌。 例如，不會記錄培訓運行的開始和模型的註冊。
>
> 其中一些操作將顯示在工作區的 **"活動"** 區域中，但這些通知並不指示誰啟動了活動。

## <a name="data-flow-diagrams"></a>資料流程圖

### <a name="create-workspace"></a>建立工作區

下圖顯示了創建工作區工作流。

* 從受支援的 Azure 機器學習用戶端之一（Azure CLI、Python SDK、Azure 門戶）登錄到 Azure AD，並請求相應的 Azure 資源管理器權杖。
* 調用 Azure 資源管理器以創建工作區。 
* Azure 資源管理器與 Azure 機器學習資來源提供者聯繫以預配工作區。

在創建工作區期間，在使用者的訂閱中創建其他資源：

* 金鑰保存庫（用於存儲機密）
* Azure 存儲帳戶（包括 Blob 和檔共用）
* Azure 容器註冊表（用於存儲 Docker 映射以進行推理/評分和實驗）
* 應用程式見解（用於存儲遙測資料）

使用者還可以根據需要預配附加到工作區的其他計算目標（如 Azure Kubernetes 服務或 VM）。

[![創建工作區工作流](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>保存原始程式碼（訓練腳本）

下圖顯示了代碼快照工作流。

與 Azure 機器學習工作區關聯的是包含原始程式碼（訓練腳本）的目錄（實驗）。 這些腳本存儲在本地電腦和雲中（在訂閱的 Azure Blob 存儲中）。 代碼快照用於執行或檢查歷史審核。

[![代碼快照工作流](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>訓練

下圖顯示了培訓工作流。

* 使用上一節中保存的代碼快照的快照 ID 調用 Azure 機器學習。
* Azure 機器學習創建運行 ID（可選）和機器學習服務權杖，稍後由機器學習計算/VM 等計算目標使用，以便與機器學習服務進行通信。
* 您可以選擇託管計算目標（如機器學習計算）或非託管計算目標（如 VM）來運行培訓作業。 以下是這兩種方案的資料流程：
   * VM/HDInsight，由 SSH 憑據在 Microsoft 訂閱中的關鍵保存庫中訪問。 Azure 機器學習在計算目標上運行管理代碼，

   1. 準備環境。 （Docker 是 VM 和本地電腦的選項。 請參閱機器學習計算的以下步驟，以瞭解在 Docker 容器上運行實驗的工作原理。
   1. 下載代碼。
   1. 設置環境變數和配置。
   1. 運行使用者腳本（上一節中提及的代碼快照）。

   * 機器學習計算，通過工作區管理的身份訪問。
由於機器學習計算是託管計算目標（即由 Microsoft 管理），因此它在 Microsoft 訂閱下運行。

   1. 如果需要，遠端 Docker 構造將啟動。
   1. 管理代碼寫入使用者的 Azure 檔共用。
   1. 容器使用初始命令啟動。 也就是說，管理代碼如上一步所述。

#### <a name="querying-runs-and-metrics"></a>查詢運行和指標

在下面的流程圖中，當訓練計算目標從 Cosmos DB 資料庫中的存儲將運行指標寫回 Azure 機器學習時，將執行此步驟發生。 用戶端可以調用 Azure 機器學習。 機器學習將依次從 Cosmos DB 資料庫中提取指標，並將它們返回到用戶端。

[![培訓工作流](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>創建 Web 服務

下圖顯示了推理工作流。 推理或模型評分是部署模型用於預測的階段，最常見的是在生產資料上。

詳細資料如下：

* 使用者使用 Azure 機器學習 SDK 等用戶端註冊模型。
* 使用者使用模型、分數檔和其他模型依賴項創建圖像。
* Docker 映射創建並存儲在 Azure 容器註冊表中。
* Web 服務使用上一步驟中創建的圖像部署到計算目標（容器實例/AKS）。
* 評分請求詳細資訊存儲在應用程式見解中，該見解位於使用者的訂閱中。
* 遙測也會推送到 Microsoft/Azure 訂閱。

[![推理工作流](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>後續步驟

* [使用 TLS 保護 Azure 機器學習 Web 服務](how-to-secure-web-service.md)
* [使用部署為 Web 服務的機器學習模型](how-to-consume-web-service.md)
* [如何執行批次預測](how-to-use-parallel-run-step.md)
* [使用應用程式見解監視 Azure 機器學習模型](how-to-enable-app-insights.md)
* [在生產環境中收集模型資料](how-to-enable-data-collection.md)
* [Azure 機器學習 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [將 Azure 機器學習與 Azure 虛擬網路一起使用](how-to-enable-virtual-network.md)
* [建置建議系統的最佳作法](https://github.com/Microsoft/Recommenders)
* [在 Azure 上建置即時建議 API](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
