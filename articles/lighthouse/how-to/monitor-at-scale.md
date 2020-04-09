---
title: 大規模監視委派資源
description: 瞭解如何跨要管理的客戶租戶以可擴充的方式有效地使用 Azure 監視器日誌。
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 9a600cec524c9f715ada9da09dea35f3baa98151
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985196"
---
# <a name="monitor-delegated-resources-at-scale"></a>大規模監視委派資源

身為服務提供者，您可能已將多個客戶租用戶上架以進行 Azure 委派資源管理。 [Azure Lighthouse](../overview.md) 可讓服務提供者一次在多個租用戶之間執行大規模作業，讓管理工作更有效率。

這個主題介紹如何在所管理的客戶租戶之間以可擴充的方式使用[Azure 監視器日誌](../../azure-monitor/platform/data-platform-logs.md)。

## <a name="create-log-analytics-workspaces"></a>建立紀錄分析工作區

為了收集數據,您需要創建日誌分析工作區。 這些日誌分析工作區是 Azure 監視器收集的數據的唯一環境。 每個工作區都有自己的資料存放庫與設定，而且資料來源和解決方案會設定為將其資料儲存在特定的工作區中。

我們建議直接在客戶租戶中創建這些工作區。 這樣,他們的數據將保留在租戶中,而不是匯出到您的租戶中。 這還允許集中監視 Log Analytics 支援的任何資源或服務,使您能夠在監視哪些類型的數據方面擁有更大的靈活性。

可以使用[Azure 門戶](../../azure-monitor/learn/quick-create-workspace.md)或使用[Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)或使用[Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)來建立日誌分析工作區。

## <a name="deploy-policies-that-log-data"></a>部署記錄資料的原則

創建日誌分析工作區後,可以跨客戶層次結構部署 Azure[策略](../../governance/policy/index.yml),以便將診斷數據發送到每個租戶中的相應工作區。 您部署的確切策略可能因要監視的資源類型而異。

要瞭解有關創建策略的更多資訊,請參閱[教程:建立和管理原則以強制實施合規性](../../governance/policy/tutorials/create-and-manage.md)。 此[社區工具](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator)提供一個文稿,可説明您創建策略來監視您選擇的特定資源類型。

確定要部署哪些策略後,可以[大規模將它們部署到委派的訂閱](policy-at-scale.md)。

## <a name="analyze-the-gathered-data"></a>分析收集的資料

部署策略後,數據將記錄在每個客戶租戶中創建的日誌分析工作區中。 要跨所有託管客戶獲取見解,可以使用[Azure 監視器工作簿](../../azure-monitor/platform/workbooks-overview.md)等工具收集和分析來自多個數據來源的資訊。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 監視器](../../azure-monitor/index.yml)。
- 瞭解[Azure 監視器紀錄](../../azure-monitor/platform/data-platform-logs.md)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
