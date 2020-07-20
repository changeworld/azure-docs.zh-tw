---
title: 建立診斷設定以將平臺記錄和計量傳送至不同的目的地
description: 使用診斷設定，將 Azure 監視器平臺計量和記錄傳送至 Azure 監視器記錄、Azure 儲存體或 Azure 事件中樞。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: a037eddb13645036fcbe501ecba33923733b6d03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84944367"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>建立診斷設定以將平臺記錄和計量傳送至不同的目的地
Azure 中的[平臺記錄](platform-logs-overview.md)，包括 azure 活動記錄檔和資源記錄，可針對 Azure 資源和其相依的 azure 平臺提供詳細的診斷和審核資訊。 預設會收集[平臺計量](data-platform-metrics.md)，而且通常會儲存在 Azure 監視器計量資料庫中。 本文提供有關建立及設定診斷設定的詳細資料，以將平臺計量和平臺記錄檔傳送至不同的目的地。

> [!IMPORTANT]
> 建立活動記錄的診斷設定之前，您應該先停用任何舊版設定。 如需詳細資訊，請參閱[舊版收集方法](activity-log.md#legacy-collection-methods)。

每個 Azure 資源都需要自己的診斷設定，以定義下列準則：

- 傳送至設定中所定義目的地之記錄和計量資料的類別。 可用類別會隨不同資源類型而異。
- 一個或多個要傳送記錄的目的地。 目前的目的地包括 Log Analytics 工作區、事件中樞和 Azure 儲存體。

單一診斷設定只能定義其中一個目的地。 如果您想要將資料傳送至超過一個的特定目的地類型 (例如，兩個不同的 Log Analytics 工作區)，請建立多個設定。 每個資源可以有最多 5 個診斷設定。

> [!NOTE]
> [平臺計量](metrics-supported.md)會自動傳送至[Azure 監視器計量](data-platform-metrics.md)。 診斷設定可以用來將特定 Azure 服務的計量傳送至 Azure 監視器記錄，以使用具有特定限制的[記錄查詢](../log-query/log-query-overview.md)來分析其他監視資料。 
>  
>  
> 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。 *例如*：您可以在每個節點層級上探索區塊鏈上的 ' IOReadBytes ' 計量並繪製成圖表。 不過，透過診斷設定匯出時，匯出的計量會代表所有節點的所有讀取位元組。 此外，由於內部限制，並非所有計量都可匯出以 Azure 監視器記錄/Log Analytics。 如需詳細資訊，請參閱可[匯出的計量清單](metrics-supported-export-diagnostic-settings.md)。 
>  
>  
> 若要解決特定計量的這些限制，建議您使用 [[計量 REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)手動將其解壓縮，並使用[Azure 監視器資料收集器 API](data-collector-api.md)將它們匯入 Azure 監視器記錄。  


## <a name="destinations"></a>Destinations

平臺記錄和計量可以傳送至下表中的目的地。 請遵循下表中的每個連結，以取得將資料傳送至該目的地的詳細資訊。

| Destination | 描述 |
|:---|:---|
| [Log Analytics 工作區](#log-analytics-workspace) | 將記錄和計量傳送至 Log Analytics 工作區，可讓您使用功能強大的記錄查詢 Azure 監視器所收集的其他監視資料進行分析，也可以利用警示和視覺效果等其他 Azure 監視器功能。 |
| [事件中樞](#event-hub) | 將記錄和計量傳送至事件中樞可讓您將資料串流至外部系統，例如協力廠商 Siem 和其他 log analytics 解決方案。 |
| [Azure 儲存體帳戶](#azure-storage) | 將記錄和計量封存到 Azure 儲存體帳戶適用于 audit、靜態分析或備份。 相較于 Azure 監視器記錄和 Log Analytics 工作區，Azure 儲存體的成本較低，而且記錄可以無限期保存。 |


## <a name="prerequisites"></a>必要條件
您必須使用必要的許可權來建立診斷設定的任何目的地。 請參閱下列各節，以瞭解每個目的地的必要條件需求。

### <a name="log-analytics-workspace"></a>Log Analytics 工作區
[建立新的工作區](../learn/quick-create-workspace.md)（如果您還沒有的話）。 工作區不一定要與資源傳送記錄位於相同的訂用帳戶中，前提是設定此設定的使用者具有這兩個訂用帳戶的適當 RBAC 存取權。

### <a name="event-hub"></a>事件中樞
[建立事件中樞](../../event-hubs/event-hubs-create.md)（如果您還沒有的話）。 事件中樞命名空間不一定要和發出記錄的訂用帳戶屬於相同的訂用帳戶，只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權，而且這兩個訂用帳戶都屬於同一個 ADD 租用戶。

命名空間的共用存取原則會定義串流機制擁有的許可權。 串流至事件中樞需要 [管理]、[傳送] 和 [接聽] 許可權。 您可以在事件中樞命名空間的 [設定] 索引標籤下的 Azure 入口網站中，建立或修改共用存取原則。 若要更新診斷設定以包含串流，您必須具有該事件中樞授權規則的 ListKey 許可權。 


### <a name="azure-storage"></a>Azure 儲存體
如果您還沒有[Azure 儲存體帳戶](../../storage/common/storage-account-create.md)，請建立一個。 儲存體帳戶不一定要與資源傳送記錄位於相同的訂用帳戶中，前提是設定此設定的使用者具有這兩個訂用帳戶的適當 RBAC 存取權。

您不應該使用已儲存其他非監視資料的現有儲存體帳戶，讓您可以更有效地控制資料的存取權。 不過，如果您要同時封存活動記錄檔和資源記錄，您可以選擇使用相同的儲存體帳戶，將所有監視資料保留在中央位置。

若要將資料傳送至不可變的儲存體，請設定儲存體帳戶的不可變原則，如[設定和管理 Blob 儲存體](../../storage/blobs/storage-blob-immutability-policies-manage.md)的不可變性原則中所述。 您必須遵循本文中的所有步驟，包括啟用受保護的附加 blob 寫入。

> [!NOTE]
> Azure Data Lake Storage Gen2 帳戶目前不支援做為診斷設定的目的地，即使其在 Azure 入口網站中可能列為有效的選項。



## <a name="create-in-azure-portal"></a>在 Azure 入口網站中建立

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
       > 請參閱本文稍早 Azure 監視器記錄的路由計量 limitatation。  


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

6. 按一下 [檔案] 。

幾分鐘之後，新的設定就會出現在此資源的設定清單中，而且記錄會在產生新的事件資料時串流處理至指定的目的地。 在事件發出和[記錄分析工作區中顯示](data-ingestion-time.md)時，可能需要15分鐘的時間。

## <a name="create-using-powershell"></a>使用 PowerShell 建立

使用[set-azdiagnosticsetting 指令程式](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting)建立具有[Azure PowerShell](powershell-quickstart-samples.md)的診斷設定。 如需其參數的描述，請參閱此 Cmdlet 的檔。

> [!IMPORTANT]
> 您不能將此方法用於 Azure 活動記錄。 相反地，請使用[Azure 監視器中的 [建立診斷設定]，使用 Resource Manager 範本](diagnostic-settings-template.md)建立 Resource Manager 範本，並使用 PowerShell 進行部署。

以下是使用這三個目的地來建立診斷設定的範例 PowerShell Cmdlet。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>使用 Azure CLI 建立

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

## <a name="create-using-resource-manager-template"></a>使用 Resource Manager 範本建立
[如需 Azure 監視器中的診斷設定 Resource Manager 範本範例](../samples/resource-manager-diagnostic-settings.md)，請參閱使用 Resource Manager 範本建立或更新診斷設定。

## <a name="create-using-rest-api"></a>使用 REST API 建立
請參閱[診斷設定](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)，以使用[Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor/)來建立或更新診斷設定。

## <a name="create-using-azure-policy"></a>使用 Azure 原則建立
因為需要為每個 Azure 資源建立診斷設定，所以 Azure 原則可以用來在每個資源建立時自動建立診斷設定。 如需詳細資訊，請參閱[使用 Azure 原則大規模部署 Azure 監視器](deploy-scale.md)。


## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 平臺記錄](platform-logs-overview.md)
