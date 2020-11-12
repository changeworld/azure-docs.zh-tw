---
title: 效能建議-適用於 MariaDB 的 Azure 資料庫
description: 本文說明適用於 MariaDB 的 Azure 資料庫中的效能建議功能
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: a20b8a43c8955e1afea7a7157e3e73425fb0d806
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540462"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的效能建議

**適用于：** 適用於 MariaDB 的 Azure 資料庫10。2

效能建議功能會分析您的資料庫來建立自訂建議，進而改善效能。 為了產生建議，分析會查看各種資料庫特性，包括結構描述。 請在您的伺服器上啟用[查詢存放區](concepts-query-store.md)，以充分利用效能建議功能。 如果效能結構描述為關閉狀態，則開啟查詢存放區時將會啟用 performance_schema 和此功能所需的效能結構描述檢測子集。 實作任何效能建議後，您應測試效能，以評估這些變更的影響。

## <a name="permissions"></a>權限

需要 **擁有者** 或 **參與者** 權限，才能使用 [效能建議] 功能執行分析。

## <a name="performance-recommendations"></a>效能建議

[效能建議](concepts-performance-recommendations.md)功能可分析整部伺服器的工作負載，來找出可能可以改善效能的索引。

在適用于 mariadb 伺服器的 [Azure 入口網站] 頁面上，從功能表列的 [ **智慧效能** ] 區段開啟 **效能建議** 。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="[效能建議] 登陸頁面":::

選取 [分析]，並選擇一個將開始進行分析的資料庫。 根據您的工作負載，分析可能需要幾分鐘才能完成。 分析完成後，入口網站中會有通知。 分析會執行資料庫的深層檢查。 我們建議您在離峰期間執行分析。

[建議] 視窗將會顯示一份建議清單 (如果有任何發現的話)，以及產生這項建議的相關查詢識別碼。 透過查詢識別碼，您可以使用 [mysql.query_store](concepts-query-store.md#mysqlquery_store) 檢視來深入了解查詢。

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="效能建議新頁面":::

建議不會自動套用。 若要套用建議，請複製查詢文字並從您選擇的用戶端中執行該文字。 請記得進行測試和監視來評估建議。

## <a name="recommendation-types"></a>建議類型

### <a name="index-recommendations"></a>索引建議

「建立索引」建議會建議新的索引，以加速工作負載中最常執行或最耗時的查詢。 此建議類型需要啟用[查詢存放區](concepts-query-store.md)。 查詢存放區會收集查詢資訊，並提供詳細的查詢執行階段和頻率統計資料，讓分析可用來提出建議。

### <a name="query-recommendations"></a>查詢建議

查詢建議會針對工作負載中的查詢建議優化和重寫。 藉由識別適用于 mariadb 查詢反模式並以語法修正它們，可改善耗時查詢的效能。 此建議類型需要啟用查詢存放區。 查詢存放區會收集查詢資訊，並提供詳細的查詢執行階段和頻率統計資料，讓分析可用來提出建議。
## <a name="next-steps"></a>後續步驟

- 深入瞭解適用於 MariaDB 的 Azure 資料庫中的 [監視和微調](concepts-monitoring.md) 。