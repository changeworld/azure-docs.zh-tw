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
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992024"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning 的企業安全性和治理

在本文中，您將瞭解適用于 Azure Machine Learning 的安全性和治理功能。 這些功能適用于想要建立與公司原則相容的安全設定的系統管理員、DevOps 和 MLOps。 使用 Azure Machine Learning 和 Azure 平臺，您可以：

* 依使用者帳戶或群組限制資源和作業的存取
* 限制連入和連出網路通訊
* 加密傳輸中和待用資料
* 掃描弱點
* 套用和審核設定原則

## <a name="restrict-access-to-resources-and-operations"></a>限制資源和作業的存取

[Azure Active Directory (Azure AD) ](../active-directory/fundamentals/active-directory-whatis.md) 是適用于 Azure Machine Learning 的身分識別服務提供者。 它可讓您建立及管理安全性物件， (使用者、群組、服務主體和受控識別) 用來向 Azure 資源 _進行驗證_ 。 如果 Azure AD 已設定為使用多重要素驗證，就會受到支援。

以下是在 Azure AD 中使用多重要素驗證 Azure Machine Learning 的驗證程式：

1. 用戶端登入到 Azure AD，並取得 Azure Resource Manager 權杖。
1. 用戶端會將權杖提供給 Azure Resource Manager 和所有 Azure Machine Learning。
1. Azure Machine Learning 為使用者計算目標提供 Machine Learning 的服務權杖 (例如 Azure Machine Learning 計算叢集) 。 在執行完成之後，使用者計算目標會使用此權杖來回呼 Machine Learning 服務。 範圍僅限於工作區。

[![Azure Machine Learning 中的驗證](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

每個工作區都有相關聯的系統指派 [受控識別](../active-directory/managed-identities-azure-resources/overview.md) ，其名稱與工作區相同。 此受控識別是用來安全地存取工作區所使用的資源。 它對附加的資源具有下列 Azure RBAC 許可權：

| 資源 | 權限 |
| ----- | ----- |
| 工作區 | 參與者 |
| 儲存體帳戶 | 儲存體 Blob 資料參與者 |
| 金鑰保存庫 | 所有金鑰、祕密、憑證的存取權 |
| Azure Container Registry | 參與者 |
| 包含工作區的資源群組 | 參與者 |
| 包含金鑰保存庫的資源群組 (如果與包含工作區的資源群組不同) | 參與者 |

我們不建議管理員撤銷上表所述資源的受控識別存取權。 您可以使用重新 [同步金鑰操作](how-to-change-storage-access-key.md)來還原存取權。

Azure Machine Learning 也會建立額外的應用程式 (名稱開頭為 `aml-` 或 `Microsoft-AzureML-Support-App-`) 具有您訂用帳戶中每個工作區區域的參與者層級存取權。 例如，如果同一個訂用帳戶中，在美國東部有一個工作區，在北歐有一個工作區，則您將看到兩個這些應用程式。 這些應用程式可讓 Azure Machine Learning 協助您管理計算資源。

您也可以設定自己的受控識別，以搭配 Azure 虛擬機器和 Azure Machine Learning 計算叢集使用。 使用 VM 時，受控識別可以用來從 SDK 存取您的工作區，而不是個別使用者的 Azure AD 帳戶。 使用計算叢集，受控識別會用來存取執行定型作業的使用者可能無法存取的資源，例如安全的資料存放區。 如需詳細資訊，請參閱 [Azure Machine Learning 工作區的驗證](how-to-setup-authentication.md)。

> [!TIP]
> 在 Azure Machine Learning 中使用 Azure AD 和 Azure RBAC 的一些例外狀況：
> * 您可以選擇性地啟用計算資源（例如 Azure Machine Learning 計算實例和計算叢集）的 __SSH__ 存取。 SSH 存取是以公開/私密金鑰組為基礎，而不是 Azure AD。 SSH 存取不受 Azure RBAC 的規範。
> * 您可以驗證部署為 web 服務的模型， (使用 __金鑰__ 或 __權杖__ 型驗證) 的推斷端點。 索引鍵是靜態字串，而權杖則是使用 Azure AD 安全性物件來抓取。 如需詳細資訊，請參閱 [設定部署為 web 服務的模型驗證](how-to-authenticate-web-service.md)。

如需詳細資訊，請參閱下列文章：
* [Azure Machine Learning 工作區的驗證](how-to-setup-authentication.md)
* [管理 Azure Machine Learning 的存取權](how-to-assign-roles.md)
* [連接至儲存體服務](how-to-access-data.md)
* [訓練時使用 Azure Key Vault 的秘密](how-to-use-secrets-in-runs.md)
* [使用 Azure AD 受控識別搭配 Azure Machine Learning](how-to-use-managed-identities.md)
* [使用 Azure AD 受控識別搭配您的 web 服務](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>網路安全性和隔離

若要限制網路存取 Azure Machine Learning 資源，您可以使用 [Azure 虛擬網路 (VNet) ](../virtual-network/virtual-networks-overview.md)。 Vnet 可讓您建立部分或完全與公用網際網路隔離的網路環境。 這可減少您的解決方案的受攻擊面，以及資料遭到外泄的機會。

您可以使用虛擬私人網路 (VPN) 閘道，將個別用戶端或您自己的網路連線至 VNet

Azure Machine Learning 工作區可以使用 [Azure Private Link](../private-link/private-link-overview.md) 在 VNet 後方建立私人端點。 這會提供一組私人 IP 位址，可用來從 VNet 記憶體取工作區。 某些 Azure Machine Learning 所依賴的服務也可以使用 Azure Private Link，但有些則依賴網路安全性群組或使用者定義的路由。

如需詳細資訊，請參閱下列文件：

* [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)
* [保護工作區資源](how-to-secure-workspace-vnet.md)
* [保護訓練環境](how-to-secure-training-vnet.md)
* [安全推斷環境](how-to-secure-inferencing-vnet.md)
* [在安全的虛擬網路中使用 studio](how-to-enable-studio-virtual-network.md)
* [使用自訂 DNS](how-to-custom-dns.md)
* [設定防火牆](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>資料加密

Azure Machine Learning 使用 Azure 平臺上的各種計算資源和資料存放區。 若要深入瞭解這兩者如何支援待用和傳輸中的資料加密，請參閱 [Azure Machine Learning 的資料加密](concept-data-encryption.md)。

將模型部署為 web 服務時，您可以啟用傳輸層安全性 (TLS) 來加密傳輸中的資料。 如需詳細資訊，請參閱 [設定安全的 web 服務](how-to-secure-web-service.md)。

## <a name="vulnerability-scanning"></a>弱點掃描

[Azure 資訊安全中心](../security-center/security-center-introduction.md) 跨混合式雲端工作負載提供統一的安全性管理和先進的威脅防護。 針對 Azure machine learning，您應該啟用 [Azure Container Registry](../container-registry/container-registry-intro.md) 資源的掃描，並 Azure Kubernetes Service 資源。 如需詳細資訊，請參閱 [Azure Container Registry 資訊安全中心](../security-center/defender-for-container-registries-introduction.md) 和 [Azure Kubernetes Services 與資訊安全中心整合](../security-center/defender-for-kubernetes-introduction.md)的影像掃描。

## <a name="audit-and-manage-compliance"></a>稽核及管理合規性

[Azure 原則](../governance/policy/index.yml) 是一種管理工具，可讓您確保 Azure 資源符合您的原則。 您可以設定原則以允許或強制執行特定設定，例如您的 Azure Machine Learning 工作區是否使用私人端點。 如需 Azure 原則的詳細資訊，請參閱 [Azure 原則檔](../governance/policy/overview.md)。 如需 Azure Machine Learning 特定原則的詳細資訊，請參閱 [使用 Azure 原則來審核和管理合規性](how-to-integrate-azure-policy.md)。

## <a name="next-steps"></a>後續步驟

* [使用 TLS 保護 Azure Machine Learning Web 服務](how-to-secure-web-service.md)
* [取用部署為 Web 服務的 Machine Learning 模型](how-to-consume-web-service.md)
* [搭配使用 Azure Machine Learning 與 Azure 防火牆](how-to-access-azureml-behind-firewall.md)
* [搭配使用 Azure Machine Learning 與 Azure 虛擬網路](how-to-network-security-overview.md)
* [待用和傳輸中的資料加密](concept-data-encryption.md)
* [在 Azure 上建置即時建議 API](/azure/architecture/reference-architectures/ai/real-time-recommendation)
