---
title: 建立診斷設定以收集 Azure 中的記錄和計量
description: 創建診斷設定以將 Azure 平台日誌轉發到 Azure 監視器日誌、Azure 儲存或 Azure 事件中心。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6ff9e62ed145b7892d2997193dae8e171ae49c98
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585373"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>建立診斷設定以在 Azure 中收集平台紀錄和指標
Azure 中[的平台日誌](platform-logs-overview.md)(包括 Azure 活動日誌和資源日誌)為 Azure 資源和它們所依賴的 Azure 平臺提供了詳細的診斷和審核資訊。 本文詳細介紹了創建和配置診斷設置以將平臺日誌發送到不同目標。

> [!IMPORTANT]
> 在創建用於收集活動日誌的診斷設置之前,應首先禁用任何舊配置。 有關詳細資訊,請參閱[使用舊版設置收集 Azure 活動日誌](diagnostic-settings-legacy.md)。

每個 Azure 資源都需要其自己的診斷設定,該設定定義以下內容:

- 傳送至設定中所定義目的地之記錄和計量資料的類別。 可用類別會隨不同資源類型而異。
- 一個或多個要傳送記錄的目的地。 目前的目的地包括 Log Analytics 工作區、事件中樞和 Azure 儲存體。
 
單個診斷設置只能定義每個目標之一。 如果您想要將資料傳送至超過一個的特定目的地類型 (例如，兩個不同的 Log Analytics 工作區)，請建立多個設定。 每個資源可以有最多 5 個診斷設定。


> [!NOTE]
> [平台指標](metrics-supported.md)自動收集到 Azure[監視器指標](data-platform-metrics.md)。 診斷設置可用於將某些 Azure 服務的指標收集到 Azure 監視器日誌中,以便使用[日誌查詢](../log-query/log-query-overview.md)與其他監視數據進行分析。

## <a name="destinations"></a>Destinations 
平臺日誌可以發送到下表中的目標。 每個目標的配置都使用相同的過程執行,以創建本文中描述的診斷設置。 請按照下表中的每個連結瞭解向該目標發送數據的詳細資訊。

| Destination | 描述 |
|:---|:---|
| [紀錄分析工作區](resource-logs-collect-workspace.md) | 通過將日誌收集到日誌分析工作區中,您可以使用 Azure 監視器使用強大的日誌查詢收集的其他監視數據進行分析,並利用其他 Azure 監視器功能(如警報和可視化)。 |
| [事件中心](resource-logs-stream-event-hubs.md) | 將日誌發送到事件中心允許您將資料流式傳輸到外部系統,如第三方 SIEM 和其他日誌分析解決方案。 |
| [Azure 儲存帳戶](resource-logs-collect-storage.md) | 將日誌存檔到 Azure 存儲帳戶對於審核、靜態分析或備份非常有用。 |

## <a name="create-diagnostic-settings-in-azure-portal"></a>在 Azure 門戶中建立診斷設定
可以從 Azure 監視器功能表或資源功能表配置 Azure 門戶中的診斷設置。

1. 在 Azure 門戶中配置診斷設置的位置取決於資源。

   - 對於單個資源,按一下資源功能表中的 **「監視器**」下的 **「診斷設置**」 。。

        ![診斷設定](media/diagnostic-settings/menu-resource.png)

    - 對於一個或多個資源,按一下 Azure 監視器功能表中的 **「設定**」下的 **「診斷設置**」,然後單擊該資源。
    
        ![診斷設定](media/diagnostic-settings/menu-monitor.png)

    - 對於活動日誌,按一下**Azure 監視器**功能表中的 **「活動」日誌**,然後單擊 **「診斷設置**」。 請確保禁用活動日誌的任何舊配置。 有關詳細資訊,請參閱[停用現有設定](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log)。

        ![診斷設定](media/diagnostic-settings/menu-activity-log.png)

2. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下「新增診斷設定」****。

   ![新增診斷設定 - 無現有的設定](media/diagnostic-settings/add-setting.png)

   若資源上有現有設定,您將看到已設定的設定清單。 按下「**新增診斷設定**」以新增新設定,或**按一下「編輯」設定**以編輯現有設定。 每個設置只能具有每個目標類型的一個。

   ![新增診斷設定 - 現有的設定](media/diagnostic-settings/edit-setting.png)

3. 如果設置尚未具有名稱,請為設置指定名稱。
4. 選中每個目標發送日誌的複選框。 按**下設定**「以指定下表中所述的設置。

    | 設定 | 描述 |
    |:---|:---|
    | Log Analytics 工作區 | 工作區的名稱。 |
    | 儲存體帳戶 | 存儲帳戶的名稱。 |
    | 事件中樞命名空間 | 創建事件中心(如果這是您第一次流式傳輸日誌)或流式傳輸到的命名空間(如果有資源正在流式傳輸到此命名空間)。
    | 事件中樞名稱 | 可選地指定事件中心名稱以發送設置中的所有數據。 如果不指定名稱,則為每個日誌類別創建一個事件中心。 如果要發送多個類別,則可能需要指定名稱以限制創建的事件中心數。 有關詳細資訊,請參閱[Azure 事件中心配額和限制](../../event-hubs/event-hubs-quotas.md)。 |
    | 事件中樞原則名稱 | 定義流式處理機制具有的許可權。 |

    ![新增診斷設定 - 現有的設定](media/diagnostic-settings/setting-details.png)

5. 選中要發送到指定目標的每個資料類別的複選框。 每個 Azure 服務的類別清單會有所不同。

   > [!NOTE]
   > 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
   >
   > 「例如」**：可以在每個佇列層級瀏覽並繪製事件中樞上的「內送郵件」計量。 不過，當您透過診斷設定匯出時，計量將會呈現為事件中樞內所有佇列的所有內送郵件。

6. 按一下 [儲存]****。

幾分鐘后,新設置將顯示在此資源的設置清單中,並在生成新事件數據時將日誌流式傳輸到指定的目標。 請注意,從發出事件到[在日誌分析工作區中出現](data-ingestion-time.md)之間,可能最多有十五分鐘。



## <a name="create-diagnostic-settings-using-powershell"></a>使用 PowerShell 建立診斷設定
使用[「設定-Az診斷設定](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting)」cmdlet 使用 Azure [PowerShell](powershell-quickstart-samples.md)創建診斷設定。 有關其參數的說明,請參閱此 cmdlet 的文檔。

> [!IMPORTANT]
> 不能對 Azure 活動日誌使用此方法。 相反,[請使用使用資源管理器樣本在 Azure 監視器中創建診斷設定](diagnostic-settings-template.md)來建立資源管理器範本,並使用 PowerShell 進行部署。

下面是 PowerShell cmdlet 使用所有三個目標創建診斷設置的範例。


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>使用 Azure CLI 建立診斷設定
使用[az 監視器診斷設定建立](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create)命令使用[Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)創建診斷設定。 有關其參數的說明,請參閱此命令的文檔。

> [!IMPORTANT]
> 不能對 Azure 活動日誌使用此方法。 相反,[請使用使用資源管理器樣本在 Azure 監視器中創建診斷設定](diagnostic-settings-template.md)來建立資源管理器範本,並將其與 CLI 一起部署。

下面是使用所有三個目標創建診斷設置的範例 CLI 命令。



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>使用 REST API 設定診斷設定
請參閱[診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings),以便使用 Azure 監視器[REST API](https://docs.microsoft.com/rest/api/monitor/)創建或更新診斷設置。


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>使用資源管理員樣本設定診斷設定
請參閱[使用資源管理器範本在 Azure 監視器中創建診斷設置](diagnostic-settings-template.md),以便使用資源管理器範本創建或更新診斷設置。

## <a name="next-steps"></a>後續步驟

* [閱讀有關 Azure 平台日誌的更多內容](platform-logs-overview.md)
