---
title: 使用 Azure Machine learning 進行資料加密
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 計算和資料存放區如何提供待用和傳輸中的資料加密。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: de83be26e3cb7105303528e10fb50b7ecc438472
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447605"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Azure Machine Learning 的資料加密

Azure Machine Learning 在定型模型和執行推斷時，會使用各種不同的 Azure 資料儲存體服務和計算資源。 其中每個都有自己的案例，說明如何提供加密待用和傳輸中的資料。 在本文中，您將瞭解每個專案，最適合您的案例。

> [!IMPORTANT]
> 針對在 __定型__ 期間的生產等級加密，Microsoft 建議使用 Azure Machine Learning 計算叢集。 針對在 __推斷__ 期間的生產等級加密，Microsoft 建議使用 Azure Kubernetes Service。
>
> Azure Machine Learning 計算實例是開發/測試環境。 使用它時，建議您將檔案（例如筆記本和腳本）儲存在檔案共用中。 您的資料應該儲存在資料存放區中。

## <a name="encryption-at-rest"></a>待用加密

> [!IMPORTANT]
> 如果您的工作區包含敏感性資料，建議您在建立工作區時，設定 [hbi_workspace 旗標](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) \(部分機器翻譯\)。 只有在建立 `hbi_workspace` 工作區時，才可以設定旗標。 無法針對現有的工作區進行變更。

旗標會 `hbi_workspace` 控制 [microsoft 針對診斷目的所收集的資料](#microsoft-collected-data) 量，並 [在 microsoft 管理的環境中啟用額外的加密](../security/fundamentals/encryption-atrest.md)。 此外，它也會啟用下列動作：

* 如果您未在該訂用帳戶中建立任何先前的叢集，就會開始加密 Azure Machine Learning 計算叢集中的本機暫存磁片。 否則，您必須提出支援票證，以啟用計算叢集的暫存磁碟加密 
* 清除執行之間的本機暫存磁碟
* 使用您的金鑰保存庫，安全地將儲存體帳戶、容器登錄和 SSH 帳戶的認證從執行層傳遞至您的計算叢集
* 啟用 IP 篩選以確保底層批次集區無法由 AzureMachineLearningService 以外的任何外部服務呼叫
* 請注意，HBI 工作區中不支援計算實例

### <a name="azure-blob-storage"></a>Azure Blob 儲存體

Azure Machine Learning 會將快照集、輸出和記錄儲存在與 Azure Machine Learning 工作區和您的訂用帳戶相關聯的 Azure Blob 儲存體帳戶中。 Azure Blob 儲存體中儲存的所有資料，都會使用 Microsoft 受控金鑰進行待用加密。

如需如何針對儲存在 Azure Blob 儲存體中的資料使用您自己的金鑰的相關資訊，請參閱[在 Azure Key Vault 中使用客戶管理金鑰進行 Azure 儲存體加密](../storage/common/customer-managed-keys-configure-key-vault.md)。

定型資料通常也會儲存在 Azure Blob 儲存體中，以便定型計算目標可以加以存取。 此儲存體不受 Azure Machine Learning 管理，但已作為遠端檔案系統裝載至計算目標。

如果您需要「輪替或撤銷」金鑰，您可以隨時執行此動作。 輪替金鑰時，儲存體帳戶將會開始使用新的金鑰 (最新版本) 來加密待用資料。 撤銷 (停用) 金鑰時，儲存體帳戶會負責處理失敗的要求。 輪替或撤銷通常需要一小時的時間才會生效。

如需重新產生存取金鑰的詳細資訊，請參閱[重新產生儲存體存取金鑰](how-to-change-storage-access-key.md)。

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

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

### <a name="azure-container-registry"></a>Azure Container Registry

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

### <a name="azure-container-instance"></a>Azure 容器執行個體

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

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

您可以隨時使用客戶管理的金鑰來加密已部署的 Azure Kubernetes Service 資源。 如需詳細資訊，請參閱[攜帶您自己的金鑰搭配 Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md)。 

此程序可讓您對 Kubernetes 叢集中已部署之虛擬機器的資料和 OS 磁碟進行加密。

> [!IMPORTANT]
> 此程序僅適用於 AKS K8s 1.17 版或更高版本。 Azure Machine Learning 已在 2020 年 1 月 13 日新增 AKS 1.17 的支援。

### <a name="machine-learning-compute"></a>Machine Learning Compute

儲存在 Azure 儲存體中之每個計算節點的 OS 磁碟，都會使用 Azure Machine Learning 儲存體帳戶中的 Microsoft 受控金鑰進行加密。 此計算目標是暫時的，而且通常會在沒有任何執行排入佇列時縮小叢集。 底層虛擬機器已解除佈建，且 OS 磁碟已刪除。 OS 磁碟不支援 Azure 磁碟加密。

每部虛擬機器也會有本機暫存磁碟供 OS 作業使用。 如果您想要的話，可以使用磁碟來暫存定型資料。 根據預設，如果 `hbi_workspace` 參數設定為 `TRUE`，則會針對工作區加密磁碟。 此環境只有在執行期間才會短暫存留，而且加密支援僅限於系統受控金鑰。

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks 可以在 Azure Machine Learning 管線中使用。 根據預設，Azure Databricks 使用的 Databricks 檔案系統 (DBFS) 會使用 Microsoft 受控金鑰進行加密。 若要設定 Azure Databricks 使用客戶管理的金鑰，請參閱[在預設 (根) DBFS 上設定客戶管理的金鑰](/azure/databricks/security/customer-managed-keys-dbfs)。

## <a name="encryption-in-transit"></a>傳輸中加密

Azure Machine Learning 使用 TLS 來保護各種 Azure Machine Learning 微服務之間的內部通訊。 所有的 Azure 儲存體存取也會透過安全通道進行。

為了保護對評分端點進行的外部呼叫，Azure Machine Learning 使用 TLS。 如需詳細資訊，請參閱[使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](./how-to-secure-web-service.md) \(部分機器翻譯\)。

## <a name="data-collection-and-handling"></a>資料收集與處理

### <a name="microsoft-collected-data"></a>Microsoft 收集的資料

Microsoft 可能會收集非使用者識別資訊，像是資源名稱 (例如資料集名稱或機器學習實驗名稱)，或作業環境變數以供診斷之用。 所有這類資料都是使用 Microsoft 受控金鑰儲存在 Microsoft 擁有之訂用帳戶內裝載的儲存體中，並遵循 [Microsoft 的標準隱私權原則和資料處理標準](https://privacy.microsoft.com/privacystatement)。

Microsoft 也建議您不要將敏感性資訊 (例如帳戶金鑰祕密) 儲存在環境變數中。 我們會記錄、加密及儲存環境變數。 同樣地，當命名 [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) 時，請避免包含敏感性資訊，例如使用者名稱或祕密專案名稱。 此資訊可能會出現在 Microsoft 支援服務工程師可存取的遙測記錄中。

在佈建工作區時，您可以將 `hbi_workspace` 參數設定為 `TRUE`，以選擇不要收集診斷資料。 使用 AzureML Python SDK、CLI、REST API 或 Azure Resource Manager 範本時，支援此功能。

## <a name="using-azure-key-vault"></a>使用 Azure Key Vault

Azure Machine Learning 使用與工作區相關聯的 Azure Key Vault 執行個體來儲存各種類型的認證：

* 相關聯的儲存體帳戶連接字串
* Azure Container Repository 執行個體的密碼
* 資料存放區的連接字串

SSH 密碼和計算目標 (例如 Azure HDInsight 和 VM) 的金鑰會儲存在與 Microsoft 訂用帳戶相關聯的個別金鑰保存庫中。 Azure Machine Learning 不會儲存使用者所提供的任何密碼或金鑰。 相反地，其會產生、授權及儲存自己的 SSH 金鑰，以連線至 VM 和 HDInsight 來執行實驗。

每個工作區都有相關聯的系統指派受控識別，其名稱與工作區相同。 此受控識別可存取金鑰保存庫中的所有金鑰、祕密和憑證。

## <a name="next-steps"></a>後續步驟

* [連接到 Azure 儲存體](how-to-access-data.md)
* [從資料存放區取得資料](how-to-create-register-datasets.md)
* [連線至資料](how-to-connect-data-ui.md)
* [使用資料集定型](how-to-train-with-datasets.md)