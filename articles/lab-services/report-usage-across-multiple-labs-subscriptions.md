---
title: Azure 開發人員測試實驗室跨多個實驗室和訂閱的使用
description: 瞭解如何報告跨多個實驗室和訂閱的 Azure 開發人員測試實驗室使用方式。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169189"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>報告跨多個實驗室和訂閱的 Azure 開發人員測試實驗室使用方式

大多數大型組織希望通過視覺化使用中的趨勢和異常值來跟蹤資源使用方式，從而更有效地利用這些資源。 根據資源使用方式，實驗室擁有者或經理可以自訂實驗室以提高[資源使用和成本](https://docs.microsoft.com/azure/billing/billing-getting-started)。 在 Azure 開發人員測試實驗室中，您可以按每個實驗室下載資源使用方式，從而更深入地瞭解使用模式。 這些使用模式可以説明確定更改，以提高效率。 大多數企業都希望跨[多個實驗室和訂閱](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/)使用單個實驗室和總體使用方式。 

本文討論了如何處理跨多個實驗室和訂閱的資源使用資訊。

![報告使用方式](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>單個實驗室使用方式

本節討論如何匯出單個實驗室的資源使用方式。

在匯出 DevTest Labs 的資源使用方式之前，必須設置 Azure 存儲帳戶，以允許存儲包含使用方式資料的不同檔。 執行資料匯出有兩種常見方法：

* [開發測試實驗室 REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* PowerShell Az.Resource 模組[Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2)具有`exportResourceUsage`的 操作，實驗室資源識別碼 和必要的參數。 

    [匯出或刪除個人資料](personal-data-delete-export.md)文章包含一個示例 PowerShell 腳本，其中包含有關匯出資料的詳細資訊。 

    > [!NOTE]
    > 日期參數不包含時間戳記，因此資料包含基於實驗室所在的時區的午夜的所有內容。

匯出完成後，Blob 存儲中將有多個帶有不同資源資訊的 CSV 檔。
  
目前有兩個 CSV 檔：

* *虛擬機器.csv* - 包含有關實驗室中虛擬機器的資訊
* *磁片.csv* - 包含有關實驗室中不同磁片的資訊 

這些檔存儲在*實驗室資源使用*blob 容器中，其名稱為實驗室名稱、實驗室唯一 ID、執行日期以及匯出請求中的完整日期或開始日期。 一個示例 blob 結構是：

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>匯出所有實驗室的使用方式

要匯出多個實驗室的使用方式資訊，請考慮使用 

* [Azure 函數](https://docs.microsoft.com/azure/azure-functions/)，提供多種語言版本，包括 PowerShell，或 
* [Azure 自動化運行簿](https://docs.microsoft.com/azure/automation/)，使用 PowerShell、Python 或自訂圖形設計器編寫匯出代碼。

使用這些技術，您可以在特定的日期和時間在所有實驗室上執行單個實驗室匯出。 

Azure 函數應將資料推送到長期存儲。 為多個實驗室匯出資料時，匯出可能需要一些時間。 為了説明提高性能並減少資訊重複的可能性，我們建議並存執行每個實驗室。 要完成並行性，可以非同步運行 Azure 函數。 還利用 Azure 函數提供的計時器觸發器。

## <a name="using-a-long-term-storage"></a>使用長期存儲

長期存儲將來自不同實驗室的匯出資訊合併到單個資料來源中。 使用長期存儲的另一個好處是能夠從存儲帳戶中刪除檔，以減少重複和成本。 

長期存儲可用於執行任何文本操作，例如： 

* 添加易記名稱
* 創建複雜的分組
* 聚合資料。

一些常見的存儲解決方案是[：SQL 伺服器](https://azure.microsoft.com/services/sql-database/)[、Azure 資料湖](https://azure.microsoft.com/services/storage/data-lake-storage/)和[Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)。 選擇您選擇的長期存儲解決方案取決於偏好。 在視覺化資料時，您可以考慮根據工具在交互可用性方面提供的內容來選擇該工具。

## <a name="visualizing-data-and-gathering-insights"></a>視覺化資料和收集見解

使用您選擇的資料視覺化檢視連接到長期存儲以顯示使用方式資料並收集見解以驗證使用效率。 例如[，Power BI](https://docs.microsoft.com/power-bi/power-bi-overview)可用於組織和顯示使用方式資料。 

可以使用[Azure 資料工廠](https://azure.microsoft.com/services/data-factory/)在單個位置介面中創建、連結和管理資源。 如果需要更大的控制，可以在單個資源組中創建單個資源，並且獨立于資料工廠服務進行管理。  

## <a name="next-steps"></a>後續步驟

一旦系統建立，資料移動到長期存儲，下一步是提出資料需要回答的問題。 例如： 

-   VM 大小使用方式是什麼？

    使用者選擇高性能（更昂貴的）VM 大小嗎？
-   正在使用哪些應用商店映射？

    自訂映射是最常見的 VM 基礎，應像[共用映射庫](../virtual-machines/windows/shared-image-galleries.md)或[映射工廠](image-factory-create.md)一樣構建公共映射存儲。
-   正在使用或不使用哪些自訂映射？
