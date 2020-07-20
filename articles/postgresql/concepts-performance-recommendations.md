---
title: 效能建議-適用於 PostgreSQL 的 Azure 資料庫-單一伺服器
description: 本文說明適用於 PostgreSQL 的 Azure 資料庫-單一伺服器中的效能建議功能。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74768464"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>適用於 PostgreSQL 的 Azure 資料庫中的效能建議-單一伺服器

**適用物件：** 適用於 PostgreSQL 的 Azure 資料庫-單一伺服器版本9.6、10、11

效能建議功能會分析您的資料庫，以建立自訂的建議以改善效能。 為了產生建議，分析會查看各種資料庫特性，包括結構描述。 請在您的伺服器上啟用[查詢存放區](concepts-query-store.md)，以充分利用效能建議功能。 實作任何效能建議後，您應測試效能，以評估這些變更的影響。 

## <a name="permissions"></a>權限
需要**擁有者**或**參與者**權限，才能使用 [效能建議] 功能執行分析。

## <a name="performance-recommendations"></a>效能建議
[效能建議](concepts-performance-recommendations.md)功能可分析整部伺服器的工作負載，來找出可能可以改善效能的索引。

在於 postgresql 伺服器的 [Azure 入口網站] 頁面上，從功能表列的 [**智慧效能**] 區段開啟 [**效能建議**]。

![[效能建議] 登陸頁面](./media/concepts-performance-recommendations/performance-recommendations-page.png)

選取 [分析]，並選擇一個將開始進行分析的資料庫。 視您的工作負載而定，第一次分析可能需要幾分鐘的時間才能完成。 分析完成後，入口網站中會有通知。 分析會執行資料庫的深層檢查。 我們建議您在離峰期間執行分析。 

[**建議**] 視窗將會顯示一份建議清單（如果找到的話）。

![效能建議新頁面](./media/concepts-performance-recommendations/performance-recommendations-result.png)

建議不會自動套用。 若要套用建議，請複製查詢文字並從您選擇的用戶端中執行該文字。 請記得進行測試和監視來評估建議。 

## <a name="recommendation-types"></a>建議類型

目前支援兩種類型的建議： *Create index*和*Drop index*。

### <a name="create-index-recommendations"></a>建立索引建議
「建立索引」建議會建議新的索引，以加速工作負載中最常執行或最耗時的查詢。 此建議類型需要啟用[查詢存放區](concepts-query-store.md)。 查詢存放區會收集查詢資訊，並提供詳細的查詢執行階段和頻率統計資料，讓分析可用來提出建議。

### <a name="drop-index-recommendations"></a>卸除索引建議
除了偵測遺漏的索引之外，適用於 PostgreSQL 的 Azure 資料庫還會分析現有索引的效能。 如果索引很少使用或重複，分析器會建議將它卸載。

## <a name="considerations"></a>考量
* [讀取複本](concepts-read-replicas.md)無法使用效能建議。
## <a name="next-steps"></a>後續步驟
- 深入了解在適用於 PostgreSQL 的 Azure 資料庫中進行[監視和微調](concepts-monitoring.md)。

