---
title: 在 Azure 中監視 Windows VM 的 Scheduled Events
description: 了解如何監視 Azure 虛擬機器的 Scheduled Events。
author: mysarn
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 3f3bf83d8155383757cc87749281c688bd281a4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82099592"
---
# <a name="monitoring-scheduled-events"></a>監視 Scheduled Events

每天都會將更新套用到 Azure 的不同部分，以確保執行服務的安全並保持在最新狀態。 除了已規劃的更新之外，也可能包含未在規劃中的事件。 例如，如果偵測到任何硬體效能降低或錯誤，則 Azure 服務可能需要執行未在規劃中的維護。 使用即時移轉時，記憶體會保留更新且通常會對更新的影響進行嚴格限制；在大多數情況下，這些事件對客戶而言幾乎為完全透明化且不會有任何影響，最多對虛擬機器造成幾秒鐘的凍結。 不過，對於某些應用程式，即使只有幾秒鐘的虛擬機器凍結也可能會造成影響。 建議事先了解即將推出的 Azure 維護，以確保擁有這些應用程式的最佳體驗。 [Scheduled Events 服務](scheduled-events.md)提供程式設計介面，以通知即將進行維護，並讓您順利地處理維護。 

在本文中，我們將示範如何使用 Scheduled Events，以取得可能會影響 VM 的維護事件通知，並建立一些可協助監視和分析的基本自動化流程。


## <a name="routing-scheduled-events-to-log-analytics"></a>將 Scheduled Events 路由傳送至 Log Analytics

Scheduled Events 是 [Azure Instance Metadata Service](instance-metadata-service.md) 的一部分，可在所有 Azure 虛擬機器上使用。 客戶可撰寫自動化來查詢虛擬機器的端點，以尋找已排程的維護通知並執行風險降低措施，例如儲存狀態並讓虛擬機器結束輪替。 建議建置自動化來記錄 Scheduled Events，如此即可取得 Azure 維護事件的稽核記錄。 

在本文中，我們將逐步引導您了解如何將維護 Scheduled Events 擷取至 Log Analytics。 然後，我們會觸發一些基本的通知動作，例如傳送電子郵件給小組，以及取得所有影響虛擬機器的事件歷程記錄檢視。 針對事件彙總和自動化，我們會使用 [Log Analytics](/azure/azure-monitor/learn/quick-create-workspace)，但您可使用任何監視解決方案來收集這些記錄檔並觸發自動化。

![顯示事件生命週期的圖表](./media/notifications/events.png)

## <a name="prerequisites"></a>必要條件

針對本範例，您必須在[可用性設定組中建立 Windows 虛擬機器](tutorial-availability-sets.md)。 針對會影響可用性設定組、雲端服務、虛擬機器擴展集或獨立 VM 中所有虛擬機器的變更，Scheduled Events 可提供相關通知。 我們將會執行[服務](https://github.com/microsoft/AzureScheduledEventsService)，此服務會在作為收集器的其中一部 VM 上輪詢 Scheduled Events，以取得可用性設定組中所有其他 VM 的事件。    

請勿在教學課程結束時刪除群組資源群組。

您也必須[建立 Log Analytics 工作區](/azure/azure-monitor/learn/quick-create-workspace)，其將用來彙總可用性設定組中 VM 的資訊。

## <a name="set-up-the-environment"></a>設定 Azure 環境

在可用性設定組中，現在應該有兩部初始 VM。 現在，我們需要在相同的可用性設定組中建立第三部 VM，其稱為 myCollectorVM。 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

從 [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip) 下載專案的 .zip 安裝檔案。

連線到 **myCollectorVM**，並將 .zip 檔案複製到虛擬機器，然後解壓縮所有檔案。 在 VM 上，開啟 PowerShell 提示字元。 將提示字元移至包含 `SchService.ps1` 的資料夾，例如：`PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`，然後設定服務。

```powershell
.\SchService.ps1 -Setup
```

啟動服務。

```powershell
.\SchService.ps1 -Start
```

服務現在開始隔每 10 秒會輪詢所有 Scheduled Events，並核准這些事件以加快維護速度。  凍結、重新開機、重新部署以及先佔都是由 Scheduled Events 所擷取的事件。   請注意，您可擴充指令碼，以在核准事件之前觸發風險降低措施。

驗證服務狀態，並確定服務正在執行。

```powershell
.\SchService.ps1 -status  
```

此動作應該會傳回 `Running`。

服務現在開始隔每 10 秒會輪詢所有 Scheduled Events，並核准這些事件以加快維護速度。  凍結、重新開機、重新部署以及先佔都是由 Scheduled Events 所擷取的事件。 在核准事件之前，您可擴充指令碼以觸發風險降低措施。

當 Scheduled Event 服務擷取了上述任何事件後，其會記錄在應用程式事件記錄檔的事件狀態、事件類型、資源 (虛擬機器名稱) 和 NotBefore (最小通知週期) 中。 您可在應用程式事件記錄檔中找到識別碼為 1234 的事件。

一旦設定並啟動服務，其會將事件記錄在 Windows 應用程式記錄檔中。   若要確認此功能是否正常，請重新啟動可用性設定組中的其中一部虛擬機器，您應該會在事件檢視器的 [Windows 記錄檔] > [應用程式] 記錄檔中看到正在記錄的事件，並顯示 VM 已重新啟動。 

![事件檢視器的螢幕擷取畫面。](./media/notifications/event-viewer.png)

當 Scheduled Event 服務擷取了事件後，其會記錄在應用程式事件記錄檔，包含事件狀態、事件類型、資源 (VM 名稱) 和 NotBefore (最小通知週期) 中。 您可在應用程式事件記錄檔中找到識別碼為 1234 的事件。

> [!NOTE] 
> 在本範例中，虛擬機器位於可用性設定組中，其可供指定單一虛擬機器作為收集器，以接聽並將 Scheduled Events 路由傳送至 Log Analytics 工作區。 如果有獨立的虛擬機器，即可在每部虛擬機器上執行服務，然後將其個別連線到 Log Analytics 工作區。
>
> 我們在設定方面選擇 Windows，但您也可以在 Linux 上設計類似的解決方案。

您可隨時使用參數 `–stop` 和 `–remove` 來停止/移除 Scheduled Event 服務。

## <a name="connect-to-the-workspace"></a>連線到工作區


我們現在想要將 Log Analytics 工作區連線到收集器 VM。 Log Analytics 工作區會作為存放庫，我們會設定事件記錄檔集合，以從收集器 VM 中擷取應用程式記錄檔。 

 若要將 Scheduled Events 路由傳送至事件記錄檔 (其將透過服務來儲存為應用程式記錄檔)，則必須將虛擬機器連線到 Log Analytics 工作區。  
 
1. 開啟所建立的工作區頁面。
1. 在 [連線到資料來源] 下選取 [Azure 虛擬機器 (VM)]。

    ![連線到作為資料來源的 VM](./media/notifications/connect-to-data-source.png)

1. 搜尋並選取 [myCollectorVM]。 
1. 在 [myCollectorVM] 的新頁面上，選取 [連線]。

此動作會在虛擬機器中安裝 [Microsoft Monitoring Agent](/azure/virtual-machines/extensions/oms-windows)。 將 VM 連線至工作區並安裝延伸模組需要幾分鐘的時間。 

## <a name="configure-the-workspace"></a>設定工作區

1. 開啟工作區頁面，然後選取 [進階設定]。
1. 在左側功能表選取 [資料]，然後選取 [Windows 事件記錄檔]。
1. 在 [從下列事件記錄檔收集] 中，輸入 *application*，然後從清單中選取 [應用程式]。

    ![選取 [進階設定]](./media/notifications/advanced.png)

1. 讓 [錯誤]、[警告] 和 [資訊] 保持在選取狀態，然後選取 [儲存] 以儲存設定。


> [!NOTE]
> 取得記錄檔最多需要花費 10 分鐘的時間。 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>使用 Azure 監視器建立警示規則 


將事件推送至 Log Analytics 之後，即可執行下列[查詢](/azure/azure-monitor/log-query/get-started-portal)來尋找 Scheduled Events。

1. 在頁面頂端，選取 [記錄檔] 並將下列內容貼入文字方塊中：

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. 選取 [儲存]，然後在 [名稱] 中鍵入 *logQuery*、將 [類型] 保持為 [查詢]、在 [類別] 中鍵入 *VMLogs*，然後選取 [儲存]。 

    ![儲存查詢](./media/notifications/save-query.png)

1. 選取 [新增警示規則]。 
1. 在 [建立規則] 頁面上，將 [資源] 保持為 `collectorworkspace`。
1. 在 [條件] 底下，選取 [每當客戶記錄檔搜尋為 <login undefined> 時] 項目。 [設定訊號邏輯] 頁面隨即開啟。
1. 在 [閾值] 中，輸入 *0*，然後選取 [完成]。
1. 在 [動作] 底下，選取 [建立動作群組]。 [新增動作群組] 頁面隨即開啟。
1. 在 [動作群組名稱] 中，鍵入 *myActionGroup*。
1. 在 [簡短名稱] 中，輸入 **myActionGroup**。
1. 在 [資源群組]中，選取 [myResourceGroupAvailability]。
1. 在 動作 下的 動作名稱 中鍵入 **Email**，然後選取 電子郵件/SMS/推播/語音。 [電子郵件/SMS/推播/語音] 頁面隨即開啟。
1. 選取 [電子郵件]，鍵入電子郵件地址，然後選取 [確定]。
1. 在 [新增動作群組] 頁面中，選取 [確定]。 
1. 在 [建立規則] 頁面的 [警示詳細資料] 下，鍵入 *myAlert* 作為 [警示規則名稱]，然後在 [描述] 中鍵入「電子郵件警示規則」。
1. 當完成時，選取 [建立警示規則]。
1. 重新啟動可用性設定組中的其中一部 VM。 您應該會在幾分鐘內收到警示已觸發的電子郵件。

若要管理警示規則，請前往資源群組，在左側功能表中選取 [警示]，然後在頁面頂端選取 [管理警示規則]。

     
## <a name="next-steps"></a>後續步驟

若要深入了解，請參閱 GitHub 上的 [Scheduled Events 服務](https://github.com/microsoft/AzureScheduledEventsService) (英文) 頁面。
