---
title: 查詢效能深入解析-適用於 MariaDB 的 Azure 資料庫
description: 本文說明中的查詢效能深入解析功能適用於 MariaDB 的 Azure 資料庫
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 88777ee44551ed6abdb7a6c7c909d6bf55db48c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79527839"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的查詢效能深入解析

**適用物件：** 適用於 MariaDB 的 Azure 資料庫10。2

查詢效能深入解析可協助您快速找出執行時間最長的查詢、一段時間後的變化情形，以及受到哪些等候的影響。

## <a name="common-scenarios"></a>常見的案例

### <a name="long-running-queries"></a>長時間執行的查詢

- 識別在過去 X 小時中執行最久的查詢
- 識別等候資源的前 N 項查詢
 
### <a name="wait-statistics"></a>等候統計資料

- 了解查詢的等候本質
- 了解資源等候的趨勢，以及資源爭用所在的位置

## <a name="permissions"></a>權限

需要**擁有者**或**參與者**權限，才能檢視查詢效能深入解析中的查詢文字。 **讀者**可以檢視圖表與資料表，但無法檢視查詢文字。

## <a name="prerequisites"></a>Prerequisites

資料必須存在於[查詢存放區](concepts-query-store.md)中，查詢效能深入解析才能運作。

## <a name="viewing-performance-insights"></a>檢視效能深入解析

Azure 入口網站中的[查詢效能深入解析](concepts-query-performance-insight.md)檢視會以視覺效果呈現來自查詢存放區的重要資訊。

在適用於 MariaDB 的 Azure 資料庫伺服器的入口網站頁面中，選取功能表列的 [**智慧效能**] 區段下的 [**查詢效能深入解析**]。

### <a name="long-running-queries"></a>長時間執行的查詢

[長時間執行的查詢] 索引標籤會每隔 15 分鐘彙總一次，依每次執行的平均持續時間，顯示前 5 個查詢。 您可以從 [查詢數目] 下拉式清單中選取，以檢視更多查詢。 當您這樣做時，特定查詢識別碼的圖表色彩可能會有所變更。

您可以在圖表中按一下並拖曳來縮小到特定時間範圍。 或者，使用放大和縮小圖示，分別檢視一段較短或較長的時間。

![查詢效能深入解析長時間執行的查詢](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>等候統計資料 

> [!NOTE]
> 等候統計資料的目的是為了針對查詢效能問題進行疑難排解。 建議您只針對疑難排解目的開啟此功能。 <br>如果您收到 Azure 入口網站「*遇到 ' DBforMariaDB ' 時發生的問題」的錯誤訊息;無法完成要求。若此問題持續發生或未預期，請洽詢支援人員以取得此資訊。*」 在檢視等候統計資料時，請使用較短的時間週期。

等候統計資料可讓您檢視執行特定查詢期間所發生的等候事件。 如需有關等候事件類型的詳細資訊，請參閱 [MySQL 引擎文件](https://go.microsoft.com/fwlink/?linkid=2098206)。

選取 [等候統計資料] 索引標籤，以檢視伺服器中等候的對應視覺效果。

[等候統計資料] 檢視中顯示的查詢會根據指定時間間隔內呈現最大等候資料的查詢來分組。

![查詢效能深入解析等候統計資料](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>後續步驟

- 深入瞭解適用於 MariaDB 的 Azure 資料庫中的[監視和微調](concepts-monitoring.md)。