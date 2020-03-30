---
title: 終止 Azure 虛擬機器縮放集實例的通知
description: 瞭解如何為 Azure 虛擬機器縮放集實例啟用終止通知
author: avirishuv
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: 6023e9bf7539b79446d0135ba731b61be166dd6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250747"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>終止 Azure 虛擬機器縮放集實例的通知
縮放集實例可以選擇接收實例終止通知，並將預定義的延遲超時設置為終止操作。 終止通知通過 Azure 中繼資料服務 （[計畫事件](../virtual-machines/windows/scheduled-events.md)） 發送，該服務為影響性操作（如重新開機和重新部署）提供通知和延遲。 解決方案將另一個事件 （終止 ） 添加到計畫事件清單中，終止事件的關聯延遲將取決於使用者在其規模集模型配置中指定的延遲限制。

註冊到該功能後，縮放集實例無需等待指定的超時過期，然後刪除實例。 收到終止通知後，實例可以選擇在終止超時到期之前隨時刪除。

## <a name="enable-terminate-notifications"></a>啟用終止通知
在規模設置實例上啟用終止通知的方法有多種，如下例中詳細說明。

### <a name="azure-portal"></a>Azure 入口網站

在創建新比例集時，以下步驟啟用終止通知。 

1. 轉到**虛擬機器縮放集**。
1. 選擇 **+ 添加**以創建新的比例集。
1. 轉到 **"管理"** 選項卡。 
1. 查找**實例終止**部分。
1. 對於**實例終止通知**，請選擇 **"打開**"。
1. 對於**終止延遲（分鐘），** 設置所需的預設超時。
1. 創建新比例集後，選擇 **"查看 + 創建**"按鈕。 

> [!NOTE]
> 無法在 Azure 門戶中的現有規模集上設置終止通知

### <a name="rest-api"></a>REST API

以下示例在規模集模型上啟用終止通知。

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "virtualMachineProfile": {
            "scheduledEventsProfile": {
                "terminateNotificationProfile": {
                    "notBeforeTimeout":"PT5M",
                    "enable":true
                }
            }
        }
    }        
}

```

上述塊為縮放集中的所有實例的任何終止操作指定 5 分鐘的超時延遲（如*PT5M*所示）。 在 ISO 8601 格式中 *，不執行之前的*欄位可以獲取 5 到 15 分鐘之間的任何值。 您可以通過修改上述 *"終止通知設定檔*"下的 *"不前超時*"屬性來更改終止操作的預設超時。

在比例集模型上啟用*計畫事件設定檔*並設置 *"不前超時*"後，將各個實例更新到[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改。

> [!NOTE]
>只能使用 API 版本 2019-03-01 及以上啟用規模設置實例上的終止通知

### <a name="azure-powershell"></a>Azure PowerShell
創建新比例集時，可以使用[New-AzVmsConfig](/powershell/module/az.compute/new-azvmssconfig) Cmdlet 在比例集中啟用終止通知。

此示例腳本使用設定檔流覽比例集和相關資源的創建：[創建完整的虛擬機器規模集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以通過將*參數終止計畫事件*和*終止計畫事件 Not 在TimeoutIn分鐘之前*添加到設定物件以創建比例集來提供配置終止通知。 以下示例啟用延遲超時為 10 分鐘的功能。

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

使用[Update-AzVms](/powershell/module/az.compute/update-azvmss) Cmdlet 在現有規模集中啟用終止通知。

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
上述示例啟用現有規模集中的終止通知，並為終止事件設置 15 分鐘的超時。

在比例集模型上啟用計畫事件並設置超時後，將各個實例更新到[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改。

### <a name="azure-cli-20"></a>Azure CLI 2.0

以下示例用於在創建新比例集時啟用終止通知。

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --terminate-notification-time 10
```

上面的示例首先創建一個資源組，然後創建一個新的比例集，並啟用了 10 分鐘的預設超時終止通知。

以下示例用於在現有規模集中啟用終止通知。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>獲取終止通知

終止通知通過[計畫事件](../virtual-machines/windows/scheduled-events.md)傳遞 ，這是 Azure 中繼資料服務。 如果您是使用可由 VM 內存取的 REST 端點來執行虛擬機器，Azure 中繼資料服務會公開這類相關資訊。 資訊可通過不可路由的 IP 獲得，因此不會在 VM 外部公開。

首次請求事件時，會為規模設置啟用計畫事件。 您可以在第一次呼叫中延遲回應長達兩分鐘。 定期查詢終結點，以檢測即將發生的維護事件和正在進行的維護活動的狀態。

如果比例集實例在 24 小時內未發出請求，則為規模集禁用計畫事件。

### <a name="endpoint-discovery"></a>端點發現
對於啟用 VNET 的 VM，中繼資料服務可從靜態不可路由 IP 169.254.169.254 獲得。

最新版已排定事件的完整端點為：
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>查詢回應
回應包含排定的事件陣列。 空白陣列表示目前沒有任何排定的事件。

在有排定事件的情況下，回應會包含事件陣列。 對於"終止"事件，回應如下所示：
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
*DocumentIncarnation*是一個 ETag，它提供了一種簡單的方法來檢查事件裝載自上次查詢以來是否已更改。

有關上述每個欄位的詳細資訊，請參閱[Windows](../virtual-machines/windows/scheduled-events.md#event-properties)和[Linux](../virtual-machines/linux/scheduled-events.md#event-properties)的"計畫事件"文檔。

### <a name="respond-to-events"></a>回應事件
一旦您得知即將發生的事件並完成了正常關閉的邏輯，您可以通過使用 EventId 對中繼資料服務進行 POST 調用來批准未完成的事件。 POST 調用向 Azure 指示可以繼續刪除 VM。

下面是 POST 請求正文中預期中的 json。 請求應包含啟動請求的清單。 每個啟動請求都包含要加速的事件的事件事件 Id：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

確保規模集中的每個 VM 僅批准與該 VM 相關的 EventID。 VM 可以通過[實例中繼資料](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)獲取自己的 VM 名稱。 此名稱採用"[縮放集名稱][實例-id]"的形式，將顯示在上述查詢回應的"資源"部分中。

您還可以使用[PowerShell](../virtual-machines/windows/scheduled-events.md#powershell-sample)和[Python](../virtual-machines/linux/scheduled-events.md#python-sample)引用示例腳本來查詢和回應事件。

## <a name="tips-and-best-practices"></a>祕訣和最佳作法
-   僅在"刪除"操作上終止通知 - 如果規模集已啟用*計畫事件設定檔*，則所有刪除操作（手動刪除或自動縮放啟動的縮放）將生成終止事件。 重新開機、重新映射、重新部署和停止/取消分配等其他操作不會生成終止事件。 無法為低優先順序 VM 啟用終止通知。
-   無需強制等待超時 - 您可以在收到事件後和事件"*不提前之前"* 之前隨時啟動終止操作。
-   超時時強制刪除 – 建置事件後無法擴展超時值。 超時過期後，將處理掛起的終止事件，並刪除 VM。
-   可修改超時值 – 您可以通過修改規模集模型*上的 notAttimeout*屬性並將 VM 實例更新為最新模型，在刪除實例之前的任何時間修改超時值。
-   批准所有掛起的刪除 - 如果未批准的VM_1上存在掛起的刪除，並且您已批准VM_2上的另一個終止事件，則VM_2在批准VM_1終止事件或其超時已過之前不會刪除。 批准VM_1終止事件後，將刪除VM_1和VM_2。
-   批准所有同時刪除 – 擴展上述示例，如果VM_1和VM_2在*時間之前具有相同的，* 則必須批准兩個終止事件，或者在超時到期之前兩個 VM 都不會刪除。

## <a name="troubleshoot"></a>疑難排解
### <a name="failure-to-enable-scheduledeventsprofile"></a>無法啟用計畫事件設定檔
如果收到"BadRequest"錯誤，並出現錯誤訊息，指出"無法在類型為"虛擬機器組態檔"的物件上找到成員'計畫事件設定檔'"，請檢查用於縮放集操作的 API 版本。 需要計算 API 版本**2019-03-01**或更高版本。 

### <a name="failure-to-get-terminate-events"></a>無法獲取終止事件
如果無法通過計畫事件獲取任何**終止**事件，請檢查用於獲取事件的 API 版本。 終止事件需要中繼資料服務 API 版本**2019-01-01**或更高版本。
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>獲取與不正確的"未之前"事件  
在比例集模型上啟用*計畫事件設定檔*並設置 *"不前超時*"後，將各個實例更新到[最新模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)以反映更改。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
