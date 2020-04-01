---
title: 匯出 Azure 活動記錄
description: 將 Azure 活動日誌匯出到儲存以進行存檔或 Azure 事件中心,以便匯出到 Azure 外部。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396726"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>將 Azure 活動紀錄匯出到儲存或 Azure 事件中心

> [!IMPORTANT]
> 將 Azure 活動紀錄傳送到 Azure 儲存和 Azure 事件中心的方法已變更為[診斷設定](diagnostic-settings.md)。 本文介紹了正在被棄用的遺留方法。 請參閱"更新以[收集和分析 Azure 監視器中的 Azure 活動日誌"](activity-log-collect.md)以進行比較。


[Azure 活動日誌](platform-logs-overview.md)提供 Azure 訂閱中發生的訂閱級事件的見解。 除了在 Azure 門戶中查看活動日誌或將其複製到日誌分析工作區,以便使用 Azure 監視器收集的其他數據對其進行分析外,還可以創建日誌配置檔以將活動日誌存檔到 Azure 儲存帳戶或將其流式傳輸到事件中心。

## <a name="archive-activity-log"></a>已歸檔活動記錄
如果要將日誌數據保留超過 90 天(完全控制保留策略),以便進行審核、靜態分析或備份,則將活動日誌存檔到存儲帳戶非常有用。 如果只需要將事件保留 90 天或更少,則無需將存檔設置為存儲帳戶,因為活動日誌事件在 Azure 平臺中保留 90 天。

## <a name="stream-activity-log-to-event-hub"></a>將活動記錄串流式傳輸到事件中心
[Azure 事件中心](/azure/event-hubs/)是一個數據流平臺和事件引入服務,每秒可以接收和處理數百萬個事件。 傳送至事件中樞的資料可以透過任何即時分析提供者或批次/儲存體配接器來轉換和儲存。 對活動紀錄使用流式處理功能的兩種方法包括:
* **串流至協力廠商記錄與遙測系統** – 隨著時間進展，「Azure 事件中樞」串流會成為將「活動記錄」輸送到協力廠商 SIEM 與記錄分析解決方案的機制。
* **建置自訂遙測與記錄平台** – 如果您已經有自訂建置的遙測平台或正考慮建置一個，「事件中樞」所具備的高度可調整發佈訂閱特質將可讓您靈活擷取活動記錄。

## <a name="prerequisites"></a>Prerequisites

### <a name="storage-account"></a>儲存體帳戶
如果要存檔活動日誌,則需要[創建存儲帳戶](../../storage/common/storage-account-create.md)(如果尚未存檔)。 不應使用現有存儲帳戶,該帳戶中存儲了其他非監視數據,以便更好地控制對監視數據的訪問。 但是,如果您還要將日誌和指標存檔到存儲帳戶,則可以選擇使用相同的存儲帳戶將所有監視數據保存在中心位置。

儲存體帳戶不一定要和訂用帳戶發出記錄屬於相同的訂用帳戶，只要使用者有適當的設定可 RBAC 存取這兩個訂用帳戶即可。 

> [!TIP]
> 請參閱[配置 Azure 儲存防火牆和虛擬網路](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions),以便對安全虛擬網路後面的存儲帳戶進行訪問。

### <a name="event-hubs"></a>事件中樞
如果要將活動日誌發送到事件中心,則需要[創建事件中心](../../event-hubs/event-hubs-create.md)(如果尚未創建)。 如果以前將活動日誌事件流式傳輸到此事件中心命名空間,則將重用該事件中心。

共用存取原則會定義串流機制具有的權限。 流式傳輸到事件中心需要管理、發送和偵聽許可權。 您可以在 Azure 入口網站中您「事件中樞」命名空間的 [設定] 索引標籤下，為「事件中樞」命名空間建立或修改共用存取原則。

要更新活動日誌日誌設定檔以包括流式處理,您必須具有該事件中心授權規則的 ListKey 許可權。 事件中樞命名空間不一定要和發出記錄的訂用帳戶屬於相同的訂用帳戶，只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權，而且這兩個訂用帳戶都屬於同一個 ADD 租用戶。

通過[創建日誌配置檔](#create-a-log-profile)將活動日誌流式傳輸到事件中心。

## <a name="create-a-log-profile"></a>建立紀錄設定檔
定義如何使用**日誌配置檔**匯出 Azure 活動日誌。 每個 Azure 訂閱只能有一個日誌配置檔。 這些設置可以通過門戶中的"活動日誌"邊欄選項卡中的 **"匯出**"選項進行配置。 也可以[使用 Azure 監視器 REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx)、PowerShell Cmdlet 或 CLI 以程式設計方式設定。

紀錄設定檔定義以下內容。

**應發送活動日誌的位置。** 目前,可用選項是存儲帳戶或事件中心。

**應發送哪些事件類別。** 紀錄設定檔和活動紀錄事件中*的類別*含義不同。 在紀錄設定檔中,*類別*表示操作類型(寫入、刪除、操作)。 在活動紀錄事件中,*類別*「* 屬性表示事件的源或類型(例如,管理、服務運行狀況和警報)。

**應匯出哪些區域(位置)。** 您應該包括所有位置,因為活動日誌中的許多事件都是全域事件。

**活動記錄應該在儲存體帳戶中保留多久。** 保留期為 0 天表示會永遠保留記錄。 否則,該值可以是介於 1 和 365 之間的任何天數。

如果設置了保留策略,但禁用了存儲帳戶中的日誌存儲,則保留策略不起作用。 保留原則是每天套用，因此在一天結束時 (UTC)，這一天超過保留原則的記錄會被刪除。 例如，如果您的保留原則為一天，在今天一開始，昨天之前的記錄會被刪除。 刪除程序會從 UTC 午夜開始，但是請注意，可能需要長達 24 小時的時間，記錄才會從您的儲存體帳戶中刪除。


> [!IMPORTANT]
> 如果 Microsoft.Insights 資源提供程式未註冊,則創建日誌配置檔時可能會收到錯誤。 請參閱[Azure 資源提供程式和類型](../../azure-resource-manager/management/resource-providers-and-types.md)以註冊此提供程式。


### <a name="create-log-profile-using-the-azure-portal"></a>使用 Azure 閘戶建立紀錄設定檔

使用 Azure 入口中中的 **「匯出到事件中心**」選項創建或編輯紀錄設定檔。

1. 從 Azure 門戶中的**Azure 監視器**選單中,選擇**活動紀錄**。
3. 按下 **「診斷設置**」。

   ![診斷設定](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 單擊紫色橫幅即可獲得舊體驗。

    ![傳統體驗](media/diagnostic-settings-subscription/legacy-experience.png)

3. 在出現的邊列選項卡中,指定以下內容:
   * 具有要導出的事件的區域。 您應該選擇所有區域,以確保不會錯過關鍵事件,因為活動日誌是全域(非區域)日誌,因此大多數事件沒有與其關聯的區域。
   * 如果要寫入儲存帳戶:
       * 要保存到的存儲帳戶。
       * 要在存儲中保留這些事件的天數。 如果設定為 0 天會永遠保留記錄。
   * 如果要寫入事件中心:
       * 服務總線命名空間,您希望在其中創建事件中心以流式傳輸這些事件。

     ![匯出活動記錄檔刀鋒視窗](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. 按一下 [儲存] **** 來儲存這些設定。 您的訂用帳戶時會立即套用設定。


### <a name="configure-log-profile-using-powershell"></a>使用 PowerShell 設定紀錄設定檔

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

如果紀錄設定檔已存在,則首先需要刪除現有紀錄配置檔,然後創建新的日誌設定檔。

1. 使用 `Get-AzLogProfile` 來識別記錄設定檔是否存在。  如果日誌配置檔確實存在,請記下*名稱*屬性。

1. 使用 `Remove-AzLogProfile` 透過 name** 屬性中的值來移除記錄設定檔。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. 使用 `Add-AzLogProfile` 來建立新的記錄設定檔：

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 屬性 | 必要 | 描述 |
    | --- | --- | --- |
    | 名稱 |是 |記錄檔設定檔的名稱。 |
    | StorageAccountId |否 |應保存活動日誌的存儲帳戶的資源 ID。 |
    | serviceBusRuleId |否 |服務匯流排規則識別碼，您想要在其中建立事件中樞的服務匯流排命名空間。 這是一個格式為的字串: `{service bus resource ID}/authorizationrules/{key name}`。 |
    | Location |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
    | RetentionInDays |是 |應在存儲帳戶中保留事件的天數,介於 1 和 365 之間。 值為 0 會無限期地儲存記錄。 |
    | 類別 |否 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值是 _'寫入_' 、_移除_與_操作_。 |

### <a name="example-script"></a>範例指令碼
下面是一個示例 PowerShell 文本,用於創建將活動日誌寫入存儲帳戶和事件中心的日誌配置檔。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>使用 Azure CLI 設定紀錄設定檔

如果記錄設定檔已經存在，您必須先移除現有的記錄設定檔，然後再建立新的記錄設定檔。

1. 使用 `az monitor log-profiles list` 來識別記錄設定檔是否存在。
2. 使用 `az monitor log-profiles delete --name "<log profile name>` 透過 name** 屬性中的值來移除記錄設定檔。
3. 使用 `az monitor log-profiles create` 來建立新的記錄設定檔：

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 屬性 | 必要 | 描述 |
    | --- | --- | --- |
    | NAME |是 |記錄檔設定檔的名稱。 |
    | storage-account-id |是 |資源識別碼，活動記錄應該要儲存至此儲存體帳戶。 |
    | 位置 |是 |以空格分隔的區域清單，其中列出您要收集的活動記錄事件的區域。 您可以使用 `az account list-locations --query [].name` 來檢視您訂用帳戶的所有區域清單。 |
    | days |是 |應保留事件的天數,介於 1 和 365 之間。 值為 0 會無限期地 (永遠) 儲存記錄。  如果為零,則啟用的參數應設置為 false。 |
    |已啟用 | 是 |True 或 False。  用來啟用或停用保留原則。  如果為 True，則 days 參數必須是大於 0 的值。
    | categories |是 |以空格分隔的類別清單，其中列出應收集的事件類別。 可能的值有 Write、Delete、Action。 |



## <a name="next-steps"></a>後續步驟

* [瞭解有關活動紀錄的更多內容](../../azure-resource-manager/management/view-activity-logs.md)
* [將活動紀錄收集到 Azure 監視器紀錄](activity-log-collect.md)
