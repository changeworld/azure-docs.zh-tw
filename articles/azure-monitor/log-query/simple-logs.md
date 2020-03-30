---
title: Azure 監視器（預覽）中的簡單日誌體驗 |微軟文檔
description: 通過簡單日誌體驗，您可以在 Azure 監視器中創建基本查詢，而無需直接與 KQL 交互。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660252"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Azure 監視器中的簡單日誌體驗（預覽）
Azure 監視器為使用 KQL 語言創建[日誌查詢](log-query-overview.md)提供了[豐富的體驗](get-started-portal.md)。 不過，您可能不需要 KQL 的全部功能，並且更喜歡簡化基本查詢要求的體驗。 通過簡單日誌體驗，您可以創建基本查詢，而無需直接與 KQL 交互。 您還可以使用簡單日誌作為 KQL 的學習工具，因為您需要更複雜的查詢。

> [!NOTE]
> 簡單日誌當前僅作為參數資料庫和金鑰保存庫的測試實現。 請通過[使用者語音](https://feedback.azure.com/forums/913690-azure-monitor)與 Microsoft 分享您的體驗，以説明我們確定是否會擴展和發佈此功能。


## <a name="scope"></a>影響範圍
簡單日誌體驗從所選資源的 Azure*診斷**、AzureMetrics*和*Azure 活動*表檢索資料。 

## <a name="using-simple-logs"></a>使用簡單日誌
導航到 Azure 訂閱中的任何 Cosmos DB 或金鑰保存庫，並[配置診斷設置以在日誌分析工作區中收集日誌](../platform/resource-logs-collect-storage.md)。 按一下 **"監視"** 功能表中的**日誌**以打開簡單日誌體驗。

![功能表](media/simple-logs/menu.png)

選擇**欄位**和**運算子**並指定**用於比較的值**。 按一下**+** 並指定**和/或**以添加其他條件。

![準則](media/simple-logs/criteria.png)

按一下 **"運行"** 以查看查詢結果。

## <a name="view-and-edit-kql"></a>查看和編輯 KQL
選擇**查詢編輯器**以在完整的日誌分析體驗中打開由簡單日誌查詢生成的 KQL。 

![查詢編輯器](media/simple-logs/query-editor.png)

您可以直接編輯 KQL，並使用日誌分析中的其他功能（如篩選器）來進一步優化結果。

![編輯 KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>後續步驟

- 在 Azure[門戶中完成有關使用日誌分析的](get-started-portal.md)教程。
- 完成有關[編寫日誌查詢的](get-started-portal.md)教程。
