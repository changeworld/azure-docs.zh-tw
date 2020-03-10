---
title: 設定容器即時資料的 Azure 監視器（預覽） |Microsoft Docs
description: 本文說明如何設定容器記錄（stdout/stderr）和事件的即時查看，而不需使用 kubectl 與容器的 Azure 監視器。
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395410"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>如何設定即時資料（預覽）功能

若要使用 Azure Kubernetes Service （AKS）叢集的容器 Azure 監視器來查看即時資料（預覽），您必須設定驗證以授與存取 Kubernetes 資料的許可權。 此安全性設定可讓您透過 Kubernetes API，直接在 Azure 入口網站中即時存取您的資料。

這項功能支援下列方法來控制對記錄、事件和計量的存取：

- 已啟用不具 Kubernetes RBAC 授權的 AKS
- 已使用 Kubernetes RBAC 授權啟用的 AKS
    - 使用叢集角色系結 **[ClusterMonitoringUser](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)** 設定的 AKS
- AKS 已啟用 Azure Active Directory （AD） SAML 型單一登入

這些指示需要 Kubernetes 叢集的系統管理存取權，而且如果設定為使用 Azure Active Directory （AD）進行使用者驗證，Azure AD 的系統管理存取權。  

本文說明如何設定驗證，以控制從叢集對即時資料（預覽）功能的存取：

- 以角色為基礎的存取控制（RBAC）已啟用 AKS 叢集
- Azure Active Directory 整合式 AKS 叢集。 

>[!NOTE]
>此功能不支援已啟用為[私人](https://azure.microsoft.com/updates/aks-private-cluster/)叢集的 AKS 叢集。 此功能依賴從瀏覽器透過 proxy 伺服器直接存取 Kubernetes API。 啟用網路安全性以封鎖此 proxy 的 Kubernetes API 將會封鎖此流量。 

>[!NOTE]
>這項功能適用于所有 Azure 區域，包括 Azure 中國。 Azure 美國政府目前無法使用此功能。

## <a name="authentication-model"></a>驗證模型

即時資料（預覽）功能會利用 Kubernetes API，與 `kubectl` 命令列工具相同。 Kubernetes API 端點會利用自我簽署憑證，您的瀏覽器將無法驗證。 此功能會利用內部 proxy 來驗證 AKS 服務的憑證，以確保流量受到信任。

Azure 入口網站會提示您驗證 Azure Active Directory 叢集的登入認證，並在叢集建立期間將您重新導向至用戶端註冊設定（在本文中重新設定）。 此行為類似于 `kubectl`所需的驗證程式。 

>[!NOTE]
>您叢集的授權是由 Kubernetes 和其設定所在的安全性模型所管理。 存取這項功能的使用者需要下載 Kubernetes 設定（*kubeconfig*）的許可權，類似于執行 `az aks get-credentials -n {your cluster name} -g {your resource group}`。 此設定檔包含**Azure Kubernetes Service 叢集使用者角色**的授權和驗證權杖，如果已啟用 Azure RBAC 的 AKS 叢集，而未啟用 rbac 授權。 其中包含使用 Azure Active Directory （AD） SAML 型單一登入啟用 AKS 時，Azure AD 和用戶端註冊詳細資料的相關資訊。

>[!IMPORTANT]
>此功能的使用者需要叢集的[Azure Kubernetes Cluster 使用者角色](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions)，才能下載 `kubeconfig` 並使用此功能。 使用者**不**需要叢集的「參與者」存取權，就能利用這項功能。 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>使用 clusterMonitoringUser 搭配已啟用 RBAC 的叢集

為了避免在[啟用 RBAC](#configure-kubernetes-rbac-authorization)授權後，套用其他設定變更以允許 Kubernetes 使用者角色系結**ClusterUser**存取即時資料（預覽）功能的需求，AKS 已新增名為**clusterMonitoringUser**的新 Kubernetes 叢集角色系結。 此叢集角色系結具有現成的所有必要許可權，可存取 Kubernetes API 和使用即時資料（預覽）功能的端點。

若要將即時資料（預覽）功能與這個新使用者搭配使用，您必須是 AKS 叢集資源上[參與者](../../role-based-access-control/built-in-roles.md#contributor)角色的成員。 啟用容器的 Azure 監視器，預設會設定為使用此使用者進行驗證。 如果 clusterMonitoringUser 角色系結不存在於叢集上，則會改用**clusterUser**來進行驗證。

AKS 已在2020年1月發行這個新的角色系結，因此在1月2020日前建立的叢集沒有該系結。 如果您有在2020年1月之前建立的叢集，可以藉由在叢集上執行 PUT 作業來將新的**clusterMonitoringUser**新增至現有的叢集，或在叢集上執行任何其他作業，以在叢集上執行 put 作業，例如更新叢集版本。

## <a name="kubernetes-cluster-without-rbac-enabled"></a>已啟用不具 RBAC 的 Kubernetes 叢集

如果您的 Kubernetes 叢集不是使用 Kubernetes RBAC 授權所設定或已與 Azure AD 單一登入整合，則您不需遵循這些步驟。 這是因為您在非 RBAC 設定中預設具有系統管理許可權。

## <a name="configure-kubernetes-rbac-authorization"></a>設定 Kubernetes RBAC 授權

當您啟用 Kubernetes RBAC 授權時，會使用兩個使用者： **clusterUser**和**ClusterAdmin**來存取 Kubernetes API。 這類似于在沒有系統管理選項的情況下執行 `az aks get-credentials -n {cluster_name} -g {rg_name}`。 這表示必須將**clusterUser**的存取權授與 Kubernetes API 中的端點。

下列範例步驟將示範如何從這個 yaml 設定範本設定叢集角色繫結。

1. 複製並貼上這個 yaml 檔案，然後將它儲存為 LogReaderRBAC.yaml。  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
        - apiGroups: ["", "metrics.k8s.io", "extensions", "apps"] 
          resources: 
             - "pods/log" 
             - "events" 
             - "nodes" 
             - "pods" 
             - "deployments" 
             - "replicasets" 
          verbs: ["get", "list"] 
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
       kind: ClusterRole 
       name: containerHealth-log-reader 
       apiGroup: rbac.authorization.k8s.io 
    subjects: 
    - kind: User 
      name: clusterUser 
      apiGroup: rbac.authorization.k8s.io 
    ```

2. 若要更新您的設定，請執行下列命令： `kubectl apply -f LogReaderRBAC.yaml`。

>[!NOTE] 
> 如果您已將舊版的 `LogReaderRBAC.yaml` 檔案套用至您的叢集，請複製並貼上上述步驟1所示的新程式碼來更新它，然後執行步驟2所示的命令，將它套用到您的叢集。

## <a name="configure-ad-integrated-authentication"></a>設定 AD 整合式驗證 

設定為使用 Azure Active Directory （AD）進行使用者驗證的 AKS 叢集會利用存取這項功能之人員的登入認證。 在此設定中，您可以使用 Azure AD 驗證權杖來登入 AKS 叢集。

Azure AD 用戶端註冊必須重新設定，才能讓 Azure 入口網站將授權頁面重新導向為信任的重新導向 URL。 然後，來自 Azure AD 的使用者會透過**ClusterRoles**和**ClusterRoleBindings**直接授與相同 Kubernetes API 端點的存取權。 

如需有關 Kubernetes 中 advanced security 設定的詳細資訊，請參閱[Kubernetes 檔](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)。 

>[!NOTE]
>如果您要建立已啟用 RBAC 的新叢集，請參閱[整合 Azure Active Directory 與 Azure Kubernetes Service](../../aks/azure-ad-integration.md) ，並遵循步驟來設定 Azure AD 驗證。 在建立用戶端應用程式的步驟中，該區段中的附注會針對符合下列步驟3中所指定容器的 Azure 監視器，反白顯示您需要建立的兩個重新導向 Url。

### <a name="client-registration-reconfiguration"></a>用戶端註冊重新設定

1. 在 Azure 入口網站的**Azure Active Directory > 應用程式註冊**底下的 Azure AD 中，找出 Kubernetes 叢集的用戶端註冊。

2. 從左側窗格中選取 [**驗證**]。 

3. 將兩個重新導向 Url 新增至此清單做為**Web**應用程式類型。 第一個 [基底 URL] 值應該是 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，而第二個 [基底 URL] 值應該是 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`。

    >[!NOTE]
    >如果您在 Azure 中國使用這項功能，第一個 [基底 URL] 值應該是 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，而第二個 [基底 URL] 值應該是 [`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`]。 
    
4. 註冊重新導向 Url 之後，請在 [**隱含授**與] 底下，選取 [**存取權杖**和**識別碼權杖**] 選項，然後儲存您的變更。

>[!NOTE]
>只有在初始部署新的 AKS 叢集時，才可以完成使用單一登入的 Azure Active Directory 來設定驗證。 您無法針對已經部署的 AKS 叢集設定單一登入。
  
>[!IMPORTANT]
>如果您已使用更新的 URI 重新設定使用者驗證 Azure AD，請清除瀏覽器的快取，以確保已下載並套用更新的驗證權杖。

## <a name="grant-permission"></a>授與權限

每個 Azure AD 帳戶都必須被授與 Kubernetes 中適當 Api 的許可權，才能存取即時資料（預覽）功能。 授與 Azure Active Directory 帳戶的步驟類似于[KUBERNETES RBAC 驗證](#configure-kubernetes-rbac-authorization)一節中所述的步驟。 將 yaml 設定範本套用到您的叢集之前，請以所需的使用者取代**ClusterRoleBinding**下的**clusterUser** 。 

>[!IMPORTANT]
>如果您授與的 RBAC 系結的使用者位於相同的 Azure AD 租使用者中，請根據 userPrincipalName 指派許可權。 如果使用者位於不同的 Azure AD 租使用者中，請查詢並使用 objectId 屬性。

如需設定 AKS 叢集**ClusterRoleBinding**的其他協助，請參閱[建立 RBAC](../../aks/azure-ad-integration-cli.md#create-rbac-binding)系結。

## <a name="next-steps"></a>後續步驟

現在您已設定驗證，您可以從叢集即時查看[計量](container-insights-livedata-metrics.md)、[部署](container-insights-livedata-deployments.md)和[事件和記錄](container-insights-livedata-overview.md)。
