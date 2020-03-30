---
title: 查詢性能洞察 - MariaDB 的 Azure 資料庫
description: 本文介紹 MariaDB Azure 資料庫中的查詢性能洞察功能
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527839"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的查詢效能深入解析

**適用于：** MariaDB 的 Azure 資料庫 10.2

查詢效能深入解析可協助您快速找出執行時間最長的查詢、一段時間後的變化情形，以及受到哪些等候的影響。

## <a name="common-scenarios"></a>常見的案例

### <a name="long-running-queries"></a>長時間執行的查詢

- 識別在過去 X 小時中執行最久的查詢
- 識別等候資源的前 N 項查詢
 
### <a name="wait-statistics"></a>等待統計資訊

- 瞭解查詢的等待性質
- 瞭解資源等待的趨勢以及存在資源爭用的位置

## <a name="permissions"></a>權限

需要**擁有者**或**參與者**權限，才能檢視查詢效能深入解析中的查詢文字。 **讀者**可以檢視圖表與資料表，但無法檢視查詢文字。

## <a name="prerequisites"></a>Prerequisites

資料必須存在於[查詢存放區](concepts-query-store.md)中，查詢效能深入解析才能運作。

## <a name="viewing-performance-insights"></a>檢視效能深入解析

Azure 入口網站中的[查詢效能深入解析](concepts-query-performance-insight.md)檢視會以視覺效果呈現來自查詢存放區的重要資訊。

在 MariaDB 伺服器 Azure 資料庫的門戶頁中，在功能表列的**智慧性能**部分下選擇 **"查詢性能洞察**"。

### <a name="long-running-queries"></a>長時間執行的查詢

**"長時間執行查詢"** 選項卡按每次執行的平均持續時間顯示前 5 個查詢，以 15 分鐘間隔聚合。 您可以通過從"**查詢數**"下拉清單中選擇查看更多查詢。 當您這樣做時，特定查詢識別碼的圖表色彩可能會有所變更。

您可以在圖表中按一下並拖曳來縮小到特定時間範圍。 或者，使用放大和縮小圖示分別查看較小或更大的時間段。

![查詢效能深入解析長時間執行的查詢](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>等待統計資訊 

> [!NOTE]
> 等待統計資訊用於排除查詢性能問題。 建議僅出於故障排除目的打開。 <br>如果在 Azure 門戶中收到錯誤訊息 *""Microsoft.DBforMariaDB 遇到的問題";如果收到錯誤訊息。"Microsoft.DBforMariaDB"遇到的問題;無法滿足請求。如果此問題仍然存在或出乎意料，請與支援人員聯繫，瞭解此資訊。* 查看等待統計資訊時，請使用較小的時間段。

Wait 統計資訊提供執行特定查詢期間發生的等待事件的視圖。 在[MySQL 引擎文檔中](https://go.microsoft.com/fwlink/?linkid=2098206)瞭解有關等待事件種類的更多內容。

選取 [等候統計資料]**** 索引標籤，以檢視伺服器中等候的對應視覺效果。

等待統計資訊視圖中顯示的查詢按在指定時間間隔內顯示最大等待的查詢分組。

![查詢性能洞察等待統計資訊](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>後續步驟

- 詳細瞭解有關在 MariaDB 的 Azure 資料庫中[監視和調優](concepts-monitoring.md)的問題。