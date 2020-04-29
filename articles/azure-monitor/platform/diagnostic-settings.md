---
title: 在 Azure 中使用診斷設定來收集平臺計量和記錄
description: 使用診斷設定，將 Azure 監視器平臺計量和記錄傳送至 Azure 監視器記錄、Azure 儲存體或 Azure 事件中樞。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681216"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>建立診斷設定以收集 Azure 中的資源記錄和計量

Azure 中的[平臺記錄](platform-logs-overview.md)，包括 azure 活動記錄檔和資源記錄，可針對 Azure 資源和其相依的 azure 平臺提供詳細的診斷和審核資訊。 預設會收集[平臺計量](data-platform-metrics.md)，而且通常會儲存在 Azure 監視器計量資料庫中。

本文提供有關建立及設定診斷設定的詳細資料，以將平臺計量和平臺記錄檔傳送至不同的目的地。

> [!IMPORTANT]
> 建立診斷設定以收集活動記錄檔之前，您應該先停用任何舊版設定。 如需詳細資訊，請參閱[使用舊版設定收集 Azure 活動記錄](diagnostic-settings-legacy.md)。

每個 Azure 資源都需要自己的診斷設定，以定義下列準則：

- 傳送至設定中所定義目的地之記錄和計量資料的類別。 可用類別會隨不同資源類型而異。
- 一個或多個要傳送記錄的目的地。 目前的目的地包括 Log Analytics 工作區、事件中樞和 Azure 儲存體。

單一診斷設定只能定義其中一個目的地。 如果您想要將資料傳送至超過一個的特定目的地類型 (例如，兩個不同的 Log Analytics 工作區)，請建立多個設定。 每個資源可以有最多 5 個診斷設定。

> [!NOTE]
> 系統會自動收集[平臺計量](metrics-supported.md)以[Azure 監視器計量](data-platform-metrics.md)。 診斷設定可以用來將特定 Azure 服務的計量收集到 Azure 監視器記錄中，以便使用[記錄查詢](../log-query/log-query-overview.md)進行其他監視資料的分析。

## <a name="destinations"></a>Destinations

平臺記錄和計量可以傳送至下表中的目的地。 請遵循下表中的每個連結，以取得將資料傳送至該目的地的詳細資訊。

| Destination | 描述 |
|:---|:---|
| [Log Analytics 工作區](resource-logs-collect-workspace.md) | 將記錄和計量收集到 Log Analytics 工作區，可讓您使用功能強大的記錄查詢 Azure 監視器所收集的其他監視資料進行分析，也可以利用警示和視覺效果等其他 Azure 監視器功能。 |
| [事件中樞](resource-logs-stream-event-hubs.md) | 將記錄和計量傳送至事件中樞可讓您將資料串流至外部系統，例如協力廠商 Siem 和其他 log analytics 解決方案。 |
| [Azure 儲存體帳戶](resource-logs-collect-storage.md) | 將記錄和計量封存到 Azure 儲存體帳戶適用于 audit、靜態分析或備份。 相較于 Azure 監視器記錄和 Log Analytics 工作區，Azure 儲存體的成本較低，而且記錄可以無限期保存。 |

## <a name="create-diagnostic-settings-in-azure-portal"></a>在 Azure 入口網站中建立診斷設定

您可以從 [Azure 監視器] 功能表或從資源的功能表，設定 Azure 入口網站中的診斷設定。

1. 您在 Azure 入口網站中設定診斷設定的位置取決於資源。

   - 若為單一資源，請按一下資源功能表中 [**監視**] 底下的 [**診斷設定**]。

        ![診斷設定](media/diagnostic-settings/menu-resource.png)

   - 對於一或多個資源，請按一下 [Azure 監視器] 功能表中 [**設定**] 底下的 [**診斷設定**]，然後按一下資源。

      ![診斷設定](media/diagnostic-settings/menu-monitor.png)

   - 在 [活動記錄] 中，按一下 [ **Azure 監視器**] 功能表中的 [**活動記錄**]，然後按一下 [**診斷設定**]。 請務必停用活動記錄的任何舊版設定。 如需詳細資訊，請參閱[停用現有的設定](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log)。

        ![診斷設定](media/diagnostic-settings/menu-activity-log.png)

2. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下「新增診斷設定」****。

   ![新增診斷設定 - 無現有的設定](media/diagnostic-settings/add-setting.png)

   如果資源上有現有的設定，您會看到已設定的設定清單。 按一下 [新增**診斷設定**] 以加入新的設定或**編輯設定**，以編輯現有的設定。 每個設定都不能有一個以上的目的地類型。

   ![新增診斷設定 - 現有的設定](media/diagnostic-settings/edit-setting.png)

3. 如果您的設定還沒有名稱，請將其命名為。

    ![新增診斷設定](media/diagnostic-settings/setting-new-blank.png)

4. **類別目錄詳細資料（要路由**的目標）-核取您要傳送到稍後指定之目的地的每個類別的方塊。 每個 Azure 服務的類別清單會有所不同。

     - **AllMetrics**會將資源的平臺計量路由至 Azure 記錄存放區，但會以記錄形式傳送。 這些計量通常只會傳送至 Azure 監視器計量時間序列資料庫。 將它們傳送至 Azure 監視器記錄存放區（可透過 Log Analytics 搜尋），您就可以將它們整合到查詢中，以在其他記錄中進行搜尋。 並非所有資源類型都可以使用此選項。 支援時， [Azure 監視器支援的計量](metrics-supported.md)會列出針對哪些資源類型所收集的計量。

       > [!NOTE]
       > 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。
       >
       > *例如*：您可以在每個節點層級上探索區塊鏈上的 ' IOReadBytes ' 計量並繪製成圖表。 不過，透過診斷設定匯出時，匯出的計量會代表所有節點的所有讀取位元組。

     - **記錄**會根據資源類型列出可用的不同類別。 檢查您要路由傳送至目的地的任何類別。

5. **目的地詳細資料**-勾選每個目的地的方塊。 當您核取每個方塊時，會顯示選項，讓您新增其他資訊。

      ![傳送至 Log Analytics 或事件中樞](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** -輸入訂用帳戶和工作區。  如果您沒有工作區，則必須[先建立一個，再繼續](../learn/quick-create-workspace.md)進行。

    1. **事件中樞**-指定下列準則：
       - 事件中樞所屬的訂用帳戶
       - 事件中樞命名空間-如果您還沒有帳戶，則必須[建立一個](../../event-hubs/event-hubs-create.md)
       - 要將所有資料傳送至其中的事件中樞名稱（選擇性）。 如果您未指定名稱，則會為每個記錄類別建立一個事件中樞。 如果您要傳送多個類別，您可能會想要指定名稱，以限制所建立的事件中樞數目。 如需詳細資訊，請參閱[Azure 事件中樞配額和限制](../../event-hubs/event-hubs-quotas.md)。
       - 事件中樞原則（選擇性）：原則會定義串流機制擁有的許可權。 如需詳細資訊，請參閱[事件中樞-功能](../../event-hubs/event-hubs-features.md#publisher-policy)。

    1. **儲存體**-選擇 [訂用帳戶]、[儲存體帳戶] 和 [保留原則]。

        ![傳送至儲存體](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 請考慮將保留原則設為0，並使用排程工作從儲存體手動刪除您的資料，以避免未來發生混淆。
        >
        > 首先，如果您要使用儲存體來進行封存，通常會想要讓資料的時間超過365天。 第二，如果您選擇大於0的保留原則，則到期日會附加到儲存體時的記錄。 儲存之後，您就無法變更這些記錄的日期。
        >
        > 例如，如果您將*WorkflowRuntime*的保留原則設定為180天，並在24小時後將它設定為365天，則前24小時內儲存的記錄將會在180天后自動刪除，而該類型的所有後續記錄檔將會在365天后自動刪除。 稍後變更保留原則並不會讓前24小時的記錄持續365天。

6. 按一下 **[儲存]** 。

幾分鐘之後，新的設定就會出現在此資源的設定清單中，而且記錄會在產生新的事件資料時串流處理至指定的目的地。 在事件發出和[記錄分析工作區中顯示](data-ingestion-time.md)時，可能需要15分鐘的時間。

## <a name="create-diagnostic-settings-using-powershell"></a>使用 PowerShell 建立診斷設定

使用[set-azdiagnosticsetting 指令程式](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting)建立具有[Azure PowerShell](powershell-quickstart-samples.md)的診斷設定。 如需其參數的描述，請參閱此 Cmdlet 的檔。

> [!IMPORTANT]
> 您不能將此方法用於 Azure 活動記錄。 相反地，請使用[Azure 監視器中的 [建立診斷設定]，使用 Resource Manager 範本](diagnostic-settings-template.md)建立 Resource Manager 範本，並使用 PowerShell 進行部署。

以下是使用這三個目的地來建立診斷設定的範例 PowerShell Cmdlet。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-diagnostic-settings-using-azure-cli"></a>使用 Azure CLI 建立診斷設定

使用[az monitor [診斷-設定](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create)] [建立] 命令，以[Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)建立診斷設定。 如需其參數的描述，請參閱此命令的檔。

> [!IMPORTANT]
> 您不能將此方法用於 Azure 活動記錄。 相反地，請使用[Azure 監視器中的 [建立診斷設定]，使用 Resource Manager 範本](diagnostic-settings-template.md)建立 Resource Manager 範本，並使用 CLI 進行部署。

以下是使用這三個目的地來建立診斷設定的範例 CLI 命令。

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

請參閱[診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)，以使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)來建立或更新診斷設定。

### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>使用 Resource Manager 範本設定診斷設定

請參閱[使用 Resource Manager 範本在 Azure 監視器中建立診斷設定](diagnostic-settings-template.md)，以 Resource Manager 範本建立或更新診斷設定。

## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 平臺記錄](platform-logs-overview.md)
