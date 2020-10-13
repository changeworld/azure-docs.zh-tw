---
title: 跨多個實驗室和訂用帳戶 Azure DevTest Labs 使用量
description: 瞭解如何報告跨多個實驗室和訂用帳戶的 Azure DevTest Labs 使用量。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d2663113e929145308f5a5712b968f3551668c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87287273"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>報告跨多個實驗室和訂用帳戶的 Azure DevTest Labs 使用量

大部分的大型組織想要藉由將使用量中的趨勢和極端值視覺化，來追蹤資源使用量，以更有效地使用這些資源。 根據資源的使用方式，實驗室擁有者或管理員可以自訂實驗室，以 [改善資源的使用和成本](../cost-management-billing/manage/getting-started.md)。 在 Azure DevTest Labs 中，您可以下載每個實驗室的資源使用量，讓您更深入瞭解使用模式的歷程記錄。 這些使用模式有助於找出變更，以提升效率。 大部分的企業都想要在 [多個實驗室和](/azure/architecture/cloud-adoption/decision-guides/subscriptions/)訂用帳戶中使用個別的實驗室和整體使用方式。 

本文討論如何跨多個實驗室和訂用帳戶處理資源使用量資訊。

![報告使用方式](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>個別實驗室使用方式

本節討論如何匯出單一實驗室的資源使用量。

在匯出 DevTest Labs 的資源使用方式之前，您必須設定 Azure 儲存體帳戶，以允許儲存包含使用量資料的不同檔案。 有兩種常見的方式可以執行資料匯出：

* [DevTest Labs REST API](/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az. Resource 模組 [調用-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) 的動作為 `exportResourceUsage` 、實驗室資源識別碼和必要的參數。 

    [匯出或刪除個人資料](personal-data-delete-export.md)文章包含範例 PowerShell 腳本，其中包含所匯出資料的詳細資訊。 

    > [!NOTE]
    > Date 參數不包含時間戳記，因此資料會根據實驗室所在的時區，從午夜中包含所有專案。

匯出完成後，blob 儲存體中會有多個 CSV 檔案與不同的資源資訊。
  
目前有兩個 CSV 檔案：

* *virtualmachines.csv* -包含實驗室中虛擬機器的相關資訊
* *disks.csv* -包含實驗室中不同磁片的相關資訊 

這些檔案會儲存在 *labresourceusage* blob 容器中的實驗室名稱、實驗室唯一識別碼、執行日期，以及以匯出要求為基礎的完整或開始日期。 範例 blob 結構如下：

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>匯出所有實驗室的使用方式

若要匯出多個實驗室的使用資訊，請考慮使用 

* [Azure Functions](../azure-functions/index.yml)，提供許多語言版本，包括 PowerShell 或 
* [Azure 自動化 runbook](../automation/index.yml)，請使用 PowerShell、Python 或自訂圖形設計工具來撰寫匯出程式碼。

使用這些技術，您可以在特定的日期和時間，在所有實驗室上執行個別的實驗室匯出。 

您的 Azure 函式應將資料推送至長期的儲存體。 匯出多個實驗室的資料時，匯出可能需要一些時間。 為了協助效能並降低重複資訊的可能性，我們建議您平行執行每個實驗室。 若要完成平行處理，請以非同步方式執行 Azure Functions。 也請利用 Azure Functions 提供的計時器觸發程式。

## <a name="using-a-long-term-storage"></a>使用長期儲存

長期儲存體會將不同實驗室的匯出資訊合併成單一資料來源。 使用長期儲存體的另一個優點是能夠從儲存體帳戶中移除檔案，以減少重複和成本。 

長期儲存可以用來執行任何文字操作，例如： 

* 加入易記名稱
* 建立複雜的分組
* 匯總資料。

以下是一些常見的儲存體解決方案： [SQL Server](https://azure.microsoft.com/services/sql-database/)、 [Azure Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)和 [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。 選擇您選擇的長期儲存體解決方案取決於喜好設定。 您可以考慮在視覺化資料時，根據互動可用性所提供的工具，選擇工具。

## <a name="visualizing-data-and-gathering-insights"></a>視覺化資料和收集見解

使用您選擇的資料視覺效果工具來連線到您的長期儲存體，以顯示使用量資料並收集見解以驗證使用效率。 例如， [Power BI](/power-bi/power-bi-overview) 可以用來組織和顯示使用方式資料。 

您可以使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) ，在單一位置介面內建立、連結和管理您的資源。 如果需要更大的控制，則可以在單一資源群組內建立個別資源，並獨立于 Data Factory 服務之外管理。  

## <a name="next-steps"></a>後續步驟

設定系統並將資料移至長期儲存體之後，下一步就是要提出資料需要回答的問題。 例如： 

-   什麼是 VM 大小使用量？

    使用者選取高效能 (昂貴) VM 大小？
-   正在使用哪些 Marketplace 映射？

    是最常見的 VM 基底的自訂映射，是否應建立 [共用映射庫](../virtual-machines/windows/shared-image-galleries.md) 或 [映射](image-factory-create.md)處理站等常見映射存放區。
-   哪些自訂映射正在使用或未使用？
