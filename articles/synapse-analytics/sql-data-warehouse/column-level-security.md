---
title: Azure 突觸的列級安全性是什麼?
description: 列級安全性允許客戶根據使用者的執行上下文或組成員身份控制對資料庫表列的訪問,簡化應用程式中安全設計和編碼,並允許您對列存取實施限制。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: b0a783ad5db86ca783ff1cebceec8d77ab528047
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687929"
---
# <a name="column-level-security"></a>資料行層級安全性

列級安全性允許客戶根據使用者的執行上下文或組成員身份控制對表列的訪問。

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
自發佈此視頻以來[,行級別安全性](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)已可用於 Azure 同步。

列級安全性簡化了應用程式中安全性的設計和編碼,允許您限制列訪問以保護敏感數據。 例如，確保特定使用者只能存取其部門相關資料表的特定資料行。 存取限制邏輯是位於資料庫層，而不是離開這些資料，到另一個應用程式層。 每次嘗試從任何層訪問數據時,資料庫都會應用訪問限制。 通過減少整體安全系統的表面積,此限制使您的安全性更加可靠和可靠。 此外,列級安全性還消除了引入視圖以篩選出列以對使用者施加訪問限制的需要。

您可以使用[GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 語句實現列級安全性。 透過這個機制，即可支援 SQL 和 Azure Active Directory (AAD) 驗證。

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

下面的範例簡用如何`TestUser`限制`SSN``Membership`存取 表的欄:

使用`Membership`儲存式社會保險號的 SSN 欄建立表:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

允許`TestUser`存取除 SSN 列之外的所有欄,該列具有敏感資料:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

如果查詢包含`TestUser`SSN 欄,則執行的查詢將失敗:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>使用案例

目前如何使用列級安全性的一些範例:

- 某家金融服務公司只允許帳戶管理員能夠存取客戶社會安全號碼 (SSN)、電話號碼和其他個人識別資訊 (PII)。
- 醫療保健提供者只允許醫生和護士訪問敏感的醫療記錄,同時阻止計費部門的成員查看此數據。
