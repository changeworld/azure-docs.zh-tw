---
title: 什麼是 Azure Synapse 的資料行層級安全性？
description: 資料行層級安全性可供客戶根據使用者其執行內容或群組成員資格來控制資料庫資料表資料行的存取，以簡化應用程式安全性的設計和編碼，並供能夠實作資料行存取限制。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: a4da74c01f732f3a62d29847d5f61934dede9778
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85208088"
---
# <a name="column-level-security"></a>資料行層級安全性

資料行層級安全性可供客戶根據使用者其執行內容或群組成員資格來控制資料表資料行的存取。

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
自此影片公佈以來，Azure Synapse 已可使用[資料列層級安全性](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

資料行層級安全性可簡化應用程式安全性的設計和編碼，並供限制資料行存取以保護機密資料。 例如，確保特定使用者只能存取其部門相關資料表的特定資料行。 存取限制邏輯是位於資料庫層，而不是離開這些資料，到另一個應用程式層。 資料庫會在每次嘗試從任何層級存取該資料時套用存取限制。 此限制可藉由縮小整個安全性系統的介面區，讓安全性更加可靠和健全。 此外，資料行層級安全性也不需引進檢視以篩選出可用來將存取限制加諸於使用者的資料行。

您可使用 [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 陳述式來實作資料行層級安全性。 透過這個機制，即可支援 SQL 和 Azure Active Directory (AAD) 驗證。

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>語法

```syntaxsql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>範例

下列範例示範如何限制 `TestUser` 存取 `Membership` 資料表的 `SSN` 資料行：

建立 `Membership` 資料表，其中含有用來儲存社會安全號碼的 SSN 資料行：

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

允許 `TestUser` 存取含機密資料的 SSN 資料行以外所有資料行：

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

如果以 `TestUser` 執行的查詢中包含 SSN 資料行，則查詢將會失敗：

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>使用案例

一些目前使用資料行層級安全性的範例：

- 某家金融服務公司只允許帳戶管理員能夠存取客戶社會安全號碼 (SSN)、電話號碼和其他個人識別資訊 (PII)。
- 某家醫療保健供應商允許醫生和護士存取機密的醫療記錄，而不允許帳務部門的成員檢視此資料。
