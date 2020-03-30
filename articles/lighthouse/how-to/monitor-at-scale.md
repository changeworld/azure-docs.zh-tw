---
title: 大規模監視委派資源
description: 瞭解如何跨要管理的客戶租戶以可擴展的方式有效地使用 Azure 監視器日誌。
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 217df3c55ab54b6569bae8cacb338764ecb7125d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122892"
---
# <a name="monitor-delegated-resources-at-scale"></a>大規模監視委派資源

身為服務提供者，您可能已將多個客戶租用戶上架以進行 Azure 委派資源管理。 [Azure Lighthouse](../overview.md) 可讓服務提供者一次在多個租用戶之間執行大規模作業，讓管理工作更有效率。

本主題介紹如何在所管理的客戶租戶之間以可擴展的方式使用[Azure 監視器日誌](../../azure-monitor/platform/data-platform-logs.md)。

## <a name="create-log-analytics-workspaces"></a>創建日誌分析工作區

為了收集資料，您需要創建日誌分析工作區。 這些日誌分析工作區是 Azure 監視器收集的資料的唯一環境。 每個工作區都有自己的資料存放庫與設定，而且資料來源和解決方案會設定為將其資料儲存在特定的工作區中。

我們建議直接在客戶租戶中創建這些工作區。 這樣，他們的資料將保留在租戶中，而不是匯出到您的租戶中。 這還允許集中監視 Log Analytics 支援的任何資源或服務，使您能夠在監視哪些類型的資料方面擁有更大的靈活性。

可以使用[Azure 門戶](../../azure-monitor/learn/quick-create-workspace.md)或使用[Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)或使用[Azure PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)來創建日誌分析工作區。

## <a name="deploy-policies-that-log-data"></a>部署記錄資料的策略

創建日誌分析工作區後，可以跨客戶層次結構部署 Azure[策略](../../governance/policy/index.yml)，以便將診斷資料發送到每個租戶中的相應工作區。 您部署的確切策略可能因要監視的資源類型而異。

要瞭解有關創建策略的更多資訊，請參閱[教程：創建和管理原則以強制實施合規性](../../governance/policy/tutorials/create-and-manage.md)。 此[社區工具](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/azure-diagnostics-policy-generator)提供一個腳本，可説明您創建策略來監視您選擇的特定資源類型。

確定要部署哪些策略後，可以[大規模將它們部署到委派的訂閱](policy-at-scale.md)。

## <a name="analyze-the-gathered-data"></a>分析收集的資料

部署策略後，資料將記錄在每個客戶租戶中創建的日誌分析工作區中。 要跨所有託管客戶獲取見解，可以使用[Azure 監視器活頁簿](../../azure-monitor/platform/workbooks-overview.md)等工具收集和分析來自多個資料來源的資訊。

## <a name="next-steps"></a>後續步驟

- 了解 [Azure 監視器](../../azure-monitor/index.yml)。
- 瞭解[Azure 監視器日誌](../../azure-monitor/platform/data-platform-logs.md)。
- 了解[跨租用戶管理體驗](../concepts/cross-tenant-management-experience.md)。
