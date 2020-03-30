---
title: 為容器設置 Azure 監視器 即時資料（預覽） |微軟文檔
description: 本文介紹如何在不使用容器的 Azure 監視器使用 kubectl 的情況下設置容器日誌（stdout/stderr）和事件的即時視圖。
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: f19071ca642cd229cbd7d49b4eab90c970672eee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275369"
---
# <a name="how-to-set-up-the-live-data-preview-feature"></a>如何設置即時資料（預覽）功能

要使用 Azure 監視器查看 Azure 庫伯內斯服務 （AKS） 群集中的容器的即時資料（預覽），需要配置身份驗證以授予訪問庫貝內斯資料的許可權。 此安全配置允許直接通過 Azure 門戶中的 Kubernetes API 即時訪問資料。

此功能支援以下方法來控制對日誌、事件和指標的訪問：

- 已啟用不具 Kubernetes RBAC 授權的 AKS
- 已使用 Kubernetes RBAC 授權啟用的 AKS
    - AKS 配置群集角色綁定**[群集監視使用者](https://docs.microsoft.com/rest/api/aks/managedclusters/listclustermonitoringusercredentials?view=azurermps-5.2.0)**
- AKS 啟用 Azure 活動目錄 （AD） 基於 SAML 的單號登錄

這些說明需要對 Kubernetes 群集進行管理訪問，如果配置為使用 Azure 活動目錄 （AD） 進行使用者身份驗證，則對 Azure AD 進行管理訪問。  

本文介紹如何配置身份驗證以控制對群集中即時資料（預覽）功能的訪問：

- 啟用基於角色的存取控制 （RBAC） 支援 AKS 群集
- Azure 活動目錄集成 AKS 群集。 

>[!NOTE]
>此功能不支援作為[專用群集](https://azure.microsoft.com/updates/aks-private-cluster/)啟用的 AKS 群集。 此功能依賴于通過瀏覽器通過代理伺服器直接存取 Kubernetes API。 啟用網路安全性來阻止 Kubernetes API 來自此代理將阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 區域（包括 Azure 中國）都可用。 它當前不在 Azure 美國政府中可用。

## <a name="authentication-model"></a>驗證模型

即時資料（預覽）功能使用 Kubernetes API，與`kubectl`命令列工具相同。 Kubernetes API 終結點使用自簽章憑證，您的瀏覽器將無法驗證該證書。 此功能利用內部代理使用 AKS 服務驗證憑證，確保流量受信任。

Azure 門戶會提示您驗證 Azure 活動目錄群集的登錄憑據，並在群集創建期間（並在本文中重新配置）將您重定向到用戶端註冊設置。 此行為與 所需的`kubectl`身份驗證過程類似。 

>[!NOTE]
>群集的授權由 Kubernetes 及其配置的安全模型管理。 訪問此功能的使用者需要許可權才能下載庫貝內斯配置 *（kubeconfig），* 類似于運行`az aks get-credentials -n {your cluster name} -g {your resource group}`。 此設定檔包含**Azure Kubernetes 服務群集使用者角色**的授權和身份驗證權杖，在啟用 Azure RBAC 和 AKS 群集的情況下，未啟用 RBAC 授權。 當使用基於 Azure 活動目錄 （AD） SAML 的單號登錄啟用 AKS 時，它包含有關 Azure AD 和用戶端註冊詳細資訊的資訊。

>[!IMPORTANT]
>此功能的使用者需要[Azure Kubernetes 群集使用者角色](../../azure/role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role permissions)到群集才能下載`kubeconfig`並使用此功能。 使用者**不需要**參與者訪問群集才能利用此功能。 

## <a name="using-clustermonitoringuser-with-rbac-enabled-clusters"></a>將群集監視使用者與啟用 RBAC 的群集一起使用

為了消除需要應用其他配置更改，以允許 Kubernetes 使用者角色綁定**群集使用者**訪問即時資料（預覽）功能後啟用[RBAC](#configure-kubernetes-rbac-authorization)授權， AKS 添加了一個新的 Kubernetes 群集角色綁定稱為**群集監視使用者**。 此群集角色綁定具有訪問 Kubernetes API 的所有必要許可權，以及使用即時資料（預覽）功能的終結點。

為了使用此新使用者使用即時資料（預覽）功能，您需要成為 AKS 群集資源上的["參與者"](../../role-based-access-control/built-in-roles.md#contributor)角色的成員。 預設情況下，容器的 Azure 監視器配置為使用此使用者進行身份驗證。 如果群集上不存在群集監視使用者角色綁定，則群集**User**將改為用於身份驗證。

AKS 在 2020 年 1 月發佈了此新角色綁定，因此在 2020 年 1 月之前創建的群集沒有該綁定。 如果群集是在 2020 年 1 月之前創建的，則可以通過在群集上執行 PUT 操作或在群集 tha 上執行任何其他操作（如更新群集版本）將新**群集監視使用者**添加到現有群集。

## <a name="kubernetes-cluster-without-rbac-enabled"></a>已啟用不具 RBAC 的 Kubernetes 叢集

如果您的 Kubernetes 叢集不是使用 Kubernetes RBAC 授權所設定或已與 Azure AD 單一登入整合，則您不需遵循這些步驟。 這是因為預設情況下，在非 RBAC 配置中，您具有管理許可權。

## <a name="configure-kubernetes-rbac-authorization"></a>配置庫伯內斯 RBAC 授權

啟用 Kubernetes RBAC 授權時，將使用兩個使用者：**群集使用者**和**群集管理員**來訪問庫伯奈斯 API。 這類似于在沒有管理選項`az aks get-credentials -n {cluster_name} -g {rg_name}`的情況下運行。 這意味著**群集使用者**需要被授予對 Kubernetes API 中的端點的存取權限。

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

2. 要更新配置，請運行以下命令： `kubectl apply -f LogReaderRBAC.yaml`。

>[!NOTE] 
> 如果將`LogReaderRBAC.yaml`檔的早期版本應用於群集，請通過複製和粘貼上述步驟 1 中顯示的新代碼來更新它，然後運行步驟 2 中顯示的命令，將其應用於群集。

## <a name="configure-ad-integrated-authentication"></a>配置廣告集成身份驗證 

配置為使用 Azure 活動目錄 （AD） 進行使用者身份驗證的 AKS 群集利用訪問此功能的人員的登錄憑據。 在此配置中，可以使用 Azure AD 身份驗證權杖登錄到 AKS 群集。

必須重新配置 Azure AD 用戶端註冊，以允許 Azure 門戶將授權頁重定向為受信任的重定向 URL。 然後，Azure AD 的使用者通過**群集角色**和**群集角色綁定**直接授予對同一庫伯奈斯 API 終結點的存取權限。 

有關庫伯內斯高級安全設置的詳細資訊，請查看[庫伯內斯文檔](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)。 

>[!NOTE]
>如果要創建新的啟用 RBAC 的群集，請參閱[將 Azure 活動目錄與 Azure 庫伯奈斯服務集成](../../aks/azure-ad-integration.md)，然後按照步驟配置 Azure AD 身份驗證。 在創建用戶端應用程式的步驟中，該部分中的注釋突出顯示了為 Azure 監視器為與下面的步驟 3 中指定的容器創建的兩個重定向 URL。

### <a name="client-registration-reconfiguration"></a>用戶端註冊重新配置

1. 在**Azure 活動目錄> Azure 門戶中的應用註冊**下，在 Azure AD 中查找 Kubernetes 群集的用戶端註冊。

2. 從左側窗格中選擇**身份驗證**。 

3. 將兩個重定向 URL 添加到此清單作為**Web**應用程式類型。 第一個基本 URL 值`https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`應為，第二個基本 URL`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`值應為 。

    >[!NOTE]
    >如果在 Azure 中國中使用此功能，則第一個基本 URL 值應為`https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`，第二個基本 URL 值`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`應為 。 
    
4. 註冊重定向 URL 後，在 **"隱式授予"** 下，選擇 **"訪問權杖**"和 **"ID 權杖"** 選項，然後保存更改。

>[!NOTE]
>只能在初始部署新的 AKS 群集時完成使用 Azure 活動目錄進行單簽名的身份驗證。 您無法針對已經部署的 AKS 叢集設定單一登入。
  
>[!IMPORTANT]
>如果使用更新的 URI 重新配置 Azure AD 進行使用者身份驗證，請清除瀏覽器的緩存，以確保下載和應用更新的身份驗證權杖。

## <a name="grant-permission"></a>授與權限

每個 Azure AD 帳戶都必須被授予 Kubernetes 中相應 API 的許可權，才能訪問即時資料（預覽）功能。 授予 Azure 活動目錄帳戶的步驟與[Kubernetes RBAC 身份驗證](#configure-kubernetes-rbac-authorization)部分中描述的步驟類似。 在將 yaml 配置範本應用於群集之前，請將**群集角色綁定**下的**群集使用者**替換為所需的使用者。 

>[!IMPORTANT]
>如果為其授予 RBAC 綁定的使用者位於同一 Azure AD 租戶中，則根據使用者主體名稱分配許可權。 如果使用者位於其他 Azure AD 租戶中，請查詢並使用 objectId 屬性。

有關配置 AKS 群集**群集角色綁定**的其他説明，請參閱[創建 RBAC 綁定](../../aks/azure-ad-integration-cli.md#create-rbac-binding)。

## <a name="next-steps"></a>後續步驟

現在，您已經擁有設置身份驗證，您可以在群集中即時查看[指標](container-insights-livedata-metrics.md)、[部署](container-insights-livedata-deployments.md)[、事件和日誌](container-insights-livedata-overview.md)。
