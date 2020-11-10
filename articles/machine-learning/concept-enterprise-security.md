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
ms.openlocfilehash: 2234b1507e6e0fdb0b668fc18a7c8533e3ea7cc1
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441778"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning 的企業安全性和治理

在此文章中，您將了解 Azure Machine Learning 可用的安全性功能。

當您使用雲端服務時，最佳做法是限制只有需要的使用者才能存取。 一開始先了解服務所使用的驗證與授權模型。 您也可能想要限制網路存取，或安全地將內部部署網路中的資源與雲端聯結。 資料加密也很重要，不論是待用，還是資料在服務之間移動。 您也可能想要建立原則，以在建立不符合規範的設定時強制執行某些設定或記錄。 最後，您必須能夠監視服務，並產生所有活動的稽核記錄。

> [!NOTE]
> 此文章中的資訊適用於 Azure Machine Learning Python SDK 1.0.83.1 版或更高版本。

## <a name="authentication--authorization"></a>驗證 & 授權

Azure Machine Learning 資源的大部分驗證都使用 Azure Active Directory (Azure AD) 進行驗證，以及以角色為基礎的存取控制 (適用于授權的 Azure RBAC) 。 例外狀況如下：

* __Ssh__ ：您可以對某些計算資源啟用 ssh 存取，例如 Azure Machine Learning 計算實例。 SSH 存取會使用以金鑰為基礎的驗證。 如需建立 SSH 金鑰的詳細資訊，請參閱 [建立和管理 ssh 金鑰](../virtual-machines/linux/create-ssh-keys-detailed.md)。 如需啟用 SSH 存取的詳細資訊，請參閱 [建立和管理 Azure Machine Learning 計算實例](how-to-create-manage-compute-instance.md)。
* __部署為 web 服務的模型__ ： web 服務部署可以使用 __金鑰__ 或 __權杖__ 型存取控制。 索引鍵是靜態字串。 使用 Azure AD 帳戶取出權杖。 如需詳細資訊，請參閱 [設定部署為 web 服務的模型驗證](how-to-authenticate-web-service.md)。

Azure Machine Learning 依賴的特定服務（例如 Azure 資料儲存體服務）有自己的驗證和授權方法。 如需有關儲存體服務驗證的詳細資訊，請參閱 [連接到儲存體服務](how-to-access-data.md)。

### <a name="azure-ad-authentication"></a>Azure AD 驗證

如果 Azure Active Directory (Azure AD) 設定為使用多重要素驗證，則支援多重要素驗證。 以下是驗證程序：

1. 用戶端登入到 Azure AD，並取得 Azure Resource Manager 權杖。  完全支援使用者和服務主體。
1. 用戶端會將權杖提供給 Azure Resource Manager 和所有 Azure Machine Learning。
1. Machine Learning 服務會為使用者計算目標 (例如，Machine Learning Compute) 提供 Machine Learning 服務權杖。 在執行完成之後，使用者計算目標會使用此權杖來回呼 Machine Learning 服務。 範圍僅限於工作區。

[![Azure Machine Learning 中的驗證](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

如需詳細資訊，請參閱 [Azure Machine Learning 工作區的驗證](how-to-setup-authentication.md)。

### <a name="azure-rbac"></a>Azure RBAC

您可以建立多個工作區，而且每個工作區都可由多人共用。 您可以藉由將 Azure AD 帳戶指派給 Azure RBAC 角色，來控制使用者可存取的工作區功能或作業。 以下是內建角色：

* 擁有者
* 參與者
* 讀取者

擁有者和參與者可以使用連結至工作區的所有計算目標和資料存放區。  

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

如果內建角色不符合您的需求，您可以建立自訂角色。 自訂角色控制工作區內的所有作業，例如建立計算、提交執行、註冊資料存放區，或部署模型。 自訂角色可以擁有工作區之各種資源的讀取、寫入或刪除許可權，例如叢集、資料存放區、模型和端點。 您可以在特定工作區層級、特定的資源群組層級或特定的訂用帳戶層級上，讓角色可供使用。 如需詳細資訊，請參閱[管理 Azure Machine Learning 工作區中的使用者和角色](how-to-assign-roles.md)。

> [!IMPORTANT]
> Azure Machine Learning 取決於其他 Azure 服務，例如 Azure Blob 儲存體和 Azure Kubernetes Services。 每個 Azure 服務都有自己的 Azure RBAC 設定。 若要達到您所需的存取控制層級，您可能需要套用適用于 Azure Machine Learning 的 Azure RBAC 設定，以及用於 Azure Machine Learning 的服務。

> [!WARNING]
> Azure Active Directory 的企業對企業共同作業支援 Azure Machine Learning，但 Azure Active Directory 的企業對消費者共同作業目前不支援。

### <a name="managed-identities"></a>受控身分識別

每個工作區也有相關聯的系統指派 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) ，其名稱與工作區相同。 受控識別是用來安全地存取工作區所使用的資源。 它具有下列附加資源的許可權：

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

（選擇性）您可以設定自己的受控識別，以搭配 Azure 虛擬機器和 Azure Machine Learning 計算叢集使用。 使用 VM 時，受控識別可以用來從 SDK 存取您的工作區，而不是個別使用者的 Azure AD 帳戶。 使用計算叢集，受控識別會用來存取執行定型作業的使用者可能無法存取的資源，例如安全的資料存放區。 如需詳細資訊，請參閱 [Azure Machine Learning 工作區的驗證](how-to-setup-authentication.md)。

## <a name="network-security-and-isolation"></a>網路安全性和隔離

若要限制實體存取 Azure Machine Learning 資源，您可以使用 Azure 虛擬網路 (VNet) 。 Vnet 可讓您建立部分或完全與公用網際網路隔離的網路環境。 這可減少您的解決方案的受攻擊面，以及資料遭到外泄的機會。

如需詳細資訊，請參閱[虛擬網路隔離和隱私權概觀](how-to-network-security-overview.md)。

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>資料加密

Azure Machine Learning 使用各種計算資源和資料存放區。 若要深入瞭解這兩者如何支援待用和傳輸中的資料加密，請參閱 [Azure Machine Learning 的資料加密](concept-data-encryption.md)。

### <a name="microsoft-generated-data"></a>Microsoft 產生的資料

使用「自動化機器學習」這類服務時，Microsoft 可能會產生暫時性、預先處理的資料來定型多個模型。 此資料會儲存在您工作區的資料存放區中，讓您能夠適當地強制執行存取控制和加密。

您可能也會想要將[從已部署的端點記錄的診斷資訊](how-to-enable-app-insights.md)，加密至您的 Azure Application Insights 執行個體。

## <a name="monitoring"></a>監視

根據角色以及受監視的內容而定，有數個監視案例 Azure Machine Learning。

| 角色 | 要使用的監視 |
| ---- | ----- |
| Admin、DevOps、MLOps | [Azure 監視器計量](#azure-monitor)、 [活動記錄](#activity-log)、 [弱點掃描](#vulnerability-scanning) |
| 資料科學家，MLOps | [監視執行](#monitor-runs) |

### <a name="monitor-runs"></a>監視執行

您可以在 Azure Machine Learning 中監視實驗執行，包括訓練腳本內的記錄資訊。 您可以透過 SDK、Azure CLI 和 studio 來查看此資訊。 如需詳細資訊，請參閱下列文章：

* [啟動、監視及取消定型回合](how-to-manage-runs.md)
* [啟用記錄檔](how-to-track-experiments.md)
* [檢視記錄](how-to-monitor-view-training-logs.md)
* [使用 TensorBoard 執行視覺化](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure 監視器

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
* [待用和傳輸中的資料加密](concept-data-encryption.md)
* [在 Azure 上建置即時建議 API](/azure/architecture/reference-architectures/ai/real-time-recommendation)
