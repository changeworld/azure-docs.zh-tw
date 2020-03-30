---
title: 監視 Azure 中 Windows VM 的計畫事件
description: 瞭解如何監視 Azure 虛擬機器的計畫事件。
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073322"
---
# <a name="monitoring-scheduled-events"></a>監視計畫事件

更新每天都會應用於 Azure 的不同部分，以確保服務在它們上運行的安全以及最新的。 除了計畫更新外，還可能發生計畫外事件。 例如，如果檢測到任何硬體降級或故障，Azure 服務可能需要執行計畫外維護。 使用即時移轉、記憶體保留更新和通常對更新的影響保持嚴格的限制，在大多數情況下，這些事件對客戶幾乎透明，並且它們不會影響或最多會導致幾秒鐘的虛擬機器凍結。 但是，對於某些應用程式，即使幾秒鐘的虛擬機器凍結也可能導致影響。 提前瞭解即將進行的 Azure 維護非常重要，以確保這些應用程式獲得最佳體驗。 [計畫事件服務](scheduled-events.md)為您提供了一個程式化介面，用於通知即將進行的維護，並使您能夠優雅地處理維護。 

在本文中，我們將介紹如何使用計畫事件來通知可能影響 VM 的維護事件，並構建一些有助於監視和分析的基本自動化。


## <a name="routing-scheduled-events-to-log-analytics"></a>將計畫事件路由到日誌分析

計畫事件作為[Azure 實例中繼資料服務的](instance-metadata-service.md)一部分提供，該服務在每個 Azure 虛擬機器上都可用。 客戶可以編寫自動化來查詢其虛擬機器的終結點，以查找計畫的維護通知並執行緩解措施，例如保存狀態和使虛擬機器退出旋轉。 我們建議進行構建自動化以記錄計畫事件，以便可以具有 Azure 維護事件的稽核記錄。 

在本文中，我們將介紹如何捕獲維護計畫事件以記錄分析。 然後，我們將觸發一些基本的通知操作，例如向您的團隊發送電子郵件，並獲取影響虛擬機器的所有事件的歷史視圖。 對於事件聚合和自動化，我們將使用[日誌分析](/azure/azure-monitor/learn/quick-create-workspace)，但您可以使用任何監視解決方案來收集這些日誌並觸發自動化。

![顯示事件生命週期的圖表](./media/notifications/events.png)

## <a name="prerequisites"></a>Prerequisites

在此示例中，您需要[在可用性集中](tutorial-availability-sets.md)創建 Windows 虛擬機器。 計畫事件提供有關可能影響可用性集中、雲服務、虛擬機器規模集或獨立 VM 中的任何虛擬機器的更改的通知。 我們將運行一[個服務](https://github.com/microsoft/AzureScheduledEventsService)，該服務輪詢將充當收集器的 VM 之一上的計畫事件，以獲取可用性集中所有其他 VM 的事件。    

不要在本教程結束時刪除組資源組。

您還需要[創建一個日誌分析工作區](/azure/azure-monitor/learn/quick-create-workspace)，我們將使用該工作區來聚合可用性集中的 VM 中的資訊。

## <a name="set-up-the-environment"></a>設定 Azure 環境

現在，可用性集中應該有 2 個初始 VM。 現在，我們需要在同一可用性集中創建第三個 VM，稱為 myCollectorVM。 

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
 

從[GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)下載專案的安裝 .ZIP 檔案。

連接到**myCollectorVM**並將 .ZIP 檔案複製到虛擬機器並提取所有檔。 在 VM 上，打開 PowerShell 提示符。 將提示移到包含`SchService.ps1`的資料夾中，例如 ：`PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`並設置服務。

```powershell
.\SchService.ps1 -Setup
```

啟動服務。

```powershell
.\SchService.ps1 -Start
```

該服務現在將每 10 秒開始輪詢任何計畫的事件，並批准事件以加快維護速度。  凍結、重新開機、重新部署和搶佔是計畫事件捕獲的事件。   請注意，在批准事件之前，可以擴展腳本以觸發一些緩解措施。

驗證服務狀態並確保其正在運行。

```powershell
.\SchService.ps1 -status  
```

這應該返回`Running`。

該服務現在將每 10 秒開始輪詢任何計畫的事件，並批准事件以加快維護速度。  凍結、重新開機、重新部署和搶佔是計畫事件捕獲的事件。 在批准事件之前，可以擴展腳本以觸發一些緩解措施。

當計畫事件服務捕獲上述任何事件時，它將記錄在應用程式事件日誌事件狀態、事件種類、資源（虛擬機器名稱）和 NotT 之前（最小通知期） 中。 您可以在應用程式事件日誌中查找 ID 1234 的事件。

設置和啟動服務後，它將在 Windows 應用程式日誌中記錄事件。   要驗證此工作，請重新開機可用性集中的其中一個虛擬機器，您應該在 Windows 日誌>應用程式日誌中看到事件正在記錄，顯示 VM 重新開機。 

![事件檢視器的螢幕截圖。](./media/notifications/event-viewer.png)

當事件由計畫事件服務捕獲時，它將記錄在應用程式中，甚至記錄與事件狀態、事件種類、資源（VM 名稱）和 NotT 之前（最小通知期）。 您可以在應用程式事件日誌中查找 ID 1234 的事件。

> [!NOTE] 
> 在此示例中，虛擬機器位於可用性集中，這使我們能夠指定單個虛擬機器作為收集器，以偵聽計畫的事件並將其路由到日誌分析工作空間。 如果您有獨立虛擬機器，則可以在每個虛擬機器上運行該服務，然後將它們單獨連接到日誌分析工作區。
>
> 對於我們的設置，我們選擇了 Windows，但您可以在 Linux 上設計類似的解決方案。

您可以隨時使用開關`–stop`和`–remove`停止/刪除計畫事件服務。

## <a name="connect-to-the-workspace"></a>連接到工作區


現在，我們要將日誌分析工作區連接到收集器 VM。 日誌分析工作區充當存儲庫，我們將配置事件日誌集合以從收集器 VM 捕獲應用程式日誌。 

 要將計畫事件路由到事件日誌（我們的服務將存儲為應用程式日誌），您需要將虛擬機器連接到日誌分析工作區。  
 
1. 打開您創建的工作區的頁面。
1. 在 **"連接到資料來源"下**選擇**Azure 虛擬機器 （VM）。**

    ![作為資料來源連接到 VM](./media/notifications/connect-to-data-source.png)

1. 搜索並選擇**我的CollectorVM。** 
1. 在**myCollectorVM**的新頁面上，選擇 **"連接**"。

這將在虛擬機器中安裝[Microsoft 監視代理](/azure/virtual-machines/extensions/oms-windows)。 將 VM 連接到工作區並安裝擴展需要幾分鐘時間。 

## <a name="configure-the-workspace"></a>配置工作區

1. 打開工作區的頁面並選擇 **"高級設置**"。
1. 從左側功能表中選擇 **"資料**"，然後選擇**Windows 事件日誌**。
1. 在 **"從以下事件日誌中收集**"中，開始鍵入*應用程式*，然後從清單中選擇 **"應用程式**"。

    ![選擇"高級設置"](./media/notifications/advanced.png)

1. 選擇**錯誤**、**警告****和資訊，** 然後選擇 **"保存**"以保存設置。


> [!NOTE]
> 會有一些延遲，並且可能需要長達 10 分鐘才能提供日誌。 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>使用 Azure 監視器創建警報規則 


將事件推送到日誌分析後，可以運行以下[查詢](/azure/azure-monitor/log-query/get-started-portal)來查找計畫事件。

1. 在頁面頂部，選擇 **"日誌"** 並將以下內容粘貼到文字方塊中：

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

1. 選擇 **"保存**"，然後鍵入名稱的*日誌查詢*，將**查詢**保留為類型，將*VMLog*鍵入為**類別**，然後選擇 **"保存**"。 

    ![儲存查詢](./media/notifications/save-query.png)

1. 選取 [新增警示規則]****。 
1. 在 **"創建規則**"頁中`collectorworkspace`，保留為**資源**。
1. 在 **"條件"下**，*每當客戶日誌搜索為<login undefined>時，* 請選擇該條目。 **配置信號邏輯**頁將打開。
1. 在**閾值**下，輸入*0，* 然後選擇 **"完成**"。
1. 在 **"操作"** 下，選擇 **"創建操作組**"。 將打開 **"添加操作組"** 頁。
1. 在**操作組名稱**中，鍵入*myActionGroup*。
1. 在**短名稱**中，鍵入**myActionGroup**。
1. 在**資源組中**，選擇 **"我的資源組可用性**"。
1. 在"操作"下，在 **"操作名稱"** 中鍵入 **"電子郵件**"，然後選擇 **"電子郵件/短信/推送/語音**"。 **電子郵件/短信/推送/語音**頁面將打開。
1. 選擇 **"電子郵件**"，鍵入您的電子郵件地址，然後選擇 **"確定**"。
1. 在"**添加操作組"** 頁中，選擇 **"確定**"。 
1. 在 **"創建規則**"頁中，在 **"ALERT 詳細資訊"** 下，鍵入**警報規則名稱***的"我的警報"，* 然後鍵入 **"描述***"的電子郵件警報規則*。
1. 完成後，選擇 **"創建警報規則**"。
1. 重新開機可用性集中的其中一個 VM。 在幾分鐘內，您應該收到一封電子郵件，指出警報已觸發。

要管理警報規則，請轉到資源組，從左側功能表中選擇 **"警報**"，然後從頁面頂部選擇 **"管理警報規則**"。

     
## <a name="next-steps"></a>後續步驟

要瞭解更多資訊，請參閱 GitHub 上的["計畫事件服務](https://github.com/microsoft/AzureScheduledEventsService)"頁面。
