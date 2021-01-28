---
title: 建立新的 Azure 監視器 Application Insights 以工作區為基礎的資源 | Microsoft Docs
description: 瞭解啟用新的 Azure 監視器 Application Insights 以工作區為基礎的資源所需的步驟。
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 9d1ef158b8096c12c36673b62f87e1d02be69175
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936459"
---
# <a name="workspace-based-application-insights-resources"></a>工作區型 Application Insights 資源

工作區型的資源支援 Application Insights 與 Log Analytics 之間的完整整合。 您現在可以選擇將您的 Application Insights 遙測傳送到通用 Log Analytics 工作區，讓您能夠完整存取 Log Analytics 的所有功能，同時將應用程式、基礎結構、平台記錄保留在合併的單一位置。

這也可讓您在資源中 (Azure RBAC) 的常見 Azure 角色型存取控制，並免除跨應用程式/工作區查詢的需求。

> [!NOTE]
> 工作區型 Application Insights 資源的資料內嵌和保留會透過資料所在的 Log Analytics 工作區來計費。 [深入瞭解]( ./pricing.md#workspace-based-application-insights)工作區型 Application Insights 資源的計費。

## <a name="new-capabilities"></a>新功能

以工作區為基礎的 Application Insights 可讓您充分利用 Azure 監視器和 Log Analytics 的最新功能，包括：

* [客戶管理的金鑰 (CMK) ](../platform/customer-managed-keys.md) 使用只有您可以存取的加密金鑰，為您的資料提供靜態加密。
* [Azure 私人連結](../platform/private-link-security.md)可讓您使用私人端點，將 Azure PaaS 服務安全地連結至您的虛擬網路。
* 使用[您自己的儲存體 (分析工具的 BYOS) ，快照偵錯工具](./profiler-bring-your-own-storage.md)可讓您完整掌控靜態加密原則、存留期管理原則，以及與 Application Insights Profiler 和快照偵錯工具相關聯之所有資料的網路存取。 
* 相較于隨用隨付價格，[容量保留層](../platform/manage-cost-storage.md#pricing-model)可讓您節省最多25% 的費用。 
* 透過 Log Analytics 串流內嵌加快資料內嵌的速度。

## <a name="create-workspace-based-resource"></a>建立以工作區為基礎的資源

登入 [Azure 入口網站](https://portal.azure.com)，並建立 Application Insights 資源：

![工作區型 Application Insights 資源](./media/create-workspace-resource/create-workspace-based.png)

如果您還沒有現有的 Log Analytics 工作區，請[參閱 Log Analytics 工作區建立文件](../learn/quick-create-workspace.md)。

**以工作區為基礎的資源目前適用于所有商業區域和 Azure Government**

建立資源之後，您會在 [概觀] 窗格中看到對應的工作區資訊：

![工作區名稱](./media/create-workspace-resource/workspace-name.png)

按一下藍色連結文字會帶您前往相關聯的 Log Analytics 工作區，您可以在其中利用新的整合工作區查詢環境。

> [!NOTE]
> 我們仍然會針對 Application Insights 體驗中的 Application Insights 傳統資源查詢、活頁簿、記錄型警示，提供完整的回溯相容性。 若要針對[新的工作區型資料表結構/結構描述](apm-tables.md)進行查詢/檢視，您必須先瀏覽至您的 Log Analytics 工作區。 從 Application Insights 窗格中選取 **記錄 (分析)** 可讓您存取傳統 Application Insights 查詢體驗。

## <a name="copy-the-connection-string"></a>複製連接字串

[連接字串](./sdk-connection-string.md?tabs=net)可識別您想要與遙測資料建立關聯的資源。 它也可讓您修改您的資源將用來做為遙測目的地的端點。 您必須複製連接字串，並將它加入應用程式的程式碼或加入環境變數。

## <a name="monitoring-configuration"></a>監視設定

一旦建立了工作區型 Application Insights 資源，設定監視就相對簡單了。

### <a name="code-based-application-monitoring"></a>程式碼型應用程式監視

針對程式碼型應用程式監視，您只需要安裝適當的 Application Insights SDK，並將它指向新建立資源的檢測金鑰或連接字串。  

如需如何為程式碼型監視設定 Application Insights SDK 的詳細文件，請參閱語言/架構的專門文件：

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [背景工作與現代化主控台應用程式 (.NET/.NET Core)](./worker-service.md)
- [傳統主控台應用程式 (.NET)](./console.md) 
- [Java ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>無程式碼監視與 Visual Studio 資源建立

對於 Azure Functions、Azure App Service 等服務的無程式碼監視，您也必須先建立工作區型 Application Insights 資源，然後在監視設定階段指向該資源。

雖然這些服務可讓您選擇在其資源建立程序中建立新的 Application Insights 資源，但透過這些 UI 選項建立的資源目前僅限於傳統 Application Insights 體驗。

這個限制也同樣發生在適用於 ASP.NET 和 ASP.NET Core 的 Visual Studio 中的 Application Insights 資源建立體驗。 您必須使用 Visual Studio 的啟用監視 UI，選取現有的工作區型資源。 從 Visual Studio 中選取 [建立新資源]，將會限制您只能建立傳統 Application Insights 資源。

## <a name="creating-a-resource-automatically"></a>自動建立資源

### <a name="azure-cli"></a>Azure CLI

若要存取預覽 Application Insights Azure CLI 命令，您必須先執行：

```azurecli
 az extension add -n application-insights
```

如果您未執行 `az extension add` 命令，將會看到錯誤訊息，指出： `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

現在您可以執行下列程式碼來建立您的 Application Insights 資源：

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>範例

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

如需此命令的完整 Azure CLI 文件，請參閱 [Azure CLI 文件](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)。

### <a name="azure-powershell"></a>Azure PowerShell

`New-AzApplicationInsights` PowerShell 命令目前不支援建立工作區型 Application Insights 資源。 若要使用 PowerShell 建立工作區型資源，可以使用下列 Azure Resource Manager 範本，並使用 PowerShell 進行部署。

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

從 Application Insights 資源窗格中，選取 [屬性]  >  [變更工作區]  >  [Log Analytics 工作區]

## <a name="export-telemetry"></a>匯出遙測

工作區型資源不支援舊版的連續匯出功能。 因此，請從您的 Application Insights 資源內選取 [診斷設定]  >  [新增診斷設定] 。 您可以選取所有資料表或其中一些資料，封存至儲存體帳戶，或串流至 Azure 事件中樞。

> [!NOTE]
> 遙測匯出目前沒有額外的費用。 這項功能的定價資訊將于 [Azure 監視器定價] 頁面](https://azure.microsoft.com/pricing/details/monitor/)上提供。  在開始計費之前，將會傳送通知。 如果您選擇在 <feature name> 通知期間之後繼續使用，則會以適用的費率向您收費。 
 

## <a name="next-steps"></a>後續步驟

* [探索度量](../platform/metrics-charts.md)
* [撰寫分析查詢](../log-query/log-query-overview.md)
