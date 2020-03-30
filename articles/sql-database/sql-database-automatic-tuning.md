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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214110"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database 中的自動調整

Azure SQL 資料庫自動調優通過基於 AI 和機器學習的持續性能調優提供峰值性能和穩定工作負載。

自動調整是完全受控的智慧效能服務，它能使用內建的智慧機制來持續監視在資料庫上執行的查詢，並且自動改善查詢的效能。 此功能是透過讓資料庫針對變動的工作負載進行動態調適，並套用調整建議來達成。 自動調整能透過 AI 從 Azure 上的所有資料庫進行水平學習，並能動態地改善其調整動作。 資料庫在自動調優下運行的時間越長，它的性能就越好。

Azure SQL 資料庫自動調優可能是您可以啟用的最重要功能之一，可用於提供穩定且性能最佳的資料庫工作負荷。

## <a name="what-can-automatic-tuning-do-for-you"></a>自動調優可以為您做什麼

- Azure SQL 資料庫的自動化效能調整
- 自動驗證效能提升
- 自動復原與自我修正
- 調整歷程記錄
- 適用於手動部署的調整動作 T-SQL 指令碼
- 主動式工作負載效能監視
- 以數十萬個資料庫向外延展的功能
- 為 DevOps 資源及擁有權總成本帶來正面影響

## <a name="safe-reliable-and-proven"></a>安全、可靠且經過實證

應用於 Azure SQL 資料庫中的資料庫的操作對於最緊張的工作負載的性能是完全安全的。 系統已經精心設計成不會干擾使用者工作負載。 自動調整建議只會在低使用率的時段套用。 系統也可以暫時地停用自動調整作業，以保護工作負載效能。 在這種情況下，"由系統禁用"消息將顯示在 Azure 門戶中。 自動調整會將工作負載考慮為具有最高資源優先順序。

自動調整是成熟的機制，且已在數百萬個於 Azure 上執行的資料庫上達到完美。 已套用的自動調整作業都會進行自動驗證，以確保對工作負載效能有正面的改進。 系統會動態地偵測迴歸的效能建議，並迅速地做出還原。 透過已記錄的調整歷程記錄，使用者可清楚查看對每個 Azure SQL Database 所做出的調整改進。

![自動調整的運作方式為何](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL 資料庫自動調優正在與 SQL Server 自動調優引擎共用其核心邏輯。 如需內建智慧機制的其他技術資訊，請參閱 [SQL Server 自動調整](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) \(英文\)。

如需自動調整運作方式的概觀與典型的使用案例，請觀賞內嵌影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-automatic-tuning/player]

## <a name="enable-automatic-tuning"></a>啟用自動微調

您可以在 Azure 門戶中或使用[ALTER 資料庫](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)T-SQL 語句[對單個資料庫和池資料庫啟用自動調優](sql-database-automatic-tuning-enable.md)。 使用[ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-mi-current) T-SQL 語句為託管實例部署中的實例資料庫啟用自動調優。

## <a name="automatic-tuning-options"></a>自動調整選項

Azure SQL Database 中可用的自動調整選項有：

| 自動調諧選項 | 單一資料庫和集區資料庫支援 | 實例資料庫支援 |
| :----------------------------- | ----- | ----- |
| **創建索引**- 標識可能提高工作負荷性能、創建索引並自動驗證查詢性能是否提高的索引。 | 是 | 否 |
| **DROP INDEX** - 每天標識冗余和重複索引，唯一索引除外，以及長時間未使用的索引（>90 天）。 請注意，此選項與使用分割區切換和索引提示的應用程式不相容。 高級服務和業務關鍵型服務層不支援刪除未使用的索引。 | 是 | 否 |
| **FORCE 最後好計畫**（自動計畫更正） - 使用執行計畫比上一個好計畫慢的 SQL 查詢，並使用最後一個已知良好的計畫而不是回歸計畫查詢來標識 SQL 查詢。 | 是 | 是 |

### <a name="automatic-tuning-for-single-and-pooled-databases"></a>單個資料庫和池資料庫的自動調優

單個資料庫和池資料庫的自動調優使用**CREATE INDEX、DROP** **INDEX**和 FORCE LAST GOOD**計畫資料庫**顧問建議來優化資料庫性能。 有關詳細資訊，請參閱 Azure 門戶[、PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction)和[REST API](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)中的[資料庫顧問建議](sql-database-advisor-portal.md)。

您可以使用 Azure 門戶手動應用調優建議，也可以允許自動調優自動為您應用調優建議。 讓系統為您自動套用調整建議的好處，就是系統會自動驗證工作負載效能是否有正面的改善，或者如果未偵測到明顯的效能改善，系統會自動還原調整建議。 請注意，針對受到沒有經常執行之調整建議所影響的查詢，其驗證階段根據設計可能需要最多 72 小時才能完成。

如果您通過 T-SQL 應用調優建議，則自動性能驗證和反轉機制不可用。 以這種方式應用的建議將保持活動狀態，並顯示在 24-48 小時的調優建議清單中。 在系統自動撤回它們之前。 如果要更快地刪除建議，可以從 Azure 門戶中丟棄它。

自動微調選項可以針對每個資料庫個別地啟用或停用，或可以在 SQL Database 伺服器上設定，並在從伺服器繼承設定的每個資料庫上套用。 SQL 資料庫伺服器可以繼承 Azure 預設值以進行自動調優設置。 Azure 預設值此時會設為已啟用 FORCE_LAST_GOOD_PLAN 和 CREATE_INDEX，且已停用 DROP_INDEX。

> [!IMPORTANT]
> 從 3 月起，2020 年對 Azure 預設值的自動調優更改將生效，如下所示：
>
> - 將FORCE_LAST_GOOD_PLAN啟用、禁用CREATE_INDEX和禁用DROP_INDEX \ 禁用新的 Azure 預設值。
> - 未配置自動調優首選項的現有伺服器將自動設定為繼承新的 Azure 預設值。 這適用于當前具有伺服器設置以在未定義狀態進行自動調優的所有客戶。
> - 創建的新伺服器將自動設定為 INHERIT 新的 Azure 預設值（與之前新伺服器創建時自動調優配置處於未定義狀態不同）。

在伺服器上配置自動調優選項和繼承屬於父伺服器的資料庫的設置是配置自動調優的推薦方法，因為它簡化了大量資料庫自動調優選項的管理。

要瞭解如何為自動調優建議構建電子郵件通知，請參閱[自動調優的電子郵件通知](sql-database-automatic-tuning-email-notifications.md)。

### <a name="automatic-tuning-for-instance-databases"></a>實例資料庫的自動調優

託管實例部署中實例資料庫的自動調優僅支援**FORCE LAST GOOD 計畫**。 有關通過 T-SQL 配置自動調優選項的詳細資訊，請參閱[自動調優引入了自動計畫更正](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management/)和[自動計畫更正](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning?view=sql-server-ver15#automatic-plan-correction)。

## <a name="next-steps"></a>後續步驟

- 要瞭解自動調優中使用的內置智慧，請參閱[人工智慧調調 Azure SQL 資料庫](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)。
