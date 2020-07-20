---
title: 如何停止監視混合式 Kubernetes 叢集 |Microsoft Docs
description: 本文說明如何使用容器的 Azure 監視器來停止監視混合式 Kubernetes 叢集。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 86a774737d5269d77c4053ad61ab870b13288aa7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84885858"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>如何停止監視混合式叢集

啟用 Kubernetes 叢集的監視之後，如果您決定不想再監視叢集，可以使用容器的 Azure 監視器來停止監視叢集。 本文說明如何在下列環境中完成這項操作：

- Azure 上的 AKS 引擎和 Azure Stack
- OpenShift 第4版及更新版本
- 已啟用 Azure Arc 的 Kubernetes (預覽)

## <a name="how-to-stop-monitoring-using-helm"></a>如何使用 Helm 停止監視

下列步驟適用于下列環境：

- Azure 上的 AKS 引擎和 Azure Stack
- OpenShift 第4版及更新版本

1. 若要先找出安裝在叢集上的容器 helm 圖版本的 Azure 監視器，請執行下列 helm 命令。

    ```
    helm list
    ```

    輸出看起來會像下面這樣：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-容器-release-1*代表適用于容器的 Azure 監視器的 helm 圖表版本。

2. 若要刪除圖表版本，請執行下列 helm 命令。

    `helm delete <releaseName>`

    範例：

    `helm delete azmon-containers-release-1`

    這會從叢集移除發行。 您可以 `helm list` 執行下列命令來確認：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

可能需要幾分鐘的時間才能完成設定變更。 由於 Helm 會在您刪除發行之後追蹤，因此您可以使用來審核叢集的歷程記錄，甚至將版本的取消刪除 `helm rollback` 。

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>如何在啟用 Arc 的 Kubernetes 上停止監視

### <a name="using-powershell"></a>使用 PowerShell

1. 使用下列命令，將腳本下載並儲存到本機資料夾，以使用監視附加元件來設定您的叢集：

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. 設定的 `$azureArcClusterResourceId` 對應值 `subscriptionId` ， `resourceGroupName` 並 `clusterName` 代表已啟用 Azure Arc 之 Kubernetes 叢集資源的資源識別碼，來設定變數。

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 藉 `$kubeContext` 由執行命令，使用您叢集的**kube 內容**來設定變數 `kubectl config get-contexts` 。 如果您想要使用目前的內容，請將值設定為 `""` 。

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. 執行下列命令以停止監視叢集。

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

### <a name="using-bash"></a>使用 bash

1. 使用下列命令，將腳本下載並儲存到本機資料夾，以使用監視附加元件來設定您的叢集：

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. 設定的 `azureArcClusterResourceId` 對應值 `subscriptionId` ， `resourceGroupName` 並 `clusterName` 代表已啟用 Azure Arc 之 Kubernetes 叢集資源的資源識別碼，來設定變數。

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. 藉 `kubeContext` 由執行命令，使用您叢集的**kube 內容**來設定變數 `kubectl config get-contexts` 。

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. 若要停止監視您的叢集，會根據您的部署案例提供不同的命令。

    執行下列命令，以使用目前的內容停止監視叢集。

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    執行下列命令，藉由指定內容來停止監視叢集

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

## <a name="next-steps"></a>後續步驟

如果 Log Analytics 工作區是為了支援監視叢集而建立的，而且不再需要，您必須手動將其刪除。 如果您不熟悉如何刪除工作區，請參閱[刪除 Azure Log Analytics 工作區](../../log-analytics/log-analytics-manage-del-workspace.md)。
