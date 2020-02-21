---
title: 自動微調概觀
description: Azure SQL Database 會分析 SQL 查詢，並自動針對使用者的工作負載調整。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/06/2019
ms.openlocfilehash: eefaaa59a3dc0f0900666bc697f64e4f405a0d74
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498714"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database 中的自動調整

Azure SQL Database 自動調整能透過以 AI 和機器學習為基礎的持續效能調整，來提供尖峰的效能與穩定的工作負載。

自動調整是完全受控的智慧效能服務，它能使用內建的智慧機制來持續監視在資料庫上執行的查詢，並且自動改善查詢的效能。 此功能是透過讓資料庫針對變動的工作負載進行動態調適，並套用調整建議來達成。 自動調整能透過 AI 從 Azure 上的所有資料庫進行水平學習，並能動態地改善其調整動作。 Azure SQL Database 在開啟自動調整的情況下執行得愈久，自動調整的效果便愈好。

Azure SQL Database 自動調整是您可以啟用以提供穩定且執行良好資料庫工作負載的其中一個最重要功能。

## <a name="what-can-automatic-tuning-do-for-you"></a>自動調整可以為您做什麼？

- Azure SQL 資料庫的自動化效能調整
- 自動驗證效能提升
- 自動復原與自我修正
- 調整歷程記錄
- 適用於手動部署的調整動作 T-SQL 指令碼
- 主動式工作負載效能監視
- 以數十萬個資料庫向外延展的功能
- 為 DevOps 資源及擁有權總成本帶來正面影響

## <a name="safe-reliable-and-proven"></a>安全、可靠且經過實證

套用到 Azure SQL 資料庫的調整作業，對於您最密集之工作負載的效能是完全安全的。 系統已經精心設計成不會干擾使用者工作負載。 自動調整建議只會在低使用率的時段套用。 系統也可以暫時地停用自動調整作業，以保護工作負載效能。 在這種情況下，「被系統停用」的訊息會顯示在 Azure 入口網站中。 自動調整會將工作負載考慮為具有最高資源優先順序。

自動調整是成熟的機制，且已在數百萬個於 Azure 上執行的資料庫上達到完美。 已套用的自動調整作業都會進行自動驗證，以確保對工作負載效能有正面的改進。 系統會動態地偵測迴歸的效能建議，並迅速地做出還原。 透過已記錄的調整歷程記錄，使用者可清楚查看對每個 Azure SQL Database 所做出的調整改進。 

![自動調整的運作方式為何](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database 自動調整與 SQL Server 自動調整引擎共用其核心邏輯。 如需內建智慧機制的其他技術資訊，請參閱 [SQL Server 自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(英文\)。

## <a name="use-automatic-tuning"></a>使用自動調整

必須在您的訂用帳戶上啟用自動調整。 若要使用 Azure 入口網站來啟用自動調整，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。

自動調整可透過自動套用調整建議 (包括自動驗證效能提升) 來自主地運作。 

如需更充分的控制，可以關閉自動套用調整建議的功能，並透過 Azure 入口網站手動套用調整建議。 您也可以僅使用此解決方案來檢視自動調整建議，並透過偏好的指令碼和工具來手動套用那些調整建議。 

如需自動調整運作方式的概觀與典型的使用案例，請觀賞內嵌影片：


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>自動調整選項

Azure SQL Database 中可用的自動調整選項有：

| 自動調整選項 | 單一資料庫和集區資料庫支援 | 實例資料庫支援 |
| :----------------------------- | ----- | ----- |
| **建立索引**-識別可改善工作負載效能、建立索引，並自動驗證查詢效能已改善的索引。 | 是 | 否 | 
| **DROP INDEX** -每日識別重複和重複的索引（唯一索引除外），以及長時間未使用的索引（> 90 天）。 請注意，此選項與使用分割區切換和索引提示的應用程式不相容。 高階和商務關鍵服務層級不支援卸載未使用的索引。 | 是 | 否 |
| **強制執行最後一個良好的計畫**（自動計畫更正）-使用比上一個良好計畫更慢的執行計畫來識別 SQL 查詢，並使用最後一個已知的良好計畫，而不是回歸計畫來查詢。 | 是 | 是 |

自動調整可識別 **CREATE INDEX**、**DROP INDEX** 和 **FORCE LAST GOOD PLAN** 建議，可以最佳化您的資料庫效能，並在 [Azure 入口網站](sql-database-advisor-portal.md)中顯示它們，還可以透過 [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) 和 [REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning) 來公開它們。 若要深入瞭解如何強制執行最後一個良好的計畫，以及透過 T-sql 設定自動調整選項，請參閱[自動調整會引進自動計畫更正](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)。

您可以使用入口網站來手動套用調整建議，或是讓自動調整為您自動套用調整建議。 讓系統為您自動套用調整建議的好處，就是系統會自動驗證工作負載效能是否有正面的改善，或者如果未偵測到明顯的效能改善，系統會自動還原調整建議。 請注意，針對受到沒有經常執行之調整建議所影響的查詢，其驗證階段根據設計可能需要最多 72 小時才能完成。

如果您要透過 T-sql 套用微調建議，則無法使用自動效能驗證和反轉機制。 以這種方式套用的建議將維持作用中狀態，並顯示于24-48 小時的微調建議清單中。 系統自動將其收回之前。 如果您想要更快移除建議，可以將它從 Azure 入口網站中捨棄。

自動微調選項可以針對每個資料庫個別地啟用或停用，或可以在 SQL Database 伺服器上設定，並在從伺服器繼承設定的每個資料庫上套用。 SQL Database 伺服器可以繼承 Azure 的自動調整設定預設值。 Azure 預設值此時會設為已啟用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，且已停用 DROP_INDEX。

> [!IMPORTANT]
> 從3月起2020，自動調整的 Azure 預設值變更將會生效，如下所示：
> - 新的 Azure 預設值將會 FORCE_LAST_GOOD_PLAN = 已啟用，CREATE_INDEX = 已停用，且 DROP_INDEX = 已停用。
> - 未設定自動調整喜好設定的現有伺服器會自動設定為繼承新的 Azure 預設值。 這適用于目前有未定義狀態自動調整之伺服器設定的所有客戶。
> - 新建立的伺服器會自動設定為繼承新的 Azure 預設值（不同于先前在建立新伺服器時，自動調整設定處於未定義狀態時）。
>

在伺服器上設定自動調整選項，並繼承屬於父代伺服器的資料庫設定，是設定自動調整的建議方法，因為這可簡化大量資料庫的自動調整選項管理。

## <a name="next-steps"></a>後續步驟

- 若要在 Azure SQL Database 中啟用自動調整以管理您的工作負載，請參閱[啟用自動調整](sql-database-automatic-tuning-enable.md)。
- 若要手動檢閱及套用自動調整建議，請參閱[尋找及套用效能建議](sql-database-advisor-portal.md)。
- 若要了解如何使用 T-SQL 來套用和檢視自動調整建議，請參閱[透過 T-SQL 管理自動調整](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)。
- 若要了解如何對自動調整建議建置電子郵件通知，請參閱[自動調整的電子郵件通知](sql-database-automatic-tuning-email-notifications.md)。
- 若要了解自動調整中使用的內建智慧機制，請參閱[透過人工智慧調整 Azure SQL 資料庫](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/) \(英文\)。
- 若要了解自動調整在 Azure SQL Database 和 SQL Server 2017 中運作的方式，請參閱 [SQL Server 自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)。
