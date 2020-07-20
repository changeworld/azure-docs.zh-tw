---
title: 啟用自動微調
description: 您可以使用 Azure 入口網站輕鬆地在您的資料庫上啟用自動調整。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: 6ffc81f7fc5cf36ff4e9bada8f72cfef013afcbc
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982772"
---
# <a name="enable-automatic-tuning-in-the-azure-portal-to-monitor-queries-and-improve-workload-performance"></a>在 Azure 入口網站中啟用自動調整，以監視查詢並改善工作負載效能
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Azure SQL Database 會自動管理持續監視查詢的資料服務，並識別您可以執行以改善工作負載效能的動作。 您可以檢閱建議並加以手動套用，或讓 Azure SQL Database 自動套用矯正措施 - 這稱為**模式**。

可以在伺服器或資料庫層級啟用自動調整，透過：

- [Azure 入口網站](automatic-tuning-enable.md#azure-portal)
- [REST API](automatic-tuning-enable.md#rest-api)呼叫
- [T-sql](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)命令

> [!NOTE]
> 針對 Azure SQL 受控執行個體，僅能透過[t-sql](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management)設定支援的選項 FORCE_LAST_GOOD_PLAN。 本文所述的以 Azure 入口網站為基礎的設定和自動索引微調選項不適用於 Azure SQL 受控執行個體。

> [!NOTE]
> 目前不支援透過 ARM （Azure Resource Manager）範本設定自動調整選項。

## <a name="enable-automatic-tuning-on-server"></a>在伺服器上啟用自動調整

在伺服器層級上，您可以選擇繼承「Azure 預設值」的自動調整設定，或不繼承設定。 Azure 預設值為 [已啟用 FORCE_LAST_GOOD_PLAN]、[CREATE_INDEX 已停用] 和 [DROP_INDEX] 已停用。

> [!IMPORTANT]
> 從2020年3月起，自動調整的新 Azure 預設值如下：
>
> - FORCE_LAST_GOOD_PLAN = 已啟用，CREATE_INDEX = 已停用，且 DROP_INDEX = 已停用。
> - 未設定自動調整喜好設定的現有伺服器會自動設定為繼承 Azure 預設值。 這適用于目前有未定義狀態自動調整之伺服器設定的所有客戶。
> - 新建立的伺服器會自動設定為繼承 Azure 預設值（不同于先前在建立新伺服器時，自動調整設定處於未定義狀態時）。

### <a name="azure-portal"></a>Azure 入口網站

若要在 Azure SQL Database 的[伺服器](logical-servers.md)上啟用自動調整，請流覽至 Azure 入口網站中的伺服器，然後選取功能表中的 [**自動調整**]。

![伺服器](./media/automatic-tuning-enable/server.png)

> [!NOTE]
> 請注意，目前的**DROP_INDEX**選項與使用分割區切換和索引提示的應用程式不相容，因此不應該在這些情況下啟用。 高階和商務關鍵服務層級不支援卸載未使用的索引。

選取您想要啟用的自動調整選項，然後選取 [套用 **]。**

伺服器上的自動調整選項會套用到此伺服器上的所有資料庫。 根據預設，所有資料庫會都繼承其父伺服器的組態，但這可加以覆寫並針對每個資料庫個別加以指定。

### <a name="rest-api"></a>REST API

若要瞭解如何使用 REST API 在**伺服器**上啟用自動調整，請參閱[伺服器自動調整更新和取得 HTTP 方法](/rest/api/sql/serverautomatictuning)。

## <a name="enable-automatic-tuning-on-an-individual-database"></a>在個別的資料庫上啟用自動調整

Azure SQL Database 可讓您個別指定每個資料庫的自動調整設定。 在資料庫層級上，您可以選擇繼承父伺服器「Azure 預設值」的自動調整設定，或不繼承設定。 Azure 預設值設為 [已啟用 FORCE_LAST_GOOD_PLAN]、[CREATE_INDEX 已停用] 和 [DROP_INDEX] 已停用。

> [!TIP]
> 一般建議是在**伺服器層級**管理自動調整設定，如此一來，就可以在每個資料庫上自動套用相同的設定。 只有在您要求該資料庫的設定不同於繼承自相同伺服器的其他設定時，設定自動調整個別的資料庫。

### <a name="azure-portal"></a>Azure 入口網站

若要在**單一資料庫**上啟用自動調整，請流覽至 Azure 入口網站中的資料庫，然後選取 [**自動調整**]。

您可以針對每個資料庫分開設定個別的自動調整設定。 您可以手動設定個別的自動調整選項，或指定選項從伺服器繼承其設定。

![資料庫](./media/automatic-tuning-enable/database.png)

請注意，此時 [DROP_INDEX] 選項與使用分割區切換和索引提示的應用程式不相容，而且不應該在這些情況下啟用。

選取所需的組態後，按一下 [套用]****。

### <a name="rest-api"></a>Rest API

若要深入瞭解如何使用 REST API 在單一資料庫上啟用自動調整，請參閱[Azure SQL Database 自動調整更新和取得 HTTP 方法](/rest/api/sql/databaseautomatictuning)。

### <a name="t-sql"></a>T-SQL

若要透過 T-SQL 在單一資料庫上啟用自動調整，請連線到該資料庫並執行下列查詢：

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

將自動調整設定成 [AUTO]，將會套用 Azure 預設值。 設定成 [INHERIT]，將會從父伺服器繼承自動調整設定。 若選擇 [CUSTOM]，您將需要手動設定自動調整。

若要透過 T-SQL 設定個別的自動調整選項，請連線到資料庫並執行如下的查詢：

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = ON, DROP_INDEX = OFF)
```

將個別的微調選項設定為 ON，將會覆寫資料庫所繼承的任何設定，並啟用微調選項。 將它設定為 OFF 也會覆寫資料庫繼承的任何設定，並停用微調選項。 已指定預設值的自動調整選項會從伺服器層級設定繼承自動調整設定。  

> [!IMPORTANT]
> 在[主動式異地](auto-failover-group-overview.md)複寫的情況下，只需要在主資料庫上設定自動調整。 自動套用的微調動作（例如索引建立或刪除）將會自動複寫到唯讀次要資料庫。 嘗試在唯讀次要複本上透過 T-SQL 啟用自動調整會導致失敗，因為不支援在唯讀次要複本上使用不同的調整設定。
>

若要找出更連續的 T-sql 選項來設定自動調整，請參閱[ALTER DATABASE SET 選項（transact-sql）](/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)。

## <a name="disabled-by-the-system"></a>被系統停用

自動調整會監視它在資料庫上採取的所有動作，而且在某些情況下，它可以判斷自動調整無法適當地在資料庫上運作。 在此情況下，系統將會停用微調選項。 在大部分情況下，發生此問題的原因是因為特定資料庫上未啟用查詢資料存放區，或是查詢存放區處於唯讀的狀態。

## <a name="permissions"></a>權限

因為自動調整是 Azure 功能，所以若要使用它，您必須使用 Azure 的內建 RBAC 角色。 僅使用 SQL 驗證並不會足以使用 Azure 入口網站的功能。

若要使用自動調整，授與使用者的最低必要許可權是 Azure 的內建[SQL Database 參與者](../../role-based-access-control/built-in-roles.md#sql-db-contributor)角色。 您也可以考慮使用較高的許可權角色，例如 SQL Server 參與者、SQL 受控執行個體參與者、參與者和擁有者。

## <a name="configure-automatic-tuning-e-mail-notifications"></a>設定自動調整電子郵件通知

請參閱[自動調整電子郵件通知](automatic-tuning-email-notifications-configure.md)指南。

## <a name="next-steps"></a>後續步驟

- 閱讀[自動調整文章](automatic-tuning-overview.md)，進一步了解自動調整，以及它如何協助您改善效能。
- 如需 Azure SQL Database 效能建議的概觀，請參閱[效能建議](database-advisor-implement-performance-recommendations.md)。
- 請參閱[查詢效能深入解析](query-performance-insight-use.md)，以了解如何檢視排名最前面查詢的效能影響。
