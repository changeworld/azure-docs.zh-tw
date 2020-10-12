---
title: Azure 中 Windows Vm 的自動 VM 來賓修補
description: 瞭解如何在 Azure 中自動修補 Windows 虛擬機器
author: mayanknayar
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/09/2020
ms.author: manayar
ms.openlocfilehash: 0a777b9008864368a6d1731cae0374e55a4c585f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842864"
---
# <a name="preview-automatic-vm-guest-patching-for-windows-vms-in-azure"></a>預覽：Azure 中 Windows VM 的自動 VM 客體修補

為您的 Windows Vm 啟用自動 VM 來賓修補有助於簡化更新管理，方法是安全地自動修補虛擬機器以維護安全性合規性。

自動 VM 來賓修補具有下列特性：
- 分類為「 *重大* 」或「 *安全性* 」的修補程式會自動下載並套用至 VM。
- 修補程式是在 VM 時區的離峰時段進行套用。
- 修補程式協調流程是由 Azure 所管理，而修補程式則適用于可用性優先原則。
- 虛擬機器健康狀態（透過平臺健康情況信號所決定）會受到監視，以偵測修補失敗。
- 適用於所有 VM 大小。

> [!IMPORTANT]
> 自動 VM 來賓修補目前處於公開預覽狀態。 需要有加入宣告的程式，才能使用下面所述的公開預覽功能。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-does-automatic-vm-guest-patching-work"></a>自動 VM 來賓修補如何運作？

如果 VM 上已啟用自動 VM 來賓修補，則會在 VM 上自動下載並套用可用的 *重大* 和 *安全性* 修補程式。 當透過 Windows Update 發行新的修補程式時，此程式會每個月自動啟動。 修補程式評定和安裝都是自動的，而且此套裝程式含視需要重新開機 VM。

VM 會定期評估，以判斷該 VM 適用的修補程式。 您可以在 vm 的離峰時段于 vm 上安裝任何一天的修補程式。 這種自動評量可確保任何遺失的修補程式都會以最早的可能機會探索。

修補程式會安裝在每月 Windows Update 版的30天內，並遵循如下所述的可用性優先協調流程。 修補程式只會在 VM 的離峰時段安裝，視 VM 的時區而定。 VM 必須在離峰時間執行，才能自動安裝修補程式。 如果 VM 在定期評量期間關閉電源，系統將會自動評估 VM，並在下一次進行 VM 開機時自動安裝適當的修補程式。

若要在您自己的自訂維護期間安裝其他修補程式分類或排程修補程式安裝的修補程式，您可以使用 [更新管理](tutorial-config-management.md#manage-windows-updates)。

### <a name="availability-first-patching"></a>可用性-優先修補

針對已啟用自動 VM 來賓修補的所有 Vm，Azure 會在全球協調修補程式安裝程式。 此協調流程會遵循 Azure 提供的不同可用性層級之間的可用性優先原則。

針對正在進行更新的一組虛擬機器，Azure 平臺將會協調更新：

**跨區域：**
- 每月更新會以分段方式跨 Azure 進行協調，以防止全域部署失敗。
- 一個階段可以有一或多個區域，而只有在階段中符合資格的 Vm 成功更新時，更新才會移至下一個階段。
- 地理配對區域不會同時更新，也不能在相同的區域階段中。
- 更新成功是藉由追蹤 VM 的健康情況更新來測量。 VM 健康情況會透過 VM 的平臺健康情況指標進行追蹤。

**區域內：**
- 不同可用性區域中的 Vm 不會同時更新。
- Vm 不是可用性設定組的一部分，因此會以最佳方式進行批次處理，以避免訂用帳戶中所有 Vm 的並行更新。

**在可用性設定組內：**
- 一般可用性設定組中的所有 Vm 不會同時更新。
-   通用可用性設定組中的 Vm 會在更新網域界限內進行更新，而跨多個更新網域的 Vm 則不會同時更新。

給定 VM 的修補程式安裝日期可能會因為每月修補週期不同的不同批次而異，因此可能會每月變更一次。

## <a name="supported-os-images"></a>支援的作業系統映像
預覽版目前僅支援從特定作業系統平臺映射建立的 Vm。 預覽版目前不支援自訂映射。

目前支援下列平臺 Sku (而且會定期新增更多) ：

| 發行者               | OS 供應項目      |  SKU               |
|-------------------------|---------------|--------------------|
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Server-Core |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-伺服器-核心 |

## <a name="patch-orchestration-modes"></a>修補協調流程模式
Azure 上的 Windows Vm 現在支援下列修補程式協調流程模式：

**AutomaticByPlatform:**
- 此模式會啟用 Windows 虛擬機器的自動 VM 來賓修補，而後續的修補程式安裝會由 Azure 進行協調。
- 設定此模式也會停用 Windows 虛擬機器上的原生自動更新，以避免重複。
- 只有使用上述支援的作業系統平臺映射建立的 Vm 才支援此模式。
- 若要使用此模式，請設定屬性 `osProfile.windowsConfiguration.enableAutomaticUpdates=true` ，並設定  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatfom` VM 範本中的屬性。

**AutomaticByOS:**
- 此模式會啟用 Windows 虛擬機器上的自動更新，並透過自動更新在 VM 上安裝修補程式。
- 如果未指定其他修補程式模式，預設會設定此模式。
- 若要使用此模式 `osProfile.windowsConfiguration.enableAutomaticUpdates=true` ，請設定屬性，並設定  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` VM 範本中的屬性。

**手動：**
- 此模式會停用 Windows 虛擬機器上的自動更新。
- 使用自訂修補解決方案時應設定此模式。
- 若要使用此模式 `osProfile.windowsConfiguration.enableAutomaticUpdates=false` ，請設定屬性，並設定  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` VM 範本中的屬性。

> [!NOTE]
>`osProfile.windowsConfiguration.enableAutomaticUpdates`目前只有在第一次建立 VM 時，才能設定屬性。 目前不支援從手動切換至自動模式，或從自動模式切換至手動模式。 支援從 AutomaticByOS 模式切換至 AutomaticByPlatfom 模式

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>啟用自動 VM 來賓修補的需求

- 虛擬機器必須已安裝 [AZURE VM 代理程式](../extensions/agent-windows.md) 。
- Windows Update 服務必須在虛擬機器上執行。
- 虛擬機器必須能夠存取 Windows Update 端點。 如果您的虛擬機器設定為使用 Windows Server Update Services (WSUS) ，則必須可存取相關的 WSUS 伺服器端點。
- 使用計算 API 版本2020-06-01 或更高版本。

啟用預覽功能需要針對每個訂用帳戶 *InGuestAutoPatchVMPreview* 功能的一次性選擇，如下所述。

### <a name="rest-api"></a>REST API
下列範例說明如何啟用訂用帳戶的預覽：

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```

功能註冊最多可能需要15分鐘的時間。 檢查註冊狀態：

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```

為您的訂用帳戶註冊此功能之後，請將變更傳播到計算資源提供者，以完成加入宣告程式。

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [AzProviderFeature 指令程式](/powershell/module/az.resources/register-azproviderfeature) 來啟用訂用帳戶的預覽。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

功能註冊最多可能需要15分鐘的時間。 檢查註冊狀態：

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
```

為您的訂用帳戶註冊此功能之後，請將變更傳播到計算資源提供者，以完成加入宣告程式。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az feature register](/cli/azure/feature#az-feature-register) 來啟用訂用帳戶的預覽。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

功能註冊最多可能需要15分鐘的時間。 檢查註冊狀態：

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
```

為您的訂用帳戶註冊此功能之後，請將變更傳播到計算資源提供者，以完成加入宣告程式。

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```
## <a name="enable-automatic-vm-guest-patching"></a>啟動自動 VM 客體修補
若要啟用自動 VM 來賓修補，請確定 VM 範本定義中的屬性 *osProfile. windowsConfiguration. enableAutomaticUpdates* 設定為 *true* 。 只有在建立 VM 時，才能設定此屬性。

### <a name="rest-api"></a>REST API
下列範例說明如何啟用自動 VM 來賓修補：

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
建立或更新 VM 時，請使用 [>set-azvmoperatingsystem](/powershell/module/az.compute/set-azvmoperatingsystem) 指令 Cmdlet 來啟用自動 vm 來賓修補。

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
在建立新的 VM 時，請使用 [az vm create](/cli/azure/vm#az-vm-create) 來啟用自動 vm 來賓修補。 下列範例會針對名為*myResourceGroup*的資源群組中名為*myVM*的 VM，設定自動 vm 來賓修補：

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

若要修改現有的 VM，請使用 [az vm update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>啟用和評量

> [!NOTE]
>在 vm 上啟用自動 VM 來賓更新可能需要三個小時以上的時間，因為啟用會在 VM 的離峰時段完成。 由於評量和修補程式安裝只會在離峰時間執行，因此您的 VM 也必須在離峰時間執行，以套用修補程式。

當 VM 啟用自動 VM 來賓修補時，vm 上會安裝類型的 VM 擴充 `Microsoft.CPlat.Core.WindowsPatchExtension` 功能。 此延伸模組不需要手動安裝或更新，因為此延伸模組是由 Azure 平臺管理，作為自動 VM 來賓修補程式的一部分。

在 vm 上啟用自動 VM 來賓更新可能需要三個小時以上的時間，因為啟用會在 VM 的離峰時段完成。 此延伸模組也會在 VM 的離峰時段進行安裝和更新。 如果 VM 的離峰時段結束後才能完成，則啟用程式會在下一個可用的離峰時間繼續進行。 如果 VM 先前已透過 AutomaticByOS 修補程式模式開啟自動 Windows Update，則在安裝擴充功能時，會關閉 VM 的自動 Windows Update。

若要確認自動 VM 來賓修補是否已完成，並已在 VM 上安裝修補延伸模組，您可以查看 VM 的實例視圖。 如果啟用程式已完成，將會安裝此延伸模組，而且 VM 的評量結果將可在下方取得 `patchStatus` 。 您可以透過多種方式來存取 VM 的實例視圖，如下所述。

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-06-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
使用 [new-azvm](/powershell/module/az.compute/get-azvm) 指令程式搭配 `-Status` 參數，以存取您 VM 的實例查看。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell 目前只提供修補程式延伸模組的相關資訊。 您 `patchStatus` 也可以透過 PowerShell 立即取得有關的資訊。

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vm get 實例-view](/cli/azure/vm#az-vm-get-instance-view) 來存取您 vm 的實例查看。

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>瞭解 VM 的修補狀態

`patchStatus`實例視圖回應的區段提供最新評量的詳細資料，以及您 VM 的最後一個修補程式安裝。

您 VM 的評量結果可以在區段底下進行審核 `availablePatchSummary` 。 系統會定期針對已啟用自動 VM 來賓修補的 VM 進行評定。 在和結果下提供評量之後可用的修補程式計數 `criticalAndSecurityPatchCount` `otherPatchCount` 。 自動 VM 來賓修補將會安裝所有在 *重大* 和 *安全性* 修補程式分類下評估的修補程式。 略過任何其他評估的修補程式。

您 VM 的修補程式安裝結果可以在一節下進行檢查 `lastPatchInstallationSummary` 。 本節提供 VM 上最後一次修補程式安裝嘗試的詳細資料，包括已安裝、擱置、失敗或略過的修補程式數目。 修補程式只有在 VM 的離峰時段維護期間才會安裝。 在下一個離峰時段的維護期間，會自動重試暫止和失敗的修補程式。

## <a name="on-demand-patch-assessment"></a>隨選修補程式評估
如果您的 VM 已啟用自動 VM 來賓修補，則會在 VM 的離峰時段于 VM 上執行定期修補程式評估。 此程式會自動執行，而最新評量的結果可以透過 VM 的實例視圖來檢查，如本檔稍早所述。 您也可以隨時針對您的 VM 觸發隨選修補程式評定。 修補程式評估可能需要幾分鐘的時間才能完成，而且最新評量的狀態會在 VM 的實例視圖上更新。

啟用預覽功能時，必須針對每個訂用帳戶 *InGuestPatchVMPreview* 的功能進行一次性選擇。 您可以遵循先前針對自動 VM 來賓修補所述的 [預覽啟用](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) 程式，啟用隨選修補程式評估的功能預覽。

> [!NOTE]
>隨選修補程式評估不會自動觸發修補程式安裝。 在 VM 的離峰時段內，評估及適用的 VM 修補程式將只會安裝在 VM 的離峰時段，遵循本檔稍早所述的可用性優先修補程式。

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
使用 [AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) 指令程式來評估您的虛擬機器可用的修補程式。

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
使用 [az vm 評定-修補程式](/cli/azure/vm#az-vm-assess-patches) 來評估虛擬機器的可用修補程式。

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [深入瞭解如何建立和管理 Windows 虛擬機器](tutorial-manage-vm.md)
