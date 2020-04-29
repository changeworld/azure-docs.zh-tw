---
title: 適用于 Azure 中 Windows Vm 的 Scheduled Events
description: 針對您的 Windows 虛擬機器，使用 Azure 中繼資料服務排定事件。
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: mimckitt
ms.openlocfilehash: 105279940546c8e5b40d1d8378b35f85af1ea98b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82099541"
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Azure 中繼資料服務：Windows VM 的已排定事件

排程的事件是 Azure 中繼資料服務，可為您的應用程式提供時間準備虛擬機器維護。 它提供即將進行維護事件 (例如重新啟動) 的相關資訊，讓您的應用程式可進行準備以及限制中斷。 它適用於所有 Azure 虛擬機器類型 (包括 Windows 和 Linux 上的 PaaS 和 IaaS)。 

如需 Linux 上已排定事件的資訊，請參閱 [Linux VM 的已排定事件](../linux/scheduled-events.md)。

> [!Note] 
> 已排定事件已在所有 Azure 區域中正式推出。 請參閱[版本和區域可用性](#version-and-region-availability)以取得最新的版本資訊。

## <a name="why-scheduled-events"></a>為什麼要使用排定的事件？

許多應用程式可受益於虛擬機器維護的準備時間。 時間可用來執行應用程式特定的工作，能改善可用性、可靠性與服務性，包括： 

- 檢查點和還原
- 清空連線
- 主要複本容錯移轉 
- 從負載平衡器移除集區
- 事件記錄
- 正常關機 

使用排程的事件，應用程式就可以探索維護所發生的時間，以及限制其影響的觸發工作。 啟用已排定事件可讓您的虛擬機器在執行維護活動之前有最短的時間。 如需詳細資料，請參閱下面的＜事件排程＞一節。

排程的事件會提供下列使用案例中的事件：
- [平臺起始的維護](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)（例如，VM 重新開機、即時移轉或保留主機的記憶體更新）
- 虛擬機器在降級的[主機硬體](https://azure.microsoft.com/blog/find-out-when-your-virtual-machine-hardware-is-degraded-with-scheduled-events)上執行，預測即將失敗
- 使用者起始的維護 (例如，使用者重新啟動或重新部署 VM)
- [找出 VM](spot-vms.md)和[點擴展集](../../virtual-machine-scale-sets/use-spot.md)實例收回

## <a name="the-basics"></a>基本概念  

如果您是使用可由 VM 內存取的 REST 端點來執行虛擬機器，Azure 中繼資料服務會公開這類相關資訊。 這項資訊是透過無法路由傳送的 IP 取得，因此不會在 VM 之外公開。

### <a name="endpoint-discovery"></a>端點探索
針對已啟用 VNET 的 VM，中繼資料服務可以從靜態非可路由 IP `169.254.169.254` 取得。 最新版已排定事件的完整端點為： 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01`

如果虛擬機器不是在虛擬網路中建立，則針對雲端服務和傳統 VM 的預設案例，需要其他邏輯來探索可使用的 IP 位址。 請參閱此範例以了解如何[探索主機端點](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)。

### <a name="version-and-region-availability"></a>版本和區域可用性
排程的事件服務已進行版本設定。 版本是必要項目，且目前版本為 `2019-01-01`。

| 版本 | 版本類型 | 區域 | 版本資訊 | 
| - | - | - | - |
| 2019-01-01 | 正式運作 | 全部 | <li> 已新增對虛擬機器擴展集「終止」的支援 |
| 2017-11-01 | 正式運作 | 全部 | <li> 已新增對點 VM 收回事件 ' Preempt ' 的支援<br> | 
| 2017-08-01 | 正式運作 | 全部 | <li> 已從 IaaS VM 的資源名稱中移除預留底線<br><li>強制所有要求的中繼資料標頭需求 | 
| 2017-03-01 | 預覽 | 全部 |<li>初始版本 |

> [!NOTE] 
> 先前排定事件的預覽版支援作為 API 版本的 {latest}。 此格式將不再受到支援且之後會遭到取代。

### <a name="enabling-and-disabling-scheduled-events"></a>啟用和停用已排定事件
系統會在您第一次提出事件要求時，為您的服務啟用「已排定事件」。 您可能會在第一次呼叫中遇到長達兩分鐘的延遲回應。 您應該定期查詢端點，以偵測近期的維護事件，以及執行之維護活動的狀態。

如果長達 24 小時未提出要求，您的服務就會停用已排定事件。

### <a name="user-initiated-maintenance"></a>使用者起始的維護
使用者透過 Azure 入口網站、API、CLI 或 PowerShell 起始的虛擬機器維護，將會產生「排定的事件」。 這可讓您測試應用程式中的維護準備邏輯，讓應用程式可以為使用者啟動的維護預作準備。

重新啟動虛擬機器會排定 `Reboot` 類型的事件。 重新部署虛擬機器會排定 `Redeploy` 類型的事件。

## <a name="using-the-api"></a>使用 API

### <a name="headers"></a>headers
查詢中繼資料服務時，您必須提供 `Metadata:true` 標頭以免不小心重新導向要求。 所有排程的事件都需要 `Metadata:true` 標頭。 要求中未包含標頭會導致中繼資料服務不正確的要求回應。

### <a name="query-for-events"></a>查詢事件
您只要進行下列呼叫，即可查詢排定的事件：

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01 -H @{"Metadata"="true"}
```

回應包含排定的事件陣列。 空白陣列表示目前沒有任何排定的事件。
在有排定事件的情況下，回應會包含事件陣列︰ 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze" | "Preempt" | "Terminate",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},
        }
    ]
}
```
DocumentIncarnation 是 ETag，透過它很容易就能檢查自從上次查詢之後，事件裝載是否已變更。

### <a name="event-properties"></a>事件屬性
|屬性  |  描述 |
| - | - |
| EventId | 此事件的全域唯一識別碼。 <br><br> 範例： <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 此事件造成的影響。 <br><br> 值： <br><ul><li> `Freeze`：虛擬機器已排程暫停幾秒鐘。 CPU 和網路連線可能會暫止，但不會影響記憶體或開啟的檔案。 <li>`Reboot`：虛擬機器已排定要重新開機 (非持續性記憶體都會遺失)。 <li>`Redeploy`︰虛擬機器已排定要移至另一個節點 (暫時磁碟都會遺失)。 <li>`Preempt`：正在刪除點虛擬機器（暫時磁片會遺失）。 <li> `Terminate`：已排程要刪除虛擬機器。 |
| ResourceType | 受此事件影響的資源類型。 <br><br> 值： <ul><li>`VirtualMachine`|
| 資源| 受此事件影響的資源清單。 其中最多只能包含來自一個[更新網域](manage-availability.md)的機器，但不能包含更新網域中的所有機器。 <br><br> 範例： <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| 事件狀態 | 此事件的狀態。 <br><br> 值： <ul><li>`Scheduled`︰此事件已排定在 `NotBefore` 屬性所指定的時間之後啟動。<li>`Started`︰已啟動事件。</ul> 如果未提供任何 `Completed` 或類似的狀態，事件完成時，將不會再傳回事件。
| NotBefore| 自此之後可能會啟動此事件的時間。 <br><br> 範例： <br><ul><li> Mon, 19 Sep 2016 18:29:47 GMT  |

### <a name="event-scheduling"></a>事件排程
系統會根據事件類型，為每個事件在未來安排最少的時間量。 事件的 `NotBefore` 屬性會反映這個時間。 

|EventType  | 最短時間通知 |
| - | - |
| 凍結| 15 分鐘 |
| 重新啟動 | 15 分鐘 |
| 重新部署 | 10 分鐘 |
| Preempt | 30 秒 |
| 結束 | [可](../../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md#enable-terminate-notifications)設定的使用者：5到15分鐘 |

> [!NOTE] 
> 在某些情況下，Azure 會因為硬體降級而預測主機失敗，並會嘗試藉由排程遷移來減輕服務中斷的影響。 受影響的虛擬機器將會收到已排程`NotBefore`的事件，其通常是在未來幾天的時間。 實際的時間會依預測的失敗風險評估而有所不同。 Azure 會在可能的情況下，儘量提供7天的事先通知，但實際的時間會有所不同，如果預測是硬體故障即將的機率很高，可能會較小。 若要將服務的風險降到最低，以免系統起始遷移之前發生硬體故障，建議您儘快自動重新部署虛擬機器。

### <a name="event-scope"></a>事件範圍     
排程的事件會傳送到：
 - 獨立虛擬機器
 - 雲端服務中的所有虛擬機器      
 - 可用性設定組中的所有虛擬機器      
 - 擴展集放置群組中的所有虛擬機器。         

因此，您應該檢查事件中的 `Resources` 欄位以找出哪些 VM 即將受到影響。 

### <a name="starting-an-event"></a>啟動事件 

在您得知即將發生的事件，並完成正常關機邏輯之後，即可使用 `EventId` 向中繼資料服務進行 `POST` 呼叫，以核准未處理的事件。 對 Azure 來說，這意謂著它可以將通知時間縮到最短 (可能的話)。 

以下是 `POST` 要求本文中必須要有的 json。 要求需包含 `StartRequests` 清單。 每個 `StartRequest` 都包含您需要加速之事件的 `EventId`：
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2019-01-01
```

> [!NOTE] 
> 認可事件時，即會允許事件中所有 `Resources` 的事件繼續進行，而不僅是認可此事件的虛擬機器。 因此，您可以選擇一個領導者來協調認可；最簡單的方法是選擇 `Resources` 欄位的第一部機器。


## <a name="powershell-sample"></a>PowerShell 範例 

下列範例會查詢中繼資料服務來找出已排定的事件，並核准每個未處理的事件。

```powershell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $uri)
{
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2019-01-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>後續步驟 

- 在 Azure Friday 上觀賞[已排定事件示範](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)。 
- 請參閱[Azure 實例中繼資料 Scheduled Events GitHub 存放庫](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)中的 Scheduled Events 程式碼範例
- 深入瞭解[實例中繼資料服務](instance-metadata-service.md)中提供的 api。
- 瞭解[Azure 中的 Windows 虛擬機器預定進行的維修](planned-maintenance.md)。
