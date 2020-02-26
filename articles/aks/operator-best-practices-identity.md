---
title: 操作員最佳做法 - Azure Kubernetes Services (AKS) 中的身分識別
description: 了解叢集操作員在 Azure Kubernetes Service (AKS) 中管理叢集的驗證和授權時的最佳做法
services: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 5ff5bdaced46a20dec3e7c5d7fb029f9428a12f2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594765"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中驗證和授權的最佳做法

當您在 Azure Kubernetes Service (AKS) 中部署及維護叢集時，您必須透過適當實作來管理對資源和服務的存取。 若沒有這樣的控制，帳戶將可能存取不需要的資源和服務。 此外，也會難以追蹤用來進行變更的認證集。

這篇最佳做法文章主要將說明叢集操作員如何管理 AKS 叢集的存取與身分識別。 在本文中，您將學會如何：

> [!div class="checklist"]
> * 使用 Azure Active Directory 驗證 AKS 叢集使用者
> * 使用角色型存取控制 (RBAC) 來控制對於資源的存取
> * 使用受控識別向其他服務驗證其本身

## <a name="use-azure-active-directory"></a>使用 Azure Active Directory

**最佳做法指引** - 使用 Azure AD 整合部署 AKS 叢集。 使用 Azure AD 可集中處理身分識別管理元件。 在存取 AKS 叢集時，使用者帳戶或群組狀態中的任何變更都將自動更新。 使用下一節所將討論的角色或 ClusterRoles 和繫結，可將使用者或群組的限定於所需的最少量權限範圍內。

Kubernetes 叢集的開發人員和應用程式擁有者需要存取不同的資源。 Kubernetes 不提供身分識別管理解決方案來控制哪些使用者可以與哪些資源互動。 您通常會將叢集與現有的身分識別解決方案整合。 Azure Active Directory (AD) 提供符合企業需求的身分識別管理解決方案，並且可與 AKS 叢集整合。

透過 Azure AD 在 AKS 中整合的叢集，您可以建立*角色*或 *ClusterRoles*，以定義資源的存取權限。 接著，您可以從 Azure AD 將角色*繫結*至使用者或群組。 這些 Kubernetes 角色型存取控制 (RBAC) 將在下一節中討論。 下圖顯示 Azure AD 的整合以及如何控制對資源的存取：

![對 Azure Active Directory 與 AKS 的整合進行叢集層級驗證](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 開發人員向 Azure AD 進行驗證。
1. Azure AD 權杖發行端點會發行存取權杖。
1. 開發人員使用 Azure AD 權杖執行動作，例如 `kubectl create pod`
1. Kubernetes 向 Azure Active Directory 驗證權杖，並擷取開發人員的群組成員資格。
1. 套用 Kubernetes 角色型存取控制 (RBAC) 和叢集原則。
1. 開發人員的要求是否成功取決於根據先前的 Azure AD 群組成員資格以及 Kubernetes RBAC 和原則的驗證。

若要建立使用 Azure AD 的 AKS 叢集，請參閱[整合 Azure Active Directory 與 AKS][aks-aad]。

## <a name="use-role-based-access-controls-rbac"></a>使用角色型存取控制 (RBAC)

**最佳做法指引** - 使用 Kubernetes RBAC 定義使用者或群組對於叢集中的資源所擁有的權限。 建立會指派最少量必要權限的角色和繫結。 與 Azure AD 整合，讓使用者狀態或群組成員資格中的任何變更都會自動更新，並且讓叢集資源的存取權保持在最新狀態。

在 Kubernetes 中，您可以對叢集中的資源存取進行更精細的控制。 權限可定義於叢集層級上，或定義至特定的命名空間。 您可以定義可管理哪些資源，以及具備哪些權限。 這些角色接著會套用至具有系結的使用者或群組。 如需*角色*、 *ClusterRoles*和系*結的詳細資訊，請*參閱[Azure Kubernetes Service （AKS）的存取和身分識別選項][aks-concepts-identity]。

例如，您可以在名為 *finance-app* 的命名空間中建立會授與完整資源存取權的角色，如下列範例 YAML 資訊清單所示：

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

接著會建立接著，將 Azure AD 使用者*developer1\@contoso.com*系結至接著，如下列 YAML 資訊清單所示：

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

當*developer1\@contoso.com*向 AKS 叢集進行驗證時，他們具有*財務應用程式*命名空間中資源的完整許可權。 如此，您將可透過邏輯方式來區分及控制對資源的存取。 如上一節的討論，Kubernetes RBAC 應與 Azure AD 整合搭配使用。

若要瞭解如何使用 Azure AD 群組來控制使用 RBAC 的 Kubernetes 資源存取，請參閱[使用角色型存取控制來控制叢集資源的存取和 AKS 中的 Azure Active Directory][azure-ad-rbac]身分識別。

## <a name="use-pod-identities"></a>使用 Pod 身分識別

**最佳做法指引** - 請勿使用 Pod 或容器映像內的固定認證，因為這些認證有外洩或濫用的風險。 請改用 Pod 身分識別，以使用中央 Azure AD 身分識別解決方案自動要求存取權。 Pod 身分識別僅適用于 Linux Pod 和容器映射。

當 Pod 需要存取其他 Azure 服務時 (例如 Cosmos DB、Key Vault 或 Blob 儲存體)，Pod 將需要存取認證。 這些存取認證可以使用容器映像來定義或插入作為 Kubernetes 祕密，但必須以手動方式建立並指派。 認證通常會跨 Pod 重複使用，而不會定期輪替。

適用于 Azure 資源的受控識別（目前實作為相關聯的 AKS 開放原始碼專案）可讓您透過 Azure AD 自動要求存取服務。 您不需手動定義 Pod 的認證，因為 Pod 會即時要求存取權杖，並且可用該權杖來存取其獲指派的服務。 在 AKS 中，叢集操作員會部署兩個元件，讓 Pod 能夠使用受控識別：

* **節點管理身分識別 (NMI) 伺服器**是在 AKS 叢集中的每個節點上以 DaemonSet 形式執行的 Pod。 NMI 伺服器會接聽 Pod 對 Azure 服務的要求。
* **受控識別控制器 (MIC)** 是一個中央 Pod，有權查詢 Kubernetes API 伺服器，以及檢查對應至 Pod 的 Azure 身分識別對應。

當 Pod 要求存取 Azure 服務時，網路規則即會將流量重新導向至節點管理身分識別 (NMI) 伺服器。 NMI 伺服器會根據 Pod 的遠端位址識別要求存取 Azure 服務的 Pod，並查詢受控識別控制器 (MIC)。 MIC 會檢查 AKS 叢集中的 Azure 身分識別對應，然後，NMI 伺服器會根據 Pod 的身分識別對應向 Azure Active Directory (AD) 要求存取權杖。 Azure AD 會將存取權提供給 NMI 伺服器，繼而傳回給 Pod。 此存取權杖隨後可供 Pod 用來要求存取 Azure 中的服務。

在下列範例中，開發人員會建立一個使用受控識別來要求存取 Azure SQL Server 執行個體的 Pod：

![Pod 身分識別可讓 Pod 自動要求存取其他服務](media/operator-best-practices-identity/pod-identities.png)

1. 叢集操作員首先建立在 Pod 要求存取服務時可用來對應身分識別的服務帳戶。
1. 部署 NMI 伺服器和 MIC，以將 Pod 的任何存取權杖要求轉送至 Azure AD。
1. 開發人員使用受控識別部署了透過 NMI 伺服器要求存取權杖的 Pod。
1. 權杖傳回至 Pod，並用來存取 Azure SQL Server 執行個體。

> [!NOTE]
> 受控 pod 身分識別是一個開放原始碼專案，不受 Azure 技術支援的支援。

若要使用 pod 身分識別，請參閱[Kubernetes 應用程式的 Azure Active Directory][aad-pod-identity]身分識別。

## <a name="next-steps"></a>後續步驟

這篇最佳做法文章主要討論叢集和資源的驗證和授權。 若要實作這些最佳做法，請參閱下列文章：

* [整合 Azure Active Directory 與 AKS][aks-aad]
* [透過 AKS 使用適用于 Azure 資源的受控識別][aad-pod-identity]

如需 AKS 中叢集作業的相關詳細資訊，請參閱下列最佳作法：

* [多租使用者和叢集隔離][aks-best-practices-cluster-isolation]
* [基本 Kubernetes 排程器功能][aks-best-practices-scheduler]
* [Advanced Kubernetes 排程器功能][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
