---
title: 將 Azure 監視器 Application Insights 傳統資源遷移到以工作區為基礎的資源 |Microsoft Docs
description: 瞭解將 Azure 監視器 Application Insights 傳統資源升級至新的工作區架構模型所需的步驟。
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 0d2c7d1b9ee57e6d201205c04557e1b5f5623eb0
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91930572"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>遷移至以工作區為基礎的 Application Insights 資源

本指南將逐步引導您完成將傳統 Application Insights 資源遷移至以工作區為基礎之資源的程式。 工作區型的資源支援 Application Insights 與 Log Analytics 之間的完整整合。 以工作區為基礎的資源會將 Application Insights 遙測傳送至一般 Log Analytics 工作區，可讓您存取 [Azure 監視器的最新功能](#new-capabilities) ，同時將應用程式、基礎結構和平臺記錄保留在單一合併位置。

以工作區為基礎的資源可以在您的資源中 (RBAC) 的一般 Role-Based 存取控制，並免除跨應用程式/工作區查詢的需求。

**以工作區為基礎的資源目前適用于所有商業區域和 Azure 美國政府**

## <a name="new-capabilities"></a>新功能

以工作區為基礎的 Application Insights 可讓您充分利用 Azure 監視器和 Log Analytics 的所有最新功能，包括：

* [客戶管理的金鑰 (CMK) ](../platform/customer-managed-keys.md) 使用只有您可以存取的加密金鑰，為您的資料提供靜態加密。
* [Azure 私人連結](../platform/private-link-security.md)可讓您使用私人端點，將 Azure PaaS 服務安全地連結至您的虛擬網路。
* 使用[您自己的儲存體 (分析工具的 BYOS) ，快照偵錯工具](./profiler-bring-your-own-storage.md)可讓您完整掌控靜態加密原則、存留期管理原則，以及與 Application Insights Profiler 和快照偵錯工具相關聯之所有資料的網路存取。 
* 相較于隨用隨付價格，[容量保留層](../platform/manage-cost-storage.md#pricing-model)可讓您節省最多25% 的費用。 
* 透過 Log Analytics 串流內嵌加快資料內嵌的速度。

## <a name="migration-process"></a>移轉程序

當您遷移至以工作區為基礎的資源時，不會從傳統資源的儲存體將任何資料傳輸至新的工作區型儲存體。 選擇遷移會改為變更將新資料寫入至 Log Analytics 工作區的位置，同時保留您傳統資源資料的存取權。 

傳統的資源資料將會保存，並受限於傳統 Application Insights 資源的保留設定。 所有新的資料內嵌遷移後，會受相關 Log Analytics 工作區的 [保留設定](../platform/manage-cost-storage.md#change-the-data-retention-period) 所制約，也就是 [依資料類型支援不同的保留設定](../platform/manage-cost-storage.md#retention-by-data-type)。
遷移程式是 **永久性的，而且無法反轉**。 將資源遷移至以工作區為基礎的 Application Insights 之後，它一律會是以工作區為基礎的資源。 不過，一旦遷移之後，您就可以視需要經常變更目標工作區。 

> [!NOTE]
> 以工作區為基礎的 Application Insights 資源的資料內嵌和保留會透過資料所在 [的 Log Analytics 工作區計費](../platform/manage-cost-storage.md) 。 如果您在遷移前選取了超過90天的資料內嵌至傳統 Application Insights 資源，資料保留期將會透過該 Application Insights 資源繼續計費。 [深入瞭解]( ./pricing.md#workspace-based-application-insights)工作區型 Application Insights 資源的計費。

如果您不需要遷移現有的資源，而想要建立以工作區為基礎的新 Application Insights 資源，請使用以 [工作區為基礎的資源建立指南](create-workspace-resource.md)。

## <a name="pre-requisites"></a>必要條件 

- 將存取控制模式設定為設定的 Log Analytics 工作區 **`use resource or workspace permissions`** 。 

    - 以工作區為基礎的 Application Insights 資源與設定為專用設定的工作區不相容 **`workspace based permissions`** 。 若要深入瞭解 Log Analytics 工作區存取控制，請參閱[Log analytics 設定存取控制模式指導](../platform/manage-access.md#configure-access-control-mode)方針

    - 如果您還沒有現有的 Log Analytics 工作區，請[參閱 Log Analytics 工作區建立文件](../learn/quick-create-workspace.md)。
    
- 以工作區為基礎的資源不支援連續匯出，且必須停用。
完成遷移後，您可以使用 [診斷設定](../platform/diagnostic-settings.md) 來設定儲存體帳戶的資料封存，或串流至 Azure 事件中樞。  

- 請在**General**  >  您的 Log Analytics 工作區中，檢查目前的保留設定，以瞭解一般**使用量和預估成本**  >  **資料保留期**。 這項設定會影響您在遷移 Application Insights 資源之後，儲存任何新內嵌資料的時間長度。 如果您目前儲存 Application Insights 的資料超過預設的90天，而且想要保留此較長的保留期間，您可能需要調整您的工作區保留設定。

## <a name="migrate-your-resource"></a>遷移您的資源

本節將逐步引導您完成將傳統 Application Insights 資源遷移至以工作區為基礎的新資源類型的流程。

1. 從您的 Application Insights 資源中，選取左側功能表列中 [**設定**] 標題底下的 [**屬性**]。

    ![以紅色方塊反白顯示的屬性](./media/convert-classic-resource/properties.png)

2. 選取 [`Migrate to Workspace-based`]。
    
     ![[遷移資源] 按鈕](./media/convert-classic-resource/migrate.png)

3. 選擇您要在其中儲存所有未來內嵌 Application Insights 遙測的 Log Analytics 工作區。

     ![具有選取相容工作區選項的遷移嚮導 UI](./media/convert-classic-resource/migration.png)
    

遷移資源之後，您會在 [ **總覽** ] 窗格中看到對應的工作區資訊：

![工作區名稱](./media/create-workspace-resource/workspace-name.png)

按一下藍色連結文字會帶您前往相關聯的 Log Analytics 工作區，您可以在其中利用新的整合工作區查詢環境。

## <a name="understanding-log-queries"></a>瞭解記錄查詢

我們仍然會針對 Application Insights 體驗中的 Application Insights 傳統資源查詢、活頁簿、記錄型警示，提供完整的回溯相容性。 

若要針對以 [工作區為基礎的新資料表結構/架構](apm-tables.md)來撰寫查詢，您必須先流覽至 Log Analytics 工作區。 

當您直接從工作區中的 Log Analytics UI 進行查詢時，只會看到在遷移後內嵌的資料。 若要在整合的查詢體驗中進行遷移之後，查看您的傳統 Application Insights 資料 + 新的資料內嵌，請從您遷移的 Application Insights 資源內使用記錄 (分析) 查詢檢視。

## <a name="programmatic-resource-migration"></a>程式設計資源遷移

### <a name="azure-cli"></a>Azure CLI

若要存取預覽 Application Insights Azure CLI 命令，您必須先執行：

```azurecli
 az extension add -n application-insights
```

如果您未執行 `az extension add` 命令，將會看到錯誤訊息，指出： `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

現在您可以執行下列程式碼來建立您的 Application Insights 資源：

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>範例

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

如需此命令的完整 Azure CLI 文件，請參閱 [Azure CLI 文件](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest#ext-application-insights-az-monitor-app-insights-component-update)。

### <a name="azure-powershell"></a>Azure PowerShell

`Update-AzApplicationInsights`PowerShell 命令目前不支援將傳統 Application Insights 資源遷移至以工作區為基礎。 若要使用 PowerShell 建立工作區型資源，可以使用下列 Azure Resource Manager 範本，並使用 PowerShell 進行部署。

### <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本

#### <a name="template-file"></a>範本檔案

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>參數檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>修改相關聯的工作區

一旦建立了工作區型 Application Insights 資源，您就可以修改相關聯的 Log Analytics 工作區。

從 Application Insights 資源] 窗格中，選取 [**屬性**  >  **變更工作區**  >  **Log Analytics 工作區**]。

## <a name="troubleshooting"></a>疑難排解

### <a name="access-mode"></a>存取模式

**錯誤訊息：** *選取的工作區已設定為以工作區為基礎的存取模式。某些 APM 功能可能會受到影響。在工作區設定中，選取另一個工作區或允許以資源為基礎的存取。您可以使用 CLI 來覆寫此錯誤。* 

為了讓以工作區為基礎的 Application Insights 資源正常運作，您需要將目標 Log Analytics 工作區的存取控制模式變更為 **資源或工作區許可權** 設定。 這項設定位於 Log Analytics 工作區 UI 中的 [**屬性**  >  **存取控制模式]** 下。 如需詳細指示，請參閱 [Log Analytics 設定存取控制模式指引](../platform/manage-access.md#configure-access-control-mode)。 如果您的存取控制模式設定為 [專屬的 **需要工作區許可權** ] 設定，透過入口網站遷移體驗的遷移將會保持封鎖狀態。

如果您因為目前目標工作區的安全性原因而無法變更存取控制模式，建議您建立新的 Log Analytics 工作區以用於遷移。 

### <a name="continuous-export"></a>連續匯出

**錯誤訊息：** *連續匯出必須停用後再繼續。在遷移之後，請使用診斷設定進行匯出。* 

工作區型資源不支援舊版的連續匯出功能。 在遷移之前，您必須停用連續匯出。

1. 從您的 Application Insights 資源查看，在 [ **設定** ] 標題下方選取 [ **連續匯出**]。

    ![連續匯出功能表項目](./media/convert-classic-resource/continuous-export.png)

2. 選取 [停用]****。

    ![連續匯出停用按鈕](./media/convert-classic-resource/disable.png)

- 選取 [停用] 之後，您可以流覽回到遷移 UI。 如果 [編輯連續匯出] 頁面提示您的設定不會儲存，您可以在此提示中選取 [確定]，因為它與停用/啟用連續匯出無關。

- 當您成功將 Application Insights 資源遷移至以工作區為基礎的之後，您可以使用診斷設定來取代連續匯出用來提供的功能。 從您的 Application Insights 資源中選取 [**診斷**設定]，以  >  **新增診斷設定**。 您可以選取所有資料表或其中一些資料，封存至儲存體帳戶，或串流至 Azure 事件中樞。 如需診斷設定的詳細指引，請參閱 [Azure 監視器診斷設定指引](../platform/diagnostic-settings.md)。

### <a name="retention-settings"></a>保留設定

**警告訊息：** *您自訂的 Application Insights 保留設定將不適用於傳送至工作區的資料。您將需要另外重新設定。*

在遷移之前，您不需要進行任何變更，但這則訊息是提醒您目前的 Application Insights 保留設定未設定為預設的90天保留期限。 此警告訊息表示您可能會想要先修改 Log Analytics 工作區的保留設定，再進行遷移和開始內嵌新資料。 

您可以在 log analytics UI 中，檢查您目前的 log analytics 保留設定是否符合**一般**  >  **使用量和預估成本**  >  **資料保留期**。 這項設定會影響您在遷移 Application Insights 資源之後，儲存任何新內嵌資料的時間長度。

## <a name="next-steps"></a>後續步驟

* [探索度量](../platform/metrics-charts.md)
* [撰寫分析查詢](../log-query/log-query-overview.md)
