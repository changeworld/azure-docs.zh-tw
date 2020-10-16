---
title: 建立診斷設定以將平台記錄和計量傳送至不同目的地
description: 使用診斷設定，將 Azure 監視器平臺計量和記錄傳送至 Azure 監視器記錄、Azure 儲存體或 Azure 事件中樞。
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: fcbce9e7a5b24cbbe695b2ad664137875464b705
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107924"
---
# <a name="create-diagnostic-settings-to-send-platform-logs-and-metrics-to-different-destinations"></a>建立診斷設定以將平台記錄和計量傳送至不同目的地
Azure 中的[平臺記錄](platform-logs-overview.md)，包括 azure 活動記錄和資源記錄，可針對 azure 資源及其相依的 azure 平臺提供詳細的診斷和審核資訊。 預設會收集[平臺計量](data-platform-metrics.md)，並通常儲存在 Azure 監視器計量資料庫中。 本文提供有關建立及設定診斷設定，以將平臺計量和平臺記錄傳送至不同目的地的詳細資料。

> [!IMPORTANT]
> 建立活動記錄的診斷設定之前，您應該先停用任何舊版設定。 如需詳細資訊，請參閱 [舊版收集方法](activity-log.md#legacy-collection-methods) 。

每個 Azure 資源都需要自己的診斷設定，以定義下列準則：

- 傳送至設定中所定義目的地之記錄和計量資料的類別。 可用類別會隨不同資源類型而異。
- 一個或多個要傳送記錄的目的地。 目前的目的地包括 Log Analytics 工作區、事件中樞和 Azure 儲存體。

單一診斷設定可以定義一個以上的目的地。 如果您想要將資料傳送至超過一個的特定目的地類型 (例如，兩個不同的 Log Analytics 工作區)，請建立多個設定。 每個資源可以有最多 5 個診斷設定。

下列影片將逐步引導您使用診斷設定來路由傳送平臺記錄。
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

> [!NOTE]
> [平臺計量](metrics-supported.md) 會自動傳送給 [Azure 監視器計量](data-platform-metrics.md)。 您可以使用診斷設定，將某些 Azure 服務的計量傳送至 Azure 監視器記錄，以使用具有特定限制的 [記錄查詢](../log-query/log-query-overview.md) 來分析其他監視資料。 
>  
>  
> 目前不支援透過診斷設定傳送多維度計量。 跨維度值所彙總的維度計量會匯出為扁平化單一維度計量。 *例如*：區塊鏈上的 ' IOReadBytes ' 度量可以在每個節點層級上進行探索和繪製。 不過，當透過診斷設定匯出時，匯出的計量會以所有節點的所有讀取位元組表示。 此外，由於內部限制，並不會將所有計量匯出至 Azure 監視器記錄/記錄分析。 如需詳細資訊，請參閱可 [匯出的計量清單](metrics-supported-export-diagnostic-settings.md)。 
>  
>  
> 若要解決這些特定度量的限制，建議您使用 [ [計量 REST API](/rest/api/monitor/metrics/list) 手動將它們解壓縮，並使用 [Azure 監視器資料收集器 API](data-collector-api.md)將它們匯入 Azure 監視器記錄中。  


## <a name="destinations"></a>Destinations
您可以將平臺記錄和計量傳送至下表中的目的地。 

| Destination | 描述 |
|:---|:---|
| [Log Analytics 工作區](design-logs-deployment.md) | 將記錄和計量傳送至 Log Analytics 工作區，可讓您使用強大的記錄查詢來分析 Azure 監視器所收集的其他監視資料，也可以利用其他 Azure 監視器功能，例如警示和視覺效果。 |
| [事件中樞](../../event-hubs/index.yml) | 將記錄和計量傳送至事件中樞，可讓您將資料串流至外部系統，例如協力廠商 Siem 和其他 log analytics 解決方案。  |
| [Azure 儲存體帳戶](../../storage/blobs/index.yml) | 將記錄和計量封存至 Azure 儲存體帳戶有助於進行審核、靜態分析或備份。 相較于 Azure 監視器記錄和 Log Analytics 工作區，Azure 儲存體的成本較低，而且記錄可以無限期保留。  |


### <a name="destination-requirements"></a>目的地需求

在建立診斷設定之前，必須先建立診斷設定的任何目的地。 只要進行設定的使用者有這兩個訂用帳戶的適當 RBAC 存取權，就不一定要將目的地與傳送記錄的資源位於相同的訂用帳戶中。 下表提供每個目的地的獨特需求，包括任何區域限制。

| Destination | 需求 |
|:---|:---|
| Log Analytics 工作區 | 工作區不一定要與受監視的資源位於相同的區域中。|
| 事件中樞 | 命名空間的共用存取原則會定義串流機制擁有的許可權。 串流至事件中樞需要管理、傳送和接聽的許可權。 若要更新診斷設定以包含串流，您必須具有該事件中樞授權規則的 ListKey 許可權。<br><br>如果資源是區域，事件中樞命名空間必須位於與所監視資源相同的區域中。 |
| Azure 儲存體帳戶 | 您不應該使用現有的儲存體帳戶，其中儲存了其他非監視的資料，讓您可以更妥善地控制資料的存取。 但是，如果您要將活動記錄檔和資源記錄檔一起保存在一起，您可以選擇使用相同的儲存體帳戶，將所有監視資料保留在中央位置。<br><br>若要將資料傳送至不可變的儲存體，請為儲存體帳戶設定不可變的原則，如 [設定和管理 Blob 儲存體的永久性原則](../../storage/blobs/storage-blob-immutability-policies-manage.md)中所述。 您必須遵循本文中的所有步驟，包括啟用受保護的附加 blob 寫入。<br><br>如果資源是區域，則儲存體帳戶必須位於與所監視資源相同的區域中。 |

> [!NOTE]
> Azure Data Lake Storage Gen2 帳戶目前不支援做為診斷設定的目的地，即使其在 Azure 入口網站中可能列為有效的選項。



## <a name="create-in-azure-portal"></a>在 Azure 入口網站中建立

您可以從 [Azure 監視器] 功能表或從該資源的功能表，設定 Azure 入口網站中的診斷設定。

1. 您可以在 Azure 入口網站中設定診斷設定，視資源而定。

   - 針對單一資源，在資源功能表中，按一下 [**監視**] 底下的 [**診斷設定**]。

        ![醒目提示診斷設定 Azure 入口網站中資源功能表 [監視] 區段的螢幕擷取畫面。](media/diagnostic-settings/menu-resource.png)

   - 針對一或多個資源，按一下 [Azure 監視器] 功能表中 [**設定**] 下的 [**診斷設定**]，然後按一下資源。

        ![醒目提示 [診斷設定] 的 [Azure 監視器] 功能表中 [設定] 區段的螢幕擷取畫面。](media/diagnostic-settings/menu-monitor.png)

   - 針對 [活動記錄檔]，按一下 [ **Azure 監視器**] 功能表中的 [**活動記錄**]，然後按一下 [**診斷設定**]。 請務必停用活動記錄的任何舊版設定。 如需詳細資料，請參閱 [停用現有的設定](./activity-log.md#legacy-collection-methods) 。

        ![[Azure 監視器] 功能表的螢幕擷取畫面，其中已選取 [活動記錄] 和 [Monitor-Activity 記錄] 功能表列中醒目提示的診斷設定。](media/diagnostic-settings/menu-activity-log.png)

2. 如果您選取的資源上沒有任何設定，系統會提示您建立設定。 按一下「新增診斷設定」  。

   ![新增診斷設定 - 無現有的設定](media/diagnostic-settings/add-setting.png)

   如果資源上有現有的設定，您會看到已設定的設定清單。 按一下 [ **新增診斷設定** ]，以新增設定或 **編輯設定** 來編輯現有的設定。 每個設定都不能有一個以上的目的地類型。

   ![新增診斷設定 - 現有的設定](media/diagnostic-settings/edit-setting.png)

3. 如果您的設定還沒有名稱，請為您的設定命名。

    ![新增診斷設定](media/diagnostic-settings/setting-new-blank.png)

4. **類別目錄詳細資料 (路由) ** -核取您想要傳送給稍後指定之目的地的每個資料類別的核取方塊。 每個 Azure 服務的類別清單各有不同。

     - **AllMetrics** 會將資源的平臺計量路由至 Azure 記錄存放區，但會以記錄形式傳送。 這些計量通常只會傳送至 Azure 監視器計量時間序列資料庫。 將它們傳送至 Azure 監視器記錄存放區 (可透過 Log Analytics 搜尋，) 您將它們整合到查詢中，以搜尋其他記錄檔。 並非所有資源類型都可使用此選項。 當支援時， [Azure 監視器支援的計量](metrics-supported.md) 會列出針對哪些資源類型收集的計量。

       > [!NOTE]
       > 請參閱本文稍早的將計量路由至 Azure 監視器記錄的限制。  


     - **記錄** 會根據資源類型列出可用的不同類別。 檢查您想要路由傳送至目的地的任何類別。

5. **目的地詳細資料** -勾選每個目的地的方塊。 當您核取每個方塊時，會出現可讓您新增其他資訊的選項。

      ![傳送至 Log Analytics 或事件中樞](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** -輸入訂用帳戶和工作區。  如果您沒有工作區，您需要 [先建立一個工作區，然後再繼續](../learn/quick-create-workspace.md)。

    1. **事件中樞** -指定下列準則：
       - 事件中樞所屬的訂用帳戶
       - 事件中樞命名空間-如果您還沒有帳戶，則必須 [建立一個](../../event-hubs/event-hubs-create.md)
       - 事件中樞名稱 (選擇性的) 傳送所有資料。 如果您未指定名稱，則會為每個記錄類別建立事件中樞。 如果您要傳送多個類別，您可能會想要指定名稱來限制建立的事件中樞數目。 如需詳細資料，請參閱 [Azure 事件中樞配額和限制](../../event-hubs/event-hubs-quotas.md) 。
       - 事件中樞原則 (選擇性) 原則會定義串流機制擁有的許可權。 如需詳細資訊，請參閱 [事件中樞-功能](../../event-hubs/event-hubs-features.md#publisher-policy)。

    1. **儲存體** -選擇訂用帳戶、儲存體帳戶和保留原則。

        ![傳送至儲存體](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > 考慮將保留原則設定為0，並使用排程工作從儲存體手動刪除您的資料，以避免未來可能產生混淆。
        >
        > 首先，如果您使用儲存體進行封存，通常會希望資料大約超過365天。 其次，如果您選擇的保留原則大於0，到期日會在儲存體時附加至記錄。 儲存之後，您就無法變更這些記錄的日期。
        >
        > 例如，如果您將 *WorkflowRuntime* 的保留原則設定為180天，然後將其設定為365天，則在這前24小時內儲存的記錄將會在180天后自動刪除，而該類型的所有後續記錄將會在365天后自動刪除。 稍後變更保留原則時，不會讓前24小時的記錄保留大約365天。

6. 按一下 [檔案] 。

經過幾分鐘之後，新的設定就會出現在此資源的設定清單中，並在產生新的事件資料時，將記錄串流處理至指定的目的地。 當事件發出和 [記錄在 Log Analytics 工作區中出現](data-ingestion-time.md)時，最多可能需要15分鐘的時間。

## <a name="create-using-powershell"></a>使用 PowerShell 建立

使用 [>set-azdiagnosticsetting 指令程式](/powershell/module/az.monitor/set-azdiagnosticsetting) ，以 [Azure PowerShell](../samples/powershell-samples.md)建立診斷設定。 請參閱此 Cmdlet 的說明文件，以瞭解其參數的描述。

> [!IMPORTANT]
> 您無法將此方法用於 Azure 活動記錄。 相反地，請使用 [Resource Manager 範本 Azure 監視器中的 [建立診斷設定](../samples/resource-manager-diagnostic-settings.md) ]，以建立 Resource Manager 範本並使用 PowerShell 進行部署。

以下是範例 PowerShell Cmdlet，可使用三個目的地建立診斷設定。

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>使用 Azure CLI 建立

使用 [az monitor 診斷-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 命令，以 [Azure CLI](/cli/azure/monitor?view=azure-cli-latest)建立診斷設定。 請參閱此命令的檔，以瞭解其參數的描述。

> [!IMPORTANT]
> 您無法將此方法用於 Azure 活動記錄。 相反地，請使用 [Resource Manager 範本 Azure 監視器中的 [建立診斷設定](../samples/resource-manager-diagnostic-settings.md) ]，以建立 Resource Manager 範本並使用 CLI 來部署它。

以下是使用三個目的地建立診斷設定的 CLI 命令範例。

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
請參閱 [Azure 監視器中的診斷設定 Resource Manager 範本範例](../samples/resource-manager-diagnostic-settings.md) ，以 Resource Manager 範本建立或更新診斷設定。

## <a name="create-using-rest-api"></a>使用 REST API 建立
請參閱使用[Azure 監視器 REST API](/rest/api/monitor/)來建立或更新診斷設定的[診斷設定](/rest/api/monitor/diagnosticsettings)。

## <a name="create-using-azure-policy"></a>使用 Azure 原則建立
因為每個 Azure 資源都需要建立診斷設定，所以 Azure 原則可以用來在每個資源建立時自動建立診斷設定。 如需詳細資訊，請參閱 [使用 Azure 原則大規模部署 Azure 監視器](../deploy-scale.md) 。


## <a name="next-steps"></a>後續步驟

- [深入瞭解 Azure 平臺記錄](platform-logs-overview.md)