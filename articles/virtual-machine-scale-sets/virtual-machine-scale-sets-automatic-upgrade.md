---
title: Azure 虛擬機器擴展集的自動 OS 映射升級
description: 瞭解如何在擴展集中的 VM 實例上自動升級 OS 映射
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 4ebb16186e613affdb886a8819240d47f944c42f
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763535"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure 虛擬機器擴展集的 OS 映像自動升級

在擴展集上啟用 OS 映像自動升級，可藉由為擴展集內的所有執行個體安全且自動地升級 OS 磁碟，來協助您簡化更新的管理。

作業系統自動升級具有下列特性︰

- 一旦設定之後，映像發行者所發行的最新作業系統映像便會自動套用至擴展集，完全不需要使用者介入。
- 每次發行者發行新的映射時，都會以輪流的方式升級實例的批次。
- 會與應用程式健康情況探查和[應用程式健康情況擴充功能](virtual-machine-scale-sets-health-extension.md)整合。
- 適用于所有 VM 大小，以及 Windows 和 Linux 映射。
- 您可以隨時選擇不自動升級 (您也可以手動起始作業系統升級)。
- VM 的作業系統磁碟會取代為使用最新映像版本建立的新作業系統磁碟。 系統會執行已設定的延伸模組和自訂資料指令碼，同時保留已保存的資料磁碟。
- 支援[擴充功能排序](virtual-machine-scale-sets-extension-sequencing.md)。
- 可以在任何大小的擴展集上啟用自動 OS 映射升級。

## <a name="how-does-automatic-os-image-upgrade-work"></a>OS 映像自動升級工作進行的方式為何？

升級的方式是使用以最新版映像建立的新磁碟來取代 VM 的 OS 磁碟。 任何已設定的擴充功能和自訂資料指令碼都會在 OS 磁碟上執行，同時保留已保存的資料磁碟。 為了將應用程式停機時間降到最低，升級會分批進行，每次升級不會超過擴展集的 20%。 您也可以整合 Azure Load Balancer 應用程式健康情況探查或[應用程式健康情況擴充功能](virtual-machine-scale-sets-health-extension.md)。 我們的建議是整合應用程式活動訊號，並在升級程序中驗證每個批次的更新是否都成功。

升級程序的運作方式如下︰
1. 在開始進行升級程序之前，協調器會確定整個擴展集內，(因為任何原因而) 狀況不良的執行個體數目未超過 20%。
2. 升級協調器會識別要升級的 VM 實例批次，其中任何一個批次最多可有20% 的總實例計數，受限於一個虛擬機器的最小批次大小。
3. 所選 VM 實例批次的 OS 磁片會取代為從最新映射建立的新 OS 磁片。 擴展集模型中的所有指定延伸模組和設定都會套用至升級的實例。
4. 若擴展集已設定應用程式健康情況探查或應用程式健康情況擴充功能，升級作業會先等候 5 分鐘讓執行個體恢復成良好狀態，然後才繼續升級下一批。 如果實例在升級後的5分鐘內未復原其健康情況，則預設會還原實例的先前作業系統磁片。
5. 升級協調器也會追蹤在升級後變得狀況不良的執行個體百分比。 如果在升級程序進行期間，已升級的執行個體有超過 20% 變成狀況不良，升級作業就會停止。
6. 上述程序會持續進行，直到擴展集中的所有執行個體皆已升級。

擴展集的 OS 升級協調器在升級每個批次之前，都會先檢查整體的擴展集健康情況。 在升級某個批次時，可能會有其他計劃性或非計劃性維護活動也在並行執行，而影響到擴展集執行個體的健康情況。 在此情況下，如果擴展集的執行個體有超過 20% 變得狀況不良，則擴展集升級程序會在當前的批次結束時停止。

> [!NOTE]
>自動 OS 升級不會升級擴展集上的參考映射 Sku。 若要將 Sku (例如 Ubuntu 16.04-LTS 變更為 18.04 LTS) ，您必須使用所需的映射 Sku 直接更新 [擴展集模型](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model) 。 無法變更現有擴展集的映射發行者和供應專案。  

## <a name="supported-os-images"></a>支援的作業系統映像
目前僅支援特定的作業系統平台映像。 如果擴展集使用來自[共用映射庫](shared-image-galleries.md)的自訂映射，則[支援](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images)自訂映射。

目前支援下列平臺 Sku (而且會定期新增更多) ：

| 發行者               | OS 供應項目      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| OpenLogic               | CentOS        | 7.5                |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Smalldisk |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-with-Containers |
| MicrosoftWindowsServer  | WindowsServer | Datacenter-核心-1903-含容器-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>設定 OS 映像自動升級的需求

- 映射的 [ *版本* ] 屬性必須設定為 [ *最新*]。
- 非 Service Fabric 的擴展集必須使用應用程式健康情況探查或[應用程式健康情況擴充功能](virtual-machine-scale-sets-health-extension.md)。
- 使用計算 API 版本2018-10-01 或更高版本。
- 請確定擴展集模型中指定的外部資源可供使用且已更新。 範例包括在 VM 擴充功能屬性中用於啟動承載的 SAS URI、儲存體帳戶中的承載，以及模型中祕密的參照等等。
- 針對使用 Windows 虛擬機器的擴展集，從計算 API 版本2019-03-01 開始，在擴展集模型定義中，屬性 *virtualMachineProfile. osProfile. windowsConfiguration. enableAutomaticUpdates* 屬性必須設為 *false* 。 上述屬性可啟用 VM 內升級，其中「Windows Update」會套用作業系統修補程式，而不需更換作業系統磁片。 在擴展集上啟用自動 OS 映射升級的情況下，不需要透過「Windows Update」進行額外的更新。

### <a name="service-fabric-requirements"></a>Service Fabric 需求

如果您使用 Service Fabric，請確定符合下列條件：
-   Service Fabric [耐久性層級](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) 為銀級或金級，而非銅級。
-   擴展集模型定義上的 Service Fabric 延伸模組必須有 TypeHandlerVersion 1.1 或更新版本。
-   在 Service Fabric 叢集和擴展集模型定義上 Service Fabric 擴充功能的持久性層級應該相同。
- 不需要額外的健康情況探查或應用程式健康情況延伸模組的使用。

請確定 Service Fabric 叢集和 Service Fabric 擴充功能的持久性設定不相符，因為不相符會導致升級錯誤。 您可以根據 [此頁面](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)上所述的指導方針來修改持久性層級。


## <a name="automatic-os-image-upgrade-for-custom-images"></a>自訂映射的自動 OS 映射升級

透過 [共用映射庫](shared-image-galleries.md)部署的自訂映射支援自動 OS 映射升級。 系統不支援自動 OS 映射升級的其他自訂映射。

### <a name="additional-requirements-for-custom-images"></a>自訂映射的其他需求
- 所有擴展集的安裝和設定程式都是相同的，如本頁的設定 [一節](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) 中所述。
- 針對自動 OS 映射升級設定的擴展集實例，會在新版本的映射 [發行並複寫](shared-image-galleries.md#replication) 至該擴展集的區域時，升級為最新版本的共用映射庫映射。 如果新映射未複寫至部署規模的區域，擴展集實例將不會升級為最新版本。 區域映射複寫可讓您控制擴展集的新映射推出。
- 新的映射版本不應從該圖庫映射的最新版本中排除。 從資源庫映射的最新版本中排除的映射版本，不會透過自動 OS 映射升級推出至擴展集。

> [!NOTE]
>擴展集在第一次設定自動 OS 升級之後，最多可能需要3小時的時間，才會觸發第一個映射升級首度發行。 這是每個擴展集的一次性延遲。 在30-60 分鐘內的擴展集上，會觸發後續的映射首度發行。


## <a name="configure-automatic-os-image-upgrade"></a>設定 OS 映像自動升級
若要設定 OS 映像自動升級，請確定擴展集模型定義中的 *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* 屬性是設為 *true*。

### <a name="rest-api"></a>REST API
下列範例說明如何設定擴展集模型上的 OS 自動升級：

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [new-azvmss 指令程式](/powershell/module/az.compute/update-azvmss) 來設定擴展集的自動 OS 映射升級。 下列範例會在名為 *myResourceGroup* 的資源群組中，針對名為 *myScaleSet* 的擴展集設定自動升級：

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss update](/cli/azure/vmss#az-vmss-update) 來設定擴展集的自動 OS 映射升級。 使用 Azure CLI 2.0.47 或更新版本。 下列範例會在名為 *myResourceGroup* 的資源群組中，針對名為 *myScaleSet* 的擴展集設定自動升級：

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>設定擴展集的自動 OS 映射升級之後，如果您的擴展集使用「手動」 [升級原則](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)，您也必須將擴展集 vm 帶入最新的擴展集模型。

## <a name="using-application-health-probes"></a>使用應用程式健康狀態探查

在作業系統升級期間，擴展集中的 VM 執行個體一次升級一個批次。 只有客戶應用程式在已升級的 VM 執行個體上狀況良好時，升級才應該繼續。 建議應用程式向擴展集的作業系統升級引擎提供健康情況訊號。 根據預設，平台在作業系統升級期間會考慮 VM 電源狀態和延伸模組佈建狀態，以判斷 VM 執行個體在升級之後是否狀況良好。 在 VM 執行個體的作業系統升級期間，VM 執行個體上的作業系統磁碟會根據最新的映像版本，取代為新的磁碟。 作業系統升級完成後，已設定的延伸模組便會在這些 VM 上執行。 只有在執行個體上的所有擴充功能都成功佈建之後，系統才會認為應用程式的狀況良好。

您可以使用應用程式健康情況探查選擇性地設定擴展集，以便為平台提供精確的應用程式持續狀態的相關資訊。 應用程式健康情況探查是當作健康情況訊號使用的自訂負載平衡器探查。 在擴展集 VM 執行個體上執行的應用程式可以回應外部 HTTP 或 TCP 要求，以指出它是否狀況良好。 如需有關自訂負載平衡器探查運作方式的詳細資訊，請參閱[了解負載平衡器探查](../load-balancer/load-balancer-custom-probe-overview.md)。 Service Fabric 擴展集不支援應用程式健康情況探查。 非 Service Fabric 的擴展集需要使用 Load Balancer 應用程式健康情況探查或[應用程式健康情況擴充功能](virtual-machine-scale-sets-health-extension.md)。

如果擴展集設定為使用多個放置群組，則需要用到使用[標準負載平衡器](../load-balancer/load-balancer-overview.md)的探查。

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>將自訂負載平衡器探查設定為擴展集上的應用程式健康情況探查
最佳作法是，針對擴展集健康情況明確地建立負載平衡器探查。 系統可針對現有的 HTTP 探查或 TCP 探查使用相同的端點，但健康情況探查可能會需要不同於傳統負載平衡器探查的行為。 例如，如果執行個體的負載太高，傳統負載平衡器探查可能會傳回狀況不良，但可能不適用於判斷作業系統自動升級期間的執行個體健康情況。 將探查設定為具有不到兩分鐘的高探查率。

您可以在擴展集的 *networkProfile* 中參考負載平衡器探查，而且可以與對內或對外公開的負載平衡器建立關聯，如下所示：

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> 搭配 Service Fabric 使用自動 OS 升級時，新的 OS 映像將以更新網域對更新網域的方式推出，以維持在 Service Fabric 中執行之服務的高可用性。 若要在 Service Fabric 中利用自動 OS 升級，您的叢集必須設定為使用銀級耐久性層或更高。 如需 Service Fabric 叢集持久性特性的詳細資訊，請參閱[這份文件](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)。

### <a name="keep-credentials-up-to-date"></a>讓認證保持最新狀態
如果您的擴展集使用任何認證來存取外部資源（例如，設定為針對儲存體帳戶使用 SAS 權杖的 VM 延伸模組），請確定已更新認證。 如果有任何認證（包括憑證和權杖）已過期，升級將會失敗，且 Vm 的第一個批次將會保持在失敗狀態。

資源驗證失敗時，復原 VM 並重新啟用自動 OS 升級的建議步驟如下：

* 重新產生傳遞至擴充功能的權杖 (或任何其他認證)。
* 請確認從 VM 內用來向外部實體通訊的任何認證是最新狀態。
* 將擴展集模型中的擴充功能更新為任一新權杖。
* 部署更新的擴展集，這會更新所有 VM 執行個體，包括失敗的 VM 執行個體。

## <a name="using-application-health-extension"></a>使用應用程式健康情況擴充功能
應用程式健康狀態延伸模組會部署在虛擬機器擴展集執行個體內部，並從擴展集執行個體內部報告 VM 健康狀態。 您可以將延伸模組設定為在應用程式端點上探查，並更新該執行個體上的應用程式狀態。 Azure 會檢查此執行個體狀態，以判斷執行個體是否適合進行升級作業。

因為此擴充功能是從 VM 內報告健康情況，所以在無法使用應用程式健康情況探查 (其利用自訂的 Azure Load Balancer [探查](../load-balancer/load-balancer-custom-probe-overview.md)) 等外部探查的情況下，可以使用此擴充功能。

有多種方法可以將應用程式健康情況擴充功能部署至您的擴展集，如[這篇文章](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)中的範例所詳述。

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>取得 OS 映像自動升級的記錄
您可以使用 Azure PowerShell、Azure CLI 2.0 或 REST API，檢查您擴展集上最近執行之 OS 升級的記錄。 您可以取得過去兩個月內的最近五次 OS 升級嘗試的記錄。

### <a name="rest-api"></a>REST API
下列範例會使用 [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) ，在名為 *myResourceGroup* 的資源群組中檢查名為 *myScaleSet* 的擴展集狀態：

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

GET 呼叫會傳回類似下列範例輸出的內容：

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [Get-AzVmss](/powershell/module/az.compute/get-azvmss) Cmdlet 來檢查擴展集的 OS 升級歷程記錄。 下列範例會詳細說明如何在名為 *myResourceGroup* 的資源群組中，檢查名為 *myScaleSet* 之擴展集的 OS 升級狀態：

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history) 來檢查擴展集的 OS 升級歷程記錄。 使用 Azure CLI 2.0.47 或更新版本。 下列範例會詳細說明如何在名為 *myResourceGroup* 的資源群組中，檢查名為 *myScaleSet* 之擴展集的 OS 升級狀態：

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>如何取得最新版的平台 OS 映像？

您可以使用下列範例，取得自動 OS 升級支援 Sku 的可用映射版本：

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>手動觸發 OS 映射升級
在擴展集上啟用自動 OS 映射升級的情況下，您不需要在擴展集上手動觸發映射更新。 OS 升級協調器會自動將最新的可用映射版本套用至您的擴展集實例，而不需要任何手動介入。

針對您不想等候 orchestrator 套用最新映射的特定情況，您可以使用下列範例，手動觸發 OS 映射升級。

> [!NOTE]
> OS 映射升級的手動觸發程式不會提供自動回復功能。 如果實例未在升級作業之後復原其健康情況，則無法還原其先前的 OS 磁片。

### <a name="rest-api"></a>REST API
使用 [啟動 OS 升級](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API 呼叫來開始輪流升級，以將所有虛擬機器擴展集實例移至最新的可用映射作業系統版本。 已在執行最新可用 OS 版本的實例不會受到影響。 下列範例會詳細說明如何在名為 *myResourceGroup* 的資源群組中，于名為 *myScaleSet* 的擴展集上開始進行滾動 OS 升級。

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) Cmdlet 來檢查擴展集的 OS 升級歷程記錄。 下列範例會詳細說明如何在名為 *myResourceGroup* 的資源群組中，于名為 *myScaleSet* 的擴展集上開始進行滾動 OS 升級。

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vmss 輪流升級開始](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) ，檢查您擴展集的 OS 升級歷程記錄。 使用 Azure CLI 2.0.47 或更新版本。 下列範例會詳細說明如何在名為 *myResourceGroup* 的資源群組中，于名為 *myScaleSet* 的擴展集上開始進行滾動 OS 升級。

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>透過範本進行部署

您可以使用範本，來為支援的映像 (例如 [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json)) 部署具有 OS 自動升級功能的擴展集。

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>後續步驟
如需如何搭配擴展集使用 OS 自動升級的其他範例，請檢閱 [GitHub 存放庫](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)。
