---
title: 概念 - Azure Kubernetes Service (AKS) 中的存取與身分識別
description: 深入了解 Azure Kubernetes Service (AKS) 中的存取與身分識別，包括 Azure Active Directory 整合、Kubernetes 角色型存取控制 (RBAC) 及角色和繫結。
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: e4945535417f7d8d33308121267ba97e1f835e13
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259600"
---
# <a name="access-and-identity-options-for-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 的存取與身分識別選項

向 Kubernetes 叢集進行驗證及保護 Kubernetes 叢集的方式有很多種。 若使用角色型存取控制 (RBAC)，您可以只授與使用者或群組所需資源的存取權。 透過 Azure Kubernetes Service (AKS)，您可以使用 Azure Active Directory 進一步加強安全性和權限結構。 這些方法可協助您保護應用程式工作負載和客戶資料。

本文所介紹的核心概念，可協助您在 AKS 中驗證和指派權限：

- [Kubernetes 服務帳戶](#kubernetes-service-accounts)
- [Azure Active Directory 整合](#azure-active-directory-integration)
- [角色型存取控制 (RBAC)](#role-based-access-controls-rbac)
- [Roles 和 ClusterRoles](#roles-and-clusterroles)
- [RoleBindings 和 ClusterRoleBindings](#rolebindings-and-clusterrolebindings)

## <a name="kubernetes-service-accounts"></a>Kubernetes 服務帳戶

Kubernetes 的其中一個主要使用者類型是「服務帳戶」。 服務帳戶存在 Kubernetes API 中，並受其管理。 服務帳戶的認證會儲存為 Kubernetes 祕密，如此便可讓已獲授權的 Pod 用這些認證與 API 伺服器進行通訊。 大部分的 API 要求會為服務帳戶或一般使用者帳戶提供驗證權杖。

一般使用者帳戶會允許系統管理人員或開發人員適用的傳統存取，而不只是允許服務和程序的存取。 Kubernetes 本身並不提供一般使用者帳戶和密碼儲存所在的身分識別管理解決方案。 而是將外部身分識別解決方案整合到 Kubernetes。 對 AKS 叢集而言，此整合的身分識別解決方案就是 Azure Active Directory。

如需 Kubernetes 中身分識別選項的詳細資訊，請參閱[Kubernetes authentication][kubernetes-authentication]。

## <a name="azure-active-directory-integration"></a>Azure Active Directory 整合

AKS 叢集的安全性可以透過整合的 Azure Active Directory (AD) 來加強。 以數十年企業身分識別管理技術為基礎，Azure AD 是多租用戶雲端式目錄，也是身分識別管理服務，可將核心目錄服務、應用程式存取管理、身分識別保護結合在單一解決方案中。 透過 Azure AD，您可以將內部部署身分識別整合至 AKS 叢集，以對帳戶管理和安全性提供單一來源。

![整合 Azure Active Directory 與 AKS 叢集](media/concepts-identity/aad-integration.png)

透過與 Azure AD 整合的 AKS 叢集，您可以允許使用者或群組存取命名空間內或叢集上的 Kubernetes 資源。 若要取得 `kubectl` 設定內容，使用者可以執行[az aks get-認證][az-aks-get-credentials]命令。 當使用者之後透過 `kubectl` 與 AKS 叢集互動時，系統會提示他們使用其 Azure AD 認證登入。 此方法可對使用者帳戶管理和密碼認證提供單一來源。 使用者只能存取叢集系統管理員所定義的資源。

AKS 叢集中的 Azure AD 驗證會使用 OpenID Connect (在 OAuth 2.0 通訊協定上方建置的身分識別層)。 OAuth 2.0 會定義取得及使用存取權杖的機制，以存取受保護的資源，而 OpenID Connect 會實作驗證，以作為 OAuth 2.0 授權程序的延伸模組。 如需 OpenID Connect 的詳細資訊，請參閱[OPEN ID connect 檔][openid-connect]。 若要驗證透過 OpenID Connect 從 Azure AD 取得的驗證權杖，AKS 叢集會使用 Kubernetes Webhook 權杖驗證。 如需詳細資訊，請參閱[Webhook 權杖驗證檔][webhook-token-docs]。

## <a name="role-based-access-controls-rbac"></a>角色型存取控制 (RBAC)

若要更細微地篩選使用者可以執行的動作，Kubernetes 會使用角色型存取控制 (RBAC)。 此控制機制可讓您指派權限給使用者或使用者群組，以執行像是建立或修改資源，或檢視執行中應用程式工作負載的記錄等動作。 這些權限可以只限於單一命名空間，或授與給整個 AKS 叢集。 透過 Kubernetes RBAC，您可以建立「角色」來定義權限，然後藉由「角色繫結」將這些角色指派給使用者。

如需詳細資訊，請參閱[使用 RBAC 授權][kubernetes-rbac]。

### <a name="azure-role-based-access-controls-rbac"></a>Azure 角色型存取控制 (RBAC)
另一個控制資源存取的機制是 Azure 角色型存取控制 (RBAC)。 Kubernetes RBAC 會用於處理您 AKS 叢集內的資源，而 Azure RBAC 會用於處理您 Azure 訂用帳戶內的資源。 透過 Azure RBAC，您可以建立「角色定義」來概述要套用的權限。 接著，使用者或群組會在特定「範圍」中獲派此角色定義，此範圍可能是個別的資源、資源群組或訂用帳戶。

如需詳細資訊，請參閱[什麼是 AZURE RBAC？][azure-rbac]

## <a name="roles-and-clusterroles"></a>Roles 和 ClusterRoles

使用 Kubernetes RBAC 將權限指派給使用者之前，您必須先將這些權限定義為「角色」。 Kubernetes 角色會「授與」權限。 這裡沒有「拒絕」權限的概念。

Roles (角色) 會用來授與一個命名空間內的權限。 如果您需要將權限授與整個叢集，或授與指定命名空間外部的叢集資源，可以改用 ClusterRoles。

ClusterRole 會以同樣方式將權限授與資源，但這些權限可以套用至整個叢集中的資源，而不是特定命名空間中的資源。

## <a name="rolebindings-and-clusterrolebindings"></a>RoleBindings 和 ClusterRoleBindings

一旦將角色定義為可授與權限給資源，您就可以透過 RoleBinding 指派這些 Kubernetes RBAC 權限。 如果您的 AKS 叢集與 Azure Active Directory 整合，繫結就是 Azure AD 使用者得到權限來執行叢集內動作的方式。

角色繫結會用來為指定命名空間指派角色。 此方法可讓您以邏輯方式區隔單一 AKS 叢集，讓使用者只能存取獲派命名空間中的應用程式資源。 如果您需要將角色繫結到整個叢集，或繫結到指定命名空間外部的叢集資源，可以改用 ClusterRoleBindings。

ClusterRoleBinding 會以同樣方式將角色繫結到使用者，但這些角色可以套用至整個叢集中的資源，而不是特定命名空間中的資源。 此方法可讓您允許系統管理員或支援工程師存取 AKS 叢集中的所有資源。

## <a name="next-steps"></a>後續步驟

若要開始使用 Azure AD 和 Kubernetes RBAC，請參閱[整合 Azure Active Directory 與 AKS][aks-aad]。

如需相關的最佳作法，請參閱[AKS 中驗證和授權的最佳做法][operator-best-practices-identity]。

如需關於 Kubernetes 及 AKS 核心概念的詳細資訊，請參閱下列文章：

- [Kubernetes/AKS 叢集和工作負載][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 安全性][aks-concepts-security]
- [Kubernetes/AKS 虛擬網路][aks-concepts-network]
- [Kubernetes/AKS 儲存體][aks-concepts-storage]
- [Kubernetes/AKS scale][aks-concepts-scale]

<!-- LINKS - External -->
[kubernetes-authentication]: https://kubernetes.io/docs/reference/access-authn-authz/authentication
[webhook-token-docs]: https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - Internal -->
[openid-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[aks-aad]: azure-ad-integration-cli.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-identity]: operator-best-practices-identity.md
