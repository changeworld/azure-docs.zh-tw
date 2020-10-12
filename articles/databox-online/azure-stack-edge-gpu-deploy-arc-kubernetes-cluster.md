---
title: 在 Azure Stack Edge Pro GPU 裝置上啟用 Kubernetes 上的 Azure Arc |Microsoft Docs
description: 說明如何在 Azure Stack Edge Pro GPU 裝置上的現有 Kubernetes 叢集上啟用 Azure Arc。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/01/2020
ms.author: alkohli
ms.openlocfilehash: 423345739ca5c078fbff4f267e1e8a118abf107c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903201"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>在 Azure Stack Edge Pro GPU 裝置上啟用 Kubernetes 叢集上的 Azure Arc

本文說明如何在 Azure Stack Edge Pro 裝置上的現有 Kubernetes 叢集上啟用 Azure Arc。 

此程式適用于已 [在 Azure Stack Edge Pro 裝置上審核 Kubernetes 工作負載](azure-stack-edge-gpu-kubernetes-workload-management.md) 的人員，並熟悉 [Azure Arc 啟用的 Kubernetes (Preview) ](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)的概念。


## <a name="prerequisites"></a>必要條件

在 Kubernetes 叢集上啟用 Azure Arc 之前，請確定您已在 Azure Stack Edge Pro 裝置和將用來存取裝置的用戶端上完成下列必要條件：

### <a name="for-device"></a>針對裝置

1. 您有1個節點 Azure Stack Edge Pro 裝置的登入認證。
    1. 裝置已啟用。 請參閱 [啟用裝置](azure-stack-edge-gpu-deploy-activate.md)。
    1. 裝置具有透過 Azure 入口網站設定的計算角色，且具有 Kubernetes 叢集。 請參閱 [設定計算](azure-stack-edge-gpu-deploy-configure-compute.md)。

1. 您擁有訂用帳戶的擁有者存取權。 在您的服務主體的角色指派步驟期間，您需要此存取權。
 

### <a name="for-client-accessing-the-device"></a>適用于存取裝置的用戶端

1. 您有將用來存取 Azure Stack Edge Pro 裝置的 Windows 用戶端系統。
  
    - 用戶端正在執行 Windows PowerShell 5.0 或更新版本。 若要下載 Windows PowerShell 的最新版本，請移至 [ [安裝 Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)。
    
    - 您也可以讓任何其他用戶端使用 [支援的作業系統](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 。 本文說明使用 Windows 用戶端的程式。 
    
1. 您已完成在 [Azure Stack Edge Pro 裝置上存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集所述的程式。 您已經：
    
    - 安裝 `kubectl` 在用戶端上  <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - 請確定 `kubectl` 用戶端版本的 Azure Stack Edge Pro 裝置上執行的 Kubernetes 主要版本，不會有多個版本的扭曲。 
      - 用 `kubectl version` 來檢查用戶端上執行的 kubectl 版本。 請記下完整版。
      - 在 Azure Stack Edge Pro 裝置的本機 UI 中，移至 [ **軟體更新** ] 並記下 Kubernetes 伺服器版本號碼。 
    
        ![確認 Kubernetes 伺服器版本號碼](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - 確認這兩個版本相容。 

<!-- az cli version requirements-->

## <a name="register-kubernetes-resource-providers"></a>註冊 Kubernetes 資源提供者

在 Kubernetes 叢集上啟用 Azure Arc 之前，您必須先針對您的訂用帳戶啟用並註冊 `Microsoft.Kubernetes` `Microsoft.KubernetesConfiguration` 。 

1. 若要啟用資源提供者，請在 Azure 入口網站中，移至您打算用於部署的訂用帳戶。 前往 **資源提供者**。 
1. 在右窗格中，搜尋您想要新增的提供者。 在此範例中為 `Microsoft.Kubernetes` 和 `Microsoft.KubernetesConfiguration` 。

    ![註冊 Kubernetes 資源提供者](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. 選取資源提供者，然後從命令列頂端選取 [ **註冊**]。 註冊需要幾分鐘的時間。 

    ![註冊 Kubernetes 資源提供者](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. 重新整理 UI 直到您看到資源提供者已註冊為止。 針對這兩個資源提供者重複此程式。
    
    ![註冊 Kubernetes 資源提供者](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

您也可以透過註冊資源提供者 `az cli` 。 如需詳細資訊，請參閱為 [Azure Arc 啟用註冊兩個提供者 Kubernetes](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)

## <a name="create-service-principal-assign-role"></a>建立服務主體、指派角色

1. 確定您擁有的 `Subscription ID` 資源組名，以及用於 Azure Stack Edge 服務的資源部署的資源組名。 若要取得訂用帳戶識別碼，請移至 Azure 入口網站中的 Azure Stack Edge 資源。 流覽至概要 **> Essentials**。

    ![取得訂用帳戶識別碼](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    若要取得資源組名，請移至 [ **屬性**]。

    ![取得資源組名](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. 若要建立服務主體，請透過使用下列命令 `az cli` 。

    `az ad sp create-for-rbac --skip assignment --name "<Informative name for service principal>"`  

    如需有關如何登入 `az cli` 、[開始 Cloud Shell 的](../cloud-shell/quickstart-powershell.md?view=azure-cli-latest#start-cloud-shell)詳細資訊 Azure 入口網站

    範例如下。 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. 請記下 `appID` 、 `name` 、和， `password` 因為在 `tenantID` 下一個命令中，您將使用此做為輸入。

1. 建立新的服務主體之後，請將 `Kubernetes Cluster - Azure Arc Onboarding` 角色指派給新建立的主體。 這是內建的 Azure 角色 (在命令) 中以有限許可權使用角色識別碼。 使用下列命令：

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    範例如下。
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    如需有關如何建立服務主體和執行角色指派的詳細資訊，請參閱 [建立已啟用 Azure Arc 的上線服務主體](https://docs.microsoft.com/azure/azure-arc/kubernetes/create-onboarding-service-principal)中的步驟。


## <a name="enable-arc-on-kubernetes-cluster"></a>在 Kubernetes 叢集上啟用 Arc

遵循下列步驟來設定 Azure Arc 管理的 Kubernetes 叢集：

1. [連接到您裝置的 PowerShell 介面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 。

1. 輸入：

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`

    若要在 Azure Stack Edge Pro 裝置上部署 Azure Arc，請確定您使用的是 [Azure Arc 的支援區域](../azure-arc/kubernetes/overview.md#supported-regions)。Azure Arc 目前為預覽狀態。 您也可以使用命令，找出要在 Cmdlet 中傳遞的確切區功能變數名稱稱 `az account list-locations` 。
    
    範例如下：
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    在 Azure 入口網站中，應該使用您在上述命令中提供的名稱來建立資源。

    ![移至 Azure Arc 資源](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. 若要確認是否已成功啟用 Azure Arc，請從 PowerShell 介面執行下列命令：

    `kubectl get deployments -n azure-arc`

    此命令會尋找 `azure-arc` 對應于 Azure Arc 的命名空間中部署的任何應用程式。

    以下是範例輸出，顯示在命名空間中 Kubernetes 叢集上部署的 Azure Arc 代理程式 `azure-arc` 。 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    您也可以取得在命名空間的 Kubernetes 叢集上執行的 pod 清單 `azure-arc` 。 Pod 是在您的 Kubernetes 叢集上執行的應用程式容器或進程。 

    使用下列命令：
    
    `kubectl get pods -n azure-arc`
    
    以下是範例輸出。
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


如先前的輸出所示，Azure Arc 啟用的 Kubernetes 是由幾個代理程式所組成 (操作員) 在部署到命名空間的叢集中執行 `azure-arc` 。

- `config-agent`：監看已連線的叢集，以了解叢集上套用的原始檔控制設定資源和更新合規性狀態
- `controller-manager`：是運算子的運算子，且可協調 Azure Arc 元件之間的互動
- `metrics-agent`：收集其他 Arc 代理程式的計量，以確保這些代理程式呈現最佳效能
- `cluster-metadata-operator`：收集叢集中繼資料-叢集版本、節點計數和 Azure Arc 代理程式版本
- `resource-sync-agent`：將上述叢集中繼資料同步處理至 Azure
- `clusteridentityoperator`： Azure Arc 啟用的 Kubernetes 目前支援系統指派的身分識別。 clusteridentityoperator 會維護其他代理程式用來與 Azure 進行通訊的受控服務身分識別 (MSI) 憑證。
- `flux-logs-agent`：從部署為原始檔控制設定一部分的 flux 運算子收集記錄。
- `connect-agent`：與 Azure Arc 資源交談。

### <a name="remove-arc-from-the-kubernetes-cluster"></a>從 Kubernetes 叢集中移除弧線

若要移除 Azure Arc 管理，請遵循下列步驟：

1. 1. [連接到您裝置的 PowerShell 介面](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 。
2. 輸入：

    `Remove-HcsKubernetesAzureArcAgent` 


## <a name="next-steps"></a>後續步驟

若要瞭解如何執行 Azure Arc 部署，請參閱 [Azure Stack Edge Pro 裝置上的使用 Redis Via Gitops) 將部署無狀態 PHP 來賓應用程式](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)
