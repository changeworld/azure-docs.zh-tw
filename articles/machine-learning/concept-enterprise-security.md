---
title: 企業安全性和指導方針
titleSuffix: Azure Machine Learning
description: 安全地使用 Azure Machine Learning：驗證、授權、網路安全性、資料加密和監視。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: 19736a37e0da07237f6b112de7da86efe3d8bfe5
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359369"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning 的企業安全性和治理

在此文章中，您將了解 Azure Machine Learning 可用的安全性功能。

當您使用雲端服務時，最佳做法是限制只有需要的使用者才能存取。 一開始先了解服務所使用的驗證與授權模型。 您也可能想要限制網路存取，或安全地將內部部署網路中的資源與雲端聯結。 資料加密也很重要，不論是待用，還是資料在服務之間移動。 您也可能想要建立原則，以在建立不符合規範的設定時強制執行某些設定或記錄。 最後，您必須能夠監視服務，並產生所有活動的稽核記錄。

> [!NOTE]
> 此文章中的資訊適用於 Azure Machine Learning Python SDK 1.0.83.1 版或更高版本。

## <a name="authentication"></a>驗證

如果 Azure Active Directory (Azure AD) 設定為使用多重要素驗證，則支援多重要素驗證。 以下是驗證程序：

1. 用戶端登入到 Azure AD，並取得 Azure Resource Manager 權杖。  完全支援使用者和服務主體。
1. 用戶端會將權杖提供給 Azure Resource Manager 和所有 Azure Machine Learning。
1. Machine Learning 服務會為使用者計算目標 (例如，Machine Learning Compute) 提供 Machine Learning 服務權杖。 在執行完成之後，使用者計算目標會使用此權杖來回呼 Machine Learning 服務。 範圍僅限於工作區。

[![Azure Machine Learning 中的驗證](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

如需詳細資訊，請參閱[設定 Azure Machine Learning 資源和工作流程的驗證](how-to-setup-authentication.md)。 此文章提供驗證的相關資訊與範例，包括使用服務主體與自動化工作流程。

### <a name="authentication-for-web-service-deployment"></a>Web 服務部署的驗證

Azure Machine Learning 支援兩種形式的 Web 服務驗證：金鑰和權杖。 每個 Web 服務一次只能啟用一種形式的驗證。

|驗證方法|描述|Azure Container Instances|AKS|
|---|---|---|---|
|Key|金鑰是靜態的，而且不需要重新整理。 金鑰可以手動重新產生。|預設為停用| 預設為啟用|
|Token|權杖會在指定的時間間隔後過期，而且需要重新整理。| 無法使用| 預設為停用 |

如需程式碼範例，請參閱 [Web 服務驗證](how-to-setup-authentication.md#web-service-authentication)一節。

## <a name="authorization"></a>授權

您可以建立多個工作區，而且每個工作區都可由多人共用。 共用工作區時，您可以透過指派下列角色給使用者來控制對工作區的存取：

* 擁有者
* 參與者
* 讀取者

下表列出一些主要 Azure Machine Learning 作業，以及可以加以執行的角色：

| Azure Machine Learning 作業 | 擁有者 | 參與者 | 讀取者 |
| ---- |:----:|:----:|:----:|
| 建立工作區 | ✓ | ✓ | |
| 共用工作區 | ✓ | |  |
| 建立計算目標 | ✓ | ✓ | |
| 連結計算目標 | ✓ | ✓ | |
| 連結資料存放區 | ✓ | ✓ | |
| 執行實驗 | ✓ | ✓ | |
| 檢視執行/計量 | ✓ | ✓ | ✓ |
| 註冊模型 | ✓ | ✓ | |
| 建立影像 | ✓ | ✓ | |
| 部署 Web 服務 | ✓ | ✓ | |
| 檢視模型/影像 | ✓ | ✓ | ✓ |
| 呼叫 Web 服務 | ✓ | ✓ | ✓ |

如果內建角色不符合您的需求，您可以建立自訂角色。 支援自訂角色來控制工作區內的所有作業，例如建立計算、提交執行、註冊資料存放區，或部署模型。 自訂角色可以擁有工作區之各種資源的讀取、寫入或刪除許可權，例如叢集、資料存放區、模型和端點。 您可以在特定工作區層級、特定的資源群組層級或特定的訂用帳戶層級上，讓角色可供使用。 如需詳細資訊，請參閱[管理 Azure Machine Learning 工作區中的使用者和角色](how-to-assign-roles.md)。

> [!WARNING]
> Azure Active Directory 的企業對企業共同作業支援 Azure Machine Learning，但 Azure Active Directory 的企業對消費者共同作業目前不支援。

### <a name="securing-compute-targets-and-data"></a>保護計算目標和資料

擁有者和參與者可以使用連結至工作區的所有計算目標和資料存放區。  

每個工作區也有相關聯的系統指派受控識別，其名稱與工作區相同。 受控識別在工作區中使用的連結資源上具有下列權限。

如需受控識別的相關詳細資訊，請參閱[適用於 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/overview.md)。

| 資源 | 權限 |
| ----- | ----- |
| 工作區 | 參與者 |
| 儲存體帳戶 | 儲存體 Blob 資料參與者 |
| 金鑰保存庫 | 所有金鑰、祕密、憑證的存取權 |
| Azure Container Registry | 參與者 |
| 包含工作區的資源群組 | 參與者 |
| 包含金鑰保存庫的資源群組 (如果與包含工作區的資源群組不同) | 參與者 |

我們不建議管理員撤銷上表所述資源的受控識別存取權。 您可以使用重新同步金鑰作業來還原存取權。

Azure Machine Learning 會針對每個工作區區域，在您的訂用帳戶中建立具有參與者等級存取權的其他應用程式 (名稱開頭為 `aml-` 或 `Microsoft-AzureML-Support-App-`)。 例如，如果同一個訂用帳戶中，在美國東部有一個工作區，在北歐有一個工作區，則您將看到兩個這些應用程式。 這些應用程式可讓 Azure Machine Learning 協助您管理計算資源。

## <a name="network-security"></a>網路安全性

Azure Machine Learning 仰賴其他 Azure 服務來處理計算資源。 計算資源 (計算目標) 用於定型和部署模型。 您可以在虛擬網路中建立這些計算目標。 例如，您可以使用 Azure 資料科學虛擬機器來定型模型，然後將模型部署到 AKS。  

如需詳細資訊，請參閱[虛擬網路隔離和隱私權概觀](how-to-network-security-overview.md)。

您也可以為您的工作區啟用 Azure Private Link。 Private Link 可讓您限制從 Azure 虛擬網路到工作區的通訊。 如需詳細資訊，請參閱[如何設定 Private Link](how-to-configure-private-link.md)。

## <a name="data-encryption"></a>資料加密

> [!IMPORTANT]
> 針對在 __定型__ 期間的生產等級加密，Microsoft 建議使用 Azure Machine Learning 計算叢集。 針對在 __推斷__ 期間的生產等級加密，Microsoft 建議使用 Azure Kubernetes Service。
>
> Azure Machine Learning 計算實例是開發/測試環境。 使用它時，建議您將檔案（例如筆記本和腳本）儲存在檔案共用中。 您的資料應該儲存在資料存放區中。

### <a name="encryption-at-rest"></a>待用加密

> [!IMPORTANT]
> 如果您的工作區包含敏感性資料，建議您在建立工作區時，設定 [hbi_workspace 旗標](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) \(部分機器翻譯\)。 只有在建立 `hbi_workspace` 工作區時，才可以設定旗標。 無法針對現有的工作區進行變更。

旗標會 `hbi_workspace` 控制 [microsoft 針對診斷目的所收集的資料](#microsoft-collected-data) 量，並 [在 microsoft 管理的環境中啟用額外的加密](../security/fundamentals/encryption-atrest.md)。 此外，它也會啟用下列動作：

* 如果您未在該訂用帳戶中建立任何先前的叢集，就會開始加密 Azure Machine Learning 計算叢集中的本機暫存磁片。 否則，您必須提出支援票證，以啟用計算叢集的暫存磁碟加密 
* 清除執行之間的本機暫存磁碟
* 使用您的金鑰保存庫，安全地將儲存體帳戶、容器登錄和 SSH 帳戶的認證從執行層傳遞至您的計算叢集
* 啟用 IP 篩選以確保底層批次集區無法由 AzureMachineLearningService 以外的任何外部服務呼叫
* 請注意，HBI 工作區中不支援計算實例

#### <a name="azure-blob-storage"></a>Azure Blob 儲存體

Azure Machine Learning 會將快照集、輸出和記錄儲存在與 Azure Machine Learning 工作區和您的訂用帳戶相關聯的 Azure Blob 儲存體帳戶中。 Azure Blob 儲存體中儲存的所有資料，都會使用 Microsoft 受控金鑰進行待用加密。

如需如何針對儲存在 Azure Blob 儲存體中的資料使用您自己的金鑰的相關資訊，請參閱[在 Azure Key Vault 中使用客戶管理金鑰進行 Azure 儲存體加密](../storage/common/customer-managed-keys-configure-key-vault.md)。

定型資料通常也會儲存在 Azure Blob 儲存體中，以便定型計算目標可以加以存取。 此儲存體不受 Azure Machine Learning 管理，但已作為遠端檔案系統裝載至計算目標。

如果您需要「輪替或撤銷」金鑰，您可以隨時執行此動作。 輪替金鑰時，儲存體帳戶將會開始使用新的金鑰 (最新版本) 來加密待用資料。 撤銷 (停用) 金鑰時，儲存體帳戶會負責處理失敗的要求。 輪替或撤銷通常需要一小時的時間才會生效。

如需重新產生存取金鑰的詳細資訊，請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)。

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning 會在 Azure Cosmos DB 執行個體中儲存計量和中繼資料。 這個執行個體與 Azure Machine Learning 所管理的 Microsoft 訂用帳戶相關聯。 Azure Cosmos DB 中儲存的所有資料，都會使用 Microsoft 受控金鑰進行待用加密。

若要使用您自己 (客戶管理) 金鑰來加密 Azure Cosmos DB 執行個體，您可以建立專用的 Cosmos DB 執行個體以搭配您的工作區使用。 如果您想要將資料 (例如執行歷程記錄資訊) 儲存在 Microsoft 訂用帳戶所裝載的多租用戶 Cosmos DB 執行個體之外，我們建議採用這種方法。 

若要使用客戶管理金鑰在您的訂用帳戶中佈建 Cosmos DB 執行個體，請執行下列動作：

* 在您的訂用帳戶中註冊 MachineLearning 和 Microsoft.DocumentDB 資源提供者（如果尚未這麼做）。

* 建立 Azure Machine Learning 工作區時，請使用下列參數。 這兩個參數都是必要的，而且在 SDK、CLI、REST API 和 Resource Manager 範本中都有支援。

    * `resource_cmk_uri`:此參數是金鑰保存庫中客戶管理金鑰的完整資源 URI，包括[金鑰的版本資訊](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)。 

    * `cmk_keyvault`:此參數是您訂用帳戶中金鑰保存庫的資源識別碼。 此金鑰保存庫必須位於您將用於 Azure Machine Learning 工作區的相同地區和訂用帳戶中。 
    
        > [!NOTE]
        > 此金鑰保存庫執行個體可以與您佈建工作區時 Azure Machine Learning 所建立的金鑰保存庫不同。 如果您想要將相同的金鑰保存庫執行個體用於工作區，請在佈建工作區時傳遞相同的金鑰保存庫，方法是使用 [key_vault 參數](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) \(部分機器翻譯\)。 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

如果您需要「輪替或撤銷」金鑰，您可以隨時執行此動作。 輪替金鑰時，Cosmos DB 將會開始使用新的金鑰 (最新版本) 來加密待用資料。 撤銷 (停用) 金鑰時，Cosmos DB 會負責處理失敗的要求。 輪替或撤銷通常需要一小時的時間才會生效。

如需使用 Cosmos DB 之客戶管理金鑰的詳細資訊，請參閱[為您的 Azure Cosmos DB 帳戶設定客戶管理金鑰](../cosmos-db/how-to-setup-cmk.md)。

#### <a name="azure-container-registry"></a>Azure Container Registry

您登錄 (Azure Container Registry) 中的所有容器映像在待用時都已加密。 Azure 會在儲存映像之前自動將其加密，並在 Azure Machine Learning 提取映像時將其解密。

若要使用您自己 (客戶管理) 金鑰來加密您的 Azure Container Registry，您必須建立自己的 ACR，並在佈建工作區時加以連結，或加密在佈建工作區時所建立的預設執行個體。

> [!IMPORTANT]
> Azure Machine Learning 需要在您的 Azure Container Registry 啟用系統管理員帳戶。 依預設，當您建立容器登錄時，就會停用此設定。 如需啟用系統管理員帳戶的相關資訊，請參閱系統 [管理員帳戶](../container-registry/container-registry-authentication.md#admin-account)。
>
> 為工作區建立 Azure Container Registry 之後，請勿將其刪除。 這樣做將會造成您的 Azure Machine Learning 工作區中斷。

如需使用現有 Azure Container Registry 建立工作區的範例，請參閱下列文章：

* [使用 Azure CLI 建立 Azure Machine Learning 的工作區](how-to-manage-workspace-cli.md)。
* [使用 PYTHON SDK 建立工作區](how-to-manage-workspace.md?tabs=python#create-a-workspace)。
* [使用 Azure Resource Manager 範本建立 Azure Machine Learning 的工作區](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Azure 容器執行個體

您可以使用客戶管理金鑰來加密已部署的 Azure 容器執行個體 (ACI) 資源。 用於 ACI 的客戶管理金鑰可以儲存在您工作區的 Azure Key Vault 中。 如需產生金鑰的詳細資訊，請參閱[使用客戶管理金鑰來加密資料](../container-instances/container-instances-encrypt-data.md#generate-a-new-key)。

若要在將模型部署到 Azure 容器執行個體時使用金鑰，請使用 `AciWebservice.deploy_configuration()` 建立新的部署設定。 使用下列參數提供金鑰資訊：

* `cmk_vault_base_url`:包含金鑰之金鑰保存庫的 URL。
* `cmk_key_name`:索引鍵名稱。
* `cmk_key_version`:金鑰的版本。

如需建立和使用部署設定的相關詳細資訊，請參閱下列文章：

* [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) 參考
* [部署的位置和方式](how-to-deploy-and-where.md)
* [將模型部署到 Azure 容器執行個體](how-to-deploy-azure-container-instance.md)

如需搭配使用客戶管理的金鑰與 ACI 的詳細資訊，請參閱[使用客戶管理的金鑰來加密資料](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key)。

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

您可以隨時使用客戶管理的金鑰來加密已部署的 Azure Kubernetes Service 資源。 如需詳細資訊，請參閱[攜帶您自己的金鑰搭配 Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md)。 

此程序可讓您對 Kubernetes 叢集中已部署之虛擬機器的資料和 OS 磁碟進行加密。

> [!IMPORTANT]
> 此程序僅適用於 AKS K8s 1.17 版或更高版本。 Azure Machine Learning 已在 2020 年 1 月 13 日新增 AKS 1.17 的支援。

#### <a name="machine-learning-compute"></a>Machine Learning Compute

儲存在 Azure 儲存體中之每個計算節點的 OS 磁碟，都會使用 Azure Machine Learning 儲存體帳戶中的 Microsoft 受控金鑰進行加密。 此計算目標是暫時的，而且通常會在沒有任何執行排入佇列時縮小叢集。 底層虛擬機器已解除佈建，且 OS 磁碟已刪除。 OS 磁碟不支援 Azure 磁碟加密。

每部虛擬機器也會有本機暫存磁碟供 OS 作業使用。 如果您想要的話，可以使用磁碟來暫存定型資料。 根據預設，如果 `hbi_workspace` 參數設定為 `TRUE`，則會針對工作區加密磁碟。 此環境只有在執行期間才會短暫存留，而且加密支援僅限於系統受控金鑰。

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks 可以在 Azure Machine Learning 管線中使用。 根據預設，Azure Databricks 使用的 Databricks 檔案系統 (DBFS) 會使用 Microsoft 受控金鑰進行加密。 若要設定 Azure Databricks 使用客戶管理的金鑰，請參閱[在預設 (根) DBFS 上設定客戶管理的金鑰](/azure/databricks/security/customer-managed-keys-dbfs)。

### <a name="encryption-in-transit"></a>傳輸中加密

Azure Machine Learning 使用 TLS 來保護各種 Azure Machine Learning 微服務之間的內部通訊。 所有的 Azure 儲存體存取也會透過安全通道進行。

為了保護對評分端點進行的外部呼叫，Azure Machine Learning 使用 TLS。 如需詳細資訊，請參閱[使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](./how-to-secure-web-service.md) \(部分機器翻譯\)。

### <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure Machine Learning 使用與工作區相關聯的 Azure Key Vault 執行個體來儲存各種類型的認證：

* 相關聯的儲存體帳戶連接字串
* Azure Container Repository 執行個體的密碼
* 資料存放區的連接字串

SSH 密碼和計算目標 (例如 Azure HDInsight 和 VM) 的金鑰會儲存在與 Microsoft 訂用帳戶相關聯的個別金鑰保存庫中。 Azure Machine Learning 不會儲存使用者所提供的任何密碼或金鑰。 相反地，其會產生、授權及儲存自己的 SSH 金鑰，以連線至 VM 和 HDInsight 來執行實驗。

每個工作區都有相關聯的系統指派受控識別，其名稱與工作區相同。 此受控識別可存取金鑰保存庫中的所有金鑰、祕密和憑證。

## <a name="data-collection-and-handling"></a>資料收集與處理

### <a name="microsoft-collected-data"></a>Microsoft 收集的資料

Microsoft 可能會收集非使用者識別資訊，像是資源名稱 (例如資料集名稱或機器學習實驗名稱)，或作業環境變數以供診斷之用。 所有這類資料都是使用 Microsoft 受控金鑰儲存在 Microsoft 擁有之訂用帳戶內裝載的儲存體中，並遵循 [Microsoft 的標準隱私權原則和資料處理標準](https://privacy.microsoft.com/privacystatement)。

Microsoft 也建議您不要將敏感性資訊 (例如帳戶金鑰祕密) 儲存在環境變數中。 我們會記錄、加密及儲存環境變數。 同樣地，當命名 [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) 時，請避免包含敏感性資訊，例如使用者名稱或祕密專案名稱。 此資訊可能會出現在 Microsoft 支援服務工程師可存取的遙測記錄中。

在佈建工作區時，您可以將 `hbi_workspace` 參數設定為 `TRUE`，以選擇不要收集診斷資料。 使用 AzureML Python SDK、CLI、REST API 或 Azure Resource Manager 範本時，支援此功能。

### <a name="microsoft-generated-data"></a>Microsoft 產生的資料

使用「自動化機器學習」這類服務時，Microsoft 可能會產生暫時性、預先處理的資料來定型多個模型。 此資料會儲存在您工作區的資料存放區中，讓您能夠適當地強制執行存取控制和加密。

您可能也會想要將[從已部署的端點記錄的診斷資訊](how-to-enable-app-insights.md)，加密至您的 Azure Application Insights 執行個體。

## <a name="monitoring"></a>監視

### <a name="metrics"></a>計量

您可以使用 Azure 監視器計量來檢視和監視 Azure Machine Learning 工作區的計量。 在 [Azure 入口網站](https://portal.azure.com)中，選取您的工作區，然後選取 [計量]：

[![顯示工作區範例計量的螢幕擷取畫面](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

計量包含有關執行、部署和註冊的資訊。

如需詳細資訊，請參閱 [Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)。

### <a name="activity-log"></a>活動記錄檔

您可以檢視工作區的活動記錄，以查看工作區上執行的各種作業。 此記錄包含基本資訊，例如作業名稱、事件起始者和時間戳記。

此螢幕擷取畫面顯示工作區的活動記錄：

[![顯示工作區活動記錄的螢幕擷取畫面](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

評分要求詳細資料會儲存在 Application Insights 中。 當您建立工作區時，會在您的訂用帳戶中建立 Application Insights。 記錄的資訊包括下列欄位：

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
> 其中一些動作會出現在工作區的 [活動] 區域中，但這些通知不會指出起始活動的人員。

### <a name="vulnerability-scanning"></a>弱點掃描

Azure 資訊安全中心為混合式雲端工作負載提供統一的安全性管理和進階威脅防護。 針對 Azure machine learning，您應該啟用 Azure Container Registry 資源的掃描，並 Azure Kubernetes Service 資源。 請參閱 [Azure Container Registry 的影像掃描（由安全性中心](../security-center/defender-for-container-registries-introduction.md) 和 [Azure Kubernetes Services 與安全性中心整合](../security-center/defender-for-kubernetes-introduction.md)）。

## <a name="data-flow-diagrams"></a>資料流程圖

### <a name="create-workspace"></a>建立工作區

下圖顯示建立工作區工作流程。

* 您可以從其中一個支援的 Azure Machine Learning 用戶端 (Azure CLI、Python SDK、Azure 入口網站) 登入 Azure AD，並要求適當的 Azure Resource Manager 權杖。
* 您會呼叫 Azure Resource Manager 來建立工作區。 
* Azure Resource Manager 聯絡 Azure Machine Learning 資源提供者以佈建工作區。

建立工作區時，會在使用者的訂用帳戶中建立額外的資源：

* Key Vault (用來儲存祕密)
* Azure 儲存體帳戶 (包含 Blob 與檔案共用)
* Azure Container Registry (儲存 Docker 映像以進行推斷/評分和實驗)
* Application Insights (用來儲存遙測資料)

使用者也可以視需要佈建連結至工作區 (例如 Azure Kubernetes Service 或 VM) 的其他計算目標。

[![建立工作區工作流程](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>儲存原始碼 (將指令碼定型)

下圖顯示程式碼快照集工作流程。

與 Azure Machine Learning 工作區相關聯的是包含原始程式碼 (定型指令碼) 的目錄 (實驗)。 這些指令碼會儲存在您的本機電腦和雲端 (在您訂用帳戶的 Azure Blob 儲存體中)。 程式碼快照集會用來執行或檢查歷程稽核。

[![程式碼快照集工作流程](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>訓練

下圖顯示定型工作流程。

* 會使用上一節中所儲存之程式碼快照集的快照集識別碼來呼叫 Azure Machine Learning。
* Azure Machine Learning 會建立執行識別碼 (選擇性) 和 Machine Learning 服務權杖，稍後會由計算目標 (例如 Machine Learning Compute/VM) 用來與 Machine Learning 服務進行通訊。
* 您可以選擇受控計算目標 (例如 Machine Learning Compute) 或非受控計算目標 (例如 VM) 來執行定型作業。 以下是這兩種案例的資料流程：
   * VM/HDInsight，由 Microsoft 訂用帳戶的金鑰保存庫中的 SSH 認證存取。 Azure Machine Learning 會在計算目標上執行管理程式碼，其：

   1. 準備環境。 (Docker 是 VM 和本機電腦的選項。 請參閱 Machine Learning Compute 的下列步驟，以了解在 Docker 容器上執行實驗的運作方式。)
   1. 下載程式碼。
   1. 設定環境變數和組態。
   1. 執行使用者指令碼 (上一節中提到的程式碼快照集)。

   * Machine Learning Compute，透過工作區受控識別來存取。
因為 Machine Learning Compute 是受控計算目標 (也就是由 Microsoft 管理)，所以其會在您的 Microsoft 訂用帳戶下執行。

   1. 如有需要，會啟動遠端 Docker 建構。
   1. 管理程式碼會寫入使用者的 Azure 檔案儲存體共用。
   1. 使用初始命令啟動容器。 也就是上一個步驟中所述的管理程式碼。

#### <a name="querying-runs-and-metrics"></a>查詢執行和計量

在下面的流程圖中，當定型計算目標將執行計量從 Cosmos DB 資料庫中的儲存體寫回 Azure Machine Learning 時，就會發生此步驟。 用戶端可以呼叫 Azure Machine Learning。 Machine Learning 接著會從 Cosmos DB 資料庫中提取計量，並將其傳回用戶端。

[![定型工作流程](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>建立 Web 服務

下圖顯示推斷工作流程。 推斷 (或模型評分) 是將部署的模型用於預測的階段，通常用於生產資料。

詳細資料如下：

* 使用者會使用 Azure Machine Learning SDK 之類的用戶端來註冊模型。
* 使用者會使用模型、分數檔案和其他模型相依性來建立映像。
* Docker 映像會建立並儲存在 Azure Container Registry 中。
* Web 服務會使用在上一個步驟中建立的映像，部署到計算目標 (容器執行個體/AKS)。
* 評分要求詳細資料會儲存在使用者訂用帳戶中的 Application Insights,。
* 遙測也會推送至 Microsoft/Azure 訂用帳戶。

[![推斷工作流程](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="audit-and-manage-compliance"></a>稽核及管理合規性

[Azure 原則](../governance/policy/index.yml) 是一種管理工具，可讓您確保 Azure 資源符合您的原則。 您可以使用 Azure Machine Learning 指派下列原則：

* **客戶管理的金鑰** ：無論工作區是否必須使用客戶管理的金鑰，請進行審核或強制執行。
* **Private link** ： Audit 工作區是否使用私人端點來與虛擬網路通訊。

如需 Azure 原則的詳細資訊，請參閱 [Azure 原則檔](../governance/policy/overview.md)。

如需 Azure Machine Learning 特定原則的詳細資訊，請參閱 [使用 Azure 原則來審核和管理合規性](how-to-integrate-azure-policy.md)。

## <a name="resource-locks"></a>資源鎖定

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>後續步驟

* [使用 TLS 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 Machine Learning 模型](how-to-consume-web-service.md)
* [搭配使用 Azure Machine Learning 與 Azure 防火牆](how-to-access-azureml-behind-firewall.md)
* [搭配使用 Azure Machine Learning 與 Azure 虛擬網路](how-to-network-security-overview.md)
* [建置建議系統的最佳作法](https://github.com/Microsoft/Recommenders)
* [在 Azure 上建置即時建議 API](/azure/architecture/reference-architectures/ai/real-time-recommendation)
