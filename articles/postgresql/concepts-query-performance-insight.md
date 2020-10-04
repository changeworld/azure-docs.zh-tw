---
title: 查詢效能深入解析-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫單一伺服器中的查詢效能深入解析功能。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: babf2c8208732a194184549dfa5ed3228b376d0f
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710255"
---
# <a name="query-performance-insight"></a>查詢效能深入解析 

**適用于：** 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器版本9.6、10、11

查詢效能深入解析可協助您快速找出執行時間最長的查詢、一段時間後的變化情形，以及受到哪些等候的影響。

## <a name="permissions"></a>權限
需要**擁有者**或**參與者**權限，才能檢視查詢效能深入解析中的查詢文字。 **讀者**可以檢視圖表與資料表，但無法檢視查詢文字。

## <a name="prerequisites"></a>Prerequisites
資料必須存在於[查詢存放區](concepts-query-store.md)中，查詢效能深入解析才能運作。

## <a name="viewing-performance-insights"></a>檢視效能深入解析
Azure 入口網站中的[查詢效能深入解析](concepts-query-performance-insight.md)檢視會以視覺效果呈現來自查詢存放區的重要資訊。 

在適用於 PostgreSQL 的 Azure 資料庫伺服器的 [入口網站] 頁面中，選取功能表列的 [**智慧型效能**] 區段底下的 [**查詢效能深入**解析]。

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="查詢效能深入解析長時間執行的查詢":::

[ **長時間執行查詢** ] 索引標籤會依每次執行的平均持續時間顯示前五個查詢，並以15分鐘的間隔匯總。 您可以從 [ **查詢數** ] 下拉式清單中選取，以查看更多查詢。 當您這樣做時，特定查詢識別碼的圖表色彩可能會有所變更。

您可以在圖表中按一下並拖曳來縮小到特定時間範圍。 或者，使用放大和縮小圖示，分別檢視一段較短或較長的時間。

圖表下方的資料表會提供該時間範圍內長時間執行之查詢的詳細資訊。

選取 [等候統計資料] 索引標籤，以檢視伺服器中等候的對應視覺效果。

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="查詢效能深入解析長時間執行的查詢":::

## <a name="considerations"></a>考量
* [讀取複本](concepts-read-replicas.md)無法使用查詢效能深入解析。

## <a name="next-steps"></a>後續步驟
- 深入了解在適用於 PostgreSQL 的 Azure 資料庫中進行[監視和微調](concepts-monitoring.md)。


