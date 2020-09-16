---
title: Azure 虛擬機器擴展集執行個體的終止通知
description: 瞭解如何啟用 Azure 虛擬機器擴展集實例的終止通知
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: d4b31eb59ed0bae2afe408546ece66eacade9ddb
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603827"
---
# <a name="terminate-notification-for-azure-virtual-machine-scale-set-instances"></a>Azure 虛擬機器擴展集執行個體的終止通知
擴展集實例可以選擇接收實例終止通知，以及將預先定義的延遲時間設定為終止作業。 終止通知會透過 Azure Metadata Service 傳送 [Scheduled Events](../virtual-machines/windows/scheduled-events.md)，以提供具影響力作業（例如重新開機和重新部署）的通知和延遲。 解決方案會將另一個事件（終止）新增至 Scheduled Events 清單，而終止事件的相關延遲將取決於使用者在其擴展集模型設定中所指定的延遲限制。

註冊到此功能之後，擴展集實例不需要等待指定的超時時間過期，再刪除實例。 收到終止通知之後，就可以在終止超時時間到期之前，隨時選擇要刪除的實例。

## <a name="enable-terminate-notifications"></a>啟用終止通知
有多種方式可以在擴展集實例上啟用終止通知，如下列範例所述。

### <a name="azure-portal"></a>Azure 入口網站

下列步驟會在建立新的擴展集時啟用終止通知。 

1. 移至 **虛擬機器擴展集**。
1. 選取 [ **+ 新增** ] 以建立新的擴展集。
1. 移至 [ **管理** ] 索引標籤。 
1. 找出 **實例終止** 區段。
1. 如需 **終止通知**，請選取 [ **開啟**]。
1. 針對 **終止延遲 (分鐘) **，請設定所需的預設超時。
1. 當您完成建立新的擴展集時，請選取 [ **審核 + 建立** ] 按鈕。 

> [!NOTE]
> 在 Azure 入口網站中，您無法在現有的擴展集上設定終止通知

### <a name="rest-api"></a>REST API

下列範例會在擴展集模型上啟用終止通知。

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

上述區塊會針對擴展集內所有實例上的任何終止作業，指定5分鐘的延遲延遲（ *PT5M*) 所示） (。 *NotBeforeTimeout*欄位可採用 ISO 8601 格式的5到15分鐘之間的任何值。 您可以藉由修改上面所述之*terminateNotificationProfile*底下的*notBeforeTimeout*屬性，來變更終止作業的預設超時。

在擴展集模型上啟用 *scheduledEventsProfile* 並設定 *notBeforeTimeout*之後，請將個別實例更新為 [最新的模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) ，以反映變更。

> [!NOTE]
>擴展集實例上的終止通知只能使用 API 版本2019-03-01 和更新版本啟用

### <a name="azure-powershell"></a>Azure PowerShell
建立新的擴展集時，您可以使用 [>new-azvmssconfig](/powershell/module/az.compute/new-azvmssconfig) Cmdlet 在擴展集上啟用終止通知。

此範例腳本會逐步解說如何使用設定檔建立擴展集和相關聯的資源： [建立完整的虛擬機器擴展集](./scripts/powershell-sample-create-complete-scale-set.md)。 您可以藉由將 *TerminateScheduledEvents* 和 *TerminateScheduledEventNotBeforeTimeoutInMinutes* 參數新增至設定物件來建立擴展集，以提供設定終止通知。 下列範例會啟用延遲時間為10分鐘的功能。

```azurepowershell-interactive
New-AzVmssConfig `
  -Location "VMSSLocation" `
  -SkuCapacity 2 `
  -SkuName "Standard_DS2" `
  -UpgradePolicyMode "Automatic" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 10
```

使用 [new-azvmss](/powershell/module/az.compute/update-azvmss) 指令程式可在現有的擴展集上啟用終止通知。

```azurepowershell-interactive
Update-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -TerminateScheduledEvents $true `
  -TerminateScheduledEventNotBeforeTimeoutInMinutes 15
```
上述範例會在現有的擴展集上啟用終止通知，並為 terminate 事件設定15分鐘的超時時間。

在擴展集模型上啟用排定的事件並設定 timeout 之後，請將個別實例更新為 [最新的模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) ，以反映變更。

### <a name="azure-cli-20"></a>Azure CLI 2.0

下列範例是在建立新的擴展集時啟用終止通知。

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

上述範例會先建立資源群組，然後建立新的擴展集，並將終止通知啟用為10分鐘的預設超時。

下列範例適用于在現有的擴展集中啟用終止通知。

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --enable-terminate-notification true \
  --terminate-notification-time 10
```

## <a name="get-terminate-notifications"></a>取得終止通知

終止通知是透過 [Scheduled Events](../virtual-machines/windows/scheduled-events.md)（也就是 Azure Metadata Service）來傳遞。 如果您是使用可由 VM 內存取的 REST 端點來執行虛擬機器，Azure 中繼資料服務會公開這類相關資訊。 此資訊可透過無法路由傳送的 IP 取得，因此不會在 VM 之外公開。

當您第一次提出事件要求時，會為您的擴展集啟用 Scheduled Events。 您可以預期第一次呼叫最多2分鐘的回應延遲。 定期查詢端點以偵測即將進行的維護事件，以及進行中維護活動的狀態。

如果擴展集實例未提出要求24小時，則會停用擴展集的 Scheduled Events。

### <a name="endpoint-discovery"></a>端點探索
針對已啟用 VNET 的 Vm，Metadata Service 可從靜態不可路由傳送的 IP 169.254.169.254 取得。

最新版已排定事件的完整端點為：
> 'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="query-response"></a>查詢回應
回應包含排定的事件陣列。 空白陣列表示目前沒有任何排定的事件。

在有排定事件的情況下，回應會包含事件陣列。 針對「終止」事件，回應看起來會如下所示：
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
*DocumentIncarnation*是 ETag，可讓您輕鬆地檢查事件承載自上一次查詢之後是否已變更。

如需上述每個欄位的詳細資訊，請參閱適用于 [Windows](../virtual-machines/windows/scheduled-events.md#event-properties) 和 [Linux](../virtual-machines/linux/scheduled-events.md#event-properties)的 Scheduled Events 檔。

### <a name="respond-to-events"></a>回應事件
當您學會即將推出的活動並完成您的邏輯以順利關機之後，您可以使用 EventId 對中繼資料服務進行 POST 呼叫，以核准未處理的事件。 POST 呼叫向 Azure 指出，它可以繼續執行 VM 刪除。

以下是 POST 要求本文中預期的 json。 要求應包含 StartRequests 的清單。 每個 StartRequest 都包含您想要加速之事件的 EventId：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

確定擴展集中的每個 VM 只核准該 VM 相關的 EventID。 VM 可以 [透過實例中繼資料](virtual-machine-scale-sets-instance-ids.md#instance-metadata-vm-name)取得自己的 vm 名稱。 此名稱採用 "{scale-set-name} _ {instance-id}" 格式，而且將會顯示在上述查詢回應的 [資源] 區段中。

您也可以參考範例腳本，以查詢和回應事件 [Python](../virtual-machines/linux/scheduled-events.md#python-sample)。

## <a name="tips-and-best-practices"></a>訣竅和最佳做法
-   僅終止「刪除」作業的通知–所有刪除作業 (手動刪除或自動調整起始的) 將會產生終止事件（如果擴展集已啟用 *scheduledEventsProfile* ）。 其他作業（例如重新開機、重新安裝映射、重新部署和停止/解除配置）不會產生終止事件。 無法針對低優先順序的 Vm 啟用終止通知。
-   沒有必要的等候等待時間，您可以在收到事件之後，以及事件的 *NotBefore* 時間到期之前，隨時啟動終止作業。
-   在超時時強制刪除-沒有任何功能可在產生事件之後擴充超時值。 當超時時間過期時，將會處理擱置中的終止事件，並且將刪除 VM。
-   可修改的超時值–您可以在刪除實例之前隨時修改超時值，方法是修改擴展集模型上的 *notBeforeTimeout* 屬性，並將 VM 實例更新為最新模型。
-   核准所有暫止的刪除–如果 VM_1 上的暫止刪除尚未核准，而且您已在 VM_2 核准另一個終止事件，則 VM_2 不會被刪除，直到 VM_1 的終止事件核准，或其超時時間已過為止。 一旦您核准 VM_1 的終止事件之後，就會刪除 VM_1 和 VM_2。
-   核准所有同時刪除-擴充上述範例，如果 VM_1 且 VM_2 具有相同的 *NotBefore* 時間，則必須核准這兩個終止事件，否則在到期前都不會刪除 VM。

## <a name="troubleshoot"></a>疑難排解
### <a name="failure-to-enable-scheduledeventsprofile"></a>無法啟用 scheduledEventsProfile
如果您收到「BadRequest」錯誤，並出現錯誤訊息，指出「在 ' VirtualMachineProfile ' 類型的物件上找不到成員 ' scheduledEventsProfile '」，請檢查用於擴展集作業的 API 版本。 需要計算 API 版本 **2019-03-01** 或更高版本。 

### <a name="failure-to-get-terminate-events"></a>無法取得終止事件
如果您未透過 Scheduled Events 取得任何 **終止** 事件，請檢查用來取得事件的 API 版本。 終止事件需要 Metadata Service API 版本 **2019-01-01** 或更高版本。
>'http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01'

### <a name="getting-terminate-event-with-incorrect-notbefore-time"></a>取得不正確 NotBefore 時間的終止事件  
在擴展集模型上啟用 *scheduledEventsProfile* 並設定 *notBeforeTimeout*之後，請將個別實例更新為 [最新的模型](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) ，以反映變更。

## <a name="next-steps"></a>後續步驟
了解如何在虛擬機器擴展集上[部署您的應用程式](virtual-machine-scale-sets-deploy-app.md)。
