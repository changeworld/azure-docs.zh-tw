---
title: Azure 監視器中的監視解決方案 | Microsoft Docs
description: Azure 監視器中的監視解決方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。  本文提供有關安裝及使用監視解決方案的資訊。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/12/2020
ms.openlocfilehash: 406991de2441836565c911ef8a8e1402f6398eb0
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87824778"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure 監視器中的監視解決方案

監視解決方案會利用 Azure 中的服務來提供特定應用程式或服務之作業的額外分析。 本文提供 Azure 中監視解決方案的簡短概觀，以及使用和安裝它們的詳細資料。 您可以針對您使用的任何應用程式和服務，將監視解決方案新增至 Azure 監視器。 這些監視解決方案通常免費提供，但是會收集可能造成使用費用的資料。

## <a name="use-monitoring-solutions"></a>使用監視解決方案

在 Azure 監視器中開啟 [概觀] 頁面，以顯示工作區中每個已安裝解決方案的圖格。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com)。 搜尋並選取 [監視]。
1. 在 [Insights] 功能表下方，選取 [其他]。
1. 使用畫面頂端的下拉式清單方塊，來變更針對圖格所使用的工作區或時間範圍。
1. 按一下解決方案的圖格以開啟其檢視，其中包含所收集資料的更詳細分析。

![概觀](media/solutions/overview.png)

監視解決方案可以包含多種的 Azure 資源，而您可以檢視解決方案隨附的任何資源，就像任何其他資源一樣。 例如，解決方案中包含的任何記錄查詢都會列在 [查詢總管](../log-query/get-started-portal.md#load-queries) 中的 [解決方案查詢] 下方。您在使用[記錄查詢](../log-query/log-query-overview.md) 執行臨機操作分析時，使用那些查詢。

## <a name="list-installed-monitoring-solutions"></a>列出已安裝的監視解決方案

### <a name="portal"></a>[入口網站](#tab/portal)

使用下列程序，列出您的訂用帳戶中已安裝的監視解決方案。

1. 移至 [Azure 入口網站](https://ms.portal.azure.com)。 搜尋並選取 [解決方案]。
1. 隨即列出您所有工作區中安裝的解決方案。 解決方案的名稱後面接著其安裝所在的工作區名稱。
1. 使用畫面頂端的下拉式方塊，依據訂用帳戶或資源群組進行篩選。

![列出所有解決方案](media/solutions/list-solutions-all.png)

按一下解決方案名稱，以開啟其摘要頁面。 此頁面會顯示解決方案中包含的任何檢視，並為解決方案本身及其工作區提供不同的選項。 使用以上其中一個程序來檢視解決方案的摘要頁面，以列出解決方案，然後按一下解決方案的名稱。

![解決方案屬性](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用[az monitor log analytics solution list](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list)命令來列出您的訂用帳戶中所安裝的監視解決方案。   `list`執行命令之前，請遵循[安裝監視解決方案](#install-a-monitoring-solution)中的必要條件。

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>安裝監視解決方案

### <a name="portal"></a>[入口網站](#tab/portal)

您可以從 [Azure Marketplace](https://azuremarketplace.microsoft.com) 取得來自 Microsoft 和夥伴的管理解決方案。 您可以使用下列程序來搜尋可用的解決方案並安裝它們。 當您安裝解決方案時，您必須選取將在其中安裝解決方案且將收集其資料的 [Log Analytics 工作區](../platform/manage-access.md)。

1. 從[訂用帳戶的解決方案清單](#list-installed-monitoring-solutions)，按一下 [新增]。
1. 瀏覽或搜尋搜尋方案。 您也可以從[此搜尋連結](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)瀏覽解決方案。
1. 找出您想要的監視解決方案，然後完整閱讀它的描述。
1. 按一下 [建立] 以啟動安裝程序。
1. 當安裝程序開始時，系統會提示您指定 Log Analytics 工作區，並提供解決方案的任何必要設定。

![安裝解決方案](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>從社群安裝解決方案

社群成員可以將管理解決方案提交至 Azure 快速入門範本。 您可以直接安裝這些解決方案，或下載其範本以便稍後安裝。

1. 依照 [Log Analytics 工作區和自動化帳戶](#log-analytics-workspace-and-automation-account)中所述的程序來連結工作區和帳戶。
2. 移至 [Azure 快速入門範本](https://azure.microsoft.com/documentation/templates/)。
3. 搜尋您感興趣的解決方案。
4. 選取結果中的解決方案，以檢視其詳細資料。
5. 按一下 [部署至 Azure] 按鈕。
6. 除了解決方案中任何參數的值以外，系統還會提示您提供資源群組和位置等資訊。
7. 按一下 [購買] 以安裝解決方案。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prepare-your-environment"></a>準備您的環境

1. 安裝 Azure CLI

   您必須先[安裝 Azure CLI](/cli/azure/install-azure-cli) ，才能執行 CLI 參考命令。  如果您想要的話，也可以使用 Azure Cloud Shell 來完成這篇文章中的步驟。  Azure Cloud Shell 是您透過瀏覽器使用的互動式 Shell 環境。  使用下列其中一種方法啟動 Cloud Shell：

   - 前往來開啟 Cloud Shell[https://shell.azure.com](https://shell.azure.com)

   - 在[Azure 入口網站](https://portal.azure.com)的右上角功能表列上，選取 [ **Cloud Shell** ] 按鈕

1. 登入。

   如果您使用的是 CLI 的本機安裝，請使用[az login 命令登](/cli/azure/reference-index#az-login)入。  請遵循您終端機上顯示的步驟，來完成驗證程序。

    ```azurecli
    az login
    ```

1. 安裝 `log-analytics` 擴充功能

   此 `log-analytics` 命令是核心 Azure CLI 的實驗性延伸模組。 深入瞭解[使用延伸](/cli/azure/azure-cli-extensions-overview?)模組中 Azure CLI 的延伸模組參考。

   ```azurecli
   az extension add --name log-analytics
   ```

   應該會出現下列警告。

   ```output
   The installed extension `log-analytics` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>使用 Azure CLI 安裝解決方案

當您安裝解決方案時，您必須選取將在其中安裝解決方案且將收集其資料的 [Log Analytics 工作區](../platform/manage-access.md)。  使用 Azure CLI，您可以使用[az monitor log analytics 工作區](/cli/azure/monitor/log-analytics/workspace)參考命令來管理工作區。  依照 [Log Analytics 工作區和自動化帳戶](#log-analytics-workspace-and-automation-account)中所述的程序來連結工作區和帳戶。

使用[az monitor log analytics 解決方案 create](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution)來安裝監視解決方案。  以方括弧括住的參數是選擇性的。

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

以下程式碼範例會為 OMSGallery/容器的方案產品建立記錄分析解決方案。

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 工作區和自動化帳戶

所有監視解決方案都需要有 [Log Analytics 工作區](../platform/manage-access.md)，才能儲存解決方案所收集的資料以及裝載其記錄搜尋和檢視。 有些解決方案也需要有 [OMS 工作區](../../automation/automation-security-overview.md)來容納 Runbook 和相關資源。 工作區和帳戶必須符合下列需求。

* 每次安裝解決方案時，只能使用一個 Log Analytics 工作區和一個自動化帳戶。 您可以將解決方案個別安裝於多個工作區中。
* 如果解決方案需要自動化帳戶，則 Log Analytics 工作區和自動化帳戶必須彼此連結。 Log Analytics 工作區只能連結到一個自動化帳戶，而自動化帳戶只能連結到一個 Log Analytics 工作區。
* 若要互相連結，Log Analytics 工作區和自動化帳戶必須位於相同的訂閱中，但可位於部署至相同區域的不同資源群組中。 例外狀況是位於美國東部區域的工作區，以及位於美國東部 2 的自動化帳戶。

當您透過 Azure Marketplace 安裝解決方案時，系統會提示您提供工作區和自動化帳戶。 如果它們尚未連結，則會建立它們之間的連結。

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>驗證 Log Analytics 工作區與自動化帳戶之間的連結

您可以使用下列程序，驗證 Log Analytics 工作區與自動化帳戶之間的連結。

1. 在 Azure 入口網站中選取自動化帳戶。
1. 捲動到功能表的 [相關資源] 區段，然後選取 [連結的工作區]。
1. 如果 [工作區] 與自動化帳戶建立連結，則此頁面會列出它所連結的工作區。 如果您選取列出的工作區名稱，系統會將您重新導向至該工作區的概觀頁面。

## <a name="remove-a-monitoring-solution"></a>移除監視解決方案

### <a name="portal"></a>[入口網站](#tab/portal)

若要使用入口網站移除已安裝的解決方案，請在[已安裝的解決方案清單](#list-installed-monitoring-solutions)中找到它。 按一下解決方案名稱，以開啟其摘要頁面，然後按一下 [刪除]。

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 移除已安裝的解決方案，請使用[az monitor log analytics 解決方案 delete](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete)命令。

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

* * *

## <a name="next-steps"></a>後續步驟

* 取得[來自 Microsoft 的監視解決方案清單](../monitor-reference.md)。
* 了解如何[建立查詢](../log-query/log-query-overview.md)，以分析監視解決方案所收集的資料。
* 查看[Azure 監視器的所有 Azure CLI 命令](/cli/azure/azure-cli-reference-for-monitor)。