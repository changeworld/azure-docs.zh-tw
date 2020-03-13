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
ms.date: 03/10/2020
ms.openlocfilehash: d4762c86268353ff0464ff3919250cd86f0038d4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214110"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database 中的自動調整

Azure SQL Database 自動調整會透過以 AI 和機器學習為基礎的持續效能微調，提供尖峰效能和穩定的工作負載。

自動調整是完全受控的智慧效能服務，它能使用內建的智慧機制來持續監視在資料庫上執行的查詢，並且自動改善查詢的效能。 此功能是透過讓資料庫針對變動的工作負載進行動態調適，並套用調整建議來達成。 自動調整能透過 AI 從 Azure 上的所有資料庫進行水平學習，並能動態地改善其調整動作。 隨著自動調整，資料庫執行的時間越長，就越好。

Azure SQL Database 自動調整可能是您可以啟用以提供穩定和尖峰執行資料庫工作負載的其中一項最重要的功能。

## <a name="what-can-automatic-tuning-do-for-you"></a>自動調整可為您做什麼

- Azure SQL 資料庫的自動化效能調整
- 自動驗證效能提升
- 自動復原與自我修正
- 調整歷程記錄
- 適用於手動部署的調整動作 T-SQL 指令碼
- 主動式工作負載效能監視
- 以數十萬個資料庫向外延展的功能
- 為 DevOps 資源及擁有權總成本帶來正面影響

## <a name="safe-reliable-and-proven"></a>安全、可靠且經過實證

在 Azure SQL Database 中套用至資料庫的微調作業，對於您最密集之工作負載的效能是完全安全的。 系統已經精心設計成不會干擾使用者工作負載。 自動調整建議只會在低使用率的時段套用。 系統也可以暫時地停用自動調整作業，以保護工作負載效能。 在這種情況下，[由系統停用] 訊息會顯示在 Azure 入口網站中。 自動調整會將工作負載考慮為具有最高資源優先順序。

自動調整是成熟的機制，且已在數百萬個於 Azure 上執行的資料庫上達到完美。 已套用的自動調整作業都會進行自動驗證，以確保對工作負載效能有正面的改進。 系統會動態地偵測迴歸的效能建議，並迅速地做出還原。 透過已記錄的調整歷程記錄，使用者可清楚查看對每個 Azure SQL Database 所做出的調整改進。

![自動調整的運作方式為何](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database 自動調整會與 SQL Server 自動調整引擎共用其核心邏輯。 如需內建智慧機制的其他技術資訊，請參閱 [SQL Server 自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(英文\)。

如需自動調整運作方式的概觀與典型的使用案例，請觀賞內嵌影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>啟用自動微調

您可以[針對 Azure 入口網站中的單一和集區資料庫啟用自動調整](sql-database-automatic-tuning-enable.md)，或使用[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) t-sql 語句。 您可以使用[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) t-sql 語句，在受控實例部署中啟用實例資料庫的自動調整。

## <a name="automatic-tuning-options"></a>自動調整選項

Azure SQL Database 中可用的自動調整選項有：

| 自動調整選項 | 單一資料庫和集區資料庫支援 | 實例資料庫支援 |
| :----------------------------- | ----- | ----- |
| **建立索引**-識別可改善工作負載效能、建立索引，並自動驗證查詢效能已改善的索引。 | 是 | 否 |
| **DROP INDEX** -每日識別重複和重複的索引（唯一索引除外），以及長時間未使用的索引（> 90 天）。 請注意，此選項與使用分割區切換和索引提示的應用程式不相容。 高階和商務關鍵服務層級不支援卸載未使用的索引。 | 是 | 否 |
| **強制執行最後一個良好的計畫**（自動計畫更正）-使用比上一個良好計畫更慢的執行計畫來識別 SQL 查詢，並使用最後一個已知的良好計畫，而不是回歸計畫來查詢。 | 是 | 是 |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>單一和集區資料庫的自動調整

單一和集區資料庫的自動調整會使用**建立索引**、 **DROP INDEX**和**強制執行最後一個良好的計畫**資料庫建議程式，以將您的資料庫效能優化。 如需詳細資訊，請參閱 Azure 入口網站、 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)和[REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)中[的 Database advisor 建議](sql-database-advisor-portal.md)。

您可以使用 Azure 入口網站手動套用微調建議，也可以讓自動調整為您自行套用微調建議。 讓系統為您自動套用調整建議的好處，就是系統會自動驗證工作負載效能是否有正面的改善，或者如果未偵測到明顯的效能改善，系統會自動還原調整建議。 請注意，針對受到沒有經常執行之調整建議所影響的查詢，其驗證階段根據設計可能需要最多 72 小時才能完成。

如果您要透過 T-sql 套用微調建議，則無法使用自動效能驗證和反轉機制。 以這種方式套用的建議將維持作用中狀態，並顯示于24-48 小時的微調建議清單中。 系統自動將其收回之前。 如果您想要更快移除建議，可以將它從 Azure 入口網站中捨棄。

自動微調選項可以針對每個資料庫個別地啟用或停用，或可以在 SQL Database 伺服器上設定，並在從伺服器繼承設定的每個資料庫上套用。 SQL Database 伺服器可以繼承自動調整設定的 Azure 預設值。 Azure 預設值此時會設為已啟用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，且已停用 DROP_INDEX。

> [!IMPORTANT]
> 從3月起2020，自動調整的 Azure 預設值變更將會生效，如下所示：
>
> - 新的 Azure 預設值將會 FORCE_LAST_GOOD_PLAN = 已啟用，CREATE_INDEX = 已停用，且 DROP_INDEX = 已停用。
> - 未設定自動調整喜好設定的現有伺服器會自動設定為繼承新的 Azure 預設值。 這適用于目前有未定義狀態自動調整之伺服器設定的所有客戶。
> - 新建立的伺服器會自動設定為繼承新的 Azure 預設值（不同于先前在建立新伺服器時，自動調整設定處於未定義狀態時）。

在伺服器上設定自動調整選項，並繼承屬於父系伺服器之資料庫的設定，是設定自動調整的建議方法，因為它可簡化大量資料庫的自動調整選項管理。

若要瞭解如何建立自動調整建議的電子郵件通知，請參閱[自動調整的電子郵件通知](sql-database-automatic-tuning-email-notifications.md)。

### <a name="automatic-tuning-for-instance-databases"></a>實例資料庫的自動調整

針對受控實例部署中的實例資料庫自動調整，只支援**強制執行最後一個良好的計畫**。 如需透過 T-sql 設定自動調整選項的詳細資訊，請參閱[自動調整會引進自動計畫更正](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)和[自動計畫更正](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)。

## <a name="next-steps"></a>後續步驟

- 若要瞭解自動調整中使用的內建智慧功能，請參閱[人工智慧微調 AZURE SQL 資料庫](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)。
