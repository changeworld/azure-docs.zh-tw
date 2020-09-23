---
title: 如何停止監視混合式 Kubernetes 叢集 |Microsoft Docs
description: 本文說明如何使用容器的 Azure 監視器來停止監視混合式 Kubernetes 叢集。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 2754649cd990b015162be158effa2b85aa1fe27e
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986049"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>如何停止監視混合式叢集

啟用 Kubernetes 叢集的監視之後，如果您決定不想再監視叢集，可以使用容器的 Azure 監視器停止監視叢集。 本文說明如何在下列環境中完成這項工作：

- Azure 和 Azure Stack 上的 AKS 引擎
- OpenShift 第4版和更高版本
- 已啟用 Azure Arc 的 Kubernetes (預覽)

## <a name="how-to-stop-monitoring-using-helm"></a>如何使用 Helm 停止監視

下列步驟適用于下列環境：

- Azure 和 Azure Stack 上的 AKS 引擎
- OpenShift 第4版和更高版本

1. 若要先找出叢集上安裝的容器 helm 圖表版本的 Azure 監視器，請執行下列 helm 命令。

    ```
    helm list
    ```

    輸出看起來會像下面這樣：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-容器-發行-1* 表示容器 Azure 監視器的 helm 圖表版本。

2. 若要刪除圖表版本，請執行下列 helm 命令。

    `helm delete <releaseName>`

    範例：

    `helm delete azmon-containers-release-1`

    這會從叢集中移除發行。 您可以藉由 `helm list` 執行下列命令來進行驗證：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

可能需要幾分鐘的時間才能完成設定變更。 因為 Helm 會在您刪除您的發行之後進行追蹤，所以您可以使用來審核叢集的歷程記錄，甚至取消刪除發行 `helm rollback` 。

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>如何停止在啟用 Arc 的 Kubernetes 上進行監視

### <a name="using-powershell"></a>使用 PowerShell

1. 使用下列命令，將腳本下載並儲存至本機資料夾，以使用監視附加元件來設定您的叢集：

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. 設定的 `$azureArcClusterResourceId` 對應值來設定變數 `subscriptionId` ， `resourceGroupName` 並 `clusterName` 代表 Azure Arc 啟用 Kubernetes 叢集資源的資源識別碼。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 藉 `$kubeContext` 由執行命令，使用叢集的 **kube 內容** 來設定變數 `kubectl config get-contexts` 。 如果您想要使用目前的內容，請將值設定為 `""` 。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 執行下列命令以停止監視叢集。

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>使用服務主體
腳本 *disable-monitoring.ps1* 使用互動式裝置登入。 如果您偏好非互動式登入，則可以使用現有的服務主體，或建立具有必要許可權的新帳戶，如 [必要條件](container-insights-enable-arc-enabled-clusters.md#prerequisites)中所述。 若要使用服務主體，您必須傳遞 $servicePrincipalClientId、$servicePrincipalClientSecret 和 $tenantId 參數，以及您想要用來 enable-monitoring.ps1 腳本的服務主體值。

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

例如：

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>使用 Bash

1. 使用下列命令，將腳本下載並儲存至本機資料夾，以使用監視附加元件來設定您的叢集：

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. 設定的 `azureArcClusterResourceId` 對應值來設定變數 `subscriptionId` ， `resourceGroupName` 並 `clusterName` 代表 Azure Arc 啟用 Kubernetes 叢集資源的資源識別碼。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 藉 `kubeContext` 由執行命令，使用叢集的 **kube 內容** 來設定變數 `kubectl config get-contexts` 。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 若要停止監視您的叢集，會根據您的部署案例提供不同的命令。

    執行下列命令，以停止使用目前內容來監視叢集。

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    執行下列命令，藉由指定內容來停止監視叢集

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>使用服務主體
Bash 腳本 *disable-monitoring.sh* 會使用互動式裝置登入。 如果您偏好非互動式登入，則可以使用現有的服務主體，或建立具有必要許可權的新帳戶，如 [必要條件](container-insights-enable-arc-enabled-clusters.md#prerequisites)中所述。 若要使用服務主體，您必須將您要用來 *enable-monitoring.sh* bash 腳本的服務主體的用戶端識別碼、--用戶端密碼和--租使用者識別碼值傳遞給您。

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

例如：

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>後續步驟

如果已建立 Log Analytics 工作區來支援監視叢集，且不再需要該工作區，則您必須手動將其刪除。 如果您不熟悉如何刪除工作區，請參閱 [刪除 Azure Log Analytics 工作區](../platform/delete-workspace.md)。
