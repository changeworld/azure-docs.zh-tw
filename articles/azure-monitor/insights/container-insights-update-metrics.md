---
title: 如何更新計量容器的 Azure 監視器 |Microsoft Docs
description: 本文說明如何更新容器的 Azure 監視器，以啟用支援探索和警示匯總計量的自訂計量功能。
ms.topic: conceptual
ms.date: 09/24/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c420c91e20cc1cf9ab5e4f58bdd352ead3ba4d0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618140"
---
# <a name="how-to-update-azure-monitor-for-containers-to-enable-metrics"></a>如何更新容器的 Azure 監視器以啟用計量

容器的 Azure 監視器引進了從 Azure Kubernetes Services 收集計量的支援 (AKS) 以及 Azure Arc 啟用 Kubernetes 叢集節點和 pod，並將它們寫入 Azure 監視器計量存放區。 這項變更的目的是為了在呈現匯總計算時提供改進的時效性， (在效能圖表中的 Avg、Count、Max、Min、Sum) 、支援在 Azure 入口網站儀表板中釘選效能圖表，以及支援計量警示。

>[!NOTE]
>這項功能目前不支援 Azure Red Hat OpenShift 叢集。
>

以下是此功能的一部分啟用的計量：

| 計量命名空間 | 計量 | 描述 |
|------------------|--------|-------------|
| 深入解析。容器/節點 | cpuUsageMillicores、cpuUsagePercentage、memoryRssBytes、memoryRssPercentage、memoryWorkingSetBytes、memoryWorkingSetPercentage、nodesCount、diskUsedPercentage、 | 作為 *節點* 計量，它們包含 *主機* 做為維度。 它們也包含<br> 節點的名稱，作為 *主機* 維度的值。 |
| 深入解析容器/pod | podCount, completedJobsCount, restartingContainerCount, oomKilledContainerCount, podReadyPercentage | 作為 *pod* 計量，它們包含下列各項：維度-ControllerName、Kubernetes 命名空間、名稱、階段。 |
| 深入解析容器/容器 | cpuExceededPercentage, memoryRssExceededPercentage, memoryWorkingSetExceededPercentage | |

為了支援這些新功能，新的容器化代理套裝程式含在發行版本中， **microsoft/oms： ciprod05262020** for AKS and version **microsoft/oms： ciprod09252020** for Azure Arc enabled Kubernetes 叢集。 AKS 的新部署會自動包含這項設定變更和功能。 更新您的叢集以支援這項功能，可以從 Azure 入口網站、Azure PowerShell 或 Azure CLI 執行。 使用 Azure PowerShell 和 CLI。 您可以針對您訂用帳戶中的每個叢集或所有叢集啟用此功能。

這兩個處理常式會將 **監視計量發行者** 角色指派給叢集的服務主體，或指派給監視附加元件的使用者指派 MSI，以便將代理程式所收集的資料發佈到您的叢集資源。 監視計量發行者只有將計量推送至資源的許可權，它無法更改任何狀態、更新資源或讀取任何資料。 如需有關角色的詳細資訊，請參閱 [監視計量發行者角色](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher)。 監視計量發行者角色需求不適用於 Azure Arc 啟用的 Kubernetes 叢集。

> [!IMPORTANT]
> Azure Arc 啟用的 Kubernetes 叢集並不需要升級，因為它們已具有最低的必要代理程式版本。

## <a name="prerequisites"></a>必要條件

在您更新叢集之前，請確認下列事項：

* 自訂計量僅適用于 Azure 區域的子集中。 支援的區域清單記載于 [此處](../platform/metrics-custom-overview.md#supported-regions)。

* 您是 AKS 叢集資源上 **[擁有](../../role-based-access-control/built-in-roles.md#owner)** 者角色的成員，可讓您收集節點和 pod 自訂效能度量。 這項需求不適用於已啟用 Azure Arc 的 Kubernetes 叢集。

如果您選擇使用 Azure CLI，必須先在本機安裝並使用 CLI。 您必須執行 Azure CLI 2.0.59 版或更新版本。 若要知道您使用的版本，請執行 `az --version`。 如果您需要安裝或升級 Azure CLI，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="upgrade-a-cluster-from-the-azure-portal"></a>從 Azure 入口網站升級叢集

針對容器 Azure 監視器所監視的現有 AKS 叢集，請在從左側窗格中選取 [深入解析]，然後從左側窗格中選取 [深入解析]，然後從左側窗格 Azure 監視器中選取 [ **深入** 解析]，以從叢集的多重叢集視圖中查看其健康情況，然後您應該會在入口網站頂端看到橫幅。

![升級 Azure 入口網站中的 AKS 叢集橫幅](./media/container-insights-update-metrics/portal-banner-enable-01.png)

按一下 [ **啟用** ] 將會起始升級叢集的程式。 此程序需要幾秒鐘才能完成，您可以在功能表的 [通知] 底下追蹤其進度。

## <a name="upgrade-all-clusters-using-bash-in-azure-command-shell"></a>在 Azure 命令 Shell 中使用 Bash 升級所有叢集

請在 Azure 命令列介面中使用 Bash 來執行下列步驟，以更新訂用帳戶中的所有叢集。

1. 使用 Azure CLI 來執行下列命令。  使用 AKS 叢集的 [ **AKS] 總覽**頁面中的值來編輯**subscriptionId**的值。

    ```azurecli
    az login
    az account set --subscription "Subscription Name"
    curl -sL https://aka.ms/ci-md-onboard-atscale | bash -s subscriptionId   
    ```

    設定變更可能需要幾秒鐘的時間才能完成。 完成之後，將會顯示如下訊息並包含結果：

    ```azurecli
    completed role assignments for all AKS clusters in subscription: <subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-cli"></a>使用 Azure CLI 針對每個叢集進行升級

使用 Azure CLI 來執行下列步驟，以更新訂用帳戶中的特定叢集。

1. 使用 Azure CLI 來執行下列命令。 使用 AKS 叢集的**AKS 總覽**頁面上的值，編輯**subscriptionId**、 **resourceGroupName**和**clusterName**的值。  若要取得 **clientIdOfSPN**的值，當您執行命令時，會傳回此值， `az aks show` 如下列範例所示。

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPN> --scope <clusterResourceId> --role "Monitoring Metrics Publisher" 
    ```

    若要取得 **clientIdOfSPNOrMsi**的值，您可以執行 `az aks show` 下列範例所示的命令。 如果 **servicePrincipalProfile** 物件具有有效的 *clientid* 值，您可以使用該物件。 否則，如果設定為 *msi*，您就必須從傳入 clientid `addonProfiles.omsagent.identity.clientId` 。

    ```azurecli
    az login
    az account set --subscription "<subscriptionName>"
    az aks show -g <resourceGroupName> -n <clusterName> 
    az role assignment create --assignee <clientIdOfSPNOrMsi> --scope <clusterResourceId> --role "Monitoring Metrics Publisher"
    ```

## <a name="upgrade-all-clusters-using-azure-powershell"></a>使用 Azure PowerShell 升級所有叢集

使用 Azure PowerShell 來執行下列步驟，以更新訂用帳戶中的所有叢集。

1. [下載](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding_atscale.ps1) **mdm_onboarding_atscale.ps1** 腳本，並將其儲存至 GitHub 存放庫中的本機資料夾。
2. 使用 Azure PowerShell 來執行下列命令。  使用 AKS 叢集的 [ **AKS] 總覽**頁面中的值來編輯**subscriptionId**的值。

    ```powershell
    .\mdm_onboarding_atscale.ps1 subscriptionId
    ```
    設定變更可能需要幾秒鐘的時間才能完成。 完成之後，將會顯示如下訊息並包含結果：

    ```powershell
    Completed adding role assignment for the aks clusters in subscriptionId :<subscriptionId>
    ```

## <a name="upgrade-per-cluster-using-azure-powershell"></a>使用 Azure PowerShell 針對每個叢集進行升級

請執行下列步驟，使用 Azure PowerShell 更新特定叢集。

1. [下載](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/docs/aks/mdmonboarding/mdm_onboarding.ps1) **mdm_onboarding.ps1** 腳本，並將其儲存至 GitHub 存放庫中的本機資料夾。

2. 使用 Azure PowerShell 來執行下列命令。 使用 AKS 叢集的**AKS 總覽**頁面上的值，編輯**subscriptionId**、 **resourceGroupName**和**clusterName**的值。

    ```powershell
    .\mdm_onboarding.ps1 subscriptionId <subscriptionId> resourceGroupName <resourceGroupName> clusterName <clusterName>
    ```

    設定變更可能需要幾秒鐘的時間才能完成。 完成之後，將會顯示如下訊息並包含結果：

    ```powershell
    Successfully added Monitoring Metrics Publisher role assignment to cluster : <clusterName>
    ```

## <a name="verify-update"></a>驗證更新

使用稍早所述的其中一個方法來起始更新之後，您可以使用 Azure 監視器計量瀏覽器，並從計量 **命名空間** 進行驗證，以列出 **見解** 。 如果是，您可以開始設定計量 [警示](../platform/alerts-metric.md) 或將圖表釘選到 [儀表板](../../azure-portal/azure-portal-dashboards.md)。  
