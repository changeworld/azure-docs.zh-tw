---
title: 建立 Azure Spot VM 的比例集
description: 瞭解如何創建使用 Spot VM 節省成本的 Azure 虛擬機縮放集。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: a7bd22032a554c83a2ea2323ffdb3ae52dfe4faf
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545944"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>用於虛擬機縮放集的 Azure Spot VM 

在規模集中使用 Azure Spot 使您能夠利用我們未使用的容量,從而顯著節省成本。 在 Azure 需要返回容量的任何時間點,Azure 基礎結構將驅逐 Spot 實例。 因此,Spot 實例非常適合處理批處理作業、開發/測試環境、大型計算工作負載等中斷的工作負載。

可用容量的數量可能因大小、區域、一天中的時間等而異。 在規模集中部署 Spot 實例時,Azure 僅在存在可用容量但這些實例沒有 SLA 時才會分配實例。 Spot 比例集部署在單個容錯域中,並且不提供高可用性保證。


## <a name="pricing"></a>定價

基於區域和 SKU 的 Spot 實例定價是可變的。 有關詳細資訊,請參閱[Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)的定價。 


使用可變定價,您可以選擇使用最多 5 個小數位設置最高價格(美元 (USD)。 例如,該值`0.98765`將是每小時 0.98765 美元的最高價格。 如果將最高價格設置為`-1`,則實例不會根據價格被逐出。 實例的價格將是 Spot 的當前價格或標準實例的價格,只要有容量和配額可用,價格就更少。

## <a name="eviction-policy"></a>驅逐政策

建立 Spot 比例集時,可以將逐出策略設定為 *「取消分配*」(預設 *)或刪除*。 

*"取消分配"* 策略會將被逐出的實例移動到已停止的已停止的已轉移狀態,從而允許您重新部署已展開的實例。 不過，不保證配置會成功。 已解除配置的 VM 會依擴展集執行個體配額計算，您將需要支付您的基礎磁碟費用。 

如果希望在 Spot 比例集中的實體在被逐出時刪除它們,則可以將逐出策略設定為*刪除*。 收回原則設定為刪除的情況下，您可以藉由增加擴展集執行個體計數屬性來建立新的 VM。 Azure 會一同刪除已收回的 VM 與其基礎磁碟，因此您將不需要支付此儲存體的費用。 您也可以使用擴展集的自動調整功能，自動嘗試和補償收回的 VM，不過不保證配置會成功。 建議您僅在將刪除策略設置為刪除時才在 Spot 比例集上使用自動縮放功能,以避免磁碟的成本並達到配額限制。 

用戶可以選擇加入通過[Azure 計劃事件](../virtual-machines/linux/scheduled-events.md)接收 VM 內通知。 如果 VM 被逐出,您將通知您,並且在驅逐之前,您將有 30 秒的時間完成任何作業並執行關閉任務。 


## <a name="deploying-spot-vms-in-scale-sets"></a>在比例集中部署 Spot VM

要在比例集中部署 Spot VM,可以將新的*優先權*標誌設置為 *「Spot*」。。 縮放集中的所有 VM 都將設置為"Spot" 要使用 Spot VM 建立比例集,請使用以下方法之一:
- [Azure 入口網站](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure 資源管理員範本](#resource-manager-templates)

## <a name="portal"></a>入口網站

創建使用 Spot VM 的比例集的過程與[入門文章中](quick-create-portal.md)詳述的過程相同。 部署比例集時,可以選擇設定「Spot」標誌和逐出策略:![使用 Spot VM 建立比例集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

使用 Spot VM 創建比例集的過程與[入門文章中](quick-create-cli.md)詳述的過程相同。 只需添加"-優先順序點",然後添加`--max-price`。 在此示例中,我們使用`-1``--max-price`, 以便實例不會根據價格被逐出。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

使用 Spot VM 創建比例集的過程與[入門文章中](quick-create-powershell.md)詳述的過程相同。
只需添加"-優先順序點",然後向`-max-price`[New-AzVmsConfig 提供](/powershell/module/az.compute/new-azvmssconfig)。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Resource Manager 範本

創建使用 Spot VM 的比例集的過程與[Linux](quick-create-template-linux.md)或[Windows](quick-create-template-windows.md)入門文章中詳述的過程相同。 

對於 Spot 範本`"apiVersion": "2019-03-01"`部署, 請使用或更高版本。 將`priority``evictionPolicy``billingProfile`與 屬性加入樣本`"virtualMachineProfile":`的項目: 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

要在實體被逐出後將刪除, 請將`evictionPolicy`參數變更為`Delete`。

## <a name="faq"></a>常見問題集

**問:** 建立後,Spot 實例是否與標準實例相同?

**答:** 是,除了 Spot VM 沒有 SLA,它們可以隨時被逐出。


**問:** 當您被逐出時該怎麼辦,但仍需要容量?

**答:** 如果您需要容量,我們建議您使用標準 VM 而不是 Spot VM。


**問:** 如何管理 Spot 的配額?

**答:** 競價實例和標準實例將具有單獨的配額池。 競價配額將在 VM 和縮放設置實例之間共用。 如需詳細資訊，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**問:** 我可以申請 Spot 的額外配額嗎?

**答:** 是的,您將能夠通過[標準配額請求流程](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)提交請求,以增加 Spot VM 的配額。


**問:** 是否可以將現有比例集轉換為「點」比例集?

**答:** 否,`Spot`僅在創建時支援設置標誌。


**問:** 如果我用於`low`低優先順序縮放集,是否需要開始`Spot`使用 ?

**答:** 現在,兩`low``Spot`者都工作,但您應該開始過渡到`Spot`使用 。


**問:** 是否可以創建具有一般 VM 和 Spot VM 的縮放集?

**答:** 否,比例集不能支援多個優先順序類型。


**問:** 是否可以將自動縮放與競量縮放集一起使用?

**答:** 可以,您可以在「競量」比例集中設置自動縮放規則。 如果 VM 被逐出,自動縮放可以嘗試創建新的 Spot VM。 請記住，不保證容量足夠。 


**問:** 自動縮放是否同時適用於逐出策略(取消分配和刪除)?

**答:** 建議您在使用自動縮放時將逐出策略設置為刪除。 這是因為解除配置執行個體是按照擴展集的容量計數進行計算。 使用自動調整時，由於已解除配置收回的執行個體，可能會很快達到目標執行個體計數。 


**問:** 哪些通道支援 Spot VM?

**答:** 有關 Spot VM 可用性,請參閱下表。

<a name="channel"></a>

| Azure 頻道               | Azure Spot VM 可用性       |
|------------------------------|-----------------------------------|
| Enterprise 合約         | 是                               |
| 隨用隨付                | 是                               |
| 雲端服務提供者 (CSP) | [連絡您的合作夥伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 優點                     | 無法使用                     |
| 贊助                    | 無法使用                     |
| 免費試用                   | 無法使用                     |


**問:** 我在哪裡可以發佈問題?

**答:** 您可以在[Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html)`azure-spot`上發表和標記您的問題。 

## <a name="next-steps"></a>後續步驟

如需定價詳細資料，請參閱[虛擬機器擴展集定價網頁](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
