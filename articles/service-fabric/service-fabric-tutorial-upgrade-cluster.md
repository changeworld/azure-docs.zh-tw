---
title: 在 Azure 中升級 Service Fabric 執行階段
description: 在本教學課程中，您會了解如何使用 PowerShell 來升級 Azure 裝載的 Service Fabric 叢集執行階段。
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 2fb08d7aba3e35fb6147b75bbcee35b46873b5f6
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252742"
---
# <a name="tutorial-upgrade-the-runtime-of-a-service-fabric-cluster-in-azure"></a>教學課程：在 Azure 中升級 Service Fabric 叢集的執行階段

本教學課程是一系列教學課程中的第四部分，會顯示如何升級 Azure Service Fabric 叢集上的 Service Fabric 執行階段。 這部分的教學課程是針對在 Azure 上執行的 Service Fabric 叢集所撰寫，並不適用於獨立的 Service Fabric 叢集。

> [!WARNING]
> 這個部分的教學課程需要 PowerShell。 Azure CLI 工具尚不支援叢集執行階段的升級。 或者，您也可以在入口網站中升級叢集。 如需詳細資訊，請參閱[升級 Azure Service Fabric 叢集](service-fabric-cluster-upgrade.md)。

若您的叢集所執行的已是最新版 Service Fabric 執行階段，則不需要進行此步驟。 不過，本文可以用來在 Azure Service Fabric 叢集上安裝任何支援的執行階段。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 讀取叢集版本
> * 設定叢集版本

在本教學課程系列中，您將了解如何：
> [!div class="checklist"]
> * 在 Azure 上使用範本建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
> * [監視叢集](service-fabric-tutorial-monitor-cluster.md)
> * [將叢集相應縮小或相應放大](service-fabric-tutorial-scale-cluster.md)
> * 升級叢集的執行階段
> * [刪除叢集](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

開始進行本教學課程之前：

* 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 安裝 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) 或 [Azure CLI](/cli/azure/install-azure-cli)。
* 在 Azure 上建立安全的 [Windows 叢集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* 設定 Windows 開發環境。 安裝 [Visual Studio 2019](https://www.visualstudio.com) 和 **Azure 開發**、**ASP.NET 和 Web 開發**以及 **.NET Core 跨平台開發**工作負載。  然後設定 [.NET 開發環境](service-fabric-get-started.md)。

### <a name="sign-in-to-azure"></a>登入 Azure

請先登入您的 Azure 帳戶並選取您的訂用帳戶，再執行 Azure 命令。

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

## <a name="get-the-runtime-version"></a>取得執行階段版本

連線到 Azure 之後，請選取包含 Service Fabric 叢集的訂用帳戶，就可以取得叢集的執行階段版本。

```powershell
Get-AzServiceFabricCluster -ResourceGroupName SFCLUSTERTUTORIALGROUP -Name aztestcluster `
    | Select-Object ClusterCodeVersion
```

或者，您也可以使用下列範例，直接取得訂用帳戶中所有叢集的清單：

```powershell
Get-AzServiceFabricCluster | Select-Object Name, ClusterCodeVersion
```

請注意 **ClusterCodeVersion** 值。 下一節將使用此值。

## <a name="upgrade-the-runtime"></a>升級執行階段

以 `Get-ServiceFabricRuntimeUpgradeVersion` Cmdlet 使用來自上一節的 **ClusterCodeVersion** 值，探索可以升級到哪些版本。 此 Cmdlet 只能在可連線到網際網路的電腦上執行。 例如，若您想要查看可以從 `5.7.198.9494` 版升級到哪些執行階段版本，請使用下列命令：

```powershell
Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion "5.7.198.9494"
```

有了版本的清單，就可以令 Azure Service Fabric 叢集升級到較新的執行階段。 例如，若可升級為 `6.0.219.9494` 版，請使用下列命令以升級您的叢集。

```powershell
Set-AzServiceFabricUpgradeType -ResourceGroupName SFCLUSTERTUTORIALGROUP `
                                    -Name aztestcluster `
                                    -UpgradeMode Manual `
                                    -Version "6.0.219.9494"
```

> [!IMPORTANT]
> 叢集執行階段升級可能需要很長的時間才能完成。 正在執行升級時，PowerShell 會遭到封鎖。 您可以使用另一個 PowerShell 工作階段來檢查升級狀態。

可以使用 PowerShell 或 Azure Service Fabric CLI (sfctl) 來監視升級狀態。

首先請以本教學課程第一個部分中所建立的 SSL 憑證連線到叢集。 請使用 `Connect-ServiceFabricCluster` Cmdlet 或 `sfctl cluster upgrade-status`。

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
                             -KeepAliveIntervalInSec 10 `
                             -X509Credential -ServerCertThumbprint $thumbprint `
                             -FindType FindByThumbprint -FindValue $thumbprint `
                             -StoreLocation CurrentUser -StoreName My
```

```console
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

接下來，使用 `Get-ServiceFabricClusterUpgrade` 或 `sfctl cluster upgrade-status` 來顯示狀態。 應該會顯示類似下面的結果。

```powershell
Get-ServiceFabricClusterUpgrade

TargetCodeVersion                          : 6.0.219.9494
TargetConfigVersion                        : 3
StartTimestampUtc                          : 11/28/2017 3:09:48 AM
UpgradeState                               : RollingForwardPending
UpgradeDuration                            : 00:09:00
CurrentUpgradeDomainDuration               : 00:09:00
NextUpgradeDomain                          : 1
UpgradeDomainsStatus                       : { "0" = "Completed";
                                             "1" = "Pending";
                                             "2" = "Pending";
                                             "3" = "Pending";
                                             "4" = "Pending" }
UpgradeKind                                : Rolling
RollingUpgradeMode                         : Monitored
FailureAction                              : Rollback
ForceRestart                               : False
UpgradeReplicaSetCheckTimeout              : 37201.09:59:01
HealthCheckWaitDuration                    : 00:05:00
HealthCheckStableDuration                  : 00:05:00
HealthCheckRetryTimeout                    : 00:45:00
UpgradeDomainTimeout                       : 02:00:00
UpgradeTimeout                             : 12:00:00
ConsiderWarningAsError                     : False
MaxPercentUnhealthyApplications            : 0
MaxPercentUnhealthyNodes                   : 100
ApplicationTypeHealthPolicyMap             : {}
EnableDeltaHealthEvaluation                : True
MaxPercentDeltaUnhealthyNodes              : 0
MaxPercentUpgradeDomainDeltaUnhealthyNodes : 0
ApplicationHealthPolicyMap                 : {}
```

```console
sfctl cluster upgrade-status

{
  "codeVersion": "6.0.219.9494",
  "configVersion": "3",

... item cut to save space ...

  },
  "upgradeDomains": [
    {
      "name": "0",
      "state": "Completed"
    },
    {
      "name": "1",
      "state": "Pending"
    },
    {
      "name": "2",
      "state": "Pending"
    },
    {
      "name": "3",
      "state": "Pending"
    },
    {
      "name": "4",
      "state": "Pending"
    }
  ],
  "upgradeDurationInMilliseconds": "PT1H2M4.63889S",
  "upgradeState": "RollingForwardPending"
}
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 取得叢集執行階段版本
> * 升級叢集執行階段
> * 對升級進行監視

前進到下一個教學課程：

> [!div class="nextstepaction"]
> [刪除叢集](service-fabric-tutorial-delete-cluster.md)
