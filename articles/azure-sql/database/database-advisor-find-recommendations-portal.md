---
title: 套用效能建議
description: 使用 Azure 入口網站來尋找可優化資料庫效能的效能建議。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: jrasnik, sstein
ms.date: 12/19/2018
ms.openlocfilehash: 0b7aab13871f1450a3c6907b30b446869b2fefa7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91443886"
---
# <a name="find-and-apply-performance-recommendations"></a>尋找和套用效能建議
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

您可以使用 Azure 入口網站來尋找效能建議，讓您可以在 Azure SQL Database 中優化資料庫的效能，或更正工作負載中所識別的某些問題。 Azure 入口網站中的 [ **效能建議** ] 頁面可讓您根據潛在的影響來尋找最佳建議。

## <a name="viewing-recommendations"></a>檢視建議

若要查看並套用效能建議，您需要在 Azure 中 [ (AZURE RBAC) 許可權的正確 azure 角色型存取控制 ](../../role-based-access-control/overview.md) 。 需要**讀取者**、**SQL DB 參與者**權限，才能檢視建議，以及需要**擁有者**、**SQL DB 參與者**權限，才能執行任何動作；建立或卸除索引並取消建立索引。

使用下列步驟，在 Azure 入口網站上尋找效能建議：

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 移至 [**所有服務**  >  **SQL 資料庫**]，然後選取您的資料庫。
3. 瀏覽至 [效能建議]**** 來檢視適用於所選資料庫的可用建議。

資料表中顯示的效能建議類似於下圖：

![螢幕擷取畫面顯示資料表中具有動作和建議描述的效能建議。](./media/database-advisor-find-recommendations-portal/recommendations.png)

依照可能帶來的效能影響排序，建議分成下列類別：

| 影響 | 描述 |
|:--- |:--- |
| 高 |高影響建議提供最明顯的效能影響。 |
| 中 |中度影響建議會改善效能，但不顯著。 |
| 低 |低影響建議比沒有建議時提供更好的效能，但改善可能不顯著。 |

> [!NOTE]
> Azure SQL Database 必須至少監視活動一整天，才能找出一些建議。 相較於隨機蹦出的零星活動，一致的查詢模式更有利於 Azure SQL Database 最佳化。 如果 [效能建議] **** 頁面中目前沒有可用的建議，該頁面會提供訊息說明原因。

您也可以檢視歷程記錄作業的狀態。 選取建議或狀態以查看詳細資訊。

以下是 Azure 入口網站中的「建立索引」建議的範例。

![建立索引](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>套用建議

Azure SQL Database 可讓您使用下列 3 個選項的其中任一選項來控制建議的啟用方式：

* 一次套用一個個別的建議。
* 啟用自動調整功能，以自動套用建議。
* 若要手動實作建議，請針對您的資料庫執行建議的 T-SQL 指令碼。

選取任何建議來檢視其詳細資料，然後按一下 [檢視指令碼] **** 來檢閱將如何建立建議的確切詳細資料。

套用建議時，資料庫仍保持連線 -- 使用效能建議或自動調整功能不會使資料庫離線。

### <a name="apply-an-individual-recommendation"></a>套用個別的建議

您可以一次檢閱並接受一個建議。

1. 選取 [建議]**** 頁面上的某個建議。
2. 在 [**詳細資料**] 頁面上，按一下 [套用 **] 按鈕。**

   ![套用建議](./media/database-advisor-find-recommendations-portal/apply.png)

針對資料庫套用選取的建議。

### <a name="removing-recommendations-from-the-list"></a>從清單中移除建議

如果建議的清單中包含您想從清單中移除的項目，您可以捨棄該建議：

1. 在 [建議] **** 清單中選取建議，以開啟詳細資料。
2. 在 [詳細資料]**** 頁面上按一下 [捨棄]****。

如有需要，您可以將捨棄的項目加回到 **建議** 清單：

1. 在 [建議]**** 頁面上按一下 [檢視已捨棄]****。
2. 從清單中選取捨棄的項目以檢視其詳細資料。
3. (選擇性) 按一下 [復原捨棄]****，將索引加回到**建議**的主要清單。

> [!NOTE]
> 請注意，如果啟用 SQL Database [自動微調](automatic-tuning-overview.md)，且您以手動方式捨棄清單中的建議，就永遠不會自動套用這類建議。 捨棄建議是一個便利的方式，可在要求不得套用特定建議時，讓使用者可以啟用自動調整。
> 您可以選取 [復原捨棄] 選項，將捨棄的建議新增回 [建議] 清單，從而還原這個行為。

### <a name="enable-automatic-tuning"></a>啟用自動微調

您可以將資料庫設定為自動執行建議。 當建議可供使用時會自動套用建議。 因為所有建議都由服務管理，所以若對效能產生負面影響，就會還原該建議。

1. 在 [建議]**** 頁面上按一下 [自動化]****：

   ![建議程式設定](./media/database-advisor-find-recommendations-portal/settings.png)
2. 選取要自動執行的動作：

   ![螢幕擷取畫面，顯示要在哪裡選取要自動執行的動作。](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> 請注意，**DROP_INDEX** 選項目前與使用分割區切換和索引提示的應用程式並不相容。

選取所需的組態後，按一下 [套用]。

### <a name="manually-apply-recommendations-through-t-sql"></a>透過 T-sql 手動套用建議

選取任何建議，然後按一下 [檢視指令碼] ****。 對資料庫執行這個指令碼，以手動套用建議。

** ，因此建議您在建立這些索引之後監視索引，以確認它們能夠提高效能，且於必要時調整或刪除它們。 如需有關建立索引的詳細資訊，請參閱 [CREATE INDEX (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)。 此外，手動套用的建議將維持使用中狀態，並顯示于24-48 小時的建議清單中。 系統自動將它們收回之前。 如果您想要更快移除建議，可以手動將它捨棄。

### <a name="canceling-recommendations"></a>取消建議

可以取消處於**擱置中**、**驗證中**或**成功**狀態的建議。 狀態為 **執行中** 的建議無法取消。

1. 在 [調整歷程記錄]**** 區域中選取建議，以開啟 [建議詳細資料]**** 頁面。
2. 按一下 [取消] **** 以中止套用建議的程序。

## <a name="monitoring-operations"></a>監視作業

套用建議時可能不會立即執行。 入口網站會提供有關建議狀態的詳細資料。 索引有下列可能的狀態：

| 狀態 | 描述 |
|:--- |:--- |
| Pending |已收到套用建議命令，且已排程執行。 |
| 執行中 |正在套用建議。 |
| Validating |成功套用建議，而服務正在衡量益處。 |
| 成功 |已成功套用建議，並證實有益處。 |
| 錯誤 |套用建議程序期間發生錯誤。 這可能是暫時性問題，也可能是資料表的結構描述變更，造成指令碼不再有效。 |
| 還原 |已套用建立但被認為無助於效能，正在自動還原。 |
| 已還原 |已還原建議。 |

按一下清單中正在處理的建議以查看其詳細資訊：

![顯示同進程建議清單的螢幕擷取畫面。](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>還原建議

如果您使用效能建議來套用建議 (表示您未手動執行 T-SQL 指令碼)，如果建議程式發現會對效能造成負面影響，它將會自動還原變更。 如果您因為任何原因想要還原建議，您可以執行以下步驟：

1. 在 [調整歷程記錄] **** 區域中選取已成功套用的建議。
2. 在 [建議詳細資料]**** 頁面上按一下 [還原]****。

![建議的索引](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>監視索引建議的效能影響

成功實作建議之後 (目前僅提供索引作業和參數化查詢建議)，您可以按一下 [建議詳細資料] 頁面上的 **查詢深入解析** 來開啟 [查詢效能深入解析](query-performance-insight-use.md)，並查看排名最前面查詢對效能的影響。

![監視效能影響](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>摘要

Azure SQL Database 提供改善資料庫效能的建議。 藉由提供 T-SQL 指令碼，您會獲得最佳化資料庫的協助，並最終改善查詢效能。

## <a name="next-steps"></a>後續步驟

監視建議，並繼續套用建議以改善效能。 資料庫工作負載會動態地持續變更。 Azure SQL Database 會繼續監視並提供可能改善資料庫效能的建議。

* 若要深入了解 Azure SQL Database 中的自動調整功能，請參閱[自動調整](automatic-tuning-overview.md)。
* 如需 Azure SQL Database 效能建議的概觀，請參閱[效能建議](database-advisor-implement-performance-recommendations.md)。
* 請參閱[查詢效能深入解析](query-performance-insight-use.md)，以了解如何檢視排名最前面查詢的效能影響。

## <a name="additional-resources"></a>其他資源

* [查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Azure 角色型存取控制 (Azure RBAC)](../../role-based-access-control/overview.md)
