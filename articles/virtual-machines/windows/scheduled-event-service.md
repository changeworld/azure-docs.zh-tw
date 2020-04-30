---
title: 在 Azure 中監視 Windows Vm 的已排程事件
description: 瞭解如何監視 Azure 虛擬機器的已排程事件。
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

更新會每天套用到 Azure 的不同部分，以確保在其上執行的服務安全且保持在最新狀態。 除了已規劃的更新之外，也可能會發生未計畫的事件。 例如，如果偵測到任何硬體效能降低或錯誤，Azure 服務可能需要執行未規劃的維護。 使用即時移轉時，記憶體會保留更新，而且通常會對更新的影響進行嚴格的追蹤，在大多數情況下，這些事件對客戶而言幾乎是透明的，而且不會有任何影響，也不會造成虛擬機器凍結的幾秒鐘。 不過，對於某些應用程式，即使幾秒鐘的虛擬機器凍結也可能會造成影響。 事先瞭解即將推出的 Azure 維護非常重要，以確保這些應用程式的最佳體驗。 [Scheduled Events 服務](scheduled-events.md)提供了程式設計介面，可讓您在即將進行的維護時收到通知，並可讓您正常地處理維護。 

在本文中，我們將示範如何使用排定的事件，以通知可能會影響您 Vm 的維護事件，並建立一些可協助進行監視和分析的基本自動化。


## <a name="routing-scheduled-events-to-log-analytics"></a>將已排程的事件路由至 Log Analytics

Scheduled Events 是[azure Instance Metadata Service](instance-metadata-service.md)的一部分，可在每個 azure 虛擬機器上取得。 客戶可以撰寫自動化來查詢其虛擬機器的端點，以尋找已排程的維護通知並執行緩和措施，例如儲存狀態並讓虛擬機器離開輪替。 我們建議您建立自動化來記錄 Scheduled Events，讓您可以擁有 Azure 維護事件的審核記錄。 

在本文中，我們將逐步引導您瞭解如何將維護 Scheduled Events 捕獲到 Log Analytics。 然後，我們將會觸發一些基本的通知動作，例如傳送電子郵件給您的小組，並取得影響虛擬機器的所有事件歷程記錄。 針對事件匯總和自動化，我們將使用[Log Analytics](/azure/azure-monitor/learn/quick-create-workspace)，但是您可以使用任何監視解決方案來收集這些記錄和觸發自動化。

![顯示事件生命週期的圖表](./media/notifications/events.png)

## <a name="prerequisites"></a>先決條件

在此範例中，您將需要[在可用性設定組中建立 Windows 虛擬機器](tutorial-availability-sets.md)。 Scheduled Events 提供可能會影響可用性設定組、雲端服務、虛擬機器擴展集或獨立 Vm 中任何虛擬機器之變更的相關通知。 我們將執行一項[服務](https://github.com/microsoft/AzureScheduledEventsService)，以輪詢做為收集器的其中一個 vm 上已排程的事件，以取得可用性設定組中所有其他 vm 的事件。    

請勿在教學課程結束時刪除群組資源群組。

您也必須[建立 Log Analytics 工作區](/azure/azure-monitor/learn/quick-create-workspace)，我們將用它來匯總可用性設定組中 vm 的資訊。

## <a name="set-up-the-environment"></a>設定 Azure 環境

在可用性設定組中，您現在應該有2個初始 Vm。 現在，我們需要在相同的可用性設定組中建立第三個 VM （稱為 myCollectorVM）。 

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
 

從[GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)下載專案的安裝 .zip 檔案。

連接到**myCollectorVM** ，並將 .zip 檔案複製到虛擬機器，並將所有檔案解壓縮。 在您的 VM 上，開啟 PowerShell 提示字元。 將您的提示移至包含`SchService.ps1`的資料夾，例如`PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`：，並設定服務。

```powershell
.\SchService.ps1 -Setup
```

啟動服務。

```powershell
.\SchService.ps1 -Start
```

服務現在會每隔10秒針對任何排程的事件啟動輪詢，並核准事件以加速維護。  凍結、重新開機、重新部署和搶先是由排程事件所捕捉的事件。   請注意，您可以擴充腳本，以在核准事件之前觸發一些緩和措施。

驗證服務狀態，並確定它正在執行。

```powershell
.\SchService.ps1 -status  
```

這應該會`Running`傳回。

服務現在會每隔10秒針對任何排程的事件啟動輪詢，並核准事件以加速維護。  凍結、重新開機、重新部署和搶先是由排程事件所捕捉的事件。 在核准事件之前，您可以擴充腳本以觸發一些緩和措施。

當上述任何事件由「排程事件服務」捕捉時，會記錄在應用程式事件記錄檔事件狀態、事件種類、資源（虛擬機器名稱）和 NotBefore （最小通知期間）。 您可以在應用程式事件記錄檔中找出識別碼為1234的事件。

一旦設定並啟動服務，它就會將事件記錄在 Windows 應用程式記錄檔中。   若要確認此功能是否正常，請重新開機可用性設定組中的其中一部虛擬機器，您應該會在 [Windows 記錄檔] 中看到事件記錄檔 > 應用程式記錄檔中顯示 VM 重新開機。 

![事件檢視器的螢幕擷取畫面。](./media/notifications/event-viewer.png)

當排程事件服務捕捉到事件時，它將會記錄在應用程式中，甚至記錄事件狀態、事件種類、資源（VM 名稱）和 NotBefore （最小通知期間）。 您可以在應用程式事件記錄檔中找出識別碼為1234的事件。

> [!NOTE] 
> 在此範例中，虛擬機器位於可用性設定組中，可讓我們指定單一虛擬機器作為收集器，以接聽並將排程的事件路由至我們的 log analytics 工作空間。 如果您有獨立的虛擬機器，您可以在每部虛擬機器上執行服務，然後將它們個別連線到您的 log analytics 工作區。
>
> 針對我們的設定，我們選擇 Windows，但您可以在 Linux 上設計類似的解決方案。

任何時候，您都可以使用參數`–stop`和`–remove`來停止/移除已排程的事件服務。

## <a name="connect-to-the-workspace"></a>連接到工作區


我們現在想要將 Log Analytics 工作區連線到收集器 VM。 Log Analytics 工作區會作為存放庫，我們將設定事件記錄檔收集，以從收集器 VM 中捕獲應用程式記錄。 

 若要將 Scheduled Events 路由傳送至事件記錄檔（由我們的服務儲存為應用程式記錄檔），您必須將虛擬機器連接到 Log Analytics 工作區。  
 
1. 開啟您所建立之工作區的頁面。
1. 在 **[連線到資料來源]** 底下，選取 [ **Azure 虛擬機器（vm）**]。

    ![連接至 VM 作為資料來源](./media/notifications/connect-to-data-source.png)

1. 搜尋並選取 [ **myCollectorVM**]。 
1. 在**myCollectorVM**的 [新增] 頁面上，選取 **[連線]**。

這會將[Microsoft Monitoring agent](/azure/virtual-machines/extensions/oms-windows)安裝在您的虛擬機器中。 需要幾分鐘的時間，將 VM 連線至工作區並安裝擴充功能。 

## <a name="configure-the-workspace"></a>設定工作區

1. 開啟您工作區的頁面，然後選取 [ **Advanced settings**]。
1. 從左側功能表中選取 [**資料**]，然後選取 [ **Windows 事件記錄**]。
1. 在 **[從下列事件記錄檔收集**] 中，開始輸入*應用程式*，然後從清單中選取 [**應用程式**]。

    ![選取 [Advanced settings]](./media/notifications/advanced.png)

1. 保留 [**錯誤**]、[**警告**] 和 [已選取的**資訊**]，然後選取 [**儲存**] 以儲存設定。


> [!NOTE]
> 會有一些延遲，而且可能需要最多10分鐘的時間才能使用記錄檔。 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>使用 Azure 監視器建立警示規則 


將事件推送至 Log Analytics 之後，您可以執行下列[查詢](/azure/azure-monitor/log-query/get-started-portal)來尋找排程事件。

1. 在頁面頂端，選取 [**記錄**]，並將下列內容貼入文字方塊中：

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

1. 選取 [**儲存**]，然後在 [名稱] 中輸入*logQuery* ，將 [**查詢**] 保留為 [類型]，輸入*VMLogs*做為**類別目錄**，然後選取 [**儲存**]。 

    ![儲存查詢](./media/notifications/save-query.png)

1. 選取 [新增警示規則]****。 
1. 在 [**建立規則**] 頁面中`collectorworkspace` ，保留為**資源**。
1. 在 [**條件**] 底下，選取*每次客戶記錄搜尋<login undefined>為*時的專案。 [**設定信號邏輯**] 頁面隨即開啟。
1. 在 [**臨界值**] 下輸入*0* ，然後選取 [**完成**]。
1. 在 [**動作**] 底下，選取 [**建立動作群組**]。 [**新增動作群組**] 頁面隨即開啟。
1. 在 [**動作組名**] 中，輸入*myActionGroup*。
1. 在 [**簡短名稱**] 中，輸入**myActionGroup**。
1. 在 [**資源群組**] 中，選取 [ **[myresourcegroupavailability**]。
1. 在 [動作] 底下的 [**動作名稱**] 中，輸入**email**，然後選取 [**電子郵件/SMS/推播/語音**]。 [**電子郵件/SMS/推播/語音**] 頁面隨即開啟。
1. 選取 [**電子郵件**]，輸入您的電子郵件地址，然後選取 **[確定]**。
1. 在 [**新增動作群組**] 頁面中，選取 **[確定]**。 
1. 在 [**建立規則**] 頁面的 [**警示詳細資料**] 下，針對 [**警示規則名稱**] 輸入*myAlert* ，然後在 [**描述**] 中輸入*電子郵件警示規則*。
1. 當您完成時，請選取 [**建立警示規則**]。
1. 重新開機可用性設定組中的其中一個 Vm。 在幾分鐘內，您應該會收到警示已觸發的電子郵件。

若要管理您的警示規則，請移至資源群組，從左側功能表中選取 [**警示**]，然後從頁面頂端選取 [**管理警示規則**]。

     
## <a name="next-steps"></a>後續步驟

若要深入瞭解，請參閱 GitHub 上的[排定的事件服務](https://github.com/microsoft/AzureScheduledEventsService)頁面。
