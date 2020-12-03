---
title: 動態資料遮罩
description: 動態資料遮罩會將機密資料遮罩給 Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 的非特殊許可權使用者，以限制敏感性資料的暴露程度
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 08/04/2020
tags: azure-synpase
ms.openlocfilehash: f8d352dac98f953f7f6d8033d0d9e1376c4da313
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532223"
---
# <a name="dynamic-data-masking"></a>動態資料遮罩 
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database、Azure SQL 受控執行個體和 Azure Synapse Analytics 支援動態資料遮罩。 動態資料遮罩會對不具權限的使用者遮罩機密資料，從而限制其曝光。 

動態資料遮罩可讓客戶在應用程式層級受到最小影響的情況下指定要顯示多少機密資料，而協助防止未經授權者存取機密資料。 這項原則式安全性功能會將機密資料隱藏在指定資料庫欄位的查詢結果集內，而資料庫中的資料則不會變更。

例如，客服中心服務代表可透過信用卡號碼的幾個數字來識別來電者，但這些資料項目不應完全公開給服務代表。 可以定義遮罩規則，以針對任何查詢的結果集中任何信用卡號碼的末四碼以外的所有數字進行遮罩處理。 另一個範例是可以定義適當的資料遮罩來保護個人資料，讓開發人員可以查詢生產環境以進行疑難排解，而不違反合規性法規。

## <a name="dynamic-data-masking-basics"></a>動態資料遮罩基本概念

您可以在 [SQL Database 設定] 窗格中，選取 [**安全性**] 底下的 **動態資料遮罩** 分頁，在 Azure 入口網站中設定動態資料遮罩原則。 這項功能無法使用適用于 SQL 受控執行個體的入口網站進行設定 (使用 PowerShell 或 REST API) 。 如需相關資訊，請參閱 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)。

### <a name="dynamic-data-masking-permissions"></a>動態資料遮罩權限

動態資料遮罩可由 Azure SQL Database 管理員、伺服器管理員或 [SQL 安全性管理員](../../role-based-access-control/built-in-roles.md#sql-security-manager)等角色來設定。

### <a name="dynamic-data-masking-policy"></a>動態資料遮罩原則

* **從遮罩排除的 sql 使用者** -一組 sql 使用者或 Azure AD 識別，可在 SQL 查詢結果中取得未遮罩的資料。 具有系統管理員權限的使用者永遠會從遮罩處理中排除，而且會看到沒有任何遮罩的原始資料。
* **遮罩規則** - 一組規則，定義會遮罩處理的指定欄位和所使用的遮罩函數。 指定的欄位可使用資料庫結構描述名稱、資料表名稱和資料行名稱來定義。
* **遮罩函數** - 一組方法，可控制不同案例的資料顯示性。

| 遮罩功能 | 遮罩邏輯 |
| --- | --- |
| **預設值** |**根據指定欄位的資料類型進行完整遮罩**<br/><br/>• 如果字串資料類型的欄位大小少於 4 個字元 (nchar、ntext、nvarchar)，請使用 XXXX 或更少 X。<br/>• 針對數值資料類型 (bigint、bit、decimal、int、money、numeric、smallint、smallmoney、tinyint、float、real)，使用零值。<br/>• 針對日期/時間資料類型 (date、datetime2、datetime、datetimeoffset、smalldatetime、time)，使用 01-01-1900 時間。<br/>• 對於 SQL 變數，會使用目前類型的預設值。<br/>• 對於 XML 會使用文件 \<masked/>。<br/>• 針對特殊資料類型 (時間戳記、資料表、hierarchyid、GUID、二進位值、影像、varbinary spatial 類型)，使用空值。 |
| **信用卡** |**遮罩方法會公開指定欄位的末四碼**，並新增常數字串做為信用卡格式的前置詞。<br/><br/>XXXX-XXXX-XXXX-1234 |
| **電子郵件** |**遮罩方法會公開第一個字母，並** 使用以電子郵件地址形式的常數位串前置詞，以 XXX.com 取代網域。<br/><br/>aXX@XXXX.com |
| **亂數字** |**遮罩方法會產生一個隨機數字**，其根據為選取的界限與實際資料類型。 如果指定的邊界相等，則遮罩函數是常數。<br/><br/>![顯示用來產生亂數字之遮罩方法的螢幕擷取畫面。](./media/dynamic-data-masking-overview/1_DDM_Random_number.png) |
| **自訂文字** |**遮罩方法會公開第一個和最後一個字元**，並在中間新增自訂填補字串。 如果原始字串小於公開的前置詞和後置詞，則只會使用填補字串。 <br/>prefix[padding]suffix<br/><br/>![導覽窗格](./media/dynamic-data-masking-overview/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>要遮罩處理的建議欄位

DDM 建議引擎會將您資料庫中的特定欄位標示為潛在敏感性欄位，而這類欄位可能適合進行遮罩處理。 在入口網站的 [動態資料遮罩] 刀鋒視窗中，您會看到您的資料庫的建議資料行。 您只需要對一或多個資料行按一下 [新增遮罩]，然後按一下 [儲存]，以對這些欄位套用遮罩。

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>使用 PowerShell Cmdlet 為您的資料庫設定動態資料遮罩

### <a name="data-masking-policies"></a>資料遮罩原則

- [AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [設定-AzSqlDatabaseDataMaskingPolicy](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>資料遮罩規則

- [AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [新 AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [移除-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [設定-AzSqlDatabaseDataMaskingRule](/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-rest-api"></a>使用 REST API 為您的資料庫設定動態資料遮罩

您可以使用 REST API，以程式設計方式管理資料遮罩原則和規則。 已發佈的 REST API 支援下列作業：

### <a name="data-masking-policies"></a>資料遮罩原則

- [建立或更新](/rest/api/sql/datamaskingpolicies/createorupdate)：建立或更新資料庫資料遮罩原則。
- [取得：取得](/rest/api/sql/datamaskingpolicies/get)資料庫資料遮罩原則。 

### <a name="data-masking-rules"></a>資料遮罩規則

- [建立或更新](/rest/api/sql/datamaskingrules/createorupdate)：建立或更新資料庫資料遮罩規則。
- [依資料庫列出](/rest/api/sql/datamaskingrules/listbydatabase)：取得資料庫資料遮罩規則的清單。
